# Java注解

## 注解简介

J2SE 5.0引入注解，使用@interface声明注解，注解继承java.lang.annotation.Annotation接口



## JDK自带注解

java.lang包下有以下注解

| 注解                 | 作用                                                         |      |
| -------------------- | ------------------------------------------------------------ | ---- |
| @Deprecated          | 用在过时的类或方法上                                         |      |
| @FunctionalInterface | 用在函数式接口上                                             |      |
| @Override            | 此注解用在方法上，只存在源代码中，编译阶段会对其处理，不加该注解也能正常重写父类方法，如果在不是重写父类的方法上加该注解就会报错。 |      |
| @SafeVarargs         |                                                              |      |
| @SuppressWarnings    |                                                              |      |
|                      |                                                              |      |
|                      |                                                              |      |

java.lang.annotation包下有以下注解

| 注解        | 作用                                                         |      |
| ----------- | ------------------------------------------------------------ | ---- |
| @Documented | 被该注解注释的注解，会被输出到javadoc中                      |      |
| @Inherited  | 用在注解类型上，被注解的类的子类获得注解                     |      |
| @Native     |                                                              |      |
| @Reqeatable |                                                              |      |
| @Retention  | 注解保留策略，参数为RetentionPolicy中的枚举，如下<br />- SOURCE：保留在源码中，编译期间处理，编译完成后不存在<br />- CLASS：保留在字节码中，jvm加载时处理，加载完成后不存在<br />- RUNTIME：jvm加载后依然存在，如果需要在运行时获取注解中的属性值，使用该类型 |      |
| @Target     | 注解作用范围（方法、类、属性等），参数为ElementType中的枚举，如下<br />- TYPE：作用在类、接口、枚举上 |      |
|             |                                                              |      |
|             |                                                              |      |

注解可以被自己注解

```java
@Documented
@Retention(RetentionPolicy.RUNTIME)
@Target(ElementType.ANNOTATION_TYPE)
public @interface Retention {
    /**
     * Returns the retention policy.
     * @return the retention policy
     */
    RetentionPolicy value();
}
```



## 自定义注解





## 注解如何起作用

1. 保留在源码和字节码中的注解，由编译器和jvm类加载器处理，

2. 保留到运行时的注解，由Java代码处理，
   通过反射获取类、属性和方法对象：`Class`，`Method`和`Field`对象
   他们都有`getAnnotation()`方法，可以获取指定类型的注解，再获取注解值

```java
    public <T extends Annotation> T getAnnotation(Class<T> annotationClass) {
        return super.getAnnotation(annotationClass);
    }
```

注解都继承了Annotation接口，使用注解时会生成一个Annotation对象，代码示例：

```java
public class Main {

    @Retention(RetentionPolicy.RUNTIME)
    @Documented
    @Target(ElementType.TYPE)
    public @interface DemoAnnotation {
        String name() default "abc";
    }

    @DemoAnnotation(name = "A")
    public static class A {}

    @DemoAnnotation(name = "B")
    public static class B {}

    public static void main(String[] args) {
        A a = new A();
        B b = new B();
        DemoAnnotation demoAnnotationA = a.getClass().getAnnotation(DemoAnnotation.class);
        DemoAnnotation demoAnnotationB = b.getClass().getAnnotation(DemoAnnotation.class);
        System.out.println(demoAnnotationA instanceof Annotation);
        System.out.println(demoAnnotationA == demoAnnotationB);
        System.out.println(demoAnnotationA.name());
    }
}
```



