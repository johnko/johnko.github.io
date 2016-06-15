---
title: Installing libssh2 1.2.4 on OpenSolaris
date: 2010-03-11 11:27:03
tags: [ libssh, opensolaris ]
alias: installing-libssh2-124-on-opensolaris/index.html
---

## Download the source

Download the file libssh2-1.2.4.tar.gz from http://www.libssh2.org/

Save this in /src

You may also want to verify the file signature or checksums and read the README or INSTALL files.

## Extract, configure and make

```sh
cd /src
tar xzf libssh2-1.2.4.tar.gz && cd libssh2-1.2.4
CFLAGS=$( find -L /my -type d -name include -exec echo "-I{} " \; | grep -v "[.][0-9]" | tr -d '\n' ) \
	LDFLAGS=$( find -L /my -type d -name lib -exec echo "-L{} -R{} " \; | grep -v "[.][0-9]" | tr -d '\n' ) \
	CC=cc ./configure --with-openssl --with-libz \
	--with-libssl-prefix=/my/openssl-0.9.8m/lib \
	--prefix=/my/libssh2-1.2.4 >>mylog.txt \
	&& gmake check >>mylog.txt \
	&& gmake all >>mylog.txt && su
gmake install >>mylog.txt
ln -s /my/libssh2-1.2.4 /my/libssh2
exit
```

Congrats! libssh2 should be built successfully!
