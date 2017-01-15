---
title:  HTML基础教程
date: 2016-12-19 20:35:26 # 文章生成时间，一般不改
categories:   # 文章分类目录，参数可省略
    - 编程语言
    - HTML
tags:   # 文章标签，参数可省略
---
### HTML基础
* `<html> 与 </html>` 之间的文本描述网页，`<html>` 元素定义了整个 HTML 文档。
* `<body> 与 </body>` 之间的文本是可见的页面内容，`<body>` 元素定义了 HTML 文档的主体。
* `<h1> 与 </h1>` 之间的文本被显示为标题
* `<p> 与 </p>` 之间的文本被显示为段落
* 开始标签常被称为开放标签（opening tag），结束标签常称为闭合标签（closing tag）。
* HTML 提示：使用小写标签
* [完整的 HTML 参考手册](http://www.w3school.com.cn/tags/index.asp)
* 默认情况下，HTML 会自动地在块级元素前后添加一个额外的空行，比如段落、标题元素前后。
* 当显示页面时，浏览器会移除源代码中多余的空格和空行。所有连续的空格或空行都会被算作一个空格。
<!--more-->
```
<html>
<body>

<h1>My First Heading</h1>

<p>My first paragraph.</p>

</body>
</html>
```

#### 注释
```
<!-- This is a comment -->
```

#### 标题
```
<h1>This is a heading</h1>
<h2>This is a heading</h2>
<h3>This is a heading</h3>
```

#### HTML 水平线
```
<p>This is a paragraph</p>
<hr />
<p>This is a paragraph</p>
```

#### 段落
```
<p>This is a paragraph.</p>
<p>This is another paragraph.</p>
```

#### 链接
```
<a href="http://www.w3school.com.cn">This is a link</a>
```

#### 图像
```
<img src="http://ofl6ycl3e.bkt.clouddn.com/images/CNCC2016%E6%80%BB%E7%BB%93/CNCC2016.jpg" width="1080" height="720" />

<img src="img_the_scream.jpg" width="220" height="277" alt="The Scream">
<p><cite>The Scream</cite> by Edward Munch. Painted in 1893.</p>
```

#### 空行
即使 `<br>` 在所有浏览器中都是有效的，但使用 `<br />` 其实是更长远的保障。

#### 属性
```
<body bgcolor="yellow">
<h1 align="center">My First Heading</h1>
```
在 HTML 4 中，有若干的标签和属性是被废弃的，请用样式替代:
```
标签	                              描述
<center>	                   定义居中的内容
<font> 和 <basefont>	          定义 HTML 字体
<s> 和 <strike>	              定义删除线文本
<u>	                          定义下划线文本

属性	                              描述
align	                      定义文本的对齐方式
bgcolor	                      定义背景颜色
color	                      定义文本颜色
```

#### 样式
样式是 HTML 4 引入的，它是一种新的首选的改变 HTML 元素样式的方式。通过 HTML 样式，能够通过使用 style 属性直接将样式添加到 HTML 元素，或者间接地在独立的样式表中（CSS 文件）进行定义。
改变背景颜色，字体，颜色和尺寸，对齐。
```
<html>
<body style="background-color:PowderBlue;">

<h1>Look! Styles and colors</h1>

<h1 style="text-align:center">This is a heading</h1>

<p style="font-family:verdana;color:red">
This text is in Verdana and red</p>

<p style="font-family:times;color:green">
This text is in Times and green</p>

<p style="font-size:30px">This text is 30 pixels high</p>

</body>
</html>
```

#### 文本格式化
* [文本格式化](http://www.w3school.com.cn/tiy/t.asp?f=html_textformatting)
```
<strong>This text is strong</strong>
```
* [预格式文本](http://www.w3school.com.cn/tiy/t.asp?f=html_preformattedtext)
```
<pre>
for i = 1 to 10
     print i
next i
</pre>
```
* [“计算机输出”标签](http://www.w3school.com.cn/tiy/t.asp?f=html_computeroutput)
```
<samp>Sample text</samp>
<br />
<var>Computer variable</var>
```
* [地址](http://www.w3school.com.cn/tiy/t.asp?f=html_address)
```
Written by <a href="mailto:webmaster@example.com">Donald Duck</a>.<br>
```
* [缩写和首字母缩写](http://www.w3school.com.cn/tiy/t.asp?f=html_abbracronym)
```
<abbr title="etcetera">etc.</abbr>
<br />
<acronym title="World Wide Web">WWW</acronym>
<p><dfn title="World Health Organization">WHO</dfn> 成立于 1948 年。</p>
```
* [文字方向](http://www.w3school.com.cn/tiy/t.asp?f=html_bdo)
```
<bdo dir="rtl">
Here is some Hebrew text
</bdo>
```
* [块引用](http://www.w3school.com.cn/tiy/t.asp?f=html_quotations)

```
<blockquote>
这是长的引用。这是长的引用。这是长的引用。这是长的引用。这是长的引用。这是长的引用。这是长的引用。这是长的引用。这是长的引用。这是长的引用。这是长的引用。
</blockquote>

<q>
这是短的引用
</q>
```
* [删除字效果和插入字效果](http://www.w3school.com.cn/tiy/t.asp?f=html_delins)
```
<p>一打有 <del>二十</del> <ins>十二</ins> 件。</p>
```

#### 变量格式化（数学变量）
```
<p>Einstein wrote:</p>

<p><var>E = m c<sup>2</sup></var></p>
```





















