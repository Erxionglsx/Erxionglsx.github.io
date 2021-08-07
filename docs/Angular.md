## Angular

> https://www.bilibili.com/video/BV1bt411e71b?p=1

------

[TOC]

#### AngularJS是什么？

1. AngularJS是什么?

   AngularJS 是一个 **JavaScript 框架**。它可通过 <script> 标签添加到 HTML 页面。

   - Google开源的 前端JS 结构化 框架
   - 动态展示页面数据, 并与用户进行交互
   - AngularJS特性(优点)
     - 双向数据绑定
     - 声明式依赖注入
     - 解耦应用逻辑, 数据模型和视图
     - 完善的页面指令
     - 定制表单验证
     - Ajax封装

**需先安装Node.js，再安装Angular**

#### 项目结构

- package.json :项目的配置文件，定义名称、版本和各种依赖
- node_modules：安装的第三方模块放在这里
- src  项目的所有文件放在src里面
  - /app：组件、服务、根模块
    * app.module.ts：Angular根模块，告诉Angular如何组装应用
      * declarations：配置当前项目运行的组件
      * imports：配置当前模块运行依赖的其他模块
      * providers：配置项目所需的服务
  - /assets：静态资源文件
  - /environments：为目标环境准备的文件
  - browserslist：支持的浏览器
  - index.html：运行的首页
  - main.ts：项目的入口文件
  - test.ts：测试的入口文件
  - styles.scss：全局样式

#### Angular项目的创建启动

```java
打开cmd命令，进入Angular的工作空间
//创建Angular项目:ng new 项目名
ng new Angular01
//cd进入项目，运行项目
ng serve --open
//停止项目 Ctrl+C
//创建组件
ng g  //然后选择要创建的组件类型
//创建components文件夹，并在其下创建news组件
ng g component components/news //通过命令创建，app.module.ts中会自动生成配置
//app.module.ts文件中会自动生成相关组件配置
//在app.component.html中通过<app-news></app-news>引用
```

#### 绑定数据

news.components.ts

```ts
import { Component, OnInit } from '@angular/core';
@Component({
  selector: 'app-news',
  templateUrl: './news.component.html',
  styleUrls: ['./news.component.scss']
})
export class NewsComponent implements OnInit {
  msg="我是一个新闻组件msg";
  //默认为public
  public title="我是一个新闻组件--";
  //指定类型
  username:String="张三";
  public number:String="我是number";
  private number1:String="number1";
  protected number2:String="number2";
  //any:可为任意类型
  public student:any="我是一个学生属性";
  public userInfo:any={
    username:"李四",
    age:'18'
  }
  //定义属性，未赋值,在constructor()中赋值
  public message:any;

  constructor() {
    this.message='这是给属性赋值--(改变属性的值)';
    //改变属性的值
    this.msg="我是改变后的msg的值";
  }

  ngOnInit(): void {
  }
}
```

news.component.html

```html
//引用其他组件
<app-header></app-header>

<h1>{{title}}</h1>
<h2>{{msg}}</h2>
<h3>{{username}}</h3>
<h4>{{number}}</h4>
<h5>{{student}}</h5>
<h6>{{userInfo.username}}</h6>
<h6>{{message}}</h6>
```

##### 声明属性的几种方式

* public：默认为public，可以在这个类里面使用，也可以在类外面使用
* protected：只有在当前类和它的子类里面可以访问
* private：只有在当前类才可以访问这个属性

#### 绑定属性

鼠标移上去会显示title信息

```ts
//.ts文件
export class NewsComponent implements OnInit {
    public student:any="我是一个学生属性";
    public content="<h2>我是一个HTML标签</h2>";
}
```

```html
<!--绑定属性-->
<div title="我是一个div">
  鼠标瞄上去看一下
</div>
<br>
<div [title]="student">
  张三
</div>
<!--解析html标签-->
<span [innerHTML]="content" class="red"></span>
<!--angular里做运算-->
1+2={{1+2}}
```

news.component.scss

```scss
.red{
  color:red;
}
```

#### 数据循环

