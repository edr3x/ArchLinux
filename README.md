# Arch Linux

## this only covers after you inserted your bootabe pendirve and only covers command you need to enter

- It is recommended to use ethernet cable for internet for this setup

```sh
setfont ter-132n
```

- Check if the internet is working

```sh
ping archlinux.org -c 5
```

- Verify the efi boot

```sh
ls /sys/firmware/efi/efivars/
```

- Set time and date

```sh
timedatectl set-timezone Asia/Kathmandu
```

- Set up keyboard layout

```sh
loadkeys /usr/share/kbd/keymaps/i386/qwerty/us.map.gz
```

- List out all of the drive connected currently

```sh
lsblk
```

- To see all the partitions of the disk

```sh
cfdisk /dev/sda
```

- We need to create three main partitions
    - root (Linux filesystem) /
    - home (Linux filesystem) /home
    - swap (Linux swap)

- Now we format these partition one by one

```sh
# root partition

mkfs.ext4 /dev/sda4   # here sda4 is the partition of root

# home

mkfs.ext4 /dev/sda5

# swap

mkswap /dev/sda6
```

- Now enable the swap partition by

```sh
swapon /dev/sda6
```

- Mount root partition

```sh
mount /dev/sda4 /mnt
```

- create a directory inside `/mnt`

```sh
mkdir /mnt/home
```

- Now mount the home partition

```sh
mount /dev/sda5 /mnt/home
```

- Verify if the mount is successful by doing

```sh
lsblk
```

- Backup the current mirrorlist

```sh
cp /etc/pacman.d/mirrorlist /etc/pacman.d/mirrorlist.bak
```

- Update the repo

```sh
pacman -Sy
```

```sh
pacman -S pacman-contrib
```

```sh
rankmirrors -n 10 /etc/pacman.d/mirrorlist.bak > /etc/pacman.d/mirrorlist
```

- mount arch linux to root partition

```sh
pacstrap -i /mnt base base-devel linux linux-zen linux-headers linux-firmware intel-ucode sudo nano neovim git neofetch networkmanager dhcpcd pulseaudio bluez
```

```sh
genfstab -U /mnt >> /mnt/etc/fstab 
```

```sh
cat /mnt/etc/fstab
```

## Enter into the drive

```sh
arch-chroot /mnt
```
```sh
lsblk  # to verify if you are on disk
```

- Set the superuser password

```sh
passwd
```
> Enter the password for superuser

- Create a standard user

```sh
useradd -m <username> # enter your username here
```

```sh
passwd <username> # Set password for your username
```

```sh
usermod -aG wheel,storage,power <username>
```

```sh
visudo
```

- Edit the sudoers file and uncomment the lines

`%wheel ALL=(ALL) ALL`

and add the line bellow this one

`Defaults timestamp_timeout=20`

> Now save and exit the file

- Set the language

```
nvim /etc/locale.gen
```

uncomment the line

`en_US.UTF-8 UTF-8`

- Generate a locale

```
locale-gen
```

- Create a locale config file

```sh
echo LANG=en_US.UTF-8 > etc/locale.conf
```

- export the system language by

```sh
export LANG=en_US.UTF-8
```

- Set hostname for computer

```sh
echo PROVIDENCE > /etc/hostname
```
```sh
cat /etc/hostname
```
> There should be a file named `PROVIDENCE`

```sh
nvim /etc/hosts
```
and add the following

```
127.0.0.1       localhost
::1             localhost
127.0.1.1       PROVIDENCE.localdomain          localhost
```
> Save and exit

- Sync the local time

```sh
ln -sf /usr/share/zoneinfo/Asia/Kathmandu /etc/localtime
```

```sh
hwclock --systohc
```

## Grub install

```
mkdir /boot/efi
```

- mount the efi partition to this directory

```sh
mount /dev/sda1 /boot/efi/
```

```sh
pacman -S grub efibootmgr dosfstools mtools
```

- edit the grub file

```sh
nvim /etc/default/grub
```
>  go to bottom and uncomment

`GRUB_DISABLE_OS_PROBER=false`

- install os-prober

```sh
pacman -S os-prober
```

- install grub

```sh
grub-install --target=x86_64-efi --bootloader-id=grub_uefi --recheck
```

- generate a grub config file

```sh
grub-mkconfig -o /boot/grub/grub.cfg
```

- enable network services

```sh
systemctl enable dhcpcd.service
```

```sh
systemctl enable NetworkManager.service
```

```sh
exit
```

```sh
umount -lR /mnt
```

```sh
reboot
```

> Eject the pendrive

### Now you will be greeted by tty

- enter your username and password to login

```sh
sudo pacman -Syy
```

install the window manager

```sh
sudo pacman -S xorg xorg-xinit
```

- now install paru for aur and to install our remaining packages

```sh
sudo pacman -S --needed base-devel
git clone https://aur.archlinux.org/paru.git
cd paru
makepkg -si
```

after this do 

```sh
paru -S awesome-git ly-git
```

- do the following

```sh
sudo pacman -S alacritty fish picom btop rofi starship tmux stow git-delta stow
```
