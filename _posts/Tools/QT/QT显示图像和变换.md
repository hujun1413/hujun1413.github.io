---
title: QT显示图片和变换
date: 2016-12-24 19:25:26 # 文章生成时间，一般不改
categories:   # 文章分类目录，参数可省略
    - 集成开发环境
    - QT
tags:   # 文章标签，参数可省略
---
## 打开并显示图片
```C++
filename=QFileDialog::getOpenFileName(this, tr("选择图像"), "",tr("Images (*.png *.bmp *.jpg *.tif *.GIF )"));
if(filename.isEmpty())
{
    filename = lastFilename;
    QMessageBox::information(this,tr("打开"),tr("未选择图像!"));
    return;
}
QImage* raw_img;
if(!(raw_img->load(filename))) //加载图像
{
    QMessageBox::information(this,tr("打开"),tr("打开图像失败!"));
    delete raw_img;
    return;
}
QGraphicsScene* scene = new QGraphicsScene;
ui->graphicsView->setScene(scene); //将视图对象于场景相连
QImage* img;
*img= raw_img->scaled(384, 216,Qt::KeepAspectRatio,Qt::SmoothTransformation);//先缩小显示
PixItem* pixItem = new PixItem(QPixmap::fromImage(*img));
scene->addItem(pixItem);
```
## 继承QGraphicsItem，写自己的Item类
```
class PixItem : public QObject, public QGraphicsItem        //继承自图元类,实现自定义的图元，，，qt预置的有直线，椭圆，文本图元，矩形图元等
{
    Q_OBJECT
    ...
}

PixItem::PixItem(QPixmap pixmap)
{
    pix = pixmap;
    setAcceptDrops(true);   //设置可拖拽
    m_scaleValue = 0;

    m_isMove = false;
}
//实现自己的图元边界函数
QRectF PixItem::boundingRect() const
{
    return QRectF(-pix.width() / 2, -pix.height() / 2,
                  pix.width(), pix.height());
}

//只需QPainter的drawPixmap()函数将图元图片绘出即可
void PixItem::paint(QPainter *painter, const QStyleOptionGraphicsItem *,
                    QWidget *)
{
    painter->drawPixmap(-pix.width() / 2, -pix.height() / 2, pix);
}
```
## 重写鼠标事件，实现拖动和滑轮缩放
```
//鼠标点击事件，拖动
void PixItem::mousePressEvent(QGraphicsSceneMouseEvent *event)
{
    m_startPos = event->pos();
    m_isMove = true;
}

void PixItem::mouseMoveEvent(QGraphicsSceneMouseEvent *event)
{
    if(m_isMove)
    {
        QPointF point = event->pos() - m_startPos;
        moveBy(point.x(), point.y());
    }
}
void PixItem::mouseReleaseEvent(QGraphicsSceneMouseEvent *)
{
    m_isMove = false;
}

//使用滚轮整体缩放
void PixItem::wheelEvent(QGraphicsSceneWheelEvent *event)
{
    /*
    setScale(s);
    setTransformOriginPoint(event->pos().x(), event->pos().y());*/

    if(event->delta() > 0)  //delta（）为正，滚轮向上滚
    {
        m_scaleValue = 1.1;
    }
    else
    {
        m_scaleValue = 1/1.1;
    }

    //qDebug() << m_scaleValue;
    emit zoomIn(m_scaleValue);
}
```
## 平移
```
pixItem->moveBy(0, -20);//上移
pixItem->moveBy(0, 20);//下移
pixItem->moveBy(-20, );//左移
pixItem->moveBy(20, 0);//右移
```
## 缩放
原图不变，将贴进scene的图片利用
> scaled(width,height,Qt::KeepAspectRatio,Qt::SmoothTransformation);

