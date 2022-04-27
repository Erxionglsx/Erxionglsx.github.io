# Git使用

> 理解Git:Java3y https://www.jianshu.com/p/52a5a7b14aef

------



![](https://note.youdao.com/yws/api/personal/file/407E488E01E54AE9BD7FB229812E6190?method=download&shareKey=a7651e6fbc242415d2b066ffca48b743)

![](https://note.youdao.com/yws/api/personal/file/4597A77382144B95B4BDFCDB36A8EADE?method=download&shareKey=f5176a78d26817b2dc69d45c12fa1cec)

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

## Git连接Gitee和Github

> 相关配置：https://blog.csdn.net/weixin_36191602/article/details/80946242?utm_medium=distribute.pc_relevant.none-task-blog-BlogCommendFromBaidu-1.control&depth_1-utm_source=distribute.pc_relevant.none-task-blog-BlogCommendFromBaidu-1.control 参考config配置文件
>
> https://www.jianshu.com/p/68578d52470c 参考步骤

------

Git使用Gitee提交：ssh -T [git@gitee.com](http://mailto:git@gitee.com)

Git使用GitHub提交：ssh -T [git@github.com](http://mailto:git@github.com)

## Gitee协同开发：

**IDEA**中提交项目代码到Gitee，协同开发：

第一次打开setting，搜索git，输入本地git.exe地址。

创建Gitee仓库，复制仓库地址，打开Get from Version Control,输入地址。

项目中添加代码后，提交前先pull一下↓↓↓，点击VCS-Commit 提交到暂存区，然后点击VCS-Git-Push,提交代码到Gitee远程仓库。

可点击VCS-Git-Pull,来查看是否会与其他人有代码冲突，若出现冲突，保存自己修改的代码后，Rollback进行回滚，复制别人的代码过来，然后再Pull一遍查看是否冲突。无冲突后，将自己修改的代码粘贴回来，VCS-Commit-Commit and Push。

## eclipse中解决代码冲突：

![](https://note.youdao.com/yws/api/personal/file/EA89A1AC93DC4FE79158AB2BCE765F8E?method=download&shareKey=d33ccb6c22b84de8cf6ab05609446d65)

然后重新提交