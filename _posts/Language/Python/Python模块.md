---
title:  Python模块
date: 2016-12-19 20:35:26 # 文章生成时间，一般不改
categories:   # 文章分类目录，参数可省略
    - 编程语言
    - Python
tags:   # 文章标签，参数可省略
---
### 使用模块
```
#!/usr/bin/env python3
# -*- coding: utf-8 -*-

'a test module'

__author__ = 'Hujun'

import sys

def test():
    args = sys.argv
    if len(args) == 1:
        print('Hello, world')
    elif len(args) == 2:
        print('Hello, %s!' %args[1])
    else:
        print('Too many arguments!')

if __name__ == '__main__':
    test()

```
* 任何模块代码的第一个字符串都被视为模块的文档注释；
* 导入sys模块后，我们就有了变量sys指向该模块，利用sys这个变量，就可以访问sys模块的所有功能。
* 当我们在命令行运行hello模块文件时，Python解释器把一个特殊变量\_\_name\_\_置为\_\_main\_\_，而如果在其他地方导入该hello模块时，if判断将失败，因此，这种if测试可以让一个模块通过命令行运行时执行一些额外的代码，最常见的就是运行测试。

### 作用域
* 正常的函数和变量名是公开的（public），可以被直接引用，比如：abc，x123，PI等；
* 类似\_\_xxx\_\_这样的变量是特殊变量，可以被直接引用，但是有特殊用途，比如上面的\_\_author\_\_，\_\_name\_\_就是特殊变量，hello模块定义的文档注释也可以用特殊变量\_\_doc\_\_访问，我们自己的变量一般不要用这种变量名；
* 类似\_xxx和\_\_xxx这样的函数或变量就是非公开的（private），不应该被直接引用，比如\_abc，\_\_abc等；
* 外部不需要引用的函数全部定义成private，只有外部需要引用的函数才定义为public。

### 安装第三方模块
* 安装第三方模块，是通过包管理工具pip完成的。
* Python Imaging Library，这是Python下非常强大的处理图像的工具库
* 其他常用的第三方库还有MySQL的驱动：mysql-connector-python，用于科学计算的NumPy库：numpy，用于生成文本的模板工具Jinja2

### 模块搜索路径
* 默认情况下，Python解释器会搜索当前目录、所有已安装的内置模块和第三方模块，搜索路径存放在sys模块的path变量中
* 添加自己的搜索目录，有两种方法：
	1. 直接修改sys.path，添加要搜索的目录：
	```
    >>> import sys
	>>> sys.path.append('/Users/michael/my_py_scripts')
    ```
    这种方法是在运行时修改，运行结束后失效。
	2. 设置环境变量PYTHONPATH，该环境变量的内容会被自动添加到模块搜索路径中。设置方式与设置Path环境变量类似。注意只需要添加你自己的搜索路径，Python自己本身的搜索路径不受影响。



















