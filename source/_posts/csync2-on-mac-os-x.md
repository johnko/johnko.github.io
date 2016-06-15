---
title: Building Csync2 on Mac OS X 10.9 and 10.10
date: 2014-10-15
tags: [ csync2, osx ]
---

> I don't use this anymore, but kept the instructions here for future reference

I installed some dependencies from MacPorts:

```sh
port install librsync sqlite2 libtasn1 gnutls pkgconfig
```

I then fetched the csync2 source and verified the hash:

```sh
curl -O http://oss.linbit.com/csync2/csync2-1.34.tar.gz
shasum -a 256 csync2-1.34.tar.gz
```

I found the 1.34 checksum to be:

```
32b250dd4a0353f71015c5c3961174b975dd5e799e4a084e8f6d00792bd8c833  csync2-1.34.tar.gz
```


I then extracted the source code:

```sh
tar -xvf csync2-1.34.tar.gz
```

And patched it:

```sh
curl -O http://www.johnko.ca/scripts/csync2-1.34-darwin.patch
cd csync2-1.34/
patch < ../csync2-1.34-darwin.patch
```

I ran `configure` with some special options:

```sh
./configure --prefix=/opt/local --exec-prefix=/opt/local CC="gcc" CFLAGS="-I/opt/local/include" LDFLAGS="-L/opt/local/lib" LIBGNUTLS_CONFIG="/opt/local/bin/pkg-config gnutls"
```

Then I ran make and make install:

```sh
make
sudo make install
```

I then created the keys:

```sh
umask 077
csync2 -k /etc/csync2.key_mygroup
openssl genrsa -out /etc/csync2_ssl_key.pem 4096
yes '' | openssl req -new -key /etc/csync2_ssl_key.pem -out /etc/csync2_ssl_cert.csr
openssl x509 -req -days 600 -in /etc/csync2_ssl_cert.csr -signkey /etc/csync2_ssl_key.pem -out /etc/csync2_ssl_cert.pem
chown chill /etc/csync2*
```

Then modified the `/etc/hosts` file to avoid DNS poisoning:

```sh
echo 192.168.255.144 csyncbar >> /etc/hosts
```

And configured my `/etc/csync2.cfg`:

```
group mygroup
{
	host chill;
	host (csyncbar);
	key /etc/csync2.key_mygroup;
	include %syncdir%/test;
	auto none;
}

prefix syncdir
{
	on chill:    /data/chill;
	on csyncbar: /cs;
}
```

Make the folders

```sh
install -d -o chill /data/chill/test
install -d -o chill ~/csync2/db
```

Copy over my configuration and PSK:

```sh
rsync -viatP /etc/csync2.key_* root@csyncbar:/etc/
rsync -viatP /etc/csync2*.cfg root@csyncbar:/usr/local/etc/
```

Then still on my Mac, run the standalone server:

```sh
csync2 -D ~/csync2/db -ii -v >~/csync2/server.log 2>&1 &
```

On my FreeBSD machine it was much easier:

```sh
pkg install -y csync2 rsync
echo 192.168.255.100 chill >> /etc/hosts
mkdir /cs/test
echo 'csync2_enable="YES"' >> /etc/rc.conf.local
/usr/local/etc/rc.d/csync2 start
```

# Daily usage

On FreeBSD, to hash and dry-run:

```sh
csync2 -xdv
```

On Mac, to sync files:

```sh
csync2 -D ~/csync2/db -N chill -xv
```

# After making changes to `/etc/csync2.cfg` on Mac:

On Mac:

```sh
rsync -viatP /etc/csync2.key_* root@csyncbar:/etc/
rsync -viatP /etc/csync2*.cfg root@csyncbar:/usr/local/etc/
```

On FreeBSD:

```sh
/usr/local/etc/rc.d/csync2 restart
csync2 -xdv
```

And back on Mac:

```sh
csync2 -D ~/csync2/db -N chill -xv
```
