| 操作                | 调用示例                                                    | Handler 签名                                                        |
| ----------------- | ------------------------------------------------------- | ----------------------------------------------------------------- |
| **TCP 读取**        | `socket.async_read_some(buffer, handler);`              | `void(const error_code& ec, std::size_t bytes_transferred)`       |
| **TCP 写入**        | `boost::asio::async_write(socket, buffer, handler);`    | `void(const error_code& ec, std::size_t bytes_transferred)`       |
| **TCP 连接**        | `socket.async_connect(endpoint, handler);`              | `void(const error_code& ec)`                                      |
| **TCP 接收新连接**     | `acceptor.async_accept(socket, handler);`               | `void(const error_code& ec)`                                      |
| **域名解析（Resolve）** | `resolver.async_resolve(host, service, handler);`       | `void(const error_code& ec, tcp::resolver::results_type results)` |
| **定时器等待**         | `timer.async_wait(handler);`                            | `void(const error_code& ec)`                                      |
| **UDP 发送**        | `socket.async_send_to(buffer, endpoint, handler);`      | `void(const error_code& ec, std::size_t bytes_transferred)`       |
| **UDP 接收**        | `socket.async_receive_from(buffer, endpoint, handler);` | `void(const error_code& ec, std::size_t bytes_transferred)`       |
| **自定义异步操作**       | 使用 `async_compose`/`use_awaitable`                      | 完全可自定义，但必须满足 Boost.Asio 的完成签名规范                                   |


### 举个更形象的比喻

把 `io_context` 想象成一个**餐厅**：

- 你（应用）往厨房（内核）下单（`async_read_some`/`async_write`），注明“完成后请给我端菜”（handler）；
    
- `io_context.run()` 就是**服务员**：
    
    1. **等菜做好**（等待 epoll_wait / IOCP）；
        
    2. **把菜端上桌**（把 ready handler 放队列）；
        
    3. **告诉顾客“菜来了”，并帮顾客动手尝一口**（执行 handler）。