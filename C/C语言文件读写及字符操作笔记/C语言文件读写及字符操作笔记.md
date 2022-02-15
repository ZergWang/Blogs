
**<font color='ff0000'>以下所有有关位置的参数，无论是C风格字符数组还是C++风格的字符串，首字符索引都为0。另外，下文无论是C风格字符数组还是C++风格的字符串，均统称为字符串。在需要区别时会特别指出。</font>**


# CR与LF
全称“Carriage Return”和“Linefeed”。
|字符|ASCII码|转义字符|功能| 十六进制编码
| ---| --- | ---| --- | --- |
|CR|13|\r|光标移至当前行行首| 0D |
|LF|10|\n|光标下移一行      | 0A|


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


# 读入
#### scanf
函数声明：
```cpp
int scanf(const char *format, ...)
```

int：%d
字符：%c
C风格字符数组：%s   （C++风格字符串不支持）
long long(unsigned long long)： %lld
float: %f
double :  %lf

 #### 多组数据连续scanf
```cpp
while (scanf("%d",&n)!=EOF)
```
#### printf
输出的时候可用占位符代表变量，也可直接输出字符内容。
```cpp
int a,b,c;
scanf("%d%d",&a,&b);
c=a+b;
printf("%d+%d=%d\n",a,b,c);
```

#### 含空格的字符串（字符数组）输入输出
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

# 文件读写
#### 打开及关闭文件
```cpp
FILE *fp;
fp = fopen(文件路径)

...

fclose(fp);
```
#### 文件读入
```cpp
fgets
```
<br/><br/>

# 字符串（字符数组）处理函数
#### 获取长度
```cpp
string s;
s.size()

char c[100];   
strlen(c) 
```
#### 比较
比较规则：自左向右逐个字符比较，直到区分或者某字符串结束，此时认为短的那个字符串小。
C中可使用函数strcmp或strncmp。
```cpp
int strcmp(const char *str1, const char *str2) 
//比较两个字符数组
int strncmp(const char *str1, const char *str2, size_t n) 
//比较两个字符数组的前n位
```
若c1==c2，则返回零；若c1<c2，则返回负数；若c1>c2，则返回正数。

C++中直接>、<、==即可，或者：
```cpp
string str1, str2;
str1.compare(str2);   
str1.compare(pos1,len1,str2,pos2,len2);
```
#### 连接
对于C++风格字符串，可以直接使用加号连接，如：
```cpp
string s="a";
s+='b';
s+="cd";
```
也可以：
```cpp
str1.append(str2);    //与str1+=str2等效
str1.append(str2,pos,len);  //在str1的末尾加上str2的第pos位开始的连续len个字符
```

对于C风格字符数组：
```cpp
strcat(c1,c2);    //将c2加到c1中
strncat(c1,c2,n); //将c2的前n个字符加到c1末尾。
```
#### 查找
对于C风格字符数组：
```cpp
strchr(c1,char);  //找到字符char在字符数组c1中第一次出现的位置，返回包括该位置及其之后的字符。
strstr(c1,c2);    //找到字符数组c2在字符数组c1中第一次出现的位置，返回包括该位置字符及其之后的c的子数组。
```
若没找到则无输出。

对于C++风格字符串：
```cpp
str1.find(str2);  //返回一个整数，表示str2在str1中第一次出现的位置，若没有返回-1
str1.rfind(str2); //同上，但从后往前找
```
#### 清除
对C++风格字符串：
```cpp
string s;
s.clear();//全部清除
s.erase(pos,len);
```
### 插入
对C++风格字符串：
```cpp
str1.insert(pos1,str2);  
str1.insert(pos1,str2,pos2,len2);  
str1.insert(pos1,num,char);   // num是插入次数，char是要插入的字符。
```
 #### 提取
对C++风格字符串：
```cpp 
str2 = str1.substr(pos);     //将str1的第pos位及之后的所有字符赋值给str2
str2 = str1.substr(pos,len); //将str1从第pos位开始连续len个字符赋值给str2
```
#### 填充
```cpp
#include <cstring>
...
memset(a,0,sizeof(a))
```
#### 替换
对于C风格字符数组：
```cpp
strcpy(str1,str2);    //等效于str1=str2;
strncpy(str1,str2,n); //str1的前n位替换为str2的前n位
```
对于C++风格字符串：
```cpp
str1.replace(pos,len,str2);  //把str1从第pos位开始的连续len位替换成str2
str1.replace(pos1,len1,str2,pos2,len2); //多出的两个参数指定了str2参与替换的部分
```

#### 字符串与字符数组相互转换
字符数组可直接赋值给字符串。

字符串s转字符数组c：
```cpp
strcpy(c, s.c_str());
```
#### 字符串转数字
字符数组转数字：（不仅可以转成整数，也可以识别小数点，直接转成实数。）
```cpp
char c[]="123.456";
int t;
double d;
sscanf(c,"%d",&t);
sscanf(c,"%lf",&d);
```

字符串转数字：
```cpp
#include <sstream>
...
string str = "123.45";
double d;
stringstream ss;
ss << str;
ss >> d;
```
#### 数字转字符串
数字转字符数组：
```cpp
char c[20];
int t=123;
double d=12.34;
sprintf(c,"%d",t);
sprintf(c,"%lf",d);
```
数字转字符串str：(同样支持整数和实数)
```cpp
#include <sstream>
...
double a=12.34;
string str;
stringstream ss;
ss << a;
ss >> str;
```
<br/><br/>
 


# 参考资料
[菜鸟教程](https://www.runoob.com/)