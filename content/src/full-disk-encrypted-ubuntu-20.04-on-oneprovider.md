---
title: "Full Disk Encrypted Ubuntu 20.04 in OneProvider"
date: 2020-06-07T11:46:49+07:00
draft: true
---

I really miss the the time when I was still exploring a lot of things in the linux world.

Nowadays, I'm mostly working with servers that are pre provisioned and almost ready out of the box.

Those are nice conviniences for work. I decided though to go back and be like me again 10 years ago.

Requirements:

* Small dedicated server that I can experiment.
* It should be encrypted on disk.
* Have fun while doing it.

## Server Providers

I looked into 2, Kimsufi and One Provider seems to fit my needs.

I benchmark CPU and Networking to see which one is better and I'm pleased with One Provider more.
Huge plus also on their dashboard. Their UI is responsive unlike Kimsufi.

## Diving in.

Frankly, I don't know how to setup a server from scratch. Even though I've setup Arch and the likes many times in the past. I just don't do it enough to memorize it. So I searched the web and found this [awesome article](https://opsblog.net/posts/full-disk-encrypted-ubuntu-kimsufi-sever/)

So for the most part, it's a rewritten parts of that article updated using Ubuntu 20.04 and using OneProvider as a server provider.

I won't go as detailed though.

### Boot into rescue mode.

This is one interesting, they won't let met boot into rescue mode first withouth installing something in their UI first. I saw Arch and I just clicked.

After sometime, I came back and indeed, Arch is installed and packages are up to date. That was nice.

But that's not really what I want, so I went to `Boot Mode`, click the ` BOOT IN RESCUE MODE (PLEASE SELECT A RESCUE IMAGE)`, selected the `Ubuntu-18.04_amd64` image and press `Boot`.

The serer will automatically restart and the current page will give you the creds on how to connect and login in the server.

### Setting up the server.

Once you're connected, it's time to actually setup the disk and install Ubuntu on it.

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

```
apt update && apt install -y cryptsetup debootstrap
```

Format boot partition.

```
mkfs.ext4 /dev/sda1
````

Encrypt the disk with strong passphrase.
```
cryptsetup -q -s 512 -c aes-xts-plain64 luksFormat /dev/sda2
```

Get the UUID of the second disk for fstab later.
```
cryptsetup luksDump /dev/sda2 | grep UUID | awk '{print $2}'
```

Open the encrypted partition.
```
cryptsetup luksOpen /dev/sda2 root
# this will be opened in /dev/mapper/root
```

Format it

```
mkfs.ext4 /dev/mapper/root
```

Now we are ready to install Ubuntu 20.04.

```
# mount the encrypted partition
mount /dev/mapper/root /mnt
# mount the boot partition
mkdir /mnt/boot && mount /dev/sda1 /mnt/boot
# minimal installation
debootstrap --arch amd64 focal /mnt http://mirrors.online.net/ubuntu
```

Let's get some coffee and relax for now.

When that's done, let's get inside.

```
mount -o bind /dev /mnt/dev
mount -t proc proc /mnt/proc
mount -t sysfs sys /mnt/sys
chroot /mnt /bin/bash
```

I'm greeted by this:
```
bash: warning: setlocale: LC_ALL: cannot change locale (en_US.UTF-8)
# quick fix
locale-gen en_US.UTF-8
```

Put the saved UUID previously into crypttab.
```
echo "root UUID=<SAVED_UUID> none luks" > /etc/crypttab
```

Create an fstab.

```
cat << EOF > /etc/fstab
/dev/mapper/root / ext4 defaults,relatime 0 1
/dev/sda1 /boot ext4 defaults,relatime 0 2
EOF
```

Not sure what this does.
```
ln -sf /proc/mounts /etc/mtab
```

Configure networking, I will be using the new default netplan.

```
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
```
echo "<hostname>" > /etc/hostname
echo "127.0.1.1 <hostname>.<domain> <hostname>" >> /etc/hosts
```

Timezones

```
echo "UTC" > /etc/timezone
dpkg-reconfigure -f noninteractive tzdata
```

Update apt sources.

```
cat << EOF > /etc/apt/sources.list
deb http://mirrors.online.net/ubuntu focal main restricted universe multiverse
deb http://mirrors.online.net/ubuntu focal-updates main restricted universe multiverse
deb http://mirrors.online.net/ubuntu focal-security main restricted universe multiverse
deb http://mirrors.online.net/ubuntu focal-backports main multiverse restricted universe
EOF
```

Install packages

When a prompt ask where to install bootloader, select the first option.
```
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

```
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

```
sed -i s/GRUB_CMDLINE_LINUX=\"\"/GRUB_CMDLINE_LINUX=\"net.ifnames=0\ biosdevname=0\ ip=:::::eth0:dhcp\"/g /etc/default/grub
```

Update grub and initramfs

```
update-grub && update-initramfs -u
```

Exit chroot and unmount disk.

```
exit
umount /mnt/{boot,dev,proc,sys}
umount /mnt
cryptsetup luksClose root
```

Whew, that was a lot of copy paste...

### The moment of truth.

Go back to OneProvider dashboard, go into boot mode again, select `BOOT IN NORMAL MODE` and click `Boot`.

This should automatically restart the machine and boot in normal disk.

Before we can use the server, we have to connect to the server first and unlock the disk.

```
ssh root@IP
cryptroot-unlock
```

Enter your password when prompted.
If it's success, it will disconnect you from the server and you can now connect to it like any normal server.

Alright, now go take another sip of that coffee.