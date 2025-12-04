# 🔐 LVM with Full Disk Encryption

> The most secure setup using LUKS encryption with LVM for flexible partition management.

![Encrypted LVM](../../images/encrypted-lvm.png)

## 📋 Table of Contents

- [Overview](#-overview)
- [Understanding Encryption](#-understanding-encryption)
- [Partition Layout](#-partition-layout)
- [Step-by-Step Setup](#-step-by-step-setup)
- [Important Configuration](#-important-configuration)
- [Mount Partitions](#-mount-partitions)
- [Verification](#-verification)

---

## 📊 Overview

This setup encrypts your entire system except the EFI and boot partitions:

```
┌────────────────────────────────────────────────────────────────────────────┐
│                                 DISK                                       │
├─────────┬─────────┬────────────────────────────────────────────────────────┤
│   EFI   │  BOOT   │              LUKS Encrypted Container                  │
│  512MB  │   1GB   │  ┌────────────────────────────────────────────────────┐│
│         │         │  │              LVM Physical Volume                   ││
│  FAT32  │  ext4   │  │  ┌──────────────────────────────────────────────┐  ││
│         │ (clear) │  │  │           Volume Group (volgroup0)           │  ││
│         │         │  │  │ ┌───────┐  ┌────────────────┐  ┌───────────┐ │  ││
│         │         │  │  │ │lv_root│  │    lv_home     │  │  lv_swap  │ │  ││
│         │         │  │  │ │ 200GB │  │     500GB      │  │    40GB   │ │  ││
│         │         │  │  │ └───────┘  └────────────────┘  └───────────┘ │  ││
│         │         │  │  └──────────────────────────────────────────────┘  ││
│         │         │  └────────────────────────────────────────────────────┘│
└─────────┴─────────┴────────────────────────────────────────────────────────┘
      ↑        ↑                              ↑
 Unencrypted  Unencrypted               ENCRYPTED (requires password)
```

---

## 🔒 Understanding Encryption

### What is LUKS?

**LUKS** (Linux Unified Key Setup) is the standard for Linux disk encryption.

| Feature | Description |
|---------|-------------|
| **Algorithm** | AES-256 by default |
| **Key Management** | Supports up to 8 key slots |
| **Header** | Contains metadata and encrypted master key |
| **Compatibility** | Standard across Linux distributions |

### Why Encrypt?

| Scenario | Protection |
|----------|------------|
| **Laptop theft** | Data cannot be read without passphrase |
| **Disk resale** | Previous data is unrecoverable |
| **Border crossing** | Privacy protection |
| **Multi-user** | Each user's data protected |

### What Gets Encrypted?

| Partition | Encrypted? | Reason |
|-----------|------------|--------|
| EFI | ❌ No | UEFI can't read encrypted partitions |
| Boot | ❌ No | Bootloader needs to load kernel |
| Root (/) | ✅ Yes | Contains system files |
| Home (/home) | ✅ Yes | Contains personal data |
| Swap | ✅ Yes | May contain sensitive RAM data |

---

## 📐 Partition Layout

For a **1TB NVMe drive** (adjust sizes for your disk):

| # | Partition | Size | Type | Encrypted |
|---|-----------|------|------|-----------|
| 1 | EFI | 512MB | FAT32 | No |
| 2 | Boot | 1GB | ext4 | No |
| 3 | LUKS Container | Remaining | LUKS | Yes |

**Inside LUKS container (LVM):**

| Volume | Size | Filesystem |
|--------|------|------------|
| lv_root | 200GB | ext4 |
| lv_swap | 40GB | swap |
| lv_home | 500GB+ | ext4 |

---

## 🛠️ Step-by-Step Setup

### Step 1: Create Partitions

```bash
gdisk /dev/nvme0n1
```

> 💡 Using `gdisk` instead of `cfdisk` for better GPT handling.

#### In gdisk:

```
# Create new GPT table (if needed)
Command: o
Proceed? Y

# Partition 1: EFI
Command: n
Partition number: 1
First sector: [Enter]
Last sector: +512M
Hex code: EF00

# Partition 2: Boot
Command: n
Partition number: 2
First sector: [Enter]
Last sector: +1G
Hex code: 8300

# Partition 3: LVM (for encryption)
Command: n
Partition number: 3
First sector: [Enter]
Last sector: [Enter] (use all remaining)
Hex code: 8E00

# Verify
Command: p

# Write and exit
Command: w
Proceed? Y
```

**Partition type codes:**

| Code | Type |
|------|------|
| EF00 | EFI System |
| 8300 | Linux filesystem |
| 8E00 | Linux LVM |

---

### Step 2: Format EFI and Boot

```bash
# EFI partition (FAT32)
mkfs.fat -F32 /dev/nvme0n1p1

# Boot partition (ext4)
mkfs.ext4 /dev/nvme0n1p2
```

---

### Step 3: Setup LUKS Encryption

#### Initialize LUKS Container

```bash
cryptsetup luksFormat /dev/nvme0n1p3
```

**You will see:**
```
WARNING!
========
This will overwrite data on /dev/nvme0n1p3 irrevocably.

Are you sure? (Type 'yes' in capital letters): YES
Enter passphrase for /dev/nvme0n1p3: [your secure passphrase]
Verify passphrase: [repeat passphrase]
```

**Command breakdown:**

| Part | Meaning |
|------|---------|
| `cryptsetup` | Disk encryption utility |
| `luksFormat` | Initialize LUKS encryption |
| `/dev/nvme0n1p3` | Partition to encrypt |

> ⚠️ **IMPORTANT:** Choose a strong passphrase! You'll need it every boot. If you forget it, your data is **unrecoverable**.

**Passphrase tips:**
- At least 20 characters
- Mix of words, numbers, symbols
- Consider a passphrase like: `correct-horse-battery-staple-2024!`

---

#### Open LUKS Container

```bash
cryptsetup open --type luks /dev/nvme0n1p3 lvm
```

**Command breakdown:**

| Part | Meaning |
|------|---------|
| `open` | Open/unlock the container |
| `--type luks` | Specify LUKS type |
| `/dev/nvme0n1p3` | Encrypted partition |
| `lvm` | Mapper name (appears at /dev/mapper/lvm) |

Enter your passphrase when prompted.

The decrypted container is now available at `/dev/mapper/lvm`.

---

### Step 4: Create LVM on Encrypted Container

#### Create Physical Volume

```bash
pvcreate /dev/mapper/lvm
```

**What this does:**
- Initializes the decrypted container as an LVM physical volume
- The encryption is transparent to LVM

---

#### Create Volume Group

```bash
vgcreate volgroup0 /dev/mapper/lvm
```

---

#### Create Logical Volumes

```bash
# Root volume
lvcreate -L 200GB volgroup0 -n lv_root

# Swap volume (large for hibernation)
lvcreate -L 40GB volgroup0 -n lv_swap

# Home volume (remaining space)
lvcreate -L 500GB volgroup0 -n lv_home
```

Adjust sizes based on your disk!

**Alternative: Use percentage for home:**
```bash
lvcreate -l 100%FREE volgroup0 -n lv_home
```

---

#### Verify Volumes

```bash
vgdisplay
lvdisplay
```

---

### Step 5: Activate LVM

```bash
modprobe dm_mod
vgscan
vgchange -ay
```

**What these do:**

| Command | Purpose |
|---------|---------|
| `modprobe dm_mod` | Load device mapper module |
| `vgscan` | Scan for volume groups |
| `vgchange -ay` | Activate all volume groups |

---

### Step 6: Format Logical Volumes

```bash
# Root filesystem
mkfs.ext4 /dev/volgroup0/lv_root

# Home filesystem
mkfs.ext4 /dev/volgroup0/lv_home

# Swap
mkswap /dev/volgroup0/lv_swap
```

---

## 📁 Mount Partitions

### Mount in Correct Order

```bash
# 1. Mount root
mount /dev/volgroup0/lv_root /mnt

# 2. Create mount points
mkdir /mnt/boot
mkdir /mnt/home

# 3. Mount boot partition
mount /dev/nvme0n1p2 /mnt/boot

# 4. Mount home
mount /dev/volgroup0/lv_home /mnt/home

# 5. Enable swap
swapon /dev/volgroup0/lv_swap
```

---

### Mount EFI Partition

EFI goes in `/boot/EFI` (after base install):

```bash
mkdir /mnt/boot/EFI
mount /dev/nvme0n1p1 /mnt/boot/EFI
```

> ⚠️ Note: Some setups mount EFI at `/boot/efi`. We use `/boot/EFI` here.

---

## ✅ Verification

### Check Mount Points

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
  └─lvm                  998G  crypt
    ├─volgroup0-lv_root  200G  lvm   /mnt
    ├─volgroup0-lv_swap   40G  lvm   [SWAP]
    └─volgroup0-lv_home  500G  lvm   /mnt/home
```

### Check Swap

```bash
swapon --show
```

---

## ⚠️ Important Configuration

After installing the base system, you **MUST** configure:

### 1. mkinitcpio Hooks

Edit `/etc/mkinitcpio.conf`:

```bash
# In chroot environment
nvim /etc/mkinitcpio.conf
```

Find the HOOKS line and modify:

```
# Original
HOOKS=(base udev autodetect modconf block filesystems keyboard fsck)

# Modified (add encrypt and lvm2, move keyboard before block)
HOOKS=(base udev autodetect modconf kms keyboard keymap consolefont block encrypt lvm2 filesystems fsck)
```

**Order matters!** Critical hooks must be in this order:
1. `keyboard keymap consolefont` - Keyboard support (needed to type password!)
2. `block` - Block device support
3. `encrypt` - LUKS decryption
4. `lvm2` - LVM support
5. `filesystems` - Mount filesystems

> ⚠️ **IMPORTANT:** `keyboard` MUST come BEFORE `block` and `encrypt`, otherwise you won't be able to type your encryption password!

Regenerate initramfs:

```bash
mkinitcpio -p linux
mkinitcpio -p linux-lts  # If you have LTS kernel
```

---

### 2. GRUB Configuration

Edit `/etc/default/grub`:

```bash
nvim /etc/default/grub
```

Add encrypted device to kernel parameters:

```
GRUB_CMDLINE_LINUX_DEFAULT="loglevel=3 quiet cryptdevice=/dev/nvme0n1p3:volgroup0"
```

**Parameter breakdown:**

| Part | Meaning |
|------|---------|
| `cryptdevice=` | Specify encrypted device |
| `/dev/nvme0n1p3` | LUKS partition |
| `:volgroup0` | Name after decryption (matches VG name) |

Regenerate GRUB config:

```bash
grub-mkconfig -o /boot/grub/grub.cfg
```

---

## 📋 Complete Command Summary

```bash
# 1. Create partitions with gdisk
gdisk /dev/nvme0n1
# Create: 512M EFI (EF00), 1G Boot (8300), remaining LVM (8E00)

# 2. Format EFI and Boot
mkfs.fat -F32 /dev/nvme0n1p1
mkfs.ext4 /dev/nvme0n1p2

# 3. Setup encryption
cryptsetup luksFormat /dev/nvme0n1p3
cryptsetup open --type luks /dev/nvme0n1p3 lvm

# 4. Create LVM
pvcreate /dev/mapper/lvm
vgcreate volgroup0 /dev/mapper/lvm
lvcreate -L 200GB volgroup0 -n lv_root
lvcreate -L 40GB volgroup0 -n lv_swap
lvcreate -L 500GB volgroup0 -n lv_home

# 5. Activate LVM
modprobe dm_mod
vgscan
vgchange -ay

# 6. Format logical volumes
mkfs.ext4 /dev/volgroup0/lv_root
mkfs.ext4 /dev/volgroup0/lv_home
mkswap /dev/volgroup0/lv_swap

# 7. Mount everything
mount /dev/volgroup0/lv_root /mnt
mkdir /mnt/boot /mnt/home
mount /dev/nvme0n1p2 /mnt/boot
mkdir /mnt/boot/EFI
mount /dev/nvme0n1p1 /mnt/boot/EFI
mount /dev/volgroup0/lv_home /mnt/home
swapon /dev/volgroup0/lv_swap

# 8. Verify
lsblk
```

---

## 🔐 LUKS Management

### Add Backup Passphrase

```bash
cryptsetup luksAddKey /dev/nvme0n1p3
```

### Remove Passphrase

```bash
cryptsetup luksRemoveKey /dev/nvme0n1p3
```

### Backup LUKS Header

```bash
cryptsetup luksHeaderBackup /dev/nvme0n1p3 --header-backup-file luks-header.img
```

> 💡 Store this backup securely! It can restore the header if corrupted.

### Check LUKS Status

```bash
cryptsetup luksDump /dev/nvme0n1p3
```

---

## 🔧 Troubleshooting

### "No key available with this passphrase"

- Caps Lock might be on
- Check keyboard layout
- Try again carefully

### Boot hangs at "Waiting for /dev/..."

- GRUB_CMDLINE_LINUX is incorrect
- Check the device path in grub configuration

### Emergency shell at boot

- Run `cryptsetup open /dev/nvme0n1p3 lvm`
- Enter passphrase
- Run `vgchange -ay`
- Exit to continue boot

---

## ➡️ Next Steps

Your encrypted system is ready for base installation!

→ [Encrypted Base Installation](../03-base-installation/base-install-encrypted.md)

> 💡 The encrypted guide includes mkinitcpio hooks and GRUB cryptdevice configuration!

---

<div align="center">

[← LVM Setup](lvm-setup.md) | [Back to Main Guide](../../README.md) | [Next: Encrypted Base Installation →](../03-base-installation/base-install-encrypted.md)

</div>
