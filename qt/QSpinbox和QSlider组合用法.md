```c++
QSpinBox* box = new QSpinBox(this);
QSlider* slider = new QSlider(Qt::Horizontal, this);
//调节两个控件的最大值和最小值
box->setRange(0, 130);
slider->setRange(0, 130);
box->setValue(35);
//连接槽函数，使得两个控件能够相互联系
//函数指针转换，因为valueChanged函数有重载版本，不用函数指针的话connect函数就不会知道用哪个函数版本
void (QSpinBox:: * spinBoxSignal)(int) = &QSpinBox::valueChanged;
QObject::connect(box, spinBoxSignal, slider, &QSlider::setValue);

QObject::connect(slider, &QSlider::valueChanged, box, &QSpinBox::setValue);

box->move(0, 0);
slider->move(0, 30);
```