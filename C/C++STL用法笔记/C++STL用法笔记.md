# STL简介
STL（Standard Template Library）即标准模版库，目前已成为国际标准，并被内置到C++编译器中，令用户能非常方便地调用各种复杂数据类型和算法。

## 容器
容器可以简单理解为兼容各种数据类型的复杂数据结构。在STL中，各种容器通过模板类实现。

STL中容器主要分成以下三类：序列式容器（array、vector、list、deque）、关联容器（set、map）以及无序关联容器（unordered_map、unordered_set）。除了以上三种容器类型之外，还有一些诸如pair、bitset等容器类型，将单独介绍。

## 迭代器
### 迭代器简介
简单来说，迭代器可以视为一种访问容器中元素的指针。不同的容器有其对应类型的迭代器。迭代器可使用解引用符号“*”来访问指定元素，也可使用自增自减符号来移动迭代器，从而遍历容器中的元素。例如下面的代码可用于遍历unordered_map，其中变量i即为unordered_map类型的迭代器。
```cpp
for (auto i = a.begin(); i != a.end(); ++i)  
//i可以简单看作是指向unordered_map中元素的指针
// i->first为元素的Key，i->second为元素的Value
```
迭代器之间可直接相减从而计算出它们之间的距离，也可以使用“==”或“!=”来判断是否相等，但不可使用大于小于号比较大小。

### 迭代器分类
一般容器中常见以下几对迭代器：
```cpp
begin, end 
//分别指向容器头，尾的迭代器，begin一般指向容器首个元素，end指向容器最后元素的后一个位置
cbegin, cend
//const版本的begin和end
rbegin, rend
//逆序迭代器，rbegin指向容器最后元素，rend指向容器首个元素前一个位置
//注意：逆序迭代器的自加自减操作逻辑相反，++会令迭代器指向容器前一个元素
crbegin, crend
//const版本的rbegin和rend
```

对于关联容器，其begin以及rbegin迭代器一般指向整个容器的最值。

### 迭代器失效
vector、deque以及基于deque的容器适配器，如果在其内部插入或删除某个元素，会导致一部分的元素迭代器都失效。根据容器类型可以分成如下两种情况：
1. 对于vector容器，如果新增元素导致容器扩容（即容器的capacity变更），则所有元素的迭代器都会失效。在不触发扩容的前提下，在容器内增删元素后，被增删元素后的所有元素都会被移动，因此这些元素的迭代器会失效。
2. deque在不同平台有不同的实现。对于其中一种实现，在首尾增删元素仅导致首尾元素的迭代器失效。如果在内部增删元素，如果增删的元素离头部近，则被修改元素之前的所有元素会被移动，其迭代器失效。如果增删的元素离尾部近，则被修改元素之后的所有元素会被移动，其迭代器失效。

关联类容器和list、forward_list一样，元素并不一定存储在连续的内存空间中，元素间通过指针来连接。因此删除某元素时，只会调整其他元素之间的指向关系，而不会移动其他元素。因此只有被删除元素的迭代器会失效。

对无序关联容器的增删一般只影响被修改元素本身的迭代器。如果增添元素后导致容器扩容（rehash），则整个容器中元素的迭代器都会失效。

## 新特性
本小节会介绍一些C++11以及更新版本引入的特性。
### emplace
emplace系列函数是C++11加入的成员函数，作用是向容器中加入新元素。共有三个：emplace（对应insert）、emplace_back（对应push_back）以及emplace_front（对应原来的push_front）。

如果容器中的元素类型较为复杂，原有的函数（如push_back等）的效率是比较低的。插入元素时，首先需要用户自行构造一个该元素类型的对象，其次调用push_back将该对象作为参数传给容器，然后容器将对象复制到容器内调用自己的构造函数去新建元素。

emplace系列函数不仅兼容原有的函数，支持用户将构建好的对象直接传入，还允许用户将构造元素对象的多个参数直接传入。emplace直接将这些参数传给容器，容器自行调用构造函数构建元素。相比原有函数节省了用户自行构建成员、成员复制到容器这两步操作。

