## 基本用法

### 创建Optional

```java
Optional.empty();          // 创建一个空Optional
Optional.of(T value);      // 不接受null，会报NullPointerException异常
Optional.ofNullable(T value);     // 可以接受null
```

### 判断是否为NULL

```java
isPresent();       // 判断是否为空,返回true、false
isEmpty();		   // Java 11 之后可用
ifPresent();	   // 结合lambda实现if语句的功能
```

### 转换值

#### map

返回值会被Optional包裹，因此可以继续调用map，可以是无限层级的

map方法源码如下

```java
public <U> Optional<U> map(Function<? super T, ? extends U> mapper) {
    Objects.requireNonNull(mapper);
    if (!isPresent()) {
        return empty();
    } else {
        return Optional.ofNullable(mapper.apply(value));
    }
}
```



#### flatMap

如果对象的属性已经被Optional包裹，就无法使用map正常转换值，会出行Optional嵌套的情况`Optional<Optional<Tearch>>`

这种情况使用flatMap

### 过滤

#### Predicate 接口

```java
Predicate<String> len6 = pwd -> pwd.length() > 6;
```

#### filter

filter方法接收Predicate类型参数，判断条件之间可以做逻辑运算

```java
public class OptionalMapFilterDemo {
    public static void main(String[] args) {
        String password = "password";
        Optional<String>  opt = Optional.ofNullable(password);

        Predicate<String> len6 = pwd -> pwd.length() > 6;
        Predicate<String> len10 = pwd -> pwd.length() < 10;
        Predicate<String> eq = pwd -> pwd.equals("password");

        boolean result = opt.map(String::toLowerCase).filter(len6.and(len10 ).and(eq)).isPresent();
        System.out.println(result);
    }
}
```

### 获取结果或默认值

#### get

ofNullable().map().get()会提示没有使用isPresent()判断

#### orElse

接收一个默认值（可以是字面量，可一个是调用一个函数）

#### orElseGet

接收一个Supplier（lambda表达式），和orElse接收一个函数调用作用类似，

```java
EmployeeVisibleRange employeeVisibleRange = Optional
        	 .ofNullable(employeeVisibleRangeMapper.getByEmployeeId(sessionService.getCurrentUserId()))
.orElseGet(EmployeeVisibleRange::new);
```



#### orElse和orElseGet的区别

源码如下

```java
 public T orElse(T other) {
        return value != null ? value : other;
    }

    /**
     * If a value is present, returns the value, otherwise returns the result
     * produced by the supplying function.
     *
     * @param supplier the supplying function that produces a value to be returned
     * @return the value, if present, otherwise the result produced by the
     *         supplying function
     * @throws NullPointerException if no value is present and the supplying
     *         function is {@code null}
     */
    public T orElseGet(Supplier<? extends T> supplier) {
        return value != null ? value : supplier.get();
    }
```

无论传入Optional的值是否为NULL，orElse方法入参表达式都会执行，orElseGet只会在值为null时调用入参表达式。orElse可能会造成资源浪费。



## 扩展用法

