# Benutzer und URI
## Vorbereitungen 
Repo Klonen
```bash
git clone https://github.com/sbhaseen/express-dashboard-demo
```
## Inbetriebnahme
```bash
# Verzeichnis wechsel
cd express-dashboard-demo
# Dependencies installieren via npm
npm install
# Applikaiton starten
npm run dev
```
Output:                                  
![Hier sollte ein Bild über die MongoDB URI + User Augabe erscheinen](../images/monoguseruri1.png)                        
Nun verbindet man sich via http://localhost:5000                        
Output:                                  
![Hier sollte ein Bild über die MongoDB URI + User Augabe erscheinen](../images/monoguseruri2.png)                  
## Testen
### Daten erstellen
Output:                                    
![Hier sollte ein Bild über die MongoDB URI + User Augabe erscheinen](../images/monoguseruri4.png)             
### Über welchen Controller wird kommunziert?
Es wird über das Dashboard kommuniziert welches dann die Controller für den Prozess, die Maschiene, die Kategorie und die Prozessinstanz aufruft                                  
Output:                                  
![Hier sollte ein Bild über die MongoDB URI + User Augabe erscheinen](../images/monoguseruri5.png)             
### Über welches Model wird der Datensatz in der Datenbank gespeichert?
Die Models werden über den jeweiligen Controller mithife eine JS Funtkion wie findById aufgerufen und dann wird so der Befehl ausgeführt. Die DB Verbindung wird jedoch im Server.js gemacht. Ein Besipiel Model welches zu Controller categoryController.js gehört ist category.js                        
Output:                                  
![Hier sollte ein Bild über die MongoDB URI + User Augabe erscheinen](../images/monoguseruri6.png)                 
###  Wie sehen die Dokumente in der Datenbank aus?
Es werden bspw. bei der Collection Processes die Dokumentenid von der Category und machine gespeichert                    
Output:                                  
![Hier sollte ein Bild über die MongoDB URI + User Augabe erscheinen](../images/monoguseruri3.png)      
## Anwendung modifizieren
### Erfassen Sie auf der MongoDB-Instanz einen Systemadministrator
Mit cleartext password als String statt passwordPrompt() gibt es keinen Konsolenpromt                    
```mongo
// Zur Admin Datenbank wechseln
use admin
db.createUser(
{
    user: "sysadm",
    pwd: passwordPrompt(),
    roles: [
    { role: "userAdminAnyDatabase", db: "admin" },
    { role: "readWriteAnyDatabase", db: "admin" }
    ]
}
)
```
Output:                                  
![Hier sollte ein Bild über die MongoDB URI + User Augabe erscheinen](../images/monoguseruri7.png)                        
### Erfassen Sie auf der MongoDB-Instanz einen Applikationsbenutzer für diese Applikation
Der Benutzer hat alle Rechte auf die Applikations DB nicht aber auf die anderen. In einer Produktiv Umgebung müssten alle Berechtigungne ausführlihc auf ihre notwendigkeit geprüft werden, damit der USer nicht zu vile Rechte hat.
```monog
db.createUser(
{
	user: "mfgdashboard",

	pwd: "password",

	roles:[
	{
		role: "read" , db:"mfgdashboard"},
    {
		role: "readWrite" , db:"mfgdashboard"}
	}
		  ]
})
```
Output:                                  
![Hier sollte ein Bild über die MongoDB URI + User Augabe erscheinen](../images/monoguseruri8.png)
### Passen Sie die URI in der Datei server.js entsprechend an
Die URI lauter "mongodb://mfgdashboard:password@localhost:27017/mfgdashboard?authSource=admin" und musste im Server.js angepasst werden
Output:                                  
![Hier sollte ein Bild über die MongoDB URI + User Augabe erscheinen](../images/monoguseruri9.png)