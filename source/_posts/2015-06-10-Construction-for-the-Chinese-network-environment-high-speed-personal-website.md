title: 构建面向国内网络环境的高速个人网站
date: 2015-06-10 08:16:00
tags: [信息技术,开源,加速乐,CDN,Github]
categories: 信息技术
toc: true
---

前些日子在万网订购了一个款免费的虚拟主机产品，速度很快。。。但是。。。
竟然对中文目录支持如此恶心。。想想，算了，放弃主机产品。。。
（说的那么好好像真的要用一样的，，，其实我申请那个主机主要是为了备案的啦，
既然面向国内用户，且不会写**不良**信息，那么备案也没什么大不了的。）


## 重新选择托管方案

免费托管方案明细选择 [github](https://github.com)  -_-!

由于 Github 本身在国外，从过来拉取数据会有较大的网络延时，如果就这样直接托管在 Github 上，
别说高速网站了，连能不能有效访问都是个问题，因为大中华区还有一款名为长城防火墙网络版的存在啊。。哈哈哈


<!-- more -->
## 变更新方案

所以我们换转一个架构，

将 访客访问请求 --> 主机托管商 的模式

切换为

访客访问请求 --> 国内 CDN 服务商  --> 主机托管商

这里我选择国内的 [加速乐](https://www.jiasule.com) 作为 CDN 服务商，主机依旧选择 Github 。

也就是 访客浏览器 -> 加速乐 -> Github 。

![加速乐](https://dn-nimages.qbox.me/2015/06/jiasule_gehaowu.com.png)

![Github](https://dn-nimages.qbox.me/2015/06/github_gehaowu.com.png)
## 为主站添加 **robots.txt** 文件，
Github 会主动扩展账户下的分支为 gh-pages 的子仓库，避免被搜索引擎收录，屏蔽之。

```sh
# robots.txt
User-agent: *
Disallow: /*.old/
Disallow: /Source_*/
Disallow: /wwwroot_*/
```

[我的个人网站](//www.gehaowu.com/)

欢迎光临，哈哈哈~~~



--------------------
**版权声明**

<a href="https://creativecommons.org/licenses/by-nc-nd/4.0/deed.zh"><img src="https://dn-nimages.qbox.me/other/CC-BY-SA-ND.png" alt="署名-非商业性使用-禁止演绎 创意共享4.0国际许可证" /></a>

[Ge Haowu's Personal Website](//www.gehaowu.com/) by [Haowu Ge](//www.gehaowu.com/aboutme/) is licensed under a [CC BY-NC-ND 4.0 International](https://creativecommons.org/licenses/by-nc-nd/4.0/deed.zh) License.
由 [葛豪武](//www.gehaowu.com/aboutme/) 创作并维护在[葛豪武的个人网站](//www.gehaowu.com/) 采用 [署名-非商业性使用-禁止演绎 创意共享 4.0 国际](https://creativecommons.org/licenses/by-nc-nd/4.0/deed.zh) 许可证。


本文首发于 [葛豪武的个人网站！· Ge Haowu's Personal Website!](//www.gehaowu.com/) ，版权所有，侵权必究。