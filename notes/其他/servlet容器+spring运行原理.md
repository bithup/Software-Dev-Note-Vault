# servlet+spring运行原理

## servlet规范（javax.servlet包下）

Servlet是JavaEE规范的一种，主要是为了扩展Java作为Web服务的功能，统一接口。由其他内部厂商如tomcat，jetty内部实现web的功能。如一个http请求到来：
 容器将请求封装为servlet中的HttpServletRequest对象，调用init（），service（）等方法输出response,由容器包装为httpresponse返回给客户端的过程。



![](https://upload-images.jianshu.io/upload_images/10998555-6456f42a80a45586.png?imageMogr2/auto-orient/strip|imageView2/2/format/webp)

servlet规范不仅用于HTTP接口，还有其他的实现，这里只关注HTTP相关的servlet

### servlet

作用：用来处理对应URL的请求，URL可以使用通配符，spring中所有的请求都匹配到DispatchServlet，然后再转发到其他servlet(controller)

声明周期：

1. 加载和实例化：加载实例化主要是交由web容器完成
2. 初始化
3. 处理请求
4. 销毁

### servletConfig

tomcat自动加载xml文件中的servletConfig配置，在初始化servlet时传入init方法

```java
void init(ServletConfig var1) throws ServletException;
```

### servletRequest和servletResponse

#### servletRequest

- 获取HTTP协议请求头部，如getHeader、getHeaders
- 获取请求路径，如getContextPath、getServletPath
- 获取cookie的方法，如getCookies
- 获取session的方法，如getSession,session是存储在服务器内存中，返回响应的时候会写入浏览器一个sessionId的cookie，用来标示这一个会话

#### servletResponse

- getOutputStream() —— 返回一个ServletOutputStream对象，Servlet用它来输出二进制的正文数据
- getWriter() —— 返回一个PrinterWriter对象，Servlet用它来输出字符串形式的正文数据；

### servletContext

Servlet与Servlet容器之间直接通信的接口,一个web应用只独有一个

作用：

1. 获取当前Web应用的资源，如getContextPath()
2. 用于在web应用范围内存取共享数据,如setAttribute(String name, Object object)

### filter



### listener

当触发某个事件，如servlet context初始化完成时，需要做一些事情，servlet规范中定义了若干个Listener用于监听这些事件。

作用：

- 监听域对象自身的创建和销毁的事件监听器

  - ServletContextListener：ServletContext的创建和销毁：contextInitialized方法和contextDestroyed方法，作为定时器、加载全局属性对象、创建全局数据库连接、加载缓存信息等
  - HttpSessionListener：HttpSession的创建和销毁：sessionCreated和sessionDestroyed方法，可用于统计在线人数、记录访问日志等
  - ServletRequestListener： ServletRequest的创建和销毁：requestInitialized和requestDestroyed方法

- 监听域对象中的属性的增加和删除的事件监听器

  - ServletContextAttributeListener、HttpSessionAttributeListener、ServletRequestAttributeListener接口。
  - 实现方法：attributeAdded、attributeRemoved、attributeReplaced

- 监听绑定到HttpSeesion域中的某个对象的状态的事件监听器(创建普通JavaBean)

  - HttpSession中的对象状态：绑定→解除绑定；钝化→活化
  - 实现接口及方法：HttpSessionBindingListener接口(valueBound和valueUnbound方法)、HttpSessionActivationListener接口(sessionWillPassivate和sessionDidActivate方法)

  

  

## servlet容器运行

tomcat中多个项目运行在同一个jvm下，一个项目对应一个线程，不同项目数据不共享，因为类加载器不同

### tomcat是如何加载spring的

tomcat xml文件中配置ContextLoaderListener，该类是org.springframework.web.context包下的类

```java
public class ContextLoaderListener extends ContextLoader 
    implements ServletContextListener {
    
    public ContextLoaderListener(WebApplicationContext context) {
        super(context);
    }

    // 初始化
    public void contextInitialized(ServletContextEvent event) {
        this.initWebApplicationContext(event.getServletContext());
    }
}
```

这里最重要的是通过ServletContext，初始化属于Spring的上下文WebApplicationContext，并将其存放在ServletContext



使用webApplicationContext.getBean()来获取被Spring管理的类，所以这也是IOC容器的核心



Spring采用这种监听器来启动自身的方法，也是一种设计模式，叫观察者模式：

整个过程是这样的，Tomcat加载webapps项目时，先通过反射加载在web.xml标明的类（通通放入一个数组）

到某个时刻，我tomcat（事件源，事件的起源）会发起一个叫ServletContextEvent的事件（里面带着各种参数）

凡是实现了ServletContextListener接口的类，我都会调用里面的contextInitialized方法，并把这个事件参数传进去

咳咳，现在我看看这个数组里有没符合条件的（遍历），发现真有实现这个接口的（类 instanceof 接口），就调用contextInitialized方法

于是Spring就被动态加载进来了~~



### DispatchServlet

DispatchServlet是如何将请求匹配到Controller上的？

Controller是servlet吗？

每个请求都有单独的线程吗？



## servlet容器、spring容器和springmvc容器

```xml
<!--以下为加载Spring需要的配置-->
<!--Spring配置具体参数的地方-->
<context-param>
  <param-name>contextConfigLocation</param-name>
  <param-value>
    classpath:applicationContext.xml
  </param-value>
</context-param>
<!--Spring启动的类-->
<listener>
  <listener-class>org.springframework.web.context.ContextLoaderListener</listener-class>
</listener>

 <!--以下为加载SpringMVC需要的配置-->
<servlet>
  <servlet-name>project</servlet-name>
  <servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
  <load-on-startup>1</load-on-startup>   <!--servlet被加载的顺序，值越小优先级越高（正数）-->

  <servlet-mapping>
        <servlet-name>project</servlet-name>
        <url-pattern>*.html</url-pattern>
  </servlet-mapping>
</servlet>
```

从tomcat的配置文件可以看出：

- tomcat先加载ContextLoaderListener，先初始化spring容器，根据applicationContext.xml，初始化bean，比如service，dao层的bean
- 然后加载DispatchServlet，初始化springmvc容器，判断是否存在spring容器，如果存在，就将spring容器设为springmvc容器的父容器
- springmvc可以不依赖spring单独使用（不配置ContextLoaderListener，只配置DispatchServlet）
- 使用spring可以为集成其他框架比如mybatis提供方便
- 父容器不能访问子容器，service和dao层bean在父容器中，controller在子容器中，所以，在controller中没办法注入service，注意：springboot中spring和springmvc使用同一个容器