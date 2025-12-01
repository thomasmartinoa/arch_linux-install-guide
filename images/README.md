# Images Directory

Place your screenshots and images here.

## Required Images

The documentation references these images:

### Pre-installation
- `arch-banner.png` - Main banner for README
- `bios-settings.png` - BIOS configuration screenshot
- `secure-boot-disable.png` - Secure boot disable screenshot
- `bootable-usb.png` - USB creation illustration
- `rufus-settings.png` - Rufus configuration screenshot
- `live-environment.png` - Live boot screenshot

### Partitioning
- `partition-overview.png` - Partition diagram
- `basic-partition.png` - Basic partition layout
- `advanced-partition.png` - Advanced partition layout
- `lvm-setup.png` - LVM diagram
- `encrypted-lvm.png` - Encrypted LVM diagram
- `cfdisk.png` - cfdisk screenshot

### Installation
- `base-install.png` - Base installation screenshot
- `grub-bootloader.png` - GRUB menu screenshot
- `first-boot.png` - First boot screenshot

### Post-installation
- `gpu-drivers.png` - Driver installation
- `audio-bluetooth.png` - Audio setup
- `nmtui.png` - Network manager TUI screenshot

### Desktop Environments
- `desktop-environments.png` - DE comparison
- `de-gnome.png` - GNOME screenshot
- `de-kde.png` - KDE Plasma screenshot
- `de-xfce.png` - XFCE screenshot
- `de-cinnamon.png` - Cinnamon screenshot
- `de-mate.png` - MATE screenshot
- `wm-hyprland.png` - Hyprland screenshot
- `hyprland-desktop.png` - Hyprland full desktop
- `hyprland-example1.png` - Hyprland rice example 1
- `hyprland-example2.png` - Hyprland rice example 2
- `kde-plasma.png` - KDE Plasma full desktop
- `gnome-desktop.png` - GNOME full desktop
- `xfce-desktop.png` - XFCE full desktop

### Other
- `essential-packages.png` - Package management
- `aur.png` - AUR illustration

## Recommended Image Specifications

- **Format:** PNG or WebP
- **Width:** 800-1200px
- **Quality:** Optimized for web

## Taking Screenshots

### In Hyprland
```bash
# Full screen
grim screenshot.png

# Select area
grim -g "$(slurp)" screenshot.png
```

### In X11
```bash
scrot screenshot.png
```
