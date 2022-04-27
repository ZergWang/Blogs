**<font color='ff0000'> 若无特殊说明，则以下SQL语法均适用于GBase数据库，大部分适用于Oracle数据库。</font>**
<br/>

# Oracle系统操作
```sql
-- 创建用户
create user 用户名 identified by 密码;
-- （若提示“invalid common user or role name”，在用户名前面加上“C##”试试）

-- 授予用户权限
grant connect, resource, dba to 用户名;

-- 连接到数据库
conn 用户名;
```
<br/><br/>

# Gbase系统操作
### onstat
```sql
-- 查看实例配置文件（onconfig）
onstat -c

-- 查看dbspace和chunk信息
onstat -d 

-- 查看环境变量
onstat -g env

-- 查看实例的各项进程
onstat -g glo 

-- 查看隔离级别和锁模式
onstat -g sql

-- 查看锁信息
onstat -k

-- 查看事务信息
onstat -x
```
### oninit
```sql
-- 初始化并启动实例
oninit -ivy
-- 仅启动实例
oninit -vy
```
### onmode
```sql
-- 完全关闭数据库实例
onmode -ky
```
### onclean
```sql
-- 清空共享内存及缓存
onclean -ky
```
### oncheck
```sql
-- 查看表的存储情况
oncheck -pt 数据库名:表名
```
<br/><br/>


# Gbase系统表
| 表名 | 内容 |
| ---  | --- |
| systables | 所有表和视图的信息（不记录表的内容） |
| syscolumns | 所有列的信息 |
| sysconstraints | 所有列上的约束信息 |
| sysfragments | 所有分片的信息 |
| sysindexes | 所有索引的信息 |
| sysviews | 所有视图的信息 |

<br/><br/>

# Create
### 创建数据库
```sql
-- 无日志的数据库
create database 数据库名;          
-- 缓冲式日志的数据库
create database 数据库名 with buffered log; 
-- 无缓冲式日志的数据库
create database 数据库名 with log;      
-- ANSI日志的数据库
create database 数据库名 with log mode ansi
```
### 创建视图
```sql
-- 创建视图
create view 视图名 as 子查询;
-- 创建限制修改的视图
create view 视图名 as 子查询 with check option;
```
### 创建表
```sql
-- 普通表
create table 表名 (列名 类型, 列名 类型 ... );
```
含约束条件的表：
```sql
create table 表名 
    (列名 类型 列约束, 列名 类型 列约束, 表约束, 表约束);
```
约束有： not null、unique、default、primary key（主键）、foreign key（外键）、check等。

#### 主键
一个表的主键可唯一标识表中所有元组。受到主键约束的列必须是unique且not null。每张表最多只能有一个主键。

主键可以是某一列，也可以是多个列组成的属性组。例如某表记录了某学校每个班级的情况，若要在学校中唯一标识每个班级，则需要班级号和年级号共同决定，因此(年级，班级)为主键。

在语法上，可以将主键写为列约束，也可以写为表约束。
```sql
-- 指定某列为主键，写成列约束
create table 表名 (列名 类型 primary key, 列名 类型);
-- 或者写成表约束
create table 表名 (列名 类型, 列名 类型, primary key(列名));

-- 指定多列共同为主键，写成表约束
create table 表名 (列名 类型, 列名 类型, primary key (列名, 列名));

-- 若要为主键命名，可使用如下命令，主键为单列或多列均可
create table 表名 (列名 类型, 列名 类型, 
    constraint 主键名 primary key (列名, 列名));
```
#### 外键
外键用于关联两张表，防止表间关系被破坏，同时防止非法插入。
所有的外键都是另一张表的主键，或者说，依赖于另一张表的主键。主键所在表为父表，外键所在表为子表。

举个例子，表Info存储学生个人信息，有两列id和name，分别存储学生的学号和姓名；表Score存储学生成绩，有列id和sum，存储学生学号和总分。两张表均存储了学生的学号，则学号可作为外键关联两表。将Info.id作为Info表的主键，同时作为Score表的外键，建表语句：
```sql
create table Score (id int, sum int, 
    foreign key (id) references Info(id));
```
此时Score表（子表）中的数据会受到Info表（父表）数据的约束。例如，不可往子表中插入父表不存在的数据。也就是说，现在有学号为1的学生的姓名和成绩，该信息未录入Info表前不可录入到Score中，以此来确保Score中的数据在Info中均有对应。

若指定多列共同为外键，或为外键命名，可：
```sql
create table 表名 (列名 类型, 列名 类型, 
    constraint 外键名 foreign key (列名, 列名) references 表名(列名, 列名));
```
#### check

用于限制列中值的范围。可以写为列约束，对单个列进行约束；也可以写为表约束，同时约束多个列。举个例子
```sql
create table score (id int, 
    score int check (score>=0 and score<=100));

create table boy (height int, gender char(5), 
    constraint 约束名 check(height>0 and gender='male'));
```
### 分片表
#### 轮转法分片
（可指定多个dbspace，每个dbspace存一个分片）
```sql
create table 表名 (列名 类型, 列名 类型) 
    fragment by round robin in dbspace01, dbspace02, dbspace03;
```

