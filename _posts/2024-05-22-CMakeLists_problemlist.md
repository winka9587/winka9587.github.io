---
title: CMakeLists实践问题与迷思
date: 2024-05-22 16:15
category: cmake
author: 
tags: []
summary: 
---

之前整理了CMakeLists中include_directories与target_include_directories, 本以为自己已经有了更深度的了解。但是实践出真知, 又一次被现实的铁拳制裁了，在实际操作中情况只会更加复杂，问题会更加匪夷所思，在这里做一个汇总的记录。

## include路径的打印与检查

### 问题

在CMakeLists中添加下面的内容来输出已经include的路径

~~~
    get_target_property(ObjectTracking_INCLUDE_DIRS ObjectTracking INCLUDE_DIRECTORIES)
    message(STATUS "ObjectTracking include directories: ${ObjectTracking_INCLUDE_DIRS}")
~~~

这是在正常编译时, 输出的结果

~~~
- ObjectTracking include directories: 
    /home/lab/workspace/ARTest/AREngine/ObjectTracking/asset;
    /home/lab/workspace/ARTest/AREngine/ObjectTracking/include;
    /home/lab/workspace/ARTest/AREngine/Basic/include 
~~~

正常编译的末尾, 添加了以下代码, 用于将当前模块单独生成一个SHARED库, 一般通过find_library来加载库, 顺便解释下： 首先创建变量ObjectTracking_LIB存储最终的读取结果, NAMES为ObjectTracking，在linux系统下，会去寻找libObjectTracking.so文件, NO_DEFAULT_PATH则是与搜索路径相关的, 在这里不深入探讨。

~~~
    find_library(ObjectTracking_LIB 
        NAMES ObjectTracking 
        PATHS ${CMAKE_CURRENT_SOURCE_DIR}/AREngine/ObjectTracking/lib 
        NO_DEFAULT_PATH
    )
~~~

但是, 当使用find_library将.so加载进来时,打印结果却是：

~~~
- ObjectTracking include directories: 
    ObjectTracking_INCLUDE_DIRS-NOTFOUND 
~~~

### 破案: INTERFACE 库对应INTERFACE_INCLUDE_DIRECTORIES

对于 **add_library(ObjectTracking INTERFACE)** 创建的库, 
需要获得其 **INTERFACE_INCLUDE_DIRECTORIES** 属性而非 **INCLUDE_DIRECTORIES** 属性。

~~~
    get_target_property(ObjectTracking_INTERFACE_INCLUDE_DIRS ObjectTracking INTERFACE_INCLUDE_DIRECTORIES)
    message(STATUS "ObjectTracking interface include directories: ${ObjectTracking_INTERFACE_INCLUDE_DIRS}")
~~~

**Q: 难道是生成一个共享库（.so 文件）时并这些路径和库的配置信息会保存在该目标的属性中。然而，当你使用 find_library 加载一个预构建的共享库并创建一个新的导入目标时，之前通过target_include_directories 添加的依赖路径、通过 target_link_libraries 链接的依赖库等，都不会自动传递到这个新的导入目标中？**

A: 是的, 需要重新include并link之前的依赖信息。

# .so是只读->INTERFACE->include？

因为add_library只能通过SHARED IMPORTED来导入库, 那么得到的库就是只读的, 因此无法使用PUBLIC和PRIVATE来include和link，那么问题出现了：导入的库如果依赖于其他的模块，需要include其他路径，应该怎么办？

### 构建 & 链接


# 对find_library的误解

起因: 之前写下了这么一段

~~~
    find_library(${module}_LIB NAMES moduleX PATHS ${CMAKE_CURRENT_SOURCE_DIR}/lib NO_DEFAULT_PATH)
    target_link_libraries(TARGET_LIBRARIES INTERFACE ${${module}_LIB})
~~~

首先：直接使用find_library只会返回一个库的路径

其次, ${module}_LIB只是一个变量的名字, ${${module}_LIB}只是返回了该变量对应的路径。

但是为什么上面的代码可以正常工作？是因为target_link_libraries接受路径作为输入。

正常的工作流程应该是：
~~~
    find_library(Basic_LIB NAMES Basic PATHS ${SHARED_LIB} NO_DEFAULT_PATH)
    if(Basic_LIB)
        add_library(Basic SHARED IMPORTED)
        set_target_properties(Basic PROPERTIES IMPORTED_LOCATION ${Basic_LIB})
        set(LIB_TYPE INTERFACE)
    else()
        message(FATAL_ERROR "Prebuilt library Basic not found")
    endif()
~~~

# 调用.so的成功与失败