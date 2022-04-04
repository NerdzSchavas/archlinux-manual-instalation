# Arch Linux MANUAL Installation (step-by-step)
A fast guide to install archlinux manually (from scratch) ->
based in: https://wiki.archlinux.org/title/installation_guide

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

