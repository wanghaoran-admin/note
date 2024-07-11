# 第一章 springboot核心概念

### 1.springboot、springmvc、spring对比

**Spring 框架**

Spring框架最核心的特性就是依赖注入DI（Dependency Injecttion）和控制反转IOC（Inversion Of Control）。如果你能够合理的使用DI和IOC，可以开发出松耦合、扩展性好的的应用程序。



**Spring MVC**

Spring MVC提供了一种友好的方式来开发Web应用程序。 通过使用诸如Dispatcher Servlet，ModelAndView和View Resolver，可以轻松开发Web应用程序。



**spring Boot**

Spring 和 Spring MVC最大的弊端就是在于存在大量的配置，并且这些配置在不同的项目中具有很高的相似性。从而导致重复配置，繁琐而且杂乱！

Spring Boot期望通过结合自动配置和starters来解决了这个问题。 另外，Spring Boot还提供了一些功能，可以更快地构建可用于生产环境的应用程序。



### 2.什么是Spring Boot Starter

Spring Boot Starter是一组被依赖第三方类库的集合。

如果你要开发一个web应用程序，就通过包管理工具(如maven)引入spring-boot-starter-web就可以了，而不用分别引入下面这么多依赖类库，spring-boot-starter-web一次性帮你引入下面的这些常用类库。

- Spring — spring 核心, beans, context上下文, AOP面向切面
- Web MVC — Spring MVC0.
- Jackson — JSON数据的序列化与反序列化
- Validation — Hibernate参数校验及校验API
- 嵌入式 Servlet Container — Tomcat
- 日志框架Logging — logback, slf4j



### 3.什么是Spring Boot Starter Parent

所有的Spring Boot项目默认使用spring-boot-starter-parent作为应用程序的父项目。

```xml
<parent>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-parent</artifactId>
        <version>3.1.7</version>
        <relativePath/> <!-- lookup parent from repository -->
</parent>
```

继承父项目的好处在于： 统一java版本配置，引入的第三方类库不要加版本号，父项目替你管理版本，而且是经过兼容性测试的。比你自己随便引入一个版本兼容性更好。



### 4.嵌入式web容器

Spring boot打成jar包，默认包含嵌入式的web容器：tomcat。你可以简单的使用如下命令启动一个web服务：

```shell
java -jar springboot-demo.jar
```

这更有利于微服务的部署及微服务的构建、启动、扩容。Spring Boot还支持Jetty和Undertow作为web容器。



### 5.spring boot2.x新特性

- 最低 JDK 8，支持 JDK 9，不再支持 Java 6 和 7。Spring Boot 2.0 要求 Java 8 作为最低版本，许多现有的 API 已更新，以利用 Java 8 的特性。
	例如，接口上的默认方法，函数回调以及新的 API，如 javax.time。

	

- 如果你正在使用 Java 7 或更早版本，则在开发 Spring Boot 2.0 应用程序之前，需要升级你的 JDK。



### 6.目录结构简介

项目结构目录整体上符合maven规范要求：

| 目录位置                                  | 功能                                                         |
| ----------------------------------------- | ------------------------------------------------------------ |
| src/main/java                             | 项目java文件存放位置，初始化包含主程序入口 XxxApplication，可以通过直接运行该类来 启动 Spring Boot应用 |
| src/main/resources                        | 存放静态资源，图片、CSS、JavaScript、web页面模板文件等       |
| src/test                                  | 单元测试代码目录                                             |
| .gitignore                                | git版本管理排除文件                                          |
| target文件夹                              | 项目代码构建打包结果文件存放位置，不需要人为维护             |
| pom.xml                                   | maven项目配置文件                                            |
| application.properties（application.yml） | 用于存放程序的各种依赖模块的配置信息，比如服务端口，数据库连接配置等 |

- src/main/resources/static主要用来存放css、图片、js等开发用静态文件
- src/main/resources/public用来存放可以直接用于访问的html文件
- src/main/resources/templates用来存放web开发模板文件





### 7.彩蛋

到了 Spring Boot 2.0 现在可以支持 Gif 文件的打印，Spring Boot 2.0 在项目启动的时候，会将 Gif 图片的每一个画面，按照顺序打印在日志中，所有的画面打印完毕后，才会启动 Spring Boot 项目。









