# ⚙️ BIOS/UEFI Settings

> Before booting into the Arch Linux installer, you need to configure your BIOS/UEFI settings properly.

![BIOS Settings](../../images/bios-settings.png)

## 📋 Table of Contents

- [Accessing BIOS/UEFI](#-accessing-biosuefi)
- [Essential Settings](#-essential-settings)
- [Boot Order](#-boot-order)
- [Common BIOS Keys by Manufacturer](#-common-bios-keys-by-manufacturer)
- [Troubleshooting](#-troubleshooting)

---

## 🔑 Accessing BIOS/UEFI

To enter your BIOS/UEFI settings, you need to press a specific key during boot:

1. **Restart** your computer
2. **Press the BIOS key** repeatedly as soon as the computer starts
3. The BIOS key varies by manufacturer (see table below)

### Common BIOS Keys by Manufacturer

| Manufacturer | BIOS Key | Boot Menu Key |
|--------------|----------|---------------|
| **ASUS** | F2 or DEL | F8 |
| **Acer** | F2 or DEL | F12 |
| **Dell** | F2 | F12 |
| **HP** | F10 or ESC | F9 |
| **Lenovo** | F1 or F2 | F12 |
| **MSI** | DEL | F11 |
| **Gigabyte** | DEL | F12 |
| **Samsung** | F2 | F10 |
| **Toshiba** | F2 | F12 |
| **Intel NUC** | F2 | F10 |

> 💡 **Tip:** If you're unsure, try pressing **F2**, **DEL**, or **ESC** repeatedly during boot.

---

## ⚡ Essential Settings

### 1. Disable Secure Boot

**What is Secure Boot?**
Secure Boot is a security feature that prevents unauthorized operating systems from loading. While Linux can work with Secure Boot, it's easier to disable it for installation.

**How to disable:**
1. Navigate to **Security** or **Boot** tab
2. Find **Secure Boot** option
3. Set to **Disabled**

![Disable Secure Boot](../../images/secure-boot-disable.png)

> ⚠️ **Note:** You can re-enable Secure Boot after installation if needed, but it requires additional configuration.

---

### 2. Set UEFI Mode (Not Legacy/CSM)

**What is UEFI vs Legacy?**
- **UEFI** (Unified Extensible Firmware Interface): Modern boot method, required for GPT partitions
- **Legacy/CSM** (Compatibility Support Module): Old BIOS mode, uses MBR partitions

**Why UEFI?**
- Supports drives larger than 2TB
- Faster boot times
- Better security features
- Required for this guide

**How to configure:**
1. Navigate to **Boot** tab
2. Find **Boot Mode** or **UEFI/Legacy Boot**
3. Set to **UEFI Only**
4. Disable **CSM** (Compatibility Support Module) if present

```
UEFI Mode: Enabled
CSM Support: Disabled
Secure Boot: Disabled
```

---

### 3. Enable AHCI Mode (For SATA drives)

**What is AHCI?**
AHCI (Advanced Host Controller Interface) enables advanced features like hot-swapping and Native Command Queuing for SATA drives.

**How to configure:**
1. Navigate to **Advanced** or **Storage** tab
2. Find **SATA Mode** or **SATA Configuration**
3. Set to **AHCI** (not IDE or RAID)

> ⚠️ **Warning:** If Windows is installed in IDE mode, changing to AHCI may prevent Windows from booting. Research how to enable AHCI in Windows first if dual-booting.

---

### 4. Disable Fast Boot (Optional but Recommended)

**What is Fast Boot?**
Fast Boot skips certain hardware checks to speed up boot time, but it can cause issues with dual-booting and USB detection.

**How to disable:**
1. Navigate to **Boot** tab
2. Find **Fast Boot** option
3. Set to **Disabled**

---

### 5. Virtualization Settings (If needed)

If you plan to use virtual machines:

1. Navigate to **Advanced** or **CPU Configuration**
2. Enable:
   - **Intel VT-x** or **AMD-V** (CPU virtualization)
   - **Intel VT-d** or **AMD-Vi** (IOMMU for GPU passthrough)

---

## 💾 Boot Order

After configuring settings, set your USB drive as the first boot device:

1. Navigate to **Boot** tab
2. Find **Boot Priority** or **Boot Order**
3. Move **USB Drive** to the top
4. Alternatively, use the **Boot Menu Key** (F12, F8, etc.) during startup

### Boot Order Example:
```
1. USB Hard Drive (UEFI)
2. Internal SSD/HDD
3. Network Boot
```

---

## ✅ Pre-Flight Checklist

Before saving and exiting, verify:

- [ ] **Secure Boot:** Disabled
- [ ] **Boot Mode:** UEFI Only
- [ ] **CSM:** Disabled
- [ ] **SATA Mode:** AHCI
- [ ] **Fast Boot:** Disabled (optional)
- [ ] **Boot Order:** USB first

### Save and Exit:
- Press **F10** to save and exit
- Confirm with **Yes** or **Enter**

---

## 🔧 Troubleshooting

### USB Drive Not Detected

1. Try a different USB port (USB 2.0 ports are more compatible)
2. Ensure USB drive was created correctly (see [Create Bootable USB](create-bootable-usb.md))
3. Disable **Fast Boot**
4. Enable **USB Legacy Support** if available

### "No Bootable Device Found"

1. Verify USB drive is created in UEFI mode, not Legacy
2. Check if Secure Boot is disabled
3. Try recreating the USB drive

### System Boots Directly to Windows

1. Disable **Fast Startup** in Windows:
   - Control Panel → Power Options → Choose what the power buttons do
   - Click "Change settings that are currently unavailable"
   - Uncheck "Turn on fast startup"
2. Enter Boot Menu (F12, etc.) and select USB drive manually

---

## 📖 Understanding the Terms

| Term | Description |
|------|-------------|
| **BIOS** | Basic Input/Output System - old firmware interface |
| **UEFI** | Unified Extensible Firmware Interface - modern replacement for BIOS |
| **GPT** | GUID Partition Table - modern partition scheme, requires UEFI |
| **MBR** | Master Boot Record - old partition scheme, works with Legacy BIOS |
| **CSM** | Compatibility Support Module - allows UEFI to boot Legacy systems |
| **Secure Boot** | Security feature that validates boot software signatures |
| **AHCI** | Advanced Host Controller Interface - enables advanced SATA features |

---

## ➡️ Next Steps

Once your BIOS is configured:

→ [Create Bootable USB](create-bootable-usb.md)

---

<div align="center">

[← Back to Main Guide](../../README.md) | [Next: Create Bootable USB →](create-bootable-usb.md)

</div>
