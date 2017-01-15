---
title:  Python模块
date: 2016-12-19 20:35:26 # 文章生成时间，一般不改
categories:   # 文章分类目录，参数可省略
    - 编程语言
    - Python
tags:   # 文章标签，参数可省略
---
### 类和实例
必须牢记类是抽象的模板，比如Student类，而实例是根据类创建出来的一个个具体的“对象”，每个对象都拥有相同的方法，但各自的数据可能不同。

可以自由地给一个实例变量绑定属性。

通过定义一个特殊的\_\_init\_\_方法，在创建实例的时候，就把name，score等属性绑上去：
```
>>> class Student(object):
...     def __init__(self, name, score):
...             self.name = name
...             self.score = score
...
>>> bart = Student('Bart Simpson', 59)
>>> bart.name
'Bart Simpson'
>>> bart.score
59
```

### 数据封装
除了第一个参数是self外，其他和普通函数一样。要调用一个方法，只需要在实例变量上直接调用，除了self不用传递，其他参数正常传入：
```
class Student(object):

    def __init__(self, name, score):
        self.name = name
        self.score = score

    def print_score(self):
        print('%s: %s' % (self.name, self.score))

>>> bart.print_score()
Bart Simpson: 59
```

### 访问限制
如果要让内部属性不被外部访问，可以把属性的名称前加上两个下划线\_\_
```
class Student(object):

    def __init__(self, name, score):
        self.__name = name
        self.__score = score

    def print_score(self):
        print('%s: %s' % (self.__name, self.__score))

>>> bart._Student__name
'Bart Simpson'
```
仍然可以通过\_Student\_\_name来访问\_\_name变量：
但是强烈建议你不要这么干，因为不同版本的Python解释器可能会把__name改成不同的变量名。

### 继承和多态
子类获得了父类的全部功能。

当子类和父类都存在相同的run()方法时，我们说，子类的run()覆盖了父类的run()，在代码运行的时候，总是会调用子类的run()。这样，我们就获得了继承的另一个好处：多态。
```
>>> isinstance(dog, Dog)
True
>>> isinstance(dog, Animal)
True

def run_twice(animal):
    animal.run()
    animal.run()
```
新增一个Animal的子类，不必对run_twice()做任何修改，实际上，任何依赖Animal作为参数的函数或者方法都可以不加修改地正常运行，原因就在于多态。

这就是著名的“开闭”原则：
对扩展开放：允许新增Animal子类；
对修改封闭：不需要修改依赖Animal类型的run_twice()等函数。

对于Python这样的动态语言来说，则不一定需要传入Animal类型。我们只需要保证传入的对象有一个run()方法就可以了：
```
class Timer(object):
    def run(self):
        print('Start...')
```
这就是动态语言的“鸭子类型”，它并不要求严格的继承体系，一个对象只要“看起来像鸭子，走起路来像鸭子”，那它就可以被看做是鸭子。

### 获取对象信息












