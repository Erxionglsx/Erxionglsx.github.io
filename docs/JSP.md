# JSP入门

> Cookie、验证码、HttpSession、pageContext、EL表达式、标签库

------

[TOC]

#### 1.JSP和Servlet

JSP是一种特殊的Servlet

* <font color="lighblue" ><%...%></font>:java代码片段（常用），用于定义0~N条Java语句！方法内能写什么，它就可以放什么

  ```jsp
  <%--变量声明--%>
  <%
      int code = 100;
      String name = "向航华";
  %>
  <%--变量输出--%>
  <%
      out.println(code);
      out.println(name);
  %>
  ```

* <font color="lighblue" ><%=...%></font>:java表达式，用于输出（常用），用于输出一条表达式(或变量)的结果。response.getWriter().print(...)；这里能放什么它就能放什么

* <font color="lighblue" ><%!...%></font>:声明，用来创建类的成员变量和成员方法(基本不用，但容易考到)，类体中可以放什么，它就可以放什么

**Request域**

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
		//转换到result.jsp中 请求转发
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

**Session域**

```jsp
<form action="<%=request.getContextPath()%>/LoginServlet" method="post">
           <table>
               <tr>
                   <td>用户名:<input type="text" name="username"></td>
               </tr>
               <tr>
                   <td>密码：<input type="text" name="password"></td>
               </tr>
               <tr>
                   <td><input type="submit" value="登录"></td>
               </tr>
           </table>
       </form>
```

```java
@WebServlet("/LoginServlet")
public class LoginServlet extends HttpServlet {
    @Override
    protected void doPost(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        HttpSession session = req.getSession(false);
        String username = req.getParameter("username");
        String password = req.getParameter("password");
        session.setAttribute("username",username);
        session.setAttribute("password",password);
        //重定向
        resp.sendRedirect(req.getContextPath()+"/result.jsp");
    }
}
```

```jsp
<html>
<head>
    <title>Title</title>
</head>
<body>
  欢迎来到网站！
   用户名：<%=session.getAttribute("username")%>
   密码：<%=session.getAttribute("password")%>
</body>
</html>
```

#### 2.Cookie和验证码

> 代码参考day09_5和day09_3

1. ##### Http协议和Cookie

   * Cookie是HTTP协议制定的，先由服务器保存Cookie到浏览器，再下次浏览器请求服务区时把上一次请求得到Cookie再归还给服务器
   * 由服务器创建保存到客户端浏览器的一个键值对
   * 当浏览器请求服务器时，会把该服务器保存的Cookie随请求发送给服务器，浏览器归还Cookie的请求头
     * 1个Cookie最大4KB
     * 1个服务器最多向1个浏览器保存20个Cookie
     * 1个浏览器最多可以保存300个Cookie

2. ##### JavaWeb中使用Cookie

   * 使用<font color="lighblue" >response.addCookie()</font>方法向浏览器保存Cookie
   * 使用<font color="lighblue" >response.getCookie()</font>方法获取浏览器归还的Cookie

   Cookie第一例：

   注：Cookie是不能跨浏览器的

3. **Cookie详解**
   
   * <font color="lighblue" >Cookies的maxAge</font>（掌握）：即Cookie可保存的最大时长，以秒为单位。
     * maxAge>0：浏览器会把Cookie保存到客户机硬盘上，有效时长为maxAge的值决定。
     * maxAge<0：Cookie只在浏览器内存中存在，当用户关闭浏览器时，浏览器进程结束，同时Cookie也就死亡了。
     * maxAge=0：浏览器会马上删除这个Cookie
   * <font color="lighblue" >Cookie的path</font>(了解):
     * Cookie的path并不是设置这个Cookie在客户端的保存路径
     * Cookie的path由服务器创建Cookie时设置
     * 当浏览器访问服务器某个路径时，需要归还哪些Cookie给服务器？这由Cookie的path决定。
     * 当浏览器访问服务器的路径，如果包含某个Cookie的路径，那么就会归还这个Cookie。
     * Cookie的path默认值，当前访问路径的父路径，例如在访问/day11_1/jsp/a.jsp时，响应的cookie，那个这个cookie的默认path为/day11_1/jsp/

```java
Cookie cookie = new Cookie("uname", username);//创建Cookie
cookie.setMaxAge(60*60*24);//设置cookie命长24h
response.addCookie(cookie);
```

