---
title: Alias 'ls' and 'll' with color
date: 2010-03-16 01:27:44
tags: [ bash, opensolaris, terminal ]
alias: alias-ls-and-ll-with-color/index.html
---

Modified from http://www.linuxguy.in/bashrc-on-opensolaris/

```sh
echo "alias ls='/usr/gnu/bin/ls -hAF --color    --group-directories-first'" >> ~/.bashrc
echo "alias ll='/usr/gnu/bin/ls -hAlF --color   --group-directories-first'" >> ~/.bashrc
echo "alias lsx='/usr/gnu/bin/ls -hAFX --color  --group-directories-first'" >> ~/.bashrc
echo "alias llx='/usr/gnu/bin/ls -hAlFX --color --group-directories-first'" >> ~/.bashrc
```

You may also need to add it to your ~/.profile

```sh
echo "alias ls='/usr/gnu/bin/ls -hAF --color    --group-directories-first'" >> ~/.profile
echo "alias ll='/usr/gnu/bin/ls -hAlF --color   --group-directories-first'" >> ~/.profile
echo "alias lsx='/usr/gnu/bin/ls -hAFX --color  --group-directories-first'" >> ~/.profile
echo "alias llx='/usr/gnu/bin/ls -hAlFX --color --group-directories-first'" >> ~/.profile
```

Then relogin.
