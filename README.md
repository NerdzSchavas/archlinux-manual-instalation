# Arch Linux MANUAL Installation (step-by-step)
A fast guide to install archlinux manually (from scratch)

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
## boot usb media (mode UEFI)

### load keyboard layout - brasilian (ABNT2)

```sh
loadkeys br-abnt2
```

### install some necessary packages for next steps
```sh
pacman -S vim efibootmgr grub
```

### install grub efi file on `/boot` mounted partition
```sh
grub-install --target=x86_64-efi --efi-directory=/boot --bootloader-id=GRUB
```

### generate `grub.cfg` file
```sh
grub-mkconfig -o /boot/grub/grub.cfg
```

