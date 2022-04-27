Ajax学习

> 参考阿里云大学Ajax课程

---

[TOC]

## 1.认识Ajax

#### 1.ajax是什么？

* aaynchronous javascript and xml；异步的js和xml
* 它能使用js访问服务器，而且是异步访问
* 服务器给客户端的响应一般是整个页面，一个html完整页面。但是ajax中因为是局部刷新，那么服务器就不再响应整个页面，而只是数据。
  * text:纯文本
  * xml:大家都熟悉，java和js都认识
  * json:它是js提供的数据层交互格式，它在ajax中最受欢迎

#### 2.异步交互和同步交互

* **同步：**
  * 发送一个请求，就要等待服务器的响应结束，然后才能发第二个请求！中间这段时间就是一个字“卡”
  * 刷新的是整个页面！
* **异步：**
  * 发送一个请求后，无需等待服务器的响应，然后就可以发第二个请求！
  * 可以使用js接受服务器的响应，然后使用js来局部判断！

```jsp
      <html>
      <head>
          <script type="text/javascript">
              window.onload = function() { //在文档加载完成后马上执行！
                  //得到btn元素
                  var btn = document.getElementById("btn");
                  //给btn的click事件注册监听
                  btn.onclick = function() { //在按钮被点击时执行
                      //获取h1元素对应的DOM对象
                      var h1 = document.getElementById("h1");
                      //给h1添加内容
                      h1.innerHTML = "Hello JS!!!";
                  };
              };
          </script>
      </head>
      
    <body>
        <button id="btn">点击这里</button>
        <h1 id="h1"></h1>
      </body>
    </html>
```

#### 3.ajax应用场景

* 百度的搜索框
* 用户注册时（）校验用户名是否被注册过
* 视频下方的点赞按钮

#### 4.ajax的优缺点

优点：

* 异步交互：增强了用户体验。
* 性能：因为服务器无需再响应整个界面，只需要响应部分内容，所以服务器的压力减轻了。

缺点：

* ajax不能应用在所有场景。
* ajax无端的增多了对服务区的访问次数，给服务器带来了压力。

#### 5.ajax发送异步请求（四步操作）

> 代码参考ajax2工程

<font color="bluesas">重要一步:在工程中添加 jstl.jar 和 standard.jar 包</font>

<font color="bluesas">在JSP文件顶部添加</font>

```jsp
<%@ taglib prefix="c" uri="http://java.sun.com/jsp/jstl/core" %>
```

**第一步（得到XMLHttpRequest）**

* ajax的一个对象<font color="lighblue">XMLHttpRequest</font>，掌握了它，就掌握了ajax。

* 得到XMLHttpRequest

  - 大多数浏览器都支持：var xmlHttp = new XMLHttpRequest();
  - IE6.0：var xmlHttp = new ActiveXObject("Msxml2.XMLHTTP");
  - IE5.5及更早版本的IE：var xmlHttp = new ActiveXObject("Micorosoft.XMLHTTP");

* 编写创建XMLHttpRequest对象的函数

  ```jsp
  <script>
  function createXMLHttpRequest() {
        try {
           return new XMLHttpRequest();
        } catch(e) {
            try {
                return new ActiveXObject("Msxml2.XMLHTTP");
            } catch(e) {
                try {
                   reutrn new ActiveXObject("Microsoft.XMLHTTP");
                } catch(e){
                    alert("不支持该浏览器！")：
                    throw e;
                 }
            }
       }
  }
  //得到异步对象    
  var xmlHttp = createXMLHttpRequest();
  </script>
  ```

**第二步（打开与服务器的连接）**

  * <font color="lighblue">xmlHttp.open()</font>:用来打开与服务器的连接，它需要三个参数；
    
    * 请求方式：可以是GET或POST
    * 请求的URL：指定服务器端资源，例如：/day23_1/AServlet
    * 请求是否为异步：如果为true表示发送异步请求；否则同步请求！
    
* ```jsp
  xmlHttp.open("GET","/day23_1/AServlet",true);
  ```

