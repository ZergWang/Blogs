**本文为适用于LaTeX及MarkDown环境的公式编辑笔记**

# 希腊字母
大写字母对应的指令只需要将首字母大写即可，例如```\Alpha```。
| 大写字母   | 小写字母   | 指令           |
| --        | --         | --            | 
| $\Alpha$  | $\alpha$   | ```\alpha```  |
| $\Beta$   | $\beta$    | ```\beta```   |
| $\Gamma$  | $\gamma$   | ```\gamma```  |
| $\Delta$  | $\delta$   | ```\delta```  |
| $\Epsilon$| $\epsilon$ | ```\epsilon```|
| $\Zeta$   | $\zeta$    | ```\zeta```   |
| $\Eta$    | $\eta$     | ```\eta```    |
| $\Theta$  | $\theta$   | ```\theta```  |
| $\Iota$   | $\iota$    | ```\iota```   |
| $\Kappa$  | $\kappa$   | ```\kappa```  |
| $\Lambda$ | $\lambda$  | ```\lambda``` |
| $\Mu$     | $\mu$      | ```\mu```     |
| $\Nu$     | $\nu$      | ```\nu```     |
| $\Xi$     | $\xi$      | ```\xi```     |
| $\Omicron$| $\omicron$ | ```\omicron```|
| $\Pi$     | $\pi$      | ```\pi```     |
| $\Rho$    | $\rho$     | ```\rho```    |
| $\Sigma$  | $\sigma$   | ```\sigma```  |
| $\Tau$    | $\tau$     | ```\tau```    |
| $\Upsilon$| $\upsilon$ | ```\upsilon```|
| $\Phi$    | $\phi$     | ```\phi```    |
| $\Chi$    | $\chi$     | ```\chi```    |
| $\Psi$    | $\psi$     | ```\psi```    |
| $\Omega$  | $\omega$   | ```\omega```  |

<br/><br/>

# 特殊符号
## 特殊数值
| 符号 | 效果 | 指令 |
| --   | --  |  --  |
| 无限 | $\infty$| ```\infty```|
| 角度 | $\circ$| ```\circ```  |

## 运算符
| 符号 | 效果 | 指令 |
| --   | --  |  --  |
| 乘号  | $\times$ | ```\times```|
| 除号  | $\div$   | ```\div```  |
| 正负号| $\pm$    | ```\pm```   |
| 根号  | $\sqrt 4$  | ```\sqrt``` |
|开多次方根 | $\sqrt[n]4$ | ```\sqrt[n]```|
| 对数     | $\log$ | ```\log```|
| 以$e$为底的对数     | $\ln$ | ```\ln```|
| 向上取整 | $\lceil x \rceil$ | ```\lceil x \rceil```|
| 向下取整 | $\lfloor x \rfloor$ | ```\lfloor x \rfloor```|
| 连续求和 | $\sum$ |```\sum``` |
| 连续求积 | $\prod$ |```\prod``` |
## 不等式
| 符号 | 效果 | 指令 |
| --   | --  |  --  |
| 大于等于 | $\geq$ | ```\geq```|
| 小于等于 | $\leq$ | ```\leq```|
| 远大于   | $\gg$  | ```\gg``` |
| 远小于   | $\ll$  | ```\ll``` |
| 不等于   | $\neq$ | ```\neq```|
| 恒等于   | $\equiv$| ```\equiv```|
| 约等于   | $\approx$| ```\approx```|

## 三角函数
| 符号 | 效果 | 指令 |
| --   | --  |  --  |
| 正弦 | $\sin$ | ```\sin```|
| 余弦 | $\cos$ | ```\cos```|
| 正切   | $\tan$  | ```\tan``` |
| 正割   | $\sec$  | ```\sec``` |
| 余割   | $\csc$ | ```\csc```|
| 反余弦   | $\arccos$ | ```\arccos```|

其他的反三角函数同理，前面加上“arc”即可。

## 微积分
| 符号 | 效果 | 指令 |
| --   | --  |  --  |
| 极限 | $\lim$ | ```\lim```|
| 趋近符号 | $\to$ | ```\to```|
| 积分 | $\int$ | ```\int```|
| 双重积分 | $\iint$ | ```\iint```|
| 三重积分 | $\iiint$ | ```\iiint```|
| 导数（点） | $\dot y$| ```\dot y```|
| 导数（撇） | $f^{'}(x)$  | ```f^{'}(x)```|
| 偏导 | $\partial$| ```\partial```|

## 箭头
### 普通箭头
| 效果 | 指令 |
| --  | --   |
|$\leftarrow$ | ```\leftarrow``` |
|$\rightarrow$| ```\rightarrow```|
|$\uparrow$   | ```\uparrow```   |
|$\downarrow$ | ```\downarrow``` |

### 向量、内容上方加箭头
| 效果 | 指令 |
| --  | --   |
|$\overrightarrow{AB}$|```\overrightarrow{AB}```|
|$\overleftarrow{AB}$|```\overleftarrow{AB}```|
|$\overleftrightarrow{AB}$|```\overleftrightarrow{AB}```|

### 箭头上下均有内容
| 效果 | 指令 |
| --  | --   |
|$\xrightarrow{b}$|```\xrightarrow{b}```|
|$\xrightarrow[a]{b}$|```\xrightarrow[a]{b}```|

## 集合
| 符号 | 效果 | 指令 |
| --   | --  |  --  |
| 真包含 | $\subset$ | ```\subset```|
| 包含 | $\subseteq$ | ```\subseteq```|
| 交 | $\cap$| ```\cap```|
| 并 | $\cup$  | ```\cup```|
| A的补集 | $\overline{A}$  | ```\overline{A}```|
| 实数集 | $\mathbb{R}$ | ```\mathbb{R}```|
| 空集 | $\emptyset$| ```\emptyset```|

<br/><br/>

# 上标符号

| 效果 | 指令 |
| --  | --   |
|$\bar x$| ```\bar x``` |
|$\hat x$| ```\hat x```|
|$\tilde x$   | ```\tilde x```   |
|$\widetilde x$ | ```\widetilde x``` |

<br/><br/>
