# SpringBoot - spring mvc



### 参数绑定

@RequestParam

@PathVariable

@RequestHeader

@RequestBody

@RequestAttribute

@RequestPart

提示：

- 传入参数名和接收参数名不相同时才需要@RequestParam
- 规范的get请求没有body（可以有，但不保证所有实现都支持），@RequestBody不用在get请求中



### 请求映射

@RequestMapping

@GetMapping

@PutMapping

@DeleteMapping

@PostMapping

@PatchMapping

@Mapping

### 路径参数可以使用正则

```
@GetMapping("/path/{group:[a-zA-Z0-9_]+}/{userid}")
@ResponseBody
public String path(@PathVariable("group") String group, @PathVariable("userid") Integer userid){}
```



## mvc相关配置

### 配置文件

```properties

```

#### 日期格式配置

使用字符串格式传递

使用时间戳格式传递

SpringMVC中GET请求使用字符串格式传参，接口无法接收

SpringMVC日期传参指定格式使用@DateTimeFormat注解

可以在yaml文件中配置jackson：data-format

还可以使用spring-boot-starter-data-rest，参考[(15条消息) springboot全局设置日期格式_shenzhou_yh的博客-CSDN博客](https://blog.csdn.net/shenzhou_yh/article/details/97657888)



思考：不使用@EnableWeb Mvc，springboot也能正常访问接口，为什么要使用这个注解，

### 自定义配置类

> 实现接口WebMvcConfigurer，该接口定义了各种配置的接口，重写其中的接口即可实现自定义配置。注意抽象类WebMvcConfigurerAdapter已过时

```java
/**
 * Spring MVC 配置
 *
 * @author qiguanqun
 */
@Configuration
@RequiredArgsConstructor(onConstructor = @__(@Autowired))
@Slf4j
public class WebMvcConfig implements WebMvcConfigurer {

    private final MappingJackson2HttpMessageConverter mappingJackson2HttpMessageConverter;
    private final AuthorityInterceptor authorityInterceptor;

    @Override
    public void addInterceptors(InterceptorRegistry registry) {
        registry.addInterceptor(authorityInterceptor)
                // 登录
                .excludePathPatterns("/login", "/login/**", "/logout")
                // 飞书回调
                .excludePathPatterns("/lark/msg", "/lark/msg/**")
                // 同步数据
                .excludePathPatterns("/jobs/**")
                .excludePathPatterns("/swagger-resources/**", "/webjars/**", "/v2/**", "/swagger-ui.html/**");
    }

    @Override
    public void addResourceHandlers(ResourceHandlerRegistry registry) {
        registry.addResourceHandler("swagger-ui.html")
                .addResourceLocations("classpath:/META-INF/resources/");
        registry.addResourceHandler("/webjars/**")
                .addResourceLocations("classpath:/META-INF/resources/webjars/");
    }

    @Override
    public void addCorsMappings(CorsRegistry registry) {
        registry.addMapping("/**")
                .allowedOrigins("*")
                .allowedMethods("POST", "GET", "PUT", "OPTIONS", "DELETE")
                .maxAge(3600)
                .allowCredentials(true);
    }

    /**
     * 配置json转换器
     */
    @Override
    public void configureMessageConverters(List<HttpMessageConverter<?>> converters) {
        converters.add(mappingJackson2HttpMessageConverter);
        converters.add(new BufferedImageHttpMessageConverter());
        log.info("spring mvc json converters init finished ");
    }


    /**
     * 处理枚举类型转换
     */
    @Override
    public void addFormatters(FormatterRegistry registry) {
        registry.addConverterFactory(new EnumConvertFactory());
    }


```

思考：将需要过滤的路径存放到数据库，在前端控制路径是否可以访问，数据库有变动时通过事件出发配置更新。

## Web context注解

@RequestScope



## spring mvc运行原理研究

[手写SpringMVC框架 (qq.com)](https://mp.weixin.qq.com/s/xezRzragPlsRY03EIEqepg)



## controller线程安全问题

controller默认是单例的，是线程不安全的

多个请求使用同一个实例，所以不要在controller实例中定义公共资源，比如售票接口的总剩余票数就是线程公共资源，

可以使用@Scope("prototype")注解，设置controller为原型模式，每次通过getBean获取该bean就会新产生一个实例，创建后spring将不再对其管理

```java
package cn.mrhan.java.thread;

import org.springframework.context.annotation.Scope;
import org.springframework.stereotype.Controller;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.ResponseBody;

@Controller
@Scope("prototype")
public class ThreadController {
    private int num = 0;
    @RequestMapping("/test1")
    @ResponseBody
    public String test1(){
        return ++num +"";
    }

    @RequestMapping("/test2")
    @ResponseBody
    public String test2(){
        return ++num +"";
    }

}

```

