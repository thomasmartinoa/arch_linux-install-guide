# ⚡ Performance Tweaks

> Optimizing your Arch Linux system.

## 🚀 Quick Optimizations

### Enable Parallel Downloads

```bash
sudo nvim /etc/pacman.conf
```

Uncomment:
```
ParallelDownloads = 5
```

### Enable TRIM (SSD)

```bash
sudo systemctl enable fstrim.timer
```

### Reduce Swappiness

```bash
echo "vm.swappiness=10" | sudo tee /etc/sysctl.d/99-swappiness.conf
```

### Enable zram (Compressed RAM)

```bash
sudo pacman -S zram-generator

sudo nvim /etc/systemd/zram-generator.conf
```

Add:
```
[zram0]
zram-size = ram / 2
compression-algorithm = zstd
```

```bash
sudo systemctl daemon-reload
sudo systemctl start systemd-zram-setup@zram0.service
```

---

## 🎮 Gaming Optimizations

```bash
# Enable GameMode
sudo pacman -S gamemode lib32-gamemode

# Steam
sudo pacman -S steam

# Proton/Wine
sudo pacman -S wine wine-gecko wine-mono
```

---

## 🔋 Laptop Power Management

```bash
sudo pacman -S tlp tlp-rdw
sudo systemctl enable tlp
sudo systemctl enable NetworkManager-dispatcher
sudo systemctl mask systemd-rfkill.service
sudo systemctl mask systemd-rfkill.socket
```

---

<div align="center">

[← Essential Software](../06-essential-software/essential-packages.md) | [Back to Main Guide](../../README.md)

</div>
