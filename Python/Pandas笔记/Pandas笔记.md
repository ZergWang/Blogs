# 简介
Pandas是一个Python的第三方库，一般用于表格数据的处理。习惯上一般调用并简称为“pd”。
```py
import pandas as pd
```

在Pandas中，对表格信息的处理一般基于Dataframe和Series这两种数据结构。其中，Dataframe是一种二维数据结构，一般可用于存储单张表的所有信息，包括索引，列名，行名等。Series为一维数据结构，一般存储表的某行或某列的所有信息。

本篇笔记可视为Pandas官方文档的极简索引，仅记载常用函数及其基本的用法与讲解。若需详细了解可通过函数名处的超链接阅读官方文档。

## Dataframe结构简介

简单来说，每个Dataframe可以视为由多个Series构成。但实际上，多个Series仅构成了Dataframe的数据部分。除了这些数据，Dataframe还有行索引（即行名）以及列索引（即列名）。如下表所示，当将以下该表的数据读入到一个Dataframe中时，其首行数据（第0行数据）实际为 ```[160, 150, 165]```，```[小智, 小霞, 小刚]```则存储为列索引。同理Dataframe的首列为```[160, 50]```，```[身高, 体重]```被存储为行索引。


| |小智 |小霞 |小刚|
| -- | -- | -- | --|
|身高| 160 |150 |165|
|体重| 50 |40 |55|





<br/><br/>

# 读写Dataframe
## 构造Dataframe
### 从Excel从读取
使用 [read_excel](https://pandas.pydata.org/docs/reference/api/pandas.read_excel.html) 函数，将excel的内容读取到一个dataframe变量中。

```py
df = pd.read_excel(file_path)   #直接从指定excel文件中读取
```

该函数默认原始excel文件中的首行数据为列索引，且默认原始excel文件中没有行索引。因此默认情况下该函数会为df自动生成一个行索引。如果原始文件中有行索引，无需额外生成，需要在读取时指定：
```py
# 令原始excel文件中的第k列数据为行索引，如果首列为行索引，则k=0
df = pd.read_excel(file_path, index_col = k)   
```

### 基于现有dataframe构造
使用 [DataFrame](https://pandas.pydata.org/docs/reference/api/pandas.DataFrame.html) 函数直接构造一个dataframe变量。
```py
# 基于旧Dataframe的数据构造一个，相当于复制
df2 = pd.DataFrame(data=df) 

# 基于一个旧的Dataframe的列结构来构造，不保留其数据
df2 = pd.DataFrame(columns=df.columns) 
```
### 基于list构造
```py
# 假设data为list类型
df = pd.DataFrame(data) 
```
### 基于字典（dict）构造
将字典转换成list类型后再构造成dataframe：
```py
# 假设data为dict类型
df = pd.DataFrame([data]) 
```
此时构造出的dataframe只有两行数据，key为一行，value为另一行。

也可以使用[DataFrame.from_dict](https://pandas.pydata.org/pandas-docs/stable/reference/api/pandas.DataFrame.from_dict.html)方法构造：
```py
# 假设data为dict类型
df = pd.DataFrame.from_dict(data, orient='index') 
```
此时构造出的dataframe有两列数据，key为索引列，value为第0列（如果想继续key为一行，value为另一行，则将参数改为```orient='columns'```）


## 保存为excel
使用 [to_excel](https://pandas.pydata.org/docs/reference/api/pandas.DataFrame.to_excel.html) 方法将Dataframe变量保存到指定的Excel文件中。

该函数默认会将行、列索引都保存到excel文件中。

```py
# 直接将变量df的内容保存到result.xlsx中
df.to_excel("result.xlsx")

# 不保存行索引信息：
df.to_excel("result.xlsx", index=False)

# 将不同dataframe输出到同一张表的不同sheet中：
with pd.ExcelWriter('output.xlsx') as writer:  
    df1.to_excel(writer, sheet_name='1')
    df2.to_excel(writer, sheet_name='2')
```

<br/><br/>

# 检索Dataframe
## 获取行列索引
```py
df.index  # 行索引
df.columns # 列索引
```
## 获取行数与列数
```py
df.shape[0] 或 len(df) # 获取行数
df.shape[1]            # 获取列数
```

## 逐行遍历
[iterrows](https://pandas.pydata.org/docs/reference/api/pandas.DataFrame.iterrows.html) 以及 [itertuples](https://pandas.pydata.org/docs/reference/api/pandas.DataFrame.itertuples.html) 均可实现。

iterrows 在每次迭代时，会返回当前行的index number和Series类型的当前行数据：
```py
for index, data in df.iterrows():
    print(index, data)
```

itertuples 在每次迭代时，仅返回一个pandas类型的tuple，该tuple的第一项为当前行的index number，后面各项为当前行的各项数据。
## 判断单元格是否为空
使用isnull函数判断：
```py
if pd.isnull(df.iloc[i, j])
    print('这个单元格是空的')
```


## 提取指定行、列的数据
可使用[iloc](https://pandas.pydata.org/docs/reference/api/pandas.DataFrame.iloc.html)函数

```py
# 取第i行数据
data = df.iloc[i]

# 取第a行到第b行的数据
data = df.iloc[a:b]

# 取第j列数据
data = df.iloc[:, j]
# 取第i行第j列的数据
data = df.iloc[i, j]
```

<br/><br/>

# 修改dataframe

## 在dataframe的末尾添加一行数据
可使用 [append](https://pandas.pydata.org/pandas-docs/version/1.4/reference/api/pandas.DataFrame.append.html) 或 [concat](https://pandas.pydata.org/docs/reference/api/pandas.concat.html) 方法。当前新版本的pandas已经弃用append方法了，如果仍要使用，可通过 _append 实现。

```py
df = df._append(new_row) # 在df末尾添加一行，其中new_row为series类型
```
## 更改列名
```py
df.columns = ['A','B']
```
## 修改索引列列名
```py
df = df.reset_index().rename(columns = {'index':'新列名'})
```

<br/><br/>

# 初始化Series
## 构建新的Series
使用[Series](https://pandas.pydata.org/docs/reference/api/pandas.Series.html)函数构建。该函数返回一个Series类型变量。

例如，通过list构建Series：
```python
s = pd.Series(data=my_list)
```

# 检索Series

## 提取指定位置的数据
可直接使用切片提取
```py
# 通过索引名取值
s['姓名']
# 通过索引值取值（索引值从0开始，如下是取Series中第3项的值）
s[3]
```

# 修改Series
## Series转为list
直接使用[Series.to_list](https://pandas.pydata.org/docs/reference/api/pandas.Series.to_list.html)函数

```py
dataList = s.to_list
```

<br/><br/>

