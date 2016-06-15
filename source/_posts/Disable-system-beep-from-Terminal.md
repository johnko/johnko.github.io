---
title: Disable system beep from Terminal
date: 2010-03-16 01:26:15
tags: [ bash, opensolaris, terminal ]
alias: disable-system-beep-from-terminal/index.html
---

From https://opensolaris.org/jive/message.jspa?messageID=219271#219271

```sh
xset -b
```

Or add to your ~/.bashrc:

```sh
echo "xset -b" >>~/.bashrc
```

You may also need to add it to your ~/.profile

```sh
echo "xset -b" >>~/.profile
```

Note: Login-ready beep will still occur. This just disables the beep that occurs when you try to use Tab to auto-complete commands.
