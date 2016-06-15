---
title: Installing libtool 2.2.6b on OpenSolaris
date: 2010-03-17 01:47:03
tags: [ libtool, opensolaris ]
alias: installing-libtool-226b-on-opensolaris/index.html
---

## Download the source

Download the file libtool-2.2.6b.tar.gz from http://www.gnu.org/software/libtool/

Save this in /src

You may also want to verify the file signature or checksums and read the README or INSTALL files.

## Extract, configure and make

```sh
cd /src
tar xzf libtool-2.2.6b.tar.gz && cd libtool-2.2.6b
./configure --prefix=/my/libtool-2.2.6b >>mylog.txt \
	&& gmake all >>mylog.txt && gmake check >>mylog.txt && su
gmake install >>mylog.txt
ln -s /my/libtool-2.2.6b /my/libtool

mkdir /usr/bin/my-original
mv /usr/bin/libtool* /usr/bin/my-original/
ln -s /my/libtool/bin/* /usr/bin/

mkdir /usr/include/my-original
mv /usr/include/ltdl.h /usr/include/my-original/
ln -s /my/libtool/include/* /usr/include/

mkdir /usr/lib/my-original
mv /usr/lib/libltdl.so /usr/lib/my-original/
ln -s /my/libtool/lib/* /usr/lib/

exit
```

Congrats! libtool should be built successfully!
