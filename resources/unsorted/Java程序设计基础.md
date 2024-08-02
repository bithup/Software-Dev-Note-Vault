# 第一章   Java语言简介

## 1.发布时间
- Java之父：James Gosling
- 最初设计目的是为机顶盒这种嵌入式设备，为了平台无关性，使用虚机器码，语言开发阶段将设计目的扩展到万维网应用。
- 1994年Java 1.0a提供下载
- 1995年正式公开发布，诞生
- 1996年初，Java 1.0版正式发布
- 1997.02.18，JDK1.1发布
- 2000.05.08，JDK1.3发表
- 2000.05.29，JDK1.4发布
- 2004.09.30，Java SE 5.0
- 2005.06，Java SE 6.0发布
- 2006.11.13，sun开始开源Java SE 6终版
- 2009年，sun被oracle收购
- 2011.07，Oracle发布Java SE 7
- 2014.03，Oracle发布Java SE 8
- 2017， Java SE 9
- 2017， Java SE 10

- 

## 2.Java版本与新特性
***Java 1.0***

- JDBC
- 内部类
- Java Bean
- Remote Method Invocation
- 反射

***Java 1.2***

- 集合框架
- 字符串常量
- Just In Time编译器
- Swing，Java 2D类库

***Java 1.3***

- 声音API
- jar文件索引

***Java 1.4***

- XML处理
- 打印
- Logging API
- JDBC 3.0
- 断言
- Preferences API
- 链式异常处理
- IPv6
- 正则
- Image I/O

***Java SE 5.0***

- 泛型
- 增强for循环
- 自动装箱和拆箱
- 类型安全的枚举
- 可变参数
- 静态引入
- 注解
- Instrumentation

***Java SE 6***

- 支持脚本语言
- JDBC 4.0 API
- Java Compiler API
- 可插拔注解

***Java SE 7***

- switch字符串

- 泛型对象类型推断
- catch多个异常
- 动态语言支持
- try-with-resources
- NIO.2
- 数值用二进制字符串表示

***Java SE 8***

- lambda
- 改进日期与时间API
- Functional接口
- 接口默认方法和静态方法
- 方法引用

***Java SE 9***

***Java SE 10***

***Java SE 11***



## 3.Java官方组织网站

