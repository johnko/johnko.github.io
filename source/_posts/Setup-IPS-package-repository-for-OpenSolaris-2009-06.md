---
title: Setup IPS package repository for OpenSolaris 2009.06
date: 2010-02-27 17:10:05
tags: [ ips, opensolaris, pkg ]
alias: setup-ips-package-repository-for-opensolaris-200906/index.html
---

## Download the repository DVD image

Get the repo image from http://genunix.org.
Download osol-repo-0906-full.iso to /src
I recommend verifying the checksums with md5sums_repo_0906.txt

Take a look at README.osol-repo while you're downloading for more information.

## (Optional) Create a compressed ZFS filesystem

We don't need access time, hence `-o atime=off`.

```sh
pfexec zfs create -o atime=off -o compression=on rpool/repo
```

## Mount the image and copy the repo directory

```sh
pfexec bash
mkdir /mnt/cd
mount -F hsfs /src/osol-repo-0906-full.iso /mnt/cd
mkdir /rpool/repo
rsync -aP /mnt/cd/repo/ /rpool/repo
```

Unmount the iso:

```sh
umount /mnt/cd
```

## (Optional) Set ZFS filesystem as readonly

```sh
pfexec zfs set readonly=on rpool/repo
```

## Configure pkg server

```sh
pfexec bash
svccfg -s pkg/server setprop pkg/inst_root=/rpool/repo
svccfg -s pkg/server setprop pkg/readonly=true
```
Specify a port that will not conflict with another service, see http://www.iana.org/assignments/port-numbers

```sh
svccfg -s pkg/server setprop pkg/port=4
```

Copy the config file:

```sh
cp /rpool/repo/cfg_cache /etc/0906_cfg_cache
svccfg -s pkg/server setprop pkg/cfg_file=/etc/0906_cfg_cache
```

## Edit the server config file

From http://genunix.org/dist/indiana/README.osol-repo:

Then, using a text editor of your choice, change the following line in the above file [/etc/0906_cfg_cache]:

origins = http://pkg.opensolaris.org/release

Replace everything after ' = ', to the end of the line, with the network-accessible hostname of the system that the depot server will be hosted on as follows (if you changed the port number above, you would include it using :, after '.com' in the example below):

I used:

```
origins = http://localhost:4/
```

## Refresh and start the pkg server

```sh
pfexec bash
svcadm refresh pkg/server
svcadm enable pkg/server
```

## Configure your pkg client

```sh
pfexec bash
pkg set-publisher -O http://localhost:4/ opensolaris.org
```

Double check pkg publisher is set:

```sh
pkg publisher
```

No errors? Great! now you can "pkg install" whatever you need from your machine instead of from the net.
