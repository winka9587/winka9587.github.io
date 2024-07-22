---
layout: post
title: CMakeLists include 问题
date: 2024-05-21 10:59
category: cmake
author: 
tags: [cmake, CMakeLists]
summary: 
---

# include_directories与target_include_directories

**include_directories**并不会将include继续向外层传播, 导致最外层的executable在编译时无法找到模块中的头文件**

# getting_started

### Step1

configure_file将一个文件映射到另一个文件, 并且将文件中的变量@VAR@进行替换, 这样能够动态地生成文件。这一步是在cmake阶段就完成的, 当cmake命令执行后, 在项目的二进制文件目录下, 可以找到生成的对应.h文件, 可以发现文件中的@xxx@变量已经被替换掉了。

例如:

而生成的文件, 默认存放在\${PROJECT_BINARY_DIR}下, 例如在build目录下, 执行
    
    cmake ..

此时的${PROJECT_BINARY_DIR}就是../build

使用target_include_directories将二进制目录包含进去, 来便于寻找头文件

## Step2

要想添加一个库, 要做到:

1. 添加子模块的目录 add_subdirectory
2. 在子模块的CMakeLists中创建库, add_library, 将source文件添加进去
3. 在项目的CMakeLists中, 将子模块创建的lib使用target_link_libraries链接到exe
4. 在项目的CMakeLists中, 将子模块所在的头文件目录target_include_directories包含在内
5. 在生成exe的源文件中, #inlcude “xx.h” 包含子模块的头文件, 并可以使用其中的函数/对象

## library与变量

add_library创建的在使用时不加$

set创建的需要加$

Q:如何区分CMakeLists中的库、变量名、字符串？是否还有其他的？

## 链接问题

之前测试时似乎有一个问题:

文件树结构如下:
~~~
|-modules
    |-A
        |-a.h
        |-a.cpp
    |-B
        |-b.h
~~~
如果b.h中使用了#include "a.h", 那么必须将A添加到B的target_link_libraries中。（但是这在报错时候不一定能够被发现），如果报错No such file or directory, 那么一定要检查报错来源的模块是哪个，以确定是模块内部include的问题还是模块之间链接的问题。

## 循环链接问题

以前我以为cmake会自动处理循环链接, 但事实是我没能链接成功导致根本不够成循环

Q:什么是weak

~~~
-- Configuring done (0.4s)
CMake Error: The inter-target dependency graph contains the following strongly connected component (cycle):
  "Basic" of type SHARED_LIBRARY
    depends on "CVF" (weak)
    depends on "CVX" (weak)
  "CVX" of type SHARED_LIBRARY
    depends on "Basic" (weak)
    depends on "CVF" (weak)
  "CVF" of type SHARED_LIBRARY
    depends on "CVX" (weak)
At least one of these targets is not a STATIC_LIBRARY.  Cyclic dependencies are allowed only among static libraries.
CMake Generate step failed.  Build files cannot be regenerated correctly.
~~~

## STATIC与SHARED的不同

动态链接库与静态链接库的不同

这次不是背八股了，到底为什么不同？使用起来有什么不同？

我们常用的.so到底是什么

## cmake, make, ninja, 编译, 生成这些都是什么概念？

## PRIVATE, PUBLIC, INTERFACE library的接口

## 工具

vscode-cmake-tool

.vscode创建settings.json

内容如下:(不显示指定的话, generator会自动选择ninja)
~~~
{
    "cmake.configureArgs": [
        "-DENABLE_T02=ON",
        "-DCMAKE_BUILD_TYPE=Release"
    ],
    "cmake.buildDirectory": "${workspaceFolder}/build"
}
~~~
等价于命令
~~~
cmake -DENABLE_T02=ON -DCMAKE_BUILD_TYPE=Release ..
~~~

**ctrl+shift+P** -> **CMake:Configure with CMake Debugger**



超级调试代码

假设当前CMakeLists中创建的library为CVX, 其target_link_libraries链接了GlobalLibs库。现在药测试其是否链接成功。

~~~
# 获取当前库CVX的 PRIVATE、PUBLIC 和 INTERFACE 包含目录属性
get_target_property(CVX_include_dirs_PRIVATE CVX INCLUDE_DIRECTORIES)
get_target_property(CVX_include_dirs_PUBLIC CVX INTERFACE_INCLUDE_DIRECTORIES)

message(STATUS "CVX PRIVATE INCLUDE_DIRECTORIES: ${CVX_include_dirs_PRIVATE}")
message(STATUS "CVX INTERFACE INCLUDE_DIRECTORIES: ${CVX_include_dirs_PUBLIC}")
# 获取接口库GlobalLibs PRIVATE、PUBLIC 和 INTERFACE 包含目录属性
get_target_property(GlobalLibs_include_dirs_PRIVATE GlobalLibs INCLUDE_DIRECTORIES)
get_target_property(GlobalLibs_include_dirs_PUBLIC GlobalLibs INTERFACE_INCLUDE_DIRECTORIES)

message(STATUS "GlobalLibs  PRIVATE INCLUDE_DIRECTORIES: ${GlobalLibs_include_dirs_PRIVATE}")
message(STATUS "GlobalLibs INTERFACE INCLUDE_DIRECTORIES: ${GlobalLibs_include_dirs_PUBLIC}")

# 检查属性
get_target_property(CVX_include_dirs_PRIVATE CVX INCLUDE_DIRECTORIES)
get_target_property(CVX_include_dirs_PUBLIC CVX INTERFACE_INCLUDE_DIRECTORIES)

get_target_property(CVX_link_libraries_PRIVATE CVX LINK_LIBRARIES)
get_target_property(CVX_link_libraries_PUBLIC CVX INTERFACE_LINK_LIBRARIES)
message(STATUS "============================================================")
message(STATUS "CVX PRIVATE INCLUDE_DIRECTORIES: ${CVX_include_dirs_PRIVATE}")
message(STATUS "CVX INTERFACE INCLUDE_DIRECTORIES: ${CVX_include_dirs_PUBLIC}")

message(STATUS "CVX PRIVATE LINK_LIBRARIES: ${CVX_link_libraries_PRIVATE}")
message(STATUS "CVX INTERFACE LINK_LIBRARIES: ${CVX_link_libraries_PUBLIC}")
~~~