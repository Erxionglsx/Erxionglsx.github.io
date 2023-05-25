# Nginx

> https://www.bilibili.com/video/BV1zJ411w7SV?p=1

------

[TOC]

## 安装Nginx

下面的操作是以Centos7为例

**1. 使用远程连接工具连接Centos7操作系统**
**2. 安装nginx相关依赖**

```nginx
gcc
pcre
openssl
zlib
```

① 安装 nginx 需要先将官网下载的源码进行编译，编译依赖 gcc 环境，如果没有 gcc 环境，则需要安装：

```nginx
yum install gcc-c++
```

② PCRE(Perl Compatible Regular Expressions) 是一个Perl库，包括 perl 兼容的正则表达式库。nginx 的 http 模块使用 pcre 来解析正则表达式，所以需要在 linux 上安装 pcre 库，pcre-devel 是使用 pcre 开发的一个二次开发库。nginx也需要此库。命令：

```nginx
yum install -y pcre pcre-devel
```

③ zlib 库提供了很多种压缩和解压缩的方式， nginx 使用 zlib 对 http 包的内容进行 gzip ，所以需要在 Centos 上安装 zlib 库。

```nginx
yum install -y zlib zlib-devel
```

④ OpenSSL 是一个强大的安全套接字层密码库，囊括主要的密码算法、常用的密钥和证书封装管理功能及 SSL 协议，并提供丰富的应用程序供测试或其它目的使用。
nginx 不仅支持 http 协议，还支持 https（即在ssl协议上传输http），所以需要在 Centos 安装 OpenSSL 库。

```nginx
yum install -y openssl openssl-devel
```

**3. 安装Nginx**

① 下载nginx，两种方式

> a. 直接下载`.tar.gz`安装包，地址：https://nginx.org/en/download.html
>
> b. **使用wget命令下载（推荐）**。确保系统已经安装了wget，如果没有安装，执行 yum install wget 安装。
>
> c.使用yum下载：yum nginx

```nginx
$ wget -c https://nginx.org/download/nginx-1.19.0.tar.gz
```

② 依然是直接命令：

```nginx
$ tar -zxvf nginx-1.19.0.tar.gz$ 
cd nginx-1.19.0
```

③ 配置：

其实在 nginx-1.12.0 版本中你就不需要去配置相关东西，默认就可以了。当然，如果你要自己配置目录也是可以的。

1.使用默认配置

```nginx
$ ./configure
```

2.自定义配置（不推荐）

```nginx
$ ./configure \--prefix=/usr/local/nginx \--conf-path=/usr/local/nginx/conf/nginx.conf \--pid-path=/usr/local/nginx/conf/nginx.pid \--lock-path=/var/lock/nginx.lock \--error-log-path=/var/log/nginx/error.log \--http-log-path=/var/log/nginx/access.log \--with-http_gzip_static_module \--http-client-body-temp-path=/var/temp/nginx/client \--http-proxy-temp-path=/var/temp/nginx/proxy \--http-fastcgi-temp-path=/var/temp/nginx/fastcgi \--http-uwsgi-temp-path=/var/temp/nginx/uwsgi \--http-scgi-temp-path=/var/temp/nginx/scgi

./configure --prefix=/usr/local/nginx --with-stream \--pid-path=/var/run/nginx.pid
```

> 注：将临时文件目录指定为/var/temp/nginx，需要在/var下创建temp及nginx目录

④ 编译安装

```nginx
$ make && make install
```

查看版本号(`使用nginx操作命令前提条件:必须进入nginx的目录/usr/local/nginx/sbin`)

```nginx
$ ./nginx -v
```

查找安装路径：

```nginx
$ whereis nginx
$ find / -name nginx
```

⑤ 启动，停止nginx

```nginx
$ cd /usr/local/nginx/sbin/
$ ./nginx 
$ ./nginx -s stop
$ ./nginx -s quit
$ ./nginx -s reload
```

查询nginx进程：

```nginx
ps aux|grep nginx
```

**使用systemctl管理编译安装的nginx**

1.创建一个名为`nginx.service`的文件，并将其保存在`/etc/systemd/system/`目录下。在文件中添加以下内容：

