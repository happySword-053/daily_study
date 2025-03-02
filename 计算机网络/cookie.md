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

也就是说，只要浏览器对应的cookie满足域名匹配，路径匹配，安全属性，有效期就可以发送cookie对吗？