Arch Linux and Awesome WM Install Notes
===================
So this install covers a fresh Arch Install with 
windows ten duel boot. This is installed using UEFI 
Firmware instead of BIOS firmware. It should work
the same just without an EFI boot partition and 
/mnt/boot mount. 

## Network configuration
```{r, engine='bash', count_lines}
wifi-menu
ping 8.8.8.8
```

## Partitioning disks
### Make Swap and Linux Filesystem
```{r, engine='bash', count_lines}
cfdisk
```

## formating disk partitions
```{r, engine='bash', count_lines}
mkfs.btrfs /dev/sda1
mkswap /dev/sda3
swapon /dev/sda3
```

## Mount the file System
```{r, engine='bash', count_lines}
mount /dev/sda1 /mnt
mkdir /mnt/{boot,home}
mount /dev/sda2 /mnt/boot #sda2 is the EFI partition
```

## Change mirror list

### Top 3 mirrors
| Mirrors               |
| :--------------------:|
| mirrors.unixheads.org |
| mirror.neotuli.net    |
| mirror.rit.edu        |

to change enter command:
```{r, engine='bash', count_lines}
vim /etc/pacman.d/mirrorlist
```

#install archlinux base system
```{r, engine='bash', count_lines}
pacstrap /mnt base
```

# generate fstab
```{r, engine='bash', count_lines}
genfstab -U -p /mnt >> /mnt/etc/fstab
```

# Change to root
```{r, engine='bash', count_lines}
arch-chroot /mnt /bin/bash
```

# Installing Grub
```{r, engine='bash', count_lines}
pacman -S grub
grub-mkconfig -o /boot/grub/grub.cfg
grub-install --recheck /dev/sda
```

# Reboot into installed media
```{r, engine='bash', count_lines}
exit
umount /mnt/home
umount /mnt
reboot
```

# change root password
```{r, engine='bash', count_lines}
passwd
```

# change hostname
```{r, engine='bash', count_lines}
hostnamectl set-hostname archbox
```

# vim change locale
```{r, engine='bash', count_lines}
vim /etc/locale.gen
locale-gen
localectl set-timezone America/Chicago
```

# Enable 64-bit compatability
```{r, engine='bash', count_lines}
vim /etc/pacman.conf # enable multilib
pacman -Syy
```

# setup user
```{r, engine='bash', count_lines}
useradd -m -g users -s /bin/bash abas
passwd abas
pacman -S sudo
visudo # give user sudo privlages
```

# build utilities
```{r, engine='bash', count_lines}
pacman -S multilib-devel fakeroot git jshon wget make pkg-config autoconf automake patch
wget http://aur.archlinux.org/packages/pa/packer/packer.tar.gz
tar zxvf packer.tar.gz
cd packer && makepkg
pacman -U packer<TAB>
```

# installing xorg
```{r, engine='bash', count_lines}
pacman -S xorg xterm xorg-twm xorg-xclock
pacman -S xorg-xinit
```

# installing yaourt and all of it's dependencies
```{r, engine='bash', count_lines}
git clone https://aur.archlinux.org/package-query.git
git clone https://aur.archlinux.org/yaourt.git
cd package-query
makepkg -ci
cd ../yaourt
makepkg -ci
```

# installing slim login manager
```{r, engine='bash', count_lines}
sudo pacman -S slim
sudo systemctl enable slim.service
```

# installing awesome
```{r, engine='bash', count_lines}
sudo pacman -S awesome
```