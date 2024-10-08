menubar是可以折叠的，点击之后会弹出相应的功能
```c++
 //触发的动作
 QAction* closeAction = new QAction(this);
 closeAction->setText("Close");
 //menubar要添加menu类
 QMenu* menu = new QMenu("text", this);
 menu->addAction(closeAction);
 ui.menuBar->addMenu(menu);
 //ui.menuBar->addAction(closeAction);
 connect(closeAction,&QAction::triggered,this,&daily_study_qt::close);
```
![[Pasted image 20240802174342.png]]
menubar也可以直接添加qaction
![[Pasted image 20240802174557.png]]
toolbar相对来说较简单，直接添加qaction即可
![[Pasted image 20240802174657.png]]