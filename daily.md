# Arch Linux common daily tasks

<!-- vim-markdown-toc GFM -->

* [Time and timezones](#time-and-timezones)
* [Find all files containing specific text](#find-all-files-containing-specific-text)
* [PDF](#pdf)
	* [Reduce a PDF filesize](#reduce-a-pdf-filesize)
	* [Merge PDF files](#merge-pdf-files)
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

## Time and timezones

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

## Find all files containing specific text

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

## PDF

### Reduce a PDF filesize

With ghostscript `gs -sDEVICE=pdfwrite -dCompatibilityLevel=1.4 -dPDFSETTINGS=/screen -dNOPAUSE -dQUIET -dBATCH -sOutputFile=output.pdf input.pdf`

You can change the `screen` option (72 dpi) to `ebook` (150 dpi), `prepress` (300 dpi), `printer` (300 dpi) and `default`.

### Merge PDF files

`pdfunite in-1.pdf in-2.pdf in-n.pdf out.pdf`

## Mount a USB drive

Check the device name with `lsblk` and then use `pmount device [ label ]` and `pumount` to mount/unmount it. If label is given, the mount point will be `/media/label`,
otherwise it will be `/media/device`.

## Encrypt a file or directory with GPG. Paranoid level 1

To **encrypt a file*** `gpg -c filename` outputs `filename.gpg`. To **decrypt a file** `gpg filename.gpg`.

To **encrypt directories** `gpgtar -c -o file.gpg dirname`. To **decrypt a directory** `gpgtar -d file.gpg`

You will then be prompted for a passphrase.

## Steganography. Paranoid level 2

When you need to hide a secret you can encrypt the file. But an adversary will know that something is hidden, and they can attempt to break the code by putting some brute force. But if you embed this encrypted secret into an apparently normal file. The third person would not even be aware of the fact that a seemingly harmless looking image or audio file carries a secret message or a file embedded in it. This type of encryption where you hide one file securely into another is called **Steganography**. And I use it a lot. 

Install `steghide` command line utility. To embed a secret into an image run:

`steghide embed -ef secret.ggp -cf image.jpg`

You will need another passphrase to embed the secret into the image. To extract the secret from the image

`steghide extract -sf image.jpg`

## Create custom commands

I keep my custom commands in a file called `.custom_commands.sh`. This file is sourced by `.bashrc` where I added this line at the bottom `source .custom_commands.sh`. Inside the file `.custom_commands.sh` I create my custom functions:

```
my_function () {
place your code here
}
```

## Install a font

Move it to `~/.local/share/fonts`. More info [here](https://wiki.archlinux.org/index.php/Fonts)

## VNC server

I use [TigerVNC](https://tigervnc.org/) for it's easy use.

### Starting a VNC server

Place a password/passphrase in `~/.vnc/passwd`

To start `x0vncserver -display :0 -passwordfile .vnc/passwd`

To stop just close the terminal process.

## Screen

### Show the connected displays

`xrandr`

### Set physical dimensions of your display

Useful for showing real dimensions at 100% `xrandr --fbmm 310x175`

### Fix overscan problems in HDMI

This should work `xrandr --output HDMI1 --set underscan on --set "underscan vborder" 25 --set "underscan hborder" 40` but it is not working in my case



## Dummy serial and lp ports

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

## Polybar

For displaying siji fonts `xfd -fa siji` check the glyph code and display with the code. For example `printf "\ue101"`
