
## 函数指针与指针函数的区别
指针函数本质上是函数，只是其返回值为指针。
函数指针本质上是指针，只是其指向的是函数。
```cpp
int* Func()   //这是指针函数
int (*fp)() //这是函数指针
```
<br/><br/>
## 函数名与函数地址
和数组名类似，函数名指向存储该函数的内存地址。
函数名后面带括号，其返回值就是函数定义时的返回值，不带括号才能得到函数地址。
因此，以下表示函数的地址：
```cpp
&Func, Func, *Func
```
<br/><br/>

## 函数指针使用
既要定义其指向函数的返回值类型，也要定义其指向函数的形参类型及数量：
```cpp
float (*fp)(char*);
int* (*fp2)(int, int);
```
如上，函数指针fp只能指向返回值为float，有一个char*参数的函数。fp2只能指向返回值为int型指针，有两个int参数的函数。

通过以下方式将函数指针指向函数，并进行调用：
```cpp
//定义
int Prt(int t) {
    printf("%d\n", t);
}

int (*fp)(int);

//赋值
fp = Prt;

//以下两种调用方式均可
fp(3);
(*fp)(3);
```
<br/><br/>

## 实际应用
#### 回调函数
首先看一个可支持各种类型数组的冒泡排序函数：
```cpp
int Compare(void* a, void* b) {
    return *(int*)a < *(int*)b;    
}

void Sort(void* a, int n, int size, int (*cmp)(void*, void*)) {
    for (int i=0; i<n; i++)
        for (int j=i+1; j<n; j++)
            if (cmp(a+i*size, a+j*size)) 
                Swap(a+i*size, a+j*size, size);
}
```

用户在调用该函数的时候，仅需根据自己的需要编写对应数据类型的Compare函数，然后将该函数指针作为参数传入Sort。这一做法在维持了Sort函数泛用性的同时，还降低了耦合性（Sort功能和Compare功能分离），也实现了对Sort函数的封装。

像Compare一样，作为一个参数被其他函数调用的函数，即为回调函数（Callback Function）

####  函数指针数组
本质上是数组，其中每个元素为函数指针。定义方式：
```cpp
double* (*fp[])(int, int);
```
如上，定义了一个数组fp，该数组的每个元素均为一个函数指针，这些函数指针返回值均为double型指针，且参数为两个int。

使用上，假设有一个对a和b进行加减乘除的函数：
```cpp
switch (mode) {
    case 0: 
        result = Add(a, b);//加
        break;
    case 1: 
        result = Sub(a, b);//减
        break;
    case 2: 
        result = Mul(a, b);//乘
        break;
    case 3: 
        result = Div(a, b);//除
        break;
    ...
}
```
根据不同的计算模式调用不同的计算函数，如果使用函数指针数组：
```cpp
int (*calc[])(int, int) = {Add, Sub, Mul, Div};

result = calc[mode](a, b);
```
数组下标即可替代switch语句直接确定需要调用的函数。
<br/><br/>

## 参考资料
[关于函数指针数组的定义](https://www.cnblogs.com/alexshi/archive/2012/03/12/2392508.html)

[C语言高级用法--函数指针](https://zhuanlan.zhihu.com/p/534611285)