# 第二章 详解yaml语法

### 1.简介

 以前的配置文件；大多都使用的是 **xxxx.xml**文件；

 YAML：**以数据为中心**，比json、xml等更适合做配置文件；



### 2.yaml语法

k: (空格)v : 表示一堆键值对(空格必须有)

以`空格`的缩进来控制层级关系；只要是左对齐的一列数据，都是同一个层级的

次等级的前面是空格，不能使用制表符(tab)

冒号之后如果有值，那么冒号和值之间至少有一个空格，不能紧贴着

```yaml
server:
	port: 8080
	path: /hello
```



### 3.规则

**规则1：字符串的单引号与双引号**

- 双引号；不会转义字符串里面的特殊字符；特殊字符会作为本身想表示的意思，如：
	​ name: “zhangsan \n lisi”：输出；zhangsan 换行 lisi

- 单引号；会转义特殊字符，特殊字符最终只是一个普通的字符串数据，如：
	​ name: ‘zhangsan \n lisi’：输出；zhangsan \n lisi

	

**规则2：支持松散的语法**

```yaml
family-name = familyName = family_name
```







# 第三章 配置文件对象注入

### 1.在yml中配置对象

**实体类**

```java
package com.example.demo1.entity;

import org.springframework.boot.context.properties.ConfigurationProperties;
import org.springframework.stereotype.Component;

@Component
//配置属性
@ConfigurationProperties(prefix = "pet")
public class Pet {
    private String name;
    private Integer age;

    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }

    public Integer getAge() {
        return age;
    }

    public void setAge(Integer age) {
        this.age = age;
    }

    @Override
    public String toString() {
        return "Pet{" +
                "name='" + name + '\'' +
                ", age=" + age +
                '}';
    }
}
```



**配置文件**

```yaml
pet:
  name: 张三
  age: 34
```

会进行自动匹配







**测试**

```java
package com.example.demo1;

import com.example.demo1.entity.Pet;
import org.junit.jupiter.api.Test;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.boot.test.context.SpringBootTest;

@SpringBootTest
class Demo1ApplicationTests {
    @Autowired
    private Pet person;

    @Test
    void contextLoads() {
        System.out.println(person);
    }
}
```



### 2.在实体中引用yml中的属性

**实体类**

```java
package com.example.demo1.entity;


import lombok.Data;
import org.springframework.beans.factory.annotation.Value;
import org.springframework.stereotype.Component;

@Component
@Data
public class Pet1 {
    //使用${}的方式引用
    //${}里的值是必须在yml中存在的
    //不存在会报异常
    //org.springframework.beans.factory.BeanCreationException    
    @Value("${pet.name}")
    private String name;
}
```



```yaml
pet:
  name: 张三
  age: 34
```







# 第四章 对注入的属性进行校验

### **1.添加依赖**

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-validation</artifactId>
</dependency>
```



### **2.在类上添加@Validated**

```java
@Validated
public class UserController 
```



### **3.在属性上添加以下的注解**

| 限制                      | 说明                                                         |
| :------------------------ | :----------------------------------------------------------- |
| @Null                     | 限制只能为null                                               |
| @NotNull                  | 限制必须不为null                                             |
| @AssertFalse              | 限制必须为false                                              |
| @AssertTrue               | 限制必须为true                                               |
| @DecimalMax(value)        | 限制必须为一个不大于指定值的数字                             |
| @DecimalMin(value)        | 限制必须为一个不小于指定值的数字                             |
| @Digits(integer,fraction) | 限制必须为一个小数，且整数部分的位数不能超过integer，小数部分的位数不能超过fraction |
| @Future                   | 限制必须是一个将来的日期                                     |
| @Max(value)               | 限制必须为一个不大于指定值的数字                             |
| @Min(value)               | 限制必须为一个不小于指定值的数字                             |
| @Past                     | 限制必须是一个过去的日期                                     |
| @Pattern(value)           | 限制必须符合指定的正则表达式                                 |
| @Size(max,min)            | 限制字符长度必须在min到max之间                               |
| @Past                     | 验证注解的元素值（日期类型）比当前时间早                     |
| @NotEmpty                 | 验证注解的元素值不为null且不为空（字符串长度不为0、集合大小不为0） |
| @NotBlank                 | 验证注解的元素值不为空（不为null、去除首位空格后长度为0），不同于@NotEmpty，@NotBlank只应用于字符串且在比较时会去除字符串的空格 |
| @Email                    | 验证注解的元素值是Email，也可以通过正则表达式和flag指定自定义的email格式 |



**例如：**

```java
@RequestMapping("/user")
@RestController
@Validated
public class UserController {
    @Autowired
    UserService userService;

