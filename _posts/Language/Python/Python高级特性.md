---
title:  Python高级特性
date: 2016-12-19 20:35:26 # 文章生成时间，一般不改
categories:   # 文章分类目录，参数可省略
    - 编程语言
    - Python
tags:   # 文章标签，参数可省略
---
### 切片
li[start : end : step]
当step = -1时，表示逆序步长为1
```
>>> L = ['Michael', 'Sarah', 'Tracy', 'Bob', 'Jack']
>>> L[0:3] / L[:3]
['Michael', 'Sarah', 'Tracy']
>>> L[-3:-1]
['Tracy', 'Bob']
>>> L[-3]
['Tracy', 'Bob', 'Jack']

>>> L = list(range(100))
>>> L[:10:2]
[0, 2, 4, 6, 8]
>>> L[::5]
[0, 5, 10, 15, 20, 25, 30, 35, 40, 45, 50, 55, 60, 65, 70, 75, 80, 85, 90, 95]
>>> li = 'ABCD'
t=li[-1:-3:-1] #-1表示从-1到-3逆序
t=li[-1:1:-1]
t=li[3:1:-1]
t=li[3:-3:-1]
t=li[:-3:-1]
t=li[:1:-1] #默认起始是末尾开始
上面6种的结果都是t = ['D','C']
```

### 迭代
```
>>> d = {'a':1, 'b':2, 'c':3}
>>> for key in d
>>> 	print(key)
>>> for value in d.values()
>>> 	print(value)
>>> for k,v in d.items()
>>> 	print(k, v)
```
判断一个对象是否是可迭代对象：
```
from collections import Iterable

isinstance('abc', Iterable)
```
把一个list实现类似Java那样的下标循环
```
>>> for i, value in enumerate(['a', 'b', 'c']):
>>> 	print(i, value)
```

### 列表生成式
```
>>> list(range(1,11))
>>> [x*x for x in range(1,11)]
[1, 4, 9, 16, 25, 36, 49, 64, 81, 100]
>>> [x*x for x in range(1,11) if x%2 == 0]
[4, 16, 36, 64, 100]
>>> [m + n for m in 'ABC' for n in 'XYZ']
['AX', 'AY', 'AZ', 'BX', 'BY', 'BZ', 'CX', 'CY', 'CZ']
```

### 生成器
一边循环一边计算的机制，被称为生成器：generator。
* 方法一：把一个列表生成式的[]改成()

```
>>> g = (x * x for x in range(10))
>>> g
<generator object <genexpr> at 0x1022ef630>
>>> for n in g:
...     print(n)
...
```

* 方法二：如果一个函数定义中包含yield关键字，那么这个函数就不在是一个普通函数，而是一个generator

```
def fib(max):
    n, a, b = 0, 0, 1
    while n < max:
        yield b
        a, b = b, a + b
        n = n + 1
    return 'done'
>>> f = fib(6)
>>> f
<generator object fib at 0x00000223A4218A98>
```
每次调用next()的时候执行，遇到yield语句返回，再次执行时从上次返回的yield语句处继续执行。
生成器的唯一注意事项就是：生成器只能遍历一次。

杨辉三角：
```
def triangles():
    L = [1]
    while True:
    		yield L
            L = [0]+L+[0]
            L = [L[i]+L[i+1] for i in range(len(L)-1)]

n = 0
for t in triangles():
    print(t)
    n = n + 1
    if n == 10:
        break
```

### 迭代器
可以被next()函数调用并不断返回下一个值的对象称为迭代器：Iterator。

用isinstance()判断一个对象是否时Iterator对象：
```
from collections import Iterator

isinstance([], Iterator)
```
生成器都是Iterator对象，但list、dict、str虽然是Iterable，却不是Iterator。

把list、dict、str等Iterable变成Iterator可以使用iter()函数：
```
>>> isinstance(iter([]), Iterator)
True
```
凡是可作用于for循环的对象都是Iterable类型；

凡是可作用于next()函数的对象都是Iterator类型，它们表示一个惰性计算的序列，只有在需要返回下一个数据时它才会计算。



























