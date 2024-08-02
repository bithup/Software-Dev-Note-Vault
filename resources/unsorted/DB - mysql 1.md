# DB - mysql

## MySQL基础

### 数据定义语言（DDL）

#### 定义数据库

##### 语法

```sql
-- 创建表
CREATE DATABASE/SCHEME [IF NOT EXISTS] <数据库名>
[[DEFAULT] CHARACTER SET <字符集名>]
[[DEFAULT] COLLATE <校对规则名>]
-- 修改表结构

-- 删除表
```

1. []表示可选部分
2. 如果不指定字符集名和校对规则名，则使用默认值
3. 指定字符集名和校对规则名时，DEFAULT可不写
4. mysql5.7和mysql8.0中关键字DATABASE和关键字SCHEME等价
5. COLLATE是字符型数据（char/varchar/text）排序和比较的规则，会影响order by/group by/having/distinct语句的结果，以及字符类型的索引。后缀_ci表示大小写不敏感
6. ❓：大小写不敏感时，如果查询某字段为a的行，结果中是否包含等于A的行。



##### 示例

```sql
-- 使用默认值创建数据库
CREATE DATABASE `test`
-- 使用指定值创建数据库
CREATE SCHEME `test` CHARACTER SET utf8mb4 COLLATE utf8mb4_unicode_ci;
-- 修改数据库属性
-- 删除数据库
```

#### 定义表

##### 语法

```sql
-- 创建表
CREATE TABLE <表名>
(
<字段名>	<数据类型>	<编码格式>	<COLLATE>	<是否为空>	<默认值>	<备注>
...
PRIMARY KEY (<主键>) USING BTREE,
UNIQUE INDEX <唯一索引> USING BTREE COMMENT '达人和平台唯一约束'
)
-- 修改表

```

1. 在MYSQL中， 若一张表里面不存在varchar、text以及其变形、blob以及其变形的字段的话，那么张这个表其实也叫静态表[static/fixed ]，即该表的row_format是fixed，就是说每条记录所占用的字节一样。其优点读取快，缺点浪费额外一部分空间。静态表改为动态表，会导致CHAR变为VARCHAR，反之亦然。
2. 🧲：设计表时，如果有对查询性能要求高的表，设计成静态的表，边长的字段单独放在一个表中。



##### 示例

```sql
CREATE TABLE `thor`.`influencer_platforms`
(
    `id`          bigint(20)                                                    NOT NULL AUTO_INCREMENT COMMENT '主键id',
    `influencer`  bigint(20)                                                    NOT NULL COMMENT '达人id',
    `platform`    tinyint(4)                                                    NOT NULL COMMENT '平台：1-快手，2-小红书，3-b站，4-微博，5-微信视频号，6-其他',
    `nickname`    varchar(30) CHARACTER SET utf8mb4 COLLATE utf8mb4_general_ci  NULL DEFAULT NULL COMMENT '昵称',
    `link`        varchar(300) CHARACTER SET utf8mb4 COLLATE utf8mb4_general_ci NULL DEFAULT NULL COMMENT '链接',
    `fans`        varchar(30) CHARACTER SET utf8mb4 COLLATE utf8mb4_general_ci  NULL DEFAULT NULL COMMENT '粉丝量',
    `remarks`     varchar(300) CHARACTER SET utf8mb4 COLLATE utf8mb4_general_ci NULL DEFAULT NULL COMMENT '备注',
    `create_time` datetime                                                      NULL DEFAULT NULL COMMENT '创建时间',
    `creator`     bigint(20)                                                    NULL DEFAULT NULL COMMENT '创建人ID',
    `modify_time` datetime                                                      NULL DEFAULT NULL COMMENT '修改时间',
    `modifier`    bigint(20)                                                    NULL DEFAULT NULL COMMENT '修改人ID',
    PRIMARY KEY (`id`) USING BTREE,
    UNIQUE INDEX `uni_influencer_platform` (`influencer`, `platform`) USING BTREE COMMENT '达人和平台唯一约束'
) ENGINE = InnoDB
  AUTO_INCREMENT = 1
  CHARACTER SET = utf8mb4
  COLLATE = utf8mb4_general_ci COMMENT = '达人外站平台'
  ROW_FORMAT = Dynamic;
```

