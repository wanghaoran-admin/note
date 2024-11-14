# 	第一章 oracle基础

### 1.oracle基本概念

oracle是一款数据库操作系统，当下最流行的数据库操作系统，国内以甲骨文命名，核心由物理和逻辑两部分构成



### 2.oracle构成

物理构成：体现为数据库【磁盘中永久保存】，由各种文件构成

​		数据文件：.dbf

​		控制文件：.ctl 【二进制文件】

​		日志文件：.log

逻辑构成：数据库实例【内存上临时保存】 表空间方式呈现



### 3.oracle安装

推荐装在非系统盘，主机名不使用中文

安装后是以c/s架构,由服务器端和客户端之分

在服务器配置监听

在客户端配置本地网络

oracle最主要的服务有两个，一个是监听服务，一个是数据库服务

【注意：oracle的端口为1521，oracle启动比较消耗资源，推荐使用手动启动的方式】



### 4.oracle登录用户

oracle连接是需要用户登录的，常用用户如下：

​		sys:超级管理员，以sysdba,sysoper身份登录

​		system:系统管理员，以nomal身份登录

​		scott:内置了四张基本数据库表【dept,emp】

【注意：在安装oracle中没有解锁scott，需要命令行解锁】

```mysql
解锁用户
alter user scott account unlock;
给用户绑定密码
alter user scott identified by ok;
```



### 5.oracle数据类型 

字符类型

char,varchar2,Unicode编码的nchar,nvarchar2



数值类型

number既可以表示整数，也可以表示小数

number（p,s）：p代表长度    s代表小数



日期类型

date:日期时间

timestarmp:秒后面的精度6位



LOB类型

BLOB：二进制大对象

CLOB：二进制大文本

BFILE：存储的外部文件地址



伪劣

不在表中直接存储的表列，对于检索查询表数据有很大的性能提升		

​		rowid：数据行地址

​		rownum：动态获取数据行的序号

```sql
select * from(select d.*,rownum as r from scott.dept d) 
where r = 3
想要获取第三条数据 要进行固化 使用子查询完成
```



### 6.sql语言分类

数据定义语言【DDL】

create  alter  drop

数据操作语言【DML】

insert  update  delete  select

事务控制语言【TCL】

commit  rollback  

数据控制语言【DCL】

grant  revoke



### 7.oracle约束

数据完整性

数据表数据录入的正确性，正确性

完整性分类：

​		实体完整性：行的操作

​		域完整性：列字段操作

​		引用完整性：表之间的主外键操作

​		自定义完整性：存储过程，触发器登实现

​		约束基本语法

```sql
alter table 表名 add constraints 约束名 约束类型
```

```sql
添加主键
alter table stuMarks add constraints sm1 primary key(stuNo)

添加约束
alter table stuinfo add constraints ch1 check(stuAge between 4 and 40)

添加外键
alter table stumarks
add constraints fk1 foreign key(stuNo)
references stuinfo(stuNo)
```

**约束定义的位置**

1.直接跟在字段的后面

```sql
stuNo number(3) primary key;
```

2.跟在当前表中，所有字段定义之后

```sql
create table info(
	id number(4) not null;
	primary key(id)
)
```

3.直接放置在表的外面，使用alter语句进行定义

【注意：前两种约束，系统会自动进行命名】



### 8.oracle DML语句

1.去重复项

使用distinct进行重复行数据的合并【多行显示为一行】

使用group by进行分组实现同样的效果



2.常规查询的位次

select 查询字段 from 表

where 基本筛选条件

group by 分组条件

having 分组后的再筛选【分组后筛选】

order by 排序【默认升序 asc 和desc 降序， 同时可以对多个列进行排序】



3.别名可以对查询的表直接表设置别名

也可以对一个查询结果整体进行别名设置

还可以对查询的字段进行别名设置

有了别名就可以使用.  操作符进行快速检索字段，区分不同表的同名字段as关键字可以省略

如果别名中有特殊字符，需要使用""字符进行识别

```sql
select d.deptno 部门编号 
from scott.dept d
```



4.利用当前已知的表创建新表

```sql
create table emp1124 
as select * from scott.emp;
```



5.聚会函数

sum（字段） 总数据和

min（字段） 最小值

max（字段） 最大值

count（1） 总数总条数

avg（字段） 平均数

【注意：分组后显示后的列不能是普通列，而是分组后的列或聚会函数】



6.rowid的使用案例

使用rowid伪劣关键字完成数据筛选，去掉重复的数据【保留一行】

```sql
delete from stuinfo where rowid not in(
	select max(rowid)
	from stuinfo s
	group by s.stuname,s.stuage
) 
```



### 9.oracle TCL语句

orcale默认的增删改语句不提交

commit：提交

rollback：回滚

savepoint：存档点

rollback to savepoint xx：回到xx存储点

