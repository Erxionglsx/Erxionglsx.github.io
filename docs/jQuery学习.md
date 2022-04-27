# jQuery学习

> JavaScript框架
>
> 代码参考：E:\编程软件\HBuilderX\训练\JQuery
>
> 视频参考B站颜群的jQuery视频

------

[TOC]

### 1.JQuery包的下载和导入

下载的版本：

jquery-3.3.1.min.js ：压缩版，发布版84.8KB
jquery-3.3.1.js ：常规版，开发版265KB 

引入JQuery的包：

```html
<meta charset="utf-8">
		<!--引入jquery-->
		<script type="text/javascript" src="js/jquery-3.3.1.js"></script>
		<!--使用-->
		<script type="text/javascript">
```

### 2.DOM对象与JQuery对象

<font color="lighblue">$(document).ready(function(){...})</font>：初始化函数， 当网页中的dom元素（不包含图片、视频、资源）全部加载完毕后 立刻执行
onload :  
	javascript，初始化函数，当网页中的dom元素（关联图片、视频、资源）全部加载完毕后 立刻执行

jquery初始化函数简化：
$(function(){...});

<font color="lighblue"> $等价于jQuery
	</font>

<font color="lighblue" style="font-weight:bold;">dom模型</font>：
将html  xml等文档结构的标签语言 看成dom模型

**dom节点有三种类型：
	**

* <font color="lighblue"> 元素节点</font> ： <html>   <ul>... <p>
  
* <font color="lighblue"> 属性节点
  </font>：title  src  alt ...
  
* <font color="lighblue"> 文本节点</font>： 文本节点

<font color="lighblue" style="font-weight:bold;">Dom对象
</font>： 

* 以上三种节点类型的具体对象 就是Dom对象。
  
* <font color="lighblue">使用层面</font>:凡是JavaSCript能够直接操作的对象，就是Dom对象。
  
* 例如，var title = document.getElementById("myTitile");通过js获取到的title对象 就是一个dom对象（就是<p>对象）

<font color="lighblue" style="font-weight:bold;">jQuery对象</font>：

* 凡是jQuery能够直接操作的对象，就是jQuery对象。
* 例如：var $title = $("#myTitile") ; 通过jquery获取到的 $title 就是一个jquery对象。
  
* 同样一个元素，即可以成为一个dom对象（javascript对象），也可以成为一个jquery对象。

**注意：dom对象 只适用于js的各种语法（函数、属性），jquery对象只用于jquery的各种语法（函数、属性）。dom对象和jquery对象的各自独立。**

例如
：

* title 是dom对象，因此可以使用js属性或方法 title.innerHTML
* $title 是jquery对象，因此可以使用jquery属性或方法  $title.html()

**建议**：
js对象 直接写title
；jquery 加上$，例如$title

*dom对象和jquery对象的转换*：
	 

* title.innerHTML;
  		tile ->$title  
* $title.html();      $title->title

<font color="lighblue">dom对象->jquery对象</font> : jquery工厂，  $(dom对象)

<font color="lighblue">jquery对象 ->dom对象</font>：
	

* 基础：jquery对象默认是一个数组 或集合   ；dom对象默认是一个单独的对
* 数组：jquery对象[0]
* 集合：jquery对象.get(0)

### 3.jQuery选择器

**1.基本选择器**

* <font color="lighblue">标签选择器</font>：
  	$("标签名")

  ​     $("p").html() 获取p标签对象，是jquery对象形式的
  ​		

  ​     $("p").length

* <font color="lighblue">类选择器</font>：
  	$(".class值")

* <font color="lighblue">id选择器</font>：$("#id值")

    ```jsp
    <script type="text/javascript">
        $("document").ready(function () {
                $("#1").click(function(){
                    $("#mydiv").hide();//id选择器
                });
              $("#2").click(function(){
                  $(".p1").hide();//类选择器
              });
          });
    </script>
    
    <div id="mydiv" class="test">this is div</div>
    <p class="p1">ffs</p>
    <button id="1">隐藏div</button>
    <button id="2">隐藏p标签</button>
    ```

* <font color="lighblue">并集(或)选择器</font>  逗号,
  		$(".class值,#id值")

