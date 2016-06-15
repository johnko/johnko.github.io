---
title: IP aliases in OpenSolaris
date: 2010-03-17 20:15:07
tags: [ bash, ip, opensolaris, script ]
alias: ip-aliases-in-opensolaris/index.html
---

Via http://ipucu.enderunix.org/view.php?id=369&lang=en

To have multiple IP addresses on 1 NIC, you need to make aliases.

```sh
pfexec bash
MYIP=192.168.255.5
MYSUBMASK=255.255.255.0
MYNET=192.168.255.0
MYHOST=$( hostname )
MYNIC="nge0:1"
```

Enable an alias:
```sh
ifconfig $MYNIC plumb
echo "$MYIP $MYHOST $MYHOST.local" >> /etc/hosts
```

Save nic settings in file:
```sh
HOSTNIC=/etc/hostname.$MYNIC
echo "$MYIP" > $HOSTNIC
echo "netmask $MYSUBMASK broadcast + up" >> $HOSTNIC
```

Manually set IP:
```sh
ifconfig $MYNIC $MYIP netmask $MYSUBMASK up
```

Add new route:
```sh
route add $MYNET $MYIP -interface -ifp $MYNIC
```

Check that your IP is set:
```sh
ifconfig -a $MYNIC
```
