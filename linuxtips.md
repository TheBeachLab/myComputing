# Linux troublehooting

1. [Canon Lide 120](#canon-lide-120)
2. [Old libraries not found](#old-libraries-not-found)
3. [Pacman troubleshooting](#pacman-troubleshooting)
4. [CUPS](#cups)
   1. [Cannot access http://localhost:631](#cannot-access-httplocalhost631)
   2. [Roland does not appear in CUPS](#roland-does-not-appear-in-cups)
5. [Send to Roland Vinyl](#send-to-roland-vinyl)

## Canon Lide 120

Create a file in `/lib/udev/rules.d/` called `60-canonlide120.rules`

Run `lsusb`:

```bash
[irix@x220 ~]$ lsusb
Bus 002 Device 004: ID 04a9:190e Canon, Inc. CanoScan LiDE 120
```

So content of file is

```bash
SUBSYSTEM=="usb", ATTRS{idVendor}=="04a9", ATTRS{idProduct}=="190e", MODE="0666"
```

And then reload the rules

`udevadm control --reload-rules`

FOUND HERE
http://unix.stackexchange.com/questions/44308/understanding-udev-rules-and-permissions-in-libusb#147777/

---
In the supported scanners by sane, there's no Lide 120:  
`cat /etc/sane.d/genesys.conf`

Tutorial config in Arch Linux:  
https://www.eanderalx.org/linux/scanner_lide110

## Old libraries not found

```bash
Error while burning bootloader.
/home/irix/opt/arduino-1.8.1/hardware/tools/avr/bin/avrdude: error while loading shared libraries: libtinfo.so.5: cannot open shared object file: No such file or directory
```

That is because a new library has been installed. Solve it with a symbolic link:

`sudo ln -s /usr/lib/libtinfo.so /usr/lib/libtinfo.so.5`

```bash
[irix@x220 lib]$ ls -l libtin*
lrwxrwxrwx 1 root root 22 May  2 13:37 libtinfo.so -> /usr/lib/libtinfo.so.6
lrwxrwxrwx 1 root root 20 May  2 14:57 libtinfo.so.5 -> /usr/lib/libtinfo.so
lrwxrwxrwx 1 root root 27 May  2 13:37 libtinfo.so.6 -> /usr/lib/libncursesw.so.6.0
```

## Pacman troubleshooting

`sudo pacman -S --force pacman-mirrorlist`

`sudo pacman -Syyu`


## CUPS

### Cannot access http://localhost:631

Connection refused: Make sure CUPS is installed and activated:

```bash
systemctl enable org.cups.cupsd.service
systemctl start org.cups.cupsd.service
```

CUPS web config 403 Forbidden: Add yourself to `wheel` group

### Roland does not appear in CUPS

Roland does not appear

`ls /dev/bus/usb/00x/00x`  where x to be found by lsusb with printer connected and lit up.
Are you on the lp user group?
Adjust udev rules like this , Ensure that is set 0666.

`lpinfo -v`

`/usr/lib/cups/backend/usb`

Create udev rule

```bash
[irix@x220 ~]$ lsusb
Bus 002 Device 014: ID 0b75:0405 Roland DG Corp.

custom udev rule in a file /etc/udev/rules.d/10-usbprinter.rules containing

ATTR{idVendor}=="0b75", ATTR{idProduct}=="0405", MODE:="0660", GROUP:="lp"
```

This is to give permission to the printer

save the file and cat to usb device
`cat filename > /dev/xxx`

If `/usr/lib/cups/backend/usb` shows
Failed to open device, code: -1

Then the power management might be disabling the Device

In `/etc/default/tlp` add the lines
`USB_BLACKLIST="0b75:0405"` and so on with every device

## Send to Roland Vinyl

`cat file.camm > /dev/usb/lp0`

Not working in Arch, says...

`device or resource busy`

might it be because of this?

```bash
[irix@hal ~]$ cat /etc/modprobe.d/blacklistusblp.conf 
blacklist usblp
```
Blacklisting usblp should not be required

It works with:

`lpr -P vinyl`
