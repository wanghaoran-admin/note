### 1. **MySQL 基本概念**

- MySQL 是一个流行的关系型数据库管理系统 (RDBMS)，它使用 SQL（结构化查询语言）来进行数据操作。



### 2. **常见数据类型**

- **数值类型**：`INT`、`DECIMAL`、`FLOAT` 等。
- **字符类型**：`VARCHAR`、`CHAR`、`TEXT`。
- **日期类型**：`DATE`、`DATETIME`、`TIMESTAMP`。



### 3. **基本操作**

#### 数据库操作

- 创建数据库：`CREATE DATABASE db_name;`
- 删除数据库：`DROP DATABASE db_name;`
- 选择数据库：`USE db_name;`





#### 表操作

- 创建表：`CREATE TABLE table_name (...);`
- 查看表结构：`DESCRIBE table_name;`
- 删除表：`DROP TABLE table_name;`





#### 数据操作

- 插入数据：`INSERT INTO table_name (columns) VALUES (values);`
- 查询数据：`SELECT columns FROM table_name WHERE conditions;`
- 更新数据：`UPDATE table_name SET column = value WHERE conditions;`
- 删除数据：`DELETE FROM table_name WHERE conditions;`





### 4. **索引**

- **索引的作用**：提高查询效率。
- 常见索引类型
  - **主键索引**：唯一标识每行，使用 `PRIMARY KEY` 创建。
  - **唯一索引**：保证列值唯一，使用 `UNIQUE` 创建。
  - **普通索引**：用于加速查询，使用 `INDEX` 创建。
  - **全文索引**：用于全文检索，使用 `FULLTEXT` 创建。



### 5. **事务**

- **事务特性（ACID）**：原子性、一致性、隔离性、持久性。
- 事务控制语句
  - 开始事务：`START TRANSACTION;`
  - 提交事务：`COMMIT;`
  - 回滚事务：`ROLLBACK;`





### 6. **存储引擎**

- **InnoDB**：支持事务、外键，适合高并发场景。
- **MyISAM**：不支持事务，适合只读和查询密集型应用。





### 7.函数

#### 字符串函数

**CONCAT()**

- **用途**: 将多个字符串连接成一个字符串。

- 示例

  ```
  SELECT CONCAT('Hello', ' ', 'World'); -- 返回 'Hello World'
  ```



**LENGTH()**

- **用途**: 返回字符串的长度（以字节为单位）。

- 示例

  ```
  SELECT LENGTH('Hello World'); -- 返回 11
  ```





**SUBSTRING()**

- **用途**: 从字符串中提取子字符串。

- 示例

  ```
  SELECT SUBSTRING('Hello World', 1, 5); -- 返回 'Hello'
  ```





#### **数值函数**

**ABS()**

- **用途**: 返回数值的绝对值。

- 示例

  ```
  SELECT ABS(-10); -- 返回 10
  ```





**CEIL()**

- **用途**: 向上取整，返回大于或等于指定值的最小整数。

- 示例

  ```
  SELECT CEIL(4.2); -- 返回 5
  ```





**FLOOR()**

- **用途**: 向下取整，返回小于或等于指定值的最大整数。

- 示例

  :

  ```
  SELECT FLOOR(4.7); -- 返回 4
  ```





#### **日期函数**

**NOW()**

- **用途**: 返回当前的日期和时间。

- 示例

  ```
  SELECT NOW(); -- 返回 '2024-09-16 15:30:45'（示例结果）
  ```





**CURDATE()**

- **用途**: 返回当前的日期（不包含时间）。

- 示例

  ```
  SELECT CURDATE(); -- 返回 '2024-09-16'
  ```





**DATE_ADD()**

- **用途**: 在指定日期上添加时间间隔。

- 示例

  ```
  SELECT DATE_ADD('2024-09-16', INTERVAL 10 DAY); -- 返回 '2024-09-26'
  ```







### 9. **用户权限管理**

- 创建用户：`CREATE USER 'username'@'host' IDENTIFIED BY 'password';`
- 授予权限：`GRANT ALL PRIVILEGES ON db_name.* TO 'username'@'host';`
- 刷新权限：`FLUSH PRIVILEGES;`