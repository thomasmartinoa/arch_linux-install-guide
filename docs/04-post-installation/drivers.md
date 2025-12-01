# 🖥️ GPU and Hardware Drivers

> Installing graphics drivers for Intel, AMD, and NVIDIA GPUs.

![GPU Drivers](../../images/gpu-drivers.png)

## 📋 Table of Contents

- [Identify Your GPU](#-identify-your-gpu)
- [Intel Graphics](#-intel-graphics)
- [AMD Graphics](#-amd-graphics)
- [NVIDIA Graphics](#-nvidia-graphics)
- [Hybrid Graphics](#-hybrid-graphics)
- [Verify Installation](#-verify-installation)

---

## 🔍 Identify Your GPU

### Check GPU Hardware

```bash
lspci | grep -i vga
lspci | grep -i 3d
```

**Example outputs:**

```bash
# Intel
00:02.0 VGA compatible controller: Intel Corporation HD Graphics 630

# AMD
06:00.0 VGA compatible controller: AMD/ATI Navi 10 [Radeon RX 5600]

# NVIDIA
01:00.0 VGA compatible controller: NVIDIA Corporation TU106 [GeForce RTX 2060]
```

### Detailed GPU Info

```bash
lspci -v -s $(lspci | grep -i vga | cut -d' ' -f1)
```

---

## 💙 Intel Graphics

Intel integrated graphics use open-source drivers included in the kernel.

### Install Intel Drivers

```bash
sudo pacman -S mesa intel-media-driver
```

**Package descriptions:**

| Package | Purpose |
|---------|---------|
| `mesa` | OpenGL implementation |
| `intel-media-driver` | Hardware video acceleration (newer Intel) |

### For Older Intel GPUs (Pre-Broadwell)

```bash
sudo pacman -S mesa libva-intel-driver
```

### Vulkan Support (Gaming)

```bash
sudo pacman -S vulkan-intel
```

### All Intel Packages

```bash
sudo pacman -S mesa intel-media-driver vulkan-intel intel-gpu-tools
```

---

## ❤️ AMD Graphics

AMD uses open-source AMDGPU drivers (included in kernel).

### Install AMD Drivers

```bash
sudo pacman -S mesa libva-mesa-driver
```

**Package descriptions:**

| Package | Purpose |
|---------|---------|
| `mesa` | OpenGL implementation |
| `libva-mesa-driver` | Hardware video acceleration |

### Vulkan Support (Gaming)

```bash
sudo pacman -S vulkan-radeon
```

### For Older AMD GPUs (GCN 2 and older)

```bash
sudo pacman -S xf86-video-amdgpu  # Optional, kernel driver usually sufficient
```

### All AMD Packages

```bash
sudo pacman -S mesa libva-mesa-driver vulkan-radeon
```

### OpenCL Support (Compute)

```bash
sudo pacman -S opencl-mesa
```

---

## 💚 NVIDIA Graphics

NVIDIA requires proprietary drivers for best performance.

### Install NVIDIA Drivers

```bash
sudo pacman -S nvidia nvidia-utils
```

### For LTS Kernel Users

```bash
sudo pacman -S nvidia-lts
```

### NVIDIA Package Options

| Package | Description |
|---------|-------------|
| `nvidia` | Driver for current kernel |
| `nvidia-lts` | Driver for LTS kernel |
| `nvidia-dkms` | DKMS version (compiles for any kernel) |
| `nvidia-utils` | Utilities and libraries |
| `nvidia-settings` | GUI settings application |

### Recommended Installation

```bash
# For both linux and linux-lts kernels
sudo pacman -S nvidia nvidia-lts nvidia-utils nvidia-settings
```

### For Any Kernel (DKMS)

```bash
sudo pacman -S nvidia-dkms nvidia-utils nvidia-settings
```

> 💡 `nvidia-dkms` automatically compiles for your kernel, useful for custom kernels.

### Configure NVIDIA

After installing, regenerate initramfs:

```bash
sudo mkinitcpio -P
```

### NVIDIA + Wayland

For Wayland compositors (like Hyprland):

```bash
# Edit environment variables
sudo nvim /etc/environment
```

Add:
```bash
LIBVA_DRIVER_NAME=nvidia
GBM_BACKEND=nvidia-drm
__GLX_VENDOR_LIBRARY_NAME=nvidia
WLR_NO_HARDWARE_CURSORS=1
```

### Enable DRM Kernel Mode Setting

```bash
sudo nvim /etc/default/grub
```

Add to `GRUB_CMDLINE_LINUX_DEFAULT`:
```bash
GRUB_CMDLINE_LINUX_DEFAULT="... nvidia-drm.modeset=1"
```

Regenerate GRUB config:
```bash
sudo grub-mkconfig -o /boot/grub/grub.cfg
```

---

## 🔀 Hybrid Graphics (Laptop)

Many laptops have both Intel/AMD integrated and NVIDIA discrete graphics.

### Option 1: PRIME Render Offload (Recommended)

Run specific applications on NVIDIA:

```bash
# Install both drivers
sudo pacman -S mesa nvidia nvidia-utils nvidia-prime

# Run application on NVIDIA
prime-run application_name
```

### Option 2: Optimus Manager

For automatic switching:

```bash
# From AUR
yay -S optimus-manager optimus-manager-qt
```

### Option 3: Bumblebee (Older method)

```bash
sudo pacman -S bumblebee mesa nvidia
sudo systemctl enable bumblebeed
sudo gpasswd -a username bumblebee
```

---

## ✅ Verify Installation

### Check Loaded Driver

```bash
lspci -k | grep -A 2 -i vga
```

Look for "Kernel driver in use":
- Intel: `i915`
- AMD: `amdgpu`
- NVIDIA: `nvidia`

### OpenGL Information

```bash
# Install mesa-utils if needed
sudo pacman -S mesa-utils

# Check OpenGL
glxinfo | grep "OpenGL"
```

### Vulkan Information

```bash
# Install vulkan-tools if needed
sudo pacman -S vulkan-tools

# Check Vulkan
vulkaninfo | head -20
```

### NVIDIA Specific

```bash
# Check NVIDIA driver
nvidia-smi
```

### Test 3D Acceleration

```bash
glxgears
```

Should show a window with spinning gears at high FPS.

---

## 📊 Driver Summary Table

| GPU | Driver Package | Vulkan Package | Video Accel |
|-----|----------------|----------------|-------------|
| Intel (new) | `mesa` | `vulkan-intel` | `intel-media-driver` |
| Intel (old) | `mesa` | `vulkan-intel` | `libva-intel-driver` |
| AMD | `mesa` | `vulkan-radeon` | `libva-mesa-driver` |
| NVIDIA | `nvidia`/`nvidia-lts` | included | included |

---

## 📋 Quick Commands

### Intel
```bash
sudo pacman -S mesa intel-media-driver vulkan-intel
```

### AMD
```bash
sudo pacman -S mesa libva-mesa-driver vulkan-radeon
```

### NVIDIA
```bash
sudo pacman -S nvidia nvidia-lts nvidia-utils nvidia-settings
sudo mkinitcpio -P
```

### Verify
```bash
lspci -k | grep -A 2 -i vga
glxinfo | grep "OpenGL"
```

---

## ➡️ Next Steps

→ [Audio & Bluetooth Setup](audio-bluetooth.md)

---

<div align="center">

[← First Boot](first-boot.md) | [Back to Main Guide](../../README.md) | [Next: Audio & Bluetooth →](audio-bluetooth.md)

</div>
