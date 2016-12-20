---
title: QT总结
date: 2016-09-26 19:25:26 # 文章生成时间，一般不改
categories:   # 文章分类目录，参数可省略
    - 集成开发环境
    - QT
tags:   # 文章标签，参数可省略
---
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
<!--more-->
## 获得当前时间
```C++
QDateTime dateTime;
QString timeStr = dateTime.currentDateTime().toString("yyyyMMddhhmm");
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
## FTP操作
### FTP上传
```C++
manager = new QNetworkAccessManager();
connect(manager,SIGNAL(finished(QNetworkReply*)),this,SLOT(finished(QNetworkReply *)));

void Widget::upload(QString filePath, QString fileName)
{
    updateFile = new QFile(filePath);
    updateFile->open(QIODevice::ReadOnly);
    QByteArray data = updateFile->readAll();
    //qDebug() << data;

    QUrl url;
    url.setScheme("ftp");
    url.setUserName(ui->userNameLineEdit->text());
    url.setPassword(ui->passwordLineEdit->text());
    url.setHost(ui->ipAddrLineEdit->text());
    url.setPort(21);
    if(ui->uploadPathLineEdit->text() != "")
    {
        url.setPath(ui->uploadPathLineEdit->text()+"/"+fileName);
    }
    else
    {
        url.setPath(fileName);
    }
    manager->put(QNetworkRequest(url), data);
}
void Widget::finished(QNetworkReply * reply)
{
    if(reply->error() == QNetworkReply::NoError)
    {
        updateFile->close();
        QMessageBox::warning(this, QString::fromLocal8Bit("提示"), QString::fromLocal8Bit("压缩并上传成功！"));
        reply->deleteLater();
    }
    else
    {
        updateFile->close();
        QMessageBox::warning(this, QString::fromLocal8Bit("提示"), reply->errorString());
        reply->deleteLater();

        delete manager;
        manager = new QNetworkAccessManager();
        connect(manager,SIGNAL(finished(QNetworkReply*)),this,SLOT(finished(QNetworkReply *)));
    }
}
```
### FTP下载
由于QT5中移除了QFtp这个类，因此QT4.x见例程qftp，QT5.x使用QNetworkAccessManager的get方法实现FTP下载。
## QT的QTableWidget表格控件
```C++
QFont font = ui->fileTable->horizontalHeader()->font();
font.setBold(true);
ui->fileTable->horizontalHeader()->setFont(font);
ui->fileTable->horizontalHeader()->setStretchLastSection(true); //设置充满表宽度
ui->fileTable->verticalHeader()->setDefaultSectionSize(20); //设置行高
ui->fileTable->setSelectionMode(QAbstractItemView::ExtendedSelection);  //可多选（Ctrl、Shift、  Ctrl+A都可以）
ui->fileTable->setSelectionBehavior(QAbstractItemView::SelectRows);  //设置选择行为时每次选择一行
ui->fileTable->setEditTriggers(QAbstractItemView::DoubleClicked); //设置双击编辑
ui->fileTable->horizontalHeader()->resizeSection(0,150); //设置表头第一列的宽度为150
ui->fileTable->horizontalHeader()->setFixedHeight(25); //设置表头的高度
ui->fileTable->setStyleSheet("selection-background-color:lightblue;"); //设置选中背景色
ui->fileTable->horizontalHeader()->setStyleSheet("QHeaderView::section{background:skyblue;}"); //设置表头背景色
```
## 基于TCP的文件传输
### 发送端：
```C++
connect(this,SIGNAL(bytesWritten(qint64)),this,SLOT(updateClientProgress(qint64)));
loadSize = 4*1024;
totalBytes = 0;
bytesWritten = 0;
bytesToWrite = 0;

/* 
    QFile *localFile;  //要发送的文件
    qint64 totalBytes;  //数据总大小
    qint64 bytesWritten;  //已经发送数据大小
    qint64 bytesToWrite;   //剩余数据大小
    qint64 loadSize;   //每次发送数据的大小
    QString fileName;  //保存文件路径
    QByteArray outBlock;  //数据缓冲区，即存放每次要发送的数据
*/

