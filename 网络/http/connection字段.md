`Connection` 字段是 HTTP 协议里的一个通用首部字段，既可以在请求头中使用，也能在响应头中使用，其主要作用是控制 HTTP 连接的管理和行为，以下详细介绍其具体用途：

### 控制持久连接

  

在 HTTP/1.0 中，默认情况下每个请求 / 响应事务结束后，TCP 连接就会关闭。而从 HTTP/1.1 开始，默认启用持久连接，这意味着多个请求和响应可以在同一个 TCP 连接上进行传输，减少了频繁建立和关闭连接的开销，从而提高了性能。`Connection` 字段可以用来控制持久连接的开启和关闭，常见的取值有：

  

- **`keep-alive`**：在请求头或响应头中使用 `Connection: keep-alive` 表示希望保持当前的 TCP 连接，以便后续的请求可以复用该连接。例如，客户端发送请求时携带该字段：

  

收起

plaintext

```
GET /index.html HTTP/1.1
Host: example.com
Connection: keep-alive
```

  

服务器若支持持久连接，会在响应头中也返回 `Connection: keep-alive`，表示同意保持连接。这样后续客户端再向该服务器发送请求时，就可以使用同一个 TCP 连接，避免了重新建立连接的时间和资源消耗。

  

- **`close`**：使用 `Connection: close` 则表示在当前请求 / 响应事务完成后关闭 TCP 连接。例如，当服务器资源紧张或者客户端希望尽快释放连接时，可以在请求或响应中使用该字段。如服务器响应：

  

收起

plaintext

```
HTTP/1.1 200 OK
Content-Type: text/html
Connection: close
```

  

客户端收到该响应后，在处理完本次响应内容后会关闭与服务器的 TCP 连接。

### 告知代理服务器处理方式

  

在存在代理服务器的网络环境中，`Connection` 字段可以告知代理服务器如何处理请求和响应。当请求或响应头中包含 `Connection` 字段时，代理服务器会根据该字段的值进行相应操作：

  

- 代理服务器会移除请求头中 `Connection` 字段指定的首部字段。例如，请求头为：

  

收起

plaintext

```
GET /resource HTTP/1.1
Host: example.com
Connection: custom-header
custom-header: some-value
```

  

代理服务器会移除 `custom-header` 字段，然后将修改后的请求转发给目标服务器。同样，代理服务器也会对响应头进行类似处理，确保连接相关的信息不会被错误传递。

### 协议升级提示

  

在某些情况下，客户端可能希望将当前的 HTTP 连接升级到其他协议（如 WebSocket），`Connection` 字段可以与 `Upgrade` 字段配合使用来实现协议升级。例如，客户端发送如下请求：

  

收起

plaintext

```
GET / HTTP/1.1
Host: example.com
Connection: Upgrade
Upgrade: websocket
```

  

这里 `Connection: Upgrade` 表示客户端希望对当前连接进行协议升级，`Upgrade: websocket` 明确指出要升级到 WebSocket 协议。服务器收到请求后，会根据自身能力决定是否同意升级，如果同意，会在响应中返回相应信息，完成协议升级过程。

分享