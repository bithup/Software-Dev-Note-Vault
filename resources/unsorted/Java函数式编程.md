# Java函数式编程

## 函数式接口

> 在jdk8 --> rt.jar --> `java.util.function`包下有一系列接口，这些接口成为函数式接口

### 函数式接口注解约束

`@FunctionalInterface`用来注解函数式接口，该注解在`java.lang`包下。

函数式接口约束如下：

1. 接口有且只能有个一个抽象方法，只有方法定义，没有方法体（default方法有方法体，函数式接口可以有多个default方法）
2. 在接口中覆写Object类中的public方法，不算是函数式接口的方法

### 函数式接口的定义

> 满足`@FunctionalInterface`注解约束，但是是否被该注解注释不是必要的；

例如：

```java
//接口一
@FunctionalInterface
public interface FunctionInterfaceTest {
 
	String getInfo(String input);
	
	@Override
	String toString();  //Object中的方法
	
	@Override
	boolean equals(Object obj); //Object中的方法
}
//接口二
@FunctionalInterface
public interface FunctionInterfaceTest {
 
	String getInfo(String input);
	 
}
 
//接口三
public interface FunctionInterfaceTest {
 
	String getInfo(String input);
}
```



### 函数式接口实例的创建

1. lambda
2. 方法引用，例如`People::getName`
3. 构造方法引用

### 常用的函数式接口

| 函数式接口名     | 说明 | 使用场景               |
| ---------------- | ---- | ---------------------- |
| `Function<T, R>` |      |                        |
| `Consumer<T>`    |      |                        |
| `Predicate<T>`   |      | Optional中的filter方法 |
| `Supplier<T>`    |      |                        |
|                  |      |                        |



#### Function<T, R>

```java
@FunctionalInterface
public interface Function<T, R> {

    /**
     * Applies this function to the given argument.
     *
     * @param t the function argument
     * @return the function result
     */
    R apply(T t);
}
```

#### Consumr\<T>



#### Predicate\<T>

```java
/**
     * 接收一个参数, 判断这个参数是否匹配某种规则, 匹配成功返回true, 匹配失败则返回false
     */
boolean test(T t);
/** 
	 * default方法, 接收另外一个Predicate<T>类型参数进行逻辑与操作
	 * 返回一个新的Predicate
	 * Predicate<T> newPredicate = (t) -> this.test(t) && other.test(t);
     * 如果传入的Predicate为空, 会抛出空指针异常
     */
default Predicate<T> and(Predicate<? super T> other) {
    Objects.requireNonNull(other);
    return (t) -> test(t) && other.test(t);
}
/** 
	 * default方法, 接收另外一个Predicate<T>类型参数进行逻辑或操作
	 * 返回一个新的Predicate
	 * Predicate<T> newPredicate = (t) -> this.test(t) || other.test(t);
     * 如果传入的Predicate为空, 会抛出空指针异常
     */
default Predicate<T> or(Predicate<? super T> other) {
    Objects.requireNonNull(other);
    return (t) -> test(t) || other.test(t);
}
/**
	 * default方法, 返回当前Predicate取反操作之后的Predicate
	 * Predicate<T> newPredicate = (t) -> !test(t);
     */
default Predicate<T> negate() {
    return (t) -> !test(t);
}
/**
     * 接收一个Object targetRef, 返回一个Predicate<T>类型
     * 返回的Predicate的test方法是用来判断传入的参数是否等于targetRef
     * 如Predicate<T> predicate = Predicate.isEqual("七夜雪");
     * 等同于Predicate<T> predicate = t -> "七夜雪".equals(t);
     * 个人感觉这个方法没多大用处
     */
static <T> Predicate<T> isEqual(Object targetRef) {
    return (null == targetRef)
        ? Objects::isNull
            : object -> targetRef.equals(object);
}
```



#### Supplier\<T>

### 总结

Java stream api，函数式编程，optional，lambda等之间的关系

`->`是Java 8新增的Lambda表达式中，变量和临时代码块的分隔符，即：`(变量) -> {代码块}`。

