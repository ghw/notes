title: Hostker 强制 HTTPS
toc: true
date: 2015-09-12 10:20:05
tags: [信息技术,开源,Hostker,SSL,HTTPS]
categories: 信息技术
---

临时转Hostker试试。。。

写个.htaccess就能强制HTTPS跳转了

```sh
RewriteEngine On
RewriteCond %{HTTP:KERSSL} !on
RewriteRule .* https://www.gehaowu.com/$1 [R=301,L]
```

参考：[博客启用SSL访问数据更安全](https://www.keinx.com/2015/ssl.html)