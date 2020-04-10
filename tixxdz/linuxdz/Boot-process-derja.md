# 2. Boot process debugging, security and beyond - Linux-dz courses  (derja)

Video Link:

Djalal Harouni

Email for corretions here:  tixxdz+linuxdz@gmail.com  -  sorry if I do not reply.

https://github.com/tixxdz

Date: 10/04/2020
LastModified: 10/04/2020


## What is about ?

References:  https://training.linuxfoundation.org/training/fundamentals-of-linux/

Adapted to be easy with video in derja language, Algeria local dialect.

* Ghir important things!
   - Dirou research alone et berkewna men excuses ya wechbikoum?

* 3leh ?
   - Tal3ou niveau 

* Goal - Hadef ?
   - Debug Linux boot process and beyond
   - Services, security, logins and timers

* Teacher ?
   - Djalal Harouni - Open Source Software maintainer - linux kernel developer... Wrote code used in millions of machines and devices.


## Plan

* Linux Boot Process - Bootloader
   - Linux Kernel and initramfs
   - Init systemd and Services

* Logins and timers (cron jobs)

* Debug boot and Security


## 1. Linux boot process

BIOS - BIOS POST

Boot loader (grub2 - uboot for embedded, etc)

Linux kernel and initramfs (initrd)

Systemd  - only distributions with systemd



### 1.1 BIOS - BIOS POST

Basic I/O System  -  is hardware working ?

https://en.wikipedia.org/wiki/BIOS_interrupt_call

Find boot record load into ram and transfert execution 
to 2) Boot  loader


### 1.2 Boot loader (Grub2 - uboot for embedded, etc)

Grub2 (GRand Unified Bootloader, version 2)
	
No more multiple stages: https://www.gnu.org/software/grub/manual/grub/grub.html

Load Linux kernel and initramfs

Files and configs:  /boot/grub2/

Find kernel and initramfs (initial kernel ramdisk) load in ram
and transfert execution  to 3) Linux kernel


### 1.3 Linux kernel and initramfs

Kernel can be compressed “vmlinuz” (Z) self extracting or uncompressed
“vmlinux” image. Located in /boot/

Initramfs (initrd) image - basic root file system and modules need by kernel

Make sure to backup your old initrd first.

```bash
mkinitrd -o /boot/initrd.img-$(uname -r) $(uname -r)
mkinitrd -o /boot/initrd.img-4.19 4.19
```

Kernel detects and initializes hardware

Kernel reads disks detects the root file system and replaces initramfs

Kernel starts its threads and transfert execution to 
4)  INIT Program /sbin/init 	 

```bash
$ ls -lha /sbin/init
lrwxrwxrwx 1 root root 20 Oct 16 14:24 /sbin/init -> /lib/systemd/systemd
```

### 1.4 Systemd init -  only distributions with systemd

Parent of all processes.

Sets up the machine

Boot targets order: http://man7.org/linux/man-pages/man7/bootup.7.html

Sysinit.target, basic.target, multi-user.target and graphical.target

systemd  - journald tools , systemd-cgls and others.


### 1.4 Systemd init - example service


Example service program running during each boot:

File [hello-world.service](../systemd/units/hello-world.service)

```
[Unit]
Description=Hello World Service

[Service]
ExecStart=/bin/bash -c 'for i in {1..30}; do echo "Hello at $(date)"; sleep 3; done'

[Install]
WantedBy=multi-user.target
```

Installation commands:
```bash
sudo cp hello-world.service /etc/systemd/system/
sudo systemctl daemon-reload
sudo systemctl enable hello-world.service
sudo systemctl start hello-world.service
```

Stop:
```bash
sudo systemctl stop hello-world.service
```

Disable:
```bash
sudo systemctl disable hello-world.service
```


NetworkManager File [NetworkManager.Service](../systemd/units/NetworkManager.service) example.

```bash
cat /lib/systemd/system/NetworkManager.service
```
