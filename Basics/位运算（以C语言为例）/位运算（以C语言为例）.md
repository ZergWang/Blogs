# 前言
本篇博文部分思路及图表来自MoreWindows博客，原文链接：https://blog.csdn.net/MoreWindows/article/details/7354571

本文所涉及操作均以C语言为例，以下操作仅能对整型数据使用。

本文仅探讨位运算的基本操作，不涉及位运算对复杂算法的优化。

**注意：位运算优先级较低，为保证正确，建议添加括号。**

<br/><br/>

# 位运算符号介绍
| 符号 | 描述 | 运算规则 |
| ---- | ---- | ---- |
| &    |  与  |  两位都为1时，结果才为1 |
| \|   |  或  |  两位都为0时，结果才为0 |
| ^    | 异或 |  两位相同为0，不同为1（异或可看作不进位的加法运算）   |
| ~    | 按位取反 | 唯一的单目运算符，将0变1，1变0 |
| <<   | 左移 | 左移若干位，高位丢弃，低位补0   |
| >>   | 右移 | 右移若干位，无符号数高位补0，有符号数高位补1 |

不同编译器对有符号数的右移运算处理方法不一样（在Dev C++中负数也是直接除以2。

**注意：执行左移或右移操作时，请保证移动的位数小于变量占用的bit的数量。以int型（32bit，取值范围为$[-2^{31} ,  2^{31}-1]$）为例，为保证结果正确，左移和右移最多31位。

<br/><br/>

# 位运算操作技巧
## 乘除
对于无符号数，右移n位相当于除以$2^n$，左移n位相当于乘以$2^n$。
```cpp
int a=10, b=10;
a <<= 3;
b >>= 2;
```
a左移3位，即a的值乘以$2^3$，也就是乘以8，即80。
b右移2位，即b的值除以$2^2$，也就是除以4，即2。

对于有符号数，左移n位相当于乘以$2^n$，但右移操作由于会在高位补1，其运算结果是不确定的，因此有符号数不应通过右移操作来进行乘除运算。

## 取符号位
判断变量的符号：
```cpp
int sign = a >> 31; //a为非负数，sign为0；a为负数，sign为-1
```
若a为非负数，其最高位（第31位的bit）为0。右移31位后，该bit移动到第0位，其他31位都是补充的0，因此右移31位后其值为0。

若a为负数，其最高位（第31位的bit）为1，右移31位后，该bit移动到第0位，其他31位都是补充的1。32位bit全为1，在int中被视为-1的补码，因此负数右移31位后得到-1。

## 取任意位
取a的第p位（从右往左数，最右边为第0位），赋值给b。
```cpp
int b = a >> p & 1; 
```
若a的第p位为0，则b为0，否则为1。

## 将任意位置为0或1
```cpp
a |= 1 << p;    //将变量a的第p位（从右往左数）置为1
b &= ~(1 << p); //将变量b的第p位（从右往左数）置为0
```

## 取相反数
```cpp
int signReverse = ~a+1;
```
由于计算机使用原码、反码、补码的方式存储数据，int范围内所有数字按位取反后加1都等于其相反数。除了$-2^{31}$，它按位取反加1后等于自己。

上述两种方法结合，可以写出一种非常高端的取绝对值的函数：
```cpp
int GetAbs(int a) {
    int t=a>>31;
    return (a^t)-t;
} 
```
用t取得a的符号位，任何数与0异或值不变，与1异或值取反，之后若a为负数 再减去-1，即加1。（该方法对-2^31无效）

## 判断是否为2的次方
[LeetCode 231](https://leetcode.com/problems/power-of-two/)
```cpp
bool isPowerOfTwo(int n){
    return n > 0 && (n & (n - 1)) == 0;
}
```

<br/><br/>

## 模拟开关
由于“~”是按位取反，因此对于值为True的bool型变量（8位存储空间，实际值为00000001），取反后变成11111110，还是True。因此要模拟开关，需使用异或操作。

```cpp
bool s = 0;
s ^= 1;   //每执行一次，s就会从0变1，或从1变0。
```

## 两数交换
```cpp
void Swap(int a, int b) {  
    a ^= b;  
    b ^= a;  
    a ^= b; 
}  
```
异或满足交换律，且一个数a对另一个数b连续异或两次，得到它自己。（即a\^b\^b=a）