```jsp
<%
  /*
  读取名为unam的Cookie!
  如果为空显示:""
  如果不为空显示：Cookie的值
  */
  String uname="";
  Cookie[] cs = request.getCookies();//获取请求中所有的cookie
  if(cs != null){//如果存在cookie
	  for(Cookie c:cs) {//循环遍历所有的cookie
		  if("uname".equals(c.getName())){//查找名为uname的cookie
			  uname = c.getValue();//获取这个cookies的值，给uname这个变量
		  }
	  }
  }
%>

 用户名：<input type="text" name="username" value="<%=uname%>"/></br> 
```

#### 3.HttpSession

1. ##### HttpSession概述

   * HttpSeesion是由JavaWeb提供的，用来会话跟踪的类，session是服务器端对象，保存至服务器端
   * HttpSession是Servlet三大域对象之一(request、session、application(ServlettContext))，所有它也有setAttribute()、getAttribute()、removeAttribute()
   * HttpSession底层依赖Cookie，或是URL重写

2. ##### HttpSession的作用

   * **会话范围**：会话范围是某个用户从首次访问服务器开始，到该用户<font color="lighblue" >关闭浏览器结束</font>

     * 会话：一个用户对服务器的多次连贯性请求！所谓连贯性请求，就是该用户多次请求中间没有关闭浏览器！

   * 服务器会为每个客户端创建一个session对象，session就好比客户的服务器端的账户，它们被服务器保存到一个Map中，这个Map被称之为session缓存！

     * <font color="lighblue" >Servlet中得到session对象</font>：HttpSession session = request.getSession();
     * <font color="lighblue" >Jsp中得到session对象</font>：session是jsp内置对象之下，不用创建就可以直接使用！

   * session域相关方法：

     * void setAttribute(String name,Object value);
     * Object getAttribute(String name);
     * void removeAttribute(String name);

   * **通常用session保存登录信息，判断是否已经登录，来防止非法访问！**

     ```jsp
     <% 
         session.setAttribute("aaa","AAA");
     %>
     
     <%
          String s = (Stirng)session.getAttribute("aaa");
     %>
     <%=s %>  <!--结果：AAA-->
     ```


#### 4.page指令

