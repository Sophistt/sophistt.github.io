---
title: Java 多线程
urlname: gkdpgrz5vzrtyi8y
date: '2023-01-28 15:57:02 +0800'
tags: []
categories: []
---

## 集合

### 线程安全

- HashTable, CocurrentHashMap
- Vector, Stack
- StringBuffer

### 线程不安全

- HashMap, TreeMap, LinkedHashMap
- HashSet, TreeSet, LinkedHashSet
- ArrayList, LinkedList
- StringBuilder

## 线程规范与管理

### 线程规范

- 创建新线程时指定名称
- 不要依赖线程调度器、线程优先级和 yield()方法
- 采用 Java1.5 提供新并发工具代替 wait()和 notify()

### 线程管理

- 禁用 Thread 的 run
- 避免不加控制地创建新线程，而应该使用线程池来管控资源
- 线程中断有业务代码来协作完成，慎用 Thread.interrupt
- 禁用 Thread 的 stop

## 线程同步

### 关键字

- synchronized
  - Jvm 对 synchronized 做了优化：锁消除，锁粗化，偏向锁，轻量级锁
- volatile
  - 可见性
  - 不保证原子性
  - 禁止进行指令重排序（有序性）
  - 线程不安全性
- Atomic 类：保证原子操作

### 线程同步类

- Semaphore（信号量）：控制并发线程的数量，通过配置信号量的个数，申请信号量来实现线程管理

```java
Semaphore semaphore = new Semaphore(3);

ThreadPoolExecutor poolExecutor =
        new ThreadPoolExecutor(10, 20,
                5000, TimeUnit.MILLISECONDS,
                new LinkedBlockingDeque<>(100));

for (int i = 0; i < 10; i++) {
    int finalI = i;
    poolExecutor.execute(new Thread() {
        @Override
        public void run() {
            try {
                //获取执行资格
                semaphore.acquire(1);
                System.out.println(finalI+"=========");
                //模拟每个线程运行的时间
                Thread.sleep(1000);
                //释放执行资格
                semaphore.release(1);
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
        }
    });
}
```

- CountDownLatch（计数器）：可以实现计数的功能，主要方法为 countDown() 和 await()
  - countDown() 使计数器减 1，计数器的数值无法重置。
  - await() 阻塞当前线程，直到计数器为 0。
- Exchanger
- Phaser
- CyclicBarrier

## 线程方法

- sleep()
  - 暂停线程的执行，但不释放锁。常用于一定时间内暂停线程执行。
  - 唤醒：睡眠指定时间后，线程自动苏醒。
- wait()
  - 暂停线程的执行，同时释放锁。常用于线程间的交互和通信。
  - 唤醒：通过 notify() 或者 notifyAll() 来唤醒线程。
- yield()：让出 cpu，从运行态转为就绪态
- interrupt()：不会直接中断线程，而是给目标线程发送中止信号，目标线程中断标记位设为 true。
- resume()：恢复被 suspend()挂起的线程。

## 线程中断

编写需要中止的多线程，需要使用响应 interrput 标准的库。如 Theread.sleep(), Thread.wait()。
反例：java.net.Socket 类的方法阻塞时不响应 interrupt。

- 检测到当前线程被 interrupt 后，应抛出 InterruptedException，并在 finally 或 try-with-resource 中清理执行状态
- 调用线程的 interrupt 方法，只有当线程走到了 sleep, wait, join 等阻塞这些方法的时候，才会抛出 InterruptedException

## 锁

### 死锁

- 产生原因：
  - 异常条件下没有正确释放锁
  - 两个或多个线程以不同的顺序请求和释放锁
  - 在产生阻塞的操作中持有锁

### 全局锁

- 产生原因：

可重用的变量，如常量池中的变量被用作锁，即可能导致全局锁。

### 可重入锁

定义：一个线程获取锁后不用释放，可以重复的获取一个锁 n 次，只是在释放的时候，也需要相应的释放 n 次。

- synchronized
- ReentrantLock：使用灵活，拓展性好。

## 线程池

- newFixedThreadPool: 固定且稳定的线程池，不会出现由于线程数增长而导致的系统线程资源不足的问题。
- newCachedThreadPool: 没有等待队列，任务总是通过创建或者复用线程的方式得到执行，对任务的时延友好。
- newWorkStealingPool: 适合用于很耗时的任务。
- newSingleThreadExecutor: 适合固定的单线程任务。
- newScheduledThreadPool: 适合执行周期性的任务。

## 线程异常

在某个线程运行出现异常时，遵循以下处理原则：

1. 子线程须自己捕获异常处理
2. 默认情况下，运行时异常从线程抛出时，会在控制台输出堆栈记录
3. 对于运行时异常可调用 Thread.setUncaughtExceptionHandler()方法设置运行时异常处理器来进行处理
