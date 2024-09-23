# NULL与nullptr
在C语言中，NULL一般被宏定义为```(void *)0```，在将NULL赋值给指针时一般会执行隐式类型转换，转换成被赋值指针对应的类型。

在C++中，NULL一般会被定义为```#define NULL 0```，在代码中一般被视为一个整型值。因此如果NULL作为参数传入函数时，由于函数重载，可能不会执行到正确的函数。若对NULL使用auto来推断类型，也可能会出错。

另外，C++一般不允许隐式类型转换，因此在C++11中引入nullptr，明确将其作为空指针，其类型为std::nullptr_t。


# new与delete
## new
new类似于malloc，用于申请动态内存空间，使用方法：

```cpp
//申请单个Type类型的空间
Type * p = new Type;
Type * p = new Type (k); //分配的同时初始化为k，相当于*p = k
Type * p = new Type {k}; //支持列表初始化

//申请一个含多个Type类型的数组
Type * p = new Type [n]; 
Type * p = new Type [n] {k1, k2 ...}; //支持列表初始化，数组不支持“()”形式的初始化
```
## delete
delete与new对应，用于回收单个元素的内存空间；delete [] 与 new [] 对应，用于回收连续的内存空间，两者一一对应，不能混用。由于new[] 相较于new会额外分配一段空间存储数组长度，delete []会根据这个长度来多次执行delete。如果混用，delete可能会从不正确的地址开始执行，从而导致bug。

## new/delete与malloc/free的关系
new的底层一般使用malloc实现。但new可以看作是更高级版本的malloc：
1. malloc是库函数，需要用户自己计算要分配的内存大小并作为参数传入。new是关键字，会根据类型自动分配内存大小，使用上new更灵活。
2. malloc的返回值为void*类型，在赋值给指针时需要强制类型转换，new则不需要。
3. 如果要为类分配空间，使用new/delete可以调用构造/析构函数，但malloc/free没有这个功能。
4. new分配内存空间失败会抛出异常，而malloc失败返回NULL。

new的空间一般不能使用free释放，同理malloc的空间也最好不用delete释放。对于没有显式定义析构函数的类，可使用free或delete释放内存。对于没有显式定义默认构造函数的类，一般也可混用new或malloc。

<br/><br/>

# 智能指针
当分配动态内存空间后，可能会遇到某些情况导致没有手动delete或者free掉，这就造成了内存泄漏。在C++11之前可以使用一种名为auto_ptr的指针，该指针可以在程序或函数结束的时候自动释放内存空间。但C++11取消了对该指针的支持。

C++11引用了三种新的智能指针来管理动态内存，需通过头文件<memory>调用。这些智能指针本质上模板类，当离开指针的作用域时，类会自动执行析构函数，从而保证分配的内存空间被释放掉。

智能指针的类重载了“*”和“->”运算符，因此这两个运算符的功能与普通指针一样。另外，智能指针的判空与普通指针也一致（```if (p)```或者```if (p==nullptr)```都可 ）。

## unique_ptr
独占分配的内存所有权的智能指针。

### 声明与初始化
```cpp
unique_ptr<int> p1 (new int(6));   //构造一个智能指针对象

unique_ptr<int[]> p2 (new int [3]);  //可管理数组空间

unique_ptr<double> p3;  //调用默认构造函数，此时p3指向nullptr
```
### 成员函数

```cpp
get()  //返回内存块地址，当指向nullptr时返回0

release() 
//结束对内存块的管理，返回内存地址，之后该智能指针指向nullptr
//然而该内存块并未被释放掉，其他指针可以指向该内存块并正常使用

reset() //释放内存块，然后指向nullptr

reset(新内存地址) 
//释放掉原内存块，然后管理新内存块，例如 p1.reset(new int)
```
### unique_ptr的排他性
unique_ptr独占其管理的内存块，不允许其他指针管理或指向其管理的内存块。因此无法直接将左值赋值给unique_ptr，也不能使用左值来构造unique_ptr（因为这意味着两个智能指针管理同一内存块，如果其中一个指针A先释放掉内存块，另一个指针B就变成悬挂指针，对B的使用或释放都会导致不可预料的问题）。

unique_ptr只允许右值赋值或构造：
```cpp
unique_ptr<int> p1;
p1 = unique_ptr<int>(new int (3));  
//使用unique_ptr的构造函数，该函数返回的是一个临时对象，该对象为右值，因此可赋值给p1
// p1 = make_unique<int>(4);  //也可使用工厂函数

unique_ptr<int> p2(std::move(p1));  //允许右值构造智能指针对象
// p2 = std::move(p1);              //允许右值赋值
```
在上述代码中，p2原来管理的内存会被释放掉，p1管理的内存转移给p2，然后指向nullptr。

