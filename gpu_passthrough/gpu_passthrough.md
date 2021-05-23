# GPU Passthrough

# Disclaimer
This is only for myself. A lot of step is missing here. And also do not follow this
instruction if you are not comfortable. Follow this at your own risk. I would
not accept any responsibilities.

Resources:
- https://mathiashueber.com/windows-virtual-machine-gpu-passthrough-ubuntu/#
- https://passthroughpo.st/using-evdev-passthrough-seamless-vm-input/

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
