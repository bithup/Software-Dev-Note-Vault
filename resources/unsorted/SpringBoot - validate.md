# SpringBoot - validate

## JSR303规范及实现

JSR303是规范，默认实现是hibernate validator

### spring boot中使用

1. 引入maven依赖

```xml
<dependency>
  <groupId>org.springframework.boot</groupId>
  <artifactId>spring-boot-starter-validation</artifactId>
</dependency>
```

2. 导入javax.validation.constraints包下的注解，org.hibernate.validator.constraints包下的注解提示过时
3. 在需要校验的参数前加上注解org.springframework.validation.annotation.Validated或者注解javax.validation.Valid
4. @Validated是JSR303规范的增强，推荐使用
5. @Valid和@Validated的区别参考：https://www.cnblogs.com/danhuang/p/12681426.html

### 校验的原理



## 已有注解 

@Future

@Size(min =1, max =4) 字符串长度

校验数组大小

@Min @Max 数值的大小

## 自定义校验注解

1. 自定义校验注解

```java
import javax.validation.Constraint; 
import javax.validation.Payload; 
import java.lang.annotation.*; 

@Documented @Retention(RetentionPolicy.RUNTIME) 
@Target({ElementType.PARAMETER,ElementType.FIELD}) 
@Constraint(validatedBy = FlagValidatorClass.class) 
public @interface FlagValidator { 
		// flag的有效值，多个使用,隔开 
		String values(); 
		// flag无效时的提示内容 
		String message() default "flag必须是预定义的那几个值，不能随便写"; 
		Class<?>[] groups() default {}; 
		Class<? extends Payload>[] payload() default {}; 
} 
```

2. 自定义校验方法

```java
import javax.validation.ConstraintValidator; 
import javax.validation.ConstraintValidatorContext; 
public class FlagValidatorClass implements ConstraintValidator<FlagValidator,Object> { 
  	/** * FlagValidator注解规定的那些有效值 */ 
  	private String values; 
  	@Override 
  	public void initialize(FlagValidator flagValidator) { this.values = flagValidator.values(); } 		/** 
  	* 用户输入的值，必须是FlagValidator注解规定的那些值其中之一。 
  	* 否则，校验不通过。 * @param value 用户输入的值，如从前端传入的某个值 
  	*/ 
  	@Override public boolean isValid(Object value, ConstraintValidatorContext constraintValidatorContext) { 
      // 切割获取值 
      String[] value_array = values.split(","); 
      Boolean isFlag = false; for (int i = 0; i < value_array.length; i++){ 
      // 存在一致就跳出循环 
      if (value_array[i] .equals(value)){ isFlag = true; break; } } return isFlag; } 
} 
```

3. 使用

```java

```



## 校验分组，groups

什么是分组校验

https://blog.csdn.net/jinjiankang/article/details/89711493

如何校验两个日期，开始日期要小于结束日期



