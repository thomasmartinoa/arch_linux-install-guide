# 🔷 KDE Plasma Installation

> Full-featured, highly customizable desktop environment.

![KDE Plasma](../../images/kde-plasma.png)

## 📦 Installation

### Minimal Installation

```bash
sudo pacman -S plasma-desktop sddm
```

### Full Installation (Recommended)

```bash
sudo pacman -S plasma kde-applications
```

### Enable Display Manager

```bash
sudo systemctl enable sddm
```

### Reboot

```bash
sudo reboot
```

---

## 📋 Package Groups

| Group | Description |
|-------|-------------|
| `plasma` | Full Plasma desktop |
| `plasma-desktop` | Minimal Plasma |
| `kde-applications` | All KDE apps |
| `kde-utilities` | Essential utilities |

---

## 🎨 Customization

KDE is extremely customizable:

1. **Right-click desktop** → Configure Desktop
2. **System Settings** → Appearance
3. **Add widgets** to panels
4. **Download themes** from KDE Store

---

## ➡️ Next Steps

→ [Essential Software](../06-essential-software/essential-packages.md)

---

<div align="center">

[← DE Overview](de-overview.md) | [Back to Main Guide](../../README.md)

</div>
