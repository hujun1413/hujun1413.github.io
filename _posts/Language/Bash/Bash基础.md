---
title:  Bash基础
date: 2016-11-13 20:35:26 # 文章生成时间，一般不改
categories:   # 文章分类目录，参数可省略
    - 编程语言
    - Bash
tags:   # 文章标签，参数可省略
---
### 打印
```bash
#!/bin/bash
echo Hello World
echo 'Hello World'
echo $"Hello World"    #bash hello.sh输出Hello World,sh hello.sh输出$Hello World

a=5
echo "$a"  #输出5
echo $a  #输出5
echo "a"  #输出a
```
<!--more-->

### 执行
```bash
bash hello.sh

sh hello.sh  #sh是指向bash的一个链接，/bin.sh->bash

chmod u+x hello.sh
./hello.sh
```
### 变量和运算
```bash
var=`python read.py`  #a赋值为read.py的返回值，=左右不能有空格
echo $var
echo '$var'   #打印的是$var这个字符串
echo ${var}  #标准写法，也可省略{},变为echo $"var"
```

### [echo命令输出](http://blog.sina.com.cn/s/blog_4da051a6010184uk.html)
* echo -n 不换行输出
* echo -e 处理特殊字符

### if语法
``` bash
if [ condition ]   #注意[]前后的空格
then
...
fi

```



### 平时杂
#### [${}用法](http://unixboy.iteye.com/blog/499329)
\# 是去掉左边(在键盘上 # 在 $ 之左边)

% 是去掉右边(在键盘上 % 在 $ 之右边)

单一符号是最小匹配；两个符号是最大匹配。
``` bash
file=/dir1/dir2/dir3/my.file.txt

${file#*/}：拿掉第一条 / 及其左边的字串：dir1/dir2/dir3/my.file.txt
${file##*/}：拿掉最后一条 / 及其左边的字串：my.file.txt
${file%.*}：拿掉最后一个 . 及其右边的字串：/dir1/dir2/dir3/my.file
${file%%.*}：拿掉第一个 . 及其右边的字串：/dir1/dir2/dir3/my
```

#### [测试相等](http://blog.sina.com.cn/s/blog_605f5b4f01013lui.html)

#### `cat /dev/null > $logfile`
清空$logfile文件





