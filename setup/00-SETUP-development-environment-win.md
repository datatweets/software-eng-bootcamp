# Complete Development Environment Setup Guide

**Purpose:** Prepare your Windows 10/11 machine for the Software Engineering Fundamentals Bootcamp.

---

## System Requirements

- **OS:** Windows 10 (build 19045+) or Windows 11 (22H2+)
- **RAM:** 8 GB minimum (16 GB recommended)
- **Disk Space:** 20 GB free
- **Administrator access**

---

## 1. Install Git

Git is essential for version control (Day 1: Version Control Systems).

1. Download from [git-scm.com](https://git-scm.com/download/win)
2. Run installer with default settings (or choose your preferred editor)
3. Verify installation:
   ```bash
   git --version
   ```

---

## 2. Create GitHub Account

GitHub hosts our course repositories and examples.

1. Sign up at [github.com](https://github.com)
2. Verify your email
3. Configure Git with your identity:
   ```bash
   git config --global user.name "Your Name"
   git config --global user.email "your.email@example.com"
   ```

**Optional but recommended:**
```bash
git config --global init.defaultBranch main
git config --global core.autocrlf true
```

---

## 3. Install Docker Desktop (WSL 2 Backend)

Docker is used for containerization examples and CI/CD labs (Day 2).

### Step 3.1: Enable WSL 2

Open **PowerShell as Administrator**:
```powershell
wsl --install
dism.exe /online /enable-feature /featurename:VirtualMachinePlatform /all /norestart
```

**Restart your computer.**

After reboot, set WSL 2 as default:
```powershell
wsl --set-default-version 2
```

Verify:
```powershell
wsl --list --verbose
```

### Step 3.2: Install Docker Desktop

1. Download from [Docker's official site](https://docs.docker.com/desktop/setup/install/windows-install/)
2. Run `Docker Desktop Installer.exe`
3. Ensure **"Use WSL 2 based engine"** is checked
4. Complete installation and restart when prompted

### Step 3.3: Configure & Verify

1. Launch Docker Desktop
2. Accept terms (sign-in optional)
3. Go to **Settings → Resources → WSL Integration** — enable your distro
4. Test installation:
   ```bash
   docker run hello-world
   ```
   You should see "Hello from Docker!" message.

---

## 4. Install Visual Studio Code

VS Code is our primary IDE for coding exercises and debugging labs.

1. Download from [code.visualstudio.com](https://code.visualstudio.com)
2. Install with these options:
   - ✅ Add "Open with Code" to context menu
   - ✅ Add to PATH
   - ✅ Register Code as editor for supported file types

### Essential Extensions

Press `Ctrl+Shift+X` in VS Code and install:

- **WSL** (Microsoft) — for seamless Linux integration
- **Docker** (Microsoft) — manage containers from VS Code
- **Python** (Microsoft) — if using Python examples
  
---

## 5. Install Additional Tools (Optional but Recommended)

### Windows Terminal
Modern terminal with tab support:
- Install from Microsoft Store: [Windows Terminal](https://aka.ms/terminal)

### Postman or Insomnia
For API testing:
- [Postman](https://www.postman.com/downloads/) or [Insomnia](https://insomnia.rest/download)

---

## 6. Verify Complete Setup

Open VS Code terminal (`Ctrl+` ` `) or Windows Terminal and run:

```bash
git --version
docker --version
code --version
wsl --status
```

All commands should return version information.

---

## Troubleshooting

| Issue | Solution |
|-------|----------|
| Docker won't start | Check Settings → General → "Use WSL 2 based engine" is enabled |
| Git commands not found | Restart terminal or add Git to PATH manually |
| WSL 2 error | Run `wsl --update` in PowerShell as Admin |
| Docker containers can't access internet | Check Windows Firewall and Docker network settings |


---

Done ✅

Your development environment is ready. 
