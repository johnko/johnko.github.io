---
title: Installing OpenSSL 0.9.8m on OpenSolaris
date: 2010-03-11 11:13:06
tags: [ opensolaris, openssl, ssl ]
alias: installing-openssl-098m-on-opensolaris/index.html
---

## Download the source

Download the file openssl-0.9.8m.tar.gz from http://www.openssl.org/source/

Save this in /src

You may also want to verify the file signature or checksums and read the README or INSTALL files.

## Extract, configure and make

```sh
cd /src
tar xzf openssl-0.9.8m.tar.gz && cd openssl-0.9.8m
./config threads shared zlib --prefix=/my/openssl-0.9.8m \
	--test-sanity >>mylog.txt \
	&& ./config threads shared zlib \
	--prefix=/my/openssl-0.9.8m >>mylog.txt \
	&& gmake all >>mylog.txt && gmake test >>mylog.txt && su
gmake install >>mylog.txt
ln -s /my/openssl-0.9.8m /my/openssl
```

Congrats! OpenSSL should be built successfully!
