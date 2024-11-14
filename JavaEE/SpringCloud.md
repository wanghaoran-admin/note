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







# 第二章 RestTemplate的介绍

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
        heartbeat: #启动服务
          enabled: true
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

<!-- bootstrap -->
<dependency>
     <groupId>org.springframework.cloud</groupId>
     <artifactId>spring-cloud-starter-bootstrap</artifactId>
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



#### 配置中心命名规则

```yml
# config/cloud-payment-service/data
#       /cloud-payment-service-dev/data
#       /cloud-payment-service-prod/data

【这里是命名规则】
```



#### bootstarp.yml配置

```yml
spring:
  application:
    name: cloud-payment-service #配置中心会以cloud-payment-service开头进行查找
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







#### application.yml配置

```yml
spring:
  profiles:
    active: dev 
```











#### 测试

```java
//使用el表单式进行注入
//这里的wang是data中yml里面的数据
@GetMapping("Test")
public void Test(@Value("${wang.value}") String value){
    System.out.println(value);
}
```





### 7.动态刷新

#### 动态刷新介绍

动态刷新功能的核心意思是配置中心的配置会实时更新，应用程序可以在配置发生变化时自动地获取最新的配置而不需要重启。



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





### 8.启动命令

进入consul目录下输入：**consul.exe agent -dev**

持久化操作百度搜







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

```java
@FeignClient("cloud-payment-service")
public interface PayFeignApi {
    @PostMapping("/pay/add")
    Result<Integer> add(@RequestBody PayDTO pay);

    @DeleteMapping("/pay/delete/{id}")
    Result<Integer> delete(@PathVariable("id") Integer id);

    @PutMapping("/pay/update")
    Result<Integer> updatePay(@RequestBody PayDTO payDTO);

    @GetMapping("/pay/getAll")
    Result<List<PayDTO>> getAll();

    @GetMapping("/pay/getById/{id}")
    Result<PayDTO> getById(@PathVariable("id") Integer id);

    @GetMapping("/pay/Test")
    Result<Integer> Test();

    @GetMapping("/pay/circuit/{id}")
    String myCircuit(@PathVariable("id") Integer id);

