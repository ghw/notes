title: 通过cron定期从github上拉去个人网站数据
date: 2015-06-09 13:43:32
tags: 
---

先配置我的个人网站，

设置几个域名的虚拟主机，

，让所有请求 gehaowu.com,www.gehaowu.com 两个域名的 http 请求，强制跳转到 https 协议的 www.gehaowu.com ，
接着一个对发给 gehaowu.com 的 https 请求也强制转发给 www.gehaowu.com 。

关闭日志记录。

<!-- more -->
```sh
server {
    listen 80;
    server_name gehaowu.com www.gehaowu.com;
    rewrite ^/(.*) https://www.gehaowu.com/$1 permanent;
    access_log off;
    error_log off;
    log_not_found off;
}
server {
    listen 443 ssl;
    ssl_certificate keys/www.gehaowu.com.crt;
    ssl_certificate_key keys/www.gehaowu.com.key;
    server_name gehaowu.com;
    rewrite ^/(.*) https://www.gehaowu.com/$1 permanent;
    access_log off;
    error_log off;
    log_not_found off;
}
server {
    listen 443 ssl;
    ssl_certificate keys/www.gehaowu.com.crt;
    ssl_certificate_key keys/www.gehaowu.com.key;
    server_name www.gehaowu.com;
    index index.html index.htm default.html;
    root /home/ghw/wwwroot;
    access_log off;
    error_log off;
    log_not_found off;
#   access_log /home/ghw/wwwlog/www.gehaowu.com.access.log; error_log 
#	/home/ghw/wwwlog/www.gehaowu.com.error.log; 静止访问 "." 开头的隐藏文件
    location ~ /\. {
        deny all;
    }
# robots.txt 与 favicon.ico 文件访问不记录日志
    location ~* ^/(favicon.ico|robots.txt)$ {
        allow all;
        access_log off;
	error_log off;
        log_not_found off;
    }
# 静态文件超期
    location ~* 
\.(html|htm|css|xml|rss|gif|jpeg|jpg|js|atom|txt|png|ico|bmp|svg|doc|pdf|xhtml|mp3|ogg|mp4|mpeg|webm|)$ {
    expires max;
    access_log off;
    error_log off;
    log_not_found off;
    }
}
```


新建一个网站根目录， 比如我这里使用 ***/home/ghw/*** ，接着执行 gitclone.sh 文件
## gitclone.sh

```sh
#!/bin/sh
PATH=/bin:/sbin:/usr/bin:/usr/sbin:/usr/local/bin:/usr/local/sbin
git clone --depth 1 -b master https://github.com/gehaowu/gehaowu.github.com /home/ghw/wwwroot
git clone --depth 1 -b gh-pages https://github.com/gehaowu/blog /home/ghw/wwwroot/fonts
git clone --depth 1 -b gh-pages https://github.com/gehaowu/blog /home/ghw/wwwroot/aboutme
git clone --depth 1 -b gh-pages https://github.com/gehaowu/blog /home/ghw/wwwroot/blog
git clone --depth 1 -b gh-pages https://github.com/gehaowu/blog /home/ghw/wwwroot/albums
```


因为我在 wwwroot 根目录放了 gitpull.sh 脚本，所以直接给 cron 添加计划任务：

## crontab

```sh
* */5 * * * /bin/sh /home/ghw/wwwroot/gitpull.sh
```

每 5 个小时执行一次脚本。


一下 gitpull.sh 的内容
## gitpull.sh

```sh
#!/bin/sh
PATH=/bin:/sbin:/usr/bin:/usr/sbin:/usr/local/bin:/usr/local/sbin
cd /home/ghw/wwwroot/
git reset --hard >/dev/null 2>&1
git pull --rebase -f >/dev/null 2>&1
cd /home/ghw/wwwroot/fonts/
git reset --hard >/dev/null 2>&1
git pull -f >/dev/null 2>&1
cd /home/ghw/wwwroot/aboutme/
git reset --hard >/dev/null 2>&1
git pull -f >/dev/null 2>&1
cd /home/ghw/wwwroot/blog/
git reset --hard >/dev/null 2>&1
git pull -f >/dev/null 2>&1
cd /home/ghw/wwwroot/albums/
git reset --hard >/dev/null 2>&1
git pull -f >/dev/null 2>&1
```


搞完，这样就可以了，
没五个小时从 github 上拉取网站数据，所以我们只需要将自己的网站数据同步到github上就可以了。。。
十分容易也十分安全，哈哈哈。。。<(￣▽￣)>