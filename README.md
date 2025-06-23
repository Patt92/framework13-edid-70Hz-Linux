## how to create the bin, not specific
echo Modeline "2256x1504_70"  281.12  2256 2304 2336 2576  1504 1507 1517 1559  +hsync +vsync ratio=3:2 | modeline2edid
add ratio parameter (see .S file)
edid-generator -> make



## add firmware

cp 2256x1504_70.bin /usr/lib/firmware/edid/Internal70.bin


## Kernel / initramfs prepare, to fix error -2 on boot

`/etc/mkinitcpio.conf`

```ini
FILES+=(/lib/firmware/edid/Internal70.bin)
```

```bash
mkinitcpio -P
```

## Add to grub in GRUB_CMDLINE_LINUX_DEFAULT

`/etc/default/grub`

```text
... drm.edid_firmware=eDP-1:edid/Internal70.bin   
```

```bash
grub-mkconfig -o /boot/grub/grub.cfg
```

## Verify after reboot
```bash
modetest -M amdgpu -c | grep -A4 eDP
#or check 
dmesg | grep edid
```
