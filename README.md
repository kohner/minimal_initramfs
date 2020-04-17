

### ABOUT THIS HOOK ###

Some devices require udev, if you don't want to load the whole udev hook consider using this hook instead.
It does not support LVM or mdadm by default.

Learn more about udev first: https://wiki.archlinux.org/index.php/Minimal_initramfs#Udev_requirement

This hook was created based on suggestions from https://unix.stackexchange.com/questions/352381/how-to-boot-into-root-btrfs-file-system-with-minimal-initramfs-without-udev-hook/352932, the original hook can be found in ./without_udev_original.

There are two no_udev files in hooks and install folder one goes to /usr/lib/initcpio/install the other to /usr/lib/initcpio/hooks.

Your mkinitcpio.conf in etc may look like this:

MODULES="ext4 crc32c_generic mmc_block serio_raw atkbd xhci_pci crc32c_intel i8042 sdhci_acpi"                  # For MODULES and BINARIES see guide below
BINARIES="fsck fsck.ext4 e2fsck"
HOOKS="base no_udev"

In order to make the hook work you need to edit the file in hooks change def="" to your device name NOT YOUR PARTITION only /dev/sda not /dev/sda1.

This hook is not supposed to work on systems where device names change frequently, the device name is literally hard coded into the initramfs.


If things don't work try the following

- increase sleep values in hooks/no_udev
- try the original script it should work
- use udev and see if it works




__More advice regarding fast boot__
### CONFIGURING MKINITCPIO ###

Consider using preset file in /etc/mkinitcpio.d/ it might be unwise to change your global mkinitcpio settings.

You can write a new config file for ex. /etc/mkinitcpio_minimal.conf and add the following to .preset file

- add 'minimal' in PRESETS= better leave default and fallback there
- minimal_config="/etc/mkinitcpio_minimal.conf"
- minimal_image="/boot/initramfs-linux-lts-minimal.img"

Now just don't forget to add the minimal boot entry to your bootloader for syslinux this would be something like

LABEL archminimal
    MENU LABEL Arch Linux Minimal
    LINUX ../vmlinuz-linux-lts
    APPEND root=/dev/sda1 rw
    INITRD ../initramfs-linux-lts-minimal.img

Note that I am using the LTS kernel if using latest kernel change the examples accordingly.



Finding out necessary modules:

If you want to remove hooks from HOOKS= you need to load to make initramfs load proper modules instead.
READ THIS: https://wiki.archlinux.org/index.php/Minimal_initramfs#Finding_needed_modules
Add your necessary modules to MODULES in your mkinitcpio config for the minimal initramfs config and remove hooks.

About BINARIES:
You probably want to have only fsck here, find BINARIES in https://wiki.archlinux.org/index.php/Minimal_initramfs and hack accordingly.



### SYSLINUX ###

Add kernel parameter quiet.

You can remove everything from /boot/syslinux except (syslinux 6.04)

ldlinux.c32
ldlinux.sys
libcom32.c32
libgpl.c32
liblua.c32
libmenu.c32
libutil.c32
syslinux.cfg
