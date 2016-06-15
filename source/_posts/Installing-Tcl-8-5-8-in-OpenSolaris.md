---
title: Installing Tcl 8.5.8 in OpenSolaris
date: 2010-04-23 19:29:42
tags: [ tcl, opensolaris ]
alias: installing-tcl-858-on-opensolaris/index.html
---

## Download the source

Download the file tcl8.5.8-src.tar.gz from http://sourceforge.net/projects/tcl/files/

Save this in /src

You may also want to verify the file signature or checksums and read the README or INSTALL files.

## Extract, configure and make

```sh
cd /src
tar xzf tcl8.5.8-src.tar.gz && cd tcl8.5.8/unix
CFLAGS=$( find -L /my -type d -name include -exec echo "-I{} " \; | grep -v "[.][0-9]" | grep -v "pth" | tr -d '\n' ) \
	CPPFLAGS=$( find -L /my -type d -name include -exec echo "-I{} " \; | grep -v "[.][0-9]" | grep -v "pth" | tr -d '\n' ) \
	LDFLAGS=$( find -L /my -type d -name lib -exec echo "-L{} -R{} " \; | grep -v "[.][0-9]" | tr -d '\n' ) \
	./configure --prefix=/my/tcl8.5.8 \
	--enable-threads >>mylog.txt && \
	gmake all >>mylog.txt && \
	gmake test >>mylog.txt && su
gmake install >>mylog.txt
ln -s /my/tcl8.5.8 /my/tcl
exit
echo "export PATH=/my/tcl/bin:\$PATH" >> ~/.profile
```

Congrats! Tcl should be built successfully!
