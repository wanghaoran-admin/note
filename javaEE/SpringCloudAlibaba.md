# 第一章 介绍

### 1.Spring Cloud Alibaba介绍

Spring Cloud Alibaba 是 Spring Cloud 体系下的一个子项目，专为中国市场打造，提供了对阿里巴巴的开源项目和云服务的支持。



### 2.主要组件

**Nacos**：作为一个动态服务发现、配置管理和服务管理平台

**Sentinel**：一个流量防护组件，主要用于服务的流量控制、熔断降级和系统负载保护。

**RocketMQ**：一种分布式消息中间件，具有高吞吐量、低延迟的特点，适用于大规模的消息系统。

**Seata**：一个分布式事务解决方案，支持高效并且易于使用的分布式事务处理，可以保证跨多个服务的事务一致性。

**Alibaba Cloud OSS**：对象存储服务（OSS）可以用于存储和管理大规模的非结构化数据，如图片、视频、日志文件等。

**Alibaba Cloud SMS**：短消息服务（SMS）提供了高效、可靠的短信发送能力，支持国内外短信发送。



# 第二章 Nacos服务注册与发现

### 1.官网地址

https://github.com/alibaba/nacos/releases/tag/2.3.0

#### 启动

> startup.cmd -m standalone



### 2.服务注册、发现

#### 添加依赖

```xml
<dependency>
    <groupId>com.alibaba.cloud</groupId>
    <artifactId>spring-cloud-starter-alibaba-nacos-discovery</artifactId>
</dependency>
```



#### 配置yml

```yml
server:
  port: 9001
spring:
  application:
    name: nacos-pay-provider
  cloud:
    nacos:
      discovery:
        server-addr: localhost:8848 # nacos地址
```







### 3.负载均衡

```yml
server:
  port: 9001
spring:
  application:
    name: nacos-pay-provider
  cloud:
    nacos:
      discovery:
        server-addr: localhost:8848 # nacos地址
```

【相同的 name: nacos-pay-provider Nacos会进行自动的负载均衡】



### 4.配置中心

#### 添加依赖

```xml
<dependency>
    <groupId>com.alibaba.cloud</groupId>
    <artifactId>spring-cloud-starter-alibaba-nacos-config</artifactId>
</dependency>
<dependency>
    <groupId>com.alibaba.cloud</groupId>
    <artifactId>spring-cloud-starter-alibaba-nacos-discovery</artifactId>
</dependency>
```



#### 配置中心命名规则

`[应用名]-[环境名].[文件扩展名]`



#### 编写bootstrap.yml

```yml
spring:
  application:
    name: nacos-config #服务名称
  cloud:
    nacos:
      discovery:
        server-addr: localhost:8848 #服务发现注册地址
      config:
        server-addr: localhost:8848 #配置中心地址
        file-extension: yaml #用于配置中心后缀【这里一定要和配置中心中的后缀一致】
```

【这里也就是说我配置中心中有一个名为nacos-config-dev.yaml的文件】





#### 编写application.yml

```yml
server:
  port: 3377
spring:
  profiles:
    active: dev
```



#### 测试

```java
@RestController
@RefreshScope // 支持动态刷新功能
public class NacosConfigClientController {

    @Value("${config.info}")
    private String configInfo;

    @GetMapping("/config/info")
    public String getConfigInfo() {
        return configInfo;
    }
}
```





### 5.三元组

#### **命名空间 (Namespace)**

命名空间用于隔离不同环境或项目的配置。你可以为不同的环境创建不同的命名空间。

```yml
config:
  server-addr: localhost:8848
  file-extension: yaml
  namespace: 命名空间id
```



#### **组 (Group)**

组用于对配置进行分类管理。默认的组名为 `DEFAULT_GROUP`，你也可以根据需要自定义组名。

```yml
config:
  server-addr: localhost:8848
  file-extension: yaml
  group: PROD_GROUP
```



#### **数据 ID (Data ID)**

就是根据nacos中**nacos-config-dev.yaml，nacos-config-test.yaml.....**



#### **学习地址**

https://www.bilibili.com/video/BV1gW421P7RD?p=99&spm_id_from=pageDriver&vd_source=7268b1c733746031a0c523593f1e1f31





# 第三章 Sentinel流量组件

### 1.介绍

Sentinel 是阿里巴巴开源的一款面向分布式服务架构的流量防卫组件。它主要用于保障微服务架构中的稳定性和可靠性，通过对流量进行控制、熔断、降级等手段，防止服务在高并发、流量突增等情况下出现崩溃或严重性能问题。



### 2.主要功能

**流量控制（限流）**：根据配置限制每秒请求数或并发数，防止服务因流量过大而崩溃。

**熔断降级**：当服务出现高延迟或错误率过高时，自动停止对该服务的请求，以保护系统稳定。

**系统负载保护**：监控系统整体负载情况，当负载过高时自动减少流量，避免系统崩溃。

**热点参数限流**：对特定请求参数的高频请求进行限流，防止单一资源的过度消耗。

**实时监控和报警**：提供实时监控服务运行情况，并在异常时发送报警通知。



### 3.纳入Sentinel监控

```yml
server:
  port: 8401
spring:
  application:
    name: cloud-alibaba-sentinel-service
  cloud:
    nacos:
      discovery:
        server-addr: localhost:8848
    sentinel:
      transport: 
        dashboard: localhost:8080 #Sentinel本地端口
        port: 8719 # 通信的本地端口

```





### 4.流控模式

【在Sentinel左边菜单栏的流控规则中进行配置】



#### 流控模式

##### 直接

**基本的限流**方式，它直接基于流量的数量来进行限流。

