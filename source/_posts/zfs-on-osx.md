---
title: ZFS on OS X 10.10
date: 2014-10-20
tags: [ zfs, openzfs, osx ]
---

> I don't use this anymore, but kept the instructions here for future reference

I ran this to check my disk partitions:

```sh
diskutil list
```

In the output below, you'll notice I have a `mac` partition where OS X 10.10 Yosemite is installed. I made sure I didn't use my whole drive.

After the installation and reboot, I added a ExFAT partition named `tank` using the Disk Utility app. This is the partition I will relabel/erase.

```
/dev/disk0

   #:                       TYPE NAME                    SIZE       IDENTIFIER

   0:      GUID_partition_scheme                        *751.3 GB   disk0

   1:                        EFI EFI                     209.7 MB   disk0s1

   2:                  Apple_HFS mac                     250.0 GB   disk0s2

   3:                 Apple_Boot Recovery HD             650.0 MB   disk0s3

   4:       Microsoft Basic Data tank                    500.4 GB   disk0s4
```

Then I installed `OpenZFS_on_OS_X_1.3.0.dmg` from [OpenZFS On OS X][1]

[1]: https://openzfsonosx.org/

I took note of the `IDENTIFIER` of the partition I want to erase/use as ZFS: in my case `disk0s4` and changed the label to ZFS with:

```sh
diskutil eraseVolume ZFS %noformat% /dev/disk0s4
```

The output looked like this:

```
Started erase on disk0s4 tank
Unmounting disk
Erasing
Finished erase on disk0s4 tank
```

I also verified my disk partitions to make sure:

```sh
diskutil list
```

With the output that looked like this now:

```
/dev/disk0

   #:                       TYPE NAME                    SIZE       IDENTIFIER

   0:      GUID_partition_scheme                        *751.3 GB   disk0

   1:                        EFI EFI                     209.7 MB   disk0s1

   2:                  Apple_HFS mac                     250.0 GB   disk0s2

   3:                 Apple_Boot Recovery HD             650.0 MB   disk0s3

   4:                        ZFS tank                    500.3 GB   disk0s4
```

Then I created a zpool with:

```sh
sudo zpool create tank /dev/disk0s4
```

And to verify it was created:

```sh
zpool status
```

With output:

```
  pool: tank
 state: ONLINE
  scan: none requested
config:

	NAME        STATE     READ WRITE CKSUM
	tank        ONLINE       0     0     0
	  disk0s4   ONLINE       0     0     0

errors: No known data errors
```

You'll notice I didn't use FileVault under my zpool. I've tried using FileVault + ZFS and it was terribly slow. I'm going to attempt to use an encrypted sparse bundle on ZFS.
