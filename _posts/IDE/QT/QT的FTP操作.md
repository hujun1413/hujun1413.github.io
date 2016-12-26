---
title: QT的FTP操作
date: 2016-09-26 19:25:26 # 文章生成时间，一般不改
categories:   # 文章分类目录，参数可省略
    - 集成开发环境
    - QT
tags:   # 文章标签，参数可省略
---
## FTP操作
### FTP上传
<!--more-->
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