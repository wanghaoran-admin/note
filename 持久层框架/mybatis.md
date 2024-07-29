# 第一章 mybatis简介

### 1.什么是mybatis

MyBatis是一个轻量级、优秀持久层框架，适用于各种规模的Java应用程序。

官网：https://mybatis.net.cn/



### 2.mybatis优点

**jdbc的缺点**

1. 硬编码：注册驱动，获取连接，sql语句
2. 操作繁琐：手动设置参数，结果集
3. 缺乏内建的连接池支持： JDBC没有提供内建的连接池支持，连接池对于提高数据库连接的性能和效率非常重要



**mybatis的优点**

1. 小巧，学习成本低，会写sql上手就很快了。
2. 比jdbc，基本上配置好了，大部分的工作量就专注在sql的部分。
3. 方便维护管理，sql不需要在Java代码中找，sql代码可以分离出来，重用。
4. 接近jdbc,灵活，支持动态sql。
5. 支持对象与数据库orm字段关系映射。



### 3.什么是持久化

持久化：程序数据在瞬时状态（临时态）和持久状态（永久态）之间转换的过程【io流的读写，数据表记录的jdbc操作】



### 4.什么是ORM

ORM：对象关系映射，java的类对象和数据型数据表之间进行的映射匹配【表-类的对应  字段-属性的对应】



# 第二章 mybatis快速入门

### 1.创建user表，添加数据

```sql
CREATE DATABASE mybatis;

CREATE TABLE `user`(
	id INT PRIMARY KEY AUTO_INCREMENT,
	username VARCHAR(20),
	`password` VARCHAR(20),
	gender CHAR(1),
	addr VARCHAR(30) 
);

INSERT INTO `user` VALUE(0,'张三','123','男','徐州');
INSERT INTO `user` VALUE(0,'李四','123','女','上海');
INSERT INTO `user` VALUE(0,'王五','123','男','无锡');
```

### 2.创建模块，导入坐标

```xml
      <dependency>
          <groupId>org.mybatis</groupId>
          <artifactId>mybatis</artifactId>
          <version>3.5.10</version>
      </dependency>

      <dependency>
          <groupId>junit</groupId>
          <artifactId>junit</artifactId>
          <version>4.12</version>
          <scope>test</scope>
      </dependency>
```

### 3.编写核心配置文件

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE configuration
        PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-config.dtd">
<!--配置-->
<configuration>
    <environments default="development">
        <!--配置具体环境-->
        <environment id="development">
            <!--事务管理器-->
            <transactionManager type="JDBC"/>
            <!--数据源配置-->
            <dataSource type="POOLED">
                <property name="driver" value="com.mysql.jdbc.Driver"/>
                <property name="url" value="jdbc:mysql:///mybatis"/>
                <property name="username" value="root"/>
                <property name="password" value="ok"/>
            </dataSource>
        </environment>
    </environments>

    <mappers>
        <!--加载sql的映射文件-->
        <mapper resource=""/>
    </mappers>
</configuration>
```

### 4.编写sql映射文件

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper
        PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
<!--
    namespace:名称空间
-->
<mapper namespace="">
     				<!--结果类型 全限定类名-->
    <select id="findAll" resultType="com.example.mybatis01.pojo.User">
        select * from user
    </select>
</mapper>
```



### 5.编写java代码

```java
package com.example.mybatis01;

import org.apache.ibatis.io.Resources;
import org.apache.ibatis.session.SqlSession;
import org.apache.ibatis.session.SqlSessionFactory;
import org.apache.ibatis.session.SqlSessionFactoryBuilder;
import java.io.IOException;
import java.io.InputStream;
import java.util.List;

public class Test {
    public static void main(String[] args) throws IOException {
        //1.加载mybatis核心配置文件,获取sqlSessionFactory对象
        String resource = "mybatis-config.xml";
        InputStream inputStream = Resources.getResourceAsStream(resource);
        SqlSessionFactory sqlSessionFactory = new SqlSessionFactoryBuilder().build(inputStream);

        //2.获取sqlSession对象，用它来执行sql
        SqlSession sqlSession  = sqlSessionFactory.openSession();

        //3.执行sql
        List<Object> findAll = sqlSession.selectList("findAll");

        //4.执行结果
        System.out.println(findAll);

        //5.释放资源
        sqlSession.close();
    }
}

```



### 6.mybatis工具类编写

```java
    private static SqlSessionFactory sqlSessionFactory;

    static {
        String resource = "mybatis-config.xml";
        InputStream inputStream = null;
        try {
            inputStream = Resources.getResourceAsStream(resource);
            sqlSessionFactory = new SqlSessionFactoryBuilder().build(inputStream);
        } catch (IOException e) {
            e.printStackTrace();
        }
    }

    public static SqlSession getsqlsession(){
        return sqlSessionFactory.openSession(true);
    }
```







# 第三章 核心文件配置

### 1.属性（properties）

**jdbc.properties文件**

```properties
driver:oracle.jdbc.driver.OracleDriver
url:jdbc:oracle:thin:@localhost:1521:orcl
username:system
password:ok
```



**mybatis-config.xml文件**

```xml
<configuration>
    <!--该属性受.dtd的约束--> 
    <properties resource="jdbc.properties" />
    <environments default="development">
        <environment id="development">
            <transactionManager type="JDBC"/>
            <dataSource type="POOLED">
                <!--通过${}的方式获取properties中的属性-->
                <property name="driver" value="${driver}"/>
                <property name="url" value="${url}"/>
                <property name="username" value="${username}"/>
                <property name="password" value="${password}"/>
            </dataSource>
        </environment>
    </environments>
</configuration>
```



**默认值**

```xml
<dataSource type="POOLED">
  <!-- ... -->
  <property name="username" value="${username:ut_user}"/>
  <!-- 如果属性 'username' 没有被配置，'username' 属性的值将为 'ut_user' -->
</dataSource>
```

