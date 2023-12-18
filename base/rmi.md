# RMI

## 0x00 Introduction

>Java远程方法调用，即Java RMI（Java Remote Method Invocation）是Java编程语言里，一种用于实现远程过程调用的应用程序编程接口。它使客户机上运行的程序可以调用远程服务器上的对象。远程方法调用特性使Java编程人员能够在网络环境中分布操作。RMI全部的宗旨就是尽可能简化远程接口对象的使用。

接口的两种常见实现方式是：      
- 最初使用JRMP（Java Remote Message Protocol，Java远程消息交换协议）实现；
- 此外还可以用与CORBA兼容的方法实现。

RMI一般指的是编程接口，也有时候同时包括JRMP和API（应用程序编程接口），而RMI-IIOP则一般指RMI接口接管绝大部分的功能，以支持CORBA的实现。

## 0x01 RMI
推荐阅读：   
- [Java RMI原理及反序列化学习
](https://y4er.com/posts/java-rmi/)

## 0x02 危险函数



### 客户端

`java.rmi.registry.LocateRegistry.getRegistry()`
`java.rmi.Naming.lookup()`

```java
package com.test.rmi;

import java.rmi.Naming;
import java.rmi.registry.LocateRegistry;
import java.rmi.registry.Registry;

import static com.test.rmi.RMIServer.RMI_NAME;

public class RMIClient {
    public static void main(String[] args) {
        try {
            // 获取服务注册器
            Registry registry = LocateRegistry.getRegistry("127.0.0.1", 8989);
            // 获取所有注册的服务
            String[] list = registry.list();
            for (String i : list) {
                System.out.println("已经注册的服务：" + i);
            }

            // 寻找RMI_NAME对应的RMI实例
            RMIInterface rt = (RMIInterface) Naming.lookup(RMI_NAME);

            // 调用Server的hello()方法,并拿到返回值.
            String result = rt.hello();

            System.out.println(result);

        } catch (Exception e) {
            e.printStackTrace();
        }
    }
}
```

### 服务端

```java
package com.test.rmi;

import java.rmi.Naming;
import java.rmi.registry.LocateRegistry;

public class RMIServer {
    public static String HOST = "127.0.0.1";
    public static int PORT = 8989;
    public static String RMI_PATH = "/hello";
    public static final String RMI_NAME = "rmi://" + HOST + ":" + PORT + RMI_PATH;

    public static void main(String[] args) {
        try {
            // 注册RMI端口
            LocateRegistry.createRegistry(PORT);

            // 创建一个服务
            RMIInterface rmiInterface = new RMIImpl();

            // 服务命名绑定
            Naming.rebind(RMI_NAME, rmiInterface);

            System.out.println("启动RMI服务在" + RMI_NAME);
        } catch (Exception e) {
            e.printStackTrace();
        }
    }

}
```