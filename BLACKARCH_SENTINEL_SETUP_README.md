# BlackArch Setup Guide for Sentinel

This README gives you a clean, repeatable setup flow for a fresh BlackArch Linux system.

The goal is to avoid the usual failure points:
- partial installs
- broken repo config
- missing dependencies
- moving too far ahead before verifying what actually worked

---

## Core Rule

Combine setup commands that do not depend on you checking output first.

Keep verification, repo repair, and recovery steps separate.

---

## Safe to run as one block

This first block is usually safe on a fresh, healthy BlackArch install:

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

### Why this block is safe

These commands all belong to the same setup path:
- update the system
- install keyrings
- initialize and populate signing keys
- install core packages

---

## Safe as a second block

Once the first block finishes cleanly, this is also safe:

```bash
python -m venv ~/sentinel-env
source ~/sentinel-env/bin/activate
pip install --upgrade pip setuptools wheel
mkdir -p ~/sentinel
cd ~/sentinel
git init
```

### Why this block is safe

This is user-space setup. It does not modify repo config or key infrastructure, so it is lower risk.

---

## Safe as a third block

Your recon and tooling installs can also be grouped:

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

## Keep these separate

These should not be blindly bundled into the main install block.

### Repo check

```bash
grep -A 2 '\[blackarch\]' /etc/pacman.conf
```

**Reason:** You want to see whether the BlackArch repo is already configured before trying to repair it.

### Repo repair

```bash
curl -O https://blackarch.org/strap.sh
chmod +x strap.sh
sudo ./strap.sh
sudo pacman -Syu
```

**Reason:** Only run this if needed. It changes repo configuration, so do not mix it into a block unless the repo is missing or broken.

### Verification commands

```bash
git --version
python --version
pip --version
node -v
npm -v
nmap --version
which tcpdump
which masscan
which sqlmap
sudo pacman -Qk
```

**Reason:** These are checkpoints. Run them after installs so you can spot exactly what failed.

---

## Best practical version

If you want the safest streamlined flow, do it in four chunks.

### Block 1 — system + core packages

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

### Block 2 — verify repo and packages

```bash
grep -A 2 '\[blackarch\]' /etc/pacman.conf
git --version
python --version
pip --version
node -v
npm -v
nmap --version
```

### Block 3 — tooling

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

### Block 4 — env + workspace

```bash
python -m venv ~/sentinel-env
source ~/sentinel-env/bin/activate
pip install --upgrade pip setuptools wheel
mkdir -p ~/sentinel
cd ~/sentinel
git init
which tcpdump
which masscan
which sqlmap
sudo pacman -Qk
```

---

## One thing to keep separate

Do not keep this in the same block:

```bash
sudo pacman-key --refresh-keys
```

### Why

This command can hang, stall, or fail because of keyserver or networking issues. It is better as an optional separate step.

Run it only if key problems show up.

---

## Safest answer in one sentence

You can safely combine:
- pacman update and install steps
- grouped package installs
- user-space environment and workspace setup

But keep these separate checkpoints:
- repo detection
- strap-based repo repair
- version checks
- package integrity checks

---

## Compact version for a brand-new BlackArch machine

Run this first:

```bash
sudo pacman -Syu &&
sudo pacman -S --needed archlinux-keyring blackarch-keyring &&
sudo pacman-key --init &&
sudo pacman-key --populate archlinux blackarch &&
sudo pacman -S --needed \
  git base-devel python python-pip nodejs npm curl wget nmap net-tools openssh tmux htop jq
```

Then stop and check output before continuing.

Run this next:

```bash
sudo pacman -S --needed wireshark-cli tcpdump masscan nikto dirb hydra sqlmap
```

Then run this:

```bash
python -m venv ~/sentinel-env &&
source ~/sentinel-env/bin/activate &&
pip install --upgrade pip setuptools wheel &&
mkdir -p ~/sentinel &&
cd ~/sentinel &&
git init
```

---

## Recommended workflow

1. Run Block 1.
2. Review output before proceeding.
3. Run verification checks.
4. Install tooling.
5. Create the Python environment and Sentinel workspace.
6. Run final integrity checks.

---

## Final note

What breaks setups most often is not one big error. It is a series of small errors that get ignored because everything was chained together too aggressively.

This README is built to prevent that.
