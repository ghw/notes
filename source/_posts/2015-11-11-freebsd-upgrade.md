title: 【流氓方案】 FreeBSD 系统的强行跨版本更新
toc: true
date: 2015-11-11 19:10:53
tags: FreeBSD
categories: 信息技术
---


#### 下载需要的镜像
准备工作，下载你希望升级到 FreeBSD 版本，如 10.2，它在官方FTP的存储的目录为：
http://ftp.freebsd.org/pub/FreeBSD/releases/amd64/10.2-RELEASE/
你需要将 FreeBSD 基本系统跟内核包下载下来备用 （base.txz,kernel.txz）
```
fetch http://ftp.freebsd.org/pub/FreeBSD/releases/amd64/10.2-RELEASE/base.txz
fetch http://ftp.freebsd.org/pub/FreeBSD/releases/amd64/10.2-RELEASE/kernel.txz
```

#### 备份旧系统

切换到 / ，将静态编译的工具包 rescue 改名为 r
创建存放旧系统的 old 目录，并把所有跟新系统冲突的文件移动到 old 目录
```
cd /
mv rescue r
mkdir old
/r/mv .profile .rnd .cshrc COPYRIGHT entropy \
bin etc media root usr boot home mnt \
sbin var lib sys libexec tmp old
```

执行完毕后可以使用 /r/ls 查看目录下文件是否已全部正常的转移了
<!--more-->
正常情况下 / 下应该还存在 
.snap .sujournal dev proc r 这几个目录，
它们文件系统或者系统虚拟设备相关的，无需理会。

```
root@daemon:/ # /r/ls -a
.       ..      dev     old     proc    r
root@daemon:/ #
```

#### 释放新系统/安装新系统
上面那一步已经完成旧系统的转移工作，也可以得瑟的说算是备份吧。

释放新系统，并拷贝必要系统配置文件回新系统，
```
/r/tar -xvf /old/root/base.txz -C /
/r/tar -xvf /old/root/kernel.txz -C /
cp -a /old/boot/loader.conf /boot/
cp -a /old/etc/rc.conf /etc/
cp -a /old/etc/fstab /etc/
cp -a /old/etc/resolv.conf /etc/
cp -a /old/root/.ssh /root
cp -a /old/boot/zfs /boot/
sync &&sync &&sync
```
需要拷回来的文件自己看着办就可以了。。不过loader.conf,rc.conf,fstab这三个文件，如果你旧系统上存在，则必须拷贝回来，如果你是根分区是ZFS，则zfs目录也必须拷回来

#### 重启完成新系统切换
只要系统间隔不是太大，重启，强制升级就完工了。。。，
```
shutdown -r now
```

#### 清理旧系统
**小贴士：重启完成后 /r 目录就可以删掉了， /old 目录下的看着有用的自己捡回来就是了，没要的了直接全部删掉就好了**
```
chflags -R noschg /r /old
rm -rf /r /old
```

最后。。。。。。。。。。。。 XD
#### 警告：请勿在生产系统上测试/实施