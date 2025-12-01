# 🔒 Security Hardening

> Basic security measures for your Arch system.

## 🔐 Firewall (UFW)

```bash
sudo pacman -S ufw
sudo ufw default deny incoming
sudo ufw default allow outgoing
sudo ufw enable
sudo systemctl enable ufw
```

## 🔑 SSH Security

```bash
sudo nvim /etc/ssh/sshd_config
```

```
PermitRootLogin no
PasswordAuthentication no  # Use keys only
```

## 🛡️ Fail2ban

```bash
sudo pacman -S fail2ban
sudo systemctl enable fail2ban
```

## 🔄 Automatic Updates

```bash
# Install pacman-contrib
sudo pacman -S pacman-contrib

# Enable paccache timer (clean old packages)
sudo systemctl enable paccache.timer
```

---

<div align="center">

[Back to Main Guide](../../README.md)

</div>
