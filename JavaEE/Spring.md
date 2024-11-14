# 第一章 spring基本概念

### 1.为什么要学spring

**1.简化开发**，降低企业级开发的复杂性

**2.框架整合**，高效整合其他技术，提供开发效率



------



### 2.初识spring

官网https://spring.io/

spring技术是javaEE开发的必备技能，企业开发技术选型高达>90%

spring发展到今天已经形成了一种开发生态圈，spring提供了若干个架构（例：springframeword,springboot,springData,springCloud...），每个架构用于完成特定的功能



------



### 3.springframework架构（分布图） 

![](D:\img\springframework模块分布图.png)



spring Framework是spring生态圈中最基础的项目，是其他项目的根基

**1.核心模块 Core Container核心容器** 

​		**IoC：**控制反转（Inversion of Control）

​		**DI：**依赖倒置（Dependency Injection）



**2.次核心 **

​		**AOP： **面向切面编程

​		**Aspects：** AOP思想实现



**3.持久层模块**

​		**Data Access：**数据访问

​		**Data Intergration：**数据集成（spring不光自己提供了数据方法层技术，同时他还支持集成其他的技术  例：mybatis....）

​		**transcations：**高效率事务控制方案



**4.web模块**

​		**WEB：**web开发（springMvc）



**5.Test模块**

​		**Test:**单元测试与集成测试



------



###  4.IoC容器概念(spring容器)

```java
//dao层
public class BookdaoMysqlImpl implements BookdaoMysql{
	public void save(){
		System.out.println("数据正在保存！！");
	}
}
//业务层
public class BookServiceImpl implements BookService{
	private BookDaoMysql bd  = new BookDaoImplMysql();
	public void save(){
		bd.save();
	}
}
/*
	如果项目上线后，客户想要换一个数据库Oracle这时业务层的代码就要进行修改。
	private BookDaoOrcale bd  = new BookDaoImplOrcale();
*/
```

代码书写现状：耦合度高

解决方案：在程序中**不在使用硬编码**的方式产生对象，对象的创建控制权**由硬编码转换 "外部" 提供对象**，这种思想就叫做 **IoC(Inversion of Control) 控制反转**

​	

Spring对IoC思想进行了实现（怎么实现的？？）

Spring提供了一个容器，成为IoC容器（Spring容器），用来充当IoC思想中的 **" 外部 "**

IoC容器负责对象的创建，初始化等一系列工作，被创建的对象在IoC中统称为**Bean**

**在容器中建立bean与bean之间依赖关系的整个过程，被称为依赖注入** （例：service运行需要dao层方法、，但是IoC思想使dao对象null，这时spring就会把他俩绑定依赖关系，在注入service对象的同时也会把dao对象同时注入）



------





# 第二章 spring入门案例

### 1.导入spring依赖

```xml
<dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-context</artifactId>
            <version>5.3.20</version>
</dependency>
```

### 2.定义spring管理的类（接口）

```java
public interface BookService{
	public void save();
}
public class BookService implements BookService{
	private BookDao bookdao = new BookDaoImpl();
	
	public void save(){
		bookdao.save();
	}
}
```

### 3.创建spring配置文件，配置对应的类作为spring管理的bean

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd">
        
     <bean class="类的全限定类名" name="bean的别名" id="id"></bean>
	   <!--id不能重复-->
        <!--
		别名可以写多个 用, ; 空格进行分隔
 		ref可以直接引用别名
		getBean也可以写别名
	   -->
</beans>
```

### 4.初始化spring容器（IoC容器），通过容器获取bean

```java
public class app{
	public state void main(String[] args){
		//加载配置文件得到上下文对象，也就是spring容器对象
		ApplicationContext ctx = new ClassPathXmlApplicationContext("application.xml");
		//获取资源
		BookService book = (BookService)ctx.getBean("bookService");
		book.save();
	}
}
```



------





# 第三章 spring_IOC(XML)

### 2.DI（依赖注入）入门案例

基于IoC思想管理对象

Service中使用new形式创建的Dao对象是否还保留？（不保留）

Service中的Dao对象如何进入Service中？（依赖注入）

Service与dao间的关系如何描述？（Service依赖DAO）

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd">
    
    <bean id="bookDao" class="com.example.ssm.dao.impl.BookDaoImpl"></bean>

    <bean id ="bookService" class="com.example.ssm.service.impl.BookServiceImpl">
<!--        property 表示配置当前bean的属性-->
<!--        name 属性是bean的属性名-->
<!--        ref 是参照哪一个bean-->
        <property name="bd" ref="bookDao"></property>
    </bean>

</beans>
```



