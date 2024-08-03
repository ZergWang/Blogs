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

# 寻找最值
需要头文件：```<algorithm>```
```cpp
//first，last为迭代器，第三个参数为自定义比较函数，某些情况下可省略
max_element(first, last, cmp)
//返回 [first, last) 中的最大元素的迭代器

// 参数与max_element一致
min_element(first, last, cmp)
//返回 [first, last) 中的最小元素的迭代器
```


# 二分查找
以下函数调用需要头文件```<algorithm>```。以下函数只可在已经升序排序好的数组中使用。
## binary_search
在指定数组范围内查找指定值，如果存在该值返回True。
```cpp
//在int a[]的[lef, rig)范围内查找值value
binary_search(a+lef, a+rig, value)

//在vector<int> b的全部范围内查找值value
binary_search(b.begin(), b.end(), value)
```
## lower_bound
参数与binary_search一致，查找指定范围内首个大于等于目标值的元素，返回该元素的指针或迭代器。
```cpp
//在int a[]的[lef, rig)范围内查找值value，返回元素的指针
lower_bound(a+lef, a+rig, value)
//如果找不到，返回的指针会指向数组外，有越界风险

//在vector<int> b的全部范围内查找值value，返回元素的迭代器
lower_bound(b.begin(), b.end(), value)
//如果找不到，返回b.end()
```

## upper_bound
参数与lower_bound一致，查找指定范围内首个大于目标值的元素，返回该元素的指针或迭代器。

<br/><br/>

# 排序  
## qsort
```cpp
#include <stdlib.h>
void qsort(a, numSize, size, Cmp)
```
其中：  
- a：指向待排序数组首个元素的地址
- numSize：数组中元素个数  
- size：每个元素的大小  
- Cmp：比较函数，按两元素的大小返回1、0或-1。该函数需要用户自定义，其参数为两个空指针，指向待比较元素的地址。

<font color="ff0000">注意：本文为了方便，在Cmp函数的返回值处使用“return 元素a-元素b”这种简单形式，但这可能导致运行结果溢出。为保险起见建议使用if语句比较元素大小并返回相应值。</font>
<br/>

## 对特殊数组使用qsort排序
### 一维整型数组
```cpp
int Cmp(const void * a, const void * b) {
    return *(int*)a - *(int*)b;
}

int a[numsSize];
qsort(a, numsSize, sizeof(a[0]), Cmp);
```
### 浮点型数组
浮点型强制转换成整型时会直接截掉小数部分，导致无法正确判断两数之差的正负性。因此需单独用if语句判断正负。
```cpp
int Cmp(const void * a, const void * b) {
    double res = *(double*)b - *(double*)a;
    if (res>0)
        return 1;
    if (res<0)
        return -1;
    return 0;
}

double a[numsSize];
qsort(a, numsSize, sizeof(a[0]), Cmp);
```
### 直接定义的二维数组
```cpp
int Cmp(const void * a, const void * b) {
    int *x = (int*)a;   
    int *y = (int*)b ;
    return x[0] - y[0]; //如果要按第k列的大小排序，就取x[k]和y[k]
}

int a[100][2];  //100行2列的数组，按第1列的大小排序（首列为第0列）
qsort(a, numsSize, sizeof(a[0]), Cmp);
```

### malloc的二维数组或指针数组

在Cmp的参数中，void型指针存储的是待排序元素的地址，而malloc的二维数组或指针数组，其元素本身为指针类型，因此void指针内存储的是指针的地址，因此要在Cmp中将参数转为二级指针，取地址后得到一级指针，再取索引使用。
```cpp
int Cmp(const void * a, const void * b) {
    int *x = *(int**)a;   
    int *y = *(int**)b;
    return x[1]-y[1];  //如果要按第k列的大小排序，就取x[k]和y[k]
}

int **a = (int**)malloc(numsSize*sizeof(int*));
qsort(a, numsSize, sizeof(a[0]), Cmp);
```

### 按结构体中某成员的值排序
```cpp
struct node {
    int n, i;
};

int Cmp(const void * a, const void * b) {
    return ((struct node *)a)->n - ((struct node *)b)->n;
    //也可以写成：return (*(struct node *)a).n - (*(struct node *)b).n;
}

struct node a[numsSize];
qsort(a, numsSize, sizeof(a[0]), Cmp);
```
注意：在Cmp函数中，对a和b强制类型转换后要加上括号，否则会报错“request for member XXX in something not a structure or union”。

<br/><br/>

## sort
### 简介
```cpp
#include <algorithm>
void sort(begin, end, Cmp) 
```
其中：
- begin：数组中第一个待排序元素的地址
- end：数组中最后一个待排序元素的地址
- Cmp：比较函数，可省略（sort默认以升序排序）
### 比较函数写法
Cmp函数接收两个参数，参数类型为待排序数组的元素类型，而不是该类型对应的指针。例如，对int型数组排序，Cmp接收int型参数，而不是int*。

Cmp函数返回值为bool型，用于指定第一个参数是否大于（小于）第二个参数，是则返回1，否则返回0。

若不想写比较函数，但又想按降序排序，可使用std:greater比较函数。同理升序排序时可使用std:less。
```cpp
//对int型数组a：
sort (a, a+n, greater<int>());
```
<br/>

## 自定义sort的比较函数
比较函数传入的参数类型为待排序数组中单个元素的类型，例如对int型数组排序，则将传入int比较函数即可。对结构体数组排序传入单个结构体类型即可。
```cpp
// 对vector <int>类型或者int型数组降序排序
bool Cmp(int a, int b) {
    return a > b;
}

//对vector <vector<int>>类型的容器降序排序
bool Cmp(vector<int> &a, vector<int> &b) {
    return a[0] > b[0];   //按二维数组中第0列的值降序排序
}

//对vector <pair<int, int>>类型的容器排序
bool Cmp(pair<int, int> &a, pair<int, int> &b) {
    return a.first > b.first;  
}
```

