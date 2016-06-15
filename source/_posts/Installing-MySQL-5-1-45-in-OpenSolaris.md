---
title: Installing MySQL 5.1.45 on OpenSolaris
date: 2010-03-19 14:14:25
tags: [ mysql, sql, opensolaris ]
alias: installing-mysql-5145-on-opensolaris/index.html
---

## Download the source

Download the file mysql-5.1.45.tar.gz from http://dev.mysql.com/downloads/mysql/

Save this in /src

You may also want to verify the file signature or checksums and read the README or INSTALL files.

## Extract, configure and make (on x86)

Via http://forge.mysql.com/w/images/0/01/MySQLUOSDevv1.pdf

We're missing g++, also note that this pkg will replace our linked `/usr/bin/gcc`

```sh
pfexec pkg install gcc-dev
cd /usr/bin
pfexec mv gcc gcc.old
pfexec ln -s gcc-4.3.2 gcc
cd /src
tar xzf mysql-5.1.45.tar.gz && cd mysql-5.1.45
PATH=/usr/sfw/bin:/usr/gnu/bin:$PATH:/usr/ccs/bin
CFLAGS=$( find -L /my -type d -name include -exec echo "-I{} " \; | grep -v "[.][0-9]" | grep -v "pth" | tr -d '\n' ) \
	CPPFLAGS=$( find -L /my -type d -name include -exec echo "-I{} " \; | grep -v "[.][0-9]" | grep -v "pth" | tr -d '\n' ) \
	LDFLAGS=$( find -L /my -type d -name lib -exec echo "-L{} -R{} " \; | grep -v "[.][0-9]" | tr -d '\n' ) \
	CC=gcc CXX=g++ \
	./configure --prefix=/my/mysql-5.1.45 \
	--with-charset=utf8 \
	--with-collation=utf8_general_ci \
	--with-extra-charsets=all \
	--with-zlib-dir=/usr \
	--with-big-tables \
	--with-mysqld-ldflags=-all-static \
	--with-plugins=all >>mylog.txt \
	&& gmake all >>mylog.txt && gmake check >>mylog.txt && su
gmake install >>mylog.txt
ln -s /my/mysql-5.1.45 /my/mysql
exit
echo "export PATH=/my/mysql/bin:\$PATH" >> ~/.profile
exit
```

Congrats! MySQL should be built successfully!
