# Arch Linux MANUAL Installation (step-by-step)
A fast guide to install archlinux manually (from scratch) ->
based on: https://wiki.archlinux.org/title/installation_guide

### download arch linux from official repo
https://archlinux.org/download/

### (optional but necessary) -> Download PGP key `.sig  file` and check autenticity:
```sh
pacman-key -v archlinux-<version>-.iso.sig
``` 

### create usb media
https://wiki.archlinux.org/title/USB_flash_installation_medium

replace `X` with your sdX usb media `/dev/sdX` 

create with `cat`
```sh
cat archlinux-<version>-.iso > /dev/sdX
````
or create with `dd`
```sh
dd bs=4M if=path/to/archlinux-<version>-.iso of=/dev/sdX conv=fsync oflag=direct status=progress
```

----------------------

## BOOT USB media (mode UEFI)

Now, `its time to boot usb` archlinux media

----------------------


## Commands sequence to proceed with manual installation


### load keyboard layout - brasilian (ABNT2)
```sh
loadkeys br-abnt2
```

### verify archlinux boot mode (UEFI mode)
```sh
ls /sys/firmware/efi/efivars
```
*if apear things in this command then OK! is UEFI mode.

### connect to internet
https://wiki.archlinux.org/title/Network_configuration

Ensure your network interface is listed and enabled, for example:
```sh
ip link
```
For wireless and WWAN, make sure the card is not blocked with rfkill.
    
    Connect to the network:

        1) Ethernet—plug in the cable. (Just plug the cable)

        2) Wi-Fi—authenticate to the wireless network using `iwctl`**. (For Wifi connection)
        
        3) Mobile broadband modem—connect to the mobile network with the mmcli utility.

**See: https://wiki.archlinux.org/title/Iwd#iwctl

Check if internet connection is working:
```sh
ping archlinux.org
``` 

### update system clock
```sh
timedatectl set-ntp true
```

### partition the disks
https://wiki.archlinux.org/title/Fdisk

change `X` with disk letter to be parted

```sh
fdisk /dev/sdX
```

**Remember:** for dual boot it's necessary to `create a EFI partition` to install grub boot manager

```
--> fdisk /dev/the_disk_to_be_partitioned (sdX)
    
	m)  --> help menu
	l)  --> list know partition types
	n)  --> add a new partition
	    for example - select partition number: 19 Linux Swap (create swapfile is preefered)
	    for example - select partition number: 23 Linux root (x86-64)
	    for example - select partition number: 41 Linux home    
    w)  --> write the created partition table on selected disk (sdx)
```
----> Note: create a root / partition and a home /home partion is strongly recommended

### format the partitions
```sh
mkfs.ext4 /dev/root_partition
mkfs.ext4 /dev/home_partition
mkfs.fat -F 32 /dev/efi_system_partition
```

If you created a partition for swap, initialize it with mkswap(8):
```sh
mkswap /dev/swap_partition
```

### Create SwapFile (prefer this)
https://wiki.archlinux.org/title/Swap#Swap_file

Creating swapfile (if you dont create a swap partition)
The performance of a swapfile or a swappartition is not diferent
```sh
dd if=/dev/zero of=/swapfile bs=1M count=512 status=progress
```
Note: Using dd to allocate a swap file is the most portable solution, see swapon(8) Files with holes for details.

Set the right permissions (a world-readable swap file is a huge local vulnerability):
```sh
chmod 600 /swapfile
```

After creating the correctly sized file, format it to swap:
```sh
mkswap /swapfile
```

Activate the swap file:
```sh
swapon /swapfile
```

Finally, edit the fstab configuration to add an entry for the swap file:
```sh
vim /etc/fstab
```
add this:
```sh
/swapfile none swap defaults 0 0
```

### mount the file systems

Mount the root volume to /mnt. For example, if the root volume is /dev/root_partition:
```sh
mount /dev/root_partition /mnt
mount /dev/home_partition /mnt/home
mount /dev/uefi_partition /mnt/boot
```
`
## Instalation

### install some necessary packages for this moment
```sh
pacman -S vim grub efibootmgr os-prober
```

### (otional) -> select yout favorite mirrors -> choose that with small latency for example
```sh
vim /etc/pacman.d/mirrorlist
```

````
[Brazilian Mirrors]
	Server = http://mirror.ufscar.br/archlinux/$repo/os/$arch
	Server = http://archlinux.c3sl.ufpr.br/$repo/os/$arch
	Server = http://www.caco.ic.unicamp.br/archlinux/$repo/os/$arch
	Server = https://www.caco.ic.unicamp.br/archlinux/$repo/os/$arch
	Server = http://linorg.usp.br/archlinux/$repo/os/$arch
	Server = http://pet.inf.ufsc.br/mirrors/archlinux/$repo/os/$arch
	Server = http://archlinux.pop-es.rnp.br/$repo/os/$arch
	Server = http://mirror.ufam.edu.br/archlinux/$repo/os/$arch
	Server = http://br.mirror.archlinux-br.org/$repo/os/$arch
````
### Install essential packages
https://wiki.archlinux.org/title/Kernel


Use the pacstrap script to install the base package, Linux kernel and firmware for common hardwares
```sh
pacstrap /mnt base linux linux-firmware
```
(alternatively) -> zen kernel
```
pacstrap /mnt base linux-zen linux-zen-headers
```

### install grub efi file on `/boot` mounted partition
```sh
grub-install --target=x86_64-efi --efi-directory=/boot --bootloader-id=GRUB
```

### generate `grub.cfg` file
```sh
grub-mkconfig -o /boot/grub/grub.cfg
```

