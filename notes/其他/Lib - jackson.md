# Fast XML - Jackson

## 序列化与反序列化

### 1. String和Object互转

### 2. String和Mapper互转

## Jackson注解

### @JsonInclude

```java
// 注解在类、属性、参数上
// Jackson序列化时，默认不包含值为null的属性，可以使用注解序列化null值
@JsonInclude(JsonInclude.Include.ALWAYS)

```



### @JsonValue

### @JsonProperty

注解到属性上：

注解在方法上：

### @JsonFormat

1. 对象序列化为JSON字符串时指定日期格式，如：LocalDateTime类型序列化时只保留年月日。

   ```java
   import com.fasterxml.jackson.annotation.JsonFormat;
   
   @JsonFormat(pattern = "yyyy-MM-dd",timezone="GMT+8")
   private LocalDateTime checkInDate;
   ```



## 自定义Jackson注解

### 自定义JsonSerializer和JsonDeserializer

1. 创建指定类型的JsonSerializer和JsonDeserializer，继承jackson-databind包下的JsonSerializer抽象类并重写serialize方法
2. 将自定义的Serializer加入配置中
   1. 使用@JsonComponent注解
3. 对需要使用自定义序列发器的字段加注解
   1. @JsonDeserializer(using = 自定义类.class)
   2. @JsonSerializer(using = 自定义类.class)

```java
package com.joymedia.config.json.serialize;

import com.fasterxml.jackson.core.JsonGenerator;
import com.fasterxml.jackson.databind.JsonSerializer;
import com.fasterxml.jackson.databind.SerializerProvider;

import java.io.IOException;
import java.math.BigDecimal;
import java.math.RoundingMode;
import java.text.NumberFormat;
import java.util.Objects;

@JsonComponent
public class BigDecimalSerializer extends JsonSerializer<BigDecimal> {

    public static final BigDecimalSerialize INSTANCE = new BigDecimalSerialize();

    private static final NumberFormat PERCENT = NumberFormat.getPercentInstance();

    @Override
    public void serialize(BigDecimal bigDecimal, JsonGenerator jsonGenerator, SerializerProvider serializerProvider) throws IOException {
        if (Objects.isNull(bigDecimal)) {
            jsonGenerator.writeString("0.00");
        } else {
            final BigDecimal bigDecimal1 = bigDecimal.setScale(2, RoundingMode.DOWN);
            jsonGenerator.writeString(bigDecimal1.toString());
        }
    }
}
```

实现时间戳反序列化为LocalDate

```java
@JsonComponent
public class TimestampLocalDateDeserializer extends JsonDeserializer<LocalDate> {
    @Override
    public LocalDate deserialize(JsonParser jsonParser, DeserializationContext deserializationContext) throws IOException, JsonProcessingException {
        if (StringUtils.isEmpty(jsonParser.getText())) return null;
        Long second = jsonParser.getLongValue();
        return Instant.ofEpochSecond(second).atZone(ZoneOffset.ofHours(8)).toLocalDate();
    }
}
```







🍀思考：是否可以使用配置类，在序列化与反序列化时，根据字段类型自动使用自定义序列化器，而不需要在字段上加注解









## 实现枚举的自动转换

1. 在接收参数时，传入枚举的name，自动转换为枚举实例，如果不存在，则抛出异常
2. 在序列化为json字符串时，自动转换为枚举的name，使用@JsonValue
