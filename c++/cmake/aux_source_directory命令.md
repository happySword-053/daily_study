`aux_source_directory` 是 CMake 提供的一个命令，用于自动收集指定目录下的所有源文件，并将这些文件的列表存储到一个变量中。这在处理包含大量源文件的项目时非常有用，因为它可以自动检测目录中的所有源文件，而不需要手动列出每个文件。

```cmake
aux_source_directory(<dir> <variable>)
例子：aux_source_directory(. SRC_LIST) 其中SRC_LIST是自定义的名称



```
- `aux_source_directory` 默认只查找 `.c`, `.C`, `.cc`, `.cxx`, `.cpp`, `.m`, `.mm`, `.f`, `.F`, `.for`, `.f77`, `.f90`, `.f95`, 和 `.f03` 扩展名的文件。如果你需要查找其他扩展名的文件，可能需要自定义脚本来实现。