```sql
--插入数据
insert into dept1123 values(50,'java','开发部');
insert into dept1123 values(60,'c#','开发部');

--设置保存点
savepoint a;

--在插入一条
insert into dept1123 values(70,'php','开发部');

--回滚到存储点
rollback to savepoint a;

--没有70数据
commit;
```



### 10.oracle 分页

在我们所学习的数据库中，各自使用不同的方法进行分页

sqlserver:使用top结合not in子查询进行分页

```sql
select top 5 * from info 
where id not in (select 10 id from info)
```

myslq:使用limit进行分页

```sql
select * from info limit 0,5
```

oraclet:使用rownum伪例进行，三层的分页

```sql
select * from (
       --完成动态rownum的固化以及上限结束位置的处理
       select e.*,rownum r from(
              --完成条件筛选 排序 表连接 模糊查登....
              select * from emp1124 order by sal desc
       ) e where rownum<=9--数据的分页行数【上限】
)where r>6--数据的分页页数【下限】
```



### 11.oracle 函数

单行函数

每一行只返回一个结果



1.字符函数

```sql
select 
     lower('ABCDE')转小写,
     upper('jfhf')转大写,
     instr('helloworld','ow')查找位置从1开始,
     substr('helloworld',3,4)截取,
     concat('hello','world123')连接字符
from
dual;
```

【注意：orcale默认只有两个参数，进行连接，如果需要连接多个值，可以嵌套使用concat或者直接使用||连接符】



2.数字函数

```sql
select 
     ceil(10.01)向上取整,
     floor(10.01)向下取整,
     mod(10,3)取余,
     round(10.36,1)四舍五入,
     round(10.56,1)四舍五入2,
     round(16.56,-1)四舍五入3,
     round(56.56,-2)四舍五入4
from
dual;
```



3.日期函数

```sql
select
     sysdate 系统默认时间,
     last_day(sysdate),
     last_day('19-8月-2023') 当月最后一天2,
     months_between('19-8月-2023','18-11月-2015') 日期月份差,
     round(sysdate,'year') 四舍五入年
from dual;
```

【注意：orcale默认日期字符串格式为（24-11月-2022）】



4.转换函数

```sql
select 
     sysdate,
     to_char(sysdate,'yyyy-mm-dd') 日期转字符串,
     to_char(sysdate,'yyyy"年"mm"月"dd"日" hh24:mi:ss') 日期转字符串2,
     to_char(sysdate,'yyyy')转年,
     to_char(sysdate),
     to_char(sysdate)
from dual;
```



5.其他函数

判断并转换【空值，判断】

--nvl：根据第一个值的非空进行填充赋值

--空就显示第二个值



--nvl2：根据第一个值的非空进行填充赋值

--空就显示第三个值   不空就显示第二个



--decode：等值判断，有点像switch

```
select 
     nvl(null,'你好')转换1,
     nvl2('xxx','hello','hi')转换2,
     decode(1,1,'男',2,'女','错误')转换3
from dual;
```

例：

```
select
	e.*,
	lower(e.ename) 姓名小写,
	e.ename || e.job拼接,
	nvl2(e.ename)
```



6.分析函数

分析函数可以根据一组进行聚合值的计算，并且可以给其他返回多个行【这是普通分组group by 无法直接实现】

有如下三种分析函数，具体用法如下

--rank相同的值排位相同，随后跳跃

--dense_rank相同值排位相同，随后连续显示，值不跳跃

--row_number直接返回联系排位，不论值是否相同

```sql
select 
e.empno,
e.ename,
e.sal,
e.deptno,
rank()over(partition by e.deptno order by e.sal desc), --rank相同的值排位相同，随后跳跃
dense_rank()over(partition by e.deptno order by e.sal desc), --dense_rank相同值排位相同，随后连续显示，值不跳跃
row_number()over(partition by e.deptno order by e.sal desc) --row_number直接返回联系排位，不论值是否相同
from emp1124 e;
```



### 12.oracle 子查询

所谓子查询，就是一个查询包含到另一个查询中的查询方式

他可以在查询语句的任意位置出现【条件区域，显示列区域，查询结果区域】



子查询执行时机，先后顺序

```sql
--【查询姓名包含s的用户】
--关系运算符【=,>,<>....】进行条件子查询子查询，最多返回一行数据
--有多行数据返回，使用in,exists
--执行顺序:先查询获取子查询的结果，再在父查询中进行二次查询
select * from emp1126
where deptno in (
      select deptno from emp1126 where ename like '%S%'
)
```

```sql
--【查询比同部门平均工资高的员工信息】
--子查询中包含了父查询的条件
--执行顺序【类似双重循环，外层一次，内层一轮】
--父查询把第一行数据的查询条件传递到子查询中进行匹配
--例：外层deptno = 10 内层的查询就会根据外层的 10进行全部数据的匹配（分组）
select * from emp1125 e
where e.sal >(
      select avg(e1.sal) from emp1125 e1
      where e.deptno = e1.deptno
)
order by deptno
```

