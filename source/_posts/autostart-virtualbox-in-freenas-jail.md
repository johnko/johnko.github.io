---
title: Autostart a VirtualBox Machine in a FreeNAS Jail
date: 2014-10-08
tags: [ virtualbox, freenas, jail ]
---

> I don't use this anymore, but kept the instructions here for future reference

Source [https://forums.freenas.org/index.php?threads/autostart-virtualbox-vm.22116/][1]

For example, my virtual machine named zenbar, I add to `/mnt/tank/jails/zenbar/etc/rc.conf`:

```
vboxnet_enable="YES"
vboxheadless_enable="YES"
vboxheadless_machines="zenbar"
vboxheadless_zenbar_name="zenbar"
vboxheadless_zenbar_user="vbox"
vboxheadless_zenbar_stop="poweroff"
```

[1]: https://forums.freenas.org/index.php?threads/autostart-virtualbox-vm.22116/
