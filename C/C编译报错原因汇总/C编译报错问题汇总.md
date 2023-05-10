### declared inside parameter list

struct的定义在使用之后，或者是包含该struct定义的头文件引用有问题。
<br/>

### operation on 'XXX' may be undefined
自增、自减运算符（即++和--运算符）与其他运算符混用时可能会报这种错
例如：
 ```cpp
int idx = 0;
a[idx++] = b[idx];
 ```
在Windows下的某编译器中，实际执行的是a[0] = b[1]，但在Linux下实际执行是a[0] = b[0]。

因此尽量避免这种可能产生问题的写法……
<br/>

### A label can only be part of a statement and a declaration is not a statement

在switch case语句中，若在case后定义变量且该case中的语句未被大括号括起来，则该变量的作用域为整个switch，而不是仅在对应的case中生效。应在对应case中加入大括号，为变量声明语句划定作用域。

<br/>

### Control reaches end of non-void function
C++版本的报错信息是“non-void function does not return a value”。

有返回值的函数最后没写return……

<br/>

### Conflicting types for ...
函数使用的位置位于声明之前，或未声明函数原型就使用函数。

<br/>

### reference to non-static member function must be called
对于非静态成员函数，其参数表会隐式地加上this指针，这可能导致