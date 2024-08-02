

## 基础
- JDK、JRE、JVM的区别？
- 基本数据类型（几种）和包装类，byte/int/char/long各占多少字节，64位和32位jvm中int的长度是否相等？
- byte/char的取值范围，如果用超出变量取值范围的值给变量赋值会怎样(用取值范围内的数值赋值能正常通过，用取值范围外的值赋值需要强制转换)？编译会报错吗？
- 两个对象hashcode相同，equals()比较的结果？
- 创建对象有哪些方式？new/反射/clone/反序列化
- 什么是不可变对象？String/Integer
- 注解，元注解，注解是否可以修饰注解，如何自定义注解？

> 1.仿佛到了



- 枚举是什么，有什么作用？和静态变量有什么区别？
- 类型推导
- 泛型是什么？有什么优点？在什么时候起作用？
- 静态代码块的作用？和构造函数执行的先后顺序？
- 编码格式有哪些，java采用的是哪种？
- String能被继承吗？为什么？
- String， Stringbuffer， StringBuilder 的区别
- 父类静态代变量、
  父类静态代码块、
  子类静态变量、
  子类静态代码块、
  父类非静态变量（父类实例成员变量）、
  父类构造函数、
  子类非静态变量（子类实例成员变量）、
  子类构造函数。
- 什么是隐式转换，什么是显式转换
- 什么是拆装箱
- ++i与i++的区别
- 程序的结构有那些（顺序/选择/循环）？选择和循环有哪几种？
- 数组实例化有几种方式？
- Java中各种类型数据的默认值？
- 构造方法能不能显式调用？构造方法能不能重写？能不能重载？
- 内部类与静态内部类的区别？
- String类的常用方法有那些？charAt：返回指定索引处的字符
  indexOf()：返回指定字符的索引
  replace()：字符串替换
  trim()：去除字符两端空白
  split()：分割字符串，返回一个分割后的字符串数组
  getBytes()：返回字符串的byte类型数组
  length()：返回字符串长度
  toLowerCase()：将字符串转成小写字母
  toUpperCase()：将字符串转成大写字符
  substring()：截取字符串
  format()：格式化字符串
  equals()：字符串比较
- 判断两个对象（不是指字符串对象）是否相同，能使用equlas比较吗？比如：比较两个整数类型的包装类？
- 当父类引用指向子类对象的时候，子类重写了父类方法和属性，那么当访问属性的时候，访问是谁的属性？调用方法时，调用的是谁的方法？

### 常用api，包名，类名

- Math.round();
- 字符串（常用方法）反转，截取，搜索，拼接
- 获取当前毫秒，年月日？格式化日期SimpleDateFormat?
- 获取系统参数？System.getProperty()
- 打印日志有哪些方法？
- 拼接大量的字符串怎么操作，多线程下用什么类？
- JSON解析
- POI处理Excel，处理JSON的类库，处理XML的方式和类库及优缺点？
- 退出多层循环

### 关键字和运算符

- final关键字的作用，String是否可变（为什么），final集合中的元素值是否可变？
- 权限
- 与或非，逻辑，按位，左移右移？
- 是否可以从一个static方法内部发出对非static方法的调用？
- Abstract/static/final 有哪几种组合？
- &和&&的区别？


## 面向对象编程

- 抽象类必须有抽象方法吗？
- 抽象类能用final修饰吗？
- 接口和抽象类的区别？
- 构造器Constructor是否可被override?
- 列举尽肯多的接口，以及他们的作用？
- 抽象类，内部类，匿名类，静态内部类，匿名内部类？
- new一个接口

## 集合

- Java容器有哪些？

- 数组能不能存对象？集合能不能存基本类型？

- Collection和Collections的区别？Map是否继承Collection？

- 容器还实现哪些接口，比较，序列号，遍历

- List Set Map的区别？Map继承Collection接口吗？

- ArrayList和LinkedList的区别？LinkedList是单向还是双向链表？

- ArrayList和数组的区别

- HashMap/Hashtable/TreeMap，如何选择？

- HashMap实现原理

- Iterrator怎么使用？该接口有哪些方法？hasnext/next/remove

- 单独遍历map集合的键或值？

- 线程同步的集合有哪些？

- 怎么确保一个集合不被修改

- 有没有有顺序的 Map 实现类， 如果有， 他们是怎么保证有序的。

  TreeMap和LinkedHashMap是有序的（TreeMap默认升序，LinkedHashMap则记录了插入顺序）。

- 用过哪些 Map 类，都有什么区别，HashMap 是线程安全的吗,并发下使用的 Map 是什么，他们内部原理分别是什么，比如存储方式， hashcode，扩容， 默认容量等。

- 结合 OO 设计理念，谈谈访问修饰符 public、private、protected、default 在应用设计中的作用。

- 继承Collection接口的接口有哪些？实现Collection接口集合有哪些？

## IO

- IO流分几种？字节流和字符流的区别？
- 常用IO类：File/FileInputStream/FileReader/BufferredReader
- 字节数组和String相互转换？
- BIO、NIO、AIO
- File类常用方法，判断是否存在，是否是文件夹
- 以字节流、字符流、缓冲流读取文件

## 反射

- 什么是反射？
- 哪种设计模式用到反射？如何实现动态代理？

## 异常

- 常见异常，异常分类，哪些需要捕获，如何处理？
- throw和throws？
- final/finally/finalize区别？
- try-catch-finally中返回值的先后顺序

## 网络

## 多线程

- 并发和并行的区别？
- 线程和进程的区别？
- 守护线程是什么？
- 创建线程有哪些方式？
- run()和start()的区别？
- runnable和callable的区别？
- 线程有哪些状态，什么情况下会阻塞，如何唤醒
- sleep() 和 wait() 的区别？
- notify() 和 notifyAll()的区别？
- 创建线程池的方式？
- 线程池submit和execute方法的区别？
- 线程安全是什么？什么情况是线程不安全的？如何保证线程安全（加锁）？
- ++操作符是线程安全的吗？不是
- 什么是死锁？如何避免死锁？
- ThreadLocal使用场景？
- synchronized底层实现原理？
- synchronized\volatile\Lock之间的区别？
- atomic的原理？

## 设计模式

- 常见设计模式及实现？

- 

## JVM

- jvm内存分配，分哪几块，存放什么数据？
- jvm中以什么编码存储字符串，一个字符占多少字节？
- class文件加载过程，classloader？
- GC是什么?为什么要有GC?GC算法 垃圾回收的优点和原理。并考虑2种回收机制。垃圾回收器可以马上回收内存吗？有什么办法主动通知虚拟机进行垃圾回收？

- 

### 计算机基础

- TCP，UDP，IP，HTTP协议是什么
- HTTP，HTTPS的区别
- HTTP请求，POST，GET，PUT，DELETE
- HTTP协议状态码及其含义：200/400/401/403/404/500
- cookie和session是什么
- DNS，DHCP，NAT
- IP地址分类，IP地址和子网掩码
- 常用数据结构：队列/栈/链表/树/图
- 如何实现链表结构
- 排序方式
- Windows和Linux下的常用命令

