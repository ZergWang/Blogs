# STL简介
STL（Standard Template Library）即标准模版库，目前已成为国际标准，并被内置到C++编译器中，令用户能非常方便地调用各种复杂数据类型和算法。

## 容器
容器可以简单理解为兼容各种数据类型的复杂数据结构。在STL中，各种容器通过模板类实现。

STL中容器主要分成以下三类：序列容器（array、vector、list、deque）、有序关联容器（set、map）以及无序关联容器（unordered_map、unordered_set）

## 迭代器


<br/><br/>


# 向量容器（vector）
又称“动态数组”，和普通数组相比，vector无需事先声明数组大小，它会根据元素数量动态分配内存空间。vector在使用前需声明vector中元素的数据类型（支持各种复杂数据类型及用户自定义类型）
## 声明与初始化
vector容器在声明后是空的，没有任何元素，因此无需额外的清空操作。
```cpp
#include  <vector>     //调用

vector <类型> 变量名;     //声明
vector <vector <int>> a;  //声明一个二维int型vector容器
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
除了以上成员函数，vector和普通数组一样，可通过下标“[]”对元素进行访问或更改。但不可使用下标访问尚未存在元素的区域，否则会报错。
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


# pair
将两个值视为一个单元，是map、unordered_map等复杂类型的基础构成。

```cpp
pair<类型1, 类型2> p;  //新建一个pair变量，变量名为p
pair<类型1, 类型2> p(值1, 值2); //新建pair变量的同时初始化值

p.first  // 取pair中的前一个值
p.second // 取pair中的后一个值

```

# 集合set
## 基本用法
集合类型，底层通过红黑树实现。该数据结构有以下特性：1. 集合中的元素互不相同；2. 每次向set中插入的元素都会被自动排序。
```cpp
#include <set> 

set <int> s;

s.size()    //返回集合中元素个数
s.begin()   //返回第一个元素的迭代器，若要取集合中的第一个（也是值最小的）元素，则为*s.begin()
s.end()     //返回最后一个元素的迭代器，若要取集合中的最后一个（也是值最大的）元素，则为*--s.end()
s.empty()   //判断集合是否为空，为空返回True
s.count(k)  //判断集合中是否有元素k
s.emplace(k) //向集合中插入元素k，一般效率高于insert方法
s.insert(k)  //向集合中插入元素k（若原集合中已有k，则自动忽略此次插入）
s.erase(k)   //在集合中删除元素k（若本来没有，也自动忽略）
s.clear()    //清空集合

if (s.find(k) != s.end()) cout << "找到了";  //查找元素k
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


# unordered_map
C++11版本加入的数据类型，底层为开链的哈希表。unordered_map简单来说是一个以键值对（Key, Value）为元素的开链哈希表，其中Key可以是复杂数据类型，如string、double或者用户自定义的类型。

## 声明及初始化
```cpp
unordered_map <Key类型, Value类型> a; //创建一个名为a的unordered_map变量

a.clear();   //清空所有键值对   
```

## 插入新元素
```cpp
a.emplace(Key, Value); //插入键值对，如果Key已经存在了，则该语句无效
a[Key] = Value;        //直接赋值可以起到插入、修改的作用

for (auto i = a.begin(); i != a.end(); ++i)  
// 迭代，其中i为对应的键值对，i->first为Key，i->second为Value
```