    @RequestMapping("/register")
    public Result<User> register(@Pattern(regexp = "^\\s{5,16}$") String username,@Pattern(regexp = "^\\s{5,16}$") String password){
        //判断是否重复
        User byUserName = userService.findByUserName(username);
        if (byUserName == null){
            //不重复
            userService.add(username,password);
            return new Result<User>(0,"成功！");
        }else{
            //重复
            return new Result<User>(1,"用户名被占用！");
        }

    }
}
```





















# 第五章 springboot快速整合ssm

### 1.添加依赖

```xml


<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-web</artifactId>
</dependency>

<dependency>
    <groupId>org.projectlombok</groupId>
    <artifactId>lombok</artifactId>
</dependency>

<dependency>
    <groupId>org.mybatis.spring.boot</groupId>
    <artifactId>mybatis-spring-boot-starter</artifactId>
    <version>3.0.3</version>
</dependency>

<dependency>
    <groupId>log4j</groupId>
    <artifactId>log4j</artifactId>
    <version>1.2.17</version>
</dependency>

<dependency>
    <groupId>com.github.pagehelper</groupId>
    <artifactId>pagehelper</artifactId>
    <version>5.3.0</version>
</dependency>

<dependency>
    <groupId>com.mysql</groupId>
    <artifactId>mysql-connector-j</artifactId>
</dependency>

<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter</artifactId>
</dependency>

<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-test</artifactId>
    <scope>test</scope>
</dependency>
```



### 2.持久层配置

在类上添加mapper注解

常用配置

```yml
spring:
  datasource:
    driver-class-name: com.mysql.cj.jdbc.Driver
    url: jdbc:mysql://localhost:3306/big_event
    username: root
    password: ok

mybatis:
  type-aliases-package: com.example.demo.entity
  configuration:
    map-underscore-to-camel-case: true  #开启驼峰命名和下划线规范
    log-impl: org.apache.ibatis.logging.log4j.Log4jImpl
    
      
# 配置mybatis-plus日志
mybatis-plus:
  configuration:
	log-impl: org.apache.ibatis.logging.stdout.StdOutImpl

```







# 第六章 自定义校验注解

### 1.编写注解

```java
package com.example.demo.annotation;

import com.example.demo.validation.StateValidation;
import jakarta.validation.Constraint;
import jakarta.validation.Payload;

import java.lang.annotation.*;

@Documented//元注解
@Constraint(validatedBy = {StateValidation.class})//提供指定校验规则的类
@Target({ElementType.FIELD})//元注解 标识可以用在哪里
@Retention(RetentionPolicy.RUNTIME)//元注解 标识该该注解会在哪个阶段进行保留
public @interface State{

    //提供校验失败后的提示信息
    String message() default "state的值只能是已发布或草稿";
    //指定分组
    Class<?>[] groups() default {};
    //负载
    Class<? extends Payload>[] payload() default {};
}
```



### 2.编写校验规则类

```java
package com.example.demo.validation;

import com.example.demo.annotation.State;
import jakarta.validation.ConstraintValidator;
import jakarta.validation.ConstraintValidatorContext;

