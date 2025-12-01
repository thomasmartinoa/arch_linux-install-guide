# 📦 Base System Installation

> Installing the core Arch Linux system using pacstrap.

![Base Installation](../../images/base-install.png)

## 📋 Table of Contents

- [Prerequisites](#-prerequisites)
- [Installing Base System](#-installing-base-system)
- [Generate fstab](#-generate-fstab)
- [Chroot into System](#-chroot-into-system)
- [System Configuration](#-system-configuration)
- [Create User](#-create-user)
- [Essential Packages](#-essential-packages)

---

## ✅ Prerequisites

Before proceeding, ensure:

- [ ] Partitions are created (see [Partitioning Guides](../02-partitioning/))
- [ ] Partitions are formatted
- [ ] Partitions are mounted at `/mnt`
- [ ] Internet connection is working

**Quick verification:**
```bash
# Check mounts
lsblk

# Check internet
ping -c 3 archlinux.org
```

---

## 📥 Installing Base System

### Using pacstrap

```bash
pacstrap -i /mnt base
```

**Command breakdown:**

| Part | Meaning |
|------|---------|
| `pacstrap` | Arch installation script |
| `-i` | Interactive mode (confirm packages) |
| `/mnt` | Target mount point |
| `base` | Base system meta-package |

**What `base` includes:**
- Core system utilities
- Essential libraries
- Package manager (pacman)
- Bash shell

> 💡 Press Enter to confirm or select packages when prompted.

### Installation Time

This takes 5-15 minutes depending on your internet speed.

**What happens:**
1. Downloads packages from mirrors
2. Extracts packages to /mnt
3. Sets up basic system structure

---

## 📝 Generate fstab

The **fstab** (File System Table) tells Linux how to mount partitions at boot.

### Generate fstab

```bash
genfstab -U -p /mnt >> /mnt/etc/fstab
```

**Command breakdown:**

| Part | Meaning |
|------|---------|
| `genfstab` | Generate fstab entries |
| `-U` | Use UUIDs (recommended, persistent) |
| `-p` | Avoid printing pseudofs |
| `/mnt` | Source mount point |
| `>>` | Append to file |
| `/mnt/etc/fstab` | Target fstab file |

### Verify fstab

```bash
cat /mnt/etc/fstab
```

**Example output:**
```
# /dev/sda2
UUID=xxxx-xxxx-xxxx-xxxx    /           ext4    rw,relatime    0 1

# /dev/sda1
UUID=XXXX-XXXX              /boot       vfat    rw,relatime    0 2

# /dev/sda3
UUID=xxxx-xxxx-xxxx-xxxx    /home       ext4    rw,relatime    0 2

# /dev/sda4 (swap)
UUID=xxxx-xxxx-xxxx-xxxx    none        swap    defaults       0 0
```

**Understanding fstab columns:**

| Column | Description |
|--------|-------------|
| 1 | Device (UUID recommended) |
| 2 | Mount point |
| 3 | Filesystem type |
| 4 | Mount options |
| 5 | Dump (backup utility, usually 0) |
| 6 | Pass (fsck order: 1=root, 2=others, 0=skip) |

---

## 🚪 Chroot into System

**arch-chroot** changes the apparent root directory to your new installation.

```bash
arch-chroot /mnt
```

**What this does:**
- Changes root to `/mnt`
- Now `/` is your new system
- Commands affect the installed system

You'll notice the prompt changes:
```
[root@archiso /]#
```

> 💡 You're now "inside" your new Arch installation!

---

## ⚙️ System Configuration

### Hostname

Set your computer's name:

```bash
echo "archpc" > /etc/hostname
```

**Command breakdown:**

| Part | Meaning |
|------|---------|
| `echo "archpc"` | Output the text "archpc" |
| `>` | Redirect output to file |
| `/etc/hostname` | Hostname configuration file |

Replace `archpc` with your preferred name (no spaces, lowercase).

---

### Hosts File

Configure local hostname resolution:

```bash
nvim /etc/hosts
```

Add these lines:

```
127.0.0.1   localhost
::1         localhost
127.0.1.1   archpc.localdomain archpc
```

**What these mean:**

| Line | Purpose |
|------|---------|
| `127.0.0.1 localhost` | IPv4 loopback |
| `::1 localhost` | IPv6 loopback |
| `127.0.1.1 archpc...` | Your hostname resolution |

Replace `archpc` with your hostname.

---

### Root Password

Set the root (administrator) password:

```bash
passwd
```

Enter and confirm your password.

> ⚠️ **Important:** Root has full system access. Use a strong password!

---

### Timezone

Set your timezone:

```bash
ln -sf /usr/share/zoneinfo/Asia/Kolkata /etc/localtime
```

**Command breakdown:**

| Part | Meaning |
|------|---------|
| `ln -sf` | Create symbolic link (force) |
| `/usr/share/zoneinfo/Asia/Kolkata` | Your timezone file |
| `/etc/localtime` | System timezone link |

**Find your timezone:**
```bash
# List regions
ls /usr/share/zoneinfo/

# List cities in a region
ls /usr/share/zoneinfo/America/
ls /usr/share/zoneinfo/Europe/
```

### Sync Hardware Clock

```bash
hwclock --systohc
```

**What this does:**
- Syncs hardware clock with system time
- Writes current time to RTC (Real Time Clock)

---

### Locale Configuration

Configure system language and regional settings.

#### Edit locale.gen

```bash
nvim /etc/locale.gen
```

Uncomment your locale (remove the `#`):

```
#en_US.UTF-8 UTF-8
```

Change to:

```
en_US.UTF-8 UTF-8
```

> 💡 You can enable multiple locales if needed.

#### Generate Locales

```bash
locale-gen
```

**Output:**
```
Generating locales...
  en_US.UTF-8... done
Generation complete.
```

#### Set System Locale

```bash
echo "LANG=en_US.UTF-8" > /etc/locale.conf
```

---

### Console Configuration (Optional)

Configure console keymap and font:

```bash
nvim /etc/vconsole.conf
```

Add:

```
KEYMAP=us
FONT=ter-132n
FONT_MAP=
```

**Options:**

| Setting | Description |
|---------|-------------|
| `KEYMAP` | Keyboard layout (us, uk, de, etc.) |
| `FONT` | Console font |
| `FONT_MAP` | Font mapping (usually empty) |

---

## 👤 Create User

### Create Your User Account

```bash
useradd -m -g users -G wheel martin
```

**Command breakdown:**

| Part | Meaning |
|------|---------|
| `useradd` | Create new user |
| `-m` | Create home directory |
| `-g users` | Primary group: users |
| `-G wheel` | Additional groups: wheel (sudo access) |
| `martin` | Username (replace with yours) |

### Set User Password

```bash
passwd martin
```

Enter and confirm the password.

---

## 📦 Essential Packages

### Install Base Packages

```bash
pacman -S base-devel dosfstools grub efibootmgr lvm2 mtools vim nvim networkmanager openssh os-prober sudo
```

**Package descriptions:**

| Package | Purpose |
|---------|---------|
| `base-devel` | Development tools (needed for AUR) |
| `dosfstools` | FAT filesystem utilities |
| `grub` | Bootloader |
| `efibootmgr` | EFI boot manager |
| `lvm2` | Logical Volume Manager (if using LVM) |
| `mtools` | DOS filesystem tools |
| `vim` | Vi Improved text editor |
| `nvim` | Neovim (modern vim) |
| `networkmanager` | Network management |
| `openssh` | SSH server/client |
| `os-prober` | Detect other operating systems |
| `sudo` | Run commands as root |

---

### Install Linux Kernel

```bash
# Standard kernel
pacman -S linux linux-headers

# LTS kernel (more stable)
pacman -S linux-lts linux-lts-headers

# Firmware for hardware
pacman -S linux-firmware
```

**Kernel options:**

| Kernel | Description |
|--------|-------------|
| `linux` | Latest stable kernel |
| `linux-lts` | Long-term support kernel |
| `linux-zen` | Gaming/performance optimized |
| `linux-hardened` | Security focused |

> 💡 Install both `linux` and `linux-lts` for a fallback option!

---

### Enable Essential Services

```bash
# Enable SSH for remote access
systemctl enable sshd

# Enable NetworkManager for networking
systemctl enable NetworkManager
```

**Command breakdown:**

| Part | Meaning |
|------|---------|
| `systemctl enable` | Enable service to start at boot |
| `sshd` | SSH daemon |
| `NetworkManager` | Network manager service |

---

## 🔐 For Encrypted Setups Only

If you're using LVM with encryption, configure mkinitcpio:

### Edit mkinitcpio.conf

```bash
nvim /etc/mkinitcpio.conf
```

Modify the HOOKS line:

```
# Original
HOOKS=(base udev autodetect modconf block filesystems keyboard fsck)

# For encrypted LVM
HOOKS=(base udev autodetect modconf block encrypt lvm2 filesystems keyboard fsck)
```

### Regenerate initramfs

```bash
mkinitcpio -p linux
mkinitcpio -p linux-lts    # If LTS kernel installed
```

**What this does:**
- Creates initial RAM filesystem
- Includes modules needed for boot
- Must include encrypt/lvm2 for encrypted systems

---

## 📋 Quick Reference

```bash
# Generate fstab
genfstab -U -p /mnt >> /mnt/etc/fstab

# Enter chroot
arch-chroot /mnt

# Basic configuration
echo "archpc" > /etc/hostname
passwd

# Timezone
ln -sf /usr/share/zoneinfo/Asia/Kolkata /etc/localtime
hwclock --systohc

# Locale
nvim /etc/locale.gen  # Uncomment en_US.UTF-8
locale-gen
echo "LANG=en_US.UTF-8" > /etc/locale.conf

# Create user
useradd -m -g users -G wheel username
passwd username

# Install packages
pacman -S base-devel dosfstools grub efibootmgr lvm2 mtools vim nvim networkmanager openssh os-prober sudo
pacman -S linux linux-headers linux-lts linux-lts-headers linux-firmware

# Enable services
systemctl enable sshd NetworkManager

# For encryption only:
nvim /etc/mkinitcpio.conf  # Add encrypt lvm2 to HOOKS
mkinitcpio -p linux
mkinitcpio -p linux-lts
```

---

## ➡️ Next Steps

Continue with bootloader installation:

→ [Bootloader Setup](bootloader.md)

---

<div align="center">

[← Partitioning](../02-partitioning/) | [Back to Main Guide](../../README.md) | [Next: Bootloader →](bootloader.md)

</div>