```javascript
[Unit]
Description=The NGINX HTTP and reverse proxy server
After=syslog.target network.target remote-fs.target nss-lookup.target

[Service]
Type=forking
PIDFile=/usr/local/nginx/logs/nginx.pid
ExecStartPre=/usr/local/nginx/sbin/nginx -t
ExecStart=/usr/local/nginx/sbin/nginx
ExecReload=/bin/kill -s HUP $MAINPID
ExecStop=/bin/kill -s QUIT $MAINPID
PrivateTmp=true

[Install]
WantedBy=multi-user.target
```

2.加载新的服务文件

```
systemctl daemon-reload
```

3.启动服务并设置开机自启

```
systemctl start nginx
systemctl enable nginx
```

如果你需要查看nginx的状态或日志，可以使用以下命令

```lua
systemctl status nginx
journalctl -xe -u nginx
```

#### yum安装

```yml
yum update #更新软件包列表
yum search nginx  #查询是否有nginx
yum install nginx #下载nginx
systemctl start nginx #启动nginx
systemctl status nginx #查看nginx启动状态
systemctl stop nginx #关闭nginx
journalctl -xeu nginx.service #查看报错日志
```

### 卸载nginx

```yml
#删除 Nginx安装目录及其下所有文件
rm -rf usr/local/nginx
rm -rf home/nginx
#删除 Nginx 系统服务文件
rm -f /lib/systemd/system/nginx.service
rm -f /etc/init.d/nginx
#删除 Nginx 用户和组
sudo userdel nginx
sudo groupdel nginx
```

### 编译安装使用systemctl管理

在编译安装 Nginx 后，可以使用以下步骤将其设置为系统服务，并使用 systemctl 命令对其进行启动和管理

1.创建一个文件 `/usr/lib/systemd/system/nginx.service`，并添加以下代码：

```
[Unit]
Description=The NGINX HTTP and reverse proxy server
After=syslog.target network.target remote-fs.target nss-lookup.target

[Service]
Type=forking
PIDFile=/usr/local/nginx/logs/nginx.pid
ExecStartPre=/usr/local/nginx/sbin/nginx -t
ExecStart=/usr/local/nginx/sbin/nginx
ExecReload=/usr/local/nginx/sbin/nginx -s reload
ExecStop=/bin/kill -s QUIT $MAINPID
PrivateTmp=true

[Install]
WantedBy=multi-user.target
```

 2.创建 `/usr/local/nginx/logs` 目录，并设置其权限：

```
mkdir /usr/local/nginx/logs
chmod 755 /usr/local/nginx/logs
```

3.重新加载 systemctl 配置文件，以使其能够读取 nginx.service：

```
systemctl daemon-reload
```

4.启用 Nginx 服务，使其在系统启动时自动启动

```
systemctl enable nginx
```

## 1.Nginx特性

### 1.Nginx简介

