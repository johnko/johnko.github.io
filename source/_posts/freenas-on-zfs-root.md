---
title: Running FreeNAS 9.2.1.8 USB Image in Chroot on ZFS Root
date: 2014-10-05
tags: [ install, zfs, freenas, chroot, usb ]
---

> I don't use this anymore, but kept the instructions here for future reference

Create `mfsbsd-mkzfsroot.sh` with these contents:

```sh
#!/bin/sh
# $Id$
#
# this is mmatuska's zfsinstall without EXTRACT, URL, ARCHIVE and zpool.cache
#
# mfsBSD ZFS install script
# Copyright (c) 2011-2013 Martin Matuska <mm at FreeBSD.org>
#
FS_LIST="var tmp"

usage() {
	echo "Usage: $0 [-h] -d geom_provider [-d geom_provider ...] [-r mirror|raidz] [-m mount_point] [-p zfs_pool_name] [-V zfs_pool_version] [-s swap_partition_size] [-z zfs_partition_size] [-c] [-l] [-4]"
}

help() {
	echo; echo "Install FreeBSD using ZFS from a compressed archive"
	echo; echo "Required flags:"
	echo "-d geom_provider  : geom provider(s) to install to (e.g. da0)"
	echo; echo "Optional flags:"
	echo "-r raidz|mirror   : select raid mode if more than one -d provider given"
	echo "-s swap_part_size : create a swap partition with given size (default: no swap)"
	echo "-z zfs_part_size  : create zfs parition of this size (default: all space left)"
	echo "-p pool_name      : specify a name for the ZFS pool (default: tank)"
	echo "-V pool_version   : specify a version number for ZFS pool (default: 13)"
	echo "-m mount_point    : use this mount point for operations (default: /mnt)"
	echo "-c                : enable lzjb compression for all datasets"
	echo "-l                : use legacy mounts (via fstab) instead of ZFS mounts"
	echo "-4                : use fletcher4 as default checksum algorithm"
	echo; echo "Examples:"
	echo "Install on a single drive with 2GB swap:"
	echo "$0 -u /path/to/release -d da0 -s 2G"
	echo "Install on a mirror without swap, pool name rpool:"
	echo "$0 -u /path/to/release -d da0 -d da1 -r mirror -p rpool"
	echo; echo "Notes:"
	echo "When using swap and raidz/mirror, the swap partition is created on all drives."
	echo "The /etc/fstab entry will contatin only the first drive's swap partition."
	echo "You can enable all swap partitions and/or make a gmirror-ed swap later."
}

while getopts d:u:t:r:p:s:z:m:V:hcl4 o; do
	case "$o" in
        	d) DEVS="$DEVS ${OPTARG##/dev/}" ;;
		p) POOL="${OPTARG}" ;;
		s) SWAP="${OPTARG}" ;;
		m) MNT="${OPTARG}" ;;
		r) RAID="${OPTARG}" ;;
		z) ZPART="${OPTARG}" ;;
		V) VERSION="${OPTARG}" ;;
		c) LZJB=1 ;;
		l) LEGACY=1 ;;
		4) FLETCHER=1 ;;
		h) help; exit 1;;
		[?]) usage; exit 1;;
esac
done

if ! `/sbin/kldstat -m zfs >/dev/null 2>/dev/null`; then
	/sbin/kldload zfs >/dev/null 2>/dev/null
fi

ZFS_VERSION=`/sbin/sysctl -n vfs.zfs.version.spa 2>/dev/null`

if [ -z "$ZFS_VERSION" ]; then
        echo "Error: failed to load ZFS module"
        exit 1
elif [ "$ZFS_VERSION" -lt "13" ]; then
	echo "Error: ZFS module too old, version 13 or higher required"
	exit 1
fi

if [ -z "$DEVS" ]; then
	usage
	exit 1
fi

if [ -z "$POOL" ]; then
	POOL=tank
fi

if [ -z "$VERSION" ]; then
	VERSION=${ZFS_VERSION}
elif [ "$VERSION" -gt "$ZFS_VERSION" ]; then
	echo "Error: invalid ZFS pool version (maximum: $ZFS_VERSION)"
	exit 1
fi

if [ "$VERSION" = "5000" ]; then
	VERSION=
else
	VERSION="-o version=${VERSION}"
fi

if /sbin/zpool list $POOL > /dev/null 2> /dev/null; then
	echo Error: ZFS pool \"$POOL\" already exists
	echo Please choose another pool name or rename/destroy the existing pool.
	exit 1
fi

EXPOOLS=`/sbin/zpool import | /usr/bin/grep pool: | /usr/bin/awk '{ print $2 }'`

if [ -n "${EXPOOLS}" ]; then
	for P in ${EXPOOLS}; do
		if [ "$P" = "$POOL" ]; then
			echo Error: An exported ZFS pool \"$POOL\" already exists
			echo Please choose another pool name or rename/destroy the exported pool.
			exit 1
		fi
	done
fi

COUNT=`echo ${DEVS} | /usr/bin/wc -w | /usr/bin/awk '{ print $1 }'`
if [ "$COUNT" -lt "3" -a "$RAID" = "raidz" ]; then
	echo "Error: raidz needs at least three devices (-d switch)"
	exit 1
elif [ "$COUNT" = "1" -a "$RAID" = "mirror" ]; then
	echo "Error: mirror needs at least two devices (-d switch)"
	exit 1
elif [ "$COUNT" = "2" -a "$RAID" != "mirror" ]; then
	echo "Notice: two drives selected, automatically choosing mirror mode"
	RAID="mirror"
elif [ "$COUNT" -gt "2" -a "$RAID" != "mirror" -a "$RAID" != "raidz" ]; then
	echo "Error: please choose raid mode with the -r switch (mirror or raidz)"
	exit 1
fi

for DEV in ${DEVS}; do
	if ! [ -c "/dev/${DEV}" ]; then
		echo "Error: /dev/${DEV} is not a block device"
		exit 1
	fi
	if /sbin/gpart show $DEV > /dev/null 2> /dev/null; then
		echo "Error: /dev/${DEV} already contains a partition table."
		echo ""
		/sbin/gpart show $DEV
		echo "You may erase the partition table manually with the destroygeom command"
		exit 1
	fi
done

if [ -z "$MNT" ]; then
	MNT=/mnt
fi

if ! [ -d "${MNT}" ]; then
	echo "Error: $MNT is not a directory"
	exit 1
fi

if [ -n "${ZPART}" ]; then
	SZPART="-s ${ZPART}"
fi

if [ "${LEGACY}" = "1" ]; then
	ALTROOT=
	ROOTMNT=legacy
else
	ALTROOT="-o altroot=${MNT}"
	ROOTMNT=/
fi

# Create GPT

for DEV in ${DEVS}; do
	echo -n "Creating GUID partitions on ${DEV} ..."
	if ! /sbin/gpart create -s GPT /dev/${DEV} > /dev/null; then
		echo " error"
		exit 1
	fi
	/bin/sleep 1
	if ! echo "a 1" | /sbin/fdisk -f - ${DEV} > /dev/null 2> /dev/null; then
		echo " error"
		exit 1
	fi
	if ! /sbin/gpart add -t freebsd-boot -s 128 ${DEV} > /dev/null; then
		echo " error"
		exit 1
	fi
	if [ -n "${SWAP}" ]; then
		if ! /sbin/gpart add -t freebsd-swap -s "${SWAP}" ${DEV} > /dev/null; then
			echo " error"
			exit 1
		fi
		SWAPPART=`/sbin/glabel status ${DEV}p2 | /usr/bin/grep gptid | /usr/bin/awk '{ print $1 }'`
		if [ -z "$SWAPPART" ]; then
			echo " error determining swap partition"
		fi
		if [ -z "$FSWAP" ]; then
			FSWAP=${SWAPPART}
		fi
	fi
	if ! /sbin/gpart add -t freebsd-zfs ${SZPART} ${DEV} > /dev/null; then
		echo " error"
		exit 1
	fi
	/bin/dd if=/dev/zero of=/dev/${DEV}p2 bs=512 count=560 > /dev/null 2> /dev/null
	if [ -n "${SWAP}" ]; then
		/bin/dd if=/dev/zero of=/dev/${DEV}p3 bs=512 count=560 > /dev/null 2> /dev/null
	fi
	echo " done"

	echo -n "Configuring ZFS bootcode on ${DEV} ..."
		if ! /sbin/gpart bootcode -b /boot/pmbr -p /boot/gptzfsboot -i 1 ${DEV} > /dev/null; then
		echo " error"
		exit 1
	fi
	echo " done"
	/sbin/gpart show ${DEV}
done

# Create zpool and zfs

for DEV in ${DEVS}; do
	PART=`/sbin/gpart show ${DEV} | /usr/bin/grep freebsd-zfs | /usr/bin/awk '{ print $3 }'`

	if [ -z "${PART}" ]; then
		echo Error: freebsd-zfs partition not found on /dev/$DEV
		exit 1
	fi

	GPART=`/sbin/glabel list ${DEV}p${PART} | /usr/bin/grep gptid | /usr/bin/awk -F"gptid/" '{ print "gptid/" $2 }'`

	GPARTS="${GPARTS} ${GPART}"
	PARTS="${PARTS} ${DEV}p${PART}"
done

echo -n "Creating ZFS pool ${POOL} on${PARTS} ..."
if ! /sbin/zpool create -f -m none ${ALTROOT} ${VERSION} ${POOL} ${RAID} ${PARTS} > /dev/null 2> /dev/null; then
	echo " error"
	exit 1
fi
echo " done"

if [ "${FLETCHER}" = "1" ]; then
	echo -n "Setting default checksum to fletcher4 for ${POOL} ..."
	if ! /sbin/zfs set checksum=fletcher4 ${POOL} > /dev/null 2> /dev/null; then
		echo " error"
		exit 1
	fi
	echo " done"
fi

if [ "${LZJB}" = "1" ]; then
	echo -n "Setting default compression to lzjb for ${POOL} ..."
	if ! /sbin/zfs set compression=lzjb ${POOL} > /dev/null 2> /dev/null; then
		echo " error"
		exit 1
	fi
	echo " done"
fi

echo -n "Creating ${POOL} root partition:"
if ! /sbin/zfs create -o mountpoint=${ROOTMNT} ${POOL}/root > /dev/null 2> /dev/null; then
	echo " error"
	exit 1
fi
echo " ... done"
echo -n "Creating ${POOL} partitions:"
for FS in ${FS_LIST}; do
	if [ "${LEGACY}" = 1 ]; then
		MNTPT="-o mountpoint=legacy"
	else
		MNTPT=
	fi
	if ! /sbin/zfs create ${MNTPT} ${POOL}/root/${FS} > /dev/null 2> /dev/null; then
		echo " error"
		exit 1
	fi
	echo -n " ${FS}"
done
echo " ... done"
echo -n "Setting bootfs for ${POOL} to ${POOL}/root ..."
if ! /sbin/zpool set bootfs=${POOL}/root ${POOL} > /dev/null 2> /dev/null; then
	echo " error"
	exit 1
fi
echo " done"
/sbin/zfs list -r ${POOL}

# Mount and populate zfs (if legacy)
if [ "${LEGACY}" = "1" ]; then
	echo -n "Mounting ${POOL} on ${MNT} ..."
	/bin/mkdir -p ${MNT}
	if ! /sbin/mount -t zfs ${POOL}/root ${MNT} > /dev/null 2> /dev/null; then
		echo " error mounting pool/root"
		exit 1
	fi
	for FS in ${FS_LIST}; do
		/bin/mkdir -p ${MNT}/${FS}
		if ! /sbin/mount -t zfs ${POOL}/root/${FS} ${MNT}/${FS} > /dev/null 2> /dev/null; then
			echo " error mounting ${POOL}/root/${FS}"
			exit 1
		fi
	done
echo " done"
fi

# Adjust configuration files

echo -n "Writing /boot/loader.conf..."
install -d -m 755 ${MNT}/boot
echo "zfs_load=\"YES\"" > ${MNT}/boot/loader.conf
echo "vfs.root.mountfrom=\"zfs:${POOL}/root\"" >> ${MNT}/boot/loader.conf
echo " done"

# Write fstab if swap or legacy
echo -n "Writing /etc/fstab..."
install -d -m 755 ${MNT}/etc
rm -f ${MNT}/etc/fstab
touch ${MNT}/etc/fstab
if [ -n "${FSWAP}" -o "${LEGACY}" = "1" ]; then
	if [ -n "${FSWAP}" ]; then
		echo "/dev/${FSWAP} none swap sw 0 0" > ${MNT}/etc/fstab
	fi
	if [ "${LEGACY}" = "1" ]; then
		for FS in ${FS_LIST}; do
			echo ${POOL}/root/${FS} /${FS} zfs rw 0 0 >> ${MNT}/etc/fstab
		done
	fi
fi
if [ "${LEGACY}" != "1" ]; then
	echo -n "Writing /etc/rc.conf..."
	echo 'zfs_enable="YES"' >> ${MNT}/etc/rc.conf
fi
echo " done"

if [ -n "${LEGACY}" ]; then
	for FS in ${FS_LIST}; do
		/sbin/umount ${MNT}/${FS} > /dev/null 2> /dev/null
	done
	/sbin/umount ${MNT} > /dev/null 2> /dev/null
fi
if ! /sbin/zpool export ${POOL} > /dev/null 2> /dev/null; then
	echo " error exporting pool"
	exit 1
fi
if ! /sbin/zpool import ${ALTROOT} ${POOL} > /dev/null 2> /dev/null; then
	echo " error importing pool"
	exit 1
fi
if [ -n "${LEGACY}" ]; then
	if ! /sbin/mount -t zfs ${POOL}/root ${MNT} > /dev/null 2> /dev/null; then
		echo " error mounting ${POOL}/root"
		exit 1
	fi
fi
if [ -n "${LEGACY}" ]; then
	for FS in ${FS_LIST}; do
		if ! /sbin/mount -t zfs ${POOL}/${FS} ${MNT}/${FS} > /dev/null 2> /dev/null; then
		echo " error mounting ${POOL}/${FS}"
		exit 1
		fi
	done
fi
echo " done"

echo "WARNING - Don't export ZFS pool \"${POOL}\"!"
```