在这种模式下，当流量超过设定的阈值时，系统会立即对超出的请求进行限制。





##### 关联

**关联模式**通过关联其他资源的流量来决定当前资源的流控策略。

即当关联的资源达到阈值时，当前资源也会触发限流。





##### 链路

**链路模式**是一种更复杂的限流方式，它基于调用链路来进行限流。只有当指定的链路（即从调用源头到目标资源的一系列调用路径）上的流量超过阈值时，才会触发限流。这种模式适用于服务调用路径比较复杂的分布式系统中，可以精准控制特定调用链上的流量。

【https://www.bilibili.com/video/BV1gW421P7RD?p=106】







#### 流控效果

##### 快速失效

**快速失效**字面意思就是快速失效



##### Warm Up（预热）

**系统启动**当系统刚启动时，可以使用预热机制逐步增加流量，以确保系统稳定。

【预热时长10意思就是在10秒内系统还处于冷启动有的请求会丢弃】



##### 排队等待

**排队等待**指的是将请求或任务放入队列中，以便系统能够逐步处理这些请求，防止系统因过载而崩溃。



**阈值的作用**：你设定的 QPS 阈值为 50，意味着每台机器每秒最多允许 50 个请求通过。如果在某个时间段内，请求量超过了这个阈值，那么超过的请求不会被立即拒绝，而是进入一个排队等待的队列。

**超时时间**（这里是 1000 毫秒）表示请求在队列中最多可以等待的时间。如果请求在队列中等待的时间超过了这个超时时间，它将被丢弃或返回错误。











# 第四章 Sentinel熔断组件

### 1.熔断总配置

#### **资源名**

资源名 `/testF` 是要应用熔断规则的资源名称，通常是某个服务的接口路径。



#### **熔断策略**

- **慢调用比例**：基于请求的响应时间来决定是否触发熔断。当响应时间超过设定的最大 RT（Response Time，响应时间）时，如果慢调用的比例超过了设定的阈值（比例阈值），就会触发熔断。

- **异常比例**：基于请求的异常比例（失败率）来决定是否触发熔断。如果异常比例超过设定的阈值，就会触发熔断。

- **异常数**：基于请求的异常次数来决定是否触发熔断。如果异常次数达到设定的阈值，就会触发熔断。

	

#### **最大 RT**

设定的最大响应时间为 200 毫秒。如果某个请求的响应时间超过 200 毫秒，就会被视为一次“慢调用”。



#### **比例阈值**

比例阈值为 0.1（即 10%）。在“慢调用比例”策略下，如果慢调用的比例超过 10%，就会触发熔断。



#### **熔断时长**

熔断时长设定为 5 秒，表示当触发熔断后，该资源将被熔断 5 秒。在熔断期间，所有对该资源的请求将被拒绝。



#### **最小请求数**

最小请求数为 5，表示只有在统计周期内的请求数达到 5 次后，才会根据熔断策略进行熔断判断。如果请求数不足 5 次，熔断规则不会生效。



#### **统计时长**

统计时长为 1000 毫秒（1 秒），表示熔断策略将在这个时间段内进行统计和评估。





# 第五章 @SentinelResource注解

### 1.默认不使用

```java
@GetMapping("/rateLimitByUrl")
public String rateLimitByUrl() {
    return "限流测试未使用注解";
}
```

【如果没有使用注解就要和Sentinel中的**新增流控规则**的**资源名**相同，比如路径名叫/rateLimitByUrl，资源名也要一样】



### 2.开启使用blockHandler

```java
@GetMapping("/rateLimitByResource")
@SentinelResource(value = "ByResource", blockHandler = "byResourceHandler")
public String rateLimitByResource() {
    return "限流测试使用注解, 返回指定的字符串";
}

public String byResourceHandler(BlockException blockException) {
    return "服务不可用, 这是自定义返回的字符串";
}
```

【@SentinelResource中value就是**新增流控规则**的**资源名**，必须Sentinel中要有这个流控，如果**限流**就会执行byResourceHandler方法】





### 3.开启使用blockHandler

```java
@GetMapping("ce1/{shu}")
@SentinelResource(value = "ce",blockHandler = "byResourceHandler",fallback = "byFallback")
public String ce1(@PathVariable("shu") String shu){
    if (Integer.parseInt(shu)==0){
        throw new  RuntimeException();
    }
    System.out.println(shu);
    return shu;
}

public String byFallback(@PathVariable("shu") String shu, Throwable throwable) {
    return "逻辑异常, 这是自定义返回的字符串";
}


public String byResourceHandler(@PathVariable("shu") String shu,BlockException blockException) {
    return "限流了..........";
}
```

【blockHandler】：用来处理限流后的操作

【byFallback】：用来处理异常后的操作

【注意】：blockHandler、byFallback中的**参数必须要和原始方法相匹配**，blockHandler的BlockException blockException参数也是必须的



# 第六章 Sentinel热点规则



### 资源名

**testHotKey**: 这是 Sentinel 识别和应用流量控制规则的标识。你需要确保在代码中的 `@SentinelResource` 注解的 `value` 属性和这里的资源名匹配。



### **限流模式**

**QPS 模式**: 表示以每秒请求数（Queries Per Second, QPS）作为限流的标准。这是常用的限流模式，适用于高流量的场景。



### **参数索引**

 指定了热点参数在请求中的索引位置。Sentinel 会基于这个位置的参数值进行限流。例如，如果设置为0，Sentinel 会将第一个参数作为热点参数进行控制。



### **单机阈值** 

设置在单台机器上的流量控制阈值。例如，如果设置为1，则当单机的请求超过这个阈值时，Sentinel 会触发流量控制。



