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
* [廖雪峰的官方网站Python教程](http://www.liaoxuefeng.com/wiki/0014316089557264a6b348958f449949df42a6d3a2e542c000)
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
name = input("please input your name: ")
age = input('please input your age: ')
print(name)
print('Hello %s\nyou are %r' % (name,age))
```
python2.x中：
input():当输入字符串时要加单引号或双引号，输入数字则接受的变量为整型
raw_input()：把接受的都当作字符串
python3.x中：
只有input()，相当于之前的raw_input()
```
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
* ASCII编码是1个字节，而Unicode编码通常是2个字节。
* UTF-8编码把一个Unicode字符根据不同的数字大小编码成1-6个字节
* 在计算机内存中，统一使用Unicode编码，当需要保存到硬盘或者需要传输的时候，就转换为UTF-8编码。
* 对于单个字符的编码，Python提供了ord()函数获取字符的整数表示，chr()函数把编码转换为对应的字符：
```
>>> ord('A')
65
>>> chr(66)
'B'
```
* 要注意区分'ABC'和b'ABC'，前者是str，后者虽然内容显示得和前者一样，但bytes的每个字符都只占用一个字节。
* 以Unicode表示的str通过encode()方法可以编码为指定的bytes
```
>>> 'ABC'.encode('ascii')
b'ABC'
>>> '中文'.encode('utf-8')
b'\xe4\xb8\xad\xe6\x96\x87'
>>> b'\xe4\xb8\xad'.decode('utf-8')
'中'
>>> len(b'ABC')
3
>>> len(b'\xe4\xb8\xad\xe6\x96\x87')
6
>>> len('中文'.encode('utf-8'))
6
文件开头加：
# -*- coding: utf-8 -*-
并且要确保文本编辑器正在使用UTF-8 without BOM编码
```
### 格式化输出
```
>>> 'Hi, %s, you have $%d.' % ('Michael', 1000000)
'Hi, Michael, you have $1000000.'
>>> '%4d' % (3)  //前补空格
'   3'
>>> print('%-4d+' %3); //后补空格
3   +
>>> '%04d' % (3)
'0003'
>>> '%.2f' % 3.1415926
'3.14'
>>> 'growth rate: %d %%' % 7
'growth rate: 7 %'
```
%s永远起作用，它会把任何数据类型转换为字符串

### 使用list和tuple
#### list
```
构造：
classmates = ['Michael', 'Bob', 'Tracy']
>>> classmates
['Michael', 'Bob', 'Tracy']

访问：
>>> classmates[1]   #第2个，最多到len(classmates)
'Bob'
>>> classmates[-1] #倒数第一个，最多到-len(classmates)
'Tracy'

增加：
>>> classmates.append('Adam')
>>> classmates.insert(1, 'Jack')

删除：
>>> classmates.pop()
'Adam'  #同时，classmates末尾的'Adam'被删除
>>> classmates.pop(i) #删除i位置的元素

修改：
>>> classmates[1] = 'Sarah'
```
list的元素的数据类型可以不同:
```
>>> L = ['Apple', 123, True, None]

>>> s = ['python', 'java', ['asp', 'php'], 'scheme']

>>> s[2][1]  #类似二维数组
'php'
```
#### tuple
```
构造：
>>> classmates = ('Michael', 'Bob', 'Tracy')
>>> classmates
('Michael', 'Bob', 'Tracy')

# tuple没有append(),insert()等方法，只能访问class[0],class[-1]，不能赋值

L = (1,)  #只有一个元素为0的tuple
L = (1)   #L = 1,默认表示小括号里是数字1

实际上，tuple的每个元素不变，指的是元素的指向不变。例如：元素指向一个list，不能再指向其他对象，但list本身可以变。
>>> t = ('a', 'b', ['A', 'B'])
>>> t[2][0] = 'X'
>>> t
('a', 'b', ['X', 'B'])
```

### 条件判断
```Python
age = 20
if age >= 18:
	print("成年人")
elif age >= 12:
	print("初中")
else:
	print("小学生")
```

### 循环
```
sum = 0
for x in range(11)
	sum = sum + x
print(sum)

sum = 0
x = 1

while x <= 10:
    sum += x
    x = x + 1
print(sum)
```
break和continue的使用

### 使用dict和set
```
构造：
>>> d = {'Michael':95, 'Bob':75, 'Tracy':85}
>>> d
{'Michael': 95, 'Bob': 75, 'Tracy': 85}
>>> d['Michael']
95

增加：
>>> d['Adam'] = 67  #若不存在则添加，若存在则修改

判断key是否存在：
>>> 'Thomas' in d
False
>>> d.get('Thomas',-1)
-1  #存在则返回value，否则返回-1

删除：
>>> d.pop('Bob')
75  #d中Bob被删除

获取keys/values：
>>> d.keys()
dict_keys(['Bob', 'Michael', 'Tracy'])
>>> list(d.keys())
['Bob', 'Michael', 'Tracy']
>>> list(d.values())
[75, 95, 85]
>>> list(d.items()) #dict转list
[('Bob', 75), ('Michael', 95), ('Tracy', 85)]
```
和list比较，dict有以下几个特点：

> 查找和插入的速度极快，不会随着key的增加而变慢；
需要占用大量的内存，内存浪费多。

而list相反：
> 查找和插入的时间随着元素的增加而增加；
占用空间小，浪费内存很少。

**dict的key必须是不可变对象。**
```
>>> d[[1,2]] = 'a list'
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
TypeError: unhashable type: 'list'
```
### set
```
构造：
>>> s = set([1,2,1,3])
>>> s
{1, 2, 3}
>>> s=set('hello')
>>> s
{'o', 'e', 'l', 'h'}
>>> s.update('kitty') #向set添加一个interable的各个元素
>>> s
{'l', 'o', 't', 'e', 'i', 'k', 'h', 'y'}
>>> s.update(4)
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
TypeError: 'int' object is not iterable

增加：
>>> s.add(4)

删除：
>>> s.remove(4)

交集,并集：
>>> s1 = set([1,2,3])
>>> s2 = set([2,3,4])
>>> s1 & s2
{2, 3}
>>> s1 | s2
{1, 2, 3, 4}
```
对于不变对象来说，调用对象自身的任意方法，也不会改变该对象自身的内容。相反，这些方法会创建新的对象并返回，这样，就保证了不可变对象本身永远是不可变的。

### 总结：
```
list:
L = [1, 2, 3]
>>> d = ([5,6])  #忽略掉(),按[]读取，因此是list，而不是tuple
>>> d
[5, 6]
>>> type(d)
<class 'list'>

tuple:
T = (1, 2, 3)

dict:
D = {'Michael':95, 'Bob':75, 'Tracy':85}

set:
S = set([1, 2, 3])
S = set((1, 2, 3))  #虽然也可以，但最好不这么构造
s4 = set([1, (2, 3)]  #正确
s4 = set([1, [2, 3]   #错误
s4 = set(1, (2, [3, 4]))  #错误
```
**通过type(S)来获取S的数据类型**
```
>>> name = {1,2}
>>> type(name)
<class 'set'>
>>> name = {}
>>> type(name)
<class 'dict'>
```



