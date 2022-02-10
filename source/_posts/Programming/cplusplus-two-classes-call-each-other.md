---
title: Two Classes Call each other (C++)
date: 2019-05-22 23:52:21
tags: OOP
categories: 
  - Programming
  - C++
---

# 两个对象互相访问成员函数及成员变量

## 头文件

父类的头文件应当 include 子类的头文件，同时声明指向子类的指针。

`Parent.h`

```c++
#include "Son.h"

class Parent
{
public:
    Parent();
    ~Parent();

    Son* sonPtr;     // 声明指向子对象的指针

public:
    int a;
}
```

在C++中，如果两个头文件互相调用，将会发生循环检测引用的编译错误。为了避免编译错误，在子类的头文件中，**<font size="4">不应该</font>** include 父类的头文件，而是直接声明父类。

`Son.h`

```c++

class Parent;

class Son
{
public:
    Son(Parent* ptr);
    ~Son();

    Parent* parentPtr;  // 声明指向父类的指针

public:
    int getParentA();   // 声明获取父类成员变量的函数
}
```

## 源文件

父类的源文件应当包含其自身的头文件, 可以在构造函数中生成子类的对象，将该对象的地址赋值给指针sonPtr。

`Parent.cpp`

```c++
#include "Parent.h"

Parent::Parent()
{
    sonPtr = new Son(this);   // 初始化父对象的同时，初始化子对象，并将父对象的指针存放到子对象中
    a = 10;
}

Parent::~Parent()
{
    delete sonPtr;
}

```

子类的源文件中应当包含**父类的头文件**，而不是包含自身的头文件，否则会发生编译错误。

`Son.cpp`

```c++
#include "Parent.h"

Son::Son(Parent* ptr)
{
     parentPtr = ptr;      // 构造子对象时，将父对象的指针保存
}

Son::~Son() {}

int Son::getParentA()
{
    return parentPtr->a;   // 通过指向父类对象的指针获取到父对象内的成员变量a
}
```

# References

- [C++ 两个包含类互相调用彼此的类成员变量和方法](https://blog.csdn.net/qq_27278957/article/details/77967672) 