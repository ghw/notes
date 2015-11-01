title: 更新我的 NGINX 配置文件
toc: false
date: 2015-09-27 13:59:13
tags: [信息技术,开源,FreeBSD,NGINX,SSL]
categories: 信息技术
---

最新版配置文件：[My-NGINX-Config](https://github.com/ghw/nginx_config)

## 不需要多创建两个虚拟机了。。。简洁多了，哈哈哈

2015，11，01，懒得管配置文件了。。。直接去我的

--->>> [NGINX 配置文件仓库](https://github.com/ghw/nginx_config)

sites/gehaowu.com.conf
```sh
server {
    server_name				gehaowu.com www.gehaowu.com;
    include						includes/listen_full.template;
    include						includes/header_add.template;
    include						includes/headers_set_more.template;
    include						sites/rewrite.gehaowu.com.template;
    ssl_certificate				keys/www.gehaowu.com.crt;
    ssl_certificate_key			keys/www.gehaowu.com.key;
    ssl_trusted_certificate			keys/startcom_ssl_trusted_certificate.crt;
    ssl_dhparam				keys/dhparam.pem;
    ssl_ecdh_curve				secp384r1;
    include						includes/ssl.template;
    include						includes/ssl_stapling.template;
    set						$rewriterule https;
    if							($scheme = https) {
    set						$rewriterule "${rewriterule}1";
    }
    if							($host ~* ^www.) {
    set						$rewriterule "${rewriterule}2";
    }
    if							($rewriterule != "https12") {
    return						301 https://www.gehaowu.com$request_uri;
    }
    include						includes/block_files.template;
    include						includes/expires.template;
    index						index.html index.htm;
    root						/usr/home/wwwroot/www.gehaowu.com;
    error_page					404 /404.html;
    error_page					403 /403.html;
    error_page					500 502 503 504 /50x.html;
    access_log					/usr/home/wwwroot/logs/gehaowu.com.access.log;
    error_log					/usr/home/wwwroot/logs/gehaowu.com.error.log;
}
```