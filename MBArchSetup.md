#Arch on MBA Installation

(Refresh Source -Syyy
# timedatectl set-ntp true
Using gdisk, ssd was zapped
Using fdisk ssd has been partioned as follows:
GPT partition table
1GB   EFI
rest of disk as one partition for encryption

# modprobe dm-crypt
# cryptsetup benchmark
# cryptsetup -c aes-xts-plain -y -s 512 luksFormat /dev/sda2
# cryptsetup luksOpen /dev/sda2 lvm
# pvcreate /dev/mapper/lvm
# vgcreate main /dev/mapper/lvm
# lvcreate -L 8GB -n swap main
# lvcreate -l 32GB -n root main
# lvcreate -l 100%FREE -n home main
8GB   swap
32GB root
72GB home

# mkfs.vfat -n BOOT /dev/sda1
# mkfs.ext4 -L root /dev/mapper/main-root
# mkfs.ext4 -L home /dev/mapper/main-home
# mkswap -L swap /dev/mapper/main-swap
# mount /dev/mapper/main-root /mnt
# mkdir /mnt/home
# mkdir /mnt/boot
# mount /dev/mapper/main-home /mnt/home
# mount /dev/sda1 /mnt/boot

# pacstrap /mnt base base-devel syslinux nano linux linux-firmware mkinitcpio lvm2 dhcpcd intel-ucode openssh

# syslinux-install_update -i -a -m -c /mnt
# nano /mnt/boot/syslinux/syslinux.cfg
Change APPEND 
cryptdevice=/dev/sda2:main root=/dev/mapper/main-root rw lang=en locale=en_GB.UTF-8

# swapon -L swap
# genfstab -U -p /mnt >> /mnt/etc/fstab
# nano genfstab /mnt/etc/fstab
add noatime & discaed to options for SSD

# arch-chroot /mnt
# echo LANG=en_GB.UTF-8 > /etc/locale.conf
# nano /etc/locale.gen
# locale-gen
# ln -sf /usr/share/zoneinfo/Africa/Cairo /etc/localtime
# nano /etc/pacman.conf
uncomment [multilib]
# pacman -Sy
# nano /etc/mkinitcpio.conf
add ext4 to MODULES
add encrypt lvm2 to HOOKS

# mkinitcpio -p linux
# passwd
# nano /etc/hostname
mbarch

# nano /etc/hosts
127.0.0.1 localhost
::1 localhost
127.0.1.1 mbarch.localdomain mbarch

# systemctl enable dhcpcd.service
# systemctl enable sshd.service
# bootctl --path=/boot install
# nano /boot/loader/loader.conf
replace code with arch-*

# blkid |grep sda2 |cut -d'"' -f 2
# blkid |grep sda2 |cut -d'"' -f 2 > /boot/loader/entries/arch.conf
# nano /boot/loader/entries/arch.conf
title	Arch Linux
linux	/vmlinuz-linux
initrd /intel-ucode.img
initrd /initramfs-linux.img
options cryptdevice=UUID=****:lvm root=/dev/mapper/main-root quiet rw

# exit
# umount /mnt/{boot,}
# reboot

# pacman -S tmux htop neofetch
# useradd -m anasr
# passwd anasr
# gpasswd -a anasr wheel
# nano /etc/sudoers

# pacman -S mesa git lib32-mesa xf86-video-intel vulkan-intel
# mkdir /home/anasr/git
# cd /home/anasr/git
# git clone https://aur.archlinux.org/auracle-git.git
# cd auracle-git
# makepkg -s
# sudo pacman -U auracle-git-r366.8739929-1-x86_64.pkg.tar.zst
# git clone https://aur.archlinux.org/pacaur.git
# cd pacaur
# makepkg -s
# sudo pacman -U pacaur-4.8.6-1-any.pkg.tar.zst
# sudo pacman -S i3-gaps xorg-xinit xorg-server terminator

# sudo nano /etc/X11/xinit/xinitrc
comment out last four and add exec i3

# git clone https://github.com/loeken/dotfiles
# cd dotfiles
# ./install
# sudo pacman -S feh

# pacaur -S linux-macbook linux-macbook-headers)
