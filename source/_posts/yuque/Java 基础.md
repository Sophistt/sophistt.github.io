---
title: Java 基础
urlname: moefchr9cq75w2fc
date: '2023-01-28 15:34:43 +0800'
tags: []
categories: []
---

## 基本数据类型

- byte：字节，1 byte，8 bits （-128~127）
- char：字符，2 bytes，如 '\n', '\u0012', '我'
  - 字符是字节通过不同编码的包装
  - 字符向字节转换时，需要注意编码问题
- short：短整型，2 bytes
- int：整型，4 bytes
- long：长整型，8 bytes
- float：短浮点型，4 bytes
- double：浮点型，8 bytes （默认浮点）

类型转换：低到高
低 --------------------------------------------------> 高
byte,short,char—> int —> long—> float —> double

- 注意 final 修饰的变量不会自动转型。

## Class and Interface

### Interface

- Interface 也可以有成员变量，默认修饰符：public static final，interface 不能有构造方法。
- Interface 可以用 public，protected，default 来修饰，不能用 private

### Class

- abstract class：用 abstract 修饰的就是抽象类，可以拥有构造方法
- extends
  - final 修饰的成员变量/方法，可以被继承，但不能被重写
  - static，private 修饰的方法，不可以被继承
  - 子类继承父类时，如果父类的构造函数有参数，则需要显式调用
  - 如果一个子类 override 了父类的某个方法，不要在父类的构造方法中调用该函数。
  - 对父类的四种操作：
    - 重写 (overload)：子类对父类接口的重写，相同的返回值和形参，使用@override 注解。
    - 隐藏 (hide)：父类同名的成员变量和静态方法只会被隐藏（静态绑定导致），只有成员方法享有动态绑定。
    - 重载 (override)：同类之间函数的不通返回值和形参
- class 的生命周期
  - 用户自定义的类：类的对象不在被引用，即被卸载
  - Java 虚拟机自带的类：保持整个 JVM 的生命周期，加载顺序：
    - Bootstrap ClassLoader：加载 %JAVA_HOME%/lib 下的所有 jar 包
    - Extention ClassLoader ：加载 %JAVA_HOME%/lib/ext 下的所有 jar 包
    - AppClassLoader：加载当前 classpath 下的所有 jar 包
- 类加载机制
  - 双亲委派模型：加载类的时候，首先委托加载父类一直到顶。
- 类加载器
  - 自定义类加载器复写`getPermissions()`方法的时候，需要调用父类的`getPermissions()`方法来获取默认系统规则。否则：
    - 该自定义类加载器加载的类具有的权限就会完全独立于系统全局策略文件规定的权限
    - 该类的权限覆盖了这些系统全局文件规定的权限。
- 对象初始化顺序：依次是（静态变量、静态初始化块）>（变量、初始化块）> 构造器

## Immutable (不可变类)

**不可变类**：所谓的不可变类是指这个类的实例一旦创建完成后，就不能改变其成员变量值。如 JDK 内部自带的很多不可变类：Boolean, Byte, Character, Double, Float, Integer, Long, Short, String, BigDecimal。
**可变类**：相对于不可变类，可变类创建实例后可以改变其成员变量值，开发中创建的大部分类都属于可变类。

## 集合

#### Size and capacity extension

- Initial Capacity：ArrayList(10), Vector(10), StringBuilder(16), StringBuffer(16), HashMap(16), HashSet(16)，HashTable(11)
- 动态扩容
  - HashTable = 2 \* size + 1
  - 2 \* size : HashMap, vector, Stack, LinkedHashMap
  - 1.5 \* size : ArrayList

#### List and Arraye

##### List

sublist()方法生成的子 list 与原 list 存在关联

- LinkedList
  - 迭代的时候使用 remove 方法会导致迭代出现预期之外的情况

##### Array

数组复制：System.array.copy (效率最高)

#### Map

- HashMap
  - 不可保证元素的顺序，key，value 均可为 null。
- LinkedHashMap
  - 调用 get() 方法会打乱 keySet 的顺序，将访问的 key 放到最后
  - 有 removeEldestEntry 方法，可以简单控制 map size
- TreeMap
  - key 不可以为 null，value 可以。
- ConcurrentHashMap
  - key, value 都不可以为 null
  - 线程安全且全程不需要手动加锁，Map 中效率最高。
- 注：HashTable 中的 key, value 也都不可以为 null

## Reflection

反射机制允许程序获取指定对象的类的相关方法与字段。

- getDeclaredFields()：获取所有声明的成员变量，包括 private
- getFields()：获取所有的 public 变量，包括父类的 public 变量。
- getDeclaredMethods(): 获取所有声明的成员方法。
- getDeclaredConstructors(): 获取所有构造函数。

