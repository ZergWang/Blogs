# 简介


# Dataframe初始化
## 从Excel从读取

## 从既有Dataframe中读取



<br/><br/>

# 对某列求值



<br/><br/>

# 输出为excel

将不同dataframe输出到同一张表的不同sheet中：

```python
with pd.ExcelWriter('output.xlsx') as writer:  
    df1.to_excel(writer, index=False, sheet_name='1')
    df2.to_excel(writer, index=False, sheet_name='2')

```