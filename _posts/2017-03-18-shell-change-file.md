---
layout: post
title: "Shell 修改文件内容"
date: 2017-03-18
author: "Xsp"
tags:
    - Shell
---

如果想批量修改整个项目里面的一些内容，用Shell还是比较方便的，当然这样的一些经常需要修改的内容，应当单独在一个配置文件里声明比较好。


如想修改项目里有用到的ip地址

``` shell
#!/bin/bash
# $1 = windows ip

if [ $# != 1 ] ; then 
    echo "USAGE: $0 WindowsIP"
    exit 1; 
fi

# 获取本机IP
# 由于本机IP可能活获取到好几个，这个用 grep '133' 进行了过滤，可以自己根据自己的IP需要进行过滤
# grep -v '169' 则就是不取含有'169'的
# 注意，变量名和等号之间不能有空格
addr=`/sbin/ifconfig|grep inet|grep -v 127.0.0.1|grep -v inet6|grep '133'|awk '{print $2}'|tr -d "addr:"`

if  [ ! -n "$addr" ] ;then
    echo "please modeify the shell to grep your localIp addr"
    exit
fi

# 定义需要修改的文件数组
localIp=(
    'js/1.js'
    'js/2.js'
)

# 修改本地IP
for str in ${localIp[*]}
do
    # DbConn.js
    sed -i "" "s/mongodb:\/\/.*:27017/mongodb:\/\/${addr}:27017/g" $str

    # properties.js
    sed -i "" "s/databaseIp = \".*\"/databaseIp = \"${addr}\"/g" $str

done
```

需要注意的是Mac和Linux下的sed命令还有点不一样（坑。。），
以上的命令在Linux上就是

```shell
    # 少了第三个个参数 ""
    sed -i "s/mongodb:\/\/.*:27017/mongodb:\/\/${addr}:27017/g" $str
```
具体原因可以参看 [sed: -i may not be used with stdin on Mac OS X](http://stackoverflow.com/questions/21242932/sed-i-may-not-be-used-with-stdin-on-mac-os-x)

另外，在ubuntu上运行的时候，因为数组的原因，会报错 ```Syntax error: "(" unexpected```，参考[网上的解答](http://bbs.csdn.net/topics/390267545)用  /bin/bash 来运行就可以了。（具体原因等以后对shell更熟了再补充好了。。）

更多的shell 命令学习 [Linux 命令大全](http://www.runoob.com/linux/linux-command-manual.html)

用Nodejs来执行这些shell命令
```javascript
const exec = require('child_process').exec;

exec('sed -i "s/world/world1111/g" 2.js', (error, stdout, stderr) => {
    if (error) {
        console.error(`exec error: ${error}`);
        return;
    }
    console.log(`stdout: ${stdout}`);
    console.log(`stderr: ${stderr}`);
});
```

用Node来实现文件的读写修改
```javascript
    #!/usr/bin/env node
    /**
        process.argv 表示命令行传入的参数
        [ '/Users/xushaopeng/.nvm/versions/node/v7.1.0/bin/node',
          '/Users/xushaopeng/Desktop/configure.js',
          '11',
          '112',
          '22' ]
    */
    var args = process.argv.slice(2);
    var fs = require('fs');

    if (args.length < 1) {
        console.log('请输入参数');
        return;
    }

    var fileArr = [
        './1.js'
    ];

    var pattern = /(serverIp=).*/g;
    fileArr.forEach(function (fileName) {
        // 同步读取文件，修改，再写入
        var file = fs.readFileSync(fileName, {encoding: 'UTF-8'});
        var result = file.replace(pattern, '$1' + args[0]);
        fs.writeFileSync(fileName, result, {encoding: 'UTF-8'});
    });
```

使用封装过后的shelljs模块
```javascript
require('shelljs/global');

ls('*.js').forEach(function(file) {
    sed('-i', 'hello', 'world', file);
});
```
shelljs通过对Node进行一些封装来模拟实现一些shell命令。比如sed命令，shelljs其实也是用的readFileSync，replace，writeFileSync这样的几个步骤来完成。这样通过Node来实现的一个好处就是没有跨平台的问题，比如sed命令在Mac和Linux上的不一致。参看[Node.js 命令行程序开发教程 - 阮一峰](http://www.ruanyifeng.com/blog/2015/05/command-line-with-node.html)。

