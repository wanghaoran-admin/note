# 第一章 springcloud简介

### 1.springcloud解决了什么？

Spring Cloud 解决了构建分布式系统和微服务架构中常见的一些复杂性和挑战，主要包括以下几个方面：

服务注册与发现

负载均衡

断路器

...........



### 3.spring主要组件

#### **1.服务注册与发现**

- **Eureka** 、 Netflix开发的服务注册与发现组件，用于构建高可用的服务注册中心。
- **Consul**、**Zookeeper**：提供替代的服务注册与发现解决方案。



#### **2.负载均衡**

- **Ribbon**：Netflix 开发的服务注册与发现组件，用于构建高可用的服务注册中心。
- **LoadBalancer**：提供替代的服务注册与发现解决方案。



#### 3.**断路器**

- **Hystrix**：提供了断路器模式的实现，防止分布式系统中的雪崩效应。



#### 4.**网关**

- **Zuul**、**Gateway**：用于构建 API 网关服务，提供路由、过滤、认证等功能。



#### 5.**配置管理**

- **Config**：集中式配置管理，支持配置的版本管理、环境分离等。
- **Bus**：消息总线，用于在分布式系统中传播配置的变化。



#### **6.服务调用**

- **Feign**：基于声明式 REST 客户端，简化了服务调用的方式。
- **RestTemplate**：传统的 REST 客户端，也可以用于服务调用。



#### **7.消息驱动**

- **Stream**：简化消息驱动微服务开发的框架，支持多种消息中间件。



#### **8.分布式追踪**

- **Sleuth**：提供分布式链路追踪，帮助监控和调试微服务架构中的请求流程。



#### **9.服务网格**：

- **Spring Cloud Alibaba**：阿里巴巴提供的一组解决微服务架构的开源软件解决方案，包括服务发现、配置管理、负载均衡、消息总线、流量控制、熔断器等。







# 第二章 RestTemplate 的介绍

### 1.介绍

`RestTemplate` 是 Spring Framework 提供的一个用于访问 RESTful Web 服务的模板类。它简化了与 RESTful 服务交互的过程，提供了多种便捷的方法来发送 HTTP 请求，并处理响应数据



### 2.功能特性

支持多种 HTTP 方法：GET、POST、PUT、DELETE 等。

支持请求和响应的序列化与反序列化，通常使用 JSON 或 XML 格式。

提供了便捷的方法处理请求头、URL 参数、请求体等。

支持异步请求处理（Spring 5+）。

可以通过拦截器（Interceptor）进行请求和响应的预处理和后处理。



### 3.适用场景

与任何 RESTful Web 服务进行交互，如调用第三方 API、内部微服务通信等。





### 4.常用方法

#### 1. 发送 GET 请求并获取响应体为字符串

```java
java复制代码RestTemplate restTemplate = new RestTemplate();
String url = "https://api.example.com/data";
String response = restTemplate.getForObject(url, String.class);
System.out.println("Response: " + response);
```



#### 2. 发送 POST 请求并传递请求体，并获取响应体为对象

```java
java复制代码RestTemplate restTemplate = new RestTemplate();
String url = "https://api.example.com/create";
User newUser = new User("John", 30); // Assuming User class with appropriate constructor
User createdUser = restTemplate.postForObject(url, newUser, User.class);
System.out.println("Created User: " + createdUser.getName());
```



#### 3. 发送 DELETE 请求并带参数

```java
java复制代码RestTemplate restTemplate = new RestTemplate();
String url = "https://api.example.com/delete/{id}";
restTemplate.delete(url, "123");
System.out.println("Deleted successfully.");
```



#### 4. 发送 PUT 请求并传递请求体

```java
java复制代码RestTemplate restTemplate = new RestTemplate();
String url = "https://api.example.com/update";
User updatedUser = new User("123", "Updated Name"); // Assuming User class with appropriate constructor
restTemplate.put(url, updatedUser);
System.out.println("Updated successfully.");
```



#### 5. 获取响应头信息

```java
java复制代码RestTemplate restTemplate = new RestTemplate();
String url = "https://api.example.com/data";
ResponseEntity<String> responseEntity = restTemplate.getForEntity(url, String.class);
HttpHeaders headers = responseEntity.getHeaders();
String contentType = headers.getFirst(HttpHeaders.CONTENT_TYPE);
System.out.println("Content-Type: " + contentType);
```





# 第三章 consul服务注册与发现

### 1.Consul 的主要功能

**服务注册与发现**：自动化服务注册，允许服务发现和负载均衡。

**健康检查**：定期检查服务的健康状态，确保服务的可用性。

**配置中心**：分布式键值存储，可以存储配置和其他共享数据。

