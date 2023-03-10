**<font color='ff0000'> 若无特殊说明，则以下SQL语法均适用于GBase数据库，大部分适用于Oracle数据库。</font>**
<br/>


# select语法顺序与执行顺序
```sql
-- 语法顺序
select ... from ... where ... group by ... having ... order by ... limit ...

-- 执行顺序
from -> where -> group by -> having -> select -> distinct -> order by -> limit
```
<br/><br/>

# 查看库名、表名和视图
```sql
-- gbase写法，查看当前库中表名
info tables;

-- Oracle写法，查看当前库中表名
select table_name from user_tables;

--Oracle写法，查看当前库名
select name from v$database;
```
<br/>

# 基本操作
```sql
-- 查询全部列
select * from 表名;

-- 返回满足指定条件的结果集
select 列表达式 from 表名 where 条件;

-- 返回去重后的结果集
select distinct 列表达式 from 表名;

-- 跳过查询结果的前K行
select skip K 列表达式 from 表名;

-- 返回查询结果的前K行
select first K 列表达式 from 表名;
-- 或者
select 列表达式 from 表名 where rownum <= K;
```
<br/>

# 集合操作
```sql
-- union：对两个select的结果进行并集操作，去除重复行，并按默认规则排序
select 列表达式 from 表1 union select 列表达式 from 表2;

-- union all：对两个select的结果进行并集操作，不去除重复行也不排序
select 列表达式 from 表1 union all select 列表达式 from 表2;

-- intersect：对两个select的结果进行交集操作（即取两个select结果的共同部分）
select 列表达式 from 表1 intersect select 列表达式 from 表2;

-- except：对对两个select的结果取差集（第一个select结果中去掉和第二个select结果相同的部分）
select 列表达式 from 表1 except select 列表达式 from 表2;
```
<br/>

# 别名
常用于涉及多表操作的select语句中，为表或列增加别名以简化sql语句。
```sql
-- 为表增加别名
select 列表达式 from 表名 as 别名;
-- 为列增加别名
select 列表达式 as 别名 from 表名;
```
在Oracle中，关键字“as”可省略。
<br/><br/>

# case when
```sql
case 
    when 判断条件1 then 列表达式1
    when 判断条件2 then 列表达式2
    when 判断条件3 then 列表达式3
    ... 
    else 列表达式4
end
```
case when整体可作为一个列表达式，或者一个值使用。
<br/><br/>

# join
![](SQL笔记（DQL语句）_1.png)

join用于把多个表中的行结合起来。
首先在待结合的多个表中分别指定一个列，然后这些表对应列中值相等的合并为一行。指定列的列名可以互不相同。
```sql
select 列表达式 from 左表名 join 右表名 on 左表.列名 = 右表.列名
```
join语法中常用的有inner join、full join、left join和right join四种，区别见上表。

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
### inner join
将多张表对应列中值相等的合并为一行，若不相等或者值缺少，则整行舍弃。
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

### full join
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

### left join
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

### right join
同理，合并时对应列中值不相等或者缺少，右表中对应行保留，左表中对应行舍弃。
<br/><br/>

