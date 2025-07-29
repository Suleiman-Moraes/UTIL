# 🐳 Docker Engine on Windows (No Docker Desktop)

This guide shows how to install and run Docker Engine inside WSL2 on Windows without Docker Desktop.

✅ Goal:
Open a terminal in any folder and run:

```bash
docker compose up -d
docker ps
```

Using Docker installed via WSL2.

---

## 📋 Prerequisites

* Windows 10 (2004+) or Windows 11
* WSL2 enabled
* A Linux distribution installed (e.g., Ubuntu)
* Windows Terminal or PowerShell
* Optional: Git Bash

---

## 🔧 Step 1 – Install WSL2

In PowerShell (as Administrator):

```powershell
wsl --install
```

If already using WSL1:

```powershell
wsl --update
wsl --set-default-version 2
```

---

## 🧱 Step 2 – Install a Linux Distribution

If not installed:

* Run: wsl --install -d Ubuntu
* Or install Ubuntu via Microsoft Store

Then open Ubuntu and set up your user/password.

---

## 🐳 Step 3 – Install Docker Engine inside WSL2

In the Ubuntu terminal:

```bash
sudo apt update
sudo apt install -y ca-certificates curl gnupg lsb-release
```

Add Docker's GPG key:

```bash
sudo install -m 0755 -d /etc/apt/keyrings
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /etc/apt/keyrings/docker.gpg
sudo chmod a+r /etc/apt/keyrings/docker.gpg
```

Add Docker's repository:

```bash
echo \
  "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.gpg] \
  https://download.docker.com/linux/ubuntu jammy stable" | \
  sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
```

Install Docker:

```bash
sudo apt update
sudo apt install -y docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin
```

---

## 🧪 Step 4 – Test Docker

Start Docker and check version:

```bash
sudo service docker start
docker version
```

(Optional) Avoid needing sudo:

```bash
sudo usermod -aG docker $USER
newgrp docker
```

---

## ⚙️ Step 5 – Auto-start Docker in WSL (Optional)

### ✅ Option: Automatically start Docker on WSL launch

1. Create script:

```bash
sudo nano /usr/local/bin/start-docker
```

Paste:

```bash
#!/bin/sh
if (! pgrep -x dockerd > /dev/null); then
  sudo dockerd > /var/log/dockerd.log 2>&1 &
  echo "Docker daemon started."
fi
```

Save: Ctrl+O → Enter → Ctrl+X

2. Make it executable:

```bash
sudo chmod +x /usr/local/bin/start-docker
```

3. Add to .bashrc:

```bash
nano ~/.bashrc
```

Append at the end:

```bash
# Start Docker if not running
/usr/local/bin/start-docker
```

Then reload:

```bash
source ~/.bashrc
```

Now Docker will auto-start with your WSL session.

Alternatively, run manually:

```bash
sudo service docker start
```

---

## 📦 Step 6 – Enable docker CLI in Windows terminal (Optional)

Stop WSL Docker service:

```bash
sudo service docker stop
```

Then run with Unix + TCP socket:

```bash
sudo dockerd -H unix:///var/run/docker.sock -H tcp://0.0.0.0:2375
```

---

## 📁 Step 7 – Install docker-compose on Windows (Optional)

1. Download latest docker-compose.exe:
   🔗 [https://github.com/docker/compose/releases](https://github.com/docker/compose/releases)

2. Rename it to:

```
docker-compose.exe
```

3. Move it to:

```
C:\Program Files\Docker CLI\
```

4. Add that folder to the system PATH.

5. Test it in PowerShell:

```powershell
docker-compose version
```

---

## ✅ Final Usage

To run your containers:

```bash
docker compose up -d
docker ps
```

You’re now running Docker Engine inside WSL2 without Docker Desktop!

