# Backup und Migration
## Begriffe
* cold Backup 
  * Sind Backups, bei denen die Daten dem User während dem Import und Export nicht zur Verfügug stehen
* warm Backup  
  * Sind Backups, bei denen die Daten dem User während dem Import und Export eingeschränkt (bwpe. nur lesend) zur Verfügug stehen
* hot Backup 
  * Sind Backups, bei denen die Daten dem User während dem Import und Export vollständig zur Verfügug stehen
* logisches Backup 
  * Das Backup steht in messbarer Form bspw. als SQL Skript zur verfüfung.
* physisches Backup 
  * Dabei stehen die Backupinformationen in Binärer Form zur Verfügung bspw. Snapshot eins Filesystems
* Point-In-Time-Restore
  * Zu jedem Zeitpuknt von jedem Zeitpunkt in der Vergangenheit zurückkehren
* Unerschied logisch und physisch
  * Physich bswp. VM Snapshot, Logisch bspw. sql dump
* full vs. incremental
  * Bei Full wird alles gesichert, dauert länger. Bei Inremental nur die Änderungen bis zum letzen Backup. Nach einem gewissen Zyklus muss auch beim Incremental ein Vollbackup passieren.

## Warmes Backup - logisch, full
Mysql dump ist ein warmes Backup weil nur Zeitweise einige Tabellen gesperrt sind. Bei diesem Vorgehen dauert ein Backup und Restore je nach dem sehr lange und alles ist sehr gross. Vorteil ist die Versions und Produktunabhänhigkeit beim Restore. Ein weiterer Vorteil, man kann die Informationen vor dem Restore überarbeiten.
```shell
# Beispiel Backup
sudo mysqldump --single-transaction -u lionel -pPassword123$ --all-databases > dump.sql
# Beispeil Restore
sudo mysql -u lionel -pPassword123$ < dump.sql
```
![Hier sollte ein Bild über das Backup erscheinen](../images/bkp1.png)              
### Wieso Single transact mysqldumo?
Das Flag --single-transaction startet eine Transaktion vor der Ausführung. Anstatt die gesamte Datenbank zu sperren, lässt mysqldump die Datenbank im aktuellen Zustand zum Zeitpunkt der Transaktion lesen und sorgt so für einen konsistenten Daten-Dump.
### mydumper, myloarder
Im gegensatz zu mysqldumo kann dieses Tool parallele Verarbeitung von Datenbank, geht alles somit schneller. Die Anwendung ist analog zu mysqldump.
```shell
# Backup parallel
mydumper --compress -a
# Restore ebenfalls parallelisiert
myloader --directory dump_dir --overwrite-tables --verbose=3
```
### Skript
```shell
#!/bin/bash
# Add the backup dir location, MySQL root password, MySQL and mysqldump location
DATE=$(date +%d-%m-%Y)
BACKUP_DIR="/tmp/test-backup"
MYSQL_USER='lionel'
MYSQL_PASSWORD='Password123$'
MYSQL=/usr/bin/mysql
MYSQLDUMP=/usr/bin/mysqldump

# To create a new directory in the backup directory location based on the date
mkdir -p $BACKUP_DIR/$DATE

# To get a list of databases
databases=`$MYSQL -u$MYSQL_USER -p$MYSQL_PASSWORD -e "SHOW DATABASES;" | grep -Ev "(Database|information_schema)"`

# To dump each database in a separate file
for db in $databases; do
echo $db
$MYSQLDUMP --single-transaction --force --opt --skip-lock-tables --user=$MYSQL_USER -p$MYSQL_PASSWORD --databases $db | gzip > "$BACKUP_DIR/$DATE/$db.sql.gz"
done

# Delete the files older than 10 days
find $BACKUP_DIR/* -mtime +10 -exec rm {} \;
```
## "Cold Backup" - physisch, full oder inkrementell
Bei dieser Methode kopieren wir einfach die DB Dateien weg
```shell
rsync -avz /var/lib/mysql/* /IRGENDEINANDERSVERZEICHNIS/ 
# oder primitver:
cp -ar /var/lib/mysql/* /IRGENDEINANDERSVERZEICHNIS/ 
```
Windows Version:
```
Robocopy  D:\dir1\data E:\backup\data
```
![Hier sollte ein Bild über das Backup erscheinen](../images/bkp2.png)            
Der Nachteil ist, dass die User den DB Server nicht nutzen können. Die Backups sind relativ klein und es geht schnell. Nachteil ist, dass der Restpore entweder geht oder nciht. Vorteil: Daten im RAM werde auch gesichert. Weiterer Nachteil: Ein bestimmter Zeitpunkt lässit sich nicht wiederherstellen.
### Percona XtraBackup (Bei physischen BKP interessant)
```shell
# Einfacges Bakup
xtrabackup --backup --parallel 4 --compress --target-dir /var/backup/
# Ein Backupfile
xtrabackup --backup --parallel 4 --stream=xbstream > mybackup.xbstream
OR
xtrabackup --backup --stream=tar > mybackup.tar

# Resore
# Prepare the backup
xtrabackup --prepare --parallel 4 --use-memory 4G --target-dir /var/backup

# Copy backup to original location (ie: /var/lib/mysql), assuming backup taken on same host
xtrabackup --copy-back --target-dir /var/backup

# Fix file permissions if necessary
chown -R mysql:mysql /var/lib/mysql

# Start MySQL
systemctl start mysql
```
## Point in Time Restore (Inkrementell)
Backup Vorgenen: 
1. Regelmässig Full-Backup erstellen
2. Die Änderungen auf der Datenbank zwischen den Backups gesondert sichern

