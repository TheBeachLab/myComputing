# Network in Arch Linux

<!-- vim-markdown-toc GFM -->

* [Changing the network interface names](#changing-the-network-interface-names)
* [Activating or deactivating network devices](#activating-or-deactivating-network-devices)
* [Obtaining DHCP address](#obtaining-dhcp-address)
* [Get your public IP](#get-your-public-ip)
* [Check current UL/DL speed](#check-current-uldl-speed)
* [Extend free wifi at airports](#extend-free-wifi-at-airports)
	* [Change MAC address with `macchanger`](#change-mac-address-with-macchanger)
	* [Change MAC address with vanilla commands](#change-mac-address-with-vanilla-commands)
	* [Change MAC address to a rooted Android in terminal](#change-mac-address-to-a-rooted-android-in-terminal)
	* [Block/unblock wireless devices to save battery](#blockunblock-wireless-devices-to-save-battery)

<!-- vim-markdown-toc -->

## Changing the network interface names

Add/edit a file in `/etc/udev/rules.d/10-network.rules` and reboot

```bash
SUBSYSTEM=="net", ACTION=="add", ATTR{address}=="c8:5b:76:e5:fc:23", NAME="cable0"
SUBSYSTEM=="net", ACTION=="add", ATTR{address}=="00:28:f8:2b:12:18", NAME="wifi0"
```

## Activating or deactivating network devices

Activate ethernet: `sudo ip link set cable0 up`

Deactivate wifi: `sudo ip link set wifi0 down`

## Obtaining DHCP address

`dhcpcd cable0`

## Get your public IP

`curl https://ipinfo.io/ip` you can pipe it to `iponmap`

`curl https://ipinfo.io/ip | iponmap`

## Check current UL/DL speed

`vnstat --live -i wifi0`

## Extend free wifi at airports

They usually bind the 1h free connection to the MAC address of the device. So after the 1h is out just change the MAC address and connect again.

### Change MAC address with `macchanger`

First install `macchanger` and then use it like this:

`macchanger -r wifi0` obtain a random MAC address

`macchanger -p wifi0` return to the hardware factory MAC address

### Change MAC address with vanilla commands

This process is a bit longer, just if your haven't yet downloaded `macchanger`, use this instead:

```bash
ip link show wifi0
ip link set dev wifi0 down
ip link set dev wifi0 address xx:xx:xx:yy:yy:yy
ip link set dev wifi0 up
```

### Change MAC address to a rooted Android in terminal

Open `termux` or any other terminal app and look for your wifi interface (here `wlan0`):

```bash
su
ip link show
busybox ip link show wlan0
busybox ifconfig wlan0 hw ether xx:xx:xx:yy:yy:yy
```

> **Warning!** These changes are permanent

### Block/unblock wireless devices to save battery

List the wireless devices `rfkill`

```bash
[unix ~]$ rfkill
ID TYPE      DEVICE                   SOFT      HARD
 0 bluetooth tpacpi_bluetooth_sw   blocked unblocked
 1 wlan      phy0                unblocked unblocked
```

And block (or unblock) the desired one `sudo rfkill block 1`.