1. pageEncoding和contentType

   * <font color="lighblue" >pageEncoding</font>：它指定当前jsp页面的编码，只要不说谎，就不会有乱码，在服务器要把jsp编译成.java时需要使用pageEncoding!

   * <font color="lighblue" >contentType</font>：它表示添加一个响应头：Content-Type等同于response.setConetentType("text/html;charset="utf-8");

   * 如果两个属性只提供一个，那么另一个的默认值为设置的那一个。

   * 如果两个属性都没有设置，那么默认为iso

   * ```java
     <%@ page language="java" contentType="text/html; charset=UTF-8"  pageEncoding="UTF-8"%>
     ```

2. import：导包，可以出现多次

   ```jsp
   <%@ page import="java.util.ArrayList"%>
   ```

3. errorPage和isErrorPage

   * <font color="lighblue" >errorPage</font>：当前页面如果抛出异常，那么要转发到哪一个页面，由errorPage来指定
   * <font color="lighblue" >isErrorPage</font>：它指定当前页面是否为处理错误的页面！当该属性为true时，这个页面会设置状态码为500！而且这个页面可以使用9大内置对象中的exception

   ```jsp
   <%@ page language="java" contentType="text/html; charset=UTF-8" pageEncoding="UTF-8" errorPage="errorPage.jsp"%>
   ```

   ```jsp
   <%@ page language="java" contentType="text/html; charset=UTF-8" pageEncoding="UTF-8" isErrorPage="true"%>
   ```

   web.xml中设置出错对应跳转界面

   ```xml
   <error-page>
      <error-code>404</error-code>
      <location>/error/errorPage.jsp</location>
   </error-page>
   <error-page>
      <error-code>500</error-code>
      <location>/error/errorPage.jsp</location>
   </error-page>
   <error-page>
      <exception-type>java.lang.RuntimeException</exception-type>
      <location>/index.jsp</location>
   </error-page>
   ```

#### 5.九大内置对象

* <font color="lighblue" >out</font>：jsp的输出流，用来向客户端响应
* <font color="lighblue" >page</font>：当前jsp对象，它的引用类型是Object，即真身中的如下代码：Object page = this;
* <font color="lighblue" >config</font>：它对应真身中的ServletCongit对象
* <font color="lighblue" >pageContext</font>：页面上下文对象
* <font color="lighblue" >request</font>：HttpServletRequest类的对象
* <font color="lighblue" >response</font>：HttpServletResponse类的对象
* <font color="lighblue" >exception</font>：Throwable，只有在错误页面中可以使用这个对象
* <font color="lighblue" >session</font>：HttpSession类的对象
* <font color="lighblue" >application</font>：ServletContext类的对象

#### 6.pageContext

Servlet中有三大域，而JSP中有四大域，它就是最后一个域对象

* <font color="lighblue" >ServletContext</font>：整个应用程序
* <font color="lighblue" >session</font>：整个会话
* <font color="lighblue" >request</font>：一个请求链
* <font color="lighblue" >pageContext</font>：一个jsp页面，这个域是在当前jsp页面和当前页面中使用的标签之间共享数据
  * 域对象
  * 代理其他域：pageContext.setAttribute("xxx","XXX",PageContext.SESSION_SCOPE);
  * 全域查找：pageContext.findAttribute("xxx");从小到大，依次查找
  * 获取其他8个内置对象

#### 7.EL表达式

![](https://note.youdao.com/yws/api/personal/file/77545D9098404F7886A5910B829F429F?method=download&shareKey=a4d8835d97878cc433cf38ca1c23054c)

1. EL是JSP内置的表达式语言！
   * jsp2.0开始，不让再使用java脚本，而是使用el表达式和动态标签替代java脚本
   * EL替代的是<%=...%>，也就是说，EL只能做输出！
   
2. EL表达式来读取**四大域**
   * ${xxx},全城查找名为xxx的属性，如果不存在，输出空字符串，而不是null。
   
   ```jsp
   <% 
      pageContext.setAttribute("username","pageContext_XXX");
      request.serAttribute("username","request_XXX"); 
      session.serAttribute("username","session_XXX"); 
      application.serAttribute("username","application_XXX"); 
   %>
   
   ${username } <!--全域查找：pageContext_XXX-->
   ${pageScope.username}<!--指定域查找：pageContext_XXX-->
   <%=request.getAttribute("username")%><!--request_XXX-->
   ${requestScope.username}<!--指定域查找：request_XXX-->
   ${sessionScope.username}<!--指定域查找：session_XXX-->
   ${applicationScope.username}<!--指定域查找：application_XXX-->
   ```
   
3. **param、paramValues**

   ```jsp
   <form action="success.jsp" method="post">
           用户名：<input type="text" name="username" /><br>
           密码：<input type="text" name="password"> <br>
           <input type="submit" value="登录">
           <select name="fruit">
                   <option value="apple">苹果</option>
                   <option value="banana">香蕉</option>
                   <option value="pear">梨</option>
           </select>
   </form>
   ```

   ```jsp
   用户名：<%=request.getParameter("username") %><br>
   密码：<%=request.getParameter("password") %><br>
   <h1>EL表达式输出</h1>
   用户名：${param.username}<br>
   密码：${param.password}<br>
   水果：${paramValues.fruit[0]}/${paramValues.fruit[1]}/${paramValues.fruit[2]}
   水果：${param.fruit}
   ```

4. **EL访问集合**

   ```jsp
   <%--List集合测试--%>
      <%
        List<String> list = new ArrayList<>();
        list.add("hello");
        list.add("world");
        pageContext.setAttribute("list",list);
      %>
      ${list[0]}/${list[1]}
   
   <%--Map集合测试--%>
       <%
           Map<Long,String> map = new HashMap<>();
           map.put(1L,"张三");
           map.put(2L,"李四");
           map.put(3L,"王五");
           pageContext.setAttribute("map",map);
       %>
   ```

#### 8.重定向、请求转发

```jsp
<body>
    <!--重定向-->
    <%response.sendRedirect(reqest.getContextPath()+"/result.jsp");%>
</body>
```

```jsp
<body>
    <!--请求转发-->
   request.getRequestDispatcher("/result.jsp").forward(request, response);
</body>
```

### 标签库

#### core标签库 --> c标签

#### 1.set、out标签

<font color="lighblue" >头标签添加：<%@ taglib uri="http://java.sun.com/jsp/jstl/core" prefix="c" %></font>

<font color="lighblue" >添加标签来识别el表达式：<%@page isELIgnored="false" %></font>

* <font color="lighblue" ><c: out>:输出</font>
  * value:可以是字符串常量，也可以是EL表达式
  * default:当要输出的内容为null时，会输出default指定的值
  * escapeXml：默认值为true，表示转义

 ```jsp
  <%
    request.setAttribute("code", "<script>alert('hello')</script>");
  %>
  <c:out value="${code } "/>
  <!--结果：<script>alert('hello')</script> -->
 ```

* <font color="lighblue" ><c: set>：设置（创建域的属性）</font>

  * var:变量
  * value:变量值，可以是EL表达式
  * scope:域，默认为page，可选值page、session、application

  ```jsp
  <c:set var="code" value="<script>alert('hello')</script>" scope="request"/>
  <c:out value="${code } "/>
  <!--结果：<script>alert('hello')</script> -->
  ```

#### 2.remove标签

* <remove>:删除域变量
  * var:变量名
  * scope:如果不给出scope，表示删除所有域中的该名称的变量：如果指定了域，那么只删除该域的变量。

#### 3.url标签

* value:指定一个路径，它会在路径前面自动添加项目名。

  <><c: url value="/index.jsp">,它会输出/day13_1/index.jsp

* 子标签：<c: prarm>,用来给url后面添加参数，例如：

  ```jsp
  <c:url value="/index.jsp"><!--等同于${pageContext.request.contextPath }/index.jsp-->
     <c:param name="username" value="张三"/> <!--可以对参数进行url编码-->
  </c:url>
  <!--结果为：/day13_1/index.jsp?username=%e5%bc%a0%e4%b8%89-->
  ```

* var:指定变量名，一旦添加了这个属性，那么url标签就不会再输出到页面，而是把生成url保存到域中。

* scope:它与var一起使用，用来保存url。

#### 4.if标签，对应java中的if语句

* <c: if test="布尔类型">....</c: if>，if标签的test属性必须是一个boolean类型的值，如果test的值为true，那么执行if标签的内容，否则不执行。

```jsp
<c:set var="a" value="hello"/>
<c:if test="${not empty a }">
    <c:out value="${a }"/>
</c:if><!--hello-->
```

#### 5.choose标签，对应java中if/else 结构

when标签的test为true时，会执行这个when的内容。当所有的when标签的test都是false是，才执行otherwise标签的内容。

```jsp
<c:set var="soore" value="${param.soore }"/>
<c:if test="${empty soore }">未给出soore的值</c:if>
<c:choose>
     <c:when test="${soore >100 || soore<0 }">错误的分数：${soore }</c:when>
     <c:when test="${soore >=90}">A级</c:when>
     <c:when test="${soore >=80}">B级</c:when>
     <c:when test="${soore >=70}">C级</c:when>
     <c:when test="${soore >=60}">D级</c:when>
     <c:otherwise>E级</c:otherwise>
</c:choose>
<!--
http://localhost:8080/day13_1/index.jsp 未给出soore的值 E级 
http://localhost:8080/day13_1/index.jsp?soore=77 C级
-->
```

#### 6.forEach标签

> 需要导入standard.jar和jstl.jar包

<font color="lighblue" >头标签添加：<%@ taglib uri="http://java.sun.com/jsp/jstl/core" prefix="c" %></font>

<font color="lighblue" >添加标签来识别el表达式：<%@page isELIgnored="false" %></font>

它用来循环遍历数组、集合！

它还可以用来计数方式来循环！

计数方式：

```jsp
for(int i = 1;i<=10;i++){
    .....
}
<!--二者等同-->
<c:forEach var="i" begin="1" end="10">
  ${i} <!--循环输出1-10-->
</c:forEach>
```

属性：

* var：循环变量
* begin：设置循环变量从几开始
* end：设置循环变量到几结束
* step：设置步长！等同于java中的i++，或1+=2，step默认为1

**用来输出数组、集合**

```jsp
 <%
      String[] strs = {"one","two"};
      request.setAttribute("strs", strs);
%>

for(String str : strs){
   .....
}
<!--二者等同-->
<c:forEach items="${strs }" var="str">
      ${str }<br/>
</c:forEach>
```

属性：

* items：指定要循环谁，它可以是一个数组或一个集合
* var：把数组或集合中的每个元素赋值给var指定的变量

循环状态

可以使用varStatus来创建循环状态变量

循环状态变量有如下属性：

* count：循环元素的个数
* index：循环元素的下标
* first：是否为第一个元素
* last：是否为最后一个元素
* current：当前元素

```jsp
<%
      ArrayList<String> list = new ArrayList<String>();
      list.add("一");
      list.add("二");
      list.add("三");
      
      pageContext.setAttribute("list", list);
%>
   <c:forEach items="${list }" var="vs">
       ${vs.index} ${vs.count } ${vs.first } ${vs.last } ${vs.current }<br/>
   </c:forEach>
<!-- 结果为：
0 1 true false 一
1 2 false false 二
2 3 false true 三
-->
```

#### 7.fmt标签

<font color="lighblue" >头标签添加:<%@ taglib prefix="fmt" uri="http://java.sun.com/jsp/jstl/fmt" %></font>

fmt库，它是格式化库

**<fmt:formDate value="" pattern="">**

* value：指定一个Date类型的变量
* pattern：用来指定输出的模板！例如：yyyy-MM-dd HH:mm:ss

```jsp
<%
      Date date = new Date();
      request.setAttribute("date", date);
   %>
   <fmt:formatDate value="${requestScope.date }" pattern="yyyy-MM-dd HH-mm"/> <!--结果：2020-07-19 09-43-->
```

**<fmt:formNumber value="${num1}" pattern="0.00">**

​        保留小数后两位，它会四舍五入！如果不足2位，以0补位

**<fmt:formNumber value="${num1}" pattern="#.##">**

​        保留小数后两位，它会四舍五入！如果不足2位，不补位

```jsp
 <%
      request.setAttribute("num1", 3.1415925);
   %>
   <fmt:formatNumber value="${requestScope.num1 }" pattern="0.000"/> <!--结果：3.142-->
```

#### 8.include标签

<font color="lighblue" >动态包含标签</font>，将多个文件通过jsp包含展示出来，但是每个jsp文件都是独立的文件。

```jsp
<body>
	<jsp:include page="first.jsp"></jsp:include>
	<jsp:include page="second.jsp">
         <jsp:param name="country" value="中国"/>
         <jsp:param name="province" value="山西"/>
    </jsp:include>
	<jsp:include page="weclome.html"></jsp:include>
</body>
```

<font color="lighblue" >静态包含标签</font>，将多个文件合并成一个jsp文件进行展示。

```jsp
<body>
    <%@include file="file.jsp"%>
    <%@include file="second.jsp"%>
    <%@include file="four.jsp"%>
</body>
```

#### 9.forward标签

<font color="lighblue" >请求转发</font>

```jsp
<%
  User user = new User("lili","1111")
  pageContext.setAttribute("username",user.getUsername());
%>
<c:set var="User" value="${pageScope.username}" scope="request"/>
<jsp:forward page="success.jsp">
    <jsp:param name="User" value="${User}"/>
</jsp:forward>
```

#### 10.forTokens标签

<c: forTokens>标签用于输出操作，像是String类的split()方法和循环输出的一种集合。

* items:输出的字符串
* delims:字符串分割符
* var:存放每一个字符串变量
* varStatus:存放当前对象的相关信息
* begin:输出位置
* end:结束位置
* step:输出间隔

```jsp
<body>
    <%
       String url = "www.cctv.com";
       pageContext.setAttribute("url",url);
    %>
    <c:forTokens items="${pageScope.url}" delims="." var="element">
        <c:out value="${element}"/>
    </c:forTokens>
</body>
<!--结果：www cctv com-->
```

```jsp
<body>
    <c:forTokens items='(A B C)-(D E F)-(G H I)-(J K L)-(M N O)-(P Q R)' delims='0' var='item' begin='2' end='8' step='2'>
        <c:out value='&{item}'/><br>
    </c:forTokens>
</body>
<!--D E F
    G H I
    J K L
    M N O-->
```

#### 11.redirect标签

进行客户端跳转

* url:跳转的地址
* context:上下文路径

```jsp
<body>
    <c:redirect url="/foreac.jsp" context="/testWeb/JSTL">
        <c:param name="author" value="nobb"></c:param>
        <c:param name="title" value="2ffs"></c:param>
    </c:redirect>
    
</body>
```