```sql
--【查询薪资大于等于3000的部门名称】
--使用exists 代替in子查询
--不需要内层子查询返回具体的字段值
--整体有查询结果就为true,不匹配就返回false不呈现父查询结果
select d.dname from dept1125 d
where exists (
      select * from emp1126 e 
      where d.deptno = e.deptno
      and e.sal >=3000
)
```



### 13.oracle 表连接

连接查询：合并列的多表间的查询

分为内连接，外连接，自连接等



**内连接：**

【主表a记录10条，从表b记录5条】

以从表外键列的值进行匹配，查询5条记录

有where和join...on两种写法

```sql
select * from dept1125 d,emp1125 e
where d.deptno = e.deptno
```

```sql
select * from dept1125 d
inner join emp1125 e
on d.deptno = e.deptno
```

除了等值查询，还有非等值查询【匹配条件从=换成<>】

```sql
select * from dept1125 d,emp1125 e
where d.deptno <> e.deptno
```



**自连接：**

所谓自连接，就是对一张表进行多次连接匹配，需要表中设计特色的字段列

【查询员工姓名以及直属领导的姓名】

```sql
select e.*,(select ename from emp1126 e1 
where e.mgr = e1.empno) 
from emp1126  e
```



**外连接：**

外连接查询以主表为基准，向从表发起查询

匹配的数据正常显示

从表不匹配主表的数据，从表列以null进行显示

外连接有左右外之分，本质是一样的

**左外的左边表，右外的右边表必须是主表，一旦位置反了，就和内连接一样了**

```sql
--左外连接【左边是主表，一旦位置交换，就是内连接】
select * from emp1126 e
left join emp1126 m
on e.mgr = m.empno
```

```sql
--右外连接【右边是主表吗，一旦位置交换，就是内连接】
select * from emp1126 m
right join  emp1126 e
on e.mgr = m.empno
```



### 14, oracle 联合查询

联合查询：使用集合操作符进行多表合并数据行的查询

**并集查询：**

union【去重复数据行】

```sql
--并集查询union【去重复数据行】
select d.deptno
from dept1126 d
union 
select e.deptno
from emp1126 e
```

union all【不去重复项】

```sql
--并集查询union all【不去重复项】
select d.deptno
from dept1126 d
union all
select e.deptno
from emp1126 e
```



**交集查询：**

intersect【获取公共项】

```sql
--交集查询intersect【获取公共项】
select d.deptno
from dept1126 d
intersect
select e.deptno
from emp1126 e
```



**补集查询：**

minus【除去交集公共项之后剩余的项】

```sql
--补集查询minus
--【除去交集公共项之后剩余的项，minus前的查询项数据要更多点】
select d.deptno
from dept1126 d
minus
select e.deptno
from emp1126 e
```



**联合查询的公共事项：**

```sql
--1.各自查询的字段类型要一致
--2.各自查询的字段数量要一致
select d.dname,d.loc
from dept1126 d
minus
select e.deptno
from emp1126 e
```





# 第二章 oracle对象

### 1.oracle表空间

oracle表空间是oracle数据库体系的逻辑体现

开创了一种新的数据存储划分的手段

通常一个表空间下有1-多个数据文件，某一个数据文件只从属于一个表空间



1.创建表空间

```sql
--创建名为tb1的表空间
--物理文件位置'd:/表空间/tb1.dbf' 
--文件大小10m 自动扩展
create tablespace tb1
datafile 'd:/表空间/tb1.dbf'
size 10m
autoextend on;
```

2.修改表空间大小

```sql
--2.1 修改文件大小
alter database 
datafile 'd:/表空间/tb1.dbf'
resize 20m;

--2.2 添加一个新的数据文件
alter tablespace tb1
add datafile 'd:/表空间/tb2.dbf'
size 10m
autoextend on;
```

3.删除表空间

```sql
--删除【包含内容和数据文件】
--datafiles:物理文件
--contents:内容
--cascade constraints:关联关系
drop tablespace  tb1
including contents and datafiles 
cascade constraints;
```

4.查看全部表空间

```sql
SELECT * 
FROM DBA_TABLESPACES;
```



### 2.oracle用户

oracle数据库系统是需要用户验证登录后才能执行相关操作

由于oracle环境通常只创建一个数据库，不同的应用项目在登录后可能会出现冲突，为了方便管理，我们可以使用不同的用户管理不同的表空间进行优化

1.创建用户

```sql
--用户创建至少给定用户名密码【用户默认表空间是users】
create user wanghaoran
identified by ok;

--查看全部用户
SELECT username
FROM dba_users;
```

2.用户授权

创建的用户没有操作数据的任何权限，我们需要使用DCL语句进行权限的授权或者回收

常用的系统权限：

create session 登录连接

