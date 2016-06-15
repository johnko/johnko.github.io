---
title: Dovecot on OS X 10.10
date: 2014-10-21
tags: [ dovecot, osx ]
---

> I don't use this anymore, but kept the instructions here for future reference

I had setup a cron job and wanted to read the mail output in Thunderbird. I didn't need this secured with SSL/TLS as it will be listening on `localhost`.

I did the following:

```sh
sudo port install dovecot
curl -O http://www.johnko.ca/scripts/mac-doveccot.conf.patch
sudo cp /opt/local/etc/dovecot/dovecot-example.conf /opt/local/etc/dovecot/dovecot.conf
sudo chown $USER /opt/local/etc/dovecot/dovecot.conf
patch -d /opt/local/etc/dovecot < mac-doveccot.conf.patch
sudo chown root /opt/local/etc/dovecot/dovecot.conf
sudo install -d -o _dovecot -g mail -m 755 /opt/local/var/log/dovecot
sudo install -d -o _dovecot -g mail -m 755 /opt/local/var/run/dovecot
sudo dseditgroup -o edit -a $USER -t user mail
sudo port load dovecot
```

I can now connect a new mail account in Thunderbird as my Mac username@127.0.0.1 with IMAP.
