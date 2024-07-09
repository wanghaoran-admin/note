# 第一章 springmvc简介

### 1.springmvc是什么

Spring MVC（Model-View-Controller）是 Spring 框架中用于构建基于 Java 的 Web 应用程序的一部分。它提供了一种设计模式，即 MVC 模式，用于开发可维护和灵活的 Web 应用。



在 Spring MVC 中，应用程序被划分为三个主要的组件：

### 2.mvc中三个主要的组件

1. **Model（模型）：** 代表应用程序的业务逻辑和数据。通常，模型对象包含应用程序的数据和处理数据的业务逻辑。
2. **View（视图）：** 负责渲染模型数据，将其呈现给用户。视图通常是用户界面的一部分，可以是 JSP 页面、HTML 页面、XML 文档等。
3. **Controller（控制器）：** 接收用户的请求，处理用户输入，并调用模型和视图来完成用户的请求。控制器负责处理用户的输入并更新模型及视图。



# 第二章 springmvc入门案例（注解）

### 1.导入依赖

```xml
<dependency>
          <groupId>org.apache.tomcat</groupId>
          <artifactId>tomcat-api</artifactId>
          <version>8.5.47</version>
      </dependency>

      <dependency>
          <groupId>org.springframework</groupId>
          <artifactId>spring-webmvc</artifactId>
          <version>5.2.10.RELEASE</version>
</dependency>
```



### 2.编写SpringMvc配置类

```java
//声明配置类
@Configuration
//扫描注解
@ComponentScan("com.example.demo2.controller")
public class SpringMvcConfig {

}
```





### 3.编写web容器上下文环境

```java
public class ServletContainersInitConfig extends AbstractDispatcherServletInitializer {
	

    @Override //要求你在这里加载springmvc配置容器对象
    protected WebApplicationContext createServletApplicationContext() {
        AnnotationConfigWebApplicationContext ctx = new AnnotationConfigWebApplicationContext();
        			//配置类
        ctx.register(SpringMvcConfig.class);
        return ctx;
    }

    @Override //请求过来后是由springmvc的路径
    protected String[] getServletMappings() {
        return new String[]{"/"}; //代表所有请求都由springmvc处理
    }

    @Override //要求你在这里加载spring配置容器对象
    protected WebApplicationContext createRootApplicationContext() {
        return null;
    }
}
```



### 4.编写controller

```java
//定义控制层
@Controller
public class UserController {
    //访问url
    @RequestMapping("/save")
    //返回格式【响应体:JSON】
    @ResponseBody
    public String save(){
        System.out.println("user.....");
        return "{'info':'springmvc'}";
    }
}
```



### 5.AbstractDispatcherServletInitializer类详解

该类是springmvc提供的快速初始化web3.0容器的抽象类

该类提供三个接口供用户实现

createServletApplicationContext（）方法，创建容器时，加载springmvc对应的bean并放入webApplicationContext对象范围中，及整个web容器范围 





### 6.bean加载的控制

@ComponentScan

```java
@ComponentScan(value = "com.example.demo2",
excludeFilters = @ComponentScan.Filter(
        type = FilterType.ANNOTATION,
        classes = Controller.class
    )
)
```

该注解会扫描全部bean并排除controller层中的bean，不会纳入spring容器中

【注：若使用该方法，controller层中就不必再扫描了】





### 7.简化web容器上下文环境的编写

继承 AbstractAnnotationConfigDispatcherServletInitializer 类

```java
public class ServletContainersInitConfig extends AbstractAnnotationConfigDispatcherServletInitializer {
    @Override  //spring配置文件
    protected Class<?>[] getRootConfigClasses() {
        return new Class[0];
    }

    @Override  //springmvc配置文件
    protected Class<?>[] getServletConfigClasses() {
        return new Class[]{SpringMvcConfig.class};
    }

    @Override	//哪些路径归springmvc处理
    protected String[] getServletMappings() {
        return new String[]{"/"};
    }
}
```





# 第三章 springmvc入门案例（xml）

### 1.添加依赖

