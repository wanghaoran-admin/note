# 第一章 redis基本概念

### 1.相关概念

Redis是一个开源的内存数据库，它通常被用作缓存、消息队列或者数据存储。它支持多种数据结构，包括字符串、哈希表、列表、集合、有序集合等。



### 2.环境搭建

redis通常可以在linux和window等多种操作系统上使用，本章主要以windows系统环境进行讲解



1.下载redis

2.启动服务

```java
redis-server.exe redis.windows.conf
```

携带windows的配置文件

【注意：服务器界面不能退出删除】



3.启动客户端

```
redis-cli.exe
```



4.进行授权

```
auth password
```

ok是在配置文件中提前设置好的密码【requirepass ok】



# 第二章 数据类型以及对应指令

### 1.string 类型

string类型是redis中最常见的数据类型，有如下指令

```java
set name wang //设置值
get name	//取值
ket * //查看匹配的key[*:全部]
exists name //判断是否存在【有1,无0】
append name word //进行追加
strlen name //获取长度    
incr num //给数值num做+1操作
incrby num 10 //给数值num做+操作，步长为10,一次加量为10     
decr num //给数值num做-1操作
decrby num 10 //给数值num做-操作，步减为10,一次减量为10      
getrange name 2 4 //截取字符串
setrange name 1 xxx //替换内容 1是从哪里开始
setex tel 10 14444444441111 //set+expire的作用【给tel设置10秒有效期，值为14444444441111，10秒后get查询为null】
setnx mykey redis //set存入前先进行mykey的判断，没有就存入redis这个值，有返回0，不进行存入值     
mset name wang age 18 //批量设置key
mget name age //批量获取key
msetnx address xuzhou email wang@qq.com //批量进行设置
getset db redis //先get取值,在set赋值，如果是第一次调用，返回nil
del db mykey //删除key
```



### 2.hash类型

hash类似于java中的map , 由key-fieId-value构成，通过我们也称为大key 小key，一个大key下可以设置多个小key

```java
hset myhash name wang //给名为myhash的大key中添加小key name 值为wang
hget myhash name //根据大key+小key结合的方式，取出值zhangsan
hmset myhash name lisi age 18 //多组添加【小key+值】
hgetall myhash //查询这个大key中所有的【小key+值】
hdel myhash address //删除大key中     
hexists myhash name //判断大key中，小key是否存在
hkeys myhash //查询大key中所有的key
hvals myhash //查询大key中所有的value
hincrby myhash ahe 1 //对小key的值进行添加操作【注：这个小key值是数值型的，跟的值为负值，即减少操作】
hsetnx myhash name lisi //在大可以中增加一组小key之前进行判断，如果有了这个小key,就无法增加     
```





### 3.list 类型

list列表【链表】类型，类似于java中arraylist,可重复的

```java
lpush mylist one //从原值的左边进行新值插入
rpush mylist two //从原值的右边进行新值插入
lrange mylist 0 -1 //查询所有的内容
lpop list //从左边开始移出一个值
rpop list //从右边开始移出一个值
lindex list 1 //取出对应下标的值，没有数据返回nil
lrem list 3 aa //移出3个aa值
ltrim list 0 -1 //截取该下标的数据 区间内没有数据集合为空
rpoplpush list list2 //从集合右边取出数据并插入list2的最左边  
lset mylist 0 good //对指定下标进行修改操作
linsert mylist before a1 good //在mylist集合中a1 key之前插入新数据 没有返回-1
linsert mylist after a1 good //在mylist集合中a1 key之后插入新数据 没有返回-1
```







### 4.set 类型

set集合类型，类似于java中的set，值不能重复

```java
sadd myset "hello"  //set集合中添加元素 重复会覆盖掉
smembers myset //查看指定set的所有数据
sismember myset "hello" //判断某一个值是否在set集合中
scard myset  //获取set集合中的个数
srem myset "hello" //移除set集合中的指定元素
srandmember myset //随机抽选出一个元素
srandmember myset 2 //随机抽选指个数元素
spop myset //一些set集合中的元素
smove myset myset2 "jiabin" //将一个指定的值，移动到另外一个set集合中！   
sinter key1 key2 //交集共同好友就可以实现
sunion key1 key2 //并集 获取key1 key2的公共值
sdiff key1 key2  //差集补集 获取公共值以外的key1剩余值，顺序不同结果也会发送改变
```