create table 建表

drop table 删除表

【自行补充......】

多个权限集中一起，给予命名就是角色身份

所以一个应用系统，URR【user-role-right】是需要核心设计

```sql
--给两个用户分别授予了角色身份
--connect角色：临时用户，具备基本的登录，增删改查数据的权限
--resource角色：更正式的用户，在connect基础上具有create等系列权限
--dba角色：超管，通常不授予
grant connect,resource to wanghaoran
grant connect to huahua
```

```sql
--通过on关键字可以把某个对象的具体权限授予出去
grant select on scott.dept to A_oe
```

```sql
--通过revoke...from...语法撤销回收权限角色
revoke connect from wanghaoran
--删除用户
DROP USER wanghaoran CASCADE;
```



### 3.oracle序列

oracle 序列是用来生成连续，数值性数据的oracle对象

1.创建序列

```sql
--system登录下进行序列的操作
create sequence sql1;--默认值序列
--配置信息的序列
--start with:序列的起始值
--increment by:每次的跨度
--maxvalue:最大值
--minvalue:最小值
--cycle:序列达到最大值后是否循环
--cache 10:缓存数量【默认20】
create sequence seq2
start with 10
increment by 3
maxvalue 100
minvalue -20
cycle 
cache 10;
```



2.修改序列

```sql
--【除了start with 名称，其他都能修改】
alter sequence seq2
start with 10
increment by 3
maxvalue 100
minvalue -20
cycle 
cache 10;
```



3.使用序列

```sql
--序列的使用【通过两个伪列，nextval和currval进行序列的使用】
--注意：currval想要使用nextval至少运行一次
--创建默认值序列
create sequence seq_str;
--创建数据表
create table stu(
      id number(6) ,
      name varchar2(20),
      sex varchar2(2)
);
--插入数据
insert into stu values(seq_str.nextval,'张三','男');
insert into stu values(seq_str.nextval,'李四','男');
--提交数据
commit;
--查询数据
select * from stu;
```



4.查看全部序列

```sql
--查看全部序列
--sequence_name列表示序列的名称。
--min_value和max_value列表示序列的最小值和最大值。
--increment_by列表示序列的递增步长。
--last_number列表示序列的最后一个生成的值。
SELECT sequence_name, min_value, max_value, increment_by, last_number
FROM dba_sequences;
```



5.主键值的其他方式

如果主键不是整形自增，是字符类型，我们可以使用sys_guid()函数进行操作，获取一个32位的唯一字符类型



### 4.oracle同义词

oracle同义词【synonym】是用来给oracle数据对象取别名

可以简化sql，隐藏信息，提升安全等级

可具体分为私有同义词，共有同义词两种



1.给用户授予创建同义词的权限

```sql
--在高权限system模式下，给uu1,uu2分别授予私有，共有创建权限
grant create synonym to uu1;
grant create public synonym to uu2;
```

2.创建同义词

```sql
--uu1模式下创建  
create or replace synonym emp1 for scott.emp;
--uu2模式下创建
create or replace public synonym emp2 for scott.emp;
```

【注:emp1同义词只能在他自身模式下访问,emp2所有用户都能访问】



3.删除同义词

```sql
--system模式下授予权限
grant drop any synonym to uu1;
grant drop public synonym to uu2;
--删除同义词【私有的同义词只能自己删除，公共的大家都可以删除】
drop synonym emp1;
drop public synonym emp2;
```



4.查询同义词

```sql
SELECT * 
FROM dba_synonym;
```



### 5.oracle索引

oracle索引是存放表中所有数据行地址的对象【rowid】

是数据库的逻辑存储结构

1.优点：

提升数据查询效率，减少i/o损耗



2.构成

物理上

b数索引：默认索引类型，也叫平衡键索引，类似于算法中的二叉树

位图索引：在底基数列【对应列重复列率高】上创建的索引

反向键索引：底层识别时，反方向进行读取，适用于学号等这里前面相同，后面不同的的这类数据

逻辑上

单例索引，组合索引，基于函数的索引，唯一否索引



3.具体语法

```sql
--索引【操作pet表等】
--查看指定表的索引
select * from user_indexes 
where TABLE_NAME = 'PET'

--1.唯一降序
create unique index index_1 on pet(adopt_time desc); 

--2.位图【低基数列，重复率高的】
create bitmap index index_2 on pet(type_id);

--3.组合
create index index_3 on pet(health,love);

--4.函数
create index index_4 on pet(to_char(adopt_time),'yyyy');

--5.方向键
create index index_5 on pet(master_id) reverse;

--删除【drop table后的所有index也自动删除】
drop index index_5;
```



### 6.oracle表分区

在oracle创建一张表的同时对其进行分区处理

可以提升后期数据查询效率

存储在磁盘的不同位置，便于后期备份的维护

range：范围

partition：区域

interval：间隔

