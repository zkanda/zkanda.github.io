+++
author = "Zakatell Kanda"
categories = ["archlinux", "kernel", "compile", "linux", "technology"]
date = 2016-06-17T01:19:50Z
description = ""
draft = false
image = "/images/2016/06/compiling-linux-4-6.png"
slug = "compiling-the-latest-beta-kernel-in-archlinux"
tags = ["archlinux", "kernel", "compile", "linux", "technology"]
title = "compiling a beta kernel in archlinux"
+++

You have a new laptop, you install ArchLinux on it and seems like some things are missing. This is currently the case for me:

* one missing feature was suspend, my laptop just seems to boot as if I turned off everything, my application is not persisted
* another is a fix for my headphone noise issue, just to name a few...

One of the few things you can try is to install the latest beta kernel. In my case it's Linux 4.6. This fix all of the issued I outlined above.

With that said, you need to err on the side of caution, this can make your computer not boot, so please have an CD/Flash drive to boot from in case something is wrong so you can revert back.

All of these process is actually not so bad, so what do you need first.

1. Get the package build: `git clone --depth 1 git://git.archlinux.org/svntogit/packages.git linux-packages`
2. `cd linux-packages/linux/trunk`
3. `makepkg -s` - This will take some time, so get you coffee or do something to make use of your time.
4. `pacman -U *.pkg.tar`

Reboot your system and voila.

There is also [linux-mainline aur](https://aur.archlinux.org/packages/linux-mainline/) to automatically do it for you. However at the time of the writing, it's already on `4.7rc1` and it's really bleeding edge and `4.6` seems to be enough for now.
