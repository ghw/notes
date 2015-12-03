title: Hostker 主机的优化配置
toc: true
date: 2015-09-12 10:20:05
tags: [信息技术,开源,Hostker,SSL,HTTPS]
categories: 信息技术
---

有点贝壳，捣腾了几天 Hostker 玩，，，哈哈哈

写个 .htaccess 来强制 HTTPS 跳转了

## 没有 HTTPS 如何*装逼*。。。![HTTPS](https://dn-gehaowu.qbox.me/notes/2015/09/HTTPS.png)

## .htaccess 配置文件：
```sh
### 我的 Hostker 主机 .htaccess 配置
## 启用地址重写模块
RewriteEngine On
# 针对 Hostker 主机的 SSL 监听方案
RewriteCond %{HTTP:KERSSL} !on
# 全局 HTTPS 并强制跳转带 www. 域名模式
RewriteRule .* https://www.gehaowu.com/$1 [R=301,L]
## 设置出错页面
# 设置404 页
ErrorDocument 404 /404.html
## 设置缓存策略
# 启用缓存功能
ExpiresActive On
# 默认全局缓存时间为3天
ExpiresDefault "access plus 3 days"
# 指定文件缓存5天
<FilesMatch "\.(css|gif|jpeg|jpg|js|png|ico|bmp|svg|doc|pdf|mp3|ogg|mp4|mpeg|webm|eot|ttf|woff)$">
ExpiresDefault "access plus 5 days"
</FilesMatch>
# 指定文本文件缓存1天
<FilesMatch "\.(htm|html|xhtml|shtml|xml|rss|atom|txt)$">
ExpiresDefault "access plus 1 days"
</FilesMatch
```

参考：[博客启用SSL访问数据更安全](https://www.keinx.com/2015/ssl.html)