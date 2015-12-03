title: "NGINX 配置多证书环境及强制 https 跳转"
date: 2015-05-23 22:52:00
tags: [信息技术,开源,FreeBSD,NGINX,SSL]
categories: 信息技术
toc: true
---
## 启用 HTTPS 的原因

原因嘛，
1，https 保证网站传说过程中不被修改，
2，Google 提升了 https 网站的权重，
3，。。。

其实，很简单嘛，，，
根本还是在比较装逼呗。。。
![https](https://dn-gehaowu.qbox.me/notes/2015/05/https.png)
小绿锁哟，哈哈哈哈哈哈


下面开始，通过 FreeBSD 的包管理器安 NGINX ，
（个人强力不推荐绕过操作系统包管理工具自己编译的超级装逼行为！）


FreeBSD 安装 NGINX 很简单，如果偷懒，可以直接：
<!-- more -->
```
pkg install -y nginx
```
不过你也可以使用 Ports 安装，因为可以自定义选项→_→
比如开启 NGINX 的 SPDY 模块，make config 出来的界面在 SPDY 前面选中就可以了
```
#cd /usr/ports/www/nginx &&make config
cd /usr/ports/www/nginx &&make install clean
```

![从Ports安装NGINX](https://dn-gehaowu.qbox.me/notes/2015/05/ports.nginx.png)

坐等一会，NGINX 就安好了，比较方便，

安装后配置，因为我的网站是纯静态网站，所以也不需要安其它动态语言。

NGINX 的主配置文件在 /usr/local/etc/nginx/nginx.conf

## NGINX 主配置文件
```
user  www;
worker_processes  5;
events {
    use kqueue;
    multi_accept on;
    worker_connections  1024;
}
http {
    include       mime.types;
    default_type  application/octet-stream;
    sendfile      on;
    tcp_nopush    on;
    #keepalive_timeout  0;
    keepalive_timeout   65;
    access_log          off;
    ssl_protocols       TLSv1 TLSv1.1 TLSv1.2;
    ssl_ciphers         HIGH:!aNULL:!MD5;
    ssl_prefer_server_ciphers on;
    gzip on;
    gzip_disable "msie6";
    gzip_vary on;
    gzip_proxied any;
    gzip_comp_level 6;
    gzip_buffers 16 8k;
    gzip_http_version 1.1;
    gzip_types text/plain text/css application/json application/javascript text/xml application/xml application/xml+rss text/javascript;
    include /usr/local/etc/nginx/sites/*.conf;
}
```
需要特殊优化的可以搜些资料自己搞。。。这里就不累赘了。
在配置里面增加
**include /usr/local/etc/nginx/sites/*.conf;**
这一行内容，接着创建默认站点配置文件


## 创建默认站点配置文件
```
mkdir /usr/local/etc/nginx/sites
$ cat /usr/local/etc/nginx/sites/defaultsite.conf
server {
    listen              80  default_server;
    listen              443 ssl default_server;
    ssl_certificate     keys/defaultsite.crt;
    ssl_certificate_key keys/defaultsite.key;
    server_name         _;
    #charset            utf-8;
    #access_log         logs/host.access.log  main;
    location    / {
        root        /home/wwwroot/default;
        index       index.html index.htm;
    }
    #error_page  404              /404.html;
    # redirect server error pages to the static page /50x.html
    #
    error_page  500 502 503 504  /50x.html;
    location =  /50x.html {
        root    /usr/local/www/nginx-dist;
    }
    location ~ /\.ht {
        deny  all;
    }
    location /NginxStatus {
    stub_status on;
    auth_basic "Nginx Status Authenticate";
    auth_basic_user_file keys/.htpasswd.db;
    access_log   off;
    }
}
```
上面的配置文件注意 **default_server** 行，
在配置文件里增加 **default_server** 后所有无法匹配的访问的请求
都会被转发给这个默认服务。

接着创建个人的虚拟主机配置



## 个人网站的配置文件
```
cat /usr/local/etc/nginx/sites/www.gehaowu.com.conf
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

整个规则流程就是


创建一个 gehaowu.com www.gehaowu.com 的虚拟主机
NGINX 会监听这两个域名的的80号端口，
所有跟这两个域名匹配的请求都会被重写为
**https://www.gehaowu.com**
接着再建一个 gehaowu.com 的 HTTPS 虚拟主机，
当发往 gehaowu.com 主机主机的 443 端口的请求时将请求重写为
**https://www.gehaowu.com**
最后创建一个 www.gehaowu.com 的 HTTPS 虚拟主机
以上的两个虚拟主机重写的链接最后都会发送到这个虚拟主机上，

**ssl_certificate ssl_certificate_key**行，可以直接自己使用openssl生成
也可以去其它证书商申请，

这里推荐 [申请免费SSL证书 - 沃通](https://buy.wosign.com/free/FreeSSL.html)


--------------------
**版权声明**

<a href="https://creativecommons.org/licenses/by-nc-nd/4.0/deed.zh"><img src="https://dn-gehaowu.qbox.me/notes/other/CC-BY-SA-ND.png" alt="署名-非商业性使用-禁止演绎 创意共享4.0国际许可证" /></a>

[Ge Haowu's Personal Website](//www.gehaowu.com/) by [Haowu Ge](//www.gehaowu.com/aboutme/) is licensed under a [CC BY-NC-ND 4.0 International](https://creativecommons.org/licenses/by-nc-nd/4.0/deed.zh) License.
由 [葛豪武](//www.gehaowu.com/aboutme/) 创作并维护在[葛豪武的个人网站](//www.gehaowu.com/) 采用 [署名-非商业性使用-禁止演绎 创意共享 4.0 国际](https://creativecommons.org/licenses/by-nc-nd/4.0/deed.zh) 许可证。


本文首发于 [葛豪武的个人网站！· Ge Haowu's Personal Website!](//www.gehaowu.com/) ，版权所有，侵权必究。