# pivot
将某列中的各个字段作为列名，形成多个新列。语法：
```sql
select * from 表名 pivot(
    列表达式 --作为新列的字段
    for 列名 in(列中字段1, 列中字段2, 列中字段3 ...) -- 字段1、2、3均作为新列的列名
);
```
举个例子：[LeetCode 1179](https://leetcode.com/problems/reformat-department-table/description/)：
```sql
select * from Department pivot(
    sum(revenue) for month in (
        'Jan' Jan_Revenue, 
        'Feb' Feb_Revenue, 
        'Mar' Mar_Revenue, 
        'Apr' Apr_Revenue, 
        'May' May_Revenue, 
        'Jun' Jun_Revenue, 
        'Jul' Jul_Revenue, 
        'Aug' Aug_Revenue, 
        'Sep' Sep_Revenue, 
        'Oct' Oct_Revenue, 
        'Nov' Nov_Revenue, 
        'Dec' Dec_Revenue
    )
);
```
<br/><br/>
# unpivot
将表中多个列（假设这几个列名为C1、C2、C3……）缩减为一个聚合列（假设聚合列列名为A，C1、C2、C3……与A中的值一一对应），而原先C1、C2、C3……列中对应的值也被整合到一个新列C中。语法如下：
```sql
unpivot (C for A in (
    C1 as 'C1对应的值', 
    C2 as 'C2对应的值',
    C3 as 'C3对应的值',
    ...
))
```

举个例子，[LeetCode 1795](https://leetcode.com/problems/rearrange-products-table/)：

输入：
| product_id | store1 | store2 | store3 |
| --- | --- | --- | --- |
| 0   | 95  | 100 | 105 |
| 1   | 70  | null| 80  |

输出：
| product_id | store  | price |
| --- | ------ | --- |
| 0   | store1 | 95  |
| 0   | store2 | 100 |
| 0   | store3 | 105 |
| 1   | store1 | 70  |
| 1   | store3 | 80  |
```sql
select 
    product_id, store, price 
from 
    Products 
unpivot (price for store in (
    store1 as 'store1',
    store2 as 'store2',
    store3 as 'store3'
));
```
<br/><br/>

# where
### 判断元素是否在集合中
使用in或not in关键字。
```sql
select 列表达式 from 表名 where 列名 in 集合或子查询;
```
其中，集合可通过以下形式定义：
```sql
(值1, 值2, 值3 ...)
```

### 判断元素是否为空
使用is NULL或is not NULL关键字。
```sql
select 列表达式 from 表名 where 列名 is NULL;
```
### like 模糊查询
适用于char、varchar等类型，一般用于where子句中，来搜索符合条件的字符串。同样，可以使用“not like”来避开符合条件的字符串。

使用like查询，可使用类似于正则表达式的特殊字符来匹配目标字符串。若不使用特殊字符，则like功能与“=”相同，进行全词匹配。
#### 使用“%”来匹配0个或多个字符
```sql
-- 寻找以“Tom”开头的字符串
select * from Person where name like 'Tom%';
-- 寻找以“Jackson”结尾的字符串
select * from Person where name like '%Jackson';
-- 寻找含有“Van”的字符串
select * from Person where name like '%Van%';
```
#### 使用“_”限制匹配的字符数量
每个“_”表示匹配一个任意字符。
```sql
-- 寻找以“Tom”开头，且后面还有任意4个字符的字符串
select * from Person where name like 'Tom____';

-- 寻找含一个“A”，且其前后各有一个字符的字符串
select * from Person where name like '_A_';
```

#### 正则表达式匹配
使用以下函数：
regexp_like(列名，正则表达式)
```sql
-- 寻找以“BMW X”开头，后面跟着一个字符3、4或5的字符串，也就是匹配“BMW X3”、“BMW X4” 、“BMW X5”这三种字符串。
select * from Car where name regexp_like(name, 'BMW X[3-5]');
```
<br/><br/>

# group by
一般和聚合函数一起使用。
group by后接列名，将表中的每行按指定列进行分组。也就是说，对于每一行，它们指定列的值相等的话会被分到同一组。
```sql
select 列表达式 from 表名 group by 指定列;
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

也就是说，对于select后的列表达式中出现的列，若取列的原值，则该列必须同时作为group by的对象，否则该列必须经聚合函数计算。
```sql
-- 非法写法
select name, id from student group by name;

-- 合法写法
select name, id from student group by name, id;
select name, max(id) from student group by name;
```
<br/><br/>

# having
用法与where类似，后面接条件判断。但两者在select语句中的执行顺序不同。where过滤原始表或join运算后的表中不符合条件的行，having则在group by之后执行，用于过滤掉不符合条件的组。
<br/><br/>

# order by
给出列名，按指定列中的值大小进行升序排序。默认为升序排序。若要按降序排行，添加关键字“desc”。如果排序条件涉及多列，则继续在语句后面添加列名。
```sql
-- 降序
select 列表达式 from 表名 order by 列名 desc

-- 按列名1排序，若列1对应值相同，则按列2排序
select 列表达式 from 表名 order by 列1 desc, 列2 asc,  ...
```
注意：order by无法对子查询使用。
<br/><br/>


# 函数
### 字符型相关
```sql
-- 连接两个字符串
concat(列表达式, 列表达式)

-- 取字符串长度
length(列表达式)

-- 转大写字母
upper(列表达式)

-- 转小写字母
lower(列表达式)

-- 取字符串从pos开始的连续len个字符（字符串首位坐标为1）
substr(列表达式, pos, len)

-- 返回子串在主串自第pos位后第k次出现的位置
-- 参数pos及k可省略，pos及结果按字符算位置
instr(主串, 子串, pos, k)

-- 同instr，pos及结果按字节算位置
instrb(主串, 子串, pos, k)
```

### 数值型相关
```sql
-- 计算指定列中行的数量
count(列表达式)

-- 求指定列中值的和
sum(列表达式)

-- 求指定列平均值
avg(列表达式)

-- 求指定列中最大值
max(列表达式)

-- 求指定列中最小值
min(列表达式)

-- 求指定列对指定值取模后的结果
mod(列表达式，值)
```
### 类型转换

#### to_date

#### to_char

#### to_number

### 其他
#### nvl
空值判断函数，如果列表达式A的结果非NULL，则返回A的值，否则返回B的值。
```sql
nvl(列表达式A, 列表达式B)
```
#### listagg
用于将在某列中，多行的值聚合成一行，语法：
```
listagg(列名, 分隔符) within group (order by 列名) as 新列名
```
指定列下，同组的多个值将聚合到一行之中，排序后以指定分隔符隔开。举个例子：[LeetCode 1484](https://leetcode.com/problems/group-sold-products-by-the-date/)：

输入：
| sell_date  | product    |
| --         | --         |
| 2020-05-30 | Headphone  |
| 2020-06-01 | Pencil     |
| 2020-06-02 | Mask       |
| 2020-05-30 | Basketball |
| 2020-06-01 | Bible      |
| 2020-06-02 | Mask       |
| 2020-05-30 | T-Shirt    |

输出：
| sell_date  | num_sold | products                     |
| -- | -- | -- |
| 2020-05-30 | 3        | Basketball,Headphone,T-shirt |
| 2020-06-01 | 2        | Bible,Pencil                 |
| 2020-06-02 | 1        | Mask                         |

```sql
select 
    to_char(sell_date, 'YYYY-MM-DD') as sell_date,
    count(product) as num_sold,
    listagg(distinct product, ',') within group (order by product) as products
from 
    activities
group by 
    sell_date;
```
<br/><br/>

# 参考资料
[SQL 教程 | 菜鸟教程](https://www.runoob.com/sql/sql-join.html)

[SQL中select的执行顺序](https://blog.csdn.net/weixin_43480466/article/details/122974596)