* <font color="lighblue">交集选择器</font>（同时存在）	连续直接写

  ​     $(".class值#id值")
  ​

  ​     $("p.myStyle").html()：选中 即是p标签，并且class的值是myStyle

  ​     注意：不能出现歧义
  ​			错误写法 $(".myStylep").html()
  ​		

  ​     交集选择器 在交接处 只能是.或#开头的选择器

* <font color="lighblue">全局选择器</font>：选中全部的元素
  		$("*")
 ```jsp
<script type="text/javascript">
$(document).ready(function(){
        alert($(".myStyle").html() ); // .取类
        alert($("#myTitle3").html() ); // #取id
        alert($(".myStyle,#myTitle3").length); // ,表示并
        alert($("p#myTitle3").length); //连续写表示交
        alert($("*").length);  // 全局选择器 
});
</script>
 ```

**2.层次选择器**（只取后面的那些元素）

* <font color="lighblue">相邻选择器</font> +
  		    $("选择器1+选择器2")
  
* <font color="lighblue">同辈选择器</font> ~	
  	$("选择器1~选择器2")
  
* <font color="lighblue">后代选择器 </font>空格   $("选择器1 选择器2")

* <font color="lighblue">子代选择器</font> >
  		   $("选择器>选择器2")
    		
    		
   
   ```java
   alert($("#b+li").html());  //相邻选择器
   alert($("#b~li").length);  //同辈选择器
   alert($("body li").length);  //后代选择器 
   alert($("body>li").length);  //子代选择器 
   ```

**3.属性选择器**  [ ... ]

* $("[属性名]")

  ​     $("[class]") :选中全部元素中 有class属性的 元素

  ​     $("[属性名=属性值]")
  ​		$("[class=xxx]")        $("[class='xxx']")    

  ​     $("[class!=a]")  不等于， 包含两种： 有class但值不是a,   没有class

  ​     $("[class^=a]")  class以a开头的元素
  ​	

  ​     $("[class$=a]")  class以a结尾的元素
  ​	

  ​     $("[class*=a]")  class有a的元素
  
  ```java
  //属性选择器,class属性
  alert($("[class]").html()); 
  // calss=属性值,可加引号
  alert($("[class=xxx]").html());  
  alert($("[class='xxx']").html());
  //不等于,两种(分两种，一种无class,一种有class)
  alert($("[class!=xxx]").length);  
  //class以x开头的元素
  alert($("[class^=x]").html());  
  //class以x结尾的元素
  alert($("[class$=x]").length); 
  //class含x的元素
  alert($("[class*=x]").length);  
  ```

**4.过滤选择器**

过滤选择器的一些方法 和其他函数类型，例如 $("ul>li:first")等价于$("ul>li").first()

有些不同，例如，  可以$("ul>li:odd")	；错误$("ul>li").odd();

<font color="lighblue">a.基本过滤选择器</font> （从0开始）

* :first：最开头那一个

* :last：最后那一个

* :even：偶数

* :odd:奇数

* :eq(index)：第index个

* :gt(index) : >index的全部元素

* :lt(index): <index的全部元素

* :not(选择器):除了...以外

* :header:选中所有的标题元素 h1 h2 <h1>

* :focus: 获取当前焦点的元素

  ```java
  alert($("ul>li:first").html());  //first：最开头那一个  (1)
  alert($("ul>li").first().html());  //first：最开头那一个(2)
  alert($("ul>li").last().html());  //last：最后那一个
  alert($("ul>li").eq(2).html());  //eq(2)：第2个,从零开始
  alert($("ul>li:odd").length);  //:odd:奇数
  alert($($("ul>li:odd")[1]).html());  //奇数数组中第一个
  alert($("ul>li:gt(2)").length);  //>index的全部元素
  alert($("ul>li:lt(2)").length); //<index的全部元素
  alert($("li:not(ul>li:eq(2))").length);  //:除了...以外
  $(":header").css("background-color","gray");  //选中所有的标题元素
  $("#3").focus(function(){//获取当前焦点的元素
      $("#div2").text("hello world");
  });
  ```

**5.可见性选择器**

* :visible ：选中所有可见的元素
  	

* :hidden：选中所有隐藏的元素

  ​    事件 ，事件函数（事件方法）

