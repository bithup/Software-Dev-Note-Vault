# SpringBoot

## springboot启动过程分析

### @Configuration和@Bean

被@Configuration注解的类会被当做Spring IOC容器的配置类

bean之间的依赖配置方式

1. 直接调用生成bean的方法

```java
@Configuration
public class MockConfiguration{
    @Bean
    public MockService mockService(){
        return new MockServiceImpl(dependencyService());
    }
    
    @Bean
    public DependencyService dependencyService(){
        return new DependencyServiceImpl();
    }
}
```

2. 给bean设置name，使用@Qualifier指明依赖的bean

```java
@Configuration
public class MockConfiguration{
    @Bean
    public MockService mockService(){
        return new MockServiceImpl(@Qualifier("dependencyService") DependencyService d);
    }
    
    @Bean(name = "dependencyService")
    public DependencyService dependencyService(){
        return new DependencyServiceImpl();
    }
}
```

### @ComponentScan

自动扫描并加载符合条件的组件（比如@Component、@Repository、@Service等）或者bean定义

通过basePackages属性指定扫描的路径

### 创建SpringApplication对象

```java
public SpringApplication(ResourceLoader resourceLoader, Class<?>... primarySources) {
	// 默认为null
    this.resourceLoader = resourceLoader;
    Assert.notNull(primarySources, "PrimarySources must not be null");
    // 设置primarySources
    this.primarySources = new LinkedHashSet<>(Arrays.asList(primarySources));
    // 判断是否时web应用
    this.webApplicationType = WebApplicationType.deduceFromClasspath();
    // 获取spring.factories文件中的初始化器和监听器
    setInitializers((Collection) getSpringFactoriesInstances(ApplicationContextInitializer.class));
    setListeners((Collection) getSpringFactoriesInstances(ApplicationListener.class));
    // 推断主类
    this.mainApplicationClass = deduceMainApplicationClass();
}
```

#### getSpringFactoriesInstances方法分析

```java
private <T> Collection<T> getSpringFactoriesInstances(Class<T> type, Class<?>[] parameterTypes, Object... args) {
   ClassLoader classLoader = getClassLoader();
   // Use names and ensure unique to protect against duplicates
   Set<String> names = new LinkedHashSet<>(SpringFactoriesLoader.loadFactoryNames(type, classLoader));
   List<T> instances = createSpringFactoriesInstances(type, parameterTypes, classLoader, args, names);
   AnnotationAwareOrderComparator.sort(instances);
   return instances;
}
```

getSpringFactoriesInstances（）方法详解，一般都是解析ＭETA-INF/spring.factories文件下ApplicationContextInitializer、ApplicationListener、SpringBootExceptionReporter、SpringApplicationRunListeners等接口的实现类，基于ＳＰＩ可插拔

### 运行SpringApplication对象run方法

#### StopWatch

记录任务运行的时间

```java
StopWatch stopWatch = new StopWatch();
stopWatch.start();
```

#### 设置HeadlessProperty

```java
configureHeadlessProperty();
```

#### 获取并运行SpringApplicationRunListeners

参考设计模式中的监听器模式

```java
class SpringApplicationRunListeners {

	private final Log log;

	private final List<SpringApplicationRunListener> listeners;
}
// SpringApplicationRunListener是接口
public interface SpringApplicationRunListener {}

public class EventPublishingRunListener implements SpringApplicationRunListener, Ordered {}
```

### SpringApplicationRunLister



ApplicationListener、SpringApplicationRunListeners、SpringApplicationRunListener的关系：

- SpringApplicationRunListeners类和SpringApplicationRunListener类是SpringBoot中新增的类。ApplicationListener是spring中框架的类。
- 在SpringBoot（SpringApplication类）中，使用SpringApplicationRunListeners、SpringApplicationRunListener来间接调用ApplicationListener。
- SpringApplicationRunListeners是SpringApplicationRunListener的封装，SpringApplicationRunListeners中包含多个SpringApplicationRunListener，是为了批量执行的封装，SpringApplicationRunListeners与SpringApplicationRunListener生命周期相同，调用每个周期的各个SpringApplicationRunListener。然后广播相应的事件到Spring框架的ApplicationListener。

通过上面的3个特点可以看出SpringApplicationRunListener就是一个ApplicationListener的代理。springboot启动的几个主要过程的监听通知都是通过他来进行回调。

```java
public class SpringApplication {
    // SpringApplication对象保存所有ApplicationListener对象
    private List<ApplicationListener<?>> listeners;
}

public class EventPublishingRunListener implements SpringApplicationRunListener, Ordered {
    //EventPublicshingRunListener对象持有SpringApplication对象
    private final SpringApplication application;

	private final String[] args;
	//EventPublicshingRunListener对象持有SimpleApplicationEventMulticaster对象
	private final SimpleApplicationEventMulticaster initialMulticaster;
	// 构造EventPublishingRunListener对象时，将SpringApplication中的ApplicationListener注册到SimpleApplicationEventMulticaster
	public EventPublishingRunListener(SpringApplication application, String[] args) {
		this.application = application;
		this.args = args;
		this.initialMulticaster = new SimpleApplicationEventMulticaster();
		for (ApplicationListener<?> listener : application.getListeners()) {
			this.initialMulticaster.addApplicationListener(listener);
		}
	}
    // 在应用的各个阶段，发布相应的事件
    @Override
	public void starting() {
		this.initialMulticaster.multicastEvent(new ApplicationStartingEvent(this.application, this.args));
	}
}


public class SimpleApplicationEventMulticaster extends AbstractApplicationEventMulticaster {
    public void multicastEvent(ApplicationEvent event) {
        this.multicastEvent(event, this.resolveDefaultEventType(event));
    }
    // SimpleApplicationEventMulticaster调用ApplicationListener处理事件的方法
    private void doInvokeListener(ApplicationListener listener, ApplicationEvent event) {
        try {
            listener.onApplicationEvent(event);
        } catch (ClassCastException var6) {
            String msg = var6.getMessage();
            if (msg != null && !this.matchesClassCastMessage(msg, event.getClass())) {
                throw var6;
            }

            Log logger = LogFactory.getLog(this.getClass());
            if (logger.isTraceEnabled()) {
                logger.trace("Non-matching event type for listener: " + listener, var6);
            }
        }

    }
    
}

```

所以说SpringApplicationRunListener和ApplicationListener之间的关系是通过ApplicationEventMulticaster广播出去的SpringApplicationEvent所联系起来的





### Bean的实例化过程

第一部分进行SpringApplication的初始化模块，配置一些基本的环境变量、资源、构造器、监听器，

第二部分实现了应用具体的启动方案，包括启动流程的监听模块、加载配置环境模块、及核心的创建上下文环境模块，

第三部分是自动化配置模块



## springboot自动配置原理

### @EnableAutoConfiguration

### @EnableWebmvc

### @Import

## starter原理

## 注解总结

[SpringBoot中必须掌握的45个注解 (qq.com)](https://mp.weixin.qq.com/s/Smru_32SLNdkwaDxbGUTxA)

[Spring Boot 最最最常用的注解梳理 (qq.com)](https://mp.weixin.qq.com/s/O_Jyw2Yp5jV4tB9eHzN_iA)

[Spring 最常用的 7 大类注解，史上最强整理！ (qq.com)](https://mp.weixin.qq.com/s/8cxO6MB9XO0pPBYGSynrmg)

@RunWith



## spring中的设计模式

[Spring 中经典的 9 种设计模式，打死也要记住啊！ (qq.com)](https://mp.weixin.qq.com/s/c-5YrPkkuPXt06gTZvGo1w)
