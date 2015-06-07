title: "为FreeBSD打包ODOO（OpenERP）软件包"
date: 2015-05-23 22:52:00
tags: [信息技术,开源,FreeBSD,OpenERP,ODOO]
categories: 信息技术
---
## 。。。

站在巨人肩膀上总是容易些，搜搜之前已经被删除的 finance/openerp-server 。。。

http://svnweb.freebsd.org/ports?view=revision&revision=340935
最后一次推送

搞到340935号提交的内容。
svnlite co svn://svn.freebsd.org/ports/head/finance/openerp-server@340935



![https](//dn-nimages.qbox.me/2015/05/https.png)
小绿锁哟，哈哈哈哈哈哈


下面开始，通过 FreeBSD 的包管理器安 NGINX ，
（个人强力不推荐绕过操作系统包管理工具自己编译的超级装逼行为！）

<!-- more -->
```
pkg install -y finance/odoo
```
```
cd /usr/ports/finance/odoo &&make install
```

![从Ports安装NGINX](//dn-nimages.qbox.me/2015/05/ports.nginx.png)


## 。。。
```
setup.py.orig setup.py
make makepatch

```


**版权声明**

<a href="https://creativecommons.org/licenses/by-nc-nd/4.0/deed.zh"><img src="//dn-nimages.qbox.me/other/CC-BY-SA-ND.png" alt="署名-非商业性使用-禁止演绎 创意共享4.0国际许可证" /></a>

[Ge Haowu's Personal Website](//gehaowu.com/) by [Haowu Ge](//gehaowu.com/aboutme/) is licensed under a [CC BY-NC-ND 4.0 International](https://creativecommons.org/licenses/by-nc-nd/4.0/deed.zh) License.
由 [葛豪武](//gehaowu.com/aboutme/) 创作并维护在[葛豪武的个人网站](//gehaowu.com/) 采用 [署名-非商业性使用-禁止演绎 创意共享 4.0 国际](https://creativecommons.org/licenses/by-nc-nd/4.0/deed.zh) 许可证。


本文首发于 [葛豪武的个人网站！· Ge Haowu's Personal Website!](//gehaowu.com/) ，版权所有，侵权必究。