//为哪个注解提供校验规则  校验的类型
public class StateValidation implements ConstraintValidator<State,String> {
    /**
     * 参数说明
     * @param value 将来要校验的数据
     * @param constraintValidatorContext
     * @return
     */
    @Override
    public boolean isValid(String value, ConstraintValidatorContext constraintValidatorContext) {
        //校验规则
        if (value == null){
            return false;
        }

        if (value.equals("已发布") ||value.equals("草稿")){
            return true;
        }

        return false;
    }
}
```





# 第七章 springboot项目部署

### 1.引入打包插件

```xml
<build>
    <plugins>
        <plugin>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-maven-plugin</artifactId>
        </plugin>
        <plugin>
            <groupId>org.apache.maven.plugins</groupId>
            <artifactId>maven-compiler-plugin</artifactId>
            <configuration>
                <source>16</source>
                <target>16</target>
            </configuration>
        </plugin>
    </plugins>
</build>
```



### 2.指令

通过mvn package进行项目的打包







# 第八章 springboot多环境开发

### 1.一个配置文件中进行配置

```yml
spring:
  profiles:
    active: dev  # 活跃的环境

---
spring:
  profiles: dev  # 开发环境配置
  datasource:
    url: jdbc:mysql://localhost:3306/dev_db
    username: root
    password: password123

---
spring:
  profiles: prod  # 生产环境配置
  datasource:
    url: jdbc:mysql://localhost:3306/prod_db
    username: root
    password: password123
```





### 2.在多个配置文件中进行配置

`application-dev.properties`（用于开发环境）

`application-test.properties`（用于测试环境）

`application-prod.properties`（用于生产环境）



在application中进行配置

```
spring:
  profiles:
    active: dev  # 活跃的环境
```







# 第九章 ThreadLocal线程局部变量

### 1.简介

ThreadLocal类被用来创建线程局部变量。通常情况下，我们是用一个变量来存储某个数据，这个变量是所有线程都可以访问的。但有时候我们希望这个变量的值能和线程状态关联，也就是每个线程的状态都能有属于它独立的一份数据。此时，我们就可以使用ThreadLocal。



### 2.简单用法

```java
@SpringBootTest
public class Test {
    @org.junit.jupiter.api.Test
    public void test(){
        ThreadLocal threadLocal = new ThreadLocal();
        User user = new User();
        threadLocal.set(user);
        Object o = threadLocal.get();
        System.out.println(o);
    }
}
```





### 3.工具类封装

```java
package com.example.demo.utlis;

import java.util.HashMap;
import java.util.Map;

/**
 * ThreadLocal 工具类
 */
@SuppressWarnings("all")
public class ThreadLocalUtil {
    //提供ThreadLocal对象,
    private static final ThreadLocal THREAD_LOCAL = new ThreadLocal();

    //根据键获取值
    public static <T> T get(){
        return (T) THREAD_LOCAL.get();
    }
   
    //存储键值对
    public static void set(Object value){
        THREAD_LOCAL.set(value);
    }

    //清除ThreadLocal 防止内存泄漏
    public static void remove(){
        THREAD_LOCAL.remove();
    }
}
```



### 4.实战用法

在拦截器中如果从jwt的载体中获取数据没有异常（也就是不为空时），拦截器会放行并且会把数据保存到threadlocal中，以便于往后的修改查询自己信息等操作

```java
package com.example.demo.Interceptor;

import com.example.demo.utlis.JwtUtil;
import com.example.demo.utlis.ThreadLocalUtil;
import jakarta.servlet.http.HttpServletRequest;
import jakarta.servlet.http.HttpServletResponse;
import org.springframework.stereotype.Component;
import org.springframework.web.servlet.HandlerInterceptor;

import java.util.Map;

@Component
public class LoginInterceptor implements HandlerInterceptor {
    @Override
    public boolean preHandle(HttpServletRequest request, HttpServletResponse response, Object handler) throws Exception {
        //验证令牌
        String authorization = request.getHeader("Authorization");

        //验证令牌
        try {
            Map<String, Object> stringObjectMap = JwtUtil.parseToken(authorization);
            ThreadLocalUtil.set(stringObjectMap);
            return true;
        } catch (Exception e) {
            //设置状态码
            e.printStackTrace();
            response.setStatus(401);
            return false;
        }
    }

