# 进入gdb
```bash
gdb 可执行文件名
# 通过可执行文件跟踪
gdb attach 进程号
# 通过进程号跟踪
```
<br/><br/>

# gdb基本操作
```bash
r
# 命令全称run，开始运行程序。

h
# 命令全称help。查看gdb帮助信息。

bt
# 命令全称backtrace。查看当前已调用的各级函数及其参数，从上至下调用的各级函数会按升序编号。

f  函数编号
# 全称frame。查看调用的某级函数。

c
# 全称continue。继续运行，直到结束或遇到断点。

n
# 全称next。执行下一条语句，若有函数则直接执行完整个函数（不进入到函数内部），相当于VS Code中的step over。

s
# 全称step。执行下一条语句，若有函数则进入，相当于VS Code中的step into。

fin
# 全程finish。跳出当前函数，相当于VS Code中的step out。

p 变量名
# 全称print。用于查看变量值。

q
# 全称quit。退出gdb调试，
```
<br/><br/>


# 断点设置
## 新增普通断点
```bash
b 行号
b 文件名:行号
b 函数名
b 文件名:函数名
# 命令全称break。在对应位置打断点，以上写法均可。
```

## 新增watch断点
```bash
watch 变量名
# 当变量的值变化时，暂停运行的程序。（相当于将断点设置到每个变量值的改变处）
```
## 断点操作
```bash
i b
# 全称info break。查看已设置的断点，每个断点都会指定一个编号。

dis 断点编号
# 全称disable。使指定断点暂时失效，若要令全部断点暂时失效，则后面不接断点编号。

ena 断点编号
# 全称enable。使指定被dis的断点生效，若要令全部断点生效，则后面不接断点编号。

d 断点编号
# 全称delete。删除指定断点，若要删除全部断点，则后面不接断点编号。
```
<br/><br/>


# 参考资料
[LINUX下GDB的使用方法(简单说说)](https://blog.csdn.net/awm_kar98/article/details/82840811)