举个例子，假如要向a中加入元素，使用push_back则需要用户事先生成pair类型的对象，然后调用push_back传入该对象。但emplace_back不仅兼容push_back的构造方法，还允许用户直接将构造pair类型的两个参数直接传入。
```cpp
vector<pair<int, int>> a;

a.push_back(make_pair(1, 2));

a.emplace_back(1,2);
```
通俗来说，旧有的插入元素的函数，需要用户将积木拼好，将完整的成品传给容器。容器再根据成品复制一个完全一样的成品加入到容器中。emplace函数允许用户直接把多个积木传进来，容器再将积木搭成成品加入到容器本身。
<br/><br/>


# 序列式容器
## vector
又称“动态数组”，和普通数组相比，vector无需事先声明数组大小，它会根据元素数量动态分配内存空间。vector在使用前需声明vector中元素的数据类型（支持各种复杂数据类型及用户自定义类型）
### 声明
vector容器在声明后是空的，没有任何元素，因此无需额外的清空操作。
```cpp
#include  <vector>     //调用

vector <元素类型> 变量名;     //直接声明，此时容器的size为0
```
### size与capacity详解
两者都是vector的属性，其中size指的是vector当前的实际大小（实际可容纳的元素数量）。而capacity是指该vector占用的空间大小（实际占用了多少个元素的空间）。一般capacity大于等于size。

vector刚声明时，size和capacity都为0。随着不断有元素push_back，size也逐渐增大。当size超过capacity时，vector会自动扩容，令capacity翻倍。例如，当size为8时，capacity也为8。当size为9时，capacity为16）。

vector的成员函数resize(k)可以将size改为k：若k大于原来的size，则补充默认元素直到有k个，如果k还大于原来的capacity，则capacity也同步扩展到k；若原来的size小于k，则只保留前k个元素，capacity不变。

vector的成员函数reserve(k)可以将capacity改为k，只有k比原capacity大该函数才生效。

### 初始化
初始化可指定容器的初始大小，也能指定部分元素的值。
```cpp
vector <元素类型> 变量名 (N);   
//声明一个vector容器并resize为N，并将这N个元素都初始化为0。之后就能在N范围内直接使用 operator []访问元素

vector <元素类型> 变量名 (N, k);   
//声明一个vector容器并并resize为N，同时将每个元素初始化为k

vector<vector<元素类型>> 变量名 (n, vector<元素类型>(m));
//对于二维的vector容器，可通过上述方式resize为n * m

vector <元素类型> 变量名 {值1, 值2, 值3}; 
//使用列表初始化的方式指定容器前三个元素的值，此时容器size为3

vector <元素类型> arr1 = arr2; 
//使用其他相同元素类型的vector初始化   
```

### 成员函数
```cpp
at(i)            //返回容器中第i个元素。如果越界at函数会返回异常（可使用try catch捕获）
back()           //返回容器中最后一个元素
capacity()       //返回容器当前实际占用的空间大小
clear()          //清空容器，size变为0，但capacity不变
emplace(pos, v)   //类似insert
emplace_back(v)   //类似push_back
empty()          //当容器为空，返回true，否则为false
erase(pos)       //删除迭代器pos处的元素，后面的所有元素均向前移动一位
front()          //返回容器中的首个元素
insert(pos, v)//在迭代器pos处插入元素v，pos处原来的元素以及后面的所有元素都会向后顺延一位
operator [k]     //下标访问并修改第k个元素，若越界直接报错，不会返回异常
pop_back()       //删除容器末尾元素，size也会减1
push_back(v) //在容器末尾添加一个元素v
reserve(k)       //改变容器capacity为k，只有k比原capacity大该函数才生效
resize(k)      //将容器size调整为k
resize(k, v)   //与resize(k)类似，若k比原size大则追加元素v
shrink_to_fit() //释放多余的capacity，令其等于size（该操作不一定生效，如果生效，可能导致空间重分配，所有元素迭代器失效）
size()          //返回容器中当前元素数量
```
<br/><br/>

