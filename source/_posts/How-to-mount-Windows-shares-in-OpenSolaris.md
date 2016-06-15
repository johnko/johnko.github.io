---
title: How to mount Windows shares in OpenSolaris
date: 2010-03-16 00:16:38
tags: [ bash, opensolaris, samba, script, shares ]
alias: how-to-mount-windows-shares-in-opensolaris/index.html
---

Via http://dlc.sun.com/osol/docs/content/SSMBAG/smbclientusertaskstm.html#mountsharetask

```sh
mount -F smbfs //[workgroup;][user[:password]@]server/share mount-point
```

In my case I had to enable smb/client:

```sh
pfexec bash
svcadm enable smb/client
mount -F smbfs //192.168.1.221/public /mnt/221
```
