---
title: Limiting ZFS ARC Cache in OpenSolaris
date: 2010-04-08 03:55:20
tags: [ arc, bash, memory, opensolaris, script, zfs ]
alias: limiting-zfs-arc-cache/index.html
---

## Set zfs_arc_max

Via http://www.solarisinternals.com/wiki/index.php/ZFS_Evil_Tuning_Guide#Limiting_the_ARC_Cache

```
For example, if an application needs 5 GBytes of memory on a system with
36-GBytes of memory, you could set the arc maximum to 30 GBytes, (0x780000000
or 32212254720 bytes). Set the zfs:zfs_arc_max parameter in the /etc/system
file:

set zfs:zfs_arc_max = 0x780000000

or

set zfs:zfs_arc_max = 32212254720
```

So basically, M(10243). Where M is the memory size in GB. I have 12 GB of memory, and I want ARC to only use up to half of it.

```sh
su
echo "set zfs:zfs_arc_max = 6442450944" >>/etc/system
```

Then reboot. When you reboot, your system may go into maintenance mode, a message says something about the boot-archive" being down because we changed /etc/system. Login with a user that has staff/root privileges and check the file, then clear the maintenance mode on the boot-archive service.

```sh
less /etc/system
# q to quit
svcadm clear system/boot-archive
```