### **统计窗口时长** 

**1 秒**: 指定了用于计算流量控制的统计窗口的时长。Sentinel 会在这个时间窗口内收集请求数据，并应用流量控制规则。1秒的窗口时长意味着流量控制规则会每秒钟更新一次。



### **是否集群** 

**是否集群**: 如果选择了集群模式，那么流量控制规则会在集群中所有节点上生效。如果不选择集群模式，那么流量控制只会在当前单机上生效。



### **参数例外项** 

**参数例外项**: 用于指定哪些特定的参数值应当被排除在流量控制之外。例如，如果你想允许某些参数值超出限流阈值而不受限制，可以在这里配置这些例外项。



### **参数类型** 

**java.lang.String**: 指定了热点参数的类型。在这个例子中，参数类型为 `String`，意味着 Sentinel 会将参数视为字符串类型来进行流量控制。



### **参数值** 

 指定了例外项的参数值。如果设置了例外项参数值 `w`，那么当请求参数值为 `w` 时，该请求将不会受到流量控制的影响。



### **限流阈值** 

**200**: 设置了流量控制的阈值。例如，如果设置为200，则表示每秒钟最多允许200个请求，如果超过这个阈值，则触发限流策略。







# 第七章 Sentinel整合OpenFeign、gateway

### Sentinel整合OpenFeign

#### 依赖

```xml
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-starter-openfeign</artifactId>
</dependency>
<dependency>
    <groupId>com.alibaba.cloud</groupId>
    <artifactId>spring-cloud-starter-alibaba-sentinel</artifactId>
</dependency>
```



#### 开启整合

```yml
#开启对sentinel的整合
feign:
  sentinel:
    enabled: true
```

【在`consumer`（消费者）模块的yml中添加】





#### feign接口编写

```java
@FeignClient(value = "nacos-pay-provider"
        , fallback = PayFeignSentinelFallback.class 
)  //fallback降级类
public interface PayFeignSentinelApi {

    @GetMapping("/pay/nacos/get/{orderNo}")
    Result<PayDTO> getPayByOrderNo(@PathVariable("orderNo") String orderNo);

}
```



#### 降级处理

```java
@Component
public class PayFeignSentinelFallback implements PayFeignSentinelApi {
    @Override
    public Result<PayDTO> getPayByOrderNo(String orderNo) {
        return Result.error(ReturnCodeEnum.RC500.getCode(), "对方服务不可用！！");
    }
}
```



### Sentinel整合gateway

#### 依赖

```xml
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-starter-gateway</artifactId>
</dependency>
<dependency>
    <groupId>com.alibaba.csp</groupId>
    <artifactId>sentinel-transport-simple-http</artifactId>
    <version>1.8.6</version>
</dependency>
<dependency>
    <groupId>com.alibaba.csp</groupId>
    <artifactId>sentinel-spring-cloud-gateway-adapter</artifactId>
    <version>1.8.6</version>
</dependency>
<dependency>
    <groupId>com.alibaba.cloud</groupId>
    <artifactId>spring-cloud-starter-alibaba-nacos-discovery</artifactId>
</dependency>
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-starter-loadbalancer</artifactId>
</dependency>
<dependency>
    <groupId>javax.annotation</groupId>
    <artifactId>javax.annotation-api</artifactId>
    <version>1.3.2</version>
    <scope>compile</scope>
</dependency>
```





#### yml编写

```yml
server:
  port: 9528

spring:
  application:
    name: cloud-alibaba-sentinel-gateway
  cloud:
    nacos:
      discovery:
        server-addr: localhost:8848 # nacos地址
    gateway:
      routes:
        - id: pay_routh1
          uri: http://localhost:9001 #使用url必须添加http://  还可以使用lb://nacos-pay-provider服务名的方式
          predicates:
            - Path=/pay/nacos/get/**  #谓词的配置
```





#### 创建网关配置类

```java
@Configuration
public class GatewayConfiguration {
    private final List<ViewResolver> viewResolvers;
    private final ServerCodecConfigurer serverCodecConfigurer;

    public GatewayConfiguration(List<ViewResolver> viewResolvers, ServerCodecConfigurer serverCodecConfigurer) {
        this.viewResolvers = viewResolvers;
        this.serverCodecConfigurer = serverCodecConfigurer;
    }

    @Bean
    @Order(Ordered.HIGHEST_PRECEDENCE)
    public SentinelGatewayBlockExceptionHandler sentinelGatewayBlockExceptionHandler() {
        return new SentinelGatewayBlockExceptionHandler(viewResolvers, serverCodecConfigurer);
    }

    @Bean
    @Order(-1)
    public GlobalFilter sentinelGatewayFilter() {
        return new SentinelGatewayFilter();
    }

    @PostConstruct
    public void doInit() {
        initBlockHandler();
    }

    private void initBlockHandler() {
        Set<GatewayFlowRule> rules = new HashSet<>();
        rules.add(
                new GatewayFlowRule("pay_routh1") //这里是路由的id是在yml中
                        .setCount(1) //设置最大访问
                        .setIntervalSec(1) //设置间隔秒
        );
        GatewayRuleManager.loadRules(rules);  //加载

        BlockRequestHandler handler = (serverWebExchange, throwable) -> {
            HashMap<String, String> map = new HashMap<>();

            map.put("ErrorCode", HttpStatus.TOO_MANY_REQUESTS.getReasonPhrase());
            map.put("ErrorMessage", "请求过于频繁, 触发了sentinel限流 ... ");

            return ServerResponse.status(HttpStatus.TOO_MANY_REQUESTS)
                    .contentType(MediaType.APPLICATION_JSON)
                    .body(BodyInserters.fromValue(map));
        };

        GatewayCallbackManager.setBlockHandler(handler);
    }
}
```



