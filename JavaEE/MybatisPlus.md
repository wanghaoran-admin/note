# 第一章 Mybatis-plus简介

#### 1.Mybatis-plus是什么?

MyBatis Plus（简称 MyBatis-Plus 或 MP）是一个基于 MyBatis 的增强工具，它简化了 MyBatis 的使用，并提供了许多实用的功能，以提高开发效率。它是对 MyBatis 的一个扩展，提供了诸如自动生成 SQL、通用 CRUD 操作、分页插件、性能分析插件等功能。使用 MyBatis Plus 可以减少开发人员对于繁琐 SQL 编写的工作，提高了代码的简洁性和可维护性。





# 第二章 入门案例

### 1.引入依赖

```xml
<dependency>
    <groupId>com.mysql</groupId>
    <artifactId>mysql-connector-j</artifactId>
</dependency>

<!--    
	mybatis-plus依赖
	这里需要配置3.5.2以上的版本    
-->
<dependency>
    <groupId>com.baomidou</groupId>
    <artifactId>mybatis-plus-boot-starter</artifactId>
    <version>3.5.3</version>
</dependency>

<dependency>
    <groupId>com.alibaba</groupId>
    <artifactId>druid</artifactId>
    <version>1.2.13</version>
</dependency>
```





### 2.配置数据源

```yml
spring:
  datasource:
    driver-class-name: com.mysql.cj.jdbc.Driver
    url: jdbc:mysql://localhost:3306/mybatis_plus
    username: root
    password: ok


mybatis-plus:
  configuration:
    log-impl: org.apache.ibatis.logging.stdout.StdOutImpl
```



### 3.配置扫描mapper

```java
//这里的路径是mapper包的路径
@MapperScan("com.example.demo.mapper")
@SpringBootApplication
public class DemoApplication {

    public static void main(String[] args) {
        SpringApplication.run(DemoApplication.class, args);
    }
}
```



### 4.继承baseMapper

```java
public interface UserMapper extends BaseMapper<User> {
}
```



### 5.调用测试

```java
@SpringBootTest
class DemoApplicationTests {
    @Autowired
    private UserMapper userMapper;

    @Test
    void contextLoads() {
        userMapper.selectList(null).forEach(System.out::println);
    }
}
```



# 第三章 baseMapper类的详解

### 1.baseMapper介绍

BaseMapper 接口提供了一些通用的方法，包括常见的增删改查操作。这些方法包括插入数据、根据主键删除数据、根据主键更新数据、根据主键查询数据、查询所有数据等



### 2.baseMapper方法说明

