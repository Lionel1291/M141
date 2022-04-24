# Begriffe
## SQL, SQL DDL, SQL DML
SQL:
Mit SQL (Structured Querry Language) werden die einzelnen Datensätze behandelt. Dabei gibt es zum löschen den Delete, zum Hinzufügen den Insert, zum Abrufen der Daten den Select und zum aktualisieren der Daten den Update Befehl.

SQL DDL:
Mit SQL DDL (Data Definition Language) wird die Struktur der Daten mittels create erstellt oder mittels drop gelöscht. Mit dem Befehl Alter die Struktur der Daten welche ich mit SQL DDL erstellt habe zu manipulieren, die geschieht mit dem alter Befehl.

SQL DML:
SQL DML (Data Manipulation Language) behandelt include alle Befehle die zur Manipulation der Daten oder deren Struktur verwendet werden können. Also Insert, update, select, delete, alter, drop, create
## ACID
ACID ist eine Abkürzung welche die erwünschten Eigenschaften an eine Datenverwaltungssoftware respektive Transaktionen beschreibt.                
ACID setzt sich aus folgenden Begriffenen zusammen:
- **A**tomicity / Abgeschlossenheit                 
    Bei Atomicity geht es darum, dass Änderungen an den Daten nur dann wirksam sind, wenn eine Transaktion erfolgreich war. Wenn während der Transaktion ein Fehler passiert muss das DBMS die Daten soweit wiederherstellen wie sie vor beginn der Transaktion waren.
- **C**onsistency / Konsistenz                   
    Die Integritätsbedingungen werden eingehalten d.h. bspw. dass alle Relationen korrekt abgespeichert werden
- **I**solation / Isolation                  
    Die Befehle sollten sich nicht gegenseitig beinflussen und deshalb nicht gleichzeitig auf die selben Tabellen zugreiffen, weshabl oft aktuell verwendete Tabellen gesperrt werden, wenn gewisse Aktionne ausgeführt werden. Da Aktionen dann seriell und nicht parallel Ausgeführt werden leidet die Performance. 
- **D**urability / Dauerhaftigkeit                   
    Daten sollten auf der Festplatte und nicht im Memory oder Cache gespeichert sein sobald ein Befehl erfolgreich durchgeführt wurde.
## Base
Base ist quasi das gegenteil von ACID. Hersteller von Datenbanksystemen welche auf BASE setzen haben einen höheren Fokus auf schnelle, konsistenz und gute Speicherung.                        
Base setz sich aus folgendem zusammen:                 
- **B**asically **A**vailable                   
    B und A stehen im wesentlichen dafür, dass die Daten irgendwie mal grösstenteils verfügbar sind
- **S**oft state                
    Das S steht dafür, dass man einen möglichen Datenverlust bewusst in kauf nimmt
- **E**ventual consistency                         
    Das E steht dafür, dass die Daten eventuell Konsistent gespeichert wurden
## "Transaktion" und was das ist
Eine Transaktion ist in der Informatik eine Abfolge von Programmschritten, die als logische Einheit behandelt werden, weil sie nach fehlerfreier und vollständiger Ausführung den Datenbestand in einen konsistenten Zustand bringen. Daher ist es für eine Transaktion ausdrücklich erforderlich, dass sie entweder vollständig und fehlerfrei oder gar nicht ausgeführt wird.
## CAP-Theorem
Das CAP Theorem definiert, dass man nie alle drei haben kann, sondern immer nur 2 von 3. CAP steht für:             
- **C** = **C**onsistency (Konsistenz)
- **A** = **A**vailability (Verfügbarkeit)
- **P** = **P**artition Tolerance (Verteilbarkeit der Daten / Ausfalltoleranz)

Nachfolgende Darstellung zeigt das CAP Theorem grafisch auf und zeigt welche Hersteller sich für welche 2 entschieden haben:              
![Grafik zum CAP-Theorem konnte nicht geladen werden](../images/CAP.jpg "CAP-Theorem")
## Praxisauftrag
Ich habe als DBMS MS SQL genommen, weil viele Programme unter anderem Veeam damit arbeitet.

Von Base hat MS SQL ganz klar von ACID das C weil MS SQL auf eine sehr hohe Konsistenz setzt. Bei ACID setzt MS SQL auch auf das D, da die Daten immer möglichst rasch auf der Festplatten gespeichert werden müssen. Von Base sind das B und A vorhanden, da man eine hohe Verfügbarkeit haben möchte.

MS SQL ist ein relationales DBMS und ist nicht kostenlos. Es ist sehr beliebt bei Softwareherstellern, welche Software und Windows verteilen, diese installieren dann oft einen MS SQL Server um darin ihre Daten zu speichern, beispiele dafür sind Veeam und Barracuda.