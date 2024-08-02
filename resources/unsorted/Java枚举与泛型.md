# Java泛型



## 泛型



### 泛型接口

```java
public interface Iterable<T> {}
```

### 泛型类

```
public class ArrayList<E> extends AbstractList<E>
        implements List<E>, RandomAccess, Cloneable, java.io.Serializable
{}
```

### 泛型方法



```java
public interface BeanFactory {
    <T> T getBean(String name, Class<T> requiredType) throws BeansException;
}
```

- \<T\> : 声明泛型方法必须在返回类型前加上
- Class\<T\> : 泛型方法必须的参数，在调用方法时传入