```java
//
// Source code recreated from a .class file by IntelliJ IDEA
// (powered by FernFlower decompiler)
//

package com.baomidou.mybatisplus.core.mapper;

import com.baomidou.mybatisplus.core.conditions.Wrapper;
import com.baomidou.mybatisplus.core.metadata.IPage;
import java.io.Serializable;
import java.util.Collection;
import java.util.List;
import java.util.Map;
import org.apache.ibatis.annotations.Param;
import org.apache.ibatis.exceptions.TooManyResultsException;

public interface BaseMapper<T> extends Mapper<T> {
    /**
     * 插入一条记录
     * @param entity 实体对象
     */
    int insert(T entity);

    /**
     * 根据 ID 删除
     * @param id 主键ID
     */
    int deleteById(Serializable id);

    /**
     * 根据实体(ID)删除
     * @param entity 实体对象
     * @since 3.4.4
     */
    int deleteById(T entity);

    /**
     * 根据 columnMap 条件，删除记录
     * @param columnMap 表字段 map 对象
     */
    int deleteByMap(@Param(Constants.COLUMN_MAP) Map<String, Object> columnMap);

    /**
     * 根据 entity 条件，删除记录
     * @param queryWrapper 实体对象封装操作类（可以为 null,里面的 entity 用于生成 where语句）
     */
    int delete(@Param(Constants.WRAPPER) Wrapper<T> queryWrapper);

    /**
     * 删除（根据ID 批量删除）
     * @param idList 主键ID列表(不能为 null 以及 empty)
     */
    int deleteBatchIds(@Param(Constants.COLLECTION) Collection<? extends
            Serializable> idList);

    /**
     * 根据 ID 修改
     * @param entity 实体对象
     */
    int updateById(@Param(Constants.ENTITY) T entity);

    /**
     * 根据 whereEntity 条件，更新记录
     * @param entity        实体对象 (set 条件值,可以为 null)
     * @param updateWrapper 实体对象封装操作类（可以为 null,里面的 entity 用于生成where 语句）
     */
    int update(@Param(Constants.ENTITY) T entity, @Param(Constants.WRAPPER) Wrapper<T> updateWrapper);

    /**
     * 根据 ID 查询
     * @param id 主键ID
     */
    T selectById(Serializable id);

    /**
     * 查询（根据ID 批量查询）
     * @param idList 主键ID列表(不能为 null 以及 empty)
     */
    List<T> selectBatchIds(@Param(Constants.COLLECTION) Collection<? extends Serializable> idList);

    /**
     * 查询（根据 columnMap 条件）
     * @param columnMap 表字段 map 对象
     */
    List<T> selectByMap(@Param(Constants.COLUMN_MAP) Map<String, Object> columnMap);

    /**
     * 根据 entity 条件，查询一条记录
     * <p>查询一条记录，例如 qw.last("limit 1") 限制取一条记录, 注意：多条数据会报异常</p>
     * @param queryWrapper 实体对象封装操作类（可以为 null）
     */
    default T selectOne(@Param(Constants.WRAPPER) Wrapper<T> queryWrapper) {
        List<T> ts = this.selectList(queryWrapper);
        if (CollectionUtils.isNotEmpty(ts)) {
            if (ts.size() != 1) {
                throw ExceptionUtils.mpe("One record is expected, but the query result is multiple records");
            }
            return ts.get(0);
        }
        return null;
    }

    /**
     * 根据 Wrapper 条件，查询总记录数
     * @param queryWrapper 实体对象封装操作类（可以为 null）
     */
    Long selectCount(@Param(Constants.WRAPPER) Wrapper<T> queryWrapper);

    /**
     * 根据 entity 条件，查询全部记录
     * @param queryWrapper 实体对象封装操作类（可以为 null）
     */
    List<T> selectList(@Param(Constants.WRAPPER) Wrapper<T> queryWrapper);

    /**
     * 根据 Wrapper 条件，查询全部记录
     * @param queryWrapper 实体对象封装操作类（可以为 null）
     */
    List<Map<String, Object>> selectMaps(@Param(Constants.WRAPPER) Wrapper<T> queryWrapper);

    /**
     * 根据 Wrapper 条件，查询全部记录
     * <p>注意： 只返回第一个字段的值</p>
     * @param queryWrapper 实体对象封装操作类（可以为 null）
     */
    List<Object> selectObjs(@Param(Constants.WRAPPER) Wrapper<T> queryWrapper);

    /**
     * 根据 entity 条件，查询全部记录（并翻页）
     * @param page         分页查询条件（可以为 RowBounds.DEFAULT）
     * @param queryWrapper 实体对象封装操作类（可以为 null）
     */
    <P extends IPage<T>> P selectPage(P page, @Param(Constants.WRAPPER) Wrapper<T> queryWrapper);

    /**
     * 根据 Wrapper 条件，查询全部记录（并翻页）
     * @param page         分页查询条件
     * @param queryWrapper 实体对象封装操作类
     */
    <P extends IPage<Map<String, Object>>> P selectMapsPage(P page, @Param(Constants.WRAPPER) Wrapper<T> queryWrapper);
}
```





### 3.baseMapper方法示例

#### selectList()

查询全部

```java
@Test
     void selectList() {
         //查询全部
         userMapper.selectList(null).forEach(System.out::println);
     }
```



#### insert()

添加

```java
@Test
    public void deleteById(){
        	//添加
          //INSERT INTO user ( id, name, age, email ) VALUES ( ?, ?, ?, ? )
          User user = new User(null, "张三", 23, "zhangsan@atguigu.com");
          int result = userMapper.insert(user);
          System.out.println("受影响行数："+result);
          System.out.println("id自动获取："+user.getId());
    }
```





