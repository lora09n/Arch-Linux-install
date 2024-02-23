first install openssh to connect in windows cmd.
make sure you have selected briged network instead nat in virtual box

```sh
pacman -Sy


```


```sh
pacman -S openssh


```
> restart openssh server after install
```sh
sudo systemctl restart sshd
```
> passwd [remember the password to connect on windows cmd]
> to connect:

```sh
 ssh root@ip
```
> if you get this while trying to connect ssh:
[
IT IS POSSIBLE THAT SOMEONE IS DOING SOMETHING NASTY!
Someone could be eavesdropping on you right now (man-in-the-middle attack)!
It is also possible that a host key has just been changed.
The fingerprint for the ED25519 key sent by the remote host is
SHA256:lVSVwYb2k6jOtlOSke0d/AbW02EElF7UhJlYzJEM8No.
Please contact your system administrator.
Add correct host key in C:\\Users\\u/.ssh/known_hosts to get rid of this message.
Offending ECDSA key in C:\\Users\\u/.ssh/known_hosts:3
Host key for 192.168.7.9 has changed and you have requested strict checking.
Host key verification failed.
]
try this:
> on your windows cmd


```sh
 ssh-keygen -R localhost(ip)
```


```sh
 ssh-keyscan -H localhost(ip) >> C:\Users\u\.ssh\known_hosts
```



# Check for UEFI mode


```sh
 ls /sys/firmware/efi/efivars
```

> If the directory is present, it means you're booted in UEFI mode.

> Partitioning the disk (use 'gdisk' or 'cfdisk' for GPT partitioning)


```sh
cfdisk /dev/sda
```

> Create EFI partition (size 512M, type EF00)
> Create root partition (remaining space, type 8300)

# Format partitions

```sh
 shmkfs.fat -F32 /dev/sda1
```


```sh
 mkfs.ext4 /dev/sda2
```
# Mount the partitions


```sh
mount /dev/sda2 /mnt
```


```sh
mkdir /mnt/boot
```


```sh 
mount /dev/sda1 /mnt/boot
```

# Install base system and XFCE desktop environment


```sh 
pacstrap /mnt base linux linux-firmware xfce4 xfce4-goodies
```

# Generate fstab


```sh
genfstab -U /mnt >> /mnt/etc/fstab
```

# Change root into the new system


```sh
arch-chroot /mnt
```

# Set timezone


```sh
ln -sf /usr/share/zoneinfo/Asia/Tokyo /etc/localtime
```


```sh
hwclock --systohc
```

# Set the system locale


```sh
echo "en_US.UTF-8 UTF-8" >> /etc/locale.gen
```


```sh
locale-gen
```


```sh
echo "LANG=en_US.UTF-8" > /etc/locale.conf
```


# Set hostname


```sh 
echo "seniority" > /etc/hostname
```

# Configure hosts file


```sh
echo "127.0.0.1 localhost" >> /etc/hosts
```


```sh
echo "::1 localhost" >> /etc/hosts
```


```sh
echo "127.0.1.1 seniority.localdomain seniority" >> /etc/hosts
```



# Install and configure bootloader (GRUB)


```sh
pacman -S grub efibootmgr
```


```sh
grub-install --target=x86_64-efi --efi-directory=/boot --bootloader-id=GRUB
```


```sh
grub-mkconfig -o /boot/grub/grub.cfg
```


# Requierd Program and tools


```sh
pacman -S firefox gnome-terminal vim nano vlc ffmpeg base-devel git gimp htop neofetch gparted zip unzip networkmanager wireless_tools wpa_supplicant dialog 
```
```sh
sudo pacman -S sudo
```
> Once the installation is complete, you may need to configure sudo. By default, members of the wheel group are granted sudo privileges. You can add your user account to the wheel group if it's not already a member:
> and (if dont add user you wont be able to login after installition finished)

```sh
useradd -m seniority
```


```sh
passwd seniority
```
> for ex:1 and 1 again


```sh
usermod -aG wheel username
```


> Edit the sudoers file to allow members of the wheel group to execute commands as root. You can use the visudo command to safely edit the sudoers file:
```sh

sudo visudo
```
> Within the sudoers file, look for the line that says:


```sh
# %wheel ALL=(ALL) ALL
```
> Uncomment this line by removing the # at the beginning:



```sh
%wheel ALL=(ALL) ALL
```
> Save and exit the editor (usually Ctrl + X, then Y to confirm, and Enter to save).

> You should now be able to use sudo to execute commands with root privileges.

# Exit chroot


```sh
exit
```

# Unmount partitions


```sh
umount -R /mnt
```

# Reboot


```sh
reboot
```
