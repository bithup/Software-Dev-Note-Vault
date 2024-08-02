# 设计模式

## 设计模式分类

| 分类   | 特点                                               | 具体模式                                   |
| ------ | -------------------------------------------------- | ------------------------------------------ |
| 创建型 | 通过特定的方式创建特定对象的设计模式               | 工厂模式，单例模式，建造者模式，原型模式   |
| 结构型 | 为了解决某一特定问题所搭建的特定代码结构的设计模式 | 适配器模式，代理模式，桥接模式，装饰器模式 |
| 行为型 | 通过构建不同的角色来完成某一特定功能的设计模式     | 观察者模式，策略模式，                     |

## 工厂模式

### 简单工厂模式

简单工厂模式中的三种角色：

1. 抽象产品：工厂创建的对象的父类或接口，定义所有产品共有接口；
2. 具体产品：工厂创建的目标对象类，实现抽象产品类中接口；
3. 工厂：根据传入的具体产品类类型，创建产品，返回抽象产品类引用；

优点：

1. 不需要关心类的创建细节。
2. 减轻类之间的耦合依赖，具体类的实现只是依赖于简单工厂，而不依赖其他类。

缺点：

1. 扩展复杂，当简单工厂需要生产出另外一种产品的时候，需要扩展工厂的内部创建逻辑，比较有可能引起较大的故障
2. 由于工厂类集中了所有实例的创建逻辑，违反了高内聚责任分配原则，将全部创建逻辑集中到了一个工厂类中

### 工厂方法模式

工厂方法模式中的四种角色：

1. 抽象产品：工厂创建的对象的父类或接口，定义所有产品共有接口；
2. 具体产品：工厂创建的目标对象类，实现抽象产品类中接口；
3. 抽象工厂：定义创建产品的共用方法；
4. 具体工厂：实现创建具体产品的逻辑；

### 抽象工厂模式

抽象工厂中的角色：

1. 抽象工厂：定义创建产品的共用方法，与工厂方法模式的区别是，工厂方法模式只创建一类产品，只有一个创建产品的方法，而抽象工厂模式，创建多个类型的产品，有多个创建产品的方法，多个类型的产品称为一簇产品；
2. 具体工厂：实现抽象工厂中的方法；
3. 抽象产品簇：多个抽象产品
4. 具体产品：每个抽象产品有多个具体产品；



## 观察者模式&监听器模式

```java
public interface Subject {

    /**
     * 注册定义
     */
    void registerObserver(Observer observer);

    /**
     * 发送通知
     */
    void notifyObservers(Object msg);

}

public class ConcreteSubject implements Subject {

    /**
     * 观察者集合
     */
    private List<Observer> observers = new ArrayList<>();

    @Override
    public void registerObserver(Observer observer) {
        // 添加订阅关系
        observers.add(observer);
    }

    @Override
    public void notifyObservers(Object msg) {
        // 通知订阅者
        for (Observer observer : observers) {
            observer.update(msg);
        }
    }
}

public interface Observer {
    // 处理业务逻辑
    void update(Object msg);
}

public class ConcreteObserver implements Observer {

    @Override
    public void update(Object msg) {
        // 业务逻辑实现
        System.out.println("ConcreteObserver 接收到主题的消息: " + msg);
    }
}


public class ObserverTest {

    public static void main(String[] args) {
        Subject subject = new ConcreteSubject();
        Observer observer = new ConcreteObserver();
        // 注册观察者
        subject.registerObserver(observer);
        // 发布消息
        subject.notifyObservers("来自Subject的消息");
    }
}
```



```java
public class Event {
    private String data;
    private String type;

    Event(String data, String type) {
        this.data = data;
        this.type = type;
    }
    // 省略getter/setter
}

public interface Listener {
    void onClick(Event event);
}

public class ListenerA implements Listener {

    @Override
    public void onClick(Event event) {
        System.out.println("触发事件，type:" + event.getType() + "，data:" + event.getData());
    }
}

public class ListenerSupport {

    private List<Listener> listeners = new ArrayList<>();

    public void addListener(Listener listener) {
        listeners.add(listener);
    }

    public void triggerEvent(Event event) {
        for (Listener listener : listeners) {
            listener.onClick(event);
        }
    }
}

public class EventTest {

    public static void main(String[] args) {
        Listener listener = new ListenerA();
        ListenerSupport listenerSupport = new ListenerSupport();
        listenerSupport.addListener(listener);
        listenerSupport.triggerEvent(new Event("dataA", "typeA"));
    }
}

```







观察者模式中包含观察者和被观察者，他们有各自的行为，把他们各自的行为封装成接口

