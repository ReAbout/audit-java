# 反序列化

## 0x00 Java序列化和反序列化

Java 序列化：把 Java 对象转换为字节序列的过程便于保存在内存、文件、数据库中，ObjectOutputStream类的 writeObject() 方法可以实现序列化。
Java 反序列化：把字节序列恢复为 Java 对象的过程，ObjectInputStream 类的 readObject() 方法用于反序列化。

>一个类的对象要想序列化成功，必须满足两个条件：
1. 该类必须实现 java.io.Serializable 接口。
2. 该类的所有属性必须是可序列化的。如果有一个属性不是可序列化的，则该属性必须注明是短暂的。

## 0x01 Java反序列化漏洞成因

1. 反序列化数据输入点
2. 反序列化操作触发函数（类似PHP魔法函数）
3. 漏洞利用执行函数序列


## 0x02 Java反序列化基础知识

这篇文章[Java 中的序列化和反序列化](https://dyfloveslife.github.io/2020/03/21/Serialization-and-Deserialization-in-Java/)从编程的角度把反序列化讲的很清楚。


## 0x03 如何发现反序列化漏洞

### 反序列化函数

```java
ObjectInputStream.readObject
ObjectInputStream.readUnshared
XMLDecoder.readObject
Yaml.load
XStream.fromXML
ObjectMapper.readValue
JSON.parseObject
```



## Ref
- [深入理解 JAVA 反序列化漏洞](https://paper.seebug.org/312/#2)
- https://yinwc.github.io/2020/02/08/java%E5%8F%8D%E5%BA%8F%E5%88%97%E5%8C%96%E6%BC%8F%E6%B4%9E
- http://llfam.cn/2019/11/11/spring_4.2.4_unser/
- https://www.iswin.org/2015/11/13/Apache-CommonsCollections-Deserialized-Vulnerability/
- https://paper.seebug.org/1318/
- [Apache Solr反序列化远程代码执行漏洞分析（CVE-2019-0192）](https://www.anquanke.com/post/id/210866?luicode=10000011&lfid=1076033957583411&featurecode=newtitle%0AUltrasonic+Fingerprint+ID+on+the+Galaxy+S10%3A+Pesto+Fingers&u=https%3A%2F%2Fwww.anquanke.com%2Fpost%2Fid%2F210866)
-[Weblogic CVE-2021-2394 反序列化漏洞分析](https://www.anquanke.com/post/id/249654)

