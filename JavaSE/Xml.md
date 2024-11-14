# 第一章 xml基本概念

### 1.xml基本概念

xml是可扩展标记语言，用来进行数据存储，在后期框架学习中进行配置文件的配置处理【注：和html超文本标记语言进行区分，他主要进行数据显示】



### 2.xml的构成

有如下6个部分构成

**1.声明**

```xml
<!--
	放在首行
	version:版本
	encoding:编码
-->
<?xml version="1.0" encoding="UTF-8" ?>
```



**2.元素**

```xml
<!--根元素【有且只有一个根元素】-->
<books>
    <book>
        <!--注释不能嵌套 放在标签中-->
        <bid>24</bid>
        <bname>张三</bname>
        <desc><![CDATA[这是多个a标签<a>cw</a>]]></desc>
    </book>
     
    <book>
        <bid>2434</bid>
        <bname>李四</bname>
        <!--空元素-->
        <desc/>
    </book>
</books>
```



**3.属性**

```xml
<books>
    <book 属性名='属性值'>
    
    </book>
</books>
```



**4.预定义实体【5个】**

```
&gt;   <
&lt;	  >
&quot; "
&amp;  &
&apos; '
```



**5.CDATA节**

```xml
<books>
    <book>
        <bid>24</bid>
        <bname>张三</bname>
         	    <!--语法说明-->
        <desc><![CDATA[这是多个a标签<a>cw</a>]]></desc>
    </book>
</books>
```



**6.注释**

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<books>
    <!--xml的注释-->
    <book>
        <!--注释不能嵌套 出现-- 放在标签中-->
        <bid>24</bid>
        <bname>张三</bname>
        <desc><![CDATA[这是多个a标签<a>cw</a>]]></desc>
    </book>
