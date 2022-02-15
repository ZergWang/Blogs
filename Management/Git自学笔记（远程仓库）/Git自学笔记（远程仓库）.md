# 使用Github作为远程仓库
例如，本地有一个仓库，暂且设其名字为“Documents”，现在想将该仓库推送到Github上，具体步骤为：

#### 设置SSH密钥
在当前用户主目录下（即C:/Users/用户名/，若是Linux系统则在/home/用户名）下查看是否有.ssh目录，若没有说明在本机上还未创建SSH密钥，创建方法：（以cmd命令为例）
```bash
ssh-keygen -t rsa -C "zergwang@example.com"
```
引号内填入自己的邮箱名。创建时若无过高的保密需要，可以不用设置密码。

创建完毕，在主目录下会有一个.ssh的目录，存储着本机的私钥和公钥。

之后打开Github，在设置中添加公钥：
![](Git自学笔记（远程仓库）_1.png)
title随便写，key项就将.ssh目录下id_rsa.pub打开，内容复制进去即可。

#### 在Github创建对应仓库并与本地关联
本地要推送的仓库为“Documents”，则可以在Github中创建对应的一个仓库（名字一不一样其实无所谓）

之后，在本地仓库的工作目录中：
```bash
git remote add origin https://github.com/用户名/Documents.git
```
其中，后面的网址要对应为自己的Github账号以及对应的远程库。假如远程库名为“Documents”，则地址如同以上示例代码，为“Documents.git”。
#### 推送
实际上是把当前分支master推送到远程。推送会将本地所有未推送过的commit一并推送。
```bash
git push origin master
```
如果是第一次推送，可加上“-u”参数，用于将本地的master分支和远程的master分支关联起来，在以后的推送或者拉取时就可以简化命令。
```bash
git push -u origin master
```
在推送时，除了master，也可以推送其他分支：
```bash
git push origin 分支名
```
如果远程的版本与当前要推送的版本不符，则可以通过以下命令覆盖掉远程的版本：
```bash
git push --force origin 分支名
```
#### 远程仓库克隆
将远程仓库上的内容复制到本地：
```bash
git clone git@github.com:用户名/XXXX.git
```
该命令使用ssh协议，注意后面要使用自己的Github账户名和仓库名。

当然，也可以使用https协议：
```bash
git clone https://github.com/ZergWang/XXXX.git
```
如果要clone指定分支：
```bash
git clone -b 分支名 https://github.com/ZergWang/XXXX.git
```
更新远程仓库到本地：
```bash
git pull origin 远程仓库分支名
```
<br/><br/>

# 其他操作
#### 查看远程库信息
```bash
git remote -v
```
可以看到返回两条信息：
```bash
origin  https://github.com/用户名/XXXX.git (fetch)
origin  https://github.com/用户名/XXXX.git (push)
```
以上信息说明了远程仓库的克隆地址以及推送地址，若无推送权限，则无第二行信息。

#### 标签推送
推送某个标签到远程：
```bash
git push origin 标签名
```
或者将所有未推送标签推送出去：
```bash
git push origin --tags
```
删除远程仓库中的标签：（先在本地删除，再同步远程仓库的）
```bash
git tag -d 标签名
git push origin :refs/tags/标签名
```
#### 推送冲突解决
对于同一仓库的同一分支，在多人协作时会由于多人推送的版本冲突而导致当前的推送失败。对此，需要先将他人推送后的版本抓取下来，在本地解决冲突后再推送：
```bash
git pull
```
当然，在pull之前，先要建立当前分支与远程分支的连接：
```bash
git branch --set-upstream-to=origin/远程分支名 本地分支名
```
一般这两个分支名是一样的。
<br/><br/>

# 参考资料
[Git Community Book 中文版 -- rebase](http://gitbook.liuhui998.com/4_2.html)

[廖雪峰的官方网站 -- 添加远程库](https://www.liaoxuefeng.com/wiki/896043488029600/898732864121440)

