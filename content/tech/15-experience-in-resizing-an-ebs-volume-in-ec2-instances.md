+++
author = "Zakatell Kanda"
categories = ["technology", "software", "aws", "linux", "devops"]
date = 2018-03-19T22:55:57Z
description = ""
draft = false
slug = "experience-in-resizing-an-ebs-volume-in-ec2-instances"
tags = ["technology", "software", "aws", "linux", "devops"]
title = "our experience in resizing an ebs volume in ec2 instances."

+++

Increasing disk space without downtime.

So in our team, we had to resize an instance disk because it's getting full, this is our experience.

For the first time we search on how to do this, there are many articles written about it and most of them require to stop the system and detaching a volume. But there must be a real doc from aws itself, that is most likely up to date.

After more searching, we found this link: https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/ebs-modify-volume.html

From there, it seems like we can modify it on the fly since our instance is the current generation, so that's good news.

Since we mount the whole volume, we didn't need to extend a partition table. You only have to use the `resize` command.

The list of block devices attached to our instance:

```sh
$ lsblk -f
NAME    FSTYPE LABEL           UUID                                 MOUNTPOINT
xvda
└─xvda1 ext4   cloudimg-rootfs b6adc449-5e3d-4331-ba6b-xxxx /
xvdb    ext4                   85a58c7a-d914-4b85-be3c-xxxx /opt/
```

And here's the resize command.

```sh
$ sudo resize2fs /dev/xvdb
resize2fs 1.42.13 (17-May-2015)
Filesystem at /dev/xvdb is mounted on /opt/couchdb; on-line resizing required
old_desc_blocks = 4, new_desc_blocks = 10
The filesystem on /dev/xvdb is now 39321600 (4k) blocks long.
```

This was a very nice and smooth experience and we are glad for this functionality, thank you AWS team and whoever made this possible.