[(17条消息) 监听器设计模式_Wiki~-CSDN博客_监听器模式](https://blog.csdn.net/gsjwxhn/article/details/90601466)

## 

## 代理模式

### 静态代理

被代理对象与代理对象一起实现相同的接口或者是继承相同父类，与对象适配器模式很类似，差别是适配器模式的作用是将一个功能适配到另一个功能，代理是实现相同的接口，只对功能进行增强。

### JDK动态代理

利用反射机制，动态生成代理对象，目标对象（被代理对象）必须实现接口，因为需要通过反射获取到接口中的方法。

```java

/**
 * 创建动态代理对象
 * 动态代理不需要实现接口,但是需要指定接口类型
 */
public class ProxyFactory{

    //维护一个目标对象
    private Object target;
    public ProxyFactory(Object target){
        this.target=target;
    }

   //给目标对象生成代理对象
    public Object getProxyInstance(){
        return Proxy.newProxyInstance(
                target.getClass().getClassLoader(),
                target.getClass().getInterfaces(),
                new InvocationHandler() {
                    @Override
                    public Object invoke(Object proxy, Method method, Object[] args) throws Throwable {
                        System.out.println("开始事务2");
                        //执行目标对象方法
                        Object returnValue = method.invoke(target, args);
                        System.out.println("提交事务2");
                        return returnValue;
                    }
                }
        );
    }

}



/**
 * 测试类
 */
public class App {
    public static void main(String[] args) {
        // 目标对象
        IUserDao target = new UserDao();
        // 【原始的类型 class cn.itcast.b_dynamic.UserDao】
        System.out.println(target.getClass());

        // 给目标对象，创建代理对象
        IUserDao proxy = (IUserDao) new ProxyFactory(target).getProxyInstance();
        // class $Proxy0   内存中动态生成的代理对象
        System.out.println(proxy.getClass());

        // 执行方法   【代理对象】
        proxy.save();
    }
}

```



### CGLib

[Java的三种代理模式 - 洋葱源码 - 博客园 (cnblogs.com)](https://www.cnblogs.com/cenyu/p/6289209.html)

## 装饰器模式

> 装饰者接收一个被装饰者对象，对被装饰对象的方法增强

- InputStream：定义抽象方法read

```java
public abstract class InputStream implements Closeable {
    public abstract int read() throws IOException;
}
```

- FilterInputStream：装饰器流的父类，没有实际作用，使用其子类实现特定功能

```java
public
class FilterInputStream extends InputStream {
    // 被装饰的输入流
    protected volatile InputStream in;
    protected FilterInputStream(InputStream in) {
        this.in = in;
    }
    // 直接调用被装饰对象的方法
    public int read() throws IOException {
        return in.read();
    }
}
```

- BufferedInputStream：缓冲流，装饰器流具体实现

```java
public
class BufferedInputStream extends FilterInputStream {
    // 缓冲区
    protected volatile byte buf[];
    // 构造方法接收被装饰的流对象，和缓冲区大小
    public BufferedInputStream(InputStream in, int size) {
        super(in);
        if (size <= 0) {
            throw new IllegalArgumentException("Buffer size <= 0");
        }
        buf = new byte[size];
    }
}
```





io流中的缓冲流

io流中的转换流

## 适配器模式

io流中的转换流：将字节流读写适配到不同编码的字符流读写

InputStreamReader实现Reader接口，接收一个InputStream对象，将InputStream对象字节读取操作，适配到Reader接口的字符读取操作

```java
public class InputStreamReader extends Reader {
    
    public InputStreamReader(InputStream in) {
        super(in);
        sd = StreamDecoder.forInputStreamReader(in, this,
                Charset.defaultCharset()); // ## check lock object
    }
    
    /**
     * Reads a single character.
     *
     * @return The character read, or -1 if the end of the stream has been
     *         reached
     *
     * @throws     IOException  If an I/O error occurs
     */
    public int read() throws IOException {
        return sd.read();
    }
}

```

适配器模式实现方式：

1. 类的适配器模式：继承被适配的类，实现目标接口
2. 对象的适配器模式：持有被代理的类对象，实现目标接口，IO转换流就属于此类型
3. 接口的适配器模式：

[JAVA设计模式之适配器模式 - 码头小渔夫 - 博客园 (cnblogs.com)](https://www.cnblogs.com/porotin/p/10334291.html)

## Spring中设计模式



### 简单工厂模式：BeanFactory



xml中配置的bean的创建过程

读取xml中的bean节点，转换为BeanDefinition对象

使用反射和CGLIB对创建bean示例



### 工厂方法模式：FactoryBean



mybatis中的SqlSessionFactoryBean的创建









[Spring 中经典的 9 种设计模式，打死也要记住啊！ (qq.com)](https://mp.weixin.qq.com/s/c-5YrPkkuPXt06gTZvGo1w)

[Spring 夺命连环18问？拿捏了！ (qq.com)](https://mp.weixin.qq.com/s/nOlAfRc0xNA-6J00pOqhGw)





## GOF23

## 面试题

Reactor模式（反应器模式）

是一种事件驱动模式

迭代器模式，用于顺序访问集合对象元素，无需知道集合对象的底层实现。

[25000 字详解 23 种设计模式（多图 + 代码） (qq.com)](https://mp.weixin.qq.com/s/jOXK_Z_wiBmeUMlleXXgfw)

[23种设计模式，我竟然秒懂了... (qq.com)](https://mp.weixin.qq.com/s/qetX2j0TnxXwC-PQfnReWw)



[Spring中用到的设计模式，你知道几个？ (qq.com)](https://mp.weixin.qq.com/s/dTuprB4PEVgYO9ZVD9mdmw)

