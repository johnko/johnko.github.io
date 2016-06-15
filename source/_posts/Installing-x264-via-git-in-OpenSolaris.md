---
title: Installing x264 (via git) in OpenSolaris
date: 2010-04-11 23:58:34
tags: [ media, opensolaris, video, x264 ]
alias: installing-x264-via-git-on-opensolaris/index.html
---

## Clone, configure and make

```sh
cd /src
git clone git://git.videolan.org/x264.git
cd x264
CFLAGS=$( find -L /my -type d -name include -exec echo "-I{} " \; | grep -v "[.][0-9]" | tr -d '\n' ) \
	CPPFLAGS=$( find -L /my -type d -name include -exec echo "-I{} " \; | grep -v "[.][0-9]" | tr -d '\n' ) \
	LDFLAGS=$( find -L /my -type d -name lib -exec echo "-L{} -R{} " \; | grep -v "[.][0-9]" | tr -d '\n' ) \
	./configure --prefix=/my/x264-2010.04.23 \
	--disable-asm \
	--extra-ldflags="-L/my/pth/lib -R/my/pth/lib" >>mylog.txt \
	&& gmake all >>mylog.txt && su
```

`gmake install` kinda doesn't copy... so we manually install:

```sh
mkdir -p /my/x264-2010.04.23/include
mkdir -p /my/x264-2010.04.23/lib/pkgconfig
mkdir -p /my/x264-2010.04.23/bin
cp x264.h /my/x264-2010.04.23/include/
cp libx264.a /my/x264-2010.04.23/lib/
cp x264.pc /my/x264-2010.04.23/lib/pkgconfig/
cp x264 /my/x264-2010.04.23/bin/
```

`gmake install` should now run with no errors:

```sh
gmake install >>mylog.txt
ln -s /my/x264-2010.04.23 /my/x264
ln -s /my/x264/lib/* /usr/lib/
ln -s /my/x264/lib/pkgconfig/* /usr/lib/pkgconfig/
ln -s /my/x264/include/* /usr/include/
exit
echo "export PATH=/my/x264/bin:\$PATH" >> ~/.profile
```
