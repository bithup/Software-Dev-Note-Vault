# Java SE - Date&Time

## 新日期时间API（java.time）

### java.time包结构

| 包               | 内容                     |
| ---------------- | ------------------------ |
| java.time        | 包含值对象的基础包       |
| java.time.chrono | 提供对不同日历系统的访问 |
| java.time.format | 解析和格式化             |
| java.temporal    | 包括底层框架和扩展特性   |
| java.time.zone   | 包含时区支持             |

接口

```java
public interface TemporalAccessor {}
public interface Temporal extends TemporalAccessor {}
```

LocalDate和LocalDateTime都实现Temporal接口；





### API使用

#### 日期比较与计算

```java
LocalDate startDate;
LocalDate endDate;
// 计算两个日期之间相差月份
// ChronoUnit实际就是封装了Duration，将两个日期之间相差的秒或纳秒换算成对应的时间单位
long num = ChronoUnit.MONTHS.between(startDate, endDate); 
// 计算相差的年月日，（注意只看日期，不看时间是否相差达到24小时）
Period period = Period.between(startDate, endDate);
int days = period.getDays();
int years = period.getYears();
// 计算相差的秒或纳秒，接收参数类型Temporal接口子类，一般是Instant或LocalDateTime
Instant start = Instant.parse("2017-10-03T10:15:30.00Z");
Instant end = Instant.parse("2017-10-03T10:16:30.00Z");
Duration duration = Duration.between(start, end);
duration.get();
duration.getSeconds();
```

总结：

- 计算天数（按24小时算一天），使用ChronoUnit
- 计算天数（单看天，不看时分秒），使用Period
- 计算秒数，使用Duration

#### 日期格式化

```java
LocalDateTime now = LocalDateTime.now();
// 只取日期
String date1 = now.format(DateTimeFormatter.ISO_DATE);
// 给定日期格式
public static final YEAR_MONTH = "yyyy-MM";
String date2 = now.format(DateTimeFormatter.ofPattern(YEAR_MONTH));
```

#### 与时间戳互转

时间戳有10位和13位的，10位的以秒位单位，13位以毫秒为单位

```java
LocalDate localDate = LocalDate.now();
long timestamp = localDate.atStartOfDay(ZoneOffset.ofHours(8)).toInstant().toEpochMilli();

LocalDateTime localDateTime = LocalDateTime.now();
long timestamp = localDateTime.toInstant(ZoneOffset.ofHours(8)).toEpochMilli();

//
long second = 1615132800; // 10时间戳
Instant.ofEpochSecond(second).atZone(ZoneOffset.ofHours(8)).toLocalDate();
long mil = 1615132800000; // 13位时间戳
Instant.ofEpochMilli(mil).atZone(ZoneOffset.ofHours(8)).toLocalDateTime();
```







## 旧日期时间API（java.util）



## 新旧API的转换

## 其他

时间戳 + 时区 => 带时区的时间

时间 + 时区 => 时间戳 



[^JAVA中关于日期时间的API大总结]: https://blog.csdn.net/ye_mulin/article/details/104832894



[死磕18个Java8日期处理，工作必用！ (qq.com)](https://mp.weixin.qq.com/s/cuzt_5kiwfWsX2Rx8G6Hug)
