# Tomcat

[TOC]

### server.xml文件

#### server.xml配置

> https://www.cnblogs.com/kismetv/p/7228274.html

server.xml位于$TOMCAT_HOME/conf目录下

```xml
<?xml version="1.0" encoding="UTF-8"?>
<Server port="8002" shutdown="SHUTDOWN">
  <Listener className="org.apache.catalina.startup.VersionLoggerListener" />
  <!-- Security listener. Documentation at /docs/config/listeners.html
  <Listener className="org.apache.catalina.security.SecurityListener" />
  -->
  <!--APR library loader. Documentation at /docs/apr.html -->
  <Listener className="org.apache.catalina.core.AprLifecycleListener" SSLEngine="on" />
  <!-- Prevent memory leaks due to use of particular java/javax APIs-->
  <Listener className="org.apache.catalina.core.JreMemoryLeakPreventionListener" />
  <Listener className="org.apache.catalina.mbeans.GlobalResourcesLifecycleListener" />
  <Listener className="org.apache.catalina.core.ThreadLocalLeakPreventionListener" />

  <!-- Global JNDI resources
       Documentation at /docs/jndi-resources-howto.html
  -->
  <GlobalNamingResources>
    <!-- Editable user database that can also be used by
         UserDatabaseRealm to authenticate users
    -->
    <Resource name="UserDatabase" auth="Container"
              type="org.apache.catalina.UserDatabase"
              description="User database that can be updated and saved"
              factory="org.apache.catalina.users.MemoryUserDatabaseFactory"
              pathname="conf/tomcat-users.xml" />
  </GlobalNamingResources>

  <Service name="Catalina">

    <Connector port="8102" protocol="org.apache.coyote.http11.Http11NioProtocol"
               connectionTimeout="20000"
               redirectPort="8443" maxThreads="2000"/>
    <!--
    <Connector port="8443" protocol="org.apache.coyote.http11.Http11AprProtocol"
               maxThreads="150" SSLEnabled="true" >
        <UpgradeProtocol className="org.apache.coyote.http2.Http2Protocol" />
        <SSLHostConfig>
            <Certificate certificateKeyFile="conf/localhost-rsa-key.pem"
                         certificateFile="conf/localhost-rsa-cert.pem"
                         certificateChainFile="conf/localhost-rsa-chain.pem"
                         type="RSA" />
        </SSLHostConfig>
    </Connector>
    -->

    <!-- Define an AJP 1.3 Connector on port 8009 -->
    <!--
    <Connector protocol="AJP/1.3"
               address="::1"
               port="8009"
               redirectPort="8443" />
    -->

    <Engine name="Catalina" defaultHost="localhost">

      <Realm className="org.apache.catalina.realm.LockOutRealm">
        <Realm className="org.apache.catalina.realm.UserDatabaseRealm"
               resourceName="UserDatabase"/>
      </Realm>

      <Host name="localhost"  appBase="webapps"
            unpackWARs="true" autoDeploy="true">

        <Valve className="org.apache.catalina.valves.AccessLogValve" directory="logs"
               prefix="localhost_access_log" suffix=".txt"
               pattern="%h %l %u %t &quot;%r&quot; %s %b" />

      </Host>
    </Engine>
  </Service>
</Server>

```

#### 元素分类

1）顶层元素：Server和Service

Server元素是整个配置文件的根元素，Service元素则代表一个Engine元素以及一组与之相连的Connector元素。

（2）连接器：Connector

Connector代表了外部客户端发送请求到特定Service的接口；同时也是外部客户端从特定Service接收响应的接口。

（3）容器：Engine、Host、Context

容器的功能是处理Connector接收进来的请求，并产生相应的响应。Engine、Host和Context都是容器，但它们不是平行的关系，而是父子关系：Engine包含Host，Host包含Context。一个Engine组件可以处理Service中的所有请求，一个Host组件可以处理发向一个特定虚拟主机的所有请求，一个Context组件可以处理一个特定Web应用的所有请求。

