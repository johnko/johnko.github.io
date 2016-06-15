---
title: Installing OpenLDAP 2.4.21 on OpenSolaris
date: 2010-03-11 13:59:33
tags: [ ldap, openldap, opensolaris ]
alias: installing-openldap-2421-on-opensolaris/index.html
---

## Download the source

Download the file openldap-stable-20100219.tgz from http://www.openldap.org/software/download/

Save this in /src

You may also want to verify the file signature or checksums and read the README or INSTALL files.

## Extract, configure and make

```sh
cd /src
tar xzf openldap-stable-20100219.tgz && cd openldap-2.4.21
CFLAGS=$( find -L /my -type d -name include -exec echo "-I{} " \; | grep -v "[.][0-9]" | tr -d '\n' ) \
	CPPFLAGS=$( find -L /my -type d -name include -exec echo "-I{} " \; | grep -v "[.][0-9]" | tr -d '\n' ) \
	LDFLAGS=$( find -L /my -type d -name lib -exec echo "-L{} -R{} " \; | grep -v "[.][0-9]" | tr -d '\n' ) \
	./configure --enable-backends --disable-ndb --disable-sql \
	--enable-overlays --with-threads --with-tls \
	--prefix=/my/openldap-2.4.21 >>mylog.txt \
	&& gmake depend >>mylog.txt && gmake >>mylog.txt \
	&& gmake test >>mylog.txt && su
gmake install >>mylog.txt
ln -s /my/openldap-2.4.21 /my/openldap
exit
echo "export PATH=/my/openldap/bin:\$PATH" >> ~/.profile
echo "export PATH=/my/openldap/sbin:\$PATH" >> ~/.profile
```

Congrats! OpenLDAP should be built successfully!
