# c++ async 使用详解
std::async
```c++
template<class Function, class... Args>
std::future<std::invoke_result_t<std::decay_t<Function>,
                                 std::decay_t<Args>...>>
    async(std::launch policy, Function&& f, Args&&... args);

template<class Function, class... Args>
std::future<std::invoke_result_t<std::decay_t<Function>,
                                 std::decay_t<Args>...>>
    async(Function&& f, Args&&... args);
// 等同于以 std::launch::async | std::launch::deferred 策略调用上面的函数
1
```



按默认情况下，std：async（）的具体实现会自行决定——等待future时，是启动新线程，还是同步执行任务。大多数情况下，我们正希望如此。不过，我们还能够给std：async（）补充一个参数，以指定采用哪种运行方式。参数的类型是std：launch，其值可以是std:launch:deferred 或 std:launch:async。前者指定在当前线程上延后调用任务函数，等 到在future上调用了wait（）或get（），任务函数才会执行；后者指定必须另外开启专属的线程，在其上运行任务函数。该参数的值还可以是std:launch:deferred |std:launch: async，表示由std：async（）的实现自行选择运行方式。最后这项是参数的默认值。若延后调用任务函数，则任务函数有可能永远不会运行。举例如下。

![[Pasted image 20241213173144.png]]


本章的后半部分和第8章将向读者讲解，凭借std：async（），即能简便地把算法拆分成多个子任务，且可并发运行。不过，使std：future和任务关联并非唯一的方法：运用类模板std：packaged_task◇的实例，我们也能将任务包装起来；又或者，利用std:promise类模板编写代码，显式地异步求值。与std:promise相比，std:packaged_task 的抽象层级更高，我们从它开始介绍。
```c++
policy：//以何种策略调用可调用对象 f，可以为以下三种：
std::launch::async：//以异步的方式调用 f，即必须另外开启专属的线程，在其上运行 f。
std::launch::deferred：//在返回的 std::future 上调用了非定时等待函数，即 wait 或者 get 时，才执行 f。
std::launch::async | std::launch::deferred：//可能异步运行 f 或者直到调用 wait 或者 get 时才运行，取决于系统的负载，无法人为控制。
f：//要调用的可调用对象。
args：//传递给 f 的参数。
```
函数返回值：

返回 std::future 对象，在其上调用 wait 可以等待 f 完成，调用 get 可以等待并获取 f 的返回值。 -
函数作用：

以异步或者同步的调用可调用对象 f，并可以通过返回的 std::future 对象获取 f 的返回值。
使用注意
如果没有任何对象接收 std::sync 的返回值，即使指定了 std::launch::async 策略，std::future 的析构函数也会阻塞直到整个调用完成。

示例如下：

```c++
// 临时量即 std::future 的析构函数等待睡眠完成。
std::async(std::launch::async, []{ std::this_thread::sleep_for(10ms); });
// 在睡眠完成之前，本行代码不会得到运行
std::async(std::launch::async, []{ printf("xx"); });

```
只有从 std::async 获取的 std::future 对象其析构函数会阻塞，以其他方式获得的 std::future 对象则不会。

示例1 函数作为参数
```c++
#include <stdio.h>
#include <thread>
#include <future>
#include <chrono>

int f1(int n, int& b)
{
    for (int i = 0; i < 3; ++i) {
        printf("async is running\n");
        ++n;
        ++b;
        std::this_thread::sleep_for(std::chrono::milliseconds(10));
    }
    printf("n is %d, b is %d\n", n, b);
    return n + b;
}

int main()
{
    int n = 0;
    int b = 0;
    // n 按值传递，b 按引用传递
    auto f = std::async(std::launch::async, f1, n, std::ref(b));
    int ret = f.get();
    printf("result is %d\n", ret);
    return 0;
}

```
示例2 lamba 作为参数
```c++
#include <stdio.h>
#include <thread>
#include <future>
#include <chrono>

int main()
{
    auto f = std::async([]() -> int {
        for (int i = 0; i < 3; ++i) {
            printf("async is running\n");
            std::this_thread::sleep_for(std::chrono::milliseconds(10));
        }
        return 100;
    });
    int ret = f.get();
    printf("result is %d\n", ret);
    return 0;
}

```
示例3 std::function 作为参数
```c++
#include <stdio.h>
#include <thread>
#include <future>
#include <chrono>
#include <functional>

int main()
{
    std::function<int(void)> func = []() -> int {
        for (int i = 0; i < 3; ++i) {
            printf("async is running\n");
            std::this_thread::sleep_for(std::chrono::milliseconds(10));
        }
        return 100;
    };
    auto f = std::async(func);
    int ret = f.get();
    printf("result is %d\n", ret);
    return 0;
}

```