</books>
```



**7.整体xml规范**

在上面6个点基础上，还需注意：

对大小写敏感

正确闭合嵌套





# 第二章 DTD约束

由于xml的可扩展性，内容任意，需要对其进行约束

DTD就是其中的一种方式，他的语法等比较单一简单【schema也可以用来进行约束】

DTD：文档类型定义，分为内部，外部，内外结合三种方式

### 1.DTD语法【元素】

```dtd
<!DOCTYPE books[
        
        <!ELEMENT books (book+)>
        <!-- 
        	PCDATA:普通文本
          EMPTY:空元素
          ANY:元素内容任意
        -->
        <!ELEMENT bid (#PCDATA)>
        <!ELEMENT bname (#PCDATA)>
        <!ELEMENT desc (#PCDATA)>
        <!ELEMENT sex (#EMPTY)>
        <!ELEMENT yxx (#PCDATA)>
        <!ELEMENT bxx (#PCDATA)>
        <!ELEMENT binfo ANY>
          
        <!--
          节点下有的元素【标签，有先后顺序】
          (bxx|yxx):代表2选一元素，不能同时出现
          
          根节点下有n个子节点
     	+:代表1-n
          ?:代表有1个或没有
          *:代表0-n
        -->
        <!ELEMENT book (bid,bname,desc?,(bxx|yxx),binfo)>
]>
```



### 2.DTD语法【属性】

 <!ATTLIST 元素名

​		属性名  属性类型  属性特点	

 >

```xml-dtd
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE persons[
    <!ELEMENT person EMPTY>
    <!ELEMENT persons (person+)>
    <!ATTLIST person
        <!--
        	类型1:ID【不可重复，不能是纯数字】
          特点1:#REQUIRED【该属性必须出现】
        -->
        id ID #REQUIRED
        <!--
        	类型2:CDATA【普通文本】
        -->       
        name CDATA #REQUIRED
        <!--
        	类型3:IDREFS【引入其他ID类型的值,结尾代表可以引入多个值】
        	特点2:#IMPLIED【该属性可有可无】
        -->       
        pid IDREFS #IMPLIED
        <!--
        	类型4:REQUIRED【枚举类型，一组常量值，多选一】               
        --> 
        sex (男|女) #REQUIRED
        <!--
        	特点3:#FIXED【固定值，不可改变】
        --> 
        country CDATA #FIXED "中国"
        <!--
        	特点4:默认值【可以改变】
        --> 
        city CDATA "徐州"
        myinfo CDATA #IMPLIED
    >
    <!ENTITY info "今天有点冷">
]>

<persons>
    <person id="S101" name="wang" sex="男" country="中国" myinfo="&info;"></person>
    <person id="S102" pid="S101" name="zhang" sex="女" city=""></person>
</persons>
```



### 3.DTD定义实体

xml中有默认的实体，我们也可以自定义

语法：

```dtd
<!ENTITY info "今天有点冷">
```

我们在xml对应文档中定义了一个名为info的实体

通过&info；方式调用





# 第三章 dom4j的使用

xml解析技术有很多，原生的，第三方的【这里是dom4j】





### 1.引入依赖

```xml
<dependency>
          <groupId>org.dom4j</groupId>
          <artifactId>dom4j</artifactId>
          <version>2.1.3</version>
</dependency>
```





### 2.从xml中获取元素

```java
static void show() throws DocumentException {
        //创建reader对象
        SAXReader Reader = new SAXReader();
        //获取文档对象
        Document document = Reader.read("E:\\mybatis\\xml\\stux.xml");
        //获取根节点books
        Element element = document.getRootElement();

        Iterator<Element> elementIterator = element.elementIterator();
        while (elementIterator.hasNext()) {
            Element next = elementIterator.next();
            System.out.println(next.attributeValue("id")+"   "+next.elementText("name")+"   "+next.elementText("age"));
        }
    }
```





### 3.向xml中插入元素【属性】

```java
static void add() throws DocumentException {
        //创建reader对象
        SAXReader Reader = new SAXReader();
        //获取文档对象
        Document document = Reader.read("E:\\mybatis\\xml\\stux.xml");
        //获取根节点books
        Element element = document.getRootElement();

        //新建一个元素
        Element student = element.addElement("student");
        student.addAttribute("id","s1003");
        student.addElement("name").addText("王五");
        student.addElement("age").addText("15");
        save(document,"E:\\mybatis\\xml\\stux.xml");
    }
```





### 4.向xml中修改元素【属性】

```java
   static void update() throws DocumentException {
        //创建reader对象
        SAXReader Reader = new SAXReader();
        //获取文档对象
        Document document = Reader.read("E:\\mybatis\\xml\\stux.xml");
        //获取根节点books
        Element element = document.getRootElement();


        Iterator<Element> elementIterator = element.elementIterator();
        while (elementIterator.hasNext()) {
            Element next = elementIterator.next();
            if (next.attributeValue("id").equals("s1003")){
                next.element("name").setText("wang");
            }
        }

        save(document,"E:\\mybatis\\xml\\stux.xml");
    }
```





### 5.删除xml元素

```java
 static void remove() throws DocumentException {
        //创建reader对象
        SAXReader Reader = new SAXReader();
        //获取文档对象
        Document document = Reader.read("E:\\mybatis\\xml\\stux.xml");
        //获取根节点books
        Element element = document.getRootElement();


        Iterator<Element> elementIterator = element.elementIterator();
        while (elementIterator.hasNext()) {
            Element next = elementIterator.next();
            if (next.attributeValue("id").equals("s1003")){
                element.remove(next);
            }
        }

        save(document,"E:\\mybatis\\xml\\stux.xml");
    }
```





### 6.保存

```java
 static void save(Document document, String filename) {
        try {
            OutputFormat format = OutputFormat.createPrettyPrint();
            XMLWriter writer = null;
            writer = new XMLWriter(new FileOutputStream(filename), format);
            writer.write(document);
            System.out.println("更新成功！");
            writer.close();
        } catch (UnsupportedEncodingException e) {
            e.printStackTrace();
        } catch (FileNotFoundException e) {
            e.printStackTrace();
        } catch (IOException e) {
            e.printStackTrace();
        }
    }
```

