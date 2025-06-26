# Cron-Jobs

### Öffentliche IP regelmäßig bei DuckDNS aktualisieren

Da sich die IP-Adresse des Internetanschlusses ändern kann, sollte sie automatisch bei DuckDNS aktualisiert werden.

#### 1. Cron installieren:

```bash
sudo apt install cron
sudo systemctl enable cron
sudo systemctl start cron
```

#### 2. Cronjob anlegen:

```bash
crontab -e
```

In der geöffneten Datei einfügen (Token und Domain ersetzen):

```bash
*/5 * * * * curl -s "https://www.duckdns.org/update?domains=<deine-subdomain>&token=<dein-token>&ip=" > /dev/null
```

Die URL überträgt die aktuelle IP alle 5 Minuten an DuckDNS.
