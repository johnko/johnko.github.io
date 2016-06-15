---
title: Installing XVid 1.2.2 in OpenSolaris
date: 2010-04-08 20:58:58
tags: [ media, opensolaris, video, xvid ]
alias: installing-xvid-122-on-opensolaris/index.html
---

## Download the source

Download the file xvidcore-1.2.2.tar.gz from http://www.xvid.org/Downloads.43.0.html

Save this in /src

You may also want to verify the file signature or checksums and read the README or INSTALL files.

## Extract, configure and make

```sh
cd /src
tar xzf xvidcore-1.2.2.tar.gz && cd xvidcore/build/generic
CFLAGS=$( find -L /my -type d -name include -exec echo "-I{} " \; | grep -v "[.][0-9]" | grep -v "pth" | tr -d '\n' ) \
	CPPFLAGS=$( find -L /my -type d -name include -exec echo "-I{} " \; | grep -v "[.][0-9]" | grep -v "pth" | tr -d '\n' ) \
	LDFLAGS=$( find -L /my -type d -name lib -exec echo "-L{} -R{} " \; | grep -v "[.][0-9]" | tr -d '\n' ) \
	CC=gcc \
	./configure --prefix=/my/xvidcore-1.2.2 >>mylog.txt
```

Modify line 133 in Makefile, replace "$(CC)" with "/bin/ld":

```sh
vi Makefile
```

Modify line 48 in platform.inc, to read
```
SPECIFIC_LDFLAGS=-h libxvidcore.$(SHARED_EXTENSION).$(API_MAJOR) -B dynamic -shared -M libxvidcore.ld -lc -lm -lpthread
```

```sh
vi platform.inc
```

## Continue gmake

```
gmake all >>mylog.txt && su
gmake install >>mylog.txt
ln -s /my/xvidcore-1.2.2 /my/xvidcore
ln -s libxvidcore.so.4.2 /my/xvidcore-1.2.2/lib/libxvidcore.so
ln -s /my/xvidcore/lib/* /usr/lib/
ln -s /my/xvidcore/include/* /usr/include/
exit
```

Congrats! XVid should be built successfully!
