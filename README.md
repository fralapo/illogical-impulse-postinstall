# 🚀 Post-Installation illogical_impulse - Complete Setup Guide

Complete guide to configure your system after installing **[illogical_impulse](https://github.com/end-4/dots-hyprland)** on Arch Linux / EndeavourOS.

---

## 📋 Table of Contents

1. [System Update](#1-system-update)
2. [Essential GUI Applications](#2-essential-gui-applications)
3. [Audio Configuration](#3-audio-configuration)
4. [Themes and Icons](#4-themes-and-icons)
5. [Bluetooth and Devices](#5-bluetooth-and-devices)
6. [Input Remapper - Mouse Button Configuration](#6-input-remapper---mouse-button-configuration)
7. [EasyEffects and Audio Presets](#7-easyeffects-and-audio-presets)
8. [Screenshots with Hyprshot](#8-screenshots-with-hyprshot)
9. [Custom Keybindings](#9-custom-keybindings)
10. [Final Optimizations](#10-final-optimizations)
11. [App Center (GNOME Software)](#11-app-center-gnome-software)
12. [Final Checklist](#12-final-checklist)
13. [Common Troubleshooting](#common-troubleshooting)
14. [Useful Resources](#useful-resources)
15. [Quick Reference Commands](#quick-reference-commands)

***

## 1. 🔄 System Update

```bash
# Update all packages
yay -Syu

# Clean old package cache
yay -Sc
```

***

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
    gnome-software \
    hyprshot \
    input-remapper-git
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
* `hyprshot` - Screenshot utility for Hyprland
* `input-remapper-git` - Mouse and keyboard button remapper for Wayland

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

***

## 3. 🔊 Audio Configuration

### Verify PipeWire

```bash
# Check PipeWire services status
systemctl --user status pipewire pipewire-pulse wireplumber

# If not active, enable them
systemctl --user enable --now pipewire pipewire-pulse wireplumber
```

### Configure Firefox for media controls

Install **Plasma Integration** extension:
1. Open Firefox
2. Go to [Plasma Integration Add-on](https://addons.mozilla.org/firefox/addon/plasma-integration/)
3. Click "Add to Firefox"
4. Restart Firefox

Verify media controls work:

```bash
# While Firefox is playing media
playerctl play-pause
playerctl metadata
```

### Add keybind for pavucontrol

Edit `~/.config/hypr/custom/keybinds.conf`:

```conf
bind = $mainMod, A, exec, pavucontrol
```

### Test Audio

```bash
# Test speakers
speaker-test -c 2 -t wav
```

***

## 4. 🎨 Themes and Icons

### Activate Papirus Icons

```bash
gsettings set org.gnome.desktop.interface icon-theme 'Papirus'
```

### Configure Qt Themes

```bash
# Open qt5ct and qt6ct
qt5ct
qt6ct
```

Select a dark theme compatible with Material You. The rice uses **kde-material-you-colors** which automatically generates colors from the wallpaper.

### Configure GTK Themes

```bash
# Open nwg-look
nwg-look
```

Select a GTK theme consistent with the rice (Adwaita-dark or similar).

### Verify Material Symbols Font

```bash
# Check that the font is installed
fc-list | grep -i material
```

If icons are missing in the interface, restart Hyprland: `Super + Shift + Q` → Logout → Login.

***

## 5. 📡 Bluetooth and Devices

### Install bluez and blueman

```bash
yay -S --needed bluez bluez-utils blueman
```

### Enable Bluetooth service

```bash
sudo systemctl enable --now bluetooth.service
```

### Connect Bluetooth devices

**Method 1: GUI (recommended)**
```bash
blueman-manager
```

**Method 2: CLI**
```bash
bluetoothctl

# Interactive commands:
# power on
# agent on
# default-agent
# scan on
# pair XX:XX:XX:XX:XX:XX
# connect XX:XX:XX:XX:XX:XX
# trust XX:XX:XX:XX:XX:XX
# exit
```

### Verify LDAC for high-quality Bluetooth headphones

```bash
# Install LDAC codec for high-quality Bluetooth audio
yay -S --needed libldac

# Restart PipeWire
systemctl --user restart pipewire wireplumber
```

In pavucontrol → **Configuration**, select **A2DP (LDAC)** profile for compatible headphones (Sony WH-1000XM series, Bose QuietComfort, etc.).

***

## 6. 🖱️ Input Remapper - Mouse Button Configuration

### Overview

**Input Remapper** allows you to remap mouse buttons (and keyboard keys) on Wayland/Hyprland. Perfect for configuring extra buttons on Logitech mice (MX series, Anywhere series, etc.) where Piper fails.

### Install Input Remapper

```bash
yay -S --needed input-remapper-git
```

### Enable Input Remapper service

```bash
sudo systemctl enable --now input-remapper
```

### Configure Mouse Buttons

Launch the GUI:
```bash
input-remapper-gtk
```

**Configuration steps:**

1. **Select Device**: In the "Device" dropdown, select your mouse (e.g., "Logitech MX Anywhere 3")

2. **Create Preset**: Click "New Preset" and give it a name (e.g., "Hyprland")

3. **Add Button Mappings**: Click "Add" to create a new mapping
   - **Key column**: Click and press the mouse button you want to remap
   - **Mapping column**: Enter the key/command you want to assign

4. **Save and Apply**: Click "Save" then "Apply"

### Common Mouse Button Mappings

#### Volume Control (Side Buttons)
```
Button 8 (Forward)  → XF86AudioRaiseVolume
Button 9 (Back)     → XF86AudioLowerVolume
```

#### Media Controls
```
Button 8  → XF86AudioNext
Button 9  → XF86AudioPrev
Button 10 → XF86AudioPlay
```

#### Workspace Navigation
```
Button 8  → key(Super_L+Right)    # Next workspace
Button 9  → key(Super_L+Left)     # Previous workspace
```

#### Custom Commands
```
Button 8  → sh -c "hyprctl dispatch workspace +1"
Button 9  → sh -c "hyprctl dispatch workspace -1"
```

### Verify Button Numbers

To find which button number corresponds to each physical button:

```bash
# Using wev (Wayland event viewer)
wev | grep BTN

# Press each button on your mouse and note the BTN_XXX code
```

Common button mapping:
- **BTN_LEFT** = Left click (Button 1)
- **BTN_RIGHT** = Right click (Button 2)
- **BTN_MIDDLE** = Middle click (Button 3)
- **BTN_SIDE** / **BTN_EXTRA** = Side buttons (Button 8/9, also called Forward/Back)

### Auto-start on Boot

The systemd service should auto-load your presets. Verify:

```bash
# Check service status
systemctl status input-remapper

# Should show: active (running)
```

### GUI Keybind (Optional)

Add to `~/.config/hypr/custom/keybinds.conf`:

```conf
# Open Input Remapper settings
bind = $mainMod SHIFT, M, exec, input-remapper-gtk
```

### Testing

After applying your preset:
1. Press your configured mouse buttons
2. Verify the action happens (volume changes, workspace switches, etc.)
3. If not working, reload: `sudo systemctl restart input-remapper`

### Troubleshooting Input Remapper

```bash
# Check if service is running
systemctl status input-remapper

# Restart service
sudo systemctl restart input-remapper

# Check logs
journalctl -u input-remapper -f

# Verify preset is loaded
input-remapper-control --list-presets

# Manually load a preset
input-remapper-control --command start --device "Your Mouse Name" --preset "Hyprland"
```

***

## 7. 🎧 EasyEffects and Audio Presets

### Install EasyEffects and presets

```bash
# EasyEffects
yay -S --needed easyeffects

# Community presets (includes Sony, Bose, Music, Video)
yay -S --needed easyeffects-bundy01-presets

# Required plugins for all effects
sudo pacman -S --needed mda.lv2 lsp-plugins-lv2 zam-plugins-lv2
```

### Available Presets

**Bundy01 Presets** include:
- **Sony** - Optimized for Sony headphones (WH-1000XM4, WH-1000XM5, WH-CH720N, etc.)
- **Bose** - Optimized for Bose headphones (QuietComfort series, etc.)
- **Music** - Generic preset for music listening
- **Video** - Preset for movies and video content
- **Bass Boost** - Bass emphasis

### Auto-load presets

1. Open `easyeffects`
2. Menu **Presets** → Select desired preset
3. Go to **PipeWire** tab → **Presets Autoloading**
4. Add association:
   - Device: Select your audio device
   - Preset: Select the preset you want to associate

### Start EasyEffects on boot

Add to `~/.config/hypr/custom/execs.conf`:

```conf
exec-once = easyeffects --gapplication-service
```

### Verify preset installation

```bash
# Check that presets are installed
ls ~/.config/easyeffects/output/
```

***

## 8. 📸 Screenshots with Hyprshot

### Install Hyprshot

```bash
yay -S hyprshot
```

### Configure Screenshot Keybinds

**IMPORTANT:** illogical_impulse uses a modular configuration system. Add your keybinds to the **custom** folder, NOT the main config.

Edit `~/.config/hypr/custom/keybinds.conf`:

```conf
# ==========================================
# Screenshot Keybindings (Hyprshot)
# ==========================================

# Screenshot area with selection (Print key)
bind = , Print, exec, hyprshot -m region

# Screenshot current window (Shift + Print)
bind = SHIFT, Print, exec, hyprshot -m window

# Screenshot entire monitor (Ctrl + Print)
bind = CTRL, Print, exec, hyprshot -m output

# Screenshot to clipboard only (Alt + Print)
bind = ALT, Print, exec, hyprshot -m region --clipboard-only
```

### Reload Configuration

```bash
# Reload Hyprland config
hyprctl reload
```

### Verify Keybinds Loaded

```bash
# Check that Print keybinds are active
hyprctl binds | grep -i print
```

### Screenshot Locations

Screenshots are saved by default to:
```
~/Pictures/screenshot-YYYYMMDD-HHMMSS.png
```

### Hyprshot Usage Summary

| Keybind | Action |
|---------|--------|
| `Print` | Screenshot selected area |
| `Shift + Print` | Screenshot window under cursor |
| `Ctrl + Print` | Screenshot entire monitor |
| `Alt + Print` | Screenshot to clipboard only |

### Advanced Options

For custom save location, edit the keybind:

```conf
# Save to custom folder
bind = , Print, exec, hyprshot -m region -o ~/Screenshots
```

Create the custom folder:
```bash
mkdir -p ~/Screenshots
```

***

## 9. ⌨️ Custom Keybindings

### Where to modify keybinds

**IMPORTANT:** illogical_impulse uses a modular configuration structure:

```
~/.config/hypr/
├── hyprland.conf          # Main file (DO NOT EDIT)
├── hyprland/              # Default config (DO NOT EDIT)
│   ├── keybinds.conf
│   ├── general.conf
│   └── ...
└── custom/                # YOUR modifications (EDIT HERE)
    ├── keybinds.conf     ← Add custom keybinds here
    ├── env.conf
    ├── execs.conf
    ├── general.conf
    └── rules.conf
```

**Rule of thumb:** Only modify files in `custom/` folder, never touch files in `hyprland/` folder.

### Edit custom keybinds

```bash
# Edit your custom keybinds file
nano ~/.config/hypr/custom/keybinds.conf
```

### Recommended custom keybinds

Add these to `~/.config/hypr/custom/keybinds.conf`:

```conf
# ==========================================
# Audio & Media
# ==========================================

# Pavucontrol
bind = $mainMod, A, exec, pavucontrol

# EasyEffects
bind = $mainMod SHIFT, A, exec, easyeffects

# ==========================================
# Applications
# ==========================================

# App Center (GNOME Software)
bind = $mainMod, S, exec, gnome-software

# Input Remapper Settings
bind = $mainMod SHIFT, M, exec, input-remapper-gtk

# ==========================================
# Screenshots (already added in section 8)
# ==========================================

bind = , Print, exec, hyprshot -m region
bind = SHIFT, Print, exec, hyprshot -m window
bind = CTRL, Print, exec, hyprshot -m output
bind = ALT, Print, exec, hyprshot -m region --clipboard-only

# ==========================================
# Laptop-specific: Brightness control
# ==========================================

bind = , XF86MonBrightnessUp, exec, brightnessctl set +5%
bind = , XF86MonBrightnessDown, exec, brightnessctl set 5%-
```

### Apply changes

```bash
# Reload configuration
hyprctl reload

# Verify keybinds loaded
hyprctl binds | grep -E "(Print|pavucontrol|easyeffects)"
```

***

## 10. ⚡ Final Optimizations

### Configure Firefox for media integration

```bash
# plasma-browser-integration should already be installed
yay -S --needed plasma-browser-integration
```

Install **Plasma Integration** extension from [Firefox Add-ons](https://addons.mozilla.org/firefox/addon/plasma-integration/) for media controls in Hyprland taskbar.

### Enable TRIM for SSD

```bash
sudo systemctl enable --now fstrim.timer
```

### Optimize swap memory (optional, recommended for SSD)

```bash
# Reduce swappiness for SSD
echo "vm.swappiness=10" | sudo tee -a /etc/sysctl.d/99-swappiness.conf
sudo sysctl -p /etc/sysctl.d/99-swappiness.conf
```

### Laptop-specific: Battery management

**Option 1: TLP (recommended for most laptops)**
```bash
sudo pacman -S --needed tlp
sudo systemctl enable --now tlp.service
```

**Option 2: auto-cpufreq (alternative)**
```bash
yay -S auto-cpufreq
sudo systemctl enable --now auto-cpufreq.service
```

**⚠️ WARNING: Do NOT use both TLP and auto-cpufreq at the same time!**

### Laptop-specific: Brightness control

```bash
# Install brightnessctl if not already present
sudo pacman -S --needed brightnessctl

# Test brightness adjustment
brightnessctl set 50%
```

### Desktop-specific: Performance mode

```bash
# Install gamemode for gaming performance
sudo pacman -S --needed gamemode lib32-gamemode

# Enable performance governor for desktop PCs
echo "performance" | sudo tee /sys/devices/system/cpu/cpu*/cpufreq/scaling_governor
```

### Verify NVIDIA drivers (if NVIDIA GPU)

```bash
# Check installed drivers
nvidia-smi

# Verify kernel modules
lsmod | grep nvidia
```

If you have dual GPU issues, consult [Arch Wiki - NVIDIA](https://wiki.archlinux.org/title/NVIDIA).

***

## 11. 🎨 App Center (GNOME Software)

### Configure App Center

Open `gnome-software` to install applications graphically.

**Recommended apps from App Center:**
- **Extension Manager** - GNOME extension management (if using GTK apps)
- **Flatseal** - Flatpak permission manager
- **Bottles** - Run Windows apps on Linux
- **Warehouse** - Advanced Flatpak app management

### Enable Flatpak (if not already active)

```bash
# Install Flatpak
sudo pacman -S --needed flatpak

# Add Flathub
flatpak remote-add --if-not-exists flathub https://flathub.org/repo/flathub.flatpakrepo
```

Restart the system to apply changes.

***

## 12. ✅ Final Checklist

Verify everything works:

- [ ] Audio works (test with `speaker-test -c 2`)
- [ ] Pavucontrol opens and shows audio devices
- [ ] Firefox media controls work in taskbar (Plasma Integration installed)
- [ ] Bluetooth connected and LDAC active (if compatible headphones)
- [ ] **Input Remapper configured and mouse buttons work**
- [ ] EasyEffects loads presets automatically
- [ ] Papirus icons visible in all apps
- [ ] Material Symbols font loaded (check illogical_impulse UI)
- [ ] Screenshots work with Print key (Hyprshot)
- [ ] Custom keybinds active in `~/.config/hypr/custom/keybinds.conf`
- [ ] Qt/GTK themes consistent with Material You
- [ ] App Center (GNOME Software) works
- [ ] TRIM enabled for SSD
- [ ] **Laptop**: TLP or auto-cpufreq active
- [ ] **Laptop**: Brightness controls work
- [ ] **Desktop**: Performance governor active (optional)

***

## 🔧 Common Troubleshooting

### Missing icons in interface

```bash
# Reinstall Material Symbols
yay -S --needed ttf-material-symbols-variable-git
fc-cache -fv
# Restart Hyprland: Super + Shift + Q
```

### Audio not working

```bash
# Restart PipeWire
systemctl --user restart pipewire pipewire-pulse wireplumber

# Verify devices
pactl list sinks
```

### Firefox media controls not showing in taskbar

```bash
# Verify Plasma Integration extension is installed
# Open Firefox → about:addons → Search "Plasma Integration"

# Verify playerctl detects Firefox
playerctl -l

# Should show: firefox.instance_X_XXX

# Test controls
playerctl play-pause
```

### Print key (Screenshot) not working

```bash
# Verify Hyprshot is installed
pacman -Qs hyprshot

# Check if keybinds are loaded
hyprctl binds | grep -i print

# If not found, verify you added them to custom/keybinds.conf
cat ~/.config/hypr/custom/keybinds.conf | grep Print

# Reload Hyprland config
hyprctl reload

# Test manually
hyprshot -m region
```

### Screenshot keybind conflicts

If you have conflicts with existing screenshot keybinds:

```bash
# Check all Print keybinds
hyprctl binds | grep Print

# Remove old keybinds from hyprland/keybinds.conf (if any)
# Keep only your custom ones in custom/keybinds.conf
```

### Input Remapper buttons not working

```bash
# Check service status
systemctl status input-remapper

# Restart service
sudo systemctl restart input-remapper

# Check logs
journalctl -u input-remapper -f

# Verify device is detected
input-remapper-control --list-devices

# Manually apply preset
input-remapper-control --command start --device "Your Mouse" --preset "Preset Name"

# Test button detection
wev | grep BTN
# Press mouse buttons and verify they're detected
```

### Bluetooth won't connect

```bash
# Restart Bluetooth service
sudo systemctl restart bluetooth.service

# Check status
systemctl status bluetooth.service

# Remove device and re-pair in blueman-manager
```

### EasyEffects presets won't load

```bash
# Verify installation
ls ~/.config/easyeffects/output/

# If empty, reinstall
yay -S --needed easyeffects-bundy01-presets

# Verify plugins
pacman -Qs lsp-plugins lv2 zam-plugins
```

### Conflict TLP and auto-cpufreq

```bash
# Disable one if you have conflicts
sudo systemctl disable --now tlp.service
# OR
sudo systemctl disable --now auto-cpufreq.service
```

### App Center not showing Flatpak apps

```bash
# Restart GNOME Software
killall gnome-software

# Update Flatpak cache
flatpak update

# Restart system
```

### Brightness keys not working (laptops)

```bash
# Check if brightnessctl is installed
pacman -Qs brightnessctl

# Add user to video group
sudo usermod -aG video $USER

# Logout and login again
```

### Custom keybinds not working

```bash
# Verify file location
ls -la ~/.config/hypr/custom/keybinds.conf

# Check for syntax errors
cat ~/.config/hypr/custom/keybinds.conf

# Verify main config sources custom configs
grep "custom/keybinds.conf" ~/.config/hypr/hyprland.conf

# Should show: source=custom/keybinds.conf

# Reload config
hyprctl reload
```

***

## 📚 Useful Resources

- [illogical_impulse Official Wiki](https://end-4.github.io/dots-hyprland-wiki/)
- [illogical_impulse GitHub Repository](https://github.com/end-4/dots-hyprland)
- [Hyprland Wiki](https://wiki.hyprland.org/)
- [Hyprland Keybinds Documentation](https://wiki.hyprland.org/Configuring/Binds/)
- [Arch Wiki](https://wiki.archlinux.org/)
- [Input Remapper GitHub](https://github.com/sezanzeb/input-remapper)
- [EasyEffects GitHub](https://github.com/wwmm/easyeffects)
- [Bundy01 Presets GitHub](https://github.com/Bundy01/EasyEffects-Presets)
- [Hyprshot GitHub](https://github.com/Gustash/Hyprshot)
- [Arch Linux Laptop Guide](https://wiki.archlinux.org/title/Laptop)

***

## 🎯 Quick Reference Commands

```bash
# System maintenance
yay -Syu                                      # Update system
yay -Sc                                       # Clean cache
pacman -Qe                                    # List manually installed packages

# Audio & Media
systemctl --user restart pipewire wireplumber # Restart PipeWire
pavucontrol                                   # Open volume control
playerctl play-pause                          # Control media playback
playerctl -l                                  # List MPRIS players

# Bluetooth
sudo systemctl restart bluetooth.service      # Restart Bluetooth
blueman-manager                               # Open Bluetooth manager

# Input Remapper
input-remapper-gtk                            # Open Input Remapper GUI
sudo systemctl restart input-remapper         # Restart Input Remapper service
input-remapper-control --list-devices         # List detected devices
wev | grep BTN                                # Detect mouse button codes

# Screenshots
hyprshot -m region                            # Screenshot selected area
hyprshot -m window                            # Screenshot window
hyprshot -m output                            # Screenshot monitor

# System monitoring
speaker-test -c 2                             # Test audio
btop                                          # Resource monitor
neofetch                                      # System info

# Hyprland
hyprctl reload                                # Reload Hyprland config
hyprctl binds | grep Print                    # Check Print keybinds
Super + Shift + Q                             # Restart Hyprland (from desktop)

# Configuration files
nano ~/.config/hypr/custom/keybinds.conf      # Edit custom keybinds
nano ~/.config/hypr/custom/execs.conf         # Edit startup programs
```

***

## 🎉 Setup Complete!

Your **illogical_impulse** system is now fully configured and optimized for:

* ✅ Advanced audio management with EasyEffects
* ✅ Bluetooth with LDAC codec support
* ✅ Mouse button remapping with Input Remapper
* ✅ Consistent Material You theming
* ✅ Firefox media controls in taskbar
* ✅ Screenshot functionality with Print key
* ✅ App Center for graphical installations
* ✅ Battery optimizations (laptops)
* ✅ Performance optimizations (desktops)
* ✅ Audio presets for various headphone brands
* ✅ Modular configuration system for easy customization

**Enjoy your Material Design 3 Hyprland experience! 🚀**
