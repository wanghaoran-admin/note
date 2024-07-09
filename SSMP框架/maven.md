# 第一章 maven简介 

传统项目管理状态分析

jar包不统一，jar不兼容

工程升级维护过程操作繁琐



### 1.maven是什么

maven本质是一个项目管理工具，将项目开发和管理过程抽象称一个项目对象模型（POM）

POM（project  object  model）:项目对象模型



### 2.maven的作用

项目构建：提供标准的，跨平台的自动化项目构建方式

依赖管理：方便快捷的管理项目依赖资源（jar），避免资源间的版本冲突问题

统一开发结构：提供标准的，统一的项目结构

 



# 第二章 maven下载与安装

### 1.maven的安装

官网地址：http://maven.apache.org/

下载地址：http://maven.apache.org/download.cgi



### 2.maven文件目录

bin：maven运行指令

boot：类加载器

conf：maven配置文件

lib：maven运行所依赖的jar包



### 3.maven环境变量

依赖java，需要配置JAVA_HOME

设置maven自身的运行环境，需要配置maven_home

```properties
JAVA_HOME:C:\Program Files\Java\jdk1.8.0_101\bin
maven_home:C:\dev\apache-maven-3.3.9\bin
```

检测是否配置成功：cmd   输入  mvn



# 第三章 maven基础概念

### 1.仓库

用于存储资源，包含各种jar包

中央仓库  https://mvnrepository.com/

### 2.仓库分类

本地仓库：自己的计算机上存储的资源，从中央仓库获取资源

中央仓库：maven自己管理，包含99%的jar包，为本地仓库提供资源 

私服：公司的仓库存储的资源，从中央仓库下载

### 3.私服的作用

保存具有版权的资源，包含购买或自主研发的jar

中央仓库的jar都是开源的，不能存储具有版权的jar

一定范围内资源共享，仅对内部开放，不对外共享

### 4.坐标

maven中坐标用于描述仓库中资源的位置

坐标的主要组成

groupid: 定义当前maven项目隶属于组织的名称（通常是域名反写，例如：org.mybatis）

artifactid: 定义当前maven项目名称(通常是模块名称)

version: 定义当前项目的版本号

### 5.配置本地仓库

本地仓库仓库默认位置  C:\Users\wanghaoran\.m2\repository

修改：conf目录下 setting文件中添加

```xml
<localRepository>要配置仓库的绝对位置</localRepository>
```



maven是国外服务器下载速度比较慢，因此阿里推出了镜像

修改：conf目录下 setting文件中找到mirrors标签添加

```xml
<!--配置具体的仓库下载镜像-->
<mirror>
	<!--此镜像唯一标识符，用来区分不同的镜像-->     
    <id>nexus-aliyun</id>
     <!--对哪种仓库进行镜像，简单说就是替代哪个仓库-->
    <mirrorOf>central</mirrorOf> <!--central中央仓库-->
     <!--镜像名称-->
    <name>Nexus aliyun</name>
    	<!--镜像url-->
    <url>http://maven.aliyun.com/nexus/content/groups/public</url>
</mirror>
```



# 第四章 maven工程

### 1.配置idea的maven  

​	配置maven的home	

​	修改maven版本，idea自带maven	

​	修改maven仓库位置，默认C:\Users\wanghaoran\.m2\repository

​	



### 2.配置依赖

```xml
<!--依赖-->
<dependencies>
     <!--具体依赖-->
        <dependency>
              <!--定义当前maven项目隶属于组织的名称-->
            <groupId>org.springframework</groupId>
              <!--定义当前maven项目名称-->
            <artifactId>spring-context</artifactId>
              <!--定义当前项目的版本号-->
            <version>5.3.20</version>
        </dependency>
</dependencies>
```



### 3.配置插件

```xml
    <!--构建-->
    <build>
	   <!--设置插件-->
        <plugins>
		  <!--具体插件-->
            <plugin>
                <groupId>org.apache.tomcat.maven</groupId>
                <artifactId>tomcat7-maven-plugin</artifactId>
                <version>2.1</version>
  			 <!--配置-->
                <configuration>
				<!--端口-->
                    <port>80</port>
				<!--路径-->
                    <path>/</path>
                </configuration>
            </plugin>
        </plugins>
    </build>
```



