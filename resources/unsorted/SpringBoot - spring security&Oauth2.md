# SpringBoot - security&Oauth2

## 登录校验

全局登录校验，使用过滤器，排除不需要登录接口

不使用全局过滤，在需要登录的接口中，调用登录校验，比如获取登录用户信息，如果获取不到就结束

优缺点：全局过滤能同一管理，方便，但是在测试时不方便，

思考：**如何在本地测试时，让全局登录过滤器失效，在线上环境生效？**

## 用户角色权限设计

## 

垂直权限认证：RBAC

水平权限认证：

单点登录：CAS

第三方授权：微信、支付宝

## 单个接口权限控制

使用注解对单个接口权限进行控制

## 水平权限的控制

用户即使登录，也只能访问自己的数据；

## 接口防止重复提交



## shiro

## spring security



[实战篇：Security+JWT组合拳 | 附源码 (qq.com)](https://mp.weixin.qq.com/s/bn0AVoKa49iuEyhtg4VyjA)



[Spring新的授权服务器Spring Authorization Server入门 (qq.com)](https://mp.weixin.qq.com/s/dFTsq9dUcJ20ujEQbUZdNw)
