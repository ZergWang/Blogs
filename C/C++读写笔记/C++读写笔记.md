**<font color="ff0000">阅读本篇博文之前，建议先阅读本人的《C读写笔记》。</font>**

# iostream与fstream

<br/><br/>

# 输入函数

## cin
（1）cin>>等价于cin.operator>>()，即调用成员函数operator>>()进行读取数据。
（2）当cin>>从缓冲区中读取数据时，若缓冲区中第一个字符是空格、tab或换行这些分隔符时，cin>>会将其忽略并清除，继续读取下一个字符，若缓冲区为空，则继续等待。但是如果读取成功，字符后面的分隔符是残留在缓冲区的，cin>>不做处理。
（3）不想略过空白字符，那就使用 noskipws 流控制。比如cin>>noskipws>>input;

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

# stringstream
[【C++】stringstream类 最全超详细解析](https://blog.csdn.net/weixin_45031801/article/details/136921743)

<br/><br/>


# 参考资料
[菜鸟教程](https://www.runoob.com/)

[对C标准中空白字符的理解](https://blog.csdn.net/boyinnju/article/details/6877087)