#### deleteById

通过id删除用户信息

```java
@Test
    public void deleteById(){
        //通过id删除用户信息
        //DELETE FROM user WHERE id=?
        int result = userMapper.deleteById("1769640848204554242");
        System.out.println("受影响行数："+result);
    }
```





#### **deleteBatchIds()**

通过多个id批量删除

```java
@Test
    public void deleteBatchIds(){
        //通过多个id批量删除
        //DELETE FROM user WHERE id IN ( ? , ? , ? )
        List<Long> idList = Arrays.asList(4L, 5L);
        int result = userMapper.deleteBatchIds(idList);
        System.out.println("受影响行数："+result);
    }
```





#### deleteByMap()

根据map集合中所设置的条件删除记录

```java
@Test
    public void deleteByMap(){
        //根据map集合中所设置的条件删除记录
        //DELETE FROM user WHERE name = ? AND age = ?
        Map<String, Object> map = new HashMap<>();
        map.put("age", 20);
        map.put("name", "Jack");
        int result = userMapper.deleteByMap(map);
        System.out.println("受影响行数："+result);
    }
```





#### updateById()

根据id修改

```java
 @Test
    public void updateById(){
        //根据id修改
        //UPDATE user SET name=?, age=? WHERE id=?
        User user = new User(3L, "admin", 22, null);
        int result = userMapper.updateById(user);
        System.out.println("受影响行数："+result);
    }
```



#### SelectById()

根据id查询用户信息

```java
 @Test
    public void testSelectById(){
        //根据id查询用户信息
        //SELECT id,name,age,email FROM user WHERE id=?
        User user = userMapper.selectById(1L);
        System.out.println(user);
    }
```



#### SelectBatchIds()

根据多个id查询多个用户信息

```java
@Test
    public void testSelectBatchIds(){
        //根据多个id查询多个用户信息
        //SELECT id,name,age,email FROM user WHERE id IN ( ? , ? )
        List<Long> idList = Arrays.asList(1L, 3L);
        List<User> list = userMapper.selectBatchIds(idList);
        list.forEach(System.out::println);
    }
```



#### SelectByMap()

通过map条件查询用户信息

```java
@Test
    public void testSelectByMap(){
        //通过map条件查询用户信息
        //SELECT id,name,age,email FROM user WHERE name = ? AND age = ?
        Map<String, Object> map = new HashMap<>();
        map.put("age", 22);
        map.put("name", "admin");
        List<User> list = userMapper.selectByMap(map);
        list.forEach(System.out::println);
    }
```







# 第四章 通用Service

### 1.IService介绍

“IService”接口作为基础服务接口，用于**提供**对实体的**常见CRUD（创建、读取、更新、删除）**操作。它提供了一组方便的方法来与数据库表进行交互，而无需为基本操作编写自定义 SQL 查询



### 2.通用Service使用

```java
@Service
public interface UserService extends IService<User> {

}
```



```java
@Service
public class UserServiceImpl extends ServiceImpl<UserMapper, User> implements UserService {
	
}
```





### 3.IService方法示例

#### save(T entity)

插入一条记录。

```java
@Test
void save() {
    // 添加一个新用户
    User user = new User();
    user.setName("张三");
    user.setAge(23);
    user.setEmail("zhangsan@example.com");
    boolean result = userService.save(user);
    System.out.println("插入结果：" + result);
}
```



#### saveBatch(Collection<T> entityList)

批量插入记录。

```java
@Test
void saveBatch() {
    // 批量添加用户
    List<User> users = Arrays.asList(
        new User("Alice", 30, "alice@example.com"),
        new User("Bob", 24, "bob@example.com")
    );
    boolean result = userService.saveBatch(users);
    System.out.println("批量插入结果：" + result);
}
```



#### saveOrUpdate(T entity)

根据实体类的主键值判断插入或更新记录。

```java
@Test
void saveOrUpdate() {
    // 如果用户存在则更新，不存在则添加
    User user = new User();
    user.setId(1L); // 假设ID为1的用户已经存在
    user.setName("张三更新");
    user.setAge(24);
    boolean result = userService.saveOrUpdate(user);
    System.out.println("保存或更新结果：" + result);
}
```



