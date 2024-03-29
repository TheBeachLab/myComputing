# Welcome BSD

I decided to try GhoshBSD based on FreeBSD because I am starting to feel that Linux distros are slowly diverging from the Unix philosophy. I understand most of the people are fine with systemd but I just want more simplicity and control over the whole OS.

I liked the first impression, feels fast and stable. So far all the programs I need are there, so I will give it a try. My goal is to have:

- Ly display manager
- bspwm window manager although I maybe try something simple like mwm
- Polybar
- Picom
- Some application launcher (still unsure)


## Wifi

First scan the available networks `sudo ifconfig wlan0 up scan`

Then connect to the desired network `sudo ifconfig wlan0 ssid "DHI Wifi"`

Check that it is associated `ifconfig`

Get an IP address `sudo dhclient wlan0`

`sudo service netif restart`

## Openvpn

`openvpn-client file.ovpn`

## Add identities to the ssh agent

`eval "$(ssh-agent)"` and `ssh-add private-key`

## Mount filesystems

### fat32

`mount -v -t msdosfs /dev/da0s1 /mnt/usb1`

### ext2 ext3 ext4

`fuse-ext2 /dev/da0s1 /mnt/usb1`

### ntfs

`ntfs-3g /dev/da0s1 /mnt/usb1`

## lp0

In dmesg

```bash
ugen0.6: <Roland DG GS-24> at usbus0
ulpt0 on uhub0
ulpt0: <Roland DG GS-24, class 0/0, rev 1.10/0.00, addr 5> on usbus0
ulpt0: using bi-directional mode
```

So the device is in `/dev/ulpt0` that belongs to `operaror` group

`crw-rw-rw-  1 root     operator  0x1b3 Feb 15 13:55 ulpt0`

This works `echo pu5000,5000 > /dev/ulpt0` if you belong to group operator.