#### 表达式分片
```sql
create table 表名 (列名 类型, 列名 类型) fragment by expression;
```
举个例子：
```sql
create table student (id int, score int) fragment by
     range(score) (partition values less than (60), partition values less than (100));
```
partition values之间可填入分片名，为partition指定名字。

### 为表中某一列创建唯一索引
<font color='FF0000'>一旦为某一列创建唯一索引，则表中任意两行的该列对应的属性值不能相同</font>
```sql
create unique index 索引名 on 表名 (列名 asc);
create unique index 索引名 on 表名 (列名 desc);
```
其中，asc表示升序，desc表示降序。
<br/><br/>

# drop & delete 
### 删除数据库
```sql
drop database 数据库名; 
```
### 删除视图
```sql
drop view 视图名;

-- 级联删除（递归删除依赖该视图的视图）
drop view 视图名 cascade;
```
举个例子，假设视图v2依赖v1，v3依赖v2，v4依赖v3……则级联删除v1时v2、v3、v4……全都会被删除。
### 删除索引
```sql
drop index 索引名;
```
### 删除表
```sql
-- 保留表但清空其内容
truncate 表名;

-- 删除整张表
drop table 表名;

-- 若要删除的表不存在也不报错
drop table if exists 表名;

-- 级联删除（递归删除依赖于该表的视图和外键约束）
drop table 表名 cascade;

-- 限制删除（表如果被视图或其他表的外键引用则无法删除）
drop table 表名 restrict;
```
### 删除表中的元组
```sql
delete from 表名 where 条件;
```
<br/><br/>

# insert
### 插入单条元组
```sql
insert into 表名 values( 列1对应值 , 列2对应值 ... );  
```
### 指定列插入单条元组
```sql
insert into 表名 (列1 , 列2 , 列3 ....) values ( 值1, 值2, 值3 ... )
```
### 从查询结果中插入
```sql
insert into 表名 子查询语句;
```
### 相同数据插入多张表
```sql
insert all into 表1 into 表2 into 表3 values或者查询语句;
```
### 各表插入不同数据
```sql
insert all into 表1  values(...)  into 表2 values(...);
```
### 根据查询结果插入不同的表
```sql
insert all when 条件1 into 表1 when 条件2 into 表2 else into 表3 查询语句;
```
### 仅执行首个匹配条件的插入操作
```sql
insert first when 条件1 into 表1 when 条件2 into 表2 else into 表3 查询语句;
```
<font color=#ff0000>使用first关键字时：对于select出来的每个元组，仅执行首个匹配的when语句中的插入操作。
使用all关键字时：对于select出来的每个元组，执行所有匹配的when语句中的插入操作。</font>
<br/><br/>

# update & alter
### 重命名表
```sql
rename table 旧表名 to 新表名;
```
### 单条元组修改
```sql
update 表名 set 列名=新值 where 条件;
```
### 修改表中列
```sql
-- 添加列
alter table 表名 add 列名 列类型;

-- 删除列
alter table 表名 drop column 列名;
```

### 约束
#### 添加约束

#### 删除约束
```sql
-- 通过约束名删除约束
alter table 表名 drop constraint 约束名;

-- 删除主键
alter table 表名 drop primary key;
```
### 增删分片
```sql
-- 添加分片
alter fragment on table 表名 
    add partition 分片名 values less than (值);

-- 拆分分片
alter fragment on table 表名 
    split partition 分片名 at (值) into (partition 新分片名1, partition 新分片名2);
```
<br/><br/>


# select
### 查看库中所有表和视图
```sql
info tables;
```
### 单表全部查询
```sql
select * from 表名 where 条件;
```
### 跳过select结果中前面的K行
```sql
select skip K * from 表名;
```
### 最多返回select结果中前面的K行
```sql
select first K * from 表名;
```
### 集合操作
```sql
-- union：对两个select的结果进行并集操作，去除重复行，并按默认规则排序
select * from 表1 union select * from 表2;

-- union all：对两个select的结果进行并集操作，不去除重复行也不排序
select * from 表1 union all select * from 表2;

-- intersect：对两个select的结果进行交集操作（即取两个select结果的共同部分）
select * from 表1 intersect select * from 表2;

-- except：对对两个select的结果取差集（第一个select结果中去掉和第二个select结果相同的部分）
select * from 表1 except select * from 表2;
```
### 别名
常用于涉及多表操作的select语句中，为表或列增加别名以简化sql语句。
```sql
-- 为表增加别名
select 列名 from 表名 as 别名;
-- 为列增加别名
select 列或表达式 as 别名 from 表名;
```
在Oracle中，as关键字可省略。
### join
![](SQL笔记_1.png)

join用于把多个表中的行结合起来。
首先在待结合的多个表中分别指定一个列，然后这些表对应列中值相等的合并为一行。指定列的列名可以互不相同。
```sql
select 列或表达式 from 左表名 join 右表名 on 左表.指定列名 = 右表.指定列名
```
join分为inner join、full join、left join和right join四种，区别见上表。

