### `std::shared_ptr` 的基本构成

一个 `std::shared_ptr` 对象在逻辑上和概念上包含了两个主要部分：

1. **指向被管理对象的指针 (`T* ptr_to_object`)：**
    
    - 这是一个普通的裸指针，指向 `shared_ptr` 所“拥有”的实际数据（即你通过 `new` 或 `make_shared` 分配的那个对象）。
        
2. **指向控制块的指针 (`ControlBlock* ptr_to_control_block`)：**
    
    - 这是一个内部指针，指向一个独立分配的、维护管理信息的**控制块 (Control Block)**。所有共享同一个对象的 `shared_ptr` 实例都会指向同一个控制块。
        

因此，每个 `std::shared_ptr` 对象本身的大小通常是**两个指针的大小**（在 64 位系统上是 16 字节）。

---

### 控制块 (Control Block) 的内部结构

控制块是 `std::shared_ptr` 实现其复杂生命周期管理的核心。它通常包含以下几个关键成员：

1. **强引用计数 (`long strong_count`)：**
    
    - 这是一个整数，记录了当前有多少个 `std::shared_ptr` 实例正在“强引用”这个对象。
        
    - 每次一个新的 `shared_ptr` 复制或构造时，它会**增加**。
        
    - 每次一个 `shared_ptr` 销毁或重置时，它会**减少**。
        
    - 当 `strong_count` 归零时，表示没有 `shared_ptr` 再拥有这个对象了，此时会**调用被管理对象的析构函数并释放对象内存**。
        
2. **弱引用计数 (`long weak_count`)：**
    
    - 这是一个整数，记录了当前有多少个 `std::weak_ptr` 实例正在“弱引用”这个对象。
        
    - 每次一个新的 `weak_ptr` 构造或复制时，它会**增加**。
        
    - 每次一个 `weak_ptr` 销毁或重置时，它会**减少**。
        
    - **即使 `strong_count` 归零，只要 `weak_count` 不为零，控制块就不会被销毁。** 这样 `weak_ptr` 仍然可以检查对象是否已不存在（通过 `expired()`）。只有当 `strong_count` 和 `weak_count` 都归零时，控制块才会被彻底释放。
        
3. **自定义删除器 (Optional Deleter)：**
    
    - 如果你在创建 `shared_ptr` 时提供了自定义删除器（例如，管理 `FILE*` 或数组），那么这个删除器会存储在控制块中。
        
    - 当强引用计数归零时，`shared_ptr` 不会简单地调用 `delete`，而是会调用这个自定义删除器来释放资源。
        
4. **自定义分配器 (Optional Allocator)：**
    
    - 如果你使用了自定义内存分配器来创建对象和控制块，那么这个分配器也会被存储在控制块中，以便在对象和控制块销毁时使用相同的分配器来释放内存。
        
5. **（可能包含）原始对象本身的存储空间：**
    
    - **这是最重要的一点，也是 `std::make_shared` 优于 `new` 的原因所在。**
        
    - 当你使用 `std::make_shared<T>()` 创建 `shared_ptr` 时，`make_shared` 会执行**一次性内存分配**，为**控制块和对象 `T` 本身**分配一块连续的内存。在这种情况下，控制块实际上包含了对象 `T` 的实际存储空间。
        
    - 当你使用 `std::shared_ptr<T> p(new T())` 创建时，`new T()` 会先分配一块内存给对象 `T`，然后 `shared_ptr` 内部会**再次分配一块独立的内存**来存储控制块。此时，控制块中会有一个额外的指针，指向单独分配的 `T` 对象。
6. **`shared_ptr` 对象本身是 RAII 包装器**：
    
    - ✅ **正确**：`shared_ptr` 是**栈对象**（或类的成员），其析构函数自动管理资源释放（RAII）。
        
    - ❌ **误解**：`shared_ptr` 的生命周期**不依赖控制块**，而是**控制块的释放依赖 `shared_ptr` 的析构逻辑**。
        
7. **控制块的创建位置**：
    
    - ✅ **正确**：控制块在**堆上创建**（`make_shared` 或 `new` 时分配）。
        
    - ❌ **误解**：`shared_ptr` 对象自身（如 `std::shared_ptr<int> p;`）是**栈上的轻量级句柄**，仅包含两个指针（指向对象和控制块）。
# 潜在问题
#### 循环引用
```c++
#include <iostream>
#include <memory>

class CB;
class CA {
public:
    CA() {
        std::cout << "CA()" << std::endl;
    }
    ~CA() {
        std::cout << "~CA()" << std::endl;
    }
    void set_ptr(std::shared_ptr<CB>& ptr) {
        m_ptr_b = ptr;
    }
private:
    std::shared_ptr<CB> m_ptr_b;
};

class CB {
public:
    CB() {
        std::cout << "CB()" << std::endl;
    }
    ~CB() {
        std::cout << "~CB()" << std::endl;
    }
    void set_ptr(std::shared_ptr<CA>& ptr) {
        m_ptr_a = ptr;
    }
private:
    std::shared_ptr<CA> m_ptr_a;
};

int main()
{
    std::shared_ptr<CA> ptr_a(new CA());
    std::shared_ptr<CB> ptr_b(new CB());
    ptr_a->set_ptr(ptr_b);
    ptr_b->set_ptr(ptr_a);
    std::cout << ptr_a.use_count() << " " << ptr_b.use_count() << std::endl;

    return 0;
}

```
运行结果
```
yngzmiao@yngzmiao-virtual-machine:~/test$ ./main 
CA()
CB()
2 2

```
并没有调用析构函数