---
title: Mirroring the root pool in OpenSolaris
date: 2010-03-02 21:06:17
tags: [ mirror, opensolaris, rpool ]
alias: mirroring-the-root-pool-in-opensolaris/index.html
---

From http://malsserver.blogspot.com/2008/08/mirroring-resolved-correct-way.html


```sh
pfexec bash
OLDDISK=c8d0
NEWDISK=c9d0
format
```

Select the blank disk (my c9d0 was 1):

```
1
fdisk
```

Since I had a new disk, it asked if I wanted to create the default Solaris partition:

```
y
quit
```

Copy disk geometry and partitions of disk 1 to disk 2

```sh
prtvtoc /dev/rdsk/${OLDDISK}s2 | fmthard -s - /dev/rdsk/${NEWDISK}s2
zpool attach -f rpool ${OLDDISK}s0 ${NEWDISK}s0
installgrub /boot/grub/stage1 /boot/grub/stage2 /dev/rdsk/${NEWDISK}s0
zpool status
```
