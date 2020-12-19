# Servlet

------

> 阿里云大学Servlet课程
>

[TOC]

## 1.什么是Servlet

  Servlet是JavaWeb的三大组件之一，它属于动态资源。Servlet的作用是处理请求，服务器会把接受到的请求交给Servlet来处理，在Servlet中通常需要：

* 接受请求数据；

* 处理请求；

* 完成响应。

  例如客户端发出登录请求，或者输出注册请求，这些请求都应该有Servlet来完成处理！Servlet需要我们自己来编写，每个Servlet必须实现javax.servlet.Servlet接口。
  
## 2.实现Servlet的方式

实现Servlet有三种方式：

* 实现javax.servlet.Servlet接口；

* 继承javax.servlet.GenericServlet类；

* 继承javax.servlet.http.HttpServlet类；

  通常我们会去继承HttpServlet类来完成我们的Servlet,但学习Servlet还要从javax.servlet.Servlet接口开始学习。

## 3.生命周期方法： 

* void init(ServletConfig)：出生之后（1次）
* void service(ServiceRequest request,ServletResponse response)：每次处理请求时都会被调用；
* void destroy()：临死之前（1次）；

```java
public class AServlet implements Servlet {    
//它是生命周期方法
	 //它会在Servlet被销毁之前调用，并且它只会被调用一次！
	@Override
	public void destroy() {
		System.out.println("destory()...");
	}
	//可以用来获取Servlet的配置信息 
	@Override
	public ServletConfig getServletConfig() {
		System.out.println("getServletConfig()...");
		return null;
	}
	//获取Servlet的信息(用户调用，基本没用)
	@Override
	public String getServletInfo() {
		System.out.println("getServletInfo()...");
		return "我是一个快乐的Servlet";
	}
	 //它是生命周期方法
	 //它会在Servlet对象创建之后马上执行，并且只执行一次！（出生之后）
	@Override
	public void init(ServletConfig servletConfig) throws ServletException {
		System.out.println("init()...");
	}
	 //它是生命周期方法
	 //它会被调用多次
	 //每次处理请求都是在调用这个方法
	@Override
	public void service(ServletRequest request, ServletResponse response) throws ServletException, IOException {
		System.out.println("service()...");
}
}
```

```xml
<!--web.xml-->
<servlet>
     <servlet-name>aservlet</servlet-name>
     <servlet-class>com.edu.cn.AServlet</servlet-class>
  </servlet>
  
  <servlet-mapping>
      <servlet-name>aservlet</servlet-name>
      <url-pattern>/AServlet</url-pattern>
  </servlet-mapping>
<!--也可以在类中加@WebServlet("/AServlet")来设置-->
```

**特性：**

* 单例，一个类只有一个对象；当然可能存在多个Servlet类
* 线程不安全的，所以它的效率是高的

**注：Servlet类由我们来写，但对象由服务器来创建，并且有服务器来调用相应的方法。**

## 4.ServletConfig

1个ServletConfig对象，对应一段wen.xml中Servlet的配置信息！

**API：**

* String getServletName()，获取Servlet上下文对象
* ServletContext getServletContext()：获取上下文对象！
* String getInitParameter(String name)：通过名称获取指定初始化参数的值
* Enumeration getInitParameterNames()：获取所有初始化参数的名称

```xml
<servlet>
   <servlet-name>BServlet</servlet-name>
   <servlet-class>edu.cn.BServlet</servlet-class>
   <init-param>
      <param-name>name</param-name>
      <param-value>zhangsan</param-value>
   </init-param>
   <init-param>
       <param-name>age</param-name>
       <param-value>18</param-value>
   </init-param>
</servlet>
<!-- 使用注解方式
      @WebServlet(value = "/BServlet",
      initParams = {@WebInitParam(name="name",value="zhangsan"),
      @WebInitParam(name="age",value="18")})
-->
```

```java
@Override
	public void init(ServletConfig servletConfig) throws ServletException {
		System.out.println("init()...");
        //获取初始化参数
        System.out.println(servletConfig.getInitParameter("p1"));
        System.out.println(servletConfig.getInitParameter("p2"));
        //获取所有初始化参数名称
        Enumeration e = servletConfig.getInitParameterName();
        while(e.hasMoreElements()) {
            System.out.println(e.nextElement());
        }
}
```

## 5.HttpServlet

```java
HttpServlet extends GenderServlet {
    void service(ServletRequest,ServletResponse) //生命周期方法
    //强调两个参数为http协议相关的类型，
    //调用本来的servlet(HttpServletRequest,HttpServletResponse)
        
    void service(HttpServletRequest,HttpServletResponse)//参数已经是Http协议相关的，使用起来更加方便。
    //它会通过request得到当前请求的请求方式，例如：GET或POST
    //根据请求方式再调用doGet()或doPost()方法
        
     void doGet(...)  //重写
     void doPsot(...) //重写
}
```

**注：**

* 不要在Servlet中创建成员！创建局部变量即可！
* 可以创建无状态成员！
* 可以创建有状态的成员，但状态必须为只读的！

## 6.url-pattern

