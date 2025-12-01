# 📊 Understanding Disk Partitioning

> A comprehensive guide to understanding disk partitioning concepts before you start.

![Partition Overview](../../images/partition-overview.png)

## 📋 Table of Contents

- [What is Partitioning?](#-what-is-partitioning)
- [Partition Tables: GPT vs MBR](#-partition-tables-gpt-vs-mbr)
- [Partition Types](#-partition-types)
- [Partition Schemes](#-partition-schemes)
- [Choosing Your Setup](#-choosing-your-setup)
- [Tools Overview](#-tools-overview)

---

## 💡 What is Partitioning?

**Partitioning** is the process of dividing a physical disk into separate logical sections. Each partition acts as an independent unit that can have its own filesystem.

### Analogy
Think of a disk as a building:
- The **disk** is the entire building
- **Partitions** are individual rooms/apartments
- Each room can be used for different purposes (bedroom, kitchen, office)

### Why Partition?

| Reason | Explanation |
|--------|-------------|
| **Organization** | Separate system files from personal data |
| **Dual Boot** | Run multiple operating systems |
| **Security** | Encrypt sensitive partitions |
| **Backup** | Easier to backup specific partitions |
| **Recovery** | Reinstall OS without losing data |

---

## 📁 Partition Tables: GPT vs MBR

A **partition table** is a data structure on the disk that defines partition locations.

### GPT (GUID Partition Table) ⭐ Recommended

| Feature | Specification |
|---------|---------------|
| **Max Partitions** | 128 partitions |
| **Max Disk Size** | 9.4 ZB (zettabytes) |
| **Boot Mode** | UEFI |
| **Redundancy** | Backup table at end of disk |
| **Compatibility** | Modern systems (2010+) |

### MBR (Master Boot Record)

| Feature | Specification |
|---------|---------------|
| **Max Partitions** | 4 primary (or 3 primary + extended) |
| **Max Disk Size** | 2 TB |
| **Boot Mode** | Legacy BIOS |
| **Redundancy** | None |
| **Compatibility** | All systems |

### Quick Comparison

```
GPT:
├── Partition 1 (EFI)
├── Partition 2 (Boot)
├── Partition 3 (Root)
├── Partition 4 (Home)
├── Partition 5 (Swap)
└── ... up to 128 partitions

MBR:
├── Primary 1
├── Primary 2
├── Primary 3
└── Extended Partition
    ├── Logical 1
    ├── Logical 2
    └── ...
```

> 🎯 **Recommendation:** Use **GPT** for all modern installations. This guide focuses on GPT.

---

## 🗂️ Partition Types

### EFI System Partition (ESP)

| Property | Value |
|----------|-------|
| **Purpose** | Stores bootloaders and boot files |
| **Required** | Yes (for UEFI) |
| **Size** | 512MB - 1GB |
| **Filesystem** | FAT32 |
| **Mount Point** | `/boot` or `/boot/efi` |

```bash
# Create EFI partition filesystem
mkfs.fat -F32 /dev/sdX1
```

---

### Boot Partition

| Property | Value |
|----------|-------|
| **Purpose** | Stores kernel and initramfs |
| **Required** | Only for encrypted setups |
| **Size** | 512MB - 1GB |
| **Filesystem** | ext4 or FAT32 |
| **Mount Point** | `/boot` |

> 💡 **Note:** For simple setups, EFI and boot can be combined. For encrypted setups, keep them separate.

---

### Root Partition (/)

| Property | Value |
|----------|-------|
| **Purpose** | Contains the operating system |
| **Required** | Yes |
| **Size** | 30GB - 100GB |
| **Filesystem** | ext4, btrfs, xfs |
| **Mount Point** | `/` |

**What's stored here:**
- System programs (`/usr`)
- Configuration (`/etc`)
- Variable data (`/var`)
- Temporary files (`/tmp`)

---

### Home Partition (/home)

| Property | Value |
|----------|-------|
| **Purpose** | Stores user personal files |
| **Required** | No (can be part of root) |
| **Size** | Remaining space |
| **Filesystem** | ext4, btrfs, xfs |
| **Mount Point** | `/home` |

**What's stored here:**
- Documents, Downloads, Pictures
- User configurations (dotfiles)
- Application data

**Benefits of separate /home:**
- Reinstall OS without losing data
- Different filesystem options
- Easier backups

---

### Swap Partition/File

| Property | Value |
|----------|-------|
| **Purpose** | Virtual memory / hibernation |
| **Required** | Recommended |
| **Size** | See table below |
| **Filesystem** | swap |
| **Mount Point** | none (swap) |

**Swap Size Guidelines:**

| RAM | No Hibernation | With Hibernation |
|-----|----------------|------------------|
| ≤ 2GB | 2x RAM | 3x RAM |
| 2-8GB | Equal to RAM | 2x RAM |
| 8-64GB | At least 4GB | 1.5x RAM |
| > 64GB | At least 4GB | Not recommended |

> 💡 **Alternative:** You can use a swap file instead of a partition, which is easier to resize.

---

## 📐 Partition Schemes

### Scheme 1: Minimal (Beginners) ⭐

```
┌─────────────────────────────────────────────┐
│                   DISK                      │
├─────────┬───────────────────────────────────┤
│   EFI   │            ROOT (/)               │
│  512MB  │          (remaining)              │
│  FAT32  │            ext4                   │
└─────────┴───────────────────────────────────┘
```

| Partition | Size | Type | Mount |
|-----------|------|------|-------|
| EFI | 512MB | FAT32 | /boot |
| Root | Remaining | ext4 | / |

**Pros:** Simple, minimal partitions
**Cons:** No separate data partition, no swap

---

### Scheme 2: Basic with Swap

```
┌───────────────────────────────────────────────────┐
│                      DISK                         │
├─────────┬─────────────────────────────┬───────────┤
│   EFI   │          ROOT (/)           │   SWAP    │
│  512MB  │        (remaining)          │   8GB     │
│  FAT32  │          ext4               │   swap    │
└─────────┴─────────────────────────────┴───────────┘
```

| Partition | Size | Type | Mount |
|-----------|------|------|-------|
| EFI | 512MB | FAT32 | /boot |
| Root | Remaining - 8GB | ext4 | / |
| Swap | 8GB | swap | [SWAP] |

**Pros:** Has swap for better performance
**Cons:** No separate home partition

---

### Scheme 3: Standard (Recommended) ⭐

```
┌─────────────────────────────────────────────────────────────┐
│                          DISK                               │
├─────────┬───────────┬───────────────────────────┬───────────┤
│   EFI   │   ROOT    │          HOME             │   SWAP    │
│  512MB  │   50GB    │       (remaining)         │   8GB     │
│  FAT32  │   ext4    │          ext4             │   swap    │
└─────────┴───────────┴───────────────────────────┴───────────┘
```

| Partition | Size | Type | Mount |
|-----------|------|------|-------|
| EFI | 512MB | FAT32 | /boot |
| Root | 50-100GB | ext4 | / |
| Home | Remaining - 8GB | ext4 | /home |
| Swap | 8GB | swap | [SWAP] |

**Pros:** Separate home for data safety
**Cons:** Fixed root size

---

### Scheme 4: Advanced (Separate Boot)

```
┌───────────────────────────────────────────────────────────────────────┐
│                              DISK                                     │
├─────────┬─────────┬───────────┬───────────────────────────┬───────────┤
│   EFI   │  BOOT   │   ROOT    │          HOME             │   SWAP    │
│  512MB  │   1GB   │   50GB    │       (remaining)         │   8GB     │
│  FAT32  │  ext4   │   ext4    │          ext4             │   swap    │
└─────────┴─────────┴───────────┴───────────────────────────┴───────────┘
```

| Partition | Size | Type | Mount |
|-----------|------|------|-------|
| EFI | 512MB | FAT32 | /boot/efi |
| Boot | 1GB | ext4 | /boot |
| Root | 50-100GB | ext4 | / |
| Home | Remaining - 8GB | ext4 | /home |
| Swap | 8GB | swap | [SWAP] |

**Pros:** Required for encryption, flexible
**Cons:** More complex

---

### Scheme 5: LVM (Flexible)

```
┌─────────────────────────────────────────────────────────────────────────┐
│                                DISK                                     │
├─────────┬───────────────────────────────────────────────────────────────┤
│   EFI   │                    LVM Physical Volume                        │
│  512MB  │  ┌──────────────────────────────────────────────────────────┐ │
│         │  │              Volume Group (volgroup0)                    │ │
│  FAT32  │  │ ┌─────────┐  ┌──────────────────┐  ┌───────────────────┐ │ │
│         │  │ │   ROOT  │  │       HOME       │  │       SWAP        │ │ │
│         │  │ │  50GB   │  │    (remaining)   │  │        8GB        │ │ │
│         │  │ │  ext4   │  │       ext4       │  │       swap        │ │ │
│         │  │ └─────────┘  └──────────────────┘  └───────────────────┘ │ │
│         │  └──────────────────────────────────────────────────────────┘ │
└─────────┴───────────────────────────────────────────────────────────────┘
```

**Pros:** Resize partitions, snapshots, span multiple disks
**Cons:** More complex setup

---

### Scheme 6: LVM + Encryption (Most Secure)

```
┌────────────────────────────────────────────────────────────────────────────┐
│                                 DISK                                       │
├─────────┬─────────┬────────────────────────────────────────────────────────┤
│   EFI   │  BOOT   │              LUKS Encrypted Container                  │
│  512MB  │   1GB   │  ┌────────────────────────────────────────────────────┐│
│         │         │  │              LVM Physical Volume                   ││
│  FAT32  │  ext4   │  │  ┌──────────────────────────────────────────────┐  ││
│         │         │  │  │           Volume Group (volgroup0)           │  ││
│         │         │  │  │ ┌───────┐  ┌────────────────┐  ┌───────────┐ │  ││
│         │         │  │  │ │ ROOT  │  │      HOME      │  │   SWAP    │ │  ││
│         │         │  │  │ │ 50GB  │  │  (remaining)   │  │    8GB    │ │  ││
│         │         │  │  │ └───────┘  └────────────────┘  └───────────┘ │  ││
│         │         │  │  └──────────────────────────────────────────────┘  ││
│         │         │  └────────────────────────────────────────────────────┘│
└─────────┴─────────┴────────────────────────────────────────────────────────┘
```

**Pros:** Full disk encryption, flexible partitions
**Cons:** Most complex, requires passphrase at boot

---

## 🎯 Choosing Your Setup

### Decision Flowchart

```
Do you need disk encryption?
├── YES → LVM + Encryption
└── NO
    │
    Do you need flexible partition resizing?
    ├── YES → LVM (without encryption)
    └── NO
        │
        Do you want separate /home?
        ├── YES → Standard partitioning
        └── NO → Basic partitioning
```

### Recommendations by Use Case

| Use Case | Recommended Scheme | Guide |
|----------|-------------------|-------|
| First time Linux | Basic with Swap | [Basic Guide](basic-partitioning.md) |
| Daily desktop use | Standard | [Advanced Guide](advanced-partitioning.md) |
| Laptop with sensitive data | LVM + Encryption | [Encryption Guide](lvm-encryption.md) |
| Server / Multi-disk | LVM | [LVM Guide](lvm-setup.md) |
| Dual boot with Windows | Basic or Standard | [Basic Guide](basic-partitioning.md) |

---

## 🔧 Tools Overview

### cfdisk (Recommended for Beginners) ⭐

Text-based, menu-driven partition editor.

```bash
cfdisk /dev/sdX
```

**Features:**
- Visual interface
- Easy to use
- Supports GPT and MBR

![cfdisk screenshot](../../images/cfdisk.png)

---

### fdisk

Traditional command-line partition editor.

```bash
fdisk /dev/sdX
```

**Common commands:**
| Key | Action |
|-----|--------|
| `m` | Help menu |
| `g` | Create new GPT table |
| `n` | New partition |
| `d` | Delete partition |
| `t` | Change partition type |
| `p` | Print partition table |
| `w` | Write changes and exit |
| `q` | Quit without saving |

---

### gdisk

GPT-specific partition editor (recommended for GPT).

```bash
gdisk /dev/sdX
```

**Advantages:**
- GPT-focused
- Better GPT handling than fdisk
- Supports GPT backup/recovery

---

### parted

GNU partition editor with scripting support.

```bash
parted /dev/sdX
```

**Features:**
- Resize partitions
- Scripting support
- Supports GPT and MBR

---

## 📖 Filesystem Types

| Filesystem | Best For | Features |
|------------|----------|----------|
| **ext4** | General use ⭐ | Stable, fast, journaling |
| **btrfs** | Advanced users | Snapshots, compression, subvolumes |
| **xfs** | Large files | High performance, scalable |
| **FAT32** | EFI partition | Required for UEFI boot |
| **swap** | Swap partition | Virtual memory |

---

## ➡️ Next Steps

Choose your partitioning guide:

| Guide | Description |
|-------|-------------|
| [Basic Partitioning](basic-partitioning.md) | Simple 2-3 partition setup |
| [Advanced Partitioning](advanced-partitioning.md) | Separate /home partition |
| [LVM Setup](lvm-setup.md) | Flexible Logical Volume Manager |
| [LVM + Encryption](lvm-encryption.md) | Full disk encryption |

---

<div align="center">

[← Live Environment](../01-pre-installation/live-environment.md) | [Back to Main Guide](../../README.md) | [Next: Basic Partitioning →](basic-partitioning.md)

</div>
