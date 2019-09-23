# Linux troublehooting

1. [Canon Lide 120](#canon-lide-120)
2. [Old libraries not found](#old-libraries-not-found)
3. [Pacman troubleshooting](#pacman-troubleshooting)
4. [CUPS](#cups)
   1. [Cannot access http://localhost:631](#cannot-access-httplocalhost631)
   2. [Roland does not appear in CUPS](#roland-does-not-appear-in-cups)
5. [Send to Roland Vinyl](#send-to-roland-vinyl)
6. [Video and YouTube](#video-and-youtube)
   1. [Download youtube video and subtitles](#download-youtube-video-and-subtitles)
   2. [Hardcode subtitles into video](#hardcode-subtitles-into-video)
   3. [Download audio from youtube video](#download-audio-from-youtube-video)
7. [Network](#network)
   1. [Changing the network interface names](#changing-the-network-interface-names)
   2. [Activating or deactivating network devices](#activating-or-deactivating-network-devices)
   3. [Obtaining DHCP address](#obtaining-dhcp-address)
   4. [Check current UL/DL speed](#check-current-uldl-speed)
8. [Dummy serial and lp ports](#dummy-serial-and-lp-ports)

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

## Video and YouTube

### Download youtube video and subtitles

`youtube-dl --write-auto-sub` URL-VIDEO

### Hardcode subtitles into video

`ffmpeg -i` VIDEO-FILE `-vf subtitles=`SUBS-FILE OUTPUT-FILE

### Download audio from youtube video

First check the available formats

`youtube-dl -F GKgfCthuiV0` where GKgfCthuiV0 is the youtube code of the video

```
[youtube] GKgfCthuiV0: Downloading webpage
[youtube] GKgfCthuiV0: Downloading video info webpage
[info] Available formats for GKgfCthuiV0:
format code  extension  resolution note
249          webm       audio only tiny   69k , opus @ 50k (48000Hz), 3.38MiB
250          webm       audio only tiny   87k , opus @ 70k (48000Hz), 4.29MiB
140          m4a        audio only tiny  130k , m4a_dash container, mp4a.40.2@128k (44100Hz), 6.95MiB
251          webm       audio only tiny  157k , opus @160k (48000Hz), 7.91MiB
160          mp4        192x144    144p   13k , avc1.4d400b, 25fps, video only, 436.59KiB
133          mp4        320x240    240p   18k , avc1.4d400d, 25fps, video only, 528.49KiB
278          webm       192x144    144p   27k , webm container, vp9, 25fps, video only, 755.36KiB
394          mp4        192x144    144p   30k , av01.0.00M.08, 25fps, video only, 892.15KiB
395          mp4        320x240    240p   47k , av01.0.00M.08, 25fps, video only, 1.17MiB
242          webm       320x240    240p   48k , vp9, 25fps, video only, 1.13MiB
18           mp4        400x300    240p  147k , avc1.42001E, mp4a.40.2@ 96k (44100Hz), 7.90MiB (best)
```

Select the one you want, in my case 251, which is the highest quality

`youtube-dl -f 251 GKgfCthuiV0`

Now transcode the webm file into wav (or any other) format you want

`ffmpeg -i file.webm file.wav`

## Network

### Changing the network interface names

Add/edit a file in `/etc/udev/rules.d/10-network.rules` and reboot

```
SUBSYSTEM=="net", ACTION=="add", ATTR{address}=="c8:5b:76:e5:fc:23", NAME="cable0"
SUBSYSTEM=="net", ACTION=="add", ATTR{address}=="00:28:f8:2b:12:18", NAME="wifi0"
```

### Activating or deactivating network devices

Activate ethernet: `sudo ip link set cable0 up`

Deactivate wifi: `sudo ip link set wifi0 down`

### Obtaining DHCP address

`dhcpcd cable0`

### Check current UL/DL speed

`vnstat --live -i wifi0`

## Dummy serial and lp ports

In order to create a dummy serial port for developing purposes install `tty0tty-git` AUR package and load the module:

```bash
sudo depmod
sudo modprobe tty0tty
```

You will see a number of serial ports `/dev/tntx`, make sure you give them permissions `sudo chmod 666 /dev/tnt*`

For testing printers and other devices, just send to `/dev/null`


