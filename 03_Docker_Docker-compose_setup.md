## Docker & Compose Installation (Ubuntu)
Kurze Anleitung zur Docker und Compose Installation

### 1. Vorbereitungen

```bash
sudo apt update
sudo apt install -y ca-certificates curl gnupg
```

### 2. Docker GPG-Schlüssel hinzufügen

```bash
sudo install -m 0755 -d /etc/apt/keyrings
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | \
  sudo gpg --dearmor -o /etc/apt/keyrings/docker.gpg
sudo chmod a+r /etc/apt/keyrings/docker.gpg
```

### 3. Docker-Repository einbinden

```bash
echo \
  "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.gpg] \
  https://download.docker.com/linux/ubuntu \
  $(. /etc/os-release && echo \"$VERSION_CODENAME\") stable" | \
  sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
```

### 4. Docker Engine & Compose installieren

```bash
sudo apt update
sudo apt install -y docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin
```

### 5. Docker als normaler Nutzer verwenden

```bash
sudo usermod -aG docker $USER
newgrp docker
```

---

### Test:

```bash
docker --version
docker compose version
```

> Sollte die Aktuellen Versionen von Docker & Docker Compose zeigen
