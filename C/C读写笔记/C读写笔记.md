# 基本概念
## 流
用户使用C语言指令控制流（stream），从而控制各类硬件进行读写。流就是沟通C与各类硬件读写的桥梁。

在C语言中，用户通过文件指针（FILE *）来控制流（为了方便理解，实际上我们可以把这些文件指针当作流）。文件指针指向特定的文件或硬件设备。将文件指针作为输入输出函数的参数，即可将精准地控制指定文件或硬件的输入输出。文件指针在使用前需要定义和手动指向指定文件或设备，使用后要手动关闭。

C语言中有三个特殊的流（文件指针）。这三个指针在程序运行时会自动打开，结束时会自动关闭。
- 标准输入流（stdin）：控制键盘。通过stdin，即可从键盘输入信息到程序中。
- 标准输出流（stdout）：控制屏幕。通过stdout，即可将程序中的信息输出到屏幕。
- 标准错误流（stderr）：控制屏幕。通过stdout将错误信息输出到屏幕。

## 缓冲区
缓冲区是C标准库在用户空间中划分的一块区域。在程序运行时，为了减少系统调用（系统调用需要从用户态切换为内核态，会带来一定的开销），提高运行效率，程序会将多次的输入输出的信息先放到缓冲区中，等到特殊时机再执行实际的系统调用，然后刷新缓冲区。

缓冲一般分为以下三种机制：
- 全缓冲：缓冲区被填满后才进行系统调用。
- 行缓冲：输入输出过程中遇到换行符时，进程才进行系统调用。Linux系统中的stdin和stdout一般为行缓冲。
- 无缓冲：不设置缓冲区，每条输入输出的指令都将直接进行系统调用。

## 系统调用函数
C语言对文件操作的函数中，我们可以发现功能和名字类似的几对函数。其中，open()、close()、read()、write()是系统调用函数，而fopen()、fclose()、fread()、fwrite()是库函数。

系统调用函数通过文件描述符来操作文件，系统调用函数更加底层，适用范围更广，但系统调用函数会触发内核态的切换。

库函数通过文件指针来操作文件，库函数适用范围窄一些，但可以利用缓冲区，在读写小块文件内容时效率更高。本文聚焦于库函数的使用，对系统调用函数不作展开。

## 特殊符号
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

# 输入函数
## fscanf
从指定流中读入信息，函数原型：
```cpp
int fscanf(FILE *stream, const char *format, ...)
```
用法和scanf类似，多了一个FILE指针类型的参数，用于指定流。

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

使用fscanf读入信息时，空白字符会被忽略（scanf和C++中的cin也会），或者作为连续fscanf时多个类型的分隔符。因此，在使用fscanf读入含空格的字符串时可能会发生信息不全的情况。
## scanf
等价于fscanf(stdin, format, ...)

scanf一样会将空白字符忽略，或者将其作为连续scanf时的分隔符。另外，由于scanf是行缓冲的，最后的换行键不会被scanf捕获，该换行键会留在缓冲区内。

若字符串（字符数组）中无空格，可直接cin、cout。
对于C风格字符数组：
```cpp
char c[100];
scanf("%[^\n]",&c); //遇到回车才算读入结束
```

## fgetc，getc与getchar
```cpp
int fgetc(FILE *stream)
int getc(FILE *stream)
int getchar()
```
fgetc从指定流中读取一个无符号字符，返回值即为该字符，如果到达文件末尾或发生错误，则返回EOF。

getc本质上是宏，而不是函数，其他与fgetc一致。

getchar函数等价于fgetc(stdin)，除了无参数外，其他与fgetc一致。

## fgets与gets
```cpp
char *fgets(char *str, int n, FILE *stream)
```
从指定流中读取最多n-1个字符（包括空字符）。读取过程中若遇到换行、文件末尾或者已经读入n-1个字符则结束。将读取到的字符存入str中。

函数返回值与str相同。如果到达文件末尾、没有读取到任何字符或者发生错误，str的内容不变，返回空指针。

gets函数和fgets类似，但由于gets函数没有参数约束最多读入的字符数，当流中给出的字符超过了字符数组的上限，gets函数仍然会执行，将字符写入数组之后的地址，导致系统被破坏，因此不建议使用gets函数。

## fread

<br/><br/>


# 输出函数
## fprintf
将信息输出到指定流中，函数原型：
```cpp
int fprintf(FILE *stream, const char *format, ...)
```
用法和printf类似，多了一个FILE指针类型的参数，用于指定流。

函数执行成功则返回写入的字符数，否则返回EOF。
## printf
等价于fprintf(stdout, format, ...)
## fputc，putc与putchar
```cpp
int fputc(int c, FILE *stream)
int putc(int c, FILE *stream)
int putchar(int c)
```
fputc将c转换为一个无符号char型字符，然后将其输出到指定流中。返回值等于c，若执行出错返回EOF。

putc本质上是宏，而不是函数，其他与fputc一致。

putchar等价于fputc(int c, stdout)，其他与fputc一致。

## fputs与puts
```cpp
int fputs(const char *s, FILE *stream);
int puts(const char *s)
```
fputs将字符数组s输出到指定流中，直到遇到\0才停止。为安全起见，在使用fputs之前需保证s以\0结尾。fputs成功执行的返回值在不同平台编译器上不同（有些返回输出的字符数，有些返回0），但若遇到错误返回EOF。

puts将字符串输出到stdout中，直到遇到\0才停止。输出完s后会多输出一个回车。其他方面和fputs一样。

## fwrite

<br/><br/>

# 其他函数
## fopen
打开一个流，并指定其要读写的文件或硬件设备。函数原型：
```cpp
FILE *fopen(const char *filename, const char *mode)
```
filename为文件路径，mode为读写模式，其中：
| 模式 | 描述 |
| ---  | --- |
| r    | 从文件或设备的开头读取，若不存在则执行失败，返回NULL|
| w    | 从文件或设备开头写入，若不存在则创建一个新的|
| a    | 从文件或设备的末尾追加写入，若不存在则创建一个新的|
| b    | 将信息以二进制形式处理|
| +    | 同时进行读写操作    |

以上这5种模式可叠加使用，如“w+”意思为读写一个文件或设备，若不存在则创建一个。

函数返回值为一个FILE指针。若函数执行错误，返回NULL，并设置全局变量errno来标识错误。

## fclose
关闭流，函数原型：
```cpp
int fclose(FILE *stream)
```
参数为FILE指针。成功关闭返回零，否则返回EOF。

## feof

## fseek

## ferror

## fflush

 
<br/><br/>

# 参考资料
[菜鸟教程](https://www.runoob.com/)

[对C标准中空白字符的理解](https://blog.csdn.net/boyinnju/article/details/6877087)