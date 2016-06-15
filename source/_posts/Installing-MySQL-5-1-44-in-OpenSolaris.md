---
title: Installing MySQL 5.1.44 on OpenSolaris
date: 2010-03-19 03:59:11
tags: [ mysql, opensolaris, sql ]
alias: installing-mysql-5144-on-opensolaris/index.html
---

## Download the source

Download the file mysql-5.1.44.tar.gz from http://dev.mysql.com/downloads/mysql/

Save this in /src

You may also want to verify the file signature or checksums and read the README or INSTALL files.

## Extract, configure and make

```sh
cd /src
tar xzf mysql-5.1.44.tar.gz && cd mysql-5.1.44
 # remove "amd64" and "-m64" to compile on 32bit systems
PATH=/usr/sfw/bin:/usr/ccs/bin:$PATH
CPPFLAGS="-m64" \
	LDFLAGS="-L/usr/lib/amd64 -R/usr/lib/amd64 -m64" \
	CFLAGS="-O3 -m64" \
	CXXFLAGS="-O3 -felide-constructors -fno-exceptions -fno-rtti -m64" \
	CC=gcc CXX=gcc \
	./configure --prefix=/my/mysql-5.1.44 \
	--with-charset=utf8 \
	--with-collation=utf8_general_ci \
	--with-extra-charsets=all \
	--with-zlib-dir=/usr \
	--with-pthread \
	--with-big-tables \
	--with-mysqld-ldflags=-all-static \
	--with-plugins=max >>mylog.txt \
	&& gmake all >>mylog.txt && gmake check >>mylog.txt && gmake test >>mylog.txt
```

"mysql_client_test" test fails, doesn't seem urgent according to http://bugs.mysql.com/bug.php?id=45073

```sh
su
gmake install >>mylog.txt
ln -s /my/mysql-5.1.44 /my/mysql
```

Congrats! MySQL should be built successfully!
