---
title: Markdown基本用法
date: 2016-04-07 11:25:26 # 文章生成时间，一般不改
categories:   # 文章分类目录，参数可省略
    - 编程语言
    - Markdown
tags:   # 文章标签，参数可省略
---
## 插入目录
使用[TOC]引用目录
`[TOC]`

[TOC]
<!--more-->

## 插入图片
`![](link "图片名1")`
![](link "图片名1")

hexo在图片下方显示图片描述：
`{% fullimage link, alt, 图片描述 %}`
{% fullimage link, alt, 图片描述 %}

## 引用
`> 这是引用`

注意>与文字之间的空格

> 这是引用

```
> 这是一级引用
>> 这是二级引用
>>> 这是三级引用

> 这是一级引用
```

> 这是一级引用
>> 这是二级引用
>>> 这是三级引用

> 这是一级引用

```
> 这是一级引用
>> 这是二级引用
>>> 这是三级引用
> 这是一级引用
```

> 这是一级引用
>> 这是二级引用
>>> 这是三级引用
> 这是一级引用

注意>与文字之间的空格

## 粗体与斜体
`**这是粗体** or __这是粗体__`

**这是粗体** or __这是粗体__


`*这是斜体* or _这是斜体_`

*这是斜体* or _这是斜体_

反斜杠\表示不想显示Markdown标记：
`\*这里不会显示斜体\*`

\*这里不会显示斜体\*


## 标题一
### 标题二
```
## 标题一
### 标题二
```

```
这是一级标题
===
```

```
这是二级标题
-------------
```

这是二级标题
-------------

### 分割线
```
-----
```

-----


## 列表
### 无序列表
```
* 列表一
* 列表二
* 1. 有序列表一
  2. 有序列表二
```

* 列表一
* 列表二
* 1. 有序列表一
  2. 有序列表二

```
+ 呵呵
    * 嘉嘉
    - 嘻嘻
    - 吼吼
        - 嘎嘎
        + 桀桀
```

* 呵呵
    * 嘉嘉
    * 嘻嘻
    * 吼吼
        * 嘎嘎
        * 桀桀

### 有序列表
```
1. * 有序一
   * 呵呵
   * 和哈
2. 有序二
3. 有序三
```

1. * 有序一
   * 呵呵
   * 和哈
2. 有序二
3. 有序三

## 表格
```
| Tables        | Are           | Cool  |
| ------------- |:-------------:| -----:|
| col 3 is      | right-aligned | $1600 |
| col 2 is      | centered      |   $12 |
| zebra stripes | are neat      |    $1 |

------: 为右对齐。
:------ 为左对齐。
:------: 为居中对齐。
------- 为使用默认居中对齐。

```

| Tables        | Are           | Cool  |
| ------------- |:-------------:| -----:|
| col 3 is      | right-aligned | $1600 |
| col 2 is      | centered      |   $12 |
| zebra stripes | are neat      |    $1 |

```
dog | bird | cat
----|------|----
foo | foo  | foo
bar | bar  | bar
baz | baz  | baz
```

dog | bird | cat
----|------|----
foo | foo  | foo
bar | bar  | bar
baz | baz  | baz

## 代码
### 行内代码(inline code)
`cd ~/kobuki/src`
### 块代码
```bash
cd ~/kobuki/src
wstool set ecl_core --git https://github.com/stonier/ecl_core --version=release/0.61-indigo-kinetic
wstool set ecl_navigation --git https://github.com/stonier/ecl_navigation --version=release/0.60-indigo-kinetic
wstool update ecl_core
wstool update ecl_navigation
```

## 链接
```
[胡君的个人博客](http://hujun1413.github.io/ "胡君博客") 
[](link "Optional title")
"Optional title"为显示标题。
在你将鼠标放到链接上后，会显示一个小框提示Optional title 里的内容。

~~胡君的个人博客<http://hujun1413.github.io/>~~

胡君的邮箱<359328242@qq.com>
[之后定义链接][id]
>cool one like this.
[id]: http://hujun1413.github.io/ 
```

[胡君的个人博客](http://hujun1413.github.io/ "胡君博客")

~~胡君的个人博客<http://hujun1413.github.io/>~~

胡君的邮箱<359328242@qq.com>
[之后定义链接][id]
>cool one like this.
[id]: http://hujun1413.github.io/ 

## 脚注
使用 [^num] 表示注脚
```
正文[^1]文字
[^1]: 脚注内容

正文[^2]文字
[^2]: 脚注内容
```
正文[^1]文字
[^1]: 脚注内容

正文[^2]文字
[^2]: 脚注内容

* 备注：关于注脚每个编辑器表示方式会有所不用，hexo的Markdown中不支持这种

## 待办事宜 Todo
使用带有- [ ] 或- [x]（未完成或已完成）项的列表语法撰写一个待办事宜列表
```
- [x] works as a framework (all components running in one process, no threads)
- [ ] redis
```

- [x] works as a framework (all components running in one process, no threads)
- [ ] redis

hexo的Markdown中不支持，采用以下方法替代
```
<input type='checkbox' onclick='return false;' checked> works as a framework 
<input type='checkbox' onclick='return false;' unchecked> redis
```
<input type='checkbox' onclick='return false;' checked> works as a framework
<input type='checkbox' onclick='return false;' unchecked> redis

## 空格和空行
### 空格
Markdown语法会忽略首行开头的空格，如果要体现出首行开头空两个的效果，可以使用 全角符号下的空格 ，windows下使用 shift+空格 切换。
```
胡君的个人博客
 胡君的个人博客１(半角空格)
　胡君的个人博客２（全角空格）

&nbsp;&nbsp;&nbsp;&nbsp;空格
　空格
```

胡君的个人博客
 胡君的个人博客１
　胡君的个人博客２

&nbsp;&nbsp;&nbsp;&nbsp;空格
　空格

### 空行
```
内容一
<br/>内容二
<br>内容二
<br />内容二
```

内容一
<br/>内容二
<br>内容二
<br />内容二

## 页内跳转
```
[你好](#jump)
<span id = "jump">hehe</span>
```

[你好](#jump)
<span id = "jump">hehe</span>

## LaTeX公式
### $ 表示行内公式
```
质能守恒方程可以用一个很简洁的方程式 $E=mc^2$ 来表达。
```

质能守恒方程可以用一个很简洁的方程式 $E=mc^2$ 来表达。
### $$ 表示整行公式
```
$$\sum_{i=1}^n a_i=0$$

$$f(x_1,x_x,\ldots,x_n) = x_1^2 + x_2^2 + \cdots + x_n^2 $$

$$\sum^{j-1}_{k=0}{\widehat{\gamma}_{kj} z_k}$$
```

$$\sum_{i=1}^n a_i=0$$

$$f(x_1,x_x,\ldots,x_n) = x_1^2 + x_2^2 + \cdots + x_n^2 $$

$$\sum^{j-1}_{k=0}{\widehat{\gamma}_{kj} z_k}$$

