## 作用域问题
#### 报错提示
error：a label can only be part of a statement and a declaration is not a statement

#### 报错原因
在switch case语句中，若在case后定义变量且该case中的语句未被大括号括起来，则该变量的作用域为整个switch，而不是仅在对应的case中生效。

#### 解决方法
在对应case中加入大括号，为变量声明语句划定作用域。