* js: onXxx
  	onclick();
    	写在<script>内，ready()外；
   
   ```java
   alert($(":visible").length);  //选中所有可见的元素
   alert($(":hidden").length);  //选中所有隐藏的元素
   $("h1").click(function(){//鼠标事件 click()单击事件
           alert("单击事件");
   });
   ```

jquery:没有onclick(); 
	ready()内；

```java
    ready(function(){
	$(选择器).事件类型(function(){
		...
	});
});
```
### 4.windows事件：ready();

<font color="lighblue">>鼠标事件</font> ：
	

* click()：单击事件
  
* mouseover():鼠标悬浮
  
* mouseout():鼠标离开

  ```java
  $("h1").click(function(){//鼠标事件 click()单击事件
   	alert("单击事件");
  });
  $("div").mouseover(function(){//悬浮事件
  	alert("悬浮。。。");
  });
  $("div").mouseout(function(){//离开事件
  	alert("离开。。。");
  });
  
  $("div").mouseover(function(){
      alert("悬浮。。。");
  }).mouseout(function(){  //链式
  	alert("离开。。。");
  });
  ```

<font color="lighblue">>键盘事件</font> ：

* keydown():按键 从上往下的 过程
  
* keypress() ： 按键被压到 最底部
  
* keyup()：松开按键

  ```java
  $("body").bind({"keydown":function(){//批量绑定
  		alert("按下。。bind。。");
  }, "keyup":function(){
  		alert("松开...bind...");
   }
  });
  
  $("body").keydown(function(event){
   	if(event.keyCode == 13){//按压,指定具体的按键,13是回车
  		alert("回车...");
  	 }
  }).keyup(function(){//松开
   		 alert("松开...");
  });
  ```

1.前端的一些事件、方法，会在某些情况下失效。 2.兼容性问题

可以通过event.keyCode指定具体的按键:

```java
$("body").keydown(function(event){
    if(event.keyCode == 13){
   		 alert("回车。。。...");
    }
});
```
<font color="lighblue">>表单事件
</font> ：

* focus()：获取焦点
  
* blur()：失去焦点

颜色值：可以使用 英语单词 或者   #6位十六进制
		

* a. #000000:黑色   #ffffff:白色
  
* b. 如果6位 是两两相同的，则可以写成3位,#aabbcc 可以写成#abc

```java
$("#uid").focus(function(){//获取光标,变红色
	//当前元素就是id="uid"的文本框
	$(this).css("background-color","#d2f222");
}).blur(function(){//失去光标,变白色
	 $(this).css("background-color","white"); 
});

function test()
{
    $("input:focus").css("background-color","yellow");//获取当前焦点的元素,并设置背景颜色
}
```

绑定事件与移除事件:  $(...).click(  function(){ ... });

<font color="lighblue">>绑定事件</font> ：

* $(...).bind("事件名",[数据],函数);
  
* $(...).bind("click",function(){ ... });

* $(...).bind("mouseover",function(){ ...});

* $(...).bind("focus",function(){ ... });

* 批量绑定
  	$(...).bind({ "事件名":函数   ,  "事件名":函数 ,...,"事件名":函数 }) ;
    	
    	
   
   ```java
   $("#uid").bind("click",function(){//绑定事件
       alert("bind形式的单击事件。。。");
   });
   
   function myBind()//绑定
   {
       $("#uname").bind("click",function(){
           alert("click...");
       });
   }
   function myUnbind()//解绑
   {
       $("#uname").unbind("click");
   }
   ```

<font color="lighblue">>移除事件</font> ：$(...).unbind("事件名");

<font color="lighblue">>复合事件</font> ：

* hover(f1,f2)：切换使用mouseover()和mouseout()

  ```jsp
  <!--当鼠标指针悬停在上面时，改变 <p> 元素的背景颜色-->
  <script>
  $(document).ready(function(){
    $("p").hover(function(){
      $("p").css("background-color","yellow");
      },function(){
      $("p").css("background-color","pink");
    });
  });
  </script>
  ```

* toggle(f1,f2,f3,...,fn):版本问题(jquery1.9以前支持)

* 多个click()事件，toggle()还有其他含义（隐藏与显示）

显示效果（隐藏与显示）
	形式：hide([速度],[回调函数]);

