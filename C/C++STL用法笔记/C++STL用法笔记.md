# 动态数组（vector）
不存东西时不占空间，来多少数据就用多少空间，用于节省空间。
```cpp
#include  <vector>     //调用

vector <int> a;     //声明

a.push_back(3)        //在数组a的最后添加一个元素3
 
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
C++11版本加入的数据类型，底层为开链的哈希表。unordered_map简单来说是一个以键值对（Key, Value）为元素的数组，其中Key可以是复杂数据类型，如string、double或者用户自定义的类型。

```cpp
unordered_map <Key类型, Value类型> a //创建unordered_map，a为变量名

a.emplace(Key, Value);     //插入键值对
a.insert(); 



for (auto i = a.begin(); i != a.end(); ++i)  
// 迭代，其中i为对应的键值对，i->first为Key，i->second为Value
```