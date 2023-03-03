# 注意：
- 以下所有有关位置的参数，无论是C风格字符数组还是C++风格的字符串，首字符索引都为0。
- 下文无论是C风格字符数组还是C++风格的字符串，均统称为字符串。在需要区别时会特别指出。
- 使用C相关函数需调用string.h库。
- 在操作C风格字符数组时，请尽量使用C相关函数，而不是“+” “=”或关系运算符，这可能会导致无法预料的错误。
<br/><br/>


# 字符串字面量
### 定义
在程序中以明文展示的字符串，例如"123"、"AB\nCD"这种，被称为字符串字面量，也称字符串常量。

在C中，字符串字面量被视为字符数组：
```cpp
printf("%d", sizeof("ABC"));  //输出4（3个字符+1个结尾的空字符）
```

因此，可对字符串字面量使用下标：
```cpp
printf("%c", "ABC"[0]);    //输出A
```
此方法可用于打印二数组：
```cpp
for (int i=0; i<n; i++)
    for (int j=0; j<m; j++)
        printf("%d%c", a[i][j], " \n"[j == m-1]);  //C版本
        //cout << a[i][j] << " \n"[j == m-1];      //C++版本
```

### 存储与赋值
字符串字面量可以初始化数组的方式赋值给字符数组：
```cpp
char s[4] = "123";
```
此时字面量按字符数组的方式逐赋值给s。s的各项可以自由更改。

若将字符串字面量赋值给char*，编译器会开辟一块只读的内存空间给字符串字面量，然后将该内存的地址赋值给char*。
由于字面量被视为常量，对指针指向的内存地址进行修改是非法的，但由于指针本身为变量，反复令指针指向不同字面量的地址则是合法的。
```cpp
char *s = "123";
*(s+1) = '4';      //非法
strcpy(s, "456");  //非法，因为对s指向的内存进行修改
s = "456";         //合法
```
<br/><br/>


# 获取长度
### C
```cpp
size_t strlen(const char *str) //返回字符串的长度
```
注意：size_t一般被编译器定义为无符号int型，因此在计算字符串长度或别的需要对size_t类型的变量运算时，要注意避免给变量赋值为负数，这会导致不可预料的错误。

另外，strlen函数不会计算字符串末尾的空字符，但对字符数组名进行sizeof，会返回数组实际大小（也就是会算上末尾的空字符）。

### C++
```cpp
string s;
s.size()
```
<br/>

# 比较
单个字符（char类型）可直接使用>、<、==比较。

字符串（string、char []、char*类型）则需要调用相关函数。比较规则：自左向右逐个字符，按其对应的ASCII码的大小进行比较。若字符串长度不一致，则短字符串末尾之后被视为空字符进行比较（空字符的ASCII码为0）。

### C
无论是char []或char*都必须使用函数strcmp或strncmp，直接用>、<、==可能导致结果不准。
```cpp
int strcmp(const char *str1, const char *str2) 
//比较两个字符串
int strcmpi(const char *str1, const char *str2)
//同上，比较时忽略大小写
int strncmp(const char *str1, const char *str2, size_t n) 
//比较两个字符串的前n位
```
若相等，则返回零；若str1<str2，则返回负数；若str1>str2，则返回正数。

### C++
直接>、<、==即可，或者：
```cpp
string str1, str2;
str1.compare(str2);   
str1.compare(pos1,len1,str2,pos2,len2);
```
<br/><br/>

# 查找
### C
```cpp
char *strchr(const char *str, int c)
//找到字符c在str中首次出现的位置p，返回str中从第p位开始的所有字符。找不到返回NULL。
char *strrchr(const char *str, int c)
//找到字符c在str中最后出现的位置p，返回str中从第p位开始的所有字符。找不到返回NULL。

char *strstr(const char *s1, const char *s2)
//找到字符串s2在s1中首次出现的位置p，返回s1中从第p位开始的所有字符。找不到返回NULL。
```
### C++
```cpp
str1.find(str2);  //返回一个整数，表示str2在str1中第一次出现的位置，若没有返回-1
str1.rfind(str2); //同上，但从后往前找
```
<br/><br/>

