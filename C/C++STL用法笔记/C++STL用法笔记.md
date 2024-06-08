# STL简介
STL（Standard Template Library）即标准模版库，目前已成为国际标准，并被内置到C++编译器中，令用户能非常方便地调用各种复杂数据类型和算法。

## 容器
容器可以简单理解为兼容各种数据类型的复杂数据结构。在STL中，各种容器通过模板类实现。

STL中容器主要分成以下三类：序列容器（array、vector、list、deque）、有序关联容器（set、map）以及无序关联容器（unordered_map、unordered_set）

## 迭代器


<br/><br/>


# 向量容器（vector）
又称“动态数组”，和普通数组相比，vector无需事先声明数组大小，它会根据元素数量动态分配内存空间。vector在使用前需声明vector中元素的数据类型（支持各种复杂数据类型及用户自定义类型）
## 声明
vector容器在声明后是空的，没有任何元素，因此无需额外的清空操作。
```cpp
#include  <vector>     //调用

vector <元素类型> 变量名;     //直接声明，此时容器的size为0
```
## 初始化
初始化可指定容器的初始大小，也能指定部分元素的值。
```cpp
vector <元素类型> 变量名 (N);   
//声明一个vector容器并为该容器预留内存（N个int的空间），这样可以在N范围内直接使用 operator []访问元素

vector <元素类型> 变量名 (N, k);   
//声明一个vector容器并为该容器预留N个元素的空间，同时将每个元素初始化为k

vector <元素类型> 变量名 {值1, 值2, 值3}; 
//使用列表初始化的方式指定容器前三个元素的值，此时容器size为3
```

## 成员函数
```cpp
push_back(value) //在容器末尾添加一个元素value
pop_back()       //删除容器末尾的一个元素
size()           //返回容器中当前元素数量
capacity()       //返回容器当前最多可容纳的元素数量。若容器的size超出capacity时，容器会额外开辟内存，capacity也会增大
clear()          //清空容器
empty()          //当容器为空，返回true，否则为false
front()          //返回容器中的首个元素
at(i)            //返回容器中第i个元素。此外也可通过 operator []对元素进行访问或更改。两者均不能访问尚未存在元素的区域，但at函数会进行越界检查，可以使用try ... catch捕获异常，operator []则会直接报错
back()           //返回容器中最后一个元素
```
<br/><br/>
 

# 队列（queue）
## 队列
```cpp
#include <queue>

queue <元素类型> 变量名;    

//成员函数
back()   //返回队尾元素
empty()  //队列为空返回true 否则返回false
front()  //返回队首元素
pop()    //删除队首元素
push(a)  //在队尾加入元素a
size()   //返回队列元素个数
```
## 优先队列（priority_queue）的定义

底层通过堆维护，以下为优先队列的定义：
```cpp
#include <queue>  //与队列调用相同的头文件
//默认为最大堆
priority_queue <元素类型> 变量名;  
```

如果需要构建最小堆或自定义比较函数：

```cpp
priority_queue <元素类型, 容器类型, 比较函数> 变量名;   
```
其中容器类型表示实现堆的容器类型。一般堆都是通过数组来实现，因此容器类型一般填vector类型。priority_queue默认也是使用vector来实现堆。

如果要实现最小堆，举个例子
```cpp
priority_queue <int, vector<int>, greater<int>> a;   
```
这里的比较函数与sort中的比较函数不同，priority_queue中传入的是类名，如果需要自定义，一般需通过重载操作传入函数对象。


## 优先队列的成员函数
```cpp
push(x)   //在队列中元素a，并重新维护堆
top()     //返回队首（堆顶）元素
pop()     //删除队首（堆顶）元素，并重新维护堆
empty()   //队列为空返回true 否则返回false 
size()    //返回队列素个数
```
<br/><br/>

# 栈（stack）
栈的定义与成员函数：
```cpp
#include <stack>

stack <元素类型> 变量名;

//成员函数
empty()  //栈为空返回true 否则返回false
size()   //返回栈中元素个数
pop()    //过程，删除栈顶元素
top()    //返回栈顶元素
push(a)  //在栈顶加入元素a
```
 <br/><br/>