（4）内嵌组件：可以内嵌到容器中的组件。实际上，Server、Service、Connector、Engine、Host和Context是最重要的最核心的Tomcat组件，其他组件都可以归为内嵌组件。

#### 核心组件

1.**Server**

Server元素在最顶层，代表整个Tomcat容器，因此它必须是server.xml中唯一一个最外层的元素。一个Server元素中可以有一个或多个Service元素。

在第一部分的例子中，在最外层有一个Server元素，shutdown属性表示关闭Server的指令；port属性表示Server接收shutdown指令的端口号，设为-1可以禁掉该端口。

Server的主要任务，就是提供一个接口让客户端能够访问到这个Service集合，同时维护它所包含的所有的Service的声明周期，包括如何初始化、如何结束服务、如何找到客户端要访问的Service。

2.**Service**

Service的作用，是在Connector和Engine外面包了一层，把它们组装在一起，对外提供服务。一个Service可以包含多个Connector，但是只能包含一个Engine；其中Connector的作用是从客户端接收请求，Engine的作用是处理接收进来的请求。

在第一部分的例子中，Server中包含一个名称为“Catalina”的Service。实际上，Tomcat可以提供多个Service，不同的Service监听不同的端口，后文会有介绍。

3.**Connector**

Connector的主要功能，是接收连接请求，创建Request和Response对象用于和请求端交换数据；然后分配线程让Engine来处理这个请求，并把产生的Request和Response对象传给Engine。

```xml
<Connector port="8080" protocol="HTTP/1.1" connectionTimeout="20000" redirectPort="8443" />
<Connector port="8009" protocol="AJP/1.3" redirectPort="8443" />
```

​		通过配置第1个Connector，客户端可以通过8080端口号使用http协议访问Tomcat。其中，protocol属性规定了请求的协议，port规定了请求的端口号，redirectPort表示当强制要求https而请求是http时，重定向至端口号为8443的Connector，connectionTimeout表示连接的超时时间。

4.**Engine**

Engine组件在Service组件中有且只有一个；Engine是Service组件中的请求处理组件。Engine组件从一个或多个Connector中接收请求并处理，并将完成的响应返回给Connector，最终传递给客户端。

```xml
 <Engine name="Catalina" defaultHost="localhost">
```

name属性用于日志和错误信息，在整个Server中应该唯一。defaultHost属性指定了默认的host名称，当发往本机的请求指定的host名称不存在时，一律使用defaultHost指定的host进行处理；因此，defaultHost的值，必须与Engine中的一个Host组件的name属性值匹配。

5.**Host**

每个Host组件代表Engine中的一个虚拟主机，Host组件代表的虚拟主机，对应了服务器中一个网络名实体(如”www.test.com”，或IP地址”116.25.25.25”)

```xml
<Host name="localhost" appBase="webapps" unpackWARs="true" autoDeploy="true">
```

* name属性：指定虚拟主机的主机名，一个Engine中有且仅有一个Host组件的name属性与Engine组件的defaultHost属性相匹配；一般情况下，主机名需要是在DNS服务器中注册的网络名，但是Engine指定的defaultHost不需要，原因在前面已经说明。
* unpackWARs：指定了是否将代表Web应用的WAR文件解压；如果为true，通过解压后的文件结构运行该Web应用，如果为false，直接使用WAR文件运行Web应用。

6.**Context**

Context是Host的子容器，每个Host中可以定义任意多的Context元素。Context元素代表在特定虚拟主机上运行的一个Web应用。

```xml
<Context docBase="D:\Program Files\app1.war" reloadable="true"/>
```

docBase指定了该Web应用使用的WAR包路径，或应用目录。reloadable属性指示tomcat是否在运行时监控在WEB-INF/classes和WEB-INF/lib目录下class文件的改动。如果值为true，那么当class文件改动时，会触发Web应用的重新加载。

