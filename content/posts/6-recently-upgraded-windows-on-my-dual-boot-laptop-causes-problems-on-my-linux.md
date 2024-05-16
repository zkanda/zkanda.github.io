+++
author = "Zakatell Kanda"
categories = ["archlinux", "bootloader", "windows"]
date = 2017-01-30T05:35:54Z
description = ""
draft = false
showDate = true
slug = "recently-upgraded-windows-on-my-dual-boot-laptop-causes-problems-on-my-linux"
tags = ["archlinux", "bootloader", "windows"]
title = "upgraded windows on my dual boot laptop causes problems on bootloader."

+++

So I upgraded my Windows partition to latest insider preview and it kind of messed up my laptop.

Here's the error message:

```sh
EFI stub: ERROR: failed to read file.
Trying to load files to higher address
EFI stub: ERROR: failed to read file.
```

Here's the step I did to resolved it.

1. Boot in an ArchLinux liveusb.
2. Mount your root and boot partition.
3. Chroot into it.
4. And reinstall the bootloader.

```sh
mkinitcpio -p linux
bootctl install
```

Reboot and it should be good now.
