---
title: Installing OpenSSL 1.0.0 on OpenSolaris
date: 2010-03-31 08:12:25
tags: [ openssl, ssl, opensolaris ]
alias: installing-openssl-100-on-opensolaris/index.html
---

## Download the source

Download the file openssl-1.0.0.tar.gz from http://www.openssl.org/source/

Save this in /src

You may also want to verify the file signature or checksums and read the README or INSTALL files.

## Extract, configure and make

```sh
cd /src
tar xzf openssl-1.0.0.tar.gz && cd openssl-1.0.0
CFLAGS=$( find -L /my -type d -name include -exec echo "-I{} " \; | grep -v "[.][0-9]" | tr -d '\n' ) \
	LDFLAGS=$( find -L /my -type d -name lib -exec echo "-L{} -R{} " \; | grep -v "[.][0-9]" | tr -d '\n' ) \
	./config threads shared zlib --prefix=/my/openssl-1.0.0 \
	--test-sanity && ./config threads shared zlib \
	--prefix=/my/openssl-1.0.0 >>mylog.txt \
	&& gmake all >>mylog.txt && gmake test >>mylog.txt && su
gmake install >>mylog.txt
ln -s /my/openssl-1.0.0 /my/openssl
ln -s /my/openssl/lib/libssl.so.1.0.0 /usr/lib/
ln -s /my/openssl/lib/libcrypto.so.1.0.0 /usr/lib/
exit
echo "export PATH=/my/openssl/bin:\$PATH" >> ~/.profile
```

Congrats! OpenSSL should be built successfully!

## Extract, configure and make (alternate)

Only perform the following if you have problems with step 3. (Usually when compiling on x86)

```sh
cd /src
tar xzf openssl-1.0.0.tar.gz && cd openssl-1.0.0
wget http://www.openssl.org/~appro/values.c
pfexec ksh -f values.c gcc
CFLAGS=$( find -L /my -type d -name include -exec echo "-I{} " \; | grep -v "[.][0-9]" | tr -d '\n' ) \
LDFLAGS=$( find -L /my -type d -name lib -exec echo "-L{} -R{} " \; | grep -v "[.][0-9]" | tr -d '\n' ) \
./config threads shared zlib --prefix=/my/openssl-1.0.0 \
	--test-sanity && ./config threads shared zlib \
	--prefix=/my/openssl-1.0.0 >>mylog.txt \
	&& gmake all >>mylog.txt && gmake test >>mylog.txt && su
gmake install >>mylog.txt
ln -s /my/openssl-1.0.0 /my/openssl
ln -s /my/openssl/lib/libssl.so.1.0.0 /usr/lib/
ln -s /my/openssl/lib/libcrypto.so.1.0.0 /usr/lib/
exit
echo "export PATH=/my/openssl/bin:\$PATH" >> ~/.profile
```
