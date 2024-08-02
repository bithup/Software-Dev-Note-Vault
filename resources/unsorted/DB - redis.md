# DB - redis

## 基础命令

- String/Hash/List/Set/Sorted Set
- 登陆命令：redis-cli -h host -p port -a password

操作key相关的命令

| 命令               | 作用         |
| ------------------ | ------------ |
| Exists key         | 判断是否存在 |
| Del key            | 删除         |
| Dump key           | 序列化       |
| Expire key seconds | 设置过期时间 |
| Rename key newly   | 重命名       |
| type key           | 判断数据类型 |
|                    |              |
|                    |              |
|                    |              |

字符串相关命令

| 命令                   | 作用                        |
| ---------------------- | --------------------------- |
| Set key val            | 设置值                      |
| get key                | 获取值                      |
| Getrange key start end | 获取子字符串                |
| Strlen key             | 判断长度                    |
| Setnx key value        | key不存在就设置，存在就不变 |
|                        |                             |
|                        |                             |
|                        |                             |
|                        |                             |

## Redisson

[分布式锁中的王者方案：Redisson (qq.com)](https://mp.weixin.qq.com/s/25V2kPgMOTUWVAqENBo89Q)

## Redis解决方案

击穿，穿透，雪崩

## Redis使用场景

[Java+Redis位图实现点赞签到相关功能 (qq.com)](https://mp.weixin.qq.com/s/ia-TlsnGc3Vwr18_8LIsrw)



## spring redis

spring data redis 

Redis OM for Spring[Redis 发布对象映射库 Redis OM - OSCHINA - 中文开源技术交流社区](https://www.oschina.net/news/170810/redis-om-client-libraries)

