# 系统自带python
一般Ubuntu自带python2.7和python3.5，有些人想着可能用不到，毕竟后面可能会装Anaconda或者别的什么python版本，但自带的python可能是很多系统包的依赖项，卸载了可能会导致系统崩溃……
<br/><br/>
 

# Anaconda下python的启动
装完了Anaconda后，在控制台启动python：

**python**：使用的Anaconda中的python（版本为3.7或更新）

**python2**：使用的是系统自带的python2.7

**python3**：使用的是系统自带的python3.5

同理可知pip、pip2、pip3分别对应的版本。

注意：sudo pip和pip并不一样，加了sudo之后，调用的为系统自带的python对应的pip，同理，如果系统中装有anaconda，直接conda没问题，但sudo conda会显示命令不存在，解决以上问题：
编辑~/.bashrc，在该文件最后一行加入：
```bash
alias sudo="sudo env PATH=$PATH"
```
然后在控制台刷新：
```bash
source ~/.bashrc
```
之后，无论加不加sudo，pip、conda都是执行同一个版本的命令。

然而，对于Anaconda中虚拟环境的pip，可能会受到上面操作的影响，也就是说，即使activate了虚拟环境，执行的pip仍然是无虚拟环境下的pip。要解决这个问题，同样需要在以上文件中的最后加：
```bash
alias pip=/home/zergwang/softwares/anaconda3/envs/faa/bin/pip
```
通过以上命令指定执行的pip为哪个版本的，注意地址是要定位到bin目录下的pip，最后同样source ~/.bashrc刷新。
<br/><br/>
 
# pip无权限：
要么加sudo，要么：
```bash
pip install package --user
```
<br/><br/>

# 卡死在登录界面
开机后发现登录界面分辨率降低，且在输入密码后又重新弹回登录界面，出现以上情况可能是因为显卡驱动的问题，或者是因为内核自动更新后显卡驱动不兼容，需要重新安装。解决方法：

Ctrl+Alt+F1进入tty1模式，正常登录。

然后卸载相关驱动：
```bash
sudo apt-get remove nvidia-*
sudo apt-get autoremove
sudo nvidia-uninstall
```
然后重启，发现上述问题解决，但图形界面分辨率依然很低，继续：

Ctrl+Alt+F1进入tty1模式，正常登录后，安装nvidia驱动：
```bash
sudo service lightdm stop
sudo bash ./NVIDIA-Linux-x86_64-440.31.run -no-x-check
```
最后一步：
```bash
sudo service lightdm restart
```
为了防止以上情况反复出现，可以选择关闭系统内核的自动更新。
<br/><br/>
 

# Windows+Ubuntu双系统时间不同步
UTC（Coordinated Universal Time，协调世界时）时间，该时间基于GMT（Greenwich Mean Time，格林威治标准时间）得来，可以看做是英国格林威治当地时间。

Windows将BIOS时间作为Windows的系统时间，但Ubuntu认为BIOS时间是UTC时间，Ubuntu系统显示的时间要在这个UTC上加减当前所在时区。例如北京在东八区，北京时间即UTC+8，所以Ubuntu显示的时间会与Windows显示的时间有8个小时之差。

解决办法：要么让Windows采用UTC，要么让Ubuntu将BIOS时间作为系统时间。后者只需一条命令：
```bash
timedatectl set-local-rtc 1 --adjust-system-clock
```
然后重启。
<br/><br/> 

# 解压rar时：parsing filters is unsupported
没有安装相应的rar文件解压工具（Ubuntu默认只支持zip文件的解压），安装rar解压工具：
```bash
sudo apt-get install unrar
```