

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