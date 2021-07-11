# Linux troublehooting

<!-- vim-markdown-toc GFM -->

* [Wacom tablet not scrolling in QT apps](#wacom-tablet-not-scrolling-in-qt-apps)
* [Fixing a segmentation fault](#fixing-a-segmentation-fault)
* [Canon Lide 120](#canon-lide-120)
* [Old libraries not found](#old-libraries-not-found)
* [Pacman troubleshooting](#pacman-troubleshooting)
* [CUPS](#cups)
	* [Cannot access http://localhost:631](#cannot-access-httplocalhost631)
	* [Roland does not appear in CUPS](#roland-does-not-appear-in-cups)
* [Send to Roland Vinyl](#send-to-roland-vinyl)
* [TODO X220](#todo-x220)
* [Intel NUC freezes with eGPU](#intel-nuc-freezes-with-egpu)
* [X not starting](#x-not-starting)

<!-- vim-markdown-toc -->

## Wacom tablet not scrolling in QT apps

Wacom tablet scrolls in gtk apps but not in qt apps (kdenlive, freecad, etc). `xinput test 21` shows button press 4 and 5 for scroll actions but seems that QT does not naturally recognize them as scroll actions. Firefox. terminals, etc work fine though.

The tablet scroll strip in the tablet works. Here is the output of `xinput test 10`

```bash
button press   4 a[3]=2 a[4]=0 a[5]=0 
button release 4 a[3]=2 a[4]=0 a[5]=0 
motion a[3]=2 a[4]=0 a[5]=0 
button press   4 a[3]=1 a[4]=0 a[5]=0 
button release 4 a[3]=1 a[4]=0 a[5]=0 
motion a[3]=1 a[4]=0 a[5]=0
```

Compare it with the output of the cursor

```bash
button press   5 
button release 5 
motion a[2]=0 a[3]=0 a[4]=0 a[5]=0 
button press   5 
button release 5 
motion a[2]=0 a[3]=0 a[4]=0 a[5]=0 
button press   5 
button release 5 
motion a[2]=0 a[3]=0 a[4]=0 a[5]=0
```

## Fixing a segmentation fault

I am going to show how I fixed the following problem

```bash
[unix ~]$ cadence
Using Tray Engine 'Qt'
Segmentation fault (core dumped)
```

First check the list of coredumps and get info

```bash
[unix ~]$ coredumpctl list
...
Sat 2020-11-14 19:29:34 CET  199894  1000   985  11 present   /usr/bin/python3.8
[unix ~]$ coredumpctl info 199894
           PID: 199894 (python3)
           UID: 1000 (unix)
           GID: 985 (users)
        Signal: 11 (SEGV)
     Timestamp: Sat 2020-11-14 19:29:34 CET (8min ago)
  Command Line: /usr/bin/python3 /usr/share/cadence/src/cadence.py
    Executable: /usr/bin/python3.8
 Control Group: /user.slice/user-1000.slice/session-1.scope
          Unit: session-1.scope
         Slice: user-1000.slice
       Session: 1
     Owner UID: 1000 (unix)
       Boot ID: 871e977e8a4c4141a55280436b88b9b0
    Machine ID: f409642b16af41dea88241c5f8a8c126
      Hostname: hal
       Storage: /var/lib/systemd/coredump/core.python3.1000.871e977e8a4c4141a55280436b88b9b0.199894.160537857400000>
       Message: Process 199894 (python3) of user 1000 dumped core.
                
                Stack trace of thread 199894:
                #0  0x00007f878f3e70e2 _ZN10QXcbCursor12queryPointerEP14QXcbConnectionPP18QXcbVirtualDesktopP6QPoin>
                #1  0x00007f878f3f727c _ZN8QXcbDrag4initEv (libQt5XcbQpa.so.5 + 0x6c27c)
                #2  0x00007f878f3f7485 _ZN8QXcbDragC2EP14QXcbConnection (libQt5XcbQpa.so.5 + 0x6c485)
                #3  0x00007f878f3c7c63 _ZN14QXcbConnectionC1EP19QXcbNativeInterfacebjPKc (libQt5XcbQpa.so.5 + 0x3cc>
                #4  0x00007f878f3cb99c _ZN15QXcbIntegrationC2ERK11QStringListRiPPc (libQt5XcbQpa.so.5 + 0x4099c)
                #5  0x00007f878f70561d _ZN21QXcbIntegrationPlugin6createERK7QStringRK11QStringListRiPPc (libqxcb.so>
                #6  0x00007f8790cbb627 _ZN22QGuiApplicationPrivate25createPlatformIntegrationEv (libQt5Gui.so.5 + 0>
                #7  0x00007f8790cbcae1 _ZN22QGuiApplicationPrivate21createEventDispatcherEv (libQt5Gui.so.5 + 0x131>
                #8  0x00007f879488b936 _ZN23QCoreApplicationPrivate4initEv (libQt5Core.so.5 + 0x2ba936)
                #9  0x00007f8790cbfb20 _ZN22QGuiApplicationPrivate4initEv (libQt5Gui.so.5 + 0x134b20)
                #10 0x00007f87913b76aa _ZN19QApplicationPrivate4initEv (libQt5Widgets.so.5 + 0x15f6aa)
                #11 0x00007f8791cc84aa n/a (QtWidgets.abi3.so + 0x37d4aa)
                #12 0x00007f8791cc858a n/a (QtWidgets.abi3.so + 0x37d58a)
                #13 0x00007f8794c87e70 n/a (sip.cpython-38-x86_64-linux-gnu.so + 0x16e70)
                #14 0x00007f8795917570 _PyObject_MakeTpCall (libpython3.8.so.1.0 + 0x127570)
                #15 0x00007f8795912894 _PyEval_EvalFrameDefault (libpython3.8.so.1.0 + 0x122894)
                #16 0x00007f879590c9a4 _PyEval_EvalCodeWithName (libpython3.8.so.1.0 + 0x11c9a4)
                #17 0x00007f87959bcf03 PyEval_EvalCode (libpython3.8.so.1.0 + 0x1ccf03)
                #18 0x00007f87959c8868 n/a (libpython3.8.so.1.0 + 0x1d8868)
                #19 0x00007f87959c2a43 n/a (libpython3.8.so.1.0 + 0x1d2a43)
                #20 0x00007f879588267b PyRun_FileExFlags (libpython3.8.so.1.0 + 0x9267b)
                #21 0x00007f87958820f2 PyRun_SimpleFileExFlags (libpython3.8.so.1.0 + 0x920f2)
                #22 0x00007f87959d582a Py_RunMain (libpython3.8.so.1.0 + 0x1e582a)
                #23 0x00007f87959b17b9 Py_BytesMain (libpython3.8.so.1.0 + 0x1c17b9)
                #24 0x00007f879564f152 __libc_start_main (libc.so.6 + 0x28152)
                #25 0x000055759116904e _start (python3.8 + 0x104e)
                
                Stack trace of thread 199906:
                #0  0x00007f879571c46f __poll (libc.so.6 + 0xf546f)
                #1  0x00007f879069463b n/a (libxcb.so.1 + 0xc63b)
                #2  0x00007f879069637b xcb_wait_for_event (libxcb.so.1 + 0xe37b)
                #3  0x00007f878f3ee1b0 _ZN14QXcbEventQueue3runEv (libQt5XcbQpa.so.5 + 0x631b0)
                #4  0x00007f879469ee8f n/a (libQt5Core.so.5 + 0xcde8f)
```

Then `coredumpctl gdb 199894` will show you some info

```bash
Reading symbols from /usr/bin/python3.8...
(No debugging symbols found in /usr/bin/python3.8)
[New LWP 199894]
[New LWP 199906]
[Thread debugging using libthread_db enabled]
Using host libthread_db library "/usr/lib/libthread_db.so.1".
Core was generated by `/usr/bin/python3 /usr/share/cadence/src/cadence.py'.
Program terminated with signal SIGSEGV, Segmentation fault.
#0  0x00007f878f3e70e2 in QXcbCursor::queryPointer(QXcbConnection*, QXcbVirtualDesktop**, QPoint*, int*) ()
   from /usr/lib/python3.8/site-packages/PyQt5/Qt/plugins/platforms/../../lib/libQt5XcbQpa.so.5
[Current thread is 1 (Thread 0x7f87954af740 (LWP 199894))]
```

From there you can backtrace at the gdb prompt `(gdb) bt`. Seems that I have multiple versions of `libQt5XcbQpa.so.5` I link the version in `site-packages` to the main `/usr/lib` but then complains

```bash
[unix ~]$ cadence
Using Tray Engine 'Qt'
Cannot mix incompatible Qt library (5.14.1) with this library (5.15.1)
Aborted (core dumped)
```

> SOLVED! Removed all QT5 installed via pip and reinstalled it via pacman. QT and python package managements are nasty. At least I learnt some debugging tools

## Canon Lide 120

Create a file in `/lib/udev/rules.d/` called `60-canonlide120.rules`

Run `lsusb`:

```bash
[irix@x220 ~]$ lsusb
Bus 002 Device 004: ID 04a9:190e Canon, Inc. CanoScan LiDE 120
```

So content of file is

```bash
SUBSYSTEM=="usb", ATTRS{idVendor}=="04a9", ATTRS{idProduct}=="190e", MODE="0666"
```

And then reload the rules

`udevadm control --reload-rules`

FOUND HERE
http://unix.stackexchange.com/questions/44308/understanding-udev-rules-and-permissions-in-libusb#147777/

---
In the supported scanners by sane, there's no Lide 120:  
`cat /etc/sane.d/genesys.conf`

Tutorial config in Arch Linux:  
https://www.eanderalx.org/linux/scanner_lide110

## Old libraries not found

```bash
Error while burning bootloader.
/home/irix/opt/arduino-1.8.1/hardware/tools/avr/bin/avrdude: error while loading shared libraries: libtinfo.so.5: cannot open shared object file: No such file or directory
```

That is because a new library has been installed. Solve it with a symbolic link:

`sudo ln -s /usr/lib/libtinfo.so /usr/lib/libtinfo.so.5`

```bash
[irix@x220 lib]$ ls -l libtin*
lrwxrwxrwx 1 root root 22 May  2 13:37 libtinfo.so -> /usr/lib/libtinfo.so.6
lrwxrwxrwx 1 root root 20 May  2 14:57 libtinfo.so.5 -> /usr/lib/libtinfo.so
lrwxrwxrwx 1 root root 27 May  2 13:37 libtinfo.so.6 -> /usr/lib/libncursesw.so.6.0
```

## Pacman troubleshooting

`sudo pacman -S --force pacman-mirrorlist`

`sudo pacman -Syyu`

## CUPS

### Cannot access http://localhost:631

Connection refused: Make sure CUPS is installed and activated:

```bash
systemctl enable cups
systemctl start cups
```

CUPS web config 403 Forbidden: Add yourself to `wheel` group

### Roland does not appear in CUPS

Roland does not appear

`ls /dev/bus/usb/00x/00x`  where x to be found by lsusb with printer connected and lit up.
Are you on the lp user group?
Adjust udev rules like this , Ensure that is set 0666.

`lpinfo -v`

`/usr/lib/cups/backend/usb`

Create udev rule

```bash
[irix@x220 ~]$ lsusb
Bus 002 Device 014: ID 0b75:0405 Roland DG Corp.

custom udev rule in a file /etc/udev/rules.d/10-usbprinter.rules containing

ATTR{idVendor}=="0b75", ATTR{idProduct}=="0405", MODE:="0660", GROUP:="lp"
```

This is to give permission to the printer

save the file and cat to usb device
`cat filename > /dev/xxx`

If `/usr/lib/cups/backend/usb` shows
Failed to open device, code: -1

Then the power management might be disabling the Device

In `/etc/default/tlp` add the lines
`USB_BLACKLIST="0b75:0405"` and so on with every device

## Send to Roland Vinyl

`cat file.camm > /dev/usb/lp0`

Not working in Arch, says...

`device or resource busy`

might it be because of this?

```bash
[irix@hal ~]$ cat /etc/modprobe.d/blacklistusblp.conf 
blacklist usblp
```
Blacklisting usblp should not be required

It works with:

`lpr -P vinyl`

## TODO X220

- Grub does not show menu
- Configure coreboot
- dotfiles
- Dual Intel / Radeon
- Hotplug Radeon

## Intel NUC freezes with eGPU

I experienced random freezes with intel NUC 11 running an external GPU. At the beginning I thought it was a memory fault but it ended up being some sort of power saving settings in the BIOS. I followed the instructions given by `marCH` here https://community.intel.com/t5/Intel-NUCs/NUC10i7FNH-Issue-with-Thunderbolt3-eGPU/m-p/1279791/highlight/true but the issue is still happening.. 

I SOLVED the issue by issuing `nvidia-settings -a [gpu:0]/GPUPowerMizerMode=1` at the start of the session. I created an alias for that `stayhot` which is the equivalent of `dontfreeze` but sounds better.

Alternatively you can make this change persistent in `/etc/X11/xorg.conf`

```
Option         "RegistryDwords" "PowerMizerEnable=0x1; PerfLevelSrc=0x2222; PowerMizerDefault=0x1; PowerMizerDefaultAC=0x1"
```

Also make sure the thunderbolt cable is not loose. That can also cause freezes in Linux.

## X not starting

Usually due to an error in `xorg.conf`. Do the following:

- `CTRL+ALT+F1` to enter a new login screen
- `sudo systemctl stop ly` note that `ly` is my display manager
- Fix the offending `/etc/X11/xorg.conf`
- `sudo systemctl start ly`

