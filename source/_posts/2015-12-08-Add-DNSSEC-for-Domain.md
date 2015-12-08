title: 为域名添加 DNSSEC 记录
toc: true
date: 2015-12-08 19:36:11
tags: [信息技术,DNSSEC,安全,DNS]
categories: 信息技术
---

为域名添加 DNSSEC 功能，前几天收到 CloudFlare 的广告邮件，说 CloudFlare 已经支持 DNSSEC 功能了，
乘着有空把 DNS 从 DNSPod 切到了 CloudFlare ，目前国内没找到合适的，支持 DNSSEC 的 DNS 服务商，
自己建太麻烦，想想就切换到 CloudFlare 上去了。

添加 DNSSEC 功能需要注册商支持， 比如我的域名注册商 [NameSilo](/go/namesilo) 很早就已经支持 DNSSEC 功能了。

### 获取 DNSSEC 的 DS 记录相关值

登陆 CloudFlare ，（如果之前每天加过，请先添加）

选择你需要添加 DNSSEC 的域名，接着选择 DNS 功能，拉倒页面最下边，会有一个 [Enable DNSSEC] 按钮，

点击后会显示相关参数，例如：
![How to enable DNSSEC](https://dn-gehaowu.qbox.me/notes/2015/12/CloudFlare1.png)
<!--more-->
### [NameSilo](/go/namesilo) 后台添加 DNSSEC 记录

登陆 NameSilo，根据一下流程操作就行了。。。NameSilo 界面真难看。。。

![Domain manager](https://dn-gehaowu.qbox.me/notes/2015/12/namesilo1.png)
![Domain manager](https://dn-gehaowu.qbox.me/notes/2015/12/namesilo2.png)
![DS](https://dn-gehaowu.qbox.me/notes/2015/12/namesilo3.png)
![DNSSEC](https://dn-gehaowu.qbox.me/notes/2015/12/namesilo4.png)

添加完记得将域名 nameserver 改成 CloudFlare 的 域名服务器。。

```
algin.ns.cloudflare.com
zelda.ns.cloudflare.com
```

完成后 CloudFlare 会显示完成
```
DNSSEC protects against forged DNS answers. DNSSEC protected zones are digitally signed to ensure the DNS records received are identical to the DNS records published by the zone owner.

Success! gehaowu.com is protected with DNSSEC.
```

![Success! gehaowu.com is protected with DNSSEC.](https://dn-gehaowu.qbox.me/notes/2015/12/CloudFlare2.png)