## array
array即“定长数组”，使用上几乎与C中的数组无区别。该容器最所能容纳的元素数量在声明时必须确定：
```cpp
#include <array>

array<元素类型, N> 变量名;
//声明一个最大长度为N的array容器

//成员函数
at(i)        //返回容器中第i个元素。如果越界at函数会返回异常（可以使用try ... catch捕获）
back()       //返回容器中最后一个元素
data()       //返回该容器底层数组的头指针，相当于返回int a[N]数组的头指针a
operator [k] //下标访问并修改第k个元素，若越界直接报错，不会返回异常
empty()      //当容器为空，返回true，否则为false
front()      //返回容器中的首个元素
size()       //返回容器中当前元素数量
```

由于array容器的大小无法更改，相比于vector，在使用上灵活性更低。vector容器的大小可变，其元素的存储空间在内存上由动态存储区（堆）来管理，因此同一个vector的存储空间在内存上不一定连续。array容器的存储空间由栈来管理，整个array容器的存储空间在内存上是连续的。因此在无需变更数组长度的情况下，array的操作效率要高于vector。


<br/><br/>

## deque
一种双端队列，此种队列两端均可插入元素，队首、队尾元素均可删除。可以理解为是一种两端都可拓展长度的vector。
### 声明
```cpp
#include <deque>

deque <元素类型> 变量名;
```
### 成员函数
```cpp
back()             //返回队尾元素
clear()            //清空队列，size变为0
emplace(pos, v)    //类似insert
emplace_front(v)   //类似push_front
emplace_back(v)    //类似push_back
empty()            //队列为空返回true
erase(pos)         //删除迭代器pos处的元素，后面的所有元素均向前移动一位
front()            //返回队首元素
insert(pos, v) //在迭代器pos处插入元素v，pos处原来的元素以及后面的所有元素都会向后顺延一位
operator [k]       //下标访问并修改第k个元素
pop_back()         //删除队尾元素
pop_front()        //删除队首元素。
push_back(v)   //在队尾插入元素
push_front(v)  //在队首插入元素
resize(k)      //将容器size调整为k
resize(k, v)   //与resize(k)类似，若k比原size大则追加元素v
size()             //返回队列元素个数
```
<br/><br/>


## list
一种底层为双向链表的容器，可在O(1)时间内在任何位置执行插入和删除，但不支持随机存取（不支持下标访问和at()函数）。

```cpp
#include <list>

list <元素类型> 变量名;

// 成员函数
back()         //返回尾部元素
clear()        //清空容器，size变为0
emplace()      //类似insert
emplace_back(v)//类似push_back
emplace_front(v)//类似push_front
empty()        //容器为空返回true
erase(iterator) //删除迭代器对应的元素
erase(iterator lef, iterator rig) //删除所有迭代器在[lef, rig)范围内的元素
front()        //返回头部元素
insert(pos, v) //在迭代器pos处插入元素v，pos处原来的元素被顺延到后面
pop_back()     //删除末尾元素
pop_front()    //删除开头元素
push_back(v)   //在末尾插入元素
push_front(v)  //在开头插入元素
resize(k)      //将容器size调整为k
resize(k, v)   //与resize(k)类似，若k比原size大则追加元素v
reverse()      //逆转容器中的元素顺序
size()         //返回容器元素个数
sort()         //排序链表元素，默认为升序
sort(Cmp)      //自定义排序函数，形式与<algorithm>的sort相同
```

## forward_list
仅能从前向后遍历的单向链表，相比双向链表，同样的元素类型和size下要更省空间。单向链表没有在链表尾部修改元素的成员函数，也没有size成员函数。
```cpp
#include <forward_list> 

forward_list <元素类型> 变量名;

// 成员函数
clear()        //清空容器，size变为0
emplace_after(pos, v) //类似insert_after
emplace_front(v)//类似push_front
empty()        //容器为空返回true
erase_after(pos) //删除迭代器pos后一个的元素
erase_after(iterator lef, iterator rig) // 删除所有迭代器在(lef, rig)范围内的元素
front()        //返回头部元素
insert_after(pos, v) //在迭代器pos的后面插入元素
pop_front()    //删除开头元素
push_front(v)  //在开头插入元素
resize(k)      //将容器size调整为k
resize(k, v)   //与resize(k)类似，若k比原size大则追加元素v
reverse()      //逆转容器中的元素顺序
sort()         //排序链表元素，默认为升序
sort(Cmp)      //自定义排序函数，形式与<algorithm>的sort相同
```

