# Video in Arch linux

<!-- vim-markdown-toc GFM -->

* [Download youtube video](#download-youtube-video)
* [Download youtube video and subtitles](#download-youtube-video-and-subtitles)
* [Download youtube audio](#download-youtube-audio)
* [Hardcode subtitles into video](#hardcode-subtitles-into-video)
* [Download a specific format from youtube video](#download-a-specific-format-from-youtube-video)
* [Convert GIF to MP4](#convert-gif-to-mp4)
* [Unsorted ffmpeg-fu](#unsorted-ffmpeg-fu)
* [Adjust webcam settings](#adjust-webcam-settings)
* [DSLR Video Webcam](#dslr-video-webcam)
	* [Hardware required](#hardware-required)
	* [Software required](#software-required)
	* [Canon 7D](#canon-7d)
	* [Canon M6](#canon-m6)
* [Virtual webcam from OBS output](#virtual-webcam-from-obs-output)
	* [v4l2loopback](#v4l2loopback)
	* [OBS Studio Plugin](#obs-studio-plugin)
* [Multiplex video devices](#multiplex-video-devices)
	* [Multiplex a physical webcam](#multiplex-a-physical-webcam)
	* [Multiplex a virtual webcam](#multiplex-a-virtual-webcam)
* [Teleprompter](#teleprompter)
* [Video production tips (Not Linux related)](#video-production-tips-not-linux-related)

<!-- vim-markdown-toc -->

## Download youtube video

`youtube-dl` URL-VIDEO

>  Slow downloads? Try  `youtube-dl --format best` URL-VIDEO

## Download youtube video and subtitles

`youtube-dl --write-auto-sub` URL-VIDEO

## Download youtube audio

`youtube-dl -x --audio-format mp3` URL-VIDEO

## Hardcode subtitles into video

`ffmpeg -i` VIDEO-FILE `-vf subtitles=`SUBS-FILE OUTPUT-FILE

## Download a specific format from youtube video

First check the available formats

`youtube-dl -F GKgfCthuiV0` where GKgfCthuiV0 is the youtube code of the video

```bash
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

Select the one you want, in my case 251, which is the highest audio quality

`youtube-dl -f 251 GKgfCthuiV0`

Optional: transcode the webm file into wav (or any other) format you want

`ffmpeg -i file.webm file.wav`

## Convert GIF to MP4

`ffmpeg -i animated.gif -movflags faststart -pix_fmt yuv420p -vf "scale=trunc(iw/2)*2:trunc(ih/2)*2" video.mp4`

Explanation

- `movflags` – This option optimizes the structure of the MP4 file so the browser can load it as quickly as possible.
- `pix_fmt` – MP4 videos store pixels in different formats. We include this option to specify a specific format which has maximum compatibility across all browsers.
- `vf` – MP4 videos using H.264 need to have a dimensions that are divisible by 2. This option ensures that’s the case.

## Unsorted ffmpeg-fu

- Neil's variable bit rate 1080p MP3: `ffmpeg -i input_video -vcodec libx264 -crf 25 -preset medium -vf scale=-2:1080 -acodec libmp3lame -q:a 4 -ar 48000 -ac 2 output_video.mp4`
- Neil's no audio: `ffmpeg -i input_video -vcodec libx264 -b:v 1000k -vf scale=-2:1080 -an output_video.mp4`
- Use nvidia hw encoder `-vcodec h264_nvenc`
- Convert single image to a 5s video `ffmpeg -loop 1 -i image.png  -t 5  output.mp4` or batch convert ``
- Concatenate videos `ffmpeg -f concat -safe 0 -i videos.txt -c copy output.mp4` Where `videos.txt` is:
```
file '/path/to/file1'
file '/path/to/file2'
file '/path/to/file3'
```

## Adjust webcam settings

Check webcams:

```bash
[unix ~]$ v4l2-ctl --list-devices
Integrated Camera: Integrated C (usb-0000:00:14.0-8):
	/dev/video0
	/dev/video1
	/dev/media0
```

Check available settings

```bash
[unix ~]$ v4l2-ctl -d /dev/video0 --list-ctrls
                     brightness 0x00980900 (int)    : min=0 max=255 step=1 de
                       contrast 0x00980901 (int)    : min=0 max=255 step=1 de
                     saturation 0x00980902 (int)    : min=0 max=100 step=1 de
                            hue 0x00980903 (int)    : min=-180 max=180 step=1
 white_balance_temperature_auto 0x0098090c (bool)   : default=1 value=1
                          gamma 0x00980910 (int)    : min=90 max=150 step=1 d
           power_line_frequency 0x00980918 (menu)   : min=0 max=2 default=1 v
      white_balance_temperature 0x0098091a (int)    : min=2800 max=6500 step=e
                      sharpness 0x0098091b (int)    : min=0 max=7 step=1 defa
         backlight_compensation 0x0098091c (int)    : min=0 max=2 step=1 defa
                  exposure_auto 0x009a0901 (menu)   : min=0 max=3 default=3 v
              exposure_absolute 0x009a0902 (int)    : min=4 max=1250 step=1 d
         exposure_auto_priority 0x009a0903 (bool)   : default=0 value=0
```

Set a parameter `v4l2-ctl -d /dev/video0 --set-ctrl=exposure_auto=3`

## DSLR Video Webcam

### Hardware required

You need a capture card to get the HDMI out signal from the DSLR. I bought a cheap HDMI to USB3 capture card in Amazon that works with the V4L2 module without drivers, just plug and play. The card is unbranded and the chipset inside is:

```bash
[66367.259465] usb 2-2: Product: FHD Capture
[66367.259468] usb 2-2: Manufacturer: VXIS Inc
[66367.269584] uvcvideo: Found UVC 1.00 device FHD Capture (1bcf:2c99)
```

The DSLR camera will become now a Webcam. You can even select it in your videoconferences.

It is convenient that you also get a AC battery adapter to make sure the battery won't die during capture.

### Software required

You can capture video with pretty much anything but I mostly use [OBS Studio](https://obsproject.com/). It allows you to create scenes, overlays, green screen, etc.

### Canon 7D

The Canon 7D has a mini HDMI port out. With the Canon firmware 2.0.3 I cannot obtain a clean HDMI out. There is always the focus rectangle there. So I downloaded [Magic Lantern Firmware](https://magiclantern.fm/). To control the camera settings I use [Entangle](https://entangle-photo.org/) which allows me to capture images as well as control the camera settings.

### Canon M6

Canon M6 has a micro HDMI port. With the Canon M6 I can obtain a clean HDMI out in manual focus. But controlling the camera settings is just annoying. USB tether does not seem to work and there is a Canon App which is so faulty.

Magic Lantern is not available for the M6. It could run [CHDK](https://chdk.fandom.com/wiki/CHDK) but at the moment of writing the firmware is still unported.

The camera turns black every 30 minutes which is annoying. To override this I followed the instructions found in [CHDK forum](https://chdk.setepontos.com/index.php?topic=13489.msg141507#msg141507)

1. Prepare the SD card, format it low level in the camera or as FAT32 (not exFAT!). Save this script as `makeCanonSDCard.sh`

```
#!/bin/bash
#Enable powershot basic scripting on a memory card
if [ $# -ne 1 ] ; then
  echo
  echo "Usage : ./makeCanonSDCard.sh [ device ]"
  echo
  echo " [ device ] is a fat32 / fat16 partition on the memory card"
  echo " example : ./makeCanonSDCard.sh /dev/sdb1"
  echo "NOTE: please run as root"
  exit 112
fi
echo Boot sector of $1 will be modified. If you are not sure this is what you want then cancel with Ctrl-C
sleep 8
#TAG on boot sector
if ! echo -n SCRIPT | dd bs=1 count=6 seek=496 of=$1 ; then {
   echo failed writing to boot sector
   exit 113
} fi
if mount | grep /mnt ; then {
    umount /mnt
} fi
if ! mount $1 /mnt ; then {
    echo failed to mount
    exit 114
} fi
#create script request file
echo -n "for DC_scriptdisk" > /mnt/script.req
#Canon M6 script
echo 'DIM palette_buffer_ptr = 0x11d4c
DIM active_palette_buffer = 0x11d44
DIM palette_to_zero = 0
private sub RegisterProcs()
    System.Create()
    ExecuteEventProcedure("UI.CreatePublic")
end sub
private sub Initialize()
    RegisterProcs()
    LockMainPower()
    adr = *palette_buffer_ptr
    adr = adr + (palette_to_zero * 4)
    if *adr <> 0 then
        adr = *adr + 4
        memset(adr, 0, 256 * 4)
    end if
end sub
'>/mnt/extend.m
#Done !
echo "Please check /mnt for files extend.m and script.req"
```

This includes saving the following script as `extend.m` in the root directory of the card.

```
DIM palette_buffer_ptr = 0x11d4c
DIM active_palette_buffer = 0x11d44
DIM palette_to_zero = 0

private sub RegisterProcs()
    System.Create()
    ExecuteEventProcedure("UI.CreatePublic")
end sub

private sub Initialize()
    RegisterProcs()
    LockMainPower()
    adr = *palette_buffer_ptr
    adr = adr + (palette_to_zero * 4)
    if *adr <> 0 then
        adr = *adr + 4
        memset(adr, 0, 256 * 4)
    end if
end sub
```

2. Check the sdcard devide with `lsblk` and run the script as a superuser. SERIOUS WARNING!! MAKE SURE YOU SPECIFY THE CORRECT SD CARD DEVICE!!! If you don't know what I am talking about **don't do it**.

3. Every time you start the camera you need will see the overlays. Enter PLAYBACK mode and do a single press of SET. This will cause the script execution. Go back to shooting mode, if the overlays are not present, the display will stay ON.

4. Do not format the card in low level or you will have to repeat steps 1 and 2 again.

## Virtual webcam from OBS output

![composite](../img/composite-small.png)

There is a way. Tricky, not trivial, to pipe the output of OBS studio into a virtual webcam that you can use in a videoconference (for instance). Here's how I do it.

### v4l2loopback

First clone and install `v4l2loopback` from here [https://github.com/umlaeute/v4l2loopback](https://github.com/umlaeute/v4l2loopback). You can load the kernel module like this:

`sudo modprobe v4l2loopback video_nr=9`

This will create a virtual video device `/dev/video9`. You can also create more than one virtual camera and label them:

`sudo modprobe v4l2loopback devices=2 video_nr=10,20 card_label=TimerCam,StreamCam`

When you want to remove the device use `sudo modprobe -r v4l2loopback` or `sudo rmprobe v4l2loopback`. Finally remember to rebuild the kernel module every time you update the kernel!

> Note: Now obs will do the modprobe as long as the module is compiled for the kernel (reinstall the module after rebooting in a new kernel)


### OBS Studio Plugin

~~I installed the AUR package `obs-v4l2sink` which is actually a sink where OBS will pour the virtual webcam. Then in OBS top menu select `tools/v4l2sink` and choose the video device `/dev/video9` that you activated before and `YUV12` format.~~ Now OBS includes a `Start Virtual Camera` button that does just that.

You will be able now to use a new webcam that will appear (tested in zoom and Firefox).

## Multiplex video devices

### Multiplex a physical webcam

I asume here that /dev/video0 is the webcam. Create a loopcam as seen before

`sudo modprobe v4l2loopback video_nr=10 card_label=COPYWebcam`

Now examine the format of your original webcam

`ffmpeg -f v4l2 -list_formats all -i /dev/video0` which will display something similar to

```bash
[video4linux2,v4l2 @ 0x559d808150c0] Raw       :     uyvy422 :           UYVY 4:2:2 : 1920x1080 1280x720 1024x768 640x480 320x240
[video4linux2,v4l2 @ 0x559d808150c0] Compressed:       mjpeg :          Motion-JPEG : 1920x1080 1280x720 1024x768 640x480 320x240
```

Now dump the stream of the original webcam into the COPYWebcam device

`ffmpeg -i /dev/video0 -f v4l2 -codec:v rawvideo -pix_fmt uyvy422 /dev/video10`

### Multiplex a virtual webcam

This is useful in the case you start a virtual cam in OBS and you want to use the virtual camera in ZOOM and a website. Create 2 virtual cameras 

`sudo modprobe -r v4l2loopback && sudo modprobe v4l2loopback devices=2 video_nr=10,20 card_label=ZOOMCam,NINJAcam` 

and start virtual camera in ZOOM. Then list the properties of the first virtual camera

`ffmpeg -f v4l2 -list_formats all -i /dev/video10` which will show properties like this one

`[video4linux2,v4l2 @ 0x56517d3c70c0] Raw       :     yuyv422 :           YUYV 4:2:2 : 1280x720`

And now dump the contents of the first virtual camera into the second virtual camera

`ffmpeg -i /dev/video10 -f v4l2 -codec:v rawvideo -pix_fmt yuyv422p /dev/video20`

## Teleprompter

I use [Imaginary Teleprompter](https://imaginary.tech/teleprompter/) with a custom glass frame. The problem I face is slight ghosting of the text but nothing too annoying for the price (free). If I keep teleprompting I will consider purchasing a glass or a prompter from <https://imaginary.tech/teleprompter/>

## Video production tips (Not Linux related)

- Chroma
	- Green: For daylight scenes
	- Blue: For dark scenes
	- RGB Green: For low light
- RGB backgrounds
	- Keep Iso low
	- Aperture full open
	- 180 degree rule
	- Better grey than white backdrop
- Teleprompter
	- Use street language, not written language
	- Use peripheral vision to read
	- Use reminder keywords not word by word script
