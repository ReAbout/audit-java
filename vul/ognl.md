# 对象导航图语言(OGNL)注入

## 0x00 Introduction

>OGNL (Object-Graph Navigation Language)即对象导航图语言，它是一种功能强大的表达式语言，通过它简单一致的表达式语法，可以存取对象的任意属性，调用对象的方法，遍历整个对象的结构图，实现字段类型转化等功能。它使用相同的表达式去存取对象的属性。这样可以更好的取得数据。

OGNL三要素(也就是核心函数的三个参数):
- 表达式（Expression）
表达式是整个OGNL的核心，所有的OGNL操作都是针对表达式的解析后进行的。表达式会规定此次OGNL操作到底要干什么。因此，表达式其实是一个带有语法含义的字符串，这个字符串将规定操作的类型和操作的内容。
OGNL支持大量的表达式语法，不仅支持“链式”描述对象访问路径，还支持在表达式中进行简单的计算，甚至还能够支持复杂的Lambda表达式等。
- Root对象（Root Object）
OGNL的Root对象可以理解为OGNL的操作对象。当OGNL表达式规定了“干什么”以后，我们还需要指定对谁干。OGNL的Root对象实际上是一个Java对象，是所有OGNL操作的实际载体。这就意味着，如果我们有一个OGNL的表达式，那么我们实际上需要针对Root对象去进行OGNL表达式的计算并返回结果。
- 上下文环境（Context）
有了表达式和Root对象，我们已经可以使用OGNL的基本功能。例如，根据表达式针对OGNL中的Root对象进行“取值”或者“写值”操作。
不过，事实上，在OGNL的内部，所有的操作都会在一个特定的数据环境中运行，这个数据环境就是OGNL的上下文环境（Context）。说得再明白一些，就是这个上下文环境（Context）将规定OGNL的操作在哪里干。

推荐阅读：
[表达式引擎--OGNL](https://juejin.cn/post/7037772093781442590)


## 0x01 危险函数

### Ognl
- `org.apache.commons.ognl.Ognl.getValue()` 通过传入的OGNL表达式，在给定的上下文环境中，从root对象里取值 
- `org.apache.commons.ognl.Ognl.setValue()` 写值 
- `org.apache.commons.ognl.Ognl.parseExpression()` 解析给定的 OGNL 表达式，并返回表达式的树形表示，OGNL 静态方法可以使用该表达式
https://commons.apache.org/dormant/commons-ognl/apidocs/org/apache/commons/ognl/Ognl.html

例子：
```java
public class Main {

    public static void main(String[] args) throws OgnlException {

        User user = new User();
        user.setId(1);
        user.setName("downpour");

        // 创建上下文环境
        Map context = new HashMap();
        context.put("introduction", "My name is ");

        // 测试从Root对象中进行表达式计算并获取结果
        Object name = Ognl.getValue(Ognl.parseExpression("name"), user);
        System.out.println(name.toString());

        // 测试从上下文环境中进行表达式计算并获取结果
        Object contextValue = Ognl.getValue(Ognl.parseExpression("#introduction"), context, user);
        System.out.println(contextValue);
        // 测试同时从将Root对象和上下文环境作为表达式的一部分进行计算
        Object hello = Ognl.getValue(Ognl.parseExpression("#introduction + name"), context, user);
        System.out.println(hello);

        // 对Root对象进行写值操作
        Ognl.setValue("group.name", user, "dev");
        Ognl.setValue("age", user, "18");

        System.out.println(user.getGroup().getName());

    }
}

```

### Struts

在[OGNL表达式注入漏洞总结](https://www.mi1k7ea.com/2020/03/16/OGNL%E8%A1%A8%E8%BE%BE%E5%BC%8F%E6%B3%A8%E5%85%A5%E6%BC%8F%E6%B4%9E%E6%80%BB%E7%BB%93/) 中总结了解析OGNL的函数，代码审计可以重点关注。

## Ref
- https://juejin.cn/post/7037772093781442590
- https://www.mi1k7ea.com/2020/03/16/OGNL%E8%A1%A8%E8%BE%BE%E5%BC%8F%E6%B3%A8%E5%85%A5%E6%BC%8F%E6%B4%9E%E6%80%BB%E7%BB%93/
- https://xz.aliyun.com/t/10482