<br/><br/>

## 容器适配器
简单来说就是基于deque容器加入某些改造和约束而创造的一种数据结构，虽然C++称之为容器适配器，但简单来说直接视为容器也没什么影响。举个例子，假如说原来的容器是一个标准的国标插座接口，适配器就相当于在这个接口上插了个其他国家标准的转换器。这里介绍栈（stack）、队列（queue）及优先队列（priority_queue）三种容器适配器。

### stack
栈的定义与成员函数：
```cpp
#include <stack>

stack <元素类型> 变量名;

//成员函数
emplace(v)  //类似push
empty()         //栈为空返回true 否则返回false
size()          //返回栈中元素个数
pop()           //删除栈顶元素，该函数无返回值
top()           //返回栈顶元素
push(v)     //在栈顶加入元素
```

### queue
队列的声明与成员函数：
```cpp
#include <queue>

queue <元素类型> 变量名;    

//成员函数
back()         //返回队尾元素
emplace(v) //类似push
empty()        //队列为空返回true 否则返回false
front()        //返回队首元素
pop()          //删除队首元素
push(v)    //在队尾加入元素
size()         //返回队列元素个数
```

### priority_queue
底层通过堆维护的优先队列。
#### 声明
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


#### 成员函数
```cpp
emplace(v)//类似push
empty()       //队列为空返回true 否则返回false 
pop()         //删除队首（堆顶）元素，并重新维护堆
push(v)   //在队列中插入元素，并重新维护堆
size()        //返回队列元素个数
top()         //返回队首（堆顶）元素
```
<br/><br/>

# 关联类容器（Associative containers）
## 简介
此类容器基本以红黑树为底层数据结构。因此此类容器中的元素基于其key值按顺序存储，对元素的增删查改操作平均可在$O(\log N)$时间内完成。

关联容器中有如下特殊的成员函数：
```cpp
lower_bound(k) //返回首个key大于等于k的迭代器
upper_bound(k) //返回首个key大于k的迭代器
```

## set
### 基本用法
以红黑树为底层数据结构的集合类型。红黑树基于集合中元素本身的值而生成。set中的元素不可直接更改（可通过删除再插入的方式进行）。容器begin迭代器处存储最小值，rbegin迭代器处存储最大值。
```cpp
#include <set> 

set <元素类型> 变量名;
```
### 成员函数
```cpp
clear()    //清空集合，size变为0
contains(k)//判断集合中是否存在元素k，若存在返回true
count(k)   //计算集合中元素k的个数，由于容器不允许有重复元素，结果只能为0或1
emplace(k) //类似insert
empty()    //判断集合是否为空，为空返回True
erase(iterator) //删除迭代器iterator对应的元素
erase(k)   //在集合中删除值为k的元素（若元素k不存在则不执行）
erase(iterator lef, iterator rig)  //删除所有迭代器在[lef, rig)范围内的元素
find(k)    //查找是否存在元素k，若存在返回k对应迭代器，否则返回end迭代器
insert(k)  //向集合中插入元素k（若已有元素k则不执行）
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
和set类型基本一致，区别在于multiset允许其中有重复元素，而set不允许。两者的声明、初始化及成员函数基本相同。

## map
map是一个以键值对（Key, Value）构成的pair为基本元素的容器结构。底层红黑树基于元素的键（Key）生成，因此元素按Key排序，不同元素的Key均不相同。map不允许其中的多个元素具有相同的key。
### 声明
```cpp
#include <amp>

