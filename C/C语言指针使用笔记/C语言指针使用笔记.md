# 常见函数及问题

#### new与delete

#### malloc与free
malloc用于分配变量所需的内存空间，并返回一个指向它的指针。若申请内存空间失败，则返回NULL。malloc函数仅一个参数size，为要申请的内存大小，以字节为单位。使用的时候一般要进行类型转换。
free无返回值，参数为待释放内存的指针变量。
```cpp
#include <stdlib.h> 
int* p;
p = (int*) malloc(4);  //申请了一个int型指针，分配了4字节的内存空间
free(p);               //使用完后释放掉p所占内存
```
#### 函数返回局部变量
若在函数内部定义了变量且函数返回值为该变量时，编译器一般会提示“function returns address of local variable”。这是因为离开函数后，函数内的局部变量会被注销，因此函数返回值所处内存空间已被占用，可能会产生错误。

解决方法：
- 定义局部变量时使用static修饰。
- 使用malloc为局部变量分配内存并使用free释放。
- 使用new为局部变量分配内存并使用delete释放。
<br/><br/>

# 二级指针
#### 函数传参
```cpp
#include <iostream>
using namespace std;
 
void test(int* q){
    cout<<&q<<endl;    
}
 
int main(){
    int a = 10;
    int* p;
    p = &a;
    cout<<&p<<endl;  
    test(p);
    cout<<&p<<endl;        
    return 0;
}
```
在进入函数的时候，传入的参数是通过创建一个副本进行传入的。以上面的程序为例，在进入函数后系统另外创建了一个指针变量q，虽然q里面存的值与p一样，但q是一个新的变量，其地址与p是不一样的。

在使用函数处理链表等带有指针的复杂数据类型时，为了确保离开函数后对指针的更改依然有效，就需要用到二级指针了。

再看个例子：
```cpp
void my_malloc(char **s){  
    *s=(char*)malloc(100);  
}
 
int main(){  
    char *p= NULL;  
    my_malloc(&p);
    //do something
    if(p)
        free(p);  
}  
```
如果不用二级指针，在函数中就是给一个副本指针s分配了空间，离开函数后副本指针s销毁，p从头到尾就没分配到空间，白忙活一场。
#### 二维数组传参
实际上，二级指针不能用于函数中二维数组的传参。这是因为二维数组在内存中和一维数组一样，仍然是顺序存储的：
![](C语言指针使用笔记_1.png)
也就是说，对于二维数组int a[3][3]，如果有int* p = a，则p[0]表示数组中的首个元素，即实际上的a[0][0]。同理，p[1]表示实际上的a[0][1]，函数中的a[2]表示实际上的a[0][2]……

二级指针是指向指针的指针，但数组名a是指向int的指针，强行传入肯定会出错。
<br/><br/>

# 数组名与指针变量的异同
#### 内存分配不同
数组名是一种特殊的指针，它本身指向数组首元素，又由于系统不会分配内存空间给数组名本身，因此，对于数组a和指针p：
```cpp
int a[3] = {1,2,3};
int* p = &a[0];
```
以下四者输出结果是一样的：
```cpp
a, p, &a, &a[0]
```
但由于系统另外分配了内存存储指针p，因此```&p```与上述地址均不同。
#### 数据类型不同
数组名和普通指针的数据类型是不同的，对于数组a，a是一个指向有3个元素的int数组的指针，而p是一个指向单个int变量的指针。
```cpp
printf("%d\n", sizeof(a));  //输出12 （3*4）
printf("%d\n", sizeof(p));  //输出8（64位系统）
printf("%d\n", sizeof(&a)); //&a表示一个内存地址，输出也为8
```
#### 下标及运算不同
数组名和普通指针均可使用操作符“[]”。但数组名会有一定的限制。
例如，下标不可为负数。a[-1]代表a[0]前一个地址的值。然而该值所处内存空间是未知的，如此取值甚至修改会带来不可预测的问题。

但对于普通指针，若能保持其指向的内容不越界，是可以令下标为负数的：
```cpp
int a[3] = {1, 2, 3};
int* p = &a[2];  
printf("%d\n", p[0]);   //输出3，p[0]与*p等价，与a[2]等价
printf("%d\n", p[-1]);  //输出2
printf("%d\n", p[-2]);  //输出1
}
```
令p指向a[2]，则p[0]指向的就是a[2]，p[-1]即p[0]前一个地址的值，也就是a[1]，以此类推。

此外，数组名被视为一个指针常量，不可修改。例如，*(a+1)同样能取得a[1]的值，但不可进行a++、a+=1这种操作。
#### 数组名退化为普通指针
在使用函数传递数组时，一般将数组名作为参数传入，由于参数一般声明为普通指针，因此在函数内部，数组名与普通指针无异。
<br/><br/>

# 数组指针
#### 性质

#### 应用

<br/><br/>

# 函数指针

<br/><br/>

# 结构体指针
#### 链表
结构体指针常用于构建链表，下面的程序展示了如何实现一个简单的单向链表。其中，head_id为头指针，其内部成员n存储链表的节点个数。current_id为遍历或插入节点过程中当前指向的节点。对于任一节点，其成员n为数据域，存储的是该节点的数据，p为指针域，指向该节点的前一个节点。
```cpp
#include <stdio.h>
#include <stdlib.h>
 
struct student_info{
    int n;
    struct student_info *p;
};
 
typedef student_info id;
 
#define Allocate(x) x=(id*)malloc(sizeof(id))
 
 
int main(){
    id *head_id = NULL;
    id *current_id = NULL;
 
    Allocate(head_id);
    Allocate(current_id);
 
    head_id->p = NULL;
    head_id->n = 0;
    current_id->p = head_id;
 
    int num;
    while (scanf("%d", &num) != EOF){
        if (num == -1) break;
        head_id->n++;
        id * new_id = NULL;
        Allocate(new_id);
        new_id->n = num;
        new_id->p = current_id->p;
        current_id->p = new_id;
    }
 
    for (int i=0; i < head_id->n; i++){
        current_id = current_id->p;
        printf("%d ",current_id->n);
        //printf("%d ",current_id->p->n);
        //current_id = current_id->p;
    }
    return 0;
}
```
在结构体指针中访问其成员的两种方法：
```cpp
(*指针变量名).成员名
 
指针变量名->成员名
```
<br/><br/>

# 参考资料
[二级指针的作用详解_majianfei1023的专栏-CSDN博客](https://blog.csdn.net/majianfei1023/article/details/46629065)

[C 库函数 – malloc() | 菜鸟教程](https://www.runoob.com/cprogramming/c-function-malloc.html)

[结构体指针，C语言结构体指针详解](http://c.biancheng.net/view/246.html)

[二级指针与二维数组](https://blog.csdn.net/u010275850/article/details/49556399)

[数组指针和指针数组的区别](https://zhuanlan.zhihu.com/p/55724052)