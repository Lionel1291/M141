# Best Practices bei SQL-Queries / Explain
## Kurzzusammenfassung zu den Performance Tips
### Query Logging:
MySQL bietet die Möglichkeit Quries über einen bestimmten längeren Zeitraum durchgehend zu loggen:
Syntax:
```sql
SET GLOBAL slow_query_log = 'ON'; # Logging aktivieren
SET GLOBAL long_query_time = X; # Dauer wird in Sekunden angegeben
SET GLOBAL slow_query_log_file = '/path/to/filename'; -- Logfile paht angeben
```
Beispiel:
```sql
SET GLOBAL slow_query_log = 'ON'; # Logging aktivieren
SET GLOBAL long_query_time = 86400; # 1 Tag in Sekunden 60x60x24
SET GLOBAL slow_query_log_file = '/tmp/logsql'; -- Logfile paht angeben
```
![Hier sollten ein Logging Screen sein](../images/logging.png)
### Performance Tipps
#### Attribute Indexieren
Alle Attribute indexieren die bei 'WHERE, ORDER BY, GROUP BY' zum Einsatz kommen, damit keine Fulltable Scanns entstehen.

Die Löung dafür ist es Indexes zu erstellen:

## Übungsdokuemntation zu Performance und Indexing
### Schlimm / Ohne Index
Sitationsanalyse von einem Querry der Joins verwendet ohne Indexing und wehere Statements verwendet. Für die Situationsanalyse wird vor den Select ein EXPLAIN angefügt um zu sehen was er macht:
```sql
EXPLAIN SELECT * FROM orderdetails od     
    INNER JOIN orders o ON od.orderNumber = o.orderNumber    
    INNER JOIN products p ON p.productCode = od.productCode     
    INNER JOIN productlines pl ON p.productLine = pl.productLine     
    INNER JOIN customers c on c.customerNumber = o.customerNumber      
WHERE o.orderNumber = "10101";
```
Ergebnis des Exlain:                                    
![Hier sollten ein Explain eines Selectes sein](../images/explaintesting1.png)                         
Erkentnisse:
* type: ALL ist schlecht / Nichts indexiert
* keys: possible_keys und keys sind NULL / Es gibt kein Indexing
* Suchgrösse: 7 x 110 x 326 x 122 x 2996 = 91750822240 gescannte Elemente, da wir imemr Full Table Scans machen

### Verbessern mit Index
#### Tabellen Anpassen
Die PKs werden gesetzt und noch ein paar andere Indexes werden gesetzt welche das durchsuchen beschleunigen (productvariants hat bereits einen PK, da er auto increment verwendet):
```sql
ALTER TABLE customers ADD PRIMARY KEY (customerNumber);
ALTER TABLE employees ADD PRIMARY KEY (employeeNumber);
ALTER TABLE offices ADD PRIMARY KEY (officeCode);
ALTER TABLE orderdetails ADD PRIMARY KEY (orderNumber, productCode);
ALTER TABLE orders ADD PRIMARY KEY (orderNumber), ADD KEY (customerNumber);
ALTER TABLE payments ADD PRIMARY KEY (customerNumber, checkNumber);
ALTER TABLE productlines ADD PRIMARY KEY (productLine);
ALTER TABLE products ADD PRIMARY KEY (productCode), ADD KEY (buyPrice), ADD KEY (productLine);
ALTER TABLE productvariants ADD KEY (buyPrice),ADD KEY (productCode);
```
Output:                                         
![Hier sollten ein ADD Key mit alter table ausgeführt worden sein](../images/explaintesting2.png)                        
#### Select mit Index
Query:
```sql
EXPLAIN SELECT * FROM orderdetails od     
    INNER JOIN orders o ON od.orderNumber = o.orderNumber    
    INNER JOIN products p ON p.productCode = od.productCode     
    INNER JOIN productlines pl ON p.productLine = pl.productLine     
    INNER JOIN customers c on c.customerNumber = o.customerNumber      
WHERE o.orderNumber = "10101";
```
![Hier sollten ein Explain eines Selectes sein](../images/explaintesting3.png)                         
Nun sehen wir, dass er beim Suchen und den Joins unsere Indexes verwendet hat.
Erkentnisse:
* type: ALL ist schlecht / Nichts indexiert
* keys: possible_keys und keys sind NULL / Es gibt kein Indexing
* Suchgrösse: 1x1x4x1x1=4 gescannte Elemente, kein Full Table Scann mehr 

#### Fazit
Indexing hat die durchsuchten Spalten auf 4 reduiziert von über 90Mio. was eine erhebliche verbesserung ist und dadutch auch die Zugriffszeit auf die DB schwindet.
### Unions
Zu viele ORs können auch zu einem Fulltable Scan führen, deshalb einzelne Queries und unions all.
```sql
#Schlecht:
select * from users where firstname like  'Ha%'  or lastname like 'Ha%' ;

# Gut:
select * from users where firstname like 'Ha%' union all select * from users where lastname like  'Ha%';
```
### Wildcards
Keine führenden Wildcards bei Selects!!!!!! 
```sql
select * from users where firstname like  '%Ha';
```
### Fulltext Search
Fulltext Searches sind besser und Effizienter als Wildcards
```sql
Alter table users ADD FULLTEXT (firstname, lastname);
Select * from users where match(firstname, lastname) AGAINST ('Ha');
explain Select * from users where match(firstname, lastname) AGAINST ('Ha');
+----+-------------+----------+------------+----------+---------------+------------+---------+-------+------+----------+-------------------------------+
| id | select_type | table    | partitions | type     | possible_keys | key        | key_len | ref   | rows | filtered | Extra                         |
+----+-------------+----------+------------+----------+---------------+------------+---------+-------+------+----------+-------------------------------+
|  1 | SIMPLE      | users    | NULL       | fulltext | firstname     | firstname  | 0       | const |    1 |   100.00 | Using where; Ft_hints: sorted |
+----+-------------+----------+------------+----------+---------------+------------+---------+-------+------+----------+-------------------------------+
```
### Datenbank Design
1. Tabellen Normalisieren / keine Redundanten Daten
2. Geschickte Datentypen
3. NULL-Werte
4. Zu viele Spalten(Attribute) im Design vermeinden
5. Bei SQL-Joins vorsichtig sein (nurt die Tabellen eingebziehen, die auch nötig sind) - nur Daten ausgeben die nötig sind (kein Select *)

### Caching aktivieren
Caching Werte anzeigen:
```sql
mysql> show variables like 'query_cache_%' ;
+------------------------------+----------+
| Variable_name                | Value    |
+------------------------------+----------+
| query_cache_limit            | 1048576  |
| query_cache_min_res_unit     | 4096     |
| query_cache_size             | 16777216 |
| query_cache_type             | OFF      |
| query_cache_wlock_invalidate | OFF      |
+------------------------------+----------+
5 rows in set (0.00 sec)
```
Config nach bedürfnissen anpassen (Zu gross braucht zu viel Speicher)
```editorconfig
query_cache_type=1          # 1 ist eingestellt / 0 ist aus
query_cache_size = 10M      # default ist 1MB / erhöhen auf 10MB macht Sinn
query_cache_limit=1M        # Individuelle Query-Resultate die gecacht werden, 1MB macht Sinn
```
