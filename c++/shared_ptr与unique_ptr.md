# 概述
正如名称所见，shared表示共享，unique表示独一维二，所以shared_ptr支出复制，而unique不行

# unique_ptr
unique_ptr 不共享它的指针。它无法复制到其他 unique_ptr，无法通过值传递到函数，也无法用于需要副本的任何标准模板库 (STL) 算法。只能移动unique_ptr。这意味着，内存资源所有权将转移到另一 unique_ptr，并且原始 unique_ptr 不再拥有此资源。我们建议你将对象限制为由一个所有者所有，因为多个所有权会使程序逻辑变得复杂。因此，当需要智能指针用于纯 C++ 对象时，可使用 unique_ptr，而当构造 unique_ptr 时，可使用make_unique 函数。
std::unique_ptr 实现了独享所有权的语义。一个非空的 std::unique_ptr 总是拥有它所指向的资源。转移一个 std::unique_ptr 将会把所有权也从源指针转移给目标指针（源指针被置空）。拷贝一个 std::unique_ptr 将不被允许，因为如果你拷贝一个 std::unique_ptr ,那么拷贝结束后，这两个 std::unique_ptr 都会指向相同的资源，它们都认为自己拥有这块资源（所以都会企图释放）。因此 std::unique_ptr 是一个仅能移动（move_only）的类型。当指针析构时，它所拥有的资源也被销毁。默认情况下，资源的析构是伴随着调用 std::unique_ptr 内部的原始指针的 delete 操作的。
自定义删除
```c++
auto deleter = [](int* p) {
    delete p;
};
int* p = new int[10] {1, 2, 3, 4, 5, 6, 7, 8, 9};
unique_ptr<int[],decltype(deleter)> ptr(p, deleter);
for (int i = 0; i < 10; i++) {
    cout << *ptr.get() + i << endl;

}
cout << "before release:" << ptr << endl;

ptr.release();
shared_ptr<int[]> sp = move(ptr);
cout << "after release:" << ptr << endl;
```
# shared_ptr
shared_ptr 代表的是共享所有权，即多个 shared_ptr 可以共享同一块内存。
因此，从语义上来看，shared_ptr 是支持复制的。如下：
```C++
auto w = std::make_shared<Widget>();
{
    auto w2 = w;
    cout << w.use_count() << endl;  // 2
}
cout << w.use_count() << endl;  // 1
```
shared_ptr 内部是利用引用计数来实现内存的自动管理，每当复制一个 shared_ptr，引用计数会 + 1。当一个 shared_ptr 离开作用域时，引用计数会 - 1。当引用计数为 0 的时候，则 delete 内存。

同时，shared_ptr 也支持移动。从语义上来看，移动指的是所有权的传递。如下：

```C++
auto w = std::make_shared<Widget>();
auto w2 = std::move(w); // 此时 w 等于 nullptr，w2.use_count() 等于 1
```
我们将 w 对象 move 给 w2，意味着 w 放弃了对内存的所有权和管理，此时 w 对象等于 nullptr。
而 w2 获得了对象所有权，但因为此时 w 已不再持有对象，因此 w2 的引用计数为 1。

用法：

```C++
std::shared_ptr<类型> 变量名称{}；

std::shared_ptr<int> ptrA{};
 
std::shared_ptr<int> ptrB{std::make_shared<int>(5)};
注意：std::make_shared不支持数组。

std::shared_ptr<int[]> ptrC{ new int[5]{1, 2, 3, 4, 5} };
```
## 2.2 性能
- 内存占用高
shared_ptr 的内存占用是裸指针的两倍。因为除了要管理一个裸指针外，还要维护一个引用计数。
因此相比于 unique_ptr, shared_ptr 的内存占用更高

- 原子操作性能低
考虑到线程安全问题，引用计数的增减必须是原子操作。而原子操作一般情况下都比非原子操作慢。

- 使用移动优化性能
shared_ptr 在性能上固然是低于 unique_ptr。而通常情况，我们也可以尽量避免 shared_ptr 复制。
如果，一个 shared_ptr 需要将所有权共享给另外一个新的 shared_ptr，而我们确定在之后的代码中都不再使用这个 shared_ptr，那么这是一个非常鲜明的移动语义。
对于此种场景，我们尽量使用 std::move，将 shared_ptr 转移给新的对象。因为移动不用增加引用计数，性能比复制更好。

## 2.3 使用场景
shared_ptr 通常使用在共享权不明的场景。有可能多个对象同时管理同一个内存时。
对象的延迟销毁。陈硕在《Linux 多线程服务器端编程》中提到，当一个对象的析构非常耗时，甚至影响到了关键线程的速度。可以使用 BlockingQueue<std::shared_ptr<void>> 将对象转移到另外一个线程中释放，从而解放关键线程


