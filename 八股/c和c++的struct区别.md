在 C 和 C++ 中，`struct` 都用于定义用户自定义的数据类型，但它们之间存在一些显著的区别，下面从多个方面进行详细介绍：

### 1. 默认访问权限

  

- **C 语言**：在 C 语言里，`struct` 中的成员默认访问权限都是公共的（public），所有外部代码都能直接访问结构体的成员。

  

收起

c

```
#include <stdio.h>

// 定义一个结构体
struct Person {
    char name[20];
    int age;
};

int main() {
    struct Person p;
    // 直接访问结构体成员
    p.age = 25; 
    printf("Age: %d\n", p.age);
    return 0;
}
```

  

- **C++**：在 C++ 中，`struct` 同样默认成员访问权限为公共的，但 C++ 的 `struct` 更像是一个轻量级的类，它支持类的一些特性，例如可以设置不同的访问权限（`private`、`protected`、`public`）。

  

收起

cpp

```
#include <iostream>
#include <string>

// 定义一个结构体
struct Person {
private:
    std::string name;
public:
    int age;
    void setName(const std::string& n) {
        name = n;
    }
    std::string getName() {
        return name;
    }
};

int main() {
    Person p;
    p.age = 25;
    p.setName("John");
    std::cout << "Name: " << p.getName() << ", Age: " << p.age << std::endl;
    return 0;
}
```

### 2. 类型定义

  

- **C 语言**：在 C 语言中，使用 `struct` 定义类型后，声明变量时必须带上 `struct` 关键字。

  

收起

c

```
#include <stdio.h>

// 定义一个结构体
struct Point {
    int x;
    int y;
};

int main() {
    // 声明变量时需要带上 struct 关键字
    struct Point p; 
    p.x = 10;
    p.y = 20;
    printf("Point: (%d, %d)\n", p.x, p.y);
    return 0;
}
```

  

- **C++**：在 C++ 中，使用 `struct` 定义类型后，声明变量时可以省略 `struct` 关键字。

  

收起

cpp

```
#include <iostream>

// 定义一个结构体
struct Point {
    int x;
    int y;
};

int main() {
    // 声明变量时可以省略 struct 关键字
    Point p; 
    p.x = 10;
    p.y = 20;
    std::cout << "Point: (" << p.x << ", " << p.y << ")" << std::endl;
    return 0;
}
```

### 3. 构造函数和析构函数

  

- **C 语言**：C 语言中的 `struct` 不支持构造函数和析构函数。构造函数是用于初始化对象的特殊成员函数，析构函数则用于在对象销毁时进行资源清理。C 语言只能通过自定义的初始化函数来对结构体进行初始化。

  

收起

c

```
#include <stdio.h>
#include <string.h>

// 定义一个结构体
struct Book {
    char title[50];
    float price;
};

// 自定义初始化函数
void initBook(struct Book* b, const char* t, float p) {
    strcpy(b->title, t);
    b->price = p;
}

int main() {
    struct Book myBook;
    initBook(&myBook, "C Programming", 29.99);
    printf("Book: %s, Price: %.2f\n", myBook.title, myBook.price);
    return 0;
}
```

  

- **C++**：C++ 的 `struct` 支持构造函数和析构函数，和类的使用方式相同。构造函数可以在创建结构体对象时自动进行初始化，析构函数可以在对象销毁时进行资源释放。

  

收起

cpp

```
#include <iostream>
#include <string>

// 定义一个结构体
struct Book {
    std::string title;
    float price;

    // 构造函数
    Book(const std::string& t, float p) : title(t), price(p) {}

    // 析构函数
    ~Book() {
        std::cout << "Book object destroyed." << std::endl;
    }
};

int main() {
    Book myBook("C++ Programming", 39.99);
    std::cout << "Book: " << myBook.title << ", Price: " << myBook.price << std::endl;
    return 0;
}
```

### 4. 成员函数

  

- **C 语言**：C 语言中的 `struct` 只能包含数据成员，不能包含成员函数。如果需要对结构体的数据进行操作，需要定义独立的函数，并将结构体指针作为参数传递给这些函数。

  

收起

c

```
#include <stdio.h>

// 定义一个结构体
struct Rectangle {
    int width;
    int height;
};

// 独立函数计算矩形面积
int calculateArea(struct Rectangle* r) {
    return r->width * r->height;
}

int main() {
    struct Rectangle rect = {5, 10};
    int area = calculateArea(&rect);
    printf("Rectangle area: %d\n", area);
    return 0;
}
```

  

- **C++**：C++ 的 `struct` 可以包含成员函数，这些成员函数可以直接访问结构体的成员变量，使用起来更加方便。

  

收起

cpp

```
#include <iostream>

// 定义一个结构体
struct Rectangle {
    int width;
    int height;

    // 成员函数计算矩形面积
    int calculateArea() {
        return width * height;
    }
};

int main() {
    Rectangle rect = {5, 10};
    int area = rect.calculateArea();
    std::cout << "Rectangle area: " << area << std::endl;
    return 0;
}
```

### 5. 继承

  

- **C 语言**：C 语言中的 `struct` 不支持继承机制。继承是面向对象编程的重要特性，允许一个结构体从另一个结构体派生，从而复用已有结构体的成员和功能。
- **C++**：C++ 的 `struct` 支持继承，可以从其他 `struct` 或类派生新的 `struct`，派生的 `struct` 可以继承基类的成员和方法。

  

收起

cpp

```
#include <iostream>

// 基结构体
struct Shape {
    int x;
    int y;
    void move(int dx, int dy) {
        x += dx;
        y += dy;
    }
};

// 派生结构体
struct Rectangle : public Shape {
    int width;
    int height;
    int area() {
        return width * height;
    }
};

int main() {
    Rectangle rect;
    rect.x = 0;
    rect.y = 0;
    rect.width = 5;
    rect.height = 10;
    rect.move(2, 3);
    std::cout << "Rectangle position: (" << rect.x << ", " << rect.y << "), Area: " << rect.area() << std::endl;
    return 0;
}
```

  

综上所述，C++ 的 `struct` 比 C 语言的 `struct` 功能更强大，它融合了类的很多特性，使得代码的组织和复用更加灵活方便。

分享

C++中struct的用法示例

在C语言中如何使用struct？

对比C和C++中struct在继承方面的区别