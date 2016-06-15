---
title: Disable xscreensaver in OpenSolaris
date: 2010-03-16 01:31:38
tags: [ opensolaris, screensaver ]
alias: disable-xscreensaver/index.html
---

```sh
pfexec bash
```

Is xscreensaver running? (should see 1 output line):

```sh
pgrep -l xscreensaver
```

If so kill it:

```sh
pkill xscreensaver
```

Rename xscreensaver so system can't find it:

```sh
XSSVR=$(which xscreensaver)
mv $XSSVR $XSSVR.original
```

Note: "Lock screen" will be disabled, however the monitor's power save mode is still enabled.

## To manually lock the screen, run:

```sh
xscreensaver.original | xscreensaver-command --activate
```
