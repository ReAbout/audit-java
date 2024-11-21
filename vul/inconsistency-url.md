# URL解析的不一致性

## 0x00 Introduction

Tomcat是常见的Web中间件，实际上是利用NIO技术处理HTTP请求，在接收到请求时会对客户端提交的参数、URL、Header和Body数据进行解析，并生成Request对象，然后调用实际的JSP或Servlet。

当后台程序使用getRequestURI()或getRequestURL()函数来解析用户请求的URL时，若URL中包含了一些特殊符号，则可能会造成访问限制绕过的安全风险。

## 0x01 危险函数

Tomcat URL解析：   

Servlet的匹配路径为/test%3F/*，并且Web应用是部署在/app下，此时请求的URL为http://30thh.loc:8480/app/test%3F/a%3F+b;jsessionid=s%3F+ID?p+1=c+d&p+2=e+f#a，各个函数解析如下表：
| 函数               | URL解码 | 解析结构                                                         |   |   |   |   |   |   |   |
|------------------|-------|--------------------------------------------------------------|---|---|---|---|---|---|---|
| getRequestURL()  | no    | http://30thh.loc:8480/app/test%3F/a%3F+b;jsessionid=s%3F+ID  |   |   |   |   |   |   |   |
| getRequestURI()  | no    | /app/test%3F/a%3F+b;jsessionid=s%3F+ID                       |   |   |   |   |   |   |   |
| getContextPath() | no    | /app                                                         |   |   |   |   |   |   |   |
| getServletPath() | yes   | /test?                                                       |   |   |   |   |   |   |   |
| getPathInfo()    | yes   | /a?+b                                                        |   |   |   |   |   |   |   |

详细分析见：[Tomcat URL解析差异性导致的安全问题](https://xz.aliyun.com/t/7544)


## Ref
-[Tomcat URL解析差异性导致的安全问题](https://xz.aliyun.com/t/7544)