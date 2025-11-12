---
title: 'Hyprland Installation & Configuration'
published: 2025-11-12
draft: false
tags: ['archlinux', 'hyprland']
toc: true
---

Working in progress...

## Before reading

:::important
I assume you have installed the Arch Linux based on my previous [post](/posts/archlinux-installation-and-configuration). If not, please proceed with caution.
:::

::github{repo="pstron/dotfiles"}

This configuration is based on my dotfiles repository. Use it with `yadm`:

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

Install neovim:

```bash
sudo pacman -S neovim
```

then launch neovim and load plugins:

```bash
nvim
```

After loading, you will have a well-prepared neovim with [NvChad](https://nvchad.com).

### Other

Tools like `tmux`, `btop`, `yazi`, etc. are also ready to use.

## Hyprland

Working in progress...
