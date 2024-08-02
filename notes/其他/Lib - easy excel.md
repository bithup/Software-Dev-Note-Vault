# Easy Excel&EasyPOI

## 1.普通导出

## 2.复杂表头导出

### 二级表头

## 3.自定义字段处理器

EasyExcel默认不支持LocalDate类型，自定义处理器

```java
package com.joymedia.utils.excel;

import com.alibaba.excel.converters.Converter;
import com.alibaba.excel.enums.CellDataTypeEnum;
import com.alibaba.excel.metadata.CellData;
import com.alibaba.excel.metadata.GlobalConfiguration;
import com.alibaba.excel.metadata.property.ExcelContentProperty;

import java.time.LocalDateTime;
import java.time.format.DateTimeFormatter;

public class LocalDateTimeConverter implements Converter<LocalDateTime> {

    private static final DateTimeFormatter DEFAULT_PATTERN = DateTimeFormatter.ofPattern("yyyy-MM-dd HH:mm:ss");

    @Override
    public Class<LocalDateTime> supportJavaTypeKey() {
        return LocalDateTime.class;
    }

    @Override
    public CellDataTypeEnum supportExcelTypeKey() {
        return CellDataTypeEnum.STRING;
    }

    @Override
    public LocalDateTime convertToJavaData(CellData cellData,
                                           ExcelContentProperty excelContentProperty,
                                           GlobalConfiguration globalConfiguration) {
        return LocalDateTime.parse(cellData.getStringValue(), DEFAULT_PATTERN);
    }

    @Override
    public CellData<String> convertToExcelData(LocalDateTime localDateTime,
                                               ExcelContentProperty excelContentProperty,
                                               GlobalConfiguration globalConfiguration) {
        return new CellData<>(localDateTime.format(DEFAULT_PATTERN));
    }
}
```



## 4.导出性能问题

### 分批读取和写入，避免内存溢出

# Easy POI

[EasyPoi教程_V1.0 (mydoc.io)](http://easypoi.mydoc.io/#category_50222)

[SpringBoot实现Excel导入导出，性能爆表，用起来够优雅！ - 掘金 (juejin.cn)](https://juejin.cn/post/7051751438715715620)



### 一对多导出
