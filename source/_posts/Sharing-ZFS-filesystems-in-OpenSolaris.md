---
title: Sharing ZFS filesystems in OpenSolaris
date: 2010-04-08 06:55:22
tags: [ bash, opensolaris, script, share, zfs ]
alias: sharing-zfs-filesystems/index.html
---

## Set sharenfs on the serving computer

```sh
su
zfs set sharenfs=ro rpool/wav
```

"ro" = read-only
"on" = read/write
"off" = off

The other setting is `sharesmb` which requires SUNWsmbs SUNWsmbskr, a reboot, and `svcadm enable -r smb/server`, and

```sh
cat >>/etc/pam.conf <EOF
# Seem to need this line for smb / cifs:
other   password required       pam_smb_passwd.so.1     nowarn
EOF

zfs set sharesmb=on rpool/wav
```

See http://www.aspdeveloper.net/tiki-index.php?page=SolarisSMBShareSetup for more info about sharesmb

## On the client computer

```sh
su
mkdir /tmp/220wav
mount -F nfs 192.168.1.220:/rpool/wav /tmp/220wav
```
