# Simple-ArchLinux-Install-Guide

ArchLinux Installation From Scratch UEFI,GUI,Steam,VLC,Libre Office,OBS-STUDIO,flatpak it's a process,but the results are worth it.You will get a fully customizable system where you can make everything work exactly how you want it,sky is the limit!

# 0. Downloading the installation image and creating a bootable device using rufus:

* Download image here via torrent or direct link:https://www.archlinux.org/download/
* Use Rufus http://rufus.ie/ and select GPT and ISO mode for the image

# 1.Creating partitions using cfdisk:

# Run this command and delete everything,from free space create the following:

* cfdisk 
* /dev/sdx1 512M  Fat32(EFI)

# NB!The size of the EFI must be 512MB and type should be EFI

# Swap patition size can be around 3-10GB 

* /dev/sdx2 8-10GB swap

# Main partition,remaining size can be as large as you want it to be: 

* /dev/sdx3 200GB ext 4

# 2.Formatting the drives:

# For the /dev/sdx1 use the following command:

* mkfs.fat -F32 /dev/sdx1

# For the swap /dev/sdx2 use the following commands:

*  mkswap /dev/sdX2
*  swapon /dev/sdX2

# For the primary partition use the following command:

* mkfs.ext4 /dev/sdX3

# 3.Mounting the drives:

* mount /dev/sdX3 /mnt

# 4 Checking if everything worked:

* lsblk
* ip link
# If you have wi-fi:
* wifi-menu

# 5.Installing basic packages and stuff:

* pacstrap /mnt base base-devel linux linux-firmware nano vim dhcpcd networkmanager netctl iw wpa_supplicant dialog 

# 6. Generating fstab file:

* genfstab -U /mnt >> /mnt/etc/fstab
# 7.Switching to root.All the below commands must be used as root!

* arch-chroot /mnt /bin/bash

# 8.Checking network connection:

* ip link

# 9.Setting date,region and time,use these commands:

* timedatectl set-ntp true
* timedatectl list-timezones
* timedatectl set-timezone Zone/SubZone
* ln -sf /usr/share/zoneinfo/Region/City /etc/localtime
* hwclock --systohc

# 10.Setting the hostname

* hostnamectl set-hostname myhostname

# 11.Setting locales:

* localectl set-locale LANG=en_US.UTF-8
* locale-gen

# For specific settings you can also use the uncomment the required locales method by editing the locale file itself

* nano /etc/locale.gen
* locale-gen
# To change or add settings to the generated locale file 
# As root:

* nano /etc/locale.conf

# As user:
* sudo nano /etc/locale.conf

# 12.Adding a main user:

* useradd -g users -G power,storage,wheel -m test

# 13.Assigning passwords for root and main user

# Password for root:

* passwd 

# Password for main user:

* passwd your_new_user

# 14.Downloading the text editors(in case you did'not add them already):

* pacman -S nano
* pacman -S vim

# 15.Installing sudo (in case it's not installed already):

* pacman -S sudo

# 16.Editing the sudoers file:

* visudo

# Uncomment the needed settings in sudoers file and add the main user into it:

# Lines safe to uncomment :ALL= ALL(ALL) ALL

* :w! + Enter to exit and write changes
* :q + Enter exit

# 17.Grub and efi tools installation(very important step!):

* pacman -S grub efibootmgr dosfstools os-prober mtools fuse2 

# 18. Creating efi boot directory on the EFI partition:

* mkdir /boot/efi

# 19. Mounting the FAT32 EFI partition:

* mount /dev/sdx1 /boot/efi  #Mount FAT32 EFI partition 

# 20. Grub installation and configuration(very important,otherwise system will not boot!): 

* grub-install --target=x86_64-efi   --bootloader-id=grub --efi-directory=/boot/efi 
* grub-mkconfig -o /boot/grub/grub.cfg

# 21.Additional security measures so that the boot does not become unbootable:

* mkdir /boot/efi/EFI/BOOT
* cp /boot/efi/EFI/GRUB/grubx64.efi /boot/efi/EFI/BOOT/BOOTX64.EFI

# 22.Creating and editing the efi boot file so that system becomes bootable:

* nano /boot/efi/startup.nsh
* bcf boot add 1 fs0:\EFI\GRUB\grubx64.efi “My grub bootloader”
* exit
# 23.Exiting root and rebboting:

* exit

# 24.Unmounting the live partitions and rebooting:

* umount -R /mnt
* reboot

# 25.First steps after reboot under root or using sudo:
* sudo systemctl enable dhcpcd.service
* sudo nano /etc/systemd/network/enp0s3.network

or

