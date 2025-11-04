 ### 1. **Name 和 Value**

- **描述** : `Cookie` 的核心部分，由键值对组成。
- **格式** : `Name=Value`
- **说明** :
    - `Name` 是 `Cookie` 的名称，必须是唯一的。
    - `Value` 是与该名称关联的值，通常以字符串形式存储。
- **示例** : `username=JohnDoe`

---

### 2. **Domain**

- **描述** : 指定 `Cookie` 所属的域。
- **作用** : 定义哪些域名可以访问该 `Cookie`。
- **规则** :
    - 如果未设置，默认为当前页面的域名。
    - 子域名可以共享父域名的 `Cookie`（例如，`sub.example.com` 可以访问 `example.com` 设置的 `Cookie`）。
- **示例** : `Domain=example.com`

---

### 3. **Path**

- **描述** : 指定 `Cookie` 的路径范围。
- **作用** : 定义哪些路径下的页面可以访问该 `Cookie`。
- **规则** :
    - 如果未设置，默认为当前页面的路径。
    - 路径 `/` 表示整个网站都可以访问该 `Cookie`。
- **示例** : `Path=/dashboard`

---

### 4. **Expires / Max-Age**

- **描述** : 定义 `Cookie` 的有效期。
- **作用** : 控制 `Cookie` 的生命周期。
- **区别** :
    - `Expires`: 设置一个具体的过期时间（UTC 格式）。
    - `Max-Age`: 设置从创建时刻开始的有效时长（单位为秒）。
- **规则** :
    - 如果未设置 `Expires` 或 `Max-Age`，`Cookie` 将成为会话 `Cookie`，在浏览器关闭时被删除。
- **示例** :
    - `Expires=Wed, 09 Nov 2023 10:00:00 GMT`
    - `Max-Age=3600` （表示 1 小时后过期）

---

### 5. **Secure**

- **描述** : 指定 `Cookie` 是否只能通过 HTTPS 协议传输。
- **作用** : 提高安全性，防止 `Cookie` 在非加密连接中被窃取。
- **规则** :
    - 如果设置了 `Secure` 属性，`Cookie` 只能在 HTTPS 请求中发送。
- **示例** : `Secure`

---

### 6. **HttpOnly**

- **描述** : 指定 `Cookie` 是否可以通过 JavaScript 访问。
- **作用** : 防止跨站脚本攻击（XSS），避免恶意脚本窃取 `Cookie`。
- **规则** :
    - 如果设置了 `HttpOnly`，JavaScript 无法通过 `document.cookie` 访问该 `Cookie`。
- **示例** : `HttpOnly`

---

### 7. **SameSite**

- **描述** : 控制 `Cookie` 是否可以在跨站请求中发送。
- **作用** : 防止跨站请求伪造（CSRF）攻击。
- **可选值** :
    - `Strict`: `Cookie` 只能随同站点请求发送。
    - `Lax`: 允许部分跨站请求（如导航到目标站点）携带 `Cookie`。
    - `None`: 允许所有跨站请求携带 `Cookie`（需要同时设置 `Secure` 属性）。
- **示例** : `SameSite=Strict`

---

### 8. **Partitioned** (较新的属性)

- **描述** : 用于支持分区存储（Partitioned Storage）。
- **作用** : 在第三方上下文中隔离 `Cookie`，增强隐私保护。
- **规则** :
    - 主要用于跨站 `Cookie`，确保不同上下文之间的 `Cookie` 不会相互干扰。
- **示例** : `Partitioned`
一个cookie可能为 Set-Cookie: sessionId=abc123; Domain=example.com; Path=/; Expires=Wed, 09 Nov 2023 10:00:00 GMT; Secure; HttpOnly; SameSite=Lax


### **浏览器如何决定发送哪些 Cookie？**

浏览器根据以下规则自动管理 Cookie 的发送：

1. **域名匹配** ：
    
    - Cookie 的 `Domain` 属性必须匹配当前请求的域名（或其子域名）。
    - 如果未设置 `Domain`，默认绑定当前页面的完整域名（不包含子域名）。
2. **路径匹配** ：
    
    - 请求的 URL 路径必须与 Cookie 的 `Path` 属性匹配（或为其子路径）。
3. **安全属性** ：
    
    - `Secure`：仅通过 HTTPS 发送。
    - `SameSite`：控制跨站请求时是否发送（如 `Strict` 或 `Lax`）。
4. **有效期** ：
    
    - 未过期的 Cookie 才会被发送。



**Cookie 生命周期示例 (伪代码/HTTP 交互)：**

**场景：** 用户首次访问 `https://www.example.com/login` 并成功登录。

1. **服务器设置 Cookie (响应):**
    
    http
    
    复制
    
    下载
    
    HTTP/1.1 200 OK
    Content-Type: text/html
    Set-Cookie: sessionId=abc123xyz; Expires=Wed, 21 Oct 2025 07:28:00 GMT; Domain=.example.com; Path=/; Secure; HttpOnly; SameSite=Lax
    Set-Cookie: theme=dark; Max-Age=2592000; Path=/; Secure; HttpOnly // 主题偏好，保存30天
    <!DOCTYPE html>
    <html>...登录成功页面...</html>
    
    - **`sessionId=abc123xyz`**: 核心会话标识。
        
    - **`Expires` / `Max-Age`**: 控制有效期（`sessionId` 持久化，`theme` 30天后过期）。
        
    - **`Domain=.example.com`**: 浏览器会将此Cookie发送给`www.example.com`, `api.example.com`, `blog.example.com`等所有子域（注意开头的点`.`表示包含子域）。
        
    - **`Path=/`**: 发送给`example.com`域下_任何路径_的请求。
        
    - **`Secure`**: 仅通过HTTPS传输。
        
    - **`HttpOnly`**: JavaScript无法读取，防止XSS窃取会话ID。
        
    - **`SameSite=Lax`**: 允许在顶级导航（如链接点击）和同站GET请求中发送Cookie，阻止跨站POST提交等CSRF常用手段。
        
2. **浏览器存储 Cookie:**
    
    - 浏览器接收到响应头中的`Set-Cookie`指令。
        
    - 根据属性(`Domain`, `Path`, `Secure`, `HttpOnly`, `SameSite`, `Expires/Max-Age`)将键值对(`sessionId=abc123xyz`, `theme=dark`)安全地存储在其Cookie仓库中。
        
3. **浏览器发送 Cookie (后续请求):**  
    当用户访问 `https://www.example.com/dashboard` 或 `https://api.example.com/user/profile` (同域或子域) 时：
    
    http
    
    复制
    
    下载
    
    GET /dashboard HTTP/1.1
    Host: www.example.com
    Cookie: sessionId=abc123xyz; theme=dark // 浏览器自动添加存储的、符合作用域和安全要求的Cookie
    
    - 浏览器检查请求的URL(`https://www.example.com/dashboard`)。
        
    - 匹配`Domain`(`.example.com`), `Path`(`/`), `Secure`(是HTTPS), `SameSite`(符合Lax策略)。
        
    - 将匹配的Cookie(`sessionId`, `theme`)自动放入`Cookie`请求头发送给服务器。
        
4. **服务器读取 Cookie:**
    
    - 后端应用（C++服务）从HTTP请求的`Cookie`头中解析出键值对。
        
    - 使用`sessionId=abc123xyz`查找服务器端存储的会话数据（通常在Redis或内存缓存中），识别用户身份和状态。
        
    - 使用`theme=dark`为用户加载深色主题界面。