# pair
由两个相同或不同类型的变量构成的值对。
```cpp
#include <utility>

pair<类型1, 类型2> p;  //新建一个pair变量，变量名为p
pair<类型1, 类型2> p {值1, 值2}; //新建pair变量的同时初始化值


//成员函数
first  // 取pair中的前一个值
second // 取pair中的后一个值
```

# 集合(set)
## 基本用法
以红黑树为底层数据结构的集合类型。红黑树基于集合中元素本身的值而生成。因此集合中的元素互不相同，并在集合中按顺序排列。
```cpp
#include <set> 

set <元素类型> 变量名;

//成员函数
begin()   //返回第一个元素的迭代器，若要取集合的最小元素，则为*s.begin()
clear()    //清空集合
count(k)  //判断集合中是否有元素k
emplace(k) //向集合中插入元素k，一般效率高于insert方法
empty()   //判断集合是否为空，为空返回True
end()     //返回最后一个元素再后一个位置的迭代器（该迭代器不指向任何元素）
erase(k)   //在集合中删除元素k（若本来没有，则忽略该命令）
find(k)    //查找是否存在元素k，若存在返回k对应迭代器，否则返回end迭代器
insert(k)  //向集合中插入元素k（若原集合中已有k，则自动忽略此次插入）
rbegin()  //r开头的为逆序迭代器，rbegin实际上是返回末尾元素的迭代器，因此可用*s.rbegin()取set中的最大值
rend()    //返回第一个元素再前一个位置的迭代器（该迭代器不指向任何元素）
size()    //返回集合中元素个数
```

## 自定义排序规则
### 函数指针

### 伪函数
使用伪函数定义排序规则，如下所示，定义了一个元素类型为pair的集合s，按pair第二项从大到小排序，若相等则pair第一项字典序小的排前面。
```cpp
class MyCompare {
public:
    bool operator()(const pair<string, int> & a, const pair<string, int> & b) const{
        return a.second > b.second || (a.second == b.second && a.first < b.first);
    }
};

set<pair<string, int>, MyCompare> s;
```
<br/> <br/>

# map
map是一个以键值对（Key, Value）为元素的红黑树数据结构。红黑树基于元素的键值（Key）生成，因此元素按Key值排序，不同元素的Key值均不相同。元素的增删改操作均可在$O(\log N)$时间内完成。
## 声明
```cpp
#include <amp>

map <Key类型, Value类型> 变量名;
```

## 成员函数
```cpp
begin()       //返回map中第一个元素（即Key值最小的元素）的迭代器
begin().first //获取Key值最小的元素的Key值
begin().second//获取Key值最小的元素的Value
contains(k)   //查找是否存在键值为k的键值对，存在则返回True，否则False
empty()       //当map为空返回true，否则为false
erase(k)      //删除Key值为k的元素
size()        //返回元素个数
[k]           //可使用下标直接查找键值为k的元素并对其value进行修改
```
<br/><br/>


# unordered_map
C++11版本加入的数据类型。unordered_map简单来说是一个以键值对（Key, Value）为元素的开链哈希表，其中Key可以是复杂数据类型，如string、double或者用户自定义的类型。

## 声明及初始化
```cpp
#include <unordered_map>

unordered_map <Key类型, Value类型> 变量名; 
```

## 成员函数
```cpp
clear()         //清空所有键值对   
contains(k)     //查找是否存在键值为k的键值对，存在则返回True，否则False
emplace(k, v)   //插入键值对，如果键值k已经存在了，则该语句无效
find(k)         //查找是否存在键值为k的键值对，存在则k对应迭代器，否则end迭代器
size()          //返回键值对的数量
[k]             //可使用下标直接查找键值为k的元素并对其value进行修改
```

## 插入新元素
```cpp
for (auto i = a.begin(); i != a.end(); ++i)  
// 迭代，其中i为对应的键值对，i->first为Key，i->second为Value
```