```xml
<dependency>
          <groupId>org.springframework</groupId>
          <artifactId>spring-webmvc</artifactId>
          <version>5.3.20</version>
      </dependency>

      <dependency>
          <groupId>org.apache.tomcat</groupId>
          <artifactId>tomcat-api</artifactId>
          <version>8.5.47</version>
</dependency>
```



### 2.配置tomcat核心文件

```xml
<servlet>
        <servlet-name>SpringMVCDispatcherServlet</servlet-name>
        <servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
        <!--配置初始化参数,用于读取SpringMVC的配置文件-->
        <init-param>
            <param-name>contextConfigLocation</param-name>
            <param-value>classpath:springmvc-config.xml</param-value>
        </init-param>
        <!--配置servlet的对象的创建时间点:应用加载时创建
            只能是非0正整数,表示启动顺序-->
        <load-on-startup>1</load-on-startup>
</servlet>

<!--配置映射 url-pattern:哪些路径归springmvc管理-->
<servlet-mapping>
        <servlet-name>SpringMVCDispatcherServlet</servlet-name>
        <url-pattern>/</url-pattern>
</servlet-mapping>
```



### 3.编写controller

```java
@RequestMapping("/user")
@Controller
public class UserController{

    @RequestMapping("/find")
    public void test1(){
        System.out.println("正在执行springmvc");
    }
}
```





# 第四章 入门案例的执行过程及原理、组件分析

### 1.执行过程

**1.服务器启动**

服务器启动，应用被加载。读取到 web.xml 中的配置创建 spring 容器并且初始化容器中的对象。



**2.浏览器发送请求**

浏览器发送请求，被 `DispatherServlet` 捕获，该 Servlet 并不处理请求，而是把请求转发出去。转发的路径是根据请求 URL，匹配@RequestMapping 中的内容。



**3.匹配到了后**

匹配到了后，执行对应方法。该方法有一个返回值。



**4.根据方法的返回值**

根据方法的返回值，借助 InternalResourceViewResolver 找到对应的结果视图。



**5.渲染结果**

渲染结果视图，响应浏览器。



### 2.涉及的组件

 **DispatcherServlet: 前端控制器**

用户请求到达前端控制器，它就相当于MVC模式中的C，DispatcherServlet 是整个流程控制的中心，由它调用其它组件处理用户的请求，DispatcherServlet 的存在降低了组件之间的耦合性。



**HandlerMapping: 处理器映射器**

HandlerMapping 负责根据用户请求找到 Handler 即处理器，SpringMVC 提供了不同的映射器实现不同的映射方式，例如：配置文件方式，实现接口方式，注解方式等。



**Handler: 处理器**

它就是我们开发中要编写的具体业务控制器。由 DispatcherServlet 把用户请求转发到 Handler。由Handler 对具体的用户请求进行处理。



**HandlerAdapter: 处理器适配器**

通过 HandlerAdapter 对处理器进行执行，这是适配器模式的应用，通过扩展适配器可以对更多类型的处理器进行执行。



**View Resolver: 视图解析器**

View Resolver 负责将处理结果生成 View 视图，View Resolver 首先根据逻辑视图名解析成物理视图名即具体的页面地址，再生成 View 视图对象，最后对 View 进行渲染将处理结果通过页面展示给用户。 



**View: 视图**

SpringMVC 框架提供了很多的 View 视图类型的支持，包括：jstlView、freemarkerView、pdfView等。我们最常用的视图就是 jsp。

一般情况下需要通过页面标签或页面模版技术将模型数据通过页面展示给用户，需要由程序员根据业务需求开发具体的页面。



**`<mvc:annotation-driven>`说明**

在 SpringMVC 的各个组件中，处理器映射器、处理器适配器、视图解析器称为 SpringMVC 的三大组件。使 用 `<mvc:annotation-driven>` 自 动加载 RequestMappingHandlerMapping （处理映射器） 和RequestMappingHandlerAdapter （ 处 理 适 配 器 ） ， 可 用 在 SpringMVC.xml 配 置 文 件 中 使 用`<mvc:annotation-driven>`替代注解处理器和适配器的配置。









