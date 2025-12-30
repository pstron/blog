---
title: 'Hyprland Setup & Configuration'
published: 2025-11-13
draft: false
tags: ['archlinux', 'hyprland', 'neovim', 'sddm', 'grub', 'dotfiles']
toc: true
---

A guide to setup Hyprland as your Arch Linux desktop with my dotfiles.

## Before reading

:::important
I assume you have installed the Arch Linux based on my previous [post](/posts/archlinux-installation-and-configuration). If not, please proceed carefully.
:::

::github{repo="pstron/dotfiles"}

This configuration is based on my dotfiles repository. You can use it with `yadm`:

```bash
sudo pacman -S yadm
yadm clone -b dotfiles git@github.com:pstron/dotfiles.git
```

If you don't need to sync or keep up with the latest, you can also just git clone and manually copy the files.

## CLI configuration

### Install necessary CLI packages

```bash
sudo pacman -S bat btop chafa eza fastfetch fd fzf lsd ripgrep yazi tmux
```

### Change to zsh

Change to `zsh` and load zinit, starship, etc automatically

```bash
chsh -s /bin/zsh
zsh
```

### Neovim

Install Neovim:

```bash
sudo pacman -S neovim
```

then launch Neovim to automatically install and load plugins:

```bash
nvim
```

After loading, you will have a well-prepared neovim with [NvChad](https://nvchad.com).

### Other

Tools like `tmux`, `btop`, `yazi`, etc. are also ready to use.

## Desktop

### Install Hyprland and other packages

```bash
sudo pacman -S hyprland hyprlock hyprpolkitagent hyprshot polkit uswm waybar wl-clipboard wofi mako ghostty alacritty xdg-desktop-portal-hyprland xdg-user-dirs
```

```bash
sudo pacman -S blueberry bluez bluez-utils brightnessctl pipewire pipewire-audio pipewire-alsa pipewire-pulse wireplumber alsa-utils alsa-firmware alsa-ucm-conf alsa-pugins pamixer pavucontrol pulseaudio-alsa
```

```bash
sudo pacman -S ark dolphin firefox gwenview mpv neovide timeshift papirus-icon-theme ttf-jetbrains-mono-nerd wqy-zenhei noto-fonts-cjk noto-fonts-emoji noto-fonts-extra
```

then enable some services:

```bash
sudo systemctl enable bluetooth
sudo systemctl --user enable waybar.service
sudo systemctl enable sddm
```

Press `Meta + Enter` to open an alacritty terminal. You can `bat ~/.config/hypr/hyprland.conf` and check the `KEYBINDINGS` section to learn how to use.

### Install sddm theme

::github{repo="catppuccin/sddm"}

I would recommend `Catppuccin Macchiato`.

Install dependencies:

```bash
pacman -Syu qt6-svg qt6-declarative qt5-quickcontrols2
```

Download your chosen flavour + accent zip file from the [latest GitHub release](https://github.com/catppuccin/sddm/releases/latest). Unzip the file and move the resulting directory to `/usr/share/sddm/themes/`.

Create and edit:

```bash
sudo vim /etc/sddm.conf.d/theme.conf
```

```txt title="theme.conf"
[Theme]
Current=catppuccin-macchiato-mauve
```

### Install GRUB theme

::github{repo="catppuccin/grub"}

Clone this repository locally and enter the cloned folder:

```bash
git clone https://github.com/catppuccin/grub.git && cd grub
```

Copy your selected theme from `src` folder to `/usr/share/grub/themes/`

Uncomment and edit following line in `/etc/default/grub` to your selected theme:

```txt title="grub"
GRUB_THEME="/usr/share/grub/themes/catppuccin-[flavor]-grub-theme/theme.txt"
```

Update grub:

```bash
sudo grub-mkconfig -o /boot/grub/grub.cfg
```

### Change Hyprland wallpaper

I use a hacky way to do this:

Prepare a `loadwall.sh`, replace `/path/to/wall.png` with yours, and then `chmod +x loadwall.sh`.

```bash title="loadwall.sh"
#!/bin/bash
sudo rm /usr/share/hypr/wall0.png
sudo rm /usr/share/hypr/wall1.png
sudo rm /usr/share/hypr/wall2.png

sudo cp /path/to/wall.png /usr/share/hypr/wall0.png
sudo cp /path/to/wall.png /usr/share/hypr/wall1.png
sudo cp /path/to/wall.png /usr/share/hypr/wall2.png
```

Now you can change Hyprland wallpaper by `sudo ./loadwall.sh`.

My wallpaper:

![A simple wallpaper with an Arch icon](arch.png)

You can [download](https://raw.githubusercontent.com/pstron/dotfiles/main/arch.png) it from my GitHub repository.

### IME

Install packages needed:

```bash
sudo pacman -S paru # if you don't have an AUR helper
sudo pacman -S fcitx5-im fcitx5-rime
paru -S rime-ice-git
```
Install Catppuccin theme:

::github{repo="catppuccin/fcitx5"}

```bash
git clone https://github.com/catppuccin/fcitx5.git
mkdir -p ~/.local/share/fcitx5/themes/
cp -r ./fcitx5/src/* ~/.local/share/fcitx5/themes
```

1. Navigate to `Fcitx5 Configuration` application through your application launcher.
2. Select the `Addons` Tab.
3. Select the setting icon (gear-wheel) for `Classical User Interface`, located to the right.
4. Apply Catppuccin as the desired fcitx5 theme by navigating to `Theme` and later selecting Catppuccin *Flavour* *Accent*.

## Reboot

After doing all the above steps, you can reboot and enjoy your Arch Linux with a fancy Hyprland!
