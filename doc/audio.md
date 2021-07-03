# Audio in Arch Linux


<!-- vim-markdown-toc GFM -->

* [ALSA](#alsa)
	* [Reset ALSA to factory](#reset-alsa-to-factory)
	* [Restart ALSA](#restart-alsa)
	* [Get persistent sound card numbers](#get-persistent-sound-card-numbers)
	* [Set default sound card](#set-default-sound-card)
	* [Set default alsamixer levels](#set-default-alsamixer-levels)
* [Pulseaudio](#pulseaudio)
	* [Reset Pulse to factory](#reset-pulse-to-factory)
	* [Output audio through HDMI port](#output-audio-through-hdmi-port)
	* [Rename Pulseaudio sinks and sources](#rename-pulseaudio-sinks-and-sources)
	* [Create a virtual microphone and virtual speaker](#create-a-virtual-microphone-and-virtual-speaker)
* [Jack Audio](#jack-audio)
	* [Add the USB mic Yeti to `jack`](#add-the-usb-mic-yeti-to-jack)
	* [Output audio through HDMI port](#output-audio-through-hdmi-port-1)
* [Software](#software)
	* [Ardour](#ardour)
	* [Helm](#helm)
	* [Music on the CLI](#music-on-the-cli)
* [Bluetooth](#bluetooth)
* [Check when the headphone is plugged and unplugged](#check-when-the-headphone-is-plugged-and-unplugged)

<!-- vim-markdown-toc -->

## ALSA

### Reset ALSA to factory

This is required after messing up with jack and getting strange behaviour. ALSA config files are:

- User level `~/.asoundrc`
- System wide `/etc/asound.conf`

### Restart ALSA

`sudo alsactl restore`

### Get persistent sound card numbers

Check the sound modules and their names `cat /proc/asound/modules` and create a file `/etc/modprobe.d/alsa-base.conf` with this content:

```bash
options snd_hda_intel index=0
```

### Set default sound card

To set the default sound card. Check your devices `cat /proc/asound/cards`

```bash
 0 [PCH            ]: HDA-Intel - HDA Intel PCH
                      HDA Intel PCH at 0xe2340000 irq 137
 1 [Microphone     ]: USB-Audio - Yeti Stereo Microphone
                      Blue Microphones Yeti Stereo Microphone at usb-0000:00:14.0-2.3, full speed
 2 [Capture        ]: USB-Audio - FHD Capture
                      VXIS Inc FHD Capture at usb-0000:00:14.0-2.2, super speed
```

Type `aplay -l` to list all the devices that can play sound

```bash
 **** List of PLAYBACK Hardware Devices ****
card 0: PCH [HDA Intel PCH], device 0: CX8200 Analog [CX8200 Analog]
  Subdevices: 0/1
  Subdevice #0: subdevice #0
card 0: PCH [HDA Intel PCH], device 3: HDMI 0 [HDMI 0]
  Subdevices: 1/1
  Subdevice #0: subdevice #0
card 0: PCH [HDA Intel PCH], device 7: HDMI 1 [HDMI 1]
  Subdevices: 1/1
  Subdevice #0: subdevice #0
card 0: PCH [HDA Intel PCH], device 8: HDMI 2 [HDMI 2]
  Subdevices: 1/1
  Subdevice #0: subdevice #0
card 0: PCH [HDA Intel PCH], device 9: HDMI 3 [HDMI 3]
  Subdevices: 1/1
  Subdevice #0: subdevice #0
card 0: PCH [HDA Intel PCH], device 10: HDMI 4 [HDMI 4]
  Subdevices: 1/1
  Subdevice #0: subdevice #0
card 1: Microphone [Yeti Stereo Microphone], device 0: USB Audio [USB Audio]
  Subdevices: 1/1
  Subdevice #0: subdevice #0
```

and modify `~/.asoundrc` to use CX8200 by default

```bash
pcm.!default {
  type hw
  card 0
    hint {
    show on
    description "Default ALSA Output (currently CX8200 sound card)"
  }
}

ctl.!default {
  type hw
  card 0
}
```

### Set default alsamixer levels

To persistently set the default mutes/levels in alsamixer run `sudo alsactl store`

## Pulseaudio

### Reset Pulse to factory

This is required after messing up with jack and getting strange behaviour. Pulseaudio config files:

- It will check first user level `~/.config/pulse`
- System wide `/etc/pulse/`

### Output audio through HDMI port

In the configuration tab of pavucontrol select the appropiate profile you wish.

### Rename Pulseaudio sinks and sources

You might want to install the AUR package `pamac` to identify the DEVICE or just look it up with `pacmd list-sinks` and `pacmd list-sources`. Then rename a sink with:

`pacmd 'update-sink-proplist DEVICE device.description="DESCRIPTION" '`

And for the sources:

`pacmd 'update-source-proplist DEVICE device.description="DESCRIPTION" '`

If you want this at login add this to `~/.config/pulse/default.pa`

```bash
update-sink-proplist DEVICE device.description="DESCRIPTION"
update-source-proplist DEVICE device.description="DESCRIPTION"
```

In my case I have:

```bash
update-sink-proplist alsa_output.pci-0000_00_1f.3.analog-stereo device.description="Internal Output"
update-source-proplist alsa_input.pci-0000_00_1f.3.analog-stereo device.description=device.description="Monitor of Internal Output"
update-source-proplist alsa_output.pci-0000_00_1f.3.analog-stereo.monitor device.description="Monitor of Internal Output"
```

### Create a virtual microphone and virtual speaker

A null sink is a virtual sink that discards audio sent to it. That’s not very useful by itself, but the monitor that comes with it can be very useful. In `~/.config/pulse/default.pa`

```bash
.ifexists module-null-sink.so
load-module module-null-sink sink_name=Source sink_properties='device.description="Virtual Sink"'
.endif
```

Most applications won't allow you to select a microphone from a source. Here is where a virtual source comes handy.

```bash
# virtual source
# This will create a virtual source (a microphone you can select) from a monitor
.ifexists module-virtual-source.so
load-module module-virtual-source source_name=VirtualMic master=nulla.monitor source_properties='device.description="Virtual Mic A"'
.endif
```

## Jack Audio

The default audio server in Linux is `pulseaudio`. That is fine for standard use (one recording sink). But once you start having more complex situations, where you want to record the output of the speakers or you want multiple recordings or playbacks, you will find limitations. That cannot be achieved with `pulseaudio`. You need a more advanced audio server. That server is `jack`.

Installing jack is a bit tricky: install the following packages `jack2 libffado cadence jack_capture python-dbus realtime-privileges pulseaudio-jack`, add yourself to the groups `audio` and `realtime` then reboot. You should be able to start jack via `cadence`. Then make sure you autostart at boot. 

> NOTE: In arch/i3 to make start at login modify `/etc/X11/xinit/xinitrc.d/61-cadence-session-inject.sh` and add `$STARTUP` ant the end
>
> ```bash
> STARTUP="$INSTALL_PREFIX/bin/cadence-session-start --system-start-by-x11-startup $STARTUP"
> $STARTUP
> ```

Otherwise it won't autostart `jack` at login. Also bridge **ALSA Audio** using `ALSA > PulseAudio > JACK (Plugin)` option and bridge **PulseAudio** with Auto-start at login. Otherwise you might realise that you cannot play youtube videos while `jack` is running. In that case install the pakage `pulseaudio-jack`. Then edit `/etc/pulse/default.pa` and the following below `#load-module module-alsa-sink` section:

```bash
load-module module-jack-sink
load-module module-jack-source
```

And restart pulseaudio with `killall pulseaudio`

### Add the USB mic Yeti to `jack`

Check the audio recording devices with `arecord -l`

```bash
**** List of CAPTURE Hardware Devices ****
card 0: PCH [HDA Intel PCH], device 0: CX8200 Analog [CX8200 Analog]
Subdevices: 0/1
Subdevice #0: subdevice #0
card 2: Microphone [Yeti Stereo Microphone], device 0: USB Audio [USB Audio]
Subdevices: 0/1
Subdevice #0: subdevice #0
card 3: Capture [FHD Capture], device 0: USB Audio [USB Audio]
Subdevices: 1/1
Subdevice #0: subdevice #0
```

In this case the name of the device is `Microphone`. So add it with `alsa_in -j "Yeti" -d hw:Microphone -q 1 2>&1 1> /dev/null &`. Explanation:
- `alsa_in` is the command
- `-j "Yeti"` is the name that will appear in `Carla`
- `-d hw:Microphone` is the name of the device we are adding
- `-q 1` sets the quality to low/passable
- `2>&1` sends all the output to std output
- `1> /dev/null ` trashes std output
- `&` puts the process in the background

### Output audio through HDMI port

In `cadence` go to `configure` and change the output device to the HDMI. Make sure you also have `duplex mode` selected. Stop and restart jack and the pulseaudio bridge.

## Software

### Ardour

~~Ardour requires you to start jack server previously.~~ Ardour now works with pulseaudio as well

### Helm

Placeholder for Helm synth

### Music on the CLI

Install `mpd` and add a config file in `~/.config/mpd/mpd.conf` like

```bash
####### MPD CONFIG #######

# Required files
db_file            "~/.config/mpd/database"
log_file           "~/.config/mpd/log"

# Optional
music_directory    "~/Music"
playlist_directory "~/.config/mpd/playlists"
pid_file           "~/.config/mpd/pid"
state_file         "~/.config/mpd/state"
sticker_file       "~/.config/mpd/sticker.sql"

audio_output {
      type  "pulse"
      name  "pulse audio"
}

audio_output {
type               "fifo"
name               "toggle_visualizer"
path               "/tmp/mpd.fifo"
format             "44100:16:2"
}

####### END MPD CONFIG #######
```

Create the playlist dir `mkdir ~/.config/mpd/playlists`

Start/enable the mpd service `systemctl start --user mpd.service` and `systemctl enable --user mpd.service`.

Install `ncmpcpp` and probably set a better alias for it.

## Bluetooth

Install `bluez bluez-utils pulseaudio-bluetooth pulseaudio-alsa` and start/enable `bluetooth.service`. Make sure is not blocked `rfkill unblock bluetooth`

Run `bluetoothctl`:
- `power on`
- `list` computer controller
- `scan on/off` scan for devices
- `devices` list discovered devices
- `paired-devices` list them
- `info` *device* check device info
- `pair` *device*
- `trust` *device*
- `connect` *device*

If you are getting a connection error `org.bluez.Error.Failed` retry by restarting PulseAudio daemon first `pulseaudio -k` and the `pulseaudio` and try again.

`pacmd list-cards` to get the card number and `pacmd set-card-profile card_number a2dp_sink`

But A2DP high fidelity profile is not working. No audio out. To keep fixing later.

## Check when the headphone is plugged and unplugged

First check if the service is running `systemctl start acpid.service` then run `acpi_listen`

```bash
jack/headphone HEADPHONE unplug
jack/microphone MICROPHONE unplug
jack/headphone HEADPHONE plug
jack/microphone MICROPHONE plug
```

You will be able to see the events.

> To Fix: Note that plugging the headphones also makes the system think that there is a mic plugged-in. This mutes the laptop mic leaving you with no mic.