> 这个特性默认是关闭的。要启用这个特性，需要添加一个特定的属性来开启这个特性。例如：

```xml
<properties resource="org/mybatis/example/config.properties">
  <!-- ... -->
  <property name="org.apache.ibatis.parsing.PropertyParser.enable-default-value" value="true"/> <!-- 启用默认值特性 -->
</properties>
```



### 2.设置（setting）

这是 MyBatis 中极为重要的调整设置，它们会改变 MyBatis 的运行时行为

一个配置完整的 settings 元素的示例如下：

```xml
<settings>
  <setting name="cacheEnabled" value="true"/>
  <setting name="lazyLoadingEnabled" value="true"/>
  <setting name="multipleResultSetsEnabled" value="true"/>
  <setting name="useColumnLabel" value="true"/>
  <setting name="useGeneratedKeys" value="false"/>
  <!--
	resultMap设置 autoMappingBehavior 
	value=PARTIAL 自动匹配
	value=PARTIAL 不匹配
	value=FULL用于关联，集合操作
  -->
  <setting name="autoMappingBehavior" value="PARTIAL"/>
  <setting name="autoMappingUnknownColumnBehavior" value="WARNING"/>
  <setting name="defaultExecutorType" value="SIMPLE"/>
  <setting name="defaultStatementTimeout" value="25"/>
  <setting name="defaultFetchSize" value="100"/>
  <setting name="safeRowBoundsEnabled" value="false"/>
  <setting name="mapUnderscoreToCamelCase" value="false"/>
  <setting name="localCacheScope" value="SESSION"/>
  <setting name="jdbcTypeForNull" value="OTHER"/>
  <setting name="lazyLoadTriggerMethods" value="equals,clone,hashCode,toString"/>
</settings>
```

具体用法https://mybatis.net.cn/configuration.html



### 3.类型别名（typeAliases）

类型别名可为 Java 类型设置一个缩写名字。 它仅用于 XML 配置，意在降低冗余的全限定类名书写。例如：

```xml
<typeAliases>
        <!-- type类的全限定类名 -->
        <!-- alias别名 -->
        <typeAlias alias="Blog" type="domain.blog.Blog"/>
        <!-- 可以添加更多的 typeAlias -->
</typeAliases>
```

当这样配置时，`Blog` 可以用在任何使用 `domain.blog.Blog` 的地方。



也可以指定一个包名，MyBatis 会在包名下面搜索需要的 Java Bean，比如：

```xml
<typeAliases>
  <package name="domain.blog"/>
</typeAliases>
```

【注：命名空间使用不了】每一个在包 `domain.blog` 中的 Java Bean，在没有注解的情况下，会使用 Bean 的首字母小写的非限定类名来作为它的别名。 比如 `domain.blog.Author` 的别名为 `author`；



若有注解，则别名为其注解值。见下面的例子：

```java
@Alias("author")
public class Author {
    ...
}
```



### 4.映射（mappers）

```xml
<mappers>
     <!-- 扫描 xml 文件 -->  
     <mapper resource="DeptMapper.xml"/>
     <!-- 扫描注解方式的 Mapper 接口 -->  
     <package name="com.it"/>
</mappers>
```



### 5.环境（environments）

```xml
<environments default="development">
    <environment id="development">
        <transactionManager type="JDBC"/>
        <dataSource type="POOLED">
            <property name="driver" value="${driver}"/>
            <property name="url" value="${url}"/>
            <property name="username" value="${username}"/>
            <property name="password" value="${password}"/>
        </dataSource>
    </environment>
</environments>
```



### 6.插件（plugins）

```xml
<plugins>
    <plugin interceptor="com.github.pagehelper.PageInterceptor">
        <property name="helperDialect" value="oracle"/>
    </plugin>
</plugins>
```



### 7.事务管理器（transactionManager）

```xml
<!--
	jdbc:事务由jdbc代理
	jdbc:事务由其他代理【spring...】
-->
<transactionManager type="[ JDBC | MANAGED ]" />
```





# 第四章 XML 映射文件

MyBatis 的真正强大在于它的语句映射，这是它的魔力所在。映射器的 XML 文件就显得相对简单



SQL 映射文件只有很少的几个顶级元素

- `cache` – 该命名空间的缓存配置。
- `cache-ref` – 引用其它命名空间的缓存配置。
- `resultMap` – 描述如何从数据库结果集中加载对象，是最复杂也是最强大的元素。
- `sql` – 可被其它语句引用的可重用语句块。
- `insert` – 映射插入语句。
- `update` – 映射更新语句。
- `delete` – 映射删除语句。
- `select` – 映射查询语句。



### 1.select 标签

```xml
<select id="selectPerson" parameterType="int" resultType="hashmap">
  SELECT * FROM PERSON WHERE ID = #{id}
</select>
```

这个语句名为 selectPerson，接受一个 int（或 Integer）类型的参数，并返回一个 HashMap 类型的对象，其中的键是列名，值便是结果行中的对应值。

**select 元素的属性**

```sql
<select
  id="selectPerson"  --必须设置
  --必须设置 
  --查询封装的结果类型【如果字段和属性不一致，就不能自动封装】
  parameterType="int"  
  parameterMap="deprecated"
  resultType="hashmap"
  resultMap="personResultMap"
  flushCache="false"
  useCache="true"
  timeout="10"
  fetchSize="256"
  statementType="PREPARED"
  resultSetType="FORWARD_ONLY">
```



