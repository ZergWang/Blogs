# 1. 写伪代码所需要的包
一般会使用algorithm、algorithmic、algorithmicx、algorithm2e这四种包：
- algorithm：给伪代码提供一个浮动体环境，防止其换页或其他因素导致的内容中断，从而跨页显示。
- algorithmic：编辑伪代码的内容，一些for、while、if等语句通过该包中的命令进行编写。
- algorithmicx：可看作algorithmic的升级版，提供了一些自定义命令。本篇笔记聚焦于algorithmicx的一些简单命令，旨在简单快速上手LaTeX伪代码。
- algorithm2e：独立于algorithmic和algorithmicx外的一套伪代码环境，两套环境语法、排版上均不相同，本篇笔记不作涉猎。

阅读此博文前，请先熟悉LaTeX基本语法，可参考本人博文：[LaTeX语法入门](https://blog.csdn.net/Zerg_Wang/article/details/104120617)

<br/><br/>

# 2. 环境构筑
\begin{algorithm}后面跟着的“H”是指定伪代码浮动体的位置，语法规则与普通浮动体一致。
```latex
\documentclass{article}
\usepackage{algorithm}
\usepackage{algpseudocode}

\begin{document}
\begin{algorithm}[H]
    \caption{title} %标题
    \label{alg1} %标签
    \begin{algorithmic}
        % 伪代码正文
    \end{algorithmic}
\end{algorithm}
\end{document}
```
<br/><br/>

# 3. 语法规则
这里介绍较为常用的algorithmicx的语法命令（algorithmic命令与algorithmicx类似，区别是：前者的命令全为大写，后者仅首字母大写）

## 普通语句
```latex
\State <text>  
% 用于一般内容显示，并根据代码实际布局自动缩进
\Return <text> 
% 返回语句，一般配合\State一起使用
\Comment {<text>} 
% 注释内容，一般跟在\State <text>之后，注释内容自动右对齐
```

## 判断语句
```latex
\If{<condition>} <text> \EndIf
\If{<condition>} <text> \Else <text> \EndIf
```
使用\ElsIf可嵌套判断语句，举个例子：
```latex
\If{$a>b$}
    \State $max = a$
\ElsIf{$a<b$}
    \State $max = b$
\Else
    \State same
\EndIf
```
## 循环语句
```latex
\For{<condition>} <text> \EndFor

\ForAll{<condition>} <text> \EndFor

\While{<condition>} <text> \EndWhile

\Repeat <text> \Until{<condition>}

\Loop <text> \EndLoop
```
## 输入输出语句
```latex
\Require <text> % 输入
\Ensure <text>  % 输出
```
如果不希望使用Require、Ensure这种，想换成Input、Output或输入、输出这种字眼，输入输出语句不用改，只需要在导言区加入：
```latex
\renewcommand{\algorithmicrequire}{\textbf{自定义输入}}
\renewcommand{\algorithmicensure}{\textbf{自定义输出}}
```

## 函数体
```latex
\Function{<name>}{<params>} <text> \EndFunction
```
<br/><br/>

# 4. 其他
## 特殊字符
以下字符仅用于algorithmic环境中
```latex
\TRUE \FALSE \AND \OR \XOR \NOT
```
## 显示行号
```latex
\begin{algorithmic}[1]
```
“1”表示每行均显示行号，如果填“2”，意思是每2行显示一次行号。

## 标题与序号更改
当使用如下语句生成标题时：
```
\caption{title}
```
标题一行会显示为“Algorithm 1 title”。如果不想使用“Algorithm”，想换成“算法”或别的什么字眼，同样无需更改命令，在导言区加入：
```latex
\floatname{algorithm}{自定义标题}
```
若有多个伪代码，希望当前伪代码的标号为2或者其他数字，则可以在\caption命令前加：
```latex
\setcounter{algorithm}{1}
```
该语句中的数字为1，实际显示的数字永远比该数字多1（也就是2）。
<br/><br/>

# 5. 参考资料
[Latex 算法怎么写？（一）：algorithm, algorithmic算法包到底什么区别？](https://zhuanlan.zhihu.com/p/145195565)

[LaTeX/Algorithms 伪代码](http://hustsxh.is-programmer.com/posts/38801.html)