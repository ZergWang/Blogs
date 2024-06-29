# STL简介
STL（Standard Template Library）即标准模版库，目前已成为国际标准，并被内置到C++编译器中，令用户能非常方便地调用各种复杂数据类型和算法。

## 容器
容器可以简单理解为兼容各种数据类型的复杂数据结构。在STL中，各种容器通过模板类实现。

STL中容器主要分成以下三类：顺序容器（array、vector、list、deque）、关联容器（set、map）以及无序关联容器（unordered_map、unordered_set）。除了以上三种容器类型之外，还有一些诸如pair、bitset等容器类型，将单独介绍。

## 迭代器
简单来说，迭代器可以视为一种访问容器中元素的指针。不同的容器有其对应类型的迭代器。迭代器可使用解引用符号“*”来访问指定元素，也可使用自增符号“++”来实现遍历容器中的元素。例如下面的代码可用于遍历unordered_map，其中变量i即为unordered_map类型的迭代器。
```cpp
for (auto i = a.begin(); i != a.end(); ++i)  
//i可以视为指向unordered_map中元素的指针
// i->first为元素的Key，i->second为元素的Value
```
<br/><br/>


# 顺序容器
## vector
又称“动态数组”，和普通数组相比，vector无需事先声明数组大小，它会根据元素数量动态分配内存空间。vector在使用前需声明vector中元素的数据类型（支持各种复杂数据类型及用户自定义类型）
### 声明
vector容器在声明后是空的，没有任何元素，因此无需额外的清空操作。
```cpp
#include  <vector>     //调用

vector <元素类型> 变量名;     //直接声明，此时容器的size为0
```
### 初始化
初始化可指定容器的初始大小，也能指定部分元素的值。
```cpp
vector <元素类型> 变量名 (N);   
//声明一个vector容器并为该容器预留内存（N个int的空间），并初始化为0。这样可以在N范围内直接使用 operator []访问元素

vector <元素类型> 变量名 (N, k);   
//声明一个vector容器并为该容器预留N个元素的空间，同时将每个元素初始化为k

vector <元素类型> 变量名 {值1, 值2, 值3}; 
//使用列表初始化的方式指定容器前三个元素的值，此时容器size为3
```

### 成员函数
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
 

## 队列（queue）
声明与成员函数：
```cpp
#include <queue>

queue <元素类型> 变量名;    

//成员函数
back()   //返回队尾元素
clear()  //清空队列
empty()  //队列为空返回true 否则返回false
front()  //返回队首元素
pop()    //删除队首元素
push(a)  //在队尾加入元素a
size()   //返回队列元素个数
```

## 优先队列（priority_queue）
底层通过堆维护
### 声明
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


### 成员函数
```cpp
empty()   //队列为空返回true 否则返回false 
pop()     //删除队首（堆顶）元素，并重新维护堆
push(x)   //在队列中元素a，并重新维护堆
size()    //返回队列元素个数
top()     //返回队首（堆顶）元素
```


## 双端队列（deque）
一种特殊的队列，此种队列两端均可插入元素，队首、队尾元素均可删除。
### 声明
```cpp
#include <deque>

deque <元素类型> 变量名;
```
### 成员函数
```cpp
back()        //返回队尾元素
clear()       //清空队列
empty()       //队列为空返回true 否则返回false 
front()       //返回队首元素
pop_back()    //删除队尾元素
pop_front()   //删除队首元素。
push_back()   //在队尾插入元素
push_front()  //在队首插入元素
size()        //返回队列元素个数
```
<br/><br/>

## 栈（stack）
栈的定义与成员函数：
```cpp
#include <stack>

stack <元素类型> 变量名;

//成员函数
empty()  //栈为空返回true 否则返回false
size()   //返回栈中元素个数
pop()    //删除栈顶元素，该函数无返回值
top()    //返回栈顶元素
push(a)  //在栈顶加入元素a
```
<br/><br/>

# 关联容器（Associative containers）
此类容器基本以红黑树为底层数据结构。因此此类容器中基于元素Key值对元素进行增删改操作均可在$O(\log N)$时间内完成。此类容器中的元素互不相同，并按顺序排列。
## set
### 基本用法
以红黑树为底层数据结构的集合类型。红黑树基于集合中元素本身的值而生成。set中的元素不可直接更改（可通过删除再插入的方式进行）。
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
erase(iterator) //删除某迭代器对应的元素
erase(k)   //在集合中删除值为k的元素（若本来没有，则忽略该命令）
erase(iterator lef, iterator rig) //删除所有大于等于lef对应元素且小于rig对应元素的元素
find(k)    //查找是否存在元素k，若存在返回k对应迭代器，否则返回end迭代器
insert(k)  //向集合中插入元素k（若原集合中已有k，则自动忽略此次插入）
rbegin()  //r开头的为逆序迭代器，rbegin实际上是返回末尾元素的迭代器，因此可用*s.rbegin()取set中的最大值
rend()    //返回第一个元素再前一个位置的迭代器（该迭代器不指向任何元素）
size()    //返回集合中元素个数
```

### 自定义排序规则
#### 函数指针

#### 伪函数
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

## multiset
和set类型基本一致，区别在于multiset允许其中有重复元素，而set不允许。两者的声明、初始化及成员函数基本相同。与set不同之处会在下面列出：

### multiset与set的部分成员函数不同
```cpp
find(k)  //寻找值为k的元素，返回第一个找到的元素的迭代器
erase(k) //在multiset中会删除所有值为k的元素
erase(iterator) // //在multiset中删除只删除迭代器对应的那一个元素
```


## map
map是一个以键值对（Key, Value）为元素的容器结构。底层红黑树基于元素的键值（Key）生成，因此元素按Key值排序，不同元素的Key值均不相同。map不允许其中的多个元素具有相同的key。
### 声明
```cpp
#include <amp>

