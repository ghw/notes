title: "通过 SSH 远程跨版本更新 FreeBSD"
date: 2015-05-06 08:54:08
tags: [信息技术,开源,FreeBSD]
categories: 信息技术
toc: true
---

因为远程，所以你，只有一次机会！！！

任务需求：
    远程将系统更新到最新版，并彻底清理掉原系统管理员可能存在的操作遗留问题。
    
需要注意的地方：
    服务器硬件在外地并未开启IPMI功能，且因为特殊原因禁止与机房管理员联系。

必备条件：
    操作员必须可接入远程服务器

升级方案：
    1，通过 FreeBSD 自带的升级工具 freebsd-update 将系统更新到最新版本。（[FreeBSD 使用手冊 - 第 25 章 更新与升级 FreeBSD](https://www.freebsd.org/doc/zh_CN/books/handbook/updating-upgrading.html)[FreeBSD Handbook - Chapter 24. Updating and Upgrading FreeBSD](https://www.freebsd.org/doc/en_US.ISO8859-1/books/handbook/updating-upgrading.html)）
    2，或者直接将旧版系统替换到最新系统。
<!-- more -->

方案一比较简单，FreeBSD官方升级方案，（-_-! 我能说我出来没通过freebsd-update升级过系统麽。。。放弃吧。）
方案二个人强力推荐，通过替换旧版系统可以顺便清理干净原系统管理员留下来的所有操作痕迹，还可以将系统从32位更新到64位。另外因为是***远程更新方案，所以只有一次机会***，必须选择最安稳的方案。

方案二详细操作流程：

因为这个机器的情况我们并不了解，需要了解一些系统基本信息
查看系统版本
```
root@7AXU:~ # uname -a
FreeBSD 7AXU.COM 9.2-RELEASE-p15 FreeBSD 9.2-RELEASE-p15 #0: Mon Nov  3 20:08:52 UTC 2014     root@amd64-builder.daemonology.net:/usr/obj/usr/src/sys/GENERIC  i386
```

查看处理器类型
```
sysctl -a |grep CPU
cpu     I686_CPU
cpu     I586_CPU
cpu     I486_CPU
hw.model: Intel(R) Xeon(R) CPU            5130  @ 2.00GHz
dev.cpu.0.%desc: ACPI CPU
dev.cpu.0.%location: handle=\_PR_.CPU1
dev.cpu.1.%desc: ACPI CPU
dev.cpu.1.%location: handle=\_PR_.CPU2
dev.p4tcc.0.%desc: CPU Frequency Thermal Control
dev.p4tcc.1.%desc: CPU Frequency Thermal Control
```

查看内存大小
```
dmesg -a |grep mem
real memory  = 4294967296 (4096 MB)
avail memory = 3404886016 (3247 MB)
hpet0: <High Precision Event Timer> iomem 0xfed00000-0xfed003ff on acpi0
bce0: <QLogic NetXtreme II BCM5708 1000Base-T (B2)> mem 0xf4000000-0xf5ffffff irq 16 at device 0.0 on pci8
mpt0: <LSILogic SAS/SATA Adapter> port 0xec00-0xecff mem 0xfc5fc000-0xfc5fffff,0xfc5e0000-0xfc5effff irq 64 at device 8.0 on pci2
bce1: <QLogic NetXtreme II BCM5708 1000Base-T (B2)> mem 0xf8000000-0xf9ffffff irq 16 at device 0.0 on pci4
ehci0: <Intel 63XXESB USB 2.0 controller> mem 0xfc700000-0xfc7003ff irq 21 at device 29.7 on pci0
vgapci0: <VGA-compatible display> port 0xdc00-0xdcff mem 0xd8000000-0xdfffffff,0xfc1f0000-0xfc1fffff irq 19 at device 13.0 on pci16
orm0: <ISA Option ROMs> at iomem 0xc0000-0xc8fff,0xc9000-0xc9fff,0xd0000-0xd17ff,0xec000-0xeffff pnpid ORM0000 on isa0
vga0: <Generic ISA VGA> at port 0x3c0-0x3df iomem 0xa0000-0xbffff on isa0
```
从uname信息看我们更新前的系统是32位的，不过机器内存也就4G，更不更新到64位实在没太大差异，那就干脆别搞了，省事。

查看 rc.conf 文件
```
cat /etc/rc.conf
sshd_enable="YES"
ifconfig_bce1="inet IP  netmask 255.255.255.0"
defaultrouter="WG"
hostname="freebsd.local"
……
```

部分内容就直接省略了，不是很有意义，之所以要查看这个文件，是因为需要确定是否有特殊的服务，如果没有针对机器特殊的服务，那就不需要处理，将文件备份到U盘后者其它地方备用。

```
mkdir /USBDISK/
mount -t msdosfs /dev/da1s1 /USBDISK/
cp /etc/rc.conf /USBDISK/
```


/boot/device.hints

查看fstab文件系统结构文件
```
cat /etc/fstab
# Device                Mountpoint      FStype  Options         Dump    Pass#
/dev/da0s1b             none            swap    sw              0       0
/dev/da0s1a             /               ufs     rw              1       1
/dev/da0s1e             /tmp            ufs     rw              2       2
/dev/da0s1f             /usr            ufs     rw              2       2
/dev/da0s1d             /var            ufs     rw              2       2
/dev/acd0               /cdrom          cd9660  ro,noauto       0       0
```
顺便把文件也备份了
```
cp /etc/fstab /USBDISK/
```

旧系统freebsd-update fetch系统停止支持的提示
```
# freebsd-update fetch
Looking up update.FreeBSD.org mirrors... 5 mirrors found.
Fetching metadata signature for 9.2-RELEASE from update6.freebsd.org... done.
Fetching metadata index... done.
Inspecting system... done.
Preparing to download files... done.

The following files will be added as part of updating to 9.2-RELEASE-p17:
/usr/src/contrib/bind9/lib/isc/counter.c
/usr/src/contrib/bind9/lib/isc/include/isc/counter.h
/usr/src/contrib/tzdata/leap-seconds.list
/usr/src/contrib/tzdata/zone1970.tab

The following files will be updated as part of updating to 9.2-RELEASE-p17:
/boot/kernel/linker.hints

WARNING: FreeBSD 9.2-RELEASE-p15 HAS PASSED ITS END-OF-LIFE DATE.
Any security issues discovered after Thu Jan  1 08:00:00 CST 2015
will not have been corrected.
```

格式化SWAP分区，挂载，并将FreeBSD10.1基本系统从网上拉下来并释放到/mnt中去。
```
#mount -t mfsdosfs /dev/da1s1 /USBDISK/
newfs /dev/da0s1b
mount /dev/da0s1b /mnt
fetch -o /USBDISK/base.txz "http://ftp.freebsd.org/pub/FreeBSD/releases/i386/10.1-RELEASE/base.txz"
fetch -o /USBDISK/kernel.txz "http://ftp.freebsd.org/pub/FreeBSD/releases/i386/10.1-RELEASE/kernel.txz"
fetch -o /USBDISK/MANIFEST "http://ftp.freebsd.org/pub/FreeBSD/releases/i386/10.1-RELEASE/MANIFEST"
tar --unlink -xvf /USBDISK/kernel.txz -C /mnt
tar --unlink -xvf /USBDISK/base.txz -C /mnt
```

向fstab中指定"/"为"/dev/da0s1b"设备，
```
echo "/dev/da0s1b / ufs rw 1 1" >/mnt/etc/fstab
```

修改引导器配置文件，让系统启动的时候选择自动挂载/dev/da0s1b，
```
echo 'vfs.root.mountfrom=”ufs:/dev/da0s1b"' >/mnt/boot/loader.conf
```

将备份的rc.conf直接覆盖回来
```
cp /USBDISK/rc.conf /mnt/etc/
```

将sshd配置文件中的#PermitRootLogin no为PermitRootLogin yes
```
sed -i.bak -e s/#PermitRootLogin\ no/PermitRootLogin\ without-password/g /etc/ssh/sshd_config
```

修改临时系统root密码
```
chroot /mnt
passwd
```

第一步完工，准备重启，可以抽个小烟，坐等系统重新上线~~

<(￣︶￣)>
```
shutdown -r now
```

------

等系统重新上线后通过root账户跟刚设定的密码登陆，然后开始第二步。

清理掉老系统，可以直接格式化，或者将文件移动到其它目录，
格式化：
```
newfs /dev/da0s1a
newfs /dev/da0s1e
newfs /dev/da0s1f
newfs /dev/da0s1d
mount /dev/da0s1a /mnt
mkdir /mnt/usr
mkdir /mnt/var
mkdir /mnt/tmp
mount /dev/da0s1e /mnt/tmp
mount /dev/da0s1f /mnt/usr
mount /dev/da0s1d /mnt/var
```

移动一个旧系统到一个目录里：
```
mount /dev/da0s1a /mnt
mkdir /mnt/OLD
mv /mnt/* /mnt/OLD
mkdir /mnt/usr
mkdir /mnt/var
mkdir /mnt/tmp
mount /dev/da0s1e /mnt/tmp
mount /dev/da0s1f /mnt/usr
mount /dev/da0s1d /mnt/var
mkdir /mnt/tmp/OLD
mkdir /mnt/usr/OLD
mkdir /mnt/var/OLD
mv /mnt/tmp/* /mnt/tmp/OLD
mv /mnt/usr/* /mnt/usr/OLD
mv /mnt/var/* /mnt/var/OLD
```

正式更新系统，
```
mkdir /USBDISK
mount -t msdosfs /dev/da1s1 /USBDISK
tar --unlink -xvf /USBDISK/kernel.txz -C /mnt
tar --unlink -xvf /USBDISK/base.txz -C /mnt
```

拷贝rc.conf,fstab到新系统
```
cp /USBDISK/rc.conf /USBDISK/fstab /mnt/etc/
```

新系统设置
```
chroot /mnt
passwd
tzsetup
adduser
。。。
其它巴拉巴拉的。。
（不添加普通用户的情况下，切记设置允许root登陆，并设置root密码）
```


<(￣︶￣)>
搞完重启就OK了，
旧的SWAP分区会因为根据fstab里的内容直接覆盖掉SWAP里的临时系统


最后放一个更新到最新的FreeBSD-10.1-RELEASE-p9靓照一张 = = ！
![FreeBSD-10.1-RELEASE-p9](https://dn-nimages.qbox.me/2015/05/FreeBSD-10.1-RELEASE-p9.png)


--------------------
**版权声明**

<a href="https://creativecommons.org/licenses/by-nc-nd/4.0/deed.zh"><img src="https://dn-nimages.qbox.me/other/CC-BY-SA-ND.png" alt="署名-非商业性使用-禁止演绎 创意共享4.0国际许可证" /></a>

[Ge Haowu's Personal Website](//www.gehaowu.com/) by [Haowu Ge](//www.gehaowu.com/aboutme/) is licensed under a [CC BY-NC-ND 4.0 International](https://creativecommons.org/licenses/by-nc-nd/4.0/deed.zh) License.
由 [葛豪武](//www.gehaowu.com/aboutme/) 创作并维护在[葛豪武的个人网站](//www.gehaowu.com/) 采用 [署名-非商业性使用-禁止演绎 创意共享 4.0 国际](https://creativecommons.org/licenses/by-nc-nd/4.0/deed.zh) 许可证。


本文首发于 [葛豪武的个人网站！· Ge Haowu's Personal Website!](//www.gehaowu.com/) ，版权所有，侵权必究。