---
title: Installing curl 7.20.0 on OpenSolaris
date: 2010-03-11 12:47:39
tags: [ curl, opensolaris ]
alias: installing-curl-7200-on-opensolaris/index.html
---

## Download the source

Download the file curl-7.20.0.tar.gz from http://curl.haxx.se/download.html

Save this in /src

You may also want to verify the file signature or checksums and read the README or INSTALL files.

## Extract, configure and make

```sh
cd /src
tar xzf curl-7.20.0.tar.gz && cd curl-7.20.0
CFLAGS=$( find -L /my -type d -name include -exec echo "-I{} " \; | grep -v "[.][0-9]" | tr -d '\n' ) \
	CPPFLAGS=$( find -L /my -type d -name include -exec echo "-I{} " \; | grep -v "[.][0-9]" | tr -d '\n' ) \
	LDFLAGS=$( find -L /my -type d -name lib -exec echo "-L{} -R{} " \; | grep -v "[.][0-9]" | tr -d '\n' ) \
	CC=cc \
	./configure --prefix=/my/curl-7.20.0 --enable-http --enable-ftp \
	--enable-file --enable-ldap --enable-ldaps --enable-rtsp \
	--enable-proxy --enable-dict --enable-telnet --enable-tftp \
	--enable-pop3 --enable-imap --enable-smtp --enable-manual \
	--enable-ipv6 --enable-nonblocking --enable-sspi \
	--enable-crypto-auth --enable-cookies --enable-thread \
	--with-ssl=/my/openssl --with-zlib \
	--with-libssh2=/my/libssh2 >>mylog.txt \
	&& gmake all >>mylog.txt && gmake test >>mylog.txt
# TESTDONE: 477 tests out of 478 reported OK: 99%
# TESTFAIL: These test cases failed: 1004
su
gmake install >>mylog.txt
ln -s /my/curl-7.20.0 /my/curl
exit
echo "export PATH=/my/curl/bin:\$PATH" >> ~/.profile
```

Congrats! curl should be built successfully!
