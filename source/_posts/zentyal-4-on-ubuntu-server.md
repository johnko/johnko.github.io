---
title: Install Zentyal 4.0 on Ubuntu Server 14.04.1
date: 2014-10-25
tags: [ install, ubuntu, linux, zentyal ]
---

> I don't use this anymore, but kept the instructions here for future reference

I installed a fresh Ubuntu Server 14.04.1 with a static IP address. After logging in to the node, I made sure it was up to date:

```sh
sudo apt-get update
sudo apt-get dist-upgrade
cat /etc/apt/sources.list | grep zentyal    > ~/etc.apt.sources.list.bkp
cat /etc/apt/sources.list | grep -v zentyal > ~/etc.apt.sources.list.new
echo 'deb http://archive.zentyal.org/zentyal 4.0 main' >> ~/etc.apt.sources.list.new
sudo bash -c 'cat ~/etc.apt.sources.list.new > /etc/apt/sources.list'
sudo apt-key adv --keyserver keyserver.ubuntu.com --recv-keys 8E9229F7E23F4777
sudo apt-get update
sudo apt-get install zentyal
```

Then I followed the prompts.

Then rebooted the node for good measure.

```sh
sudo reboot
```

When the system was back online and ready, I was able to connect to the HTTPS port of the node's IP address with my web browser. For example: `https://192.168.0.10:8443`

It's important I used a static IP addresses for server nodes so that it wouldn't change and I could access the web interface without having to find what IP address it changed to. Also each node should have it's own IP address; no two nodes on the same network should have the same IP address at the same time.