#### 测试

发送请求到网关，网关根据

```java
  new GatewayFlowRule("pay_routh1") //这里是路由的id是在yml中
                        .setCount(1) //设置最大访问
                        .setIntervalSec(1) //设置间隔秒
```

来决定是否放行











# 第八章 Seata分布式事务

### 1.介绍

Seata是一个开源的分布式事务解决方案，主要用于解决微服务架构中跨服务的数据一致性问题。Seata 提供了高效且易用的分布式事务处理能力，确保在分布式系统中进行的数据操作能够保持一致性。





### 2.组成部分

#### TC

事务协调器，管理全局事务的生命周期，负责全局事务的开始、提交、回滚等操作。

#### TM

事务管理器，负责定义全局事务的范围，触发全局事务的开始和提交/回滚

#### RM

资源管理器，负责管理分支事务，参与具体的业务操作，并与 TC 进行通讯来实现分支事务的注册和状态汇报。



**【其中，TC 为单独部署的 Server 服务端，TM 和 RM 为嵌入到应用中的 Client 客户端。】**





### 3.事务模式

#### **AT 模式**

- AT 模式是一种自动化的分布式事务模式，能够自动管理本地数据库事务。Seata 在两阶段提交（2PC）协议的基础上，增强了性能和可用性：
  - **第一阶段**：在业务执行过程中，RM 会生成一份数据的“镜像”用于后续的回滚，同时提交本地事务并释放数据库锁。
  - **第二阶段**：TC 根据事务的最终状态（提交或回滚）来决定是否需要回滚前一阶段的操作。

#### **TCC 模式**

- TCC（Try-Confirm-Cancel）模式，要求业务服务提供 Try、Confirm 和 Cancel 三个接口，分别对应预操作、确认操作和取消操作。

#### **SAGA 模式**

- SAGA 模式适合长事务处理，将全局事务拆分为一系列的本地事务，每个本地事务具有对应的补偿机制（回滚操作）。

#### **XA 模式**

- XA 模式遵循标准的 2PC 协议，由数据库本身支持 XA 事务管理，适用于需要严格强一致性的场景。





### 4.项目实战

#### 添加依赖

```xml
<dependencies>
    <dependency>
        <groupId>com.alibaba.cloud</groupId>
        <artifactId>spring-cloud-starter-alibaba-nacos-discovery</artifactId>
    </dependency>
    <dependency>
        <groupId>com.alibaba.cloud</groupId>
        <artifactId>spring-cloud-starter-alibaba-seata</artifactId>
    </dependency>
    <dependency>
        <groupId>org.springframework.cloud</groupId>
        <artifactId>spring-cloud-starter-openfeign</artifactId>
    </dependency>
    <dependency>
        <groupId>org.springframework.cloud</groupId>
        <artifactId>spring-cloud-starter-loadbalancer</artifactId>
    </dependency>
    <!-- 引入自定义api通用库-->
    <dependency>
        <groupId>org.example</groupId>
        <artifactId>cloud-commons</artifactId>
        <version>1.0-SNAPSHOT</version>
    </dependency>


    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-web</artifactId>
    </dependency>
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-actuator</artifactId>
    </dependency>
    <dependency>
        <groupId>com.alibaba</groupId>
        <artifactId>druid-spring-boot-starter</artifactId>
    </dependency>
    <!-- http://localhost:8001/swagger-ui/index.html -->
    <dependency>
        <groupId>org.springdoc</groupId>
        <artifactId>springdoc-openapi-starter-webmvc-ui</artifactId>
    </dependency>
    <dependency>
        <groupId>org.mybatis.spring.boot</groupId>
        <artifactId>mybatis-spring-boot-starter</artifactId>
    </dependency>
    <dependency>
        <groupId>mysql</groupId>
        <artifactId>mysql-connector-java</artifactId>
    </dependency>
    <dependency>
        <groupId>javax.persistence</groupId>
        <artifactId>persistence-api</artifactId>
    </dependency>
    <dependency>
        <groupId>tk.mybatis</groupId>
        <artifactId>mapper</artifactId>
    </dependency>
    <dependency>
        <groupId>cn.hutool</groupId>
        <artifactId>hutool-all</artifactId>
    </dependency>
    <dependency>
        <groupId>com.alibaba.fastjson2</groupId>
        <artifactId>fastjson2</artifactId>
    </dependency>
    <dependency>
        <groupId>org.projectlombok</groupId>
        <artifactId>lombok</artifactId>
        <scope>provided</scope>
        <version>1.18.30</version>
    </dependency>
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-test</artifactId>
        <scope>test</scope>
    </dependency>


    <dependency>
        <groupId>com.baomidou</groupId>
        <artifactId>mybatis-plus-boot-starter</artifactId>
        <version>3.5.4.1</version>
    </dependency>
    <dependency>
        <groupId>org.mybatis</groupId>
        <artifactId>mybatis-spring</artifactId>
        <version>3.0.3</version>
    </dependency>
</dependencies>
```



#### 编写数据库

##### seata 数据库

