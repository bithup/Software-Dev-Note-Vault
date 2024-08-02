# Coding Guidelines

## 字面量和常量

代码中尽量不要直接使用字面量，推荐使用常量



## MySQL

表名前缀：系统规模大，建议加表名前缀

字段名：id前加表名，下划线分隔

字段类型对应：

多对多关联：中间表

一对多、多对一关联：外键关联

多对多关联mybatis查询

唯一约束：唯一索引

主键约束：多列主键



1. 绘制E-R图

2. 绘制数据库模型图

3. 数据库设计

   工具：Power Designer、navicat、Visio



[数据库设计的 10 个最佳实践 (qq.com)](https://mp.weixin.qq.com/s/Bsc8bziUPj-7p2fmYIBYWw)

[数据库设计 (biancheng.net)](http://c.biancheng.net/mysql/35/)



### 命名规范

1. 库名
2. 表名
3. 字段名
4. 索引
   1. 普通索引
   2. 唯一索引：uni_开头
5. 以UNI开头，下划线分隔字段名

索引设计规范

字段长度

### 可扩展性