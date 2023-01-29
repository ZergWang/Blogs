# Warning


<br/><br/>

# Error
#### A label can only be part of a statement and a declaration is not a statement

报错原因：在switch case语句中，若在case后定义变量且该case中的语句未被大括号括起来，则该变量的作用域为整个switch，而不是仅在对应的case中生效。

应对方法：在对应case中加入大括号，为变量声明语句划定作用域。

<br/>

#### Control reaches end of non-void function
报错原因：有返回值的函数最后没写return……

<br/>

#### Conflicting types for ...
报错原因：函数使用的位置位于声明之前，或未声明函数原型就使用函数。

应对方法：提前声明函数，或调整函数定义位置。
