## Automatische Updates unter Ubuntu Server aktivieren

Diese Anleitung beschreibt, wie automatische Updates auf einem Ubuntu Server konfiguriert werden, um Sicherheitslücken und veraltete Pakete ohne manuelle Eingriffe zu vermeiden.

---

### 1. Paket installieren

```bash
sudo apt update
sudo apt install unattended-upgrades
```

---

### 2. Aktivieren

```bash
sudo dpkg-reconfigure --priority=low unattended-upgrades
```

> Bestätigen mit "Ja" – dies aktiviert Sicherheitsupdates automatisch.

---

### 3. Konfiguration: Welche Repositories sollen automatisch aktualisiert werden?

Datei bearbeiten:

```bash
sudo nano /etc/apt/apt.conf.d/50unattended-upgrades
```

Aktiviere folgende Quellen:

```text
Unattended-Upgrade::Allowed-Origins {
        "${distro_id}:${distro_codename}";
        "${distro_id}:${distro_codename}-security";
        "${distro_id}:${distro_codename}-updates";
        // Optional:
        // "${distro_id}:${distro_codename}-backports";
        // "${distro_id}:${distro_codename}-proposed";
};
```

Erklärung:

* `-security`: Enthält sicherheitsrelevante Updates (z. B. für OpenSSH, sudo, systemd).
* `-updates`: Normale stabile Paketupdates (z. B. rsync, curl, docker.io).
* `-backports` und `-proposed`: Für experimentelle oder vorab getestete Pakete – nicht empfohlen im Homelab.

Außerdem wichtig:

```text
Unattended-Upgrade::DevRelease "false";
```

Damit werden keine Entwickler- oder Beta-Versionen automatisch installiert.

---

### 4. Paket-Blacklist (optional)

In derselben Datei:

```text
Unattended-Upgrade::Package-Blacklist {
    // Beispiel: keine Kernel-Updates
    // "linux-";
};
```

Nur nötig, wenn bestimmte Pakete explizit ausgeschlossen werden sollen.

---

### 5. Intervall für automatische Updates festlegen

Datei bearbeiten:

```bash
sudo nano /etc/apt/apt.conf.d/20auto-upgrades
```

Inhalt:

```text
APT::Periodic::Update-Package-Lists "1";
APT::Periodic::Download-Upgradeable-Packages "1";
APT::Periodic::Unattended-Upgrade "1";
```

Die "1" bedeutet: Täglich ("0" = deaktiviert)

---

### 6. Testlauf (optional)

```bash
sudo unattended-upgrade --dry-run --debug
```

---

### Was wird dadurch automatisch erledigt?

* Täglich werden neue Paketlisten geladen und Updates heruntergeladen
* Sicherheitsupdates und normale Updates werden automatisch installiert
* Du musst **nicht mehr manuell `apt update && apt upgrade` ausführen**
* Neustarts sind nur bei bestimmten Systempaketen notwendig (z. B. nach Kernel-Update)
