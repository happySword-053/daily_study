`constexpr` 和 `const` 都可以用来声明常量，但它们之间有一些重要的区别。让我们详细解释一下这两者的主要区别和应用场景。

### 1. 编译时常量 vs 运行时常量

- **`const`**：声明的变量是运行时常量。它的值可以在编译时确定，也可以在运行时确定。一旦初始化后，就不能再修改。
- **`constexpr`**：声明的变量是编译时常量。它的值必须在编译时确定，并且可以在编译时使用。

### 2. 初始化时机

- **`const`**：可以在编译时或运行时初始化。
- **`constexpr`**：必须在编译时初始化。

### 3. 使用范围

- **`const`**：可以用于需要运行时常量的上下文。
- **`constexpr`**：可以用于需要编译时常量的上下文，例如数组大小、模板参数等。

### 4. 函数和构造函数

- **`const`**：不能用于声明编译时可计算的函数或构造函数。
- **`constexpr`**：可以用于声明编译时可计算的函数或构造函数。

### 示例代码

#### `const` 示例

cpp

深色版本

```c++
const int a = 5; // 编译时常量
const int b = some_runtime_function(); // 运行时常量

int main() {
    const int c = 10; // 运行时常量
    // c = 20; // 错误：c 是常量，不能修改
    return 0;
}
```

#### `constexpr` 示例

cpp

深色版本

```c++
constexpr int a = 5; // 编译时常量

constexpr int add(int x, int y) {
    return x + y;
}

int main() {
    constexpr int b = add(3, 4); // b 在编译时计算为 7
    // b = 20; // 错误：b 是常量，不能修改

    constexpr int size = 10;
    int arr[size]; // 正确：size 是编译时常量

    return 0;
}
```

### 详细解释

#### `const` 的特性

- **编译时常量或运行时常量**：`const` 变量可以是编译时常量，也可以是运行时常量，取决于初始化的方式。
- **初始化时机**：可以在编译时或运行时初始化。
- **使用范围**：主要用于需要运行时常量的上下文。

#### `constexpr` 的特性

- **编译时常量**：`constexpr` 变量必须是编译时常量，其值必须在编译时确定。
- **初始化时机**：必须在编译时初始化。
- **使用范围**：可以用于需要编译时常量的上下文，如数组大小、模板参数等。
- **函数和构造函数**：可以用于声明编译时可计算的函数或构造函数。

### 示例对比

#### 数组大小

cpp

深色版本

```c++
const int size1 = 10;
int arr1[size1]; // 正确：size1 是运行时常量

constexpr int size2 = 10;
int arr2[size2]; // 正确：size2 是编译时常量
```

#### 模板参数

cpp

深色版本

```c++
template<int N>
class Array {
public:
    int data[N];
};

const int size1 = 10;
Array<size1> arr1; // 错误：size1 是运行时常量

constexpr int size2 = 10;
Array<size2> arr2; // 正确：size2 是编译时常量
```

### 总结

- **`const`**：声明运行时常量，可以在编译时或运行时初始化，主要用于需要运行时常量的上下文。
- **`constexpr`**：声明编译时常量，必须在编译时初始化，可以用于需要编译时常量的上下文，如数组大小、模板参数等，还可以用于声明编译时可计算的函数或构造函数。