# 清除或填充
### C
```cpp
memset(void *p, int c, size_t n)
```
从p指向的地址开始，对接下来的连续n个字节的内存空间，逐字节置为c。对于字符串，n为字符串长度乘以sizeof(char)；对于数组，n为元素数量乘以每个元素的字节数。

对于int型数组，数组的单个元素占4个字节，若c=1，逐个字节填充后数组中单个元素会被置为：

00000001 00000001 00000001 00000001

转成十进制是16843009。

因此，若要用memset初始化整型数组，仅0和-1可起到预期效果。


### C++
```cpp
string s;
s.clear();//全部清除
s.erase(pos,len);
```
<br/><br/>

# 修改
### C
```cpp
char *strcat(char * s1, const char *s2)
//在s1末尾添加s2，返回值为指向s1的指针
char *strncat(char *s1, const char *s2, size_t n)
//将s2的前n位添加到s1末尾，返回值为指向s1的指针

char *strcpy(char *s1, const char *s2)
//将s1替换为s2，返回指向s1的指针
char *strncpy(char *s1, const char *s2, size_t n)
//将s1的前n位替换为s2的前n位，返回指向s1的指针。若s2不足n位，用空字符填充
```
此外，也可以使用memcpy，直接从内存中复制对应值实现对字符串的修改。

### C++
```cpp
str1.append(str2);          
//与str1+=str2等效
str1.append(str2,pos,len);  
//在str1的末尾加上str2的第pos位开始的连续len个字符


str1.insert(pos1,str2);      //在str1的第pos1位上插入str2
str1.insert(pos1,str2,pos2,len2);  //在str1的第pos1位上插入str2从pos2位开始的连续len2个字符
str1.insert(pos1,num,char);   // num是插入次数，char是要插入的字符。


str2 = str1.substr(pos);     //将str1的第pos位及之后的所有字符赋值给str2
str2 = str1.substr(pos,len); //将str1从第pos位开始连续len个字符赋值给str2

str1 = str2;    //直接将str2覆盖掉str1
str1.replace(pos,len,str2);  //把str1从第pos位开始的连续len位替换成str2
str1.replace(pos1,len1,str2,pos2,len2); //多出的两个参数指定了str2参与替换的部分
```
<br/><br/>

# 格式转换

### C与C++转换
C字符串可直接赋值给C++字符串。

C++字符串s转C字符串c：
```cpp
strcpy(c, s.c_str());
```
### 字符串转数字
##### C
使用sscanf函数，方法与参数与scanf类似，区别在于scanf从设备（键盘）中取得内容放入变量，而sscanf从str中获得内容放入变量。
```cpp
int sscanf(const char *str, const char *format, ...)

//用法举例
int d;
char s[5] = "123";
sscanf(s, "%d", &d);
```
该方法支持整数、小数及多种进制类型，通过格式控制符format来操作。

##### C++：
可使用stoi、stol、stof、stod系列的函数。支持字符串及字符数组。
```cpp
#include <string>
using namespace std;

string s = stoi(123);
char s[] = stod(12.34);
```
### 数字转字符串
##### C
使用sprintf函数，方法与参数与printf类似，区别在于printf将变量内容输出到屏幕，而sprintf将变量内容输出到str。
```cpp
int sprintf(char *str, const char *format, ...)

//用法举例
int d = 123;
char s[5];
sprintf(s, "%d", d);
```
该方法支持整数、小数及多种进制类型，通过格式控制符format来操作。


##### C++：
使用标准的to_string函数，支持整数和实数。
```cpp
#include <string>
std::string s = std::to_string(1234);
```
<br/><br/>

# 参考资料
[菜鸟教程](https://www.runoob.com/)