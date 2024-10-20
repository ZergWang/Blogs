# 问题引入
给出两个至多n项的多项式：

$f(x) = a_0x^0 + a_1x^1 + a_2x^2 + ... + a_{n-1}x^{n-1}$ 

以及

$g(x) = b_0x^0 + b_1x^1 + b_2x^2 + ... + b_{n-1}x^{n-1}$

求它们相乘的结果。

最直接的解决方法是：枚举$f(x)$的每一项，与$g(x)$的每一项分别相乘，再合并同类项，时间复杂度为$O(n^2)$。然而，使用快速傅里叶变换，时间复杂度仅$O(n \times log_2 ​n)$。
<br/><br/>

# 背景知识
## 点值表示法
对于一个有n项的多项式，可以使用其n个系数来表示它（这n个系数可表示唯一多项式）：

$f(x)=\{a_0​, a_1​, a_2​, ... , a_{n−1​}\}$

在平面直角坐标系中，通过n个点，也可以表示唯一多项式。取n个不同的x值： $x_0, x_1, x_2 ... x_{n-1}$ 代入$f(x)$，可得到n个不同的点：

$(x_0,f(x_0))$ 、 $(x_1,f(x_1))$ 、 $(x_2,f(x_2)) ... (x_{n-1},f(x_{n-1}))$ 

这n个点也可表示唯一多项式。这是因为：

如果将这n个系数视为未知数，取n个不同的x值，就得到了n个互不相同的n元一次方程，这n个方程联立必定能解出这n个未知数。也就是说这n个不同的x值和$f(x)$值可以唯一表示这n个系数。

## 复数
复数相乘时，模长相乘，幅角相加。（幅角即复数对应的向量与坐标轴间的夹角），简要证明如下：
- 首先，将复数写为极坐标形式：$z = a + bi = r\cos\theta + ir\sin\theta$，此时$r$为模长，$\theta$为幅角。

- 其次，根据欧拉公式：$e^{i \theta}=\cos \theta + i \sin \theta $，可将复数转成指数形式：$r\cos\theta + ir\sin\theta=r e^{i\theta}$

- 若有复数$r_1 e^{i\theta_1}$以及 $r_2 e^{i\theta_2}$，则二者相乘为：$r_1r_2 e^{i(\theta_1+\theta_2)}$，结果即为“模长相乘，幅角相加”。

## 离散傅里叶变换
Discrete Fourier Transform，简称DFT，作用是将多项式从系数表示转为点值表示。

离散傅里叶变换在将$f(x)$转为点值表达式时，是将n个不同的复数代入$f(x)$。这n个复数均从复平面上的单位圆上取得：

![](快速傅里叶变换（Fast%20Fourier%20Transformation）_1.png)

设取得的n个复数为$w_n^0、w_n^1、w_n^2 ... w_n^{n-1}$。首先在复平面坐标$(1,0)$处取第一个点$w_n^0$，之后逆时针在单位圆上等距离依次取剩下n-1个点。如图展示了当n为8时取点情况。

对于这n个复数，假设$0 \leq i \leq n-1$，则有如下性质：

- $w_n^i = (w_n^1)^k $ （由模长相乘，幅角相加的特性可得）
<br/>
- $w_n^i = w_{2n}^{2i}$
<br/>
- $w_n^i = -w_n^{i+n/2}$ （一个点转半圈$(i+n/2)$得到的点与原来的点关于坐标原点对称）
<br/>
- $w_n^i = w_n^{i+n}$ （一个点转一整圈$(i+n)$就回到原来的点）
<br/>
- $w_n^i$的倒数与其共轭复数相等
<br/>

<font color='ff0000'>注意：复平面上的每个点代表一个复数，而点值表示法中的点坐标$(w_n^i, f(w_n^i))$是由两个复数组成的。两者不要混淆</font>

## 快速傅里叶变换
Fast Fourier Transformation，简称FFT。离散傅里叶变换的时间复杂度仍为$O(n)$。对此，快速傅里叶变换利用分治和递归的思想进行优化，使时间复杂度降为$O(n \times log_2 ​n)$。

首先对$f(x)$的奇数项和偶数项进行分离：（假设n为偶数）

$f(x) = (a_0x^0 + a_2x^2 + ... + a_{n-2}x^{n-2}) + (a_1x^1 + a_3x^3 + ... +  a_{n-1}x^{n-1})$

$ = (a_0 + a_2x^2 + ... + a_{n-2}x^{n-2}) + x (a_1 + a_3x^2 + a_5x^4 + ...a_{n-1}x^{n-2})$

$ = f_0(x^2) + x f_1(x^2)$
<br/>

将$w_n^i$代入$f(x)$：

$f(w_n^i) = f_0(w_n^{2i}) + w_n^i f_1(w_n^{2i})$

$ = f_0(w_{n/2}^{i}) + w_n^i f_1(w_{n/2}^{i})$
<br/>

将$w_n^{i+n/2}$代入$f(x)$：

$f(w_n^{i+n/2}) = f_0(w_n^{2i+n}) + w_n^{i+n/2} f_1(w_n^{2i+n})$

$ = f_0(w_n^{2i}) - w_n^i f_1(w_n^{2i})$

$ = f_0(w_{n/2}^i) - w_n^i f_1(w_{n/2}^i)$
<br/>

## 离散傅里叶逆变换
Inverse Discrete Fourier Transform，简称IDFT，用于将多项式从点值表示转为系数表示。



# 代码及优化


<br/><br/>

# 参考资料
[FFT详解](https://blog.csdn.net/GGN_2015/article/details/68922404)
[FFT算法讲解](https://blog.csdn.net/WADuan2/article/details/79529900)
[小学生都能看懂的FFT！！！](https://www.cnblogs.com/RabbitHu/p/FFT.html)