**服务网格**：通过代理提供服务间的安全通信、负载均衡和策略控制。



### 2.心跳检查

**监测服务健康状态**: 通过心跳检查，服务注册中心可以知道每个服务的运行状况。如果服务出现故障或者停止响应，注册中心可以及时发现，并进行相应的处理，如通知运维人员或者自动触发故障转移。



**服务发现与负载均衡**: 心跳检查的结果也用于服务发现，客户端可以向注册中心查询健康的服务实例，并将请求分发给这些健康的实例，实现负载均衡。



**自动治理和自愈**: 在微服务架构中，心跳检查是自动治理的基础。通过心跳检查，系统可以实现自动发现、自动注册、自动调整和自动恢复，提高系统的稳定性和可靠性。



### 3.使用

```xml
<!--    consul依赖-->
		<dependency>
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-starter-consul-discovery</artifactId>
<!--            <exclusions>-->
<!--                <exclusion>-->
<!--                    <groupId>commons-logging</groupId>-->
<!--                    <artifactId>commons-logging</artifactId>-->
<!--                </exclusion>-->
<!--            </exclusions>-->
        </dependency>

<!--    心跳检查依赖-->
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-actuator</artifactId>
        </dependency>
```



### 4.application和bootstarp

**加载顺序**：

`bootstrap.properties`：在应用程序上下文刷新之前加载。它主要用于在应用程序启动时初始化更早的配置，如配置中心客户端的配置。

`application.properties`：在`bootstrap.properties`之后加载，主要用于应用程序的常规配置。





### 5.服务发现与注册

#### 添加依赖

```xml
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-starter-consul-discovery</artifactId>
    <exclusions>
        <exclusion>
            <groupId>commons-logging</groupId>
            <artifactId>commons-logging</artifactId>
        </exclusion>
    </exclusions>
</dependency>
```



#### **配置yml**

```yml
spring:
  application:
    name: cloud-cunsumer-order
  cloud:
    consul:
      port: 8500  #consul的端口
      host: localhost  #consul的地址
      discovery:  #配置发现与注册
        serviceName: ${spring.application.name}
        prefer-ip-address: true #优先使用服务ip进行注册
```



#### **启动类配置**

```java
@SpringBootApplication
@EnableDiscoveryClient  //启动服务注册于发现
public class main80 {
    public static void main(String[] args) {
        SpringApplication.run(main80.class,args);
    }
}
```



#### 修改url

```java
//直接使用consul注册的服务名
public static final String PAYMENT_URL = "http://cloud-payment-service";
```



#### 配置负载均衡

```java
@Configuration
public class RestTemplateConfig {

    //负载均衡
    @LoadBalanced
    @Bean
    public RestTemplate restTemplate() {
        return new RestTemplate();
    }
}
```



#### 负载均衡说明

**介绍：**`@LoadBalanced` 注解用于Spring Cloud中的负载均衡配置，它通常应用在`RestTemplate`的Bean定义上，以启用客户端负载均衡功能



**作用：**

在微服务架构中，服务之间的通信通常是通过HTTP请求完成的。一个服务可能需要调用另一个服务的API来完成某些业务逻辑。这时，如果被调用的服务有多个实例（为了高可用性和扩展性），就需要一种机制来决定具体调用哪个实例。这就是负载均衡的作用。





### 6.配置中心

#### 添加依赖

```xml
<!-- consul 配置中心 -->
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-starter-consul-config</artifactId>
</dependency>

<!-- consul 发现与注册 -->
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-starter-consul-discovery</artifactId>
    <exclusions>
        <exclusion>
            <groupId>commons-logging</groupId>
            <artifactId>commons-logging</artifactId>
        </exclusion>
    </exclusions>
</dependency>
```



#### 创建bootstarp.yml

```yml
spring:
  application:
    name: cloud-payment-service #配置中心会以cloud-payment-service开头进行查找
    						  #比如配置中心有cloud-payment-service-dev这个配置时
    						  #application.yml中就能进行以下配置
    						  
    						  #profiles:
    						  # active: dev
  cloud:
    consul:
      discovery:
        serviceName: ${spring.application.name}
      port: 8500
      host: localhost
      config:
        enabled: true  #启动配置中心
        profile-separator: '-' #文件配置，修改成-
        format: YAML  #文件格式
```



#### 使用

```java
//使用el表单式进行注入
//这里的wang是data中yml里面的数据
@GetMapping("Test")
public void Test(@Value("${wang.value}") String value){
    System.out.println(value);
}
```





### 7.动态刷新

#### 方式1：添加注解

```java
@SpringBootApplication
@RefreshScope  //动态刷新
@MapperScan("com.it.wang.mapper")
@EnableDiscoveryClient
public class Main8001 {
    public static void main(String[] args) {
        SpringApplication.run(Main8001.class,args);
    }
}
```