#### removeById(Serializable id)

根据 ID 删除记录。

```java
@Test
void removeById() {
    // 根据ID删除用户
    boolean result = userService.removeById(1L);
    System.out.println("删除结果：" + result);
}
```



#### updateById(T entity)

根据 ID 修改记录。

```java
@Test
void updateById() {
    // 根据ID更新用户信息
    User user = new User();
    user.setId(1L);
    user.setName("张三更新");
    user.setAge(25);
    boolean result = userService.updateById(user);
    System.out.println("更新结果：" + result);
}
```



#### getById(Serializable id)

根据 ID 查询记录。

```java
@Test
void getById() {
    // 根据ID查询用户
    User user = userService.getById(1L);
    System.out.println("查询结果：" + user);
}
```



#### list()

查询所有记录。需要在实体上添加@TableId注解

```java
@Test
void list() {
    // 查询所有用户
    List<User> users = userService.list();
    users.forEach(System.out::println);
}
```



#### page(IPage<T> page, QueryWrapper<T> queryWrapper)

根据分页对象和包装条件分页查询记录。

```java
@Test
void page() {
    // 分页查询用户，第一页，每页显示5条数据
    Page<User> page = new Page<>(1, 5);
    QueryWrapper<User> queryWrapper = new QueryWrapper<>();
    queryWrapper.eq("age", 25); // 添加条件
    IPage<User> userPage = userService.page(page, queryWrapper);
    userPage.getRecords().forEach(System.out::println);
}
```



【使用分页需要配置以下代码】

```java
package org.example.dome.utils;

import com.baomidou.mybatisplus.annotation.DbType;
import com.baomidou.mybatisplus.extension.plugins.MybatisPlusInterceptor;
import com.baomidou.mybatisplus.extension.plugins.inner.OptimisticLockerInnerInterceptor;
import com.baomidou.mybatisplus.extension.plugins.inner.PaginationInnerInterceptor;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;

@Configuration
public class MyBatisPlusConfig {

    /**
     * 插件配置
     *
     * @return
     */
    @Bean
    public MybatisPlusInterceptor mybatisPlusInterceptor() {
        MybatisPlusInterceptor interceptor = new MybatisPlusInterceptor();

        // 向MyBatis-Plus的过滤器链中添加分页拦截器，需要设置数据库类型（主要用于分页方言）
        interceptor.addInnerInterceptor(new PaginationInnerInterceptor(DbType.POSTGRE_SQL));
//        interceptor.addInnerInterceptor(new PaginationInnerInterceptor(DbType.MYSQL));
        // 添加乐观锁拦截器
        interceptor.addInnerInterceptor(new OptimisticLockerInnerInterceptor());
        return interceptor;
    }
}

```









# 第五章 时间处理器

### 1.实体属性配置

```java
@TableField(fill = FieldFill.INSERT)
private Date createTime;
```



### 2.创建处理器

```java
package com.it.wang.Handle;

import com.baomidou.mybatisplus.core.handlers.MetaObjectHandler;
import org.apache.ibatis.reflection.MetaObject;
import org.springframework.stereotype.Component;
import java.time.LocalDateTime;
import java.util.Date;

@Component
public class DateHandler implements MetaObjectHandler {

    @Override
    public void insertFill(MetaObject metaObject) {
        //这里createTime和实体属性对应
        this.setFieldValByName("createTime", new Date(), metaObject);
        this.setFieldValByName("updateTime", new Date(), metaObject);
    }

    @Override
    public void updateFill(MetaObject metaObject) {
        this.setFieldValByName("updateTime", new Date(), metaObject);
    }

}

```







# 第六章 逻辑删除

### 1.配置yml

```yml
# mybatis-plus相关配置
mybatis-plus:
  # 全局配置
  global-config:
    db-config:
      # 全局逻辑删除的字段名
      logic-delete-field: deleted 
      # 逻辑已删除值(默认为 1)
      logic-delete-value: 1
      # 逻辑未删除值(默认为 0)
      logic-not-delete-value: 0
```





### 2.实体配置

```java
@TableLogic
private Integer deleted;
```
