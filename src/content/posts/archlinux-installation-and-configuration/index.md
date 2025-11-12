---
title: 'Arch Linux Installation & Configuration'
published: 2025-11-12
draft: false
tags: ['archlinux', 'zsh', 'refind']
toc: true
---

This is a brief record of Arch Linux installation and configuration for quick reference.

## Preparation

### Download ISO

Download the latest [ISO](https://mirrors.ustc.edu.cn/archlinux/iso/latest) from the [USTC Mirror](https://mirrors.ustc.edu.cn).

## Installation

### Disable reflector

```bash
systemctl stop reflector.service
```

:::note
Disable the buzzer:

```bash
rmmod pcspkr
```
:::

### Ensure UEFI

```bash
ls /sys/firmware/efi/efivars
```

### Connecting to WIFI

```bash
iwctl
device list
station wlan0 scan
station wlan0 get-networks
station wlan0 connect wifi-name
station wlan0 show
exit
```

### Update time

```bash
timedatectl set-ntp true
timedatectl status
```

### Use mirror source

```bash
vim /etc/pacman.d/mirrorlist
```

Recommend mirrors:

```
Server = https://mirrors.ustc.edu.cn/archlinux/$repo/os/$arch
Server = https://mirrors.tuna.tsinghua.edu.cn/archlinux/$repo/os/$arch
```

### Setup Btrfs

#### Check partition status

```bash
lsblk
```

#### Use `cfdisk` to setup the new partition

```bash
cfdisk /dev/sdx
```

:::note
I assume your disk is `sdx`. It may also be `nvmexn1`. You should always replace it carefully with your actual device.
:::

Make a Swap first. The recommended size is the 60% of your RAM *(e.g. 10GB if your RAM is 16GB)*. The type should be `Linux swap`.

Then make the main partition for your files. The type should be the default `Linux filesystem`.

If you didn't have a boot partition before, make a partition of type `EFI System`. About 200 ~ 300 MB is proper, however I would recommend 512 MB.

After complete the partition, check it again and `[Write]`.

Now you can use `fdisk -l` or `lsblk` to check your partition status.

#### Format Btrfs and others

If neccessary, format EFI partition:

```bash
mkfs.fat -F32 /dev/sdxn
```

Format Swap

```bash
mkswap /dev/sdxn
```

Format Btrfs

```bash
mkfs.btrfs -L ArchZone /dev/sdxn
```

*You can also use your own label instead of `ArchZone`*

```bash
mount -t btrfs -o compress=zstd /dev/sdxn /mnt
btrfs subvolume create /mnt/@
btrfs subvolume create /mnt/@home
btrfs subvolume list -p /mnt # if you want to check
umount /mnt
```

### Mount

```bash
mount -t btrfs -o subvol=/@,compress=zstd /dev/sdxn /mnt
mkdir /mnt/home
mount -t btrfs -o subvol=/@home,compress=zstd /dev/sdxn /mnt/home
mkdir -p /mnt/boot
mount /dev/sdxn /mnt/boot
swapon /dev/sdxn
```

To check:

```bash
df -h
free -h
```

### Install system

```bash
pacstrap /mnt base base-devel linux-zen linux-firmware btrfs-progs
```

:::note
If you meet some GPG problem, solve it by:

```bash
pacman -S archlinux-keyring
```
:::

```bash
pacstrap /mnt iwd networkmanager vim sudo zsh zsh-completions
```

### Generate fstab

```bash
genfstab -U /mnt > /mnt/etc/fstab
```

### Change root

```bash
arch-chroot /mnt
```

### Follow-up setup

```bash
vim /etc/hostname
```

```bash
vim /etc/hosts
```

```
127.0.0.1   localhost
::1 localhost
127.0.1.1   [hostname].localdomain [hostname]
```

```bash
ln -sf /usr/share/zoneinfo/Asia/Shanghai /etc/localtime
```

```bash
hwclock -systohc
```

```bash
vim /etc/locale.gen # en_US.UTF-8 UTF-8 & zh_CN.UTF-8 UTF-8
locale-gen
echo 'LANG=en_US.UTF-8' > /etc/locale.conf
```

```bash
passwd root
```

```bash
# Choose one
pacman -S intel-ucode
pacman -S amd-ucode
```

### Boot software

Install GRUB:

```bash
pacman -S grub efibootmgr os-prober
grub-install -target=x86_64-efi -efi-directory=/boot --bootloader-id=GRUB
```

Get your swap partition id:

```bash
lsblk -o name,mountpoint,size,uuid | grep SWAP
```

Configuration:

```bash
vim /etc/default/grub
```

```
GRUB_CMDLINE_LINUX_DEFAULT="loglevel=5 nowatchdog resume=UUID=[your swap partition uuid]"
GRUB_DISABLE_OS_PROBER=false
GRUB_DEFAULT=saved
GRUB_SAVEDEFAULT=true
GRUB_DISABLE_SUBMENU=y # Opitional
```

Apply:

```bash
grub-mkconfig -o /boot/grub/grub.cfg
```

Edit `resume` hook:

```bash
vim /etc/mkinitcpio.conf
```

Add `resume` in the `HOOKS` line. It must be added after `udev`, and before `lvm2` if there is it.

Regenerate `initramfs`:

```bash
sudo mkinitcpio -P
```

### Enter your Arch Linux

After the above steps, you can reboot and directly enter you Arch Linux now!

#### Connecting to WIFI

```bash
vim /etc/NetworkManager/conf.d/wifi_backend.conf
```

```
[device]
wifi.backend=iwd
```

```bash
systemctl enable --now NetworkManager
```

Then use `nmtui` or `iwctl`.

#### fastfetch

```bash
pacman -S fastfetch
```

```bash
fastfetch
```

## Basic configuration

```bash
pacman -Syu
```

*Do this regularly :)*

Edit your `.bash_profile`

```bash
vim ~/.bash_profile
```

to add:

```bash
export EDITOR='vim'
```

to use vim as default editor.

Add a main admin user:

```bash
useradd -m -G wheel -s /bin/bash [username]
passwd [username]
EDITOR=vim visudo
#%wheel ALL=(ALL) NOPASSWD: ALL
```

Config pacman:

```bash
vim /etc/pacman.conf
```

Uncomment `[multilib]` and `Color`, add `archlinuxcn` at the bottom:

```
[archlinuxcn]
Server = https://mirrors.ustc.edu.cn/archlinuxcn/$arch
Server = https://mirrors.tuna.tsinghua.edu.cn/archlinuxcn/$arch
```

Then

```bash
pacman -Sy archlinuxcn-keyring
pacman -Syyu
```

## Hyprland and more with my dotfiles

Login with your main user instead of root for the following steps.

::github{repo="pstron/dotfiles"}

My dotfiles repository includes mordern configuration files for CLI tools like `zsh` and a fancy rice for `Hyprland`.

Use it with yadm:

```bash
sudo pacman -S yadm
yadm clone -b dotfiles git@github.com:pstron/dotfiles.git
```

Now you can change your shell to zsh:

```bash
chsh -s /bin/zsh
```

To install Hyprland and other, refer to the next [post](/posts/hyprland-installation-and-configuration).

## References

[Arch Linux Brief Guide](https://arch.icekylin.online/) - A really nice guide for Arch Linux installation. Especially for CN users.

[Arch Linux Wiki](https://wiki.archlinux.org/) - Almost everything you want to know about Arch Linux or even more can be found here.