#### 方式2：配置yml文件

```yml
config:
  enabled: true
  profile-separator: '-'
  format: YAML
  watch:
    wait-time: 1 #一秒刷新一次
```





# 第四章 LoadBalancer负载均衡

### 1.介绍

LoadBalancer（负载均衡器）是分布式系统中的一个重要组件，用于在多个服务器或服务实例之间分配工作负载，以提高系统的性能和可靠性【客户端】。



### 2.负载均衡策略



**轮询（Round Robin）【默认】**：按顺序将请求依次分配给每个服务器，适用于服务器性能相近的场景。

```java
算法：总请求次数%集群数量=对应分配的服务器
```



**随机（Random）**：随机选择一个服务器来处理每个新请求。







### 3.轮询策略

#### 添加依赖

```xml
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-starter-loadbalancer</artifactId>
</dependency>

<!-- consul config -->
<dependency>
     <groupId>org.springframework.cloud</groupId>
     <artifactId>spring-cloud-starter-consul-config</artifactId>
</dependency>
<!-- consul discovery -->
<dependency>
     <groupId>org.springframework.cloud</groupId>
     <artifactId>spring-cloud-starter-consul-discovery</artifactId>
     <exclusions>
       <exclusion>
           <groupId>commons-logging</groupId>
           <artifactId>commons-logging</artifactId>
       </exclusion>
  	</exclusions>
</dependency>
```



#### RestTemplate的配置

```java
@Configuration
public class RestTemplateConfig {
    /**
    	* 在进行微服务内部调用时
    	* 进行负载均衡
    	*/
    @LoadBalanced
    @Bean
    public RestTemplate restTemplate() {
        return new RestTemplate();
    }
}
```



#### 控制器的配置

```java
@RestController
@RequestMapping("/consumer")
public class OrderController {
    @Autowired
    private RestTemplate restTemplate;

    //这里要确保路径是被consul注册的，这样在进行内部访问的时候才能进行负载均衡
    public static final String PAYMENT_URL = "http://cloud-payment-service";
```



### 4.随机策略

```java
@Configuration
@LoadBalancerClient(value = "cloud-payment-service",configuration = RestTemplateConfig.class)
public class RestTemplateConfig {
    //负载均衡
    @LoadBalanced
    @Bean
    public RestTemplate restTemplate() {
        return new RestTemplate();
    }
     
    @Bean
    ReactorLoadBalancer<ServiceInstance> randomLoadBalancer(Environment environment,
                                                            LoadBalancerClientFactory loadBalancerClientFactory) {
        String name = environment.getProperty(LoadBalancerClientFactory.PROPERTY_NAME);
        return new RandomLoadBalancer(loadBalancerClientFactory
                .getLazyProvider(name, ServiceInstanceListSupplier.class),
                name);
    }
}
```







# 第五章 OpenFeign微服务通信

### 1.介绍

OpenFeign 是一个声明式的 HTTP 客户端，用于简化和标准化微服务之间的通信。它允许开发者通过简单的接口声明和注解配置，与远程 HTTP 服务进行交互，而不需要手动编写 HTTP 请求的相关代码【比如restTemplate】。



### 2.基本用法

#### 添加依赖

```xml
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-starter-openfeign</artifactId>
</dependency>
```



#### 启用 Feign 客户端

在你的 Spring Boot 应用程序的主类上添加 `@EnableFeignClients` 注解

```java
@SpringBootApplication
@EnableFeignClients
public class FeignClientApplication {
    public static void main(String[] args) {
        SpringApplication.run(FeignClientApplication.class, args);
    }
}
```



#### 定义 Feign 接口

使用 `@FeignClient` 注解定义一个接口来表示远程服务

```java
@FeignClient(name = "user-service") //这里必须是注册到consul中的服务名
public interface UserClient {
    @GetMapping("/users/{id}") //这里的路径要和user-service中controller的路径一致
    User getUserById(@PathVariable("id") Long id);
}
```



### 3.高级特性

#### 配置超时

```yml
spring:
  application:
    name: cloud-cunsumer-openfeign-order
  cloud:
    consul:
      port: 8500
      host: localhost
      discovery:
        serviceName: ${spring.application.name}
        prefer-ip-address: true #优先使用服务ip进行注册
    openfeign:
      client:
        config:
         #cloud-payment-service:  单独配置服务  
          default: #这里配置全部服务
           #连接超时时间
           connectTimeout: 3000
           # 读取超时时间
           readTimeout: 3000
```



#### 负载均衡

Feign 与 LoadBalancer集成，可以实现客户端负载均衡。在 `@FeignClient` 注解中指定服务名称，Feign 会自动从注册中心获取服务实例并进行负载均衡。







