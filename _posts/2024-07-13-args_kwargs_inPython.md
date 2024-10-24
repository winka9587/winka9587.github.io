---
layout: post
title: Python中的\*args 和 \*\*kwargs
date: 2024-07-13 15:23
category: python, research tool
author: 
tags: []
summary: 
---

\*args 和 \*\*kwargs

~~~
def test_func(arg1, arg2, **kwargs):
    print("arg1:", arg1)
    print("arg2:", arg2)
    for key, value in kwargs.items():
        print(f"{key} = {value}")

# 调用函数
test_func(1, 2, third=3, fourth=4)

kwargs = {"cxx": 333, "xxc": 456}

test_func(1, 2, **kwargs)
~~~

~~~
arg1: 1
arg2: 2
third = 3
fourth = 4
arg1: 1
arg2: 2
cxx = 333
xxc = 456
~~~

**但是还有一个有趣的用法此前没有见过**

可变数量关键字参数可以被 位置参数 接受

~~~
def my_function(arg1, arg2, **kwargs):
    print("arg1:", arg1)
    print("arg2:", arg2)
    for key, value in kwargs.items():
        print(f"{key} = {value}")
        
def my_function2(arg1, arg2, cxx=1, xxc=2, **kwargs):
    print("arg1:", arg1)
    print("arg2:", arg2)
    print("cxx:", cxx)
    print("xxc:", xxc)

kwargs = {"cxx": 333, "xxc": 456}

my_function(1, 2, **kwargs)
my_function2(1, 2, **kwargs)
~~~

~~~
arg1: 1
arg2: 2
cxx = 333
xxc = 456
arg1: 1
arg2: 2
cxx: 333
xxc: 456
~~~

但是如果我们尝试使用：

~~~
def my_function(arg1, arg2, **kwargs):
    print("arg1:", arg1)
    print("arg2:", arg2)
    for key, value in kwargs.items():
        print(f"{key} = {value}")
        
def my_function2(arg1, arg2, cxx=1, xxc=2, **kwargs):
    print("arg1:", arg1)
    print("arg2:", arg2)
    print("cxx:", cxx)
    print("xxc:", xxc)

kwargs = {"arg1": 100, "arg2": 200, "cxx": 333, "xxc": 456}

my_function(1, 2, **kwargs)
my_function2(**kwargs)
~~~
~~~
    my_function(1, 2, **kwargs)
TypeError: my_function() got multiple values for argument 'arg1'
~~~
但是请注意, 报错的是第一个，现在我们将my_function的调用注释掉
~~~
#my_function(1, 2, **kwargs)
my_function2(**kwargs)
~~~
结果如下：
~~~
arg1: 100
arg2: 200
cxx: 333
xxc: 456
~~~