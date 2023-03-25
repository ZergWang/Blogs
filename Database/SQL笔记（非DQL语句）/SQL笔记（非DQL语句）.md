**<font color='ff0000'> 若无特殊说明，则以下SQL语法均适用于GBase数据库，适用于Oracle数据库的操作会特别标明。</font>**
<br/>

# 环境设置
### 字符集
若要创建指定字符集的库，首先在Linux中设置环境变量：（以中文utf8为例）
```bash
export DB_LOCALE=zh_cn.utf8
export CLIENT_LOCALE=zh_cn.utf8 
```
然后在dbaccess中直接创建新库，该库的字符集就与环境变量设置的字符集一致了。

查询指定库的字符集：
```sql
select * from sysmaster:sysdbslocale where dbs_dbsname='库名';
```
在显示的结果中，“zh_CN.57372”即zh_cn.utf8。“zh_CN.5488”即为zh_cn.GB18030-2000。

无法通过dbaccess访问与当前环境变量字符集不匹配的库（错误号：23197）。
### 语法格式
```sql
-- 设置语法为oracle模式
set environment sqlmode 'oracle';
-- 设置语法为gbase模式
set environment sqlmode 'gbase';
```
### 锁等待策略
```sql
-- 设置全局锁等待时间
set lock mode to wait 秒数;
--设置锁不等待
set lock mode to not wait;
-- 设置锁一直等待
set lock mode to wait;
```
### 隔离级别
```sql
-- 设置为脏读
set isolation to dirty read;
-- 设置为提交读
set isolation to committed read;
-- 设置为游标读
set isolation to cursor stability;
-- 设置为可重复读
set isolation to repeatable read;
```
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
### 创建普通表
```sql
create table 表名 (列名 类型, 列名 类型 ... );
```
### 创建含约束条件的表：
```sql
create table 表名 
    (列名 类型 列约束, 列名 类型 列约束, 表约束, 表约束...);
```
每个列约束只能对单个列进行约束，而表约束可同时对多个列进行约束，且可以为该约束起名。

约束类型有： not null、unique、default、primary key（主键）、foreign key（外键）、check等。

列约束的通用格式为：
```sql
约束类型 约束内容
```
表约束通用格式为：
```sql
-- gbase写法
约束类型 约束内容 constraint 约束名

-- Oracle写法
constraint 约束名 约束类型 约束内容 
```
若不想起名，可省略“constrint 约束名”这两部分。

#### 主键
一个表的主键可唯一标识表中所有元组。受到主键约束的列必须是unique且not null。每张表最多只能有一个主键。

主键可以是某一列，也可以是多个列组成的属性组。例如某表记录了某学校每个班级的情况，若要在学校中唯一标识每个班级，则需要班级号和年级号共同决定，因此(年级，班级)为主键。
```sql
-- 写成列约束
primary key
-- 写成表约束
primary key (列名, 列名, ...)
-- 写成表约束，同时为主键命名
primary key (列名, 列名 ...) constraint 主键名 
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
foreign key (列名, 列名) references 表名(列名, 列名) constraint 外键名);
```
#### check
用于限制列中值的范围。可以写为列约束，对单个列进行约束；也可以写为表约束，同时约束多个列。举个例子
```sql
create table score (id int, 
    score int check (score>=0 and score<=100));

create table boy (height int, gender char(5), 
    check (height>0 and gender='male'));
```

#### 其他约束
```sql
-- 以下均为列约束写法
-- default：当表中元组在该列的值为空时，补上一个默认值。
default 值

-- not null：表中所有元组在该列的值不能为空。
not null

-- null：允许该列的值为空。
null

-- unique：表中所有元组在该列的值不能重复
unique

-- distinct: 同unique
distinct
```

### 创建分片表
#### 轮转法分片
轮转分片表的数据会均匀分布在各个分片中。在创建此类分片表时，需要指定一个至多个datadbs，每个datadbs对应一个分片（当然也可以将多个分片存在同一个datadbs中）。
```sql
-- 不指定分片名
create table 表名 (列名 类型, 列名 类型 ...) fragment by round robin 
    in datadbs1, datadbs2, datadbs3;

-- 指定分片名
create table 表名 (列名 类型, 列名 类型 ...) fragment by round robin 
    partition 分片名1 in datadbs1,
    partition 分片名2 in datadbs2,
    partition 分片名3 in datadbs3;
```

