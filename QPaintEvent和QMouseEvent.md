#Learning
##QPaintEvent和QMouseEvent
>2018.10.11 
>>https://blog.csdn.net/u013928315/article/details/78195491

###当发生一下情况时会产生绘制事件并调用paintEvent()函数: 

1.在窗口部件第一次显示时，系统会自动产生一个绘图事件，从而强制绘制这个窗口部件。

2.当重新调整窗口部件的大小时，系统也会产生一个绘制事件。

3.当窗口部件被其他窗口部件遮挡，然后又再次显示出来的时候，就会对那些隐藏的区域产生一个绘制事件。

同时可以调用QWidget::update()或者QWidget::repaint()来强制产生一个绘制事件。二者的区别是:

repaint()函数会强制产生一个即时的重绘事件,而update()函数只是在Qt下一次处理事件时才调用一次绘制事件。

如果多次调用update(),Qt会把连续多次的绘制事件压缩成一个单一的绘制事件，这样可避免闪烁现象。

>>https://blog.csdn.net/qq_37233607/article/details/78305585  
###QMouseEvent
QMouseEvent下可以被重写的虚函数: 
1.void mousePressEvent(QMouseEvent *event); //鼠标键被按下的事件. 
2.void mouseReleaseEvent(QMouseEvent *event); //鼠标键被放开的事件. 
3.void mouseMoveEvent(QMouseEvent *event); //按住鼠标键移动的事件. 
4.void mouseDoubleClickEvent(QMouseEvent *event); //鼠标键被双击的事件.

QMouseEvent类中的重要函数: 
1.QPoint pos(); //返回鼠标相对于父部件的坐标. 
2.QPoint globalPos(); //返回鼠标的全局坐标. 
3.QMouseButton button(); //返回鼠标被点击的键.

```
#include "c.h"

c::c(QWidget *parent)
    : QMainWindow(parent)
{
    ui.setupUi(this);
}

c::~c()
{

}

void c::mouseMoveEvent(QMouseEvent *event)
{
if (sign)
    {
    //保存鼠标移动的时候的尾坐标.
    end = event->pos();
    //用来重绘更新.可以理解为调用paintEvent函数，但不一定是立即调用的.在此程序中可以理解为:马上更新窗口上显示的图像.
    update();
    }
}
void c::paintEvent(QPaintEvent *event)
{
    //画一个矩形.
    QPainter painter(this);
    painter.setPen(Qt::red);

    QRect temp(start, end);
    painter.setBrush(Qt::green);
    painter.drawRect(temp);
}

void c::mouseReleaseEvent(QMouseEvent *event)
{
    //当左键释放后，保存尾坐标.
    if (event->button() == Qt::LeftButton)
    {
        this->end = event->pos();
        update();
        sign = false;
    }
}

void c::mousePressEvent(QMouseEvent *event)
{
    //当左键被按下时，保存首坐标.并更新重绘.
    if (event->button() == Qt::LeftButton)
    {
        //点击的时候定下初始位置.
        this->start = event->pos();
        sign = true;
    }
}
```

```
#ifndef C_H
#define C_H

#include <QtWidgets/QMainWindow>
#include "ui_c.h"
#include <QPixmap>
#include <QMouseEvent>
#include <QMessageBox>
#include <QPainter>
#include <QPaintEvent>
#include <QVector>

class c : public QMainWindow
{
    Q_OBJECT

public:
    c(QWidget *parent = 0);
    ~c();
protected:
    void mouseMoveEvent(QMouseEvent *event);
    void mouseReleaseEvent(QMouseEvent *event);
    void mousePressEvent(QMouseEvent *event);
    void paintEvent(QPaintEvent *event);
private:
    Ui::cClass ui;
    QPoint start;
    QPoint end;
    bool sign = false;
};

#endif // C_H
```

```
#include "c.h"
#include <QtWidgets/QApplication>

int main(int argc, char *argv[])
{
    QApplication a(argc, argv);
    c w;
    w.show();
    return a.exec();
}
```
