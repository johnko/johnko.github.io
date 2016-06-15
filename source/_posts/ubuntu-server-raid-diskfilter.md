---
title: Ubuntu Server RAID Diskfilter Error
date: 2014-10-08
tags: [ install, ubuntu, linux, grub, diskfilter, raid ]
---

I got an error message after a fresh install with RAID1 `error:  Diskfilter writes are not supported`.
I found this fix by Rarylson Freitas that worked for me.

Source [http://askubuntu.com/questions/468466/why-this-occurs-error-diskfilter-writes-are-not-supported][1]

```sh
wget https://gist.githubusercontent.com/rarylson/da6b77ad6edde25529b2/raw/33dfde10655718c4b32cbfc23306064965ee9e72/00_header_patched
mv /etc/grub.d/00_header /etc/grub.d/00_header.orig
mv 00_header_patched /etc/grub.d/00_header
chmod -x /etc/grub.d/00_header.orig
chmod +x /etc/grub.d/00_header
update-grub
```

[1]: http://askubuntu.com/questions/468466/why-this-occurs-error-diskfilter-writes-are-not-supported
