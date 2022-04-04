# archlinux-manual-instalation
A fast guide to install archlinux manually

### create usb media

## Boot usb media (mode UEFI)

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

