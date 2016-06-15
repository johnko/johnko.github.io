---
title: Workaround Invalid CD Error During Ubuntu Server Install
date: 2014-10-05
tags: [ install, ubuntu, linux, liveisousb, usb ]
---

I got an Invalid CD-ROM error during install from my LiveISO-USB, so I selected `Execute a shell`.

I created folder to mount my LiveISO-USB and mount it:

```sh
mkdir /liveusb
mount -o ro /dev/sda1 /liveusb
```

Mount the Ubuntu Server ISO to `/cdrom`:

```sh
mount -o loop /liveusb/boot/iso/ubuntu-14.04.1-server-amd64.iso /cdrom
exit
```

The installer continues for a bit, and gets stuck again.

I had to skip to `Install GRUB...` to complete the installation.

On reboot, I created folder to mount my LiveISO-USB and mount it again:

```sh
sudo mkdir /liveusb
sudo mount -o ro /dev/sda1 /liveusb
```

Mount the Ubuntu Server ISO to `/media/cdrom` this time:

```sh
sudo mount -o loop /liveusb/boot/iso/ubuntu-14.04.1-server-amd64.iso /media/cdrom
```

Install the rest of the missing packages:

```sh
sudo apt-get install ubuntu-standard openssh-server curl
```

Update my apt sources.list:

```sh
curl -O http://www.johnko.ca/scripts/sources14.04.1.list
sudo bash -c 'cat ~/sources14.04.1.list > /etc/apt/sources.list'
```

Update my packages:

```sh
sudo apt-get update
sudo apt-get dist-upgrade
```

And reboot for good measure:

```sh
sudo reboot
```