| 属性            | 描述                                                         |
| :-------------- | :----------------------------------------------------------- |
| `id`            | 在命名空间中唯一的标识符，可以被用来引用这条语句。           |
| `parameterType` | 将会传入这条语句的参数的类全限定名或别名。这个属性是可选的，因为 MyBatis 可以通过类型处理器（TypeHandler）推断出具体传入语句的参数，默认值为未设置（unset）。 |
| parameterMap    | 用于引用外部 parameterMap 的属性，目前已被废弃。请使用行内参数映射和 parameterType 属性。 |
| `resultType`    | 期望从这条语句中返回结果的类全限定名或别名。 注意，如果返回的是集合，那应该设置为集合包含的类型，而不是集合本身的类型。 resultType 和 resultMap 之间只能同时使用一个。 |
| `resultMap`     | 对外部 resultMap 的命名引用。结果映射是 MyBatis 最强大的特性，如果你对其理解透彻，许多复杂的映射问题都能迎刃而解。 resultType 和 resultMap 之间只能同时使用一个。 |
| `flushCache`    | 将其设置为 true 后，只要语句被调用，都会导致本地缓存和二级缓存被清空，默认值：false。 |
| `useCache`      | 将其设置为 true 后，将会导致本条语句的结果被二级缓存缓存起来，默认值：对 select 元素为 true。 |
| `timeout`       | 这个设置是在抛出异常之前，驱动程序等待数据库返回请求结果的秒数。默认值为未设置（unset）（依赖数据库驱动）。 |
| `fetchSize`     | 这是一个给驱动的建议值，尝试让驱动程序每次批量返回的结果行数等于这个设置值。 默认值为未设置（unset）（依赖驱动）。 |
| `statementType` | 可选 STATEMENT，PREPARED 或 CALLABLE。这会让 MyBatis 分别使用 Statement，PreparedStatement 或 CallableStatement，默认值：PREPARED。 |
| `resultSetType` | FORWARD_ONLY，SCROLL_SENSITIVE, SCROLL_INSENSITIVE 或 DEFAULT（等价于 unset） 中的一个，默认值为 unset （依赖数据库驱动）。 |
| `databaseId`    | 如果配置了数据库厂商标识（databaseIdProvider），MyBatis 会加载所有不带 databaseId 或匹配当前 databaseId 的语句；如果带和不带的语句都有，则不带的会被忽略。 |
| `resultOrdered` | 这个设置仅针对嵌套结果 select 语句：如果为 true，将会假设包含了嵌套结果集或是分组，当返回一个主结果行时，就不会产生对前面结果集的引用。 这就使得在获取嵌套结果集的时候不至于内存不够用。默认值：`false`。 |
| `resultSets`    | 这个设置仅适用于多结果集的情况。它将列出语句执行后返回的结果集并赋予每个结果集一个名称，多个名称之间以逗号分隔。 |











### 2.insert, update , delete  标签

```xml
<insert id="insertAuthor">
  insert into Author (id,username,password,email,bio)
  values (#{id},#{username},#{password},#{email},#{bio})
</insert>

<update id="updateAuthor">
  update Author set
    username = #{username},
    password = #{password},
    email = #{email},
    bio = #{bio}
  where id = #{id}
</update>

<delete id="deleteAuthor">
  delete from Author where id = #{id}
</delete>
```



```xml
<insert
  id="insertAuthor"
  parameterType="domain.blog.Author"
  flushCache="true"
  statementType="PREPARED"
  keyProperty=""
  keyColumn=""
  useGeneratedKeys=""
  timeout="20">

<update
  id="updateAuthor"
  parameterType="domain.blog.Author"
  flushCache="true"
  statementType="PREPARED"
  timeout="20">

<delete
  id="deleteAuthor"
  parameterType="domain.blog.Author"
  flushCache="true"
  statementType="PREPARED"
  timeout="20">
```



| 属性               | 描述                                                         |
| :----------------- | :----------------------------------------------------------- |
| `id`               | 在命名空间中唯一的标识符，可以被用来引用这条语句。           |
| `parameterType`    | 将会传入这条语句的参数的类全限定名或别名。这个属性是可选的，因为 MyBatis 可以通过类型处理器（TypeHandler）推断出具体传入语句的参数，默认值为未设置（unset）。 |
| `parameterMap`     | 用于引用外部 parameterMap 的属性，目前已被废弃。请使用行内参数映射和 parameterType 属性。 |
| `flushCache`       | 将其设置为 true 后，只要语句被调用，都会导致本地缓存和二级缓存被清空，默认值：（对 insert、update 和 delete 语句）true。 |
| `timeout`          | 这个设置是在抛出异常之前，驱动程序等待数据库返回请求结果的秒数。默认值为未设置（unset）（依赖数据库驱动）。 |
| `statementType`    | 可选 STATEMENT，PREPARED 或 CALLABLE。这会让 MyBatis 分别使用 Statement，PreparedStatement 或 CallableStatement，默认值：PREPARED。 |
| `useGeneratedKeys` | （仅适用于 insert 和 update）这会令 MyBatis 使用 JDBC 的 getGeneratedKeys 方法来取出由数据库内部生成的主键（比如：像 MySQL 和 SQL Server 这样的关系型数据库管理系统的自动递增字段），默认值：false。 |
| `keyProperty`      | （仅适用于 insert 和 update）指定能够唯一识别对象的属性，MyBatis 会使用 getGeneratedKeys 的返回值或 insert 语句的 selectKey 子元素设置它的值，默认值：未设置（`unset`）。如果生成列不止一个，可以用逗号分隔多个属性名称。 |
| `keyColumn`        | （仅适用于 insert 和 update）设置生成键值在表中的列名，在某些数据库（像 PostgreSQL）中，当主键列不是表中的第一列的时候，是必须设置的。如果生成列不止一个，可以用逗号分隔多个属性名称。 |



### 3.sql 标签

这个元素可以用来定义可重用的 SQL 代码片段，以便在其它语句中使用



**示例一：**

```xml
 <sql id="sql"> select * from scott.dept</sql>
```

通过include  refid  id名进行调用

```xml
<select id="findByid" resultType="dept">
	<include refid="sql"></include>
</select>
```



**示例二：**

```xml
<sql id="sql">${name}.dname</sql>
```

