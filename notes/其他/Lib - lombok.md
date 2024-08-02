# Lombok

## 功能简介

## 注解

### 构造函数注解

#### 普通构造函数

反对法

#### 构造注入函数

使用构造函数进行依赖注入

```java
@Slf4j
@Component
public class MessageHandler extends LarkHandler {

    private final LarkDefaultClient larkDefaultClient;
    private final ImService imService;

    @Autowired
    public MessageHandler(LarkDefaultClient larkDefaultClient, ImService imservice) {
        this.larkDefaultClient = larkDefaultClient;
        this.imService = imService;
    }
}
```

使用注解代替`@RequiredArgsConstructor(onConstructor = @__(@Autowired))`

```java
@Slf4j
@RequiredArgsConstructor(onConstructor = @__(@Autowired))
@Component
public class MessageHandler extends LarkHandler {

    private final LarkDefaultClient larkDefaultClient;
    private final ImService imService;
}
```



## 插件配置

