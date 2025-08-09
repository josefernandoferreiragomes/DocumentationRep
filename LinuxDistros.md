# Move to Linux

🐧 Most Popular Linux Distributions (2025 Edition)

For an intermediate legacy machine
An Intel Core i5-4590, 8GB RAM is well-suited for most modern Linux distros. Below is a comparison of the top contenders:

📊 Comparison Table

| Distribution     | Desktop Environment | Performance on Your PC | User Experience       | Ideal For                     |
|------------------|---------------------|-------------------------|------------------------|-------------------------------|
| Linux Mint   | Cinnamon / XFCE     | Excellent               | Windows-like, intuitive| Beginners, daily use          |
| Ubuntu       | GNOME               | Good                    | Modern, polished       | General users, developers     |
| Zorin OS     | Zorin Desktop       | Excellent               | Customizable, sleek    | Windows switchers             |
| Kubuntu      | KDE Plasma          | Excellent               | Elegant, customizable  | Power users, multitaskers     |
| Fedora       | GNOME               | Good                    | Cutting-edge, minimal  | Developers, tech enthusiasts  |
| Pop!_OS      | COSMIC (GNOME-based)| Good                    | Tiling, modern         | Creators, gamers              |
| Linux Lite   | XFCE                | Excellent               | Lightweight, simple    | Older hardware, minimalists   |

---

🔧 How to Create a Bootable USB Drive

🧰 What You’ll Need
- A USB stick (8GB+ recommended)
- ISO file of the Linux distro
- A USB flashing tool:
  - Windows: Rufus
  - Cross-platform: balenaEtcher
  - Advanced: Ventoy

---

🚀 Steps to Create a Bootable USB

1. Download the ISO
Choose your distro:
- Linux Mint

https://linuxmint.com/download.php

- Ubuntu

https://ubuntu.com/download

- Zorin OS

https://zorin.com/os/download/

- Kubuntu

https://kubuntu.org/getkubuntu/

- Fedora

https://fedoraproject.org//

- Pop!_OS

https://system76.com/pop/

- Linux Lite

https://www.linuxliteos.com/download.php

2. Flash the ISO to USB

Using Rufus (Windows)
`text
1. Open Rufus
2. Select your USB drive
3. Choose the ISO file
4. Set Partition Scheme to MBR (for BIOS) or GPT (for UEFI)
5. Click Start
`

Using balenaEtcher (All OSes)
`text
1. Open Etcher
2. Select ISO file
3. Select USB drive
4. Click Flash
`

Using Ventoy (Multi-ISO USB)
`text
1. Install Ventoy to USB
2. Copy ISO files directly to USB
3. Boot and choose ISO from menu
`

---

🧪 Booting from USB

1. Insert USB into your PC
2. Restart and enter Boot Menu (F9, ESC, or DEL)
3. Select USB drive
4. Choose “Try” or “Install” option

---

🧠 Tips for Choosing a Distro

- Want Windows familiarity? → Linux Mint or Zorin OS
- Want customization? → Kubuntu or Fedora
- Want speed on older hardware? → Linux Lite or Mint XFCE
- Want cutting-edge tools? → Pop!_OS or Fedora

# Installing software 

🧠 Linux Compatibility Guide for Popular Tools (2025)

This guide compares Linux distributions that support Chrome, Roblox, Steam, Riot Games, Visual Studio Code, and Docker. It also includes installation walkthroughs for each tool.

---

✅ Compatible Linux Distributions

| Distro        | Chrome | Roblox | Steam | Riot Games | VS Code | Docker |
|---------------|--------|--------|-------|------------|---------|--------|
| Ubuntu    | ✅     | ⚠️ via Wine/Vinegar | ✅     | ⚠️ via Wine | ✅     | ✅     |
| Linux Mint| ✅     | ⚠️ via Wine/Vinegar | ✅     | ⚠️ via Wine | ✅     | ✅     |
| Pop!_OS   | ✅     | ⚠️ via Wine/Vinegar | ✅     | ⚠️ via Wine | ✅     | ✅     |
| Kubuntu   | ✅     | ⚠️ via Wine/Vinegar | ✅     | ⚠️ via Wine | ✅     | ✅     |
| Manjaro   | ✅     | ⚠️ via Wine/Vinegar | ✅     | ⚠️ via Wine | ✅     | ✅     |

> ⚠️ Roblox and Riot Games require workarounds like Wine, Vinegar, or Sober. Native support is not available.

---

🔧 Tool Installation Walkthroughs

🌐 Google Chrome
`bash
wget https://dl.google.com/linux/direct/google-chrome-stablecurrentamd64.deb
sudo apt install ./google-chrome-stablecurrentamd64.deb
`
- For Manjaro:
`bash
pamac install google-chrome
`

---

🎮 Steam

Ubuntu/Mint/Pop!_OS/Kubuntu:
`bash
sudo apt update
sudo apt install steam
`

Manjaro:
`bash
sudo pacman -S steam
`

---

🧩 Roblox (via Vinegar & Sober)

Step-by-Step:
`bash
sudo apt install flatpak
flatpak remote-add --if-not-exists flathub https://flathub.org/repo/flathub.flatpakrepo
flatpak install flathub org.vinegarhq.Vinegar
flatpak install flathub org.vinegarhq.Sober
`

---

⚔️ Riot Games (League of Legends)
`bash
sudo apt install lutris wine
`
- Use Lutris to install League via community scripts.

---

💻 Visual Studio Code

Ubuntu/Mint/Pop!_OS/Kubuntu:
`bash
sudo apt update
sudo apt install software-properties-common apt-transport-https wget
wget -q https://packages.microsoft.com/keys/microsoft.asc -O- | sudo apt-key add -
sudo add-apt-repository "deb [arch=amd64] https://packages.microsoft.com/repos/vscode stable main"
sudo apt update
sudo apt install code
`

Manjaro:
`bash
pamac install code
`

---

🐳 Docker

Ubuntu/Mint/Pop!_OS/Kubuntu:
`bash
sudo apt update
sudo apt install docker.io docker-compose
sudo systemctl enable docker
sudo systemctl start docker
sudo usermod -aG docker $USER
`

Manjaro:
`bash
sudo pacman -S docker
sudo systemctl enable docker
sudo systemctl start docker
`

---

🧠 Recommended Distro by Use Case

| Use Case        | Best Distro     |
|-----------------|-----------------|
| Gaming + Steam  | Pop!_OS, Manjaro |
| Roblox & Wine   | Ubuntu, Linux Mint |
| Docker & Dev    | Ubuntu, Kubuntu |
| General Use     | Linux Mint, Pop!_OS |

---

🔧 Creating a Bootable USB

🧰 What You’ll Need
- USB stick (8GB+)
- ISO file of your chosen distro
- Flashing tool: Rufus, balenaEtcher, or Ventoy

🚀 Steps

Using Rufus (Windows)
`text
1. Open Rufus
2. Select USB drive
3. Choose ISO file
4. Set Partition Scheme: MBR (BIOS) or GPT (UEFI)
5. Click Start
`

Using balenaEtcher (All OSes)
`text
1. Open Etcher
2. Select ISO file
3. Select USB drive
4. Click Flash
`

Using Ventoy (Multi-ISO)
`text
1. Install Ventoy to USB
2. Copy ISO files directly to USB
3. Boot and choose ISO from menu
`

---

📚 Resources

- Roblox on Linux – DevForum
- League of Linux
- Docker on Pop!_OS guide
