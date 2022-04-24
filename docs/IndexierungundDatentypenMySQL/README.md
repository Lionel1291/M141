# Indexierung und Datentypen MySQL
## Datentypen
### Text
| Datentyp        | Beschreibung                                                                                                                                                                                                                                                                                   |
|------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| CHAR(size)       | Enthält eine Zeichenfolge fester Länge (kann Buchstaben, Zahlen und Sonderzeichen enthalten). Die feste Größe wird in Klammern angegeben. Kann bis zu 255 Zeichen speichern                                                                                                                    |
| VARCHAR(size)    | Enthält eine Zeichenkette variabler Länge (kann Buchstaben, Zahlen und Sonderzeichen enthalten). Die maximale Größe wird in Klammern angegeben. Es können bis zu 255 Zeichen gespeichert werden. Hinweis: Wenn Sie einen größeren Wert als 255 eingeben, wird er in einen TEXT-Typ umgewandelt |
| TINYTEXT         | Enthält eine Zeichenkette mit einer maximalen Länge von 255 Zeichen                                                                                                                                                                                                                            |
| TEXT             | Enthält eine Zeichenkette mit einer maximalen Länge von 65535 Zeichen                                                                                                                                                                                                                          |
| BLOB             | Für BLOBs (Binary Large Objects/Binäre Grossobjekte). Speichert bis zu 65535 Bytes an Daten                                                                                                                                                                                                    |
| MEDIUMTEXT       | Enthält eine Zeichenkette mit einer maximalen Länge von 16777215 Zeichen                                                                                                                                                                                                                       |
| MEDIUMBLOB       | Für BLOBs (Binary Large Objects/Binäre Grossobjekte). Speichert bis zu 16777215 Bytes an Daten                                                                                                                                                                                                 |
| LONGTEXT         | Enthält eine Zeichenkette mit einer maximalen Länge von 4294967295 Zeichen                                                                                                                                                                                                                     |
| LONGBLOB         | Für BLOBs (Binary Large Objects/Binäre Grossobjekte). Speichert bis zu 4294967295 Bytes an Daten                                                                                                                                                                                               |
| ENUM(x,y,z,etc.) | Erlaubt die Eingabe einer Liste von möglichen Werten. Sie können bis zu 65535 Werte in einer ENUM-Liste auflisten. Wird ein Wert eingegeben, der nicht in der Liste enthalten ist, wird ein leerer Wert eingefügt: ENUM('X','Y','Z')                                                           |
| SET              | Ähnlich wie ENUM, mit dem Unterschied, dass SET bis zu 64 Listenelemente enthalten kann und mehr als eine Auswahl speichern kann                                                                                                                                                               |
### Numerisch
| Datentyp       | Beschreibung                                                                                                                                                                                                                                                 |
|-----------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| TINYINT(size)   | -128 bis 127 normal. 0 bis 255 Vorzeichenbehaftet*. Die maximale Anzahl der Ziffern kann in Klammern angegeben werden                                                                                                                                       |
| BOOL/BOOLEAN    | Diese Typen sind Synonyme für TINYINT(1). Ein Wert von Null wird als falsch angesehen. Werte ungleich Null werden als wahr angesehen. True oder False können auch verwendet werden.                                                                         |
| SMALLINT(size)  | -32768 bis 32767 normal. 0 bis 65535 Vorzeichenbehaftet*. Die maximale Anzahl der Ziffern kann in Klammern angegeben werden                                                                                                                                 |
| MEDIUMINT(size) | -8388608 bis 8388607 normal. 0 bis 16777215 Vorzeichenbehaftet*. Die maximale Anzahl der Ziffern kann in Klammern angegeben werden                                                                                                                          |
| INT(size)       | -2147483648 bis 2147483647 normal. 0 bis 4294967295 Vorzeichenbehaftet*. Die maximale Anzahl der Ziffern kann in Klammern angegeben werden                                                                                                                  |
| BIGINT(size)    | -9223372036854775808 bis 9223372036854775807 normal. 0 bis 18446744073709551615 Vorzeichenbehaftet*. Die maximale Anzahl der Ziffern kann in Klammern angegeben werden                                                                                      |
| FLOAT(size,d)   | Eine kleine Zahl mit einem fliessenden Dezimalpunkt. Die maximale Anzahl der Ziffern kann mit dem Parameter size angegeben werden. Die maximale Anzahl der Ziffern rechts vom Dezimalpunkt wird mit dem Parameter d angegeben                               |
| DOUBLE(size,d)  | Eine grosse Zahl mit einem fliessenden Dezimalpunkt. Die maximale Anzahl der Ziffern kann mit dem Parameter size angegeben werden. Die maximale Anzahl der Ziffern rechts vom Dezimalpunkt wird mit dem Parameter d angegeben                               |
| DECIMAL(size,d) | Ein DOUBLE, der als String gespeichert wird und einen festen Dezimalpunkt enthält. Die maximale Anzahl der Ziffern kann mit dem Parameter size angegeben werden. Die maximale Anzahl der Ziffern rechts vom Dezimalpunkt wird mit dem Parameter d angegeben |
### Zeit
| Datentyp   | Beschreibung                                                                                                                                                                                                                            |
|-------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| DATE()      | Ein Datum. Format: YYYY-MM-DD Der unterstützte Bereich reicht von '1000-01-01' bis '9999-12-31'.                                                                                                                                       |
| DATETIME()  | Eine Kombination aus Datum und Uhrzeit. Format: YYYY-MM-DD HH:MI:SS Der unterstützte Bereich reicht von '1000-01-01 00:00:00' bis '9999-12-31 23:59:59'.                                                                               |
| TIMESTAMP() | Ein Zeitstempel. TIMESTAMP-Werte werden als die Anzahl der Sekunden seit der Unix-Epoche ('1970-01-01 00:00:00' UTC) gespeichert. Format: YYYY-MM-DD HH:MI:SS Der unterstützte Bereich reicht von '1970-01-01 00:00:01' UTC bis '2038-01-09 03:14:07' UTC |
| TIME()      | Eine Zeit. Format: HH:MI:SS Der unterstützte Bereich reicht von '-838:59:59' bis '838:59:59'.                                                                                                                                                  |
| YEAR()      | Eine Jahreszahl im zweistelligen oder vierstelligen Format.Hinweis: Werte im vierstelligen Format sind zulässig: 1901 bis 2155. Zulässige Werte im zweistelligen Format: 70 bis 69, was den Jahren von 1970 bis 2069 entspricht.                                                   |
### JSON
Der JSON-Datentyp bietet diese Vorteile gegenüber der Speicherung von Strings im JSON-Format in einer String-Spalte:
* Automatische Validierung von JSON-Dokumenten, die in JSON-Spalten gespeichert sind. Ungültige Dokumente erzeugen einen Fehler.
* Optimiertes Speicherformat. JSON-Dokumente, die in JSON-Spalten gespeichert werden, werden in ein internes Format konvertiert, das einen schnellen Lesezugriff auf die Dokumentelemente ermöglicht. Wenn der Server später einen in diesem Binärformat gespeicherten JSON-Wert lesen muss, muss der Wert nicht aus einer Textdarstellung geparst werden. Das Binärformat ist so strukturiert, dass der Server in der Lage ist, Unterobjekte oder verschachtelte Werte direkt nach Schlüssel oder Array-Index zu suchen, ohne alle Werte vor oder nach ihnen im Dokument zu lesen.

