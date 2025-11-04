`http` 配置块是 Nginx 配置中的核心部分，它包含了 Web 服务器处理 HTTP 请求的所有设置。`http` 块通常位于 `nginx.conf` 文件的顶层，并且包含多个子配置块。我们一起来详细了解一下 `http` 块及其每个字段的作用。

### `http` 配置块

`http` 块是 Nginx 配置文件中处理 HTTP 请求的主要部分。它包含了与 HTTP 相关的全局设置，虚拟主机配置，以及其他辅助功能（如负载均衡、缓存、压缩等）。通常，你会在 `http` 块中包含多个 `server` 块，每个 `server` 块定义了一个虚拟主机。

#### 示例：



```nginx
http {     # 全局配置      
	include       mime.types;     
	default_type  application/octet-stream;      
	log_format    main '$remote_addr - $remote_user [$time_local] "$request" '                        '$status $body_bytes_sent "$http_referer" '                        '"$http_user_agent" "$http_x_forwarded_for"';     
	access_log    /var/log/nginx/access.log main;     
	sendfile      on;    
	tcp_nopush    on;     
	tcp_nodelay   on;      
	keepalive_timeout  65;      
	server {         
		listen 80;         
		server_name example.com;                  
		location / {             
			root /usr/share/nginx/html;             
			index index.html index.htm;         
			}     
	}      # 更多的 server 块... 
}
```

### 1. **include**

`include` 用来包含其他配置文件。这是非常常见的做法，特别是在大型的配置文件中，可以将不同的配置模块放入单独的文件中，方便维护。

nginx

复制编辑

`include mime.types;  # 引入 mime.types 文件，定义 MIME 类型`

### 2. **default_type**

`default_type` 定义了如果未能匹配到文件类型的情况下，Nginx 会使用的默认 MIME 类型。通常设置为 `application/octet-stream`，这意味着文件类型将被视为二进制流。

nginx

复制编辑

`default_type application/octet-stream;`

### 3. **log_format**

`log_format` 用来定义访问日志的格式。Nginx 支持自定义日志格式，可以选择记录请求的各个信息（如客户端 IP、请求时间、请求方法等）。

nginx

复制编辑

`log_format main '$remote_addr - $remote_user [$time_local] "$request" '                  '$status $body_bytes_sent "$http_referer" '                  '"$http_user_agent" "$http_x_forwarded_for"';`

**字段解释：**

- `$remote_addr`：客户端的 IP 地址。
    
- `$remote_user`：客户端的用户名（如果启用了身份验证）。
    
- `$time_local`：请求的本地时间。
    
- `$request`：请求的完整 URL。
    
- `$status`：HTTP 响应状态码。
    
- `$body_bytes_sent`：响应体的大小。
    
- `$http_referer`：请求的来源 URL（即 Referer 头部）。
    
- `$http_user_agent`：客户端的 User-Agent 信息。
    
- `$http_x_forwarded_for`：代理服务器传递的客户端 IP 地址（如果有的话）。
    

### 4. **access_log**

`access_log` 指定访问日志的存储路径及其格式。上面已经定义了 `log_format main`，所以 `access_log` 会使用 `main` 格式来记录日志。

nginx

复制编辑

`access_log /var/log/nginx/access.log main;`

### 5. **sendfile**

`sendfile` 启用或禁用 `sendfile()` 系统调用。它用于通过直接将文件从磁盘发送到网络，而不经过用户空间的方式，来提高文件传输效率。通常在静态文件服务中启用。

nginx

复制编辑

`sendfile on;  # 启用 sendfile 系统调用`

### 6. **tcp_nopush 和 tcp_nodelay**

这两个指令是优化 TCP 连接性能的选项。它们通常与 `sendfile` 一起使用。

- **tcp_nopush**：用于优化发送大块数据时的延迟问题。在发送大文件时，Nginx 会尽量推迟将数据发送到客户端，以便一次性发送更大的数据包，减少 TCP 包的数量。
    

nginx

复制编辑

`tcp_nopush on;`

- **tcp_nodelay**：在 TCP 连接中，减少延迟，立即发送小的网络数据包。这通常用于需要即时响应的请求，如 API 请求。
    

nginx

复制编辑

`tcp_nodelay on;`

### 7. **keepalive_timeout**

`keepalive_timeout` 设置 Nginx 与客户端之间保持连接的最大时间。默认情况下，Nginx 会在响应之后保持连接，以便处理后续的请求。

nginx

复制编辑

`keepalive_timeout 65;  # 设置连接保持超时时间（秒）`

### 8. **server**

`server` 块用于定义一个虚拟主机配置，通常会根据不同的域名或 IP 地址来分发请求。每个 `server` 块包含了该虚拟主机的监听端口、域名、根目录等配置。

nginx

复制编辑

`server {     listen 80;     server_name example.com;          location / {         root /usr/share/nginx/html;         index index.html index.htm;     } }`

### 9. **listen**

`listen` 配置指令指定了 Nginx 监听的端口和协议。常见的配置是监听 HTTP 请求（80 端口）和 HTTPS 请求（443 端口）。

nginx

复制编辑

`listen 80;  # 监听 HTTP 请求的 80 端口 listen 443 ssl;  # 监听 HTTPS 请求的 443 端口，启用 SSL`

### 10. **server_name**

`server_name` 配置指令定义了该虚拟主机匹配的域名。多个域名可以用空格分隔。

nginx

复制编辑

`server_name example.com www.example.com;  # 匹配 example.com 和 www.example.com`

### 11. **root 和 index**

- **root**：定义站点的根目录，Nginx 会根据这个路径来查找请求的文件。
    
- **index**：定义默认的首页文件，如果请求目录时没有指定文件，Nginx 会查找 `index` 指定的文件。
    

nginx

复制编辑

`root /usr/share/nginx/html;  # 网站根目录 index index.html index.htm;  # 默认首页文件`

### 12. **location**

`location` 块用于匹配请求的 URI，并根据 URI 执行不同的处理。它是 Nginx 配置中最常用的部分之一。

nginx

复制编辑

`location / {     root /usr/share/nginx/html;     index index.html; }`

#### 常用的 location 匹配方式：

- **精确匹配**：`location = /path/` 只匹配完全等于 `/path/` 的请求。
    
- **前缀匹配**：`location /path/` 匹配以 `/path/` 开头的请求。
    
- **正则匹配**：`location ~ /path/` 使用正则表达式来匹配请求。
    

### 13. **proxy_pass**

`proxy_pass` 用于设置反向代理，将请求转发到另一个服务器。常用于负载均衡和 API 网关。

nginx

复制编辑

`location /api/ {     proxy_pass http://backend_server;  # 将请求转发到后端服务器 }`

### 14. **try_files**

`try_files` 用来在文件不存在时尝试多个路径，或者返回一个错误。常用于处理静态文件请求。

nginx

复制编辑

`location / {     try_files $uri $uri/ =404;  # 如果请求的文件不存在，返回 404 错误 }`

### 15. **rewrite**

`rewrite` 用于重写 URL 路径。常用于实现 URL 重定向、SEO 优化等。

nginx

复制编辑

`location / {     rewrite ^/oldpage$ /newpage permanent;  # 永久重定向 }`