**第三步（发送请求）**

  * <font color="lighblue">xmlHttp.send(null)</font>：如果不给可能会造成部分浏览器无法发送！

  * 参数：结束请求体内容，如果是GET请求，必须给出null。

    ```jsp
    xmlHttp.send(null);
    ```

**第四步（得到响应）**

* 在<font color="lighblue">xmlHttp对象</font>的一个事件上<font color="lighblue">注册监听器</font>：<font color="lighblue">onreadystatechange</font>
  
* xmlHttp对象一共有五个状态：
  
    * 0状态：刚创建，还没有调用open()方法；
    * 1状态：请求开始：调用了open()方法，但还没有调用send()方法；
    * 2状态：调用完了send()方法了；
    * 3状态：服务器已经开始响应，但不代表响应结束了！
  * 4状态：服务器响应结束了（通常我们只关心这个状态）
  
* 得到xmlHttp对象的状态：
  
  * var state = xmlHttp.readyState;   //可能是0、1、2、3、4
  
* 得到服务器响应的状态码
  
  * var status = xmlHttp.status;   //例如为200、404、500
  
* 得到服务器响应的内容
  
    * var conntent = xmlHttp.responseText;   //得到服务器的响应的文本格式的内容
  * var conntent = xmlHttp.responseXML;  //得到服务器的响应的内容，它是Document对象了
  
```jsp
<script>
    xmlHttp.onreadystatechange = finction() { //xmlHttp的5种状态都会调用本方法
       if(xmlHttp.readyState == 4 && xmlHttp.status == 200) { //双重判断：判断是否为4状态，而且还要判断是否为200(响应成功)
            //获取服务器的响应内容
            var text = xmlHttp.responseText;
       }
    };
  </script>
```

#### 6.发送post请求

> 如果发送请求时需要带有参数，一般都用POST请求

* open：xmlHttp.open("POST"....)；

* 添加一步：设置Content-Type请求头：

  xmlHttp.setRequestHeader("Content-Type","application/x-www-form-urlencoded");

* send：xmlHttp.send("username=zhangSan&password=123");

```jsp
<script>
......
var xmlHttp = createXMLHttpRequest();
//修改post方法，指定请求方式为POST
xmlHttp.open("POST","c:url value='AServlet'/",true);
//设置Content-Type请求头
xmlHttp.setRequestHeader("Content-Type","application/x-www-form-urlencoded");
//发送时指定请求体
xmlHttp.send("username=zhangsan&password=123");
xmlHttp.onreadystatechange = finction() { //xmlHttp的5种状态都会调用本方法
       if(xmlHttp.readyState == 4 && xmlHttp.status == 200) { //双重判断：判断是否为4状态，而且还要判断是否为200(响应成功)
            //获取服务器的响应内容
            var text = xmlHttp.responseText;
       }
};
</script>
```

#### 7.注册表单之校验用户是否注册

> 代码参考web工程的ajax3.jsp和ajax2工程的ajax4.jsp

* 编写页面

  ajax3.jsp

  * 给出注册表单页面
  * 给用户名文本框添加onblur事件的监听
  * 获取文本框的内容，通过ajax四步发送给服务器，得到响应结果
    * 如果为1：在文本框后显示“用户名已被注册”
    * 如果为0：什么都不做！

