---
title: Installing GnuPG 1.4.10 and 2.0.15 on OpenSolaris
date: 2010-03-17 01:24:29
tags: [ gnupg, pgp, opensolaris ]
alias: installing-gnupg-1410-and-2015-on-opensolaris/index.html
---

## Download the sources

Download the files gnupg-1.4.10.tar.gz, gnupg-2.0.15.tar.bz2, libgcrypt-1.4.5.tar.bz2, libksba-1.0.7.tar.bz2, libgpg-error-1.7.tar.bz2, libassuan-2.0.0.tar.bz2 from http://www.gnupg.org/download/index.en.html and pth-2.0.7.tar.gz from http://www.gnu.org/software/pth/

Save these in /src

You may also want to verify the file signature or checksums and read the README or INSTALL files.

## Extract, configure and make dependencies

### libgpg-error-1.7

```sh
cd /src
tar xjf libgpg-error-1.7.tar.bz2 && cd libgpg-error-1.7
CFLAGS=$( find -L /my -type d -name include -exec echo "-I{} " \; | grep -v "[.][0-9]" | tr -d '\n' ) \
	CPPFLAGS=$( find -L /my -type d -name include -exec echo "-I{} " \; | grep -v "[.][0-9]" | tr -d '\n' ) \
	LDFLAGS=$( find -L /my -type d -name lib -exec echo "-L{} -R{} " \; | grep -v "[.][0-9]" | tr -d '\n' ) \
	./configure --prefix=/my/libgpg-error-1.7 \
	--with-libiconv-prefix=/usr/lib >>mylog.txt \
	&& gmake all >>mylog.txt && gmake check >>mylog.txt && su
gmake install >>mylog.txt
ln -s /my/libgpg-error-1.7 /my/libgpg-error
exit
echo "export PATH=/my/libgpg-error/bin:\$PATH" >> ~/.profile
```

### libksba-1.0.7

```sh
cd /src
tar xjf libksba-1.0.7.tar.bz2 && cd libksba-1.0.7
CFLAGS=$( find -L /my -type d -name include -exec echo "-I{} " \; | grep -v "[.][0-9]" | tr -d '\n' ) \
CPPFLAGS=$( find -L /my -type d -name include -exec echo "-I{} " \; | grep -v "[.][0-9]" | tr -d '\n' ) \
LDFLAGS=$( find -L /my -type d -name lib -exec echo "-L{} -R{} " \; | grep -v "[.][0-9]" | tr -d '\n' ) \
./configure --prefix=/my/libksba-1.0.7 \
	--with-gpg-error-prefix=/my/libgpg-error >>mylog.txt \
	&& gmake all >>mylog.txt && gmake check >>mylog.txt && su
gmake install >>mylog.txt
ln -s /my/libksba-1.0.7 /my/libksba
exit
echo "export PATH=/my/libksba/bin:\$PATH" >> ~/.profile
```

### pth-2.0.7

```sh
cd /src
tar xzf pth-2.0.7.tar.gz && cd pth-2.0.7
CFLAGS=$( find -L /my -type d -name include -exec echo "-I{} " \; | grep -v "[.][0-9]" | tr -d '\n' ) \
CPPFLAGS=$( find -L /my -type d -name include -exec echo "-I{} " \; | grep -v "[.][0-9]" | tr -d '\n' ) \
LDFLAGS=$( find -L /my -type d -name lib -exec echo "-L{} -R{} " \; | grep -v "[.][0-9]" | tr -d '\n' ) \
./configure --prefix=/my/pth-2.0.7 \
	--enable-pthread >>mylog.txt \
	&& gmake all >>mylog.txt && gmake test >>mylog.txt && su
gmake install >>mylog.txt
ln -s /my/pth-2.0.7 /my/pth
mkdir /usr/lib/original
mv /usr/lib/libpthread* /usr/lib/original/
ln -s /my/pth/lib/* /usr/lib/
mv /usr/lib/original/libpthread.so.1 /usr/lib/
exit
echo "export PATH=/my/pth/bin:\$PATH" >> ~/.profile
```

### libassuan-2.0.0

```sh
cd /src
tar xjf libassuan-2.0.0.tar.bz2 && cd libassuan-2.0.0
CFLAGS=$( find -L /my -type d -name include -exec echo "-I{} " \; | grep -v "[.][0-9]" | tr -d '\n' ) \
CPPFLAGS=$( find -L /my -type d -name include -exec echo "-I{} " \; | grep -v "[.][0-9]" | tr -d '\n' ) \
LDFLAGS=$( find -L /my -type d -name lib -exec echo "-L{} -R{} " \; | grep -v "[.][0-9]" | tr -d '\n' ) \
./configure --prefix=/my/libassuan-2.0.0 \
	--with-gpg-error-prefix=/my/libgpg-error >>mylog.txt \
	&& gmake all >>mylog.txt && gmake check >>mylog.txt && su
gmake install >>mylog.txt
ln -s /my/libassuan-2.0.0 /my/libassuan
ln -s /my/libassuan/lib/* /usr/lib/
exit
echo "export PATH=/my/libassuan/bin:\$PATH" >> ~/.profile
```

### libgcrypt-1.4.5

