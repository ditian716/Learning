# Learning

## 1. 如何在QLable的图像上画线
>2018.10.11 https://bbs.csdn.net/topics/391844175?page=1

**继承一个QLabel, 然后在paintEvent中画**
1. 新建一个类比如 MyLabel, 继承于QLabel, 用向导生成即可
2. 添加虚函数 virtual void paintEvent(QPaintEvent *) override 要画什么在这函数里面画. 
3. 然后双击ui文件打开设计器, 找到label, 在对象查看中右击类名称,选择提升, 里面填MyLabel, 保存ui, 重新生成就可以了

```

#ifndef MYLABEL_H
#define MYLABEL_H
#include <QLabel>
#include <QPoint>
#include <QColor>
#include <QPaintEvent>
#include <QImage>
#include <QPixmap>

class myLabel : public QLabel
{
    //Q_OBJECT  //<span style="color: #FF0000;">必须没有，要不然会报错，因为Label不是Widget等</span>
public:
    myLabel();
    //~myLabel();
    //绘制线条
    virtual void paintEvent(QPaintEvent *event) override;
    //鼠标按下
    void mousePressEvent(QMouseEvent *e);
    //鼠标移动
    void mouseMoveEvent(QMouseEvent *e);
    //鼠标抬起
    void mouseReleaseEvent(QMouseEvent *e);

    //设置所画线条属性
    void setLineColor(const QColor lineColor);
    void setLineSize(const int lineSize);
    //得到画线的起点和终点
    QPoint getStartPoint();
    QPoint getEndPoint();
    //将图片设置为背景
    void setImage(const QImage &pic);

    void clear();

private:
    QPoint lineStartPoint;          //画线起点
    QPoint lineEndPoint;            //画线终点
    QColor lineColor;               //线条颜色
    int lineSize;                  //5种线型
    bool isPressed;
};

#endif // MYLABEL_H
```

```

#include "myLabel.h"
#include <QPen>
#include<QPainter>

myLabel::myLabel()
{
    this->lineStartPoint = QPoint(0,0);
    this->lineEndPoint = QPoint(0,0);
    this->lineColor = QColor(Qt::black);
    this->lineSize = 3;
}

//绘制线条
void myLabel::paintEvent(QPaintEvent *event)
{
    QLabel::paintEvent(event);//<span style="color: #FF0000;">必须有，才能让自己设置的背景图片显示出来</span>
    QPainter painter(this);
    QPen pen;
    pen.setColor(lineColor);
    pen.setWidth(lineSize);
    painter.setPen(pen);
    painter.drawLine(lineStartPoint,lineEndPoint);
}

//鼠标按下
void myLabel::mousePressEvent(QMouseEvent *e)
{
    lineStartPoint = e->pos();
    lineEndPoint = e->pos();
    //在图片上绘制
    isPressed = true;
}

//鼠标移动
void myLabel::mouseMoveEvent(QMouseEvent *e)
{
    if(isPressed)
    {
        lineEndPoint=e->pos();
        update();
    }
}

//鼠标抬起
void myLabel::mouseReleaseEvent(QMouseEvent *e)
{
    isPressed=false;
    update();
}

void myLabel::setLineColor(const QColor lineColor)
{
    this->lineColor = lineColor;
}

void myLabel::setLineSize(const int lineSize)
{
    this->lineSize = lineSize;
}

QPoint myLabel::getStartPoint()
{
    return lineStartPoint;
}

QPoint myLabel::getEndPoint()
{
    return lineEndPoint;
}

void myLabel::setImage(const QImage &pic)
{
    this->setPixmap(QPixmap::fromImage(pic));
}

void myLabel::clear()
{
    lineStartPoint = QPoint(0,0);
    lineEndPoint = QPoint(0,0);
    update();
}
```
