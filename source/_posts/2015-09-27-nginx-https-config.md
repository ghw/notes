title: 更新我的 NGINX 配置文件
toc: false
date: 2015-09-27 13:59:13
tags: [信息技术,开源,FreeBSD,NGINX,SSL]
categories: 信息技术
---

## 不需要多创建两个虚拟机了。。。简洁多了，哈哈哈

```sh
################################################################
# NGINX 虚拟主机 gehaowu.com 主配置文件
# 监听IPv4,IPv6地址，监听80及SSL的443端口
# 强制重定向 HTTP 请求到 https://www.gehaowu.com
# 强制重定向 https://gehaowu.com 请求到 https://www.gehaowu.com
################################################################
server {
	charset								utf-8;
	listen								80;
	listen								443 ssl http2;
	listen								[::]:80;
	listen								[::]:443 ssl http2;
	server_name							gehaowu.com www.gehaowu.com;
	ssl_certificate							/usr/home/ghw/wwwroot/keys/www_gehaowu_com.crt;
	ssl_certificate_key						/usr/home/ghw/wwwroot/keys/www_gehaowu_com.key;
	ssl_ciphers							EECDH+ECDSA+AESGCM:EECDH+aRSA+AESGCM:EECDH+ECDSA+SHA256:EECDH+aRSA+SHA256:EECDH+ECDSA+SHA384:EECDH+ECDSA+SHA256:EECDH+aRSA+SHA384:EDH+aRSA+AESGCM:EDH+aRSA+SHA256:EDH+aRSA:EECDH:!DH:!aNULL:!eNULL:!MEDIUM:!LOW:!3DES:!MD5:!EXP:!PSK:!SRP:!DSS:!RC4:!SEED;
	ssl_protocols							TLSv1 TLSv1.1 TLSv1.2;
	ssl_prefer_server_ciphers				on;
	ssl_session_cache						shared:SSL:5m;
	ssl_session_timeout						10m;
	ssl_stapling							on;
	ssl_stapling_verify						on;
	ssl_trusted_certificate					/usr/home/ghw/wwwroot/keys/startcom_ssl_trusted_certificate.crt;
	add_header							Strict-Transport-Security max-age=31536000;
	resolver								8.8.8.8 8.8.4.4 [2001:4860:4860::8888]:5353 [2001:4860:4860::8844]:5353 valid=30s;
	more_set_headers						"Server: Microsoft-IIS/7.77";
	if									($scheme = http) {
	return								301 https://www.gehaowu.com$request_uri; }
	if									($host !~* ^www\.) {
	rewrite								^(.*) https://www.gehaowu.com$1 permanent; }
	index								index.html index.htm;
	root									/usr/home/ghw/wwwroot/www.gehaowu.com;
	error_page							404 /404.html;
	error_page							403 /403.html;
	error_page							500 502 503 504 /50x.html;
	access_log							/usr/home/ghw/wwwroot/logs/access.gehaowu.com.log;
	error_log								/usr/home/ghw/wwwroot/logs/error.gehaowu.com.log;
# 反向代理 bookcase 子目录
	location								= /bookcase {
		return							301 /bookcase/; }
	location								^~ /bookcase {
		proxy_pass						http://gehaowu.github.io;
		proxy_set_header					Host www.gehaowu.com;
		proxy_set_header					X-Host www.gehaowu.com;
		proxy_set_header					X-Forwarded-For $proxy_add_x_forwarded_for; }
# 静止访问 "." 开头的隐藏文件
	location								~ /\. {
		deny								all; }
# robots.txt 、 favicon.ico ，status.gif 文件访问不记录日志
	location								~* ^/(favicon.ico|robots.txt|status.gif)$ {
		log_not_found						off;
		access_log						off; }
# 静态文件超期
	location								~* \.(html|htm|xml|rss|atom|txt|xhtml)$ {
		expires							1d; }
	location								~* \.(css|gif|jpeg|jpg|js|png|ico|bmp|svg|doc|pdf|mp3|ogg|mp4|mpeg|webm|eot|ttf|woff)$ {
		expires							3d; }
# 图片文件 302 重定向到 CDN
	#location								~* \.(gif|jpeg|jpg|png|bmp|svg)$ {
	#	rewrite							^/(.*) https://dn-gehaowu-com.qbox.me/$1 redirect; }
# NGINX STATUS 服务
	#location								/nginxstatus {
	#	stub_status						on;
	#	auth_basic						"Welcome to https://www.gehaowu.com Website!!!";
	#	auth_basic_user_file				/usr/home/ghw/wwwroot/.htaccess;
	#}
}
```

最新版配置文件：[gehaowu.com.conf](https://github.com/gehaowu/MyServerConfigs/blob/gh-pages/FreeBSD/Webserver/NGINX/sites/gehaowu.com.conf)