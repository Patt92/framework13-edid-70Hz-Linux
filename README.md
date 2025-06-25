## how to create the bin, skip this, just for information

```bash
git clone https://github.com/akatrevorjay/edid-generator
#check requirements, if there are errors
cd edid-generator
echo Modeline "2256x1504_70"  281.12  2256 2304 2336 2576  1504 1507 1517 1559  +hsync +vsync ratio=3:2 | ./modeline2edid
#add ratio parameter for 3:2 (see .S file at the end). It is not supported out of the box in the edid.S
make
```

## 1 add firmware
```bash
cp 2256x1504_70.bin /usr/lib/firmware/edid/Internal70.bin
#or the Internal70.bin (same file)
```

## 2 Kernel / initramfs prepare, to fix error -2 on boot

`/etc/mkinitcpio.conf`

```ini
FILES=(/lib/firmware/edid/Internal70.bin)
```

```bash
mkinitcpio -P
```

## 3 Add to the grub defaults for autoloading in pre state

append:
```text
... drm.edid_firmware=eDP-1:edid/Internal70.bin   
```
in `/etc/default/grub` => Default Commandline

```bash
GRUB_CMDLINE_LINUX_DEFAULT="loglevel=3 quiet drm.edid_firmware=eDP-1:edid/Internal70.bin"
```

### regenerate grub config

```bash
grub-mkconfig -o /boot/grub/grub.cfg
```

it should look like this at the end in the ma
```bash
linux   /boot/vmlinuz-linux root=XXX rw zswap.enabled=0 rootfstype=ext4 loglevel=3 quiet drm.edid_firmware=eDP-1:edid/Internal70.bin
```


## Verify after reboot
```bash
modetest -M amdgpu -c | grep -A4 eDP
```

or check the boot log

```bash
dmesg | grep edid
```
