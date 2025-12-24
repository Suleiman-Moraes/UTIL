Below is a **complete and reliable** procedure for setting up **SSH with GitHub on Windows**, ensuring you can **commit and push** via both **CMD/PowerShell** and **VS Code**, without using a password or HTTPS token.

---

## Overview of what will be done

1. Verify Git and OpenSSH on Windows
2. Generate SSH key
3. Register the key on GitHub
4. Test the SSH connection
5. Configure the repository to use SSH
6. Validate usage via CMD/PowerShell
7. Validate usage via VS Code
8. Checklist of common problems

---

## 1. Verify prerequisites

### 1.1 Git installed

In **CMD or PowerShell**:

```bash
git --version

```

If not installed: [https://git-scm.com/download/win](https://git-scm.com/download/win)

---

### 1.2 OpenSSH available

Still in the terminal:

```bash
ssh -V

```

If it doesn't exist, enable it in:

> **Settings** → Applications → Optional Resources → OpenSSH Client**

---

## 2. Generate your SSH key

Use **ED25519** (current GitHub default).

```bash
ssh-keygen -t ed25519 -C "your-github-email@email.com"

```

### When prompted:

* **File:** press `Enter` (default)
* **Passphrase:** optional (recommended, but not required)

Files created:

```
C:\Users\YOUR_USERNAME\.ssh\

├── id_ed25519 (private key)

└── id_ed25519.pub (public key)

```

---

## 3. Start the ssh-agent

In **PowerShell (as a normal user)**:

```powershell Get-Service ssh-agent | Set-Service -StartupType Automatic
Start-Service ssh-agent

Add the key to the agent:

bash
ssh-add $env:USERPROFILE\.ssh\id_ed25519


---

## 4. Add the key to GitHub

### 4.1 Copy the public key

bash
type $env:USERPROFILE\.ssh\id_ed25519.pub


Copy **everything**, starting with:

ssh-ed25519 AAAAC3NzaC1...


---

### 4.2 Register on GitHub

1. GitHub → **Settings**
2. **SSH and GPG keys**
3. **New SSH key**
4. Title: `Windows - VSCode`
5. Key type: **Authentication Key**
6. Paste the key
7. Save

---

## 5. Test connection to GitHub

```bash
ssh -T git@github.com

```

Expected response:

```
Hi YOUR_USERNAME! You've successfully authenticated...

```

If this works, **SSH is correctly configured**.

---

## 6. Configure the repository to use SSH

### 6.1 Check current URL

Within the project:

```bash
git remote -v

```

If something like this appears:

```
https://github.com/username/repository.git
```

we need to change it.

---

### 6.2 Switching to SSH

Correct format:

```
git@github.com:username/repository.git
```

Command:

```
bash git remote set-url origin git@github.com:YOUR_USERNAME/YOUR_REPOSITORY.git
```

Validate:

```
bash git remote -v
```

---

## 7. Commit and push via CMD or PowerShell

Standard flow:

```bash
git status
git add .
git commit -m "My message"
git push origin main

```

✔ No password
✔ No token
✔ Uses SSH automatically

---

## 8. Commit and push via VS Code

### 8.1 Ensure VS Code uses the system Git

In VS Code:

* `Ctrl + Shift + P`
* **Git: Select Git Path**
* Select the installed Git (usually automatic)

---

### 8.2 Commit and Push

1. Open the project in VS Code
2. **Source Control** tab
3. Stage changes
4. Commit
5. Push / Sync

VS Code **inherits the system SSH**, so:

* It will not ask for login
* It will not ask for a token
* It will use the same SSH key

---

## 9. Quick validation checklist

✔ `ssh` `git remote -v` works

✔ `git remote -v` uses `git@github.com:`

✔ Push works in the terminal
✔ Push works in VS Code

---

## 10. Common Problems (and Quick Solution)

### ❌ Permission denied (public key)

```bash
ssh-add -l
```

If empty:

```bash
ssh-add ~/.ssh/id_ed25519
```

---

### ❌ VS Code asks for login

→ The repository is still in HTTPS. Fix the `remote origin`

---

### ❌ Multiple SSH keys

Create `~/.ssh/config`:

```text
Host github.com
HostName github.com
User git
IdentityFile ~/.ssh/id_ed25519
```

---
