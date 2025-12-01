# 🐧 Arch Linux Installation Guide

[![Arch Linux](https://img.shields.io/badge/Arch%20Linux-1793D1?style=for-the-badge&logo=arch-linux&logoColor=white)](https://archlinux.org/)
[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg?style=for-the-badge)](https://opensource.org/licenses/MIT)

> A comprehensive, beginner-friendly guide to installing Arch Linux with detailed explanations of every command.

![Arch Linux Banner](images/arch-banner.png)

## 📋 Table of Contents

1. [Introduction](#-introduction)
2. [Prerequisites](#-prerequisites)
3. [Guide Structure](#-guide-structure)
4. [Quick Navigation](#-quick-navigation)
5. [Installation Paths](#-installation-paths)
6. [Contributing](#-contributing)

---

## 🎯 Introduction

This guide documents my personal Arch Linux installation process, designed to help both beginners and experienced users. Unlike other guides that just list commands, **this guide explains what each command does** so you can learn while installing.

### What Makes This Guide Different?

- ✅ **Detailed explanations** for every command
- ✅ **Multiple partitioning methods** (Basic, Advanced, LVM, Encrypted)
- ✅ **Screenshots** for visual learners
- ✅ **Desktop environment options** with comparisons
- ✅ **Post-installation optimizations**
- ✅ **Troubleshooting tips**

---

## 📚 Prerequisites

Before starting, ensure you have:

- [ ] A computer with UEFI support (most modern PCs)
- [ ] A USB drive (8GB or larger)
- [ ] Internet connection (Ethernet recommended, WiFi supported)
- [ ] Backup of important data
- [ ] Basic command line knowledge (helpful but not required)

---

## 📂 Guide Structure

```
arch_linux-install-guide/
├── README.md                          # You are here
├── docs/
│   ├── 01-pre-installation/
│   │   ├── bios-settings.md           # BIOS/UEFI configuration
│   │   ├── create-bootable-usb.md     # Creating installation media
│   │   └── live-environment.md        # Live boot setup
│   │
│   ├── 02-partitioning/
│   │   ├── partition-overview.md      # Understanding partitions
│   │   ├── basic-partitioning.md      # Simple dual-partition setup
│   │   ├── advanced-partitioning.md   # Separate /home, /boot, swap
│   │   ├── lvm-setup.md               # LVM without encryption
│   │   └── lvm-encryption.md          # Full disk encryption with LVM
│   │
│   ├── 03-base-installation/
│   │   ├── base-install.md            # Core system installation
│   │   ├── system-configuration.md    # Hostname, locale, users
│   │   └── bootloader.md              # GRUB installation
│   │
│   ├── 04-post-installation/
│   │   ├── first-boot.md              # Initial setup after reboot
│   │   ├── drivers.md                 # GPU and hardware drivers
│   │   ├── audio-bluetooth.md         # Audio and Bluetooth setup
│   │   └── network-setup.md           # Network configuration
│   │
│   ├── 05-desktop-environments/
│   │   ├── de-overview.md             # Comparison of DEs
│   │   ├── hyprland.md                # Hyprland (Wayland compositor)
│   │   ├── kde-plasma.md              # KDE Plasma
│   │   ├── gnome.md                   # GNOME
│   │   ├── xfce.md                    # XFCE
│   │   └── display-managers.md        # GDM, SDDM, LightDM
│   │
│   ├── 06-essential-software/
│   │   ├── essential-packages.md      # Must-have packages
│   │   ├── aur-helpers.md             # yay, paru installation
│   │   └── recommended-apps.md        # Useful applications
│   │
│   └── 07-optimization/
│       ├── performance-tweaks.md      # System optimization
│       ├── security.md                # Security hardening
│       └── maintenance.md             # System maintenance
│
├── package-lists/
│   ├── base-packages.txt              # Minimal installation packages
│   ├── desktop-packages.txt           # Desktop environment packages
│   └── my-packages.txt                # My personal package list
│
├── scripts/
│   └── post-install.sh                # Automated post-install script
│
└── images/
    └── (screenshots and diagrams)
```

---

## 🚀 Quick Navigation

### 🔰 For Beginners (Recommended Path)
1. [BIOS Settings](docs/01-pre-installation/bios-settings.md)
2. [Create Bootable USB](docs/01-pre-installation/create-bootable-usb.md)
3. [Live Environment Setup](docs/01-pre-installation/live-environment.md)
4. [Basic Partitioning](docs/02-partitioning/basic-partitioning.md)
5. [Base Installation](docs/03-base-installation/base-install.md)
6. [First Boot](docs/04-post-installation/first-boot.md)
7. [Choose a Desktop Environment](docs/05-desktop-environments/de-overview.md)

### 🔒 For Security-Focused Users
1. [Pre-installation steps](#-for-beginners-recommended-path) (Steps 1-3)
2. [LVM with Encryption](docs/02-partitioning/lvm-encryption.md)
3. [Base Installation](docs/03-base-installation/base-install.md)
4. [Security Hardening](docs/07-optimization/security.md)

### ⚡ For Experienced Users
Jump directly to [Package Lists](package-lists/) and [Scripts](scripts/)

---

## 🛤️ Installation Paths

Choose your installation path based on your needs:

| Path | Difficulty | Use Case |
|------|------------|----------|
| [Basic](docs/02-partitioning/basic-partitioning.md) | ⭐ Easy | Simple setup, dual boot |
| [Advanced](docs/02-partitioning/advanced-partitioning.md) | ⭐⭐ Medium | Separate /home partition |
| [LVM](docs/02-partitioning/lvm-setup.md) | ⭐⭐⭐ Advanced | Flexible partition management |
| [LVM + Encryption](docs/02-partitioning/lvm-encryption.md) | ⭐⭐⭐⭐ Expert | Full disk encryption |

---

## 🤝 Contributing

Found an error or want to improve this guide? Contributions are welcome!

1. Fork this repository
2. Create a new branch (`git checkout -b fix/typo`)
3. Commit your changes (`git commit -am 'Fix typo in partitioning guide'`)
4. Push to the branch (`git push origin fix/typo`)
5. Open a Pull Request

---

## 📖 Additional Resources

- [Arch Wiki](https://wiki.archlinux.org/) - The ultimate Arch Linux resource
- [Arch Linux Forums](https://bbs.archlinux.org/) - Community support
- [r/archlinux](https://www.reddit.com/r/archlinux/) - Reddit community

---

## 📜 License

This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details.

---

<div align="center">

**⭐ Star this repo if you found it helpful!**

Made with ❤️ by Martin

</div>