```tsx
// .ts文件
export class NewsComponent implements OnInit {
	// 定义数组
 	public arr = ['111','222','333'];
    // 推荐
    public list:any[] = ["我是第一个新闻","22222","我是第三个新闻"];
    public items:Array<any> = ["我是第一个新闻","我是第二个新闻"];
    public userList:any[] = [{
        username:'张三',
        age:20
    },{
        username:'李四',
        age:24
    },{
        username:'王五',
        age:40
    }];
    
    public cars:any[] = [
    {
      cate:"宝马",
      list:[
        {
          title:"宝马x1",
          price:"20万"
        },
        {
          title:"宝马x2",
          price:"25万"
        },
        {
          title:"宝马x3",
          price:"30万"
        },
      ]
    },
    {
      cate:"奥迪",
      list:[
        {
          title:"奥迪q1",
          price:"20万"
        },
        {
          title:"奥迪q2",
          price:"25万"
        },
        {
          title:"奥迪q3",
          price:"30万"
        }
      ]
    }
  ]
    
}
```

```html
<!--angular里数据循环-->
<ul>
    <li *ngFor="let item of arr">
        {{item}}
    </li>
</ul>
<ol>
    <li *ngFor="let item of list">
        {{item}}
    </li>
</ol>
<ol>
    <li *ngFor="let item of items">
        {{item}}
    </li>
</ol>
<ul>
    <li *ngFor="let item of userList">
        {{item.username}}--{{item.age}}
    </li>
</ul>
<ul>
    <li *ngFor="let item of cars">
        <h2>{{item.cate}}</h2>
        <ul *ngFor="let car of item.list">
            <li>{{car.title}}---{{car.price}}</li>
        </ul>
    </li>
</ul>
```