通过include子级元素property属性进行字符串的替换【name要和${}中的名称一致】

```xml
<select id="findByid" resultType="dept">
        select  <include refid="sql"><property name="name" value="d"/></include>
        from scott.dept d where d.deptno = ${id} and d.dname = #{name}
</select>
```



**实例三：**

```xml
<sql id="sql"><include refid="sel"/>${name}.dname</sql>
<sql id="sel">${sel}</sql>
```

嵌套 sql 标签

```xml


    <select id="findByid" resultType="dept">
          <include refid="sql">
		    <!--name是表的别名-->
              <property name="name" value="d"/>
		    <!--sel是嵌套在sql中的-->
              <property name="sel" value="select"/>
            </include>
        from scott.dept d where d.deptno = ${id} and d.dname = #{name}
</select>
```



### 4.参数

| 属性  | 说明       |
| ----- | ---------- |
| #{id} | 预编译     |
| ${id} | 字符串替换 |



### **5.字符串替换**

当 SQL 语句中的元数据（如表名或列名）是动态生成的时候，字符串替换将会非常有用

例子：

```java
@Select("select * from user where ${column} = #{value}")
User findByColumn(@Param("column") String column, @Param("value") String value);
```

```java
User userOfId1 = userMapper.findByColumn("id", 1L);
User userOfNameKid = userMapper.findByColumn("name", "kid");
User userOfEmail = userMapper.findByColumn("email", "noone@nowhere.com");
```









### 6.结果映射

`resultMap` 元素是 MyBatis 中最重要最强大的元素

查询语句中必须要进行配置

```xml
<select id="selectUsers" resultType="map">
  select id, username, hashedPassword
  from some_table
  where id = #{id}
</select>
```









### 7.返回自增字段

1.数据库自带自增属性

比如mysql数据库【auto_increment】,sqlServer数据库【identity(1,1)】

```xml
 <insert id="add" useGeneratedKeys="true" keyProperty="类属性">
        insert into apply_info values(apply_info_sequence.nextval,#{name},#{age},#{aclass},#{game})
</insert>
```



2.数据库不自带自增属性

比如oracle数据库，自增是通过序列实现自增效果的

```xml
 <insert id="add">
        <selectKey keyProperty="类属性" order="AFTER" resultType="int">
            select apply_info_sequence.nextval from dual
        </selectKey>
        insert into apply_info values(apply_info_sequence.nextval,#{name},#{age},#{aclass},#{game})
</insert>
```



### 8.resultMap 标签

如果类属性和数据库字段名称不一致，无法进行自动装配，就需要我们使用resultMap节点进行一一匹配。

```xml
<resultMap id="ApplyInfoMap" type="ApplyInfo">
     <!--匹配主键-->
   	<id property="ApplyInfoId" column="ApplyInfo_id"></id>
     <!--匹配基本字段，也可以匹配主键-->
     <result property="aclass" column="class"></result>
</resultMap>
```

设置该节点内容之后，我们在查询的时候，就可以直接使用select元素的resultmap属性进行数据封装【resultType和resultMap不能同时出现】





# 第五章 XML 动态SQL标签

动态 SQL 是 MyBatis 的强大特性之一，如果你之前用过 JSTL 或任何基于类 XML 语言的文本处理器，你对动态 SQL 元素可能会感觉似曾相识

- if
- choose (when, otherwise)
- trim (where, set)
- foreach





### 1.if 

使用动态 SQL 最常见情景是根据条件包含 where 子句的一部分。比如：

```xml
<select id="findActiveBlogWithTitleLike"
     resultType="Blog">
  SELECT * FROM BLOG
  WHERE state = ‘ACTIVE’
  <if test="title != null">
    AND title like #{title}
  </if>
</select>
```





### 2.choose、when、otherwise                                                                                   

有时候，我们不想使用所有的条件，而只是想从多个条件中选择一个使用。针对这种情况，MyBatis 提供了 choose 元素，它有点像 Java 中的 switch 语句 比如：

```xml
<select id="findActiveBlogLike"
     resultType="Blog">
  SELECT * FROM BLOG WHERE state = ‘ACTIVE’
  <choose>
    <when test="title != null">
      AND title like #{title}
    </when>
    <when test="author != null and author.name != null">
      AND author_name like #{author.name}
    </when>
    <otherwise>
      AND featured = 1
    </otherwise>
  </choose>
</select>
```

还是上面的例子，但是策略变为：传入了 “title” 就按 “title” 查找，传入了 “author” 就按 “author” 查找的情形。若两者都没有传入，就返回标记为 featured 的 BLOG（这可能是管理员认为，与其返回大量的无意义随机 Blog，还不如返回一些由管理员挑选的 Blog）。





### 3.trim、where、set

**3.1 where**

现在回到之前的 “if” 示例，这次我们将 “state = ‘ACTIVE’” 设置成动态条件，看看会发生什么。

```xml
<select id="findActiveBlogLike"
     resultType="Blog">
  SELECT * FROM BLOG
  WHERE
  <if test="state != null">
    state = #{state}
  </if>
  <if test="title != null">
    AND title like #{title}
  </if>
  <if test="author != null and author.name != null">
    AND author_name like #{author.name}
  </if>
</select>
```

如果没有匹配的条件会怎么样？最终这条 SQL 会变成这样：

```sql
SELECT * FROM BLOG
WHERE
```

这会导致查询失败。如果匹配的只是第二个条件又会怎样？这条 SQL 会是这样:

```sql
SELECT * FROM BLOG
WHERE
AND title like ‘someTitle’
```

这个查询也会失败



MyBatis 有一个简单且适合大多数场景的解决办法,只需要一处简单的改动：

```xml
<select id="findActiveBlogLike"
     resultType="Blog">
  SELECT * FROM BLOG
  <where>
    <if test="state != null">
         state = #{state}
    </if>
    <if test="title != null">
        AND title like #{title}
    </if>
    <if test="author != null and author.name != null">
        AND author_name like #{author.name}
    </if>
  </where>
</select>
```

