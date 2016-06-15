---
title: 'OpenSolaris 2009.06, dev setup'
date: 2010-05-26 20:30:24
tags: [ main instructions, opensolaris ]
alias: opensolaris-200906-dev-setup/index.html
---

## Snapshot the root zfs pool

This allows us to revert to the pristine files at a later date.



```sh
pfexec zfs snapshot -r rpool@firstboot
```

## (Optional) [Disable system beep from Terminal](/2010/03/16/Disable-system-beep-from-Terminal/)

## (Optional) [Alias "ls" and "ll" with color](/2010/03/16/Alias-ls-and-ll-with-color/)

Here's my ~/.bashrc:

```sh
#
# Define default prompt to @:<"($|#) "># and print '#' for user "root" and '$' for normal users.
#
PS1='${LOGNAME}@$(/usr/bin/hostname):$(
    [[ "${LOGNAME}" == "root" ]] && printf "%s" "${PWD/${HOME}/~}# " ||
    printf "%s" "${PWD/${HOME}/~}\$ ")'
xset -b
alias ls='/usr/gnu/bin/ls -aFh --color   --group-directories-first'
alias ll='/usr/gnu/bin/ls -aFhl --color   --group-directories-first'
alias lsx='/usr/gnu/bin/ls -aFhX --color   --group-directories-first'
alias llx='/usr/gnu/bin/ls -aFhlX --color   --group-directories-first'
```

## (Optional) [Disable xscreensaver in OpenSolaris](/2010/03/16/Disable-xscreensaver-in-OpenSolaris/)

## (Optional) Reduce boot selection timeout



```sh
# edit /rpool/boot/grub/menu.lst
# change "timeout 30" to a more reasonable number, "timeout 10"
pfexec vi /rpool/boot/grub/menu.lst
# if you can't figure out how to use "vi", use "nano"
pfexec nano /rpool/boot/grub/menu.lst
```

## (Optional) Change boot mode to verbose

Via http://opensolaris.org/jive/thread.jspa?messageID=464482 and http://opensolaris.org/jive/thread.jspa?threadID=60655



```sh
# edit /rpool/boot/grub/menu.lst
# make a copy of your kernel$ line, comment out the original
# add "-kv -m verbose" to the end of the copy of the kernel$ line
# you also need to comment out all splashimage lines
# and remove ",console=graphics"
pfexec vi /rpool/boot/grub/menu.lst
# if you can't figure out how to use "vi", use "nano"
pfexec nano /rpool/boot/grub/menu.lst
```

Note: You'll see the console login first, but after a while, the GDM login will appear.

Here's my `/rpool/boot/grub/menu.lst`:

```
#splashimage /boot/grub/splash.xpm.gz
background 215ECA
timeout 10
default 0
#---------- ADDED BY BOOTADM - DO NOT EDIT ----------
title OpenSolaris 2009.06
findroot (pool_rpool,0,a)
bootfs rpool/ROOT/opensolaris
#splashimage /boot/solaris.xpm
foreground d25f00
background 115d93
#kernel$ /platform/i86pc/kernel/$ISADIR/unix -B $ZFS-BOOTFS,console=graphics
kernel$ /platform/i86pc/kernel/$ISADIR/unix -B $ZFS-BOOTFS -kv -m verbose
module$ /platform/i86pc/$ISADIR/boot_archive
#---------------------END BOOTADM--------------------
```

## [Mirroring the root pool in OpenSolaris](/2010/03/02/Mirroring-the-root-pool-in-OpenSolaris/)

## [Static IP in OpenSolaris 2009.06](/2010/03/02/Static-IP-in-OpenSolaris-2009-06/)

## Create a zfs filesystem for sources and "my" installation folder

```sh
pfexec bash
zfs set compression=on rpool
zfs create -o mountpoint=/apps rpool/apps
zfs create -o mountpoint=/music rpool/music
zfs create -o mountpoint=/src rpool/src
chmod 777 /src
```

## [Setup IPS package repository for OpenSolaris 2009.06](/2010/02/27/Setup-IPS-package-repository-for-OpenSolaris-2009-06/)

## [Useful packages in OpenSolaris](/2010/04/06/Useful-packages-in-OpenSolaris/)

## Link missing binaries

For some reason, `gcc` can't be found, so let's link it


```sh
cd /usr/bin
pfexec ln -s gcc-4.3.2 gcc
pfexec ln -s /usr/bin/ginstall /usr/ucb/install
```

Use python2.6:

```sh
pfexec rm python
pfexec ln -s python2.6 python
```

## [Installing libtool 2.2.6b in OpenSolaris](/2010/03/17/Installing-libtool-2-2-6b-in-OpenSolaris/)

## [Installing Readline 6.1 in OpenSolaris](/2010/03/17/Installing-Readline-6-1-in-OpenSolaris/)

## [Installing Berkeley DB 4.8.26 NC (no crypto) in OpenSolaris](/2010/03/11/Installing-Berkeley-DB-4-8-26-NC-no-crypto-in-OpenSolaris/)

## [Installing pkg-config 0.23 in OpenSolaris](/2010/03/11/Installing-pkg-config-0-23-in-OpenSolaris/)

## [Installing OpenSSL 1.0.0 in OpenSolaris](/2010/03/31/Installing-OpenSSL-1-0-0-in-OpenSolaris/)

## [Installing libssh2 1.2.4 in OpenSolaris](/2010/03/11/Installing-libssh2-1-2-4-in-OpenSolaris/)

## [Installing OpenLDAP 2.4.21 in OpenSolaris](/2010/03/11/Installing-OpenLDAP-2-4-21-in-OpenSolaris/)

## [Installing cURL 7.20.0 in OpenSolaris](/2010/03/11/Installing-curl-7-20-0-in-OpenSolaris/)

## [Installing GnuPG 1.4.10 and 2.0.15 in OpenSolaris](/2010/03/17/Installing-GnuPG-1-4-10-and-2-0-15-in-OpenSolaris/)

## [Installing MySQL 5.1.45 in OpenSolaris](/2010/03/19/Installing-MySQL-5-1-45-in-OpenSolaris/)

## [Installing XVid 1.2.2 in OpenSolaris](/2010/04/08/Installing-XVid-1-2-2-in-OpenSolaris/)

## [Installing LAME 3.98.4 in OpenSolaris](/2010/04/08/Installing-LAME-3-98-4-in-OpenSolaris/)

## [Installing Git 1.7.0.4 in OpenSolaris](/2010/04/08/Installing-Git-1-7-0-4-in-OpenSolaris/)

## [Installing x264 (via git) in OpenSolaris](/2010/04/11/Installing-x264-via-git-in-OpenSolaris/)

## [Installing FFmpeg 0.5.1 in OpenSolaris](/2010/04/12/Installing-FFmpeg-0-5-1-in-OpenSolaris/)

## [Installing Tcl 8.5.8 in OpenSolaris](/2010/04/23/Installing-Tcl-8-5-8-in-OpenSolaris/)

## [Installing PostgreSQL 8.4.3 in OpenSolaris](/2010/04/26/Installing-PostgreSQL-8-4-3-in-OpenSolaris/)

TODO: subversion, apache, php, fetchmail, courier, roundcube, davical, mplayer, tuntap, openvpn...
