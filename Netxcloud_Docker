# Nextcloud auf Intel NUC mit Docker und NGINX Proxy Manager

## Voraussetzungen

* Intel NUC mit Ubuntu Server (SSH eingerichtet)
* Externe SSD (mindestens 1 TB, ext4 formatiert)
* Internetzugang
* TP-Link Switch + Powerline-Verbindung zum Speedport-Router
* Domain via DuckDNS (z. B. `bataraycloud.duckdns.org`)

---

## 1. SSD vorbereiten

```bash
sudo mkfs.ext4 /dev/sdb1
lsblk
sudo mkdir /mnt/ssd
sudo mount /dev/sdb1 /mnt/ssd
```

### SSD dauerhaft mounten

```bash
sudo blkid /dev/sdb1
# Ausgabe z.B.: UUID="1234-ABCD"
sudo nano /etc/fstab
# Eintragen:
UUID=1234-ABCD /mnt/ssd ext4 defaults 0 2
```

---

## 2. System aktualisieren und Docker installieren

```bash
sudo apt update && sudo apt upgrade
sudo apt install docker.io docker-compose
sudo systemctl enable docker --now
```

---

## 3. Portainer installieren

```bash
sudo docker volume create portainer_data

sudo docker run -d -p 9000:9000 -p 8000:8000 \
  --name portainer \
  --restart=always \
  -v /var/run/docker.sock:/var/run/docker.sock \
  -v portainer_data:/data \
  portainer/portainer-ce
```

Zugriff: http\://<IP-des-NUC>:9000

---

## 4. Nextcloud per Docker Compose

### Verzeichnis anlegen

```bash
mkdir ~/nextcloud && cd ~/nextcloud
```

### `docker-compose.yml` erstellen:

```yaml
version: '3'

services:
  db:
    image: mariadb
    restart: always
    command: --transaction-isolation=READ-COMMITTED --log-bin=binlog --binlog-format=ROW
    volumes:
      - db:/var/lib/mysql
    environment:
      - MYSQL_ROOT_PASSWORD=strong-root-password
      - MYSQL_PASSWORD=nextcloud
      - MYSQL_DATABASE=nextcloud
      - MYSQL_USER=nextcloud

  app:
    image: nextcloud
    restart: always
    ports:
      - 8080:80
    volumes:
      - nextcloud:/var/www/html
      - /mnt/ssd/nextcloud_data:/var/www/html/data
    environment:
      - MYSQL_PASSWORD=nextcloud
      - MYSQL_DATABASE=nextcloud
      - MYSQL_USER=nextcloud
      - MYSQL_HOST=db
      - NEXTCLOUD_TRUSTED_DOMAINS=bataraycloud.duckdns.org
      - NEXTCLOUD_ADMIN_USER=admin
      - NEXTCLOUD_ADMIN_PASSWORD=dein-passwort
    depends_on:
      - db

volumes:
  db:
  nextcloud:
```

### Starten:

```bash
sudo docker-compose up -d
```

---

## 5. Nextcloud Cronjob (wichtig für Wartung)

```bash
crontab -e
```

Einfügen:

```cron
*/5 * * * * docker exec -u www-data nextcloud_app_1 php -f /var/www/html/cron.php
```

---

## 6. DuckDNS Container

### `duckdns.yml` erstellen

```yaml
version: '3'

services:
  duckdns:
    image: linuxserver/duckdns
    container_name: duckdns
    environment:
      - PUID=1000
      - PGID=1000
      - SUBDOMAINS=bataraycloud
      - TOKEN=dein-token-von-duckdns
      - LOG_FILE=true
    restart: unless-stopped
```

### Starten:

```bash
sudo docker-compose -f duckdns.yml up -d
```

---

## 7. NGINX Proxy Manager (NPM)

### `nginx-proxy-manager.yml` erstellen

```yaml
version: '3'

services:
  npm:
    image: jc21/nginx-proxy-manager:latest
    container_name: npm
    restart: unless-stopped
    ports:
      - "80:80"
      - "443:443"
      - "81:81"
    volumes:
      - npm_data:/data
      - npm_letsencrypt:/etc/letsencrypt

volumes:
  npm_data:
  npm_letsencrypt:
```

### Starten:

```bash
sudo docker-compose -f nginx-proxy-manager.yml up -d
```

Zugriff: http\://<IP-des-NUC>:81

---

## 8. NGINX Proxy Host einrichten

* Domain: `bataraycloud.duckdns.org`
* Scheme: **http**
* Forward Hostname/IP: `172.18.0.3`
* Forward Port: `80`
* SSL:

  * Let’s Encrypt-Zertifikat anfordern
  * Force SSL ✓
  * Block Common Exploits ✓
  * HTTP/2 Support ✓
  * Cache Assets ✓

---

## 9. Portweiterleitung im Router (Speedport)

| Port | Protokoll | Ziel-IP       | Ziel-Port |
| ---- | --------- | ------------- | --------- |
| 80   | TCP       | 192.168.2.121 | 80        |
| 443  | TCP       | 192.168.2.121 | 443       |

---

## 10. Backup-Empfehlung

### Dateien sichern:

```bash
sudo rsync -a /mnt/ssd/nextcloud_data /pfad/zum/backup/
```

### Datenbank sichern:

```bash
sudo docker run --rm \
  --network nextcloud_default \
  -e MYSQL_PWD=strong-root-password \
  mariadb \
  mysqldump -h nextcloud_db_1 -u root nextcloud > backup.sql
```

---

##Zugriff via: [https://bataraycloud.duckdns.org](https://bataraycloud.duckdns.org)

---

Letzter Test: Smartphone im mobilen Netz → [https://bataraycloud.duckdns.org](https://bataraycloud.duckdns.org) sollte erreichbar sein.