* 编写Servlet

  ValidateUsernameServlet

  * 获取客户端传递的用户名参数
  * 判断是否为itcast
    * 是：返回1
    * 否：返回0

  ```jsp
  <script type="text/javascript">
  function createXMLHttpRequest(){
      try{
          return new XMLHttpRequest();//大多数浏览器
      }catch (e) {
        try{
            return ActiveXObject("Msxml2.XMLHTTP");//IE6.0
        }catch (e) {
            try {
             return ActiveXObject("Microsoft.XMLHTTP");//IE5.5及更早版本
            } catch (e) {
                alert("哥们儿,你用的什么浏览器????");
                throw e;
             }
          }
      }
  }
  window.onload = function() {
  	 //获取文本框，给它的失去焦点事件注册监听
  	 var userEle = document.getElementById("usernameEle");
  	 userEle.onblur = function() {
  	 //1.得到异步对象
  	 var xmlHttp = createXMLHttpRequest();
  	 //2.打开链接
  	 xmlHttp.open("POST","<c:url value='/ValidateUsernameServlet'/>",true);
  	 //3.设置请求头：Content-Type
  	 xmlHttp.setRequestHeader("Content-Type","application/x-www-form-urlencoded");
  	 //4.发送请求，给出请求体
  	 xmlHttp.send("username="+userEle.value);
  	 //5.给xmlHttp的onreadystatechange事件注册监听
  	 xmlHttp.onreadystatechange = function() {
  		 if(xmlHttp.readyState == 4 && xmlHttp.status ==200 ){//双重监听
  			 //获取服务器响应，判断是否为1
  			 //是：获取spam,添加内容：“用户名已被注册”
  			 var text = xmlHttp.responseText;
  			//得到span元素
  	    	 var span = document.getElementById("p1");
  		     if(text == 1) {
  		    	 span.innerHTML = "用户名已被注册！";
  		     } else {
  		    	 span.innerHTML = "";
  		     }
  		 }
  	  };
     };
  };
  </script>
  </head>
  <body>
  <h1>演示用户名是否被注册</h1>
  <form action="" method="post">
  用户名：<input type="text" name="username" id="usernameEle"/><p id="p1"></p></br>
  密    码：<input type="password" name="password"/><br/>
  <input type="submit" value="注册"/>
  </form>
  </body>
  ```


#### 8.响应内容为xml数据

> 代码参考web/ajax4.jsp和ajax2/ajax3.jsp

* 服务器端：
  * 设置响应头：ContentType，其值为：text/xml;charset=utf-8
* 客户端：
  * var doc = xmlHttp.responseXML; //得到的是Document对象

#### 9.省市联动

1. 页面

   ```html
   <select  name="province">
       <option>===请选择省份===</option>
   </select>
   <select  name="city">
       <option>===请选择城市===</option>
   </select>
   ```

2. ProvinceServlet

   * ProvinceServlet：当页面加载完毕后马上请求这个Servlet

     它需要加载china.xml文件，把所有的省的名称使用字符串发送给客户端

3. 页面的工作

   * 获取这个字符串，使用逗号分割，得到数组
   * 循环遍历每个字符串（省份的名称），使用每个字符串创建一个<option>元素添加到<select name="province">这个元素中

4. CityServlet

   * CityServlet：当页面选择某个省时，发送请求
   * 得到省份的名称，加载china.xml文件，查询出该省份对应的元素对象，吧这个元素转成xml字符串，发送给客户端

5. 页面的工作

   * 得到服务器的响应结果:doc
   * 获取所有的<city>子元素，循环遍历，得到<city>的内容
   * 使用每个<city>的内容创建一个<option>元素，添加到<select name = "city">
   
6. XStream可以吧JavaBean转换为（序列化为）xml

#### 9.JSON的概述

> 代码参考web工程的json1.jsp和json2.jsp

1. json是什么

   * 它是js提供的一种数据交换格式

2. json语法

   * {}：是对象
     * 属性名必须使用双引号括起来！单引不行
     * 属性值：
       * null
       * 数值
       * 字符串
       * 数组：使用[]括起来
       * bootlean值：true和false

3. 应用json

   * var person = {"name":"zhangsan","age":18,"sex":"male"};

 ```jsp
<script>
      window.onload = function() {
         var person = {"name":"zhangsan","age":19,"sex":"male"};
         alert(person.name+","+person.age+","+person.sex);
      }
</script>
 ```

4. 转义字符

   为了能够输出内容 " ，不被混淆，要加\"来进行转义

   ```java
      var s = "ab\"c"
      alert(s);//结果为：ab\"c
   ```

5. eval()函数

   eval() 函数可计算某个字符串，并执行其中的的 JavaScript    代码。

   ```jsp
   <  // eval(string)
      var str = "1 + 2";
      var sum = eval("(" + str + ")");
         alert(sum);  //结果为3
   ```

6. json与xml比较

   * 可读性：XML胜出
   * 解析难度：JSON本身就是JS对象（主场作战），所有简单很多
   * 流行度：XML以及流行好多年，但在AJAX领域，JSON更受欢迎。
