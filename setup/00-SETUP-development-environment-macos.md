# Complete Development Environment Setup Guide (macOS)

**Purpose:** Prepare your macOS machine for the Software Engineering Fundamentals Bootcamp.

---

## System Requirements

- **OS:** macOS 12 (Monterey) or later
- **RAM:** 8 GB minimum (16 GB recommended)
- **Disk Space:** 20 GB free
- **Administrator access**
- **Processor:** Intel or Apple Silicon (M1/M2/M3)

---

## 1. Install Homebrew (macOS Package Manager)

Homebrew simplifies installing development tools on macOS.

1. Open **Terminal** (⌘ + Space, type "Terminal")
2. Install Homebrew:
   ```bash
   /bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"
   ```
3. Follow the on-screen instructions
4. **For Apple Silicon (M1/M2/M3) only**, add Homebrew to your PATH:
   ```bash
   echo 'eval "$(/opt/homebrew/bin/brew shellenv)"' >> ~/.zprofile
   eval "$(/opt/homebrew/bin/brew shellenv)"
   ```
5. Verify installation:
   ```bash
   brew --version
   ```

---

## 2. Install Git

Git is essential for version control (Day 1: Version Control Systems).

### Option A: Using Homebrew (Recommended)
```bash
brew install git
```

### Option B: Xcode Command Line Tools
```bash
xcode-select --install
```

### Verify Installation
```bash
git --version
```

---

## 3. Create GitHub Account

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
git config --global core.autocrlf input
```

---

## 4. Install Docker Desktop

Docker is used for containerization examples and CI/CD labs (Day 2).

### Step 4.1: Download Docker Desktop

Choose the correct version for your Mac:

- **Apple Silicon (M1/M2/M3):** [Download Apple Silicon version](https://desktop.docker.com/mac/main/arm64/Docker.dmg)
- **Intel Mac:** [Download Intel version](https://desktop.docker.com/mac/main/amd64/Docker.dmg)

Or visit [Docker's official site](https://docs.docker.com/desktop/setup/install/mac-install/) to download.

### Step 4.2: Install Docker Desktop

1. Open the downloaded `.dmg` file
2. Drag **Docker** icon to **Applications** folder
3. Open **Docker** from Applications (or Spotlight)
4. **Grant permissions** when macOS asks for:
   - Privileged access (required for networking)
   - Accessibility permissions (if prompted)
5. Accept the Docker Subscription Service Agreement
6. Choose sign-in or skip (optional)

### Step 4.3: Configure & Verify

1. Wait for Docker to start (menu bar icon should show "Docker Desktop is running")
2. Open Terminal and test:
   ```bash
   docker run hello-world
   ```
   You should see "Hello from Docker!" message.

3. Verify Docker Compose:
   ```bash
   docker compose version
   ```

**Docker Desktop Settings (optional but recommended):**
- Go to **Settings → Resources** and allocate:
  - **CPUs:** 4 or more
  - **Memory:** 4-8 GB
  - **Disk:** 60+ GB

---

## 5. Install Visual Studio Code

VS Code is our primary IDE for coding exercises and debugging labs.

### Option A: Download from Website (Recommended)

1. Download from [code.visualstudio.com](https://code.visualstudio.com)
2. Open the downloaded `.zip` file
3. Drag **Visual Studio Code** to **Applications** folder
4. Launch VS Code from Applications

### Option B: Using Homebrew

```bash
brew install --cask visual-studio-code
```

### Add VS Code to PATH

1. Open VS Code
2. Press `⌘ + Shift + P` (Command Palette)
3. Type "shell command" and select **"Shell Command: Install 'code' command in PATH"**
4. Verify:
   ```bash
   code --version
   ```

### Essential Extensions

Press `⌘ + Shift + X` in VS Code and install:

- **Docker** (Microsoft) — manage containers from VS Code
- **GitLens** — supercharge Git capabilities
- **Python** (Microsoft) — if using Python examples

---

## 6. Install Additional Tools (Optional but Recommended)

### iTerm2 (Enhanced Terminal)

Better alternative to default Terminal:
```bash
brew install --cask iterm2
```

### Postman or Insomnia

For API testing labs:
```bash
# Postman
brew install --cask postman

# Or Insomnia
brew install --cask insomnia
```


---

## 7. Verify Complete Setup

Open Terminal (or iTerm2) and run:

```bash
git --version
docker --version
docker compose version
code --version
brew --version
```


---

## Troubleshooting

| Issue | Solution |
|-------|----------|
| Docker won't start | Check System Settings → Privacy & Security → Allow Docker; Restart Mac |
| "Docker.app can't be opened" | Right-click Docker.app → Open → Allow |
| Homebrew command not found (Apple Silicon) | Run the PATH command from Step 1 again |
| Git commands not found | Run `xcode-select --install` |
| Docker performance issues | Increase Resources in Docker Settings → Resources |
| Permission denied errors | Run `sudo chmod` or check file permissions |

---

## Platform-Specific Notes

### Apple Silicon (M1/M2/M3) Users

- Docker Desktop uses **Rosetta 2** for Intel-based images
- Some older images may run slower; prefer ARM64-native images when available
- If Rosetta 2 isn't installed, Docker will prompt you to install it

### Intel Mac Users

- Docker Desktop uses native virtualization (HyperKit)
- All images run at native speed

---

**Done ✅**  
Your development environment is ready.