map <Key类型, Value类型> 变量名;
```

### 成员函数
```cpp
at(k)         //返回key为k的pair的value，若不存在对应的pair则返回异常
clear()       //清空map，size变为0
contains(k)   //查找是否存在Key为k的键值对，存在则返回True，否则False
count(k)      //返回Key为k的元素个数
empty()       //当map为空返回true，否则为false
emplace(k, v) //兼容insert，此外还可传入key和value，让容器自行生成pair并插入
erase(iterator) //删除迭代器对应的元素
erase(k)   //在map中删除key为k的元素
erase(iterator lef, iterator rig) //删除所有迭代器在[lef, rig)范围内的元素
insert(pair)  //将pair插入map中
operator [k]  //可使用下标直接查找Key为k的元素并对其value进行修改
size()        //返回元素个数
```

## multimap
和map类型基本一致，区别在于multimap允许其中有多个元素有相同的Key，而map不允许。两者的声明、初始化及成员函数基本相同。


<br/><br/>

# 无序关联容器（Unordered associative containers）
## 简介
C++11加入的容器类型，底层为开链哈希表。在此类容器中，基于元素的Key值去进行增删改操作平均能以$O(1)$时间完成。

无序关联容器中会生成若干个桶（bucket），每个桶中存储的是哈希值相同的元素。容器会根据元素的key值计算出该元素对应的哈希值，然后将该元素存储到对应的桶中。

容器初始时仅有一个桶，随着容器中元素数量越多，平均每个桶中的元素数量也越多，发生哈希冲突的可能性也越高。当平均每个桶中的元素数量超过一个阈值时，容器会扩充出更多的桶，来降低哈希冲突的概率。这个阈值一般称为负载因子（load_factor），在C++中默认为1。

无序关联容器有以下几个特有的成员函数：
```cpp
bucket(k)           
//计算Key为k的元素会存储到哪个桶中，返回通的序号（只计算k对应的桶的序号，不会更改容器）
bucket_count()     
//返回当前容器中bucket的数量
bucket_size(n)     
//第n个bucket中元素的数量，bucket的序号按0 ... bucket_count()-1排序
load_factor()      
//返回容器当前平均每个桶中的元素数量，等于size()/bucket_count()。
max_bucket_count() 
//返回容器最多能容纳的bucket数量，该值是一个常量
max_load_factor() 
//返回当前容器的负载因子，当容器的load_factor超过该值会扩充（rehash）
max_load_factor(k) 
//将当前容器的负载因子更改为k
rehash(k)
//调整bucket数量，使其不低于k，且容器每个桶中平均元素数量不超过负载因子
reserve(k)
//调整bucket数量，使容器每个桶中平均元素数量不超过负载因子的情况下能容纳至少k个元素
```
另外，无序关联容器没有逆向迭代器rbegin及rend。

## unordered_map
unordered_map简单来说是一个以键值对（Key, Value）构成的pair为元素的开链哈希表，其中Key和Value可以是复杂数据类型，如string、double或者用户自定义的类型。unordered_map中不允许存在key相同的元素。

### 声明及初始化
```cpp
#include <unordered_map>

unordered_map <Key类型, Value类型> 变量名; 
```

### 成员函数
除了没有关联容器特有的几种成员函数外，unordered_map的成员函数与map基本一致，可参考上文介绍map容器的部分。

## unordered_multimap
允许存储重复的元素的unordered_map，定义与成员函数基本与unordered_map一致。

## unordered_set
底层为开链哈希表的集合类型。哈希表以元素本身的值为Key存储元素。unordered_set不允许存储重复的元素。

### 声明及初始化
```cpp
#include <unordered_set>

unordered_set <元素类型> 变量名; 
```
### 成员函数
除了没有关联容器特有的几种成员函数外，unordered_set的成员函数与set基本一致，可参考上文介绍set容器的部分。另外，由于unordered_set是无序关联容器，因此无需像set一样定义排序规则。

## unordered_multiset
允许存储重复的元素的unordered_set，定义与成员函数基本与unordered_set一致。
<br/><br/>

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
operator [k]//直接访问并修改第k个位置的bit
reset()     //将所有bit位设为0
set()       //将所有bit位设为1
set(pos, v)  //将第pos位设置为v
size()      //返回bitset大小，即所有bit的个数
to_ullong() //返回unsigned long long形式的bitset变量
to_ulong()  //返回unsigned long形式的bitset变量
to_string() //返回字符串形式的bitset变量
```

此外，可直接使用“==”或“!=”对不同bitset变量进行比较，也可直接对bitset变量使用位运算操作。


# 参考资料

[OI Wiki：迭代器](https://oi-wiki.org/lang/csl/iterator/)

[C++ reference](https://en.cppreference.com/w/)

[Iterator invalidation rules for C++ containers](https://stackoverflow.com/questions/6438086/iterator-invalidation-rules-for-c-containers)