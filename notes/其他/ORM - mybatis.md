# ORM - mybatis

## JDBC

### jdbc操作数据库

```java
package com.lagou.demo;

import java.sql.*;

public class JDBCTest {
    private static final String url = "jdbc:mysql://shimmer.bitoy.org:31306/shimmer?useSSL=false&allowMultiQueries=true&zeroDateTimeBehavior=convertToNull&transformedBitIsBoolean=true";
    private static final String username = "root";
    private static final String password = "xghwep999xgh";

    public static void main(String[] args) {
        Connection connection = null;
        PreparedStatement preparedStatement = null;
        ResultSet resultSet = null;
        try {
            /*
             com.mysql.cj.jdbc.Driver类中包含静态代码块，在被加载时主动注册自己到DriverManager中
             新的jdbc不需要使用这一行加载驱动
            */
            //Class.forName("com.mysql.cj.jdbc.Driver"); 
            // jdbc使用SPI机制加载数据库驱动，getConnection的过程中会自动加载数据库驱动
            connection = DriverManager.getConnection(url, username, password);
            String sql1 = "select * from user where username = ?";
            preparedStatement = connection.prepareStatement(sql1);
            preparedStatement.setString(1, "';delete from user;#");
            resultSet = preparedStatement.executeQuery();
            System.out.println(preparedStatement);
            // 遍历结果集
            while (resultSet.next()) {
                System.out.println(resultSet.getInt("id"));
                System.out.println(resultSet.getString("username"));
            }
        } catch (SQLException e) {
            e.printStackTrace();
        } finally {
            try {
                assert resultSet != null;
                resultSet.close();
                preparedStatement.close();
                connection.close();
            } catch (SQLException e) {
                e.printStackTrace();
            }
        }

    }
}
```

### jdbc事务控制

```java
package com.lagou.demo;

import java.sql.*;

public class JDBCTest {
    private static final String url = "jdbc:mysql://shimmer.bitoy.org:31306/shimmer?useSSL=false&allowMultiQueries=true&zeroDateTimeBehavior=convertToNull&transformedBitIsBoolean=true";
    private static final String username = "root";
    private static final String password = "xghwep999xgh";

    public static void main(String[] args) {
        Connection connection = null;
        PreparedStatement preparedStatement1 = null;
        PreparedStatement preparedStatement2 = null;
        ResultSet resultSet = null;
        try {
            connection = DriverManager.getConnection(url, username, password);
            // 自动提交默认值为true，设置为false表示开启事务
            connection.setAutoCommit(false);
            // 设置事务隔离级别，不同级别对并发读取的性能有不同影响
            connection.setTransactionIsolation(Connection.TRANSACTION_READ_UNCOMMITTED);
            String sql1 = "delete from user where username = ?";
            String sql2 = "delete from user where username = ?";
            preparedStatement1 = connection.prepareStatement(sql1);
            preparedStatement1.setString(1, "tom");
            preparedStatement1.execute();
            preparedStatement2 = connection.prepareStatement(sql2);
            preparedStatement2.setString(2, "bob");
            preparedStatement2.execute();
            // 提交事务
            connection.commit();
        } catch (SQLException e) {
            e.printStackTrace();
            // 遇错回滚事务
            connection.rollback();
        } finally {
            try {
                assert resultSet != null;
                resultSet.close();
                preparedStatement.close();
                connection.close();
            } catch (SQLException e) {
                e.printStackTrace();
            }
        }

    }
}
```



### sql注入与PreparedStatement

在使用Statement对象的setString方法设置参数时，在参数中加入其他sql语句，并在字符串参数头部加上单引号，尾部加上井号；Statement对象不会对参数中的单引号进行处理，会执行参数中的sql，PreparedStatement会ch

```java
"';delete from user;#"
```

---



重写DTO的get / set 方法可以实现自定义的转化，比如查询出来的字段转为enumerate

EnumOrdinalTypeHandler能不能实现枚举的自动转换

数据库字段类型定义为enum，自动生成的代码对应的java类型

## 注解详解

## 复杂映射

## 分页

github pagehelper插件







## spring boot数据源配置

## 自定义类型处理器

### 数据库json类型处理器

1. json格式字段查询为List

