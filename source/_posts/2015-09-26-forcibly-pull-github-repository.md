title: 强制拉取 Github 仓库内容
toc: false
date: 2015-09-26 21:31:19
tags: [信息技术,开源,Github]
categories: 信息技术
---

## 让 anonymouscoward 君给帮忙写了个 github 同步脚本。。哈哈哈

## 完美解决 github 同步的麻烦问题了

## 将脚本加入 cron 定时执行就可以了，
## ---------------------------------------------
## 如果出现代码需要合并啥的不正常 git pull 情况，
## 则执行脚本会直接删除仓库，然后重新 git clone 仓库回来。。
## ---------------------------------------------

(￣︶￣)↗ 飕装逼了~~~

<!--more-->
```sh

#!/bin/sh
#############################################################
###              《 Github 仓库同步脚本 》
### 同步远程 Github 仓库代码，如果出现异常，则直接删除后重来
### Created by: anonymouscoward <root@anonymous744wjcx.onion>
### Created Date: Sat Sep 26 12:21:00 CST 2015
#############################################################

GITHUB="https://github.com"
DSTDIR="/home/ghw/wwwroot"

REPOS="gehaowu/gehaowu.github.io master www.gehaowu.com
gehaowu/zh-Hans gh-pages www.gehaowu.com/zh-Hans
gehaowu/notes gh-pages www.gehaowu.com/notes
gehaowu/albums gh-pages www.gehaowu.com/albums
gehaowu/resume gh-pages www.gehaowu.com/resume
gehaowu/data gh-pages www.gehaowu.com/data
gehaowu/Resources gh-pages www.gehaowu.com/Resources"

while read repo branch dir
do
    (cd "${DSTDIR}/${dir}" && git pull)
    if [ "$?" -ne 0 ]; then
        rm -fr "${DSTDIR}/${dir}"
        git clone --depth 1 -b "${branch}" "${GITHUB}/${repo}" "${DSTDIR}/${dir}"
    fi
done <<-EOF
${REPOS}
EOF

```