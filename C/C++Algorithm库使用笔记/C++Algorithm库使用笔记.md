本博文介绍<algorithm>库的使用方法。

# 容器填充
使用fill函数：

```cpp
void fill(first, last, value);
//first，last为迭代器或指针，将容器内[first, last)范围内的元素都置为value
```

<br/><br/>

# 寻找最值
```cpp
//first，last为迭代器，第三个参数为自定义比较函数，某些情况下可省略
max_element(first, last, cmp)
//返回 [first, last) 中的最大元素的迭代器

// 参数与max_element一致
min_element(first, last, cmp)
//返回 [first, last) 中的最小元素的迭代器
```


# 二分查找
以下函数只可在已经升序排序好的数组中使用。
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
void sort(begin, end, Cmp) 
```
其中：
- begin：数组中第一个待排序元素的地址或迭代器
- end：数组中最后一个待排序元素的地址或迭代器
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

