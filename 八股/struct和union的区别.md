`struct`（结构体）和 `union`（联合体，也称为共用体）是 C 和 C++ 中用于组织数据的两种不同构造，它们有以下多方面的区别：

### 1. 内存布局

  

- **`struct`**：结构体的成员在内存中是依次存储的，每个成员都有自己独立的内存空间。结构体的总大小是其所有成员大小之和，并且可能会有内存对齐带来的额外填充字节，以满足特定硬件平台对数据存储的要求。

  

收起

c

```
#include <stdio.h>

// 定义一个结构体
struct ExampleStruct {
    char c;    // 1 字节
    int i;     // 4 字节
    double d;  // 8 字节
};

int main() {
    struct ExampleStruct s;
    printf("Size of struct ExampleStruct: %zu bytes\n", sizeof(s));
    return 0;
}
```

  

在这个例子中，`struct ExampleStruct` 的总大小通常是 16 字节（考虑内存对齐），因为 `char` 占 1 字节，`int` 占 4 字节，`double` 占 8 字节，为了保证 `double` 成员的地址是 8 字节对齐的，编译器会在 `char` 和 `int` 后面添加填充字节。

  

- **`union`**：联合体的所有成员共享同一块内存空间，联合体的大小取决于其最大成员的大小。在同一时间，联合体只能存储一个成员的值。

  

收起

c

```
#include <stdio.h>

// 定义一个联合体
union ExampleUnion {
    char c;    // 1 字节
    int i;     // 4 字节
    double d;  // 8 字节
};

int main() {
    union ExampleUnion u;
    printf("Size of union ExampleUnion: %zu bytes\n", sizeof(u));
    return 0;
}
```

  

在这个例子中，`union ExampleUnion` 的大小是 8 字节，因为 `double` 是最大的成员，所有成员都共享这 8 字节的内存空间。

### 2. 成员访问

  

- **`struct`**：结构体的各个成员可以同时被访问和修改，每个成员都有自己独立的内存地址。通过成员访问运算符 `.`（对于结构体变量）或 `->`（对于结构体指针）来访问结构体的成员。

  

收起

c

```
#include <stdio.h>

struct Point {
    int x;
    int y;
};

int main() {
    struct Point p;
    p.x = 10;
    p.y = 20;
    printf("Point: (%d, %d)\n", p.x, p.y);
    return 0;
}
```

  

- **`union`**：联合体同一时间只能访问一个成员。当给一个成员赋值后，其他成员的值会被覆盖。

  

收起

c

```
#include <stdio.h>

union Data {
    int i;
    float f;
};

int main() {
    union Data data;
    data.i = 10;
    printf("data.i: %d\n", data.i);
    data.f = 3.14f;
    // 此时 data.i 的值已经被覆盖，是无意义的
    printf("data.f: %f, data.i: %d\n", data.f, data.i);
    return 0;
}
```

### 3. 使用场景

  

- **`struct`**：结构体适用于需要同时存储多个不同类型数据的场景，例如表示一个包含姓名、年龄、地址等信息的人员记录。

  

收起

c

```
#include <stdio.h>
#include <string.h>

struct Person {
    char name[50];
    int age;
    char address[100];
};

int main() {
    struct Person p;
    strcpy(p.name, "John");
    p.age = 30;
    strcpy(p.address, "123 Main St");
    printf("Name: %s, Age: %d, Address: %s\n", p.name, p.age, p.address);
    return 0;
}
```

  

- **`union`**：联合体适用于需要在不同类型的数据之间共享内存，以节省内存空间的场景。例如，在解析不同类型的数据时，数据可能以不同的格式存储，但在同一时间只使用一种格式。

  

收起

c

```
#include <stdio.h>

union Packet {
    int integerValue;
    float floatValue;
    char stringValue[10];
};

// 模拟解析不同类型的数据包
void parsePacket(union Packet p) {
    // 假设根据某种标志判断数据类型
    // 这里简单示例，假设先存储的是整数
    printf("Integer value: %d\n", p.integerValue);
}

int main() {
    union Packet packet;
    packet.integerValue = 123;
    parsePacket(packet);
    return 0;
}
```

### 4. 初始化

  

- **`struct`**：结构体可以同时初始化多个成员，使用花括号 `{}` 按成员定义的顺序依次列出初始化值。

  

收起

c

```
#include <stdio.h>

struct Date {
    int year;
    int month;
    int day;
};

int main() {
    struct Date d = {2024, 10, 15};
    printf("Date: %d-%d-%d\n", d.year, d.month, d.day);
    return 0;
}
```

  

- **`union`**：联合体只能初始化一个成员，同样使用花括号 `{}`，但只能指定一个初始值，该值会赋给联合体的第一个成员。

  

收起

c

```
#include <stdio.h>

union Value {
    int i;
    float f;
};

int main() {
    union Value v = {10};  // 初始化第一个成员 i
    printf("v.i: %d\n", v.i);
    return 0;
}
```

  

综上所述，`struct` 和 `union` 在内存布局、成员访问、使用场景和初始化等方面都存在明显的区别，开发者需要根据具体的需求来选择使用哪种数据结构。

分享

结构体和联合体在内存布局上的区别有什么优缺点？

在什么情况下使用结构体而不是类？

结构体和联合体的初始化方式有什么不同？