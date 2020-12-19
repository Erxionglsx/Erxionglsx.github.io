

# JS

------

[TOC]

## JavaScript

#### 1.选择日期、颜色、数字、内容

```html
选择日期：<input type="date" name="Date"/><br/><!--日期-->
选择颜色：<input type="color" name="Color"/><br/><!--颜色-->
选择数字：<input type="number"  name="num" min="1" max="10" /><br/>
选择浏览器：<input list="browsers" name="browser">
<datalist id="browsers">
    <option value="Chrome"></option>
    <option value="Firox"></option>
    <option value="360"></option>
    <option value="baidu"></option>
</datalist>
```

#### 2.元素改变事件onchange

```jsp
<script>
    //元素改变时触发
    function checkAddress(){
        alert("这是验证地址");
    }
</script>

省份：<select onchange="checkAddress()"><!-- HTML元素改变触发 -->
    <option value="1">四川省</option>
    <option value="2">广东省</option>
    <option value="3">云南省</option>
    <option value="4">湖北省</option>
</select>
```

#### 3.鼠标移动事件onmouseover 和onmouseout

```jsp
<script>
    //鼠标移动到该元素时触发
    function movedOnSubmit(){
        alert("这是鼠标移动事件");
    }
    //鼠标移出该元素时触发
    function movedOutitle(){
        alert("这是鼠标移出事件");
    }
</script>

<span onmouseover="movedOnSubmit()" >欢迎</span>
<span onmouseout="movedOutitle()" >恭喜</span>
```

#### 4.按下键盘事件onkeydown

```jsp
<script>
    //按下键盘按钮
    function inputOnKeyDown(){
        alert("这是按下键盘事件");
    }
</script>

昵称：<input type="text" onkeydown="inputOnKeyDown()" />
```

#### 5.页面加载完成onload

```jsp
<script>
    //页面加载完成后触发
    function checkLoadCompleted(){
        alert("这是页面加载完成");
    }
</script>

<body onload="checkLoadCompleted()"></body>
```

#### 6.焦点事件onblur和onfocus

```jsp
<script>
    //失去焦点事件
    function nickBlur(){
        alert("失去焦点");
    }
    //获得焦点
    function nickOnFoucs(){
        console.log("获得焦点");
    }
</script>

账号：<input type="text" onblur="nickBlur()"/><br /><!-- 失去焦点 -->
密码：<input type="text" onfocus="nickOnFoucs()"/><br />
```

#### 7.提交表单触发onsubmit

```jsp
<script>
    //提交表单触发，必须有submit按钮
    function submitForm(){
        alert("提交");
    }
</script>

<form action="" onsubmit="submitForm()">
<input type="submit" value="提交"/></form>
```

#### 8.复制、剪切、粘贴事件 Oncopy、OnCut、OnPaste

```jsp
<script>
    //复制该内容时触发
    function Oncopy(){
        alert("你正在复制该内容");
    }
    //剪切该内容时触发
    function OnCut(){
        alert("你正在剪切该内容！");
    }
    //粘贴内容时触发
    function OnPaste(){
        alert("你在粘贴内容");
    }
</script>

<span oncopy="Oncopy()">复制提醒</span>
<span oncut="OnCut()">剪贴提醒</span>
<input type="text" onpaste="OnPaste()">
```

#### 9.显示数据的类型 typeof

```jsp
<script>
    //显示数据的类型
    function typeOf(){
        document.getElementById("type_of").innerHTML =typeof "john" + "<br>" +
            typeof 3.14 + "<br>" +
            typeof false + "<br>" +
            typeof [1,2,3,4] + "<br>" ;
    }
</script>

<input type="button"  onclick="typeOf()" value="显示变量类型" /><br />
			 <span id="type_of"></span>
<!--结果：string number boolean object-->
```

#### 10.属性返回对创建此对象的数组函数的引用Constructor

```jsp
<script>
    //属性返回对创建此对象的数组函数的引用
    function testContructor(){
        document.getElementById("contructor").innerHTML =
            "测试字符串的constroctor---"+"Jone".constructor  +
            "------测试数字的constroctor---"+(3.14).constructor;
    }
</script>

<input type="button"  onclick="testContructor()" value="测试Constructor" /><br />
<span id="contructor"></span>
<!--结果：测试字符串的constroctor---function String() { [native code] }------测试数字的constroctor---function Number() { [native code] }-->
```