### 5.zset 类型

zset可以看成是对set的进阶，可以进行有序排序，在每个值之前加入一个分数值【score】

```java
zadd myzset 1 one 2 two 3 three //存入数据并且给没个值匹配了一个分数值
zrange myzset 0 -1 //查询所有
zrangebyscore myzset -inf +inf //显示用户 从小到大 也可以写具体分值区间【200,300】
zrangebyscore myzset -inf +inf withscores //显示全部用户并且携带分数值
zrangebyscore myzset -inf 3500 withscores //显示工资小于3500的并且升序
zrem myzset xiaohong //移除有序集合中的指定元素
zcount myzset 1 2 //获取对应区间的数量
zcard myzset //获取集合中的数量
```





# 第三章 事务

### 1.正常执行事务

```java
MULTI //开启事务
set k1 v1 //存放数据
set k2 v2
exec //执行事务
```



### 2.放弃事务

```java
MULTI //开启事务
set k1 v1 //存放数据
set k2 v2
DISCARD //取消事务
get k2 //事务队列中的命令都不会执行
(nil)
```







# 第四章 jedis操作

### 1.使用

开启redis服务器

在java环境下引入依赖

```xml
<dependency>
   <groupId>redis.clients</groupId>
   <artifactId>jedis</artifactId>
</dependency>
```







在java环境下获取连接

```java
Jedis jedis = new Jedis("localhost",6379);
jedis.auth("ok");
jedis.close();
```



```java
//使用jedis.【命令】的方式来进行操作 
//示例：
String ping = jedis.ping(); //测试连接
jedis.set("name","王浩然"); //设置string类型数值
jedis.lpush("color","红","橙","黄","绿","青"); //设置集合
jedis.lrange("color",0,-1).forEach(System.out::println); //获取并输出
jedis.sadd("names","张三","李四","lili"); //设置set集合

//设置zset
jedis.zadd("scores",1,"one");
jedis.zadd("scores",3,"22323");
jedis.zadd("scores",4,"444");
jedis.zadd("scores",2,"646");

//分值区间查询
jedis.zrangeByScore("scores",1,3).forEach(System.out::println);

//携带分值区间查询
jedis.zrangeByScoreWithScores("scores",1,3).forEach(System.out::println);

//设置hash
Map map= new HashMap();
map.put("name","wang");
map.put("age","13");
map.put("address","xhuzhou");
jedis.hmset("stu",map);

//查询这个大key中所有的【小key+值】
Map<String, String> stu = jedis.hgetAll("stu");
System.out.println(stu.toString());
```





### 2.工具类

```java
package com.example.demo3.utils;

import redis.clients.jedis.Jedis;
import redis.clients.jedis.JedisPool;
import redis.clients.jedis.JedisPoolConfig;

public class JedisUtil {
    private static JedisPoolConfig config;
    private static JedisPool pool;

    static {
        config = new JedisPoolConfig();
        config.setMaxTotal(30);
        config.setMaxIdle(2);
        pool = new JedisPool(config, "127.0.0.1", 6379 , 60, "ok");
    }

    public static Jedis getJedis() {
        return pool.getResource();
    }

    public static void closeJedis(Jedis j) {
        j.close();
    }
}

```



### 3.存取数据

```java
Jedis jedis = JedisUtil.getJedis();
   if (jedis.exists("list")) {
      System.out.println("从redis中取数据");
      String s = jedis.get("list");
      List<Faqs> faqs = JSON.parseArray(s, Faqs.class);
      return new Result(faqs,200,"获取成功！");
   }else{
      System.out.println("从数据库中取数据");
      List<Classes> list = classesService.list();
      jedis.setex("list",30,JSON.toJSONString(list));
      return new Result(list,200,"获取成功！");
   }
```





### 4.综合应用

通常我们把数据库中不常更新的表进行mysql+redis的组合查询，比如商品类型，年级信息，产地信息











