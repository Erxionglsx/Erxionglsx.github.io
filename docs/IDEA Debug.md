## IDEA Debug

> https://www.bilibili.com/video/BV1ur4y1P7SV?spm_id_from=333.788.b_636f6d6d656e74.7

------

> Debug按钮解释：https://www.cnblogs.com/zsty/p/9950722.html

![](https://note.youdao.com/yws/api/personal/file/92186892982141B9BF68879677FE79B7?method=download&shareKey=9df2d7c42501a07582460568f703b2d8)

* Show Execution Point (Alt + F10)：如果你的光标在其它行或其它页面，点击这个按钮可跳转到当前代码执行的行。
* Step Over (F8)：步过，一行一行地往下走，如果这一行上有方法不会进入方法。
* Step Into (F7)：步入，如果当前行有方法，可以进入方法内部，一般用于进入自定义方法内，不会进入官方类库的方法，如第25行的put方法。
* Force Step Into (Alt + Shift + F7)：强制步入，能进入任何方法，查看底层源码的时候可以用这个进入官方类库的方法。
* Step Out (Shift + F8)：步出，从步入的方法内退出到方法调用处，此时方法已执行完毕，只是还没有完成赋值。
* Drop Frame (默认无)：回退断点，后面章节详细说明。
* Run to Cursor (Alt + F9)：运行到光标处，你可以将光标定位到你需要查看的那一行，然后使用这个功能，代码会运行至光标行，而不需要打断点。
* Evaluate Expression (Alt + F8)：计算表达式，后面章节详细说明。

![](https://note.youdao.com/yws/api/personal/file/69F7BF4019A644CEA43DBDA0B31BD18F?method=download&shareKey=86f57f4bd7fff81c918f2d5c1b88faaa)

* Rerun 'xxxx'：重新运行程序，会关闭服务后重新启动程序。
* Update 'tech' application (Ctrl + F5)：更新程序，一般在你的代码有改动后可执行这个功能。而这个功能对应的操作则是在服务配置里，如图2.3。
* Resume Program (F9)：恢复程序，比如，你在第20行和25行有两个断点，当前运行至第20行，按F9，则运行到下一个断点(即第25行)，再按F9，则运行完整个流程，因为后面已经没有断点了。
* Pause Program：暂停程序，启用Debug。目前没发现具体用法。
* Stop 'xxx' (Ctrl + F2)：连续按两下，关闭程序。有时候你会发现关闭服务再启动时，报端口被占用，这是因为没完全关闭服务的原因，你就需要查杀所有JVM进程了。
* View Breakpoints (Ctrl + Shift + F8)：查看所有断点，后面章节会涉及到。
* Mute Breakpoints：哑的断点，选择这个后，所有断点变为灰色，断点失效，按F9则可以直接运行完程序。再次点击，断点变为红色，有效。如果只想使某一个断点失效，可以在断点上右键取消Enabled，如图2.4，则该行断点失效。

#### 行断点

![](https://note.youdao.com/yws/api/personal/file/WEB8129d94a5e9ccb4366abc8408e76dc0c?method=download&shareKey=aeffce7b9d7475d25c47bd72005a9181)

**全部使用Debug运行**

![](https://note.youdao.com/yws/api/personal/file/WEB134886db7511112ffd34423698b47052?method=download&shareKey=3952ce4305c19803518fa4bb5f5d5f06)

#### 详细断点

![](https://note.youdao.com/yws/api/personal/file/WEBccd67eb0fb9568fff03fe1b48684c390?method=download&shareKey=a6a46f12c11ce5b9c65adc0fa3495430)

#### 方法断点

![](https://note.youdao.com/yws/api/personal/file/WEBd8070ee93ad406a9cba7e8fbba831988?method=download&shareKey=4198eeb9f08495be5460ab2d0baa5609)

![](https://note.youdao.com/yws/api/personal/file/WEB80c0bdce4ce0ee424353b3893b0d1b35?method=download&shareKey=c2d185cee077750ffabf26b8f1688bcf)

#### 异常断点

![](https://note.youdao.com/yws/api/personal/file/WEBe22b6723ecb23eeb45e59ece6ad05dd6?method=download&shareKey=6eafee9f557a8317fdbada04a0f8d60c)

![](https://note.youdao.com/yws/api/personal/file/WEBcf4a7935bc30b1794521cf8c4bc72fd4?method=download&shareKey=d3e17903cf1d09703be863d54f6aad28)

![](https://note.youdao.com/yws/api/personal/file/WEBc9fc2a4278f8f56b41d09f93242a6846?method=download&shareKey=10e9188c1a70464a33839184ebbcad9f)

#### 字段断点

![](https://note.youdao.com/yws/api/personal/file/WEB3dc1b2c38d03ed6579c3a5503cc81dab?method=download&shareKey=b5466aba2ea301b6c4bc7981b548721a)

![](https://note.youdao.com/yws/api/personal/file/WEB19c9521659c0f2dbdcef10fe129e735d?method=download&shareKey=1655e497835ccdcf22d9b0b512ed0542)

![](https://note.youdao.com/yws/api/personal/file/WEB0ec2e9da8c52157f326a682ad03e01a6?method=download&shareKey=e81cff1bf25881e6ab96deae6700e248)

#### 切分支代码冲突处理

![](https://note.youdao.com/yws/api/personal/file/286ECDFF72604B7A8BCCF65E4C5A9DB4?method=download&shareKey=077342410a015b8dc654fae6d1cf9982)

smart checkout就会把冲突的这部分内容带到目的分支（如果你没有点进窗口的那些文件处理冲突的话）

force checkout就不会把冲突的这部分内容带到目的分支,但是你在当前分支修改的所有内容就会被删除,就算你再切回来也找不到了,所以需要慎重

don`t checkout 当然是不切分支,继续留在当前分支了