numtoyminterval：间隔细节【年/月】



范围分区

提前设定好整张表的所有分区范围，固定了

```sql
--对orders表进行了6个范围的设定
CREATE TABLE orders
    ( order_id           NUMBER(12)
    , order_date         DATE NOT NULL
    , order_mode         VARCHAR2(8)
    , customer_id        NUMBER(6) NOT NULL
    , order_status       NUMBER(2)
    , order_total        NUMBER(8,2)
    , sales_rep_id       NUMBER(6)
    , promotion_id       NUMBER(6)
    ) 
partition by range(order_date)
(
          partition p1 values less than(to_date('2005-1-1','yyyy-mm-dd')),
          partition p2 values less than(to_date('2006-1-1','yyyy-mm-dd')),
          partition p3 values less than(to_date('2007-1-1','yyyy-mm-dd')),
          partition p4 values less than(to_date('2008-1-1','yyyy-mm-dd')),
          partition p5 values less than(to_date('2009-1-1','yyyy-mm-dd'))
);
```



间隔分区

可以优化范围分区，实现分区的自动化设置

需要设置一个最小范围值【起始值】

```sql
--创建自动化分区
create table sale2(
       id number,
       pid number,
       sdate date
)
--根据sdate进行分区
partition by range(sdate)
--按1年进行分区
interval(numtoyminterval(1,'year'))(
          --分区的最小区间
          partition par1 values less than(to_date('2023-1-1','yyyy-mm-dd'))
);
```



查看表分区

```sql
--查看分区的结构名称
select * from user_tab_partitions 
where table_name = 'SALE2'

--查看分区数据
select * from sale2 
partition(SYS_P22)
```



# 第三章 PL/SQL

PL/SQL是一种过程化语言，把原来sql语句只能单独执行的方式，转换为编程方式的各种流程化控制

### 1.执行原理

在有oracle环境的基础上，把语句块通过PL/SQL引擎进行识别

流程控制剥离后，把对应的sql语句传入oracle数据库中进行CRUD操作



### 2.PL/SQL语句块

他是PL/SQL编程的最小逻辑存储单位，是后续学习存储过程，函数，包等的语句基础

```sql
declare
	--定义变量
begin
	--要操作的流程控制级sql
exception
	--异常捕获后的处理
end;
```





### 3.变量

**规范：**由字母和下划线构成首位置，其他位置可以加上数字，长度注意并见名之意

```sql
--可以通过:=或者default关键字进行初始值的赋予
declare 变量名 【constant标记常量】 数据类型
```



**类型：**

标量类型：常规oracle中的字符，数字，日期等同等类型



LOB类型：等同于oracle的二进制类型【blog,clog等】



属性类型：引用数据表的一些类型，隐藏自身信息，改变会同时发生

具体属性类型有如下两种

1.%type：引用表中某个字段类型

2.%rowtype：引用表中整体行的类型



**变量赋值**

可以通过以下两种方式赋值

1:=方式：赋常量值

2:=into子句方式：可以把数据表中的值通过sql结合into进行赋值



**相关案例**

```sql
--变量的赋初始值
declare 
  v_name varchar2(20);
  v_sex varchar(2) default '男';
  v_age number :=18;
--常量必须
  v_phone constant varchar2(11) default '12345678901';
begin
--使用||进行拼接
  dbms_output.put_line('姓名:'||v_name);
  dbms_output.put_line('性别:'||v_sex);
  dbms_output.put_line('年龄:'||v_age);
  dbms_output.put_line('电话:'||v_phone);
  v_name :='张三';
   dbms_output.put_line('修改完后的姓名:'||v_name);
end;
```

```sql
--属性类型和into子句赋值
declare
 --属性类型%type：表的字段类型
 v_date scott.emp.hiredate %type;
 --属性类型%rowtype：表的整行类型
 v_dept scott.dept %rowtype;
begin
 --变量还可以使用into子句赋值【sql】
  select e.hiredate into v_date from scott.emp e where e.ename = 'SMITH';
  dbms_output.put_line('生日：'||v_date);
  
  --使用into赋值
  select * into v_dept from scott.dept where deptno=10; 
  dbms_output.put_line(v_dept.dname||'------'||v_dept.loc);
end;
```

```sql
--交互
declare
  v_name varchar(20) :='&请输入姓名';
  v_age number :=&请输入年龄;
begin
  dbms_output.put_line('你输入的姓名:'||v_name);
  dbms_output.put_line('你输入的年龄:'||v_age);
end;
```



### 4.流程控制语句

语句块执行的顺序结构通常有顺序结构，选择结构，循环结构

1.顺序结构

​		程序从上往下依次顺序执行

2.选择结构

​		也可以称为条件语句

```sql
--先择结构
--单分支
declare 
  v_score number :=&请输入成绩;
begin 
   if v_score>=60 then
    dbms_output.put_line('成绩'||v_score||'分，及格了');
    end if;
end;
```

