# XML外部实体(XXE)注入

## 0x00 Introdution
在Web安全手册有介绍
[EXP手册-XXE Injecton(XML External Entity Injection)](https://github.com/ReAbout/web-sec/blob/master/exp/EXP-XXE.md)

## 0x01 危险函数

### DocumentBuilder
`javax.xml.parsers.DocumentBuilder.parse()`   （原生dom解析xml）

```java
DocumentBuilderFactory doc=DocumentBuilderFactory.newInstance();
DocumentBuilder db=doc.newDocumentBuilder();
InputStream is= new  FileInputStream("test.xml");    
Document doc=dombuilder.parse(is);   #注入点
Element rootElement = document.getDocumentElement();

```
### XMLStreamReader
`javax.xml.stream.XMLStreamReader`    （StAX解析器，可读可写）

```java
XMLInputFactory factory = XMLInputFactory.newFactory();
InputStream stream = XmlInputFactory.class.getClassLoader().getResourceAsStream("webService/xml/users.xml");
XMLStreamReader reader = factory.createXMLStreamReader(stream);   #注入点
while (reader.hasNext()) {...}
```
### SAXParser XMLReader

`javax.xml.parsers.SAXParser`  /  `org.xml.sax.XMLReader`  （原生SAX解析xml）

```java
SAXParserFactory factory = SAXParserFactory.newInstance();
SAXParser parser = factory.newSAXParser();
XMLReader reader = parser.getXMLReader();   //reader.setContentHandler(new MyContentHandler());
reader.parse(xmlPath);    #注入点
```

### SAXBuilder

`org.jdom.input.SAXBuilder`  /  `org.jdom2.input.SAXBuilder`   （jdom解析xml）

```java
SAXBuilder sax = new SAXBuilder();
Document doc = sax.build("src/config.xml");    #注入点
```
### SAXReader

`org.dom4j.io.SAXReader`   （dom4j解析xml）

```java
InputStream fis=new FileInputStream("F:\\eclipsewk\\SDK201702\\Test-Pack\\package\\work\\before\\AndrdManit.xml");
Document document = new SAXReader().read(fis);   #注入点
```

### SchemaFactory

`javax.xml.validation.SchemaFactory``    （校验xml）

```java
File xsdfile=new File("xml/orders.xsd");
File xmlfile=new File("xml/orders.xml");
Handler errorHandler=new Handler();
SchemaFactory schemafactory=SchemaFactory.newInstance("http://www.w3.org/2001/XMLSchema"); //
Schema schema=schemafactory.newSchema(xsdfile);
Validator vaildator=schema.newValidator();
vaildator.setErrorHandler((ErrorHandler) errorHandler);
vaildator.validate(new StreamSource(xmlfile));   #注入点

```
### Unmarshaller

`javax.xml.bind.Unmarshaller`

```java
JAXBContext jc = JAXBContext.newInstance(clazz);
Unmarshaller u = jc.createUnmarshaller();
u.unmarshal(new File(xmlstr));    #注入点
```
### XPathExpression
j`avax.xml.xpath.XPathExpression`   （XPath查询）

```java
//值得注意的是：javax.xml.xpath.XPathExpression类似于Unmarshaller，它无法自行安全地配置，因此必须首先通过另一个安全的XML解析器解析不受信任的数据
DocumentBuilderFactory df = DocumentBuilderFactory.newInstance();    
DocumentBuilder builder = df.newDocumentBuilder();
String result = new XPathExpression().evaluate(builder.parse(new ByteArrayInputStream(xml.getBytes())));
 
```

## Ref
- https://blog.csdn.net/qq_48985780/article/details/122166048