`include_directories` 指令是 CMake 构建系统中的一个命令，用于指定编译器在编译过程中搜索包含文件（如头文件）的路径。当你在一个项目中使用了外部库或者有自定义的头文件目录时，就需要使用这个指令来告诉 CMake 在哪里可以找到这些头文件。


```cmake
include_directories([AFTER|BEFORE] [SYSTEM] dir1 [dir2 ...])
- `AFTER` 和 `BEFORE` 是可选参数，用来控制新添加的目录是在现有目录列表之前还是之后。
- `SYSTEM` 也是一个可选参数，它表示该目录被视为系统目录，这通常会影响警告级别的处理方式。对于某些编译器来说，如果指定了 `SYSTEM`，那么来自这个目录的头文件可能会被忽略一些特定的警告。
- `dir1`, `dir2`, ... 是你想要添加到搜索路径中的目录。
```



`target_include_directories` 是 CMake 中用于为特定目标指定头文件搜索路径的命令。它提供了比 `include_directories` 更细粒度和更清晰的控制，有助于更好地管理项目依赖和模块化。
```cmake
target_include_directories(<target> [SYSTEM] [BEFORE] <INTERFACE|PUBLIC|PRIVATE> [items1...] [<INTERFACE|PUBLIC|PRIVATE> [items2...] ...])
```