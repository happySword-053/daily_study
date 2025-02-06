# 定义
`std::packaged_task` 是 C++11 引入的一个模板类，位于 `<future>` 头文件中。它封装了一个可调用对象（如函数、lambda 表达式或绑定表达式），并且可以异步执行该任务。当任务完成时，它会设置一个与之关联的 `std::future` 对象，这个 `future` 可以用来获取任务的结果或检查任务的状态。
# 功能
它主要负责封装一个可调用对象，并提供一种机制来异步获取该任务的结果。`std::packaged_task` 本身并不启动任务；它只是一个包装器，你可以选择在任何时间点、任何上下文中通过调用它的 `operator()` 来执行任务。通常情况下，你会将 `std::packaged_task` 传递给 `std::thread` 或者其他并发设施（如线程池）来实际启动任务的执行。因此，`std::packaged_task` 提供了更大的灵活性，因为它让你能够显式控制任务何时以及如何被执行。

以下是关于 `std::packaged_task` 的详细用法说明：

### 1. 创建 `packaged_task`

要创建一个 `packaged_task`，你需要指定其返回类型和参数列表，并提供一个可调用对象作为参数。例如：

cpp

深色版本

```c++
#include <iostream>
#include <future>
#include <thread>

// 定义一个简单的函数
int add(int a, int b) {
    return a + b;
}

int main() {
    // 创建 packaged_task，指定返回类型为 int 并接受两个 int 参数
    std::packaged_task<int(int, int)> task(add);

    // 获取与 task 关联的 future
    std::future<int> result = task.get_future();

    // ...
}
```

### 2. 执行 `packaged_task`

你可以通过直接调用 `operator()` 来执行 `packaged_task`，也可以将其传递给线程或其他异步机制来执行。执行后，`packaged_task` 不再有效，不能再次调用。

cpp

深色版本

```c++
// 在主线程中同步执行任务
task(1, 2); // 这里会立即执行任务

// 或者在一个新线程中异步执行任务
std::thread t(std::move(task), 1, 2);
t.join();
```

注意：`packaged_task` 一旦被移动后就变成了空状态，不能再被调用。因此，在将 `packaged_task` 传递给线程时需要使用 `std::move` 将所有权转移过去。

### 3. 获取结果

在任务完成后，可以通过之前从 `packaged_task` 获取的 `std::future` 对象来访问任务的结果。`future` 提供了多种方法来等待和获取结果：

- `get()`：阻塞直到结果可用，然后返回结果或抛出异常。
- `wait()`：阻塞直到结果可用。
- `wait_for()` 和 `wait_until()`：分别等待一段时间或直到某个时间点，如果结果还没准备好则返回一个状态指示符。

cpp

深色版本

```c++
try {
    // 等待并获取结果
    int sum = result.get(); // 阻塞直到任务完成
    std::cout << "Result: " << sum << std::endl;
} catch (const std::exception& e) {
    std::cerr << "Exception caught: " << e.what() << '\n';
}
```

### 4. 使用 Lambda 表达式

`packaged_task` 也支持 lambda 表达式，这使得它可以很方便地用于更复杂的场景：

cpp

深色版本

```
auto lambdaTask = [](int x) { return x * x; };
std::packaged_task<int(int)> task(lambdaTask);
std::future<int> result = task.get_future();

std::thread t(std::move(task), 5);
t.join();

std::cout << "Squared: " << result.get() << std::endl;
```

### 5. 捕获异常

如果任务抛出了未捕获的异常，那么关联的 `future` 调用 `get()` 时将会重新抛出这个异常。因此，你应该总是准备好处理可能发生的异常情况。

### 6. 使用 `async` 替代

虽然 `packaged_task` 提供了一种灵活的方式来管理异步任务，但有时你可能会发现使用 `std::async` 更加简便。`std::async` 自动为你创建并启动任务，并返回一个 `std::future`。不过，请注意 `std::async` 默认是同步执行的，除非指定了 `std::launch::async` 策略。

cpp

深色版本

```
std::future<int> result = std::async(std::launch::async, add, 1, 2);
std::cout << "Result from async: " << result.get() << std::endl;
```