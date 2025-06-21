# Home Server Setup – Dokumentation

Diese Dokumentation ist für ein privaten Homeserver auf Basis eines Intel NUC mit Ubuntu Server (barebones). Ziel ist es, eine stabile, nachvollziehbar aufgebaute Umgebung für selbst gehostete Anwendungen zu schaffen. Alle Konfigurationen und Schritte werden hier versioniert dokumentiert.

## Ziel

- Einrichtung und Betrieb eines eigenen Homeservers
- Vollständige technische Dokumentation für Wartung und Wiederaufbau
- Sicherheit, Übersicht und Flexibilität als Grundlage für spätere Dienste

## Systemübersicht

- Hardware: Intel NUC
- Betriebssystem: Ubuntu Server 22.04 (barebones)
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
                                SSH: Port 22 offen


## Dokumentierte Komponenten

### SSH-Zugang

Die Einrichtung des SSH-Zugangs mit Schlüssel-Authentifizierung ist im Detail in [`01_ssh_setup.md`](./01_ssh_setup.md) dokumentiert. SSH sollte immer der erste dienst sein der eingerichtet wird.

Enthaltene Schritte:
- Installation des OpenSSH-Servers
- Schlüsselerzeugung auf dem Client
- Autorisierung auf dem Server
- Deaktivierung von Passwort-Login
- Einrichtung einer Firewall mit UFW

## Struktur des Repositories

```plaintext
.
├── README.md                         # Projektübersicht und Zielbeschreibung
├── ssh-setup.md                      # Detaillierte Anleitung zur SSH-Einrichtung und ufw Install
├── auto-updates.md                   # Automatisierte Wartung/Updates
├── docker_docker-compose_setup.md    # Docker/Docker-Compose Installation
├── nextcloud-docker.md               # Nextcloud setup mit Docker
├── caddy-reverse-proxy.md            # einrichtung des reverse-proxy um Nextcloud von außen zu erreichen (Docker)
└── tbd
