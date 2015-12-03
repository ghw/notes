title: 通过cron定期从github上拉去个人网站数据
date: 2015-06-09 13:43:32
tags: [信息技术,开源,NGINX,SSL,Github]
categories: 信息技术
toc: true
---

## 创建个人网站主机配置文件，

先设置几个域名的虚拟主机，

，让所有请求 gehaowu.com,www.gehaowu.com 两个域名的 http 请求，强制跳转到 https 协议的 www.gehaowu.com ，
接着一个对发给 gehaowu.com 的 https 请求也强制转发给 www.gehaowu.com 。

关闭日志记录。

<!-- more -->
```sh
## NGINX 虚拟主机配置

# 监听 gehaowu.com 与 www.gehaowu.com 的 80 端口的请求
# 除百度蜘蛛外全部 301 到 https://www.gehaowu.com
server {
    listen                              80;
    listen                              [::]:80;
    server_name                         gehaowu.com www.gehaowu.com;
    add_header                          Strict-Transport-Security max-age=31536000;
    if ($http_user_agent !~* baidu.com) {
        rewrite                         ^/(.*) https://www.gehaowu.com/$1 permanent;
    }
    index                               index.html;
    root                                /home/ghw/wwwroot;
    error_page                          404 /404.html;
    error_page                          403 /403.html;
    error_page                          500 502 503 504 /50x.html;
    access_log                          /home/ghw/wwwlog/baidu.access.log;
    error_log                           /home/ghw/wwwlog/baidu.error.log;
}

# 监听 gehaowu.com 的 443 请求
# 强制跳转到 https://www.gehaowu.com
server {
    listen                              443 ssl spdy;
    listen                              [::]:443 ssl spdy;
    server_name                         gehaowu.com;
    ssl_certificate                     keys/www.gehaowu.com.crt;
    ssl_certificate_key                 keys/www.gehaowu.com.key;
    ssl_protocols                       TLSv1 TLSv1.1 TLSv1.2;
    ssl_ciphers                         EECDH+ECDSA+AESGCM:EECDH+aRSA+AESGCM:EECDH+ECDSA+SHA256:EECDH+aRSA+SHA256:EECDH+ECDSA+SHA384:EECDH+ECDSA+SHA256:EECDH+aRSA+SHA384:EDH+aRSA+AESGCM:EDH+aRSA+SHA256:EDH+aRSA:EECDH:!DH:!aNULL:!eNULL:!MEDIUM:!LOW:!3DES:!MD5:!EXP:!PSK:!SRP:!DSS:!RC4:!SEED;
    ssl_prefer_server_ciphers           on;
    add_header                          Strict-Transport-Security max-age=31536000;
    ssl_session_cache                   shared:SSL:5m;
    ssl_session_timeout                 5m;
    ssl_stapling                        on;
    ssl_stapling_verify                 on;
    ssl_trusted_certificate             keys/www.gehaowu.com.crt;
    resolver                            8.8.4.4 8.8.8.8;
    rewrite                             ^/(.*) https://www.gehaowu.com/$1 permanent;
}

# 虚拟主机 https://www.gehaowu.com 的配置部分
server {
    listen                              443 ssl spdy;
    listen                              [::]:443 ssl spdy ipv6only=on;
    server_name                         www.gehaowu.com;
    ssl_certificate                     keys/www.gehaowu.com.crt;
    ssl_certificate_key                 keys/www.gehaowu.com.key;
    ssl_protocols                       TLSv1 TLSv1.1 TLSv1.2;
    ssl_ciphers                         EECDH+ECDSA+AESGCM:EECDH+aRSA+AESGCM:EECDH+ECDSA+SHA256:EECDH+aRSA+SHA256:EECDH+ECDSA+SHA384:EECDH+ECDSA+SHA256:EECDH+aRSA+SHA384:EDH+aRSA+AESGCM:EDH+aRSA+SHA256:EDH+aRSA:EECDH:!DH:!aNULL:!eNULL:!MEDIUM:!LOW:!3DES:!MD5:!EXP:!PSK:!SRP:!DSS:!RC4:!SEED;
    ssl_prefer_server_ciphers           on;
    add_header                          Strict-Transport-Security max-age=31536000;
    ssl_session_cache                   shared:SSL:5m;
    ssl_session_timeout                 5m;
    ssl_stapling                        on;
    ssl_stapling_verify                 on;
    ssl_trusted_certificate             keys/www.gehaowu.com.crt;
    resolver                            8.8.4.4 8.8.8.8;
    index                               index.html;
    root                                /home/ghw/wwwroot;
    error_page                          404 /404.html;
    error_page                          403 /403.html;
    error_page                          500 502 503 504 /50x.html;
    access_log                          /home/ghw/wwwlog/www.gehaowu.com.access.log;
    error_log                           /home/ghw/wwwlog/www.gehaowu.com.error.log;

# 反向代理 bookcase 子目录
    location                            = /bookcase {
        return                          301 /bookcase/;
    }
    location                            ^~ /bookcase {
        proxy_pass                      https://gehaowu.github.io;
        proxy_set_header                Host gehaowu.github.io;
        proxy_set_header                X-Host gehaowu.github.io;
        proxy_set_header                X-Forwarded-For $proxy_add_x_forwarded_for;
#       proxy_cache                     ghw;
#       proxy_cache_valid               200 302 1h;
#       proxy_cache_valid               404 1m;

    }
    location                            = /life {
        return                          301 /life/;
    }
    location                            ^~ /life {
        proxy_pass                      https://gehaowu.github.io;
        proxy_set_header                Host gehaowu.github.io;
        proxy_set_header                X-Host gehaowu.github.io;
        proxy_set_header                X-Forwarded-For $proxy_add_x_forwarded_for;
    }

# 静止访问 "." 开头的隐藏文件
    location                            ~ /\. {
        deny                            all;
    }

# robots.txt 、 favicon.ico ，status.gif 文件访问不记录日志
    location                            ~* ^/(favicon.ico|robots.txt|status.gif)$ {
        allow                           all;
        access_log                      /dev/null;
        error_log                       /dev/null;
    }


# 静态文件超期
    location                            ~* \.(html|htm|xml|rss|atom|txt|xhtml)$ {
        expires                         1d;
    }

    location                            ~* \.(css|gif|jpeg|jpg|js|png|ico|bmp|svg|doc|pdf|mp3|ogg|mp4|mpeg|webm|eot|ttf|woff)$ {
        expires                         7d;
    }
}
```