其中速度：可以是数字（毫秒），也可以是 单词（fast normal  slow，注意加双引号）

* hide();隐藏
  	

* show();显示
  	

* toggle()：切换隐藏与显示

* 淡入淡出（透明度）
  	

  ​      fadeIn(速度):淡入  显示
  ​	

  ​      fadeOut(速度):淡出 隐藏

        fadeToggle(速度)：切换
  
* 控制高度
  

  ​      slideDown(速度):下拉，显示
  ​	

  ​      slideUp(速度);上拉  隐藏
  
  ```java
  function myHide()//隐藏
  {
  	    //$("h3").hide(3000);
       	//$("h3").fadeOut(5000);//透明度
  		$("h3").slideUp(5000);//上拉显示
  }
  function myShow()//显示 3种方法
  {
  		$("h3").show(3000,myCallBack);//hide([速度],[回调函数]);  3000是时间
  		//$("h3").fadeIn(5000);//透明度
  		$("h3").slideDown(5000);//下拉显示
  }
  ```

总结显示问题：
	

* 显示：show()  fadeIn()  slideDown();
  
* 隐藏：hide()	fadeOut()	slideUp();

### 5.操作DOM

**a.样式操作**

* <font color="lighblue">>复合事件</font> i.设置css()
  
  * jquery对象.css("属性名","属性值");
    
  *  jquery对象.css({ "属性名":"属性值"      ,"属性名":"属性值" ,...,"属性名":"属性值"     });
* <font color="lighblue">>复合事件</font> ii.追加或移除样式class
  
  * addClass("x");
  
  * addClass("x x x");
    
    ```jsp
    <script type="text/javascript">
      $("document").ready(function () {
          $("#1").click(function(){
             $("div").addClass("blue"); 
          });
          $("#2").click(function(){
             $("div").removeClass("important"); 
          });
          $("#3").click(function(){
              $("div").toggleClass("blue");
              $("div").toggleClass("important");
          })
      }
    </script>
    <style>
        .blue{
            color:blue;
        }
        .important{
            color:red;
        }
    </style>
    <div id="div1">欢迎访问</div>
    <button id="1">添加样式</button>
    <button id="2">移除样式</button>
    <button id="3">切换样式</button>
    ```
    
  * removeClass(x);
    
  * removeClass(x x );
    
  * removeClass():移除全部样式
    
  * toggleClass("x x x"):切换追加与移除
  
    ```java
     //复合事件 hover(f1,f2)：切换使用mouseover()和mouseout()
    $("#color2Id").css("color","red");//DOM方法
    $("#color2Id").css({"color":"red","font-size":"100px","background-color":"gray"});
    $("#mytestId").addClass("myStyle myStyle2");//多个样式同时生效
    $("#mytestId").click(function(){
    	$("#mytestId").removeClass("myStyle");//移除myStyle样式
     });
    $("#mytestId").click(function(){
        $("#mytestId").removeClass(); //移除所有样式 	
        $("#mytestId").toggleClass("myStyle myStyle2");//样式切换
    });
    ```

* animate()-操作多个属性

  ```jsp
  <script>
     $("document").ready(function(){
         $("#1").click(function(){
             $("#mydiv").animate({
                 left:"200px",
                 height:"250px"
                 width:"250px"
             });
         });
     });
  </script>
  <div id="mydiv" style="width:200px;height:200px;background-color:blue"></div>
  <button id="1">测试动画</button>
  ```

b.**内容操作**

* html()：获取值 ，获取的是元素 的内容 ，包含了 元素内部的 各种标签
  
* html(xxxx) :先渲染，后显示

* text()：获取值 ，值获取文本值

* text(xxxx)：將值原样显示，没有渲染

* val()：获取value值

* val(xxx)：设置value值

  ```java
  alert($("#mytestId").html());//获取内容
  alert($("#mytestId").text());//获取文本值
  alert($("#divId2").height());//获取高度
  alert($("#divId2").width());//获取宽度
  $("#divId2").height(400);//高度赋值
  $("#divId2").width(400);//宽度赋值
  var $myDivOffset = $("#divId2").offset();//获取位置
  alert($myDivOffset.left + "," + $myDivOffset.top);//输出左边距和上边距
  $("#id1").val("111");//设置value值
  alert($("#id1").val());//获取value值
  ```

