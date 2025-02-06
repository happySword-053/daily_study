# GDB 调试工具使用指南

GDB（GNU Debugger）是一个强大的调试工具，常用于调试 C、C++ 等程序。以下是常用的 GDB 指令及其详细说明和示例。

---

## 1. 启动和退出

### 启动 GDB



```bash
gdb ./my_program
```

启动 GDB 并加载可执行文件 `my_program`。

### 退出 GDB



```
(gdb) quit
```

或简写：



```
(gdb) q
```

---

## 2. 运行程序

### 运行程序


```
(gdb) run
```

或简写：


```
(gdb) r
```

### 带参数运行

如果你的程序需要命令行参数，可以这样运行：



```
(gdb) run arg1 arg2
```

---

## 3. 断点管理

### 设置断点

- 在函数 `main` 处设置断点：
    
    
    
    ```bash
    (gdb) break main
```
    
    或简写：
    
    
    
    ```bash
(gdb) b main
```
    
- 在文件 `example.c` 的第 10 行设置断点：
    
    
    
    ```bash
    (gdb) break example.c:10
    
```

### 查看断点



```
(gdb) info breakpoints
```

或简写：



```
(gdb) i b
```

输出示例：



```
Num     Type           Disp Enb Address            What
1       breakpoint     keep y   0x000000000040113a in main at example.c:10
```
### 删除断点

删除编号为 1 的断点：



```
(gdb) delete 1
```

或简写：



```
(gdb) d 1
```

### 禁用/启用断点

- 禁用编号为 1 的断点：
    
    
    
    ```bash
(gdb) disable 1
```
    
- 启用编号为 1 的断点：
    
    
    
    ```bash
(gdb) enable 1
```
    

---

## 4. 单步执行

### 单步执行（进入函数）



(gdb) step

或简写：

bash

复制

(gdb) s

### 单步执行（不进入函数）

bash

复制

(gdb) next

或简写：

bash

复制

(gdb) n

### 继续执行

bash

复制

(gdb) continue

或简写：

bash

复制

(gdb) c

### 执行到当前函数返回

bash

复制

(gdb) finish

---

## 5. 查看代码

### 查看源代码

bash

复制

(gdb) list

或简写：

bash

复制

(gdb) l

### 查看特定行

查看第 10 行附近的代码：

bash

复制

(gdb) list 10

### 查看函数代码

查看函数 `main` 的代码：

bash

复制

(gdb) list main

---

## 6. 查看变量和内存

### 打印变量值

打印变量 `x` 的值：

bash

复制

(gdb) print x

或简写：

bash

复制

(gdb) p x

### 打印表达式

打印表达式 `x + y` 的值：

bash

复制

(gdb) print x + y

### 查看内存

查看地址 `0x7fffffffe0` 处的 4 个字（word），以 16 进制显示：

bash

复制

(gdb) x/4xw 0x7fffffffe0

---

## 7. 堆栈跟踪

### 查看堆栈

bash

复制

(gdb) backtrace

或简写：

bash

复制

(gdb) bt

### 切换到指定帧

切换到帧编号为 1 的帧：

bash

复制

(gdb) frame 1

或简写：

bash

复制

(gdb) f 1

---

## 8. 修改变量

### 修改变量值

将变量 `x` 的值改为 10：

bash

复制

(gdb) set var x = 10

---

## 9. 其他常用指令

### 查看寄存器

bash

复制

(gdb) info registers

或简写：

bash

复制

(gdb) i r

### 查看线程

bash

复制

(gdb) info threads

### 切换线程

切换到线程编号为 2 的线程：

bash

复制

(gdb) thread 2

### 查看帮助

查看 `print` 指令的帮助：

bash

复制

(gdb) help print

---

## 10. 调试多线程程序

### 查看所有线程

bash

复制

(gdb) info threads

### 切换到指定线程

切换到线程编号为 2 的线程：

bash

复制

(gdb) thread 2

### 在所有线程中执行命令

在所有线程中打印变量 `x` 的值：

bash

复制

(gdb) thread apply all print x

---

## 11. 调试核心转储文件

### 加载核心转储文件

bash

复制

gdb ./my_program core

### 查看崩溃位置

bash

复制

(gdb) bt

---

## 12. 调试优化代码

### 关闭优化

编译时使用 `-O0` 选项：

bash

复制

gcc -O0 -g -o my_program my_program.c

### 查看优化后的代码

查看函数 `main` 的反汇编代码：

bash

复制

(gdb) disassemble main

---

## 13. 调试动态链接库

### 查看加载的共享库

bash

复制

(gdb) info sharedlibrary

### 设置共享库断点

在库 `libc.so.6` 的 `malloc` 函数处设置断点：

bash

复制

(gdb) break libc.so.6:malloc

---

## 14. 调试信号

### 查看信号处理

bash

复制

(gdb) info signals

### 忽略信号

忽略 `SIGINT` 信号：

bash

复制

(gdb) handle SIGINT ignore

### 捕获信号

捕获 `SIGSEGV` 信号并暂停程序：

bash

复制

(gdb) handle SIGSEGV stop

---

## 15. 调试多进程程序

### 查看进程

bash

复制

(gdb) info inferiors

### 切换到指定进程

切换到进程编号为 2 的进程：

bash

复制

(gdb) inferior 2

### 分离进程

分离进程编号为 2 的进程：

bash

复制

(gdb) detach inferior 2