```sql
--双分支
declare 
  v_score number :=&请输入成绩;
begin 
   if v_score>=60 then
    dbms_output.put_line('成绩'||v_score||'分，及格了');
    else
    dbms_output.put_line('成绩'||v_score||'分，不及格了');
    end if;
end;
```

```sql
--多分支
declare 
  v_score number :=&请输入成绩;
begin 
   if v_score>=90 then
    dbms_output.put_line('成绩'||v_score||'分,优秀');
    elsif v_score>=80 then
    dbms_output.put_line('成绩'||v_score||'分,良好');
    elsif v_score>=70 then
    dbms_output.put_line('成绩'||v_score||'分,一般');
    elsif v_score>=60 then
    dbms_output.put_line('成绩'||v_score||'分,合格');
    else
    dbms_output.put_line('成绩'||v_score||'分,不及格了');
    end if;
end;

--case语句【完成和if语句一样的分支】
declare 
  v_score number :=&请输入成绩;
begin 
  case
    when v_score >=90 then
        dbms_output.put_line('成绩'||v_score||'分,优秀');
    when v_score >=80 then
        dbms_output.put_line('成绩'||v_score||'分,良好');
    when v_score >=70 then
        dbms_output.put_line('成绩'||v_score||'分,一般');
    when v_score >=60 then
        dbms_output.put_line('成绩'||v_score||'分,合格');
    else
        dbms_output.put_line('成绩'||v_score||'分,不及格了');
    end case;
end;
```

```sql
--case等值优化
declare 
  v_level number :=&请输入考试名次;
begin 
  case v_level
    when 1 then
        dbms_output.put_line('第'||v_level||'名,七日游');
    when 2 then
        dbms_output.put_line('第'||v_level||'名,五日游');
    when 3 then
        dbms_output.put_line('第'||v_level||'名,三日游');
    else
        dbms_output.put_line('第'||v_level||'名,继续努力');
    end case;
end;
```

```sql
--循环结构
--纯loop循环输入1-10的数字
declare 
 v_num number :=1;

begin
 loop
   dbms_output.put_line(v_num);
   --变化
   v_num:=v_num+1;
   --主动退出
   exit when v_num=11;
  end loop;   
end;
```

```sql
--while-loop循环输出1-10数字
declare 
 v_num number :=1;
begin 
 while v_num!=11 loop
   dbms_output.put_line(v_num);
   v_num:=v_num+1;
   end loop;
end;
```

```sql
--for-loop循环输入1-10的数字[reverse:反向，放在in后]
begin
 for i in reverse 1..10  loop 
   dbms_output.put_line(i);
 end loop;
end;
```

```sql
--九九乘法表
begin 
   for i in 1..9  loop 
       for j in 1..i  loop 
           dbms_output.put(i||'*'||j||'='||i*j||' ');
       end loop;
       dbms_output.put_line('');
   end loop;
end;
```



### 5.异常

在语句块执行过程中出现的非正常状态，需要对他进行相关处理

1.预定义异常

系统提前定义好的异常类型

no_data_found：未检索到数据

too_many_rows：检索数据行过多异常

.......



2.自定义异常

通过exceptIon类型定义的变量，就是一种自定义的类型



3.语法

```sql
ex exception --定义异常
```

```sql
raise ex --抛出异常
```

```sql
exception 
	--在处理异常的过顺序中，others放在最后
	when ex then.... 
	when others then
```



### 6.游标

在oracel中PL/SQL块中不能直接编写纯select普通列的sql语句，所以我们可以通过游标Cursor容器进行存储

游标就是用来查询多行数据的一个工具



**1.分类**

静态游标

​	隐式游标

​	显示游标

动态游标

​	自定义游标

​	系统游标

​	有强弱之分





**2.属性**

| 属性      | 说明           |
| --------- | -------------- |
| %found    | 检索到数据     |
| %notfound | 没有检索到数据 |
| %isopen   | 游标是否打开   |
| %rowcount | 返回的影响行数 |



**3.语法**

| 语法                   | 说明                   |
| ---------------------- | ---------------------- |
| cursor 游标 is 静态sql | 声明静态游标           |
| open游标               | 打开游标               |
| fetch 游标 into 变量   | 从游标中抓取数据并赋值 |
| close游标              | 关闭游标               |
| open 游标 for动态sql   | 声明动态游标           |



**4.静态游标**