*where* 元素只会在子元素返回任何内容的情况下才插入 “WHERE” 子句。而且，若子句的开头为 “AND” 或 “OR”，*where* 元素也会将它们去除。



**3.2 where trim**

如果 *where* 元素与你期望的不太一样，你也可以通过自定义 trim 元素来定制 *where* 元素的功能

```xml
<trim prefix="WHERE" prefixOverrides="AND |OR ">
  ...
</trim>
```

*prefixOverrides* 属性会忽略通过管道符分隔的文本序列（注意此例中的空格是必要的）



**3.3 set**

用于动态更新语句的类似解决方案叫做 *set*。*set* 元素可以用于动态包含需要更新的列，忽略其它不更新的列。比如：

```xml
<update id="updateAuthorIfNecessary">
  update Author
    <set>
      <if test="username != null">username=#{username},</if>
      <if test="password != null">password=#{password},</if>
      <if test="email != null">email=#{email},</if>
      <if test="bio != null">bio=#{bio}</if>
    </set>
  where id=#{id}
</update>

<trim prefix="SET" suffixOverrides=",">
  ...
</trim>
```

这个例子中，*set* 元素会动态地在行首插入 SET 关键字，并会删掉额外的逗号（这些逗号是在使用条件语句给列赋值时引入的）。



**3.4 set trim**

*set* 元素等价的自定义 *trim* 元素

```xml
<trim prefix="SET" suffixOverrides=",">
  ...
</trim>
```





### **4.foreach**

动态 SQL 的另一个常见使用场景是对集合进行遍历（尤其是在构建 IN 条件语句的时候）。比如：

```xml
<select id="selectPostIn" resultType="domain.blog.Post">
  SELECT *
  FROM POST P
  WHERE ID in
  <foreach item="item" index="index" collection="list"
      open="(" separator="," close=")">
        #{item}
  </foreach>
</select>
```

*foreach* 元素的功能非常强大，它允许你指定一个集合，声明可以在元素体内使用的集合项（item）和索引（index）变量。它也允许你指定开头与结尾的字符串以及集合项迭代之间的分隔符。这个元素也不会错误地添加多余的分隔符





### **5.script**

要在带注解的映射器接口类中使用动态 SQL，可以使用 *script* 元素

```java
@Update({"<script>",
      "update Author",
      "  <set>",
      "    <if test='username != null'>username=#{username},</if>",
      "    <if test='password != null'>password=#{password},</if>",
      "    <if test='email != null'>email=#{email},</if>",
      "    <if test='bio != null'>bio=#{bio}</if>",
      "  </set>",
      "where id=#{id}",
      "</script>"})
void updateAuthorValues(Author author);
```





### **6.bind**

`bind` 元素允许你在 OGNL 表达式以外创建一个变量，并将其绑定到当前的上下文。比如：

```xml
<select id="selectBlogsLike" resultType="Blog">
  <bind name="pattern" value="'%' + _parameter.getTitle() + '%'" />
  SELECT * FROM BLOG
  WHERE title LIKE #{pattern}
</select>
```





### **7.多数据库支持**

https://mybatis.net.cn/dynamic-sql.html 最下面

### 8.动态 SQL 中的插入脚本语言

https://mybatis.net.cn/dynamic-sql.html 最下面







# 第六章 注解开发

映射注解示例

### 1.@SelectKey 注解在插入前读取数据库序列

这个例子展示了如何使用 @SelectKey 注解来在插入前读取数据库序列的值：

```java
@Insert("insert into table3 (id, name) values(#{nameId}, #{name})")
@SelectKey(statement="call next value for TestSequence", keyProperty="nameId", before=true, resultType=int.class)
int insertTable3(Name name);
```



### 2.@SelectKey 注解在插入后读取数据库序列

这个例子展示了如何使用 @SelectKey 注解来在插入后读取数据库自增列的值：

```java
@Insert("insert into table2 (name) values(#{name})")
@SelectKey(statement="call identity()", keyProperty="nameId", before=false, resultType=int.class)
int insertTable2(Name name);
```



### 3.@Flush注解

这个例子展示了如何使				用 `@Flush` 注解来调用 `SqlSession#flushStatements()`：

```java
@Flush
List<BatchResult> flush();
```



### 4.@Result注解

这些例子展示了如何通过指定 @Result 的 id 属性来命名结果集：

```java
@Results(id = "userResult", value = {
  @Result(property = "id", column = "uid", id = true),
  @Result(property = "firstName", column = "first_name"),
  @Result(property = "lastName", column = "last_name")
})
@Select("select * from users where id = #{id}")
User getUserById(Integer id);

@Results(id = "companyResults")
@ConstructorArgs({
  @Arg(column = "cid", javaType = Integer.class, id = true),
  @Arg(column = "name", javaType = String.class)
})
@Select("select * from company where id = #{id}")
Company getCompanyById(Integer id);
```



### 5.@单个参数SqlProvider注解

这个例子展示了如何使用单个参数的 @SqlProvider 注解：

```java
@SelectProvider(type = UserSqlBuilder.class, method = "buildGetUsersByName")
List<User> getUsersByName(String name);

class UserSqlBuilder {
  public static String buildGetUsersByName(final String name) {
    return new SQL(){{
      SELECT("*");
      FROM("users");
      if (name != null) {
        WHERE("name like #{value} || '%'");
      }
      ORDER_BY("id");
    }}.toString();
  }
}
```



### 6.@多个参数SqlProvider注解

这个例子展示了如何使用多个参数的 @SqlProvider 注解：

