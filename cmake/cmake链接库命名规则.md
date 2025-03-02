### **CMake 链接库的命名规则**

#### **1. Unix/Linux 系统的库命名惯例**

- **库文件命名格式**：`lib<name>.so`（动态库）或 `lib<name>.a`（静态库）。
- **链接时简写规则**：  
    使用 `-l<name>` 时，链接器会自动搜索 `lib<name>.so` 或 `lib<name>.a`。  
    **示例**：`-lhiredis` → 自动匹配 `libhiredis.so` 或 `libhiredis.a`。

#### **2. CMake 对链接库名称的处理**

- **传递 `hiredis` 时**：  
    CMake 会将其转换为 `-lhiredis`，依赖链接器的搜索机制。
- **关键依赖条件**：  
    链接器必须能通过以下途径找到库文件：
    - 显式添加的库路径（如 `target_link_directories` 或 `link_directories`）。
    - 系统默认库路径（如 `/usr/lib`）。
    - 环境变量（如 `LD_LIBRARY_PATH`）。