#### 整体关系

核心组件之间的整体关系，在上一部分有所介绍，这里总结一下：

Server元素在最顶层，代表整个Tomcat容器；一个Server元素中可以有一个或多个Service元素。

Service在Connector和Engine外面包了一层，把它们组装在一起，对外提供服务。一个Service可以包含多个Connector，但是只能包含一个Engine；Connector接收请求，Engine处理请求。

Engine、Host和Context都是容器，且 Engine包含Host，Host包含Context。每个Host组件代表Engine中的一个虚拟主机；每个Context组件代表在特定Host上运行的一个Web应用。

#### 监听器

```xml
  <Listener className="org.apache.catalina.startup.VersionLoggerListener" />
  <Listener className="org.apache.catalina.core.AprLifecycleListener" SSLEngine="on" />
  <Listener className="org.apache.catalina.core.JasperListener" />
  <Listener className="org.apache.catalina.core.JreMemoryLeakPreventionListener" />
  <Listener className="org.apache.catalina.mbeans.GlobalResourcesLifecycleListener" />
  <Listener className="org.apache.catalina.core.ThreadLocalLeakPreventionListener" />
```

- VersionLoggerListener：当Tomcat启动时，该监听器记录Tomcat、Java和操作系统的信息。该监听器必须是配置的第一个监听器。
- AprLifecycleListener：Tomcat启动时，检查APR库，如果存在则加载。APR，即Apache Portable Runtime，是Apache可移植运行库，可以实现高可扩展性、高性能，以及与本地服务器技术更好的集成。
- JasperListener：在Web应用启动之前初始化Jasper，Jasper是JSP引擎，把JVM不认识的JSP文件解析成java文件，然后编译成class文件供JVM使用。
- JreMemoryLeakPreventionListener：与类加载器导致的内存泄露有关。
- GlobalResourcesLifecycleListener：通过该监听器，初始化< GlobalNamingResources>标签中定义的全局JNDI资源；如果没有该监听器，任何全局资源都不能使用。< GlobalNamingResources>将在后文介绍。
- ThreadLocalLeakPreventionListener：当Web应用因thread-local导致的内存泄露而要停止时，该监听器会触发线程池中线程的更新。当线程执行完任务被收回线程池时，活跃线程会一个一个的更新。只有当Web应用(即Context元素)的renewThreadsWhenStoppingContext属性设置为true时，该监听器才有效。



### Tomcat的连接数与线程数

> https://www.cnblogs.com/kismetv/p/7806063.html
>

Connector的主要功能，是接收连接请求，创建Request和Response对象用于和请求端交换数据；然后分配线程让Engine（也就是Servlet容器）来处理这个请求，并把产生的Request和Response对象传给Engine。当Engine处理完请求后，也会通过Connector将响应返回给客户端。Connector是Tomcat处理请求的主干。

#### NIO、BIO、APR

Connector在处理HTTP请求时，会使用不同的protocol。不同的Tomcat版本支持的protocol不同，其中最典型的protocol包括BIO、NIO和APR。

（1）BIO：BIO(Blocking I/O)，顾名思义，即阻塞式I/O操作，表示Tomcat使用的是传统的Java I/O操作(即java.io包及其子包)。Tomcat在默认情况下，是以bio模式运行的。遗憾的是，就一般而言，bio模式是三种运行模式中性能最低的一种。BIO配置采用默认即可。

（2）NIO：NIO(New I/O)，是Java SE 1.4及后续版本提供的一种新的I/O操作方式(即java.nio包及其子包)。Java nio是一个基于缓冲区、并能提供非阻塞I/O操作的Java API，因此nio也被看成是non-blocking I/O的缩写。它拥有比传统I/O操作(bio)更好的并发运行性能。

 （3）APR：APR(Apache Portable Runtime/Apache可移植运行时)，是Apache HTTP服务器的支持库。你可以简单地理解为:Tomcat将以JNI的形式调用 Apache HTTP服务器的核心动态链接库来处理文件读取或网络传输操作，从而大大地提高 Tomcat对静态文件的处理性能。 