**c.节点与属性操作**

* <font color="lighblue"> i.节点操作</font>
  
  * <font color="lighblue">查询节点</font>   （jquery选择器）
    
  * <font color="lighblue">创建节点 </font>  ：  
    
    * $()
      
    * $(选择器):获取节点		
      
    * $(DOM对象)：转换
      
    * $(html字符串)，   
    * $("<li onclick="..">xxx</li>")
    
  * <font color="lighblue">插入节点</font>   文档
  * <font color="lighblue"> 替换节点</font>
    				
    
    * $X.replaceWith($Y)	：用$Y替换$X	
      
    * replaceAll();
    
  * <font color="lighblue">删除节点</font> 				
    
    * remove()：彻底删除
      
    * detach()：将结点删除，但关联的事件、数据不会删除（不推荐使用）
      
    * empty()	：只删除内容
    
  * <font color="lighblue">克隆节点</font> 
    				
    
    * clone(true|false)
      
    * $(document).ready(function(){    
    
    * $("li").click(function(){  ... });
      				});	
      
      ```java
      //内部插入节点
      var $myElement=$("<li>xxx</li>")
      $("ul").append($myElement);//往后插(1)
      $myElement.appendTo($("ul"));//往后插(2)	
      $("ul").prepend($myElement);//往前插(1)
      $myElement.prependTo($("ul"));//往前插(2)
      //外部插入节点
      var $myElement=$("<p>hello</p>");
      $("ul").after($myElement);//往后插(1)
      $myElement.insertAfter($("ul"));//往后插(2)
      $("ul").before($myElement);//往前插(1)
      $myElement.insertBefore($("ul"));//往前插(2)
      //替换节点
      $("ul>li:eq(1)").replaceWith("<li>xx</li>");//(1)
      $("<li>xx</li>").replaceAll("ul>li:eq(1)");//(2)
      //删除节点
      $("ul>li:eq(1)").remove();
      //克隆节点
       $("ul").append($("ul>li:eq(1)").clone());
      ```
* <font color="lighblue"> ii.属性操作 </font>
  
   * attr("属性名") ：获取属性值
   
   * attr("属性名","属性值") ：设置属性值
   
   * attr({ "属性名":"属性值",    "属性名":"属性值" ,  "属性名":"属性值" } );
   
   * 删除属性值   removeAttr("属性名");
   
     ```java
     //属性操作	
     alert( $("#uid").attr("type") );//取属性值 type,id,name...
     $("#uid").attr({"name":"zs","value":"111"});//属性赋值
     alert($("#uid").attr("name")   +"--"+$("#uid").attr("value") );
     $("#uid").removeAttr("value");//删除属性
     ```

**d.获取集合与遍历集合**

* <font color="lighblue">子节点集合</font> 
  		 $(...).children( "li")


* <font color="lighblue">后代集合</font> (不推荐使用，影响性能)
  		 $(...).find( "li")
				
     
     ```jsp
     <script type="text/javascript">
         $("#ul1").children().css({
             "color":"blue";
         });
         $("#ul2").find("#li2").css({
             "color":"red";
         });
     </script>
     ```
     
* <font color="lighblue">同辈集合</font> 

  * next()：后一个  +
    
  * prev(): 前一个
    
  * siblings()：同辈，左、右

* <font color="lighblue">前辈</font> 

  * parent():父代
    
  * parents("ul"):祖先
  
* <font color="lighblue"> 过滤集合</font>：

     * 很多方法的()就是一个过滤选择器
       
     * filter("选择器...");  

* <font color="lighblue">遍历集合</font> ：
     	

     * $(...).each(function(index,element){
       .... });

       ```java
       var $lis=$("ul").children("li");//子代
       alert($lis.length);
       alert(   $("ul>li:eq(1)").next().html());//后面元素
       alert(   $("ul>li:eq(1)").prev().html());//前面元素
       alert(   $("ul>li:eq(1)").siblings().length);//同辈元素
       alert(   $("ul>li:eq(1)").parent().html());//父辈元素
       alert(   $("ul>li:eq(1)").parents().length);//祖先
       alert(   $("ul>li:eq(1)").siblings(":odd").css("background-color","red"));//过滤集合
       //遍历
       $("ul>li").each(function(index,element){
           alert(index+","+$(this).html()+","+$(element).html() );//element相当于this
       });
       ```

