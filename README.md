# 🐧 Arch Linux Installation Guide

[![Arch Linux](https://img.shields.io/badge/Arch%20Linux-1793D1?style=for-the-badge&logo=arch-linux&logoColor=white)](https://archlinux.org/)

> A comprehensive, beginner-friendly guide to installing Arch Linux with detailed explanations of every command.

![Arch Linux Banner](images/arch-banner.png)

## 📋 Table of Contents

1. [Introduction](#-introduction)
2. [Prerequisites](#-prerequisites)
3. [Quick Navigation](#-quick-navigation)
4. [Installation Paths](#-installation-paths)
5. [Contributing](#-contributing)

---

## 🎯 Introduction

This guide documents my personal Arch Linux installation process, designed to help both beginners and experienced users. Unlike other guides that just list commands, **this guide explains what each command does** so you can learn while installing.

---

## 📚 Prerequisites

Before starting, ensure you have:

- [ ] A computer with UEFI support (most modern PCs)
- [ ] A USB drive (8GB or larger)
- [ ] Internet connection (Ethernet recommended, WiFi supported)
- [ ] Backup of important data
- [ ] Basic command line knowledge

---

## 🚀 Quick Navigation

### 🔰 For Beginners (Standard Path)
1. [BIOS Settings](docs/01-pre-installation/bios-settings.md)
2. [Create Bootable USB](docs/01-pre-installation/create-bootable-usb.md)
3. [Live Environment Setup](docs/01-pre-installation/live-environment.md)
4. [Basic Partitioning](docs/02-partitioning/basic-partitioning.md)
5. [Standard Base Installation](docs/03-base-installation/base-install-standard.md)
6. [Standard Bootloader](docs/03-base-installation/bootloader-standard.md)
7. [First Boot](docs/04-post-installation/first-boot.md)
8. [Choose a Desktop Environment](docs/05-desktop-environments/de-overview.md)

### 📦 For LVM Users (Flexible Partitioning)
1. [Pre-installation steps](#-for-beginners-standard-path) (Steps 1-3)
2. [LVM Setup](docs/02-partitioning/lvm-setup.md)
3. [LVM Base Installation](docs/03-base-installation/base-install-lvm.md)
4. [LVM Bootloader](docs/03-base-installation/bootloader-lvm.md)
5. [First Boot](docs/04-post-installation/first-boot.md)

### 🔒 For Security-Focused Users (Encrypted)
1. [Pre-installation steps](#-for-beginners-standard-path) (Steps 1-3)
2. [LVM with Encryption](docs/02-partitioning/lvm-encryption.md)
3. [Encrypted Base Installation](docs/03-base-installation/base-install-encrypted.md)
4. [Encrypted Bootloader](docs/03-base-installation/bootloader-encrypted.md)
5. [First Boot](docs/04-post-installation/first-boot.md)
6. [Security Hardening](docs/07-optimization/security.md)

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

## 📖 Additional Resources

- [Arch Wiki](https://wiki.archlinux.org/) - The ultimate Arch Linux resource
- [Arch Linux Forums](https://bbs.archlinux.org/) - Community support
- [r/archlinux](https://www.reddit.com/r/archlinux/) - Reddit community

---

## 🤝 Contributing

Found an error or want to improve this guide? Contributions are welcome!

1. Fork this repository
2. Create a new branch (`git checkout -b fix/typo`)
3. Commit your changes (`git commit -am 'Fix typo in partitioning guide'`)
4. Push to the branch (`git push origin fix/typo`)
5. Open a Pull Request

---

<div align="center">

**⭐ Star this repo if you found it helpful!**

Made with ❤️ by Martin

</div>
