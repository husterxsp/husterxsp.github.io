---
layout: post
title: Mac/Linux 常用命令整理
date: 2017-03-22
author: "Xsp"
catalog: true
tags:
    - Mac
    - Linux
    - Shell
---


### find 文件查找

查找所有后缀是.js的文件 
```
find . -name *.js
```
查找所有.DS_Store 并删除
```
find . -name *.DS_Store -delete
```

### ifconfig / ipconfig 查看本机IP

`ifconfig` 是Mac/Linux的，`ipconfig` 是Win的，以前总是搞混了，不过多用几次就记住了，然后想着可以这样记，两个的差别就只是if/ip 写代码写if比较多啊，写代码当然是mac比较舒服啊，那就是ifconfig了

### which 查找软件安装位置

`which` 指令会在环境变量$PATH设置的目录里查找符合条件的文件。
比如查找mysql 的安装位置 
```
which mysql
```

好像还有whereis、locate

### ps 查看进程

```ps au``` 显示当前用户的进程
```ps aux``` 显示所有包含其他使用者的进程(没太懂，不过一般还是用au就够了)

### grep 过滤

文档解释是 [用于查找文件里符合条件的字符串](http://www.runoob.com/linux/linux-comm-grep.html)。我理解是过滤的意思吧，例如找到当前的node 进程 
``` 
ps au | grep node
```
觉得比较有用的一个参数是 -v (--revert-match) 反转查找，就是取非，例如不显示node进程
``` 
ps au | grep -v node
``` 

### kill 查杀进程

一般遇到的情况是想找到某个进程然后kill掉，在上面的 ```ps au``` 找到对应程序的PID，然后直接kill PID就好了
![](/img/post/2017-03-22-mac-linux-command-1.png)

另外还可以用killall 直接指定进程名，如`killall node`

### tar 压缩/解压

压缩
```
//压缩 test.js 文件为test.tar.gz
tar -czvf test.tar.gz test.js
```
+ -c，--create，建立新的备份文件
+ -z，--gzip或--ungzip，通过gzip指令处理备份文件
+ -v，--verbose，显示指令执行过程。
+ -f，或--file=<备份文件>，指定备份文件


解压

```
tar -xzvf test.tar.gz
```
+ -x，--extract或--get，从备份文件中还原文件

还有zip

[更多Linux命令>>](http://www.runoob.com/linux/linux-command-manual.html)