```java
package com.joymedia.thor.mapper.typehandler;

import com.fasterxml.jackson.core.type.TypeReference;
import com.joymedia.thor.utils.JsonUtils;
import org.apache.ibatis.type.BaseTypeHandler;
import org.apache.ibatis.type.JdbcType;
import org.apache.ibatis.type.MappedJdbcTypes;
import org.apache.ibatis.type.MappedTypes;

import java.sql.CallableStatement;
import java.sql.PreparedStatement;
import java.sql.ResultSet;
import java.sql.SQLException;
import java.util.List;
import java.util.Map;

/**
 * 用以mysql中json格式的字段，进行转换的自定义转换器，转换为实体类的List属性
 * MappedTypes注解中的类代表此转换器可以自动转换为的java对象
 * MappedJdbcTypes注解中设置的是对应的JdbcType
 */
@MappedTypes(List.class)
@MappedJdbcTypes(JdbcType.OTHER)
public class JsonArrayHandler extends BaseTypeHandler<List<Map<String, Object>>> {

    //设置非空参数
    @Override
    public void setNonNullParameter(PreparedStatement ps, int i, List<Map<String, Object>> parameter, JdbcType jdbcType) throws SQLException {
        ps.setString(i, JsonUtils.toJSON(parameter));
    }

    //根据列名，获取可以为空的结果
    @Override
    public List<Map<String, Object>> getNullableResult(ResultSet rs, String columnName) throws SQLException {
        String sqlJson = rs.getString(columnName);
        if (null != sqlJson) {
            return JsonUtils.toObject(sqlJson, new TypeReference<List<Map<String, Object>>>() {
            });
        }
        return null;
    }

    //根据列索引，获取可以为空的结果
    @Override
    public List<Map<String, Object>> getNullableResult(ResultSet rs, int columnIndex) throws SQLException {
        String sqlJson = rs.getString(columnIndex);
        if (null != sqlJson) {
            return JsonUtils.toObject(sqlJson, new TypeReference<List<Map<String, Object>>>() {
            });
        }
        return null;
    }

    @Override
    public List<Map<String, Object>> getNullableResult(CallableStatement cs, int columnIndex) throws SQLException {
        String sqlJson = cs.getString(columnIndex);
        if (null != sqlJson) {
            return JsonUtils.toObject(sqlJson, new TypeReference<List<Map<String, Object>>>() {
            });
        }
        return null;
    }
}
```

2. json格式字段查询为Map

```java
package com.joymedia.thor.mapper.typehandler;

import com.fasterxml.jackson.core.type.TypeReference;
import com.joymedia.thor.utils.JsonUtils;
import org.apache.ibatis.type.BaseTypeHandler;
import org.apache.ibatis.type.JdbcType;
import org.apache.ibatis.type.MappedJdbcTypes;
import org.apache.ibatis.type.MappedTypes;

import java.sql.CallableStatement;
import java.sql.PreparedStatement;
import java.sql.ResultSet;
import java.sql.SQLException;
import java.util.Map;

/**
 * 用以mysql中json格式的字段，进行转换的自定义转换器，转换为实体类的Map属性
 * MappedTypes注解中的类代表此转换器可以自动转换为的java对象
 * MappedJdbcTypes注解中设置的是对应的JdbcType
 */
@MappedTypes(Map.class)
@MappedJdbcTypes(JdbcType.OTHER)
public class JsonObjectHandler extends BaseTypeHandler<Map<String, Object>> {
 
    //设置非空参数
    @Override
    public void setNonNullParameter(PreparedStatement ps, int i, Map<String, Object> parameter, JdbcType jdbcType) throws SQLException {
        ps.setString(i, JsonUtils.toJSON(parameter));
    }
    //根据列名，获取可以为空的结果
    @Override
    public Map<String, Object> getNullableResult(ResultSet rs, String columnName) throws SQLException {
        String sqlJson = rs.getString(columnName);
        if (null != sqlJson){
            return JsonUtils.toObject(sqlJson, new TypeReference<Map<String, Object>>() {
            });
        }
        return null;
    }
    //根据列索引，获取可以为空的结果
    @Override
    public Map<String, Object> getNullableResult(ResultSet rs, int columnIndex) throws SQLException {
        String sqlJson = rs.getString(columnIndex);
        if (null != sqlJson){
            return JsonUtils.toObject(sqlJson, new TypeReference<Map<String, Object>>() {
            });
        }
        return null;
    }
 
    @Override
    public Map<String, Object> getNullableResult(CallableStatement cs, int columnIndex) throws SQLException {
        String sqlJson = cs.getString(columnIndex);
        if (null != sqlJson){
            return JsonUtils.toObject(sqlJson, new TypeReference<Map<String, Object>>() {
            });
        }
        return null;
    }
}
```

思考：在需要扩展表字段时，可以将多个字段保持到一个json中，查询的时候再用自定义的handler转换为多个字段？

### 枚举类型处理器

参考：Java SE - Enum

#### mybatis提供的默认枚举处理器



## 自定义SQL拦截器

## 防止SQL注入

$()对应statement，#{}对应prepare statement

preparestatement会自动加单引号，防止sql注入

[MyBatis中#{}和${}的区别_bingguang1993的博客-CSDN博客_mybatis中#{}和${}的区别](https://blog.csdn.net/bingguang1993/article/details/105793795/)

${}和#{}的区别，为什么能防止注入statement和prepare statement的区别？

## 事务

[MyBatis：颠覆你心中对事务的理解 (qq.com)](https://mp.weixin.qq.com/s/u5iBq1sxIZ5A-vXMwSjxKg)



## 其他

mybatis的查询方法返回值

1. 返回类型是一个对象，如果查询不到记录，返回null，需要对返回对象判空；
2. 返回类型是一个列表，如果查询不到记录，返回空列表（不是null）；

## 批量插入

[MyBatis 三种批量插入方式的比较，我推荐第3个！ (qq.com)](https://mp.weixin.qq.com/s/Qar0E7Z9o7DCud9Psat4cQ)





[玩转 MyBatis：深度解析与定制 - LinkedBear - 掘金课程 (juejin.cn)](https://juejin.cn/book/6944917557878980638/section/6945996973203783687)
