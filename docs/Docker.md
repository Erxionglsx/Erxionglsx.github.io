# Docker

> CentOS7安装Docker:https://blog.csdn.net/u014069688/article/details/100532774
>
> Docker使用教程:https://edu.aliyun.com/lesson_1940_16962?spm=5176.8764728.0.0.50a0bc11OxEIa6#_16962

------

[TOC]

### 1.简介

Docker是基于Go语言实现的云开源项目。Docker 可以让开发者打包他们的应用以及依赖包到一个轻量级、可移植的容器中，然后发布到任何流行的 Linux 机器上，也可以实现虚拟化。使用户的应用及其运行环境能够做到“**一次封装，到处运行**”。

Docker 使用<font color="lighblue">客户端-服务器 (C/S) 架构</font>模式，使用远程API来管理和创建Docker容器。

**好处：**Docker容器在任何操作系统上都是一致的，这就实现了跨平台、跨服务器。只需要一次配置好环境，换到别的机子上就可以一键部署好，大大简化了操作。

### 2.三要素：

- **镜像（Image）**：Docker 镜像（Image），就相当于是一个 root 文件系统。比如官方镜像 ubuntu:16.04 就包含了完整的一套 Ubuntu16.04 最小系统的 root 文件系统。
- **容器（Container）**：镜像（Image）和容器（Container）的关系，就像是面向对象程序设计中的类和实例一样，镜像是静态的定义，容器是镜像运行时的实体。容器可以被创建、启动、停止、删除、暂停等。
- **仓库（Repository）**：仓库可看成一个代码控制中心，用来保存镜像。

**Docker 容器通过 Docker 镜像来创建，一个镜像可以创建很多容器。**

容器与镜像的关系类似于面向对象编程中的对象与类。

| Docker | 面向对象 |
| ------ | -------- |
| 容器   | 对象     |
| 镜像   | 类       |

**仓库（Repository）**是集中<font color="lighblue">存放镜像文件</font>的场所。

仓库和仓库注册服务器的有区别的。仓库注册服务器上往往存放着多个仓库，每个仓库中又包含了多个镜像，每个镜像有不同的标签(tag)。

仓库分为**公开仓库**和**私有仓库**两种形式。

### 3.运行过程

