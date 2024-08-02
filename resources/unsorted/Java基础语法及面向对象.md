# 基础语法

## 1. Java关键字

### 关键字分类列表

| category | key word                                                     |
| -------- | ------------------------------------------------------------ |
| 基本类型 | double、boolean、byte、float、long、char、int、short         |
| 类和接口 | class、interface、extends、implements、new、instanceof、package、import、enum |
| 访问权限 | public、private、protected                                   |
| 异常处理 | try、catch、finally、throw、throws                           |
| 流程控制 | do、while、if、else、for、continue、break、switch、case、default、return、assert |
| 类型修饰 | static、final、native、abstract、synchronized、volatile、strictfp、transient |
| 引用     | this、super、void                                            |
| 保留     | goto、const                                                  |

### 不常用

| key word     | description                                |
| ------------ | ------------------------------------------ |
| default      | switch语句中的默认分支；Java8中default还可以作为接口默认方法修饰符 |
| native       | 表示方法用非java代码实现                             |
| synchronized | 表示同一时间只能由一个线程访问的代码块                        |
| volatile     | （挥发物，易变的）标记字段可能会被多个线程同时访问，而不做同步            |
| strictfp     | 浮点数比较使用严格的规则                               |
| transient    | （短暂的）修饰不要序列化的字段                            |
| assert       | 断言条件是否满足                                   |
| instanceof   | 判断对象是否是某个接口、类及其子类的实例                       |

### 小结