如果代码块只有一个表达式，大括号可以省略。如果变量类型可以自动推断出来，可以不写变量类型。

`::`是类似于C++的域运算符，用来获取类方法。



函数接口@FunctionalInterface和lambda表达式

```java
interface Addition {
	int add(int a, int b);
}

public static Addition getAddition() {
	return (a, b) -> a + b; //Lambda表达式 return 语句
}

public static void main(String args[]) {
	System.out.println("a和b的相加是: " + getAddition().add(20, 50));
}

// 泛型函数接口
interface Addition<T> { T add(T a, T b)}
```



## Lambda表达式

### lambda中使用局部变量

在lambda表达式中使用的**`局部`**变量（lambda对于实例变量、静态变量不限制）必须时`final`或者`effectively final`的，

1. final：被final修饰的变量，变量引用值不能再改变
2. effectively final：没有被没有被从新赋值的变量（比如List类型可以增删元素，但不能重新赋值），实际上和final一样

```java
private void test(Integer id, List<Integer> idList) {
    // 给id重新赋值，id就不是effectively final的了，不能再lambda中使用
    if (id == null) id = 1;
    // 可以再定义一个变量，满足effectively final的条件
    Integer idFinal = id; 
    idList.stream().filter(o -> o.equals(idFinal));
}
```

原因：lambda表达式可能在另一个线程中执行，此时lambda中使用的局部变量存在的栈可能被销毁，从而导致错误。此种情况何时会发生

### lambda和匿名内部类、函数式接口实例、方法引用的异同

lambda和匿名内部类

- 所需类型不同
  - 匿名内部类：可以是接口，也可以是抽象类，还可以是具体类
  - Lambda表达式：只能是接口
- 使用限制不同
  - 如果接口中有且仅有一个抽象方法，可以使用Lambda表达式，也可以使用匿名内部类
  - 如果接口中多于一个抽象方法，只能使用匿名内部类，而不能使用Lambda表达式
- 实现原理不同
  - 匿名内部类：编译之后，产生一个单独的.class字节码文件
  - Lambda表达式：编译之后，没有一个单独的.class字节码文件。对应的字节码会在运行的时候动态生成



直接new接口或者抽象类（重写抽象方法），创建匿名内部类

Runnable接口引用指向lambda表达式

Runnable接口引用指向静态方法或类实例方法（方法引用不能通过类引用非静态方法，只能通过对象引用非静态方法）

使用方法引用时如果对象为null会直击抛出NPE，使用lambda则会在执行lambda时才会抛出NPE



通过实现Runnable接口创建线程

```java
public class RunnableDemo implements Runnable {
    void run () {
        System.out.println("hello world");
    }
}

public class Demo {
    public static void main(String[] args) {
        RunnableDemo rd = new RunnableDemo();
        Thread1 th1 = new Thread(rd, "thread1");
        Thread1 th2 = new Thread(rd, "thread2");
        th1.start();
        th2.start();
    }
}
```

通过lambda或方法引用创建线程

```java
public class A {
    void nonStaticMethod() {
        System.out.println("hello world");
    }
    
    static void staticMethod() {
        
    }
}

public class Demo {
    public static void main(String[] args) {
        A a = new A();
        Thread1 th1 = new Thread(A::staticMethod, "thread1");
        Thread1 th2 = new Thread(a::nonStaticMethod, "thread2");
        Runnable r1 = new Runnable() {
            void run() {
                System.out.println("hello world");
            }
        };
        Thread3 th3 = new Thread(r1, "thread3");
        Runnable r2 = () -> {System.out.println("hello world");};
        Thread4 th4 = new Thread(r2, "thread3");
        th1.start();
        th2.start();
        th3.start();
        th4.start();
        // 方法引用和lambda都可赋值给Runnable引用
        Runnable rn = A::staticMethod;
    }
}
```



Thread类构造函数参数时Runnable类型，传入lambda表达式和方法引用都可以。

