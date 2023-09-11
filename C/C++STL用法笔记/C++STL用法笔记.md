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


# unordered_map
