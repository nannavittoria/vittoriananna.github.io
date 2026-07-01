+++
date = '2026-07-01T09:47:44+02:00'
draft = true
title = "Running Claude Science on Windows via WSL"
tags = ["claude-science", "wsl", "hpc", "computational-biology", "ai-tools"]
categories = ["tutorials"]
description = "A step-by-step guide to installing and running Anthropic's Claude Science AI workbench inside WSL2 on Windows"
+++

Anthropic just launched [**Claude Science**](https://www.anthropic.com/news/claude-science-ai-workbench), an AI workbench built for scientific research: literature review, code execution, reproducible figures. It's officially supported on macOS and Linux. If you're on Windows, that means going through WSL2. Here's exactly how I set it up.

## What you'll need

- **Windows 10 (2004+) or Windows 11**, with virtualization enabled in BIOS
- **WSL2**, not WSL1 — the sandbox Claude Science runs code in depends on it
- **Ubuntu 24.04 or newer** inside WSL — Ubuntu 22.04 ships an older version of `bubblewrap` than the sandbox requires
- A **Claude Pro, Max, Team, or Enterprise** plan (Team/Enterprise users need an admin to enable it first)

## 1. Install WSL2 with Ubuntu 24.04

If you don't already have WSL set up, open PowerShell and run:

```powershell
wsl --install -d Ubuntu-24.04
```

Reboot if Windows asks you to, then open **Ubuntu 24.04** from the Start Menu and create your Linux user.

Already have a WSL distro? Check its version and make sure it's WSL2:

```powershell
wsl -l -v
```

If it shows `1` under `VERSION`, upgrade it:

```powershell
wsl --set-version Ubuntu-24.04 2
```

You can confirm you're on WSL2 from inside the Linux shell too — the kernel string will contain `-WSL2`:

```bash
uname -r
# 5.15.146.1-microsoft-standard-WSL2
```

## 2. Install the prerequisites

Inside your Ubuntu terminal:

```bash
sudo apt update && sudo apt install -y curl bubblewrap socat
```

Worth double-checking `bubblewrap` meets the minimum version (0.8.0+) before moving on:

```bash
bwrap --version
```

## 3. Install Claude Science

Run the installer **from inside Ubuntu**, not by downloading a binary through a Windows browser and copying it across — the installer verifies the checksum and keeps you on the stable release channel:

```bash
curl -fsSL https://claude.ai/install-claude-science.sh | bash
```

## 4. Fix the PATH (if needed)

If the installer finishes with a warning that `~/.local/bin` isn't on your `PATH`, add it to your shell profile:

```bash
echo 'export PATH="$HOME/.local/bin:$PATH"' >> ~/.bashrc
source ~/.bashrc
```

Then confirm it's working:

```bash
claude-science --version
```

## 5. Launch it

```bash
claude-science serve --port 8765 --no-browser
```

This prints a local URL you can open in your browser to start a session. If you'd rather it keep running in the background of your current WSL session:

```bash
claude-science serve --port 8765 --no-browser --detached
```

Enjoy!