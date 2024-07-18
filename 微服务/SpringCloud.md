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

负载均衡（Load Balancing）是指在计算机网络中，将工作负载（如请求、数据流量等）分布到多个服务器或其他计算资源上，以达到更好的资源利用、最大化吞吐量、最小化响应时间和避免单点故障的目的。



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