# 第五章 PostMan简介

### 1.简介

PostMan是一款功能强大的网页调试与发送网页http请求的Chrome插件

作用：常用于接口调试

特征：简单  实用  美观  





# 第六章 springmvc常用注解



| 注解名称             | 用法                                                         |
| -------------------- | ------------------------------------------------------------ |
| `@RequestMapping`    | 该注解可以作用在类或者方法上，用于设定可以处理哪些URL请求。  |
| `@Controller`        | 在类上使用，标示该类是一个处理者，Spring将会把它视为控制器。 |
| `@RequestParam`      | 用在方法的某个参数前面，表示请求参数从request中获取。        |
| `@PathVariable`      | 用在方法的某个参数前面，表示URL中的参数。                    |
| `@GetMapping`        | 用于处理GET类型的请求，是@RequestMapping(method=RequestMethod.GET)的简化写法。 |
| `@PostMapping`       | 用于处理POST类型的请求，是@RequestMapping(method=RequestMethod.POST)的简化写法。 |
| `@ResponseBody`      | 如果放在方法上，表示该方法的返回结果直接写入HTTP response body中，一般在异步获取数据时使用。 |
| `@RequestBody`       | 用于读取请求体中的数据。                                     |
| `@RestController`    | 是@Controller和@ResponseBody的组合注解。表示该类所有方法返回的数据直接写入HTTP Response的Body中。 |
| `@ModelAttribute`    | 绑定请求和表单域，参数为对象类型。                           |
| `@SessionAttributes` | 将数据放在session域内。                                      |
| `@Autowired`         | 自动按照类型匹配，它可以对类成员变量、方法及构造函数进行标注，完成自动装配的工作。 |
| `@DateTimeFormat`    | 主要用于控制器方法参数上,格式化日期                          |







# 第七章 带参请求

### 1.基础语法

```java
@Controller
@RequestMapping("/user")
public class UserController {

    @ResponseBody
    @RequestMapping("show")//形参要和url上的属性名一致，否则null
    public String shu(String name,int sex){
        System.out.println(name);
        System.out.println(sex);
        return "helloword";
    }
}
```



### 2.参数种类

#### 2.1 普通参数

```java
//发送请求 http://localhost:8080/oa/user/show1?name=哇&sex=13
@ResponseBody
@RequestMapping("show")//不一致的参数可以使用@RequestParam("url参数的名称")
public String shu(String name,int sex){ 
    System.out.println(name);
    system.out.println(sex);
return "helloword";
}
```



#### 2.2 pojo类型

```java
//发送请求 http://localhost:8080/oa/user/show1?name=哇&sex=13
@ResponseBody
@RequestMapping("show1")
//如果url参数和实体属性一致的话会进行自动封装【set方法】没有封装不了
public String shu(user u){
    System.out.println(u);
    return "hello word";
}

```



#### 2.3 嵌套pojo

```java
//发送请求 http://localhost:8080/oa/user/show2?name=哇&sex=13&address.sheng=江苏
@Data
public class user {
    private String name;
    private int sex;
    private address address;
}
//如果实体属性中有引用类型的话,url上可以写对象名.属性的方式进行取值
//【例如】  其中name,sex都是基本数据  address是引用数据类型

```



#### 2.4 数组

```java
//发送请求 http://localhost:8080/oa/user/show2?strings=wang&strings=wcw
@ResponseBody
    @RequestMapping("show2")
    public String shu(String[] strings){
        System.out.println(Arrays.toString(strings));
        return "hello word";
    }
```



#### 2.5 集合

```java
//发送请求 http://localhost:8080/oa/user/show3?strings=wang&strings=wcw
@RequestMapping("show3")
public String shu(@RequestParam List strings){
    System.out.println(strings);
    return "hello word";
}
```



#### 2.6 json参数

1.添加依赖【自动转化json数据】

```xml
<dependency>
    <groupId>com.fasterxml.jackson.core</groupId>
    <artifactId>jackson-databind</artifactId>
    <version>2.9.0</version>
</dependency>
```



2.发送请求

