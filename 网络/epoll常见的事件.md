

- **`EPOLLIN`**：表示对应的文件描述符可读（包括对端关闭的情况）。
- **`EPOLLOUT`**：表示对应的文件描述符可写。
- **`EPOLLPRI`**：表示有紧急数据可读（例如，带外数据）。
- **`EPOLLERR`**：表示发生错误条件。
- **`EPOLLHUP`**：表示挂起（hang up），通常表示连接被异常关闭或文件描述符不再可用。
- **`EPOLLET`**：设置边缘触发模式（Edge Triggered）。默认情况下，`epoll` 使用水平触发（Level Triggered）模式。
- **`EPOLLONESHOT`**：只监听一次事件，当事件触发后会自动移除该文件描述符的监控。
- **`EPOLLRDHUP`**：当客户端关闭连接（半关闭，比如调用了 `shutdown(sock, SHUT_WR)`）时，服务端的 `epoll` 会触发 `EPOLLRDHUP` 事件。这可以用来检测远端是否关闭了写端或断开了连接。半连接就是，客户端的套接字无法再写，但是能读