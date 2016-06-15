---
title: Installing Readline 6.1 on OpenSolaris
date: 2010-03-17 02:01:31
tags: [ opensolaris, readline ]
alias: installing-readline-61-on-opensolaris/index.html
---

## Download the source

Download the file readline-6.1.tar.gz from http://tiswww.case.edu/php/chet/readline/rltop.html

Save this in /src

You may also want to verify the file signature or checksums and read the README or INSTALL files.

## Extract, configure and make

```sh
cd /src
tar xzf readline-6.1.tar.gz && cd readline-6.1
CFLAGS=$( find -L /my -type d -name include -exec echo "-I{} " \; | grep -v "[.][0-9]" | tr -d '\n' ) \
	LDFLAGS=$( find -L /my -type d -name lib -exec echo "-L{} -R{} " \; | grep -v "[.][0-9]" | tr -d '\n' ) \
	./configure --prefix=/my/readline-6.1 --with-curses >>mylog.txt \
	&& gmake all >>mylog.txt && su
gmake install >>mylog.txt
ln -s /my/readline-6.1 /my/readline
exit
```

Congrats! Readline should be built successfully!
