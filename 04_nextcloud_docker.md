# Nextcloud mit Docker auf Ubuntu Server hosten

Diese Anleitung beschreibt, wie Nextcloud auf einem Ubuntu-Server mithilfe von Docker und Docker Compose installiert wird. Die Daten werden in diesem Fall auf einer externen, als ext4 formatierte SSD gespeichert, die dauerhaft unter `/mnt/nextcloud-data` eingebunden ist.

## Voraussetzungen

- Docker und Docker Compose sind installiert (`docker compose version`)
- Die SSD ist unter `/mnt/nextcloud-data` eingebunden
- Internetverbindung vorhanden
- SSH-Zugriff auf den Server funktioniert
- Ein Domainname wird **noch nicht benötigt**, Zugriff erfolgt lokal im Netzwerk

---

## 1. Verzeichnis vorbereiten

```bash
cd /mnt/nextcloud-data
````

Falls das Verzeichnis nicht existiert:

```bash
sudo mkdir -p /mnt/nextcloud-data
sudo chown $USER:$USER /mnt/nextcloud-data
cd /mnt/nextcloud-data
```

---

## 2. `docker-compose.yml` erstellen

```bash
nano docker-compose.yml
```

Inhalt:

```yaml
version: '3.8'

services:
  db:
    image: mariadb:11
    container_name: nextcloud-db
    restart: unless-stopped
    volumes:
      - ./db:/var/lib/mysql
    environment:
      - MYSQL_ROOT_PASSWORD=Ändern
      - MYSQL_DATABASE=nextcloud
      - MYSQL_USER=nextcloud
      - MYSQL_PASSWORD=Ändern
    networks:
      - nextcloud-net

  app:
    image: nextcloud:29
    container_name: nextcloud-app
    restart: unless-stopped
    ports:
      - "8080:80"
    volumes:
      - ./nextcloud:/var/www/html
    environment:
      - MYSQL_PASSWORD=Ändern
      - MYSQL_DATABASE=nextcloud
      - MYSQL_USER=nextcloud
      - MYSQL_HOST=nextcloud-db
    depends_on:
      - db
    networks:
      - nextcloud-net

networks:
  nextcloud-net:
```

---

## 3. Container starten

```bash
docker compose up -d
```

Die Container werden im Hintergrund gestartet.

---

## 4. Nextcloud im Browser einrichten

Öffne im lokalen Netzwerk:

```
http://192.168.2.173:8080 #Ggf. Ip-adresse ändern
```

### Einrichtung:

* Admin-Nutzer anlegen (Daten in KeePass o.ä speichern)
* Speicherort bleibt unverändert
* Datenbank:

  * Benutzername: `nextcloud`
  * Passwort: `Ändern`
  * Datenbankname: `nextcloud`
  * Host: `nextcloud-db`

Nach wenigen Minuten ist Nextcloud einsatzbereit.

---

## 5. Datenpersistenz

* App-Daten werden unter: `/mnt/nextcloud-data/nextcloud/` gespeichert
* MariaDB-Daten liegen in: `/mnt/nextcloud-data/db/`

---
