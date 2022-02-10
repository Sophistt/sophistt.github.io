---
title: Smart Pointer in C++
date: 2020-01-04 16:52:03
tags: 
categories:
  - Programming
  - C++
---

C++ 编程中，指针一直是重要且深奥的一项，在很多时候因为指针使用不当会导致内存泄漏，产生严重后果。

## Example

如以下代码所示，在类中存在类型为指针的成员变量，如果该类**没有重新编写拷贝，赋值构造函数**，在对该类的对象进行拷贝，赋值操作时，
会调用编译器生成的默认的拷贝，赋值构造函数。此时，当执行`c2(c1)`或者`c3 = c2`时，指针`data`的地址被复制了多次，
`c1`，`c2`，`c3`各持有一份，因此在进行析构的时候便会产生内存错误。

```c++
#include <iostream>

class ErrClass {

private:
    int* data;

public:
    ErrClass(int _data):data(NULL) {
        init(_data);
        std::cout << "construct" << std::endl;
    }

    virtual ~ErrClass() {
        if (data) delete data;
        data = NULL;
    }

    void init(int _data) {
        if (data) delete data;
        data = new int(_data);
    }
};

int main() {
    
    ErrClass c1(10);
    ErrClass c2(c1);
    ErrClass c3 = c1;

    return 0;
}
```

## Solution

要解决或避免此错误的产生，方法便是在类中自定义拷贝，赋值构造函数，令该对象在进行此类操作的时候使用深拷贝。
但是，`boost`库提供了智能指针，可以方便的完成简单情况下的指针赋值，避免重复造轮子，在这里不对自定义拷贝，赋值函数进行讨论，
只讨论如何使用智能指针解决该问题。

```c++
#include <iostream>
#include <boost/smart_ptr.hpp>

using namespace boost;

class RealClass {

private:
    shared_ptr<int> ptr;

public:
    RealClass(int _data):ptr(new int) {
        set_data(_data);
        std::cout << "construct" << std::endl;
    }

    virtual ~RealClass() {}

    int get_data() const {
        return *ptr;
    }

    void set_data(int data) {
        *ptr = data;
    }

    long ptr_count() const {
        return ptr.use_count();
    }

};

int main() {
    
    RealClass c1(10);
    RealClass c2(c1);
    RealClass c3 = c2;
    
    std::cout << c1.ptr_count() << std::endl;
    std::cout << c2.get_data() << std::endl;
    c1.set_data(12);
    std::cout << c2.get_data() << std::endl;
    
    return 0;
}
```

如上面代码所示，在调用编译器自动生成的拷贝，赋值构造函数后，智能指针赋值拷贝的同时，引用计数也加1了。在调用析构函数的时候，
只有检查了指针的引用指数为0，才会调用`delete`方法删除掉内存，从而保证不会出现内存报错。

## Warning

但是，`shared_ptr`也有无法解决的问题：不能管理循环引用的对象。

```c++

#include <iostream>
#include <string>
#include <boost/lexical_cast.hpp>
#include <boost/smart_ptr.hpp>

using namespace boost;

class Parent;
class Child;

typedef boost::shared_ptr<Parent> parent_ptr;
typedef boost::shared_ptr<Child> child_ptr;

class Parent {

public:
    child_ptr child;
    ~Parent() {std::cout << "destorying parent\n";}
    
};

class Child {

public:
    parent_ptr parent;
    ~Child() {std::cout << "destorying child\n";}
};

int main() {
    
    parent_ptr father(new Parent());
    child_ptr son(new Child());
    
    father->child = son;
    son->parent = father;
    
    return 0;
}
```
在上面的程序中，因为`father`对象中的`child`成员指针指向了`son`对象，而`son`对象中的`parent`成员指针重新只想`father`，
导致循环引用，所以指针的引用数永远不为0,导致无法调用析构函数，因此产生了**内存泄漏**。

## Solution

为了解决此问题，只需要将其中一个类中的`shared_ptr`转换为`weak_ptr`即可。`weak_ptr`是只是提供了对管理对象的一个访问手段。
弱引用不更改引用计数，类似普通指针，只要把循环引用的一方使用弱引用，即可解除循环引用。弱引用还有两个常用的功能函数：
`expired()`用于检测所管理的对象是否已经释放，`lock()`用于获取所管理对象的强引用指针。
```c++
class Child {

public:
    boost::weak_ptr<Parent> parent;
    ~Child() {std::cout << "destorying child\n";}
};
```

# References

- [boost学习笔记4：智能指针 smart_ptr](https://blog.einverne.info/post/2015/12/boost-learning-note-4.html)