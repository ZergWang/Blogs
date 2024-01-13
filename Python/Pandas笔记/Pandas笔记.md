# 简介
Pandas是一个Python的第三方库，一般用于表格数据的处理。习惯上一般调用并简称为“pd”。
```py
import pandas as pd
```

在Pandas中，对表格信息的处理一般基于Dataframe和series这两种数据结构。其中，Dataframe是一种二维数据结构，一般可用于存储单张表的所有信息，包括索引，列名，行名等。series为一维数据结构，一般存储表的某行或某列的所有信息。

本篇笔记可视为Pandas官方文档的极简索引，仅记载常用函数及其基本的用法与讲解。若需详细了解可通过函数名处的超链接阅读官方文档。
<br/><br/>

# Dataframe初始化
## 从Excel从读取
使用 [read_excel](https://pandas.pydata.org/docs/reference/api/pandas.read_excel.html) 函数，将excel的内容读取到一个dataframe变量中。

```py
df = pd.read_excel(file_path)   #直接从指定excel文件中读取
```

## 直接构造dataframe
使用 [DataFrame](https://pandas.pydata.org/docs/reference/api/pandas.DataFrame.html) 函数直接构造一个dataframe变量。
```py
df2 = pd.DataFrame(columns=df.columns) 
# 基于一个旧的Dataframe变量来构造一个新的，保留原dataframe的列结构
```


<br/><br/>

# 数据检索
## 逐行遍历
[iterrows](https://pandas.pydata.org/docs/reference/api/pandas.DataFrame.iterrows.html) 以及 [itertuples](https://pandas.pydata.org/docs/reference/api/pandas.DataFrame.itertuples.html) 均可实现。

iterrows 在每次迭代时，会返回当前行的index number和Series类型的当前行数据：
```py
for index, data in df.iterrows():
    print(index, data)
```

itertuples 在每次迭代时，仅返回一个pandas类型的tuple，该tuple的第一项为当前行的index number，后面各项为当前行的各项数据。



<br/><br/>

# 向dataframe添加数据

## 在dataframe的末尾添加一行数据
可使用 [append](https://pandas.pydata.org/pandas-docs/version/1.4/reference/api/pandas.DataFrame.append.html) 或 [concat](https://pandas.pydata.org/docs/reference/api/pandas.concat.html) 方法。当前新版本的pandas已经弃用append方法了，如果仍要使用，可通过 _append 实现。

```py
df = df._append(new_row) # 在df末尾添加一行，其中new_row为series类型
```

<br/><br/>

# Dataframe保存为excel
使用 [to_excel](https://pandas.pydata.org/docs/reference/api/pandas.DataFrame.to_excel.html) 方法将Dataframe变量保存到指定的Excel文件中。

```py
# 直接将变量df的内容保存到result.xlsx中
df.to_excel("result.xlsx")

# 将不同dataframe输出到同一张表的不同sheet中：
with pd.ExcelWriter('output.xlsx') as writer:  
    df1.to_excel(writer, index=False, sheet_name='1')
    df2.to_excel(writer, index=False, sheet_name='2')

```