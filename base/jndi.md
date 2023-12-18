# JNDI

## 0x00 Introduction

>JNDI(Java Naming and Directory Interface)是一个应用程序设计的API，为开发人员提供了查找和访问各种命名和目录服务的通用、统一的接口，类似JDBC都是构建在抽象层上。 现在JNDI已经成为J2EE的标准之一，所有的J2EE容器都必须提供一个JNDI的服务。

JNDI支持的服务主要有：DNS、LDAP、CORBA、RMI等。    


## 0x01 JNDI
推荐阅读：    
- [浅析JNDI注入](https://www.mi1k7ea.com/2019/09/15/%E6%B5%85%E6%9E%90JNDI%E6%B3%A8%E5%85%A5/)


## 0x02 危险函数
`javax.naming.InitialContext`的`lookup()`方法。
jndi客户端示例：     
```java
package com.rmi.demo;

import javax.naming.InitialContext;
import javax.naming.NamingException;

public class jndi {
    public static void main(String[] args) throws NamingException {
        String uri = "rmi://127.0.0.1:1099/Exploit";    // 指定查找的 uri 变量
        InitialContext initialContext = new InitialContext();// 得到初始目录环境的一个引用
        initialContext.lookup(uri); // 获取指定的远程对象

    }
}
```

## 0x03 限制绕过

JDK 11.0.1、8u191、7u201、6u211，trustURLCodebase default false，限制了远程加载codebase。

bypass:触发本地Gadget.


## Ref
- https://www.mi1k7ea.com/2019/09/15/%E6%B5%85%E6%9E%90JNDI%E6%B3%A8%E5%85%A5/
- https://xz.aliyun.com/t/12277
- https://m0d9.me/2020/07/23/JNDI-LDAP%20%E6%B3%A8%E5%85%A5%E5%8F%8A%E9%AB%98%E7%89%88%E6%9C%ACJDK%E9%99%90%E5%88%B6%E2%80%94%E2%80%94%E4%B8%8A/ 高版本限制 