```java
@SelectProvider(type = UserSqlBuilder.class, method = "buildGetUsersByName")
List<User> getUsersByName(
    @Param("name") String name, @Param("orderByColumn") String orderByColumn);

class UserSqlBuilder {

  // 如果不使用 @Param，就应该定义与 mapper 方法相同的参数
  public static String buildGetUsersByName(
      final String name, final String orderByColumn) {
    return new SQL(){{
      SELECT("*");
      FROM("users");
      WHERE("name like #{name} || '%'");
      ORDER_BY(orderByColumn);
    }}.toString();
  }

  // 如果使用 @Param，就可以只定义需要使用的参数
  public static String buildGetUsersByName(@Param("orderByColumn") final String orderByColumn) {
    return new SQL(){{
      SELECT("*");
      FROM("users");
      WHERE("name like #{name} || '%'");
      ORDER_BY(orderByColumn);
    }}.toString();
  }
}
```



### 7.@Param注解

如果你的映射方法接受多个参数，就可以使用这个注解自定义每个参数的名字。否则在默认情况下，除 `RowBounds` 以外的参数会以 "param" 加参数位置被命名。例如 `#{param1}`, `#{param2}`。如果使用了 `@Param("person")`，参数就会被命名为 `#{person}`。





### 8.@ResultMap注解

这个注解为 `@Select` 或者 `@SelectProvider` 注解指定 XML 映射中 `` 元素的 id。这使得注解的 select 可以复用已在 XML 中定义的 ResultMap。如果标注的 select 注解中存在 `@Results` 或者 `@ConstructorArgs` 注解，这两个注解将被此注解覆盖。





### 9.@ResultType注解

在使用了结果处理器的情况下，需要使用此注解。由于此时的返回类型为 void，所以 Mybatis 需要有一种方法来判断每一行返回的对象类型。如果在 XML 有对应的结果映射，请使用 `@ResultMap` 注解。如果结果类型在 XML 的 `` 元素中指定了，就不需要使用其它注解了。否则就需要使用此注解。比如，如果一个标注了 @Select 的方法想要使用结果处理器，那么它的返回类型必须是 void，并且必须使用这个注解（或者 @ResultMap）。这个注解仅在方法返回类型是 void 的情况下生效。



### 10.@Insert @Update @Delete @Select注解

每个注解分别代表将会被执行的 SQL 语句





### 11.@options注解

```java
@options(keyProperty="属性名",userGeneratrdKeys=true)
```







# 第七章 mybatis缓存

### 1.简介

缓存技术是一种空间换时间的方式，先把查询结果存入本地缓存，二级缓存，或者第三方缓存中，在执行相同sql，先从缓存中读取数据，没有在从数据中读取



默认情况下，只启用了本地的会话缓存，它仅仅对一个会话中的数据进行缓存。 



### 2.一级缓存

一级缓存默认开启，session基本的缓存

**java程序**

```java
public static void main(String[] args) {
        SqlSession getsqlsession = MybatisConfig.getsqlsession();
        DistrictMapper mapper = getsqlsession.getMapper(DistrictMapper.class);
        List<District> districts = mapper.find();
        List<District> districts1 = mapper.find();
        System.out.println(districts);
        System.out.println(districts1);
        getsqlsession.close();
}
```



**日志记录**

```
DEBUG - ==>  Preparing: SELECT d.*,s.`name` sname FROM district d,street s WHERE d.`id` = s.`district_id`
DEBUG - ==> Parameters: 
DEBUG - <==      Total: 7
```

在同一个sqlsession域中，第一次查询到数据并且存入缓存中，第二次查询会先从缓存中查。



### 3.二级缓存

要启用全局的二级缓存，只需要在你的 SQL 映射文件中添加一行：

```
 <cache/>
```

这个简单语句的效果如下:

- 映射语句文件中的所有 select 语句的结果将会被缓存。
- 映射语句文件中的所有 insert、update 和 delete 语句会刷新缓存。
- 缓存会使用最近最少使用算法（LRU, Least Recently Used）算法来清除不需要的缓存。
- 缓存不会定时进行刷新（也就是说，没有刷新间隔）。
- 缓存会保存列表或对象（无论查询方法返回哪种）的 1024 个引用。
- 缓存会被视为读/写缓存，这意味着获取到的对象并不是共享的，可以安全地被调用者修改，而不干扰其他调用者或线程所做的潜在修改。
- 作用域和SqlSessionFactory同级









# 第八章 逆向工程











# 第九章 mybatis分页

### 1.插件分页

#### 1.1 添加依赖

```xml
 <dependency>
          <groupId>com.github.pagehelper</groupId>
          <artifactId>pagehelper</artifactId>
          <version>5.1.10</version>
</dependency>
```



#### 1.2 mybatis-config.xml 配置插件

```xml
  <plugins>
        <plugin interceptor="com.github.pagehelper.PageInterceptor">
            <!--配置哪种数据库-->
            <property name="helperDialect" value="oracle"/>
            <!--分页是否合理化-->
            <property name="reasonable" value="true"/> 
       </plugin>
    </plugins>
```



#### 1.3 开始分页

```java
public class MembersInfoServiceImpl implements MembersInfoService {
    MembersInfoMapper mapper = MybatisUtils.getsqlsession().getMapper(MembersInfoMapper.class);

    @Override
    public List<MembersInfo> findByPage(int index, int size) {
        //开始分页
        //index页面
        //size每页多少条数据
        //【只会有分页数据，没有共..条等】
        PageHelper.startPage(index,size);
        List<MembersInfo> byPage = mapper.findByPage(index, size);
        return byPage;
    }

    @Override
    public int update(MembersInfo m) {
        return mapper.update(m);
    }
}
```



#### 1.4 获取共..条等

