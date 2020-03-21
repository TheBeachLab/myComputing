# My Computing
 
This is how I do my computing now


<!-- vim-markdown-toc GFM -->

* [The Operating System](#the-operating-system)
* [The display manager](#the-display-manager)
* [The Window Manager](#the-window-manager)
* [Terminal emulator](#terminal-emulator)
* [Cursor theme](#cursor-theme)
* [Arch Linux common daily tasks](#arch-linux-common-daily-tasks)
	* [Time and timezones](#time-and-timezones)
	* [Find all files containing specific text](#find-all-files-containing-specific-text)
	* [Reduce a PDF filesize](#reduce-a-pdf-filesize)
	* [Mount a USB drive](#mount-a-usb-drive)
	* [Encrypt a file or directory with GPG. Paranoid level 1](#encrypt-a-file-or-directory-with-gpg-paranoid-level-1)
	* [Steganography. Paranoid level 2](#steganography-paranoid-level-2)
	* [Create custom commands](#create-custom-commands)
	* [Install a font](#install-a-font)
	* [VNC server](#vnc-server)
		* [Starting a VNC server](#starting-a-vnc-server)
	* [Screen](#screen)
		* [Show the connected displays](#show-the-connected-displays)
		* [Set physical dimensions of your display](#set-physical-dimensions-of-your-display)
		* [Fix overscan problems in HDMI](#fix-overscan-problems-in-hdmi)
	* [Dummy serial and lp ports](#dummy-serial-and-lp-ports)
	* [Polybar](#polybar)

<!-- vim-markdown-toc -->

## The Operating System

I started using Linux distributions in the mid 90's. In the age of Windows 95 -still rocking the floppy disks- I was experimenting with Red Hat Linux and Debian. In 2004 I moved to Mac OS X and it was cool for a while. But then in 2013, I realised I was trapped. I became an Apple Victim. I needed to buy the iPhone to be compatible with the Macbook Pro and also the play music with iTunes and iThis and iThat and iWantedtogetthef@ckoutofthere. So fed up of the lack of freedom, I went back to Linux. I am now using Arch Linux, a rolling release distribution. And I think I will stay like this for a while. Arch Linux is not for beginners though.

## The display manager

I chose a console display manager called [ly](https://github.com/cylgom/ly) with a custom `/etc/ly/config.ini` and a modifyed language file. Somehow modifying `/etc/ly/lang/en.ini` did not have any effect, so I created a copy called `/etc/ly/lang/en2.ini` and called it from the config file. 

Since I did not like the original console font I [modified](https://wiki.archlinux.org/index.php/Linux_console#Fonts) `/etc/vconsole.conf` to use [Lat2-Terminus16](http://alexandre.deverteuil.net/pages/consolefonts/) with 8859-1 font map.

```bash
FONT=Lat2-Terminus16
FONT_MAP=8859-1
```

To use that font from from the early userspace I make sure I use the `consolefont` hook in `/etc/mkinitcpio.conf`. Also load the graphics driver module (in my case intel) earlier in [Early KMS start](https://wiki.archlinux.org/index.php/Kernel_mode_setting#Early_KMS_start) to avoid font changes/flickering/glitches. To apply these changes rebuild with your kernel preset `sudo mkinitcpio -p linux` (check your kernel presets in `ls /etc/mkinitcpio.d`). Then reboot.
w

## The Window Manager

In the past, I used XFCE for a long time with compiz. XFCE is very lightweight and compiz has many cool effects. But maybe biased from my 8-bit age, I use mostly command line tools. For that reason I swapped my window manager to [i3wm](https://i3wm.org/). Actually an i3 fork with [gaps](https://github.com/Airblader/i3).

The program launcher I use is [rofi](https://github.com/DaveDavenport/rofi) with some [themes](https://github.com/davatorium/rofi-themes).

The bar I use in i3 is [polybar](https://github.com/jaagr/polybar)

> TODO For lock screen I have key combo that triggers a [script]() that pixelates the current screen.

I am currently exploring the move to sway <https://swaywm.org/>

## Terminal emulator

I am using uxterm with [Iosevka](https://github.com/be5invis/Iosevka) font. 

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




## Arch Linux common daily tasks

### Time and timezones

In my laptop the hardware clock (BIOS clock) is considered by the operating system (arch) as UTC. So it is important to set that appropiately first.

`sudo hwclock --show`

I travel quite often and I need to adjust the timezone in my computer. I do it with `timedatectl`

`timedatectl status` check the current time, date and timezone. The following will appear:

```bash
[unix ~]$ timedatectl status
               Local time: Fri 2019-11-08 18:42:28 CET
           Universal time: Fri 2019-11-08 17:42:28 UTC
                 RTC time: Fri 2019-11-08 17:42:28
                Time zone: Europe/Madrid (CET, +0100)
System clock synchronized: no
              NTP service: inactive
          RTC in local TZ: no
```

`timedatectl list-timezones` check the timezones
`timedatectl set-timezone zone/subzone` Set the timezone

### Find all files containing specific text

`grep -rnw '/path/to/somewhere/' -e 'pattern'`

- `-r` or `-R` is recursive,
- `-n` is line number
- `-w` stands for match the whole word.
- `-l` (lower-case L) can be added to just give the file name of matching files.

Along with these, `--exclude`, `--include`, `--exclude-dir` flags could be used for efficient searching:

This will only search through those files which have .c or .h extensions:

`grep --include=\*.{c,h} -rnw '/path/to/somewhere/' -e "pattern"`

This will exclude searching all the files ending with .o extension:

`grep --exclude=*.o -rnw '/path/to/somewhere/' -e "pattern"`

For directories it's possible to exclude a particular directory(ies) through --exclude-dir parameter. For example, this will exclude the dirs dir1/, dir2/ and all of them matching *.dst/:

`grep --exclude-dir={dir1,dir2,*.dst} -rnw '/path/to/somewhere/' -e "pattern"`

### Reduce a PDF filesize

With ghostscript `gs -sDEVICE=pdfwrite -dCompatibilityLevel=1.4 -dPDFSETTINGS=/screen -dNOPAUSE -dQUIET -dBATCH -sOutputFile=output.pdf input.pdf`

You can change the `screen` option (72 dpi) to `ebook` (150 dpi), `prepress` (300 dpi), `printer` (300 dpi) and `default`.

### Mount a USB drive

Check the device name with `lsblk` and then use `pmount device [ label ]` and `pumount` to mount/unmount it. If label is given, the mount point will be `/media/label`,
otherwise it will be `/media/device`.

### Encrypt a file or directory with GPG. Paranoid level 1

To **encrypt a file*** `gpg -c filename` outputs `filename.gpg`. To **decrypt a file** `gpg filename.gpg`.

To **encrypt directories** `gpgtar -c -o file.gpg dirname`. To **decrypt a directory** `gpgtar -d file.gpg`

You will then be prompted for a passphrase.

### Steganography. Paranoid level 2

When you need to hide a secret you can encrypt the file. But an adversary will know that something is hidden, and they can attempt to break the code by putting some brute force. But if you embed this encrypted secret into an apparently normal file. The third person would not even be aware of the fact that a seemingly harmless looking image or audio file carries a secret message or a file embedded in it. This type of encryption where you hide one file securely into another is called **Steganography**. And I use it a lot. 

Install `steghide` command line utility. To embed a secret into an image run:

`steghide embed -ef secret.ggp -cf image.jpg`

You will need another passphrase to embed the secret into the image. To extract the secret from the image

`steghide extract -sf image.jpg`

### Create custom commands

I keep my custom commands in a file called `.custom_commands.sh`. This file is sourced by `.bashrc` where I added this line at the bottom `source .custom_commands.sh`. Inside the file `.custom_commands.sh` I create my custom functions:

```
my_function () {
place your code here
}
```

### Install a font

Move it to `~/.local/share/fonts`. More info [here](https://wiki.archlinux.org/index.php/Fonts)

### VNC server

I use [TigerVNC](https://tigervnc.org/) for it's easy use.

#### Starting a VNC server

Place a password/passphrase in `~/.vnc/passwd`

To start `x0vncserver -display :0 -passwordfile .vnc/passwd`

To stop just close the terminal process.

### Screen

#### Show the connected displays

`xrandr`

#### Set physical dimensions of your display

Useful for showing real dimensions at 100% `xrandr --fbmm 310x175`

#### Fix overscan problems in HDMI

This should work `xrandr --output HDMI1 --set underscan on --set "underscan vborder" 25 --set "underscan hborder" 40` but it is not working in my case



### Dummy serial and lp ports

In order to create a dummy serial port for developing purposes install `tty0tty-git` AUR package and load the module:

```bash
sudo depmod
sudo modprobe tty0tty
```

You will see a number of serial ports `/dev/tntx`, make sure you give them permissions 

```bash
sudo chmod 666 /dev/tnt*
```

For testing printers and other devices, just send to `/dev/null`

### Polybar

For displaying siji fonts `xfd -fa siji` check the glyph code and display with the code. For example `printf "\ue101"`





