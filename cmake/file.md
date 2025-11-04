`file` 指令是一个非常强大且多功能的命令，用于执行各种文件和目录操作。它的语法是 `file(<SUBCOMMAND> ...)`，其中 `<SUBCOMMAND>` 是具体的操作。

`file` 指令主要分为以下几大类功能：

---

### 1. 文件内容操作

#### `file(READ <filename> <variable> [OFFSET <offset>] [LIMIT <max_len>] [HEX])`

- **作用**: 从文件中读取内容并存储到变量中。
    
- **示例**:
    
    
    
    ```CMake
    # 读取文件内容到变量
    file(READ "${CMAKE_CURRENT_SOURCE_DIR}/version.txt" APP_VERSION)
    message("App Version: ${APP_VERSION}")
    ```
    

#### `file(WRITE <filename> <content>...)`

- **作用**: 将内容写入文件。如果文件不存在则创建，如果存在则覆盖。
    
- **示例**:
    
    CMake
    
    ```CMake
    # 将文本写入文件
    file(WRITE "${CMAKE_CURRENT_BINARY_DIR}/config.h" "#define MY_APP_VERSION \"1.0\"")
    ```
    

#### `file(APPEND <filename> <content>...)`

- **作用**: 将内容追加到文件末尾。
    
- **示例**:
    
    CMake
    
    ```CMake
    # 在文件中追加内容
    file(APPEND "${CMAKE_CURRENT_BINARY_DIR}/log.txt" "Build started at ${CMAKE_CURRENT_DATETIME}\n")
    ```
    

---

### 2. 文件系统操作

#### `file(GLOB <variable> [LIST_DIRECTORIES true|false] [RELATIVE <path>] [CONFIGURE_DEPENDS] <globbing_expressions>...)`

- **作用**: 查找匹配给定模式的文件，并将结果存储到变量中。
    
- **注意**: 这种方法不推荐用于查找源文件，因为它会导致 CMakeLists.txt 在文件增删时不会自动重新配置。现代 CMake 推荐手动列出源文件，或者使用 `GLOB_RECURSE` 配合 `CONFIGURE_DEPENDS`。
    
- **示例**:
    
    CMake
    
    ```CMake
    # 查找当前目录下的所有 .cpp 文件
    file(GLOB SOURCE_FILES "*.cpp")
    add_executable(MyProgram ${SOURCE_FILES})
    ```
    

#### `file(GLOB_RECURSE <variable> [LIST_DIRECTORIES true|false] [RELATIVE <path>] [CONFIGURE_DEPENDS] <globbing_expressions>...)`

- **作用**: 递归地查找匹配模式的文件。
    
- **示例**:
    
    CMake
    
    ```CMake
    # 递归查找 src 目录下的所有 .cpp 和 .h 文件
    file(GLOB_RECURSE SOURCES "src/*.cpp" "src/*.h")
    add_executable(MyProgram ${SOURCES})
    ```
    
    > **面试技巧**: 当面试官问到如何自动添加源文件时，你可以提到 `file(GLOB_RECURSE)`，但同时要指出它的缺点（不自动触发重新配置），并提及 **`CONFIGURE_DEPENDS`** 参数可以缓解这个问题（需要 CMake 3.12+）。
    

#### `file(COPY <file>... <destination_dir>)`

- **作用**: 复制文件或目录到目标位置。
    
- **示例**:
    
    CMake
    
    ```CMake
    # 将 README.md 和 LICENSE 复制到构建目录
    file(COPY "${CMAKE_CURRENT_SOURCE_DIR}/README.md" "${CMAKE_CURRENT_SOURCE_DIR}/LICENSE" DESTINATION "${CMAKE_CURRENT_BINARY_DIR}")
    ```
    

#### `file(REMOVE <file>...)` / `file(REMOVE_RECURSE <file>...)`

- **作用**: 删除文件或目录。`REMOVE_RECURSE` 会递归删除。
    
- **示例**:
    
    CMake
    
    ```CMake
    # 删除临时文件
    file(REMOVE "${CMAKE_CURRENT_BINARY_DIR}/temp.log")
    
    # 递归删除整个目录
    file(REMOVE_RECURSE "${CMAKE_CURRENT_BINARY_DIR}/cache")
    ```
    

#### `file(MAKE_DIRECTORY <dir>...)`

- **作用**: 创建一个或多个目录。
    
- **示例**:
    
    CMake
    
    ```CMake
    # 创建一个用于存放配置文件的目录
    file(MAKE_DIRECTORY "${CMAKE_CURRENT_BINARY_DIR}/config")
    ```
    

---

### 3. 文件信息和路径操作

#### `file(CREATE_LINK <original> <link_name> [RESULT <variable>] [SYMBOLIC])`

- **作用**: 创建文件或目录的硬链接或软链接（符号链接）。
    
- **示例**:
    
    CMake
    
    ```CMake
    # 创建一个符号链接
    file(CREATE_LINK "my_app" "my_app_link" SYMBOLIC)
    ```
    

#### `file(SIZE <filename> <variable>)`

- **作用**: 获取文件大小。
    
- **示例**:
    
    CMake
    
    ```CMake
    file(SIZE "main.cpp" MAIN_CPP_SIZE)
    message("main.cpp size: ${MAIN_CPP_SIZE} bytes")
    ```
    

#### `file(TIMESTAMP <filename> <variable> [<format>] [UTC])`

- **作用**: 获取文件的修改时间戳。
    
- **示例**:
    
    CMake
    
    ```CMake
    file(TIMESTAMP "CMakeLists.txt" LAST_MODIFIED_TIME)
    message("CMakeLists.txt was last modified at: ${LAST_MODIFIED_TIME}")
    ```
    

---

### 4. 哈希和校验和

#### `file(<HASH> <filename> <variable>)`

- **作用**: 计算文件的哈希值，支持 `MD5`, `SHA1`, `SHA224`, `SHA256`, `SHA384`, `SHA512` 等。
    
- **示例**:
    
    CMake
    
    ```CMake
    # 计算文件的 SHA256 哈希值
    file(SHA256 "archive.zip" ARCHIVE_HASH)
    message("archive.zip SHA256: ${ARCHIVE_HASH}")
    ```