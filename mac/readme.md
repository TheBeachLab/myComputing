# MacOS 

## Move Motion Templates to external drive

- Move the `Motion Templates.localized` folder in `Movies` to an external drive
- Open a terminal in `Movies`
- `ln -s ` and drag the external folder

## qemu on Intel macOS with acceleration

Install qemu and samba via brew `brew install qemu samba` and use `-accel hvf` instead of kvm

```bash
qemu-system-x86_64 -hda /Volumes/Mac\ Data/win7/win7.img -cdrom 
/Volumes/Mac\ Data/win7/win7sp1u64.iso -boot d -accel hvf -cpu host -smp 2 
-m 3G -vga std -net nic,model=e1000 -net user,smb=/Users/tbl -usbdevice 
tablet
```