- 共有50个关键字，其中const和goto没有使用，1.2版本增加strictfp，1.4版本增加asser，5.0版本增加enum；[官方参考文档](http://docs.oracle.com/javase/tutorial/java/nutsandbolts/_keywords.html)
- default即可用于switch语句，也能用作默认权限修饰符，用作默认权限修饰符可省略；
- Java没有sizeof关键字，Java基本类型包装类都有SIZE属性，单位是位（不是以字节位单位）；

## 2. Java标识符和字面量

- Java标识符包含`类名、接口名、方法名、变量名`，只能包含字母、数字、下划线、**$**和**￥**组成，不能以数字开头，长度不限，区分大小写，`null/true/false`不在关键字列表中，但不能用作标识符；
- Java命名规范：类名首字母大写，属性名首字母小写，变量声明为public static final时，变量名建议使用全大写；
- 浮点数字面量表示方法

## 3. Java数据类型

### 基本类型

| type    | space    | range        | default  |
| ------- | -------- | ------------ | -------- |
| byte    | 1        | -128~127     | 0        |
| short   | 2        | -2^15~2^15-1 | 0        |
| char    | 2        | 0~2^16-1     | '\u0000' |
| int     | 4        | -2^31~2^31-1 | 0        |
| float   | 4        | 3.4028235E38 | 0.0F     |
| long    | 8        | -2^63~2^63-1 | 0        |
| double  | 8        | IEEE754      | 0.0D     |
| boolean | not sure | true false   | false    |

### 引用类型

引用类型包括：类、接口、数组、枚举

### 类型转换

类型转换的语法：（目标类型）原类型对象

#### 基本类型间的自动转换和强制转换

boolean类型不能转换为数值类型，整数类型（byte/short/int/long）之间或整数与浮点数之间或浮点数之间，取值范围小的可以自动转换为取值范围大的；

long类型占8个字节，float只占4个字节，但是float取值范围大于long类型，所以long可以自动转换为float，而float不能自动转换为long；

char类型不能取负数，所以char只能自动转换为取值范围包含char的取值范围的类型，且其他的数值类型都不能自动转换为char；思考：将一个负数强制转换为char类型会发送什么？

浮点数转整数，如果在改整数类型取值范围内，直接去掉小数，如果超出取值范围则取边界值；

> 问：short s1 = 1; s1 = s1 + 1;有什么错?  short s1 = 1; s1 += 1;有什么错?  short x = 1; y = 2; short z = x + y;有什么错？  
> 答：因为给short赋值时并没有像float那样使用f，明确指出类型；  所以short s1 = 1; s1 += 1;这两个表达式右边的 1 都会被当成int16；所以不会报错；而		s1 = s1 + 1右边的表达式计算的结果是int32的所以报错。x + y表达式计算的值默认是int32，所以会出错。

#### 引用类型的类型转换

```java
class A {}
class B extends A {}
class C extends A {}

A a1 = new A(); // 父类对象不能转换位子类
A a2 = new B(); // 可以转换为 B
A a3 = new C(); // 不能转换为 B
```



## 4. Java运算符

### 运算符优先级列表

| priority | operator                                                     | associative |
| -------- | ------------------------------------------------------------ | ----------- |
| 1        | () . []                                                      | 左到右      |
| 2        | ! + - （这里表示正负号）~ `++` `--`                          | 右到左      |
| 3        | * / %                                                        | 左到右      |
| 4        | + -                                                          | 左到右      |
| 5        | `<<` `>>` `>>>`                                              | 左到右      |
| 6        | < `<=` > `>=` `instanceof`                                   | 左到右      |
| 7        | `==` `!=`                                                    | 左到右      |
| 8        | `&`                                                          | 左到右      |
| 9        | `^`                                                          | 左到右      |
| 10       | `|`                                                          | 左到右      |
| 11       | `&&`                                                         | 左到右      |
| 12       | `||`                                                         | 左到右      |
| 13       | ?:                                                           | 右到左      |
| 14       | `=` `+=` `-=` `*=` `/=` `%=` `&=` `\|=` `^=` `~=` `<<=` `>>=` `>>>=` | 右到左      |

### 运算符功能分类表

| 功能     | operator      |      |
| -------- | ------------- | ---- |
| 算术运算 | +，-，++      |      |
| 赋值运算 | =，+=         |      |
| 逻辑运算 | &&，！        |      |
| 比较运算 | >，<          |      |
| 位运算   | &，！，>>，<< |      |
| 条件运算 | ? :           |      |
| 其他     | []，.，()     |      |

### 运算符优先级与结合性问题分析

运算符的优先级与结合性的作用：一个变量左右两边都有运算符，该变量会先和优先级高的运算符进行作用，如果左右两边的运算符优先级相同，就需要使用结合性判断先与哪个运算符作用；另外优先级相同的运算符，其结合性也是相同的，不会出现优先级相同结合性不一致的情况；

```java
public class Main {
    public static void main(String[] args) {
        test3();
    }

    /**
     * 自增、减运算符
     * 可以用于除boolean外的所有基本类型及其包装类（Java5.0及以上）
     * 它们的运算结果的类型与被运算的变量的类型相同，不会自动转换
     */
    public static void test1() {
        int i1 = 0; int i2 = 0;
        System.out.println(++i1); // 输出1，用在变量前，先自增后使用
        System.out.println(i2++); // 输出0，用在变量后，先使用后自增

        int j1 = 0;
        int j2 = j1++ + ++j1;
        System.out.println(j2);
        int j3 = 0;
        int j4 = ++j3 + j3++;
        System.out.println(j4);

        int k1 = 0;
        // int k2 = ++k1--;
    }

    /**
     * 赋值运算符
     */
    public static void test2() {
        // java连续的赋值运算，从右往左计算，但是右边的表达式计算后不影响左边的a的值，
        // 比如a-=a=9，在a=9运算完后，左边第一个a还是取0，但是在c语言中却是取最新的a的值
        int a=0;
        System.out.println(a+=a-=a=9); // 输出-9，c语言中输出0

        //连续赋值，=向右结合，自右向左，所以下面表达式等价于a = (b = c)
        //结合可简单理解为给表达式加上（），自右向左就是加括号或执行的顺序；
        int b=0, c=1;
        int d=b=c;
        System.out.println(b);
        System.out.println(d);
    }

    /**
     * 条件运算符
     */
    public static void test3() {
        int x = 3;
        int y = 2;
        // 先计算后面的tiao间运算符，++y, 但是++y的操作对，x>y的值没有影响
        // 所以x > y始终位true，z = 100
        int z = x > y ? 100 : ++y > 2 ? 20 : 30;
        System.out.println(z);
    }

}

```

### 运算符使用小技巧

使用&符号判断奇偶性：（length & 1) != 0



## 5. Java流程控制语法

### 循环结构

- for、增强for、foreach

```java
import java.util.ArrayList;
import java.util.List;

public class Main {
    public static void main(String[] args) {
        Integer[] integers = {1,2,3};
        // 一般for循环
        for (int i = 0; i < integers.length; i++) {
            System.out.println(integers[i]);
        }
        // 增强for
        for (Integer integer : integers) {
            System.out.println(integer);
        }
        // 实现Iterable接口foreach方法的集合，结合函数式编程
        List<Integer> integerList = new ArrayList<>();
        integerList.add(1);
        integerList.add(2);
        integerList.add(3);
        integerList.forEach(System.out::println);
    }
}
```

- while和do while

```java
public class Main {
    public static void main(String[] args) {
        Integer[] integers = {1,2,3};
        int i = 0;
        while (i < integers.length) {
            System.out.println(integers[i]);
            i++;
        }
        // do while循环
        int j = 0;
        do {
            System.out.println(integers[j]);
            j++;
        } while (j < integers.length);
    }
}
```

- 迭代器

