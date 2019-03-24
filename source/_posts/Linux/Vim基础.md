---
title: Vim基础
date: 2016-09-26 19:25:26 # 文章生成时间，一般不改
categories:   # 文章分类目录，参数可省略
    - Linux
tags:   # 文章标签，参数可省略
    - Vim
---
## 参考
[ma6174/vim](https://github.com/ma6174/vim)
## 插入
i 在当前光标插入

I 在当前行最前插入

A 在当前行最后插入

O 在光标上一行最前插入
<!--more-->

## 复制/剪切/粘贴
v d 进入可视模式后剪切

dd 剪切当前行

dnd 剪切n行

v y 进入可视模式后复制

yy 复制当前行

yny 复制n行

p 粘贴

## 光标移动
ctrl+f 向下翻一页

ctrl+b 向上翻一页

ctrl+u 向上翻半页

ctrl+d 向下翻半页

j 向上移动一行

k 向下移动一行

h 向左移动一个字符

l 向右移动一个字符

数字0：移动到行首

^:移动光标到非空格的行首

$:移动光标到行尾

gg 移动到第一行

G 移动到最后一行

nG 移动到第n行

## 撤销/重做
u 撤销，相当于word的ctrl+z

ctrl+r 重做，相当于word的ctrl+y

## 查找和替换
/word 在光标后查找word字符串

回车后输入n代表同方向下一个,N代表反方向下一个

?word 在光标前查找word字符串

:n1,n2s/word1/word2/g 在第n1行和第n2行之间查找word1,并替换成word2

## 保存/退出
:w 写入

:wq 写入并退出

:q 退出

:q! 不保存退出

:r [filename] 读入文件加入光标下一行

:n1,n2 w [filename] 将n1到n2读内容保存到文件中

## 多文件/窗口
### 未启动vim时
vim file1 file2 ... 只显示一个窗口

vim -o file1 file2 上下分布

vim -O file1 file2 左右分布
### 已启动vim时
#### 打开
:open file 只显示一个文件

:sp file 上下分布

:vs file 左右分布
#### 切换
文件间（在当前窗格间切换不同文件）：
ctrl+6  下一个文件
:bn 下一个文件
:bp 上一个文件
窗格间：
ctrl+ww 依次向后切换下一个窗格
ctrl+w+h/j/k/l 切换到上下左右（k上j下h左l右）的窗格

## Vim配置
[个人VIM配置实例](http://blog.csdn.net/younger_china/article/details/13613461)
修改 /etc/vim/vimrc，进行个性化配置
```C
if filereadable("/etc/vim/vimrc.local")
  source /etc/vim/vimrc.local
endif

"显示行号
set nu
"缩进为4空格
set tabstop=4
"继承前一行的缩进方式
set autoindent
#### "允许退格键删除
set backspace=2
"启用鼠标
set mouse=a
set selection=exclusive
set selectmode=mouse,key
```

## 一般模式：删除、复制与粘贴类命令
x,X                      x为向后删除一个字符，X为先前删除一个字符
nx(n代表数字)             向后删除n个字符
dd                       删除当前行
D                        删除当前行所有字符，试成为空行
ndd(n代表数字)            删除光标所在行的向下n列
d1G                      删除光标所在行到第一行的所有数据
dG                       删除光标所在行到最后一行的所有数据
yy                       复制光标所在行
y1G                      复制光标所在行到第一行的所有数据
yG                       复制光标所在行到最后一行的所有数据
ynj(n代表数字)            复制光标所在行向下n+1行
dnj(n代表数字)            删除光标所在行向下n+1行
p,P                      p为复制的数据粘贴在光标的下一行，P为复制的数据粘贴在光标的上一行
J                        将光标所在行与下一行的数据结合成一行
u                        恢复前一个动作(undo)
## 编辑模式命令
i,I                      i为在当前光标所在处插入输入的文字，I为在光标所在行第一个非空字符插入输入的文字
a,A                      a为在当前光标所在处下一个字符插入输入的文字，A为在光标所在行最后一个字符的下一个字符处插入输入的文字
o,O                      o为在光标所在行的下一行行首开始插入字符，O为在光标所在行的上一行行首开始插入字符
r,R                      r为替换光标所在那一个字符，R为一直替换光标所指的文字，直到退出
Esc                      退出，回到一般模式
## 命令模式
h                        光标向左移一个字符
j                        光标向下移一个字符
k                        光标向上移一个字符
l                        光标向右移一个字符
Ctrl+f                   屏幕向下翻一页
Ctrl+b                   屏幕向上翻一页
Ctrl+d                   屏幕向下翻半页
Ctrl+u                   屏幕向上翻半页
+                        光标移动到下一行的第一个非空字符
-                        光标移动到当前行的第一个非空字符
n空格(n代表数字)           光标向当前行向右移动n个字符
0(数字0)                  光标移动到当前行的第一个字符(可以为空字符,注意与-区分）
$                        光标移动到当前行的最后一个字符(可以为空字符,注意与-区分）
H                        光标移动到当前屏幕最上方的那一行的第一个非空字符
M                        光标移动到当前屏幕最中间那一行的第一个非空字符
L                        光标移动到当前屏幕最下方的那一行的第一个非空字符
G                        光标移动到该文章最后一行的第一个非空字符
nG(n代表数字)             光标移动到该文章第n行的第一个非空字符
n                        光标从当前行向下移动n行的第一个非空字符
/word                    在光标之后查找word字符串
?word                    在光标之前查找word字符串
:s/word1/word2/g         在光标当前行查找word1，并替换成word2
:n1,n2s/word1/word2/g    在第n1行与第n2行之间查找word1，并替换成word2
:%s/word1/word2/g        整个文章查找word1，并替换成word2
:w                       将编辑的数据保存到硬盘文件中
:w [filename]            将编辑后的数据保存到硬盘的另一个文件中
:r [filename]            在编辑数据时，读入另一个文件中的数据，即将filename文件中的内容加到光标所在行下一行
:wq或:x                  保存并退出
:q                       退出，适用于未修改的文件
:q!                      强制退出，适用于修改文件后不保存退出
:set nu                  显示行号
:set nonu                取消行号
:n1,n2 w [filename]      将n1到n2行的内容保存到名为filename的文件中

## [linux下vim命令总结](http://blog.sina.com.cn/s/blog_4ce89f200100vkb5.html)