```sh
cd /src
tar xjf libgcrypt-1.4.5.tar.bz2 && cd libgcrypt-1.4.5
CFLAGS=$( find -L /my -type d -name include -exec echo "-I{} " \; | grep -v "[.][0-9]" | tr -d '\n' ) \
CPPFLAGS=$( find -L /my -type d -name include -exec echo "-I{} " \; | grep -v "[.][0-9]" | tr -d '\n' ) \
LDFLAGS=$( find -L /my -type d -name lib -exec echo "-L{} -R{} " \; | grep -v "[.][0-9]" | tr -d '\n' ) \
./configure --prefix=/my/libgcrypt-1.4.5 \
	--with-gpg-error-prefix=/my/libgpg-error \
	--with-pth-prefix=/my/pth --disable-asm >>mylog.txt \
	&& gmake all >>mylog.txt && gmake check >>mylog.txt && su
gmake install >>mylog.txt
ln -s /my/libgcrypt-1.4.5 /my/libgcrypt
mv /usr/lib/libgcrypt* /usr/lib/original/
ln -s /my/libgcrypt/lib/* /usr/lib/
exit
echo "export PATH=/my/libgcrypt/bin:\$PATH" >> ~/.profile
echo "export PATH=/my/libgcrypt/sbin:\$PATH" >> ~/.profile
```

## Extract, configure and make GnuPG 1

```sh
cd /src
tar xzf gnupg-1.4.10.tar.gz && cd gnupg-1.4.10
CFLAGS=$( find -L /my -type d -name include -exec echo "-I{} " \; | grep -v "[.][0-9]" | tr -d '\n' ) \
CPPFLAGS=$( find -L /my -type d -name include -exec echo "-I{} " \; | grep -v "[.][0-9]" | tr -d '\n' ) \
LDFLAGS=$( find -L /my -type d -name lib -exec echo "-L{} -R{} " \; | grep -v "[.][0-9]" | tr -d '\n' ) \
./configure --prefix=/my/gnupg-1.4.10 --enable-threads=solaris \
	--with-tar=usr/gnu/bin --with-ldap=/my/openldap/lib \
	--with-libcurl=/my/curl/lib --with-gnu-ld \
	--with-libpth-prefix=/my/pth/lib \
	--with-libiconv-prefix=/usr/lib/iconv --with-included-gettext \
	--with-included-regex --with-zlib=/usr/lib --with-bzip2=/usr/lib \
	--with-libusb=/usr/lib --with-readline >>mylog.txt \
	&& gmake all >>mylog.txt && gmake check >>mylog.txt && su
gmake install >>mylog.txt
ln -s /my/gnupg-1.4.10 /my/gnupg1
exit
echo "export PATH=/my/gnupg1/bin:\$PATH" >> ~/.profile
```

Congrats! GnuPG 1 should be built successfully!

## Extract, configure and make GnuPG 2

```sh
cd /src
tar xjf gnupg-2.0.15.tar.bz2 && cd gnupg-2.0.15
wget http://src.opensolaris.org/source/raw/sfw/usr/src/lib/libusb/inc/usb.h
CFLAGS=$( find -L /my -type d -name include -exec echo "-I{} " \; | grep -v "[.][0-9]" | tr -d '\n' ) \
CPPFLAGS=$( find -L /my -type d -name include -exec echo "-I{} " \; | grep -v "[.][0-9]" | tr -d '\n' ) \
LDFLAGS=$( find -L /my -type d -name lib -exec echo "-L{} -R{} " \; | grep -v "[.][0-9]" | tr -d '\n' ) \
./configure --prefix=/my/gnupg-2.0.15 --with-tar \
	--with-gpg-error-prefix=/my/libgpg-error \
	--with-libgcrypt-prefix=/my/libgcrypt \
	--with-libassuan-prefix=/my/libassuan \
	--with-ksba-prefix=/my/libksba \
	--with-pth-prefix=/my/pth \
	--with-ldap=/my/openldap \
	--with-libcurl=/my/curl \
	--with-libiconv-prefix=/usr/lib \
	--with-zlib=/usr/lib \
	--with-bzip2=/usr/lib >>mylog.txt \
	&& gmake all >>mylog.txt && gmake check
```

`gmake check` will fail with 3 errors, because it expects gpg-agent to be installed, so let's install it:

```sh
su
gmake install >>mylog.txt
ln -s /my/gnupg-2.0.15 /my/gnupg2
exit
```

Now gmake check will work, but will skip 18 PKITS tests:

```sh
gmake check >>mylog.txt
echo "export PATH=/my/gnupg2/bin:\$PATH" >> ~/.profile
echo "export PATH=/my/gnupg2/sbin:\$PATH" >> ~/.profile
```

Congrats! GnuPG 2 should be built successfully!

## Quick GnuPG Guide

### Generate your private key

```sh
gpg --gen-key
```

### Import key

```sh
gpg --import FILENAME
```

### Verify files

You're looking for "Good signature from..."

```sh
gpg --verify ASC-SIG-FILE
```

### Trust their key

```sh
gpg --edit-key DSA-KEY-ID
trust
save
```

### Sign their key (only do so if you trust them completely)

```sh
gpg --edit-key DSA-KEY-ID
sign
save
```

### Receive keys from keyserver

```sh
gpg --recv-keys --keyserver hkp://subkeys.pgp.net DSA-KEY-ID
```

You can find some keys at http://wwwkeys.pgp.net/ or substitute "subkeys.pgp.net" with "pgpkeys.pca.dfn.de"

More info at http://www.gnupg.org/documentation/howtos.en.html