```java
import java.util.*;

public class Main {
    public static void main(String[] args) {
        // 迭代器 + while循环
        List<Integer> integerList = new ArrayList<>();
        integerList.add(1);
        integerList.add(2);
        integerList.add(3);
        Iterator<Integer> iterator = integerList.iterator();
        while (iterator.hasNext()) {
            System.out.println(iterator.next());
        }
        Map<String, Integer> map = new HashMap<>();
        map.put("a", 1);
        map.put("b", 2);
        map.put("c", 3);
        // 遍历键集合
        for (String key : map.keySet()) {
            System.out.println(key);
        }
        // 遍历值集合
        for (Integer val : map.values()) {
            System.out.println(val);
        }
        // 增强for遍历Entry集合
        for (Map.Entry<String, Integer> entry : map.entrySet()) {
            System.out.println(entry.getKey() + " : " + entry.getValue());
        }
        // iterator遍历Entry集合
        Iterator<Map.Entry<String, Integer>> entryIterator = map.entrySet().iterator();
        while (entryIterator.hasNext()) {
            Map.Entry<String, Integer> entry = entryIterator.next();
            System.out.println(entry.getKey() + " : " + entry.getValue());
        }
    }
}

```

- 递归

```java
public class Main {
    public static void main(String[] args) {
        System.out.println(factorial(10));
        System.out.println(fibonacci(3));
        System.out.println(fibonacciUsingWhile(47));
    }

    // 递归求斐波那契数列，非常慢
    public static Long fibonacci(long n) {
        if (n <= 2) {
            return 1L;
        } else {
            return fibonacci(n - 1) + fibonacci(n - 2);
        }
    }

    // while求斐波那契数列，很快
    public static Long fibonacciUsingWhile(long n) {
        long a = 1, b = 1, c = 1;
        while (n > 2) {
            c = a + b;
            a = b;
            b = c;
            n--;
        }
        return c;
    }

    // 递归求阶乘
    public static long factorial(long i) {
        if (i == 1) return 1;
        return i * factorial(i-1);
    }
}

```



### 分支结构

- if/else

- switch case

```java
// 不支持Long类型
switch(expression){
    case value :
       //语句
       break; //可选
    case value :
       //语句
       break; //可选
    //你可以有任意数量的case语句
    default : //可选
       //语句
}
```

- assert：需要手动开启断言，断言失败抛出`AssertionError`；



### 顺序结构

- return后的代码不可达
- try catch finally中的代码执行顺序及return是否生效
  - try块中有return语句，那么finally正常执行
  - try块中有return，finally中没有return，但是finally中修改了try中要返回的变量，最终的return的值不受finally代码影响
  - try块和finally块中都有return，最终返回的是finally中return的值


# 面向对象

## 1. 面向对象

面向对象的特点：

- 继承
- 封装
- 多态：父类或接口引用指向子类对象，方法的重载和重写



Java中所有类的都基础Object类

- static native void registerNative();加载类时在静态代码块中执行
- native Class<?> getClass();
- native int hashCode();
- boolean equals(Object);
- native Object clone();
- String toString();
- native void notify();
- native void notifyAll();
- native void wait(long);
- void wait(long, int);
- void wait();
- void finalize();被垃圾回收器调用，该方法没有方法体



Java中所有类的.class，都是Class类的实例，该实例包含对应类的包名、类名、属性、方法、访问权限等信息



```java
public class Main {
    public static void main(String[] args) {
        Class clazz = A.class;
        Object obj = new Object();
        System.out.println(clazz instanceof Object);
        System.out.println(Object.class instanceof Class);
        System.out.println(obj instanceof Class);
		System.out.println(Class.class instanceof Class);
    }
}
```



Class类中判断类的类型（枚举、注解、合成类型）的方法，synthetic表示合成类型，是由编译器创建的。

## 2. Java中的访问权限

public：最大访问权限

protected：本类和子类可以访问，子类可以在其他包下

default：包访问权限，其他包下的类包括其子类都不能访问

private：只有本类可以访问，子类不能访问，跨包不能访问



## 3. Java内部类

### 外部类和内部类

1. 外部类：定义在.java文件中，一个java文件中可以定义多个类，只有一个public的
2. 内部类：
   1. 成员内部类、
   2. 静态内部类：
      1. 如果是public的，和外部类类似，可以访问外部类的private属性
      2. 外部类中的方法可以直接访问静态内部类的private属性
   3. 局部内部类：
      1. 静态方法局部类：只能访问外部类静态属性
      2. 非静态方法局部类：可以访问外部类实例属性和静态属性
   4. 匿名内部类：通过new接口或抽象类创建匿名内部类对象，然后直接调用



类中可以定义：内部类和静态内部类，内部类存在外部类的实例中

方法中可以定义：局部类（静态方法或普通方法）

### 匿名内部类