#### 11.video视频

```jsp
<video  width="300" height="200" controls>
    <source src="https://www.runoob.com/try/demo_source/movie.mp4" type="video/mp4"></source>
</video>
```

#### 12.类型转换、parseFloat、parseInt

```jsp
<script>
    //类型转换
    function trackOf(){
        var test1 = String(100+23);
        console.log(test1);//123

        //数字转换为字符串
        var test2 = String(100);
        var test3 = String(23);
        var test4 = test2 + test3;
        console.log("test2:"+test4);//10023
        
        var num_one = 10;
        var num_two = "20";
        var num_thr = num_one + num_two;
        console.log(num_thr);//1020
        
        //获取日期
        var test5 = new Date();
        console.log("日"+test5.getDate());
        console.log("星期"+test5.getDay());
        console.log("时"+test5.getHours());
        
        //字符串转换为数字
        var test6 = "3.14";
        var test61 = Number("3.14");//Number字符串转换为数字
        var test62 = 6;
        var test63 = test61+test62;//9.14
        console.log(test63);
        
        //parseFloat() 解析一个字符串，并返回一个浮点数
        //parseInt()  解析一个字符串，并返回一个整数
        var test7 = "3.14";
        var test71 = parseFloat(test7);

        var test72 = "6";
        var test73 = parseInt(test72);

        var test74 = test71 + test73;
        console.log(test74);//9.14
    }
</script>
```

#### 13.search查找位置、替换replace

```jsp
<script>
function elsser(){
    var str="hello world";
    var exp=/Ello/;
    //返回查询到的起始位置
    var index = str.search(exp);
    console.log("search 测试 结果："+index);//-1,没匹配到
    var index2 = str.search(/Ello/i);//忽略大小写
    console.log("search 测试 结果2："+index2);//1
    
    //replace 测试
    var str = "hello world world";
    var exp = /world/;
    var exp1= /world/g; //全局匹配
   //替换字符串
    var replaceResult = str.replace(exp,"JS");
    console.log("replace 测试 结果："+replaceResult);//hello JS world
    var replaceResult1 = str.replace(exp1,"JS");
    console.log("replace1 测试 结果："+replaceResult1);//hello JS JS 
}
</script>
```

#### 14.test、exec判断字符串是否存在

```jsp
<script>
 function elsser(){
     //判断对象中是否有目标字符串，有则返回true
     var exp = /world/;
     var target="hello world, this is test js";
     var result=exp.test(target);
     console.log("test 测试结果："+result);//true

     //判断对象中是否有目标字符串，有则返回目标字符串
     var exp = /world/;
     var result=exp.exec("hello world, this is test js");
     console.log("exec测试结果："+result);//world
      var exp = /rorqd/;
     var result1=exp.exec("hello world, this is test js");
     console.log("exec测试结果："+result);//null
 }
</script>
```

#### 15.JSON转JS

```jsp
<script>
    //JS学习 JSON学习
    //1.使用parse将JS转换JSON
    var testStudent='{"student":[{"name":"zhangsan","age":18},{"name":"lisi","age":20},{"name":"wangli","age":19}]}';
    var student=JSON.parse(testStudent);
    console.log("JSON 测试："+student.student[1].name+",age="+student.student[1].age);
    console.log("JSON 测试："+student.student[2].name+",age="+student.student[2].age);
    // JSON 测试：lisi,age=20
    // JSON 测试：wangli,age=19

    //2.使用eval将JS转换SJSON
    var testStudent='{"student":[{"name":"zhangsan","age":18},{"name":"lisi","age":20},{"name":"wangli","age":19}]}';
    var student=eval("("+testStudent+")");
    console.log(student.student[1].name+",age="+student.student[1].age);
    //lisi,age=20
</script>
```

## 正则表达式

#### 1.概述

定义：描述或者匹配一系列符合某个语法规则的字符串的单个字符串。

溯源两个类：

- Pattern   正则表达式的编译表示
- Matcher   对输入的字符串进行解释和匹配操作的封装
- \\插入一个正则表达式的反斜线，其后的字符具有特殊意义    \\d

