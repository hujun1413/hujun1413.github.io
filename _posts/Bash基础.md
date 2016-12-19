---
title:  Bash基础
date: 2016-11-13 20:35:26 # 文章生成时间，一般不改
categories:   # 文章分类目录，参数可省略
    - Linux
tags:   # 文章标签，参数可省略
    - Bash
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
```




