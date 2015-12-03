title: MINGW64环境更新HEXO到Github提示TTY错误
toc: true
date: 2015-09-20 20:24:20
tags: [信息技术,开源,HEXO,TTY,MINGW64,Github]
categories: 信息技术
---

最近碰到好多朋友使用 MINGW64 使用 Github + HEXO 搭建博客的时候提示下列错误，

## ERROR INFO
```sh
 #Code Star
The file will have its original line endings in your working directory.
bash: /dev/tty: No such device or address
error: failed to execute prompt script (exit code 1)
fatal: could not read Username for 'https://github.com': Invalid argument
FATAL Something's wrong. Maybe you can find the solution here: http://hexo.io/docs/troubleshooting.html
Error: bash: /dev/tty: No such device or address
error: failed to execute prompt script (exit code 1)
fatal: could not read Username for 'https://github.com': Invalid argument
 #Code End
```

目测是 *终端* 哪个地方出问题，
<!--more-->

偷懒的做法是换 Github 客户端自带的 SHELL ，

## Github Shell
![Github Shell](https://dn-gehaowu.qbox.me/notes/2015/09/githubshell.png)

就像酱紫。。

[GitHub Desktop](https://desktop.github.com/)