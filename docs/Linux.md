# Linux

------



![](https://pic.downk.cc/item/5fdd63273ffa7d37b32e4738.jpg)

![](https://pic.downk.cc/item/5fdd63273ffa7d37b32e473b.jpg)

![](https://pic.downk.cc/item/5fdd63273ffa7d37b32e473f.jpg)

![](https://pic.downk.cc/item/5fdd63273ffa7d37b32e4744.jpg)

![](https://pic.downk.cc/item/5fdd63273ffa7d37b32e4735.jpg)

![](https://pic.downk.cc/item/5fdd642e3ffa7d37b32f12c4.jpg)

![](https://pic.downk.cc/item/5fdd642f3ffa7d37b32f12d2.jpg)

![](https://pic.downk.cc/item/5fdd642e3ffa7d37b32f12b7.jpg)

![](https://pic.downk.cc/item/5fdd642e3ffa7d37b32f12ba.jpg)

![](https://note.youdao.com/yws/api/personal/file/96D31325628845CD94CADA404C42CA48?method=download&shareKey=001217721e7fb76552216d264e3a44a2)

#### 查看日志

如果设置的日志文件为catalina.out：

cat catalina.out   查看所有日志

cat catalina.out | grep '开始请求CMS'   查看所有日志并搜索

tail -f catalina.out  查看当前及之后产生的日志

tail -f catalina.out | grep '开始'   查看日志并搜索

tail -5000  catalina.out | grep '门店编码为'  查看含字符串 '门店编码为' 的最近5000条数据

tail -5000  catalina.out | grep -B 10 '门店编码为'  查看最近5000条数据中 '门店编码为' 的前10条数据

tail -5000  catalina.out | grep -A 10 '门店编码为'  查看最近5000条数据中 '门店编码为' 的后10条数据



**软链接：**

1.软链接，以路径的形式存在。类似于Windows操作系统中的快捷方式
2.软链接可以 跨文件系统 ，硬链接不可以
3.软链接可以对一个不存在的文件名进行链接
4.软链接可以对目录进行链接

ln -s /bin/less /usr/local/bin/less 

df -l 显示磁盘使用情况

ps -ef | grep java  搜索进程中有'java'字段的进程数据

Tab键 补全命令

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







