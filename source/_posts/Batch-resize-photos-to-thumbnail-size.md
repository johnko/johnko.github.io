---
title: Batch resize photos to thumbnail size
date: 2010-05-09 19:24:17
tags: [ batch, image, imagemagick, opensolaris, photo, resize ]
alias: batch-resize-photos-to-thumbnail-size/index.html
---

## On OpenSolaris, make sure SUNWimagick is installed

```sh
pfexec pkg install SUNWimagick
```

## Script

This script will resize all photos (ending in JPG) to a 200x150 size thumbnail.
These will overwrite the files in the current folder! Make sure you have a copy of the photos to preserve as original size.

```sh
#!/bin/bash
FN=$( find . -type f -name "*JPG" -exec echo {} \; )
for i in $FN ; do
	echo mogrify -resize 200x150 $i
	mogrify -resize 200x150 $i
done
```
