---
title: Ripping Audio CD in OpenSolaris
date: 2010-06-12 20:04:18
tags: [ audio, bash, cd, flac, opensolaris, rip, script, wav ]
alias: ripping-audio-cd-in-opensolaris/index.html
---

## Install the SUNWcdrw package, create the filesystem(s)

Open a Terminal and type:

```sh
pfexec pkg install SUNWcdrw
pfexec zfs create -o mountpoint=/music rpool/music
```

## Create a ripCD.sh script

```sh
#!/bin/bash
RCDEV=cdrom0
[ -z "$1" ] && RCDIR=/z/music/$( date +%Y-%m-%d_%H-%M-%S ) || RCDIR=/z/music/"$1"
sudo mkdir -p "$RCDIR"
RCTRK=$( sudo cdrw -d $RCDEV -M | grep "^ [0-9]" | tail -1 | sed 's/ //g' | sed 's/|.*//' )
[ -z $RCTRK ] && sudo eject $RCDEV && exit
for i in $( seq $RCTRK ) ; do
	sudo cdrw -d $RCDEV -x -T wav $i "$RCDIR"/"$( printf %02d $i ).wav"
done
sudo eject $RCDEV
```

## Export your CD playlist to m3u

Sample:

```
#EXTM3U
#EXTINF:,One More Time
cdda://1#/dev/sr0
#EXTINF:,Aerodynamic
cdda://2#/dev/sr0
#EXTINF:,Digital Love
cdda://3#/dev/sr0
#EXTINF:,Harder, Better, Faster, Stronger
cdda://4#/dev/sr0
#EXTINF:,Crescendolls
cdda://5#/dev/sr0
#EXTINF:,Nightvision
cdda://6#/dev/sr0
#EXTINF:,Superheroes
cdda://7#/dev/sr0
#EXTINF:,High Life
cdda://8#/dev/sr0
#EXTINF:,Something About Us
cdda://9#/dev/sr0
#EXTINF:,Voyager
cdda://10#/dev/sr0
#EXTINF:,Veridis Quo
cdda://11#/dev/sr0
#EXTINF:,Short Circuit
cdda://12#/dev/sr0
#EXTINF:,Face to Face
cdda://13#/dev/sr0
#EXTINF:,Too Long
cdda://14#/dev/sr0
```

## Use like so

```sh
chmod u+x ripCD.sh
./ripCD.sh Discovery.m3u cdrom0
```