------





### 3.Bean的作用范围(单例还是非单例)

定义bean的作用范围，可选范围如下 

​		singleton:单例（默认）

​		protetype:非单例

```xml
<bean id="bookDao" class="com.example.ssm.dao.impl.BookDaoImpl" scope="prototype"></bean>
```



为什么bean默认是单例？

如果不是单例每次从容器中获取bean都会去新建一个对象，对象多了容器压力大，spring容器主要是管理那些可以复用的对象，这样效率才会高



适合交给容器进行管理的bean:

​			表现层对象

​			业务层对象

​			数据层对象

​			工具对象



不适合交给spring管理的bean

​			封装实体的域对象



------



### **4.**bean实例化（三种方式）

**构造方式（常用）**

```java
public class BookDaoImpl implements BookDao {
    //提供可访问的构造方法
    //注：如果构造方法不存在，将抛出BeanCreationException异常
    public BookDaoImpl() {
        System.out.println("dao层正在初始化！");
    }

    @Override
    public void save() {
        System.out.println("dao层正在保存数据！！");
    }
}
```

```xml
 <bean id="bookDao" class="com.example.ssm.dao.impl.BookDaoImpl"></bean>
```



**静态工厂**

```java
public static BookDao getbd(){
        System.out.println("其他操作");
        return new BookDaoImpl();
}
```

```xml
<bean id="factory" class="com.example.ssm.Factory.OrderByFactory" factory-method="getbd"></bean>
```



**实例工厂**

```java
public BookDao getBookDao(){
        return new BookDaoImpl();
}
```

```xml
<!--配置工厂-->
<bean id="bookFactory" class="com.example.ssm.Factory.BookFactory"></bean> 
<!--配置对象-->
<bean id="book" factory-bean="bookFactory" factory-method="getBookDao"></bean>
```

实例工厂实例化bean是有弊端的 bookFactory实际上是搭配book中的属性factory-bean使用的毫无意义  其次属性factory-method方法名不固定每次需要配置



**FactoryBean(相对实例工厂配置方便些)**

```java
public class BookFactoryBean implements FactoryBean<BookDao> {
    //代替原始实例工厂中创建对象的方法
    @Override
    public BookDao getObject() throws Exception {
        return new BookDaoImpl();
    }
    @Override
    public Class<?> getObjectType() {
        return BookDao.class;
    }
    @Override
    public boolean isSingleton() {
        return true;//true单例 false非单例
    }
}

```

```xml
<!--
    注：bean对象是com.example.FactoryBean.Factory.BookFactoryBean
 	  中的getObject方法的返回值，不是工厂                                                   
-->
<bean id="bookDao" class="com.example.FactoryBean.Factory.BookFactoryBean"> </bean>
```



------



### 5.Bean的生命周期

生命周期：从创建到销毁的完整过程

bean生命周期的控制：bean在**创建后的到销毁前**做的一些事

1、提供生命周期控制方法（xml配置）

```java
public class BookDaoImpl implements BookDao {
    @Override
    public void save() {
        System.out.println("dao层正在保存数据！！");
    }
    
    public void init(){
   	   System.out.println("正在初始化！！");
    }
    
    public void destory(){
   	   System.out.println("正在销毁！！");
    }
}
```

xml配置

```xml
<bean id="bookDao" class="com.example.ssm.dao.impl.BookDaoImpl"
init-method="初始化方法名" 
destory-method="销毁前方法名">
</bean>
<!--注：若想销毁前方法名被调用则需要销毁spring容器-->
```



2、实现 InitializingBean, DisposableBean接口

```java
public class BookDaoImpl implements BookDao,InitializingBean, DisposableBean {
    @Override
    public void find() {
        System.out.println("正在保存数据！！");
    }

    @Override
    public void destroy() throws Exception {
        //销毁前的方法
    }

    @Override
    public void afterPropertiesSet() throws Exception {
        //初始化方法名
    }
}

```



bean生命周期：bean从创建到销毁的整体过程

​	初始化容器	

​			1.创建对象(new)

​			2.执行构造方法

​			3.执行属性注入（set）

