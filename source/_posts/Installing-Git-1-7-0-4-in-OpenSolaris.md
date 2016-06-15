---
title: Installing Git 1.7.0.4 in OpenSolaris
date: 2010-04-08 21:51:22
tags: [ dev, git, opensolaris ]
alias: installing-git-1704-on-opensolaris/index.html
---

## Download the source

Download the file git-1.7.0.4.tar.gz from http://git-scm.com/download

Save this in /src

You may also want to verify the file signature or checksums and read the README or INSTALL files.

## Extract, configure and make

```sh
cd /src
tar xzf git-1.7.0.4.tar.gz && cd git-1.7.0.4
CFLAGS=$( find -L /my -type d -name include -exec echo "-I{} " \; | grep -v "[.][0-9]" | grep -v "pth" | tr -d '\n' ) \
	CPPFLAGS=$( find -L /my -type d -name include -exec echo "-I{} " \; | grep -v "[.][0-9]" | grep -v "pth" | tr -d '\n' ) \
	LDFLAGS=$( find -L /my -type d -name lib -exec echo "-L{} -R{} " \; | grep -v "[.][0-9]" | grep -v "pth" | tr -d '\n' ) \
	CC=cc
	./configure --prefix=/my/git-1.7.0.4 --with-openssl --with-curl \
	--with-expat --with-tcltk >>mylog.txt && gmake all >>mylog.txt \
	&& su
```

NB: Some "gmake test" fail, should probably be worried

```
gmake install >>mylog.txt
ln -s /my/git-1.7.0.4 /my/git
exit
echo "export PATH=/my/git/bin:\$PATH" >> ~/.profile
```

Congrats! Git should be built successfully!
