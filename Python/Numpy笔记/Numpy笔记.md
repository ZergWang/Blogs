# 数组遍历
使用[nditer](https://numpy.org/doc/stable/reference/generated/numpy.nditer.html)函数遍历numpy数组：

```py
a = np.array([[1,2], [3,4]])

for i in np.nditer(a):   # 遍历数组并输出每个元素
    print(i)
```

由于nditer函数在遍历时默认令数组处于只读状态，如果要在遍历的同时修改数组各项元素（例如，每项元素乘2），需要使用以下代码：
```py
with np.nditer(a, op_flags=['readwrite']) as it:
    for i in it:
        i[...] = i * 2
```