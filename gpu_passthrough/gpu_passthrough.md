# GPU Passthrough and virtual machine
On Ubuntu

# Disclaimer
This is only for myself. A lot of step is missing here. And also do not follow this
instruction if you are not comfortable. Follow this at your own risk. I would
not accept any responsibilities.

Resources:
- https://mathiashueber.com/windows-virtual-machine-gpu-passthrough-ubuntu/#
- https://passthroughpo.st/using-evdev-passthrough-seamless-vm-input/
- https://www.majorgeeks.com/content/page/how_to_download_windows_10_enterprise_iso_with_media_creation_tool.html

# Disable Nvidia GPU
```bash
./script.sh | grep -i 'nvidia'
# find the proper GPU ids
```
```bash
sudo subl /etc/initramfs-tools/modules 
```
and add the following
```
vfio vfio_iommu_type1 vfio_virqfd vfio_pci ids={comma separated guest GPU IDs}
```

```bash
sudo subl /etc/modules
```
and add the following
```
vfio vfio_iommu_type1 vfio_pci ids={comma separated guest GPU IDs}
```



```bash
sudo subl /etc/modprobe.d/nvidia.conf
```
add
```
softdep nouveau pre: vfio-pci 
softdep nvidia pre: vfio-pci 
softdep nvidia* pre: vfio-pci
```

```bash
sudo subl /etc/default/grub
```
add to the end of `GRUB_CMDLINE_LINUX_DEFAULT` like after splass to be something like
```
GRUB_CMDLINE_LINUX_DEFAULT="quiet splash intel_iommu=on vfio-pci.ids={comma separated guest GPU IDs}"
```
```bash
sudo update-grub
```

(just for myself) disable CSM in the bios

# Virt settings
- Chipset: Q35
- Firmware: UEFI x86_64: /usr/share/OVMF/OVMF_CODE.fd

To share mouse and keyboard, add the followings to the xml file, with proper device ids
```
  <qemu:commandline>
    <qemu:arg value="-object"/>
    <qemu:arg value="input-linux,id=mouse1,evdev=/dev/input/by-id/usb-Logitech_Gaming_Mouse_G502_046D38593531-event-mouse"/>
    <qemu:arg value="-object"/>
    <qemu:arg value="input-linux,id=kbd1,evdev=/dev/input/by-id/usb-Kinesis_Advantage2_Keyboard_314159265359-if01-event-kbd,grab_all=on,repeat=on"/>
  </qemu:commandline>
```

- When installing OS, just change the boot option to see only the CD/DVD ROM, just for the first time. The rest, change it back.
- [Disable windows auto update,](https://www.windowscentral.com/how-stop-updates-installing-automatically-windows-10) specially if you have chosen small storage for the virtual machine.

## Driver for the virtual machine before GPU Passthrough
Guide: https://youtu.be/jLRmVNWOrgo

Download `Windows guest tools - spice-guest-tools` from the following link and install it inside the virtual machine.
- https://www.spice-space.org/download.html