![](https://note.youdao.com/yws/api/personal/file/91B7B0EF99614277A7A0F8A63F633964?method=download&shareKey=e2048cb2aaa8613ce9025564c9074bf5)

#### 引入图片

```tsx
export class HomeComponent implements OnInit {
  public picUrl="https://dss2.bdstatic.com/5aV1bjqh_Q23odCf/static/superman/img/weather/icons2/a1.png";
  constructor() { }
  ngOnInit(): void {
  }
}
```

```html
<img src="assets/images/2.jpg" alt="收藏"/>
<br>
<img [src]="picUrl">
```

#### 数据循环：*ngFor 

```html
<h1>循环数据显示数据的索引（key）</h1>
    <ul>
   		<li *ngFor="let item of list;let key-index;">
            {{key}}---ifitem.title}}
         </li>
</ul>
```

#### 条件判断：*ngIf 

```html
<ul>
    <li *ngFor="let item of list;let key=index;">
         <span *ngIf="key==1" class="red">{{ key}}---{{item.title}}</span>
         <span *ngIf="key!=1">ffkey}}---iffitem.title}}</span>
    </li>
</ul>

```

#### *ngSwitch

```html
<span [ngSwitch]="orderstatus">
<p *ngSwitchcase="1">
	表示已经支付
</p>
<p *ngSwitchCase="2">
	支付并且确认订单</p>
<p *ngSwitchcase="3">
    表示已经发货
</p>
<p *ngSwitchCase="4">
	表示已经收货
</p>
<p *ngSwitchDefault>无效订单
</p>
</span>
```

#### 动态改变样式

```html
<!--[ngClass]、[ngStyle]-->
<div class="red">
	ngclass演示（尽量不要用dom来改变class)
</div>
<div [ngClass]="{ 'blue' :true,'red' :false}">
	ngclass演示
</div>
<hr>
<div [ngClassJ="{ 'orange':flag,' red' : !f1ag}">
	ngclass演示
</div>

<strong>循环数组，让数组的第一个元素的样式为red ,第二个为orange</strong>
<ul>
<li *ngFor="let item of list;let key=index;"
    [ngClass]=" {'red':key==0,'orange':key==1,'blue':key==2}">
	{{key}---{{fitem.title}}
</li>
</ul>

<p style="color:red">我是一个p标签</p>
<p [ngstyle]="{'color':'blue'}">我是一个p标签</P>
```

#### 管道

日期转换

```tsx
//.ts
public today:any=new Date();
```

```html
<h1>管道</h1>
{{today | date : 'yyyy-M-dd HH:mm:ss'}}
```

#### 事件

```tsx
//.ts
export class HomeComponent implements OnInit {
    public title:string="我是一个title";
    run(){
    alert("这是一个自定义方法");
  	}

    getDate(){
        alert(this.title);
    }

    setDate(){
        this.title = '我是修改后的数据';
    }
}
```

```html
<h1>事件</h1>
<button (click)="run()">执行事件</button>
<br>
<strong>{{title}}</strong><br>
<button (click)="getDate()">执行事件获取数据</button>
<button (click)="setDate()">执行事件改变数据</button>
```

#### 表单事件

```tsx
//.ts
export class HomeComponent implements OnInit { 
keyDown(e:any){
    if(e.keyCode==13){
      console.log('按了一下回车');
    }else{
      console.log(e.KeyCode);
    }
  }

  keyUp(e:any){
    if(e.keyCode==13){
      console.log(e.target.value);console.log("按了一下回车");
    }
  }
}
```

```html
<h1>表单事件 事件对象</h1>
keyDown:<input type="text" (keydown)="keyDown($event)"/><br>
keyUp:<input type="text"(keyup)="keyUp($event)"/>
```

#### 双向数据绑定

需先在app.module.ts的imports中引入FormsModule

模型会影响视图，视图会影响模型

```html
<h1>双向数据绑定--MWVM只是针对表单</h1>
<input type="text" [(ngModel)]="keywords'/>{{keywords}}
```

![](https://note.youdao.com/yws/api/personal/file/0F07AA98327545C29BB589AE2E4573F0?method=download&shareKey=e6dd5f020d91292e244628071bcd93ae)

#### 表单

```tsx
//form.component.ts
import { Component, OnInit } from '@angular/core';

@Component({
  selector: 'app-form',
  templateUrl: './form.component.html',
  styleUrls: ['./form.component.scss']
})
export class FormComponent implements OnInit {
  public peopleInfo:any={
    username:'',
    sex:'',
    cityList:['北京','上海','深圳'],
    city:'北京',
    hobby:[{
      title:'吃饭',
      checked:false
    },
    {
      title:'睡觉',
      checked:false
    },
    {
      title:'敲代码',
      checked:true
    }],
    mark:''
  }

  constructor() { }
  ngOnInit(): void {
  }

  doSubmit(){
      console.log(this.peopleInfo);
  }
}
```

```html
<h2>人员登记系统</h2>
<div class="people_list">
  <ul>
    <li>姓名:<input type="text" id="username" [(ngModel)]="peopleInfo.username" value="fonm_input"/></li>
    <li>性别:
      <input type="radio" value="1" name="sex" id="sex1" [(ngModel)]="peopleInfo.sex"><label for="sex1">男</label>
      <input type="radio" value="2" name="sex" id="sex2" [(ngModel)]="peopleInfo.sex"><label for="sex2">女</label>
    </li>
  </ul>

  城市:
  <select name="city" id="city" [(ngModel)]="peopleInfo.city">
     <option [value]="item" *ngFor="let item of peopleInfo.cityList">{{item}}</option>
  </select>

  爱好:
  <span *ngFor="let item of peopleInfo.hobby;let key=index; ">
  <input type="checkbox" [id]="'check'+key" [(ngModel)]="item.checked"/>
    <label [for]="'checx'+key">{{item.title}}</label>
&nbsp;&nbsp;
</span>
  <br>
  <br>
  备注:
  <br>
  <textarea name="mark" id="mark" cols="30" rOws="10" [(ngModel)]="peopleInfo.mark"></textarea>

<button (click)="doSubmit()" class="submit">获取表单的内容</button>
  <pre>
    {{peopleInfo | json}}
  </pre>
</div>

```

![](https://note.youdao.com/yws/api/personal/file/BBA8E258015F4C53988C786BF5B69169?method=download&shareKey=2d9582bcf80c204b018f25ccb5ce17ee)

## 表单模型

#### 常用表单基础类

- <font color="lighblue">FormControl</font> 实例用于追踪单个表单控件的值和验证状态。
- <font color="lighblue">FormGroup</font> 用于追踪一个表单控件组的值和状态。
- <font color="lighblue">FormArray</font> 用于追踪表单控件数组的值和状态。
- <font color="lighblue">ControlValueAccessor</font> 用于在 Angular 的FormControl实例和原生 DOM 元素之间创建一个桥梁。

[@Component](https://angular.cn/api/core/Component)

​		一个装饰器，用于把某个类标记为 Angular 组件，并为它配置一些元数据，以决定该组件在运行期间该如何处理、实例化和使用。组件必须从属于某个 NgModule 才能被其它组件或应用使用。 要想让它成为某个 NgModule 中的一员，请把它列在 `@NgModule` 元数据的 `declarations` 字段中。

[@ViewChild](https://angular.cn/api/core/ViewChild#description)

​		属性装饰器，用于配置一个视图查询。 变更检测器会在视图的 DOM 中查找能匹配上该选择器的第一个元素或指令。 如果视图的 DOM 发生了变化，出现了匹配该选择器的新的子节点，该属性就会被更新。

[OnInit](https://angular.cn/api/core/OnInit)

​		一个生命周期钩子，它会在 Angular 初始化完了该指令的所有数据绑定属性之后调用。 定义 `ngOnInit()` 方法可以处理所有附加的初始化任务。

**ngOnInit()**

​		它的调用时机在默认的变更检测器首次检查完该指令的所有数据绑定属性之后，任何子视图或投影内容检查完之前。 它会且只会在指令初始化时调用一次。

```tsx
@Component({selector: 'my-cmp', template: `...`})
class MyComponent implements OnInit {
  ngOnInit() {
    // ...
  }
}
```

[@NgModule](https://angular.cn/api/core/NgModule)

把一个类标记为 NgModule，并提供配置元数据。

* providers：在当前模块的注入器中可用的一组可注入对象。
* declarations：属于该模块的一组组件、指令和管道（统称可声明对象）。
* imports：这里列出的 NgModule 所导出的可声明对象可用在当前模块内的模板中。
* exports：此 NgModule 中声明的一组组件、指令和管道可以在导入了本模块的模块下任何组件的模板中使用。 导出的这些可声明对象就是该模块的公共 API。
* providers：在当前模块的注入器中可用的一组可注入对象。

## 生命周期函数

​		当 Angular 实例化组件类并渲染组件视图及其子视图时，组件实例的生命周期就开始了。生命周期一直伴随着变更检测，Angular 会检查数据绑定属性何时发生变化，并按需更新视图和组件实例。当 Angular 销毁组件实例并从 DOM 中移除它渲染的模板时，生命周期就结束了。当 Angular 在执行过程中创建、更新和销毁实例时，指令就有了类似的生命周期。

| 钩子方法                                        | 用途                                                         | 时机                                                         |
| ----------------------------------------------- | ------------------------------------------------------------ | ------------------------------------------------------------ |
| `ngOnChanges()`                                 | 当Angular(重新)设置数据绑定输入属性时响应。该方法接受当前和上一属性值的 SimpleChanges对象当被绑定的输入属性的值发生变化时调用，首次调用一定会发生在 ngOnInit()之前。 | 在 `ngOnInit()` 之前以及所绑定的一个或多个输入属性的值发生变化时都会调用。注意，如果你的组件没有输入，或者你使用它时没有提供任何输入，那么框架就不会调用 `ngOnChanges()`。 |
| <font color="lighblue">ngOnInit()</font>        | 在 Angular第一次显示数据绑定和设置指令/组件的输入属性之后，初始化指令/组件。<br/> | 在第一轮 `ngOnChanges()` 完成之后调用，只调用**一次**。<br/>使用ngOnInit()有两个原因:<br/>1、在构造函数之后马上执行复杂的初始化逻辑<br/>2、在 Angular设置完输入属性之后，对该组件进行准备。 |
| `ngDoCheck()`                                   | 检测，并在发生 Angular 无法或不愿意自己检测的变化时作出反应。 | 紧跟在每次执行变更检测时的 `ngOnChanges()` 和 首次执行变更检测时的 `ngOnInit()` 后调用。 |
| `ngAfterContentInit()`                          | 当 Angular 把外部内容投影进组件视图或指令所在的视图之后调用。 | 第一次 `ngDoCheck()` 之后调用，只调用一次。                  |
| `ngAfterContentChecked()`                       | 每当 Angular 检查完被投影到组件或指令中的内容之后调用。      | `ngAfterContentInit()` 和每次 `ngDoCheck()` 之后调用         |
| <font color="lighblue">ngAfterViewInit()</font> | 当 Angular 初始化完组件视图及其子视图或包含该指令的视图之后调用。 | 第一次 `ngAfterContentChecked()` 之后调用，只调用一次。      |
| `ngAfterViewChecked()`                          | 每当 Angular 做完组件视图和子视图或包含该指令的视图的变更检测之后调用。 | `ngAfterViewInit()` 和每次 `ngAfterContentChecked()` 之后调用。 |
| <font color="lighblue">ngOnDestroy()</font>     | 每当 Angular 每次销毁指令/组件之前调用并清扫。 在这儿反订阅可观察对象和分离事件处理器，以防内存泄漏。 | 在 Angular 销毁指令或组件之前立即调用。                      |

constructor：构造函数中除了使用简单的值对局部变量进行初始化之外，什么都不应该做。**(非生命周期函数)**
