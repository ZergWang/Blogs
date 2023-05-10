**<font color="ff0000">阅读本篇博文之前，建议先阅读本人的《C读写笔记》。</font>**

# iostream与fstream

<br/><br/>

# 输入函数
## get与cin.get


## getline与cin.getline


## 含空格的字符串（字符数组）输入输出
若字符串（字符数组）中无空格，可直接cin、cout。
对于C风格字符数组：
```cpp
char c[100];
scanf("%[^\n]",&c); //遇到回车才算读入结束
```
或者：
```cpp
cin.get(c,50);
```
表示读入c的前50位，若实际输入长度不够50，则有多少位收多少位。
（这个参数可以超过字符数组的长度！）
再或者：
```cpp
cin.getline(c, 50);
```
对于C++风格字符串：
```cpp
#include <string>
...
string s;
getline(cin,s);
```

<br/><br/>

# 参考资料
[菜鸟教程](https://www.runoob.com/)

[对C标准中空白字符的理解](https://blog.csdn.net/boyinnju/article/details/6877087)