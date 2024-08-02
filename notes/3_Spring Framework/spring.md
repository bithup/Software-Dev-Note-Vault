---
tags:
  - java/spring
---

## Spring模块梳理


### 核心容器模块
spring最核心的大模块是Core Container模块

spring的核心思想是IoC(Inversion of Control)，控制反转，实现方式是依赖注入

==IoC容器==：负责创建、初始化、配置和管理对象的生命周期。通过IoC容器，可以实现对象之间的解耦，将对象的创建和依赖关系委托给容器来管理。

==依赖注入==：它是实现IoC的一种方式（另有依赖查找等方式）。通过依赖注入，可以将对象之间的依赖**关**系通过配置文件或注解的方式进行声明，由容器自动将依赖的对象注入到目标对象中。

---

#### Core模块

> [!faq] AttributeAccessor和AttributeAccessorSupport的作用？
> Contents

#### Beans模块

Beans模块中最重要的两个接口`BeanDefinition`和`BeanFactory`
##### BeanDefinition
BeanDefinition的实现类中最常用的是`GenericBeanDefinition`和`AnnotatedGenericBeanDefinition`
```java
package test;  
  
import org.springframework.beans.factory.support.DefaultListableBeanFactory;  
import org.springframework.beans.factory.xml.XmlBeanDefinitionReader;  
import org.springframework.core.io.ClassPathResource;  
  
@Deprecated  
public class Main {  
    public static void main(String[] args) {  
       DefaultListableBeanFactory beanFactory = new DefaultListableBeanFactory();  
       // beanFactory.setBeanClassLoader(myClassLoader); // 设置类加载器  
       // beanFactory.addBeanPostProcessor(new MyBeanPostProcessor()); // 注册BeanPostProcessor  
       /**  
        * 为什么要将beanFactory实例传给BeanDefinitionReader  
        * 读取beanDefiniton的方式有多种，比如从注解获取，从xml文件获取，这种方式方便扩展  
        * */  
       XmlBeanDefinitionReader reader = new XmlBeanDefinitionReader(beanFactory);  
  
       reader.loadBeanDefinitions(new ClassPathResource("beans.xml"));  
       beanFactory.preInstantiateSingletons();  
       User user = (User) beanFactory.getBean("user");  
       System.out.println(user.getAge());  
       System.out.println(user.getName());  
       System.out.println(user.getCompanyInfo().getCompanyName());  
  
  
    }  
}
```


> [!tip] Tip
> AttributeAccessorSupport属于core模块，由此看出beans模块依赖core模块


##### BeanFactory

> [!NOTE]+ BeanFactory的子接口或实现类
> 1. **ListableBeanFactory**：扩展了`BeanFactory`接口，提供了按类型获取bean的能力，允许通过类型进行bean的枚举，而不是通过名称逐个查询2。
> 2. **AutowireCapableBeanFactory**：提供了自动装配的功能，允许为现有对象进行自动装配，通常不需要在应用程序中使用，而是在与其他框架集成时使用4。
> 3. **ConfigurableBeanFactory**：提供了对`BeanFactory`进行配置的能力，如设置类加载器、类型转换器、属性编辑器、BeanPostProcessor、作用域等4。
> 4. **HierarchicalBeanFactory**：实现了工厂的层次结构，允许一个`BeanFactory`有一个父工厂，如果当前工厂中没有找到所需的bean，会向父工厂搜索4。
> 5. **SingletonBeanRegistry**：接口非常简单，提供了单例bean的注册功能，`ConfigurableBeanFactory`同时继承了`HierarchicalBeanFactory`和`SingletonBeanRegistry`这两个接口4。
> 6. **AbstractBeanFactory**：作为`BeanFactory`最基本的抽象实现，提供了`ConfigurableBeanFactory` SPI的全部功能，并且可以获取BeanDefinition5。
> 7. **DefaultListableBeanFactory**：是`ConfigurableListableBeanFactory`和`BeanDefinitionRegistry`接口的默认实现，是Spring中常用的`BeanFactory`实现类，它是基于BeanDefinition的BeanFactory实现，可以通过后置处理器进行扩展5。
> 8. **XmlBeanFactory**：曾是Spring中用于解析XML配置文件的`BeanFactory`实现，但在Spring 3.1之后被标记为过时，推荐使用`DefaultListableBeanFactory`配合`XmlBeanDefinitionReader`作为替代方案5。
> 9. **StaticListableBeanFactory**：是`ListableBeanFactory`的简单实现，功能相对单一，只能管理单例Bean，没有BeanDefinition等概念5。

