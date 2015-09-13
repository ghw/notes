title: 升级 DigitalOcean VPS 里的 FreeBSD
toc: true
date: 2015-09-13 15:49:26
tags: [信息技术,开源,FreeBSD,DigitalOcean]
categories: 信息技术
---


猜猜怎么升级的。。哈哈哈。。。


![FreeBSD](//dn-nimages.qbox.me/2015/09/freebsdr.png)

<!--more-->
![DigitalOcean](//dn-nimages.qbox.me/2015/09/DOVPS.png)

```sh
[ghw@haomwei.pts/1] ~ % dmesg -a | grep CPU
CPU: Intel(R) Xeon(R) CPU E5-2630L v2 @ 2.40GHz (2400.09-MHz K8-class CPU)
cpu0: <ACPI CPU> on acpi0
[ghw@haomwei.pts/1] ~ % dmesg -a | grep mem
real memory  = 536870912 (512 MB)
avail memory = 490225664 (467 MB)
hpet0: <High Precision Event Timer> iomem 0xfed00000-0xfed003ff on acpi0
vgapci0: <VGA-compatible display> mem 0xfc000000-0xfdffffff,0xfebf0000-0xfebf0fff at device 2.0 on pci0
virtio_pci0: <VirtIO PCI Network adapter> port 0xc060-0xc07f mem 0xfebf1000-0xfebf1fff irq 11 at device 3.0 on pci0
virtio_pci1: <VirtIO PCI Network adapter> port 0xc080-0xc09f mem 0xfebf2000-0xfebf2fff irq 11 at device 4.0 on pci0
virtio_pci2: <VirtIO PCI Block adapter> port 0xc000-0xc03f mem 0xfebf3000-0xfebf3fff irq 10 at device 5.0 on pci0
orm0: <ISA Option ROMs> at iomem 0xc0000-0xc8fff,0xeb800-0xeffff on isa0
vga0: <Generic ISA VGA> at port 0x3c0-0x3df iomem 0xa0000-0xbffff on isa0
[ghw@haomwei.pts/1] ~ % freebsd-version -ku
u10.2-RELEASE
10.2-RELEASE-p2
[ghw@haomwei.pts/1] ~ % uname -a
FreeBSD haomwei.com 10.2-RELEASE FreeBSD 10.2-RELEASE #0 r286666: Wed Aug 12 15:26:37 UTC 2015     root@releng1.nyi.freebsd.org:/usr/obj/usr/src/sys/GENERIC  amd64
[ghw@haomwei.pts/1] ~ %
```

彻底清理掉旧系统。。。全新安装，，，

→_→ →_→ →_→ →_→ →_→