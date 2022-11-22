# 编译前的准备

本笔记适用于个人开发者在单机单节点下PostgreSQL软件的安装与调试。

#### 下载并配置编译环境

MSYS2是一个集成了MinGW、Cygwin等编译引擎的软件，同时集成了Shell命令以及编译环境配置等功能，专门用于在Windows平台下编译各类C/C++软件。

官方网站：https://www.msys2.org/

下载安装MSYS2，成功后打开MSYS2 MinGW x64，执行以下命令来安装gcc和gdb工具：
```bash
pacman -Syu
pacman --needed -S mingw-w64-x86_64-gcc base-devel
pacman --needed -S mingw-w64-x86_64-gdb
```
遇到选择时全都输入“y”即可。

#### 下载并解压PostgreSQL源码

可通过PostgreSQL的Github仓库下载，也可以从官网下载。

Github地址：https://github.com/postgres/postgres

官网地址：https://www.postgresql.org/ftp/source/
<br/><br/>

# 编译与安装
#### 生成makefile文件
MSYS2 MinGW x64下的命令与Shell一致，C盘根目录对应的地址为“/c/”。

打开MSYS2 MinGW x64，进入PostgreSQL源码目录后执行以下命令：
```bash
./configure --host=x86_64-w64-mingw32 --prefix=/c/pg/installer/ -enable-debug
```
其中，--perfix指定了编译完成后的安装包生成的位置（以下简称为安装目录），如上述命令，本人的安装目录为C盘下pg下的installer目录。

编译完成后进入源码目录，修改src目录下的Makefile.global文件，找到以“CFLAGS = -Wall”以及“CXXFLAGS = -Wall”开头的两行，将这两行命令中的“-O2”参数去掉。

#### 编译并安装PostgreSQL
打开MSYS2 MinGW x64，进入PostgreSQL源码目录后执行以下命令：
```bash
make
make install
```

#### 初始化数据库
在Windows下的cmd命令行中进入安装目录下的bin中，执行：
```bash
initdb -D C:\pg\data
```
该命令创建了一个单机单节点的数据库服务器。其中“-D”参数指定了PostgreSQL数据库的配置文件位置。本人的为C盘下pg下的data目录。同一台机器中可能有多个配置文件，PostgreSQL服务器通过各自的配置文件进行区分。

如果不想每次都要进入安装目录下的bin中执行命令，可将该目录加入系统环境变量。

#### 数据库注册（该步骤可跳过）
将数据库服务注册为Windows的系统服务，方便以后启停数据库服务。以下命令需要在有管理员权限的cmd中使用。
```bash
pg_ctl register -N pg -D "C:\pg\data" -w -S demand
```
参数解释如下：
- -w参数指定数据库服务器需等待操作完成，并给出操作的反馈

- -S参数指定数据库的启动方式是自动启动（auto）还是按需启动（demand）

- -D参数指定数据库配置文件的位置，与上一条命令中的保持一致

- -N参数指定PostgreSQL在Windows系统中对应的服务名（本人使用的服务名为“pg”），之后可使用含有该服务名的命令来启停数据库。

如果要撤销上述的注册，可：
```bash
pg_ctl unregister -N 服务名
```

至此，PostgreSQL数据库编译安装成功。
<br/><br/>

# 数据库管理命令
以下命令均在cmd命令行中执行。具体命令和参数可参考[官方网站](https://www.postgresql.org/docs/current/app-pg-ctl.html)
```bash
# 查看数据库运行状态
pg_ctl status -D 配置文件目录

# 启动数据库
pg_ctl start -D 配置文件目录
# 如果数据库已注册为Windows系统服务，则可在有管理员权限的cmd中：
net start 系统服务名

# 关闭数据库
pg_ctl stop -D 配置文件目录
# 如果数据库已注册为Windows系统服务，则可在有管理员权限的cmd中：
net stop 系统服务名

# 进入数据库（初始数据库名为postgres）
psql -d 数据库名
```
<br/><br/>

# 调试PostgreSQL
#### gdb调试
首先在psql命令行下查找数据库服务的进程号：
```sql
select pg_backend_pid();
```
然后直接在cmd中：
```bash
gdb attach 进程号
```
#### VS Code调试

<br/><br/>


# 参考资料
[pg_ctl](https://www.postgresql.org/docs/current/app-pg-ctl.html)

[How to build and debug PostgreSQL using MinGW](https://osmanthus.work/?p=164)

[BUILDING POSTGRESQL WITH MSYS2 AND MINGW UNDER WINDOWS](https://www.cybertec-postgresql.com/en/building-postgresql-with-msys2-and-mingw-under-windows/)

