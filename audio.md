# Audio in Arch Linux


<!-- vim-markdown-toc GFM -->

* [Get persistent sound card numbers](#get-persistent-sound-card-numbers)
* [Jack Audio](#jack-audio)
* [alsamixer](#alsamixer)
* [Add the USB mic Yeti to `jack`](#add-the-usb-mic-yeti-to-jack)
* [Check when the headphone is plugged and unplugged](#check-when-the-headphone-is-plugged-and-unplugged)
* [Ardour](#ardour)
* [Helm](#helm)

<!-- vim-markdown-toc -->

## Get persistent sound card numbers

Check the sound modules and their numbers `cat /proc/asound/modules` and create a file `/etc/modprobe.d/alsa-base.conf` with this content:

```bash
options snd_hda_intel index=0
```


## Jack Audio

The default audio server in Linux is `pulseaudio`. That is fine for standard use (one recording sink). But once you start having more complex situations, where you want to record the output of the speakers or you want multiple recordings or playbacks, you will find limitations. That cannot be achieved with `pulseaudio`. You need a more advanced audio server. That server is `jack`.

Installing jack is a bit tricky: install the following packages `jack2 libffado cadence jack_capture python-dbus realtime-privileges pulseaudio-jack`, add yourself to the groups `audio` and `realtime` then reboot. You should be able to start jack via `cadence`. Then make sure you autostart at boot. Otherwise you might notice that you cannot start `jack` if there is any other program using the audio, including browsers since they might block the interface. Hence the need to start `jack` server before starting any other audio software.

Also bridge **ALSA Audio** using `ALSA > PulseAudio > JACK (Plugin)` option and bridge **PulseAudio** with Auto-start at login. Otherwise you might realise that you cannot play youtube videos while `jack` is running. 
 
In that case install the pakage `pulseaudio-jack`. Then edit `/etc/pulse/default.pa` and the following below `#load-module module-alsa-sink` section:

```bash
load-module module-jack-sink
load-module module-jack-source
```

And restart pulseaudio with `killall pulseaudio`

## alsamixer

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
 
and modify `sudo nano /etc/asound.conf` to use CX8200 by default


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


To set the default mutes/levels in alsamixer `su` and `alsactl store`

## Add the USB mic Yeti to `jack`

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

## Ardour

Ardour requires you to start jack server previously.

## Helm

Placeholder for Helm synth