### unique_ptr相比auto_ptr的优势
1. unique_ptr仅允许右值赋值或构造，强化了排他性，防止多个指针管理同一块内存。
2. unique_ptr可管理数组空间。

## share_ptr
可以与其他指针共同管理同一内存块的智能指针。其内置的计数器可以记录当前有多少个share_ptr共同管理这个内存块。

### 声明与初始化
```cpp
shared_ptr<int> p1;  //调用默认构造函数，此时指向nullptr

p1 = shared_ptr<int>(new int (1));

shared_ptr<int> p2 (new int (2));

shared_ptr<int> p3 = p2;  //允许同类型的左值或右值赋值与构造

shared_ptr<int> p4 (p3);

shared_ptr<int> p5 = make_shared<int>(5); //使用工厂函数
```

### 成员函数
```cpp
get()  //返回智能指针管理的内存地址，当指向nullptr时返回0

reset()     //结束对当前内存块的管理，内存块计数器减1
reset(新内存地址)  //管理新内存块，原内存块计数器减1，新内存块计数器加1
unique()       //如果当前内存块只被当前指针管理则返回1，否则返回0
use_count()    //返回管理当前内存块的share_ptr数量
```
### share_ptr特性
对于某个内存块m，当首个share_ptr接管m时，该指针会重新分配空间，多留一个空间存储一个计数器。每当多一个share_ptr管理mm时，m对应的计数器就加一。当某个管理m的share_ptr被析构或者取消对m的管理时，计数器就减一。当计数器变为0，说明没有指针管理m了，此时m会自动被释放掉。

根据以上特性，share_ptr使用make_shared函数分配并托管内存的效率要更高，因为make_shared只需要分配一次内存。而使用share_ptr的构造函数等方式需要两次分配内存，首先new int会分配一次内存空间，然后构造函数需要再一次分配（要留出计数器的空间）。

### share_ptr循环引用
假设有如下代码
```cpp
class B;
class A { public: shared_ptr<B> p = nullptr; };
class B { public: shared_ptr<A> p = nullptr; };

int main() {
    shared_ptr<A> pa (new A);
    shared_ptr<B> pb (new B);
    pa->p = pb;
    pb->p = pa;    
}
```
pa指向的内存块，其计数器为2（分别是pa和pb->p）；同理pb指向的内存块的计数器为2。当程序结束时，pa、pb被释放掉，两个内存块的计数都减1，但由于没有归零，两块内存都无法释放，造成了内存泄漏。

## weak_ptr

weak_ptr可以解决上述share_ptr循环引用的问题。weak_ptr可以简单理解成电竞游戏里的OB（观察者）。它只能用于观察share_ptr对应内存块的计数器，但它本身并不引起计数器的变化。

对于上述share_ptr循环引用的问题，只要将其中一个类的成员声明成weak_ptr即可。当程序结束时，当程序结束时，两个内存块的计数都减1，但其中一个变成了0，该内存块就会释放掉，这样一来另一个内存块的计数也会减1，从而成功释放。

### 声明及初始化
```cpp
shared_ptr<int> p1 (new int (2));
//weak_ptr要么使用默认构造函数，要么通过share_ptr或其他weak_ptr构造
weak_ptr<int> p2 (p1);  
weak_ptr<int> p3 (p2);
weak_ptr<int> p4;
```
### 成员函数
weak_ptr的“*”以及“->”方法被禁用。
```cpp
expired()    //若观察的内存块计数器变0返回1，否则返回0    
lock()       //生成并返回一个管理当前内存块的share_ptr
//用法： shared_ptr<int> ps = pw.lock();
//如果ps指向nullptr，则ps也指向nullptr
reset()      //结束对当前内存块的观察
use_count()  //返回weak_ptr观察的内存块的计数器
```

## 注意事项

1. 智能指针在其作用域结束后调用其析构函数，从而释放内存空间。因此不宜将智能指针声明为全局变量。

2. 使用智能指针并不能完全避免内存泄漏。如果使用过程中违反了某些原则，也会导致一些无法预料的后果。例如，对于智能指针get方法得到的地址，不能delete该地址（导致智能指变成悬挂指针），不能使用该地址初始化别的智能指针（可能导致多个unique_ptr管理同一内存块）；智能指针最好不要与普通指针混用……

<br/><br/>

# 参考资料

[share_ptr循环引用产生原因及其解决方案](https://blog.csdn.net/weixin_45880571/article/details/119345415)

[C++ 智能指针 - 全部用法详解](https://blog.csdn.net/cpp_learner/article/details/118912592)