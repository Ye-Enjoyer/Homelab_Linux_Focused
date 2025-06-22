# System-Monitoring mit Netdata (Docker)

Diese Anleitung beschreibt die Einrichtung von [Netdata](https://www.netdata.cloud/) zur Überwachung des Homelab-Servers. Netdata liefert in Echtzeit detaillierte Informationen zu CPU, RAM, Netzwerk, Prozessen, Docker-Containern und mehr – direkt im Browser und vollständig lokal.

---

## Setup mit Docker

Netdata wird im Docker-Container betrieben und lauscht nur im lokalen Netzwerk auf Port `19999`.

### Voraussetzungen

- Docker und Docker Compose sind installiert
- UFW-Firewall ist aktiv und entsprechend freigeschaltet
- Zugriff erfolgt nur im lokalen Netzwerk

### Start des Containers

```bash
docker run -d \
  --name=netdata \
  -p 19999:19999 \
  --cap-add=SYS_PTRACE \
  -v netdataconfig:/etc/netdata \
  -v netdatalib:/var/lib/netdata \
  -v netdatacache:/var/cache/netdata \
  -v /etc/passwd:/host/etc/passwd:ro \
  -v /etc/group:/host/etc/group:ro \


```
## Zugriff im Browser

Lokal: http://localhost/IP:19999

Zugriff absichern
Falls UFW aktiviert ist, Zugriff nur im Heimnetz erlauben:

```bash
sudo ufw allow from 192.168.178.0/24 to any port 19999 proto tcp