## 注解 （Annotation）

### 元注解 （java.annotations）

@Documented ：该注解标记的内容会被 JavaDoc 工具提取成文档
@Target ：用于注解一个注解的使用范围，用成员变量 value 来指定范围
![image.png](https://cdn.nlark.com/yuque/0/2023/png/34997132/1675699015804-e6bc1468-16c1-4601-9bcd-e3a4ee4717d5.png#averageHue=%23f2f2f2&clientId=u6a707b01-3786-4&from=paste&height=293&id=ue809fee9&name=image.png&originHeight=586&originWidth=894&originalType=binary∶=1&rotation=0&showTitle=false&size=76157&status=done&style=none&taskId=ucb5f28f2-9757-4445-8b53-2bb833eec56&title=&width=447)
@Rentation 用于描述注解的生命周期

- SOURCE: 仅在源文件中有效
- CLASS：在 Class 文件中有效
- RUNTIME：在运行中有效

@Inherited 表示该注解可以被继承
@Native 修饰成员变量，表示该变量可以被本地代码引用

### 其他注解（java.lang）

- @Override
- @Deprecated
- @SuppressWarnings

### 自定义注解

可以开发一个注解类，但是需要使用 @interface 关键字来声明。

## Exception 异常

### 敏感异常

- InsufficientResourcesException：服务器资源不足，可能会造成 dos 攻击
- OutOfMemoryError：dos 攻击
- StackOverflowError：dos 攻击
- JarException：泄露文件系统结构
- FileNotFoundException：泄露文件系统结构和文件名列举
- SQLException：暴露数据库结构，并造成用户名列举
- MissingResourceException，造成资源列举
- NotOwnerException：所有人列举
- BindException：当不信任客户端能够选择服务器端口时造成开放端口列举
- ConcurrentModificationException：可能提供线程不安全的代码信息

### 是否受检

受检异常需要在代码中明确使用 throw 抛出异常，否则编译不通过。

- 受检：FileNotFoundException, IOException, SQLException，ClassNotFoundException, InterruptedException, NoSuchFieldException
- 非受检：NullPointerException, ClassCastException, IllegalArgumentException, ArrayIndexsOutOfBoundsException, ArithmeticException

### 异常处理

- 不要直接捕获基类 Exception 来处理异常，可以使用并语句明确指出需要捕获的异常
- 打开文件流有异常，必须进行处理(输入流，输出流，错误流)；在 finally 语句中关闭流，抛出异常时可以忽略异常，仅记录日志。
- 不要直接抛出 NullPointerException 异常，而是使用空引用判断。

## GC

垃圾回收（GC）是由 Java 虚拟机（JVM）垃圾回收器提供的一种对内存回收的一种机制，它一般会在内存空闲或者内存占用过高的时候对那些没有任何引用的对象不定时地进行回收。

### GC 使用

- 在频率/周期性的逻辑中更要尽量避免主动 GC 的调用。因为在 GC 过程中的某些阶段程序会完全停顿，这会让程序失去响应，对系统造成非常大的风险。
- 即使调用主动 GC 方法后，系统也不会立即进行垃圾回收操作。

### GC 算法

- 后台运算而不需要太多交互的任务
  - Parallel Scavenge（吞吐量优先）
- 对响应速度有较高要求的服务
  - CMS（响应速度优先）
  - G1（响应速度优先）
  - ZGC（STW 小于 10ms）

## 编译

使用 javac 进行编译时，包含以下几个过程：

- 语义分析及生成字节码
- 词法分析及填充符号表
- 注解处理

### JVM 代码优化

JVM 在对代码执行的优化可分为运行时（runtime）优化和即时编译器（JIT）优化。运行时优化主要是解释执行和动态编译通用的一些机制，比如说模版解释器、内联缓存（inline cache，用于优化虚方法调用的动态绑定）。

- JITTest.doubleIndex 被内联
- JITTest.doubleIndex 会被编译成机器码执行

## 序列化

Java 提供了一种对象序列化的机制，该机制中，一个对象可以被表示为一个字节序列用于网络传输。但是，序列化只能保存对象的非静态成员变量，不能保存任何的成员方法和静态的成员变量。

### 安全问题

- 可以通过定义 serialPersistentFields 数组字段来确保敏感字段被排除在序列化之外，除此之外，也可以通过自定义 writeObject()、writeReplace()、writeExternal()这些函数，不将包含敏感信息的字段写到序列化字节流中。
- 通过将敏感字段声明为 transient，可以使它们不包括在依照默认的序列化机制应该被序列化的字段列表中。
