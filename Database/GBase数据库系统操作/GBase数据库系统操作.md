# GBase管理命令
## onstat
用于查看数据库各项信息，后面可接参数查看指定信息
```sql
-     查看数据库基本信息及运行模式（有On-Line、Off-Line、Quiescent、Read-Only、Recovery等模式）
-c    查看实例配置文件（onconfig）
-d    查看dbspace和chunk信息
-k    查看锁信息
-l    查看物理日志和逻辑日志相关信息
-m    查看最近的运行日志（即online.log）
-x    查看事务信息
-V    查看软件版本信息（-version亦可）
```

onstat -g命令用于查看数据库调试信息
```sql
-g ath    查看实例的线程
-g cmsm   查看各个实例的连接管理器
-g dis    查看实例的整体信息，包括安装目录和配置文件（sqlhosts和onconfig）
-g dri    查看高可用集群的数据同步信息
-g env    查看环境变量
-g glo    查看实例虚拟处理器的信息
-g sql    查看隔离级别和锁模式
```
## oninit
用于启动数据库软件，不加任何参数时将数据库从Off-Line模式转为On-Line模式。
```sql
-i    初始化rootdbs并从Off-Line模式转为On-Line模式（一般仅安装完成后第一次启动时使用该命令）
-s    将数据库从Off-Line模式转为Quiescent模式
-v    启动时显示提示信息 
-y    对所有交互信息均以“yes”回应
```
## onmode
主要用于更改数据库运行模式，部分命令可更改数据库运行参数。
```sql
-k    将数据库转为Off-Line模式
-m    从Quiescent模式转为On-Line模式
-y    对所有交互信息均以“yes”回应
-s    从On-Line模式转为Quiescent模式
-u    立即从On-Line模式转为Quiescent模式
```
-s和-u的区别在于，执行前者后，现存用户仍能继续使用，但新用户会被拒绝连接至数据库。待所有用户退出后数据库才转为Quiescent模式。后者执行后所有用户立刻被断开，所有未提交事务rollback，数据库立刻切换为Quiescent模式。
## onclean
当使用onmode命令无法关闭数据库，或数据库无法重启时，使用onclean强制关闭
```sql
-k    清空共享内存，关闭数据库
-y    对所有交互信息均以“yes”回应
```
## oncheck
用于查看磁盘结构，包括其中的索引、表空间等结构。
```sql
-pt 数据库名:表名    查看表的存储情况
-pT 数据库名:表名    和-pt类似，但会额外显示索引的情况
```
## onspaces
用于管理数据库的存储空间。
```sql
-a dbspace名       往指定dbspace中添加chunk
-d dbspace名       删除指定dbspace
-c -d dbspace名    创建dbspace（支持多种类型）并指定其名字
-c -S dbspace名    创建sbspace，并指定其名字
-c -b dbspace名    创建blobspace，并指定其名字
-c -P dbspace名    创建plogdbs，并指定其名字

# 以下参数在创建或添加dbspace时使用
-p 路径    指定dbspace的文件路径
-s 数字    指定dbspace的初始chunk的大小
-o 数字    为达到dbspace所需的偏移量
```

## onparams 
用于增减逻辑日志文件
```sql
-a -d dbspace名 -s 整数    在指定dbspace中添加一个指定大小的逻辑日志
-d -l 逻辑日志编号         删除指定的逻辑日志（其编号可用onstat -l命令查得）

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
| sysdepend | 所有表和视图的依赖信息 |
