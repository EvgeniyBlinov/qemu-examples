## qemu ventoy

```
## download ventoy
wget https://github.com/ventoy/Ventoy/releases/download/v1.0.97/ventoy-1.0.97-livecd.iso

qemu-system-x86_64 \
    -enable-kvm    \
    -cpu host      \
    -boot d        \
    -m 2048        \
    -smp 4         \
    -cdrom ventoy-1.0.97-livecd.iso
```

### qemu ventoy create usb file

```
dd if=/dev/urandom of=./usb.img bs=2G count=1

qemu-system-x86_64                                             \
    -enable-kvm                                                \
    -cpu host                                                  \
    -boot order=d,menu=on                                      \
    -m 2048                                                    \
    -smp 4                                                     \
    -drive if=none,id=stick,format=raw,cache=none,file=usb.img \
    -device nec-usb-xhci,id=xhci                               \
    -device usb-storage,bus=xhci.0,drive=stick                 \
    -cdrom ventoy-1.0.97-livecd.iso

## add files
sudo losetup -fP ./usb.img

sudo mkdir -p /mnt/usbp1

sudo mount $(sudo losetup -a |awk -F: '/usb.img/{print $1}')p1 /mnt/usbp1

## add iso images
sudo mkdir -p /mnt/usbp1/iso

## deactivate
sudo umount /mnt/usbp1

sudo losetup -d $(sudo losetup -a |awk -F: '/usb.img/{print $1}')

## check
qemu-system-x86_64                             \
    -enable-kvm                                \
    -cpu host                                  \
    -boot order=c,menu=on                      \
    -m 2048                                    \
    -smp 4                                     \
    -device nec-usb-xhci,id=xhci               \
    -device usb-storage,bus=xhci.0,drive=stick \
    -drive if=none,id=stick,format=raw,cache=none,file=usb.img
```