进行缩放，再remove元原先的Item，添加新的Item。
```
*img = raw_img->scaled(img->width()*1.1,img->height()*1.1,Qt::KeepAspectRatio,Qt::SmoothTransformation);
//*img = raw_img->scaled(img->width()/1.1,img->height()/1.1,Qt::KeepAspectRatio,Qt::SmoothTransformation);

int x = pixItem->x(), y = pixItem->y();
scene->removeItem(pixItem);
pixItem = new PixItem(QPixmap::fromImage(*img));
connect(pixItem,SIGNAL(zoomIn(qreal)),this,SLOT(zoomInSlot(qreal)));
scene->addItem(pixItem);
pixItem->setPos(x,y);
```
## 旋转
原图旋转，重新添加Item：
* QImage和QPixmap的旋转:
> QMatrix leftmatrix;
leftmatrix.rotate(90);
*raw_img = raw_img-transformed(leftmatrix,Qt::SmoothTransformation);
* Item旋转：
> pixItem->setRotation(pixItem->rotation()+90);  //顺时90

```
QMatrix leftmatrix;
leftmatrix.rotate(90);
*raw_img = raw_img->transformed(leftmatrix,Qt::SmoothTransformation);

pixItem->setRotation(pixItem->rotation()+90);  //顺时90
```
## 镜像
原图镜像，重新添加Item：
* QImage和QPixmap的镜像:
> *raw_img = raw_img->mirrored(true, false);

```
*raw_img = raw_img->mirrored(true, false); //水平镜像
*raw_img = raw_img->mirrored(false, true); //垂直镜像
*raw_img = raw_img->mirrored(true, true); //垂直水平镜像

```
## 调节亮度/对比度/转灰度图
[QT获取图片RGB值并改变图片亮度算法](http://blog.csdn.net/taoerit/article/details/40190135)
[图像的亮度和对比度调整算法](http://blog.csdn.net/pizi0475/article/details/6740428)
通过获取图片的RGB值，根据算法调整RGB的值，进而达到改变亮度或对比度等的效果。
```
uchar *line =raw_img->scanLine(0);
uchar *pixel = line;

for (int y = 0; y < raw_img->height(); ++y)
{
    pixel = line;
    for (int x = 0; x < raw_img->width(); ++x)
    {
    	//调节亮度
    	*pixel = qBound(0, *pixel - 10, 255); //red
        *(pixel + 1) = qBound(0, *(pixel + 1) - 10, 255);//green
        *(pixel + 2) = qBound(0, *(pixel + 2) - 10, 255);//blue

		//调节对比度
        //*pixel = qBound(0, (int)((*pixel-127 )*1.3)+127, 255);
        //*(pixel + 1) = qBound(0, (int)((*(pixel+1)-127 )*1.3)+127, 255);
        //*(pixel + 2) = qBound(0, (int)((*(pixel+2)-127 )*1.3)+127, 255);

        //转灰度图
        //uchar gray = qGray(*pixel,*(pixel + 1),*(pixel + 2));
        //*pixel = gray;
        //*(pixel + 1) = gray;
        //*(pixel + 2) = gray;

        pixel += 4;
    }
    line += raw_img->bytesPerLine();
}
```
还有一种效率不高的，建议使用上面这种：
```
for (int x = 0; x < raw_img->width(); ++x)
{
    for (int y = 0; y < raw_img->height(); ++y)
    {
        //利用QColor
        QColor old = QColor(raw_img->pixel(x, y));
        int r=qBound(0, old.red()-10, 255);
        int g=qBound(0, old.green()-10, 255);
        int b=qBound(0, old.blue()-10, 255);

		//利用QRgb
        /*QRgb pixel=raw_img->pixel(x,y);
        int r=qBound(0, qRed(pixel)+10, 255);
        int g=qBound(0, qGreen(pixel)+10, 255);
        int b=qBound(0, qBlue(pixel)+10, 255);*/

		//利用QGray将QRgb转成gray值，赋给r,g,b
        /*QRgb pixel = raw_img->pixel(i,j);
        int gray = qGray(pixel);
        raw_img->setPixel(i,j,qRgb(gray,gray,gray));*///灰度

        raw_img->setPixel(x,y,qRgb(r,g,b));
    }
}
```