#### 2.字符类

- [abc] a、b、或c 任意一个字符的匹配要求

  ```java
  String regex = "[abc]";
  System.out.println("a".matches(regex)); //true
  System.out.println("t".matches(regex)); //false
  ```

- [^abc] ^O^ 取[abc]的补，a、b、或c之外的任何字符 

  ```java
  String regex1 = "[^abc]";
  System.out.println("a".matches(regex1)); //false
  System.out.println("t".matches(regex1)); //true
  ```

- [a-zA-Z]  26个大小写字母

  ```java
  String regex1 = "[a-zA-Z]";
  System.out.println("a".matches(regex1)); //true
  System.out.println("5".matches(regex1)); //false
  String regex1 = "[a-e&&[def]]"; //取交集
  System.out.println("a".matches(regex1)); //false
  System.out.println("e".matches(regex1)); //true
  ```

- [0-9]   0-9的字符

  ```java
  String regex1 = "[0-9]";
  System.out.println("1".matches(regex1));
  System.out.println("a".matches(regex1));
  ```

#### 3.预定义字符类

- . 任何一个字符

- ..任何两个字符

  ```java
  String regex1 = "..";
  System.out.println("a".matches(regex1)); //false
  System.out.println("aa".matches(regex1)); //true
  ```

- \ \d 数字：[0-9]

  ```java
  String regex1 = "\\d";
  System.out.println("3".matches(regex1)); //true
  System.out.println("a".matches(regex1)); //false
  ```

- \ \D 求反 [ ^0-9]

  ```java
  String regex1 = "\\D";
  System.out.println("3".matches(regex1)); //false
  System.out.println("a".matches(regex1)); //true
  ```

- \ \w 单词字符：[a-zA-Z_0-9]

  ```java
  String regex1 = "\\w";
  System.out.println("3".matches(regex1)); //true
  System.out.println("b".matches(regex1)); //true
  System.out.println("+".matches(regex1)); //false
  ```

- \ \W 求反 [ ^\w]

  ```java
  String regex1 = "\\w";
  System.out.println("3".matches(regex1)); //false
  System.out.println("b".matches(regex1)); //false
  System.out.println("+".matches(regex1)); //true
  ```

- \ \s 空白字符   

  注意：不同键的输出结果相同，但信号实质不同。是tab键的结果，而不是""或空格键。

  ```java
  String regex1 = "\\s";
  System.out.println("3".matches(regex1)); //false
  System.out.println(" ".matches(regex1)); //true，一个空格
  System.out.println("	".matches(regex1)); //true，一个Tab键
  ```

- \ \S 非空白字符 数字或字母字符、下划线   

  ```java
  String regex1 = "\\S";
  System.out.println("3".matches(regex1)); //true
  System.out.println(" ".matches(regex1)); //false
  ```

#### 4.数量词

- X?  X一次或一次也没有

  ```java
  String regex1 = "[abc]?";
  System.out.println("a".matches(regex1)); //true
  System.out.println("b".matches(regex1)); //true
  System.out.println("d".matches(regex1)); //false
  System.out.println(" ".matches(regex1)); //false
  System.out.println("".matches(regex1));  //true
  ```

- X*  X零次->多次

  ```java
  String regex1 = "[abc]*";
  System.out.println("a".matches(regex1)); //true
  System.out.println("d".matches(regex1)); //false
  System.out.println("".matches(regex1)); //true
  System.out.println("abc".matches(regex1)); //true
  ```

- X+  X1次或多次

  ```java
  String regex1 = "[abc]+";
  System.out.println("a".matches(regex1)); //true
  System.out.println("d".matches(regex1)); //false
  System.out.println("".matches(regex1)); //false
  System.out.println("abc".matches(regex1)); //true
  ```

- X{n} X正好n次

  ```java
  String regex1 = "[abc]{5}";
  System.out.println("abcab".matches(regex1)); //true
  System.out.println("abcabcabc".matches(regex1)); //false
  System.out.println("abcag".matches(regex1)); //false
  ```

- X{n,} X至少n次

  ```java
   String regex1 = "[abc]{5,}";
  System.out.println("abcab".matches(regex1)); //true
  System.out.println("abcabcabc".matches(regex1)); //true
  System.out.println("abc".matches(regex1)); //false
  System.out.println("abafesg".matches(regex1)); //false
  ```