##### 读取解析XML中的Bean
解析bean标签时，需要判断是否时默认标签
bean模块中只能解析默认的xml标签，其他模块中的xml标签，比如aop、mvc中的标签需要各自模块提供解析器，
如何找到这些扩展标签对应的解析器，通过xml中的命名空间和spring.handler文件去查找





#### Context模块
>如果说核心模块中的**BeanFactory**使Spring成为容器的话，那么上下文模块就是Spring成为框架的原因。这个模块扩展了BeanFactory，增加了对国际化（I18N）消息、事件传播、验证的支持。另外提供了许多企业服务，例如电子邮件、JNDI访问、EJB集成、远程以及时序调度（scheduling）服务。也包括了对模版框架例如Velocity和FreeMarker集成的支持


### AOP模块
AOP模块：基于动态代理来实现，默认如果使用接口的，用JDK提供的动态代理实现，如果是方法则使用CGLIB实现。
Aspects模块：基于AspectJ实现。


### 数据访问与集成模块

#### JDBC

#### ORM

#### 事务


### Web模块
#### Web MVC

#### Web Socket
实现简单的聊天，当有某个订单状态改变时，立即通知web端的相关人员，不需要等待刷新页面

## Spring容器启动过程
- Bean元数据需要被IoC容器解析和注册，以便容器可以管理bean的生命周期。
- `BeanMetadataAttributeAccessor` 持有Bean元数据source
- `BeanDefinition`接口的对象会被注册到`BeanDefinitionRegistry`中，这是Spring容器用来存储和查询bean定义的注册表。
BeanDefinition
BeanFactory
BeanDefinitionRegistry
BeanPostProcessor
BeanFactoryPostProcessor

### DefaultListableBeanFactory
DefaultListableBeanFactory是整个bean加载的核心部分，是Spring注册及加载bean的默认实现。
![[DefaultListableBeanFactory继承关系.png|继承关系图]]
下面分析该类实现的接口功能
- **AliasRegistry**（接口）：alias指的是bean的别名，而aliasRegistry定义了对alias的增删改查等操作。
- SimpleAliasRegistry（类）：主要使用map作为alias的缓存，并对接口AliasRegistry进行实现。
- `SingletonBeanRegistry`（接口）：定义对单例的注册及获取。
- BeanFactory（接口）：定义获取bean及bean的各种属性。
- DefaultSingleTonBeanRegistry（类）：实现了SingletonBeanRegistry的方法，同时继承了SimpleAliasRegistry。
- HierarchicalBeanFactory（接口）：继承BeanFactory，也就是在BeanFactory定义功能的基础上增加了对parantFactory的支持。
- BeanDefinitionRegistry（接口）：定义对BeanDefinition的增删改查功能，BeanDefinition就是描述一个bean的实例，包含了属性值（scope、bean的name、lazy加载模式等），构造参数以及其他更多的实现信息。
- FactoryBeanRegistrySupport（类）：在DefaultSingleTonBeanRegistry基础上增加了对FactoryBean的特殊功能。
- ConfigurableBeanFactory（接口）：提供配置Factory的各种方法，包括设置父工厂、bean的加载器、添加BeanPostProcessor等功能。
- ListableBeanFactory（接口）：根据各种条件获取bean的配置清单，可以根据bean的name、type等条件获得bean配置清单。
- AbstractBeanFactory（类）：综合FactoryBeanRegistrySupport和ConfigurableBeanFactory的功能。
- AutowireCapableBeanFactory（接口）：提供创建Bean、自动注入、初始化以及应用bean的后置处理器。
- AbstractAutowireCapableBeanFactory（类）：综合AbstractBeanFactory并对接口AutowireCapableBeanFactory进行实现 ^49daaf
- ConfigurableListableBeanFactory：BeanFactory配置清单，指定忽略类型及接口等。
- DefaultListableBeanFactory：综合上面所有功能，主要对Bean注册后的处理。


> [!NOTE] BeanFactory和BeanDefinitionRegistry功能比较
> BeanFactory提供获取bean的方法，BeanDefinitionRegistry提供的是操作BeanDefinition的方法


> [!faq] 获取Bean Definition的过程
> 从xml文件获取
> 从yml文件获取
> 从注解获取


> [!faq] BeanDefinition转化为bean的过程？
> Contents



> [!NOTE] spring中的bean为什么必须要有一个无参构造函数？
> Contents
