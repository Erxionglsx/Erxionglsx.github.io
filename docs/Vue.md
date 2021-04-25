# Vue

> [Vue视频教程](https://www.bilibili.com/video/BV12J411m7MG?p=1)

------

[TOC]

## Vue简介

* <font color="lighblue">JavaScript</font>框架
* 简化<font color="lighblue">Dom</font>操作
* <font color="lighblue">响应式</font>数据驱动

引入Vue

```javascript
<!-- 开发环境版本，包含了有帮助的命令行警告 -->
<script src="https://cdn.jsdelivr.net/npm/vue/dist/vue.js"></script>
```

## 声明式渲染

#### el:挂载点

```html
<body>
    <div id="app" class="app">
        {{ message }}
      </div>
    <!-- 开发环境版本，包含了有帮助的命令行警告 -->
    <script src="https://cdn.jsdelivr.net/npm/vue/dist/vue.js"></script>
    <script>
        var app = new Vue({
            // el: "#app", //id选择器
            // el:".app", //类选择器
            el:"div",//标签选择器
            data: {
                message: 'Hello Vue！'
            }
        })
    </script>
</body>
<!--Hello Vue!-->
```

**Vue实例的作用范围是什么呢？**

Vue会管理el选项<font color="lighblue">命中的元素</font>及其内部的<font color="lighblue">后代元素</font>

**是否可以使用其他的选择器？**

可以只用其他的选择器，但是建议使用<font color="lighblue">ID选择器</font>

**是否可以设置其他的dom元素？**

可以使用其他的双标签，不能使用<font color="lighblue">HTML和BODY</font>

#### data:数据对象

- Vue中用到的数据定义在<font color="lighblue">data</font>中
- data中可以写<font color="lighblue">复杂类型</font>的数据
- 渲染复杂类型数据时，遵守js的<font color="lighblue">语法</font>即可

```html
<body>
    <div id="app">
        {{ message }}
        <h2>{{school.name}} {{school.mobile}}</h2>
        <ul>
            <li>{{campus[0]}}</li>
            <li>{{campus[1]}}</li>
            <li>{{campus[2]}}</li>
        </ul>
    </div>
    <!-- 开发环境版本，包含了有帮助的命令行警告 -->
    <script src="https://cdn.jsdelivr.net/npm/vue/dist/vue.js"></script>
    <script>
        var app = new Vue({
            el:"#app",
            data:{
                message:"你好 世界",
                school:{
                    name:"程序员",
                    mobile:"200-4335-2344"
                },
                campus:["北京","上海","广州"]
            }
        })
    </script>
</body>
<!--
    你好 世界
    程序员 200-4335-2344
    北京
    上海
    广州
-->
```

#### methods:方法

## Vue方法

### 数组方法

**push():** 方法可向数组的末尾添加一个或多个元素，并返回新的长度。

**pop():** 方法用于删除并返回数组的最后一个元素。

**shift():** 方法用于把数组的第一个元素从其中删除，并返回第一个元素的值。

**unshift():** 方法可向数组的开头添加一个或更多元素，并返回新的长度。

**splice():** 方法向/从数组中添加/删除项目，然后返回被删除的项目。

**sort():** 方法用于对数组的元素进行排序。

**reverse():** 方法用于颠倒数组中元素的顺序。

### 替换数组

**filter():** 方法创建一个新的数组，新数组中的元素是通过检查指定数组中符合条件的所有元素。

**concat():** 方法用于连接两个或多个数组。

**slice():** 方法可从已有的数组中返回选定的元素。

### 其他

**split():** 方法用于把一个字符串分割成字符串数组。

## 本地应用

### Vue指令

#### v-text 

* <font color="lighblue">v-text</font>指令的作用是：设置标签的内容(textContext)
* 默认写法会替换全部内容，使用差值表达式{{}}可以替换指定内容
* 内部支持表达式

```html
<body>
    <div id="app">
        <h2 v-text="message+'!'">成功</h2>
        <h2 v-text="info+'!'">成功</h2>
        <h2>{{message+"!"}}成功</h2>
    </div>
    <!-- 开发环境版本，包含了有帮助的命令行警告 -->
    <script src="https://cdn.jsdelivr.net/npm/vue/dist/vue.js"></script>
    <script>
    var app = new Vue({
        el:"#app",
        data:{
            message:"你好，程序员",
            info:"前端和后端"
        }
    })
    </script>
</body>
<!--
    你好，程序员!
    前端和后端!
    你好，程序员!成功
-->
```

#### v-html

- <font color="lighblue">v-html</font>指令的作用是：设置元素的<font color="lighblue">innerHTML</font>
- 内容中有<font color="lighblue">html</font>结构会被解析为<font color="lighblue">标签</font>
- <font color="lighblue">v-text</font>指令无论内容是什么，只会解析为<font color="lighblue">文本</font>

```html
<body>
    <div id="app">
        <p v-html="message"></p>
        <p v-text="message"></p>
    </div>
    <!-- 开发环境版本，包含了有帮助的命令行警告 -->
    <script src="https://cdn.jsdelivr.net/npm/vue/dist/vue.js"></script>
    <script>
        var app = new Vue({
            el:"#app",
            data:{
                message:"<a href='http://www.baidu.com'>百度</a>"
            }
        })
    </script>
</body>
<!--
	百度 //是链接，可直接访问
	<a href='http://www.baidu.com'>百度</a>  //原样输出
-->
```

#### v-on

- <font color="lighblue">v-on</font>指令的作用是：为元素绑定<font color="lighblue">事件</font>
- 事件名不需要写<font color="lighblue">on</font>
- 指令可以简写为<font color="lighblue">@</font>
- 绑定的方法定义在<font color="lighblue">methods</font>属性中

```html
<body>
    <div id="app">
        <input type="button" value="v-on指令" v-on:click="hello"></input>
        <input type="button" value="v-on简写" @click="hello"></input>
        <input type="button" value="双击事件" @dblclick="hello"></input>
        <h2 @click="changeFood">{{food}}</h2>
    </div>
    <!-- 开发环境版本，包含了有帮助的命令行警告 -->
    <script src="https://cdn.jsdelivr.net/npm/vue/dist/vue.js"></script>
    <script>
        var app = new Vue({
            el:"#app",
            data:{
                food:"西蓝花炒蛋"
            },
            methods:{
                hello:function(){
                    alert("你好！");
                },
                changeFood:function(){
                    this.food+="好好吃!"
                }
            }
        })
    </script>
</body>
```

* 事件绑定的方法写成<font color="lighblue">函数调用</font>的形式，可以传入自定义参数
* 定义方法时需要定义<font color="lighblue">形参</font>来接收传入的实参
* 事件的后面跟上 <font color="lighblue">.修饰符</font> 可以对事件进行限制
* <font color="lighblue">.enter</font> 可以限制触发的按键为回车

```vue
<body>
    <script src="https://cdn.jsdelivr.net/npm/vue/dist/vue.js"></script>
    <div id="app">
        <!-- 传参 -->
        <input type="button" value="点击" @click="doIt(666,'老铁')">
        <!-- 按下Enter键触发 -->
        <input type="text" @keyup.enter="sayHi">
    </div>
    
    <script>
        var app = new Vue({
            el:"#app",
            methods:{
                doIt:function(p1,p2){
                    console.log("123");
                    console.log(p1);
                    console.log(p2);
                },
                sayHi:function(){
                    alert("吃了没？");
                }
            }
        })
    </script>
</body>
```

#### 计数器

- 创建Vue示例时：<font color="lighblue">el</font>(挂载点),<font color="lighblue">data</font>(数据)，<font color="lighblue">methods</font>(方法)
- <font color="lighblue">v-on</font>指令的作用是绑定事件，简写为<font color="lighblue">@</font>
- 方法中通过<font color="lighblue">this</font>，关键字获取<font color="lighblue">data</font>中的数据
- <font color="lighblue">v-text</font>指令的作用是：设置元素的<font color="lighblue">文本值</font>，简写为<font color="lighblue">{{}}</font>

```html
<body>
    <div id="app">
        <div class="input-num">
            <button @click="sub">
                -
            </button>
            <span>{{num}}</span>
            <button @click="add">
                +
            </button>
        </div>
    </div>
    <!-- 开发环境版本，包含了有帮助的命令行警告 -->
    <script src="https://cdn.jsdelivr.net/npm/vue/dist/vue.js"></script>
    <script>
        var app = new Vue({
            el:"#app",
            data:{
                num:1
            },
            methods:{
                add:function(){
                    if(this.num<10){
                        this.num++;
                    }else{
                        alert("值只能在0~10之间！");
                    }
                },
                sub:function(){
                    if(this.num>0){
                        this.num--;
                    }else{
                        alert("值只能在0~10之间！");
                    }
                }
            }
        })
    </script>
</body>
```

#### v-show

- <font color="lighblue">v-show</font>指令的作用是：根据真假切换元素的显示状态
- 原理是修改元素的display，实现显示隐藏
- 指令后面的内容，最终都会解析为<font color="lighblue">布尔值</font>
- 值为<font color="lighblue">true</font>元素显示，值为<font color="lighblue">false</font>元素隐藏
- 数据改变之后，对应元素的显示状态会<font color="lighblue">同步更新</font>

```vue
<body>
    <script src="https://cdn.jsdelivr.net/npm/vue/dist/vue.js"></script>
    <div id="app">
       <input type="button" @click="changeIsShow" value="切换显示">
       <img v-show="isShow" src="img/2177.jpg" style="width: 30%;">
       <!--累加年龄，到达18岁时显示图片-->
       <input type="button" @click="addAge" value="累加年龄">
       <img v-show="age>=18" src="img/2177.jpg" style="width: 30%;">
    </div>
    <script>
        var app = new Vue({
            el:"#app",
            data:{
                isShow:false,
                age:16
            },
            methods:{
                changeIsShow:function(){
                    this.isShow = !this.isShow
                },
                addAge:function(){
                    this.age++
                }
            }
        })
    </script>
</body>
```

#### v-if

* <font color="lighblue">v-if</font>指令是根据表达值的真假，切换元素的显示和隐藏
* 频繁切换用v-show，反之用v-if
* 本质是通过操纵<font color="lighblue">dom</font>元素来切换显示状态
* 表达式的值为<font color="lighblue">true</font>，元素存在于<font color="lighblue">dom</font>树中，为false，从<font color="lighblue">dom</font>树中移除

```vue
<body>
    <div id="app">
        <p v-if="isShow">软件工程</p>
        <p v-show="isShow">软件工程 v-show修饰</p>
        <input type="button" @click="toggleIsShow" value="切换">
        <p v-if="temperature>35">热死啦</p>
    </div>
    <script src="https://cdn.jsdelivr.net/npm/vue/dist/vue.js"></script>
    <script>
        var app = new Vue({
            el:"#app",
            data:{
                isShow:true,
                temperature:40
            },
            methods:{
                toggleIsShow:function(){
                    this.isShow = !this.isShow;
                }
            }
        })
    </script>
</body>
```

#### v-bind(class/src/title)

- <font color="lighblue">v-bind</font>指令的作用是：为元素绑定属性
- 完整写法是<font color="lighblue">v-bind:属性名</font>
- 简写的话可以直接省略<font color="lighblue">v-bind</font>，只保留：<font color="lighblue">属性名</font>

 ```vue
<head>
    <meta charset="UTF-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>v-bind</title>
    <style>
        .active{
            border: 5px solid blueviolet;
        }
    </style>
</head>
<body>
    <script src="https://cdn.jsdelivr.net/npm/vue/dist/vue.js"></script>
    <div id="app">
        <img v-bind:src="imgSrc" width="30%">
        <br>
        <img :src="imgSrc" width="30%" :title="imgTitle+'!!!'" 
        :class="isActive?'active':''" @click="toggleActive">
        <br>
        <!--三元表达式简写-->
        <img :src="imgSrc" width="30%" :title="imgTitle+'!!!'" 
        :class="{active:isActive}" @click="toggleActive">
    </div>
    <script>
        var app = new Vue({
            el:"#app",
            data:{
                imgSrc:"img/2177.jpg",
                imgTitle:"程序员就是我",
                isActive:false
            },
            methods:{
                toggleActive:function(){
                    this.isActive = !this.isActive
                }
            }
        })
    </script>
</body>
 ```
#### v-bind:title

- title属性规定关于元素的额外信息。
- 这些信息通常会在鼠标移到元素上时显示一段工具<font color="lighblue">提示文本</font>
- v-bind:title 指令把所在的<span>元素的 title 的值和 message这个属性值绑定在一起了。
- 网页效果：title 显示的值就是message的属性值

```html
<body>
    <div id="app">
        <span v-bind:title="message">
            鼠标悬停几秒查看提示信息！
        </span>
    </div>
    <!-- 开发环境版本，包含了有帮助的命令行警告 -->
    <script src="https://cdn.jsdelivr.net/npm/vue/dist/vue.js"></script>
    <script>
    var app = new Vue({
        el:"#app",
        data:{
            message:'页面加载于'+new Date().toLocaleString()
        }
    })
</script>
</body>
```

#### 切换图片

列表数据使用数组保存

```vue
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>图片切换</title>
    <style type="text/css">
        .center{
            text-align:center;
            margin-top: 100px;
        }
    </style>
</head>
<body>
    <script src="https://cdn.jsdelivr.net/npm/vue/dist/vue.js"></script>
    <div id="mask">
        <div class="center">
            <!--左箭头-->
            <a href="javascript:void(0)" class="left" @click="prev" v-show="index!=0">
                <img src="img/left.png" >
            </a>
            <!--图片-->
            <img :src="imgArr[index]" width="35%">
            <!--右箭头-->
            <a href="javascript:void(0)" class="right" @click="next" v-show="index<imgArr.length-1">
                <img src="img/right.png" >
            </a>
        </div>
    </div>
    <script>
        var app = new Vue({
            el:"#mask",
            data:{
                imgArr:[
                    "img/00.jpg",
                    "img/01.jpg",
                    "img/02.jpg",
                    "img/03.jpg",
                    "img/04.jpg",
                    "img/05.jpg"
                ],
                index:0,
            },
            methods:{
                prev:function(){
                        this.index--;
                },
                next:function(){
                        this.index++;
                }
            }
        })
    </script>
</body>
</html>
```

![](https://note.youdao.com/yws/api/personal/file/C2CA429C8DA54509B3EAC9D9409C97E4?method=download&shareKey=72d831aff4d5bdc63b6c9a8e1d3bc40b)

#### v-for

* <font color="lighblue">v-for</font>指令的作用是：根据数据生成列表结构
* 数组经常和<font color="lighblue">v-for</font>结合使用
* 语法是<font color="lighblue">(item,index) in 数据</font>
* item和index可以结合其他指令一起使用
* 数组长度的更新会同步到页面上，是响应式的

```vue
<body>
    <div id="app">
        <input type="button" value="添加数据" @click="add">
        <input type="button" value="删除数据" @click="remove">
        <ul>
            <li v-for="(item,index) in arr">
                {{index+1}} 中北大学:{{item}}
            </li>
        </ul>
        <h2 v-for="item in vegetables" v-bind:title="item.name">
            {{item.name}}
        </h2>
    </div>
    <script src="https://cdn.jsdelivr.net/npm/vue/dist/vue.js"></script>
    <script>
        var app = new Vue({
            el:"#app",
            data:{
                arr:["北京","上海","南京","无锡"],
                vegetables:[
                    {name:"西红柿炒鸡蛋"},
                    {name:"蒜薹炒肉"}
                ]
            },
            methods:{
                add:function(){
                    // 添加数据
                    this.vegetables.push({name:"花菜炒蛋"});
                },
                remove:function(){
                    // 把数组的第一个元素从其中删除，并返回第一个元素的值
                    this.vegetables.shift();
                }
            }
        })
    </script>
</body>
```

#### v-model

* <font color="lighblue">v-model</font>指令的作用是便捷的设置和获取表单元素的值
* 绑定的数据会和表单元素<font color="lighblue">值</font>相关联
* 绑定的数据<font color="lighblue">←→</font>表单元素的值

```vue
<body>
    <script src="https://cdn.jsdelivr.net/npm/vue/dist/vue.js"></script>
    <div id="app">
        <input type="text" v-model="message" @keyup.enter="getM">
        <h2>{{message}}</h2>
        <input type="button" value="修改message" @click="setM">
    </div>
    <script>
        var app = new Vue({
            el:"#app",
            data:{
                message:"山西太原"
            },
            methods:{
                getM:function(){
                    alert(this.message);
                },
                setM:function(){
                    this.message="崛围山"
                }
            }
        })
    </script>
</body>
```

#### 备忘录

* 列表结构可以通过<font color="lighblue">v-for</font>指令结合数据生成
* <font color="lighblue">v-on</font>结合事件修饰符可以对事件进行限制，比如 <font color="lighblue">.enter</font>
* <font color="lighblue">v-on</font>在绑定事件时可以传递自定义参数
* 通过<font color="lighblue">v-model</font>可以快速的设置和获取表单元素的值
* 基于数据的开发方式

```vue
<!-- 还需导入example.js和index.css文件 -->
<html>
  <head>
    <meta http-equiv="content-type" content="text/html; charset=UTF-8" />
    <title>备忘录</title>
    <meta http-equiv="content-type" content="text/html; charset=UTF-8" />
    <meta name="robots" content="noindex, nofollow" />
    <meta name="googlebot" content="noindex, nofollow" />
    <meta name="viewport" content="width=device-width, initial-scale=1" />
    <link rel="stylesheet" type="text/css" href="./css/index.css" />
  </head>

  <body>
    <!-- 主体区域 -->
    <section id="todoapp">
      <!-- 输入框 -->
      <header class="header">
        <h1>备忘录</h1>
        <input
          autofocus="autofocus"
          autocomplete="off"
          placeholder="请输入内容"
          class="new-todo"
          v-model="inputValue"
          @keyup.enter="add"
        />
      </header>
      <!-- 列表区域 -->
      <section class="main">
        <ul class="todo-list">
          <li class="todo" v-for="(item,index) in list">
            <div class="view">
              <span class="index">{{index+1}}</span> 
              <label>{{item}}</label>
              <button class="destroy" @click="remove(index)"></button>
            </div>
          </li>
        </ul>
      </section>
      <!-- 统计和清空 -->
      <footer class="footer">
        <!-- 使用v-if隐藏 -->
        <span class="todo-count" v-if="list.length!=0"> 
          <!-- 统计数据条数 -->
          <strong>共 {{list.length}} </strong>条
        </span>
        <!-- 使用v-show隐藏 -->
        <button class="clear-completed" @click="removeAll" v-show="list.length!=0">
           清除
        </button>
      </footer>
    </section>
    <!-- 开发环境版本，包含了有帮助的命令行警告 -->
    <script src="https://cdn.jsdelivr.net/npm/vue/dist/vue.js"></script>
    <script>
      var app = new Vue({
        el:"#todoapp",
        data:{
          list:["吃饭","睡觉","办卡","敲代码"],
          inputValue:""
        },
        methods:{
          add:function(){
            //push添加数组元素
            this.list.push(this.inputValue);
          },
          remove:function(p){
            console.log(p);
            //splice(p,1)删除数组中从p开始，长度为1的值
            this.list.splice(p,1);
          },
          removeAll:function(){
            this.list=[];
          }
        }
      })
    </script>
  </body>
</html>
```

![](https://note.youdao.com/yws/api/personal/file/5ECF4C116D274887926B612B223F6867?method=download&shareKey=130a3028ccdefb88c4c7919ee83f867e)

## axios网络请求库

```vue
<!--官网提供的axios在线地址-->
<script src="http://unpkg.com/axios/dist/axios.min.js"></script>
```

* <font color="lighblue">axios</font>必须先导入才可以使用
* 使用<font color="lighblue">get</font>或<font color="lighblue">post</font>方法即可发送对应的请求
* <font color="lighblue">then</font>方法中的回调函数会在请求成功或失败时触发
* 通过回调函数的形参可以获取响应内容，或错误信息

```vue
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>axios</title>
</head>
<body>
    <input type="button" value="get请求" class="get">
    <input type="button" value="post请求" class="post">
    <!--官网提供的axios在线地址-->
    <script src="http://unpkg.com/axios/dist/axios.min.js"></script>
    <script>
        /*
            接口1：随机笑话
            请求地址：https://autumnfish.cn/api/joke/list
            请求方法：get
            请求参数：num(笑话条数，数字)
            响应内容：随机笑话
        */
       document.querySelector(".get").onclick = function(){
           axios.get("https://autumnfish.cn/api/joke/list?num=3")
           .then(function(response){
               console.log(response);
           }),function(err){
               console.log(err);
           }
        }
       /*
            接口2：用户注册
            请求地址：https://autumnfish.cn/api/user/reg
            请求方法：post
            请求参数：username(用户名，字符串)
            响应内容：注册成功或失败
       */   
       document.querySelector(".post").onclick = function(){
           axios.post("https://autumnfish.cn/api/user/reg",
           {username:"ase"})
           .then(function(response){
               console.log(response);
           }),function(err){
               console.log(err);
           }
        }
    </script>
</body>
</html>
```

**get请求**

![](https://note.youdao.com/yws/api/personal/file/B9C396C1CC424E26B0665CEEE4C3CEC8?method=download&shareKey=69cc09fe280898cc19e43ef0470c8a3b)

**post请求**

![](https://note.youdao.com/yws/api/personal/file/07257F2C6C2C41E78A3F01D216978AF0?method=download&shareKey=485048b8be939ad5fe8f2a23b0f1fd8a)

#### axios+vue

* <font color="lighblue">axios</font>回调函数中的<font color="lighblue">this</font>已经改变，无法访问到<font color="lighblue">data</font>中的数据
* 把<font color="lighblue">this</font>保存起来，回调函数中直接使用保存的<font color="lighblue">this</font>即可
* 和本地应用的最大区别就是改变了<font color="lighblue">数据来源</font>

```vue
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>axios+vue</title>
</head>
<body>
    <div id="app">
        <input type="button" value="获取笑话" @click="getJoke">
        <p>{{joke}}</p>
    </div>
    <!-- 官网提供的 axios 在线地址 -->
    <script src="https://unpkg.com/axios/dist/axios.min.js"></script>
    <!-- 开发环境版本，包含了有帮助的命令行警告 -->
    <script src="https://cdn.jsdelivr.net/npm/vue/dist/vue.js"></script>
    <script>
        /*
            接口:随机获取一条笑话
            请求地址:https://autumnfish.cn/api/joke
            请求方法:get
            请求参数:无
            响应内容:随机笑话
        */
        var app = new Vue({
            el:"#app",
            data:{
                joke:"好笑的笑话"
            },
            methods:{
                getJoke:function(){
                    axios.get("https://autumnfish.cn/api/joke").then
                    (function(response){
                        console.log(response.data);
                        app.joke=response.data;
                    },
                    function(err){
                        console.log(err);
                    })
                }
            }
        })
    </script>
</body>
</html>
```

#### 天知道

```vue
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="UTF-8" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />
    <meta http-equiv="X-UA-Compatible" content="ie=edge" />
    <title>天知道</title>
    <link rel="stylesheet" href="css/reset.css" />
    <link rel="stylesheet" href="css/index.css" />
  </head>

  <body>
    <div class="wrap" id="app">
      <div class="search_form">
        <div class="logo"><img src="img/logo.png" alt="logo" /></div>
        <div class="form_group">
          <input
            type="text"
            @keyup.enter="searchWeather"
            v-model="city"
            class="input_txt"
            placeholder="请输入查询的天气"
          />
          <button class="input_sub">
            搜 索
          </button>
        </div>
        <div class="hotkey">
          <a href="javascript:;" @click="changeCity('北京')">北京</a>
          <a href="javascript:;" @click="changeCity('上海')">上海</a>
          <a href="javascript:;" @click="changeCity('广州')">广州</a>
          <a href="javascript:;" @click="changeCity('深圳')">深圳</a>
          <a href="javascript:;" @click="changeCity('太原')">太原</a>
        </div>
      </div>
      <ul class="weather_list">
        <li v-for="item in weatherList">
          <div class="info_type"><span class="iconfont">{{item.type}}</span></div>
          <div class="info_temp">
            <b>{{item.low}}</b>
            ~
            <b>{{item.high}}</b>
          </div>
          <div class="info_date"><span>{{item.date}}</span></div>
        </li>
      </ul>
    </div>
    <!-- 开发环境版本，包含了有帮助的命令行警告 -->
    <script src="https://cdn.jsdelivr.net/npm/vue/dist/vue.js"></script>
    <!-- 官网提供的 axios 在线地址 -->
    <script src="https://unpkg.com/axios/dist/axios.min.js"></script>
    <!-- 自己的js -->
    <script src="js/main.js"></script>
  </body>
</html>
```
main.js

```js
/*
  请求地址:http://wthrcdn.etouch.cn/weather_mini
  请求方法:get
  请求参数:city(城市名)
  响应内容:天气信息

  1. 点击回车
  2. 查询数据
  3. 渲染数据
  */
var app = new Vue({
    el:"#app",
    data:{
        city:'',
        weatherList:[]
    },
    methods:{
        searchWeather:function(){
            // console.log(this.city);
            axios.get("http://wthrcdn.etouch.cn/weather_mini?city="+this.city)
            .then(function(response){
                // console.log(response.data.data.forecast);
                //this指向已发生改变
                app.weatherList = response.data.data.forecast;
            })
        },
        changeCity:function(city){
            app.city = city;
            //methods中定义的方法内部，可以通过this关键字点出其他方法
            this.searchWeather();
        }
    }
})
```

![](https://note.youdao.com/yws/api/personal/file/A1755517EB14498E9DA8578E75681678?method=download&shareKey=530d64d6b7f7417047e1a250e7155df0)