```java
//发送请求 http://localhost:8080/oa/user/show4
//携带json数据["wanf","3sc","scw"]
@ResponseBody
@RequestMapping("show4")//json数据是在请求体中的所以要用@RequestBody【只能有一个】
public String shu1(@RequestBody List<String> strings){
    System.out.println(strings);
    return "hello word";
}
```



#### 2.8 日期参数

```java
//发送请求 http://localhost:8080/oa/user/show5?date=2024/1/1&date1=2025-1-1&date2=2025/1/1 8:04:08
//日期类型转化可以使用 @DateTimeFormat(pattern = "yyyy-MM-dd")注解
//pattern中是你要和url中的参数一致
    @ResponseBody
    @RequestMapping("show5")
    public String shu1(Date date,
                       @DateTimeFormat(pattern = "yyyy-MM-dd") Date date1,
                       @DateTimeFormat(pattern = "yyyy/MM/dd HH:mm:ss") Date date2){
        System.out.println(date);
        System.out.println(date1);
        System.out.println(date2);
        return "hello word";
    }
```















# 第八章 rest风格

### 1.rest简介

REST:(Representational State Transfer),表示形式状态转换

传统风格描述形式

http://localhost/user/getById?id=1

http://localhost/user/saveUser



REST风格描述形式

http://localhost/user/getById/1

http://localhost/user





### 2.优点

1.隐藏资源的访问行为，无法通过地址得知对资源是何种操作

2.书写简化



### 3.风格约定

springmvc【一共支持8种风格】

Get:用于所有查询

Post:用于新增、保存

Put:用于修改、更新

Delete:用于删除



### 4.案例【简化前】

```java
package com.example.demo2.controller;
import org.springframework.stereotype.Controller;
import org.springframework.web.bind.annotation.*;

@Controller
@RequestMapping("user")
public class RestUserController {

    @ResponseBody
    @RequestMapping(value = "find",method = RequestMethod.GET)
    public String find(){
        System.out.println("find");
        return "find";
    }

    @ResponseBody
    @RequestMapping(value = "add/{user}",method = RequestMethod.POST)
    //@PathVariable("user") 路径参数注解
     public String add(@PathVariable("user") String use){
        System.out.println(use);
        System.out.println("add");
        return "add";
    }
}
```





### 5.案件【简化后】

```java
package com.example.demo2.controller;
import org.springframework.stereotype.Controller;
import org.springframework.web.bind.annotation.*;

@RestController
@RequestMapping("user")
public class RestUserController {

    @GetMapping("find")
    public String find(){
        System.out.println("find");
        return "find";
    }

    @PostMapping("add/{user}")
    public String add(@PathVariable("user") String use){
        System.out.println(use);
        System.out.println("add");
        return "add";
    }

}
```

【简化说明：】

@RestController  相当于  @Controller+@ResponseBody

@GetMapping  相当于  @RequestMapping(value ="find",method= RequestMethod.GET)
    	





# 第九章 数据交互Result类编写

### 1.Result类编写

```java
package com.example.demo2.Result;

public class Result {
    private Object data; //数据
    private Integer code; //编码
    private String msg; //异常信息

    public Result(Object data, Integer code) {
        this.data = data;
        this.code = code;
    }

    public Result(Object data, Integer code, String msg) {
        this.data = data;
        this.code = code;
        this.msg = msg;
    }

    public Object getData() {
        return data;
    }

    public void setData(Object data) {
        this.data = data;
    }

    public Integer getCode() {
        return code;
    }

    public void setCode(Integer code) {
        this.code = code;
    }

    public String getMsg() {
        return msg;
    }

    public void setMsg(String msg) {
        this.msg = msg;
    }

    public Result() {
    }

    @Override
    public String toString() {
        return "Result{" +
                "data=" + data +
                ", code=" + code +
                ", msg='" + msg + '\'' +
                '}';
    }
}
```



### 2.Code类编写