```java
@WebServlet({"/page","/update"})
public class MembersInfoController extends HttpServlet {
    MembersInfoService ms = new MembersInfoServiceImpl();

    @Override
    protected void service(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
        request.setCharacterEncoding("utf-8");
        response.setContentType("text/html;charset=utf-8");
        String servletPath = request.getServletPath();
        if (servletPath.equals("/page")){
            page(request,response);
        }
    }

    private void page(HttpServletRequest request, HttpServletResponse response) throws IOException {
        List<MembersInfo> byPage = ms.findByPage(1, 4);
        //pageInfo对象会获取共..条等
        PageInfo<MembersInfo> pageInfo = new PageInfo<MembersInfo>(byPage);
        System.out.println(pageInfo);
        PrintWriter out = response.getWriter();
        out.println(JSON.toJSONString(pageInfo));
    }
}

```





### 2.原生分页

#### 2.1 编写xml

```xml
<select id="findByPage" resultMap="ApplyInfoMap">
    <![CDATA[
                select * from (
                      select a.*,rownum r from apply_info a
                      where rownum <=#{index}*#{size}
                  ) where r>(#{index}-1)*#{size} and name like '%${name}%'
    ]]>
</select>
```



#### 2.2 编写service

```java
@Override
    public void findByPage(PageUtil pg) {
        pg.setCount(mapper.count());
        String name = (String) pg.getWhere().get("name");
        pg.setList(mapper.findByPage(pg.getIndex(),pg.getSize(), name));
    }
```









# 第十章 日志整合

### 1.添加依赖

```xml
 <dependency>
          <groupId>log4j</groupId>
          <artifactId>log4j</artifactId>
          <version>1.2.17</version>
 </dependency>
```



### 2.编写properties文件

```properties
log4j.rootLogger=debug,stdout,logfile 

log4j.appender.stdout=org.apache.log4j.ConsoleAppender
log4j.appender.stdout.Target=System.err
log4j.appender.stdout.layout=org.apache.log4j.SimpleLayout

log4j.appender.logfile=org.apache.log4j.FileAppender
log4j.appender.logfile.File=log.log
log4j.appender.logfile.layout=org.apache.log4j.PatternLayout
log4j.appender.logfile.layout.ConversionPattern=%d{yyyy-MM-dd HH\:mm\:ss} %l %F %p %m%n
```



### 3.mybatis核心文件配置

```xml
<settings>
    <setting name="logImpl" value="log4j"/>
</settings>
```











# 第十一章 mybatis调oracle存储过程

在oracle环境下定义的存储过程，我们可以使用mybatis框架对他进行远程调用

1.基础案例



### 1.mapper接口编写

```java
package com.example.demo4.mapper;

import java.util.Map;

public interface BookMapper {
    void findAll(Map<String,Object> map);

    void add(Map<String,Object> map);

    void delete(Map<String,Object> map);

    void findByPage(Map<String,Object> map);
}

```



### 2.mapper.xml编写

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper
        PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
<mapper namespace="com.example.demo4.mapper.BookMapper">
    <resultMap id="BookMap" type="book"/>

    <insert id="findAll" statementType="CALLABLE">
        		<!--存储过程的名称-->
         {call b_findAll(
         		<!--参数以及对应的参数类型-->
                #{cur,mode=OUT,jdbcType=CURSOR,resultMap=BookMap}
        )}
    </insert>

    <delete id="delete" statementType="CALLABLE">
        {call b_delete(
                #{bid}
            )}
    </delete>

    <select id="add" statementType="CALLABLE">
        {call b_add(
            #{bname},
            #{bauthor},
            #{bprice},
            #{bnum}
        )}
    </select>

    <select id="findByPage" statementType="CALLABLE">
        {call Pager(
                #{currPage,mode=IN},
                #{pageSize,mode=IN},
                #{tableName},
                #{strWhere},
                #{orderBy},
                #{numCount,mode=OUT,jdbcType=INTEGER,resultMap=BookMap},
                #{v_cur,mode=OUT,jdbcType=CURSOR,resultMap=BookMap}
            )}
    </select>
</mapper>
```

【注：输入参数只需要定义名称即可，输出参数需要定义mode，jdbcType等】



### 3.测试编写

```java
package com.example.demo4;

import com.example.demo4.entity.Book;
import com.example.demo4.mapper.BookMapper;
import com.example.demo4.utils.MybatisConfig;

import java.util.*;

public class Test {
    public static void main(String[] args) {
        BookMapper mapper = MybatisConfig.openSqlSession().getMapper(BookMapper.class);
        Scanner input = new Scanner(System.in);
        System.out.println("========存储过程调用========");
        System.out.println("1.显示所有 2.添加 3.删除 4.分页 0退出");
        int xuan = input.nextInt();
        if (xuan==1){
            Map<String,Object> map = new HashMap<String,Object>();
            mapper.findAll(map);
										//cur要和mapper.xml中值对应
            List<Book> list = (List<Book>) map.get("cur");
            for (Book book :list){
                System.out.println(book);
            }
        }else if (xuan==2){
            Map<String,Object> map = new HashMap<String,Object>();
            //key要和xml中的参数名一致
            map.put("bname","花花");
            map.put("bauthor","32");
            map.put("bprice","53");
            map.put("bnum","32");
            mapper.add(map);

        }else if (xuan==3){
            Map<String,Object> map = new HashMap<String,Object>();
            map.put("bid","9");
            mapper.delete(map);
        }else if (xuan==4){
            Map<String,Object> map = new HashMap<String,Object>();
            map.put("currPage","1");
            map.put("pageSize","4");
            map.put("tableName","book_info");
            map.put("strWhere","and bname like '%J%'");
            map.put("orderBy","bid");
            mapper.findByPage(map);
            List v_cur = Collections.singletonList(map.get("v_cur"));
            System.out.println(v_cur);
        }else if (xuan==0){
            return;
        }else{
            System.out.println("退出成功！！");
        }
    }
}
```



### 4.存储过程分页的写法



```sql
--创建oracle分页存储过程
create or replace procedure Pager(
currPage in number,--当前页码
pageSize in number,--每页大小
tableName nvarchar2,--表名
strWhere nvarchar2,--where条件
orderBy nvarchar2,--排序
numCount out number,--总记录数
v_cur out sys_refcursor--分页数据
) is
strSql varchar2(2000);--获取数据的sql语句
begin
  strSql:='select count(1) from '||tableName;
  --拼接条件
  if strWhere is not null or strWhere<>'' then 
     strSql:=strSql||' where 1=1 '||strWhere;
  end if;  
  execute immediate strSql into numCount;
  
  strSql:='select * from ' || tableName;  
  if strWhere is not null or strWhere<>'' then 
     strSql:=strSql||' where 1=1 '||strWhere;
  end if;
  
  if  orderBy is not null or orderBy<>'' then 
     strSql:=strSql||' order by '||orderBy;
  end if;
  
  strSql:='select t.*,rownum r from ('||strSql||') t where rownum <= '|| currPage * pageSize;  
  strSql:='select * from ('||strSql||') where r > '||(currPage - 1) * pageSize;
  DBMS_OUTPUT.put_line(strSql);
  OPEN v_cur FOR strSql; 