* sudo vi /etc/systemd/network/enp0s3.network

# Be sure that the below lines are entered into the file:

* [Match]
* name=en*
* [Network]
* DHCP=yes

# For Wi-fi(you will need networkmanager and netctl installed via pacstrap)
* wifi-menu
* sudo systemctl enable NetworkManager.service

# 26.Run these commands before the next reboot:

* sudo systemctl restart systemd-networkd
* sudo systemctl enable systemd-networkd

# 27.Reboot again:

* sudo reboot

# 28.Install networkmanager(if not installed):

* sudo pacman -S networkmanager

# 29. Install intel firmware it is important:

* sudo pacman -S intel-ucode


# 30. Install Xorg: 

* sudo pacman -S xorg xorg-server xorg-xinit xterm xorg-xrandr

# Optional for noveau drivers 

* xf86-video-vesa mesa

# 31. Install alsa audio drivers and utilities:

* sudo pacman -S alsa
* sudo pacman -S alsa-utils
* sudo pacman -S pulseaudio
# 32. Check if alsamixer is working:

* alsamixer

# 33.(Optional) Install the terminal:

* sudo pacman -S lxterminal

# 34. Install Deepin/Gnome/XFCE/KDE/Cinnamon/LXDE/LXQt desktop environments(choose one): 

* sudo mkdir home
* cp /etc/X11/xinit/xinitrc ~/.xinitrc
# Xfce

* sudo pacman -S xfsce4 xfce4-goodies
* "exec startxfce4" > ~/.xinitrc

# Deepin:

* sudo pacman -S deepin deepin-extra
* "exec startdde" > ~/.xinitrc

# Gnome

* sudo pacman -S gnome gnome-extra
* "exec gnome-session" > ~/.xinitrc

# KDE

* sudo pacman -S plasma kde-applications
* "exec startplasma-x11" > ~/.xinitrc

# Cinnamon:

* sudo pacman -S cinnamon
* "exec cinnamon-session" > ~/.xinitrc

# MATE

* sudo pacman -S mate mate-extra
* "exec mate-session" > ~/.xinitrc

# LXDE

* sudo pacman -S lxde
* "exec startlxde" > ~/.xinitrc

# LXQt

* sudo pacman -S lxqt
* "exec startlxqt" > ~/.xinitrc

# Budgie

# NB! Also requires gnome gtk installed before that!

* sudo pacman -S budgie-desktop
* export XDG_CURRENT_DESKTOP=Budgie:GNOME
* "exec budgie-desktop" > ~/.xinitrc

# NB!:Removing anything can be done by:

* sudo pacman -Rscn application

# 35. Enable the GUI desktop to start at launch: 

# In case lightdm is missing: 

* pacman -S lightdm
* sudo systemctl enable lightdm.service
* sudo systemctl start lightdm.service

# In case gdm is missing:

* sudo pacman -S gdm
* sudo systemctl enable gdm.service
* sudo systemctl start  gdm.service

# For XFCE and KDE install sddm:

* sudo pacman -S sddm
* sudo systemctl enable sddm
* sudo systemctl start sddm

# For LXDE (optional):

* sudo pacman -S lxdm
* sudo systemctl enable lxdm
* sudo systemctl start  lxdm

# For XFCE you might want to enable autologin in the conf file:

* sudo nano /etc/sddm.conf.d/autologin.conf

* [Autologin]
* User=test
* Session=default

# 36. Edit the pacman conf file to enable mirror list so we can enable multilib(same as multiarch) for Steam and proprietary drivers: 

* sudo nano /etc/pacman.conf

# Uncomment these lines:

* [multilib]
* Include = /etc/pacman.d/mirrorlist

# 37. Update pacman libraries and system:

* sudo pacman -Syu

# 38. Install NVIDIA or AMD proprietary drivers and utilities last!:

* sudo pacman -S nvidia
* sudo pacman -S nvidia-utils
* sudo pacman -S lib32-nvidia-utils
* sudo pacman -S nvidia-settings
* sudo reboot

# 39. Install Steam 

* sudo pacman -S steam

# 40. Install VLC and other stuff

* sudo pacman -S vlc
* sudo pacman -S libreoffice-fresh
* sudo pacman -S chromium
* sudo pacman -S obs-studio
* sudo pacman -S firefox
* sudo pacman -S flatpak

# 41. Update the whole system using:

* sudo pacman -Syu

# 42.(Optional) Clear terminal by using:

* clear

# 43.(Optional) Show system status:

* sudo systemctl status

That's it you are good for using pure ArchLinux!Enjoy!

Thank you!

#gimalaji_blake