<url-pattern>是<servlet-mapping>的子元素，用来指定Servlet的访问路径，即URL。它必须是以"/"开头。

1. 可以在<servlet-mapping>中给出多个<url-pattern>，例如：

   ```xml
   <servlet-mapping>
       <servelt-name>AServlet</servelt-name>
       <url-pattern>/AServlet</url-pattern>
       <url-pattern>/BServlet</url-pattern>
   </servlet-mapping>
   ```

   那么这说明一个Servlet绑定了两个URL，无论访问/AServlet还是/BSerlvlet，访问的都是AServlet。

2. 还可以在<url-pattern>中使用通配符，所谓通配符就是星号“*”，星号可以匹配任何URL前缀或者后缀，使用通配符可以命名一个Servlet绑定一组URL，例如：
```xml
<url-pattern>/servlet/*<url-pattern>:/servlet/a、/servlet/b，都匹配/servlet/*；
<url-pattern> *.do<url-pattern>:/abc/def/ghi.do、/a.do，都匹配*.do；
<url-pattern>/*<url-pattern>:匹配所有URL；
```

注意：通配符要么为前缀，要么为后缀，不能出现在URL中间位置，也不能只有通配符。例如：/*.do就是错误的，因为星号出现在URL的中间位置上了。 * . * 也是不对的，因为一个URL中最多只能出现一个通配符。

通配符是一种模糊匹配URL的方式，如果存在更具体的<url-pattern>，那么访问路径会去匹配具体的<url-pattern>。

## 7.ServletContext（重要）

> 一个项目只有一个ServletContext对象
>
> 我们可以在N多个Servlet中来获取这个唯一的对象，使用它可以给多个Servlet传递数据！

##### a.ServletContext概述

服务器会为每个应用创建一个ServletContext对象；

* ServletContext对象的创建是在服务器启动时完成的；
* ServletContext对象的销毁是在服务器关闭时完成的。

ServletContext对象的作用是在<font color="lighblue">整个web应用的动态资源之间共享数据</font>！

##### b.获取ServletContext

在Servlet中获取ServletConetxt对象：

* 在void init(ServletConfig config)中：ServletContext context = config.getServletContext();，
* ServletConfig类的etServletConfigtext()方法可以用来获取ServletContext对象；

在GenericServlet或HttpServlet中获取ServletContext对象：

* GenericServlet类有getServietContext()方法，所以可以直接使用this.getServletContext()来获取；

##### c.域对象的功能

> 代码参考EclipseIDE中day09_2

**功能**：Servlet中传递数据

* 域对象必须要有存数据功能
* 域对象必须要有取数据功能
* 域对象内部其实有一个Map

ServletContext是JavaWeb<font color="lighblue" style="font-weight:bold;">四大域对象</font>之一：

* PageContext
* ServletRequest
* HttpSession
* ServletContext

所有域对象都有存取数据的功能，因为域对象内部有一个Map，用来存储数据，下面是**ServletContext**对象来操作数据的方法：

* <font color="lighblue" >void setAttribute(String name,Object value)</font>：用来存储一个对象，也可以称之为存储一个域属性，例如：servletContext.setAttribute("xxx","XXX")，在ServletContext中保存了一个域属性，域属性名称为xxx，域属性的值为XXX。请注意，如果多次调用该方法，并且使用相同的name，那么会覆盖上一次的值，这一特性与Map相同；
* <font color="lighblue" >Object getAttribute(String name)</font>：用来获取ServletContext中的数据，当前在获取之前需要先去存储才行，例如：String value = (String)servletContext.getAttribute("xxx");，获取名为xxx的域属性；
* <font color="lighblue" >void removeAttribute(String name)</font>:用来移除ServletContext中的域属性，如果参数name指定的域属性不存在，那么本方法什么都不做；
* <font color="lighblue" >Enumeration getAttributeNames()</font>：获取所有域属性的名称；

##### d.获取应用初始化参数

* Servlet也可以获取初始化参数，但它是局部的参数；也就是说，一个Servlet只能获取自己的初始化参数，不能获取别人的，即初始化参数只为一个Servlet准备！

* 可以配置<font color="lighblue" >公共的初始化参数</font>，为所有Servlet而用！这需要使用ServletContext才能使用！

* 可以使用ServletContext来获取在web.xml文件中配置的应用初始化参数！注意，应用初始化参数与Servlet初始化参数不同：

  ```xml
  <web-app>
   <context-param>
         <param-name>context-param</param-name>
         <param-value>context-value</param-value>
   </context-param>
  </web-app>
  ```

  ```java
  //演示ServletContext获取公共的初始化参数
  public void doGet(...) {  
     /* 1.得到ServletContext
        2.调用它getInitParameter(String)得到初始化参数*/
      ServletContext app = this.getServletContext();
      String value = app.getInitParameter("context-param");
      System.out.println(value); //输出结果：context-value
  }
  ```

##### e.获取资源路径

```java
//使用ServletContext获取资源路径
public void doGet(...) ...{
    String path = this.getServletContext().getRealPath("/index.jsp");
    System.out.println(path);// D:\JAVA\webapps\day09_2\index.jsp
    //获取资源的路径后，再创建出输入流对象
    InputStream input = this.getServletContext().getResourceAsStream("/index.jsp");
    //获取当前路径下所有资源的路径
    Set<String> paths = this.getServletContext().getResourcePaths("/WEB-INF");
    System.out.println(paths);
}
```

##### f.访问量统计

一个项目所有的资源被访问都要对访问量进行累加

创建一个int类型的变量，用来保存访问量，然后把它保存到ServletContext的域中，这样可以保存所有的Servlet都可以访问到。

* 最初时，ServletContext中没有保存访问量相关的属性；

* 当本站第一次被访问时，创建一个变量，设置其值为1；保存到ServletContext中；

* 当以后的访问时，就可以从ServletContext中获取这个变量，然后在其基础之上加1。

* 获取ServletContext对象，查看是否存在名为context的属性，如果存在，说明不是第一次访问，如果不存在，说明是第一次访问；

  * 第一次访问：调用ServletContext的setAttribute()传递的一个属性，名为count，值为1；
  * 第2~n次访问：调用ServletContext的getAttribute()方法获取原来的访问量，给访问量加1，再调用ServletContext的setAttribute()方法完成设置。

  ```java
  public void doGet(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
  		/*
  		 * 1.获取ServletContext对象
  		 * 2.从ServletContext对象汇总获取名为count的属性
  		 * 3.如果存在：给访问量加1，然后在保存回去;
  		 * 4.如果不存在：说明是第一次访问，向ServletContext中保存名为count的属性，值为1
  		 */
  		ServletContext app = this.getServletContext();
  		Integer count = (Integer)app.getAttribute("count");
  		if(count == null) {
  			app.setAttribute("count", 1);
  		} else {
  			app.setAttribute("count", count+1);
  		}
  		/*
  		 * 向浏览器输出
  		 *    需要使用响应对象
  		 */
  		PrintWriter pw = response.getWriter();
  		pw.print("<h1>"+count+"</h1>");
  	}
  ```

## 8.BeanServlet

> 代码参考day09_5 

* 我们希望在一个Servlet中可以有多个请求处理方法！
* 客户端发送请求时，必须多给出一个参数，用来说明要调用的方法
* 请求处理方法的签名必须与service相同，即返回值和参数，以及声明的异常都相同！
* 请求客户端必须传递名为method的参数
* 请求转发和重定向

## 9.JSP和Servlet

JSP是一种特殊的Servlet

* <font color="lighblue" ><%...%></font>:java代码片段（常用），用于定义0~N条Java语句！方法内能写什么，它就可以放什么
* <font color="lighblue" ><%=...%></font>:java表达式，用于输出（常用），用于输出一条表达式(或变量)的结果。response.getWriter().print(...)；这里能放什么它就能放什么
* <font color="lighblue" ><%!...%></font>:声明，用来创建类的成员变量和成员方法(基本不用，但容易考到)，类体中可以放什么，它就可以放什么

```jsp
<form action="/day09_5/CServlet" method="post">
    整数1：<input type="text" name="num1"/></br>
    整数2：<input type="text" name="num2"/></br>
  <input type="submit" value="提交"/>
