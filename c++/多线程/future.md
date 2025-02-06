前面已经多次提到过 std::future，那么 std::future 究竟是什么呢？简单地说，std::future 可以用来获取异步任务的结果，因此可以把它当成一种简单的线程间同步的手段。std::future 通常由某个 Provider 创建，你可以把 Provider 想象成一个异步任务的提供者，Provider 在某个线程中设置共享状态的值，与该共享状态相关联的 std::future 对象调用 get（通常在另外一个线程中） 获取该值，如果共享状态的标志不为 ready，则调用 std::future::get 会阻塞当前的调用者，直到 Provider 设置了共享状态的值（此时共享状态的标志变为 ready），std::future::get 返回异步任务的值或异常（如果发生了异常）。

一个有效(valid)的 std::future 对象通常由以下三种 Provider 创建，并和某个共享状态相关联。Provider 可以是函数或者类，其实我们前面都已经提到了，他们分别是：

- std::async 函数，本文后面会介绍 std::async() 函数。
- std::promise::get_future，get_future 为 promise 类的成员函数
- std::packaged_task::get_future，此时 get_future 为 packaged_task 的成员函数。
一个 std::future 对象只有在有效(valid)的情况下才有用(useful)，由 std::future 默认构造函数创建的 future 对象不是有效的（除非当前非有效的 future 对象被 move 赋值另一个有效的 future 对象）。

在一个有效的 future 对象上调用 get 会阻塞当前的调用者，直到 Provider 设置了共享状态的值或异常（此时共享状态的标志变为 ready），std::future::get 将返回异步任务的值或异常（如果发生了异常）。
# shared_future
