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
9. [Hardware](#hardware)
   1. [Space Navigator](#space-navigator)
   2. [Contour Shuttle Pro 2](#contour-shuttle-pro-2)
   3. [Wacom Intuos 3](#wacom-intuos-3)
   4. [Trackpad tips](#trackpad-tips)

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

## Hardware

### Space Navigator

For a [free, open source alternative driver](http://spacenav.sourceforge.net/) to the proprietary [3DConnexion](https://www.3dconnexion.com), install the packages `spacenavd` and the config tool `spnavcfg`. Start the mouse with:

`sudo systemctl start spacenavd`

At the time of writing (September 2019) Space Navigator is having trouble with the Appimage version of FreeCAD.

### Contour Shuttle Pro 2

This is a device I use to edit video.

### Wacom Intuos 3

My old A4 tablet for drawing and painting

### Trackpad tips

I use `libinput` driver instead of the former `xf86-input-synaptic`. 

