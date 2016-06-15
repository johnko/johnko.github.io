---
title: Installing PostgreSQL 8.4.3 in OpenSolaris
date: 2010-04-26 02:27:23
tags: [ database, opensolaris, pgsql, postgresql ]
alias: installing-postgresql-843-on-opensolaris/index.html
---

## Download the source

Download the file postgresql-8.4.3.tar.gz from http://www.postgresql.org/ftp/source/

Save this in /src

You may also want to verify the file signature or checksums and read the README or INSTALL files.

## Extract, configure and make

```sh
cd /src
tar xzf postgresql-8.4.3.tar.gz && cd postgresql-8.4.3
PATH=/usr/sfw/bin:/usr/gnu/bin:$PATH:/usr/ccs/bin \
	CFLAGS=$( find -L /my -type d -name include -exec echo "-I{} " \; | grep -v "[.][0-9]" | grep -v "pth" | tr -d '\n' ) \
	CPPFLAGS=$( find -L /my -type d -name include -exec echo "-I{} " \; | grep -v "[.][0-9]" | grep -v "pth" | tr -d '\n' ) \
	LDFLAGS=$( find -L /my -type d -name lib -exec echo "-L{} -R{} " \; | grep -v "[.][0-9]" | tr -d '\n' ) \
	LD_OPTIONS="-M /usr/lib/ld/map.noexstk" \
	CC=gcc \
	./configure --prefix=/my/postgresql-8.4.3 \
	--enable-nls \
	--with-system-tzdata=/usr/share/lib/zoneinfo \
	--with-tcl --with-tclconfig=/my/tcl/lib \
	--with-krb5 --with-python --with-gssapi \
	--with-pam --with-ldap --with-openssl \
	--with-libxml --with-libxslt \
	--with-includes=/usr/include:/usr/sfw/include:/usr/include/kerberosv5 \
	--with-libs=/usr/lib:/usr/sfw/lib >>mylog.txt && gmake all >>mylog.txt && su
gmake install >>mylog.txt
ln -s /my/postgresql-8.4.3 /my/postgresql
exit
echo "export PATH=/my/postgresql/bin:\$PATH" >> ~/.profile
```

Congrats! PostgreSQL should be built successfully!
