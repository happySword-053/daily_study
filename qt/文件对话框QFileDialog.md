文件对话也属于dialog类，是选择文件的模态对话框
```c++
 QFileDialog* dialog = new QFileDialog(      this,
                                           "Open File",//窗口名称
                                           ".",//选的目录，.表示当前目录
                                           "Text Files(*.txt)");//选择的文件格
int result = dialog->exec();
```
文件的选择和保存
```c++
void daily_study_qt::openFile()
{
    QString path = QFileDialog::getOpenFileName(this,
        tr("Open File"),
        ".",
        tr("Text Files(*.txt)"));
    if (!path.isEmpty()) {
        QFile file(path);
         /*QIODevice::ReadOnly 表示文件将以只读模式打开。
 QIODevice::Text 表示文件将被当作文本文件处理，这意味着当从文件中读取或向文件中写入数据时，会自动处理行尾的 \n 字符。*/
        if (!file.open(QIODevice::ReadOnly | QIODevice::Text)) {
            QMessageBox::warning(this, tr("Read File"),
                tr("Cannot open file:\n%1").arg(path));
            return;
        }
        QTextStream in(&file);
        textEdit->setText(in.readAll());
        file.close();
    }
    else {
        QMessageBox::warning(this, tr("Path"),
            tr("You did not select any file."));
    }
}
void daily_study_qt::saveFile()
{
    QString path = QFileDialog::getSaveFileName(this,
        tr("Open File"),
        ".",
        tr("Text Files(*.txt)"));
    if (!path.isEmpty()) {
        QFile file(path);
         /*QIODevice::ReadOnly 表示文件将以只写模式打开。
 QIODevice::Text 表示文件将被当作文本文件处理，这意味着当从文件中读取或向文件中写入数据时，会自动处理行尾的 \n 字符。*/
        if (!file.open(QIODevice::WriteOnly | QIODevice::Text)) {
            QMessageBox::warning(this, tr("Write File"),
                tr("Cannot open file:\n%1").arg(path));
            return;
        }
        QTextStream out(&file);
        out << textEdit->toPlainText();
        file.close();
    }
    else {
        QMessageBox::warning(this, tr("Path"),
            tr("You did not select any file."));
    }
}
daily_study_qt::daily_study_qt(QWidget *parent)
    : QMainWindow(parent)
{
    ui.setupUi(this);
    QPushButton* button = new QPushButton("ok", this);
    button->move(100, 100);
    QAction* openAction = new QAction(this);
    QAction* saveAction = new QAction(this);
    openAction = new QAction(QIcon(":/images/file-open"), tr("&Open..."), this);
    openAction->setShortcuts(QKeySequence::Open);//快捷键ctrl + o
    openAction->setStatusTip(tr("Open an existing file"));
    saveAction = new QAction(QIcon(":/images/file-save"), tr("&Save..."), this);
    saveAction->setShortcuts(QKeySequence::Save);
    saveAction->setStatusTip(tr("Save a new file"));
    QMenu* file = menuBar()->addMenu(tr("&File"));
    file->addAction(openAction);
    file->addAction(saveAction);
    QToolBar* toolBar = addToolBar(tr("&File"));
    toolBar->addAction(openAction);
    toolBar->addAction(saveAction);
    textEdit = new QTextEdit(this);
    setCentralWidget(textEdit);
    connect(openAction, &QAction::triggered, this, &daily_study_qt::openFile);
    connect(saveAction, &QAction::triggered, this, &daily_study_qt::saveFile);
    connect(button, &QPushButton::clicked, this, [&]() {
        
        QFileDialog* dialog = new QFileDialog(      this,
                                                  "Open File",
                                                  ".",
                                                  "Text Files(*.txt)");
        int result = dialog->exec();
        if(result == QDialog::Accepted)
            qDebug() << dialog->selectedFiles().at(0);
        

        delete dialog;
        dialog = nullptr;
        if(dialog == nullptr)qDebug()<<"dialog is nullptr";
    });
}
```
`QFileDialog::getOpenFileName()`返回值是选择的文件路径。我们将其赋值给 path。通过判断 path 是否为空，可以确定用户是否选择了某一文件。只有当用户选择了一个文件时，我们才执行下面的操作。在`saveFile()`中使用的`QFileDialog::getSaveFileName()`也是类似的。使用这种静态函数，在 Windows、Mac OS 上面都是直接调用本地对话框，但是 Linux 上则是`QFileDialog`自己的模拟。这暗示了，如果你不使用这些静态函数，而是直接使用`QFileDialog`进行设置，就像我们前面介绍的 QMessageBox 的设置一样，那么得到的对话框很可能与系统对话框的外观不一致。这一点是需要注意的。

首先，我们创建一个`QFile`对象，将用户选择的文件路径传递给这个对象。然后我们需要打开这个文件，使用的是`QFile::open()`，其参数是指定的打开方式，这里我们使用只读方式和文本方式打开这个文件（因为我们选择的是后缀名 txt 的文件，可以认为是文本文件。当然，在实际应用中，可能需要进行进一步的判断）。`QFile::open()`打开成功则返回 true，由此继续进行下面的操作：使用`QTextStream::readAll()`读取文件所有内容，然后将其赋值给`QTextEdit`显示出来。最后不要忘记关闭文件。另外，`saveFile()`函数也是类似的，只不过最后一步，我们使用`<<`重定向，将`QTextEdit`的内容输出到一个文件中。