map <Key类型, Value类型> 变量名;
```

### 成员函数
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

## multimap
和map类型基本一致，区别在于multimap允许其中有多个元素有相同的key，而map不允许。两者的声明、初始化及成员函数基本相同。


<br/><br/>

# 无序关联容器（Unordered associative containers）
C++11加入的容器类型，底层为开链哈希表。在此类容器中，基于元素Key值去进行增删改操作平均能以$O(1)$时间完成。
## unordered_map
unordered_map简单来说是一个以键值对（Key, Value）为元素的开链哈希表，其中Key和Value可以是复杂数据类型，如string、double或者用户自定义的类型。

### 声明及初始化
```cpp
#include <unordered_map>

unordered_map <Key类型, Value类型> 变量名; 
```

### 成员函数
```cpp
clear()         //清空所有键值对   
contains(k)     //查找是否存在键值为k的键值对，存在则返回True，否则False
emplace(k, v)   //插入键值对，如果键值k已经存在了，则该语句无效
find(k)         //查找是否存在键值为k的键值对，存在则k对应迭代器，否则end迭代器
size()          //返回键值对的数量
[k]             //可使用下标直接查找键值为k的元素并对其value进行修改。
                //如果不存在Key为k的元素，“[k]” 的访问会默认执行emplace(k, 0)
```



## unordered_set
底层为开链哈希表的集合类型。哈希表以元素本身的值为Key存储元素。




# 其他容器
## pair
由两个相同或不同类型的变量构成的值对。
```cpp
#include <utility>

pair<类型1, 类型2> p;  //新建一个pair变量，变量名为p
pair<类型1, 类型2> p (值1, 值2); //新建pair变量的同时初始化值

p = make_pair(值1, 值2); //可通过make_pair函数对变量进行赋值

//成员函数
first  // 取pair中的前一个值，也可对其进行赋值操作
second // 取pair中的后一个值
```

相同类型的pair变量之间可直接赋值，也可直接使用“>”、“<”等符号进行比较，首先比较first的值，若相同再比较second的值。


## bitset
### 声明
正常布尔数组中每个元素均为布尔类型，占用1字节。但如果使用bitset（位图）来作为布尔数组，每个bit都可起到单个布尔值的作用，1字节大小的bitset相当于原来8个布尔类型，大大减少空间占用。此外，基于bitset的位运算也能提高运算效率。

C++自带bitset类型，定义如下：
```cpp
#include <bitset>

std::bitset<10> bs;  //声明一个含10个bit的bitset变量。默认全部bit位为0

std::bitset<10> bs (25);//使用无符号整数来初始化bitset，该整数的二进制即为bitset的值

std::bitset<10> bs ("11001");  //也可使用仅“0”和“1”构成的字符串来初始化bitset

```
bitset中bit的坐标是从右到左设置的，例如上面使用“11001”初始化的bitset，其值实际为“0000011001”，该变量的第0位（即bs[0]）是最右边的bit，即“1”。


### 成员函数
```cpp
all()       //若所有bit都为1则返回True
any()       //若至少有一个bit为1则返回True
count()     //返回bitset中值为1的bit的个数
flip()      //翻转每个bit
flip(pos)   //翻转第pos个bit
none()      //若所有bit都为0则返回True
reset()     //将所有bit位设为0
set()       //将所有bit位设为1
set(pos, value)  //将第pos位设置为value
size()      //返回bitset大小，即所有bit的个数
to_ullong() //返回unsigned long long形式的bitset变量
to_ulong()  //返回unsigned long形式的bitset变量
to_string() //返回字符串形式的bitset变量
operator [] //可对变量直接进行下标访问并修改
```

此外，可直接使用“==”或“!=”对不同bitset变量进行比较，也可直接对bitset变量使用位运算操作。


# 参考资料

[OI Wiki：迭代器](https://oi-wiki.org/lang/csl/iterator/)

[C++ reference](https://en.cppreference.com/w/)