#### 表达式分片
表达式分片表的数据会根据各个分片的表达式来存储到对应分片中。
```sql
create table 表名 (列名 类型, 列名 类型 ...) fragment by expression 
    partition 分片名1 表达式1 in datadbs1,
    partition 分片名2 表达式2 in datadbs2,
    partition 分片名3 表达式3 in datadbs3;
```
可以省略分片名和partition关键字。

#### 列表分片
当表中数据较为离散、无序，或者对表需要等值查询时，可以通过列表分片的形式创建分片表。
```sql
create table 表名 (列名 类型, 列名 类型 ...) fragment by list(列名) 
    partition 分片名1 values (值1) in datadbs1,
    partition 分片名2 values (值2) in datadbs2,
    partition 分片名3 values (值3) in datadbs3;
```
一个分片可以对应多个值，在values关键字后的括号里用逗号隔开即可。
#### 间隔分片
当表中数据连续增长，且增加的数据值不确定但易于分类时，可以使用间隔分片。对于持续增加的数据，间隔分片表可自动为新数据按照间隔规则创建新的分片。例如一个记录员工上班时间的表，其中时间相关的数据会持续增加，可以一个月为间隔划分分片，同一个月的数据放在一个分片中。之后每个月的数据系统会自动创建新的分片进行存储。
```sql
create table 表名 (列名 类型, 列名 类型 ...) 
    fragment by range(列名) interval (间隔规则) store in datadbs_list
        partition 分片名1 values < 值1 in datadbs1,
        partition 分片名2 values < 值2 in datadbs2,
        partition 分片名3 values < 值3 in datadbs3;
```
虽然系统会根据新加入数据自动划分分片，但在创建间隔分片表时，应手动创建至少一个分片。
另外，store in 子句可以省略。

间隔规则的填写与列的类型相关，若以int型数据为间隔规则，则应填入一个整型的常量表达式，且值最小为1；若以date或datetime型数据为间隔规则，则需要使用NUMTOYMINTERVAL等函数为间隔规则，具体请参考文末的相关函数。

举个例子，对int型的列以10为间隔的分片表：
```sql
create table 表名 (列名 int) 
    fragment by range(列名) interval (10) partition minus values<0 in datadbs;
```

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
insert all 
    when 条件1 then into 表1 
    when 条件2 then into 表2 
    else into 表3 查询语句;
```
### 仅执行首个匹配条件的插入操作
```sql
insert first 
    when 条件1 then into 表1 
    when 条件2 then into 表2 
    else into 表3 查询语句;
```
<font color=#ff0000>使用first关键字时：对于select出来的每个元组，仅执行首个匹配的when语句中的插入操作。
使用all关键字时：对于select出来的每个元组，执行所有匹配的when语句中的插入操作。</font>

### 从外部文件导入数据（load命令）
#### GBase操作步骤
```sql
load from 数据文件路径 insert into 表名;
```
在数据文件中，待插入的数据按行用回车隔开，按列用“|”隔开。

对于特殊数据类型（例如clob等），可使用自带的filetoclob函数进行插入，其中，“文件位置”这一参数用于指明文件在服务器端还是在客户端，该参数可选值为'server'或'client'。
```sql
insert into 表名 values (filetoclob(路径名, 文件位置));
```
#### Oracle操作步骤
导入前需准备数据文件和导入脚本两个文件

导入脚本编写：
```bash
load data infile '数据文件路径' 
truncate into table 表名
fields terminated by '|'
optionally enclosed by '"'
(列名, 列名, 列名 ...)
```
其中，数据文件路径写成绝对路径，并保证Oracle系统用户操作该数据文件的权限。数据文件中，数据按行用回车隔开，按列用统一的分隔符隔开，分隔符要在脚本文件的fields terminated中说明。若数据文件中某些数据有双引号，可在脚本文件中用optionally enclosed命令忽略。

然后在终端中执行：
```bash
sqlldr userid=用户名/密码@库名 control=脚本文件路径
```
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
```sql
alter table 表名 add constraint 约束类型 约束内容 constraint 约束名;
```
若不需要为约束起名，可省略“constraint 约束名”这两部分。
#### 删除约束
```sql
-- 通过约束名删除约束
alter table 表名 drop constraint 约束名;

