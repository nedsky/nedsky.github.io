---
title: "How to Enable Sound for xRDP on Ubuntu (ESXi/Virtual Machines)"
date: 2024-12-17
categories:
  - Ubuntu
  - Linux
tags:
  - xRDP
  - Ubuntu
  - Audio
  - Remote Desktop
  - ESXi
  - Virtual Machines
---

If you have installed Ubuntu on an ESXi VM and are frustrated by the lack of audio when connecting via Remote Desktop Protocol (xRDP), you're not alone. The issue is that xRDP doesn't include the audio sink modules needed to stream sound to your local machine.

## The Solution

I recommend using an **automated installer script** maintained by C-Nergy as the most reliable approach. This tool automatically detects the Ubuntu version and compiles necessary PulseAudio or PipeWire modules.

## Prerequisites

Before running the installer, ensure:
- Your system is up to date
- "Source Code" repositories are enabled via **Software & Updates**
  - This is required for the script to download build files

## Installation Steps

### 1. Download the xRDP Installer

```bash
wget https://www.c-nergy.be/downloads/xRDP/xrdp-installer-1.5.5.zip
```

### 2. Extract the Archive

First, install unzip if you don't have it:

```bash
sudo apt install unzip -y
```

Then extract the installer:

```bash
unzip xrdp-installer-1.5.5.zip
```

### 3. Make the Script Executable

```bash
chmod +x xrdp-installer-1.5.5.sh
```

### 4. Run the Installer with Sound Support

```bash
./xrdp-installer-1.5.5.sh -s
```

**Important Note:** Run this as your normal user, not as root/sudo directly—the script will ask for your password when it needs it.

The `-s` flag activates sound module compilation. The script automatically detects your Ubuntu version and compiles the appropriate PulseAudio or PipeWire modules for remote desktop audio streaming.

## Expected Outcome

After the script completes, audio devices should appear in your "Output Devices" settings, enabling sound through your Remote Desktop connections.

Now you can enjoy audio when connecting to your Ubuntu VM via xRDP!