​			4.**创建bean初始化方法**

​	使用bean

​			1.执行业务操作

​	关闭/销毁容器

​			1.**执行bean销毁方法**



bean销毁时机

​	容器关闭前触发bean的销毁

​	关闭容器的方式：

​			手动关闭容器ConfigurableApplicationContext接口中的close()					方法

​			在虚拟机退出前关闭容器在退ConfigurableApplicationContext					接口中的registerShutdownHook()方法

​			

------



### 6.依赖注入的方式

set注入

```java
public class BookServiceimpl implements BookService {
    private BookDao bd;//引用类型
    private String name;//简单类型

    public void setBd(BookDao bd) {
        this.bd = bd;
    }

    public void setName(String name) {
        this.name = name;
    }

    @Override
    public void find() {
        System.out.println(bd);
        System.out.println(name);
    }
}
```

```xml
<bean id="bd" class="com.example.FactoryBean.dao.impl.BookDaoImpl"> </bean>
<bean id="bookservice" class="com.example.FactoryBean.service.impl.BookServiceimpl">
    <property name="bd" ref="bookDao"></property>
    <property name="name" value="wang"></property>
</bean>
```



构造注入

```java
 private BookDao bd;
    private String name;

    public BookServiceimpl(BookDao bd, String name) {
        this.bd = bd;
        this.name = name;
    }

    @Override
    public void find() {
        System.out.println(bd);
        System.out.println(name);
    }
```

```xml
<bean id="bd" class="com.example.FactoryBean.dao.impl.BookDaoImpl"> </bean>
<bean id="bookservice" class="com.example.FactoryBean.service.impl.BookServiceimpl">
     <constructor-arg name="bd" ref="bookDao"></constructor-arg>
     <constructor-arg name="name" value="wang"></constructor-arg>
    	<!--
	<constructor-arg index="1" value="wang"></constructor-arg>
	<constructor-arg index="1" type="wang"></constructor-arg>
	<constructor-arg type="java.lang.String" value="wang"></constructor-arg>	
	-->
</bean>
```



自动装配

```java
public class BookServiceimpl implements BookService {
    private BookDao bd;
    
    //使用自动装配，必须提供set方法
    public void setBd(BookDao bd) {
        this.bd = bd;
    }

    @Override
    public void find() {
        System.out.println(bd);
    }
}

```

```xml
<bean id="bd" class="com.example.FactoryBean.dao.impl.BookDaoImpl"> </bean>
<bean id="bookservice" class="com.example.FactoryBean.service.impl.BookServiceimpl" autowire="byType"></bean>
```

自动装配用于引用类型依赖注入，不能对简单类型进行操作

使用按类型装配时（byType）必须保障容器中相同类型的bean唯一，推荐使用

使用按名称装配时（byName）必须保障容器中具有指定名称的bean，耦合度高不推荐

自动装配优先级低于setter注入和构造注入，同时出现会出现装配失效



------



### 7.集合,数组,map....注入

```xml
<bean id="user" class="com.example.FactoryBean.entity.User">
    <property name="array">
        <array>
            <value>array1</value>
            <value>array2</value>
            <value>array3</value>
            <value>array4</value>
             <!--引用类型-->
            <ref bean="spring容器对象"></ref>
        </array>
    </property>
    <property name="list">
        <list>
            <value>list1</value>
            <value>list2</value>
            <value>list3</value>
            <value>list4</value>
        </list>
    </property>
    <property name="map">
        <map>
            <entry key="map1" value="map1"></entry>
            <entry key="map2" value="map2"></entry>
            <entry key="map3" value="map3"></entry>
            <entry key="map4" value="map4"></entry>
        </map>
    </property>
    <property name="props">
        <props>
            <prop key="prop1">prop1</prop>
            <prop key="prop2">prop2</prop>
            <prop key="prop3">prop3</prop>
        </props>
    </property>
</bean>
```



------



### 8.连接池配置

```xml
<bean id="DataSource" class="com.alibaba.druid.pool.DruidDataSource">
    <property name="driverClassName" value="com.mysql.jdbc.Driver"></property>
    <property name="username" value="root"></property>
    <property name="password" value="ok"></property>
    <property name="url" value="jdbc:mysql://localhost:3306/"></property>
</bean>
```



------



### 9.spring加载properties文件

**自行百度**



------



### 10.spring容器相关

