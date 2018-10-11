# QT拖拽功能
## **QDrag and QDrop**
>2018.10.11 
>>https://blog.csdn.net/vvsxr/article/details/49384199

### 1.QDrag
首先是创建QDrag，可以在mousePressEvent或者mouseMoveEvent中创建。
```
void DragDropWidget::mousePressEvent(QMouseEvent *event)
{
	if (event->button() == Qt::LeftButton)
	{
		QDrag *drag = new QDrag(this);
		QMimeData *data = new QMimeData;
		drag->setMimeData(data);
		drag->exec(Qt::MoveAction);
	}
}
```

QDrag在exec前，一定要设置QMimeData，否则会弹出警告“QDrag: No mimedata set before starting the drag”。并且不会开始拖拽操作。
QMimeData在拖拽中非常有用，可以用来保存拖拽操作附带的信息，比如字符串、文件或者图片，同时也可以用来验证其所保存的信息格式，并以此来判断是否可接收。
另外要注意，在windows下，QDrag::exec()是个同步操作，要在exec()返回后，才会继续执行下面的代码。

### 2.QDrag相关事件
首先，当需要一个控件接收drag和drop，就要先调用控件的方法：setAcceptDrops(true)。

qt中一共有三个drag相关事件，dragEnterEvent、dragMoveEvent、dragLeaveEvent。这三个事件触发条件类似鼠标移入，鼠标移动，鼠标移出。当鼠标拖拽进入控件触发dragEnterEvent，在控件内拖拽移动触发dragMoveEvent，鼠标拖拽离开控件触发dragLeaveEvent。

dragEnterEvent事件有一个参数，参数类型是QDragEnterEvent，继承自QDragMoveEvent。

dragMoveEvent事件有一个参数，参数类型是QDragMoveEvent。

dragLeaveEvent事件有一个参数，参数类型是QDragLeaveEvent，继承自QEvent，无特别用法。

当鼠标拖拽进入控件时，会触发dragEnterEvent，如果不做处理，后续将不会接收到dragMoveEvent事件和dragLeaveEvent事件。在dragEnterEvent事件中，如果调用了QDragMoveEvent::accept()函数，后续将可以收到dragMoveEvent事件和dragLeaveEvent事件。而如果调用QDragMoveEvent::ignore()函数，效果相当于不处理，不会接收后续事件。

### 3.QDrop相关事件
当drag为accept状态，然后释放鼠标，就会产生dropEvent。我们可以在这个事件里处理本次拖拽附带的Mime信息。