*Nginx* 是一个高性能的[HTTP](https://baike.baidu.com/item/HTTP)和[反向代理](https://baike.baidu.com/item/反向代理/7793488)web服务器，特点是**占有内存少，并发能力强**，事实上Nginx的并发能力确实在同类型的网页服务器中表现较好。

### 2.反向代理

**正向代理**：在客户端(浏览器)配置代理服务器，通过代理服务器进行互联网访问。

![](https://note.youdao.com/yws/api/personal/file/7748A991B4A44E59A30D37D336053642?method=download&shareKey=87d920f263eded84e2529d2670f6ac38)

**反向代理**：其实客户端对代理是无感知的，因为客户端不需要任何配置就可以访问，我们只需要将请求发送到反向代理服务器，由反向代理服务器去选择目标服务器获取数据后，再返回给客户端，此时反向代理服务器和目标服务器对外就是一个服务器，暴露的是代理服务器地址，隐藏了真实服务器IP地址。

![](https://note.youdao.com/yws/api/personal/file/650E9C0D631B4D958BE223B2EE771700?method=download&shareKey=f24b20edfe96f9f120cce6b71fbacdad)

### 3.负载均衡

单个服务器解决不了，我们增加服务器的数量，然后将请求分发到各个服务器上，将原先请求集中到单个服务器上的情况改为将请求分发到多个服务器上，将负载分发到不同的服务器，也就是我们所说的负载均衡。

![](https://note.youdao.com/yws/api/personal/file/1CEB6B4A0FED4600A9A73BF4AA3EB3F6?method=download&shareKey=3759c41a078a78ec54d745657582eb55)

### 4.动静分离

为了加快网站的解析速度，可以把动态界面和静态界面由不同的服务器来解析，加快解析速度，降低原来单个服务器的压力。

![](https://note.youdao.com/yws/api/personal/file/C1F4A988E2A144E78286FA8FCE2024ED?method=download&shareKey=51da6bc714b2427eeaa65e8c1478fabe)

## 2.Nginx常用命令

```nginx
systemctl start nginx #启动nginx服务
systemctl status nginx #查看nginx状态
systemctl reload nginx #重新加载配置 
systemctl stop nginx #停止服务
systemctl restart nginx #重启服务
systemctl enable nginx #设置开机自启动
systemctl disable nginx #停止开机自启动
ps aux|grep nginx #查询nginx进程
whereis nginx #查找安装路径
find / -name nginx #查找nginx相关文件
nginx -t #语法检查
```

## 3.Nginx配置文件组成

Nginx配置文件主要有4部分，main(全局设置)、server（主机设置）、upstream（上游服务器设置，主要为反向代理，负载均衡相关配置）和location（url匹配特定位置的设置），每部分包含若干指令。

* Main部分的设置影响其他所有部分的设置；
* Server部分主要用于指定虚拟机主机域名，ip和端口；
* Upstream的指令用于设置一系列的后端服务器，设置反向代理及后端服务器的负载均衡；
* Location部分用于匹配网页位置（如，跟目录“/”,”/images”等）。

它们之间的关系是，server继承main，location继承server，upstream既不会继承指令也不会被继承。

**可以将 nginx.conf 配置文件分为三部分**
第一部分：**全局块**

从配置文件开始到 events 块之间的内容，主要会设置一些影响 nginx 服务器整体运行的配置指令，主要包括配置运行 Nginx 服务器的用户（组）、允许生成的 worker process 数，进程 PID 存放路径、日志存放路径和类型以及配置文件的引入等。

第二部分：**events 块**

events 块涉及的指令主要影响 Nginx 服务器与用户的网络连接，常用的设置包括是否开启对多 work process下的网络连接进行序列化，是否允许同时接收多个网络连接，选取哪种事件驱动模型来处理连接请求，每个 word process 可以同时支持的最大连接数等。
比如 worker_connections 1024; 支持的最大连接数为 1024

第三部分：**http 块**
这算是 Nginx 服务器配置中最频繁的部分，代理、缓存和日志定义等绝大多数功能和第三方模块的配置都在这里。
需要注意的是：http 块也可以包括 http 全局块、server 块。
①、http 全局块

http 全局块配置的指令包括文件引入、MIME-TYPE 定义、日志自定义、连接超时时间、单链接请求数上限等。

②、server 块

这块和虚拟主机有密切关系，虚拟主机从用户角度看，和一台独立的硬件主机是完全一样的，该技术的产生是为了节省互联网服务器硬件成本。 每个 http 块可以包括多个 server 块，而每个 server 块就相当于一个虚拟主机。 而每个 server 块也分为全局 server 块，以及可以同时包含多个 locaton 块。
1、全局 server 块
最常见的配置是本虚拟机主机的监听配置和本虚拟主机的名称或 IP 配置。
2、location 块
一个 server 块可以配置多个 location 块。 这块的主要作用是基于 Nginx 服务器接收到的请求字符串（例如 server_name/uri-string），对虚拟主机名称（也可以是 IP 别名）之外的字符串（例如 前面的 /uri-string）进行匹配，对特定的请求进行处理。地址定向、数据缓存和应答控制等功能，还有许多第三方模块的配置也在这里进行。

**alias与root的区别**

```yml
# alias 指令会使用 location 块中匹配到的 URI 替换 alias 指令中所配置的路径，从而构成服务器上真实的文件路径。若按照上述配置的话，则访问/img/目录里面的文件时，ningx会自动去/var/www/image/目录找文件
#alias必须以/结尾
location /img/ {
	alias /var/www/image/;
}
# root 指令会将 location 块中匹配到的 URI 直接拼接到 root 指令中所配置的路径之后，从而构成服务器上真实的文件路径。若按照这种配置的话，则访问/img/目录下的文件时，nginx会去/var/www/image/img/目录下找文件
location /img/ {
	root /var/www/image;
}
```

**index**

index 的作用就是当没有访问任何文件时，则默认访问 index.html

```yml
# 访问 http://127.0.0.1:8081/tkben 时就会访问 /usr/local/html/tkben 下的 index.html 文件
location /tkben {
    index index.html;
    root /usr/local/html;
}
```

如 `http://127.0.0.1:8081/tkben/test.html` 就会访问 `/usr/local/html/tkben` 下的 `test.html` 文件

**proxy_pass配置规则**

* 配置 proxy_pass 时，当在后面的 url 加上了 /，相当于是绝对路径，则 Nginx 不会把 location 中匹配的路径部分加入代理 uri。
* 如果配置 proxy_pass 时，后面没有 /，Nginx 则会把匹配的路径部分加入代理 uri。

```yml
server {
        listen       8081;
        server_name  localhost;
 
        location / {
            root   html;
            index  index.html index.htm;
        }
        
#情景1:proxy_pass后有/ ，表绝对路径，不把匹配部分加入最终代理路径（location 和proxy_pass结尾一致）
        #访问地址：http://localhost:8081/model/asc.shtml
        #最终代理：http://127.0.0.1:8082/model/asc.shtml
		location /model/ {
            proxy_pass   http://127.0.0.1:8082/model/;
        }
        
#情景2:proxy_pass后有/ ，表绝对路径，不把匹配部分加入最终代理路径（location 和proxy_pass结尾不一致）
        #访问地址：http://localhost:8081/model/asc.shtml
        #最终代理：http://127.0.0.1:8082/asc.shtml
		location /model/ {
            proxy_pass   http://127.0.0.1:8082/;
        }
        
#情景3：proxy_pass后没有 / ，Nginx会把匹配部分带到代理的url
        #访问地址：http://localhost:8081/model/asc.shtml
        #最终代理：http://127.0.0.1:8082/model/asc.shtml
		location /model/ {
            proxy_pass   http://127.0.0.1:8082;
        }
    }
```

**return**

* 返回 状态码+ 字符串

  ```
  location /a {
  	return 200 'Hi, I am a.';
  }
  ```

* 重定向

  ```
  location /a {
    return https://www.baidu.com;
  }
  ```

**rewrite**

当访问
`http://127.0.0.1:8000/c`
`http://127.0.0.1:8000/c/c.html`
`http://127.0.0.1:8000/c/xxx/xxx/xxx`
都会重定向访问 root/r/1.html

```yml
#访问地址：http://127.0.0.1:8000/c
#访问地址：http://127.0.0.1:8000/c/c.html
#访问地址：http://127.0.0.1:8000/c/xxx/xxx/xxx
#最终代理：http://127.0.0.1:8000/mnt/nfs/r/1.html
location /c {
	alias /mnt/nfs;
	rewrite ^(.*)$ /r/1.html;
}

#带有last指令
location /c {
    rewrite ^(.*)$ /r/1.html last;
    # 不会触发这个，因为 last 会跳过下面的执行语句，但它会继续走进下一个 location 块
    rewrite ^(.*)$ /r/2.html last; 
}
# 这里监听 /r/1.html 再做一次重定向
location = /r/1.html {
	rewrite ^(.*)$ /r/3.html last;
}

#带有break指令
location /c {
    rewrite ^(.*)$ /r/1.html break;
    # 不会触发这个，因为 break 会跳过下面的执行语句，同时也会跳过下面的 location 块
    rewrite ^(.*)$ /r/2.html last; 
  }
  # 由于 break 这里不会触发到
  location = /r/1.html {
    rewrite ^(.*)$ /r/3.html last;
  }
```

> location配置：https://blog.csdn.net/wangzhongshun/article/details/126434539

nginx.conf

```
user  nginx;
worker_processes  auto;

error_log  /var/log/nginx/error.log warn;
pid        /var/run/nginx.pid;
worker_rlimit_nofile 655350;

events {
    worker_connections  7168;
}

http {
    include       mime.types;
    default_type  application/octet-stream;

    upstream lcell-auth {
        server 127.0.0.1:8081;
    }

    upstream lcell-api {
        server 127.0.0.1:8082;
    }

    sendfile        on;
    #tcp_nopush     on;

    #keepalive_timeout  0;
    keepalive_timeout  65;
    
    #我们在conf.d目录下，新建.conf文件，写入自己的nginx配置
    include /etc/nginx/conf.d/*.conf;
}
```

/etc/nginx/conf.d/80.conf

```conf
 server {
        listen       80; //监听端口
        server_name  localhost; //监听地址
        
        # location 匹配内容的相对路径，alias 表示 一个绝对路径,而且必须以"/"结尾
        location /tkben {
			index index.html;
			alias /usr/local/html/tkben;
		}
		
		location = /50x.html {
              root   /usr/share/nginx/html;
         }
		
		location /tfish {
			index index.html;
			alias /usr/local/html/tfish;
		}
		
		location /public/ {
           	 alias /mnt/nfs/lcellstorage/public/;
           	 rewrite /public/images/header/.*0_(.*) /public/images/$1; //url重写
        }
		
		location /images/ {
			alias /mnt/nfs/lcellstorage/images/;
		}
       
        location /v2/api {
            client_max_body_size 1024m;
            proxy_pass          http://lcell-api;
            proxy_set_header    Host $host;
            proxy_set_header    X-Real-IP $remote_addr;
            proxy_set_header    X-Forwarded-For $proxy_add_x_forwarded_for;
            proxy_connect_timeout 18000;
            proxy_send_timeout 18000;
            proxy_read_timeout 18000;
        }

        location /v2/uc {
            client_max_body_size 10m;
            proxy_pass          http://lcell-auth;
            proxy_set_header    Host $host;
            proxy_set_header    X-Real-IP $remote_addr;
            proxy_set_header    X-Forwarded-For $proxy_add_x_forwarded_for;
            proxy_connect_timeout 600;
            proxy_send_timeout 600;
            proxy_read_timeout 600;
        }
    }
```

skee.conf

```conf
server {
        listen       80;
        server_name  192.168.12.166;

	    #重定向到其他网站
        location = / {
                return 301 http://www.baidu.cn/;
                add_header Cache-Control no-cache;
                add_header Pragma no-cache;
                add_header Expires 0;
        }
    }
```

## 4.反向代理实例Ⅰ

**访问过程分析**

![](https://note.youdao.com/yws/api/personal/file/ACCF07DB8F3F40779A5D0FD2F1C6AAE1?method=download&shareKey=e90c03540a6d273a553fe9158539225b)

**具体配置**

第一步 在Windows系统的host文件进行域名和ip对应关系的配置

![](https://note.youdao.com/yws/api/personal/file/39EB23A2A4D24BC5BBE6EA2B4BD7EB3A?method=download&shareKey=902b9ec75971ce19677148d4c9379d3f)

添加内容在hosts文件中

![](https://note.youdao.com/yws/api/personal/file/3ABB755421AA4E5282CE086EF6CE54D0?method=download&shareKey=25fcf4769d36dfb4b7c82571dc92286f)

![](https://note.youdao.com/yws/api/personal/file/25F4EA005D8C4C6192264AEB5987DC8A?method=download&shareKey=6e6b39703c64978e4f6a0c30b8b080c4)

第二步 在Nginx进行请求转发的配置(反向代理配置)

![](https://note.youdao.com/yws/api/personal/file/BE391E64E54E4C9A9F02E95384BD16C3?method=download&shareKey=06fbaeda1f3932c454a45c7d742b240b)

![](https://note.youdao.com/yws/api/personal/file/653C8256F5C3454896BE50F92D437150?method=download&shareKey=c340b86dc70d1503fa6bfc9edc82d064)

## 5.反向代理实例Ⅱ

**1、实现效果**

使用nginx反向代理，根据访问的路径跳转到不同端口的服务中。

nginx监听端口为9001

* 访问http://127.0.0.1:9001/edu/ 直接跳转到127.0.0.1:8081
* 访问http://127.0.0.1:9001/vod/ 直接跳转到127.0.0.1:8082

**2、准备工作**

* 准备两个tomcat服务器，一个8080端口，一个8081端口
* 创建文件夹和测试页面。

**3、具体nginx配置**

* 找到Nginx配置文件，进行反向代理配置

```
$ vi /usr/local/nginx/conf/nginx.conf
```

![](https://note.youdao.com/yws/api/personal/file/E6DBA469FB6A466B8EFA1799C8568AD2?method=download&shareKey=83867d75b319a913ac56d59bbad93fd3)

* 开放对外访问的端口号9001
* 重启nginx服务器，使配置文件生效

**4.效果**

![](https://note.youdao.com/yws/api/personal/file/9D56FA574E034A57A80DDBF8D4A7B712?method=download&shareKey=2a0d8d30b9d7b18fedff109faf2b8ea5)

![](https://note.youdao.com/yws/api/personal/file/F5FB36EE53FF445982138FC276A2D550?method=download&shareKey=31983628c8b68c59f45c33409d7ff7f5)

**5、补充location部分**

location指令说明，该指令用于匹配URL。

语法如下:

```
location [ = | ~ | ~* | ^~] uri {

}

1、=: 用于不含正则表达式的uri前，要求请求字符串与uri严格匹配，如果匹配成功，
	就停止继续向下搜索并立即处理该请求
2、~: 用于表示uri包含正则表达式，并且区分大小写
3、~*: 用于表示uri包含正则表达式，并且不区分大小写
4、^~: 用于不含正则表达式的uri前，要求Nginx服务器找到标识uri和请求字
	符串匹配度最高的location后，立即使用此location处理请求，而不再使用location
	块中的正则uri和请求字符串做匹配
注意: 如果uri包含正则表达式，则必须要有~或者~*标识。
```

## 6.负载均衡

**1.实现效果**

(1) 浏览器地址栏输入地址http://192.168.xxx.xxx/edu/index.html, 负载均衡效果，平均到8080
和8081端口中。

**2.准备工作**

* 准备两台tomcat服务器，一 台8080， 一台8081。
* 在两台tomcat里面webapps目录中，创建名称是edu文件夹，在edu文件夹中创建页面index.html,用于测试。

**3.在nginx的配置文件中进行负载均衡的配置**

服务名叫myserver

![](https://note.youdao.com/yws/api/personal/file/05B921AAE4F54F2E92EC0BDA8FF388DB?method=download&shareKey=4fff7007e103a0c8059699f6736f973e)

**4、效果**

![](https://note.youdao.com/yws/api/personal/file/9DDCA8332D2A40A1B15432C7DAE1B1B6?method=download&shareKey=183def0393dac4d3a537aa933c723366)

![](https://note.youdao.com/yws/api/personal/file/39DE9990CEC541808FA339E3A1D87D33?method=download&shareKey=dfb69fdc0d67daef8f334d2995b75f84)

**5.Nginx分配服务器策略**

- **轮询(默认)**

  每个请求按时间顺序逐一分配到不同的后端服务器，如果后端服务器down掉，能自动剔除。

- **weight**
  weight代表权重默认为1,权重越高被分配的客户端越多。
  指定轮询几率，weight和访问比率成正比，用于后端服务器性能不均的情况。

  ![](https://note.youdao.com/yws/api/personal/file/411BB58523CF46CEA658C85C0514BCAC?method=download&shareKey=9a81fb0f7dd884aa220e7f3cff566d74)

- **ip hash**

  每个请求按访问ip的hash结果分配, 这样每个访客固定访问一个后端服务器,可以解诀session的问题。

  ```
  upstream server pool{
     ip_ hash
     server 192.168.5.21:80
     server 192.168.5.22:80
  }
  ```

- **fair (第三方)**
  按后端服务器的响应时间来分配请求，响应时间短的优先分配。

  ```
  upstream server_pool 
  	server 192.168.5.21:80;
  	server 192.168.5.22:80;
  	fair;
  }
  ```
  
## 7.动静分离

Nginx动静分离简单来说就是把动态跟静态请求分开，可以理解成使用Nginx处理静态页面，Tomcat处理动态页面。实现角度来讲大致分为两种：

* 把静态文件独立成单独的域名，放在独立的服务器上。
* 动态跟静态文件混合在一起发布，通过Nginx来分开。

![](https://note.youdao.com/yws/api/personal/file/AE6BF2D8C2AB45FA91644088533C2F75?method=download&shareKey=ddbe64f5c2e89e4fec1b88ebb6ae5137)

​       通过location指定不同的后缀名实现不同的请求转发。通过expires参数设置，可以使浏览器缓存过期时间，减少与服务器之前的请求和流量。具体Expires定义: 是给一个资源设定一个过期时间，也就是说无需去服务端验证，直接通过浏览器自身确认是否过期即可，所以不会产生额外的流量。此种方法非常适合不经常变动的资源。(如果经常更新的文件，不建议使用Expires来缓存)，如果设置3d, 表示在这3天之内访问这个URL, 发送一个请求，比对服务器该文件最后更新时间没有变化，则不会从服务器抓取，返回状态码304,如果有修改，则直接从服务器重新下载，返回状态码200。

**2、准备工作**

(1) 在liunx系统中准备静态资源，用于进行访问

![](https://note.youdao.com/yws/api/personal/file/BE56231A0C6745FCA71D93022A2C3891?method=download&shareKey=8d7b6c033f46d18c61065df51d4ba324)

**3、具体配置**

在nginx配置文件中进行配置

![](https://note.youdao.com/yws/api/personal/file/111F7664845A48BB9C6B21BB1CB372E2?method=download&shareKey=8d28250655a533ecb0a97e40bae6a230)

4、实际测试

浏览器中输入地址

```
http://192.168.17.129/image/1.jpg
```

![](https://note.youdao.com/yws/api/personal/file/01005CD6316A4E559A34871E35279F4E?method=download&shareKey=5b1733ac4ab1b1a1b5b6e3cb89fa6e81)

上图是因为`autoindex on`这个设置生效的

```
http://192.168.17.129/www/a.html
```

![](https://note.youdao.com/yws/api/personal/file/4FB6858CF265426FBDCF369CA2A41371?method=download&shareKey=1d432564d33eb531dc4d923a1c029625)

## 8.高可用

**1.什么是Nginx高可用**

![](https://note.youdao.com/yws/api/personal/file/4855DE92BA5840A6ABD976882360353C?method=download&shareKey=afeb059f5ff78504ab3f3ff1c5d6e5e4)

* 需要两台nginx服务器
* 需要keepalived
* 需要虚拟ip

**2、配置高可用的准备工作**

* 需要两台服务器192.168.17.129 和192.168.17.1314
* 在两台服务器安装nginx.
* 在两合服务器安装keepalived.

**3、在两台服务器安装keepalived**

使用yum命令进行安装

```nginx
$ yum install keepalived  #安装命令
$ rpm -q -a keepalived    #查看是否已经安装上
```

默认安装路径: /etc/keepalived

安装之后，在etc里面生成目录keepalived, 有配置文件keepalived.conf

**4、完成高可用配置(主从配置)**

（1）修改keepalived的配置文件`keepalived.conf`为：

```nginx
global_defs {
	notification_email {
	  acassen@firewall.loc
	  failover@firewall.loc
	  sysadmin@firewall.loc
	}
	notification_email_from Alexandre.Cassen@firewall.loc
	smtp_ server 192.168.17.129
	smtp_connect_timeout 30
	router_id LVS_DEVEL	# LVS_DEVEL这字段在/etc/hosts文件中看；通过它访问到主机
}

vrrp_script chk_http_ port {
	script "/usr/local/src/nginx_check.sh"
	interval 2   # (检测脚本执行的间隔)2s
	weight 2  #权重，如果这个脚本检测为真，服务器权重+2
}

vrrp_instance VI_1 {
	state BACKUP   # 备份服务器上将MASTER 改为BACKUP
	interface ens33 //网卡名称,可用ifconfig命令查看
	virtual_router_id 51 # 主、备机的virtual_router_id必须相同
	priority 100   #主、备机取不同的优先级，主机值较大，备份机值较小
	advert_int 1	#每隔1s发送一次心跳
	authentication {	# 校验方式， 类型是密码，密码1111
        auth type PASS
        auth pass 1111
    }
	virtual_ipaddress { # 虛拟ip
		192.168.17.50 // VRRP H虛拟ip地址
	}
}
```

（2）在路径/usr/local/src/ 下新建检测脚本 nginx_check.sh，相当于哨兵一直在监视。

```sh
#! /bin/bash
A=`ps -C nginx -no-header | wc - 1`
if [ $A -eq 0];then
	/usr/local/nginx/sbin/nginx
	sleep 2
	if [`ps -C nginx --no-header| wc -1` -eq 0 ];then
		killall keepalived
	fi
fi
```

(3) 把两台服务器上nginx和keepalived启动

```nginx
$ systemctl start keepalived.service		#keepalived启动
$ ps -ef I grep keepalived		#查看keepalived是否启动
```

**5、最终测试**

(1) 在浏览器地址栏输入虚拟ip地址192.168.17.50

(2) 把主服务器(192.168.17.129) nginx和keealived停止，再输入192.168.17.50.

```nginx
$ systemctl stop keepalived.service  #keepalived停止
```

**9.Nginx原理解析**

**1、master和worker**

![](https://note.youdao.com/yws/api/personal/file/2FDFE3BE8333495F823F5112527BAB7D?method=download&shareKey=ce117744c97dcc537015fb0d6308b690)

**2、worker如何进行工作的**

![](https://note.youdao.com/yws/api/personal/file/9A32069B77A947B5A504AABA91444AA7?method=download&shareKey=bc936cae0e2f4b67da3e499a4036ae03)

**3、一个master和多个woker的好处**

可以使用nginx -s reload热部署。

首先，对于每个worker进程来说，独立的进程，不需要加锁，所以省掉了锁带来的开销，同时在编程以及问题查找时，也会方便很多。其次,采用独立的进程，可以让互相之间不会影响，一个进程退出后，其它进程还在工作，服务不会中断，master进程则很快启动新的worker进程。当然，worker进程的异常退出，肯定是程序有bug了，异常退出，会导致当前worker.上的所有请求失败，不过不会影响到所有请求，所以降低了风险。

**4、设置多少个woker合适**

Nginx同redis类似都采用了io多路复用机制，每个worker都是一个独立的进程， 但每个进程里只有一个主线程，通过异步非阻塞的方式来处理请求，即使是 千上万个请求也不在话下。每个worker的线程可以把一个cpu的性能发挥到极致。所以worker数和服务器的cpu数相等是最为适宜的。设少了会浪费cpu,设多了会造成cpu频繁切换上下文带来的损耗。

**5、连接数worker_ connection**

这个值是表示每个worker进程所能建立连接的最大值，所以，一个nginx 能建立的最大连接数，应该是worker.connections * worker processes。当然，这里说的是最大连接数，对于HTTP 请求本地资源来说，能够支持的最大并发数量是worker.connections * worker processes,如果是支持http1.1的浏览器每次访问要占两个连接，所以普通的静态访问最大并发数是: worker.connections * worker.processes / 2, 而如果是HTTP作为反向代理来说，最大并发数量应该是worker.connections * worker_proceses/4. 因为作为反向代理服务器，每个并发会建立与客户端的连接和与后端服务的连接，会占用两个连接。

第一个: 发送请求，占用了woker的几个连接数?
答案: 2或者4个。

第二个: nginx有一个master,有四个woker,每个woker支持最大的连接数1024,支持的最大并发数是多少?
答案：普通的静态访问最大并发数是: worker connections * worker processes /2，
而如果是HTTP作为反向代理来说，最大并发数量应该是worker connections * worker processes/4