#### 重试机制

在 Spring Cloud OpenFeign 中，可以通过配置重试机制来增强微服务间通信的可靠性。

```java
@Configuration
public class FeignConfig {
    // 重试机制
    @Bean
    public Retryer myRetryer() {
	   // return new Retryer.Default();
        // 初次间隔 最大间隔 最大请求次数(1+2) = 3
        return new Retryer.Default(100, 1, 3);
    }
}
```



#### 性能优化

HttpClient5替换默认的HttpURLConnection

```xml
 	   <dependency>
            <groupId>io.github.openfeign</groupId>
            <artifactId>feign-hc5</artifactId>
            <version>13.1</version>
        </dependency>
        
        <dependency>
            <groupId>org.apache.httpcomponents.client5</groupId>
            <artifactId>httpclient5</artifactId>
            <version>5.3</version>
        </dependency>
```



```yml
spring:
  application:
    name: cloud-cunsumer-openfeign-order
  cloud:
    consul:
      port: 8500
      host: localhost
      discovery:
        serviceName: ${spring.application.name}
        prefer-ip-address: true
    openfeign:
    	 #这里进行配置HttpClient5
      httpclient:
        hc5:
          enabled: true
      client:
        config:
          default:
           connectTimeout: 3000
           readTimeout: 3000
```





#### 请求回应压缩

```yml
spring:
  application:
    name: cloud-cunsumer-openfeign-order
  cloud:
    consul:
      port: 8500
      host: localhost
      discovery:
        serviceName: ${spring.application.name}
        prefer-ip-address: true 
    openfeign:
    	 #这里配置请求回应压缩
      compression:
        request: #配置请求
          enabled: false
          min-request-size: 2048 #最小触发压缩大小
          mime-types: text/xml,application/xml,application/json #触发压缩的类型
        response: #配置响应
          enabled: false
```







#### 日志开启

```yml
logging:
  level:
    com:
      it:
        wang:
          apis:
            PayFeignApi: debug
```



**-------------------------日志级别-------------------------**

**TRACE**：最细粒度的信息，主要用于开发过程中记录详细的应用行为。

**DEBUG**：比 TRACE 粒度稍粗，通常用于调试信息。

**INFO**：常规的消息，例如启动、关闭、配置等信息。

**WARN**：警告信息，表示有潜在问题，但不会影响应用的正常运行。

**ERROR**：错误信息，表示发生了问题，可能会影响应用的正常运行。

**FATAL**：严重错误，表示发生了严重的问题，应用可能无法继续运行（在某些日志框架中会使用）。

**OFF**：关闭日志输出。







# 第六章 Resilience4j 短路器

### 1.Circuit breaker和Resilience4j 的关系

Circuit Breaker（熔断器）是一种设计模式，用于检测系统的运行状态，并在检测到错误或失败时进行保护，防止错误扩散，提升系统的稳定性和容错性。

Resilience4j 是一个实现了这一设计模式



### 2.熔断器三种状态

**关闭 (Closed)**：所有请求正常通过。如果失败率超过设定的阈值，熔断器将进入打开状态。

**打开 (Open)**：所有请求立即失败，执行备用逻辑，经过一段时间后，熔断器进入半开状态。

**半开 (Half-Open)**：部分请求被允许通过。如果这些请求成功，熔断器将重新进入关闭状态；如果失败，熔断器将重新进入打开状态。



### 3.基于次数配置断路器

```yml
# 基于次数配置
resilience4j:
  circuitbreaker:
    configs:
      default:
        failure-rate-threshold: 50 # 调用失败达到50%后打开断路器
        sliding-window-type: count_based # 滑动窗口类型
        sliding-window-size: 6 # 滑动窗口大小 count_based6个请求 time_base6秒
        minimum-number-of-calls: 6 # 每个滑动窗口的周期，必须6个请求才会进行计算
        automatic-transition-from-open-to-half-open-enabled: true # 开始过度到半开状态
        wait-duration-in-open-state: 5s # 从开启到半开启需要5s
        permitted-number-of-calls-in-half-open-state: 2 #半开状态允许通过的最大请求数
        record-exceptions:
          - java.lang.Exception
    instances: #配置特定服务实例
      cloud-payment-service: #这是一个服务实例的名称
        base-config: default #指定断路器配置，即configs下面的default
        
#说明：这里接受6个请求，失败率达到50%后打开断路器，必须达到6个请求才会进行计算
#     开始过度到半开状态开启，如果开启后5秒内会进入半启动，半开状态中会接受2个请求
#	 如果失败率达到50%会进行开启短路，否则断开所有请求都能进入
```









