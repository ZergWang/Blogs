### onstat
```sql
-- 查看数据库状态
-- 有On-Line、Off-Line、Quiescent、Read-Only、Recovery等模式
onstat - 

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

-- 查看运行日志（即online.log）
onstat -m

-- 查看事务信息
onstat -x

-- 查看实例的版本
onstat -V
--或者
onstat -version
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