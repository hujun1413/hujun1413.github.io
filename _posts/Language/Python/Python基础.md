---
title:  Python基础
date: 2016-12-19 20:35:26 # 文章生成时间，一般不改
categories:   # 文章分类目录，参数可省略
    - 编程语言
    - Python
tags:   # 文章标签，参数可省略
---
## 参考资料
* [Python3.5.2官方文档](https://docs.python.org/3/library/functions.html#abs)
* [廖雪峰的官方网站Python教程](http://www.liaoxuefeng.com/wiki/0014316089557264a6b348958f449949df42a6d3a2e542c000/001432339008728d0ddbe19ee594980be3f0644a9371894000)
* [Python快速教程](http://www.cnblogs.com/vamei/archive/2012/09/13/2682778.html)
* [某Python学习笔记](http://lovenight.github.io/2016/09/25/Python-3-学习笔记/)
* [服务器上的文件的上传和下载](http://www.cnblogs.com/rollenholt/p/4917032.html)
<!--more-->

## 安装
根据你的Windows版本（64位还是32位）从Python的官方网站下载Python 3.5对应的[64位安装程序](https://www.python.org/ftp/python/3.5.2/python-3.5.2-amd64.exe)或[32位安装程序](https://www.python.org/ftp/python/3.5.2/python-3.5.2.exe)（网速慢的同学请移步国内镜像），然后，运行下载的EXE安装包：

**特别注意勾上 `Add Python 3.5 to PATH` , 然后点“Install Now”即可。**

### Python解释器
* CPython，官方版本的解释器
* IPython，交互式解释器
* PyPy，采用JIT技术，动态编译来提高执行速度
* Jython，把Python编译成Java字节码
* IronPython，把Python编译成.Net字节码

Python的解释器很多，但使用最广泛的还是CPython。如果要和Java或.Net平台交互，最好的办法不是用Jython或IronPython，而是通过**网络调用**来交互，确保各程序之间的独立性。

### 执行
绝对不能用Word和Windows自带的记事本编辑python代码，Word保存的不是纯文本文件，而记事本会自作聪明地在文件开始的地方加上几个特殊字符（UTF-8 BOM），结果会导致程序运行出现莫名其妙的错误。
```bash
Windows，Mac和Linux:
python test.py

Mac和Linux:
在文件头加一句
#!/usr/bin/env python3

./test.py
```

**用Python开发程序，完全可以一边在文本编辑器里写代码，一边开一个交互式命令窗口，在写代码的过程中，把部分代码粘到命令行去验证，事半功倍！**

## Python基础
### 输入输出
```python
name = input('Please input your name: ')
print('Hello', name)  #,相当于空格
print("I\'m OK \"haha\"")
print("I'm OK!")
print(r'''line1\n
line2
line3''')    #字符串前的r将\n不理解为换行，而是反斜杠\+字母n
print('''line1\n
line2
line3''')
```

### 数据类型和变量
#### 布尔值
and or not
```
print(3>2 or 2>3)

output: True
```
#### 空值
None表示空
#### 变量
这种变量本身类型不固定的语言称之为动态语言，与之对应的是静态语言。
#### 常量
在Python中，通常用全部大写的变量名表示常量：
```
PI = 3.14159265359
10/3  #精确除
10//3 #地板除，得到结果是取整后的整数
10%3  #求余
```

### 字符串和编码

