+++
author = "Zakatell Kanda"
categories = ["technology", "elasticsearch", "linux"]
date = 2017-07-03T05:46:21Z
description = ""
draft = false
slug = "running-elasticsearch-on-a-2gb-digital-ocean-droplet"
tags = ["technology", "elasticsearch", "linux"]
title = "running elasticsearch on a 2gb digital ocean droplet"

+++

So I want to run [Elasticsearch](https://www.elastic.co/products/elasticsearch) on a very limited memory machine and I keep running on this error: `cannot allocate memory`.

Did some research and found this [docs](https://www.elastic.co/guide/en/elasticsearch/reference/current/vm-max-map-count.html) on increasing `max_map_count`:

```sh
echo "vm.max_map_count=262144" | sudo tee -a /etc/sysctl.conf
sudo sysctl -w vm.max_map_count=262144
```

But the problem still persist, turns out memory still actually still not enough. I want don't to upgrade the memory since I just want to test things out and I have no plan on running it on production.

Swap memory to the rescue.

```sh
fallocate -l 2G /swapfile
chmod 600 /swapfile
mkswap /swapfile
swapon /swapfile
echo '/swapfile none swap sw 0 0' | sudo tee -a /etc/fstab
```

Run your elasticsearch and it should now be good.

Small Rant: I don't get why we need more than 2GB of memory to just test ES.
