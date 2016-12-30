---
title: QT的小点记录
date: 2016-12-24 19:25:26 # 文章生成时间，一般不改
categories:   # 文章分类目录，参数可省略
    - 集成开发环境
    - QT
tags:   # 文章标签，参数可省略
---
## 通用
### 构造函数
```
setFixedSize(width(),height());  //设置长宽不变，即不可拉伸窗口
setWindowTitle("图像处理工具");
```
### 只有继承QObject或者其子类（例如Qwidget）的类才能包含信号和槽
因为信号与槽的连接是通过 QObject 的 connect() 成员函数来实现的。
```
connect(sender, SIGNAL(signal), receiver, SLOT(slot));
```
其中 sender 与 receiver 是指向对象的指针，SIGNAL() 与 SLOT() 是转换信号与槽的宏。
* 信号之间可以相互连接
>发射第一个信号时，也会发射第二个信号。
* 连接可以被移除
这种情况用得比较少，因为在对象被删除时，Qt会自动移除与这个对象相关的所有连接。语法如下：

```
disconnect(sender, SIGNAL(signal), receiver, SLOT(slot));
```
## MainWindow相关
### 在Action Editor设置快捷键
### [布局管理器](http://blog.csdn.net/lhchen922/article/details/38351793)
右键QWainWindow界面（即主界面），选择[栅格布局](http://blog.csdn.net/yf210yf/article/details/7383558)，控件随窗口变化而自动布局。
局管理器和垂直布局管理器的组合方式相比，使用栅格布局管理器只需要消耗一个布局管理器即可完成整个界面的布局。但是这种方式的一个最大的缺点是，需要事先精确设计好每个部件的位置和占用尺寸，在部件数量比较大的情况下，仅仅使用栅格布局管理器就显得力不从心了。所以，在做界面布局的时候，可以使用栅格布局管理器做整体框架设计，然后在其中填充一些水平或垂直布局管理器，或者他们的组合，以便来达到更好的效果。
### 在窗体下方设置状态栏（如文本编辑器下面的行数状态栏）
```
QLabel *first_statusLabel = new QLabel; //新建标签
    first_statusLabel->setMinimumSize(150,25); //设置标签最小尺寸
    first_statusLabel->setFrameShape(QFrame::WinPanel); //设置标签形状
    first_statusLabel->setFrameShadow(QFrame::Raised); //设置标签阴影
    ui->statusBar->addWidget(first_statusLabel);
    first_statusLabel->setText("Copyright 2016 Hujun. All rights reserved");
 ```
### [QPainter](http://www.mamicode.com/info-detail-1281326.html)
QPainter一般在部件的绘图事件paintEvent()中进行绘制，首先创建QPainter对象，然后进行图形的绘制，最后记得销毁QPainter对象。当窗口程序需要升级或者重新绘制时，调用此成员函数。
使用repaint()和update()后，调用函数paintEvent()。