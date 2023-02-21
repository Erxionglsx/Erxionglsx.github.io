# Git使用

> 理解Git:Java3y https://www.jianshu.com/p/52a5a7b14aef

------



![](https://note.youdao.com/yws/api/personal/file/407E488E01E54AE9BD7FB229812E6190?method=download&shareKey=a7651e6fbc242415d2b066ffca48b743)

![](https://note.youdao.com/yws/api/personal/file/4597A77382144B95B4BDFCDB36A8EADE?method=download&shareKey=f5176a78d26817b2dc69d45c12fa1cec)

[TOC]

## 常用的Git命令：

**查看Git工作区、暂存区的变更情况**（可以知道哪些没有commit、哪些没有被Git追踪）：git status

**拉取远程最新的变更到本地**：git fetch

**切换分支**：git checkout 分支名

**将代码还原到某个版本**(包括工作目录)：git reset --hard 版本号

**查看Git的提交(commit)记录**：git log

将代码还原到某个版本后，后悔了，想重新回去，但在提交记录已经找不到了。git reset --hard 把reset 之后的 commit都给抹杀掉了。**找到最近的执行Git命令**：git reflog

还原到某个版本了，现在我为了稳健，不想再原来的分支上修改了，再**新建一个分支**吧（-b 参数把当前分支切换到了要创建的分支上）：git checkout -b 分支名

我们把上一次还是”相对稳健“的**分支合并**到我新建的分支上：git merge 分支

突然想看看现在**有多少个分支**：git branch -a

新增几个文件了，随手git add一下吧

改得差不多了，随手git commit -m 一下吧，最好还是**写好备注**，不然以后等改多了，你都不知道你改了什么啦。

改完了，**提交到远程**吧：git push

想把远程分支**最新的代码给拉下来**，然后**合并到本地**上。我们可以用git fetch和git merge 来实现，也可以通过git pull来实现。一般我用的都是git fetch+git merge ，这样会更加**可控**一些

有的时候，本地分支在master分支，然后忘了切其他的分支去修改，直接在master改了，然后也push到远程了。等你发现的时候，你会真的想骂自己。

咋办？最简单的办法其实我们还是可以git reset --hard到对应的版本，然后将其**修改或者复原**，再强制提交到master分支：git push -u origin/master -f

#### 查看用户名和邮箱

查看用户名：git config user.name

查看邮箱：git config user.email

修改用户名：git config --global user.name "用户名"

修改邮箱：git config --global user.email "邮箱"

### git reset回滚

> https://blog.csdn.net/qq_16221009/article/details/125490631
> 


| 选项  | git reset产生影响 | 索引（暂存区） | 工作目录 | 原有文件内容的变更                           | 目录结构的变更（增加或者删除文件）                           |
| ----- | ----------------- | -------------- | -------- | -------------------------------------------- | ------------------------------------------------------------ |
| soft  | 是                | 否             | 否       | 修改内容还在，变成未add的状态                | 新增文件：还存在，变成未add的状态(目录结构中文件变成绿色，可以再次执行git commit )；删除文件：目录结构中还是没有，可以直接执行git commit |
| mixed | 是                | 是             | 是       | 修改内容还在，变成未add的状态                | 新增文件： 还存在，变成未add的状态(目录结构中文件变成红色，需要执行命令git add . 再执行git commit )；删除文件：目录结构中还是没有，可以直接执行git commit |
| hard  | 是                | 是             | 是       | 修改内容丢失，修改的代码 不会变成未add的状态 | 新增文件丢失、删除的文件相当于没删                           |


#### 已commit的代码回退

> https://blog.csdn.net/yaobao888/article/details/116108731

工作中经常遇到git commit后（尚未git push操作），需要回退的情况具体方法如下：

1.执行git log找到本次commi的ID信息

比如commit id信息为：90f1ce4d73c5dc63f46fa61984a6bb878f47374

2.执行git reset --soft HEAD^操作，该命令的主要功能是重置HEAD，保留index和工作区。

对应HEAD即上述commit id信息

git reset --soft "90f1ce4d73c5dc63f46fa61984a6bb878f47374^"

#### IDEA已commit的代码回退回滚

选择要回滚到的目标提交记录

![](https://note.youdao.com/yws/api/personal/file/WEB42dd5aff5d45a5f32345ee0369aebf53?method=download&shareKey=a1b40df9279ac56c8f5ed26590b297a3)

![](https://note.youdao.com/yws/api/personal/file/WEB8da1c5c8cae74c4599c786d1326263a1?method=download&shareKey=b635cee8233087f98e0dae5ef79f7d4b)

#### 已提交代码合并分支

![](https://note.youdao.com/yws/api/personal/file/WEB7ae5fd3f57d11fdef490d1c8c9930548?method=download&shareKey=91da1928914fc15ceb43291fe84ec1dd)

#### 出现Merge remote-tracking branch

> https://blog.csdn.net/FuChenRenShen/article/details/116265581?spm=1001.2101.3001.6661.1&utm_medium=distribute.pc_relevant_t0.none-task-blog-2%7Edefault%7ECTRLIST%7ERate-1-116265581-blog-113087450.topnsimilarv1&depth_1-utm_source=distribute.pc_relevant_t0.none-task-blog-2%7Edefault%7ECTRLIST%7ERate-1-116265581-blog-113087450.topnsimilarv1&utm_relevant_index=1

出现原因：使用git pull有可能出现这样的提交。如果你创建了本地提交，执行了git commit命令，但未push，并且在他人提交到远程仓库之后运行了git pull命令，那么git 会拉取其他开发者的提交记录并且合并进你的本地分支。

**应尽量避免这种情况的发生**

#### git pull和git fetch的区别

![](https://note.youdao.com/yws/api/personal/file/WEB2a69f1d52a01d5912bbf9887152dec8c?method=download&shareKey=a26d5c62c985564e6e387144ac5bb476)

**git fetch**

在拉取代码过程中，`git fetch`会首先检查本地仓库和远程仓库的差异，检查哪些不存在于本地仓库，然后将这些变动的提交拉取到本地。

但是，这里请注意，它是把远程提交拉取到本地仓库，而不是本地工作目录，它不会自行将这些新数据合并到当前工作目录中，我们需要继续执行`git merge`才会把这些变动合并到当前工作目录。

**git pull**

`git pull`和`git fetch`刚好相反，它直接获取远程的最新提交，直接拉取并合并到本地工作目录，而且在合并过程中不会经过我们的审查，如果不仔细检查，这样很容易遇到冲突。

#### git rebase

> https://blog.csdn.net/weixin_42310154/article/details/119004977

如果你的分支不只有你在进行开发，那么git-rebase可能会导致提交记录的丢失，但是如果你是在自己的分支上进行开发，那么这无疑是一个很棒的选择。

![](https://note.youdao.com/yws/api/personal/file/WEBb2d3b2a87bd683069c413eeae37f2869?method=download&shareKey=17ed378fd51a2f26f6e68d257d2b8a33)

![](https://note.youdao.com/yws/api/personal/file/WEB5c8ef57da1d9ffbf25a2830229dd412f?method=download&shareKey=454e91f0c55a9ef452a49d7f7cf44c9e)

**理解**：张三从B拉了代码进行开发，目前提交了两次，开发到D了；李四也从B拉出来开发了并且开发完毕，他提交到了M，然后合到主干上了。此时张三想拉下最新代码，于是他在feature分支上执行了git rebase master，即把master分支给rebase过来，由于李四更早开发完并合了主干，如此就相当于张三是基于李四的最新提交M进行的开发了。

## Git连接Gitee和Github

> 相关配置：https://blog.csdn.net/weixin_36191602/article/details/80946242?utm_medium=distribute.pc_relevant.none-task-blog-BlogCommendFromBaidu-1.control&depth_1-utm_source=distribute.pc_relevant.none-task-blog-BlogCommendFromBaidu-1.control 参考config配置文件
>
> https://www.jianshu.com/p/68578d52470c 参考步骤

#### 切分支时的提醒

1. Smart checkout:当前分支未提交的代码会被带到你要切换的那个分支上去
2. Force Checkout:当前分支未提交的代码不会带到你要切换的那个分支上去,还是保留在当前分支
3. don`t checkout:就是不进行分支切换,还是留在当前分支

