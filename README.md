# Arch linux setup
## Install Arch
1. Verify UEFI bood mode
<pre>ls /sys/firmware/efi/efivars</pre>
2. Verify internet connection
<pre>ping google.com</pre>
3. Update system clock
<pre>timedatectl set-ntp true</pre>
4. Partition the disks
<pre>gdisk /dev/sdb</pre>
| Name | Code | Size | Number |
|------|------|------|--------|
| EFI System | EF00 | 550M | 1 |
| Linux x86_64 root (/) | 8304 | 32G | 2 |
| Linux swap | 8200 | 8G | 3 |
| Linux /home | 8301 | 150G | 4 |
5. Format the partitions
<pre>mkfs.vfat -F32 /dev/sdb1</pre>
<pre>mkfs.ext4 /dev/sdb2</pre>
<pre>mkswap /dev/sdb3</pre>
<pre>swapon /dev/sdb3</pre>
6. Mount the file systems
<pre>mount /dev/sdb2 /mnt</pre>
<pre>mkdir /mnt/{home,boot}</pre>
<pre>mount /dev/sdb1 /mnt/boot</pre>
<pre>mount /dev/sdc1 /mnt/home</pre>
7. Install the base and base-devel packages
<pre>pacstrap /mnt base base-devel</pre>
8. Generate fstab
<pre>genfstab -U /mnt >> /mnt/etc/fstab</pre>
9. Chroot
<pre>arch-chroot /mnt</pre>
10. Time zone
<pre>ln -sf /usr/share/zoneinfo/Region/City /etc/localtime</pre>
<pre>hwclock --systohc</pre>
11. Localization  
Uncomment in `/etc/locale.gen`  
<pre>locale-gen</pre>  
In /etc/locale.conf` set <pre>LANG=en_US.UTF-8</pre>  

12. Network configuration  
Set `myhostname` in `/etc/hostname`  
Edit `/etc/hosts`
<pre>pacman -S networkmanager</pre>
13. Initramfs
<pre>mkinitcpio -p linux</pre>
14. Root password
<pre>passwd</pre>
15. Boot loader
<pre>pacman -S grub efibootmgr</pre>
<pre>grub-install --target=x86_64-efi --efi-directory=/boot --bootloader-id=GRUB</pre>
<pre>pacman -S intel-ucode vim</pre>
<pre>grub-mkconfig -o /boot/grub/grub.cfg</pre>
16. DHCPC
<pre>systemctl enable dhcpcd</pre>
<pre>systemctl enable NetworkManager</pre>
17. Unmount
<pre>
exit
umount -R /mnt</pre>
18. Reboot  
`reboot`  
19. Useradd
<pre>useradd -g users -G wheel -s /bin/bash USER
passwd USER
visudo #Unconmment %wheel ALL=(ALL) ALL</pre>
20. Audio/Graphics
<pre>sudo pacman -S pulseaudio pulseaudio-alsa</pre>
<pre>sudo pacman -S nvidia nvidia-utils nvidia-settings</pre>
<pre>sudo pacman -S gnome gnome-extra#use only needed</pre>
21. Additional settings
<pre>sudo pacman -S icedtea-web jre8-openjdk ufw gimp libreoffice-fresh vlc redshift bash-completion git</pre>
<pre>sudo ufw enable
sudo ufw allow ssh
sudo ufw status verbose
sudo systemctl enable ufw.service</pre>
<pre>sudo pacman -Rns $(pacman -Qtdq)</pre>
<pre>sudo systemctl --failed
sudo journalctl -p 3 -xb</pre>
<pre>sudo rsync -aAXvP --delete --exclude=/dev/* --exclude=/proc/* --exclude=/sys/* --exclude=/tmp/* --exclude=/run/* --exclude=/mnt/* --exclude=/media/* --exclude=/lost+found --exclude=/home/.ecryptfs / /mnt/backupDestination/</pre>
<pre>https://youtu.be/giAb4Ckh8BQ</pre>
<pre>sudo pacman -S --needed base-devel</pre>
22. AUR
<pre>Clone aurman from https://aur.archlinux.org/aurman.git
cd aurman && makepkg -si
#if gpg key error
gpg --recv-keys `KEY`</pre>