函数式接口也可以接收lambda表达式和方法引用

Runnable接口也是函数式接口

lambda表达式中的::运算符，原理是eta转换



## Stream API



### Stream的实现原理

> 采用流的方式处理集合数据,把真正的函数式编程风格引入到Java中。可以说Lambda、函数式编程、方法引用只是代码语法上进行了优化，而Stream则是在Lambda、函数式编程、方法引用的基础之上优化了集合的操作，采用流式处理使得集合只需要经过一个遍历就可以执行大量的操作，不仅简化了集合操作而提供了操作的性能。

```java
list.stream()
    .sorted().filter(i->i.getGender()==0)
    .map(i->i.getName()).limit(3).forEach((u)->System.out.println(u));
```

如果使用循环实现上面代码的功能，该集合会被遍历多次，会生成新的临时集合保存结果；

而Stream本质是一个流水线，流水线是特点就是数据只需要遍历一次，每条数据流水线头部流下尾部并进行计算得出结果。Stream的api有如下特点：

1. Stream的操作主要分成`中间操作`和`结束操作`，中间操作只会对操作进行记录，不会实际处理数据。而结束操作会触发实际的计算操作，是一种惰性计算的方式，这是Stream高效的原因之一。

2. 中间操作又分为`有状态操作`StatefulOp和`无状态操`作StatelessOp两种，有状态操作指处理元素时必须拿到所有元素才可以处理，比如sorted()、limit()、distinct()等操作；无状态操作指处理元素时不受之前的元素影响，比如filter()、map()等操作；

3. 结束操作又分为`短路操作`和`非短路操作`两种，短路操作指达到某种条件之后就可以结束流水线，不需要处理完全部数据，比如findFirst()、findAny()等操作; 非短路操作必须处理完全部数据才能得到结果，比如collect()、max()、count()、foreach()等操作

Stream流水线中每一个操作都包含了数据源+中间操作+回调函数，可以抽象成一个完整的流程Stage，Stream流水线就是由多个Stage组成，每个Stage分别持有前一个Stage和后一个Stage的引用形成了一个双向链表。

Stream的抽象实现类是AbstractPipeline，AbstractPipeline就可以看作是一个Stage，而第一个Stage就是Head，可以通过Collection.stream()方法获取Head对象，Head也是Stream的实现类，不包含操作，只是流水线的开头。

从Head开始每执行一个中间操作都会产生一个新的Stream，Stream对象以双向链表构成，形成完整的流水线，所以这个双向链表的Stream就完整的记录了源数据和需要执行的所有操作。

参考：

[Java8--lambda表达式与函数式编程 - Lucky帅小武 - 博客园 (cnblogs.com)](https://www.cnblogs.com/jackion5/p/15025906.html)

[Java 8 的Stream流那么强大，你知道它的原理吗？ (qq.com)](https://mp.weixin.qq.com/s/fLMba0Uqag_zpm3FJQM11w)

### 基本概念与用法

#### 创建stream

1. Arrays.stream()
2. Arrays.asList().stream()
3. Stream.of()
4. Stream.iterate(0, x -> x + 1).limit(10)
5. Stream.generate(() -> "x").limit(10)
6. Collection.stream()

#### 中间操作

> 中间操作主要有以下方法（此类型方法返回的都是Stream）：map (mapToInt, flatMap 等)、 filter、 distinct、 sorted、 peek、 limit、 skip、 parallel、 sequential、 unordered。

##### peek



##### limit



##### sorted



##### reduce（聚合）



##### skip（删除）





#### 终止操作

> 终止操作主要有以下方法：forEach、 forEachOrdered、 toArray、 reduce、 collect、 min、 max、 count、 anyMatch、 allMatch、 noneMatch、 findFirst、 findAny、 iterator

#### 并行stream



### 其他

[Java8 Stream流递归，几行代码搞定遍历树形结构 (qq.com)](https://mp.weixin.qq.com/s/KCpKx1biYEzz6hDTn1mPYQ)