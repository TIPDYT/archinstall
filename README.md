# Arch base install

Set Keyboard layout to Swiss douring installation
```bash
loadkeys de_CH-latin1
```

Connect to Wlan
```bash
iwctl
station wlan0 connect <wifiname>
#check connection
station list
```

Syncroneze Time
```bash
timedatectl set-ntp true
```

Set best Mirrorlist for location
```bash
# Sync Pacman
pacman -Syyy

# Install reflector for setting mirrorlist
pacman -S reflector

# Set Mirrorlist
reflector -c Switzerland -a 6 --sort rate --save /etc/pacman.d/mirorlist
```

Formatting Harddrive with `fdisk`
```bash
fdisk /dev/nvme0n1
```
`g` - create gpt partition for uefi bios

`n` - create new uefi partition with +500M and type `1`.

`n` - create a root partition with all remaining disk space.

Make a file system for each partition
```bash
mkfs.fat -F32 /dev/nvme0n1p1
mkfs.ext4 /dev/nvme0n1p2
```

Mounting partitions
```bash
mount /dev/nvme0n1p2 /mnt
mkdir /mnt/boot
mount /dev/nvme0n1p1 /mnt/boot
```

Start installing base system with the following command and packages
```bash
pacstrap /mnt base linux linux-firmware nano
```

Generate `fstab` for the partitions using the `-U` for UUID of partitions
```bash
genfstab -U /mnt >> /mnt/etc/fstab
```

Change Installation directory (on drive)
```bash
arch-chroot /mnt
```

Create a swap-file
```bash
fallocate -l 2GB /swapfile
chmod 600 /swapfile
mkswap /swapfile
swapon /swapfile
```

Add swapfile to `fstab`
```bash
# Edit `fstab`
nano /etc/fstab
# Insert at the end
/swapfile none  swap  defaults  0 0
```

Create a Symlink for timezone
```bash
ln -sf /usr/share/zoneinfo/Europe/Zurich /etc/localetime
```

Synchronize Hardware-Clock
```bash
hwclock --systohc
```

Edit and generate locales
```bash
# Edit locale.gen
nano /etc/lolace.gen
# Uncomment, save and exit
en_US.UTF-8

# Generate the locale
locale-gen
```

Set `locale`
```bash
# Edit file
nano /etc/locale.conf
# Insert, save and exit
LANG=en_US.UTF-8
```

Set Keyboard layout to Swiss for persistance.
```bahs
# Edit
/etc/vconsole.conf
# Insert and save file
KEYMAP=de_CH-latin1
```

Set Hostname
```bash
nano /etc/hostname
# Set Hostname, save and exit
x2100
```

Set Hosts
```
nano /etc/hosts
# Set hosts, save and exit
127.0.0.1   localhost
::1         localhost
127.0.1.1   x2100.localdomain   x2100
```

Set `root` password
```bash
passwd
```

Install remaining base packages
```bash
pacman -S grub efibootmgr networkmanager network-manager-applet dialog wireless_tools wpa_supplicant os-prober base-devel linux-headers reflector bluez bluez-utils git cups xdg-utils xdg-user-dirs
```

Install grub-bootloader
```bash
grub-install --target=x86_64-efi --efi-directory=/boot --bootloader-id=GRUB
# Create grub bootloader config
grub-mkconfig -o /boot/grub/grub.cfg
```

Enable Services on boot
```bash
systemctl enable NetworkManager
systemctl enable bluetooth
systemctl enable cups.service
```

Create User
```bash
useradd -mG wheel fr
passwd fr
```

Add user to sudoers
```bash
EDITOR=nano visudo
# Uncomment
%whell ALL=(ALL) ALL
```

Exit, Unmount and reboot system
```bash
exit
umount -a
reboot
```

After reboot, login into system and connect to wifi
```bash
nmtui
```

install intel grafics driver
```bash
sudo pacman -S xf86-vido-intel
```

Install Displayserver
```bash
sudo pacman -S xorg
```

Install DE (gnome)
```bash
sudo pacman -S gnome gnome-tweaks
```

Enable `gdm` login screen manager
```bash
sudo systemctl enable gdm
```

Install and enable on boot `intel-ucode` Intel Microcode
```bash
sudo pacman -S intel-ucode
grub-mkconfig -o /boot/grub/grub.cfg
```

Congrats, you have managed to Install sucessfully Arch with Gnome!