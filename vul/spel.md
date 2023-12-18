# SPEL

## 0x00 Introduction

Spring Expression Language(SPEL)

>SPEL（Spring Expression Language），即Spring表达式语言，是比JSP的EL更强大的一种表达式语言。从Spring 3开始引入了Spring表达式语言，它能够以一种强大而简洁的方式将值装配到Bean属性和构造器参数中，在这个过程中所使用的表达式会在运行时计算得到值。使用SPEL你可以实现超乎想象的装配效果，这是其他装配技术很难做到的。

推荐阅读：[Spring 表达式语言 (SpEL)](https://itmyhome.com/spring/expressions.html)

## 0x01 危险函数

### 注解

```java
//@Value能修饰成员变量和方法形参
//#{}内就是SPEL表达式的语法
//Spring会根据SPEL表达式语法，为变量arg赋值
@Value("#{表达式}")
public String arg;
```

等效的属性setter方法
```java
public static class PropertyValueTestBean

    private String defaultLocale;

    @Value("#{ systemProperties['user.region'] }")
    public void setDefaultLocale(String defaultLocale) {
        this.defaultLocale = defaultLocale;
    }

    public String getDefaultLocale() {
        return this.defaultLocale;
    }

}
```

### 函数

`org.springframework.expression.ExpressionParser.parseExpression()`

例子——输出Hello World
```java
ExpressionParser parser = new SpelExpressionParser();
Expression exp = parser.parseExpression("'Hello World'");
String message = (String) exp.getValue();
```
例子——针对一个特定的对象实例（称为root object）提供被解析的表达式字符串.
```java
// Create and set a calendar
GregorianCalendar c = new GregorianCalendar();
c.set(1856, 7, 9);
// The constructor arguments are name, birthday, and nationality.
Inventor tesla = new Inventor("Nikola Tesla", c.getTime(), "Serbian");
ExpressionParser parser = new SpelExpressionParser();
Expression exp = parser.parseExpression("name");
EvaluationContext context = new StandardEvaluationContext(tesla);
String name = (String) exp.getValue(context);
```
### XML
SpEL表达式可以与XML或基于注释的配置元数据使用 定义BeanDefinitions。在这两种情况下，以定义表达式语法的 形式`#{<表达式字符串>}`。
```xml
<bean id="taxCalculator" class="org.spring.samples.TaxCalculator">
    <property name="defaultLocale" value="#{ systemProperties['user.region'] }"/>

    <!-- other properties -->
</bean>
```

## Ref
- https://itmyhome.com/spring/expressions.html
- https://paper.seebug.org/1694/