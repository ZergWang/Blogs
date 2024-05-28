# LaTeX 语法简介
LaTeX简介及环境配置，可以参考本人博客：[LaTeX简介及环境配置](https://blog.csdn.net/Zerg_Wang/article/details/104213500)

## 基本命令
TeX语言主要可以分成以下三种：有控制格式与排版效果的控制命令、内容文本，以及注释。

### 控制命令
控制命令以反斜杠“\”开头，以第一个空格或非字母字符结束。注意，控制命令区分大小写。

```latex
\documentclass{article}
```
以以上代码为例，控制命令为“\documentclass”，后面大括号内为该命令的必需参数，若是中括号”[]”，则为可选参数。

### 注释与转义符
TeX中“%”为注释标记，该行“%”之后的内容都会被当作注释。

若要输出“%”本身，需要在前面加上转义符“\”，一些特殊字符，如“\”、“&”等也无法直接输出，都需要前面加上转义符。


<br/><br/>


## 语法框架
```latex
\documentclass{article}
%导言区
\begin{document}
Hello, world!
\end{document}
```
以上代码展示了一个几乎最简单的TeX文档，其中，\documentclass{article}定义了一个article类型的文档类。文档类是TeX预设（或是用户自定的）一些格式的集合。用C++类比，该命令类似于让当前文档继承class article然后自己再作更改。


\documentclass到\begin之间的区域叫作导言区，用于对整篇文档的格式进行设置，如页面大小、页眉页脚样式、章节标题样式等等。在这个区域

\begin到\end之间为环境区，该区域中的内容才会被处理到最终的文档之中，\begin和\end要有一致的必需参数，该参数又称环境名，上述例子
中，环境名为document。




# 参考资料
[使用VSCode编写LaTeX](https://zhuanlan.zhihu.com/p/38178015)

[一份其实很短的 LaTeX 入门文档](https://liam.page/2014/09/08/latex-introduction/)