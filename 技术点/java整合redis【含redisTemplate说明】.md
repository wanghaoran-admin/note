### springboot整合redis

#### 添加依赖

```xml
<!--redis依赖-->
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-data-redis</artifactId>
</dependency>
```



#### yml

```yml
spring:
  data:
    redis:
      host: 101.132.108.87
      password: whr.11.18
      port: 6379
```



#### 创建配置类

```java
@Configuration
public class RedisConfig {
    @Bean
    public RedisTemplate<String, Object> redisTemplate(RedisConnectionFactory redisConnectionFactory) {
        RedisTemplate<String, Object> template = new RedisTemplate<>();
        template.setConnectionFactory(redisConnectionFactory);
      
        // 设置 key 的序列化方式为 String
        template.setKeySerializer(new StringRedisSerializer());
        // 设置 value 的序列化方式为 Fastjson
        template.setValueSerializer(new FastJsonRedisSerializer<>(Object.class));

        return template;
    }
}
```



#### 使用

```java
@Autowired
private RedisTemplate<泛型, 泛型> redisTemplate;
```







### RedisTemplate方法说明

#### `opsForValue()`

- **功能**：用于操作字符串类型的数据。
- 常用方法
  - `set(K key, V value)`: 设置指定 key 的值。
  - `get(K key)`: 获取指定 key 的值。
  - `delete(K key)`: 删除指定 key。
  - `increment(K key, long delta)`: 增加 key 的值。
  - `multiSet(Map<? extends K, ? extends V> map)`: 批量设置多个 key 的值。







#### `opsForHash()`

- **功能**：用于操作哈希类型的数据。
- 常用方法
  - `put(K key, HK hashKey, V value)`: 向哈希中添加一个项。
  - `get(K key, HK hashKey)`: 获取哈希中指定的项。
  - `delete(K key, Object... hashKeys)`: 删除哈希中的项。
  - `entries(K key)`: 获取整个哈希表。







#### `opsForList()`

- **功能**：用于操作列表类型的数据。
- 常用方法
  - `rightPush(K key, V value)`: 在列表的右端添加一个元素。
  - `leftPush(K key, V value)`: 在列表的左端添加一个元素。
  - `range(K key, long start, long end)`: 获取列表的指定范围的元素。
  - `index(K key, long index)`: 获取列表中指定索引的元素。







#### `opsForSet()`

- **功能**：用于操作无序集合类型的数据。
- 常用方法
  - `add(K key, V... values)`: 向集合中添加元素。
  - `members(K key)`: 获取集合中的所有元素。
  - `isMember(K key, Object o)`: 检查某个元素是否是集合的成员。
  - `remove(K key, Object... values)`: 从集合中删除元素。







#### `opsForZSet()`

- **功能**：用于操作有序集合类型的数据。
- 常用方法
  - `add(K key, V value, double score)`: 向有序集合中添加一个元素及其分值。
  - `range(K key, long start, long end)`: 获取指定范围内的元素。
  - `score(K key, Object o)`: 获取某个元素的分值。

