---
title: "Full Disk Encrypted Ubuntu 20.04 in a small dedicated server."
date: 2020-06-07T11:46:49+07:00
showDate: true
draft: false
tags: ["sysadmin", "linux", "ubuntu", "kimsufi", "oneprovider", "disk", "encrypted"]
---

I miss the time when I was still exploring a lot of things in the Linux world.

Nowadays, I'm mostly working with servers that are pre-provisioned and almost ready out of the box.

Those are nice conveniences for work. I decided though to go back and be like me again ten years ago.

Requirements:

* Experiments with small dedicated server
* It should be encrypted on disk.
* Have fun while doing it.

## Server Providers

I looked into two dedicated, Kimsufi and One Provider seems to fit my needs.

I benchmark CPU and Networking to see which one is better, and I'm pleased with One Provider more.
Huge plus also on their dashboard. Their web interface is responsive, unlike Kimsufi.

## Diving in

Frankly, I don't know how to set up a server from scratch even though I've set up Arch and the likes many times. I just don't do it enough to memorize it. So I searched the web and found this [excellent article](https://opsblog.net/posts/full-disk-encrypted-ubuntu-kimsufi-sever/)

So, for the most part, it's a rewritten parts of that article updated using Ubuntu 20.04 and using OneProvider as a server provider.

I won't go as detailed, though.

### Boot into rescue mode

This one is interesting. It won't let me boot into rescue mode first without installing something in their UI first. So I tried Arch, and as far as I can tell, it works.

But that's not really what I want, so I went to `Boot Mode`, click the `BOOT IN RESCUE MODE (PLEASE SELECT A RESCUE IMAGE)`, selected the `Ubuntu-18.04_amd64` image and press `Boot`.

The server will automatically restart and the current page will give you the creds on how to connect and login in to the server.

### Setting up the server

Once you're connected, it's time to setup the disk and installs Ubuntu on it.

```sh
# Turn off the swap, not sure why it's on in the first place
sudo swapoff -a
# Erase the entire
wipefs -a /dev/sda
# Create MBR layour
parted -a optimal /dev/sda mklabel msdos
# Create first 512MiB partition
parted /dev/sda -a optimal mkpart primary 0% 512MiB
# Create partition in remaining disk space
parted /dev/sda -a optimal mkpart primary 512MiB 100%
# Set first partition as bootable
parted /dev/sda set 1 boot on
```

Install and update packages.

```sh
apt update && apt install -y cryptsetup debootstrap
```

Format boot partition.

```sh
mkfs.ext4 /dev/sda1
````

Encrypt the disk with a strong passphrase.

```sh
cryptsetup -q -s 512 -c aes-xts-plain64 luksFormat /dev/sda2
```

Get the UUID of the second disk for fstab later.

```sh
cryptsetup luksDump /dev/sda2 | grep UUID | awk '{print $2}'
```

Open the encrypted partition.

```sh
cryptsetup luksOpen /dev/sda2 root
# this will be opened in /dev/mapper/root
```

Format it

```sh
mkfs.ext4 /dev/mapper/root
```

Now we are ready to install Ubuntu 20.04.

```sh
# mount the encrypted partition
mount /dev/mapper/root /mnt
# mount the boot partition
mkdir /mnt/boot && mount /dev/sda1 /mnt/boot
# minimal installation
debootstrap --arch amd64 focal /mnt http://mirrors.online.net/ubuntu
```

Let's get some coffee and relax for now.

After that, let's get inside the newly installed system.

```sh
mount -o bind /dev /mnt/dev
mount -t proc proc /mnt/proc
mount -t sysfs sys /mnt/sys
chroot /mnt /bin/bash
```

I'm greeted by this:

```sh
bash: warning: setlocale: LC_ALL: cannot change locale (en_US.UTF-8)
# quick fix
locale-gen en_US.UTF-8
```

Put the saved UUID previously into crypttab.

```sh
echo "root UUID=<SAVED_UUID> none luks" > /etc/crypttab
```

Create fstab file.

```sh
cat << EOF > /etc/fstab
/dev/mapper/root / ext4 defaults,relatime 0 1
/dev/sda1 /boot ext4 defaults,relatime 0 2
EOF
```

Not sure what this does.

```sh
ln -sf /proc/mounts /etc/mtab
```

Configure network. I will be using the new default netplan.

```sh
cat << EOF >> /etc/netplan/01-netcfg.yaml
network:
  ethernets:
    eth0:
      dhcp4: true
      nameservers:
        addresses:
          - "8.8.8.8"
          - "8.8.4.4"
  renderer: networkd
  version: 2
