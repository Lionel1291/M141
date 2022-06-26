# MySQL Cluster
## MySQL-Router
### Analyse docker-compose.yml
### Installation
```bash
# Umgebung starten
docker-compose up -d
# Funtkionskontrolle
docker-compose ps
docker-compose logs -f
```
### Kontrolle
Funktionalität des Cluster Verifizieren
```bash
# Login auf den Master Node
docker-compose exec mysql_node01 bash
# Auf dem Master auf sich selbst verbinden
mysqlsh --js root@mysql_node01 # Login mit root    
```
```mysql-js
var cluster = dba.getCluster()
cluster.status()
```
## ProxySQL und Spring Boot -> the Full Monty
### Analyse docker-compose.yml
### Bearbeitung der Schritte aus dem Repo
#### Start Environment
```bash
./init-environment.sh
```
#### Check MySQL Replication
```bash
./check-replication-status.sh
```
#### Check ProxySQL configuration
```bash
# Connect to Proxy
./proxysql-admin.sh
# see Servers
SELECT * FROM mysql_servers;
# Globale Variablen anzeigen
SELECT * FROM global_variables;
```
#### Start customer-api
```bash
# Applikation starten
./mvnw clean spring-boot:run --projects customer-api
```

Applikation ist unter http://localhost:8080/swagger-ui.html erreichbar               
#### Simulation
Für jeden Node ein Terminal öffnen                     
```bash 
# Monitor / Master
docker exec -it -e MYSQL_PWD=secret mysql-master mysql -uroot --database customerdb
# Slvae 1
docker exec -it -e MYSQL_PWD=secret mysql-slave-1 mysql -uroot --database customerdb
# Slave 2
docker exec -it -e MYSQL_PWD=secret mysql-slave-2 mysql -uroot --database customerdb
```
#### Shutdown