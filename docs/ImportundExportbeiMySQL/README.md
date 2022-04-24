# Import und Export bei MySQL
Wichtig bei folgender Fehlermeldung: mysqldump: Got error: 1290: The MySQL server is running with the --secure-file-priv option so it cannot execute this statement when executing 'SELECT INTO OUTFILE'                       
1. Möglichkeit 1: Server ohne secure-file-priv Option starten
   SHOW VARIABLES LIKE "secure_file_priv"; location herausfinden und dann im file den Wert bei secure-file-priv= auf '' setzen
2. Möglichkeit 2: Files unter /var/lib/mysql-files/ speichern

## mysql
Das Tool mysql gehört zum Programm mysql-client. Unter *nix Systemen können Werkzeuge kombiniert genutzt werden.
### Login
```shell
# Login Local
mysql -u USERNAME -pPASSWORD DBNAME
# Login Remtote, geht nur wenn der TCP Port 3306 via Netzwerk erreichbar ist
mysql -u USERNAME -pPASSWORD DBNAME -h HOSTIP
```
### Skripts einlesen
```shell
# Local
mysql -u USERNAME -pPASSWORD DBNAME < /path/to/script.sql
# Remote
mysql -u USERNAME -pPASSWORD DBNAME -h HOSTIP < /path/to/script.sql
```
## Mysqldump -> SQL File
Exportiert in SQL Dateien, ist mit phantasie ein Backup
```shell
# Allgemein
mysqldump [options] db_name [tbl_name ...] > dump.sql

# Datenbanken angeben
mysqldump [options] --databases db_name ... > dump.sql

# ...oder einfach alle zusammen
mysqldump [options] --all-databases > dump.sql
```
Die Options findet man unter [mySQL Dump](https://dev.mysql.com/doc/refman/8.0/en/mysqldump.html)
## Export in CSV
```sql
# Ohne Header
SELECT 
    table1, table2, table3, table4, table5
FROM
    db1
WHERE
    status = 'state' 
INTO OUTFILE '/tmp/statedb.csv' -- Arbeiten Sie hier mit dem tmp-Verzeichnis, arbeiten Sie mit absoluten Pfaden
FIELDS ENCLOSED BY '"'  -- Jedes Feld wird mit "" eingefasst
TERMINATED BY ';' -- Feld wird mit einem Semikolon beendet
LINES TERMINATED BY '\n'; -- Zeilenendings nach Linux-Style (\r\n für Windows)
# Mit Header
(SELECT 'T1','T2','T3','T4','T5')
UNION
(SELECT 
    table1, table2, table3, table4, table5
FROM
    db1
WHERE
    status = 'state' 
INTO OUTFILE '/tmp/statedb.csv' 
FIELDS ENCLOSED BY '"' 
TERMINATED BY ';' 
LINES TERMINATED BY '\n');
```

### mysqldump -> CSX
Export in eine CSV Datei via mysqldump
```shell
# Evtl. noch username -u USERNAME und passwort -pPASSWORD angeben oder -h HOSTIP
# Gesamte Datenbankstruktur mit allen Tabellen
mysqldump DBNAME --fields-terminated-by ',' \
--fields-enclosed-by '"' --fields-escaped-by '\' \
--no-create-info --tab /var/lib/mysql-files/

# Nur einzelne Tabellen
mysqldump DBNAME TABLENAME --fields-terminated-by ',' \
--fields-enclosed-by '"' --fields-escaped-by '\' \
--no-create-info --tab /var/lib/mysql-files/
```
## Import von CSV
```sql
LOAD DATA INFILE '/tmp/data.csv' 
INTO TABLE country  
FIELDS TERMINATED BY ',' 
ENCLOSED BY '"'
LINES TERMINATED BY '\n'
IGNORE 1 ROWS
```
oder mti mysqlimport
```shell
mysqlimport --ignore-lines=1 \ # Header überspringen
            --fields-terminated-by=, \ # Feld-Begrenzungen: Komma oder Semikolons
            --local -u root -p Database \ # Zugriff auf die DB
             TableName.csv # Filename
```