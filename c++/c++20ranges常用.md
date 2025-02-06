C++20 的 `std::ranges` 库提供了丰富的功能，用于处理和操作范围（ranges）。这些功能包括视图（views）、动作（actions）、算法（algorithms）等。以下是一些常用的 `std::ranges` 函数和操作的概述：

### 视图（Views）

视图是一种懒惰的、可组合的操作，它们不会立即执行，而是在需要时才产生结果。视图可以链式调用，形成复杂的操作流水线。

#### 常见的视图操作

1. **`std::views::all`**：
    
    - 将任何范围转换为一个视图。
    - 例如：`auto view = std::views::all(vec);`
2. **`std::views::filter`**：
    
    - 过滤范围中的元素，只保留满足条件的元素。
    - 例如：`auto even = vec | std::views::filter([](int i) { return i % 2 == 0; });`
3. **`std::views::transform`**：
    
    - 对范围中的每个元素应用一个转换函数。
    - 例如：`auto squares = vec | std::views::transform([](int i) { return i * i; });`
4. **`std::views::take`**：
    
    - 获取范围的前 n 个元素。
    - 例如：`auto first_three = vec | std::views::take(3);`
5. **`std::views::drop`**：
    
    - 跳过范围的前 n 个元素。
    - 例如：`auto after_three = vec | std::views::drop(3);`
6. **`std::views::reverse`**：
    
    - 反转范围中的元素顺序。
    - 例如：`auto reversed = vec | std::views::reverse;`
7. **`std::views::split`**：
    
    - 按指定的分隔符将范围分割成多个子范围。
    - 例如：`auto words = str | std::views::split(' ');`
8. **`std::views::join_with`**：
    
    - 将多个范围连接成一个范围，中间插入指定的分隔符。
    - 例如：`auto joined = ranges | std::views::join_with(", ");`

### 动作（Actions）

动作是立即执行的操作，通常用于将视图的结果转换为具体的容器或值。

1. **`std::ranges::to`**：
    
    - 将视图转换为指定的容器类型。
    - 例如：`auto vec = range | std::ranges::to<std::vector<int>>;`
2. **`std::ranges::copy`**：
    
    - 将范围中的元素复制到另一个范围或容器中。
    - 例如：`std::ranges::copy(vec, std::back_inserter(result));`

### 算法（Algorithms）

`std::ranges` 提供了许多与 `std::algorithm` 相同的算法，但它们可以更自然地与视图一起使用。

1. **`std::ranges::for_each`**：
    
    - 对范围中的每个元素应用一个函数。
    - 例如：`std::ranges::for_each(vec, [](int i) { std::cout << i << ' '; });`
2. **`std::ranges::sort`**：
    
    - 对范围中的元素进行排序。
    - 例如：`std::ranges::sort(vec);`
3. **`std::ranges::find`**：
    
    - 在范围内查找指定的元素。
    - 例如：`auto it = std::ranges::find(vec, 42);`
4. **`std::ranges::count`**：
    
    - 计算范围内满足某个条件的元素数量。
    - 例如：`auto count = std::ranges::count(vec, 42);`
5. **`std::ranges::accumulate`**：
    
    - 计算范围内元素的累积和。
    - 例如：`int sum = std::ranges::accumulate(vec, 0);`

### 示例代码

以下是一个综合示例，展示了如何使用 `std::ranges` 进行多种操作：

cpp

深色版本

```
#include <iostream>
#include <ranges>
#include <vector>
#include <string>

int main() {
    std::vector<int> vec = {1, 2, 3, 4, 5, 6, 7, 8, 9, 10};

    // 过滤出偶数
    auto even = vec | std::views::filter([](int i) { return i % 2 == 0; });

    // 对偶数求平方
    auto squares = even | std::views::transform([](int i) { return i * i; });

    // 取前三个平方数
    auto first_three_squares = squares | std::views::take(3);

    // 将结果转换为 vector
    auto result = first_three_squares | std::ranges::to<std::vector<int>>;

    // 输出结果
    for (const auto& num : result) {
        std::cout << num << ' ';
    }
    std::cout << std::endl;

    return 0;
}
```

### 输出结果

深色版本

```
4 16 36
```

# 分隔
```c++
#pragma once
#include <iostream>
#include <cstdarg>
#include <vector>
#include<string>
#include<ranges>
using namespace std;
class ThreePoint
{
public:
    static void fun(const char* fmt, ...) {
        va_list args;
        va_start(args, fmt);

        std::string curr(fmt);
        if (curr != "") {
            //移除前导%
            while (curr.find_first_of("%") == 0) {
                curr = curr.substr(1);
            }
            auto view = std::ranges::views::split(curr, '%');
            int pos = 1;

            for (auto&& it : view) {
                
                string tag(it.begin(),it.end());
                if (tag == "cs") {
                    const char* value = va_arg(args, const char*);
                    cout << "Setting cs at position " << pos << ": " << value << endl;
                }
                else if (tag == "s") {
                    const char* value = va_arg(args, const char*);
                    cout << "Setting s at position " << pos << ": " << value << endl;
                }
                else if (tag == "i") {
                    int value = va_arg(args, int);
                    cout << "Setting i at position " << pos << ": " << value << endl;
                }
                else if (tag == "ui") {
                    unsigned int value = va_arg(args, unsigned int);
                    cout << "Setting ui at position " << pos << ": " << value << endl;
                }
                else if (tag == "ll") {
                    long long value = va_arg(args, long long);
                    cout << "Setting ll at position " << pos << ": " << value << endl;
                }
                else if (tag == "ull") {
                    unsigned long long value = va_arg(args, unsigned long long);
                    cout << "Setting ull at position " << pos << ": " << value << endl;
                }
                else if (tag == "bi") {
                    const char* value = va_arg(args, const char*);
                    cout << "Setting bi at position " << pos << ": " << value << endl;
                }
                else if (tag == "nul") {
                    int value = va_arg(args, int);
                    cout << "Setting nul at position " << pos << ": " << value << endl;
                }
                else if (tag == "c") {
                    char value = va_arg(args, char);
                    cout << "Setting c at position " << pos << ": " << value << endl;
                }
                else if (tag == "b") {
                    bool value = va_arg(args, int); // 注意：va_arg 不能直接处理 bool，这里用 int 替代
                    cout << "Setting b at position " << pos << ": " << value << endl;
                }
                else if (tag == "d") {
                    double value = va_arg(args, double);
                    cout << "Setting d at position " << pos << ": " << value << endl;
                }
                else if (tag == "dt") {
                    const char* value = va_arg(args, const char*);
                    cout << "Setting dt at position " << pos << ": " << value << endl;
                }
                else {
                    cout << "Unknown tag: " << tag << endl;
                }
                pos++;
                
            }
        }

        va_end(args);
        //delete[] strs;
    }
    static void test() {
        string fmt = "%s%c%ll%d%lf";
        if(fmt.find_first_of("%")==0)fmt = fmt.substr(1);
        vector<string> v;
        auto view = std::ranges::views::split(fmt, '%');
        for (auto&& item : view) {
            v.emplace_back(item.begin(), item.end());
        }
        cout << v.size() << endl;
    }
};

```
### 总结

- **视图**：提供懒惰的、可组合的操作，如 `filter`、`transform`、`take`、`drop`、`reverse`、`split` 等。
- **动作**：提供立即执行的操作，如 `to`、`copy` 等。
- **算法**：提供与 `std::algorithm` 相同的算法，但更自然地与视图一起使用，如 `for_each`、`sort`、`find`、`count`、`accumulate` 等。

通过这些功能，`std::ranges` 使得处理和操作范围变得更加简洁和高效