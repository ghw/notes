title: 通过 VeryCloud 为个人网站实现 HTTPS 功能
toc: true
date: 2015-08-02 16:36:34
tags: [信息技术,开源,VeryCloud,SSL,HTTPS,Github]
categories: 信息技术
---

## 需求

提高国内用户的访问体验，并实现 HTTPS 功能。

## 必备条件

1，你要有个域名
2，你要有备案号
3，你要有备案号
4，你要有备案号

重要的事情说三遍 →_→
<!--more-->

## 闲扯一下

主机在国外的情况下，由于距离远与长城防火墙的存在，导致挂在国外的网站打开速度十分缓慢，

所以想到套一层国内 CDN 来提高网站速度，同时出于装逼需要，
最终选择了 [VeryCloud](https://www.verycloud.cn/) 作为 CDN 加速商 （我需要 HTTPS 功能）

**网站源站可以使用任何一个你顺手的网络空间，如： [Github.io](https://www.github.io/)， [GitCafe.io](https://www.gitcafe.io/)**

## 开始捣腾

### 选取主机服务提供商
直接把个人网站托管到 github 跟 gitcafe 就可以了，一个主站，另外一个做备份站。
像仓库添加 CNAME 文件后托管商会自动给你做绑定，
更多信息请自行 Google

### 申请 HTTPS 网站证书
去沃通申请一个证书 [沃通数字证书在线申请](https://buy.wosign.com/free/)

### 配置 VeryCloud 加速
到 VeryCloud 注册你的帐号
将申请到的 SSL 证书添加到 VeryCloud 中去
进入 VeryCloud 控制台，
![VeryCloud 控制台](//dn-nimages.qbox.me/2015/08/VeryCloudSSL0.png)
选择 *云分发* -> *证书管理* -> *新建*
![新建证书](//dn-nimages.qbox.me/2015/08/VeryCloudSSL1.png)
证书名称随便填，自己知道是哪一份就可以了
授权证书就是 CA 签发给你的证书
证书密钥。。。
CA 机构证书就是证书链，证书商会提供，可选选项
证书添加完成后就可以添加域名了
这里**推荐使用二级域名**，因为 VeryCloud 使用 CNAME CDN 方案，如果裸域名使用 CNAME 会破坏当前域上的其它 DNS 记录。
选择 *云分发* -> *频道管理* -> *新建*
![基础设置](//dn-nimages.qbox.me/2015/08/VeryCloudAdd1.png)
频道选择你需要加速的二级域名，我使用 *www.gehaowu.com*
ICP 备案号会自动获取
加速类型选择云分发
源站填 *gehaowu.github.io* ，端口80
接着选择下一步
![策略设置](//dn-nimages.qbox.me/2015/08/VeryCloudAdd2.png)
策略选择 *遵循源站* 就可以了，你也可以选择自定义，
接着再下一步
![高级设置](//dn-nimages.qbox.me/2015/08/VeryCloudAdd3.png)
备份源站填 *gitcafe.io* ，端口80
证书选择之前添加的证书
创建，等官方审核通过。
### 配置 DNS 记录
审核通过后修改 DNS 记录
修改 DNS 记录后台，为 *www* 添加 *CNAME* 记录值 *www.gehaowu.com.verygslb.com*
![DNSPod添加www记录](//dn-nimages.qbox.me/2015/08/DNSPod.png)
并将裸域名解析到 github.io 所在 IP （这里由 Github 实现 http://gehaowu.com 跳转到 http://www.gehaowu.com）
## 收官，开始装逼吧~~
最终的效果（忽悠朋友也开了VeryCloud的）
```sh

% curl -I https://www.ioliu.cn
HTTP/1.1 200 OK
Server: GitHub.com
Content-Type: text/html; charset=utf-8
Last-Modified: Sat, 01 Aug 2015 15:37:36 GMT
Access-Control-Allow-Origin: *
Expires: Sun, 02 Aug 2015 10:05:56 GMT
Cache-Control: max-age=600
Content-Length: 32799
Accept-Ranges: bytes
Date: Sun, 02 Aug 2015 09:55:56 GMT
Via: 1.1 varnish
Age: 17
X-Served-By: cache-nrt6131-NRT
X-Cache: MISS
X-Cache-Hits: 0
X-Timer: S1438509356.272132,VS0,VE325
Vary: Accept-Encoding
Strict-Transport-Security: max-age=15552000
Powered-By-VeryCDN: MISS from ctc-dy-2-1-c1761, STALE from utn-cz-1-4-c17a1
Connection: keep-alive

%

```
![我的后台选项](//dn-nimages.qbox.me/2015/08/VeryCloud1.png)
我的源站并没有使用Github，直接挂在朋友的VPS上的
![CURL](//dn-nimages.qbox.me/2015/08/VeryCloud2.png)
我是高级装逼工程师，我是巨硬爱好者，，，哈哈哈


--------------------
**版权声明**

<a href="https://creativecommons.org/licenses/by-nc-nd/4.0/deed.zh"><img src="//dn-nimages.qbox.me/other/CC-BY-SA-ND.png" alt="署名-非商业性使用-禁止演绎 创意共享4.0国际许可证" /></a>

[Ge Haowu's Personal Website](//www.gehaowu.com/) by [Haowu Ge](//www.gehaowu.com/aboutme/) is licensed under a [CC BY-NC-ND 4.0 International](https://creativecommons.org/licenses/by-nc-nd/4.0/deed.zh) License.
由 [葛豪武](//www.gehaowu.com/aboutme/) 创作并维护在[葛豪武的个人网站](//www.gehaowu.com/) 采用 [署名-非商业性使用-禁止演绎 创意共享 4.0 国际](https://creativecommons.org/licenses/by-nc-nd/4.0/deed.zh) 许可证。


本文首发于 [葛豪武的个人网站！· Ge Haowu's Personal Website!](//www.gehaowu.com/) ，版权所有，侵权必究。