![freebsdlogo.sh](https://dn-gehaowu.qbox.me/notes/2015/06/freebsd_gehaowu.com.png)



新建一个网站根目录， 比如我这里使用 ***/home/ghw/*** ，接着执行 gitclone.sh 文件


## 首次 clone 用的 gitclone.sh 脚本

```sh
#!/bin/sh
PATH=/bin:/sbin:/usr/bin:/usr/sbin:/usr/local/bin:/usr/local/sbin
git clone --depth 1 -b gh-pages https://github.com/gehaowu/www.gehaowu.com /home/ghw/wwwroot
git clone --depth 1 -b gh-pages https://github.com/gehaowu/aboutme /home/ghw/wwwroot/aboutme
git clone --depth 1 -b gh-pages https://github.com/gehaowu/notes /home/ghw/wwwroot/notes
git clone --depth 1 -b gh-pages https://github.com/gehaowu/albums /home/ghw/wwwroot/albums
```

![gitclone.sh](https://dn-gehaowu.qbox.me/notes/2015/06/gitclone_gehaowu.com.png)


因为我在 wwwroot 根目录放了 gitpull.sh 脚本，所以直接给 cron 添加计划任务：

## 让 crontab 每 5 个小时执行一次 pull 脚本

```sh
# 在需要运行 gitpull.sh 脚本的用户上执行 crontab -e  ，添加以下内容：
* */5 * * * /bin/sh /home/ghw/wwwroot/gitpull.sh >/dev/null 2>&1
```

![gitpull.sh](https://dn-gehaowu.qbox.me/notes/2015/06/gitpull_gehaowu.com.png)

## gitpull.sh 脚本的内容

因为 blog 模块实际上会出现向github提交的时候会使用-f参数，所以我们下拉的时候需要先清理掉分支
```sh
#!/bin/sh
PATH=/bin:/sbin:/usr/bin:/usr/sbin:/usr/local/bin:/usr/local/sbin
export LC_ALL=zh_CN.UTF-8
date >> /home/ghw/gitpulldate.log
cd /home/ghw/wwwroot/
git pull
cd /home/ghw/wwwroot/aboutme/
git pull
cd /home/ghw/wwwroot/notes/
git pull
git reset --hard
cd /home/ghw/wwwroot/albums/
git pull
```


搞完，这样就可以了，
没五个小时从 github 上拉取网站数据，所以我们只需要将自己的网站数据同步到github上就可以了。。。
十分容易也十分安全，哈哈哈。。。<(￣▽￣)>



--------------------
**版权声明**

<a href="https://creativecommons.org/licenses/by-nc-nd/4.0/deed.zh"><img src="https://dn-gehaowu.qbox.me/notes/other/CC-BY-SA-ND.png" alt="署名-非商业性使用-禁止演绎 创意共享4.0国际许可证" /></a>

[Ge Haowu's Personal Website](//www.gehaowu.com/) by [Haowu Ge](//www.gehaowu.com/aboutme/) is licensed under a [CC BY-NC-ND 4.0 International](https://creativecommons.org/licenses/by-nc-nd/4.0/deed.zh) License.
由 [葛豪武](//www.gehaowu.com/aboutme/) 创作并维护在[葛豪武的个人网站](//www.gehaowu.com/) 采用 [署名-非商业性使用-禁止演绎 创意共享 4.0 国际](https://creativecommons.org/licenses/by-nc-nd/4.0/deed.zh) 许可证。


本文首发于 [葛豪武的个人网站！· Ge Haowu's Personal Website!](//www.gehaowu.com/) ，版权所有，侵权必究。