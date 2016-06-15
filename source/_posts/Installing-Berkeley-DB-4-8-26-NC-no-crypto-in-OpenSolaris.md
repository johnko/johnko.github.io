---
title: Installing Berkeley DB 4.8.26 NC (no crypto) on OpenSolaris
date: 2010-03-11 10:55:43
tags: [ berkeley, db, opensolaris ]
alias: installing-berkeley-db-4826-nc-on-opensolaris/index.html
---

## Download the source

Download the file db-4.8.26.NC.tar.gz from http://www.oracle.com/technology/software/products/berkeley-db/db/index.html

Save this in /src

You may also want to verify the file signature or checksums and read the README or INSTALL files.

## Extract, configure and make

```sh
cd /src
tar xzf db-4.8.26.NC.tar.gz && cd db-4.8.26.NC/build_unix
CFLAGS=$( find -L /my -type d -name include -exec echo "-I{} " \; | grep -v "[.][0-9]" | tr -d '\n' ) \
	LDFLAGS=$( find -L /my -type d -name lib -exec echo "-L{} -R{} " \; | grep -v "[.][0-9]" | tr -d '\n' ) \
	../dist/configure --enable-cxx --enable-java --enable-test \
	--enable-tcl --with-tcl=/usr/lib \
	--prefix=/my/db-4.8.26.NC >>mylog.txt \
	&& gmake all >>mylog.txt && su
gmake install >>mylog.txt
ln -s /my/db-4.8.26.NC /my/db
ln -s /my/db/include/db.h /usr/include/
exit
echo "export PATH=/my/db/bin:\$PATH" >> ~/.profile
```

Congrats! Berkeley DB should be built successfully!