void TcpSocket::sendFile(int id, QString fileName)
 {
     if(this->socketDescriptor() == id && fileName != "")
     {
         localFile = new QFile(fileName);
         if(!localFile->open(QFile::ReadOnly))
         {
             qDebug() << "open file error!";
             return;
         }

         totalBytes = localFile->size();
         //文件总大小
         QDataStream sendOut(&outBlock,QIODevice::WriteOnly);
         sendOut.setVersion(QDataStream::Qt_4_6);
         QString currentFileName = fileName.right(fileName.size() - fileName.lastIndexOf('/')-1);
         this->fileName = currentFileName;
         sendOut << qint64(0) << qint64(0) << currentFileName;
         //依次写入总大小信息空间，文件名大小信息空间，文件名
         totalBytes += outBlock.size();
         //这里的总大小是文件名大小等信息和实际文件大小的总和
         sendOut.device()->seek(0);
         sendOut<<totalBytes<<qint64((outBlock.size() - sizeof(qint64)*2));
         //返回outBolock的开始，用实际的大小信息代替两个qint64(0)空间
         bytesToWrite = totalBytes - this->write(outBlock);
         //发送完头数据后剩余数据的大小
         qDebug() << currentFileName;
         outBlock.resize(0);

     }
 }

 void TcpSocket::updateClientProgress(qint64 numBytes) //更新进度条，实现文件的传送
{
     if(this->fileName != "designed_by_hujun359328242")
     {
         bytesWritten += (int)numBytes;
         //已经发送数据的大小
         if(bytesToWrite > 0) //如果已经发送了数据
         {
             outBlock = localFile->read(qMin(bytesToWrite,loadSize));
           //每次发送loadSize大小的数据，这里设置为4KB，如果剩余的数据不足4KB，
           //就发送剩余数据的大小
             bytesToWrite -= (int)this->write(outBlock);
            //发送完一次数据后还剩余数据的大小
             outBlock.resize(0);
             //清空发送缓冲区
         }
         else
         {
             localFile->close(); //如果没有发送任何数据，则关闭文件
         }
         //ui->progressBar->setMaximum(totalBytes);
         //ui->progressBar->setValue(bytesWritten);
         //qDebug() << bytesWritten/totalBytes;
         //更新进度条
         if(bytesWritten == totalBytes) //发送完毕
         {
             qDebug() << QString::fromLocal8Bit("传送文件 %1 成功").arg(fileName);
             localFile->close();
             this->fileName = "";
             bytesWritten = 0;
         }
     }
     else
     {
         this->fileName = "";
         bytesWritten = 0;
     }
}
```
### 接收端
```C++
connect(socket,SIGNAL(readyRead()),this,SLOT(updateServerProgress()));
totalBytes = 0;
bytesReceived = 0;
fileNameSize = 0;
filePath = "/home/hujun/download";
/*
    qint64 totalBytes;  //存放总大小信息
    qint64 bytesReceived;  //已收到数据的大小
    qint64 fileNameSize;  //文件名的大小信息
    QString fileName;   //存放文件名
    QString filePath;   //存放文件目录
    QFile *localFile;   //本地文件
    QByteArray inBlock;   //数据缓冲区
*/

void TcpSocket::updateServerProgress()  //更新进度条，接收数据
{
   QDataStream in(socket);
   in.setVersion(QDataStream::Qt_4_6);
   if(bytesReceived <= sizeof(qint64)*2)
   { //如果接收到的数据小于16个字节，那么是刚开始接收数据，我们保存到//来的头文件信息
        if((socket->bytesAvailable() >= sizeof(qint64)*2)
            && (fileNameSize == 0))
        { //接收数据总大小信息和文件名大小信息
            in >> totalBytes >> fileNameSize;
            bytesReceived += sizeof(qint64) * 2;
        }
        if((socket->bytesAvailable() >= fileNameSize)
            && (fileNameSize != 0))
        {  //接收文件名，并建立文件
            in >> fileName;
            bytesReceived += fileNameSize;
            
            qDebug() << QString::fromLocal8Bit("接收文件 %1 …").arg(fileName);
            localFile = new QFile(filePath+"/"+fileName);
            if(!localFile->open(QFile::WriteOnly))    //打开文件失败
            {
                //qDebug() << "open file error!";
                socket->readAll();
                totalBytes = 0;
                bytesReceived = 0;
                fileNameSize = 0;
                qDebug() << "open file error!";
                return;
            }
        }
        else return;
   }
   if(bytesReceived < totalBytes)
   {  //如果接收的数据小于总数据，那么写入文件
       bytesReceived += socket->bytesAvailable();
       inBlock = socket->readAll();
       localFile->write(inBlock);
       inBlock.resize(0);
   }
   //ui->serverProgressBar->setMaximum(totalBytes);
   //ui->serverProgressBar->setValue(bytesReceived);
   //更新进度条
   if(bytesReceived == totalBytes)
   { //接收数据完成时
        totalBytes = 0;
        bytesReceived = 0;
        fileNameSize = 0;
        localFile->close();
        qDebug() << QString::fromLocal8Bit("接收文件 %1 成功").arg(fileName);
   }
}
```
### [参考：基于Qt的P2P局域网聊天及文件传送软件设计](http://blog.csdn.net/zouxy09/article/details/9140881)