EOF
```

Hostnames

```sh
echo "<hostname>" > /etc/hostname
echo "127.0.1.1 <hostname>.<domain> <hostname>" >> /etc/hosts
```

Timezones

```sh
echo "UTC" > /etc/timezone
dpkg-reconfigure -f noninteractive tzdata
```

Update apt sources.

```sh
cat << EOF > /etc/apt/sources.list
deb http://mirrors.online.net/ubuntu focal main restricted universe multiverse
deb http://mirrors.online.net/ubuntu focal-updates main restricted universe multiverse
deb http://mirrors.online.net/ubuntu focal-security main restricted universe multiverse
deb http://mirrors.online.net/ubuntu focal-backports main multiverse restricted universe
EOF
```

Install packages

When a prompt ask where to install bootloader, select the first option.

```sh
apt update && apt install -y \
    busybox console-setup \
    cryptsetup cryptsetup-initramfs \
    dropbear dropbear-initramfs \
    grub-pc initramfs-tools kbd \
    linux-image-generic-hwe-20.04
    linux-tools-generic-hwe-20.04 \
    locales ssh
```

SSH Keys

```sh
mkdir /root/.ssh && chmod 600 /root/.ssh
echo "<PUBLIC_SSH_KEY>" > /root/.ssh/authorized_keys
echo "<PUBLIC_SSH_KEY>" > /etc/dropbear-initramfs/authorized_keys
```

Configuring dropbear.

```sh
sed -i 's/local flags=\"Fs\"/local flags=\"F\"/' /usr/share/initramfs-tools/scripts/init-premount/dropbear

cat << EOF > /etc/initramfs-tools/hooks/passwd_hook.sh
#!/bin/sh

PREREQ=""
prereqs()
{
echo "\$PREREQ"
}
case \$1 in
    prereqs)
        prereqs
        exit 0
    ;;
esac

grep -e root /etc/shadow > \${DESTDIR}/etc/shadow
EOF

chmod +x /etc/initramfs-tools/hooks/passwd_hook.sh
```

Bootloader and network interface.

```sh
sed -i s/GRUB_CMDLINE_LINUX=\"\"/GRUB_CMDLINE_LINUX=\"net.ifnames=0\ biosdevname=0\ ip=:::::eth0:dhcp\"/g /etc/default/grub
```

Update grub and initramfs

```sh
update-grub && update-initramfs -u
```

Exit chroot and unmount disk.

```sh
exit
umount /mnt/{boot,dev,proc,sys}
umount /mnt
cryptsetup luksClose root
```

Whew, that was a lot of copy paste...

### The moment of truth.

Go back to OneProvider dashboard, go into boot mode again, select `BOOT IN NORMAL MODE` and click `Boot`.

It should automatically restart the machine and boot in normal disk.

Before we can use the server, we have to connect to the server first and unlock the disk.

```sh
ssh root@IP
cryptroot-unlock
```

Enter your password when prompted.
If it's a success, it will disconnect you from the server. Connect again like any standard server.

Alright, take another sip of that coffee. :)

## So what now?

This is already a second server that I did and the article is base on that experience. The first one is in Kimsufi and apart from booting the rescue mode. There's almost no difference between the two.

I had a lot of fun setting this up, and I'm already using the previous server to host [lounge](https://thelounge.chat/) a self hosted IRC client and I've been pleased with it so far.

My hope for this article is to show you dear reader that its not that complicated to have a relatively secure system that you control and have fun while doing it.

Maintaining and monitoring a server though is another thing, and I might write more articles about it once I could try the tools that I'm planning to use.
