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

 
# 动态数组（vector）
不存东西时不占空间，来多少数据就用多少空间，用于节省空间。
```cpp
#include  <vector>     //调用

vector  < int > a;     //声明

a.push_back (3)        //在数组a的最后添加一个元素3
 
a.pop_back()           //删除数组最后一个元素
 
a.size()               //返回元素个数
 
a.clear()              //清空

a[0]                    //该数组第一个元素

a.back()                //该数组最后一个元素
```
<br/><br/>
 

# 队列（queue）
```cpp
#include <queue>

queue <int> q;    

q.empty()  //队列为空返回true 否则返回false

q.size()   //返回队列元素个数

q.pop()    //删除队首元素

q.push(a)  //在队尾加入元素a

q.front()  //返回队首元素

q.back()   //返回队尾元素
```
<br/><br/>

# 栈（stack）
```cpp
#include <stack>

stack <int> s;

s.empty()  //栈为空返回true 否则返回false

s.size()   //返回栈中元素个数

s.pop()    //过程，删除栈顶元素

s.top()    //返回栈顶元素

s.push(a)  //在栈顶加入元素a
```
 <br/><br/>
 
# 集合set
该数据类型与vector相似，但也有以下特性：（1）集合中的元素互不相同；（2）每次向set中插入的元素都会被自动按顺序排列。
```cpp
#include <set> 

set <int> s;

s.size()    //返回集合中元素个数

s.begin()   //返回第一个元素的迭代器，若要取得集合中的第一个（也是值最小的）元素，则为*s.begin()

s.end()     //返回最后一个元素的迭代器，若要取集合中的最后一个（也是值最大的）元素，则为*--s.end()

s.empty()   //判断集合是否为空，为空返回True

s.count(k)  //判断集合中是否有元素k

s.insert(k) //向集合中插入元素k（若原集合中已有k，则自动忽略此次插入）

s.erase(k)  //在集合中删除元素k（若本来没有，也自动忽略）

s.clear()   //清空集合
```
<br/> <br/>

# 随机数
计算机的随机数是伪随机数，因此要设置随机数种子来保证得到的随机数序列不同。（若随机数种子相同，每次还是会得到相同随机数），因为时间每时每刻都在变，一般会利用时间来设置随机数种子：
```cpp
#include <cstdlib>
#include <ctime>   

srand(time(0));  //设置随机数种子

int a = rand();  //从[0 , 32767]中取随机整数

int b = rand() % n + k;   //在[ k, n-1+k ]中取随机整数
```
随机生成浮点数：
```cpp
double c = rand() / (RAND_MAX + 0.0);
double d = rand() / (double)RAND_MAX;  
```
两种方法一样，都是生成 [ 0 , 1 ] 之间的浮点数，若有范围需要，再乘以相应的系数扩大范围，加上相应常数移动范围即可。
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

# 排序  
#### qsort
函数声明：  
```cpp
#include <stdlib.h>
void qsort(arr, num, size, Cmp)
```
其中：  
- arr：指  向待  排  序  数组首  个元素
- num：数组中元素个数  
- size：每个元素的大小  
- Cmp：比较函数，按两元素的大小返回1、0或-1。
  
举个例子，给int型数组升序排序：
```cpp
int Cmp(const void * a, const void * b) {
    return *(int*)a - *(int*)b;
}

int a[N];
qsort(a, N, sizeof(int), Cmp);
```
按结构体中某成员的值升序排序：
```cpp
struct node {
    int n, i;
};

int Cmp(const void * a, const void * b) {
    return (*(struct node *)a).n - (*(struct node *)b).n;
}

struct node a[numsSize];
qsort(a, numsSize, sizeof(a[0]), Cmp);
```
降序排序把a、b交换一下位置即可。
