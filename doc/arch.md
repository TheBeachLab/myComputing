# Arch Linux Operating System

I started using Linux distributions in the mid 90's. In the age of Windows 95 -still rocking the floppy disks- I was experimenting with Red Hat Linux and Debian. In 2004 I moved to Mac OS X and it was cool for a while. But then in 2013, I realised I was trapped. I became an Apple Victim. I needed to buy the iPhone to be compatible with the Macbook Pro and also the play music with iTunes and iThis and iThat and iWantedtogetthef@ckoutofthere. So fed up of the lack of freedom, I went back to Linux. I am now using Arch Linux, a rolling release distribution. And I think I will stay like this for a while. Arch Linux is not for beginners though.

<!-- vim-markdown-toc GFM -->

* [The display manager](#the-display-manager)
* [The Window Manager](#the-window-manager)
* [Terminal emulator](#terminal-emulator)
* [Cursor theme](#cursor-theme)
* [Virtual Machines](#virtual-machines)

<!-- vim-markdown-toc -->

## The display manager

I chose a console display manager called [ly](https://github.com/cylgom/ly) with a custom `/etc/ly/config.ini` and a modifyed language file. Somehow modifying `/etc/ly/lang/en.ini` did not have any effect, so I created a copy called `/etc/ly/lang/en2.ini` and called it from the config file. 

Since I did not like the original console font I [modified](https://wiki.archlinux.org/index.php/Linux_console#Fonts) `/etc/vconsole.conf` to use [Lat2-Terminus16](http://alexandre.deverteuil.net/pages/consolefonts/) with 8859-1 font map and also have the colemak keyboard layout system wide.

```bash
KEYMAP=colemak
FONT=Lat2-Terminus16
FONT_MAP=8859-1
```

To use that font from from the early userspace I make sure I use the `consolefont` hook in `/etc/mkinitcpio.conf`. Also load the graphics driver module (in my case intel) earlier in [Early KMS start](https://wiki.archlinux.org/index.php/Kernel_mode_setting#Early_KMS_start) to avoid font changes/flickering/glitches. To apply these changes rebuild with your kernel preset `sudo mkinitcpio -p linux` (check your kernel presets in `ls /etc/mkinitcpio.d`). Then reboot.


## The Window Manager

In the past, I used XFCE for a long time with compiz. XFCE is very lightweight and compiz has many cool effects. But maybe biased from my 8-bit age, I use mostly command line tools. For that reason I swapped my window manager to [i3wm](https://i3wm.org/). Actually an i3 fork with [gaps](https://github.com/Airblader/i3).

The program launcher I use is [rofi](https://github.com/DaveDavenport/rofi) with some [themes](https://github.com/davatorium/rofi-themes).

The bar I use in i3 is [polybar](https://github.com/jaagr/polybar)

> TODO For lock screen I have key combo that triggers a [script]() that pixelates the current screen.

I am currently exploring the move to sway <https://swaywm.org/>

## Terminal emulator

I am using ~~URxvt~~ kitty. 

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

## Virtual Machines

Unfortunately my Full Spectrum Laser controller only works in Windows Operating system. It became bothering to restart the computer to use the laser so I run Windows 7 in qemu. Here's how:

- Install the `qemu` and `samba` packages and have a Windows install image on hand.
- Make a qcow2 image `qemu-img create -f qcow2 win7.img 20G`
- `qemu-system-x86_64 -hda win7.img -cdrom win7sp1u64.iso -boot d -enable-kvm -cpu host -smp 2 -m 3G -vga std -net nic,model=e1000 -net user,smb=/home/unix -usbdevice tablet`
- The shared folders will be located at `\\10.0.2.4\qemu`


