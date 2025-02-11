### 1. 管道（Pipe）

管道是一种半双工的通信方式，数据只能在一个方向上流动，通常用于有亲缘关系的进程之间（如父子进程）。

**示例代码**：



```cpp
#include <iostream>
#include <unistd.h>
#include <cstring>

int main() {
    int pipefd[2];
    pid_t pid;
    char buffer[100];

    // 创建管道
    if (pipe(pipefd) == -1) {
        perror("pipe");
        return 1;
    }

    // 创建子进程
    pid = fork();
    if (pid == -1) {
        perror("fork");
        return 1;
    }

    if (pid == 0) {
        // 子进程：关闭写端
        close(pipefd[1]);
        // 从管道读取数据
        ssize_t n = read(pipefd[0], buffer, sizeof(buffer));
        if (n == -1) {
            perror("read");
            return 1;
        }
        std::cout << "Child process received: " << buffer << std::endl;
        // 关闭读端
        close(pipefd[0]);
    } else {
        // 父进程：关闭读端
        close(pipefd[0]);
        const char* message = "Hello, child process!";
        // 向管道写入数据
        ssize_t n = write(pipefd[1], message, strlen(message));
        if (n == -1) {
            perror("write");
            return 1;
        }
        // 关闭写端
        close(pipefd[1]);
    }

    return 0;
}
```
父子进程可以在一定程度上同时进行读写操作，但由于管道本身是半双工的特性，同一时刻数据只能在一个方向流动，下面具体分析：

### 理论上的并发情况

从进程调度的角度来看，操作系统会以时间片轮转等方式来调度各个进程执行。这意味着父进程和子进程的读写操作在宏观上可能看起来是 “同时” 进行的。例如，父进程可能在一个时间片内尝试向管道写入数据，而子进程在另一个时间片内尝试从管道读取数据。

### 管道半双工特性的限制

虽然进程调度可以让父子进程的读写操作交替进行，但管道的半双工特性决定了同一时刻数据只能从写端流向读端，不能双向同时传输。具体情况如下：

#### 写操作阻塞

当父进程正在向管道写入数据时，如果此时子进程也尝试向管道写入数据，由于管道缓冲区空间有限，后一个写操作可能会被阻塞，直到管道缓冲区有足够的空间或者前面的写操作完成。例如，当管道缓冲区已满时，新的写操作会被阻塞，直到有进程从管道读取数据释放出空间。

#### 读操作依赖数据

当子进程尝试从管道读取数据时，如果管道缓冲区为空，读操作会被阻塞，直到有进程（如父进程）向管道写入数据。同样，父进程的读操作也遵循这个规则。

### 示例代码分析



```cpp
#include <iostream>
#include <unistd.h>
#include <cstring>

int main() {
    int pipefd[2];
    pid_t pid;
    char buffer[100];

    // 创建管道
    if (pipe(pipefd) == -1) {
        perror("pipe");
        return 1;
    }

    // 创建子进程
    pid = fork();
    if (pid == -1) {
        perror("fork");
        return 1;
    }

    if (pid == 0) {
        // 子进程关闭写端
        close(pipefd[1]);
        // 从管道读取数据
        ssize_t n = read(pipefd[0], buffer, sizeof(buffer));
        if (n == -1) {
            perror("read");
            return 1;
        }
        std::cout << "Child process received: " << buffer << std::endl;
        // 关闭读端
        close(pipefd[0]);
    } else {
        // 父进程关闭读端
        close(pipefd[0]);
        const char* message = "Hello, child process!";
        // 向管道写入数据
        ssize_t n = write(pipefd[1], message, strlen(message));
        if (n == -1) {
            perror("write");
            return 1;
        }
        // 关闭写端
        close(pipefd[1]);
    }

    return 0;
}
```

在这个示例中，父进程负责向管道写入数据，子进程负责从管道读取数据。虽然父进程和子进程的操作可能在不同的时间片内执行，但数据的流向是单向的，先由父进程写入，然后子进程才能读取，不能同时进行双向的数据传输。

### 实现全双工通信的方法

如果需要实现父子进程之间的全双工通信（即同时进行双向数据传输），可以创建两个管道，一个用于父进程向子进程发送数据，另一个用于子进程向父进程发送数据。示例代码如下：


```cpp

#include <iostream>
#include <unistd.h>
#include <cstring>

int main() {
    int pipe1[2], pipe2[2];
    pid_t pid;
    char buffer[100];

    // 创建两个管道
    if (pipe(pipe1) == -1 || pipe(pipe2) == -1) {
        perror("pipe");
        return 1;
    }

    // 创建子进程
    pid = fork();
    if (pid == -1) {
        perror("fork");
        return 1;
    }

    if (pid == 0) {
        // 子进程关闭 pipe1 的写端和 pipe2 的读端
        close(pipe1[1]);
        close(pipe2[0]);

        // 子进程从 pipe1 读取数据
        ssize_t read_n = read(pipe1[0], buffer, sizeof(buffer));
        if (read_n == -1) {
            perror("read");
            return 1;
        }
        buffer[read_n] = '\0';
        std::cout << "Child received from parent: " << buffer << std::endl;

        // 子进程向 pipe2 写入数据
        const char* child_msg = "Message from child";
        ssize_t n = write(pipe2[1], child_msg, strlen(child_msg));
        if (n == -1) {
            perror("write");
            return 1;
        }

        // 关闭剩余的文件描述符
        close(pipe1[0]);
        close(pipe2[1]);
    } else {
        // 父进程关闭 pipe1 的读端和 pipe2 的写端
        close(pipe1[0]);
        close(pipe2[1]);

        // 父进程向 pipe1 写入数据
        const char* parent_msg = "Message from parent";
        ssize_t n = write(pipe1[1], parent_msg, strlen(parent_msg));
        if (n == -1) {
            perror("write");
            return 1;
        }

        // 父进程从 pipe2 读取数据
        ssize_t read_n = read(pipe2[0], buffer, sizeof(buffer));
        if (read_n == -1) {
            perror("read");
            return 1;
        }
        buffer[read_n] = '\0';
        std::cout << "Parent received from child: " << buffer << std::endl;

        // 关闭剩余的文件描述符
        close(pipe1[1]);
        close(pipe2[0]);
    }

    return 0;
}
```

通过使用两个管道，父子进程可以在不同的管道上同时进行读写操作，从而实现全双工通信。