```sql
/*
 Navicat Premium Data Transfer

 Source Server         : mysql
 Source Server Type    : MySQL
 Source Server Version : 80028 (8.0.28)
 Source Host           : localhost:3306
 Source Schema         : seata

 Target Server Type    : MySQL
 Target Server Version : 80028 (8.0.28)
 File Encoding         : 65001

 Date: 18/08/2024 22:32:47
*/

SET NAMES utf8mb4;
SET FOREIGN_KEY_CHECKS = 0;

-- ----------------------------
-- Table structure for branch_table
-- ----------------------------
DROP TABLE IF EXISTS `branch_table`;
CREATE TABLE `branch_table`  (
  `branch_id` bigint NOT NULL,
  `xid` varchar(128) CHARACTER SET utf8mb4 COLLATE utf8mb4_0900_ai_ci NOT NULL,
  `transaction_id` bigint NULL DEFAULT NULL,
  `resource_group_id` varchar(32) CHARACTER SET utf8mb4 COLLATE utf8mb4_0900_ai_ci NULL DEFAULT NULL,
  `resource_id` varchar(256) CHARACTER SET utf8mb4 COLLATE utf8mb4_0900_ai_ci NULL DEFAULT NULL,
  `branch_type` varchar(8) CHARACTER SET utf8mb4 COLLATE utf8mb4_0900_ai_ci NULL DEFAULT NULL,
  `status` tinyint NULL DEFAULT NULL,
  `client_id` varchar(64) CHARACTER SET utf8mb4 COLLATE utf8mb4_0900_ai_ci NULL DEFAULT NULL,
  `application_data` varchar(2000) CHARACTER SET utf8mb4 COLLATE utf8mb4_0900_ai_ci NULL DEFAULT NULL,
  `gmt_create` datetime(6) NULL DEFAULT NULL,
  `gmt_modified` datetime(6) NULL DEFAULT NULL,
  PRIMARY KEY (`branch_id`) USING BTREE,
  INDEX `idx_xid`(`xid` ASC) USING BTREE
) ENGINE = InnoDB CHARACTER SET = utf8mb4 COLLATE = utf8mb4_0900_ai_ci ROW_FORMAT = Dynamic;

-- ----------------------------
-- Records of branch_table
-- ----------------------------

-- ----------------------------
-- Table structure for distributed_lock
-- ----------------------------
DROP TABLE IF EXISTS `distributed_lock`;
CREATE TABLE `distributed_lock`  (
  `lock_key` char(20) CHARACTER SET utf8mb4 COLLATE utf8mb4_0900_ai_ci NOT NULL,
  `lock_value` varchar(20) CHARACTER SET utf8mb4 COLLATE utf8mb4_0900_ai_ci NOT NULL,
  `expire` bigint NULL DEFAULT NULL,
  PRIMARY KEY (`lock_key`) USING BTREE
) ENGINE = InnoDB CHARACTER SET = utf8mb4 COLLATE = utf8mb4_0900_ai_ci ROW_FORMAT = Dynamic;

-- ----------------------------
-- Records of distributed_lock
-- ----------------------------
INSERT INTO `distributed_lock` VALUES ('AsyncCommitting', ' ', 0);
INSERT INTO `distributed_lock` VALUES ('RetryCommitting', ' ', 0);
INSERT INTO `distributed_lock` VALUES ('RetryRollbacking', ' ', 0);
INSERT INTO `distributed_lock` VALUES ('TxTimeoutCheck', ' ', 0);

-- ----------------------------
-- Table structure for global_table
-- ----------------------------
DROP TABLE IF EXISTS `global_table`;
CREATE TABLE `global_table`  (
  `xid` varchar(128) CHARACTER SET utf8mb4 COLLATE utf8mb4_0900_ai_ci NOT NULL,
  `transaction_id` bigint NULL DEFAULT NULL,
  `status` tinyint NOT NULL,
  `application_id` varchar(32) CHARACTER SET utf8mb4 COLLATE utf8mb4_0900_ai_ci NULL DEFAULT NULL,
  `transaction_service_group` varchar(32) CHARACTER SET utf8mb4 COLLATE utf8mb4_0900_ai_ci NULL DEFAULT NULL,
  `transaction_name` varchar(128) CHARACTER SET utf8mb4 COLLATE utf8mb4_0900_ai_ci NULL DEFAULT NULL,
  `timeout` int NULL DEFAULT NULL,
  `begin_time` bigint NULL DEFAULT NULL,
  `application_data` varchar(2000) CHARACTER SET utf8mb4 COLLATE utf8mb4_0900_ai_ci NULL DEFAULT NULL,
  `gmt_create` datetime NULL DEFAULT NULL,
  `gmt_modified` datetime NULL DEFAULT NULL,
  PRIMARY KEY (`xid`) USING BTREE,
  INDEX `idx_status_gmt_modified`(`status` ASC, `gmt_modified` ASC) USING BTREE,
  INDEX `idx_transaction_id`(`transaction_id` ASC) USING BTREE
) ENGINE = InnoDB CHARACTER SET = utf8mb4 COLLATE = utf8mb4_0900_ai_ci ROW_FORMAT = Dynamic;

-- ----------------------------
-- Records of global_table
-- ----------------------------

-- ----------------------------
-- Table structure for lock_table
-- ----------------------------
DROP TABLE IF EXISTS `lock_table`;
CREATE TABLE `lock_table`  (
  `row_key` varchar(128) CHARACTER SET utf8mb4 COLLATE utf8mb4_0900_ai_ci NOT NULL,
  `xid` varchar(128) CHARACTER SET utf8mb4 COLLATE utf8mb4_0900_ai_ci NULL DEFAULT NULL,
  `transaction_id` bigint NULL DEFAULT NULL,
  `branch_id` bigint NOT NULL,
  `resource_id` varchar(256) CHARACTER SET utf8mb4 COLLATE utf8mb4_0900_ai_ci NULL DEFAULT NULL,
  `table_name` varchar(32) CHARACTER SET utf8mb4 COLLATE utf8mb4_0900_ai_ci NULL DEFAULT NULL,
  `pk` varchar(36) CHARACTER SET utf8mb4 COLLATE utf8mb4_0900_ai_ci NULL DEFAULT NULL,
  `status` tinyint NOT NULL DEFAULT 0 COMMENT '0:locked ,1:rollbacking',
  `gmt_create` datetime NULL DEFAULT NULL,
  `gmt_modified` datetime NULL DEFAULT NULL,
  PRIMARY KEY (`row_key`) USING BTREE,
  INDEX `idx_status`(`status` ASC) USING BTREE,
  INDEX `idx_branch_id`(`branch_id` ASC) USING BTREE,
  INDEX `idx_xid`(`xid` ASC) USING BTREE
) ENGINE = InnoDB CHARACTER SET = utf8mb4 COLLATE = utf8mb4_0900_ai_ci ROW_FORMAT = Dynamic;

-- ----------------------------
-- Records of lock_table
-- ----------------------------

SET FOREIGN_KEY_CHECKS = 1;

```