- [JCP官网](https://jcp.org/en/home/index)
- [OpenJDK](http://openjdk.java.net/)



# 第二章   Java基础

## 1.Java关键字

### 1.1 分类	

- **基本类型**：double、**boolean**、byte、float、long、char、int、short
- 类和接口：class、interface、extends、implements、new、instanceof、package、import、enum
- 访问权限：public、private、protected
- 异常处理：try、catch、finally、throw、throws
- 流程控制：do、while、if、else、for、continue、break、switch、case、default、return、assert  
- 类型修饰符：static、final、native、abstract、synchronized、volatile、strictfp、transient
- 引用：this、super、void
- 保留：goto、const

### 1.2 不常用

| key word     | description                                  |
| ------------ | -------------------------------------------- |
| default      | switch语句中的默认分支                       |
| native       | 表示方法用非java代码实现                     |
| synchronized | 表示同一时间只能由一个线程访问的代码块       |
| volatile     | 标记字段可能会被多个线程同时访问，而不做同步 |
| strictfp     | 浮点数比较使用严格的规则                     |
| transient    | 修饰不要序列化的字段                         |
| assert       | 断言条件是否满足                             |

### 1.3 小结

- 一共有50个关键字，其中const和goto没有使用
- 1.2版本增加strictfp，1.4版本增加asser，5.0版本增加enum
- null/true/false不在关键字列表中，但不能用作标识符
- default是与switch语句相关的关键字，那默认的权限修饰符与default有管吗
- sizeof不是Java关键字，怎么实现sizeof的功能
- Java中对null，void，true，false是如何处理的
- [Java SE官方文档](https://docs.oracle.com/javase/10/)
- [oracle官方tutorial关键字列表](http://docs.oracle.com/javase/tutorial/java/nutsandbolts/_keywords.html)

## 2.Java标识符

- 类名、接口名、方法名、变量名
- 只能包含字母、数字、下划线、**$**和**￥**组成，不能以数字开头，长度不限，区分大小写
- 一个类文件可以包含多个class，只有一个public class
- main函数的修饰符public static、返回值void、参数String[] args
- 变量名首字母大写和class首字母小写都是可以的，而且class首字母小写，创建对象时  
  对象名和类名相同不会报错;但是Bean的属性名首字母大写，在生成构造函数以及setter  
  和getter方法时会出错；
- 变量声明为public static final时，变量名建议使用全大写

## 3.Java数据类型

### 3.1 基本类型

| type    | space    | range        | default  |
| ------- | -------- | ------------ | -------- |
| byte    | 1        | -128~127     | 0        |
| short   | 2        | -2^15~2^15-1 | 0        |
| char    | 2        | -2^15~2^15-1 | '\u0000' |
| int     | 4        | -2^31~2^31-1 | 0        |
| float   | 4        | 3.4028235E38 | 0.0F     |
| long    | 8        | -2^63~2^63-1 | 0        |
| double  | 8        | IEEE754      | 0.0D     |
| boolean | not sure | true false   | false    |

### 3.2 数值类型隐式与显式转换

- boolean类型不能转换
- byte,short,char到int到float到long到double自动转换
- byte,short,char之间不能自动转换
- 浮点数转换整数的方式是去掉小数点
- 字节数少的类型转字节数多的类型，自动转换
- byte/short/char与整数运算，默认隐式转换为int
- short与char如何转换
- long到float是自动转换吗
- float和double的小数不是绝对精确的，比较时不能直接与0比较或相减与0比较
- [为什么是-128，而不是-127](https://www.cnblogs.com/ysj4428/p/6030771.html)
- Java浮点数结构
- Javalong型长度和系统位数的关系
- Java使用UTF16编码
- boolean类型取决与使用情况和JVM具体实现，单个boolean值使用int表示，boolean数组中boolean用byte表示
- 大型稀疏矩阵使用byte数组更节约空间
- 数值包装类都继承值Number抽象类
- Java中char是Unicode编码，无宽窄字符之分
- 整数默认是int，尾部加L表示long，浮点数默认是double，尾部加f表示float
- 引用所占内存空间与系统位数有关，64位系统占8个字节
- 变量初始化与默认值：  
  变量的初始化，类的属性，在创建对象时会默认初始化，但是在方法中定义的局部变量必须先初始化，再使用

> 问：short s1 = 1; s1 = s1 + 1;有什么错?  
> short s1 = 1; s1 += 1;有什么错?  
> short x = 1; y = 2; short z = x + y;有什么错？  
> 答：因为给short赋值时并没有像float那样使用f，明确指出类型；  
> 所以short s1 = 1; s1 += 1;这两个
> 表达式右边的 1 都会被当成int16；所有不会报错；
> 而s1 = s1 + 1右边的表达式计算的结果是int32的所以报错。
> x + y表达式计算的值默认是int32，所以会出错。

### 2.3 引用类型

- 引用类型包括：类、接口、数组、枚举
- 字符数组与字符串的区别
- 数组是一个对象，是哪个类的对象
- 底层结构
- 占用内存
- 一个中文字符在内存中占 `fdfd` 几个字节
- 引用类型的类型转换
- 枚举

```java
enum Color {
    RED(1, "红", 1),BLUE(2, "蓝", 2),YELLOW(1, "黄");
    int i;
    String s;
    int i1;

    Color(int i, String s, int i1) {
        this.i = i;
        this.s = s;
        this.i1 = i1;
    }

    Color(int i, String s) {
        this.i = i;
        this.s = s;
    }



}
```



## 4.Java运算符

### 1.优先级和结合性

| priority | operator                                   | associative |
| -------- | ------------------------------------------ | ----------- |
| 1        | () . []                                    | 左到右      |
| 2        | ! + - ~ ++ --                              | 右到左      |
| 3        | * / %                                      | 左到右      |
| 4        | + -                                        | 左到右      |
| 5        | << >> >>>                                  | 左到右      |
| 6        | < <= > >= instanceof                       | 左到右      |
| 7        | == !=                                      | 左到右      |
| 8        | &                                          | 左到右      |
| 9        | ^                                          | 左到右      |
| 10       | \|                                         | 左到右      |
| 11       | &&                                         | 左到右      |
| 12       | \|\|                                       | 左到右      |
| 13       | ?:                                         | 右到左      |
| 14       | = += -= *= /= %= &= \|= ^= ~= <<= >>= >>>= | 右到左      |

### 2.例子

```java
public class Demo {
    public static void main(String[] args){
      int i = 0;
      int j = i++;
      int k = --i;
      
      int i = 0;
      int j = i++ + ++i;
      int k = --i + i--;
      
      int i = 0;
      int k = ++i--;
      /*自增与自减运算符还遵循以下规律：
         1. 可以用于整数类型byte、short、int、long，浮点类型float、double，以及字符串类型char。
         2. 在Java5.0及以上版本中，它们可以用于基本类型对应的包装器类
            Byte、Short、Integer、Long、Float、Double、Character。
         3. 它们的运算结果的类型与被运算的变量的类型相同。
      */
      
      //赋值运算符    
      int a=0;
      a+=a-=a=9;
      
      //连续赋值，=向右结合，自右向左，所以下面表达式等价于a = (b = c)
      //结合可简单理解为给表达式加上（），自右向左就是加括号或执行的顺序；
      int a=b=c;
      
      
      //条件运算符
      int x = 3;  
      int y = 2;  
      int z = x > y ? 100 : ++y > 2 ? 20 : 30; 
    }
}
```

- 问：运算符为什么需要有优先级和结合性？  
  答：如果所有的变量和常量的左边  
  或右只有一个运算符，就不需要优先级；如果一个变量的左右都有运算符，就需  
  要使用优先级判断先执行那个表达式。如果一个变量被两个表达式共享，而且变  
  量左右的运算符优先级相同，就需要使用优先级判断先执行那个表达式。
- 考虑结合性是在相邻的两个运算符优先级相同的情况下
- 大多数运算符结合性都是自左向右；括号，点号，四则运算，  
  关系运算（等于，大于小于等），逻辑与，逻辑或
- 逻辑与会短路，按位与不会短路，Java中使用逻辑与，如果左边  
  表达式为false，则不会计算右边表达式；如果使用按位与，两边表达式都要计算。
- c语言中没用bool值，Java语言中有。Java中按位与运算两边的表达式计算的值是数值型还是bool型。
- 结合性自右向左：赋值运算符，常见的单目运算符，逻辑非，按位取反，条件运算符

## 5.Java流程控制语法

### 1.循环结构

- for、增强for
- while
- do while
- 递归

### 2.分支结构

- if else
- switch case
- assert

## 6.泛型

什么是泛型，泛型的作用（优点）

# 第三章   输入输出流

## 1.BIO

### 1.1 字节流

***InputStream***

- 抽象类，实现Closeable接口

- 有抽象方法read()，读取下一个字节，返回int型，无数据可读返回 -1；

- 问：为什么能用-1作为文件结束标志？

- IOException和IOError的区别？

- read()方法读取一段数据到一个字节数字，循环读取数组长度个字节，每次读取都需要将int强转
  为byte。如果数组长度为0，返回0，如果读到文件尾返回-1，如果读取到数组长度个字节或中途
  读取到文件尾，返回已读取到字节长度。

- 拥有子类
  ByteArrayInputStream：字节数组输入流
  ObjectInputStream：引用类型输入处理流（对象反序列化）
  PipedInputStream
  StringBufferInputStream
  FilterInputStream：

  ​        BufferedInputStream：字节输入缓冲流
  ​        DataInputStream：数据类型输入流（基本类型+String）

  SequenceInputStream
  FileInputStream：文件输入流

- 上述子类用在不同的输入源，都是以字节为单位读取

- StringBufferInputStream已过时，建议使用StringReader

***OutputStream***

- 缓冲流

### 1.2 字符流

***Reader***

- 抽象类，实现Readable、Closeable接口
- 有抽象方法read()，读取一段字符到字符数组，返回int型，以-1作为文件尾标志 
- 转换流InputStreamReader

***Writer***

- 转换流OutputStreamWriter
- 缓冲流BufferedWriter，默认缓冲区大小8192Byte，8kb.

> 总结：1、FileReader继承了InputStreamReader，这两个类基本没有区别。InputStreamReader将输入的字节流转换成字符流，可指定编码格式。2、缓冲流就是在流对象中设置一个缓冲区，输入缓冲流先从输入源将数据读取到缓冲区，调用read方法时，从缓冲区取出一个字节或字符返回。3、BufferedReader流有readLine方法。4、读取字符文件，也可使用字节流读取字节数组，然后使用String或StringCoding类进行解码。5、字节缓冲流BufferedInputStream/BufferedOutputStream继承了FilterInputStream/FilterOutputStream，字符缓冲流BufferedRead/BufferedWriter没有继承FilterReader/FilterWriter。

问：字节缓冲流BufferedInputStream/BufferedOutputStream为什么继承FilterInputStream/FilterOutputStream？

> 大端字节模式，数据的高位存储在低地址处，CPU从低地址向高地址顺序读取时，先读到数据的高位。小端字节模式反之。



## 2.NIO

### 2.1 Linux IO 模型

- 阻塞：线程一直等待接收数据
- 非阻塞：调用recvfrom，无数据就返回EWOULDBLOCK，反复如此
- I/O复用：select/poll，select顺序扫描f d是否就绪。基于事件驱动的epoll更好。
- 信号驱动：通过系统调用sigaction，立即返回，有数据时，系统为该进程生成SIGIO信号。
- 异步I/O：告知内核读取数据，并在读取完成后通知进程。与信号驱动的区别是，信号驱动是在数据可读取时通知，异步时在数据读取完成（数据从内核复制到进程缓冲区）后通知。
- select和epoll的比较：1.epoll支持打开更多的socket描述符，2.在打开了很多socket，但只有少部分活跃的情况下，e poll效率更高，epoll中活跃的socket会主动调用callback函数。
- nio不支持文件的异步读写，jdk1.7升级NIO，提供AIO库支持文件异步操作。
- NIO只能算使用多路复用技术的非阻塞IO，AIO才是基于事件驱动的异步IO

### 2.2 核心类&概念

- Channels：FileChannel/DatagramChannel/SocketChannel/ServerSocketChannel
- Buffers：ByteBuffer/CharBuffer/DoubleBuffer/FloatBuffer/IntBuffer/LongBuffer/ShortBuffer
- Selectors：在单线程中处理多个Channel

### 2.3 Channel

- Channel既可读也可写
- 可以异步读写
- 数据总是读到buffer或从buffer写入
- Channel可分为两类：SelectableChannel(网络)和FileChannel(文件)

### 2.4 Buffer

- 本质是一块内存，Buffer对象提供一组方便访问该内存的方法。
- capacity
- position
- limit
- allocate方法
- put/get方法
- flip方法
- rewind方法：将position设回0
- clear/compact方法：清空所有/已读数据

### 2.5 Selector

- open方法：创建Selector
- 调用Channel的register方法注册到Selector
- 与selector一起使用时，channel必须处于非阻塞模式，FileChannel不能切换到非阻塞模式。
- 四种不同类型事件：
  Connect，channel成功连接到另一个服务器
  Accept，server socket channel准备好接受新连接
  Read，有数据可读
  Write，等待写数据

### 2.6 代码实例

```java
package com.jettech;

import java.io.*;

public class Main {

    public static void main(String[] args) throws Exception {
	// write your code here
        String s = "/Users/bithup/Desktop/test";
        String d = "/Users/bithup/Desktop/test1";
        copyDir(s, d);

    }

    public static void copyFile(String srcPath, String destPath) {
        BufferedInputStream bis = null;
        BufferedOutputStream bos = null;
        byte[] buf = new byte[1024];
        try {
            bis = new BufferedInputStream(new FileInputStream(srcPath));
            bos = new BufferedOutputStream(new FileOutputStream(destPath));
            int len;
            while ((len = bis.read(buf)) > -1) {
                bos.write(buf, 0, len);
            }
            bos.flush();
        } catch (Exception e) {
            e.printStackTrace();
        } finally {
            try {
                bis.close();
                bos.close();
            } catch (Exception e) {
                e.printStackTrace();
            }

        }

    }

    public static void copyDir(String srcPath, String destPath) throws Exception {
        File src = new File(srcPath);
        File dest = new File(destPath);
        if (!src.exists() || !src.isDirectory()) {
            throw new IllegalArgumentException("源文件不存在或不是文件夹！");
        }
        if (!dest.exists()) {
            dest.mkdir();
        }
        File[] files = src.listFiles();
        for (File f : files) {
            if (f.isFile()) {
                copyFile(srcPath+File.separator+f.getName(), destPath+File.separator+f.getName());
            } else if (f.isDirectory()) {
                copyDir(srcPath+File.separator+f.getName(), destPath+File.separator+f.getName());
            }
        }
    }
}

```

# 第四章   集合

## 1.Set（集合）

- 元素无序不重复，可以为null
- hashCode返回元素相加结果
- HashSet：无序、非同步、可为null
- LinkedHashSet：根据hashcode值觉得元素存储位置，按元素添加顺序访问。链表保证元素顺序，哈希表保证唯一
- TreeSet：红黑树，有序，可用于排序



## 2.Queue（队列）

- 元素先进先出
- LinkedList：链表底层，查询慢，增删快，不同步，效率高
- Pop/push/peek
- BlockingQueue
- Deque
- add()和offer()方法都是用来向队列添加元素，如果容量已满，add()会抛出异常，offer()会返回false。
- remove()和pull()方法都是用来从头部删除元素，如果队列为空，remove()会抛出异常，pull会返回null。

## 3.List（列表）

- 元素有序
- Vector：矢量列表（ArrayList的早期实现，线程安全），数组底层，随机查询快，增删慢，同步，效率低
- Stack：继承Vector，先进后出。
- ArrayList：数组底层，随机查询快，增删慢，不同步，效率高，初始容量是10，扩容50%
- List 不能强转为ArrayList，但是可以使用ArrayList的构造方法转化



## 4.Map

### 4.1 HashMap

- HashMap：key和value都允许null，不同步，父类是AbstractMap
- LinkedHashMap
- WeakHashMap
- HashTable：不允许null，同步，父类是Dictionary

### 4.2 TreeMap

- TreeMap
- IdentityHashMap

```java
package com.jettech;

import java.util.*;
import java.util.function.BiConsumer;

public class Main {

    public static void main(String[] args) throws Exception {
	// write your code here
        TreeSet<Integer> ts = new TreeSet<>();
        ts.add(3);
        ts.add(4);
        ts.add(2);
        Iterator<Integer> ii = ts.iterator();
        while (ii.hasNext()) {
            System.out.println(ii.next());
        }

        HashMap<String, Object> hm = new HashMap<>();
        hm.put("aa", "aa");
        hm.put("bb", "bb");

        Iterator<Map.Entry<String, Object>> it = hm.entrySet().iterator();
        for (Map.Entry<String, Object> so : hm.entrySet()) {
            so.getKey();
            so.getValue();
        }
        

    }
}
```



## 5.其他

### 5.1 Collections

- 是一个包装类，包含有各种有关集合的静态操作，不能实例化。
- sort()：自然排序
- shuffle()：随机置换
- reverse()：反转
- fill()：用某个值替换集合中所有元素，可用于初始化集合
- copy()：复制，覆盖对应索引元素
- min/max：比较，可以使用自定义比较器
- rotate：元素向后移动，旋转
- swap：交换元素位置
- binarySearch：查找元素索引
- 其他

### 5.2 Arrays

- 将数组转换为List，使用Arrays.asList()
- 将List转换为数组，使用List的toArray()
- 



# 第五章   多线程

## 1.简介

进程：一个进程包括**由操作系统分配的内存空间**，**包含一个或多个线程**。**一个线程不能独立的存在，它必须是进程的一部分**。**一个进程一直运行，直到所有的非守护线程都结束运行后才能结束**



## 2.生命周期

线程的状态：***新建状态***，执行start()方法后变为***就绪状态***，执行run()方法后变为***运行状态***，运行过程中可变为***阻塞状态***，执行完后变成***死亡状态***，运行状态的线程最复杂，它可以变为就绪、阻塞、死亡三种状态



- 阻塞状态:

  如果一个线程执行了**sleep（睡眠）、suspend（挂起）**等方法，失去所占用资源之后，该线程就从运行状态进入阻塞状态。在睡眠时间已到或获得设备资源后可以重新进入就绪状态。可以分为三种：

  - 等待阻塞：运行状态中的线程执行 wait() 方法，使线程进入到等待阻塞状态。
  - 同步阻塞：线程在获取 synchronized 同步锁失败(因为同步锁被其他线程占用)。
  - 其他阻塞：通过调用线程的 sleep() 或 join() 发出了 I/O 请求时，线程就会进入到阻塞状态。当sleep() 状态超时，join() 等待线程终止或超时，或者 I/O 处理完毕，线程重新转入就绪状态。

## 3.优先级

优先级是一个整数1～10，默认是5，优先级高的一般会先执行，但优先级不能保证执行顺序

## 4.API

### 4.1 创建线程的方式

- Runnable
- Thread，本质上是实现Runnable接口
- Callable和Future
- 启动一个线程，必须通过创建Thread类对象，并调用start方法，直接运行run方法不是多线程
- 实现Runnable和继承Thread的优缺点：继承Thread类不够灵活，因为Java只能单继承。

| **序号** | **方法描述**                                                 |
| -------- | ------------------------------------------------------------ |
| 1        | **public void start()** 使该线程开始执行；**Java** 虚拟机调用该线程的 run 方法。 |
| 2        | **public void run()** 如果该线程是使用独立的 Runnable 运行对象构造的，则调用该 Runnable 对象的 run 方法；否则，该方法不执行任何操作并返回。 |
| 3        | **public final void setName(String name)** 改变线程名称，使之与参数 name 相同。 |
| 4        | **public final void setPriority(int priority)**  更改线程的优先级。 |
| 5        | **public final void setDaemon(boolean on)** 将该线程标记为守护线程或用户线程。 |
| 6        | **public final void join(long millisec)** 等待该线程终止的时间最长为 millis 毫秒。 |
| 7        | **public void interrupt()** 中断线程。                       |
| 8        | **public final boolean isAlive()** 测试线程是否处于活动状态。 |

静态方法

| 1    | **public static void yield()** 暂停当前正在执行的线程对象，并执行其他线程。 |
| ---- | ------------------------------------------------------------ |
| 2    | **public static void sleep(long millisec)** 在指定的毫秒数内让当前正在执行的线程休眠（暂停执行），此操作受到系统计时器和调度程序精度和准确性的影响。 |
| 3    | **public static boolean holdsLock(Object x)** 当且仅当当前线程在指定的对象上保持监视器锁时，才返回 true。 |
| 4    | **public static Thread currentThread()** 返回对当前正在执行的线程对象的引用。 |
| 5    | **public static void dumpStack()** 将当前线程的堆栈跟踪打印至标准错误流。 |

代码实例

```java
package com.jettech;


import java.util.concurrent.Callable;
import java.util.concurrent.FutureTask;

public class Main {

    public static void main(String[] args) throws Exception {
        //使用 Callable和Future
        FutureTask<Integer> ft = new FutureTask<>(new Callable<Integer>() {
            @Override
            public Integer call() throws Exception {
                int sum = 0;
                for (int i = 0; i < 1000; i++) {
                    sum = sum + i;
                }
                Thread.sleep(2000);
                System.out.println("sum = " + sum);
                return sum;
            }
        });
        new Thread(ft, "ft").start();
        //通过FutureTask中的get()方法获取返回值，get()方法会阻塞当前线程。
        System.out.println(ft.get());
        //new Runable接口
        Thread tt = new Thread(new Runnable() {
            @Override
            public void run() {
                System.out.println("tt");
            }
        },"tt");
        //new Thread类，重写run方法
        Thread dd = new Thread() {
            @Override
            public void run() {
                try {
                    Thread.sleep(1000);
                    System.out.println("dd");
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }
            }
        };
        dd.start();
        System.out.println("end");
    }

}
```

什么是守护线程：守护线程可以在创建它的父线程结束后继续执行，直到进程退出。

守护线程的生命周期

interrupt：打断线程

join：可使线程强制运行，运行期间其他线程无法运行



suspend：线程对象方法，使线程暂停，已过时，暂停时不会释放已占有的锁，使用resume()方法调起线程。

wait:线程对象方法，使线程暂停，暂停时会释放已占有的锁，使用notify()或notifyAll()方法调起线程

yield：将CPU资源让给其他线程

sleep：休眠线程，使线程进入阻塞状态

Stop/destroy：方法已过时，使线程变为死亡状态。

## 5.线程同步

### 5.1 产生线程安全的原因

- 有共享资源
- 多线程环境
- 线程同时使用共享资源

### 5.2 同步的方法

##### 同步代码块

```java
package com.jettech;

public class SaleWindow implements Runnable {
    private int ticketCount = 10;
    @Override
    public void run() {
        for (int i = 0; i < 10; i++) {
            //可随意设置锁
            synchronized (this) {
                if (ticketCount > 0) {
                    System.out.println(Thread.currentThread().getName() + "卖出" + ticketCount + "张票");
                    ticketCount--;
                    try {
                        Thread.sleep(500);
                    } catch (InterruptedException e) {
                        e.printStackTrace();
                    }
                }
            }
        }
    }
}

```



##### 同步方法

```java
package com.jettech;

public class SaleWindow implements Runnable {
    private int ticketCount = 10;

    @Override
    public void run() {
        for (int i = 0; i < 10; i++) {
            syncB();
        }
    }
    //默认固定的锁对象this
    public synchronized void syncB() {
        if (ticketCount > 0) {
            System.out.println(Thread.currentThread().getName() + "卖出" + ticketCount + "张票");
            ticketCount--;
            try {
                Thread.sleep(500);
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
        }
            

    }
}

```

##### 创建线程（注意不同线程要调用同一个对象的方法）

```java
public class Main {
		/*
		创建线程t1和t2时必须用同一个对象sw，不然，两个线程使用的就不是同一个锁。也没有资源共享
		*/
    public static void main(String[] args) throws Exception {
        SaleWindow sw = new SaleWindow();
        Thread t1 = new Thread(sw, "窗口1");
        Thread t2 = new Thread(sw, "窗口2");
        t1.start();
        t2.start();

    }

}
```



### 5.3 死锁

有两个锁对象A和B，E和F是两个线程类，E有嵌套的同步方法，先获取A锁，后获取B锁。F有嵌套的同步方法，先获取B锁

后获取A锁。当E的线程获取了A锁，还没获取B锁，此时F的线程获取了B锁，就会形成死锁。

### 5.4 同步锁和线程间通信

同步锁必须是同一把锁，锁就

线程间通信，是指两个不同的线程类（run方法是不同的），上述的多线程并发，指的是用同一个线程类创建的线程。

线程间通信，可以用某个object对象作为锁。通过调用wait（）和notify（）方法切换线程



### 5.5 sychronized和Lock

- sychronized是关键字，Lock是接口

- sychronized无法判断是否获取锁状态，Lock可以

- sychronized自动释放锁，Lock需要在finally中使用unlock方法释放

- sychronized会阻塞线程，Lock可以不用一直阻塞下去。

- sychronized锁可重入，不可判断，非公平，Lock锁可重入，可判断，可公平

- Lock锁适合大量同步代码的问题

- ReentrantLock继承了Lock，是可重入锁

- 为什么需要Lock锁：如果获取了synchronized锁的线程要等待IO等，其他的线程也会被阻塞。

- 什么是可重入锁：比如线程类中有两个synchronized方法a和b，a中调用b，run方法中调用a
  在线程执行run方法时就需要获取同一个锁两次，因为是同一个线程，同一个锁，所以可以多次
  获取，不会死锁

- Lock用法

  ```java
  Lock lock = new ReentrantLock();
  lock.lock();//获取锁
  //tryLock() 尝试获取锁，返回true/false
  try{
    
  }catch(Exception e){
    
  }finally{
    lock.unlock(); //释放
  }
  ```

  

## 6.线程池



## 7.原子类



# 第六章   网络

## 1.TCP/IP

### 1.socket编程

- ServerSocket：创建实例监听某个端口，调用accept接收数据。
- Socket：使用IP和端口创建实例，使用getInputStream/getOutPutStream获取和发送数据。
- Socket发送和读取字符数据需要使用转换流。
- NIO中与ServerSocket和Socket对应的有SocketChannel和ServerSocketChannel

```java
package com.jettech;
import java.io.FileWriter;
import java.io.OutputStreamWriter;
import java.net.Socket;
public class Main {

    public static void main(String[] args) throws Exception {
	// write your code here
        Socket s = new Socket("127.0.0.1", 8080);
        OutputStreamWriter op = new OutputStreamWriter(s.getOutputStream());
        op.write("QUERY TIME ORDER");
        op.close();
        s.close();
    }
}
```



## 第二节 网络分层

### 1.OSI七层

| OSI七层模型 | 功能                   | 数据格式 | 对应网络协议  | TCP/IP四层模型 |
| ----------- | ---------------------- | -------- | ------------- | -------------- |
| 应用层      |                        |          | DNS、HTTP     | 应用           |
| 表达层      |                        |          |               |                |
| 会话层      |                        |          |               |                |
| 传输层      |                        |          | TCP、UDP、TLS | 传输层         |
| 网络层      |                        |          | IP、ICMP      | 网络层         |
| 数据链路层  | 负责网络寻址、错误侦测 |          | Wi-Fi         | 数据链路层     |
| 物理层      |                        |          |               |                |

protobuff





