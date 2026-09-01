# Network in Arch Linux

<!-- vim-markdown-toc GFM -->

* [Changing the network interface names](#changing-the-network-interface-names)
* [Activating or deactivating network profiles](#activating-or-deactivating-network-profiles)
* [Get your public IP](#get-your-public-ip)
* [Check current UL/DL speed](#check-current-uldl-speed)
* [Extend free wifi at airports](#extend-free-wifi-at-airports)
	* [Change MAC address with `macchanger`](#change-mac-address-with-macchanger)
	* [Change MAC address with vanilla commands](#change-mac-address-with-vanilla-commands)
	* [Change MAC address to a rooted Android in terminal](#change-mac-address-to-a-rooted-android-in-terminal)
	* [Block/unblock wireless devices to save battery](#blockunblock-wireless-devices-to-save-battery)
* [Check if a remote port is open](#check-if-a-remote-port-is-open)
* [Download an entire website](#download-an-entire-website)

<!-- vim-markdown-toc -->

## Changing the network interface names

Add/edit a file in `/etc/udev/rules.d/10-network.rules` and reboot

```bash
SUBSYSTEM=="net", ACTION=="add", ATTR{address}=="c8:5b:76:e5:fc:23", NAME="cable0"
SUBSYSTEM=="net", ACTION=="add", ATTR{address}=="00:28:f8:2b:12:18", NAME="wifi0"
```

## Activating or deactivating network profiles

Create network profiles in `/etc/netctl/`. There are sample ones in the `examples` folder.

Stop all profiles: `sudo netctl stop-all`

Activate ethernet: `sudo netctl start ethernet-static`

Activate ethernet dhcp: `sudo netctl start ethernet-dhcp`

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

## Tailscale (do not use the snap)

The [Canonical snap](https://snapcraft.io/tailscale) is strictly confined. Documented limitations (verified 2026-09-01 against that listing):

* `tailscale ssh` will not work.
* If Tailscale SSH mode is on (`tailscale set --ssh`), **normal** SSH over the tailnet also fails. Disable it: `tailscale set --ssh=false`.
* `tailscale update` does not work; use `sudo snap refresh tailscale`.
* `tailscale file cp` / `tailscale cert` only see snap-writable paths (e.g. `~/snap/tailscale/common/`).
* `tailscale drive share` does not work.

`sudo snap refresh tailscale` only updates the same confined snap. It does not make Tailscale SSH work. Replace the snap with the [official Linux package](https://tailscale.com/docs/install/linux). This is a new node: you re-authenticate, you get a new Tailscale IP, then delete the old snap node in the admin console.

On the machine (local console):

```bash
. /etc/os-release
echo "$ID $VERSION_ID"
which tailscale
snap list tailscale
```

**Debian / Ubuntu** ([official install](https://tailscale.com/docs/install/linux)):

```bash
sudo snap remove --purge tailscale
curl -fsSL https://tailscale.com/install.sh | sh
sudo tailscale up
sudo tailscale set --ssh
tailscale ip
which tailscale   # must NOT be /snap/bin/tailscale
```

**Arch** ([Arch Wiki](https://wiki.archlinux.org/title/Tailscale), [community package](https://archlinux.org/packages/?name=tailscale)):

```bash
sudo snap remove --purge tailscale
sudo pacman -S tailscale
sudo systemctl enable --now tailscaled
sudo tailscale up
sudo tailscale set --ssh
tailscale ip
which tailscale   # must NOT be /snap/bin/tailscale
```

`sudo tailscale up` prints a login URL. After auth, `tailscale status` should show the node online. `tailscale set --ssh` only works on the official daemon, not the snap.

OpenSSH is a separate backup path (`sudo apt install openssh-server` / `sudo pacman -S openssh`, then enable `ssh` or `sshd`). Use that if you do not want Tailscale SSH. Do not enable Tailscale SSH on a remaining snap install.

X220 (2026-09-02): Arch-only. Ubuntu on `sda3` was wiped and reformatted as `spare` (`/spare`). Arch GRUB is on the MBR. Tailscale `1.102.3` pacman, `--ssh` on, node `x220-2` (`100.95.99.50`). `irix` NOPASSWD sudo.

Tailscale is the network path only. It does not install OSes, shrink partitions, or pick GRUB entries. A second-OS install on this machine needs a local console for partition/GRUB/first boot. After Arch is installed and Tailscale+sshd are in that install, remote setup can continue over the tailnet.

## Check if a remote port is open

```bash
[unix ~]$ telnet
telnet> open beachlab.org 80
Trying 95.17.151.251...
Connected to beachlab.org.
Escape character is '^]'.
```

## Download an entire website

`npm install website-scraper website-scraper-puppeteer`

Create `index.js` with this content

```js
// index.js
const scrape = require('website-scraper');
const PuppeteerPlugin = require('website-scraper-puppeteer');
const path = require('path');

scrape({
    // Provide the URL(s) of the website(s) that you want to clone
    // In this example, you can clone the Our Code World website
    urls: ['https://URL/'],
    // Specify the path where the content should be saved
    // In this case, in the current directory inside the ourcodeworld dir
    directory: path.resolve(__dirname, 'DIRECTORY'),
    // Load the Puppeteer plugin
    plugins: [ 
        new PuppeteerPlugin({
            launchOptions: { 
                // If you set  this to true, the headless browser will show up on screen
                headless: true
            }, /* optional */
            scrollToBottom: {
                timeout: 10000, 
                viewportN: 10 
            } /* optional */
        })
    ]
});
```

And run `npm index.js`