```java
package com.example.demo2.Result;

public class Code {
    public static final Integer SAVE_OK = 20011;
    public static final Integer DELETE_OK = 20011;
    public static final Integer UPDATE_OK = 20011;
    public static final Integer ADD_OK = 20011;

    public static final Integer SAVE_ERR = 20010;
    public static final Integer DELETE_ERR = 20010;
    public static final Integer UPDATE_ERR = 20010;
    public static final Integer ADD_ERR = 20010;
}
```



### 3.controller变化

```java
@PostMapping("add")
public Result add(@RequestBody user user){
    boolean flag = userService.add(user);
    return new Result(flag?Code.ADD_OK:Code.ADD_ERR,flag);
}
```









# 第十章 异常处理【aop思想】

### 1.异常处理方案

**恶意参数异常**

发送消息给用户，提醒规范操作



**系统异常**

发送固定消息传递给用户，安抚用户

发送给运维人员，提醒维护

记录日志



**其他异常**

发送固定消息传递给用户，安抚用户

发送特定消息给编程人员，提醒维护

记录日志





### 2.案例【业务异常】

ProjectExceptionAdvice类编写

```java
package com.example.demo2.controller;

import org.springframework.web.bind.annotation.ExceptionHandler;
import org.springframework.web.bind.annotation.RestControllerAdvice;

@RestControllerAdvice
public class ProjectExceptionAdvice {
    @ExceptionHandler(Exception.class)
    public Result doException(Exception ex){
        System.out.println("异常。。。。。");
        return new Result(666,null);
    }
}

```

【说明】

@RestControllerAdvice 声明异常统一处理类

@ExceptionHandler 要拦截的异常类型【在项目中遇见哪些异常进入此方法】

参数：要处理的异常对象





### 3.案例【系统异常】 

编写系统异常类

```java
package com.example.demo2.controller;

public class SystemException extends RuntimeException{
    private Integer code;


    public SystemException(Integer code, String message) {
        super(message);
        this.code = code;
    }

    public Integer getCode() {
        return code;
    }

    public void setCode(Integer code) {
        this.code = code;
    }

    public SystemException(String message, Throwable cause, Integer code) {
        super(message, cause);
        this.code = code;
    }


}
```



控制器

```java
@RestController
@RequestMapping("user")
public class RestUserController {
    @GetMapping("finById")
    public String finById(int id){
	   //模拟服务器异常
        try {
            int i = id/0;
        } catch (Exception e) {
            throw new SystemException(200,"服务器异常",e);
        }

        return "find";
    }
}
```



ProjectExceptionAdvice类处理

```java
package com.example.demo2.controller;

import com.example.demo2.Exception.BusinessException;
import com.example.demo2.Result.Result;
import org.springframework.web.bind.annotation.ExceptionHandler;
import org.springframework.web.bind.annotation.RestControllerAdvice;

@RestControllerAdvice
public class ProjectExceptionAdvice {
         @ExceptionHandler(SystemException.class)
         public Result doSystemException(SystemException ex){
             //记录日志
             //发送消息给运维
             //发送邮件给开发人员
             return new Result(ex.getCode(),null,"系统繁忙请稍后再试！！");
         }
}
```





### 4.案例【恶意参数异常】

编写业务异常类

```java
package com.example.demo2.Exception;

public class MaliciousParameterException extends RuntimeException{
    private Integer code;

    public Integer getCode() {
        return code;
    }

    public void setCode(Integer code) {
        this.code = code;
    }

    public MaliciousParameterException(Integer code, String message ) {
        super(message);
        this.code = code;
    }

    public MaliciousParameterException(Integer code, String message, Throwable cause ) {
        super(message, cause);
        this.code = code;
    }

}

```



控制器

```java
package com.example.demo2.controller;
import com.example.demo2.Exception.MaliciousParameterException;
import org.springframework.web.bind.annotation.*;

@RestController
@RequestMapping("user")
public class RestUserController {

    @GetMapping("finById")
    public String finById(int id){
        if (id == 1){
            throw new MaliciousParameterException(666,"请不要挑战我");
        }
        return "find";
    }

}

```



ProjectExceptionAdvice类处理

