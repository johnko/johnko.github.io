---
title: Make a USB Drive That Can Boot Some LiveCD ISO Files
date: 2014-10-05
tags: [ install, ubuntu, linux, liveisousb, usb ]
---

I created a USB thumb drive that can boot some LiveCD ISO files.

This is useful for me because I can copy the ISO files to the USB thumb drive instead of burning CDs or DVDs.

(Optional) modified my `/etc/hosts` file so my script can copy the ISO files I already had downloaded:

```sh
sudo bash
echo "192.168.255.100 chill.home.local" >> /etc/hosts
```

Run and follow the prompts:

```sh
read -p "USB Device (ex. /dev/sdb):" DEVICE
VOLUME=liveusb

# create filesystem on usb pen
mkfs.vfat -n ${VOLUME} ${DEVICE}1

# mount usb
mkdir /tmp/mnt
mount ${DEVICE}1 /tmp/mnt/

# install grub2 on usb pen
grub-install --no-floppy --root-directory=/tmp/mnt ${DEVICE}
```

You should create your grub config menu and save it as `/tmp/mnt/boot/grub/grub.cfg`, here's an example:

```
menuentry "Clonezilla Live 20140915 Trusty 64bit" {
 set iso=/boot/iso/clonezilla-live-20140915-trusty-amd64.iso
 loopback loop ${iso}
 linux (loop)/live/vmlinuz boot=live live-config quiet noswap nolocales edd=on nomodeset noprompt ocs_live_run="ocs-live-general" ocs_live_extra_param="" ocs_live_keymap="" ocs_live_batch="no" ocs_lang="" video=uvesafb:mode_option=1024x768-16 toram=filesystem.squashfs ip=frommedia nosplash findiso=${iso}
 initrd (loop)/live/initrd.img
}

menuentry "Ubuntu 14.04.1 Desktop 64bit" {
 set iso=/boot/iso/ubuntu-14.04.1-desktop-amd64.iso
 loopback loop ${iso}
 linux (loop)/casper/vmlinuz.efi file=/cdrom/preseed/ubuntu.seed boot=casper iso-scan/filename=${iso} quiet splash noeject noprompt toram --
 initrd (loop)/casper/initrd.lz
}

menuentry "mfsBSD SE 10.0 64bit - pw:mfsroot" {
 set iso="/boot/iso/mfsbsd-se-10.0-RELEASE-amd64.iso"
 linux16 /memdisk iso
 initrd16 ${iso}
}

menuentry "Ubuntu 14.04.1 Server 64bit" {
 set iso=/boot/iso/ubuntu-14.04.1-server-amd64.iso
 loopback loop ${iso}
 linux (loop)/install/vmlinuz file=/cdrom/preseed/ubuntu-server.seed iso-scan/filename=${iso} quiet noeject noprompt toram --
 initrd (loop)/install/initrd.gz
}
```

Now we continue by adding iso images:

```sh
# create iso directory
mkdir /tmp/mnt/boot/iso

# copy memdisk
cp /usr/lib/syslinux/memdisk /tmp/mnt/memdisk
chmod 555 /tmp/mnt/memdisk

# copy iso files to /tmp/mnt/boot/iso
cp clonezilla-live-20140915-trusty-amd64.iso /tmp/mnt/boot/iso/
cp ubuntu-14.04.1-desktop-amd64.iso /tmp/mnt/boot/iso/
cp ubuntu-14.04.1-server-amd64.iso /tmp/mnt/boot/iso/
cp mfsbsd-se-10.0-RELEASE-amd64.iso /tmp/mnt/boot/iso/
cp pfSense-LiveCD-2.1.5-RELEASE-amd64.iso /tmp/mnt/boot/iso/
cp FreeNAS-9.2.1.8-RELEASE-x64.iso /tmp/mnt/boot/iso/

# umount
sync
umount /tmp/mnt/
```

I then had a USB drive I could boot from and a menu to select which ISO to load. It only works with LiveCDs that have a ramdisk or boot-to-ram option: CloneZilla, Ubuntu Desktop; Ubuntu Server and mfsBSD.