##### seata_account 账户数据库

```sql
/*
 Navicat Premium Data Transfer

 Source Server         : mysql
 Source Server Type    : MySQL
 Source Server Version : 80028 (8.0.28)
 Source Host           : localhost:3306
 Source Schema         : seata_account

 Target Server Type    : MySQL
 Target Server Version : 80028 (8.0.28)
 File Encoding         : 65001

 Date: 18/08/2024 22:33:09
*/

SET NAMES utf8mb4;
SET FOREIGN_KEY_CHECKS = 0;

-- ----------------------------
-- Table structure for t_account
-- ----------------------------
DROP TABLE IF EXISTS `t_account`;
CREATE TABLE `t_account`  (
  `id` bigint NOT NULL AUTO_INCREMENT COMMENT 'id',
  `user_id` bigint NULL DEFAULT NULL COMMENT '用户id',
  `total` decimal(10, 0) NULL DEFAULT NULL COMMENT '总额度',
  `used` decimal(10, 0) NULL DEFAULT NULL COMMENT '已用账户余额',
  `residue` decimal(10, 0) NULL DEFAULT NULL COMMENT '剩余可用额度',
  PRIMARY KEY (`id`) USING BTREE
) ENGINE = InnoDB AUTO_INCREMENT = 2 CHARACTER SET = utf8mb4 COLLATE = utf8mb4_0900_ai_ci ROW_FORMAT = Dynamic;

-- ----------------------------
-- Records of t_account
-- ----------------------------
INSERT INTO `t_account` VALUES (1, 1, 1000, 0, 1000);

-- ----------------------------
-- Table structure for undo_log
-- ----------------------------
DROP TABLE IF EXISTS `undo_log`;
CREATE TABLE `undo_log`  (
  `branch_id` bigint NOT NULL COMMENT 'branch transaction id',
  `xid` varchar(128) CHARACTER SET utf8mb4 COLLATE utf8mb4_0900_ai_ci NOT NULL COMMENT 'global transaction id',
  `context` varchar(128) CHARACTER SET utf8mb4 COLLATE utf8mb4_0900_ai_ci NOT NULL COMMENT 'undo_log context,such as serialization',
  `rollback_info` longblob NOT NULL COMMENT 'rollback info',
  `log_status` int NOT NULL COMMENT '0:normal status,1:defense status',
  `log_created` datetime(6) NOT NULL COMMENT 'create datetime',
  `log_modified` datetime(6) NOT NULL COMMENT 'modify datetime',
  UNIQUE INDEX `ux_undo_log`(`xid` ASC, `branch_id` ASC) USING BTREE,
  INDEX `ix_log_created`(`log_created` ASC) USING BTREE
) ENGINE = InnoDB CHARACTER SET = utf8mb4 COLLATE = utf8mb4_0900_ai_ci COMMENT = 'AT transaction mode undo table' ROW_FORMAT = Dynamic;

-- ----------------------------
-- Records of undo_log
-- ----------------------------

SET FOREIGN_KEY_CHECKS = 1;

```



##### seata_order 订单数据库

```sql
/*
 Navicat Premium Data Transfer

 Source Server         : mysql
 Source Server Type    : MySQL
 Source Server Version : 80028 (8.0.28)
 Source Host           : localhost:3306
 Source Schema         : seata_order

 Target Server Type    : MySQL
 Target Server Version : 80028 (8.0.28)
 File Encoding         : 65001

 Date: 18/08/2024 22:33:14
*/

SET NAMES utf8mb4;
SET FOREIGN_KEY_CHECKS = 0;

-- ----------------------------
-- Table structure for t_order
-- ----------------------------
DROP TABLE IF EXISTS `t_order`;
CREATE TABLE `t_order`  (
  `id` bigint NOT NULL AUTO_INCREMENT,
  `user_id` bigint NULL DEFAULT NULL,
  `product_id` bigint NULL DEFAULT NULL,
  `count` int NULL DEFAULT NULL,
  `moeny` int NULL DEFAULT NULL,
  `status` int NULL DEFAULT NULL COMMENT '订单状态：0创建中：1已完结',
  PRIMARY KEY (`id`) USING BTREE
) ENGINE = InnoDB AUTO_INCREMENT = 12 CHARACTER SET = utf8mb4 COLLATE = utf8mb4_0900_ai_ci ROW_FORMAT = Dynamic;

-- ----------------------------
-- Records of t_order
-- ----------------------------

-- ----------------------------
-- Table structure for undo_log
-- ----------------------------
DROP TABLE IF EXISTS `undo_log`;
CREATE TABLE `undo_log`  (
  `branch_id` bigint NOT NULL COMMENT 'branch transaction id',
  `xid` varchar(128) CHARACTER SET utf8mb4 COLLATE utf8mb4_0900_ai_ci NOT NULL COMMENT 'global transaction id',
  `context` varchar(128) CHARACTER SET utf8mb4 COLLATE utf8mb4_0900_ai_ci NOT NULL COMMENT 'undo_log context,such as serialization',
  `rollback_info` longblob NOT NULL COMMENT 'rollback info',
  `log_status` int NOT NULL COMMENT '0:normal status,1:defense status',
  `log_created` datetime(6) NOT NULL COMMENT 'create datetime',
  `log_modified` datetime(6) NOT NULL COMMENT 'modify datetime',
  UNIQUE INDEX `ux_undo_log`(`xid` ASC, `branch_id` ASC) USING BTREE,
  INDEX `ix_log_created`(`log_created` ASC) USING BTREE
) ENGINE = InnoDB CHARACTER SET = utf8mb4 COLLATE = utf8mb4_0900_ai_ci COMMENT = 'AT transaction mode undo table' ROW_FORMAT = Dynamic;

-- ----------------------------
-- Records of undo_log
-- ----------------------------

SET FOREIGN_KEY_CHECKS = 1;

```







