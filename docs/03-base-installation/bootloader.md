# 🚀 Bootloader Installation (GRUB)

> Setting up GRUB bootloader for UEFI systems.

![GRUB Bootloader](../../images/grub-bootloader.png)

## 📋 Table of Contents

- [What is a Bootloader?](#-what-is-a-bootloader)
- [GRUB Installation](#-grub-installation)
- [GRUB Configuration](#-grub-configuration)
- [Encrypted System Setup](#-encrypted-system-setup)
- [Dual Boot with Windows](#-dual-boot-with-windows)
- [Final Steps](#-final-steps)

---

## 💡 What is a Bootloader?

A **bootloader** is the first program that runs when you turn on your computer. It loads the operating system.

**Boot process:**
```
Power On → UEFI → Bootloader (GRUB) → Linux Kernel → System
```

### Why GRUB?

| Feature | Description |
|---------|-------------|
| **Multi-boot** | Boot multiple operating systems |
| **Customizable** | Themes, menus, timeout |
| **Flexible** | Supports encryption, LVM, RAID |
| **Recovery** | Edit boot parameters on the fly |

---

## 🛠️ GRUB Installation

### Prerequisites

Ensure you have installed:
```bash
pacman -S grub efibootmgr
```

### Create EFI Directory

If not already mounted:

```bash
mkdir /boot/EFI
mount /dev/nvme0n1p1 /boot/EFI
```

Replace `/dev/nvme0n1p1` with your EFI partition.

### Reload systemd

```bash
systemctl daemon-reload
```

### Install GRUB

```bash
grub-install --target=x86_64-efi --bootloader-id=grub_uefi --recheck
```

**Command breakdown:**

| Part | Meaning |
|------|---------|
| `grub-install` | GRUB installation command |
| `--target=x86_64-efi` | Target platform (64-bit UEFI) |
| `--bootloader-id=grub_uefi` | Name in UEFI boot menu |
| `--recheck` | Recheck device map |

**Expected output:**
```
Installing for x86_64-efi platform.
Installation finished. No error reported.
```

### Copy Locale File

```bash
cp /usr/share/locale/en\@quot/LC_MESSAGES/grub.mo /boot/grub/locale/en.mo
```

**What this does:**
- Copies GRUB messages to locale directory
- Enables English language in GRUB menu

---

## ⚙️ GRUB Configuration

### Basic Configuration

Edit GRUB defaults:

```bash
nvim /etc/default/grub
```

**Key settings:**

```bash
# Default menu entry (0 = first)
GRUB_DEFAULT=0

# Boot timeout in seconds
GRUB_TIMEOUT=5

# Default kernel parameters
GRUB_CMDLINE_LINUX_DEFAULT="loglevel=3 quiet"

# Additional kernel parameters
GRUB_CMDLINE_LINUX=""

# Disable submenu
GRUB_DISABLE_SUBMENU=y

# Enable os-prober (for dual boot)
GRUB_DISABLE_OS_PROBER=false
```

**Parameter descriptions:**

| Parameter | Description |
|-----------|-------------|
| `loglevel=3` | Only show errors during boot |
| `quiet` | Suppress most boot messages |

### Generate Configuration

```bash
grub-mkconfig -o /boot/grub/grub.cfg
```

**Command breakdown:**

| Part | Meaning |
|------|---------|
| `grub-mkconfig` | Generate GRUB config |
| `-o` | Output file |
| `/boot/grub/grub.cfg` | Config location |

---

## 🔐 Encrypted System Setup

If you're using LUKS encryption with LVM, you **must** configure GRUB correctly!

### Edit GRUB Configuration

```bash
nvim /etc/default/grub
```

### Add Encrypted Device Parameter

Find this line:
```bash
GRUB_CMDLINE_LINUX_DEFAULT="loglevel=3 quiet"
```

Modify it:
```bash
GRUB_CMDLINE_LINUX_DEFAULT="loglevel=3 quiet cryptdevice=/dev/nvme0n1p3:volgroup0"
```

Or use the `GRUB_CMDLINE_LINUX` line:
```bash
GRUB_CMDLINE_LINUX="cryptdevice=/dev/nvme0n1p3:volgroup0"
```

**Parameter breakdown:**

| Part | Meaning |
|------|---------|
| `cryptdevice=` | Specify encrypted device |
| `/dev/nvme0n1p3` | Your LUKS partition |
| `:volgroup0` | Name to map it to |

> ⚠️ Replace `/dev/nvme0n1p3` with your actual encrypted partition!

### Using UUID (More Reliable)

For more reliable identification, use UUID:

```bash
# Find UUID
blkid /dev/nvme0n1p3
```

Then use:
```bash
GRUB_CMDLINE_LINUX="cryptdevice=UUID=xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx:volgroup0"
```

### Regenerate GRUB Config

```bash
grub-mkconfig -o /boot/grub/grub.cfg
```

Look for output like:
```
Found linux image: /boot/vmlinuz-linux
Found initrd image: /boot/initramfs-linux.img
Found fallback initrd image: /boot/initramfs-linux-fallback.img
```

---

## 🪟 Dual Boot with Windows

If you have Windows installed, GRUB can detect and add it to the boot menu.

### Enable os-prober

```bash
nvim /etc/default/grub
```

Ensure this line is:
```bash
GRUB_DISABLE_OS_PROBER=false
```

### Mount Windows EFI (if separate)

```bash
mkdir -p /mnt/win_efi
mount /dev/nvme0n1p1 /mnt/win_efi  # Windows EFI partition
```

### Run os-prober

```bash
os-prober
```

**Expected output:**
```
/dev/nvme0n1p1@/EFI/Microsoft/Boot/bootmgfw.efi:Windows Boot Manager:Windows:efi
```

### Regenerate GRUB Config

```bash
grub-mkconfig -o /boot/grub/grub.cfg
```

Look for:
```
Found Windows Boot Manager on /dev/nvme0n1p1@/EFI/Microsoft/Boot/bootmgfw.efi
```

### Fix Time Issues (Windows Dual Boot)

Windows uses local time for hardware clock, Linux uses UTC. Fix this:

**Option 1: Tell Linux to use local time**
```bash
timedatectl set-local-rtc 1
```

**Option 2: Tell Windows to use UTC (recommended)**

In Windows, run as Administrator:
```
reg add "HKEY_LOCAL_MACHINE\System\CurrentControlSet\Control\TimeZoneInformation" /v RealTimeIsUniversal /d 1 /t REG_DWORD /f
```

---

## ✅ Final Steps

### Exit Chroot

```bash
exit
```

### Unmount All Partitions

```bash
umount -a
```

> ⚠️ You may see "target is busy" warnings - that's normal.

### Reboot

```bash
reboot
```

> 💡 Remove the USB drive when the system restarts!

---

## 🔧 Troubleshooting

### GRUB Not Found in UEFI

```bash
# Check if EFI was mounted correctly
mount | grep efi

# Reinstall GRUB
grub-install --target=x86_64-efi --bootloader-id=grub_uefi --recheck
```

### "error: no such device" at Boot

- UUID might be wrong
- Check device path in GRUB config
- Run `grub-mkconfig` again

### System Boots Directly to Windows

- Windows Fast Startup is enabled
- Boot into UEFI and change boot order
- Set GRUB as first boot option

### "Slot 0 opened" Then Hangs (Encrypted)

- mkinitcpio hooks are incorrect
- Regenerate initramfs:
```bash
mkinitcpio -p linux
```

### No Windows Entry

```bash
# Install os-prober if missing
pacman -S os-prober

# Mount Windows partition and rerun
os-prober
grub-mkconfig -o /boot/grub/grub.cfg
```

---

## 📋 Complete GRUB Commands

```bash
# Mount EFI partition
mkdir /boot/EFI
mount /dev/nvme0n1p1 /boot/EFI

# Reload systemd
systemctl daemon-reload

# Install GRUB
grub-install --target=x86_64-efi --bootloader-id=grub_uefi --recheck

# Copy locale
cp /usr/share/locale/en\@quot/LC_MESSAGES/grub.mo /boot/grub/locale/en.mo

# Edit config (if needed)
nvim /etc/default/grub

# Generate config
grub-mkconfig -o /boot/grub/grub.cfg

# Exit and reboot
exit
umount -a
reboot
```

---

## ➡️ Next Steps

After rebooting, continue with:

→ [First Boot](../04-post-installation/first-boot.md)

---

<div align="center">

[← Base Installation](base-install.md) | [Back to Main Guide](../../README.md) | [Next: First Boot →](../04-post-installation/first-boot.md)

</div>