    @Override
    public void afterCompletion(HttpServletRequest request, HttpServletResponse response, Object handler, Exception ex) throws Exception {
        ThreadLocalUtil.remove();
    }
}
```





# 第十章 JWT令牌

### 1.简介

全称：json web token

用于json数据格式的安全信息传输



### 2.组成

header(头)：记录令牌的类型，签名算法

payload(有效载荷)：携带定义的信息

signature(签名)：防止token被篡改，确保安全性



### 3.用法

创建令牌

```java
@Test
void contextLoads() {
    //数据对象
    Map map = new HashMap();
    map.put("id",1);
    map.put("name","wang");
    String jwt = JWT.create()
            .withClaim("user", map)//添加载荷
            .withExpiresAt(new Date(System.currentTimeMillis() + 1000 * 60 * 60 * 3))//添加过期时间
            .sign(Algorithm.HMAC256("itqingniao"));//指定算法，配置秘钥

    //打印生成的JWT令牌
    System.out.println(jwt);
}
```



解析令牌

```java
@Test
void contextLoads1() {
    //生成的JWT令牌
    String token = "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJ1c2VyIjp7Im5hbWUiOiJ3YW5nIiwiaWQiOjF9LCJleHAiOjE3MDU3NTYxMzJ9.1nRyPqfNi7WKsVxgRfFe0y9K6G__lhA_razB2V96lVk";
                                            //指定算法，配置秘钥
    JWTVerifier jwtVerifier = JWT.require(Algorithm.HMAC256("itqingniao")).build();
    //进行审核
    DecodedJWT verify = jwtVerifier.verify(token);
    //获取载荷
    Map<String, Claim> claims = verify.getClaims();
    //打印
    System.out.println(claims.get("user"));
}
```



### 4.实战用法

在登录成功后创建令牌并返回

```java
@PostMapping("/login")
public Result<String> login(@Pattern(regexp = "^[a-zA-Z0-9]{5,16}$") String username, @Pattern(regexp = "^[a-zA-Z0-9]{5,16}$") String password) {
    User byUserName = userService.findByUserName(username);
    if (byUserName == null) {
        return new Result<String>(1, "用户名错误！！！");
    }
     
    if (byUserName.getPassword().equals(Md5Util.getMD5String(password))) {
        Map map = new HashMap();
        map.put("id", byUserName.getId());
        map.put("username", byUserName.getUsername());
        String token = JwtUtil.genToken(map);
        return new Result<String>(0, "登录成功！！", token);
    }	
    return new Result<String>(1, "密码错误！！");
}
```





# 第十一章 UUID随机标识符

### 1.简介

在Java中，UUID类用于生成唯一的随机标识符。全称是Universally Unique Identifier，即通用唯一识别码。这个类提供了一系列的静态方法用于生成UUID。



UUID类所生成的标识符可以保证在同一时空中的所有机器上都是唯一的。每个UUID由一组十六进制数字组成，共32位。整个UUID为一个128位的数字，由五段式表示，例如：550e8400-e29b-41d4-a716-446655440000。



### 2.用法

```java
UUID uuid = UUID.randomUUID();
System.out.println(uuid);
```



### 3.实战

```java
String filename = UUID.randomUUID().toString()+originalFilename.substring(originalFilename.lastIndexOf("."));
```

主要就是为一些需要独立的数据设置名称







# 第十二章 springboot常用注解

### 1.列表

| 注解                     | 用法                                                         |
| ------------------------ | ------------------------------------------------------------ |
| @SpringBootApplication   | 这是一个复合注解，包含了@SpringBootConfiguration（表示应用的主配置类）、@EnableAutoConfiguration（开启自动配置功能）、@ComponentScan（组件扫描）等多个注解。 |
| @RestController          | 结合了@Controller和@ResponseBody的功能，通常用于创建REST API。 |
| @RequestMapping          | 请求的格式                                                   |
| @GetMapping              | 请求的格式                                                   |
| @PostMapping             | 请求的格式                                                   |
| @PutMapping              | 请求的格式                                                   |
| @DeleteMapping           | 请求的格式                                                   |
| @Autowired               | 用于自动装配Spring容器中的bean。                             |
| @Service                 | 用于表示Spring容器要自动扫描的组件                           |
| @Controller              | 用于表示Spring容器要自动扫描的组件                           |
| @Repository              | 用于表示Spring容器要自动扫描的组件                           |
| @Component               | 用于表示Spring容器要自动扫描的组件                           |
| @Configuration           | 用于定义配置类，类似于Spring的XML配置文件。                  |
| @Bean                    | 用于把方法返回的对象定义为Spring容器中的bean。               |
| @EnableAutoConfiguration | 开启自动配置，Spring Boot会根据应用的依赖自动配置相关的bean。 |
| @Value                   | 用于注入配置文件中的值。                                     |
| @Profile                 | 用于定义在哪个环境下会加载这个bean。                         |
| @Entity                  | 用于标注实体类，与数据库表对应。                             |



# 第十三章 MultipartFile上传文件

### 前端

```html
<input type="file" id="file" multiple>
<br>
<input type="button" onclick="upload1()" value="上传" id="shangchuan">


