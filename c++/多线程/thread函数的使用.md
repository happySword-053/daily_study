thread 构造函数
类型	函数
- 默认构造函数	thread() noexcept;  默认构造函数，创建一个空的 std::thread 执行对象。
- 初始化构造函数	template <class Fn, class… Args> 初始化构造函数，创建一个 std::thread 对象，该 std::thread 对象可被 joinable，新产生的线程会调用 fn 函数，该函数的参数由 args 给出。
- 拷贝构造函数 [deleted]	thread(const thread&) = delete; 拷贝构造函数(被禁用)，意味着 std::thread 对象不可拷贝构造。***不可拷贝**！*
- Move 构造函数	thread(thread&& x) noexcept;   Move 构造函数，move 构造函数调用成功之后 x 不代表任何 std::thread 执行对象。




例子
```c++
#include <iostream>
#include <utility>
#include <thread>
#include <chrono>
#include <functional>
#include <atomic>

void f1(int n)
{
    for (int i = 0; i < 5; ++i) {
        std::cout << "Thread " << n << " executing\n";
        std::this_thread::sleep_for(std::chrono::milliseconds(1000));
    }
}

void f2(int& n)
{
    for (int i = 0; i < 5; ++i) {
        std::cout << "Thread 2 executing\n";
        ++n;
        std::this_thread::sleep_for(std::chrono::milliseconds(1000));
    }
}

int main()
{
    int n = 0;
    std::thread t1;                   // t1 is not a thread
    std::thread t2(f1, n + 1);        // pass by value
    std::thread t3(f2, std::ref(n));  // pass by reference, ref表示传引用
    std::thread t4(std::move(t3));    // t4 is now running f2(). t3 is no longer a thread
    t2.join();
    t4.join();
    std::cout << "Final value of n is " << n << '\n';
}

```
