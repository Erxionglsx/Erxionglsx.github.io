# SVN

------

[TOC]

## 1.SVN是什么？

* 代码版本管理工具
* 它能记住你每次的修改
* 查看所有的修改记录
* 恢复到任何历史版本
* 恢复已经删除的文件

2.SVN和Git相比，有什么优势？

* 使用简单，上手快
* 目录级权限控制，企业安全必备
* 子目录Checkout，减少不必要的文件检出

3.主要应用：

* 开发人员用来做代码的版本管理
* 用来存储一些重要的文件，比如合同
* 公司内部文件共享，并且能按目录划分权限

4.SVN仓库

推荐：svnbucket.com，SVN全家桶

5.安装SVN客户端

TortoiseSVN

Cornstone

## 2.基本操作

在https://svnbucket.com创建项目，添加项目简介

![](https://img.tool22.com/image/5f7310e61dc66.jpg)

复制项目地址

![](https://img.tool22.com/image/5f731005a6bd4.jpg)

右键检出，粘贴项目地址

![](https://img.tool22.com/image/5f731006bff29.jpg)

输入SVN用户名和密码

![](https://img.tool22.com/image/5f73100614ee2.jpg)

绿色正常的，修改后为红色，需要更新

![](https://img.tool22.com/image/5f7310061ea9c.jpg)

可选择SVN提交和更新，显示日志等

![](https://img.tool22.com/image/5f7310092be6d.jpg)

选择要提交的文件，填写说明

![](https://img.tool22.com/image/5f7310079f0d8.jpg)

查看提交记录

![](https://img.tool22.com/image/5f7310089e2bf.jpg)

## 3.撤销修改或者恢复到某一版本

撤销本次修改

![](https://note.youdao.com/yws/api/personal/file/C345EBD346784CA1ADFF89ACA862CFAC?method=download&shareKey=f21bc7599bfd1e73ed3202231152e513)

提交中撤销本次修改

![](https://note.youdao.com/yws/api/personal/file/1A1806C6C2954FEDB35164E43549C2D7?method=download&shareKey=9206592fef433a0f6b32af77c920c6b2)

撤销已经提交的修改

![](https://note.youdao.com/yws/api/personal/file/B23773EEFB5843D98F029F6F6BA8EE2D?method=download&shareKey=2822d2665d3e236f56cc920106270b31)

恢复到之前的某一版本，需重新提交

![](https://note.youdao.com/yws/api/personal/file/7593E87FF3AA481CA60A77F31DF66B05?method=download&shareKey=4b0b688695fb744385a1315b8e9ea628)

## 4.忽略文件或文件夹

忽略文件夹，该文件夹不进行提交(若已提交过，则删除)

![](https://note.youdao.com/yws/api/personal/file/C7C136AE12D44597B23B121CC0C45F7F?method=download&shareKey=6354a69028a39dfb8b7cb8b914708fc5)

先更新，后提交

![](https://note.youdao.com/yws/api/personal/file/C1C132FDE804482D9CC77F451A8C7EE1?method=download&shareKey=a860447010e6afabf258cd009847ca69)

忽略文件，不进行提交

![](https://note.youdao.com/yws/api/personal/file/AF296D4F7CCD4EEAA9D7E3FA449560AA?method=download&shareKey=bc5d1b76387ea72e3976ab013bab9ad4)

取消忽略，重新提交

![](https://note.youdao.com/yws/api/personal/file/FBFC19D053D146E7A5E65FDDB3F33D20?method=download&shareKey=5a16536ea92f0dec17b47c94ff3f1181)

什么情况容易发生冲突？

* 多个人修改了同个文件的同一行
* 无法进行合并的二进制文件

如何避免冲突？

* 经常update同步下他人的代码
* 二进制文件不要多个人同时操作

## 5.发生提交冲突

![](https://note.youdao.com/yws/api/personal/file/18C8A689E54145B38D2420DD19FB0DB2?method=download&shareKey=c09837805c14a92f9d4e240ce640dc48)

编辑冲突，可以查看冲突

![](https://note.youdao.com/yws/api/personal/file/F4994F7DB8DD404097287DE67800CAF4?method=download&shareKey=07499dd88bb16befa7be6e4c07e0a20d)

显示冲突代码

![](https://note.youdao.com/yws/api/personal/file/948F3CC8EBFB4AD6A2AD757FF9BC7DEE?method=download&shareKey=ba2ed43731bbd9dddbb7511b2a311b83)

右键选择要使用的代码块

![](https://note.youdao.com/yws/api/personal/file/7A1F66906830479A971EA85BF996F609?method=download&shareKey=413aa17335f4f43256c10e863a61349b)

替换后的代码

![](https://note.youdao.com/yws/api/personal/file/105D74FE13324306A5AF84953E5B2893?method=download&shareKey=0546e5695c4b7d59d9af58afb1715308)

## 6.分支

什么时候需要开分支？

* 隔离线上版本和开发版本
* 大功能开发，不想影响到其他人，自己独立开个分支去开发

创建分支

![](https://note.youdao.com/yws/api/personal/file/2FC75CB0A1DC449DA7F9FE9418DCB33B?method=download&shareKey=ca8fdb9bf33e326c35f5c4cd1dde6fd8)

选择创建分支的路径和名称

![](https://note.youdao.com/yws/api/personal/file/291A8ED69B3F48409CE99E3EAB01B025?method=download&shareKey=8262cc2226acc82734abc7c6b6ff0758)

将分支放到外边的目录

![](https://note.youdao.com/yws/api/personal/file/96FC34774A0443F893BBB0B70952A5A9?method=download&shareKey=2ea366ec95c523dd0257c7ea224b383b)

将分支检出

![](https://note.youdao.com/yws/api/personal/file/8BB383DC7A4B4811A257086A4382C1D3?method=download&shareKey=f429057df39f207094b3cb3869c8baf3)

分支版本与主干版本

![](https://note.youdao.com/yws/api/personal/file/EC23CE193F624141A96CE7BDE9C932C5?method=download&shareKey=fcde43d8168f799985a4eda4fc4c24b5)

分支版本修改后，版本进行合并

![](https://note.youdao.com/yws/api/personal/file/2E3A585E174349A2A533EE851F91B664?method=download&shareKey=d7e64f93ab50abd8f6c532fc1bac6e6c)

选择目标版本

![](https://note.youdao.com/yws/api/personal/file/8E77129C612F43C4845FE4AA05F0C422?method=download&shareKey=3b51116a67f60b592bf09ef9749b1af2)

