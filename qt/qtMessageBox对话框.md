所谓标准对话框，是 Qt 内置的一系列对话框，用于简化开发。事实上，有很多对话框都是通用的，比如打开文件、设置颜色、打印设置等。这些对话框在所有程序中几乎相同，因此没有必要在每一个程序中都自己实现这么一个对话框。

Qt 的内置对话框大致分为以下几类：

- `QColorDialog`：选择颜色；
- `QFileDialog`：选择文件或者目录；
- `QFontDialog`：选择字体；
- `QInputDialog`：允许用户输入一个值，并将其值返回；
- `QMessageBox`：模态对话框，用于显示信息、询问问题等；
- `QPageSetupDialog`：为打印机提供纸张相关的选项；
- `QPrintDialog`：打印机配置；
- `QPrintPreviewDialog`：打印预览；
- `QProgressDialog`：显示操作过程。

## QMessageBox
`QMessageBox`用于显示消息提示。我们一般会使用其提供的几个 static 函数：

- `void about(QWidget * parent, const QString & title, const QString & text)`：显示关于对话框。这是一个最简单的对话框，其标题是 title，内容是 text，父窗口是 parent。对话框只有一个 OK 按钮。
- `void aboutQt(QWidget * parent, const QString & title = QString())`：显示关于 Qt 对话框。该对话框用于显示有关 Qt 的信息。
- `StandardButton critical(QWidget * parent, const QString & title, const QString & text, StandardButtons buttons = Ok, StandardButton defaultButton = NoButton)`：显示严重错误对话框。这个对话框将显示一个红色的错误符号。我们可以通过 buttons 参数指明其显示的按钮。默认情况下只有一个 Ok 按钮，我们可以使用`StandardButtons`类型指定多种按钮。
- `StandardButton information(QWidget * parent, const QString & title, const QString & text, StandardButtons buttons = Ok, StandardButton defaultButton = NoButton)`：`QMessageBox::information()`函数与`QMessageBox::critical()`类似，不同之处在于这个对话框提供一个普通信息图标。
- `StandardButton question(QWidget * parent, const QString & title, const QString & text, StandardButtons buttons = StandardButtons( Yes | No ), StandardButton defaultButton = NoButton)`：`QMessageBox::question()`函数与`QMessageBox::critical()`类似，不同之处在于这个对话框提供一个问号图标，并且其显示的按钮是“是”和“否”两个。
- `StandardButton warning(QWidget * parent, const QString & title, const QString & text, StandardButtons buttons = Ok, StandardButton defaultButton = NoButton)`：`QMessageBox::warning()`函数与`QMessageBox::critical()`类似，不同之处在于这个对话框提供一个黄色叹号图标。
```c++
if (QMessageBox::Yes == QMessageBox::question(this,
											tr("Question"),
											tr("Are you OK?"),																QMessageBox::Yes | QMessageBox::No,
											QMessageBox::Yes)) {

QMessageBox::information(this, tr("Hmmm..."), tr("I'm glad to hear that!"));

} else {

QMessageBox::information(this, tr("Hmmm..."), tr("I'm sorry!"));

}
```
`QMessageBox`类的 static 函数优点是方便使用，缺点也很明显：非常不灵活。我们只能使用简单的几种形式。为了能够定制`QMessageBox`细节，我们必须使用`QMessageBox`的属性设置 API。如果我们希望制作一个询问是否保存的对话框，我们可以使用如下的代码：
```c++
QMessageBox msgBox;
msgBox.setText(tr("文件已更改"));
msgBox.setInformativeText(tr("是否要保存"));
msgBox.setDetailedText(tr("Differences here..."));//这个是用来显示详细信息的，会自动生成一个show details按钮。不调用此api的话就不会生成
msgBox.setStandardButtons(QMessageBox::Save
    | QMessageBox::Discard
    | QMessageBox::Cancel);
msgBox.setDefaultButton(QMessageBox::Save);
int ret = msgBox.exec();
switch (ret) {
case QMessageBox::Save:
    qDebug() << "Save document!";
    break;
case QMessageBox::Discard:
    qDebug() << "Discard changes!";
    break;
case QMessageBox::Cancel:
    qDebug() << "Close document!";
    break;
}
```