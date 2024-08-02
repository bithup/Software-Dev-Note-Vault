# SpringBoot - filter&interceptor

## filter

### 创建filter类的方式

1. 实现Filter接口，可以匿名实现
2. 继承spring提供的过滤器类，比如：OncePerRequestFilter

### 注册filter的方式

1. 使用注解：@WebFilter和@ServletComponentScan
2. 使用配置类：@Configuration注解的类中，@Bean注解的方法中创建并返回FilterRegistrationBean实例

## interceptor

### 创建interceptor类的方式

1. 实现HandlerInterceptor接口

### 注册interceptor的方式

1. 实现WebMvcConfigurer接口addInterceptors()方法



## 对比

区别：

1. 拦截器是基于java的反射机制的，而过滤器是基于函数回调。
2. 拦截器不依赖与servlet容器，过滤器依赖与servlet容器。
3. 拦截器只能对action请求起作用，而过滤器则可以对几乎所有的请求起作用。
4. 拦截器可以访问action上下文、值栈里的对象，而过滤器不能访问。
5. 在action的生命周期中，拦截器可以多次被调用，而过滤器只能在容器初始化时被调用一次
6. 拦截器可以获取IOC容器中的各个bean，而过滤器就不行，这点很重要，在拦截器里注入一个service，可以调用业务逻辑



执行顺序：

filter -> Interceptor（pre）->@Aspect ->Interceptor(post) ->Interceptor(after)
