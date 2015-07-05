title: 【走进装逼学：】通过CSS字距调整空格
date: 2015-06-30 21:49:53
tags: [信息技术,CSS]
categories: 信息技术
toc: true
---


抄来的主页，发现对方用了好多空格，

想着空格多不舒服。。。

就想通过调整字距来去掉空格


```sh
letter-spacing: 1em;
```


然后悲剧的发现 **不居中了** 。。。

![道法自然](//dn-nimages.qbox.me/2015/06/imitation_of_nature1.png)

<!-- more -->

想想算了，，，改回去吧。。


![道法自然](//dn-nimages.qbox.me/2015/06/imitation_of_nature2.png)



![兼爱非攻](//dn-nimages.qbox.me/2015/07/jafg.jpg)

原方案：
```html
				<!-- 头部代码 -->
					<header id="header">
						<h1>道&nbsp;&nbsp;法&nbsp;&nbsp;自&nbsp;&nbsp;然</h1>
						<p>Welcome to Ge Haowu's Personal Homepage!</p>
						<nav>
							<ul>
```