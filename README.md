# Home Server Setup – Dokumentation

Diese Dokumentation ist für ein privaten Homeserver auf Basis eines Intel NUC mit Ubuntu Server (barebones). Ziel ist es, eine stabile, nachvollziehbar aufgebaute Umgebung für selbst gehostete Anwendungen zu schaffen. Alle Konfigurationen und Schritte werden hier dokumentiert.

## Ziel

- Einrichtung und Betrieb eines eigenen Homeservers
- Vollständige technische Dokumentation für Wartung und Wiederaufbau
- Sicherheit, Übersicht und Flexibilität als Grundlage für spätere Dienste

## Systemübersicht

- Hardware: Intel NUC
- Betriebssystem: Ubuntu Server (barebones)
- Netzwerk:
  - Server über LAN an TP-Link Switch
  - Switch angebunden per Powerline an Router im Keller
  - Zugriff über das lokale Netzwerk (Headless-Betrieb)
 
## Netzwerkübersicht

Der Homeserver (Intel NUC) und der Client-PC befinden sich im selben Raum und sind über Powerline mit dem Router im Keller verbunden. Beide Geräte befinden sich im lokalen Subnetz `192.168.2.0/24`.


                    [Router] (Keller)
                 IP: 192.168.2.1/24
                        │
           ┌────────────┴────────────┐
           │     Stromnetz (LAN)     │
           └────────────┬────────────┘
              [Powerline Verbindung]
                        │
                [Powerline Adapter]
                        │
           Standort: Zimmer (lokales Netzwerk)
           ┌────────────┴────────────┐
           │                         │
     [Client-PC]               [Intel NUC]
     IP: 192.168.2.132/24       IP: 192.168.2.173/24
                                


## Dokumentierte Komponenten

### SSH-Zugang

Die Einrichtung des SSH-Zugangs mit Schlüssel-Authentifizierung ist im Detail in [`01_ssh_setup.md`](./01_ssh_setup.md) dokumentiert. SSH sollte immer der erste dienst sein der eingerichtet wird.

### UFW Firewall-Konfiguration (Beispiel)

```bash
# Default-Regeln
sudo ufw default deny incoming
sudo ufw default allow outgoing
sudo ufw default deny routed

# SSH nur intern erlaubt
sudo ufw allow from 1.1.1.1 to any port 22 proto tcp #Ip-ändern

# HTTP/HTTPS öffentlich (für Caddy/Nextcloud)
sudo ufw allow 80/tcp
sudo ufw allow 443/tcp

# Netdata nur intern erreichbar
sudo ufw allow from 1.1.1.1 to any port 19999 proto tcp #Ip-ändern

# Aktivieren & Logging
sudo ufw logging on
sudo ufw enable

  
```
# Struktur des Repositories

```plaintext
.
├── README.md                         # Projektübersicht und Zielbeschreibung,
├── Bilder                            # Bilder für Visual Sneak Peek
├── ssh_setup.md                      # Detaillierte Anleitung zur SSH-Einrichtung und ufw Install
├── auto_updates.md                   # Automatisierte Wartung/Updates
├── docker_docker-compose_setup.md    # Docker/Docker-Compose Installation
├── nextcloud_docker.md               # Nextcloud setup mit Docker
├── caddy_reverse_proxy.md            # Einrichtung des reverse-proxy um Nextcloud von außen zu erreichen (Docker)
├── fail2ban.md                       # Für extra sicherheit, Setup und beispiele
├── cron.md                           # Automatisierung für alles mögliche
├── netdata.md                        # Server-Monitoring, Einrichtung mit Docker beschrieben 
├── romm.md                           # Host Retro games und lassen sich im Browser spielen
├── stromspareinstellungen.md         # Einrichtung für Stromsparmaßnahmen
├── glance.md                         # Dashboard für den server und leichtes Monitoring   
└── tbd
