---
title: QT读写文件/FTP/socket
date: 2016-09-26 19:25:26 # 文章生成时间，一般不改
categories:   # 文章分类目录，参数可省略
    - 集成开发环境
    - QT
tags:   # 文章标签，参数可省略
---
## 获得当前时间
```C++
QDateTime dateTime;
QString timeStr = dateTime.currentDateTime().toString("yyyyMMddhhmm");
```
<!--more-->
## 遍历文件夹下的文件目录
```C++
QDir dir=QDir(filePath);
dir.setFilter(QDir::Files |QDir::NoSymLinks);
/*
    QStringList filters;
    filters << "*.cpp";
    dir.setNameFilters(filters);
*/
dir.setSorting(QDir::Name);
QFileInfoList list = dir.entryInfoList();

for (int i = 0; i < list.size(); ++i)
{
    QFileInfo fileInfo = list.at(i);
    qDebug() << QString::fromLocal8Bit("%1 %2 %3").arg(fileInfo.size(), 10)
    .arg(fileInfo.fileName()).arg(fileInfo.filePath());
}
```
## 执行外部程序
```C++
QProcess::execute("mkdir -p \""+tarPath+"\"");   //创建新目录
QProcess::execute("cp -r \""+unzipPath+"/"+name+"\" \""+tarPath+"\"");   //复制
QProcess::execute("chmod " + auth + " \"" + tarPath+"/"+name + "\"");
QProcess::execute("chown " + own + ":" + group + " \"" + tarPath+"/"+name + "\"");
QProcess::execute("rm -rf  \""+fileName+"\"");
```
## 文件操作
### 写文件
```C++
QFile file("ConfigFile.txt");
if(!file.open(QIODevice::WriteOnly | QIODevice::Text))
{
    QMessageBox::warning(this, QString::fromLocal8Bit("提示"), QString::fromLocal8Bit("创建配置文件失败！"));
    return;
}
QTextStream out(&file);

for(int k=0; k<strList.size(); ++k)
{
    out << strList.at(k) << endl;
}
out.flush();
file.close();
QMessageBox::warning(this, QString::fromLocal8Bit("提示"), QString::fromLocal8Bit("生成配置文件成功！"));
```
### 读文件
```C++
QFile file("ConfigFile.txt");
if(!file.open(QIODevice::ReadOnly | QIODevice::Text))
{
    QMessageBox::warning(this, QString::fromLocal8Bit("提示"), QString::fromLocal8Bit("打开配置文件失败！"));
    return;
}
QTextStream in(&file);
while(!in.atEnd())
{
    QString str = in.readLine();
    qDebug() << str;
}
file.close();
```