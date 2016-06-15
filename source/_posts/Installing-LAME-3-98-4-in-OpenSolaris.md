---
title: Installing LAME 3.98.4 in OpenSolaris
date: 2010-04-08 21:36:38
tags: [ audio, lame, media, mp3, opensolaris ]
alias: installing-lame-3984-on-opensolaris/index.html
---

## Download the source

Download the file lame-3.98.4.tar.gz from http://sourceforge.net/projects/lame/files/lame/

Save this in /src

You may also want to verify the file signature or checksums and read the README or INSTALL files.

## Extract, configure and make

```sh
cd /src
tar xzf lame-3.98.4.tar.gz && cd lame-3.98.4
CFLAGS=$( find -L /my -type d -name include -exec echo "-I{} " \; | grep -v "[.][0-9]" | grep -v "pth" | tr -d '\n' ) \
	CPPFLAGS=$( find -L /my -type d -name include -exec echo "-I{} " \; | grep -v "[.][0-9]" | grep -v "pth" | tr -d '\n' ) \
	LDFLAGS=$( find -L /my -type d -name lib -exec echo "-L{} -R{} " \; | grep -v "[.][0-9]" | tr -d '\n' ) \
	./configure --prefix=/my/lame-3.98.4 >>mylog.txt \
	&& gmake all >>mylog.txt && gmake check >>mylog.txt && \
	gmake test >>mylog.txt && su
gmake install >>mylog.txt
ln -s /my/lame-3.98.4 /my/lame
ln -s /my/lame/lib/* /usr/lib/
ln -s /my/lame/include/lame /usr/include/lame
exit
echo "export PATH=/my/lame/bin:\$PATH" >> ~/.profile
```

Congrats! LAME should be built successfully!