### 4.pom.xml常用标签及说明

| 标签          | 说明                                                         |
| ------------- | ------------------------------------------------------------ |
| modelVersion  | 指定pom的模型版本                                            |
| packaging     | 打包方式（web工程为war,java工程为jar）                       |
| groupid       | 所属的组织或者团队                                           |
| artifactId    | 项目的名称                                                   |
| version       | 项目的版本号(release 完成版  snapshort 开发版)               |
| dependencies  | 设置当前工程的所有依赖                                       |
| dependencie   | 具体的依赖                                                   |
| build         | 配置构建                                                     |
| plugins       | 设置插件                                                     |
| plugin        | 具体插件                                                     |
| configuration | 配置                                                         |
| port          | 端口                                                         |
| path          | 路径                                                         |
| optional      | 可选依赖（当一个工程引入了另一个工程时不想展现当前依赖时，可以设置为true   默认false） |
| exclusions    | 排除依赖                                                     |
| exclusions    | 具体排除依赖（可以排除间接依赖）                             |
| scope         | 依赖范围                                                     |
|               |                                                              |
|               |                                                              |



### 5.依赖传递

依赖具有传递性

直接依赖：在当前项目中通过依赖配置建立的依赖关系

间接依赖：被依赖资源如果依赖其他资源，这种关系就叫做间接依赖

 

依赖传递时冲突问题：

层级越深，优先级越低，层级越浅，优先级越高

当资源在相同层级被依赖时，配置顺序靠前的覆盖配置靠后的

当同级配置了相同资源不同版本，后配置的生效



在同一个项目中，一个模块想引入另一个模块时

```xml
<!--只需要把对应的模块引入就可以-->
<dependencies>
  <dependency>
    <groupId>org.example</groupId>
    <artifactId>spring_01</artifactId>
    <version>1.0-SNAPSHOT</version>
  </dependency>
</dependencies>
```



### 6.依赖范围

依赖的jar默认情况可以在任何地方使用，可以通过scope标签设定其作用范围

作用范围

![](D:\img\scope作用范围.png)





# 第五章 生命周期和插件

### 1.生命周期

maven对项目构建的生命周期划分为3套

clean：清理工作

default：核心工作，例如编译，测试，打包，部署等

site：产生报告，发布站点等



### 2.插件

插件与生命周期的关系在于，插件可以绑定到生命周期的不同阶段中，以在构建过程的特定点执行相应的任务。通过在`pom.xml`文件中配置插件的执行，可以实现自定义的构建行为

【具体自行百度】





# 第六章 多模块开发

### 1.多模块开发的特点

1. **模块化设计**：
	- 项目被拆分成多个功能单一、职责明确的模块，每个模块只关注特定的功能或逻辑部分。
2. **独立构建**：
	- 每个模块可以独立编译、测试和打包，这使得开发过程更灵活，各个模块可以并行开发和测试。
3. **共享依赖**：
	- 多个模块可以共享公共的依赖和构建配置，减少重复配置和依赖冲突。
4. **提高可维护性**：
	- 通过模块化设计，代码结构更加清晰，有助于代码的理解和维护。
5. **代码重用**：
	- 公共模块可以被多个其他模块依赖和重用，减少代码重复，提高开发效率。





### 2.多模块项目的结构

```
my-multi-module-project
│
├── pom.xml
│
├── module1
│   └── pom.xml
│
├── module2
│   └── pom.xml
│
└── module3
    └── pom.xml

```







### 3.dependencyManagement标签的使用

`dependencyManagement` 是 Maven 的一个标签，它用来在父 POM 中声明依赖版本信息，但不实际引入这些依赖。这样做的目的是为了在多个子模块中统一管理依赖的版本信息，确保依赖版本的一致性，并简化子模块的 POM 文件。





### 4.父工程注意

```xml
<groupId>org.qingniao</groupId>
<artifactId>springcloud</artifactId>
<version>1.0-SNAPSHOT</version>
<packaging>pom</packaging>  
```

打包是pom



```xml
<modules>
    <module>user</module>
</modules>
```

module是子工程名
