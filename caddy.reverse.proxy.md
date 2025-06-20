## 📄 `caddy-reverse-proxy.md`

# Caddy als Reverse Proxy für Nextcloud (Docker-basiert)

Diese Anleitung beschreibt die Einrichtung von **Caddy als Reverse Proxy** für eine lokal gehostete Nextcloud-Instanz, um diese über eine öffentliche Domain (z. B. via DuckDNS) mit **automatischem HTTPS** erreichbar zu machen.

---

## Voraussetzungen

* Nextcloud läuft bereits via Docker (siehe `nextcloud-docker.md`)
* Docker + Docker Compose installiert
* DuckDNS-Domain eingerichtet (z. B. `bataraycloud.duckdns.org`)
* Portweiterleitungen im Router:

  * TCP **80** → Server-IP:80
  * TCP **443** → Server-IP:443
* Extern erreichbar über Internet

---

## 1. Docker-Netzwerk anlegen (einmalig)

```bash
docker network create nextcloud-net
```

---

## 2. Caddy Docker-Setup vorbereiten

### Verzeichnis erstellen:

```bash
mkdir -p ~/docker/caddy
cd ~/docker/caddy
```

### `docker-compose.yml`

```yaml
services:
  caddy:
    image: caddy:2
    container_name: caddy
    restart: unless-stopped
    ports:
      - "80:80"
      - "443:443"
    volumes:
      - ./Caddyfile:/etc/caddy/Caddyfile
      - caddy_data:/data
      - caddy_config:/config
    networks:
      - nextcloud-net

volumes:
  caddy_data:
  caddy_config:

networks:
  nextcloud-net:
    external: true
```

### `Caddyfile`

```caddy
bataraycloud.duckdns.org {
    reverse_proxy nextcloud-app:80 {
        header_up Host {host}
        header_up X-Real-IP {remote}
        header_up X-Forwarded-For {remote}
        header_up X-Forwarded-Proto {scheme}
    }
}
```

---

## 3. Caddy starten

```bash
docker compose up -d
```

---

## 4. Nextcloud für Reverse Proxy konfigurieren

Die folgenden Befehle **im Kontext des Servers** ausführen:

### 4.1 Trusted Domain hinzufügen:

```bash
docker exec -u 33 -it nextcloud-app php occ config:system:set trusted_domains 1 --value=bataraycloud.duckdns.org
```

### 4.2 Protokoll erzwingen:

```bash
docker exec -u 33 -it nextcloud-app php occ config:system:set overwriteprotocol --value=https
```

### 4.3 Reverse Proxy IP als vertrauenswürdig markieren (laut `docker network inspect`):

```bash
docker exec -u 33 -it nextcloud-app php occ config:system:set trusted_proxies 0 --value=172.19.0.2
```

---

## 5. Zugriff testen

Auf einem externen Gerät (z. B. Smartphone ohne WLAN):

```
https://bataraycloud.duckdns.org
```

* ✔ Login-Seite erscheint
* ✔ Keine Weiterleitungsprobleme
* ✔ App-Anmeldung funktioniert

---

## Troubleshooting

### ❌ `502 Bad Gateway`

* Nextcloud nicht im `nextcloud-net`
* Container-Namen falsch
* Caddy kann Nextcloud nicht auf Port 80 erreichen

### ❌ Login-Seite lädt ewig, bleibt hängen:

* `overwriteprotocol` nicht gesetzt
* `trusted_proxies` fehlt
* App-Cookies blockiert durch Reverse Proxy


## Was macht Caddy?

Caddy ist ein Webserver, der als Reverse Proxy vor Nextcloud sitzt. Das bedeutet, er nimmt Anfragen aus dem Internet entgegen und leitet sie an den internen Nextcloud-Container weiter.

Dabei kümmert sich Caddy automatisch um HTTPS-Verschlüsselung über Let's Encrypt. Es ist keine manuelle Zertifikatsverwaltung notwendig.

Durch das Setzen spezieller HTTP-Header teilt Caddy Nextcloud mit, dass die Anfrage ursprünglich über HTTPS kam und von außen stammt. So funktioniert der Login korrekt, und es kommt nicht zu Weiterleitungsfehlern oder abgelehnten Verbindungen.

Caddy erkennt automatisch die konfigurierte Domain (z. B. bataraycloud.duckdns.org) und sorgt dafür, dass alle Zugriffe dorthin beim richtigen Container landen.

Insgesamt macht Caddy den Zugriff auf Nextcloud über das Internet einfach, sicher und wartungsarm.