I boot from my LiveISO-USB, then

```sh
echo "192.168.255.100 chill.home.local" >> /etc/hosts
DRIVE=ada0
ZFSCOPIES=2
IMAGE=FreeNAS-9.2.1.8-RELEASE-x64.img

# Destroy drive
#destroygeom -d ${DRIVE}
if gpart show ${DRIVE} > /dev/null 2> /dev/null ; then
	echo "ERROR: ${DRIVE} is not empty. Wipe with: destroygeom -d ${DRIVE}" >&2
	exit 1
fi

# Use modified zfsinstall script (mfsbsd-mkzfsroot.sh) from above
chmod a+x mfsbsd-mkzfsroot.sh
./mfsbsd-mkzfsroot.sh -d ${DRIVE} -p autoroot
zfs destroy autoroot/root/tmp
zfs destroy autoroot/root/var
rm -r /mnt/etc
zfs set copies=${ZFSCOPIES} autoroot

# Fetch FreeNAS USB running image
if [ ! -e /mnt/${IMAGE} ]; then
	rsync -vitP chill@chill.home.local:~/Downloads/freebsd/freenas/${IMAGE} /mnt/
fi

# Mount LiveISO-USB
mkdir /liveusb
mkdir /cdrom
if [ ! -e /dev/da0s1 ]; then
	echo "ERROR: LiveISO-USB device not found!" >&2
	exit 1
fi
mount_msdosfs -o ro /dev/da0s1 /liveusb
if [ ! -e /liveusb/boot/iso/mfsbsd-se-10.0-RELEASE-amd64.iso ]; then
	echo "ERROR: mfsBSD ISO not found!" >&2
	exit 1
fi

# Mount mfsBSD ISO
mfsiso=$( mdconfig -a -t vnode -f /liveusb/boot/iso/mfsbsd-se-10.0-RELEASE-amd64.iso )
mount_cd9660 /dev/${mfsiso} /cdrom

# Copy /boot
tar -x -C /mnt --include boot -f /cdrom/10.0-RELEASE-amd64/kernel.txz
rm /mnt/boot/kernel/kernel
rm /mnt/boot/kernel/*symbols
rsync -viaP /boot/ /mnt/boot/
rsync -viaP --exclude 10.0-RELEASE-amd64 /cdrom/ /mnt/

# Modify /mnt/boot/loader.conf
cat >>/mnt/boot/loader.conf <<EOF
zfs_load="YES"
hw.bge.allow_asf="0"
hw.usb.no_shutdown_wait="1"
vfs.zfs.arc_max="256000000"
EOF
grep -v 'mfsbsd.autodhcp' /mnt/boot/loader.conf > bootloader.conf
cat bootloader.conf > /mnt/boot/loader.conf
mv /mnt/boot /mnt/boot2

# First mount of FreeNAS running image
freenas=$( mdconfig -a -t vnode -f /mnt/${IMAGE} )
mkdir /freenas
mount /dev/ufs/FreeNASs1a /freenas
rm /freenas/etc/rc.d/mountlate 2> /dev/null
rm /freenas/conf/base/etc/rc.d/mountlate 2> /dev/null

# Copy FreeNAS /freenas/boot to /mnt/boot
rsync -viaP /freenas/boot/ /mnt/boot/
rsync -viaP /freenas/usr/ /mnt/usr/

# Merge mfsBSD and FreeNAS /mnt/boot/loader.conf
cat /mnt/boot/loader.conf >> /mnt/boot2/loader.conf
cat /mnt/boot2/loader.conf > /mnt/boot/loader.conf
rm -r /mnt/boot2

# Modify /mnt/boot/loader.conf
grep -v 'module_path' /mnt/boot/loader.conf > bootloader.conf
cat bootloader.conf > /mnt/boot/loader.conf
cat >>/mnt/boot/loader.conf <<EOF
module_path="/boot/kernel;/boot/modules;/usr/local/modules;/mnt/boot/kernel;/mnt/boot/modules;/mnt/usr/local/modules"
EOF

# Mount mfsroot
cd /mnt
gunzip mfsroot.gz
mfsroot=$( mdconfig -a -t vnode -f /mnt/mfsroot )
mkdir /mfsroot
mount /dev/${mfsroot} /mfsroot

# Modify rc.local
mkdir /testroot
tar -x -C /testroot/ -f /mfsroot/root.txz
cat >>/testroot/rw/etc/rc.local <<EOF

# Import autoroot and start freenas chroot script
zpool status autoroot || zpool import -f -R /mnt autoroot
sh /mnt/freenas.sh
EOF
chmod a+x /testroot/rw/etc/rc.local
cd /testroot
tar -cf - ./ | xz - > /mfsroot/root.txz
umount /mfsroot
mdconfig -d -u ${mfsroot}

# freenas chroot script
cat >/mnt/freenas.sh <<EOF
#!/bin/sh
freenas=\$( mdconfig -a -t vnode -f /mnt/${IMAGE} )
mkdir /freenas
mount /dev/ufs/FreeNASs1a /freenas
mount /dev/ufs/FreeNASs3 /freenas/cfg
mount /dev/ufs/FreeNASs4 /freenas/data
mount -t devfs devfs /freenas/dev
# Link these for mfsbsd's roothack init
test -e /freenas/freenas || ln -s / /freenas/freenas
test -e /freenas/rw || ln -s / /freenas/rw
# Start FreeNAS rc
chroot /freenas sh /etc/rc
EOF

reboot
```

And unplug my LiveISO-USB!
