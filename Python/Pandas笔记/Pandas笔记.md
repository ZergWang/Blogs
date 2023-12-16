# 简介


# Dataframe初始化
```python
df = pd.read_excel(file_path)   # 从Excel从读取

df2 = pd.DataFrame(columns=df.columns) # 从Dataframe中读取并保留原Dataframe的列结构
```


<br/><br/>

# 对某列求值



<br/><br/>

# 输出为excel

```python
# 将dataframe保存为Excel


# 将不同dataframe输出到同一张表的不同sheet中：
with pd.ExcelWriter('output.xlsx') as writer:  
    df1.to_excel(writer, index=False, sheet_name='1')
    df2.to_excel(writer, index=False, sheet_name='2')

```