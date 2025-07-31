# 🐳 Docker Engine on Windows (No Docker Desktop)

This guide shows how to install and run Docker Engine inside WSL2 on Windows without Docker Desktop.

✅ Goal:  
Open a terminal in any folder and run:

```bash
docker compose up -d
docker ps
````

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

* Run: `wsl --install -d Ubuntu`
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

Check version:

```bash
docker version
```

(Optional) Avoid needing `sudo`:

```bash
sudo usermod -aG docker $USER
newgrp docker
```

---

## ⚙️ Step 5 – Auto-start Docker in WSL

Docker does **not** start automatically in WSL2, and `systemctl` is not supported. So we’ll create a lightweight script that launches the Docker daemon whenever your WSL terminal opens.

### ✅ Create the auto-start script

1. Create script file:

```bash
sudo nano /usr/local/bin/start-docker
```

Paste this:

```bash
#!/bin/bash

# Start Docker daemon if not already running
if ! pgrep -x dockerd > /dev/null; then
  echo "Starting Docker daemon..."
  nohup sudo dockerd > "$HOME/dockerd.log" 2>&1 &
fi
```

> This avoids permission issues and logs to `~/dockerd.log`.

2. Make it executable:

```bash
sudo chmod +x /usr/local/bin/start-docker
```

3. Add it to your shell startup:

If using Bash:

```bash
nano ~/.bashrc
```

If using Zsh:

```bash
nano ~/.zshrc
```

Append this at the end:

```bash
# Automatically start Docker if it's not running
/usr/local/bin/start-docker
```

Then reload the shell:

```bash
source ~/.bashrc  # or source ~/.zshrc
```

> ✅ Now Docker will **start automatically** every time you launch WSL.

---

## 📦 Step 6 – Enable docker CLI in Windows terminal (Optional)

If you want to use Docker CLI from PowerShell or CMD instead of inside WSL:

Stop WSL Docker service:

```bash
sudo pkill dockerd
```

Then run:

```bash
sudo dockerd -H unix:///var/run/docker.sock -H tcp://0.0.0.0:2375
```

This opens access to Docker over TCP (for tools like Portainer or Docker CLI in Windows).
Use with caution — no auth is enabled on port 2375.

---

## 📁 Step 7 – Install docker-compose on Windows (Optional)

1. Download latest `docker-compose.exe`:
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

You’re now running Docker Engine inside WSL2 — **no Docker Desktop required!**
