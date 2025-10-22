
# 🚀 Post-Installation illogical_impulse - Complete Setup Guide

Complete guide to configure your system after installing **illogical_impulse** on Arch Linux / EndeavourOS.

---

## 📋 Table of Contents
1. [System Update](#1-system-update)
2. [Essential GUI Applications](#2-essential-gui-applications)
3. [Audio Configuration](#3-audio-configuration)
4. [Themes and Icons](#4-themes-and-icons)
5. [Bluetooth and Devices](#5-bluetooth-and-devices)
6. [EasyEffects and Audio Presets](#6-easyeffects-and-audio-presets)
7. [Custom Keybindings](#7-custom-keybindings)
8. [Final Optimizations](#8-final-optimizations)
9. [App Center (GNOME Software)](#9-app-center-gnome-software)
10. [Final Checklist](#10-final-checklist)
11. [Common Troubleshooting](#🔧-common-troubleshooting)
12. [Useful Resources](#📚-useful-resources)
13. [Quick Reference Commands](#🎯-quick-reference-commands)

---

## 1. 🔄 System Update

```bash
# Update all packages
yay -Syu

# Clean old package cache
yay -Sc
````

---

## 2. 📦 Essential GUI Applications

### Install base system management packages

```bash
yay -S --needed \
    pavucontrol \
    mpv \
    gparted \
    baobab \
    imv \
    papirus-icon-theme \
    qt5ct \
    qt6ct \
    nwg-look \
    ttf-material-symbols-variable-git \
    gnome-software
```

**What they do:**

* `pavucontrol` - Volume control and audio device selector
* `mpv` - Lightweight video player
* `gparted` - Disk partition editor
* `baobab` - Disk usage analyzer (GNOME Disk Usage)
* `imv` - Wayland-native image viewer
* `papirus-icon-theme` - Essential icon pack
* `qt5ct/qt6ct` - Qt theme configuration
* `nwg-look` - GTK theme configurator
* `ttf-material-symbols-variable-git` - Material Design 3 icon font (REQUIRED)
* `gnome-software` - App Center for graphical app management

### (Optional) Additional Applications

```bash
# Only if needed
yay -S --needed \
    telegram-desktop \
    discord \
    obs-studio \
    vlc \
    libreoffice-fresh \
    thunderbird \
    code
```

---

## 3. 🔊 Audio Configuration

```bash
# Check PipeWire services status
systemctl --user status pipewire pipewire-pulse wireplumber

# If not active, enable them
systemctl --user enable --now pipewire pipewire-pulse wireplumber
```

**Keybind Example:**

```bash
bind = $mainMod, A, exec, pavucontrol
```

Test audio:

```bash
speaker-test -c 2 -t wav
```

---

## 4. 🎨 Themes and Icons

```bash
gsettings set org.gnome.desktop.interface icon-theme 'Papirus'
qt5ct
qt6ct
nwg-look
```

Check font:

```bash
fc-list | grep -i material
```

---

## 5. 📡 Bluetooth and Devices

```bash
yay -S --needed bluez bluez-utils blueman
sudo systemctl enable --now bluetooth.service
blueman-manager
```

**For LDAC support:**

```bash
yay -S --needed libldac
systemctl --user restart pipewire wireplumber
```

---

## 6. 🎧 EasyEffects and Audio Presets

```bash
yay -S --needed easyeffects
yay -S --needed easyeffects-bundy01-presets
sudo pacman -S --needed mda.lv2 lsp-plugins-lv2 zam-plugins-lv2
```

Autoload presets in EasyEffects → **PipeWire → Presets Autoloading**

Start on boot:

```bash
exec-once = easyeffects --gapplication-service
```

---

## 7. ⌨️ Custom Keybindings

```bash
nano ~/.config/hypr/hyprland.conf
```

**Examples:**

```bash
bind = $mainMod, A, exec, pavucontrol
bind = $mainMod SHIFT, A, exec, easyeffects
bind = $mainMod, E, exec, dolphin
bind = $mainMod, S, exec, gnome-software
bind = , Print, exec, grim -g "$(slurp)" - | wl-copy
bind = SHIFT, Print, exec, grim - | wl-copy
bind = , XF86MonBrightnessUp, exec, brightnessctl set +5%
bind = , XF86MonBrightnessDown, exec, brightnessctl set 5%-
```

---

## 8. ⚡ Final Optimizations

```bash
sudo pacman -S --needed plasma-browser-integration
sudo systemctl enable --now fstrim.timer
echo "vm.swappiness=10" | sudo tee -a /etc/sysctl.d/99-swappiness.conf
sudo sysctl -p /etc/sysctl.d/99-swappiness.conf
```

**Laptop:**

```bash
sudo pacman -S --needed tlp
sudo systemctl enable --now tlp.service
# or
yay -S auto-cpufreq
sudo systemctl enable --now auto-cpufreq.service
```

**Desktop:**

```bash
sudo pacman -S --needed gamemode lib32-gamemode
echo "performance" | sudo tee /sys/devices/system/cpu/cpu*/cpufreq/scaling_governor
```

---

## 9. 🎨 App Center (GNOME Software)

```bash
sudo pacman -S --needed flatpak
flatpak remote-add --if-not-exists flathub https://flathub.org/repo/flathub.flatpakrepo
```

---

## 10. ✅ Final Checklist

* [ ] Audio works
* [ ] Bluetooth LDAC active
* [ ] EasyEffects presets load
* [ ] Icons visible
* [ ] Font loaded
* [ ] Screenshots work
* [ ] Themes consistent
* [ ] TRIM enabled
* [ ] Battery optimization active
* [ ] Performance mode active

---

## 🔧 Common Troubleshooting

```bash
# Restart PipeWire
systemctl --user restart pipewire wireplumber

# Restart Bluetooth
sudo systemctl restart bluetooth.service

# Reinstall missing icons
yay -S --needed ttf-material-symbols-variable-git
fc-cache -fv
```

---

## 📚 Useful Resources

* [illogical_impulse Wiki](https://end-4.github.io/dots-hyprland-wiki/)
* [Hyprland Wiki](https://wiki.hyprland.org/)
* [Arch Wiki](https://wiki.archlinux.org/)
* [EasyEffects GitHub](https://github.com/wwmm/easyeffects)
* [Bundy01 Presets](https://github.com/Bundy01/EasyEffects-Presets)

---

## 🎯 Quick Reference Commands

```bash
yay -Syu
yay -Sc
systemctl --user restart pipewire wireplumber
sudo systemctl restart bluetooth.service
speaker-test -c 2
btop
neofetch
```

---

## 🎉 Setup Complete!

Your **illogical_impulse** system is now fully configured and optimized for:

* ✅ Advanced audio with EasyEffects
* ✅ Bluetooth with LDAC
* ✅ Material You theming
* ✅ App Center integration
* ✅ Battery/Performance tuning

Enjoy your **Material Design 3 Hyprland** experience! 🚀
