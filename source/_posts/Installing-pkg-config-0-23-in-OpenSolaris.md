---
title: Installing pkg-config 0.23 on OpenSolaris
date: 2010-03-11 10:56:00
tags: [ opensolaris, pkg-config ]
alias: installing-pkg-config-023-on-opensolaris/index.html
---

## Download the source

Download the file pkg-config-0.23.tar.gz from http://pkg-config.freedesktop.org/wiki/

Save this in /src

You may also want to verify the file signature or checksums and read the README or INSTALL files.

## Extract, configure and make

```sh
cd /src
tar xzf pkg-config-0.23.tar.gz && cd pkg-config-0.23
CFLAGS=$( find -L /my -type d -name include -exec echo "-I{} " \; | grep -v "[.][0-9]" | tr -d '\n' ) \
	LDFLAGS=$( find -L /my -type d -name lib -exec echo "-L{} -R{} " \; | grep -v "[.][0-9]" | tr -d '\n' ) \
	./configure --prefix=/my/pkg-config-0.23 >>mylog.txt \
	&& gmake all >>mylog.txt \
	&& gmake check >>mylog.txt && su
gmake install >>mylog.txt
ln -s /my/pkg-config-0.23 /my/pkg-config
exit
echo "export PATH=/my/pkg-config/bin:\$PATH" >> ~/.profile
```

Congrats! pkg-config should be built successfully!
