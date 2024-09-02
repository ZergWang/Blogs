# 简介
Hadoop是一种大数据的分布式存储和计算框架，核心部件有HDFS（分布式文件存储系统）、YARN（分布式资源管理器）、MapReduce（分布式计算引擎）。本文为Windows下单机版Hadoop的安装与使用笔记，java版本为17.0.6，不安装HBase。

<br/><br/>

# 安装
## 下载
Hadoop压缩包下载：[下载链接](https://archive.apache.org/dist/hadoop/common/hadoop-3.1.0/hadoop-3.1.0.tar.gz)

由于Hadoop本身不兼容Windows系统，因此需额外下载Windows兼容工具：[下载链接](https://github.com/s911415/apache-hadoop-3.1.0-winutils)

Windows兼容工具是一个bin文件夹，直接覆盖掉Hadoop中的bin文件夹即可。

## 系统变量配置
### HADOOP_HOME
在Windows系统设置中新增一个名为HADOOP_HOME的系统变量：
```bash
变量名：HADOOP_HOME
变量值：C:\Softwares\Hadoop\hadoop-3.1.0
```
变量值根据Hadoop具体安装路径确定

### JAVA_HOME
在Windows系统设置中新增一个名为JAVA_HOME的系统变量：
```bash
变量名：HADOOP_HOME
变量值：C:\Program Files\Microsoft\jdk-17.0.6.10-hotspot\bin
```
变量值根据Java具体安装路径确定

### 修改系统变量的Path
Path中添加以下两个路径：
```bash
C:\Softwares\Hadoop\hadoop-3.1.0\bin
C:\Program Files\Microsoft\jdk-17.0.6.10-hotspot\bin
```

## Hadoop配置文件修改
### hdfs-site.xml
在文件末尾的```<configuration>```中添加：
```xml
<configuration>
    <property>       
        <name>dfs.replication</name>       
        <value>1</value>   
    </property>   
    <property>       
        <name>dfs.namenode.name.dir</name>       
        <value>C:\Softwares\Hadoop\data\namenode</value>
    </property>   
    <property>       
        <name>dfs.datanode.data.dir</name>     
        <value>C:\Softwares\Hadoop\data\datanode</value>
    </property>
</configuration>
```
配置文件中涉及到datanode和namenode的文件夹的地址，这个可以根据需要设置，但确保对应的文件夹存在。

### core-site.xml
在文件末尾的```<configuration>```中添加：
```xml
<configuration>
    <property>
        <name>fs.defaultFS</name>
        <value>hdfs://localhost:9000</value>
    </property>
</configuration>
```
端口号可自行定义，没有冲突就行。

### hadoop-env.cmd
修改文件中含JAVA_HOME的这一行：
```
@rem The java implementation to use.  Required.
set JAVA_HOME=C:\Progra~1\Microsoft\jdk-17.0.6.10-hotspot
```
地址填为Java的安装地址，如果Java安装在Program Files文件夹内，考虑到配置文件可能无法识别含空格的文件夹，可以将“Program Files”写成“Progra~1”。

<br/><br/>

# 初始化与启动hdfs
## 初始化
完成上述配置文件修改后重启电脑，然后可通过以下命令确定hdfs是否配置正确：
```bash
hadoop version
```

执行以下命令来初始化hdfs：

```bash
hdfs namenode –format
```
## 启动与关闭Hadoop
在Hadoop安装目录的sbin目录下执行以下命令：

```bash
.\start-all.cmd
```
执行后总计会弹出四个窗口，不要关闭。

之后在浏览器中访问http://localhost:9870/

如果页面能打开，说明启动成功。

关闭Hadoop使用以下命令：
```bash
.\stop-all.cmd
```
<br/><br/>

# 实战：使用C++编写word count
使用C++实现一个MapReduce风格的word count程序。Hadoop原生支持Java，如果要用非Java语言编写MapReduce程序，需要使用Hadoop Streaming来运行，因此在C++中需要使用标准输入输出来传递```<key value>```。

## C++程序编写
mapper.cpp
```cpp
#include <iostream>
#include <string>

using namespace std;

int main(){
    string s;
    while(cin>>s) 
        cout<<s<<"\t"<<"1"<<endl;
    return 0;
}
```
reducer.cpp
```cpp
#include <iostream>
#include <string>
#include <map>

using namespace std;

int main(){
    string key,num;
    map<string,int> count;
    map<string,int>::iterator it;
    while(cin>>key>>num){
        it = count.find(key);
        if(it!=count.end())
            it->second++;
        else
            count.insert(make_pair(key,1));
    
    }
    for(it=count.begin(); it!=count.end(); it++)
        cout<<it->first<<"\t"<<it->second<<endl;
    return 0;
}
```

## 数据准备
假设待统计词频的文件名为input.txt，首先需要将该文件上传到hdfs文件系统中。使用如下命令进行上传：
```bash
hdfs dfs -put 待上传文件名 / 
```
该命令将文件上传到hdfs文件系统的根目录中。
## 执行MapReduce
使用hadoop-streaming-3.1.0.jar来执行非Java语言编写的MapReduce程序。使用如下命令执行：
```bash
hadoop jar C:\Softwares\Hadoop\hadoop-3.1.0\share\hadoop\tools\lib\hadoop-streaming-3.1.0.jar -D mapred.job.name="wordcount" -input /input.txt -output /output --mapper mapper.exe --reducer reducer.exe
```
其中，-input指定了要统计词频的文件，-output指定了结果存储的位置，--mapper指定了执行map的程序（mapper.exe通过编译mapper.cpp得来），--reducer指定了执行reduce的程序（reducer.exe通过编译reducer.cpp得来）

## 查看结果
上述命令将结果存储在hdfs文件系统的output目录内，可用如下命令查看结果：
```
hdfs dfs -cat /output/*
```
<br/><br/>


# 参考资料
[Windows环境下安装Hadoop3.1.0全过程（超详细手把手一条龙教学）](https://blog.csdn.net/GCTTTTTT/article/details/126730436)

[windows 安装 hadoop3.3.6 和 hbase 2.5.7 以及解决遇到的一些坑](https://blog.csdn.net/qq_26564185/article/details/136768068)

[c++编程实现简单mapreduce程序](https://blog.csdn.net/feinifi/article/details/101123071)