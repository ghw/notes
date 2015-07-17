title: 修复IE下显示br换行符显示方块问题
toc: true
date: 2015-07-18 00:48:25
tags: [碎碎念,网络,IE]
categories: 碎碎念
---

发现 IE 下竟然

![IE 下显示的尾部](//dn-nimages.qbox.me/2015/07/br1.png)

会显示一个方块。。。


然后。。。定位发现是 br 换行符导致的，直接取消掉 br 换行符，换成 p 就可以了，哈哈哈

![取消 BR 换行符](//dn-nimages.qbox.me/2015/07/br2.png)