BeanFactory是IoC容器的顶级接口，初始化BeanFactory对象时，加载bean延迟加载(bean使用的时 实例化bean)

ApplicationContext接口是spring容器的核心接口，初始化是bean立即加载(容器初始化时 实例化bean)      注：bean属性加上lazy-init="true"

ApplicationContext接口提供基础bean操作相关的方法，通过其他接口扩展其功能

ApplicationContext接口常用的初始化类

ClassPathXmlApplicationContext		项目路径

FileSystemXmlApplicationContext        绝对路径



------



### 11.常用XML属性

| 属性           | 说明                                   |
| -------------- | -------------------------------------- |
| id             | beanid                                 |
| name           | bean别名                               |
| class          | bean类型，静态工厂类，FactoryBean类    |
| scope          | 是否是单例                             |
| init-method    | 生命周期的初始化方法                   |
| destroy-method | 生命周期的销毁方法                     |
| autowire       | 自动装配类型                           |
| factory-method | bean工厂方法，应用于静态工厂或实例工厂 |
| factory-bean   | 实例工厂bean                           |
| lazy-init      | 控制bean延迟加载                       |





# 第四章 spring_IOC(注解)

### 1.注解开发定义bean

@Component三个衍生注解

@Controller:用于控制层定义

@Service:用于bean层定义

@Repository:用户数据层定义

**半注解**

1.使用@Component("id")定义bean

```java
@Component("book")
public class book {
    int id = 1;
    String name = "wang";

    @Override
    public String toString() {
        return "book{" +
                "id=" + id +
                ", name='" + name + '\'' +
                '}';
    }
}

```

2.核心配置文件中通过组件扫描加载bean

```xml
<context:component-scan base-package="com.example.FactoryBean.entity"></context:component-scan>
```



**全注解**

java3.0开启了纯注解开发模式，使用java类替代配置文件，开启了spring快速开发

@Configuration注解用于设定当前类为配置类

@ComponentScan注解用于设定扫描路径

1.编写配置类

```java
@ComponentScan("com.example.Annotation.bean")
@Configuration
public class springConfig {
}
```

2.测试类有所改变

```java
ApplicationContext ctx = new AnnotationConfigApplicationContext(springConfig.class);
user user = ctx.getBean("user", user.class);
System.out.println(user);
```



------



### 2.第三方bean管理

@Bean纳入spring容器中

第三方bean注入

​			引用类型：方法形参

​			简单类型：成员变量



------



### 3.常用注解

| 注解                  | 说明                                                         |
| --------------------- | ------------------------------------------------------------ |
| @Component            | 定义bean                                                     |
| @Controller           | 用于控制层定义                                               |
| @Service              | 用于bean层定义                                               |
| @Repository           | 用户数据层定义                                               |
| @Configuration        | 用于设定当前类为配置类                                       |
| @ComponentScan        | 用于设定扫描路径                                             |
| @Scope                | 是否是单例                                                   |
| @PostConstrust        | 初始化方法                                                   |
| @PreDestroy           | 销毁前方法                                                   |
| @Autowired            | 自动装配(默认按类型)                                         |
| @Qualifier            | 如何有多个实现的话，可以指定bean                             |
| @Value                | 注入简单类型                                                 |
| @PropertySource       | 加载属性资源                                                 |
| @Bean                 | 把返回值纳入spring容器中                                     |
| @Import（类名.class） | 手动加入配置类到核心文件中                                   |
| @Resource             | 属于 Java 的标准注解，按照名称进行自动装配，只有一个`name` 属性 |
|                       |                                                              |
|                       |                                                              |
|                       |                                                              |



------





# 第五章 spring_AOP

### 1.简介

Spring AOP（Aspect-Oriented Programming，面向切面编程）是Spring框架的一个关键组成部分，它允许你通过切面和通知来定义跨多个切点（方法或者类）的行为



### 2.XML配置方式

**定义切面 (Aspect)**

在Spring的XML配置文件中，可以使用`<aop:config>`元素来配置切面。

```xml
<aop:config>
    <aop:aspect id="myAspect" ref="aBean">
        <!-- 配置通知和切点 -->
    </aop:aspect>
</aop:config>

<bean id="aBean" class="com.example.MyAspect"/>
```



**定义切点 (Pointcut)**

切点表达式定义了切面将应用于哪些方法。