Ein JSON ist ungefähr so gross wie ein LONGBLOB oder LONGTEXT
### Anwendungsbeispile JSON und ENUM
Bei JSON können User und Gruppen prinzipien QUick and Dirty realisiert werden indem diese im JSON und nicht über die Datenbank realisiert werden. Dies wird nachfolgend in SQL dargestellt:
```sql
CREATE TABLE `users` (
  `id` int(11) NOT NULL AUTO_INCREMENT,
  `name` varchar(255) NOT NULL,
  `email` varchar(255) NOT NULL,
  `password` varchar(255) NOT NULL,
  `created_at` datetime NOT NULL,
  `roles` json NOT NULL,
  PRIMARY KEY (`id`)
) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4;

INSERT INTO `users` (`id`, `name`, `email`, `password`, `created_at`, `roles`) VALUES
(1,	'admin',	'admin@admin.ch',	'$2y$12$uyfcBXbakFWti4ohJHB72.2W.9a2B7v1fGKfLHcy5X93BBc97W4IO',	'2019-11-27 19:06:45',	'[\"user\", \"admin\"]'), 
(2,	'user_passuser',	'user_passuser@test.ch',	'$2y$12$wWfk.xAVn2htcNlfRUplceL5J5T/hn9lOiF3rA/zIcrdDPM3JfIUW',	'2019-11-27 19:14:26',	'[\"user\"]');
```

