事件对象创建完毕后，Qt 将这个事件对象传递给`QObject`的`event()`函数。`event()`函数并不直接处理事件，而是将这些事件对象按照它们不同的类型，分发给不同的事件处理器（event handler）。

如上所述，`event()`函数主要用于事件的分发。所以，如果你希望在事件分发之前做一些操作，就可以重写这个`event()`函数了。例如，我们希望在一个`QWidget`组件中监听 tab 键的按下，那么就可以继承`QWidget`，并重写它的`event()`函数，来达到这个目的：
```c++
bool CustomWidget::event(QEvent *e)
{
    if (e->type() == QEvent::KeyPress) {
        QKeyEvent *keyEvent = static_cast<QKeyEvent *>(e);
        if (keyEvent->key() == Qt::Key_Tab) {
            qDebug() << "You press tab.";
            return true;
        }
    }
    return QWidget::event(e);
}
```

同时重载了`mousePressEvent`和`event`函数。这个窗口会响应鼠标点击事件，并且还会响应键盘按键事件。
```c++
#include <QWidget> 
#include <QMouseEvent> 
#include <QKeyEvent> 
#include <QEvent> 
class MyWidget : public QWidget { 
Q_OBJECT 
public: MyWidget(QWidget *parent = nullptr) : QWidget(parent) { 
// 设置窗口的一些属性 
	setWindowTitle("Event Handling Example"); 
	resize(300, 200); 
} 
protected: 
	void mousePressEvent(QMouseEvent *event) override { 
	// 处理鼠标按下事件 
	if (event->button() == Qt::LeftButton) { qDebug() << "Left mouse button pressed at" << event->pos(); } else if (event->button() == Qt::RightButton) { qDebug() << "Right mouse button pressed at" << event->pos(); } // 基类的实现 QWidget::mousePressEvent(event); } bool event(QEvent *event) override { switch (event->type()) { case QEvent::KeyPress: { QKeyEvent *keyEvent = static_cast<QKeyEvent*>(event); if (keyEvent->key() == Qt::Key_Escape) { qDebug() << "Escape key pressed"; // 如果是Escape键，关闭窗口 close(); return true; } break; } // 其他事件类型 default: break; } // 让基类处理事件 return QWidget::event(event); } };
```