![](https://note.youdao.com/yws/api/personal/file/4E925BA09CB24C3DB5CA98767AA86CEE?method=download&shareKey=4485866c54cbbff745e570dc93569091)

eg: 运行hello-world镜像：docker run hello-world 

![](https://note.youdao.com/yws/api/personal/file/6DBEA76F60FC416287F662FACA469A63?method=download&shareKey=1660b5e60bdd8256a8705b78af9e866b)

### 4.常用命令

#### 帮助命令

* <font color="lighblue">docker version</font>：查看Docker版本
* <font color="lighblue">docker info</font>：显示系统相关信息
* <font color="lighblue">docker --help</font>：显示帮助

#### 镜像命令

* <font color="lighblue">docker images</font>：列出当前镜像
  * -a：列出本地所有镜像
  * -q：只显示镜像ID
  * --digests：显示镜像的摘要信息
  * --no-trunc：显示完整的镜像信息
* <font color="lighblue">docker search [OPTIONS] 镜像名字</font>：在 docker hub 中搜索镜像
  * OPTIONS说明：
    * --no-trunc：显示完整的镜像描述
    * -s：列出收藏数不小于指定值的镜像
      * eg：docker search -s 30 tomcat 列出收藏数大于30的tomcat
    * --automated：只列出automated build类型的镜像
* <font color="lighblue">docker pull 镜像名字[:TAG]</font>：拉取镜像
  * docker pull tomcat 等价于 docker pull tomcat:3.2
* <font color="lighblue">docker rmi 镜像名字</font>：删除镜像
  * docker rmi -f 镜像名字/镜像ID：强制删除镜像
  * docker rmi -f 镜像名1:TAG 镜像名2:TAG：删除多个
  * docker rmi -f $(docker images -qa)：删除全部

#### 容器命令

**有镜像才能创建容器，这是根本前提**

* <font color="lighblue">docker run [OPTIONS] IMAGE [COMMAND] [ARG...]</font>：新建并运行容器
  * [OPTIONS]
    * **-i**：以交互模式运行容器，通常与-t同时使用
    * **-t**：为容器重新分配一个伪输入终端
      * **eg**：docker run -it 88ec626ba223
    * --name="容器新名字"：为容器指定一个名称
    * -d：**后台运行容器**，并返回容器ID **守护式容器**
      * **eg**：docker run -d centos /bin/sh -c "while true;do echo hello zzyy;sleep 2;done" 保证后台挂起的容器不会自动退出
    * -P：随机端口映射
      * **eg**:docker run -it -P tomcat ：**随机分配访问Tomcat的端口**
    * -p：指定端口映射
      * ip:hostPort:containerPort
      * ip::containerPort
      * hostPort:containerPort
      * containerPort
      * **eg**:docker run -it -p 8888:8080 tomcat： **指定端口8888来访问Tomcat**
* <font color="lighblue">docker ps [OPTIONS]</font>：列出容器列表
  * -a：列出当前所有正在运行的容器+历史上运行过的
  * -l：显示最近创建的容器
  * -n 数字：显示最近n个创建的容器
    * docker ps -n 2：显示最近两条容器记录
  * **-q**：静默模式，只显示容器编号
  * --no-trunc：不截断输出
* <font color="lighblue">exit</font>：容器停止并退出
* <font color="lighblue">Ctrl+q+p</font>：容器不停止退出
  * <font color="lighblue">docker attach 容器ID</font>：返回容器，直接进入容器启动命令的终端，不会启动新的进程
  * <font color="lighblue">docker exec -it 容器ID /bin/bash:</font> 在容器中打开新的终端，并且可以启动新的进程
* <font color="lighblue">docker start 容器ID/容器名称</font>：启动容器
* <font color="lighblue">docker restart 容器ID/容器名称</font>：重启容器
* <font color="lighblue">docker stop 容器ID/容器名称</font>：停止容器
* <font color="lighblue">docker kill 容器ID/容器名称</font>：强制停止容器
* <font color="lighblue">docker rm 容器ID/容器名称</font>：删除容器记录
  * docker rm -f $(docker ps -a -q)：一次性删除多个容器

#### 重要

* <font color="lighblue">启动守护式容器</font>：docker run -d 容器名
* <font color="lighblue">查看容器日志</font>：docker logs [OPTIONS] 容器ID
  * -t：是加入时间戳
  * -f：跟随最新的日志打印
  * -tail：显示最后多少条
* <font color="lighblue">查看容器内运行的进程</font>：docker top 容器ID
* <font color="lighblue">查看容器内部细节</font>：docker inspect 容器ID
* <font color="lighblue">从容器内拷贝文件到主机上</font>
  * docker cp 容器ID：容器内路径 目的主机路径
  * eg：docker cp 10b9a4335an4:/tmp/yum.log /root 将虚拟机内的yum.log文件复制到本地

### 5.Docker镜像

**为什么Docker镜像要采用分层结构？**

最大的一个好处——**共享资源**

比如：有多个镜像都从相同的base镜像构建而来，那么宿主机只需在磁盘上保存一份base镜像，同时内存中也只需要加载一份base镜像，就可以为所有容器服务了。而且镜像的每一层都可以被共享。

commit提交容器副本使之成为一个新的镜像

修改本地tomcat文件后使用commit提交容器成为一个新的镜像

![](https://note.youdao.com/yws/api/personal/file/3DAC609987044A21BE2D8C42590C1B4D?method=download&shareKey=522d0a31ff7eb869c83055c354d16bed)

<font color="lighblue">docker commit -m="提交的描述信息" -a="作者" 容器ID要创建的目标镜像名:[标签名]</font>

### 6.安装包安装JDK

官网下载jdk-8u181-linux-x64.tar.gz的包（版本号可以不一样），用Xftp可导入文件到虚拟机。

切换用户为root,在Linux的/usr目录(cd ~ 然后cd .. 然后cd ./usr)下新建文件夹java(mkdir java)，并且通过xftp工具将jdk安装包上传到java文件中。

cd /usr/java（进入java目录下）

tar xvf jdk-8u181-linux-x64.tar.gz（解压jdk安装包）

vi /etc/profile（修改系统环境变量）

在/etc/profile文件末尾添加这几行：

修改环境变量要特别注意，到最下面添加以下代码，不要更改其他代码及格式，修改错会导致source失败。

```
JAVA_HOME=/usr/java/jdk1.8.0_121
PATH=$PATH:$JAVA_HOME/bin
CLASSPATH=.:$JAVA_HOME/lib
export JAVA_HOME PATH CLASSPATH
```

在命令行输入source /etc/profile（重新加载，使其生效）

查看jdk是否安装成功输入命令java -version

### 7.Docker安装mysql

> https://segmentfault.com/a/1190000021523570

docker run -p 3306:3306 --name mysql -v  /zzyyuse/mysql/conf:/etc/mysql/conf.d -v /zzyyuse/mysql/logs:/logs -v /zzyyuse/mysql/data:/var/lib/mysql -e MYSQL_ROOT_PASSWORD=123456 -d mysql:8.0  **运行mysql容器**

* -p 3306:3306：将主机的3306端口映射到docker容器的3306端口。前者是外围访问端口：后者是容器内部端口
* --name mysql：运行服务名字
* -v /zzyyuse/mysql/conf:/etc/mysql/conf.d：将主机/zzyyuse/mysql录下的conf/my.cnf**挂载**到容器的/etc/mysql/conf.d
* /zzyyuse/mysql/logs:/logs：将主机/zzyyuse/mysql目录下的logs目录挂载到容器的logs。
* /zzyyuse/mysql/data:/var/lib/mysql：将主机/zzyyuse/mysql目录下的data目录挂载到容器的/var/lib/mysql
* -e MYSQL_ROOT_PASSWORD=123456：初始化root用户的密码
* -d mysql:8.0：后台程序运行mysql8.0

docker exec -it 容器ID /bin/bash **进入数据库容器**

mysql -uroot -p

docker update mysql --restart=always **设置开机自动启动**

### 8.Docker安装redis

```dockerfile
docker pull redis

docker run -p 6379:6379 
-v /zzyyuse/myredis/data:/data 
-v /zzyyuse/myredis/conf/redis.conf:/usr/local/etc/redis/redis.conf 
-d redis:latest redis-server /usr/local/etc/redis/redis.conf 
--appendonly yes

vim /zzyyuse/myredis/conf/redis.conf/redis.conf  
# 填写redis.conf文件内容 https://blog.csdn.net/weixin_42456466/article/details/87270959

docker exec -it 运行Redis服务的容器ID redis-cli
```

![](https://note.youdao.com/yws/api/personal/file/ECA6C25E35834B18959E511BD73431D9?method=download&shareKey=2bbbef0ff28e604409066b996f96a504)

### 9.Docker安装Nginx

```dockerfile
docker pull nginx:latest  #拉取官方的最新版本的镜像

docker images  #查看是否已安装了 nginx

docker run --name nginx -p 80:80 -d nginx  #运行 nginx 容器

docker update nginx的ID --restart=always #设置开机自启动

docker exec -it nginx的ID bash  #进入Nginx容器中
# 日志位置：/var/log/nginx/
# 配置文件位置：/etc/nginx/
# 项目位置：/usr/share/nginx/html
```