- X{n,m} X至少n次，但不超过m次；包含上下限数次。

  ```java
  String regex1 = "[abc]{5,7}";
  System.out.println("abcab".matches(regex1)); //true
  System.out.println("abcabcc".matches(regex1)); //true
  System.out.println("abcbbcaaa".matches(regex1)); //false
  ```

#### 5.分割功能 split()

```java
String str = "床前明月光,疑是地上霜";
String[] arr = str.split("\\,"); //通过正则表达式分割字符串
for (int i = 0; i < arr.length; i++) {
    System.out.println(arr[i]);
}
//床前明月光
//疑是地上霜
```

#### 6.替换功能  replaceAll(规则，替换对象)

```java
String str = "ni111hao222zhongbei";
String regex = "\\d";
String str1 = str.replaceAll(regex, ""); //将字符串中的数字替换为空
System.out.println(str1); //结果：nihaozhongbei
```

#### 7.分组功能

- （（））通过从左到右计算开括号来编号，进而进行分组。
- 比如 ((A)(B(C)))分组后成了四组：
      
  - ((A)(B(C)))
  - (A)
  - (B(C)))
  - (C)

```java
   String regex = "(..)\\1"; // \1表示重复正则第一个圆括号内匹配到的内容
  System.out.println("高高兴兴".matches(regex)); //false
  System.out.println("快乐快乐".matches(regex)); //true
```

  实质：替换功能的延申

```java
String s = "我我..要.我要..学学..学.学..编程..程..程...程";
String s2 = s.replaceAll("\\.", ""); //异常或多次
System.out.println(s2); //结果：我我要我要学学学学编程程程程
```

#### 8、获取功能

- Pattern：正则表达式的编译表示形式。
- Matcher：通过解释 Pattern 对 character sequence （字符序列）执行匹配操作的引擎。 

```java
String str = "号码13893441835，我曾用过18492732323，还用过18839472842 ";
String regex = "1[3578]\\d{9}";
Pattern p = Pattern.compile(regex); //将给定的正则表达式编译到模式中
Matcher m = p.matcher(str); //创建匹配给定输入与此模式的匹配器
while(m.find()) //尝试查找与该模式匹配的输入序列的下一个子序列
    System.out.println(m.group());
/* 结果：  13893441835
          18492732323
          18839472842*/
```

**1.【数量：单个】字符匹配**

- 任意字符：表示由任意字符组成；
- \ \:匹配“\”
- \ n:匹配换行
- \ t:匹配制表符

**2.【数量：单个】字符集（可以从里面任选一个字符）**

- [abc]: 表示可能是字母a、b、c中的任意一个；

- [^abc]: 表示不是字母a、b、c中的任意一个；

- [a-zA-Z]: 表示由一个任意字母所组成，不区分大小写;

- [0-9]: 表示由任意一个数字组成；

**3.【数量：单个】简化字符集**

- . :表示任意的一个字符；
- \d:等价于“[0-9]”范围；
- \D:等价于“[ ^0-9]”范围;
- \s:匹配任意的一位空格，可能是空格、换行、制表符；
- \S:匹配任意的非空格数据；
- \w:匹配字母、数字、下划线，等价于“[a-zA-Z 0-9]”
- \W:匹配非字母、数字、下划线，等价于“[ ^a-zA-Z 0-9]”

**4,边界匹配**

- ^:匹配边界开始；
- $:匹配边界结束；

**5.数量表示，默认情况下只有添加上了数量单位才可以匹配多位字符；**

- 表达式？：该正则可以出现0次或1次；
- 表达式*：该正则可以出现0次，1次或n次；
- 表达式+：该正则可以出现1次或n次；
- 表达式{n}：表达式的长度正好为n次；
- 表达式{n,}：表达式的长度为n次以上；
- 表达式{n,m}：表达式的长度在n~m次；

**6.逻辑表达式：可以连接多个正则**

- 表达式X表达式Y：X表达式之后紧跟上Y表达式；
- 表达式X|表达式Y：有一个表达式满足即可；
- (表达式)：为表达式设置一个整体描述，可以为整体描述设置数量单位。