```java
@RestControllerAdvice
public class ProjectExceptionAdvice {
    @ExceptionHandler(MaliciousParameterException.class)
    public Result doMaliciousParameterException(MaliciousParameterException ex){
        //记录日志
        //发送消息给运维
        //发送邮件给开发人员
        //警告用户 
        return new Result(ex.getCode(),null,ex.getMessage());
    }
}

```











# 第十一章 拦截器

### 1.概念

拦截器（Interceptor）是一种动态拦截方法调用的机制，在springmvc中动态拦截控制器方法的执行

作用：在指定方法调用前后执行预习设定的代码

阻止原始方法的执行



### 2.拦截器和过滤器的区别

1.归属不同：filter属于servlet技术，Interceptor属于springmvc技术

2.拦截内容不同：filter对所有访问进行增强，Interceptor仅对springmvc访问进行增强



### 3.拦截器方法、参数说明

```java
1. **preHandle方法：**
   - 方法签名：`boolean preHandle(HttpServletRequest request, HttpServletResponse response, Object handler) throws Exception;`
   - 参数：
     - `HttpServletRequest request`：表示当前请求的`HttpServletRequest`对象。
     - `HttpServletResponse response`：表示当前请求的`HttpServletResponse`对象。
     - `Object handler`：表示被处理的处理程序对象，通常是一个控制器类的实例。
   - 返回值：如果返回true，表示继续处理请求；如果返回false，表示中断请求处理。

   `preHandle`方法在请求处理之前被调用，允许你执行一些预处理逻辑，例如权限检查、日志记录等。

2. **postHandle方法：**
   - 方法签名：`void postHandle(HttpServletRequest request, HttpServletResponse response, Object handler, ModelAndView modelAndView) throws Exception;`
   - 参数：
     - `HttpServletRequest request`：表示当前请求的`HttpServletRequest`对象。
     - `HttpServletResponse response`：表示当前请求的`HttpServletResponse`对象。
     - `Object handler`：表示被处理的处理程序对象，通常是一个控制器类的实例。
     - `ModelAndView modelAndView`：表示请求处理完成后的视图对象。
   - 返回值：无。

   `postHandle`方法在请求处理之后、视图渲染之前被调用，允许你对响应进行一些后处理工作。

3. **afterCompletion方法：**
   - 方法签名：`void afterCompletion(HttpServletRequest request, HttpServletResponse response, Object handler, Exception ex) throws Exception;`
   - 参数：
     - `HttpServletRequest request`：表示当前请求的`HttpServletRequest`对象。
     - `HttpServletResponse response`：表示当前请求的`HttpServletResponse`对象。
     - `Object handler`：表示被处理的处理程序对象，通常是一个控制器类的实例。
     - `Exception ex`：表示在请求处理过程中发生的异常，如果没有异常则为null。
   - 返回值：无。

   `afterCompletion`方法在请求处理之后、视图渲染之后被调用，允许你对响应进行一些后处理工作。

```





### 4.案例

编写拦截器

```java
package com.example.demo2.controller.interceptor;

import org.springframework.web.servlet.HandlerInterceptor;
import org.springframework.web.servlet.ModelAndView;

import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;
import javax.xml.ws.handler.Handler;

public class ProjectInterceptor implements HandlerInterceptor {
    @Override
    public boolean preHandle(HttpServletRequest request, HttpServletResponse response, Object handler) throws Exception {
        System.out.println(1);
        return true;
    }

    @Override
    public void postHandle(HttpServletRequest request, HttpServletResponse response, Object handler, ModelAndView modelAndView) throws Exception {
        System.out.println(2);
    }

    @Override
    public void afterCompletion(HttpServletRequest request, HttpServletResponse response, Object handler, Exception ex) throws Exception {
        System.out.println(3);
    }
}
```



编写配置文件【springmvc.xml】

```xml
<mvc:interceptors>
        <mvc:interceptor>
            <mvc:mapping path="/user/**"/> <!-- 拦截的路径 -->
            <bean class="com.example.demo2.controller.interceptor.ProjectInterceptor"/> <!-- 拦截器的Bean -->
        </mvc:interceptor>
</mvc:interceptors>
```

