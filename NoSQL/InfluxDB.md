### 1.**InfluxDB介绍**

InfluxDB 是一个开源的时间序列数据库（TSDB），专门设计用于存储和查询带有时间戳的数据，如监控指标、传感器数据、日志、事件、以及实时分析数据。在时间序列数据处理方面具有高性能和高效性。





### 2. **连接到 InfluxDB**

在命令行或 InfluxDB Shell（`influx` 命令）中连接到 InfluxDB 服务器：

```
influx
```

进入 InfluxDB Shell 后，可以执行各种命令。





### 3. **管理数据库**

##### 显示所有数据库

```
sql
复制代码
SHOW DATABASES
```



##### **创建数据库**

```
CREATE DATABASE <database_name>
```

例如：

```
CREATE DATABASE mydb
```



##### **使用数据库**

```
sql
复制代码
USE <database_name>
```

例如：

```
sql
复制代码
USE mydb
```



##### **删除数据库**

```
DROP DATABASE <database_name>
```

例如：

```
DROP DATABASE mydb
```





### 4. **管理数据表（Measurement）**



##### **显示所有数据表（Measurement）**

```
SHOW MEASUREMENTS
```



##### **删除数据表**

```
DROP MEASUREMENT <measurement_name>
```

例如：

```
DROP MEASUREMENT cpu
```











### 5. **写入数据**

##### 数据表构成

**Measurement Name**: `temperature`   表名

**Tags**: `location=office`, `device=thermometer `  标签能有多个

**Fields**: `value=22.5`  值

**Timestamp**: `1620000000000`  时间戳



数据点的写入遵循 `measurement,tag_key=tag_value field_key=field_value timestamp` 的格式。示例：

```
INSERT cpu,host=server01,region=us-west value=0.64
```

这个命令插入了一个 `cpu` 数据表的数据点，标签为 `host=server01` 和 `region=us-west`，字段值为 `value=0.64`，时间戳为当前时间。







### 6. **查询数据**

##### **查询所有数据**

```
SELECT * FROM <measurement_name>
```

例如：

```
SELECT * FROM cpu
```





##### **查询带有特定条件的数据**

```
SELECT * FROM <measurement_name> WHERE <condition>
```

例如：

```
SELECT * FROM cpu WHERE host='server01'
```







##### **按时间范围查询**

```
SELECT * FROM <measurement_name> WHERE time > now() - 1h
```

例如，查询过去一小时内的数据：

```
SELECT * FROM cpu WHERE time > now() - 1h
```









##### **聚合查询（例如求平均值）**

```
SELECT MEAN(<field_key>) FROM <measurement_name> WHERE <condition> GROUP BY time(<interval>)
```

例如，每 10 分钟计算一次 `cpu` 表中 `value` 字段的平均值：

```
SELECT MEAN(value) FROM cpu GROUP BY time(10m)
```







### 7. **管理用户**

##### **创建用户**

```
CREATE USER <username> WITH PASSWORD '<password>'
```

例如：

```
CREATE USER admin WITH PASSWORD 'adminpass'
```



##### **创建管理员用户**

```
CREATE USER <username> WITH PASSWORD '<password>' WITH ALL PRIVILEGES
```

例如：

```
CREATE USER admin WITH PASSWORD 'adminpass' WITH ALL PRIVILEGES
```



**授予权限**

```
GRANT ALL ON <database_name> TO <username>
```

例如：

```
GRANT ALL ON mydb TO admin
```



##### **撤销权限**

```
REVOKE ALL ON <database_name> FROM <username>
```

例如：

```
REVOKE ALL ON mydb FROM admin
```



##### **删除用户**

```
DROP USER <username>
```

例如：

```
DROP USER admin
```









### 8. **管理保留策略（Retention Policy）**

##### **显示所有保留策略**

```
SHOW RETENTION POLICIES ON <database_name>
```



##### **创建保留策略**

```
CREATE RETENTION POLICY <policy_name> ON <database_name> DURATION <duration> REPLICATION <replication> DEFAULT
```

例如：

```
CREATE RETENTION POLICY "one_day" ON "mydb" DURATION 1d REPLICATION 1 DEFAULT
```



##### **删除保留策略**

```
DROP RETENTION POLICY <policy_name> ON <database_name>
```