## 定义变量
aux_source_directory()也存在弊端，它会把指定目录下的所有源文件都加进来，可能会加入一些我们不需要的文件，此时我们可以使用set命令去新建变量来存放需要的源文件，如下：
```cmake
cmake_minimum_required (VERSION 2.8)

project (demo)

set( SRC_LIST
	 ./main.c
	 ./testFunc1.c
	 ./testFunc.c)

add_executable(main ${SRC_LIST})

```


## 指定c++使用标准
```cmake
#增加-std=c++11
set(CMAKE_CXX_STANDARD 11)
#增加-std=c++14
set(CMAKE_CXX_STANDARD 14)
#增加-std=c++17
set(CMAKE_CXX_STANDARD 17)

```

## 指定输出路径
```cmake
set(HOME /home/robin/Linux/Sort)
set(EXECUTABLE_OUTPUT_PATH ${HOME}/bin)
#第一行：定义一个变量用于存储一个绝对路径
#第二行：将拼接好的路径值设置给EXECUTABLE_OUTPUT_PATH宏


```


## 设置输出目录
```cmake
# 设置可执行文件的输出目录

set(CMAKE_RUNTIME_OUTPUT_DIRECTORY ${CMAKE_CURRENT_SOURCE_DIR}/out)

  

# 设置库文件的输出目录

set(CMAKE_LIBRARY_OUTPUT_DIRECTORY ${CMAKE_CURRENT_SOURCE_DIR}/out)

  

# 设置静态库的输出目录

set(CMAKE_ARCHIVE_OUTPUT_DIRECTORY ${CMAKE_CURRENT_SOURCE_DIR}/out)
```