# 文件，标准，流

<br/><br/>


# 特殊符号
### CR与LF
全称“Carriage Return”和“Line Feed”。
|字符|ASCII码|转义字符|功能| 十六进制编码
| ------- | --- | ---| --- | --- |
|回车（Carriage Return）|13|\r|光标移至当前行行首| 0x0D |
|换行（Line Feed）|10|\n|光标下移一行      | 0x0A |


平时所说的“换行”，即光标移至下一行行首，在Windows中使用“CRLF”控制，但在Linux中仅用“LF”即可。

在使用printf时，仅使用“\n”作换行处理。这是因为Windows下的编译器会自动在“\n”前补上“\r”。例如：
```cpp
FILE *fp;
fp = fopen("a.out", "w");
fprintf(fp, "1\n2");
fclose(fp);
```
用十六进制编辑器查看a.out，发现在“0A”前被补上了一个“0D”。
```
31 0D 0A 32
```

如果 fprintf 输出“\r\n”或者“\n\r”，实际上会输出为“0D 0D 0A”或者“0D 0A 0D”。

### 空字符
又称为“字符串结束符”。
|字符|ASCII码|转义字符| 十六进制编码
| ---| --- | ---| --- |
|空字符（Null）|0|\0| 0x00 |

打印空字符会发现其没有形状且不占位置，例如，打印“a\0b”，显示为“ab”。

C一般以空字符作为字符串结束的标志。使用scanf 及 “%s” 读入的字符串会在字符串末尾自动补上一个空字符。
### 空白字符
共有六种空白字符，除了回车（CR）和换行（LF），还有：

|字符|ASCII码|转义字符|功能| 十六进制编码
| ---| --- | ---| --- | --- |
|空格（Space）    |32|无需转义| | 0x20 |
|换页（Form Feed）|12|\f|将当前位置移到下页开头| 0x0C |
|水平制表符（Horizontal Tab）|9|\t|     | 0x09|
|垂直制表符（Vertical Tab）  |11|\v|    | 0x0B|

<br/><br/>


# C++文件输入输出函数
### iostream与fstream
### get与cin.get

<br/>

### getline与cin.getline

<br/>

### 含空格的字符串（字符数组）输入输出
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

# C文件输入输出函数

在C语言中，文件读写通过一个FILE类型的指针进行。该指针同样为文件访问的唯一标识。在各类文件读写函数中，FILE指针作为参数来指定要操作的文件。

FILE指针变量的声明：
```cpp
FILE *fp;   //一般变量名为fp，即file pointer缩写
```


### fopen
打开需要读写的文件，函数原型：
```cpp
FILE *fopen(const char *filename, const char *mode)
```
filename为文件路径，mode为文件读写模式，其中：
| 模式 | 描述 |
| ---  | --- |
| r    | 从原文件的开头读取，若原文件不存在则执行失败，返回NULL|
| w    | 从原文件的开头写入，若原文件不存在则创建一个新的|
| a    | 从原文件的末尾写入，若原文件不存在则创建一个新的|
| b    | 以二进制形式处理文件|
| +    | 同时进行读写操作    |

以上这5种模式可叠加使用，如“w+”意思为读写一个文件，若文件不存在则创建一个。

函数返回值为一个FILE指针。若函数执行错误，返回NULL，并设置全局变量errno来标识错误。

### fclose
关闭文件，函数原型：
```cpp
int fclose(FILE *stream)
```
参数为FILE指针。成功关闭文件，返回零，否则返回EOF。

### fscanf
读取文件，函数原型：
```cpp
int fprintf(FILE *stream, const char *format, ...)
```
用法和scanf类似，多了一个FILE指针类型的参数，用于指定要读取的文件。

函数执行成功则返回读取的字符数，否则返回EOF。

常用的类型说明符（即format）：
| 符号 | 对应类型 |
| --- |  --- |
| %d  |  十进制整数，数字前可接“+”或“-”号  |
| %c  | 单个字符      |
| %s  | C风格字符串，连续读入直到遇到空白字符  |
| %o  | 八进制整数（数字前有一个前导零“0”） |
| %x  | 十六进制整数（数字前有“0x”）  |
| %p  | 指针  |
| %f  | 浮点型数（float） |
| %lf  | 双精度浮点型数(double) |
| %i  | 同时支持八、十、十六进制整数 |
| %u  | 无符号的十进制整数（unsigned int）|
| %lld  | long long类型整数|
### fprintf
写入文件，函数原型：
```cpp
int fprintf(FILE *stream, const char *format, ...)
```
用法和printf类似，多了一个FILE指针类型的参数，用于指定要写入的文件。

函数执行成功则返回写入的字符数，否则返回EOF。
### fgetc
```cpp
int fgetc(FILE *stream)
```
从指定文件中读取一个无符号字符，返回值即为该字符，如果到达文件末尾或发生错误，则返回EOF。
### fgets
```cpp
char *fgets(char *str, int n, FILE *stream)
```
从指定文件中读取最多n个字符（包括空字符）。读取过程中若遇到换行、文件末尾或者已达到n个字符则结束。将读取到的字符存入str中。

函数返回值与str相同。如果到达文件末尾、没有读取到任何字符或者发生错误，str的内容不变，返回空指针。

### fputc

### fputs

### fread

### fwrite

### feof

<br/><br/>
 


# 参考资料
[菜鸟教程](https://www.runoob.com/)

[对C标准中空白字符的理解](https://blog.csdn.net/boyinnju/article/details/6877087)