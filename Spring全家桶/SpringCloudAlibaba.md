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





### 4.流控规则

【在Sentinel左边菜单栏的流控规则中进行配置】



#### 流控模式-直接

是一种基本的限流方式，它直接基于流量的数量来进行限流。

在这种模式下，当流量超过设定的阈值时，系统会立即对超出的请求进行限制。





#### 流控模式-关联

**关联模式**通过关联其他资源的流量来决定当前资源的流控策略。

即当关联的资源达到阈值时，当前资源也会触发限流。





#### 流控模式-链路

**链路模式**是一种更复杂的限流方式，它基于调用链路来进行限流。只有当指定的链路（即从调用源头到目标资源的一系列调用路径）上的流量超过阈值时，才会触发限流。这种模式适用于服务调用路径比较复杂的分布式系统中，可以精准控制特定调用链上的流量。
