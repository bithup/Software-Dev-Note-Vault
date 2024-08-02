# SpringBoot - profile

## 多个配置文件切换

### 配置文件格式

application.yml或application.yaml是主配置文件，存放各个环境中公共的配置。

### 配置文件激活

1. 直接指定激活配置文件

```yaml
spring:
  profiles:
    active: dev
    include: jobs
```

2. 动态指定激活配置文件
   - 运行jar包时使用命令行参数指定
   - maven打包时使用maven参数指定，结合Jenkins（maven打包命令中加参数），可实现不同环境配置切换

```yaml
spring:
  profiles:
    active: @profile.action@
    include: jobs
```

相应的maven配置

```xml
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/maven-v4_0_0.xsd">

    <profiles>
        <profile>
            <id>dev</id>
            <activation>
                <activeByDefault>true</activeByDefault>
            </activation>
            <properties>
                <profile.action>dev</profile.action>
            </properties>
        </profile>
        <profile>
            <id>test</id>
            <properties>
                <profile.action>test</profile.action>
            </properties>
        </profile>
        <profile>
            <id>prod</id>
            <properties>
                <profile.action>prod</profile.action>
            </properties>
        </profile>
    </profiles>
</project>
```

## 配置文件读取的方式

### @Value注解

1. 使用spring注解`@Value` + 两种表达式

```java
// 使用SpEL表达式，以#开通，获取bean的属性，或者常量
@Value("#{1}")  
private int number; //获取数字 1  

@Value("#{'Spring Expression Language'}") //获取字符串常量  
private String str;  

@Value("#{dataSource.url}") //获取bean的属性  
private String jdbcUrl;  

// 使用$开头的表达式，获取配置文件中的值
@Value("${init.password}")  
 private String initPwd;
```



2. 使用spring注解`@ConfigurationProperties`

```java
@Configuration
@EnableConfigurationProperties
@ConfigurationProperties(prefix = "feishu.app")
@Data
public class LarkConfig {

    private String id;
    private String secret;
    private String encryptKey;
    private String verificationToken;

}
```

@value注意：[一个Spring的注解竟然能玩出这么多花样 (qq.com)](https://mp.weixin.qq.com/s/MqthQ803CpzLdLmNXrKmEQ)



### spring-boot-configuration-processor

## 其他注解

@PropertySource("classpath:/dbconfig.properties")

@Profile()

## spring-boot-configuration-processor的作用

[(15条消息) spring-boot-configuration-processor 是干啥用的_卡梅丽多的博客-CSDN博客](https://blog.csdn.net/yuhan_0590/article/details/85100246)