```xml
<aop:pointcut id="myPointcut"
              expression="execution(* com.example.service.*.*(..))
```





**定义通知 (Advice)**

通知定义了切面的具体行为，在何时执行等。

```xml
<aop:aspect id="myAspect" ref="aBean">
    <aop:before pointcut-ref="myPointcut" method="beforeMethod"/>
    <aop:after-returning pointcut-ref="myPointcut" method="afterReturningMethod"/>
    <!-- 其他通知 -->
</aop:aspect>
```

这里`beforeMethod`和`afterReturningMethod`是在`aBean`中定义的方法，分别在方法执行前和执行后运行。



### 3.注解方式

注解方式是Spring AOP的现代配置方法，不需要复杂的XML配置。



**启用AOP注解支持**

在配置类上使用`@EnableAspectJAutoProxy`注解来启用AOP注解支持。

```java
@Configuration
@EnableAspectJAutoProxy
public class AppConfig {
    // 其他Bean定义
}
```



**定义切面**

使用`@Aspect`注解在一个类上声明它是一个切面。

```java
@Aspect
@Component
public class MyAspect {

    // 通知方法定义

}
```



**定义切点**

使用`@Pointcut`注解定义切点表达式。

```java
@Pointcut("execution(* com.example.service.*.*(..))")
public void myPointcut() {
    // 切点方法的内容通常为空
}
```



**定义通知**

使用通知注解定义切面的具体行为。

```java
@Before("myPointcut()")
public void beforeMethod(JoinPoint joinPoint) {
    // 方法执行前的逻辑
}

@AfterReturning(pointcut = "myPointcut()", returning = "result")
public void afterReturningMethod(JoinPoint joinPoint, Object result) {
    // 方法执行后的逻辑
}
```





### 4.五种类型的通知

#### Before Advice (`@Before`)

- 在目标方法执行之前执行。
- 不能阻止方法执行流程（除非它抛出一个异常）。

```java
   @Before("somePointcut()")
   public void doSomethingBefore(JoinPoint joinPoint) {
       // ...
   }
```



#### After Returning Advice (`@AfterReturning`)

- 在目标方法成功执行之后执行。
- 可以访问方法的返回值。

```java
   @AfterReturning(pointcut = "somePointcut()", returning = "result")
   public void doSomethingAfterReturning(JoinPoint joinPoint, Object result) {
       // ...
   }
```



#### After Throwing Advice (`@AfterThrowing`)

- 在目标方法抛出异常后执行。
- 可以访问抛出的异常。

```java
   @AfterThrowing(pointcut = "somePointcut()", throwing = "exception")
   public void doSomethingAfterThrowing(JoinPoint joinPoint, Throwable exception) {
       // ...
   }
```

#### After (finally) Advice (`@After`)

- 在目标方法执行之后执行，无论其结果如何。
- 类似于`finally`块，它总是执行。

```java
   @After("somePointcut()")
   public void doSomethingAfter(JoinPoint joinPoint) {
       // ...
   }
```



#### Around Advice (`@Around`)

- 围绕目标方法执行，可以在方法执行前后执行自定义逻辑。
- 可以决定是否继续执行目标方法，或者替换其返回值，或者抛出异常。

```java
@Around("somePointcut()")
public Object doAround(ProceedingJoinPoint joinPoint) throws Throwable {
    // 方法执行之前
    try {
        Object result = joinPoint.proceed(); // 继续执行目标方法
        // 方法成功执行之后
        return result;
    } catch (Throwable throwable) {
        // 方法抛出异常之后
        throw throwable;
    } finally {
        // 方法执行之后（相当于finally）
    }
}
```





# 第六章 spring整合mybatis

### 1.配置数据源

```java
public class JdbcConfig {
    @Value("${jdbc.driver}")
    private String driver;
    @Value("${jdbc.url}")
    private String url;
    @Value("${jdbc.password}")
    private String password;
    @Value("${jdbc.user}")
    private String user;

    @Bean
    public DataSource dataSource(){
        DruidDataSource dataSource = new DruidDataSource();
        dataSource.setDriverClassName(driver);
        dataSource.setPassword(password);
        dataSource.setUsername(user);
        dataSource.setUrl(url);
        return dataSource;
    }
}
```

### 2.配置spring

