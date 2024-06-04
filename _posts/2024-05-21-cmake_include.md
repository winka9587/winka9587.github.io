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

