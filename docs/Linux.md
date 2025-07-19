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

 **查看Linux系统版本**

```
cat /etc/os-release
```

### 权限控制

![](https://note.youdao.com/yws/api/personal/file/WEB6496abf118705ce4bd2347169de6fe07?method=download&shareKey=48d553d292d2613625596f922fab01e3)

![](https://note.youdao.com/yws/api/personal/file/WEB952c9e22e273c2c9079257ba0a128d3a?method=download&shareKey=284e079de781d62b5175bf18dc0eda86)

* r表示读权限
* w表示写权限
* x表示执行权限

**八进制数字表示权限**

|            | r    | w    | x    |
| ---------- | ---- | ---- | ---- |
| 二进制代表 | 100  | 010  | 001  |
| 十进制代表 | 4    | 2    | 1    |

例：

rw-的值为4+2=6

rwxr-xr-x的值为755

#### 更改用户权限

```java
//修改文件夹权限，7 可读可写可操作，6 可读可写，5可读可操作
chmod 755 /tomcat
chomd 777 /resource
//递归地更改目录及其所有子目录和文件的权限
chomd -R 777 /resource
chmod -R 777 /mnt/nfs/lcellstorage
```

#### 更改文件或目录的用户和用户组

```java
//更改文件的所有者
chown mysql /mysql
//更改文件的所有者和用户组
chown mysql:mysql /mysql
//递归地更改目录及其所有子文件和子目录的所有者
chown -R mysql /mysql
```

**查看账号信息**

```java
//用户帐户的基本信息
cat /etc/passwd
//用户组的基本信息
cat /etc/group
```

### 查看文件

cat file  查看文件，全部打印

more file   查看文件，打印一页  q退出

head file  查看文件前几行

grep 222  file    在文件中搜索字符串222，将找到的行打印出来

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

### 移动文件

mv newFiles/* audio/  移动一个文件夹下的所有内容到另一个文件夹下面

mv /home/packageA  /home/packageB/  移动一个文件夹到另一个文件夹下面

mv /home/packageA  /home/packageB  移动一个文件夹到另一个文件夹下面

### SCP复制数据

```sql
本地文件复制到远程服务器
-- 格式：scp 本地文件路径 用户名@远程IP:远程路径
scp /local/path/file.txt user@192.168.1.1:/remote/path/
-- 示例：将本地 /data/documents/report.pdf 复制到远程服务器 /var/www/files/ 目录：
scp /data/documents/report.pdf admin@10.0.0.5:/var/www/files/

远程文件复制到本地
-- 格式：scp 用户名@远程IP:远程文件路径 本地路径
scp user@192.168.1.1:/remote/path/file.txt /local/path/

远程目录文件复制到本地
-- 示例：从远程服务器下载 /mnt/vdata/nfs/etc/ 到本地 /mnt/nfs/lcellstorage/ 目录：
scp -r  user@172.24.226.93:/mnt/vdata/nfs/etc/ /mnt/nfs/lcellstorage/
```

### 查找文件

find 目录路径 [选项参数] [搜索条件]

```sql
-- 查找/home 目录下所有以 .txt 扩展名结尾的文件
find /home -name "*.txt"
-- 查找当前目录及其子目录中查找所有以 .txt 结尾的文本文件
find . -name "*.txt"
-- 全局查找名称为nginx的文件
find / -name nginx #查找nginx相关目录和文件
-- 查询当前文件夹下路径最长的20条路径
find . -type f -o -type d | awk '{print length, $0}' | sort -nr | head -20 | cut -d" " -f2-
```

### 压缩解压

zip -r lcell-web.zip lcell-web/  压缩文件夹

unzip input_filename.zip   解压文件夹

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

查询’Could not fetch‘在日志中最早出现时间：grep 'Could not fetch' catalina.out | awk '{print $1 " " $2; exit}'

### 清除日志文件内容

ll > /home/tomcat/tkben-api/logs/catalina.out

### 软链接

1.软链接，以路径的形式存在。类似于Windows操作系统中的**快捷方式**
2.软链接可以 跨文件系统 ，硬链接不可以
3.软链接可以对一个不存在的文件名进行链接
4.软链接可以对目录进行链接

语法：ln -s 参数1 参数2

* -s选项，创建软链接
* 参数1，被链接的**文件或文件夹**
* 参数2，要链接去的目的地

```java
ln -s /bin/less /gaojj/tomcat/bin/less 
// ln -s <目标文件或目录> <软连接名>
//不需要提前创建less文件夹
```

### 挂载

> https://blog.csdn.net/lhwjgs123456789/article/details/127472189

Linux下，mount挂载的作用，就是将一个设备（通常是存储设备）挂接到一个已存在的目录上。访问这个目录就是访问该存储设备。类似于windows中U盘的功能，但U盘会自动挂载。

许多的安装包都在/dev下，虽然所有的包都有但是还没有与Linux系统建立联系，如果想要知道这些安装包就需要挂载到文件mnt 或者 media 里面。

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

mount 192.168.12.23:/mnt/vdata/nfs/etc /mnt/nfs/lcellstorage
```

**umount**(卸载):将设备与目录取消关联

```
例如：
已经挂载的 /dev/sdb1              29G   16G   13G  55% /mnt/usbFAT32
umount /dev/sdb1    或者   umount /mnt/usbFAT32   都可以进行卸载
umount -f /dev/sdb1    或者   umount -f /mnt/usbFAT32   强制卸载
umount /mnt/nfs/lcellstorage
```

mount挂载失败，检查网关是否正确 ip a

#### /etc/fstab文件

Linux系统都是各磁盘或者分区是通过挂载的方式访问的，临时使用的U盘、光盘等我们可以使用mount命令临时挂载，如果是系统固定的磁盘则需要我们将磁盘挂载写入/etc/fstab文件，实现开机自动挂载。我们可以通过查看/etc/fastab文件了解磁盘挂载信息，也可以通过编辑该文件更新、修改磁盘挂载信息。

![](https://img-blog.csdnimg.cn/71b7e3592c804e628f3a2a57ac339c36.png)

**配置文件检查**

我们可以使用命令mount -a命令检查配置文件是否有错误

#### 挂载硬盘

```java
//使用fdisk -l命令查看当前系统的所有存储设备信息，确认硬盘已正确连接到系统并被识别
fdisk -l
//创建挂载点
mkdir /mnt/mydisk
//挂载硬盘
mount /dev/sda1 /mnt/mydisk
//查看挂载情况
df -h
```

### 查看磁盘

df -lh  查看磁盘使用情况和挂载点

df -h 查看挂载，显示更全

df /home 查看home目录磁盘使用情况

du -sh 查看的是当前目录的大小

du -sh * 查看Linux文件夹下每个文件和文件夹的大小

du -sh /*  查看的是当前目录下所有子文件与子目录的大小，将其一 一列出

du -sh */  只查看当前目录下文件夹的大小

ls -lh  查看当前文件夹每个文件大小

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

## 网络

**ping命令**

语法：ping [-c num] ip或主机名

```
ping baidu.com
ping 192.110.168.11
ping -c 3 baidu.com
```

### 网关

打印Linux中的路由表，查看网关

```java
//查看网关
route -n
//修改网关
vi /etc/sysconfig/network-scripts/ifcfg-eth0
//重启网关
systemctl restart network
//查看日志
/var/log/messages
```

### 新增网关

```java
//在/etc/sysconfig/network-scripts下新建文件ifcfg-eth0:0
//要确保新的网关ip没有被占用
//文件内容
OXY_METHOD="none"  
BROWSER_ONLY="no"  
BOOTPROTO="static"  
DEFROUTE="no"  # 通常设置为 no，除非这是默认路由  
NAME="eth0:0"  
DEVICE="eth0:0"  
ONBOOT="yes"  
IPADDR="192.168.5.28"  
PREFIX="24"
//重启网关
systemctl restart network
//查看是否生效
ip a
//若未生效则查看日志
cat /var/log/messages
```

**wegt命令**

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

### 查看内存占用

**free命令**

free命令显示系统使用和空闲的内存情况，包括物理内存、交互区内存(swap)和内核缓冲区内存。

可以采用free -m和free -g命令查看，分别表示MB和GB

```
-- 内存不足，释放空间
lsof -n / |grep deleted|awk '{print $2}'|xargs kill -9
```

**查看内存占用命令**

ps -e -o pid,comm,%mem --sort=-%mem | head -n 10

**top命令**

top 命令查看系统的实时负载， 包括进程、CPU负载、内存使用等等

![](https://img2023.cnblogs.com/blog/1925794/202304/1925794-20230407022218419-543043266.png)

-Xms：初始Heap大小，使用的最小内存，

-Xmx：java heap最大值，使用的最大内存

-Xmn：年轻代

eg：-Xms8g -Xmx8g -Xmn3g

正常情况下，`-Xmn`参数总是应当小于`-Xmx`参数，否则就会触发`OOM`错误。

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

历史命令文件地址：~/.bash_history

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

### service文件

systemd的一个重要概念就是单元(systemd units)。

(1) systemd units所在的不同目录代表不同的units：

/usr/lib/systemd/system/: 该目录存放由RPM包安装时产生的unit文件；

/run/systemd/system/: 存放在运行时创建的unit文件，这里的unit文件可以覆盖安装时产生的unit文件；

/etc/systemd/system/: 存放由"systemctl enable"创建的unit文件以及由个人手动创建的unit文件；

(2) systemd unit的不同类型：

| **Unit Type**  | **File Extension** | **Description**    |
| -------------- | ------------------ | ------------------ |
| Service unit   | .service           | 系统服务           |
| Target unit    | .target            | systemd units组    |
| Automount unit | .automount         | 文件系统自动挂载点 |
| Device unit    | .device            | 设备文件           |
| Mount unit     | .mount             | 文件系统挂载点     |

### systemctl命令

语法：systemctl start | stop | status | restart | reload | enable | disable 服务名

* start  启动服务
* stop  关闭服务
* status  查看服务状态
* restart  重启服务
* reload  重新加载配置
* enable  开启开机自启
* disable  关闭开机自启

#### service文件地址

etc/systemd/system

查看启动日志：journalctl -u <service-name>

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

### 环境变量

```java
//查看系统的环境变量
env
```

### 防火墙

#### 查看防火墙状态

```java
//查看防火墙状态 
systemctl status firewalld
//开启防火墙 
systemctl start firewalld
//关闭防火墙 
systemctl stop firewalld
//开启防火墙 
service firewalld start
//若遇到无法开启
先用：systemctl unmask firewalld.service
然后：systemctl start firewalld.service
//列出防火墙的详细信息
firewall-cmd --list-all-zones
//移除custom的ip段限制
firewall-cmd --permanent --zone=custom --remove-source=10.0.0.0/8
//清除custom端口限制
firewall-cmd --permanent --zone=custom --remove-port=8081/tcp
//清除 custom 区域中所有先前设置的富规则
firewall-cmd --zone=custom --remove-all-rich-rules
```

#### 查看对外开放的端口状态

```java
//查询已开放的端口（已开放的端口号集合）：
firewall-cmd --zone=public --list-ports
//查询已开放的端口 
netstat -ntulp | grep 端口号：可以具体查看某一个端口号
//查询指定端口是否已开 
firewall-cmd --query-port=666/tcp
//提示 yes，表示开启；no表示未开启。
```

#### 对外开放端口

```java
//查询已开放端口
firewall-cmd --zone=public --list-ports
//查看想开的端口是否已开
firewall-cmd --query-port=6379/tcp
//添加指定需要开放的端口
firewall-cmd --add-port=123/tcp --permanent
//重新加载防火墙规则
firewall-cmd --reload
//查询指定端口是否开启成功
firewall-cmd --query-port=123/tcp
//移除指定端口
firewall-cmd --permanent --remove-port=123/tcp
```

#### 限制ip访问

```java
//使用firewall-cmd命令来限制特定IP地址对系统的访问
firewall-cmd --permanent --add-rich-rule='rule family="ipv4" source address="183.238.22.166" reject'
//使用firewall-cmd命令来限制特定IP地址对系统端口的访问
firewall-cmd --permanent --add-rich-rule='rulefamily="ipv4" sourceaddress="192.16.150.10" port protocol="tcp" port="8035" reject"
//重新加载防火墙规则
firewall-cmd --reload
//查询已禁用的ip
firewall-cmd --list-rich-rules
//使用firewall-cmd命令来解除被禁用ip的端口限制
firewall-cmd --permanent --add-rich-rule='rulefamily="ipv4" sourceaddress="192.16.150.10" accept'
//使用firewall-cmd命令来解除被禁用ip的端口限制
firewall-cmd --permanent --add-rich-rule='rulefamily="ipv4" sourceaddress="192.16.150.10" port protocol="tcp" port="8035" accept'
//重新加载防火墙规则
firewall-cmd --reload
```

### iptable防火墙

```java
//查看已开放端口
iptables -L -n --line-numbers
//添加开放端口
iptables -I INPUT -p tcp --dport 80 -j ACCEPT
iptables -I INPUT -p tcp --dport 3306 -j ACCEPT
//永久添加端口
vi /etc/sysconfig/iptables
```

### 服务器代理

全局代理：/etc/profile

在 `/etc/profile` 文件中设置 `http_proxy` 和 `https_proxy` 环境变量是为了让系统上的所有用户都能够通过配置的代理服务器来访问 HTTP 和 HTTPS 资源。这些环境变量通常被各种命令行工具和应用程序用来确定是否需要以及如何通过一个代理服务器来发送网络请求。

需要注意的是，在 `/etc/profile` 文件中设置这些环境变量会影响系统上的所有用户。这通常是一个全局设置，适用于所有登录到系统的用户。如果你只想为特定用户设置这些环境变量，你可以将这些 `export` 命令添加到该用户的 `~/.bashrc`、`~/.bash_profile` 或其他相应的 shell 初始化文件中。

```
export http_proxy=http://aicfe:aicfe@172.16.185.244:3128
export https_proxy=http://aicfe:aicfe@172.16.185.244:3128
```

项目代理：tkben-api/bin/setenv.sh

```sh
# Add apr lib to java.library.path
JAVA_OPTS="$JAVA_OPTS -Djava.library.path=/usr/local/apr/lib  -Dhttp.maxConnections=5000"

# proxy
JAVA_OPTS="$JAVA_OPTS -Dhttp.proxyHost=172.16.185.244 -Dhttp.proxyPort=3128 -Dhttps.proxyHost=172.16.185.244 -Dhttps.proxyPort=3128 -Dhttp.nonProxyHosts='localhost|127.0.0.1|172.*.*.*' -Dlog4j2.formatMsgNoLookups=true"

#CATALINA_OPTS="$CATALINA_OPTS -server -Xms512m -Xmx4192m -XX:+UseConcMarkSweepGC -XX:-PrintGC -XX:-PrintGCDetails -XX:-PrintGCTimeStamps -Xloggc:../logs/gc.log"

CATALINA_OPTS="$CATALINA_OPTS -Xms256m -Xmx1024m  -XX:+UseConcMarkSweepGC -XX:ParallelGCThreads=8 -XX:CMSInitiatingOccupancyFraction=80 -XX:+UseCMSCompactAtFullCollection -XX:CMSFullGCsBeforeCompaction=0 -XX:-PrintGC -XX:-PrintGCDetails -XX:-PrintGCTimeStamps -Xloggc:../logs/gc.log"
```

```
-Dhttp.proxyHost=172.16.185.244: 设置HTTP代理的主机地址为172.16.185.244。
-Dhttp.proxyPort=3128: 设置HTTP代理的端口号为3128。
-Dhttps.proxyHost=172.16.185.244: 设置HTTPS代理的主机地址为172.16.185.244。
-Dhttps.proxyPort=3128: 设置HTTPS代理的端口号为3128。
-Dhttp.nonProxyHosts='localhost|127.0.0.1|172.*.*.*': 这是一个用逗号分隔的列表，定义了哪些主机名或IP地址不应使用代理。在这个例子中，localhost、127.0.0.1以及以172开头的所有IP地址都不会通过代理。
```

### yum

```java
//安装所有更新软件
yum update
//列出所有可安装的软件包
yum list
//列出所有已安装的软件包
yum list installed
//搜索是否有对应软件包
yum search <keyword>
eg:yum search squid
//若有，则可以下载
yum install <keyword>
//清除YUM缓存
yum clean packages
//重新生成缓存
yum makecache
```

安装运行443端口

```java
//安装SSL
yum install mod_ssl
//启动
systemctl restart httpd
//防火墙开启443端口
```

### 镜像

**YUM 仓库镜像配置**：查看 `/etc/yum.repos.d/` 目录下的 `.repo` 文件

```
[yumlocal]
name=yumlocal
baseurl=http://192.168.2.3/media
gpgcheck=0
enabled=1

[yumQH]
name=yumlocal
baseurl=http://192.168.2.3/ius
gpgcheck=0
enabled=0
```

### 端口

计算机程序之间的通讯，通过IP只能锁定计算机，但是无法锁定具体的程序。

通过端口可以锁定计算机上具体的程序，确保程序之间进行沟通。

IP只能确定计算机，通过端口才能锁定要交互的程序。

IP地址相当于小区地址，在小区里有许多住户(程序)，而门牌号(端口)就是各个住户的联系地址。

Linux系统是一个超大号小区，可以支持65535个端口，这6万多个端口分为3类进行使用：

- 公认端口: 1~1023，通常用于一些系统内置或知名程序的预留使用，如SSH服务的22端口， HTTPS服务的443端口
  非特殊需要，不要占用这个范围的端口
- 注册端口: 1024~49151，通常可以随意使用，用于松散的绑定一些程序\服务（用户自定义）
- 动态端口: 49152~65535，通常不会固定绑定程序，而是当程序对外进行网络链接时，用于临时使用。（多用于出口）

**查看端口占用**

**nmap命令**，安装nmap：yum -y install nmap

查看该IP的对外暴露端口

语法：nmap 被查看的IP地址

```
nmap 127.0.0.1
```

**lsof命令** 

查看端口连接情况 

```
lsof -i :8072
```

在tomcat里查看服务端口：vi conf/server.xml

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

### crontab

crontab是Unix和Linux用于设置周期性被执行的指令，是互联网很常用的技术，很多任务都会设置在crontab循环执行。

crontab：定时任务的守护进程，精确到分，设计秒的我们一般写脚本 –>相当于闹钟

日志文件: ll /var/log/cron*

编辑文件： vim /etc/crontab

进程：ps -ef | grep crond ==> /etc/init.d/crond restart

作用：定时备份，实时备份

```yml
crontab -l #查看定时内容
crontab -e
# 此时会进入 vi 的编辑画面让您编辑工作！注意到，每项工作都是一行。
#分 时 日  月 周      |<==============任务的完整命令行
# 凌晨两点执行rsyncfile.sh脚本任务
 *  2  *  *  *       /home/blue/do/rsyncfile.sh
```

**sh脚本**

```sh
#!/bin/bash
# 删除上次执行过的sql文件
rm -f abilities.sql indicators.sql knowledges.sql
# 备份表数据
mysqldump -h172.18.136.20 -P4000 -ueasyuser -pOTsTp=Mu --skip-opt -t --default-character-set utf8 easy_paper abilities > abilities.sql
mysqldump -h172.18.136.20 -P4000 -ueasyuser -pOTsTp=Mu --skip-opt -t --default-character-set utf8 easy_paper indicators > indicators.sql
mysqldump -h172.18.136.20 -P4000 -ueasyuser -pOTsTp=Mu --skip-opt -t --default-character-set utf8 easy_paper knowledges > knowledges.sql
echo "$(date +%F) export success"
# 清空表数据
sed -i "1i\TRUNCATE TABLE abilities;" abilities.sql
sed -i "1i\TRUNCATE TABLE indicators;" indicators.sql
sed -i "1i\TRUNCATE TABLE knowledges;" knowledges.sql
# 替换文件内的字符串
sed -i "s#abilities#yice_abilities#g" abilities.sql
sed -i "s#indicators#yice_indicators#g" indicators.sql
sed -i "s#knowledges#yice_knowledges#g" knowledges.sql
# 执行备份sql命令
mysql -h172.18.136.19 -uliufang -pGaojj2023. --default-character-set utf8 learningcell_v2 < abilities.sql
mysql -h172.18.136.19 -uliufang -pGaojj2023. --default-character-set utf8 learningcell_v2 < indicators.sql
mysql -h172.18.136.19 -uliufang -pGaojj2012. --default-character-set utf8 learningcell_v2 < knowledges.sql
```

**rpm**

查看系统中是否已安装mysql

rpm -qa |grep mysql

返回空值说明没有安装mysql

### SELinux

```java
//查询SELinux的状态
getenforce
```

- `Enforcing`：SELinux处于启用状态，并正在强制执行其安全策略。
- `Permissive`：SELinux处于宽容模式，这意味着它不会阻止任何操作，但会记录那些违反策略的操作。
- `Disabled`：SELinux被禁用，不会提供任何额外的访问控制。

```java
//通过修改配置文件永久禁用SELinux
vi /etc/selinux/config
//SELINUX=disabled
```

### 关机

```java
//立即关机
shutdown -h now
```