    @GetMapping("/pay/rateLimit/{id}")
    String myRateLimit(@PathVariable("id") Integer id);
```





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



### 4.fegin接口和服务接口的区别

在微服务架构中，Feign 和服务（Service）是两个不同的概念，分别用于服务调用和服务定义

#### Feign

**定义**：Feign 是一个声明式的 HTTP 客户端，用于简化 Java 应用程序中对 RESTful 服务的调用。Feign 使得 HTTP 请求的创建、发送和响应处理变得更加简洁。



#### 服务

**定义**：在微服务架构中，“服务”通常指的是独立部署、提供业务功能的模块。每个服务都是一个独立的应用程序



# 第六章 Resilience4j熔断器

### 1.Circuit breaker和Resilience4j 的关系

Circuit Breaker（熔断器）是一种设计模式，用于检测系统的运行状态，并在检测到错误或失败时进行保护，防止错误扩散，提升系统的稳定性和容错性。

Resilience4j 是一个实现了这一设计模式



### 2.熔断器（Circuit breaker）

#### 熔断器三种状态

**关闭 (Closed)**：所有请求正常通过。如果失败率超过设定的阈值，熔断器将进入打开状态。

**打开 (Open)**：所有请求立即失败，执行备用逻辑，经过一段时间后，熔断器进入半开状态。

**半开 (Half-Open)**：部分请求被允许通过。如果这些请求成功，熔断器将重新进入关闭状态；如果失败，熔断器将重新进入打开状态。



#### 基于次数配置

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
          - java.lang.Exception #哪种异常视为失败
    instances: #配置特定服务实例
      cloud-payment-service: #这是一个服务实例的名称
        base-config: default #指定断路器配置，即configs下面的default
        
#说明：这里接受6个请求，失败率达到50%后打开断路器，必须达到6个请求才会进行计算
#     开始过度到半开状态开启，如果开启后5秒内会进入半启动，半开状态中会接受2个请求
#	 如果失败率达到50%会进行开启短路，否则断开所有请求都能进入
```



#### 基于慢调用的

```yml
# 基于慢调用的降级
resilience4j:
  timelimiter:
    configs:
      default:
        timeout-duration: 10s # 默认1s 超过1s直接降级 (坑)
  circuitbreaker:
    configs:
      default:
        failure-rate-threshold: 50 # 调用失败达到50%后打开断路器
        slow-call-duration-threshold: 3s # 慢调用时间阈值
        slow-call-rate-threshold: 30 # 慢调用百分比峰值
        sliding-window-type: time_based # 滑动窗口类型
        sliding-window-size: 2 # 滑动窗口大小 ount_based2个请求 time_base2秒
        minimum-number-of-calls: 2 # 每个滑动窗口的周期
        automatic-transition-from-open-to-half-open-enabled: true # 开始过度到半开状态
        wait-duration-in-open-state: 5s # 从开启到半开启需要5s
        permitted-number-of-calls-in-half-open-state: 2 #半开状态允许通过的最大请求数
        record-exceptions:
          - java.lang.Exception
    instances:
      cloud-payment-service:
        base-config: default
        
#说明：这里接受两个请求如果请求到达3s即慢调用阈值
#     慢调用比例到达30%打开断路器
#	 调用失败达到50%后打开断路器
```





#### 降级和熔断的区别

**熔断器**是一种保护系统的机制，当系统检测到某个服务出现故障或响应时间过长时，熔断器会“断开”对该服务的调用，防止故障扩散，保护系统的整体稳定性。

**服务降级**是一种策略，当服务调用失败或响应超时时，提供一个备用的、简化的响应或服务，以保证系统的基本功能和用户体验。

```java
@RestController
@RequestMapping("/feign")
public class OrderCircuitController {

    @Resource
    private PayFeignApi payFeignApi;

    /**
     * name是yml中instances下面的cloud-payment-service
     * fallbackMethod是出现异常执行的方法
     */
    @GetMapping("/pay/circuit/get/{id}")
    @CircuitBreaker(name = "cloud-payment-service", fallbackMethod = "fallback4RateLimit")
    public String getPayById4CircuitBreaker(@PathVariable("id") Integer id) {
        return payFeignApi.myCircuit(id);
    }

    //这里是降级处理
    public String fallback4RateLimit(Integer id, Throwable throwable){
        return "服务器限流, 请稍后重试...";
    }
}
```







### 3.舱壁（Bulk head）

#### 舱壁和熔断器区别

**舱壁（Bulkhead）** 类比于微服务中的**服务隔离**和**边界控制**，确保不同服务之间的独立性，防止问题传播。



**熔断器（Circuit Breaker）** 是微服务中的**熔断器模式**，用于处理服务调用故障，保护系统的稳定性，提供故障隔离和自动恢复功能。



#### 实战

```xml
<!--        隔离仓-->
        <dependency>
            <groupId>io.github.resilience4j</groupId>
            <artifactId>resilience4j-bulkhead</artifactId>
        </dependency>
```



```yml
#舱壁
resilience4j:
  timelimiter:
    configs:
      default:
        timeout-duration: 10s # 默认1s 超过1s直接降级 (坑)
  bulkhead:
    configs:
      default:
        maxConcurrentCalls: 2 #最大并发呼叫数
        maxWaitDuration: 20ms #最大等待持续时间
    instances:
        cloud-payment-service:
          base-config: default
          
```



```java
@GetMapping("/pay/circuit/get1/{id}") 
@Bulkhead(name = "cloud-payment-service",fallbackMethod = "fallback4RateLimit",type =Bulkhead.Type.SEMAPHORE )
public String getPayById41CircuitBreaker(@PathVariable("id") Integer id) {
    return payFeignApi.myCircuit(id);
}

//这里的类型有SEMAPHORE 信号量舱壁）以及 Thread Bulkhead（线程舱壁）
```





#### 信号量舱壁、线程舱壁

**Semaphore Type**（信号量类型）：通过限制同时允许访问的请求数来控制并发，适合控制资源的并发访问量。

**Thread Type**（线程类型）：通过限制线程池中的线程数来控制并发，适合控制线程的使用和任务管理。

【这里**Thread Type**没有介绍】











### 4.限流（Rate Limiting）

#### 介绍

**限流（Rate Limiting）** 是一种重要的机制，用于控制系统的请求流量，防止在同一时间请求流量过大，导致系统崩溃



#### 限流算法

漏桶算法

令牌桶算法

计数器算法

滑动窗口算法

Redis 限流算法



#### 实战

```xml
<!--        限流-->
        <dependency>
            <groupId>io.github.resilience4j</groupId>
            <artifactId>resilience4j-ratelimiter</artifactId>
        </dependency>
```



```java
@GetMapping("/pay/rateLimit/get/{id}")
@RateLimiter(name = "cloud-payment-service", fallbackMethod = "fallback4RateLimit")
public String getPayById4RateLimit(@PathVariable("id") Integer id) {
    return payFeignApi.myRateLimit(id);
}
```



```yml
#限流
resilience4j:
  ratelimiter:
    configs:
      default:
        limit-for-period: 2 # 一次刷新周期内允许最大的请求数
        limit-refresh-period: 1s # 刷新周期
        timeout-duration: 1 # 默认等待时间
    instances:
      cloud-payment-service:
        base-config: default
```



### 5.注意事项

**标注限流**

```java
@RateLimiter(name = "cloud-payment-service", fallbackMethod = "fallback4RateLimit")
```



**标注熔断器**

```java
@CircuitBreaker(name = "cloud-payment-service", fallbackMethod = "fallback4RateLimit")
```



**标注舱壁**

```java
@Bulkhead(name = "cloud-payment-service",fallbackMethod = "fallback4RateLimit",type =Bulkhead.Type.SEMAPHORE )
```



这里用到了3个注解

name是instances:下面的名称比如

```yml
 instances:
      cloud-payment-service:
```



fallbackMethod即失败后的调用哪个方法

```java
@GetMapping("/pay/rateLimit/get/{id}")
@RateLimiter(name = "cloud-payment-service", fallbackMethod = "fallback4RateLimit")
public String getPayById4RateLimit(@PathVariable("id") Integer id) {
    return payFeignApi.myRateLimit(id);
}

public String fallback4RateLimit(Integer id, Throwable throwable){
    return "服务器限流, 请稍后重试...";
}
```

【舱壁的Type在舱壁中有解释】







# 第七章 micrometer链路追踪

### 介绍

链路追踪（Distributed Tracing）是一种用于监控和故障排除分布式系统的技术。它可以帮助开发人员和运维人员跟踪请求在不同微服务之间的流动，查看每个请求经过的路径、时间和各个服务的性能。



### 使用

```xml
<!--micrometer-tracing一系列包  -->
<dependency>
    <groupId>io.micrometer</groupId>
    <artifactId>micrometer-tracing</artifactId>
</dependency>
<dependency>
    <groupId>io.micrometer</groupId>
    <artifactId>micrometer-tracing-bridge-brave</artifactId>
</dependency>
<dependency>
    <groupId>io.micrometer</groupId>
    <artifactId>micrometer-observation</artifactId>
</dependency>
<dependency>
    <groupId>io.github.openfeign</groupId>
    <artifactId>feign-micrometer</artifactId>
</dependency>
<dependency>
    <groupId>io.zipkin.reporter2</groupId>
    <artifactId>zipkin-reporter-brave</artifactId>
</dependency>
```



```yml
# 配置链路
management:
  zipkin: #配置zipkin用了渲染链路
    tracing:
      endpoint: http://localhost:9411/api/v2/spans #固定路径
  tracing:
    sampling:
      probability: 1.0 # 采样率,默认值0.1(就是10%的链路会被记录下来)
```







# 第八章 GateWay网关

### 1.简介

网关（Gateway）在网络和应用程序架构中起到了至关重要的作用。它作为不同网络、服务或协议之间的中介，负责转发、转换和处理请求。



### 2.Gateway核心组件

Route（路由）：满足谓词【断言】后的跳转路径

Predicate（谓词）：路径匹配

Filter（过滤器）	



### 3.入门案例

#### 添加依赖

```xml
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-starter-gateway</artifactId>
</dependency>

<!--    consul依赖-->
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-starter-consul-discovery</artifactId>
</dependency>

<!--    心跳检查依赖-->
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-actuator</artifactId>
</dependency>
```

使用gateway必须使用服务注册，这里使用的是consul





#### 网关配置

```yml
server:
  port: 9527

spring:
  application:
    name: cloud-gateway
  cloud:
    consul:
      discovery:
        serviceName: ${spring.application.name}
      port: 8500
      host: localhost
    gateway:
      routes:
        - id: pay_routh1 #路由id,类似与mysql主键没有固定规则但是唯一,一般是服务名
          uri: http://localhost:8001  #路由地址
          predicates:
            - Path=/feign/gateway/pay/get/**  #路径匹配的进行断言

        - id: pay_routh2 #路由id,类似与mysql主键没有固定规则但是唯一,一般是服务名
          #uri: http://localhost:8001  #路由地址
           uri: lb://cloud-payment-service  #地址一般是服务名能够负载均衡，也就是动态路由
          predicates:
            - Path=/feign/gateway/pay/getInfo/**  #路径匹配的进行断言
```

【这里的】predicates断言满足就会跳转到  uri: http://localhost:8001中去  







#### 修改feignApi

```java
@FeignClient("cloud-gateway") //这里的意思是如果想访问必须先找网关，断言通过后才能进行访问
public interface PayFeignApi {
```

这是使用网关的服务





#### 运行

访问这个路径

```
http://localhost/feign/gateway/pay/getInfo
```



启动网关能够获取结果

没启动会报503（服务器暂时无法处理客户端的请求）







### 4.Predicate（谓词）

#### 配置

```yml
spring:
  application:
    name: cloud-gateway
  cloud:
    consul:
      discovery:
        serviceName: ${spring.application.name}
      port: 8500
      host: localhost
    gateway:
      routes:
        - id: pay_routh1 #路由id,类似与mysql主键没有固定规则但是唯一,一般是服务名
          uri: http://localhost:8001  #路由地址
          predicates:
            - Path=/feign/gateway/pay/get/**  #路径匹配的进行断言
            - Custom=ccc #自定义谓词 
#            - After=2024-08-05T20:31:19.207227200+08:00[Asia/Shanghai] #在指定时间之后路由
#            - Between=2024-08-05T20:31:19.207227200+08:00[Asia/Shanghai],2024-08-05T20:36:19.207227200+08:00[Asia/Shanghai]#在指定时间路由
#            - Cookie=username,zzyy #cookie中携带指定数据
#            - Header=X-Request-Id, \d+ #请求头要有X-Request-Id属性并且为整数的正则表达式
#            - Query=username, \d+ #要有参数username并且为整数才能路由
#            - Host=**.it.com #请求中必须带着主机
#            - Method=GET,POST #配置请求方法
#            - RemoteAddr=192.168.124.1/24 #外部访问我的ip限制，最大不超过32目前是1·24他们是CIDR表示法
```





#### 自定义Predicate

```java
/** 自定义会员等级
 * @Author WongSilver
 * @Date 2024/3/20 14:47
 * @Description
 */
@Component	//这里开头Custom结尾必须是RoutePredicateFactory
public class CustomRoutePredicateFactory extends AbstractRoutePredicateFactory<CustomRoutePredicateFactory.Config> {


    public CustomRoutePredicateFactory() {
        super(CustomRoutePredicateFactory.Config.class);
    }

    //配置紧凑语法
    public List<String> shortcutFieldOrder() {
        return Collections.singletonList("userType");
    }

    //获取参数userType和yml中的进行比较
    @Override
    public Predicate<ServerWebExchange> apply(CustomRoutePredicateFactory.Config config) {
        return serverWebExchange -> {
            String userLevel = serverWebExchange.getRequest().getQueryParams().getFirst("userType");
            if (userLevel == null) {
                return false;
            }
            return userLevel.equalsIgnoreCase(config.getUserType());
        };
    }

    @Getter
    @Setter
    @Validated
    public static class Config {
        @NotNull
        private String userType;
    }

}
```



```yml
 Custom=ccc #自定义谓词
```













### 5.filters（过滤）

#### 配置

```yml
spring:
  application:
    name: cloud-gateway
  cloud:
    consul:
      discovery:
        serviceName: ${spring.application.name}
      port: 8500
      host: localhost
    gateway:
      routes:
        - id: pay_routh2 #路由id,类似与mysql主键没有固定规则但是唯一,一般是服务名
          uri: http://localhost:8001  #路由地址
          predicates:
            - Path=/feign/gateway/pay/getInfo/**  #路径匹配的进行断言
          filters:
            - CustomSingle=wong
            - RedirectTo=302, https://baidu.com  # 将请求重定向到https://baidu.com，使用HTTP状态码302（临时重定向）。
            - SetPath=/pay/gateway/{segment} # 设置请求路径为/pay/gateway/{segment}
            - PrefixPath=/pay # 在请求路径前添加前缀/pay。
            - AddRequestHeader=X-Request-Wong,Wong # 添加一个请求头，名称为X-Request-Wong，值为Wong。
            - RemoveRequestHeader=sec-fetch-site # 移除请求头sec-fetch-site。
            - AddResponseHeader=test, wongsilver #添加一个响应头，名称为test，值为wongsilver。
```





#### 自定义全局filter

```java
@Slf4j
@Component
public class CustomGlobalFilter implements GlobalFilter, Ordered {

    public static final String START_VISIT_TIME = "startVisitTime";

    /**
     * 自定义全局filter
     * @param exchange
     * @param chain
     * @return
     */
    @Override
    public Mono<Void> filter(ServerWebExchange exchange, GatewayFilterChain chain) {
        // 在请求的属性中存储当前时间戳（访问开始时间）
        exchange.getAttributes().put(START_VISIT_TIME, System.currentTimeMillis());

        // 继续处理请求，将其传递给过滤器链中的下一个过滤器
        // 当请求处理完成时，执行Mono.fromRunnable中的任务
        return chain.filter(exchange).then(Mono.fromRunnable(() -> {

            // 从请求的属性中获取开始访问时间
            Long startVisitTime = exchange.getAttribute(START_VISIT_TIME);
            if (startVisitTime != null) {
                // 获取当前时间戳（访问结束时间）
                long endVisitTime = System.currentTimeMillis();
                log.info("=================================================");
                log.info("访问接口主机: {}", exchange.getRequest().getURI().getHost());
                log.info("访问接口端口: {}", exchange.getRequest().getURI().getPort());
                log.info("访问接口URL: {}", exchange.getRequest().getURI().getPath());
                log.info("访问接口参数: {}", exchange.getRequest().getURI().getRawQuery());
                log.info("访问接口耗时: {}ms", (endVisitTime - startVisitTime));
                log.info("=================================================");
            }
        }));
    }

    // 数字越小优先级越高
    @Override
    public int getOrder() {
        return -1;
    }
}
```



#### 单一filter

> ==========百度搜==========

















