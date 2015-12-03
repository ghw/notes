title: 构建 VPN 网络服务
toc: true
date: 2015-09-20 10:14:50
tags: [信息技术,开源,FreeBSD,VPN,Linux,PPTP]
categories: 信息技术
---

## 写在前面
**朋友公司希望启用远程办公，所以稍微考虑考虑，就准备搞个 VPN 来实现登陆内部网络**


关于更多远程办公相关信息请 Google [VPN 远程办公](https://www.google.com/?gws_rd=cr,ssl#safe=strict&q=VPN+%E8%BF%9C%E7%A8%8B%E5%8A%9E%E5%85%AC)

## 在 FreeBSD 上用 mpd5 构建 PPTP VPN

### 安装 PPTP 服务：
```sh
 #Code Star
portsnap fetch update					#更新ports
cd /usr/ports/net/mpd5					#or pkg install -y mpd5
make install clean
 #Code End
```
<!--more-->

## 设置随机启动服务
```sh
 #Code Star
# PPTP 服务
mpd_enable="YES"					# 开启 VPN 服务
gateway_enable="YES"					# 网关转发
firewall_enable="YES"					# 防火墙
firewall_type="OPEN"
firewall_logging_enable="YES"
natd_enable="YES"					# 开启 NAT 服务
natd_interface="vtnet0"					# NAT 使用的网卡接口
 #Code End
```

### 设置 mpd5 主体配置文件
```sh
 #Code Start
cat /usr/local/etc/mpd5/mpd.conf
#################################################################
#
#       MPD configuration file
#
# This file defines the configuration for mpd: what the
# bundles are, what the links are in those bundles, how
# the interface should be configured, various PPP parameters,
# etc. It contains commands just as you would type them
# in at the console. Lines without padding are labels. Lines
# starting with a "#" are comments.
#
# $Id: mpd.conf.sample,v 1.47 2012/12/18 15:39:47 dmitryluhtionov Exp $
#
#################################################################

startup:
        # configure mpd users
        set user foo bar admin
        set user foo1 bar1
        # configure the console
        set console self 127.0.0.1 5005
        set console open
        # configure the web server
        set web self 0.0.0.0 5006
        set web open

#
# Default configuration is "dialup"

default:
	#load dialup
    load pptp_server					# 修改为 PPTP 模式

pptp_server:
#
# Mpd as a PPTP server compatible with Microsoft Dial-Up Networking clients.
#
# Suppose you have a private Office LAN numbered 192.168.1.0/24 and the
# machine running mpd is at 192.168.1.1, and also has an externally visible
# IP address of 1.2.3.4.
#
# We want to allow a client to connect to 1.2.3.4 from out on the Internet
# via PPTP.  We will assign that client the address 192.168.1.50 and proxy-ARP
# for that address, so the virtual PPP link will be numbered 192.168.1.1 local
# and 192.168.1.50 remote.  From the client machine's perspective, it will
# appear as if it is actually on the 192.168.1.0/24 network, even though in
# reality it is somewhere far away out on the Internet.
#
# Our DNS server is at 192.168.1.3 and our NBNS (WINS server) is at 192.168.1.4.
# If you don't have an NBNS server, leave that line out.
#

# Define dynamic IP address pool.
        set ippool add pool1 192.168.7.2 192.168.7.254	# VPN 分配的 IP 范围

# Create clonable bundle template named B
        create bundle template B
        set iface enable proxy-arp
        set iface idle 1800
        set iface enable tcpmssfix
        set ipcp yes vjcomp
# Specify IP address pool for dynamic assigment.
        set ipcp ranges 192.168.1.1/32 ippool pool1
        set ipcp dns 119.29.29.29			# 腾讯 DNS 服务
        #set ipcp nbns 192.168.1.4
# The five lines below enable Microsoft Point-to-Point encryption
# (MPPE) using the ng_mppc(8) netgraph node type.
        set bundle enable compression
        set ccp yes mppc
        set mppc yes e40
        set mppc yes e128
        set mppc yes stateless

# Create clonable link template named L
        create link template L pptp
# Set bundle template to use
        set link action bundle B
# Multilink adds some overhead, but gives full 1500 MTU.
        set link enable multilink
        set link yes acfcomp protocomp
        set link no pap chap eap
        set link enable chap
# We can use use RADIUS authentication/accounting by including
# another config section with label 'radius'.
#       load radius
        set link keep-alive 10 60
# We reducing link mtu to avoid GRE packet fragmentation.
        set link mtu 1460
# Configure PPTP
        set pptp self 162.243.135.5			# 服务器 IP
# Allow to accept calls
        set link enable incoming
 #Code End
```

### 添加 VPN 服务的登陆帐号密码
```sh
 #Code Start
echo "ghw ghwpass" >/usr/local/etc/mpd5/mpd.secret	#设置帐号，用户 + 空格 +密码，一行一个
 #Code End
```

### 添加转发规则，让登陆上VPN的客户端也能连接外网
```sh
 #Code Start
echo "ipfw add allow all from any to any" >/etc/ipfw.rules	#设置防火墙规则
echo "ipfw add divert natd ip from any to any via vtnet0" >> >/etc/ipfw.rules
echo "net.inet.ip.forwarding=1" >>/etc/sysctl.conf	#修改syctl配置，下次启动依旧有效
sysctl net.inet.ip.forwarding=1 			#让sysctl立即生效
service ipfw start					#重启防火墙
 #Code End
```

### 配置完成，重启服务
```sh
 #Code Start
service mpd5 restart
 #Code End
```

重启后就能正常使用了。


## 发几个 KDE5 的图片，哈哈哈
![KDE 桌面 - 一切自由](https://dn-gehaowu.qbox.me/notes/2015/09/kde5_1.jpg)
![KDE 提交错误报告](https://dn-gehaowu.qbox.me/notes/2015/09/kde5_2.jpg)
![KDE Login](https://dn-gehaowu.qbox.me/notes/2015/09/kde5_3.png)
![SLIM Login](https://dn-gehaowu.qbox.me/notes/2015/09/kde5_4.png)
![KDE 关于](https://dn-gehaowu.qbox.me/notes/2015/09/kde5_5.png)

文章原版来自 FreeBSDChina Wiki：[在 FreeBSD 上用 mpd5 构建 PPTP VPN](https://wiki.freebsdchina.org/howto/m/mpd5_setup)