Bei ENUM können zum Beispiel als mögliche eingaben bei Hundeobjekten ihr Geschlecht ein Enum sein. Nachfolgend wurde approved als ENUM definiert, weil diese Konfiguration nur gewisse zustände haben kann
```sql
CREATE TABLE `Config` (
                          `id` int NOT NULL AUTO_INCREMENT,
                          `version` float NOT NULL,
                          `approved` enum('','2','3') DEFAULT NULL,
                          `lastmodify` datetime NOT NULL,
                          `create` datetime NOT NULL,
                          `hosts` tinytext NOT NULL,
                          `commandvalue` tinytext NOT NULL,
                          `name` tinytext NOT NULL,
                          `comment` varchar(255) DEFAULT NULL,
                          `userid` int NOT NULL,
                          `commandid` int NOT NULL,
                          PRIMARY KEY (`id`),
                          KEY `commandid` (`commandid`),
                          KEY `userid` (`userid`),
                          CONSTRAINT `config_ibfk_1` FOREIGN KEY (`commandid`) REFERENCES `Command` (`id`),
                          CONSTRAINT `config_ibfk_2` FOREIGN KEY (`userid`) REFERENCES `users` (`id`)
) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4 COLLATE=utf8mb4_0900_ai_ci;

INSERT INTO `Config` (`id`, `version`, `approved`, `lastmodify`, `create`, `hosts`, `commandvalue`, `name`, `comment`, `userid`, `commandid`) VALUES
(1,	1,	'',	'2017-06-15 09:34:21',	'2017-06-15 09:34:21',	'aruba',	'10.11.11.250',	'First',	'',	1,	1),
(2,	1,	'2',	'2017-06-15 09:34:21',	'2017-06-15 09:34:21',	'aruba',	'10.11.11.250',	'Second',	'',	2,	2),
(3,	1,	'',	'2017-06-15 09:34:21',	'2017-06-15 09:34:21',	'aruba',	'10.11.11.250',	'asd',	'',	1,	3),
(4,	1,	'2',	'2017-06-15 09:34:21',	'2017-06-15 09:34:21',	'aruba',	'10.11.11.250',	'sad',	'',	1,	4),
(5,	1,	'',	'2017-06-15 09:34:21',	'2017-06-15 09:34:21',	'aruba',	'10.11.11.250',	'Fisadrst',	'',	2,	5),
(6,	1,	'3',	'2017-06-15 09:34:21',	'2017-06-15 09:34:21',	'aruba',	'10.11.11.250',	'Firsasdt',	'',	2,	4),
(7,	1,	'',	'2017-06-15 09:34:21',	'2017-06-15 09:34:21',	'aruba',	'10.11.11.250',	'Firasdst',	'',	1,	4),
(8,	1,	'3',	'2017-06-15 09:34:21',	'2017-06-15 09:34:21',	'aruba',	'10.11.11.250',	'asd',	'',	1,	3);
```
## Index
### Allgemein
Indexe werden dazu verwendet, um Daten ausfindig zu machen d.h. z.B. bei Statements mit where wird die Suchanfragezeit reduziert und die Datenbanknuzung somit verringert, Nachteile sind, dass der Index selbst auch noch Platz braucht auf der Festplatte zudem entstehen weitere Strukturen die gepfelgt werden müssen. Wenn nun bspw. nach einer Mail gesucht wird welche indiziert wurde werden nicht Tausendende Einträge druchsucht sondern im Index wird nachgeschglagen und die gewünschten Daten werden zurückgegeben. Der Aufwand zum Nachschlagen ist dabei O(log n), d.h. bei 100000 Einträgen braucht mysql noch ungefähr log(100.000) = 17 Vergleiche. Damit dieses Nachschlagen funktioniert muss jedoch zuerst definiert werden was indexiert wird und diesen Index muss man dann erstellen.
### Was sollte ich Indexieren
Der Vorteil eines Index ist wie oben beschriebn, dass das Auffinden von Dantensätzen, wenn in einer Spalte gesucht wird schneller geht, ein Nachteil ist, dass ebenfalls wie oben beschrieben mehr Speicherplatz benötigt wird und INSERT, UPDATE und DELETE Statements ggf. mehr Zeit benötigen.                 

Ein Index macht nur sinn, wenn eine Tabelle über 1000 Datensaätze besitzt und der Index sollte dann nur auf die Spalten angewendet werden, welche regelmässig durch WEHRE oder ORDER BY Statements verwendet werden. Der Speicherplatz, welcher mehr beansprucht wird ist jedoch nur sekundär.
### Welchen Index sollte ich verwenden
* Der Primary Key wir bei jeder Tabelle für die primäre Indexierung verwendet und typischerweise wird eine ID als Primary Ley definiert. Es empfielt sich immer eine ID pro Tabelle anzulegen.
* Unique sollte verwendet werden, wenn doppelte Einträge nicht erlaubt sein dürfen. Dies kann zum Beispiel bei Benutzernamen oder Emailadressen von Benutzer nützlich sein.
* Index ist ideal, wenn doppelte einträge erlaubt sein sollten.
* Fulltext ist einer der aufwendigsten Index, da hier jedes Wort des Textes indiziert wird. Diesen Indextypen sollte man nu in kombination mit der MySQL Volltextsuche verwenden.
* Bei Fremdschlüsseln wird ein Index auf der referenzierenden Spalte erstellt oder ein neuer erstellt. 

### Mehrspaltige Indizes
Mehrspaltige Indexes sind sehr komplex und nicht empfehlenswert, es ist besser mehrere Indexes über je eine Spalte anzulegen. Bei mehrspaltigen INdexes verbindet MySQL die Spalten und speichert diese als einen Index. Dies ist nur von Vorteil wenn stets alle Spalten druchsucht werden, dadruch kann die Performance erhöht werden. Bspw. bei einer User Tabelle mit Vor und Nachnamen macht dies Sinn wenn immer beide gesucht werden, wenn jedoch jede einzeln geuscht wird macht dies keinen Sinn.
### Befehle
#### Syntax erstellen
```sql
CREATE [UNIQUE | FULLTEXT] INDEX index_name
    ON tbl_name (key_part,...)
```
#### Syntax löschen
```sql
DROP INDEX index_name ON tbl_name
```
#### Bearbeiten
Indexe werden ganz einfach mit ALTER TABLE bearbeitet.