-- 删除主键
alter table 表名 drop primary key;
```
### 修改分片表
```sql
-- 新建分片
alter fragment on table 分片表名 
    add partition 分片名 values less than (值);

-- 拆分分片
alter fragment on table 分片表名 
    split partition 分片名 at (值) into (partition 新分片名1, partition 新分片名2);

-- 删除分片
alter fragment on table 分片表名 drop 分片名;

-- 剥离分片（将分片表中的某个分片独立出来，作为一张普通表）
alter fragment on table 分片表名 detach partition 分片名 普通表名;

-- 将普通表作为分片加入分片表
alter fragment on table 分片表名 attach 普通表名;
```
<br/><br/>



# 特殊数据类型
### 大对象
用于存储大文件、多媒体文件的一种数据类型。有简单大对象和智能大对象两种类型。每种类型下通过存储文本文件还是二进制文件又可分为总计四种类型。

| 类型 | 简单大对象 | 智能大对象 |
| ---  | ---- | ---|
| 数据最大长度 | 2GB |  4TB |
| 数据访问方式 | 仅顺序访问| 可顺序访问，也可随机访问 |
| 存储位置| datadbs 或 blobspace| sbspace|
| 存储文本文件的类型| text | clob |
| 存储二进制文件的类型| byte | blob |

简单大对象的insert可通过load命令从文件中加载，智能大对象通过filetoblob和filetoclob函数从文件中加载，在Oracle模式下，clob可仿照char类型，直接通过insert插入。
### date
### datetime
用于日期和时间的记录。默认格式：YYYY-MM-DD HH:MM:SS:FFF，可通过GL_DATETIME环境变量来修改。
建一个含datetime列的表：
```sql
create table 表名  (列名 datetime year to second);
```
“year to second”规定了datetime列的格式为YYYY-MM-DD HH:MM:SS，若只需精确到月份，可“year to month”，最大可精确到3位毫秒（YYYY-MM-DD HH:MM:SS.FFF），即“year to fraction(3)”。

#### 相关函数
NUMTOYMINTERVAL

<br/><br/>

# 其他
### 执行sql脚本
```sql
-- gbase写法，在终端中执行
dbaccess 库名 脚本路径

-- Oracle写法，在sql中执行
@脚本路径
```

若要将脚本执行结果输出到文件，则：
```sql
-- gbase写法，在终端中执行
dbaccess 库名 脚本路径 -> 结果文件路径

-- Oracle写法，在sql脚本头部加入：
spool 结果文件路径
-- 在sql脚本尾部加入：
spool off
```
若不希望Oracle将执行结果输出到屏幕，可以在sql脚本头部加入：
```sql
set termout off
```
### 查看sql执行时间
```sql
-- gbase写法，在终端中执行
export DBACCESS_SHOW_TIME=1

-- Oracle写法，在sql中执行
set timing on;
```
### 查看执行计划
```sql
-- gbase写法，在sql中执行，在当前路径下生成文件sqexplain.out
set explain on;

-- oracle写法，在sql中执行，for后面接需要查看执行计划的语句
--（只会生成执行计划，目标语句不会执行）
explain plan for SQL语句
select * from table (DBMS_XPLAN.DISPLAY);
```
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
### Oracle用户管理
```sql
create user 用户名 identified by 密码;
-- 若提示“invalid common user or role name”，在用户名前面加上“C##”

-- 授予用户权限
grant connect, resource, dba to 用户名;

-- 连接到数据库
conn 用户名;

-- 查看当前用户（在sql中执行）
show user
```
