# 字符、字符串及处理

## 字符编码格式

## Java字符

Character

## Java字符串类

### String类

#### split()函数与逗号分隔字符串

```java
import java.util.Arrays;
import java.util.stream.Collectors;
import org.springframework.util.StringUtils;
String ids = "";
// 1式
System.out.println(Arrays.stream(ids.split(",")).collect(Collectors.toSet()).size());
// 2式
System.out.println(StringUtils.commaDelimitedListToSet(ids).size());
```

| ids的值 | 1式结果 | 2式结果 |
| ------- | ------- | ------- |
| `""`    | 1       | 0       |
| `" "`   | 1       | 1       |
| `"1,"`  | 1       | 2       |
| `",1"`  | 2       | 2       |
| `","`   | 0       | 1       |
| `", "`  | 2       | 2       |
| `" ,"`  | 1       | 2       |

总结：

1. split对空字符串和空白字符串都起作用
2. split接受逗号前的空字符串，不接受逗号后的空字符串
3. 如果字符串和分隔符相同，split得到的结果为空
4. 排除逗号分隔的字符串特殊情况：去掉首位逗号，再判断是否为空
5. 先分割字符串，再过滤掉空白字符串

### StringBuffer类

大量的字符串拼接

## Java字符串正则匹配与校验

### Java正则

### Java字符串正则匹配API

### 常用正则





## 格式化输出

### String.format

```java
String.format("%.2f", influencer.getTotalFansNum() / 10000f)
```

### NumberFormat

如下格式化百分号，不会保留小数

```java
import java.text.NumberFormat;
double num = 46.99;
System.out.println(NumberFormat.getPercentInstance().format(num/100));
```

为什么会四舍五入，如何改

```java
import java.text.DecimalFormat;
import java.text.NumberFormat;
double num = 34.45343243;

String a = NumberFormat.getPercentInstance().format(num / 100);
System.out.println(a);
DecimalFormat df1 = new DecimalFormat("0.00%");
DecimalFormat df2 = new DecimalFormat("#.00");
System.out.println(df1.format(num/100));
System.out.println(df2.format(num));
```

### DecimalFormat

### MessageFormat



