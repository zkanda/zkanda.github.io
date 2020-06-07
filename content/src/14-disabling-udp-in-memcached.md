+++
author = "Zakatell Kanda"
categories = ["software", "devops", "technology"]
date = 2018-03-07T18:54:46Z
description = ""
draft = false
showDate = true
slug = "disabling-udp-in-memcached"
tags = ["software", "devops", "technology"]
title = "disabling udp in memcached."

+++

One of our server provider which is running in Germany have a bot that scans for an open port, this is to prevent other humans/robot in abusing services that are open on the internet.

For us, they determined that our memcached port is open, we were confused at first how could this happen, we were sure that this is blocked via iptables.

It turns out memcached by default listens to both TCP and UDP, since we don't have *yet* a firewall for UDP, we opted for something simpler and to disable UDP on memcached(we know we only use TCP).

Here's how to do it, add this line in `/etc/memcached.conf`

```txt
# Disable UDP
-U 0
```

Simple fix yet effective for now.