Restore:
1. Zeitpunkt für die Wiederherstellung finden
2. Letztes Full-Backup vor des Wiederherstellung-Zeitpunktes einspielen (Dump)
3. Alle Änderungen zwischen dem Full-Backup um dem Wiederherstellung-Zeitpunktes einspielen

### Aktivieren / Wiederherstellung PITR
Dafür muss die Binären MySQL Logdateien aktiviert sein. Nun kann ich mir in der Logdatei immer den Entsprechenden Zeitpunkt raussuchen, da jetzt alles geloggt wird.
```editorconfig
# Im Abschnitt mysqld die Server-ID setzen
[mysqld]
 server_id=master-01
 log-bin=mysql-bin

# Optionen
expire_logs_days = 10
max_binlog_size = 100
```
Restore:
```shell
# Variante 1 : Suchen der relevaten Binär-logs und Konvertierung in Menschenlesbarer Form:
mysqlbinlog mysql-bin.010310 > mysql-bin.010310.sql

# Variante 2 : Bin-Log bis zu einem bestimmten Datum "abschneiden"
mysqlbinlog \
    --stop-datetime = ’2021-04-29 17:20:00’ \
    mysql-bin.010313 > mysql-bin.010310.sql

# Import wie gehabt
mysql --user=root -password < mysql-bin.010310.sql
```
## Migration von Daten
Migrationen kann man wie beim Backup Physisch oder Logisch durchfühen. Die Vor und Nachteile bleichen geleich:
* Physisch
  * PRO: Schnell, platzsparend, einfach
  * Con: nicht fehlertolerant
* Logisch
  * Pro: Struktur vorhanden, lesbar und veränderbar, lässit Migration auf andere Server-Versionen/Produkte zu
  * Con: gross und langsam
### Einfach
```sql
# Dump erstellen und direkt zippen
mysqldump --single-transaction -u lionel -pPassword123$ sakila | gzip -9 > /tmp/DUMP_DB.sql.gz

# Files auf einen anderen Server kopieren, meistens über SSH
scp /tmp/DUMP_DB.sql.gz root@10.11.11.128:/tmp/

# Auf dem Zielserver neue NEUEDB erstellen
create database sakila2;

# Direktes Entzippen und importieren
gunzip < DUMP_DB.sql.gz | mysql -u lionel -pPassword123$ Sakila2
```
### Elegant
Diese Methode kann bei häufigen Migrationen recht mühsam werden, da es lange dauert. Der Sitch -h wird hier mit mysqldump verwendet.
1. Export der via mysqldump
2. umleitung der Ausgabe auf einen anderen Client mysql-client eines anderen Servers

```sql
# Export von local to remote:
mysqldump --single-transaction -u lionel -pPassword123$ sakila -h localhost \
    | mysql -u lionel -pPassword123$ -h 10.11.11.128 sakila

# Import von Remote to local:
mysqldump --single-transaction -u lionel -pPassword123$ sakila -h 10.11.11.128 \ 
    | mysql -u lionel -pPassword123$ -h localhost sakila
```
Sinnvolle Optionen bei mysqldump:
* --databases DB erstellt automatisch auch ein CREATE dbname im export Skript
* -add-drop-databases erstell automatisch auch ein DROP dbName
* --add-drop-table fügt automatisch ein Drop TAble hinzu

### Super Elegant
Hier wird die Migration über eine SSH Verbindung durchgeführt, dadurch kann ich den alles mittels Pipe direkt umleiten. Vorgehen:
1. SSH Verbindung auf Server1 machen und direkt in der Verbindung den DUMP BEfehel ausführen
2. Ausgabe dann in ein Pipe umleiten
3. Direkt aus der Pipe eine SSH Verbindung auf den Server2 umleiten, wo mit den Daten der Import ausgeführt wird

Die DB muss schon vorhanden sein auf dem neuen Server.
#### Keine Direkte Verbindung, Daten zwischenspeichern
```shell
ssh user@SERVER1 'mysqldump --single-transaction -u lionel -pPasswor123$ sakila' | 
    ssh user@SERVER2 'mysql -u lionel -pPasswor123$ sakila2'
```
#### Verbindung zwischen den Servern mit SSH
```shell
mysqldump --single-transaction -u lionel -pPasswor123$ sakila | 
    ssh user@SERVER2 'mysql -u lionel -pPasswor123$ sakila2'
# Mit beidseitigem SSH
ssh user@SERVER1 'mysqldump --single-transaction -u lionel -pPasswor123$ sakila' 
    | ssh user@SERVER2 'mysql -u lionel -pPasswor123$ sakila2'
```
Bei schlechtem Netz sollte man FIFOs verwenden, das sind physische Dateien, die wie Pipes funktionieren:
```shell
# FIFO erstellen
mkfifo namedPipe

# Export in Fifo umleiten
ssh user@SERVER1 'mysqldump --single-transaction -u lionel -pPasswor123$ sakila | gzip -9' > namedPipe;

# Import aus Fifo machen und umleiten auf die Import-SSH-Verbindung
ssh user@SERVER2 'gunzip | mysql -u lionel -pPasswor123$ sakila2' < namedPipe;
```