这里也举个例子，先建如下两张表，表A存储学生的姓名与学号。
| id | name |
| -- | ---- |
| 1  | wang |
| 2  | liu  |
| 3  | kai  |

表B存储学生的成绩。
| id | sum |
| -- | --- |
| 1  | 80  |
| 2  | 75  |
| 4  | 95  |
#### inner join
inner join将多张表中对应列中值相等的合并为一行，若不相等或者值缺少，则整行舍弃。
执行如下sql语句：
```sql
select A.id, A.name, B.sum from A join B on A.id=B.id;
```
得到结果：
| id | name | sum | 
| -- | ---- | --- |
| 1  | wang | 80  |
| 2  | liu  | 75  |

在sql语法中“inner”可省略，直接写“join”即可。

#### full join
将多张表中对应列中值相等的合并为一行，若不相等或者值缺少，行仍然保留。
```sql
select A.id, A.name, B.sum from A full join B on A.id=B.id;
```
得到结果：
| id | name | sum | 
| -- | ---- | --- |
| 1  | wang | 80  |
| 2  | liu  | 75  |
| 3  | kai  |     |
| 4  |      | 95  |

#### left join
将多张表中对应列中值相等的合并为一行，若不相等或者值缺少，左表中对应行保留，右表中对应行舍弃。
```sql
select A.id, A.name, B.sum from A left join B on A.id=B.id;
```
得到结果：
| id | name | sum | 
| -- | ---- | --- |
| 1  | wang | 80  |
| 2  | liu  | 75  |
| 3  | kai  |     |

#### right join
同理，合并时对应列中值不相等或者缺少，右表中对应行保留，左表中对应行舍弃。

### group by
一般和聚合函数一起使用。
group by后接列名，将表中的每行按指定列进行分组。也就是说，对于每一行，它们指定列的值相等的话会被分到同一组。
```sql
select 列或表达式 from 表名 group by 指定列;
```
假设有表T记录了学校中每个学生的成绩：
| grade | class | id | score |
| ----- | ----- | -- | ----- |
| 1     | 1     | 1  | 80    |
| 1     | 1     | 2  | 90    |
| 2     | 3     | 3  | 99    |
| 2     | 3     | 5  | 99    |
| ...   | ...   | ...| ...   |

若要显示每个班学生的平均成绩：
```sql
select grade, class, avg(score) from T group by grade, class;
```
得到结果：
| grade | class | score |
| ----- | ----- | ----- |
| 1     | 1     | 85    |
| 2     | 3     | 99    |
| ...   | ...   | ...   |

在group by的返回结果中，每组仅显示为一行，但每组是由原表中的多行构成的，对于group by的指定列，这多行的值是一样的，可以直接显示；但对于非指定列，原来的多行数据并不一样，那该显示为什么值呢？因此group by一般需要聚合函数，对非指定列进行求和、求平均、计数等方式计算出一个值来代表这一组。

### order by
给出列名，按指定列中的值大小进行升序排序。若要按降序排行，添加关键字“desc”。
```sql
-- 升序
select 列名 from 表名 order by 列名
-- 降序
select 列名 from 表名 order by 列名 desc
```
注意：order by无法对子查询使用。

### distinct
去除select结果中相同的值
```sql
select distinct 列名或列表达式 from 表名;
```

### case when
```sql
case 
    when 判断条件1 then 列表达式1
    when 判断条件2 then 列表达式2
    when 判断条件3 then 列表达式3 
    else 列表达式4
end
```
其中，when ... then 可一直叠加。case when语法整体作为一个列表达式使用。
<br/><br/>

# 特殊数据类型
### datetime
用于日期和时间的记录。默认格式：YYYY-MM-DD HH:MM:SS:FFF，可通过GL_DATETIME环境变量来修改。
建一个含datetime列的表：
```sql
create table 表名  (列名 datetime year to second);
```
“year to second”规定了datetime列的格式为YYYY-MM-DD HH:MM:SS，若只需精确到月份，可“year to month”，最大可精确到3位毫秒（YYYY-MM-DD HH:MM:SS.FFF），即“year to fraction(3)”。
<br/><br/>

# 函数
### 聚合函数
```sql
-- 计算指定列中行的数量
count(列名)

-- 求指定列中值的和
sum(列名)

-- 求指定列平均值
avg(列名)

-- 求指定列中最大值
max(列名)

-- 求指定列中最小值
min(列名)
```

### to_date

### to_char

### to_number


<br/><br/>


# 其他
### 注释
单行注释
```sql
-- 注释内容
```
多行注释
```sql
/* ...
   注释内容
   ... */
```
### 事务
```sql
-- 开始事务
begin;
-- 提交事务
commit;
-- 回滚事务
rollback;
```
### 隔离级别
```sql
-- 设置全局锁等待时间
set lock mode to wait 秒数;
--设置锁不等待
set lock mode to not wait;
-- 设置锁一直等待
set lock mode to wait;
```
<br/><br/>

# 参考资料
[SQL 教程 | 菜鸟教程](https://www.runoob.com/sql/sql-join.html)