# 🛡️ BlackArch Setup Guide (Sentinel Build)

This guide provides a **clean, repeatable setup process** for a fresh BlackArch Linux system.
It is structured to avoid the most common failure points: partial installs, broken repos, and missing dependencies.

---

# ⚠️ Core Rule

> **Combine setup commands that do not require inspection.**
> **Separate anything that requires validation or could fail silently.**

---

# 📦 Block 1 — System + Core Packages

Run this as a single block on a fresh install:

```bash
sudo pacman -Syu
sudo pacman -S --needed archlinux-keyring blackarch-keyring
sudo pacman-key --init
sudo pacman-key --populate archlinux blackarch
sudo pacman -S --needed \
  git \
  base-devel \
  python \
  python-pip \
  nodejs \
  npm \
  curl \
  wget \
  nmap \
  net-tools \
  openssh \
  tmux \
  htop \
  jq
```

### What this does:

* Updates system packages
* Installs and initializes keyrings
* Sets up essential development + networking tools

---

# 🔍 Block 2 — Verification (DO NOT SKIP)

Run these **separately** and review output:

```bash
grep -A 2 '\[blackarch\]' /etc/pacman.conf
git --version
python --version
pip --version
node -v
npm -v
nmap --version
```

### Purpose:

* Confirms repo is active
* Ensures core tools installed correctly
* Detects silent failures early

---

# 🧰 Block 3 — Security / Recon Tooling

Install your working toolkit:

```bash
sudo pacman -S --needed \
  wireshark-cli \
  tcpdump \
  masscan \
  nikto \
  dirb \
  hydra \
  sqlmap
```

---

# 🧪 Block 4 — Environment + Workspace

Set up your development environment:

```bash
python -m venv ~/sentinel-env
source ~/sentinel-env/bin/activate
pip install --upgrade pip setuptools wheel
mkdir -p ~/sentinel
cd ~/sentinel
git init
```

### Verify tools + system integrity:

```bash
which tcpdump
which masscan
which sqlmap
sudo pacman -Qk
```

---

# 🧠 Optional (Only if Needed)

## Repo Check

```bash
grep -A 2 '\[blackarch\]' /etc/pacman.conf
```

## Repo Repair (ONLY if missing/broken)

```bash
curl -O https://blackarch.org/strap.sh
chmod +x strap.sh
sudo ./strap.sh
sudo pacman -Syu
```

---

# ⚠️ Important Warning

Do **NOT** include this in your main install block:

```bash
sudo pacman-key --refresh-keys
```

### Why:

* Can hang or fail due to network/keyserver issues
* Should only be run if key errors appear

---

# 🧩 Compact Execution Version

For experienced runs:

```bash
sudo pacman -Syu &&
sudo pacman -S --needed archlinux-keyring blackarch-keyring &&
sudo pacman-key --init &&
sudo pacman-key --populate archlinux blackarch &&
sudo pacman -S --needed \
  git base-devel python python-pip nodejs npm curl wget nmap net-tools openssh tmux htop jq
```

Then stop and verify.

---

Then:

```bash
sudo pacman -S --needed wireshark-cli tcpdump masscan nikto dirb hydra sqlmap
```

---

Then:

```bash
python -m venv ~/sentinel-env &&
source ~/sentinel-env/bin/activate &&
pip install --upgrade pip setuptools wheel &&
mkdir -p ~/sentinel &&
cd ~/sentinel &&
git init
```

---

# ✅ Summary

### Safe to combine:

* `pacman` update/install operations
* grouped package installs
* user-space setup (venv, directories)

### Keep separate:

* repo detection
* repo repair (`strap.sh`)
* version checks
* system integrity checks

---

# 🚀 Next Step

After completing this setup:

* Confirm no errors occurred
* Verify tools respond correctly
* Proceed to Sentinel integration / automation layer

---