### jQuery操作Ajax

#### 1.jQuery.load()方法

* load()方法从服务器加载数据，并把返回的数据放入被选元素中。

* 语法：$(selector).load(URL,data,callback);

  * 必须的URL参数规定您希望请求的URL
  * 可选的data参数规定连同请求发送的数据
  * 可选的callback参数是请求成功后所执行的函数名

  ```jsp
  <script type="text/javascript">
      $("document").ready(function(){
          $("#1").click(function () {
                $("#div1").load(
                    "http://localhost:8080/train_8_4_war_exploded/ZB/ajax/text.txt",
                    function (data,status) {
                        alert(status);
                    }
                );
          });
      });
  </script>
  <body>
      <div id="div1">
          加载服务器的数据
      </div>
      <button id="1">测试load</button>
  </body>
  ```

#### 2.jQuery.get()请求

  * $.get()方法通过GET请求从服务器上请求数据。

  语法：$.get(URL,data.callback);

  * 必须的URL参数规定您希望请求的URL
  * 可选的data参数规定连同请求发送的数据
  * 可选的callback参数是请求成功后所执行的函数名

  ```jsp
<script type="text/javascript">
    $("document").ready(function() {
            $("#1").click(function () {
                $.get(
                    "http://localhost:8080/train_8_4_war_exploded/ZB/ajax/success.jsp",
                    {
                        "city":"成都",
                        "street":"天府"
                     },
                    function(data,status){
                        alert(status)
                        $("#div1").html(data)
                    });
            });
    });
</script>
<body>
    <div id="div1">服务器的返回数据</div>
    <button id="1">测试get</button>
</body>
  ```

```jsp
<!--success.jsp-->
<%
   String city=request.getParameter("city");
   String street=request.getParameter("street");
%>
<body>
    <%
        out.print(city);
        out.print(street);
    %>
</body>
```

#### 3..jQuery.post()请求

* $.post()方法通过POST请求从服务器上请求数据。

  语法：$.post(URL,data,callback);

  * 必须的URL参数规定您希望请求的URL
  * 可选的data参数规定连同请求发送的数据
  * 可选的callback参数是请求成功后所执行的函数名

  ```jsp
  <script type="text/javascript">
     $("document").ready(function(){
         $("#1").clcik(function(){
             $.post(
                "http://localhost:8080/train_8_4_war_exploded/ZB/ajax/check.jsp",
                 {
                     "username":document.getElementById("username").value,
                     "password":document.getElementById("password").value           
                 },
                 function (data,status){
                     $("#div1").html(data)
                 }
             );
         });
     });
  </script>
  <body>
      用户名：<input type="text" name="username">
      密码：<input type="password" name="password">
      <div id="div1"></div>
      <button id="1">测试post</button>
  </body>
  ```

  ```jsp
  <!--check.jsp-->
  <% String username = request.getParmeter("username");
  String password = request.getParmeter("password");%>
  <body>
      <%
          if(username.equals("bobo")&&password.equals("123")){
              out.println("登陆成功");
          }else{
              out.println("登陆失败");
          }
      %>
  </body>
  ```

#### 4.jQuery.ajax()请求

```jsp
<script type="text/javascript">
    $("document").ready(function(){
        $("#1").click(function(){
            $.ajax({
                url:"http://localhost:8080/train_8_4_war_exploded/ZB/ajax/result.jsp",
                type:"get",
                data:{
                    "city":document.getElementById("city").value,
                    "street":document.getElementById("street").value
                },
                success:function (data,status) {
                    alert(status);
                    $("#div1").html(data);
                }
            });
        });
    });
</script>
<body>
  城市：<input id="city" type="text" name="city">
  街道：<input id="street" type="text" name="street">
  <div id="div1">
      服务器加载数据
  </div>
<button id="1">通用测试</button>
</body>
```

```jsp
<%
    String city=request.getParameter("city");
    String street=request.getParameter("street");
%>
<body>
<%
    out.print(city);
    out.print(street);
%>
</body>
```
