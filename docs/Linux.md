# Linux

------

[TOC]

![](https://note.youdao.com/yws/api/personal/file/WEB50750812e163dd9663df71e511b826e4?method=download&shareKey=964bd02977cab0b5d9b392367e36e44f)

![](https://note.youdao.com/yws/api/personal/file/WEB5640827a683fc57bfd8cf9dc43ab358e?method=download&shareKey=b698d5bc0a9c452c4ff905b82407a3fd)

![](https://note.youdao.com/yws/api/personal/file/WEBd7850819f8000ff1371fd2a88b749722?method=download&shareKey=de905a076d7869ad7151067d4d351bf2)

![](https://note.youdao.com/yws/api/personal/file/WEB283e71f7743dca60a362c8b8b2f6a8fa?method=download&shareKey=19ea07776e7822100d4361ffd9bed913)

![](https://note.youdao.com/yws/api/personal/file/WEB109f3a05b5ea51a9a3336243a3759189?method=download&shareKey=9c0b85a958166a7e68721809a9bfab1c)

![](https://note.youdao.com/yws/api/personal/file/WEB5436ddde43a207eab55b6f1024b841ec?method=download&shareKey=8758d7555872c5ff27eef7c4feb5046b)

![](https://note.youdao.com/yws/api/personal/file/WEB634c65f0710f47368492dab3d9aa707a?method=download&shareKey=d50c9e9febcfe0876af0b0d8d1c317eb)

![](https://note.youdao.com/yws/api/personal/file/WEB7860e7bd6844e713e9b9cef856fdbe75?method=download&shareKey=26a08cc550ae3c87f70d4ca5fc6d0af6)

![](https://note.youdao.com/yws/api/personal/file/WEB4c84e52d87f92cebe5d06aed8b36c946?method=download&shareKey=3b03bf3473fd2eb52ef3ec6a805ee46b)

![](https://note.youdao.com/yws/api/personal/file/96D31325628845CD94CADA404C42CA48?method=download&shareKey=001217721e7fb76552216d264e3a44a2)

### 权限控制

![](https://note.youdao.com/yws/api/personal/file/WEB6496abf118705ce4bd2347169de6fe07?method=download&shareKey=48d553d292d2613625596f922fab01e3)

![](https://note.youdao.com/yws/api/personal/file/WEB952c9e22e273c2c9079257ba0a128d3a?method=download&shareKey=284e079de781d62b5175bf18dc0eda86)

* r表示读权限
* w表示写权限
* x表示执行权限

### 查看文件

cat file  查看文件，全部打印

more file   查看文件，打印一页  q退出

head file  查看文件前几行

grep 222  file    在文件中搜索字符串222，将找到的行打印出来

find -name 文件名或目录名   全局搜索文件或目录

less file	查看文件，打印一页，可上下翻页

**less交互命令**

| 交互指令   | 功能                                 |
| ---------- | ------------------------------------ |
| G          | 移动至最后一行                       |
| g          | 移动到第一行                         |
| 【PgDn】键 | 向下移动一页                         |
| 【PgUp】键 | 向上移动一页                         |
| /字符串    | 向下搜索“字符串”的功能               |
| ?字符串    | 向上搜索“字符串”的功能               |
| n          | 重复*前一个搜索（与 / 成 ? 有关）    |
| N          | 反向重复前一个搜索（与 / 或 ? 有关） |
| q 或 Q     | 退出 less 命令                       |
| ZZ         | 退出 less 命令                       |

### 查看日志

如果设置的日志文件为catalina.out：

cat catalina.out   查看所有日志

cat catalina.out | grep '开始请求CMS'   查看所有日志并搜索

tail -f catalina.out  查看当前及之后产生的日志

tail -f catalina.out | grep '开始'   查看日志并搜索

tail -5000  catalina.out | grep '门店编码为'  查看含字符串 '门店编码为' 的最近5000条数据

tail -5000  catalina.out | grep -B 10 '门店编码为'  查看最近5000条数据中 '门店编码为' 的前10条数据

tail -5000  catalina.out | grep -A 10 '门店编码为'  查看最近5000条数据中 '门店编码为' 的后10条数据

查看springboot是否启动成功： tail -5000  catalina.out | grep 'startup' 

### 软链接

1.软链接，以路径的形式存在。类似于Windows操作系统中的**快捷方式**
2.软链接可以 跨文件系统 ，硬链接不可以
3.软链接可以对一个不存在的文件名进行链接
4.软链接可以对目录进行链接

语法：ln -s 参数1 参数2

* -s选项，创建软链接
* 参数1，被链接的**文件或文件夹**
* 参数2，要链接去的目的地

```
ln -s /bin/less /usr/local/bin/less 
```

### 挂载

> https://blog.csdn.net/lhwjgs123456789/article/details/127472189

Linux下，mount挂载的作用，就是将一个设备（通常是存储设备）挂接到一个已存在的目录上。访问这个目录就是访问该存储设备。类似于U盘的功能

挂载操作：https://blog.csdn.net/qq_51399192/article/details/124318863

**mount**(挂载):将设备与目录去做关联,目的是可以让用户通过目录去访问到设备中的信息

```
mkdir /mnt/usbFAT32   --/mnt 目录下创建手动进行挂载的目录
fdisk -i              --查看挂载的设备信息（需要root用户进行查看）
 	Device Boot      Start        End     Blocks    Id  System
/dev/sdb1   *          72        3767    29681664    c  W95 FAT32 (LBA)
--一般情况下系统会自动检测对应的文件系统类型，所以可以不使用 -t vfat 指定文件系统类型
mount -t vfat /dev/sdb1 /mnt/usbFAT32 
df -h --查看设备挂载情况，如下即成功挂载。
/dev/sdb1              29G   16G   13G  55% /mnt/usbFAT32
```

**umount**(卸载):将设备与目录取消关联

```
例如：
已经挂载的 /dev/sdb1              29G   16G   13G  55% /mnt/usbFAT32
umount /dev/sdb1    或者   umount /mnt/usbFAT32   都可以进行卸载
```

### 查看磁盘

df -lh  查看磁盘使用情况和挂载点

df /home 查看home目录磁盘使用情况

du -sh /*  查看的是当前目录下所有子文件与子目录的大小，将其一一列出

ls -lh  查看当前文件夹大小

ps -ef | grep java  搜索进程中有'java'字段的进程数据

kill -9 进程id   杀死某个进程

./catalina.sh start 启动tomcat

### 文件上传下载

sz 文件：下载Linux文件到Windows本机

* sz filename   下载文件filename
* sz file1 file2   下载多个文件
* sz dir/*　　　下载dir目录下所有文件

rz 文件：从本地上传文件到Linux

注意：如果机器上没有安装过 lrzsz 安装包，则无法使用rz和sz命令。可使用yum命令安装：yum install -y lrzsz

### 主机名

**hostname命令**

查看主机名：hostname

修改主机名：hostnamectl set-hostname 主机名

**域名解析**

![](https://note.youdao.com/yws/api/personal/file/WEB22dab93bf1e03b5dcf84ff36b567b13d?method=download&shareKey=54653f863b19d8fb966547ef57bca89a)

### 网络请求

**ping命令**

语法：ping [-c num] ip或主机名

```
ping baidu.com
ping 192.110.168.11
ping -c 3 baidu.com
```

**wege命令**

语法：wget [-b] url

选项：-b，后台下载

```
wget http://archive.apache.org/dist/hadoop-3.3.1.tar.gz
wget -b http://archive.apache.org/dist/hadoop-3.3.1.tar.gz
```

**curl命令**

发送网络请求或下载文件，网络请求会返回页面代码

语法：curl [-O] url

选项：-O，用于下载使用

```
curl cip.cc
curl baidu.com
curl -O http://archive.apache.org/dist/hadoop-3.3.1.tar.gz
```

### 端口

计算机程序之间的通讯，通过IP只能锁定计算机，但是无法锁定具体的程序。

通过端口可以锁定计算机上具体的程序，确保程序之间进行沟通。

IP只能确定计算机，通过端口才能锁定要交互的程序。

IP地址相当于小区地址，在小区里有许多住户(程序)，而门牌号(端口)就是各个住户的联系地址。

Linux系统是一个超大号小区，可以支持65535个端口，这6万多个端口分为3类进行使用：

* 公认端口: 1~1023，通常用于一些系统内置或知名程序的预留使用，如SSH服务的22端口， HTTPS服务的443端口
  非特殊需要，不要占用这个范围的端口
* 注册端口: 1024~49151，通常可以随意使用，用于松散的绑定一些程序\服务（用户自定义）
* 动态端口: 49152~65535，通常不会固定绑定程序，而是当程序对外进行网络链接时，用于临时使用。（多用于出口）

**查看端口占用**

**nmap命令**，安装nmap：yum -y install nmap

查看该IP的对外暴露端口

语法：nmap 被查看的IP地址

```
nmap 127.0.0.1
```

**netstat命令**，安装netstat：yum -y install net-tools

查看该端口号的占用情况

语法：netstat -anp | grep 端口号

```
netstat -anp | grep 6000
```

![](https://note.youdao.com/yws/api/personal/file/WEB9693382ae5498ea7b0bec9cbd50bd286?method=download&shareKey=ae81fd632fb162c05135bce4f38e0b1c)

当前系统9090端口被程序(进程号7174)占用了

其中，0.0.0.0:9090，表示端口绑定在0.0.0.0这个IP地址上，表示允许外部访问

netstat -nlp | grep 8102  查看网络端口

| 参数 | 解释                                     |
| ---- | ---------------------------------------- |
| -a   | 显示所有连线中的Socket                   |
| -p   | 显示正在使用Socket的程序识别码和程序名称 |
| -u   | 显示UDP传输协议的连线状况                |
| -i   | 显示网络界面信息表单                     |
| -n   | 直接使用IP地址，不通过域名服务器         |
| -l   | 仅列出有在 Listen (监听) 的服务状态      |
| -t   | (tcp)仅显示tcp相关选项                   |

### 查看内存占用

**free命令**

free命令显示系统使用和空闲的内存情况，包括物理内存、交互区内存(swap)和内核缓冲区内存。

可以采用free -m和free -g命令查看，分别表示MB和GB

**top命令**

top 命令查看系统的实时负载， 包括进程、CPU负载、内存使用等等

### 技巧快捷键

Tab键 补全命令

**查看历史命令**

语法：history  会列出输入过的命令

```
history | grep ls  查询输入过的以ls开头的命令
```

语法：!命令前缀 自动执行上一次匹配前缀的命令

```
!tai  执行最近一次以tai开头的命令
```

语法：ctrl+r  输入内容去匹配历史命令，回车键可以直接执行，键盘左右键，可以得到此命令(不执行)

**光标移动快捷键(命令)**

* ctrl+a，跳到命令开头
* ctrl+e，跳到命令结尾
* ctrl+键盘左键，向左跳一个单词
* ctrl+键盘右键，向右跳一个单词

**ctrl+c强制停止**

* linux某些程序的运行，如果想要强制停止它，可以使用快捷键ctrl+c
* 命令输入错误，也可以通过快捷键ctrl+c，退出当前输入，重新输入

**ctrl+d退出或登出**

* 可以通过快捷键：ctrl+d，退出账户的登录
* 或者退出默写特定程序的专属页面

### systemctl命令

语法：systemctl start | stop | status | restart | reload | enable | disable 服务名

* start  启动服务
* stop  关闭服务
* status  查看服务状态
* restart  重启服务
* reload  重新加载配置
* enable  开启开机自启
* disable  关闭开机自启

系统内置服务：

* firewalld，防火墙服务
* sshd，ssh服务

```
#重启命令
systemctl restart tomcat-tkben-api
systemctl restart tomcat-opent-data
systemctl restart tomcat-opent-api
```

tomcat-tkben-api.service

```
[Unit]
Description=Tomcat Cluster for Learning Cell API Server No. 0
After=syslog.target network.target nfs-client.target

[Service]
Type=forking

Environment=CATALINA_HOME=/opt/tomcat8
Environment=CATALINA_BASE=/home/tomcat/tkben-api
WorkingDirectory=/home/tomcat/tkben-api

LimitNOFILE=65535
LimitNPROC=65535

ExecStart=/opt/tomcat8/bin/startup.sh
ExecStop=/opt/tomcat8/bin/shutdown.sh
SuccessExitStatus=143

User=tomcat
Group=tomcat

[Install]
WantedBy=multi-user.target
```

重启tomcat项目服务：systemctl restart tomcat-tkben-api

### 服务器

**linux超时自动退出**

vim /etc/profile

```
export TMOUT=1800  //超过1800秒无操作，当前用户自动退出登录
```

source /etc/profile

**linux超时断开ssh连接**

vi /etc/ssh/sshd_config

```
# 60表示每分钟发送一次, 然后客户端响应, 这样就保持长连接了
ClientAliveInterval 
# 表示服务器发出请求后客户端没有响应的次数达到一定值, 就自动断开
ClientAliveCountMax 3
```

systemctl restart sshd



