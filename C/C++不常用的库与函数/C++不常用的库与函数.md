# 强制保留n位小数
位数不足则强制补零
```cpp
#include <iomanip>
...
cout<<setprecision(n);
```
不使用iomanip头文件的话可以：
```cpp
cout.setf(ios::fixed);
cout.precision(n);
```
或者使用printf：（以保留3位为例）
```cpp
double d=2.0;
printf("%.3lf",d);
```
<br/><br/>


# 计时
需要调用头文件time.h

## 获取系统时间

```cpp
time_t time(time_t *timer)
```
返回值为time_t类型（实际为long long）。若参数为空，则返回自UTC时间1970年1月1日零点到当前经过的秒数。若不为空，则将系统时间设置为timer。

## 计算运行时间

使用clock函数。返回值为自程序进程开始后到调用clock()时经过的时间，单位为毫秒。在要计时的代码前后调用clock()，获得两个时间戳，相减后得到代码执行所用时间。

```cpp
clock_t start, end; //clock_t实际上为整型
start = clock();
// 需要计时的代码
end = clock();
printf("%d ms\n", end-start);
```
<br/><br/>


# 随机数
计算机的随机数是伪随机数，如果设置的随机数种子相同，得到的随机数序列也相同。由于时间是时刻变化的，为保证每次得到的随机数序列不同，一般通过获取时间值来设置随机数种子。

设置随机数种子：
```cpp
srand(time(NULL)); 
```
如果要在循环中获得不同的随机数，由于循环运行速度极快，可能导致每次循环时得到的time(NULL)值是一样的，此时可以使用精度更高（毫秒级）的clock()函数来设置随机数种子：
```cpp
srand(clock()); 
```

生成随机数：
```cpp
int a = rand();  //从[0, RAND_MAX]随机取整数，RAND_MAX一般为2^31-1
int b = rand() % n + k;   //从[k, n-1+k]中取随机整数
double c = rand() / (double)RAND_MAX;  
```
生成 [0, 1] 之间的浮点数，若有范围需要，再乘以相应的系数扩大范围，加上相应常数移动范围即可。
<br/><br/>
 

# 复数
```cpp
#include <complex>

complex <double> a;   
complex <double> b(2.0,3.0);//可以在声明的同时赋值，前一个数表示实部，后一个为虚部

complex <double> a;
double b1=a.real();     //取得a的实部
double b2=a.imag();     //取得a的虚部
 
complex <double> c=conj(a);   //将a的共轭复数赋值给c
 
double angle=arg(a);     //取得a的幅角，以弧度制表示
```
复数之间可以直接加减乘除，复数也可以和实数进行乘除运算， 但不能加减。
<br/><br/>
