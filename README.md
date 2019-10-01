# My Computing

This is how I do my computing

1. [The Operating System](#the-operating-system)
2. [The display manager](#the-display-manager)
3. [The Window Manager](#the-window-manager)
4. [Terminal emulator](#terminal-emulator)
5. [Cursor theme](#cursor-theme)
6. [Cool GUI programs](#cool-gui-programs)
   1. [Xournal](#xournal)
   2. [Losless Cut](#losless-cut)
7. [Cool CLI programs](#cool-cli-programs)
   1. [scrot](#scrot)
   2. [Betty, the CLI Siri](#betty-the-cli-siri)
   3. [The Fuck](#the-fuck)
   4. [Check file sizes with `du` `ncdu` and `df`](#check-file-sizes-with-du-ncdu-and-df)
   5. [Monitor the system with `htop` `gtop` and `powertop`](#monitor-the-system-with-htop-gtop-and-powertop)
   6. [iponmap](#iponmap)
   7. [wego](#wego)
   8. [instantmusic](#instantmusic)
   9. [neomutt](#neomutt)
   10. [mapscii](#mapscii)
   11. [midnight commander](#midnight-commander)
   12. [vifm](#vifm)
   13. [asciinema](#asciinema)
   14. [nms](#nms)
   15. [cmatrix](#cmatrix)
   16. [lolcat](#lolcat)
   17. [cowsay](#cowsay)
   18. [ponysay](#ponysay)
   19. [irssi](#irssi)
   20. [testdisk](#testdisk)
   21. [cal](#cal)
8. [Arch Linux common daily tasks](#arch-linux-common-daily-tasks)
   1. [Mount a USB drive](#mount-a-usb-drive)
   2. [Install a font](#install-a-font)
   3. [VNC server](#vnc-server)
      1. [Starting a VNC server](#starting-a-vnc-server)
9. [Network](#network)
   1. [Changing the network interface names](#changing-the-network-interface-names)
   2. [Activating or deactivating network devices](#activating-or-deactivating-network-devices)
   3. [Obtaining DHCP address](#obtaining-dhcp-address)
   4. [Check current UL/DL speed](#check-current-uldl-speed)
10. [Video and YouTube](#video-and-youtube)
   1. [Download youtube video and subtitles](#download-youtube-video-and-subtitles)
   2. [Hardcode subtitles into video](#hardcode-subtitles-into-video)
   3. [Download audio from youtube video](#download-audio-from-youtube-video)
   4. [Convert GIF to MP4](#convert-gif-to-mp4)
11. [Dummy serial and lp ports](#dummy-serial-and-lp-ports)
12. [Hardware](#hardware)
   1. [Asus MB168B+ USB display](#asus-mb168b-usb-display)
   2. [Space Navigator](#space-navigator)
   3. [Contour Shuttle Pro 2](#contour-shuttle-pro-2)
   4. [Wacom Intuos 3](#wacom-intuos-3)
   5. [Canon LiDE 60](#canon-lide-60)
   6. [eGPU Beast 8.5c](#egpu-beast-85c)
      1. [For Radeon HD 6450](#for-radeon-hd-6450)
   7. [Trackpad tips](#trackpad-tips)

![screenshot](img/mysystem.png)

## The Operating System

I started using Linux distributions in the mid 90's. In the age of Windows 95 -still rocking the floppy disks- I was experimenting with Red Hat Linux and Debian. In 2004 I moved to Mac OS X and it was cool for a while. But then in 2013, I realised I was trapped. I became an Apple Victim. I needed to buy the iPhone to be compatible with the Macbook Pro and also the play music with iTunes and iThis and iThat and iWantedtogetthef@ckoutofthere. So fed up of the lack of freedom, I went back to Linux. I am now using Arch Linux, a rolling release distribution. And I think I will stay like this for a while. Arch Linux is not for beginners though.

~~Currently testing [Archlabs](https://archlabslinux.com/) distribution~~ Not anymore since archlabs ceased development.

## The display manager

I chose a console display manager called [ly](https://github.com/cylgom/ly) with a custom `/etc/ly/config.ini` and a modifyed language file. Somehow modifying `/etc/ly/lang/en.ini` did not have any effect, so I created a copy called `/etc/ly/lang/en2.ini` and called it from the config file. 

Since I did not like the original console font I [modifyed](https://wiki.archlinux.org/index.php/Linux_console#Fonts) `/etc/vconsole.conf` to use [Lat2-Terminus16](http://alexandre.deverteuil.net/pages/consolefonts/) with 8859-1 font map.

```bash
FONT=Lat2-Terminus16
FONT_MAP=8859-1
```

To use that font from from the early userspace I make sure I use the `consolefont` hook in `/etc/mkinitcpio.conf`. Also load the graphics driver module (in my case intel) earlier in [Early KMS start](https://wiki.archlinux.org/index.php/Kernel_mode_setting#Early_KMS_start) to avoid font changes/flickering/glitches. To apply these changes rebuild with your kernel preset `sudo mkinitcpio -p linux51` (check your kernel presets in `ls /etc/mkinitcpio.d`). Then reboot.

## The Window Manager

In the past, I used XFCE for a long time with compiz. XFCE is very lightweight and compiz has many cool effects. But maybe biased from my 8-bit age, I use mostly command line tools. For that reason I swapped my window manager to [i3wm](https://i3wm.org/). Actually an i3 fork with [gaps](https://github.com/Airblader/i3).

The program launcher I use is [rofi](https://github.com/DaveDavenport/rofi) with some [themes](https://github.com/davatorium/rofi-themes).

The bar I use in i3 is [polybar](https://github.com/jaagr/polybar)

For lock screen I have key combo that triggers a [script]() that pixelates the current screen.

## Terminal emulator

I am using Urxvt, [custom](https://addy-dclxvi.github.io/post/configuring-urxvt/) themed with [Iosevka](https://github.com/be5invis/Iosevka) font. 

## Cursor theme

I downloaded [Bibata Ice](https://github.com/KaizIqbal/Bibata_Cursor) cursor theme and placed it `/usr/share/icons/` and then call it in `~/.Xresources` 

`Xcursor.theme: Bibata Ice`

But somehow is not loading in i3wm. When I execute `fix_xcursor` it does not get the full name:

```bash
[irix@hal ~]$ fix_xcursor
setting cursortheme "Bibata"
```

Does not matter if I use single or double quotes. The name of the cursor theme is set by `/usr/share/icons/Bibata_Ice/index.theme` file:

```bash
[Icon Theme]
Name=Bibata Ice
Comment=Light Bibata Cursor Theme
Name[ar]=Bibata Ice
Name[bg]=Bibata Ice
...
```
## Cool GUI programs

### Xournal

A simple tool to fill out and annotate PDFs

### Losless Cut

[Lossless Cut](https://github.com/mifi/lossless-cut) has saved me GigaBytes of storage by allowing me to trim videos losslessly. This is a must for video.

![losslesscut](./img/lossless.gif)

## Cool CLI programs

### scrot

`scrot` is a cli tool for taking SCReenshOTs. It has plenty of options.

### Betty, the CLI Siri

I bet you didn't know this one. Start with `Betty what time is it?`

### The Fuck

Have you ever forget to use `sudo`? After a command fails, use `fuck`, then it will fix it for you LOL!

### Check file sizes with `du` `ncdu` and `df`

### Monitor the system with `htop` `gtop` and `powertop`

### iponmap

Mandatory tool for hackers pretending be cool. It will place a dot in a map when you supply an IP address. Try `iponmap 4.4.4.4` 

### wego

An awesome ASCII CLI weather tool for the terminal. You need to register for an API key [here](https://developer.forecast.io/register).
  
### instantmusic

`instantmusic` is a `youtube-dl` variant for downloading music. Just type `instantmusic` and the tool will ask you to enter any detail about the song you want (artist, song name, etc...). It will display a list of options for you to download. The resulting format will be a `mp3` file

### neomutt

The classic mail client `mutt` just supercharged with some extra functionalities.

### mapscii

That is one of these amazing cli tools! Just explore highly detailed maps from the command line.

### midnight commander

A file navigation system for the CLI to easily copy/move/detete files and folders. It's main characteristic is the split screen. Some useful shortcuts to get used to it:

- `CONTROL` + `o` toggle MC and terminal
- `ALT` + `.` toggle hidden files
- `CONTROL` + `u` swap the panels
- `ALT` + `i` bring inactive panel to the same directory as the active panel
- `TAB` jump between panels
- `ALT` + `s` find as you type and select. Next by using `ALT` + `s` again
- `INSERT` or right click toggle select file/folder under the cursor
- `*` invert the selection
- `+` or `-` select or deselect using a pattern
- When copying or moving, the opposite panel is selected as the destination
- `F3` quick view a text file
- `F4` quick edit a text file

### vifm

Another cli file manager

### asciinema

`asciinema` is a tool to record and share terminal sessions.

### nms

Did you watch [Sneakers](https://en.wikipedia.org/wiki/Sneakers_(1992_film)) the movie? You will probably remember this [scene](https://www.youtube-nocookie.com/embed/GS3npSv8iuM).

`nms` is a command that does exactly that! I usually use it in a pipe. Try `ls | nms` and pretend you are a hacker decoding your own disk.

### cmatrix

For those like me who like to pretend they are hackers you have this tool that will show a matrix encoded screen.

### lolcat

`lolcat` is a colourful variant of `cat`. It just displays the file in a full rainbow gradient.

### cowsay

`cowsay` is a funny way to echo messages to the screen. I usually pipe it to `lolcat`

![cowsay and lolcat](img/cowsay.png)

### ponysay

An even cooler alternative to `cowsay` is `ponysay` with it's full colour drawings (do not pipe to `lolcat` or you will mess up the colours!).

### irssi

A great IRC client for the cli. I really miss those IRC days and I use it all the time. For those born in the 80's and later check this [quick start guide](https://irssi.org/documentation/startup/).


### testdisk

`testdisk` is the perfect data recovery tool for the cli. It can undelete files you mistakenly wiped out.

### cal

This simple tool allows you to display a simple calendar with many display options available.

![cal](img/cal.png)

## Arch Linux common daily tasks

### Mount a USB drive

Check the device name with `lsblk` and then use `pmount device [ label ]` and `pumount` to mount/unmount it. If label is given, the mount point will be `/media/label`,
otherwise it will be `/media/device`.

### Install a font

Move it to `~/.local/share/fonts`. More info [here](https://wiki.archlinux.org/index.php/Fonts)

### VNC server

I use [TigerVNC](https://tigervnc.org/) for it's easy use.

#### Starting a VNC server

Place a password/passphrase in `~/.vnc/passwd`

To start `x0vncserver -display :0 -passwordfile .vnc/passwd`

To stop just close the terminal process.

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

### Convert GIF to MP4

`ffmpeg -i animated.gif -movflags faststart -pix_fmt yuv420p -vf "scale=trunc(iw/2)*2:trunc(ih/2)*2" video.mp4`

Explanation

- `movflags` – This option optimizes the structure of the MP4 file so the browser can load it as quickly as possible.
- `pix_fmt` – MP4 videos store pixels in different formats. We include this option to specify a specific format which has maximum compatibility across all browsers.
- `vf` – MP4 videos using H.264 need to have a dimensions that are divisible by 2. This option ensures that’s the case.

## Dummy serial and lp ports

In order to create a dummy serial port for developing purposes install `tty0tty-git` AUR package and load the module:

```bash
sudo depmod
sudo modprobe tty0tty
```

You will see a number of serial ports `/dev/tntx`, make sure you give them permissions `sudo chmod 666 /dev/tnt*`

For testing printers and other devices, just send to `/dev/null`

## Hardware

### Asus MB168B+ USB display

This is a displaylink USB screen I use for video editing and also following tutorials while I run a program. You must install the [displaylink](https://aur.archlinux.org/packages/displaylink/)AUR package that allows configuring displaylink monitors using [Xrandr](https://wiki.archlinux.org/index.php/Xrandr). Then create the file `/usr/share/X11/xorg.conf.d/20-evdidevice.conf` with the following content:

```bash
Section "OutputClass"
	Identifier "DisplayLink"
	MatchDriver "evdi"
	Driver "modesetting"
	Option  "AccelMethod" "none"
EndSection
```

and reboot. My config is:

`xrandr --output DP2 --off --output eDP1 --primary --auto --output DVI-I-1-1 --right-of eDP1 --auto`

### Space Navigator

For a [free, open source alternative driver](http://spacenav.sourceforge.net/) to the proprietary [3DConnexion](https://www.3dconnexion.com), install the packages `spacenavd` and the config tool `spnavcfg`. Start the mouse with:

`sudo systemctl start spacenavd`

Enable the service if you want to run it everytime you boot.

`sudo systemctl enable spacenavd`

At the time of writing (September 2019) Space Navigator is having trouble with the Appimage version of FreeCAD. It works just fine in the AUR compiled version. It just takes a long time to compile.

### Contour Shuttle Pro 2

This is a device I use to edit video. I Installed `contour-shuttle-git` AUR package and now I am missing the key configuration file for Lightworks

https://www.lwks.com/index.php?option=com_kunena&func=view&catid=217&id=83353&Itemid=

https://www.lwks.com/index.php?option=com_kunena&func=view&catid=217&id=116188&Itemid=81

http://freshmeat.sourceforge.net/projects/shuttlepro 

### Wacom Intuos 3

My old tablet for drawing and painting, still works like a charm. I am learning to use it now with grease pencil, the new feature in blender 2.8. You need to install the  `xf86-input-wacom` and reboot. You should now see some devices with the command `xsetwacom list devices`.

There is manual configuration but I prefer to use a graphical tool `kcm-wacomtablet` for that.

### Canon LiDE 60

At the time of writing `sane 1.0.28` has a bug that prevents using the Canon LiDE 60:

`Failed to open device 'genesis:libusb:001:005': Invalid argument.`

Install an old version of sane:

`sudo pacman -U https://archive.archlinux.org/packages/s/sane/sane-1.0.27-2-x86_64.pkg.tar.xz`

And prevent the sane package to be upgraded in `/etc/pacman.conf` until a fix is found.

Install also the frontend `xsane`. Add yourself to the scanner group and reboot `sudo usermod -a -G scanner $USER`

Check that your scanner is listed `scanimage -L`. If you don't want your cameras to be listed comment `v4l` in `/etc/sane.d/dll.conf`.

Try to scan something through xsane or manually using the device name shown in the step above:

`scanimage --device "genesys:libusb:001:004" --format=png > test.png`

### eGPU Beast 8.5c

This is an external GPU adapter that I bought on [Amazon](https://amzn.to/2nruDnH). My version has a expresscard that I use for my Thinkpad X220. Seems to work with the Radeon HD6450. The following command detects the card:

`lspci -k | grep -A 2 -i "VGA"`

But does not detect my Nvidia 8800GT so far. Still researching.

#### For Radeon HD 6450

Install the open source driver `xf86-video-ati`. Install also `radeon-profile-git` AUR package which installs a qt application `radeon-profile` to display info about a Radeon card. 

So far it shows no card. I suspect the Kernel module is not loaded.

### Trackpad tips

I use `libinput` driver instead of the former `xf86-input-synaptic`. Something that I hate of the standars behaviour of the trackpad is the area for middle and right click, because I usually accidentally click the wrong button.