#### Connector的功能参数

**1.acceptCount**

accept队列的长度；当accept队列中连接的个数达到acceptCount时，队列满，进来的请求一律被拒绝。默认值是100。acceptCount的设置，与应用在连接过高情况下希望做出什么反应有关系。如果设置过大，后面进入的请求等待时间会很长；如果设置过小，后面进入的请求立马返回connection refused。

**2.maxConnections**

Tomcat在任意时刻接收和处理的最大连接数。当Tomcat接收的连接数达到maxConnections时，Acceptor线程不会读取accept队列中的连接；这时accept队列中的线程会一直阻塞着，直到Tomcat接收的连接数小于maxConnections。如果设置为-1，则连接数不受限制。

默认值与连接器使用的协议有关：NIO的默认值是10000，APR/native的默认值是8192，而BIO的默认值为maxThreads（如果配置了Executor，则默认值是Executor的maxThreads）。

在windows下，APR/native的maxConnections值会自动调整为设置值以下最大的1024的整数倍；如设置为2000，则最大值实际是1024。

maxConnections的设置与Tomcat的运行模式有关。如果tomcat使用的是BIO，那么maxConnections的值应该与maxThreads一致；如果tomcat使用的是NIO，maxConnections值应该远大于maxThreads。

**3.maxThreads**

请求处理线程的最大数量。默认值是200（Tomcat7和8都是的）。如果该Connector绑定了Executor，这个值会被忽略，因为该Connector将使用绑定的Executor，而不是内置的线程池来执行任务。

maxThreads规定的是最大的线程数目，并不是实际running的CPU数量；实际上，maxThreads的大小比CPU核心数量要大得多。这是因为，处理请求的线程真正用于计算的时间可能很少，大多数时间可能在阻塞，如等待数据库返回数据、等待硬盘读写数据等。因此，在某一时刻，只有少数的线程真正的在使用物理CPU，大多数线程都在等待；因此线程数远大于物理核心数才是合理的。

换句话说，Tomcat通过使用比CPU核心数量多得多的线程数，可以使CPU忙碌起来，大大提高CPU的利用率。

但maxThreads的值并不是越大越好，如果maxThreads过大，那么CPU会花费大量的时间用于线程的切换，整体效率会降低。

#### 线程池Executor

Executor元素代表Tomcat中的线程池，可以由其他组件共享使用；要使用该线程池，组件需要通过executor属性指定该线程池。

```xml
<Executor name="tomcatThreadPool" namePrefix ="catalina-exec-" maxThreads="150" minSpareThreads="4" />
<Connector executor="tomcatThreadPool" port="8080" protocol="HTTP/1.1" connectionTimeout="20000" redirectPort="8443" acceptCount="1000" />
```

Executor的主要属性包括：

- name：该线程池的标记
- maxThreads：线程池中最大活跃线程数，默认值200（Tomcat7和8都是）
- minSpareThreads：线程池中保持的最小线程数，最小值是25
- maxIdleTime：线程空闲的最大时间，当空闲超过该值时关闭线程（除非线程数小于minSpareThreads），单位是ms，默认值60000（1分钟）
- daemon：是否后台线程，默认值true
- threadPriority：线程优先级，默认值5
- namePrefix：线程名字的前缀，线程池中线程名字为：namePrefix+线程编号

#### 查看服务器状态

1.连接数

假设Tomcat接收http请求的端口是8083，则可以使用如下语句查看连接情况：

```
netstat –nat | grep 8083
```

2.线程

查看进程状态

```
ps –e | grep java
```

看到该进程内有多少个线程

```
ps –o nlwp 端口号
```

获得真正在running的线程数量

```
ps -eLo pid ,stat | grep 端口号 | grep running | wc -l
```



