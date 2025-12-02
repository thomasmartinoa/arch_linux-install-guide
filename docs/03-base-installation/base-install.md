# 📦 Base System Installation

> Installing the core Arch Linux system using pacstrap.

![Base Installation](../../images/base-install.png)

## 📋 Table of Contents

- [Prerequisites](#-prerequisites)
- [Choose Your Installation Path](#-choose-your-installation-path)
- [Path A: Standard Installation](#-path-a-standard-installation)
- [Path B: LVM Installation](#-path-b-lvm-installation)
- [Path C: Encrypted LVM Installation](#️-path-c-encrypted-lvm-installation)

---

## ✅ Prerequisites

Before proceeding, ensure:

- [ ] Partitions are created and formatted
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

## 🛤️ Choose Your Installation Path

> ⚠️ **Important:** Follow only ONE path based on your partition setup!

| Your Partitioning Method | Follow This Path |
|--------------------------|------------------|
| [Basic Partitioning](../02-partitioning/basic-partitioning.md) | [🅰️ Path A: Standard](#-path-a-standard-installation) |
| [Advanced Partitioning](../02-partitioning/advanced-partitioning.md) | [🅰️ Path A: Standard](#-path-a-standard-installation) |
| [LVM Setup](../02-partitioning/lvm-setup.md) | [🅱️ Path B: LVM](#-path-b-lvm-installation) |
| [LVM + Encryption](../02-partitioning/lvm-encryption.md) | [🅲️ Path C: Encrypted](#️-path-c-encrypted-lvm-installation) |

---

---

---

# 🅰️ Path A: Standard Installation

> For users who followed **Basic** or **Advanced** partitioning (no LVM, no encryption).

---

## A1. Verify Your Mounts

```bash
lsblk
```

**Expected output (Basic partitioning):**
```
NAME   MAJ:MIN RM   SIZE RO TYPE MOUNTPOINT
sda      8:0    0   500G  0 disk
├─sda1   8:1    0   512M  0 part /mnt/boot
├─sda2   8:2    0 491.5G  0 part /mnt
└─sda3   8:3    0     8G  0 part [SWAP]
```

**Expected output (Advanced with separate /home):**
```
NAME   MAJ:MIN RM   SIZE RO TYPE MOUNTPOINT
sda      8:0    0   500G  0 disk
├─sda1   8:1    0   512M  0 part /mnt/boot
├─sda2   8:2    0    50G  0 part /mnt
├─sda3   8:3    0 441.5G  0 part /mnt/home
└─sda4   8:4    0     8G  0 part [SWAP]
```

If your mounts don't look right, go back to [Partitioning](../02-partitioning/).

---

## A2. Install Base System

```bash
pacstrap -i /mnt base
```

**What this does:**
- Downloads base packages from mirrors
- Installs them to `/mnt`
- Sets up basic system structure

Wait for installation to complete (5-15 minutes).

---

## A3. Generate fstab

```bash
genfstab -U -p /mnt >> /mnt/etc/fstab
```

**Verify it looks correct:**
```bash
cat /mnt/etc/fstab
```

You should see entries for each partition with UUIDs.

---

## A4. Enter the New System (Chroot)

```bash
arch-chroot /mnt
```

Your prompt changes to `[root@archiso /]#` - you're now "inside" your new system!

---

## A5. Set Hostname

```bash
echo "archpc" > /etc/hostname
```

Replace `archpc` with your preferred computer name.

---

## A6. Configure Hosts File

```bash
nvim /etc/hosts
```

Add these lines:
```
127.0.0.1   localhost
::1         localhost
127.0.1.1   archpc.localdomain archpc
```

---

## A7. Set Root Password

```bash
passwd
```

Enter a strong password for the root account.

---

## A8. Set Timezone

```bash
ln -sf /usr/share/zoneinfo/Asia/Kolkata /etc/localtime
hwclock --systohc
```

Replace `Asia/Kolkata` with your timezone.

---

## A9. Configure Locale

```bash
nvim /etc/locale.gen
```

Uncomment `en_US.UTF-8 UTF-8` (remove the `#`), then:

```bash
locale-gen
echo "LANG=en_US.UTF-8" > /etc/locale.conf
```

---

## A10. Create Your User

```bash
useradd -m -g users -G wheel martin
passwd martin
```

Replace `martin` with your username.

---

## A11. Install Essential Packages

```bash
pacman -S base-devel dosfstools grub efibootmgr mtools \
vim neovim networkmanager openssh os-prober sudo
```

---

## A12. Install Linux Kernel

```bash
pacman -S linux linux-headers linux-lts linux-lts-headers linux-firmware
```

---

## A13. Install GPU Drivers

**Intel GPU:**
```bash
pacman -S mesa intel-media-driver
```

**AMD GPU:**
```bash
pacman -S mesa libva-mesa-driver
```

**NVIDIA GPU:**
```bash
pacman -S nvidia nvidia-lts nvidia-utils
```

---

## A14. Enable Services

```bash
systemctl enable NetworkManager
systemctl enable sshd
```

---

## A15. Next Step: Bootloader

→ **Continue to [Bootloader Installation (Standard)](bootloader.md#-standard-grub-installation)**

---

---

---

# 🅱️ Path B: LVM Installation

> For users who followed **LVM Setup** (without encryption).

---

## B1. Verify Your Mounts

```bash
lsblk
```

**Expected output:**
```
NAME                   SIZE TYPE  MOUNTPOINT
sda                    500G disk
├─sda1                 512M part  /mnt/boot
└─sda2               499.5G part
  ├─volgroup0-lv_root   50G lvm   /mnt
  ├─volgroup0-lv_swap    8G lvm   [SWAP]
  └─volgroup0-lv_home  441G lvm   /mnt/home
```

You should see `lvm` type for your logical volumes.

---

## B2. Install Base System

```bash
pacstrap -i /mnt base
```

Wait for installation to complete (5-15 minutes).

---

## B3. Generate fstab

```bash
genfstab -U -p /mnt >> /mnt/etc/fstab
```

**Verify:**
```bash
cat /mnt/etc/fstab
```

---

## B4. Enter the New System (Chroot)

```bash
arch-chroot /mnt
```

---

## B5. Set Hostname

```bash
echo "archpc" > /etc/hostname
```

---

## B6. Configure Hosts File

```bash
nvim /etc/hosts
```

Add:
```
127.0.0.1   localhost
::1         localhost
127.0.1.1   archpc.localdomain archpc
```

---

## B7. Set Root Password

```bash
passwd
```

---

## B8. Set Timezone

```bash
ln -sf /usr/share/zoneinfo/Asia/Kolkata /etc/localtime
hwclock --systohc
```

---

## B9. Configure Locale

```bash
nvim /etc/locale.gen
```

Uncomment `en_US.UTF-8 UTF-8`, then:

```bash
locale-gen
echo "LANG=en_US.UTF-8" > /etc/locale.conf
```

---

## B10. Create Your User

```bash
useradd -m -g users -G wheel martin
passwd martin
```

---

## B11. Install Essential Packages (Including LVM!)

```bash
pacman -S base-devel dosfstools grub efibootmgr lvm2 mtools \
vim neovim networkmanager openssh os-prober sudo
```

> ⚠️ **Critical:** The `lvm2` package is **required** for LVM systems!

---

## B12. Install Linux Kernel

```bash
pacman -S linux linux-headers linux-lts linux-lts-headers linux-firmware
```

---

## B13. Install GPU Drivers

**Intel GPU:**
```bash
pacman -S mesa intel-media-driver
```

**AMD GPU:**
```bash
pacman -S mesa libva-mesa-driver
```

**NVIDIA GPU:**
```bash
pacman -S nvidia nvidia-lts nvidia-utils
```

---

## B14. ⚠️ Configure mkinitcpio for LVM

This step is **required** for LVM systems!

```bash
nvim /etc/mkinitcpio.conf
```

Find the `HOOKS` line. It looks like this:

```
HOOKS=(base udev autodetect modconf block filesystems keyboard fsck)
```

**Add `lvm2` between `block` and `filesystems`:**

```
HOOKS=(base udev autodetect modconf block lvm2 filesystems keyboard fsck)
```

**Save and regenerate initramfs:**

```bash
mkinitcpio -p linux
mkinitcpio -p linux-lts
```

---

## B15. Enable Services

```bash
systemctl enable NetworkManager
systemctl enable sshd
```

---

## B16. Next Step: Bootloader

→ **Continue to [Bootloader Installation (LVM)](bootloader.md#-lvm-grub-installation)**

---

---

---

# 🅲️ Path C: Encrypted LVM Installation

> For users who followed **LVM with Encryption**.

---

## C1. Verify Your Mounts

```bash
lsblk
```

**Expected output:**
```
NAME                      SIZE TYPE  MOUNTPOINT
nvme0n1                    1T  disk
├─nvme0n1p1              512M  part  /mnt/boot/EFI
├─nvme0n1p2                1G  part  /mnt/boot
└─nvme0n1p3              998G  part
  └─lvm                  998G  crypt           ← Encrypted!
    ├─volgroup0-lv_root  200G  lvm   /mnt
    ├─volgroup0-lv_swap   40G  lvm   [SWAP]
    └─volgroup0-lv_home  500G  lvm   /mnt/home
```

You should see `crypt` type for your encrypted container.

---

## C2. Install Base System

```bash
pacstrap -i /mnt base
```

Wait for installation to complete (5-15 minutes).

---

## C3. Generate fstab

```bash
genfstab -U -p /mnt >> /mnt/etc/fstab
```

**Verify:**
```bash
cat /mnt/etc/fstab
```

---

## C4. Enter the New System (Chroot)

```bash
arch-chroot /mnt
```

---

## C5. Set Hostname

```bash
echo "archpc" > /etc/hostname
```

---

## C6. Configure Hosts File

```bash
nvim /etc/hosts
```

Add:
```
127.0.0.1   localhost
::1         localhost
127.0.1.1   archpc.localdomain archpc
```

---

## C7. Set Root Password

```bash
passwd
```

---

## C8. Set Timezone

```bash
ln -sf /usr/share/zoneinfo/Asia/Kolkata /etc/localtime
hwclock --systohc
```

---

## C9. Configure Locale

```bash
nvim /etc/locale.gen
```

Uncomment `en_US.UTF-8 UTF-8`, then:

```bash
locale-gen
echo "LANG=en_US.UTF-8" > /etc/locale.conf
```

---

## C10. Console Configuration (Optional)

```bash
nvim /etc/vconsole.conf
```

Add:
```
KEYMAP=us
FONT=ter-132n
```

---

## C11. Create Your User

```bash
useradd -m -g users -G wheel martin
passwd martin
```

---

## C12. Install Essential Packages (Including LVM!)

```bash
pacman -S base-devel dosfstools grub efibootmgr lvm2 mtools \
vim neovim networkmanager openssh os-prober sudo
```

> ⚠️ **Critical:** The `lvm2` package is **required**!

---

## C13. Install Linux Kernel

```bash
pacman -S linux linux-headers linux-lts linux-lts-headers linux-firmware
```

---

## C14. Install GPU Drivers

**Intel GPU:**
```bash
pacman -S mesa intel-media-driver
```

**AMD GPU:**
```bash
pacman -S mesa libva-mesa-driver
```

**NVIDIA GPU:**
```bash
pacman -S nvidia nvidia-lts nvidia-utils
```

---

## C15. ⚠️ Configure mkinitcpio for Encryption + LVM

This step is **CRITICAL** for encrypted systems!

```bash
nvim /etc/mkinitcpio.conf
```

Find the `HOOKS` line:

```
HOOKS=(base udev autodetect modconf block filesystems keyboard fsck)
```

**Add `encrypt` and `lvm2` between `block` and `filesystems`:**

```
HOOKS=(base udev autodetect modconf block encrypt lvm2 filesystems keyboard fsck)
```

> ⚠️ **Order matters!** `encrypt` must come **before** `lvm2`.

**Save and regenerate initramfs:**

```bash
mkinitcpio -p linux
mkinitcpio -p linux-lts
```

You should see:
```
==> Building image from preset: /etc/mkinitcpio.d/linux.preset: 'default'
...
==> Image generation successful
```

---

## C16. Enable Services

```bash
systemctl enable NetworkManager
systemctl enable sshd
```

---

## C17. Next Step: Bootloader

→ **Continue to [Bootloader Installation (Encrypted)](bootloader.md#-encrypted-lvm-grub-installation)**

---

---

---

# 📋 Quick Reference Summary

## Path A: Standard (No LVM)
```bash
pacstrap -i /mnt base
genfstab -U -p /mnt >> /mnt/etc/fstab
arch-chroot /mnt
# System configuration...
pacman -S base-devel grub efibootmgr vim networkmanager sudo
pacman -S linux linux-headers linux-firmware
systemctl enable NetworkManager
# → Continue to standard bootloader
```

## Path B: LVM (No Encryption)
```bash
pacstrap -i /mnt base
genfstab -U -p /mnt >> /mnt/etc/fstab
arch-chroot /mnt
# System configuration...
pacman -S base-devel grub efibootmgr lvm2 vim networkmanager sudo
pacman -S linux linux-headers linux-firmware
# IMPORTANT: Edit /etc/mkinitcpio.conf
# Add lvm2 to HOOKS: ... block lvm2 filesystems ...
mkinitcpio -p linux
systemctl enable NetworkManager
# → Continue to LVM bootloader
```

## Path C: Encrypted LVM
```bash
pacstrap -i /mnt base
genfstab -U -p /mnt >> /mnt/etc/fstab
arch-chroot /mnt
# System configuration...
pacman -S base-devel grub efibootmgr lvm2 vim networkmanager sudo
pacman -S linux linux-headers linux-firmware
# CRITICAL: Edit /etc/mkinitcpio.conf
# Add encrypt lvm2 to HOOKS: ... block encrypt lvm2 filesystems ...
mkinitcpio -p linux
systemctl enable NetworkManager
# → Continue to encrypted bootloader
```

---

## ➡️ Next: Bootloader

Based on your path, continue to the appropriate bootloader section:

- **Path A:** [Standard GRUB Installation](bootloader.md#-standard-grub-installation)
- **Path B:** [LVM GRUB Installation](bootloader.md#-lvm-grub-installation)
- **Path C:** [Encrypted GRUB Installation](bootloader.md#-encrypted-lvm-grub-installation)

---

<div align="center">

[← Partitioning](../02-partitioning/) | [Back to Main Guide](../../README.md) | [Next: Bootloader →](bootloader.md)

</div>