```sql
--静态游标
--隐式游标【DML语句】使用sql%...进行属性获取
select * from dept1127;
--插入一条部门信息
begin
    insert into dept1127 values(70,'研发部','上海');
    if sql%found then
      dbms_output.put_line('数据插入成功'||sql%rowcount);
    else
      dbms_output.put_line('数据插入失败');
    end if;
    commit;
end;


--修改一条部门信息
begin
    update dept1127 set dname = 'c#' where deptno = 50;
    if sql%found then
      dbms_output.put_line('数据修改成功'||sql%rowcount);
    else
      dbms_output.put_line('数据修改失败');
    end if;
    commit;
end;




--删除一条部门信息
begin
    delete from dept1127 d where deptno>20;
    if sql%found then
      dbms_output.put_line('数据删除成功'||sql%rowcount);
    else
      dbms_output.put_line('数据删除失败');
    end if;
    commit;
end;



--根据编号查询部门信息
declare 
    v_dept dept1127%rowtype;
begin
    select * into v_dept from dept1127 where deptno = 10;
    if sql%found then
      dbms_output.put_line(v_dept.deptno||v_dept.dname);
    end if;
exception 
  when others then
      dbms_output.put_line('异常');
end;

--显示游标
declare 
      v_no number :=&请输入员工编号;
      v_emp emp1126%rowtype;
      --使用游标存储
      cursor cur_emp is select * from emp1126 where empno = v_no;
begin
      --打开游标
      open cur_emp;
      --先抓取数据
      fetch cur_emp into v_emp;
      --在判断
      if cur_emp %found then
         dbms_output.put_line(v_emp.ename||'=='||v_emp.sal);
      else
         dbms_output.put_line('没有检索到'||v_no||'数据');
      end if;
      --关闭游标
      close cur_emp;
end;
```

5.动态游标

```sql
--动态游标
declare 
      v_emp emp1126%rowtype;
      --定义动态游标类型【return 加上就是强类型】
      type cut_type is ref cursor;
      --定义该类型的变量
      cur_emp cut_type;
begin
      open cur_emp for select * from emp1126;
      loop
        fetch cur_emp into v_emp;
        exit when cur_emp%notfound;
        dbms_output.put_line('姓名：'||v_emp.ename);
        dbms_output.put_line('工种：'||v_emp.job);
        dbms_output.put_line('工资：'||v_emp.sal);
        dbms_output.put_line('=================');
      end loop;
end;

--直接使用系统定义好的动态游标类型
declare 
      v_emp emp1126%rowtype;
      cur_emp sys_refcursor;--弱类型
begin
      open cur_emp for select * from emp1126;
      loop
        fetch cur_emp into v_emp;
        exit when cur_emp%notfound;
        dbms_output.put_line('姓名：'||v_emp.ename);
        dbms_output.put_line('工种：'||v_emp.job);
        dbms_output.put_line('工资：'||v_emp.sal);
        dbms_output.put_line('=================');
        end loop;
end;
--自定义类型
type my_type is record(
     ename varchar2(20),
     esal number,
     dname
     varchar2(20)
);
```

**6.三种循环**

```sql
--三种循环
--纯loop
declare
vemp emp1126%rowtype;
cursor cur_emp is select * from emp1126;
begin
  open cur_emp;
  loop 
    fetch cur_emp into vemp;
    dbms_output.put_line('姓名:'||vemp.ename);
    dbms_output.put_line('工种:'||vemp.job);
    dbms_output.put_line('工资:'||vemp.sal);
    dbms_output.put_line('================');
    exit when cur_emp %notfound;
   end loop;
   close cur_emp;
end;

--while-loop
declare
vemp emp1126%rowtype;
cursor cur_emp is select * from emp1126;
begin
  open cur_emp;
  fetch cur_emp into vemp;
  while cur_emp %found  loop 
    dbms_output.put_line('姓名:'||vemp.ename);
    dbms_output.put_line('工种:'||vemp.job);
    dbms_output.put_line('工资:'||vemp.sal);
    dbms_output.put_line('================');
    fetch cur_emp into vemp;
   end loop;
   close cur_emp;
end;

--for
declare
cursor cur_emp is select * from emp1126;
begin
  for i in cur_emp loop
    dbms_output.put_line('姓名:'||i.ename);
    dbms_output.put_line('工种:'||i.job);
    dbms_output.put_line('工资:'||i.sal);
    dbms_output.put_line('================');
   end loop;
end;
```





# 第四章 子程序、包、触发器

### 1.子程序

概念：命名的pl/sql语句块，编译后永久保存在数据中

分类：存储过程、函数【有无返回值】

**1.存储过程**

基本语法：

```sql
--创建存储过程
create or replace procedure p1
as 
--局部变量位置
begin
    --语句块
    dbms_output.put_line('编写的第一个oracel存储过程');
end;

--调用
begin
    p1;
end;
```



参数模式【三种】：

| 模式   | 说明                                                 |
| ------ | ---------------------------------------------------- |
| in     | 这类值只能传入值不能改变【类似java中的基本数据类型】 |
| out    | 输出值【传入的值是无效的，类似java中的引用数据类型】 |
| in out | 既能传入值，也可以输出值                             |

基本案例：