```java
public class AnonymousDemo1
{
    public static void main(String args[])
    {
        new AnonymousDemo1().play();
    }

    private void play()
    {
        Dog dog = new Dog();
        Runnable runnable = new Runnable()
        {
            public void run()
            {
                while(dog.getAge()<100)
                {
                    // 过生日，年龄加一
                    dog.happyBirthday();
                    // 打印年龄
                    System.out.println(dog.getAge());
                }
            }
        };
        new Thread(runnable).start();

        // do other thing below when dog's age is increasing
        // ....
    }
}

```

上面的代码，在函数play()中创建了匿名内部类（也可使用lambda表达式），匿名内部类中使用了play函数中的变量dog，此时编译会提示dog变量必须是final的。

原理分析：

1. 匿名内部类会被编译器编译成单独的class文件
2. 匿名内部类中使用的dog变量会被当做构造函数参数传递给匿名内部类。
3. dog变量在虚拟机方法站中，无法被内部类直接访问，所以在创建匿名内部类对象时，会将dog的引用值拷贝到内部类中
4. 如果dog变量被重新赋值，导致dog变量和内部类中不同步



- 匿名内部类与局部类对作用域内的变量拥有相同的的访问权限
- 匿名内部类可以访问外部内的所有成员
- 匿名内部类不能访问外部类未加final修饰的变量（`注意：JDK1.8即使没有用final修饰也可以访问`）
- `匿名内部类中是否可以通过this访问外部类的属性？`
- 匿名内部类可以有常量属性（final修饰的属性）；
- 匿名内部内中可以可以有额外的方法（父接口、类中没有的方法）;
- 匿名内部内中可以定义内部类；
- 匿名内部内中可以对其他类进行实例化。

### 内部类的底层原理

[(17条消息) 为什么说Java匿名内部类是残缺的闭包_SexyCode-CSDN博客_匿名内部类 闭包](https://blog.csdn.net/hzy38324/article/details/77986095)

## 其他

一个类文件可以包含多个class，只有一个public class

- Java中对null，void，true，false是如何处理的
- 字符数组与字符串的区别

数组是一个对象，是哪个类的对象

一个中文字符在内存中占 `fdfd` 几个字节

boolean类型取决与使用情况和JVM具体实现，单个boolean值使用int表示，boolean数组中boolean用byte表示



float和double的小数不是绝对精确的，比较时不能直接与0比较或相减与0比较



[为什么是-128，而不是-127](https://www.cnblogs.com/ysj4428/p/6030771.html)





[JDK 17 Documentation - Home (oracle.com)](https://docs.oracle.com/en/java/javase/17/)

# Java各个版本的新特性

### JDK 1.0 ~ JDK 1.1.8

- JDBC
- 内部类
- Java Bean
- Remote Method Invocation
- 反射

### J2SE 1.2 ~ J2SE 1.4.2

#### J2SE 1.2

- 集合框架
- 字符串常量
- Just In Time编译器
- Swing，Java 2D类库

#### J2SE 1.3

- 声音API
- jar文件索引

#### J2SE 1.4

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

### Java SE 5.0（1.5）

#### 泛型

参考Java SE - Generic

增强for循环

自动装箱和拆箱

类型安全的枚举

#### 可变参数

使用三个点  ...  表示可变参数，可变参数是一个数组

```java
public class Main {
    public static void main(String[] args) {
        String[] nameArray = {"joby", "tom"};
        test(nameArray); // 可接收数组
        test("bob", "lucy");
        test("a", null); // 正常运行打印null
        test(null); // 运行会报空指针异常
    }

    public static void test(String... names) {
        for(String name : names) {
            System.out.println(name);
        }
    }
}
```

静态引入

注解

Instrumentation

### Java SE 6.0（1.6）

- 支持脚本语言
- JDBC 4.0 API
- Java Compiler API
- 可插拔注解

### Java SE 7.0（1.7）（2011.07发布）

- switch字符串：原理是（编译阶段）将字符串转为哈希值，相当于语法糖，参考[(15条消息) java7新特性之switch字符串比较原理_青春念邵的博客-CSDN博客](https://blog.csdn.net/mo_fan_qing_wa/article/details/51823290)

- 泛型对象类型推断
- catch多个异常
- 动态语言支持
- try-with-resources
- NIO.2
- 数值用二进制字符串表示

### Java SE 8.0（1.8）（2014.03发布）

lambda

改进日期与时间API

Functional接口

接口默认方法和静态方法

#### 方法引用

使用  :: 符号表示方法引用，例如Integer::sum

### Java SE 9~11

### Java SE 12~17

[Java17的新特性 - 掘金 (juejin.cn)](https://juejin.cn/post/7008186720906641438)

