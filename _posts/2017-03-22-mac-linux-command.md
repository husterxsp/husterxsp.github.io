---
layout: post
title: Mac/Linux 常用命令整理
date: 2017-03-22
author: "Xsp"
catalog: true
tags:
    - Mac/Linux
    - Shell
---


### find 文件查找

查找所有后缀是.js的文件
```
find . -name "*.js"
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

`ps au`显示当前用户的进程
`ps aux` 显示所有包含其他使用者的进程(没太懂，不过一般还是用au就够了)

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

一般遇到的情况是想找到某个进程然后kill掉，在上面的 `ps au` 找到对应程序的PID，然后直接kill PID就好了
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

### mv 文件重命名

若a、b都是文件名，那当时直接将a重命名为b了，若b是目录就把a直接移入b(b不存在会新建)。
```
mv a b
```

还有rename，ubuntu可以用，但是[mac好像没有rename](https://discussions.apple.com/thread/1760288?start=0&tstart=0)
，在ubuntu输入`man rename` 可以看到一些使用说明。例如执行
```
//利用正则，把文件名中的AA替换成aa
rename "s/AA/aa/" *
```

### chmod 777 添加权限

之前有遇到无权访问本地mongodb数据库的问题，随手`chmod 777 -R /data`就可以解决。需要注意 `-R`不能忘记加了，它代表 —recursive 递归整个目录修改权限。

7的含义：r=4，w=2，x=1，r表示可读取，w表示可写入，x表示可执行。7即代表可读、写、执行。

777的含义：分别代表文件拥有者、群组、其他用户的权限。

chmod 777 就代表所有人都可读、写、执行了。执行`ll` 看下权限
![](/img/post/2017-03-22-mac-linux-command-2.png)

可看到上图最左侧一列即代表权限，这一列有11位，第一位字母表示文件类型，d是目录文件，l是链接文件，-是普通文件。中间9位就代表文件权限的那几位了。最后一位ubuntu上没有，[查了一下具体解释戳这里](https://www.zhihu.com/question/25069561)。图里还有个wheel，[查了一下](http://blog.csdn.net/cbbbc/article/details/51712797)大概是管理员用户的意思。


[更多Linux命令>>](http://www.runoob.com/linux/linux-command-manual.html)
