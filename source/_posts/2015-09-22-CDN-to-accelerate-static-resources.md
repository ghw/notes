title: 静态资源临时重定向到CDN实现网站加速
toc: false
date: 2015-09-22 21:16:40
tags: [信息技术,开源,NGINX,SSL,HTTPS]
categories: 信息技术
---

把图片啥的，直接重定向到七牛上了。。还是302方便

![302](https://dn-gehaowu.qbox.me/notes/2015/09/IMAGES302CDN.png)

```sh
	location                            ~* \.(gif|jpeg|jpg|png|bmp|svg|otf|woff|eot|ttf|woff2)$ {
	rewrite                             ^/(.*) https://dn-gehaowu-com.qbox.me/$1 redirect;
	}
```