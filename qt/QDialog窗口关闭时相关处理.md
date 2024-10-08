QDialog是有返回值的，用QDialogButtonBox建立到窗口中，分别返回两个值QDialog::Accepted和QDialog::Rejected，表示用户点击取消还是确认，以此来进行处理
```c++
QDialog* dialog = new QDialog(this);
// 创建按钮布局
QDialogButtonBox* buttonBox = new QDialogButtonBox(QDialogButtonBox::Ok | QDialogButtonBox::Cancel, dialog);
dialog->setAttribute(Qt::WA_DeleteOnClose);
// 连接按钮布局的accepted信号到对话框的accept槽
connect(buttonBox, &QDialogButtonBox::accepted, dialog, &QDialog::accept);
// 连接按钮布局的rejected信号到对话框的reject槽
connect(buttonBox, &QDialogButtonBox::rejected, dialog, &QDialog::reject);
buttonBox->move(100, 100);


int result = dialog->exec();
if(result == QDialog::Accepted)
{
	//点击确认时的相应逻辑
    //qDebug() << "ok";
}
else if(result == QDialog::Rejected)
{
	//点击取消时的相应逻辑
    //qDebug() << "cancel";
}
```