end Pager;

```







# 第十二章 mybatis关联集合操作

### 1.表通常会有以下几种关系

1.一对一：一个人只有一张身份证

2.一对多：一个人可以有多张银行卡

3.多对一：和一对多类似，发起方反向

4.多对多：老师和学生的关系



在mybatis中，我们对一对多，多对一进行了配置



### 2.关联查询

【以县，街道表为例】

1.实体类添加对象

```java
@Data        //街道对象
public class Street {
    private int id;
    private String name;
 				//县对象
    private District district;
}
```



2.在县mapper中添加查询方法

```java
List<District> findById(int id);
```

```xml
<select id="findById" resultType="com.example.demo4.entity.District">
    select * from district where id = #{id}
</select>
```



3.在街道mapper中进行配置

```xml
<resultMap id="StreetMap" type="street">
    <association property="district" column="district_id" javaType="district" select="com.example.demo4.mapper.DistrictMapper.findById" />
</resultMap>
```

association：关联

property：实体中的属性

column：数据库字段

javaType：类对象

select：查询的sql位置

com.example.demo4.mapper.DistrictMapper.findById代表我们从区县映射文件中获取sql





```xml
<select id="find" resultMap="StreetMap">
    SELECT * FROM street
</select>
```







### 3.关联结果

【以县，街道表为例】

1.实体类添加对象

```java
@Data        //街道对象
public class Street {
    private int id;
    private String name;
 				//县对象
     private District district;
}
```



2.在街道mapper中进行配置

```xml
 <resultMap id="StreetMap1" type="street" autoMapping="true">
        <association property="district" javaType="district">
            <result property="id" column="did"></result>
            <result property="name" column="dname"></result>
        </association>
    </resultMap>
```



3.sql语句

```xml
<select id="find1" resultMap="StreetMap1">
        SELECT s.id,s.name,d.id did,d.name dname FROM street s ,district d
        WHERE s.district_id = d.id
</select>
```



4.测试

```java
public class Test2 {
    public static void main(String[] args) {
        ApplicationContext applicationContext = new ClassPathXmlApplicationContext("application.xml");
        StreetMapper streetMapper = (StreetMapper) applicationContext.getBean("streetMapper");
        System.out.println(streetMapper.find1());
    }
}
```

【注：日志记录的两种手法】







### 4.集合查询

查询主表区县数据信息的同时，把街道也查询出来



区县实体类

```java
@Data
public class District {
    private int id;
    private String name;
    private List<Street> list;
}
```



区县mapper接口

```java
List<District> findAll1();
```

街道mapper接口

```java
List<Street> findByDistrictId(int id);
```



区县mapper.xml

```xml
<resultMap id="DistrictMap" type="district" autoMapping="true">
    <result property="id" column="id"></result>
    <collection property="list" ofType="street"  column="id" select="com.example.demo4.mapper.StreetMapper.findByDistrictId"></collection>
</resultMap>

<select id="findAll1" resultMap="DistrictMap">
    select * from district
</select>
```

collection：集合

**ofType**：确定该集合的泛型

column：匹配外键列字段【里面的值是主键】

select：全限定方法名



街道mapper.xml

```xml
<select id="findByDistrictId" resultType="com.example.demo4.entity.Street">
    SELECT * FROM street where district_id = #{id}
</select>
```





### 5.集合结果

区县实体类

```java
@Data
public class District {
    private int id;
    private String name;
    private List<Street> list;
}
```



区县mapper接口

```java
List<District> findAll3();
```

街道mapper接口

```xml
<resultMap id="DistrictMap1" type="district" autoMapping="true">
    
    <collection property="list" ofType="Street">
       <result property="name" column="sname"></result>
    </collection>

</resultMap>

<select id="findAll3" resultMap="DistrictMap1">
     SELECT d.*,s.`name` sname FROM district d,street s
     WHERE d.`id` = s.`district_id`
</select>
```

collection：集合

**ofType**：确定该集合的泛型

result：配置泛型的属性【sql语句查出的值与之对应】





# 第十三章 mybatis的延迟加载

### 1.核心概念

在进行嵌套查询的时候，我们会产生多条sql，这些sql执行的时机我们是可控的，用以提升查询检索效率，这种方式一般在数据持久层都统称为延迟加载策略



在mybatis中我们也可以进行者方面的配置

立即加载：在调用属性前，所有sql都先执行这种方式

延迟加载：在需要的时候sql再进行加载



### 2.实现步骤

**setting配置**

```xml
<setting name="lazyLoadingEnabled" value="true"/>
<setting name="aggressiveLazyLoading" value="false"/>
```

lazyLoadingEnabled：延迟加载的开启，true开启false关闭，关闭时所有关联对象都会立即加载



aggressiveLazyLoading：延迟加载的设置，true立即加载，false所需加载



