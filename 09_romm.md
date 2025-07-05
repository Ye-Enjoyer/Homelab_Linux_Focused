# ROMM-Docker.md

## Projektziel

ROMM (Retro Online Metadata Manager) ist ein selbst gehosteter ROM-Manager mit Weboberfläche. Er dient als komfortabler Katalog für ROM-Dateien und bietet automatische Metadaten aus Quellen wie IGDB und Screenscraper.

ROMM läuft auf dem Homeserver als Docker-Container und ist lokal über [http://192.168.2.173:82](http://192.168.2.173:82) (IP ändern wenn nötig) erreichbar.

---

## Voraussetzungen

* Docker & Docker Compose
* UFW mit freigegebenem Port `82/tcp`
* Manuell erstellte Verzeichnisse:

  * `/home/<benutzername>/romm/config/` (enthält `config.yml` und `docker-compose.yml`)
  * `/home/<benutzername>/romm/assets/`
  * `/home/<benutzername>/romm/library/roms/` inkl. Unterordner wie `n64/`
  * `/home/<benutzername>/romm/library/bios/` (optional)
* API-Keys für IGDB und Screenscraper (siehe unten)

---

## Docker Compose Setup

### Verzeichnisstruktur

```text
/home/<benutzername>/romm/
├── assets/                  # Speicher für Savestates etc.
│   └── users/
├── config/                  # Enthält config.yml & docker-compose.yml
├── library/                 # ROM-Verzeichnis
│   ├── bios/               # Manuell erstellt (optional)
│   └── roms/               # Manuell erstellt
│       └── n64/            # Beispiel: Nintendo 64 Plattform
└── docker-compose.yml       # Liegt in /home/<benutzername>/romm/config/
```

Beispiel-Inhalt nach einem Upload:

```text
library/roms/n64/
└── Banjo-Kazooie.zip
```

Die Plattformordner wie `n64/` können entweder manuell angelegt oder durch Uploads über die Weboberfläche automatisch erstellt werden.

### docker-compose.yml

```yaml
version: "3"

volumes:
  mysql_data:
  romm_resources:
  romm_redis_data:

services:
  romm:
    image: rommapp/romm:latest
    container_name: romm
    restart: unless-stopped
    environment:
      - DB_HOST=romm-db
      - DB_NAME=romm
      - DB_USER=<DB_USER>
      - DB_PASSWD=<DB_PASSWORD>
      - ROMM_AUTH_SECRET_KEY=<AUTH_SECRET_KEY>
      - IGDB_CLIENT_ID=<IGDB_CLIENT_ID>
      - IGDB_CLIENT_SECRET=<IGDB_CLIENT_SECRET>
      - SCREENSCRAPER_USER=<SCREENSCRAPER_USER>
      - SCREENSCRAPER_PASSWORD=<SCREENSCRAPER_PASSWORD>
    volumes:
      - romm_resources:/romm/resources
      - romm_redis_data:/redis-data
      - /home/<benutzername>/romm/library:/romm/library
      - /home/<benutzername>/romm/assets:/romm/assets
      - /home/<benutzername>/romm/config:/romm/config
    ports:
      - 82:8080
    depends_on:
      - romm-db

  romm-db:
    image: mariadb:latest
    container_name: romm-db
    restart: unless-stopped
    environment:
      - MARIADB_ROOT_PASSWORD=<DB_ROOT_PASSWORD>
      - MARIADB_DATABASE=romm
      - MARIADB_USER=<DB_USER>
      - MARIADB_PASSWORD=<DB_PASSWORD>
    volumes:
      - mysql_data:/var/lib/mysql
    healthcheck:
      test: ["CMD", "healthcheck.sh", "--connect", "--innodb_initialized"]
      start_period: 30s
      start_interval: 10s
      interval: 10s
      timeout: 5s
```

---

## API Keys

ROMM nutzt externe APIs für automatische Metadaten:

### IGDB (Twitch)

* [https://dev.twitch.tv/console/apps](https://dev.twitch.tv/console/apps)
* Erfordert Twitch-Konto mit 2FA
* Client-ID & Secret in `.env` oder direkt in Compose-Datei eintragen

### Screenscraper

* [https://www.screenscraper.fr/](https://www.screenscraper.fr/)
* Konto erforderlich
* Benutzername & Passwort in Compose-Datei eintragen

**Optional:**

* MobyGames, SteamGridDB, RetroAchievements können zusätzlich integriert werden

---

## Nutzung

### 1. Start

```bash
cd /home/<benutzername>/romm/
docker compose up -d
```

### 2. Zugriff im Browser

```
http://192.168.2.173:82
```

### 3. ROMs hochladen

1. In der linken Sidebar auf **„Upload“** klicken
2. Passende **Plattform** auswählen (z. B. *Nintendo 64*)
3. Auf **„+ ADD“** klicken und eine `.zip`-Datei hochladen
4. Nach dem Hochladen wird das Spiel automatisch einsortiert (z. B. in `roms/n64/`) und mit Metadaten angereichert


### 4. Covers & Infos

* Werden automatisch über IGDB/Screenscraper geladen
* Voraussetzung: gültige API-Keys

---

## Hinweise

* Die Ordnerstruktur sollte nicht manuell verändert werden, wenn die Plattformverwaltung vollständig ROMM überlassen wird
* Es werden idealerweise nur `.zip`-Dateien hochgeladen
* Scans über die Weboberfläche manuell anstoßen, falls ROMs extern hinzugefügt wurden

---

## Troubleshooting

### Webinterface nicht erreichbar?

* Port 82 freigegeben? (`sudo ufw status`)
* Container laufen? (`docker compose ps`)
* Logs prüfen:

  ```bash
  docker compose logs romm --tail=50
  ```

### "Connection reset by peer"

* Port falsch gemappt?
* Intern horcht ROMM auf Port `8080`
* In `ports:` Abschnitt: `82:8080`

---

## Bild vin ROMM GUI

![alt text] 
