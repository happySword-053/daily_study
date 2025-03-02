`   new` 和 `malloc` 都用于在程序运行时动态分配内存，但它们属于不同的编程范式（`new` 是 C++ 的运算符，`malloc` 是 C 语言标准库函数），在使用方式、功能特性等方面存在诸多区别，以下是详细介绍：

### 1. 语法和使用方式

  

- **`new`**：`new` 是 C++ 中的运算符，使用时直接指定数据类型，编译器会自动计算所需内存大小。它可以用于分配单个对象或数组。

  

收起

cpp

```
// 分配单个对象
int* ptr1 = new int;
*ptr1 = 10;

// 分配数组
int* ptr2 = new int[5];
for (int i = 0; i < 5; ++i) {
    ptr2[i] = i;
}
```

  

- **`malloc`**：`malloc` 是 C 语言标准库中的函数，需要包含 `<cstdlib>` 头文件（C++ 中）或 `<stdlib.h>` 头文件（C 语言中）。使用时需要手动指定要分配的内存字节数。

  

收起

cpp

```
#include <cstdlib>

// 分配单个对象
int* ptr3 = (int*)malloc(sizeof(int));
if (ptr3 != nullptr) {
    *ptr3 = 10;
}

// 分配数组
int* ptr4 = (int*)malloc(5 * sizeof(int));
if (ptr4 != nullptr) {
    for (int i = 0; i < 5; ++i) {
        ptr4[i] = i;
    }
}
```

### 2. 内存分配的类型安全性

  

- **`new`**：`new` 是类型安全的，因为它会根据指定的数据类型自动计算所需内存大小，并且返回的指针类型与所分配对象的类型匹配，无需进行显式的类型转换。

  

收起

cpp

```
std::string* strPtr = new std::string("Hello");
```

  

- **`malloc`**：`malloc` 不是类型安全的，它返回的是 `void*` 类型的指针，需要手动将其转换为所需的指针类型。

  

收起

cpp

```
std::string* strPtr = (std::string*)malloc(sizeof(std::string));
```

### 3. 内存初始化

  

- **`new`**：对于自定义类型（类、结构体），`new` 会调用相应的构造函数进行对象的初始化。对于内置类型，如果使用 `new` 分配单个对象，可以进行值初始化；如果分配数组，默认情况下不会进行初始化，但可以使用初始化列表进行初始化。

  

收起

cpp

```
// 自定义类型，调用构造函数
class MyClass {
public:
    MyClass() { std::cout << "Constructor called" << std::endl; }
};
MyClass* objPtr = new MyClass;

// 内置类型，值初始化
int* numPtr = new int(5);

// 内置类型数组，使用初始化列表
int* arrPtr = new int[3]{1, 2, 3};
```

  

- **`malloc`**：`malloc` 只是分配指定大小的内存块，不会对内存进行初始化，分配的内存中包含的是未定义的值。

  

收起

cpp

```
int* numPtr = (int*)malloc(sizeof(int));
// *numPtr 的值是未定义的
```

### 4. 内存释放

  

- **`new`**：使用 `new` 分配的内存需要使用 `delete`（单个对象）或 `delete[]`（数组）来释放。`delete` 会调用对象的析构函数，确保对象的资源被正确清理。

  

收起

cpp

```
// 释放单个对象
int* ptr1 = new int;
delete ptr1;

// 释放数组
int* ptr2 = new int[5];
delete[] ptr2;
```

  

- **`malloc`**：使用 `malloc` 分配的内存需要使用 `free` 函数来释放，`free` 不会调用对象的析构函数，只是简单地将内存标记为可用。

  

收起

cpp

```
int* ptr3 = (int*)malloc(sizeof(int));
free(ptr3);

int* ptr4 = (int*)malloc(5 * sizeof(int));
free(ptr4);
```

### 5. 异常处理

  

- **`new`**：当 `new` 无法分配所需内存时，会抛出 `std::bad_alloc` 异常。可以使用 `try-catch` 块来捕获并处理该异常。

  

收起

cpp

```
try {
    int* largeArray = new int[1000000000];
} catch (const std::bad_alloc& e) {
    std::cerr << "Memory allocation failed: " << e.what() << std::endl;
}
```

  

- **`malloc`**：`malloc` 在内存分配失败时会返回 `nullptr`，需要手动检查返回值来判断内存分配是否成功。

  

收起

cpp

```
int* largeArray = (int*)malloc(1000000000 * sizeof(int));
if (largeArray == nullptr) {
    std::cerr << "Memory allocation failed" << std::endl;
}
```

### 6. 可重载性

  

- **`new`**：在 C++ 中，`new` 和 `delete` 运算符可以被重载，允许开发者自定义内存分配和释放的行为。这在一些特定的场景下，如内存池管理、性能优化等方面非常有用。

  

收起

cpp

```
class MyClass {
public:
    static void* operator new(size_t size) {
        std::cout << "Custom new operator called" << std::endl;
        return ::operator new(size);
    }
    static void operator delete(void* ptr) {
        std::cout << "Custom delete operator called" << std::endl;
        ::operator delete(ptr);
    }
};

MyClass* obj = new MyClass;
delete obj;
```

  

- **`malloc`**：`malloc` 是一个标准库函数，不能被重载。

  

综上所述，`new` 是 C++ 中更高级、更安全的内存分配方式，尤其适用于处理自定义类型的对象；而 `malloc` 是 C 语言中的内存分配函数，在 C++ 中仍然可以使用，主要用于与 C 代码的兼容性或对内存分配有特殊需求的场景。