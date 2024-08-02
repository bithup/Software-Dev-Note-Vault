# SpringBoot - spring doc

## swagger2和swagger3对比

|      |      |      |
| ---- | ---- | ---- |
|      |      |      |
|      |      |      |
|      |      |      |



## swagger3配置与使用

### 引入依赖

```xml
<dependency>
     <groupId>io.springfox</groupId>
      <artifactId>springfox-boot-starter</artifactId>
      <version>3.0.0</version>
</dependency>
```

### 配置

1. springboot启动类上加注解

```Java
@EnableOpenApi
```

2. swagger配置类

```java
@Configuration
public class SwaggerConfig {
    @Bean
    public Docket createRestApi() {
        return new Docket(DocumentationType.OAS_30)
                .apiInfo(apiInfo())
                .select()
                .apis(RequestHandlerSelectors.withMethodAnnotation(ApiOperation.class))
                .paths(PathSelectors.any())
                .build();
    }

    private ApiInfo apiInfo() {
        return new ApiInfoBuilder()
                .title("文档标题")
                .description("文档描述")
                .contact(new Contact("姓名", "网址", "邮箱"))
                .version("1.0.0")
                .build();
    }
}
```

3. 配置文件

```yaml

```

4. swagger3必须开启@Enable WebMvc

## spring doc

spring doc基于swagger3，springdoc有哪些好用的地方？

## spring restdocs

restdocs是由单元测试生成文档的



> ## Swagger注解的滥用
>
> 在代码中的注解已经存储了大量的信息，如果再用`swagger-core`的注解将这些信息用另一种方式表达出来，很容易造成改了这里忘了改那里，即修改不同步。因此Springfox的开发者不推荐大家使用`swagger-core`的注解来描述API，认为`swagger-core`的注解只是补充，只能用于实现其他方法都不能实现的调整或者覆盖。例如，更应该使用`Jackson`的注解，而不是`@ApiModelProperty`；更应该使用`@NotNull`或者`@RequestParam#required`，而不是在文档里写一句`此字段必填`。

 https://www.cnblogs.com/jason1990/archive/2020/03/27/12581773.html
