title: 通过cron定期从github上拉去个人网站数数据
date: 2015-06-09 13:43:32
tags: 
---



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
# /home/ghw/wwwlog/www.gehaowu.com.error.log; 静止访问 "." 开头的隐藏文件
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




## gitclone.sh

```sh
#!/bin/sh
git clone --depth 1 -b master https://github.com/gehaowu/gehaowu.github.com /home/ghw/wwwroot
git clone --depth 1 -b gh-pages https://github.com/gehaowu/blog /home/ghw/wwwroot/fonts
git clone --depth 1 -b gh-pages https://github.com/gehaowu/blog /home/ghw/wwwroot/aboutme
git clone --depth 1 -b gh-pages https://github.com/gehaowu/blog /home/ghw/wwwroot/blog
git clone --depth 1 -b gh-pages https://github.com/gehaowu/blog /home/ghw/wwwroot/albums
```


## crontab

```sh
* */5 * * * /bin/sh /home/ghw/wwwroot/gitpull.sh
```



## gitpull.sh

```sh
#!/bin/sh
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