```sql
--输入参数in
create or replace procedure p3(
       num1 in number,
       num2 in number
)
as 
--局部变量
       num3 number;
begin
       num3 := num1+num2;
       dbms_output.put_line(num1||'+'||num2||'='||num3);
end;


declare 
       num1 number := &请输入第一个数;
       num2 number := &请输入第二个数;
begin
       p3(num1,num2);
end;

--输出参数out
create or replace procedure p4(
       num1 in number,
       num2 in number,
       num3 out number
)as 
begin
  num3 :=num1+num2;
end;

declare 
       num1 number := &请输入第一个数;
       num2 number := &请输入第二个数;
       num3 number;
begin
        p4(num1,num2,num3);
        dbms_output.put_line(num1||'+'||num2||'='||num3);
end;

--输入参数模式in out【1+1=2】
--注意：in模式的参数只能传入并不能修改
create or replace procedure p5(
       num1 in number,
       num2 in number,
       num3 in out number
)as 
begin
      dbms_output.put_line('num3的初始值是'||num3);
      num3:=num1+num2;
end;

declare
      num1 number :=3;
      num2 number :=3;
      num3 number :=3;
begin
  p5(num1,num2,num3);
  dbms_output.put_line(num1||'+'||num1||'='||num3);
end;

```





### 2.函数

基本语法

```sql
create or replace function f1
return varchar2 --返回数据类型
as 
begin
  return '这是函数';
end;

declare 
  v_info varchar2(20);
begin
  v_info := f1;
  dbms_output.put_line(v_info);
end;
```



参数的三种模式【in,out,in out】

```sql
--in
create or replace function f2(
       num1 in number,
       num2 in number
)return number
as 
       sums number;
begin
       sums:=num1+num2;
       return sums;
end;

declare 
       a number :=3;
       b number :=6;
       c number;
begin
       c := f2(a,b);
       dbms_output.put_line(c);
end;


--out
create or replace function f3(
       num1 in number,
       num2 in number,
       num3 out number
)return varchar2
as 
begin
       num3:=num1+num2;
       return num3;
end;


declare 
       a number :=3;
       b number :=6;
       c number;
       str varchar2(20);
begin
       str := f3(a,b,c);
       dbms_output.put_line(str);
end;


--in out
create or replace function f4(
       num1 number,
       num2 number,
       num3 in out number
)return varchar2
as begin
       dbms_output.put_line('传入的in out模式参数的值为：'||num3);
       num3:= num2+num1;
       return num1||'+'||num2||'='||num3;
end;
```





### 3.包

包：package,是一组oracle对象的集合，可以在里面自由组合自定义变量，游标，过程，函数，存储过程等。

防止重名冲突，其功效和java的分包机制类似

包整体分为两个部分，由包的规范和包的主体构成

```sql
--包
--包的声明【类似java中接口】
create or replace package pk1
as 
       --包中的存储过程
       procedure p1;
       --包中的函数
       function f1 return varchar2;
end; 
```

```sql
--包的实现，主题【类似java中的实现类】
create or replace package body pk1 
as procedure p1
   as 
      begin
        dbms_output.put_line('我是pk1包中的存储过程');
      end;
  function f1 return varchar2
    as
    begin
      return '我是pk1包中的函数';
    end;
end;
```

```sql
--调用【需要加前缀】
begin
    pk1.p1;
    dbms_output.put_line(pk1.f1);
end;

```





### 4.触发器

命名的pl/sql语句块，编译后存储在数据库中

特征：隐式   自动   不能传递参数   

**分类**

DDL级：进行定义语言操作触发【create  drop  alter等】



数据库级【数据库连接，关闭等】

DML行级【有一行受影响（增删改），就会触发】

DML语句级【一条sql语句触发】



**语法**

```sql
--行级触发器
/*
    在对emp11表进行插入获取修改sal字段值的时候
    如果sal的值小于等于1500 那么给他的comm赋值500
*/
create or replace trigger tr1
       before insert or update of sal on emp11
       for each row --行级关键字
       when (new.sal<=1500)
begin
  :new.comm :=500;
end;
```

```sql
--语句级触发器
--在对emp11表进行添加或者修改或者删除之后
--自动对emp_log表插入一条相关日志记录
--【注：触发器中不能进行事务提交等操作】
create or replace trigger tr2
       after insert or update or delete on emp11
declare 
       v_info varchar2(200);
begin
       if inserting then 
         v_info := '执行insert操作';
       elsif updating then
         v_info := '执行update操作';
       elsif deleting then
         v_info := '执行delete操作';
       end if;
       --插入日志
       insert into emp_log values(v_info,user,sysdate);
end;

```







# 第五章 oracel补充

### 1.all_sequences和dba_sequences区别

主要区别在于视图的访问权限和所提供信息的范围。`DBA_SYNONYMS`提供数据库中所有同义词的信息，而`ALL_SYNONYMS`则提供当前用户有权限访问的同义词信息
