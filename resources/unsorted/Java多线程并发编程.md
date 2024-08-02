# Java多线程并发编程

jvm是一个操作系统进程，jvm中的线程和操作系统线程一一对应，进程：一个进程包括由操作系统分配的内存空间，包含一个或多个线程**。**一个线程不能独立的存在，它必须是进程的一部分。一个进程一直运行，直到所有的非守护线程都结束运行后才能结束




















## 线程隔离（ThreadLocal）

```java
/* ThreadLocal values pertaining to this thread. This map is maintained
 * by the ThreadLocal class. */
ThreadLocal.ThreadLocalMap threadLocals = null;

/*
 * InheritableThreadLocal values pertaining to this thread. This map is
 * maintained by the InheritableThreadLocal class.
 */
ThreadLocal.ThreadLocalMap inheritableThreadLocals = null;
```

https://www.zhihu.com/question/341005993

https://www.pianshen.com/article/9758535220/



## Java线程性能优化

​	线程并发越多越好，创建线程占用内存资源，线程调度占用CPU资源

[性能优化 | 巧妙使用LongAdder替换AtomicLong (qq.com)](https://mp.weixin.qq.com/s/ZPUH9FQaR1RjkOY_5s1woA)



## 其他

java线程是jvm调度还是操作系统调度