```java
//配置类 注解
@Configuration
//spring扫描bean
@ComponentScan("com.example.mybatis")
//加载mybatis核心配置
@Import({JdbcConfig.class,MybatisConfig.class})
//引用属性文件
@PropertySource("jdbc.properties")
public class SpringConfig {
}
```

### 3.属性配置文件

```properties
jdbc.driver=com.mysql.jdbc.Driver
jdbc.url=jdbc:mysql://localhost:3306/ssm_db
jdbc.user=root
jdbc.password=ok
```

### 4.mybatis配置文件

```java
public class MybatisConfig {
    /**
     * 会话工厂bean
     * @param dataSource
     * @return
     */
    @Bean
    public SqlSessionFactoryBean sqlSessionFactoryBean(DataSource dataSource){
        SqlSessionFactoryBean s = new SqlSessionFactoryBean();
        s.setTypeAliasesPackage("com.example.mybatis.bean");
        //设置数据源
        s.setDataSource(dat aSource);
        return s;
    }
    /**
     * 用于扫描mapper
     * @return
     */
    @Bean
    public MapperScannerConfigurer mapperScannerConfigurer(){
        MapperScannerConfigurer mc = new MapperScannerConfigurer();
        mc.setBasePackage("com.example.mybatis.mapper");
        return mc;
    }
}
```

注：@ComponentScan注解只会扫描他和他衍生出来的三个，并不能扫描到@bean注解，@bean只能以配置文件的形式纳入spring容器中。



------



# 第七章 spring整合junit

添加依赖













# 第八章 在Spring中使用Jackson

不建议将Jackson替换为Gson或fastjson。jackson的常用注解的使用方法。

> 什么叫序列化与反序列化？说白了就是把对象转成可传输、可存储的格式（json、xml、二进制、甚至自定义格式）叫做序列化。反序列化顾名思义。

### 1.常用注解

这些注解通常用于标注java实体类或实体类的属性。

- @JsonPropertyOrder(value={"pname1","pname2"}) 改变子属性在JSON序列化中的默认定义的顺序。如：param1在先，param2在后。
- @JsonIgnore 排除某个属性不做序列化与反序列化
- @JsonProperty(anotherName) 为某个属性换一个名称，体现在JSON数据里面
- @JsonInclude(JsonInclude.Include.NON_NULL) 排除为空的元素不做序列化反序列化
- @JsonFormat(pattern = "yyyy-MM-dd HH:mm:ss", timezone = "GMT+8") 指定日期类型的属性格式

```java
@JsonPropertyOrder(value={"content","title"})  
public class Article {

    @JsonIgnore
    private Long id;

    @JsonProperty("auther")
    private String author;
    private String title;
    private String content;

    @JsonInclude(JsonInclude.Include.NON_NULL)
    @JsonFormat(pattern = "yyyy-MM-dd HH:mm:ss", timezone = "GMT+8")
    private Date createTime;
    private List<Reader> reader;

}
```

上文代码中对应的JSON数据格式可以为：

```json
{
    auther :"",
    content:"",
    title:"",
    createTime:"2019-10-20 12:12:12",
    reader:[{"name":"zimug","age":18},{"name":"kobe","age":37}]
}
```

- 因为定义了JsonPropertyOrder，content在先，title在后
- 因为定义了JsonIgnore，id属性被忽略
- 因为定义了JsonProperty，author属性变为auther
- 因为定义了JsonInclude和JsonFormat，createTime不要为空，并且格式为 "yyyy-MM-dd HH:mm:ss"

通常会对日期类型转换，进行全局配置，而不是在每一个java bean里面配置

```yaml
spring: 
    jackson:
        date-format: yyyy-MM-dd HH:mm:ss
        time-zone: GMT+8
```

### 2.手动数据转换

除了在spring框架内实现自动的前后端JSON数据与java对象的转换，我们还可以使用jackson自己写代码进行转换。

```java
//jackson的ObjectMapper 转换对象
ObjectMapper mapper = new ObjectMapper();
//将某个java对象转换为JSON字符串
String jsonStr = mapper.writeValueAsString(javaObj);
//将jsonStr转换为Ademo类的对象
Ademo ademo = mapper.readValue(jsonStr, Ademo.class);
```

当JSON字符串代表的对象的字段多于类定义的字段时，使用readValue会抛出UnrecognizedPropertyException异常，在类的定义处加上@JsonIgnoreProperties(ignoreUnknown = true)可以解决这个问题。