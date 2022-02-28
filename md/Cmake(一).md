# Cmake(一)：基本指令


> 官方文档:<https://cmake.org/cmake/help/latest/>


## 1. cmake_minimum_required(VERSION 版本号)

设置工程所需要的最低版本号

## 2. project(工程名)

设置工程名

## 3. set()

设置变量

## 4. message ( [ < mode >] "message text" ... )

打印信息

< mode >：
- FATAL_ERROR
- FATAL_ERROR
- WARNING
- AUTHOR_WARNING
- DEPRECATION
- (none) or NOTICE
- STATUS
- VERBOSE
- VERBOSE
- TRACE


## 4. add_subdirectory()

添加工程子目录

## 5. include_directories([AFTER|BEFORE] [SYSTEM]  dir1  dir2 ...)

向工程添加多个特定的头文件搜索路径

- AFTER:添加到当前列表后
- BEFORE:添加到当前列表前
- SYSTEM:把指定目录当成系统的搜索目录。该命令作用范围只在当前的CMakeLists.txt


## 6. link_directories(directory1 directory2 ...)

向工程添加多个特定的库文件搜索路径

## 7. aux_source_directory(文件夹路径 变量名)

发现一个目录下所有的源代码文件并将列表存储在一个变量中，这个指令临时被用来自动构建源文件列表

```c
// 定义SRC变量，其值为当前目录下所有的源代码文件
aux_source_directory(. SRC)
```

## 8. add_library()

将源码构建成一个库

```C
add_library(<name> [STATIC | SHARED | MODULE]
            [EXCLUDE_FROM_ALL]
            source1 [source2 ...])

SHARED,动态库
STATIC,静态库
MODULE,在使用 dyld 的系统有效,如果不支持 dyld,则被当作 SHARED 对待。
```

## 9. target_link_libraries()

该指令的作用为将目标文件与库文件进行链接

```c
target_link_libraries(<target>
                      <PRIVATE|PUBLIC|INTERFACE> <item>...
                     [<PRIVATE|PUBLIC|INTERFACE> <item>...]...)

```

## 10. add_compile_options()

添加编译参数

## 11. add_executable()

生成可执行文件





