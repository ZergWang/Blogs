# GBase管理命令
### onstat
用于查看数据库各项信息，后面可接参数查看指定信息
```
-     查看数据库基本信息及运行模式（有On-Line、Off-Line、Quiescent、Read-Only、Recovery等模式）
-c    查看实例配置文件（onconfig）
-d    查看dbspace和chunk信息
-k    查看锁信息
-m    查看最近的运行日志（即online.log）
-x    查看事务信息
```

onstat -g命令用于查看数据库调试信息
```
-g ath    查看实例的线程
-g cmsm   查看各个实例的连接管理器
-g dis    查看实例的整体信息，包括安装目录和配置文件（sqlhosts和onconfig）
-g dri    查看高可用集群的数据同步信息
-g env    查看环境变量
-g glo    查看实例虚拟处理器的信息
-g sql    查看隔离级别和锁模式
```
### oninit
用于启动数据库软件，将数据库模式转为On-Line
```
-i    初始化磁盘空间（仅数据库安装完成后第一次启动时使用）
-v    启动时显示提示信息 
-y    对所有交互信息均以“yes”回应
```
### onmode
主要用于更改数据库运行模式，部分命令可更改数据库运行参数。
```
-k    清理共享内存，将数据库转为Off-Line模式
-y    对所有交互信息均以“yes”回应
```
### onclean
当使用onmode命令无法关闭数据库，或数据库无法重启时，使用onclean强制关闭
```
-k    清空共享内存，关闭数据库
-y    对所有交互信息均以“yes”回应
```
### oncheck
用于查看磁盘结构，包括其中的索引、表空间等结构。
```
-pt 数据库名:表名    查看表的存储情况
-pT 数据库名:表名    和-pt类似，但会额外显示索引的情况
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