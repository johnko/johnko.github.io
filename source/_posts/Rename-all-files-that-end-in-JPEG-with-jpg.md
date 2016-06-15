---
title: Rename all files that end in 'JPEG' with 'jpg'
date: 2010-04-18 03:11:51
tags: [ bash, files, opensolaris, rename, script ]
alias: rename-all-files-that-end-in-jpeg-with-jpg/index.html
---

## Script

This script will save the path and name of all files ending in JPEG in the FN variable.
Then goes through each entry and renames the files (and displays the file it's on)

```sh
#!/bin/bash
FN=$( find /rpool/wav -type f -name "*JPEG" -exec echo {} \; )
for i in $FN ; do
	echo mv $i $( echo $i | sed 's/JPEG/jpg/' )
	mv $i $( echo $i | sed 's/JPEG/jpg/' )
done
```