### 6.CSS-dom操作

* offset()：偏移量（左上角的点）,left top
  	

* offset(function(n,oldOffset){   ... })

* offsetParent()：获取  （已定位）的最近的祖先元素
  		

* 已定位：元素position属性(默认static)被设置为了relative  absolute  或fixed  

  ```java
  var $myDivOffset = $("#divId2").offset();//获取位置
  alert($myDivOffset.left + "," + $myDivOffset.top);//输出左边距和上边距
  var $myDivOffset = $("#divId2").offset(function(index,oldOffset){
  		var newOffset = new Object();
  		newOffset.left = oldOffset.left+100;//修改偏移量
  		newOffset.top = oldOffset.top+ 100;
  		return newOffset;
  });
  
   $("#bid").click(function(){
  		//获取已定位的父节点
  		$(this).offsetParent().css("border","10px blue solid");
  });
  ```

**重要：表单校验，可以减轻对服务端的访问次数**

* 获取要校验的元素值(选择器)	用户名、密码
  	

* 通过 字符串处理方法、或者 正则表达式等手段 进行校验
  		"aaa@abc.com".indexOf("@") !=-1


* 触发校验的方法或事件（校验时机）

  * blur()：失去焦点时触发
    
  * submit()：当点击表单的 “提交submit”按钮时 触发
    
  * onblur="xxx()"
  * onsubmit="xx()"

  ```java
  $("#uage").blur(function(){ //blur失去焦点触发
  				var $age = $("#uage").val();
  				if(!($age > 0 &&$age < 100)){
  					alert("年龄输入有误");
  				}
  });
  //当单击submit按钮时触发,通过返回值true|false告知程序 是否校验成功
  		  $("#myForm").submit(function(){
  				var $name = $("#uname").val();
  				var $pwd = $("#upwd").val();
  				var $height = $("#uheight").val();
  				if($name.length<2|| $name.length> 6)
  				{
  					alert("名字必须2-6位");
  					return false;//校验失败
  				}
  				if($pwd.length<6)
  				{
  					alert("密码必须6位以上");
  					return false;//校验失败
  				}
  				if($height<100 || $height>200)
  				{
  					alert("身高应该在100-200");
  					return false;//校验失败
  				}
  				return true;//校验成功
  			});
  
  
  <form id="myForm" action="result.html">
  			姓名：<input id="uname" /><br/>
  			密码：<input type="password" id="upwd" /><br/>
  			身高：<input id="uheight" /><br/>
  			
  			邮箱：<input id="uemail" />
  			      <font color="red" style="display:none" id="emailTip">邮箱格式不正确</font><!--默认隐藏-->
  			<br/>
  			<input type="submit" value="注册" />
  		</form><br/>
  		
  		年龄：<input id="uage" />
  ```

submit()或onsubmit的返回值 会决定表单是否跳转，如果返回true则正常跳转；如果返回false 则终止条件

在校验时建议的写法：

```web
if(....不合理的情况) alert() ; return false ;
	if(....不合理的情况) alert() ; return false ;
	if(....不合理的情况) alert() ; return false ;
	if(....不合理的情况) alert() ; return false ;
	
	return true ;
```

正则表达式：用于定义规则

* email="aaa" ;

* 正则表达式.test( email);	

  ```java
  $("#uemail").blur(function(){
  			var $email = $(this).val();
  			//邮箱的正则表达式
  			var reg = /^\w+@[0-9a-zA-Z]{2,4}\.[a-zA-Z]{2,3}(\.[a-zA-Z]{2,3})?$/;
  			if(!reg.test($email) )
  			{
  				  if($email=""|| $email.length==0)
  				  {
  					  $("#emailTip").css("display","none");
  				  }
  				else{							                                                                    $("#emailTip").css("display","inline");
  				    }
  				else{
  					$("#emailTip").css("display","none");
  				}
  });
  ```

使用：
手写的正则表达式规则.test(校验的值) -> true|false

### 7.jQuery操作Ajax

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