##### seata_storage 库存数据库

```sql
/*
 Navicat Premium Data Transfer

 Source Server         : mysql
 Source Server Type    : MySQL
 Source Server Version : 80028 (8.0.28)
 Source Host           : localhost:3306
 Source Schema         : seata_storage

 Target Server Type    : MySQL
 Target Server Version : 80028 (8.0.28)
 File Encoding         : 65001

 Date: 18/08/2024 22:33:20
*/

SET NAMES utf8mb4;
SET FOREIGN_KEY_CHECKS = 0;

-- ----------------------------
-- Table structure for t_storage
-- ----------------------------
DROP TABLE IF EXISTS `t_storage`;
CREATE TABLE `t_storage`  (
  `id` bigint NOT NULL AUTO_INCREMENT COMMENT 'id',
  `product_id` bigint NULL DEFAULT NULL COMMENT '产品id',
  `total` decimal(10, 0) NULL DEFAULT NULL COMMENT '总库存',
  `used` decimal(10, 0) NULL DEFAULT NULL COMMENT '已用库存',
  `residue` decimal(10, 0) NULL DEFAULT NULL COMMENT '剩余库存',
  PRIMARY KEY (`id`) USING BTREE
) ENGINE = InnoDB AUTO_INCREMENT = 2 CHARACTER SET = utf8mb4 COLLATE = utf8mb4_0900_ai_ci ROW_FORMAT = Dynamic;

-- ----------------------------
-- Records of t_storage
-- ----------------------------
INSERT INTO `t_storage` VALUES (1, 1, 100, 0, 100);

-- ----------------------------
-- Table structure for undo_log
-- ----------------------------
DROP TABLE IF EXISTS `undo_log`;
CREATE TABLE `undo_log`  (
  `branch_id` bigint NOT NULL COMMENT 'branch transaction id',
  `xid` varchar(128) CHARACTER SET utf8mb4 COLLATE utf8mb4_0900_ai_ci NOT NULL COMMENT 'global transaction id',
  `context` varchar(128) CHARACTER SET utf8mb4 COLLATE utf8mb4_0900_ai_ci NOT NULL COMMENT 'undo_log context,such as serialization',
  `rollback_info` longblob NOT NULL COMMENT 'rollback info',
  `log_status` int NOT NULL COMMENT '0:normal status,1:defense status',
  `log_created` datetime(6) NOT NULL COMMENT 'create datetime',
  `log_modified` datetime(6) NOT NULL COMMENT 'modify datetime',
  UNIQUE INDEX `ux_undo_log`(`xid` ASC, `branch_id` ASC) USING BTREE,
  INDEX `ix_log_created`(`log_created` ASC) USING BTREE
) ENGINE = InnoDB CHARACTER SET = utf8mb4 COLLATE = utf8mb4_0900_ai_ci COMMENT = 'AT transaction mode undo table' ROW_FORMAT = Dynamic;

-- ----------------------------
-- Records of undo_log
-- ----------------------------

SET FOREIGN_KEY_CHECKS = 1;

```





#### 修改seata/conf的application.yml

```yml
#  Copyright 1999-2019 Seata.io Group.
#
#  Licensed under the Apache License, Version 2.0 (the "License");
#  you may not use this file except in compliance with the License.
#  You may obtain a copy of the License at
#
#  http://www.apache.org/licenses/LICENSE-2.0
#
#  Unless required by applicable law or agreed to in writing, software
#  distributed under the License is distributed on an "AS IS" BASIS,
#  WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
#  See the License for the specific language governing permissions and
#  limitations under the License.

server:
  port: 7091

spring:
  application:
    name: seata-server

logging:
  config: classpath:logback-spring.xml
  file:
    path: ${log.home:${user.home}/logs/seata}
  extend:
    logstash-appender:
      destination: 127.0.0.1:4560
    kafka-appender:
      bootstrap-servers: 127.0.0.1:9092
      topic: logback_to_logstash

console:
  user:
    username: seata
    password: seata
seata:
  config:
    # support: nacos, consul, apollo, zk, etcd3
    type: nacos
    nacos:
      server-addr: 127.0.0.1:8848
      namespace: public
      group: DEFAULT_GROUP
      username: nacos
      password: nacos
  registry:
    # support: nacos, eureka, redis, zk, consul, etcd3, sofa
    type: nacos
    nacos:
      application: seata-server
      server-addr: 127.0.0.1:8848
      group: DEFAULT_GROUP
      namespace: public
      cluster: default
      username: nacos
      password: nacos
  store:
    # support: file 、 db 、 redis 、 raft
    mode: db
    db:
      datasource: druid
      db-type: mysql
      driver-class-name: com.mysql.cj.jdbc.Driver
      url: jdbc:mysql://localhost:3306/seata?serverTimezone=Asia/Shanghai&useSSL=false
      user: root
      password: ok
      min-conn: 10
      max-conn: 100
      global-table: global_table
      branch-table: branch_table
      lock-table: lock_table
      distributed-lock-table: distributed_lock
      query-limit: 1000
      max-wait: 5000




  security:
    secretKey: SeataSecretKey0c382ef121d778043159209298fd40bf3850a017
    tokenValidityInMilliseconds: 1800000
    ignore:
      urls: /,/**/*.css,/**/*.js,/**/*.html,/**/*.map,/**/*.svg,/**/*.png,/**/*.jpeg,/**/*.ico,/api/v1/auth/login,/metadata/v1/**

```