### 数据操纵语言（DML） 

#### insert语句

```sql
INSERT INTO <表名> (column1, column2) VALUES (val1, val2);
```

#### delete语句

```sql
DELETE FROM <表名> WHERE <条件>
```

#### update语句

```sql
UPDATE <表名> SET column1 = val1, column2 = val2 WHERE <条件>
```

### 数据查询语言（DRL）

```sql
SELECT column1, column2 FROM <表名>  [join on]  [WHERE <>] [GROUP BY column1] [HAVING] [UNION] [ORDER BY] [LIMIT m, n]

```

#### 连接查询

[MySQL的几种连接 join/inner join/cross join/逗号/left join/right join/natural join](https://www.cnblogs.com/liulaoshi/p/6219540.html)

> 在不使用on语法时，join、inner join、逗号、cross join结果相同，都是取2个表的笛卡尔积。逗号与其他操作符优先级不同，所以有可能产生语法错误，尽量减少用逗号
>
> join、inner join、cross join支持on和using语法，逗号不支持on和using语法

##### 逗号JOIN



##### LEFT/RIGHT JOIN



##### INNER/OUTER jOIN



#### 查询子句

##### GROUP BY与聚合函数

select语句中存在group by子句时，select的字段必须时group by的字段或聚合函数的结果

##### WHERE 与 HAVING



UNION 与 UNION ALL



##### 子句的顺序

- 语法顺序

```sql
select distinct from join on where [group by] having union [order by] limit
```

- 执行顺序

```sql
from on join where [group by] having select distinct union [order by] limit
```

#### 内置函数

##### 聚合函数

求值

```sql
min()
max()
avg()
sum()
-- 只试用于单个条件判断,如果筛选条件很多,我们可以用sum(case when then else end)来进行多条件筛选
sum(if)
```

统计

```sql
-- count函数会忽略null值
COUNT()
COUNT(IF(exp1, exp2, exp3))
COUNT(DISTINCT IF(exp1, exp2, exp3))
```

使用场景：例如订单表order如下，需要统计订单的数量，关联的订单计为一单，可以使用count(distinct if())组合实现

| 字段名      | 字段描述     |      |
| ----------- | ------------ | ---- |
| id          |              |      |
| relative_id | 订单关联的id |      |
| user_id     | 用户         |      |

##### 条件函数

```sql
IF()
IFNULL()
case  when then else end
```

##### 日期函数

```sql
-- 近30天
SELECT * FROM inquiries where DATE_SUB("2021-07-29 18:29:23",INTERVAL 30 day) < create_time
SELECT * FROM inquiries WHERE TIMESTAMPDIFF(SECOND,"2021-07-29 18:29:23",create_time) BETWEEN 0 and  360000
-- DATE()函数取datetime中的date

```

1. mysql比较大小不支持左右两边都有大于号或小于号，可以使用between代替
2. 使用`BETWEEN`比较，包含头和尾。注意如果数据库是DateTime的类型，传入的Java类型是LocalDate，MySQL在比较时会自动补全0时间。

##### 字符串函数

```sql
FIND_IN_SET()
```

##### JSON函数

```sql
JSON_EXTRACT()
```

#### DUAL表的作用

[mysql dual表的用途及案例 (icode9.com)](https://www.icode9.com/content-2-746595.html)



### 事务控制语言（TCL）

- 事务就是多步数据库操作，如果有一步操作失败，整个事务失败
- 原子性（atomicity）、一致性（consistency）、隔离性（isolation）、持续性（durability）。这四个属性通常称为ACID特性
- 事务的提交有自动提交和手动提交，set autocommit = true;
- 手动提交模式下：commit提交事务，savepoint name_point保存还原点，rollback name_point回滚事务



### 数据类型

| MySQL类型  | Java类型 | 备注            |
| ---------- | -------- | --------------- |
| tinyint(1) | boolean  | 0:false，1:true |
| json       |          |                 |
| char(1)    |          |                 |

tinyint默认长度4

使用char(1)和tinyint(1)存放Boolean值



### 关键字&操作符

#### 数据类型定义

#### 表定义



#### CRUD关键字

判断是否为空，应该使用`IS NULL`或`IS NOT NULL`，而不能使用`=`

### 约束&索引

约束：非空/主键/外键/唯一性

#### 主键(Primary Key)



#### 外键约束（Foreign Keys）

##### 创建外键的要素

![](img/mysql-1637140935.png)

| Name              |      |      |
| ----------------- | ---- | ---- |
| Fields            |      |      |
| Referenced Scheme |      |      |
| Referenced Table  |      |      |
| Referenced Fields |      |      |
| On Delete         |      |      |
| On Update         |      |      |



先看On Delete属性，可能取值如上图为：No Action, Cascade,Set Null, Restrict属性。

当取值为No Action或者Restrict时，则当在父表（即外键的来源表）中删除对应记录时，首先检查该记录是否有对应外键，如果有则不允许删除。

当取值为Cascade时，则当在父表（即外键的来源表）中删除对应记录时，首先检查该记录是否有对应外键，如果有则也删除外键在子表（即包含外键的表）中的记录。

当取值为Set Null时，则当在父表（即外键的来源表）中删除对应记录时，首先检查该记录是否有对应外键，如果有则设置子表中该外键值为null（不过这就要求该外键允许取null）。





当取值为No Action或者Restrict时，则当在父表（即外键的来源表）中更新对应记录时，首先检查该记录是否有对应外键，如果有则不允许更新。

当取值为Cascade时，则当在父表（即外键的来源表）中更新对应记录时，首先检查该记录是否有对应外键，如果有则也更新外键在子表（即包含外键的表）中的记录。

当取值为Set Null时，则当在父表（即外键的来源表）中更新对应记录时，首先检查该记录是否有对应外键，如果有则设置子表中该外键值为null（不过这就要求该外键允许取null）。



##### 外键命名规范



#### 索引（Index）

- 创建/删除索引的语法

```sql
-- 创建表时
CREATE TABLE 表名 (
   字段名1  数据类型 [完整性约束条件…],
   字段名2  数据类型 [完整性约束条件…],
   [UNIQUE | FULLTEXT | SPATIAL ]   INDEX | KEY
   [索引名]  (字段名[(长度)]  [ASC |DESC]) 
);
-- 在已存在的表上创建索引
CREATE  [UNIQUE | FULLTEXT | SPATIAL ]  INDEX  索引名 
	ON 表名 (字段名[(长度)]  [ASC |DESC]) ;
-- 在已存在的表上创建索引
ALTER TABLE 表名 ADD  [UNIQUE | FULLTEXT | SPATIAL ] INDEX
	索引名 (字段名[(长度)]  [ASC |DESC]) ;                          
-- 删除索引：
DROP INDEX 索引名 ON 表名字;
```

- 索引的类型
  1. 唯一索引（UNIQUE）
  2. 普通索引（NORMAL）
  3. 空间索引（SPATIAL）
  4. 全文索引（FULLTEXT）

- 索引的数据结构类型
  1. BTREE：适合范围查询
  2. HASH：适合等值查询
  3. FULL-TEXT
- 索引的物理存储类型
  1. 聚簇索引
  2. 二级索引（辅助索引）
- 组合索引
  1. 组合索引生效规则
- 主键索引
  1. PRIMARY KEY = UNIQUE + NOT NULL
  2. 每张表可以有多个唯一索引，但只能有一个主键索引

[MySQL模糊查询再也不用like+%了 - PHP开发工程师的个人空间 - OSCHINA - 中文开源技术交流社区](https://my.oschina.net/u/5079097/blog/5361039)



#### 索引失效场景

```sql
-- 使用EXPLAIN关键字加sql语句，查看索引是否失效
```

[MySql底层索引与数据优化【上篇】 - 掘金 (juejin.cn)](https://juejin.cn/post/7052228858602324004)

[MySql底层索引与数据优化【下篇】 - 掘金 (juejin.cn)](https://juejin.cn/post/7052239841580285988)

### 存储过程（Procedure）&函数(Function)

#### 存储过程

一个简单的存储过程例子

```sql
DELIMITER //
DROP PROCEDURE if EXISTS `UPDATE_TRAIN_TA`;
CREATE PROCEDURE UPDATE_TRAIN_TA()
BEGIN
	DECLARE s int DEFAULT 0;
	DECLARE out_ta VARCHAR(30);
	DECLARE ta VARCHAR(30);
	DECLARE sn VARCHAR(20);
	DECLARE cur CURSOR FOR SELECT tob.out_travel_apply_no AS out_ta, sta.id AS ta, sta.serial_number AS sn FROM train_order_base tob LEFT JOIN sys_travel_applies sta ON sta.out_travel_apply_no = tob.out_travel_apply_no WHERE tob.serial_number is null;
	DECLARE CONTINUE HANDLER FOR NOT FOUND SET s = 1;
	OPEN cur;
	FETCH cur INTO out_ta, ta, sn;
	WHILE s < 1 DO
			UPDATE train_order_base SET travel_apply_no = ta, serial_number = sn WHERE out_travel_apply_no = out_ta;
			FETCH cur INTO out_ta, ta, sn;
	END WHILE;
	CLOSE cur;
END
//
DELIMITER ;
CALL UPDATE_TRAIN_TA();
```

> 注意：存储过程中定义的字段的类型长度不能小于表中相应字段的长度，否则存储过程执行不报错，但是结果没变化。

#### 函数

函数有返回值，可以在查询语句，插入语句中使用函数，

什么场景适合使用自定义函数

### 视图（View）



### 触发器（Trigger）



### 事务

- 事务的使用场景：转账、删除有关联的多张表中的数据

- begin开启事务，rollback回滚，commit提交结束事务

- 事务隔离级别
  
- 五、事务隔离级别
  
  1、事务的并发读问题
  
  - 脏读：读取到另外一个事务未提交数据（不允许出来的事）；
  - 不可重复读：两次读取不一致；
  - 幻读（虚读）：读到另一事务已提交数据。
  
  2、并发事务问题
  
  因为并发事务导致的问题大致有5类，其中两类是更新问题三类是读问题。
  
  - **脏读（dirty read）：读到另一个事务的未提交新数据，即读取到了脏数据；**
  - **不可重复读（unrepeatable）：对同一记录的两次读取不一致，因为另一事务对该记录做了修改；**
  - **幻读（虚读）（phantom read）：对同一张表的两次查询不一致，因为另一事务插入了一条记录。**
  
  3、四大隔离级别
  
    4个等级的事务隔离级别，在相同的数据环境下，使用相同的输入，执行相同的工作，根据不同的隔离级别，可以导致不同的结果。不同事务隔离级别能够解决的数据并发问题的能力是不同的。
  
  1、SERIALIZABLE(串行化)
  
  - 不会出现任何并发问题，因为它是对同一数据的访问是串行的，非并发访问的；
  - 性能最差
  
  **2、REPEATABLE READ(可重复读)（MySQL）**
  
  - **防止脏读和不可重复读，不能处理幻读**
  - **性能比SERIALIZABLE好**
  
  **3、READ COMMITTED(读已提交数据)（Oracle）**
  
  - **防止脏读，不能处理不可重复读和幻读；**
  - **性能比REPEATABLE READ好**
  
  4、READ UNCOMMITTED(读未提交数据)
  
  - 可能出现任何事物并发问题，什么都不处理。
  - 性能最好
  
  六、MySQL隔离级别
  
  MySQL的默认隔离级别为Repeatable read,可以通过下面语句查看：
  
  > SELECT @@`TX_ISOLATION`;
  
  也可以通过下面语句来设置当前连接的隔离级别：
  
  > SET TRANSACTION ISOLATION LEVEL REPEATABLE READ ;//[4选1]
  
- 
  
  

### 存储引擎

#### 存储引擎特点

使用`show engines`查看支持的引擎，不同引擎适用不同场景

| 引擎               | 特点                                               |
| ------------------ | -------------------------------------------------- |
| InnoDB             | 默认引擎，最常用，只有InnoDB支持事务、行锁、外键   |
| MyISAM             | 不支持事务等功能，查询效率高，修改效率低，支持分表 |
| MRG_MYISAM         | MyISAM的组合                                       |
| MEMEORY            | 速度快，适合缓存数据                               |
| BLACKHOLE          |                                                    |
| CSV                |                                                    |
| ARCHIVE            |                                                    |
| PERFORMANCE_SCHEMA |                                                    |

#### 存储引擎支持的功能

| 功能     | InnoDB | MyISAM | Memory | MGR_MyISAM | CSV  |      |      |
| -------- | ------ | ------ | ------ | ---------- | ---- | ---- | ---- |
| BTREE    |        |        |        |            |      |      |      |
| HASH     |        |        |        |            |      |      |      |
| FULLTEXT |        |        |        |            |      |      |      |
| 事务     |        |        |        |            |      |      |      |
| 外键     |        |        |        |            |      |      |      |
| 行锁     |        |        |        |            |      |      |      |
| 表锁     |        |        |        |            |      |      |      |
| 分表     |        |        |        |            |      |      |      |
|          |        |        |        |            |      |      |      |

#### 锁

- 行锁和表锁
- 表锁虽然开销小，锁表快，但高并发下性能低。行锁虽然开销大，锁表慢，但高并发下相比之下性能更高。事务和行锁都是在确保数据准确的基础上提高并发的处理能力
- InnoDB更适合高并发场景

## MySQL日志

> Mysql中比较重要的日志包括二进制日志、relay_log(中继日志)、slow_query_log(慢查询日志)、redo_log、undo_log等

### 慢SQL日志（slow_query_log）

#### 配置慢SQL

```ini
# 是否开启慢查询日志
slow_query_log=1
# 查询阈值，超过了该阈值则记录到慢查询日志中,最小值和默认值分别为0  10，单位为秒
long_query_time=1 
# 如何存储慢查询日志，可选项：FILE或者TABLE
log_output=FILE
# 以FILE类型存储慢查询日志时的存储位置，如果没有为慢查询日志指定名称，默认为host_name-slow.log
slow_query_log_file
```

> 开启慢查询日志或多或少的会对mysql的性能产生一些影响，可以在排除问题是开启，解决后关闭。使用`set global slow_query_log on`临时开启配置，更改为TABLE存储后，默认存储在mysql.slow_log表中。

```sql
-- 查询mysql.slow_log表
SELECT CAST(sql_text AS char) FROM slow_log;
```



#### 慢SQL的格式

## MySQL调优

Select *，系统要先解析出所有字段的名字，如果记录数特别多，效率低

#### 多条join语句如何优化

a表是商单表，b表是商单代理费提报表，a和b是一对多关系，c表是代理费结算表，b和c是一对一关系；

商单代理费提报审核通过后，填写代理费结算信息；

需求：统计代理费提报通过，还没有填写结算信息的商单



连接查询优化

- mysql只支持循环嵌套链接算法（三种实现），优化器根据情况选择
- 关联字段有索引，可一减少链接查询时比较的次数。
- 如果join两张表很大，必须要在关联字段上建立索引
- left join，以左表为基准
- where条件在join条件之后生效



只有一个数据库，从MySQL使用层面上优化，比如合理的建立索引，使用合理的引擎，分库分表等

[MySQL 亿级数据分页的优化 (qq.com)](https://mp.weixin.qq.com/s/FOG_A6ZhhkmOmf47d0K-2g)

[爆肝，52条SQL语句，性能优化，干货必收藏 ！ (qq.com)](https://mp.weixin.qq.com/s/R3i6BgHP6yvRLXAxFYsdmA)

[SQL优化还不会？速来掌握这15个小技巧 (qq.com)](https://mp.weixin.qq.com/s/w7c_sJ4GV-MeIrldgAr7dQ)

## MySQL架构

### 集群架构下的性能优化方案

### 集群架构下的数据一致性问题

#### 主从复制问题

### 



### 分库分表

[MySQL分库分表会带来哪些棘手的问题？ (qq.com)](https://mp.weixin.qq.com/s/dLj95iVSSoyIr7c1_S8aQg)

**单库单表**

**单库多**

**多库多表**

[(14条消息) MySQL 分库分表，写得太好了！_azhuyangjun的博客-CSDN博客_mysql分库分表](https://blog.csdn.net/azhuyangjun/article/details/86976568)



- 将不同业务数据的表放到不同数据库中
- 可以按ID的哈希值或其他规则将表分片，当用户增加时，可增加一台服务，增加一个分片
- 单库单表
- 将表水平拆分，分表
- 
- 读写分离
- 主从备份

### 可用性

备份

## 其他

1. 现有id集合A，想判断A中的id是否存在表t_a中，如果存在执行更新，使用判断条件`WHERE id IN A`，当集合A比较大时，是否会有性能问题，如何解决？
2. 判断字段值是否为 `NULL` 不能使用 `=` 符号，应该使用 `IS` 关键字 
3. MySQL集合运算，求并集union 和union all：union会去重，MySQL不支持求交集和差集。
4. 关于是否在数据库中建立约束，数据库约束会影响性能，开发阶段设置约束，生产环境删除约束，保留重要的约束，比如与金钱相关的。



max() 和case when then结合使用

[在mysql中使用Max和CASE - 我爱学习网 (5axxw.com)](https://www.5axxw.com/questions/content/x6nk01)

```sql
SELECT distinct taed.DEPARTMENT_ID
 FROM t_actor_score tas
          LEFT JOIN t_auth_employee tae ON tae.id = tas.APPLY_EMP_ID
          LEFT JOIN t_auth_employee_department taed ON tae.id = taed.EMPLOYEE_ID
 union
 select id
 from t_auth_department
 where TYPE = 2;

select ID,NAME
from t_auth_department
where TYPE = 2 OR id in
(
    SELECT distinct taed.DEPARTMENT_ID
    FROM t_actor_score tas
    LEFT JOIN t_auth_employee tae ON tae.id = tas.APPLY_EMP_ID
    LEFT JOIN t_auth_employee_department taed ON tae.id = taed.EMPLOYEE_ID

);
select
    t.id,
    (case t.state when 0 then t.num END) num1,
    (case t.state when 1 then t.num END) num2,
    (case t.state when 2 then t.num END) num3,
    (case t.state when 3 then t.num END) num4,
    (case t.state when 5 then t.num END) num5
from
(select count(tas.ID) num, tas.STATE AS state, taed.DEPARTMENT_ID as id
FROM t_actor_score tas
          LEFT JOIN t_auth_employee tae ON tae.id = tas.APPLY_EMP_ID
          LEFT JOIN t_auth_employee_department taed ON tae.id = taed.EMPLOYEE_ID
where tas.CREATE_TIME between "2020-01-01" and "2022-05-01" group by taed.DEPARTMENT_ID, tas.stATE) t group by id;



select count(1) num , tas.STATE AS state, taed.DEPARTMENT_ID as id
FROM t_actor_score tas
         LEFT JOIN t_auth_employee tae ON tae.id = tas.APPLY_EMP_ID
         LEFT JOIN t_auth_employee_department taed ON tae.id = taed.EMPLOYEE_ID
where tas.CREATE_TIME between "2020-01-01" and "2022-05-01" group by taed.DEPARTMENT_ID, tas.stATE
```

