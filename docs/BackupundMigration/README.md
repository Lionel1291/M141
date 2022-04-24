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
