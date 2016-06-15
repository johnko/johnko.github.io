---
title: Static IP in OpenSolaris 2009.06
date: 2010-03-02 21:03:04
tags: [ ip, opensolaris ]
alias: static-ip-in-opensolaris-200906/index.html
---

Based on instructions from http://dd-b.net/ddbcms/2009/01/opensolaris-static-ip/

```sh
pfexec bash
MYIP=192.168.1.5
MYSUBMASK=255.255.255.0
MYROUTER=192.168.1.1
MYHOST=$( hostname )
```

Find your network interface (hint, not lo0):

```sh
MYNIC=$( ifconfig -a | grep -v lo0 | grep IPv4 | sed 's/:.*//g' )
```

Enable network, disable NWAM:

```sh
svcadm enable physical:default
svcadm disable physical:nwam
```

Backup hosts file:

```sh
cp /etc/hosts /etc/hosts.bak
```

Comment existing, populate with 2 entries:

```sh
cat /etc/hosts.bak | sed 's/^/#/g' > /etc/hosts
echo "::1 localhost loghost ip6-localhost" >> /etc/hosts
echo "127.0.0.1 localhost loghost" >> /etc/hosts
echo "$MYIP $MYHOST $MYHOST.local" >> /etc/hosts
```

Check that you have DNS servers in `/etc/resolv.conf`, otherwise, overwrite with the Google and OpenDNS ones:

```sh
cp /etc/resolv.conf /etc/resolv.conf.bak
cat /etc/resolv.conf.bak | sed 's/^/#/g' > /etc/resolv.conf
echo "nameserver 8.8.8.8" >> /etc/resolv.conf
echo "nameserver 8.8.4.4" >> /etc/resolv.conf
echo "nameserver 208.67.222.222" >> /etc/resolv.conf
echo "nameserver 208.67.220.220" >> /etc/resolv.conf
```

Tell name server service to look at DNS for hosts:

```sh
cp /etc/nsswitch.dns /etc/nsswitch.conf
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

Add default route:

```sh
route add 0.0.0.0 $MYROUTER
echo "$MYROUTER" > /etc/defaultrouter
```

Check that your default gateway/router is marked as default:

```sh
netstat -rn
```

Check that your IP is set:

```sh
ifconfig $MYNIC
```