</form>
```

```java
protected void doPost(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
        //获取参数
		String s1 = request.getParameter("num1");
		String s2 = request.getParameter("num2");
		//转换成int类型
		int num1 = Integer.parseInt(s1);
		int num2 = Integer.parseInt(s2);
		int sum = num1+num2;
		//把结果保存到request域中
		request.setAttribute("result", sum);
		//转换到result.jsp中
		request.getRequestDispatcher("/result.jsp").forward(request, response);
	}
```

```jsp
<body>
  <%
     Integer result = (Integer)request.getAttribute("result");
  %>
  <%=result %>
</body>
```

## 10.过滤器

当需要限制用户访问某些服务器上的资源时，需要提前实现某些拦截功能的时候，就可以使用过滤器完成。可以定义多个过滤器。

```java
public class LoginFilter implements Filter {

    @Override
    public void init(FilterConfig filterConfig) throws ServletException {
        System.out.println("----LoginFilter初始化-----");
    }

    @Override
    public void doFilter(ServletRequest servletRequest, ServletResponse servletResponse, FilterChain filterChain) throws IOException, ServletException {
        System.out.println("----LoginFilter执行-----");
        //设置编码格式
        servletRequest.setCharacterEncoding("UTF-8");
        servletResponse.setCharacterEncoding("UTF-8");
        //放行
        filterChain.doFilter(servletRequest, servletResponse);
    }

    @Override
    public void destroy() {
        System.out.println("----LoginFilter销毁-----");
    }
}
```

```xml
<filter>
       <filter-name>loginFilter</filter-name>
       <filter-class>com.edu.servlet.LoginFilter</filter-class>
   </filter>
    
    <filter-mapping>
        <filter-name>loginFilter</filter-name>
        <url-pattern>/LoginServlet</url-pattern><!--拦截请求-->
    </filter-mapping>
<!--注解方式，在LoginFilter类上添加
@WebFilter("/LoginServlet")-->
```