<script src="jquery-1.8.3.min.js"></script>
<script>
    function upload1() {
        var elementById = document.getElementById("file").files;
        console.log(elementById)

        var formData = new FormData();
        for (var i = 0; i < elementById.length; i++) {
            formData.append("file", elementById[i]);
        }

        $.ajax({
            url: '/upload',
            type: 'POST',
            data: formData,
            processData: false,    // 告诉 jQuery 不要处理数据
            contentType: false,    // 告诉 jQuery 不要设置 contentType
            success: function(response) {
                console.log(response);
            }
        });
    }
</script>
```



### 后端

```java
 @PostMapping("upload")
    //在进行多文件上传需要加上@RequestParam注解
    public Result upload(@RequestParam("file") MultipartFile file[]){


        for (MultipartFile multipartFile : file) {
            File file1 = new File("E:\\kaoshi\\target\\classes\\static\\img",multipartFile.getOriginalFilename());
            try {
                multipartFile.transferTo(file1);
            } catch (IOException e) {
                throw new RuntimeException(e);
            }
        }

        return new Result(200,null,"获取数据成功！！");
    }
```







# 第十四章 Swagger3使用

### 1.主要功能和特点

**API 文档生成**：

- 自动从代码注释和注解中生成 API 文档。
- 支持多种编程语言和框架，如 Java、Spring Boot、Node.js、Python 等。

**可视化界面**：

- 提供一个交互式的用户界面（Swagger UI），用户可以通过该界面查看和测试 API。
- 支持在浏览器中直接调用 API，方便开发和调试。



### 2.常用注解

| 注解                                                         | 作用                       |
| ------------------------------------------------------------ | -------------------------- |
| @Tag(name = "支付微服务模块",description = "支付CRUD")       | 用在controller上面标注     |
| @Operation(summary = "新增",description = "新增支付流水方法,对象作为参数") | 用在具体controller上面标注 |
| @Schema(title = "支付id")                                    | 用在实体                   |





### 3.使用

**添加依赖**

```xml
 <dependency>
            <groupId>org.springdoc</groupId>
            <artifactId>springdoc-openapi-starter-webmvc-ui</artifactId>
      	  <version>2.2.0</version>
</dependency>
```



**编写config**

```java
package com.it.wang.config;

import io.swagger.v3.oas.models.ExternalDocumentation;
import io.swagger.v3.oas.models.OpenAPI;
import io.swagger.v3.oas.models.info.Info;
import org.springdoc.core.models.GroupedOpenApi;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;

/**
 * @Author WongSilver
 * @Date 2024/3/12 21:55
 * @Description <a href="http://localhost:8001/swagger-ui/index.html">Swagger3管理页面</a>
 */
@Configuration
public class Swagger3Config {

    @Bean
    public GroupedOpenApi payApi() {
        return GroupedOpenApi.builder().group("支付微服务模块").pathsToMatch("/pay/**").build();
    }

    @Bean
    public GroupedOpenApi otherApi() {
        return GroupedOpenApi.builder().group("其他微服务模块").pathsToMatch("/other/**").build();
    }

    @Bean
    public OpenAPI docsOpenApi() {
        return new OpenAPI()
                .info(new Info()
                        .title("spring-cloud")
                        .description("通用设计")
                        .version("v1.0")
                )
                .externalDocs(new ExternalDocumentation()
                        .description("")
                        .url("")
                );
    }
}
```



访问：http://localhost:8080/swagger-ui/index.html