#### 编写yml文件

```yml
server:
  port: 2003

spring:
  application:
    name: seata-account-service
  cloud:
    nacos:
      server-addr: localhost:8848
  datasource:
    type: com.alibaba.druid.pool.DruidDataSource
    driver-class-name: com.mysql.cj.jdbc.Driver
    url: jdbc:mysql://localhost:3306/seata_account?characterEncoding=utf-8&useSSL=false&serverTimezone=GMT%2B8&rewriteBatchedStatements=true&allowPublicKeyRetrieval=true
    username: root
    password: ok

seata:
  registry:
    type: nacos
    nacos:
      server-addr: localhost:8848
      namespace: ""
      group: DEFAULT_GROUP
      application: seata-server
  tx-service-group: default_tx_group #这里是组的id用于标识和管理分布式事务的全局事务组。 
  service:
    vgroup-mapping:
      default_tx_group: default #默认default
  data-source-proxy-mode: AT

logging:
  level:
    io:
      seata: info

```





#### 编写业务

##### 订单业务

```java
/**
 * <p>
 *  服务实现类
 * </p>
 *
 * @author author
 * @since 2024-08-18
 */
@Service
@Slf4j
public class TOrderServiceImpl extends ServiceImpl<TOrderMapper, TOrder> implements ITOrderService {

    @Resource
    private TOrderMapper orderMapper;
    @Resource
    private StorageFeignApi storageFeignApi;
    @Resource
    private AccountFeignApi accountFeignApi;

    @Override
    public void create(TOrder order) {
        // xid全局事务检查
        String xid = RootContext.getXID();

        // 1. 新建订单
        log.info("-------------> 开始新建订单, XID: {}", xid);
        order.setStatus(0);
        int result = orderMapper.insert(order);

        TOrder orderFromDB;
        if (result > 0) {

            System.out.println(order.getId());
            orderFromDB = orderMapper.selectById(order.getId());
            log.info("-------------> 新建订单成功, OrderInfo: {}", orderFromDB);

            // 2. 扣减库存
            log.info("-------------> 开始扣减库存");
            storageFeignApi.decrease(orderFromDB.getProductId(), orderFromDB.getCount());
            log.info("-------------> 扣减库存成功");

            // 3. 扣减账户余额
            log.info("-------------> 开始扣减余额");
            accountFeignApi.decrease(order.getUserId(), Long.valueOf(order.getMoeny()));
            log.info("-------------> 扣余额存成功");

            // 4. 修改订单状态
            log.info("-------------> 开始修改订单状态");
            order.setId(orderFromDB.getId());
            order.setStatus(1);
            int i = orderMapper.updateById(order);
            log.info("-------------> 修改订单状态成功");

        }
        log.info("-------------> 结束新建订单, XID: {}", xid);
    }
}
```



##### 账户业务

```java
/**
 * <p>
 *  服务实现类
 * </p>
 *
 * @author author
 * @since 2024-08-18
 */
@Slf4j
@Service
public class TAccountServiceImpl extends ServiceImpl<TAccountMapper, TAccount> implements ITAccountService {

    @Resource
    private TAccountMapper accountMapper;

    @Override
    public void decrease(Long userId, Long money) {
        log.info("------------->AccountService 开始扣减余额");
        accountMapper.decrease(userId, money);
        log.info("------------->AccountService 开始扣减余额");


        // 超时异常
         timeout();
        // 抛出异常
//         int i = 10 / 0;
    }

    private void timeout() {
        try {
            TimeUnit.SECONDS.sleep(65);
        } catch (InterruptedException e) {
            throw new RuntimeException(e);
        }
    }
}

```



##### 库存业务

```java
/**
 * <p>
 *  服务实现类
 * </p>
 *
 * @author author
 * @since 2024-08-18
 */
@Slf4j
@Service
public class TAccountServiceImpl extends ServiceImpl<TAccountMapper, TAccount> implements ITAccountService {

    @Resource
    private TAccountMapper accountMapper;

    @Override
    public void decrease(Long userId, Long money) {
        log.info("------------->AccountService 开始扣减余额");
        accountMapper.decrease(userId, money);
        log.info("------------->AccountService 开始扣减余额");


        // 超时异常
         timeout();
        // 抛出异常
//         int i = 10 / 0;
    }

    private void timeout() {
        try {
            TimeUnit.SECONDS.sleep(65);
        } catch (InterruptedException e) {
            throw new RuntimeException(e);
        }
    }
}

```



#### 测试·总结

【控制器直接调用 ，OpenFeign也一样】

【通过添加订单进行分布式事务的测试】



