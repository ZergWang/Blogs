# 引入

本篇博文介绍字符串匹配算法中最快的算法之一：Sunday算法。该算法比KMP算法、Boyer-Moore算法还快。

Sunday算法由Daniel M Sunday在1990年提出。论文原文：[A VERV FAST SU6STRINC SEARCH ALGORITHM](https://dl.acm.org/doi/pdf/10.1145/79173.79184)

在论文中，作者提出了三个不同的算法：Quick Search算法、Maximal Shift算法以及Optimal Mismatch算法。作者证明了这三种算法效率均优于Boyer-Moore算法。其中，Optimal Mismatch略微好于前两者。但在原理及应用上，Quick Search最为简单，本文将着重介绍该算法。

在本篇博文中，字符串s为目标串（主串、被匹配串），字符串c被称为模式串（子串、匹配串）。

<br/><br/>

# Quick Search算法

Quick Search可以被视为一种简化的Boyer-Moore算法。该算法仅使用Boyer-Moore中的“坏字符”规则。

在下面的描述中，字符串s为目标串（主串、被匹配串），字符串c被称为模式串（子串、匹配串）。
#### 算法步骤

首先需要进行预处理，基于字符串c构造一张表，

#### 代码


# 参考资料
[EXACT STRING MATCHING ALGORITHMS](http://www-igm.univ-mlv.fr/~lecroq/string/index.html)

[Quick Search algorithm](http://www-igm.univ-mlv.fr/~lecroq/string/node19.html#SECTION00190)

[A VERV FAST SU6STRINC SEARCH ALGORITHM](https://dl.acm.org/doi/pdf/10.1145/79173.79184)