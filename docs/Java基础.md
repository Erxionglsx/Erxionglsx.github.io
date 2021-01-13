# Java基础

> [参考阿里云大学Java相关课程](https://edu.aliyun.com/roadmap/java?spm=5176.11400045.0.0.44d83a89exPtV4)

***

[TOC]

### Java开发第一步：搭环境

1. 安装JDK
   2. 配环境变量
      3. 验证环境变量

* Java ME 微型版本，开发手机程序的
* Java SE 标准版
* Java EE 企业版
* Java Card 开发智能卡，有线电视

Java特点：<font color="lighblue">跨平台，开源，面向对象</font>
     写一次到处执行，高级语言唯一一个跨平台的是Java

```java
public static void main(){
    System.out.println("Hello World!");
}
```

​     开源：完全开发，源码，工具等开放，免费

Java语言是编译型与解释型的结合。

一.编程语言无好坏之分，只是应用场景不同

1. <font color="lighblue">C语言</font>：用在硬件设备开发、算法开发、底层系统开发
         适合：搞科研的、嵌入式开发的；学电子、自动化等

2. <font color="lighblue">C++语言</font>：做图形界面软件、通信软件、游戏；美图，QQ，PS...
         只能做C/S结构的软件，如：qq
3. <font color="lighblue">Java语言</font>：大型管理系统、大数据开发、Android开发、硬件开发
         都能做，主要做：B/S结构的软件：教务系统，12306，页游

Java语言1991年出现，1995年全球发布。

### 1.static静态变量

static修饰的变量我们称之为<font color="lighblue">静态变量</font>，没有用static修饰的变量称之为<font color="lighblue">实例变量</font>，他们两者的<font color="lighblue" style="font-weight:bold;" size="4">区别</font>是：
     

​       <font color="lighblue">静态变量</font>是随着类加载时被完成初始化的，它在内存中仅有一个，且JVM也只会为它分配一次内存，同时类所有的实例都共享静态变量，可以直接通过类名来访问它。  
&#160;&#160;&#160;&#160;&#160;&#160;&#160;但是<font color="lighblue">实例变量</font>则不同，它是伴随着实例的，每创建一个实例就会产生一个实例变量，它与该实例同生共死。   
&#160;&#160;&#160;&#160;&#160;&#160;&#160;所以我们一般在这两种情况下使用静态变量：对象之间共享数据、访问方便。  
​       **“static” 关键字**表明一个成员变量或者是成员方法可以在没有所属的类的实例变量的情况下被访问。   
​       方便在没有创建对象的情况下来进行调用（方法/变量）。

![image](https://note.youdao.com/yws/api/personal/file/B143C0AC38BD49D98B8E45233547ABCA?method=download&shareKey=46e0c1a59c5e363560eb8ae18762e257)

<font color="lighblue" style="font-weight:bold;">static属性</font>其本身是一个**公共**的属性，其可以**由类名称直接调用**。

**结论**：在以后进行类设计的时候首选的一定是非static属性（95%），而考虑到公共信息存储时才会使用static属性（5%），非static属性必须在实例化对象产生之后才可以使用，而static属性可以在没有实例化对象对象产生的情况下直接通过类名称进行调用。

### 2.JDK,JRE和JVM的联系区别

- <font color="lighblue" style="font-weight:bold;">JDK</font>提供编译、调试和运行功能，用于开发，包含了JRE和JVM
- <font color="lighblue" style="font-weight:bold;">JRE</font>提供了运行Java程序的平台，包含了JVM
- <font color="lighblue" style="font-weight:bold;">JVM</font>是Java的虚拟机，提供了内存管理、垃圾回收和安全机制等

### 3.覆写和重载是什么意思？

**<font color="lighblue" style="font-weight:bold;">覆写（Override）</font>是指子类对父类方法的一种重写，** 只能比父类抛出更少的异常，访问权限不能比父类的小，被覆写的方法不能是 private 的，否则只是在子类中重新定义了一个新方法。

**<font color="lighblue" style="font-weight:bold;">重载（Overload）</font>表示同一个类中可以有多个名称相同的方法，但这些方法的参数列表各不相同。**

**构成重载的条件有哪些？**  参数类型不同、参数个数不同、参数顺序不同。

一般认为，**Java 内的传递都是值传递，Java 中实例对象的传递是引用传递，<font color="lighblue" style="font-weight:bold;">Java 是值传递的！</font>**

<font color="lighblue" style="font-weight:bold;">覆写原因</font>：子类与父类一旦产生继承关系之后，实际上子类会继承父类中的全部定义，但是这里面也有可能出现不合适的场景，子类如果发现父类中设计不足并且需要保留父类中的方法或者属性名称的情况下就会发生覆写。

<font color="lighblue" style="font-weight:bold;">方法的覆写</font>：当子类定义了与父类方法名称相同，参数类型及个数完全相同（和父类方法一模一样）的时候，就称为方法的覆写。

```java
class Channel{
    public void connect(){
        System.out.println("父类进行资源的连接。");
    }
}
class DatabaseChannel extends Channel{
    @Override //明确表达该方法是一个覆写的方法，帮助检查出程序错误
    public void connect(){
        System.out.println("子类进行资源的连接。");
    }
}
```

| No.  |   区别   |            Overloading             |               Override               |
| :--: | :------: | :--------------------------------: | :----------------------------------: |
|  1   | 中文含义 |                重载                |                 覆写                 |
|  2   |   概念   | 方法名称相同，参数的类型及个数不同 | 方法名称、参数类型及个数、返回值相同 |
|  3   |   权限   |            没有权限限制            |  被覆写方法不能拥有更严格的控制权限  |
|  4   |   范围   |           发生在一个类中           |          发生在继承关系类中          |

### 4.HashMap是使用了哪些方法来有效解决哈希冲突？

1. 使用 <font color="lighblue" style="font-weight:bold;">链地址法</font>（使用散列表）来链接拥有相同hash值的数据；

2. 使用 <font color="lighblue" style="font-weight:bold;">2次扰动函数</font>（hash函数）来降低哈希冲突的概率，使得数据分布更平均；

3. <font color="lighblue" style="font-weight:bold;">引入红黑树</font>进一步降低遍历的时间复杂度，使得遍历更快；

   #### HashMap在JDK1.7和JDK1.8中有哪些不同

| 不同                     | JDK 1.7                                                      | JDK 1.8                                                      |
| :----------------------- | :----------------------------------------------------------- | :----------------------------------------------------------- |
| 存储结构                 | 数组 + 链表                                                  | 数组 + 链表 + 红黑树                                         |
| 初始化方式               | 单独函数：`inflateTable()`                                   | 直接集成到了扩容函数`resize()`中                             |
| hash值计算方式           | 扰动处理 = 9次扰动 = 4次位运算 + 5次异或运算                 | 扰动处理 = 2次扰动 = 1次位运算 + 1次异或运算                 |
| 存放数据的规则           | 无冲突时，存放数组；冲突时，存放链表                         | 无冲突时，存放数组；冲突 & 链表长度 < 8：存放单链表；冲突 & 链表长度 > 8：树化并存放红黑树 |
| 插入数据方式             | 头插法（先讲原位置的数据移到后1位，再插入数据到该位置）      | 尾插法（直接插入到链表尾部/红黑树）                          |
| 扩容后存储位置的计算方式 | 全部按照原来方法进行计算（即hashCode ->> 扰动函数 ->> (h&length-1)） | 按照扩容后的规律计算（即扩容后的位置=原位置 or 原位置 + 旧容量） |

### 5.堆和栈的区别，类和对象：

<font color="lighblue" style="font-weight:bold;">堆内存</font>：保存的是对象的具体信息，在程序之中堆内存空间的开辟是通过new完成的；

<font color="lighblue" style="font-weight:bold;">栈内存</font>：保存的是一块堆内存的地址，即：通过地址找到堆内存，而后找到对象内容；

![image](https://note.youdao.com/yws/api/personal/file/7F617A4A168E42198F8C664B85FA1B3A?method=download&shareKey=b0078ca881203e8e94a6deaeb7996748)

**引用传递：**
![image](https://note.youdao.com/yws/api/personal/file/DFEA7E5429AB4B10845307E58A797926?method=download&shareKey=98596db17eb7e581d2d8e19dec9574f2)

<font color="lighblue" style="font-weight:bold;">类</font>是对某一类事物的共性的抽象概念，而<font color="lighblue" style="font-weight:bold;">对象</font>描述的是一个具体的产物。

类是一个模板，而对象才是类可以使用的实例，先有类再有对象。

对象必须实例化以后才能使用，Person p = new Person();

###  6.垃圾空间

​       所谓的<font color="lighblue" style="font-weight:bold;">垃圾空间</font>指的就是没有任何栈内存所指向的堆内存空间，所有的垃圾将被GC(Garbage Collector、垃圾收集器)定期进行回收并且释放无用内存，但是如果垃圾过多，一定会影响到GC的处理性能，从而降低整体的程序性能，那么在实际的开发之中，对于垃圾的产生应该越少越好。

### 7.构造方法

```java
class Person{
    private String name;
    private int id;
    public Person(String name,int id){
        this.name=name;
        this.id=id;
    }
}
```

<font color="lighblue" style="font-weight:bold;">主要目的</font>：可以通过构造方法实现实例化对象中的属性初始化处理。相当于调用setter方法。

<font color="lighblue" style="font-weight:bold;">要求如下</font>：

* 构造方法必须与类名保持一致；
* 构造方法不允许设置任何的返回值类型，即：没有返回值定义；
* 构造方法是在使用关键字new实例化对象的时候自动调用的。

<font color="lighblue" style="font-weight:bold;">结论</font>：一个类至少存在有一个构造方法，永恒存在。若没有手动构造，则类存在一个默认无参构造方法。

#### 问题：为什么构造方法上不允许设置返回值类型？

如果在构造方法上使用了void，那么此结构就与普通方法的结构完全相同了，这样编译器会认为此方法是一个普通方法。

#### 普通方法与构造方法最大的区别：

构造方法是在类对象实例化的时候调用的，而普通方法是在类对象实例化产生之后调用的。

### 8.请解释String比较中“==”与equals()区别？

* **==**：进行的是数值比较，如果用于对象比较上比较的是两个内存的地址数值；
* **equals()**：是类所提供的一个比较方法，可以直接进行字符串内容的判断。
```java
//正确
String str = "ssi";
System.out.println(str.equals(null));//false
//错误
String str = null;
System.out.println(str.equals(ssi));//空指针异常
```
```java
//直接赋值过程中，内存池数据自动保存，如果有相同数据定义时可以减少对象的产生
String strA = "mldn";
String strB = "mldn";
System.out.println(strA==strB);//true
```
**判断字符串是否为空的方法**

```java
if(s == null || s.equals(""));
if(s == null || s.length() == 0);
if(s == null || s.isEmpty());
if(s == null || s == "");
//null是没有地址,null代表声明了一个空对象，根本就不是一个字符串。   
//""是有地址但是里面的内容是空的,""代表声明了一个对象实例，这个对象实例的值是一个长度为0的空字符串。
```

**String类两种对象实例化方式：**

```java
//直接赋值，只会产生一个实例化对象，并且可以自动保存到对象池中，以实现该字符串实例的重用；
String strA = "mldn";
//构造方法，会产生两个实例化对象，并且不会自动入池，无法实现对象的重用，但是可以利用intern()方法手工入池处理。
String strB = new String("mldn");
System.out.println(strA==strB);//false
```
 ![image](https://note.youdao.com/yws/api/personal/file/B0320573241844D7B449967C8E5D0081?method=download&shareKey=f3237722a114299496117ca13122fdb6)

**下面这种字符串修改方式会带来大量的垃圾内存（不推荐）：**

```java
String str = "www.";
str += "mldn.";
str = str+"cn";
System.out.println(str);
```
![image](https://note.youdao.com/yws/api/personal/file/58E3597B32F04E2BABE17024C369A944?method=download&shareKey=d4d7d97b80683dfac37b4ed3792e0353)

### 9.主方法分析

**public static void main(String args[])**

* **public**：描述的是一种访问权限，主方法是一切的开始点，开始点一定是公共的；
* **static**：程序的执行是通过类名称完成的，所以表示此方法是由类直接调用；
* **void**：主方法是一切的起点，起点一旦开始就没有返回的可能了；
* **main**：是一个系统定义好的方法名称；
* **String args[]**：字符串的名称，可以实现程序启动参数的接收。

### 10.请解释super与this的区别？

* 在程序类汇总使用this表示先从本类查找所需要的属性或方法，如果本类不存在则查找父类定义，如果使用super则表示不查找子类，直接查找父类；
* this与super都可以进行构造方法的的调用，但是this()调用的是本类构造，必须放在构造方法的首行，所以不能同时出现；
* this可以表示当前对象。

### 11.多态性

**多态是同一个行为具有多个不同表现形式的能力。**

- **多态性**：多态性是指允许不同子类型的对象对同一消息作出不同的响应。简单的说就是<font color="lighblue" >用同样的对象引用调用同样的方法但是做了不同的事情</font>。多态性分为<font color="lighblue" >编译时的多态性和运行时的多态性</font>。如果将对象的方法视为对象向外界提供的服务，那么运行时的多态性可以解释为：当A系统访问B系统提供的服务时，B系统有多种提供服务的方式，但一切对A系统来说都是透明的（就像电动剃须刀是A系统，它的供电系统是B系统，B系统可以使用电池供电或者用交流电，甚至还有可能是太阳能，A系统只会通过B类对象调用供电的方法，但并不知道供电系统的底层实现是什么，究竟通过何种方式获得了动力）。<font color="lighblue" >方法重载（overload）实现的是编译时的多态性（也称为前绑定），而方法重写（override）实现的是运行时的多态性（也称为后绑定）</font>。运行时的多态是面向对象最精髓的东西，要实现多态需要做两件事：1). 方法重写（子类继承父类并重写父类中已有的或抽象的方法）；2). 对象造型（用父类型引用引用子类型对象，这样同样的引用调用同样的方法就会根据子类对象的不同而表现出不同的行为）。

在Java之中对于多态性有两种实现的模式：

**1. 方法的多态性：**

* 方法的重载

* 方法的重写

**2. 对象的多态性：**父子实例之间的转换处理，它有两种模式：

#### 对象向上转型：

* 父类  父类实例  =  子类实例、自动完成转换；

  提高代码的简洁性，减少代码量。

  **向上转型，也就是创建一个子类对象，把他当做父类来看待，向上转型一定*是安全的*，但是一旦向上转型为父类对象，就不能调用子类原本特有的方法**
  
  ```java
  //DataBaseMessage extends Message
  //WebServerMessage extends Message
  public static void mian(String args[]){
      fun(new DataBaseMessage());//Message msg=new DataBaseMessage();
      fun(new WebServerMessage());//Message msg=new WebServerMessage();
  }
  public static void fun(Message msg){
      msg.print();
  }
  ```

```java
package cpm.edu;
class Base
{
    public void method()
    {
        System.out.println("Base");
    } 
}
class Son extends Base
{
    public void method()
    {
        System.out.println("Son");
    }
    
    public void methodB()
    {
        System.out.println("SonB");
    }
}
public class Testw
{
    public static void main(String[] args)
    {
        Base base = new Son();
        base.method();   //Son
//      base.methodB();  //编译错误
    }
}
```

#### 对象向下转型

* 子类  子类实例  =  （子类）父类实例、强制完成转换。

  由于向上转型，per是Person类型，所以执行子类特有方法fly()时要使用向下转型进行强转，才能使用SuperMan特有方法。

```java
//SuperMan extends Person
//发生对象的向下转型之前一定要首先发生向上转型
Person per = new SuperMan();//向上转型
per.print();
if(per instanceof SuperMan){//可以使用instanceof关键字判断
    SuperMan man = (SuperMan)per;//向下转型，容易出错
}
System.out.println(man.fly());
```

<font color="lighblue" >instanceof关键字</font>：可以用来判断某个实例是否是某个类的对象。

```java
Person per = new Person();
System.out.println(per instanceof Person);//true
System.out.println(per instanceof SuperMan);//false
```

###  12.Object类

​      在Java之中只有一个类是不存在有继承关系的，那么这个类就是Object，也就是说所有的类默认情况下都是Object子类，以下两种类的定义效果完全相同：

```java
class Person{}
class Person extends Object{}
```

​      在Object类设计的时候考虑到了所有的继承问题，所以该类提供有<font color="lighblue" >无参构造方法</font>，所以子类不会出现构造方法调用失败的语法错误。

​      如果一个程序的方法要求可以接受所有类对象的时候就可以利用Object实现处理。

​     Object类对象可以接收所有数据类型，包括<font color="lighblue" >基本数据类型、类对象、接口对象、数组。</font>

**获取对象信息：toString()**

toString是Object类提供的一个方法，可以获取一个对象的完整信息。

```java
Person per = new Person();
System.out.println(per);//结果为Person@6dle7682，默认使用toString方法。
System.out.println(per.toString());//结果为Person@6dle7682，Object类继承而来。
```

可在对象类中直接覆写此方法。

```java
public String toString(){
    return "姓名："+ this.name + "、年龄：" + this.age;
}
```

### 13.抽象类

<font color="lighblue" style="font-weight:bold;">基本定义</font>：抽象类的主要作用在于对子类中覆写方法进行约定，在抽象类里面可以去定义一些抽象方法以实现这样的约定，抽象方法指的是使用了abstract关键字定义的并且没有提供方法体的方法，而抽象方法所在的类必须为抽象类，抽象类必须使用<font color="lighblue">abstract关键字</font>来定义（在普通类的基础上追加抽象方法就是抽象类）

当一个抽象类定义完成之后（切记：抽象类不是完整的类），如果要想去<font color="lighblue">使用抽象类</font>，则要<font color="lighblue">满足以下条件：</font>

   * 抽象类必须提供有子类，子类使用extends继承一个抽象类；
   * 抽象类的子类（不是抽象类）一定要覆写抽象类中的全部抽象方法；
   * 抽象类的对象实例化可以利用对象多态性通过子类向上转型的方式完成。

**注：抽象类自己无法直接实例化。**

1. 抽象类中允许没有抽象方法，但即便没有抽象方法，也<font color="lighblue">无法直接使用关键字new实例化</font>抽象类对象。
2. 抽象类中<font color="lighblue">可以提供有static方法</font>，并且该方法不受到抽象类对象的局限。
3. 在定义抽象类的的时候绝对<font color="lighblue">不能够使用final关键字</font>来进行定义，因为抽象类必须有子类，而final定义的类是不能够有子类；

<font color="lighblue">抽象类最大的好处：</font>一是对子类方法的统一管理，二是可以自身提供有一些普通方法可以调用抽象方法（这些抽象方法必须在有子类提供实现的时候才会生效）。

### 14.接口

<font color="lighblie" style="font-weight:bold;font-size: 17px">接口使用原则：</font>

* 接口需要被子类实现（implements)，一个子类可以实现多个父接口；
* 子类（如果不是抽象类），那么一定要覆写接口之中的全部抽象方法；
* 接口对象可以利用子类对象的向上转型进行实例化。

注意：由于接口描述的是一个公共的定义标准，所有在接口之中所有的抽象方法的访问权限都为public。

下面代码含义相同，第二种为简化版：

```java
public static final String INFO="hello";
public abstract String getInfo();
```

```java
String INFO="hello";
String getInfo();
```

**先继承后实现**

```java
class MessageImpl extends Database implements IMessage
```

特点：全部由抽象方法和全局变量所组成。

<font color="lighblie" style="font-weight:bold;font-size: 20px">抽象类与接口的区别：</font>

| No.  |    区别    |                        抽象类                        |                      接口                      |
| :--: | :--------: | :--------------------------------------------------: | :--------------------------------------------: |
|  1   | 定义关键字 |             abstract class 抽象类名称{}              |              interface 接口名称{}              |
|  2   |    组成    | 构造、普通方法、静态方法、全局常量、成员、static方法 |               抽象方法、全局变量               |
|  3   |    权限    |                 可以使用各种权限定义                 |                只能够使用public                |
|  4   |  子类使用  |       子类通过extends关键字可以继承一个抽象类        |    子类使用implements关键字可以实现多个接口    |
|  5   |  两者关系  |               抽象类可以实现若干个接口               | 接口不允许继承抽象类，但是允许继承多个父接口。 |

* 1.抽象类可以有构造方法，接口中不能有构造方法。
* 2.抽象类中可以有普通成员变量，接口中没有普通成员变量。
* 3.抽象类中可以包含非抽象的普通方法，接口中的所有方法必须都是抽象的，不能有非抽象的普通方法。
* 4.抽象类中的抽象方法的访问类型可以是public，protected和（默认类型,虽然eclipse下不报错，但应该也不行），但接口中的抽象方法只能是public类型的，并且默认即为public abstract类型。
* 5.抽象类中可以包含静态方法，接口中不能包含静态方法。
* 6.抽象类和接口中都可以包含静态成员变量，抽象类中的静态成员变量的访问类型可以任意，但接口中定义的变量只能是public static final类型，并且默认即为public static final类型。

**共同点：**

1. 抽象类或接口必须定义子类；
2. 子类一定要覆盖抽象类或接口中的全部抽象方法；
3. 通过子类的向上转型实现抽象类或接口对象实例化。

注意：当抽象类和接口都可以使用的情况下优先要考虑接口，因为接口可以避免子类的单继承局限。

**为什么MVC中我们用接口不用抽象类？**

抽象类被定义为永远不会也不能被实例化为具体的对象。它往往用于定义一种抽象上的概念，在类的继承关系中它往往被定义在较上层的位置。在程序设计的实践活动中，抽象类与接口存在类似的地方，即它更偏重于对共通的方法和属性进行规约。但与接口存在一个非常大的差异则在于，抽象类往往可以规约一个共同的方法和属性时提供一个对他们的实现。
以现实世界为例：“水果”可以算作一个抽象类，而“苹果”、“香蕉”则可以作为它的派生类。区别在于，“水果”是个概念，它不会有实例，但是“苹果”、“香蕉”则肯定会有实例。

### 15.泛型

**出现原因**：在没有泛型的情况的下，通过对类型 Object 的引用来实现参数的“任意化”，“任意化”带来的缺点是要做显式的强制类型转换，而这种转换是要求开发者对实际参数类型可以预知的情况下进行的。对于强制类型转换错误的情况，编译器可能不提示错误，在运行的时候才出现异常，这是本身就是一个安全隐患。

<font color="lighblue" style="font-weight:bold;">本质</font>：把类型明确的工作推迟到创建对象或调用方法的时候才去明确的特殊的类型。

**注意**：泛型之中只允许设置引用类型，如果现在要操作基本类型必须使用包装类；

```java
/*
    1:把泛型定义在类上
    2:类型变量定义在类上,方法中也可以使用
 */
public class ObjectTool<T> {
    private T obj;

    public T getObj() {
        return obj;
    }

    public void setObj(T obj) {
        this.obj = obj;
    }
}

 //创建对象并指定元素类型
ObjectTool<Integer> tool = new ObjectTool<Integer>();
```

使用泛型可以解决大部分的类对象的强制转换处理，避免了对象的向下转型处理。

<font color="lighblue" style="font-weight:bold;">好处:</font>

- 代码更加简洁【<font color="lighblue">不用强制转换</font>】
- 程序更加健壮【<font color="lighblue">只要编译时期没有警告，那么运行时期就不会出现ClassCastException异常</font>】
- 可读性和稳定性【<font color="lighblue">在编写集合的时候，就限定了类型</font>】

#### 增强for遍历集合

在创建集合的时候，**我们明确了集合的类型了**，所以我们可以使用增强for来遍历集合:

```java
        //创建集合对象
        ArrayList<String> list = new ArrayList<>();

        list.add("hello");
        list.add("world");
        list.add("java");

        //遍历,由于明确了类型.我们可以增强for
        for (String s : list) {
            System.out.println(s);
        }
```

#### 通配符：

**通配符"?"只能调用与对象无关的方法，不能调用对象与类型有关的方法**。因为直到外界使用才知道具体的类型是什么。也就是说，我是不能使用add()方法的。**因为add()方法是把对象丢进集合中，而现在我是不知道对象的类型是什么。**

```java
public void test(List<?> list){
    for(int i=0;i<list.size();i++){
        System.out.println(list.get(i));
    }
}
```

？和 T 都表示不确定的类型，区别在于我们可以对 T 进行操作，但是对 ？不行

```java
//指定集合元素只能是T类型
List<T> list = new ArrayList<T>();
//集合元素可以是任意类型，这种没有意义，一般是方法中，实施未来说明用法
List<?> list = new ArrayList<?>();
```

**总结**：T 是一个 确定的 类型，通常用于泛型类和泛型方法的定义，？是一个 不确定 的类型，通常用于泛型方法的调用代码和形参，不能用于定义类和泛型方法。

在“？”这个通配符的基础之上实际上还提供有两类小的通配符：

* <font color="lighblue">？ extends 类</font>：设置泛型的上限：

        |- 例如：定义“？ extends Number”:表示该泛型类型只允许设置Number或Number的子类；

* <font color="lighblue">？ super 类</font>：设置泛型的下限：

  ​      |- 例如：定义“？ super Number”:只能够使用String或其父类。

  ```java
  public static void fun(Message<? extends Number> temp){
      System.out.println(temp.getContent());  
  }
  ```

**区别**：上界通配符主要用于读数据，下界通配符主要用于写数据。

### 16.包装类

**包装原因**：Object类最大的特点是所有的类的父类，并且可以接受所有的数据类型，但是在这个过程中就有个问题：基本数据类型并不是一个类，所以现在如果想将基本数据类型以类的形式进行处理，那么就需要对其进行包装。

Java中的基本数据类型没有方法和属性，而包装类就是为了让这些拥有方法和属性，实现对象化交互。

* <font color="lighblue">装箱</font>：基本数据类型转换为包装类；
* <font color="lighblue">拆箱</font>：包装类转换为基本数据类型。　

1. 通过包装类Integer.toString()将整型转换为字符串；
2. 通过Integer.parseInt()将字符串转换为int类型；
3. 通过valueOf()方法把字符串转换为包装类然后通过自动拆箱。

```java
 public static void main(String[] args) {
         //基本数据类型转换为字符串
         int t1=12;
         String t2=Integer.toString(t1);
         System.out.println("int转换为String："+t2);
         //字符串转换为基本数据类型
         //通过paerInt方法
         int t3=Integer.parseInt(t2);
         //通过valeOf,先把字符串转换为包装类然后通过自动拆箱
         int t4=Integer.valueOf(t2);
         System.out.println("t3:"+t3);
         System.out.println("t4:"+t4);
}
```

### 17.枚举

主要作用是用于定义有限个对象的一种结构，枚举属于多例设计。

使用<font color="lighblue">enum关键字</font>来定义枚举类型，其中FRONT、BEHIND、LEFT、RIGHT都是枚举项，它们都是本类的实例，本类一共就只有四个实例对象：

```java
public enum Direction {
    FRONT, BEHIND, LEFT, RIGHT("红色");
}
Direction d = Direction.FRONT;
```

不能使用new来创建枚举类的对象，因为枚举类中的实例就是类中的枚举项，所以在类外只能使用<font color="lighblue">类名.枚举项</font>。

#### 枚举与switch

在switch中，<font color="lighblue">不能使用枚举类名称</font>，例如：“case  Direction.FRONT：”这是错误的，编译器会根据switch中d的类型来判定每个枚举类型，在case中必须直接给出与d相同类型的枚举选项，而不能再有类型。

```java
Direction d = Direction.FRONT;
  switch(d) {
    case FRONT: System.out.println("前面");break;
    case BEHIND:System.out.println("后面");break;
    case LEFT:  System.out.println("左面");break;
    case RIGHT: System.out.println("右面");break;
    default:System.out.println("错误的方向");
}
Direction d1 = d;
System.out.println(d1);

```

请解释enum与Enum的区别？

* enum：是从JDK1.5之后提供的一个关键字，用于定义枚举类。
* Enum：是一个抽象类，所以使用enum定义的类就默认继承了此类。

### 18.异常

![](https://note.youdao.com/yws/api/personal/file/1CE54EC1FFAE43E0BFB96BF65981D17A?method=download&shareKey=cb530caa1481d5a718f3865f1a9883d4)

出现异常以后将<font color="lighblue">终止整个程序</font>。为了保证程序出现了非致命错误之后程序依然可以正常完成，所有需要有一个完善的异常处理机制。

#### 处理异常

在Java之中如果要进行异常的处理，可以使用：try、catch、finally这几个关键字来完成，其基本的结构如下：

```java
try{
    //可能出现异常的语句
}[catch(异常类型 异常对象){
    //异常处理
}catch(异常类型 异常对象){
    //异常处理
}catch(异常类型 异常对象){
    //异常处理
}...][finally{
    不管异常是否处理都要执行;
}]
```

在此格式之中可以使用的组合为：<font color="lighblue">try...catch、try...catch...finally、try...finally</font>。

如果想要获得非常完整的异常信息，则可以使用异常类中提供的<font color="lighblue">printStackTrace()方法</font>。

对于异常的处理格式也可以在最后追加一个<font color="lighblue">finally程序块</font>，表示异常处理后的出口，<font color="lighblue">不管是否出现异常都会被执行</font>。

**注意：**

* **即便有了异常处理语句，但是如果没有进行正确的异常捕获，那么程序也会导致<font color="lighblue">中断</font>（finally的代码依旧会被执行），这样的情况应该捕获多个异常。**
* **在以后进行多个异常同时处理的时候要把捕获范围大的异常放在捕获范围小的异常之后。**

程序中可以处理的异常的最大的类型就是<font color="lighblue">Throwable</font>，而打开Throwable可以看到有两个子类：

* <font color="lighblue">Error</font>：此时程序还未执行出现的错误，开发者无法处理；
* <font color="lighblue">Exception</font>：程序中出现的异常，开发者可以处理。

#### throws关键字

* 使用throws关键字来进行异常类型的标注，必须有对应的try..catch处理；
* throws表示出现异常的一种可能性，并不一定会发生这些异常；
* 异常处理是会一层层往上抛的，直到遇到了某个方法处理了这个异常或者最后抛给了JVM。

```java
class MyMath{
    //这个代码执行的时候可能会产生异常，如果产生异常调用处理
    public static int div(int x,int y) throws Exception{
        return x/y;
    }
}
public class JavaDemo{
    public static void main(String args[]) throws Exception{
        try{
             System.out.println(MyMath.div(10,0));
    } catch(Exception e){
        e.printStackTrace();
    }      
  }
}
```

如果主方法继续向上抛出异常，那么就表示此异常将交由JVM负责处理。

#### throw关键字

表示<font color="lighblue">手工进行异常的抛出</font>，即：此时将手工产生一个异常类的实例化对象，并进行异常的抛出处理。

```java
class MyMath{
    //异常交给被调用处处理则一定要在方法上使用throws
    public static int div(int x,int y) throws Exception{
        int temp = 0;
        System.out.println("***[START]除法计算开始。");
        try{
            temp = x / y;
        } catch (Exception e){//这里的catch可省略，将会自动向上抛出异常
            throw e;//向上抛出异常对象
        } finally{
            System.out.println("***[END]除法计算结束。");
        }
        return temp;
    }
}
public class JavaDemo{
    public static void main(String args[]){
        try{
            System.out.println(MyMath.div(10,0));
        }catch(Exception e){
            e.printStackTrace();
        }
    }
}
```

**面试题：请解释throw与throws区别？**

* throw：是在代码块中使用的，主要是手工进行异常对象的抛出；
* throws：是在方法定义上使用的，表示将此方法中可能产生的异常明确告诉给调用处，由调用处进行处理。
* throws表示出现异常的一种可能性，并不一定会发生这些异常；throw则是抛出了异常，执行throw则一定抛出了某种异常。

**面试题：请解释RuntimeExceptiong与Exception区别？常见的RuntimeException?**

* RuntimeException是Exception的子类；
* <font color="lighblue">RuntimeException</font>标注的异常可以<font color="lighblue">不需要进行强制性try...catch处理</font>，而Exception异常必须强制性处理。
* 常见的RuntimeException异常：NumberFormatException、ClassCastException、NullPointException...

#### 自定义异常类

分为继承Exception和RuntimeException两种，如果继承的是Exception，则需要进行try...catch处理：

```java
class BombException extends RuntimeException {
    public BombException(String msg) {
        super(msg);
    }
}
class Food {
    public static void eat(int num) throws BombException {
        if(num > 10) {
            throw new BombException("吃太多了，坏了，肚子要撑爆了。");
        }else {
            System.out.println("正常吃，不怕吃胖。");
        }
    }
}
public class JavaDemo {
    public static void main(String args[]) throws Exception {
        Food.eat(11);
    }
}
```

#### assert断言

从JDK1.4开始追加有一个断言的功能，确定代码执行到某行之后一定是所期待的结果。但断言不一定是准确的，也有可能出现偏差，但是这种偏差不应该影响程序的正常执行。

```java
public class JavaDemo {
    public static void main(String args[]) throws Exception {
        int x = 10;
        //中间会经过许多的x变量的操作步骤
        assert x == 100 : "x的内容不是100";
        System.out.println(x);
    }
}
```

如果想要执行断言，则必须在程序执行的时候加入参数：<font color="lighblue">java -ea JavaDemo</font>

运行结果：

```
Exception in therad "main" java.lang.AssertionError:x的内容不是100     
at JavaDemo.main(JavaDemo.java:5)
```

所以在Java里面并没有将断言设置为一个程序必须执行的步骤，需要在特定环境下才可以开启。

### 19.内部类

将一个类定义在另一个给类里面或者方法里面，这样的类就被称为<font color="lighblue">内部类</font>。

```java
class Outer {  //外部类
    private String msg = "www.mldn.cm";  //私有成员属性
    public void fun() { //普通方法
        Inner in  = new Inner();  //实例化内部类对象
        in.print();  //调用内部类方法
        System.out.println(in.info); //访问内部类的私有属性
    }
    class Inner {  //在Outer类的内部定义了Inner类
        private String info = "今天天气不好，收衣服拉！";
        public void print(){
            System.out.println(Outer.this.msg);  //Outer里类中的属性
        }
    }
}

public class JavaDemo {
    public static void main(String args[]) {
        Outer out = new Outer();  //实例化外部类对象
        out.fun();  //调用外部类中的方法
    }
}
```

* 内部类的**优点**：轻松的访问外部类中的私有属性。
* 内部类的**缺点**：破坏了程序的结构。

**注意：内部类可以轻松的访问外部类的私有属性，同理，外部类也可以轻松访问内部类中的私有成员或私有方法。**

使用了内部类后，内部类与外部类之间的私有操作的访问就不再需要通过setter、getter以及其它的间接方式完成了，可以直接进行处理操作。

<font color="lighblue">内部类</font>本身也属于一个类，虽然大部分的情况下内部类往往是被外部类包裹的，但是外部依然可以产生内部类的实例化对象，而此时<font color="lighblue">实例化格式</font>如下：

<font color="lighblue">外部类.内部类 内部类对象 = new 外部类().new 内部类()；</font>

```java
class Outer {  //外部类
    private String msg = "www.mldn.cn" ;  //私有成员属性
    class Inner {  //在Outer类的内部定义了Inner类
        public void print(){
            System.out.println(Outer.this.msg);  //Outer类中的属性
        }
    }
}
public class Java Demo {
    public static void main(String args[]){
        Outer.Inner in = new Outer().new Inner();
        in.print();
    }
}
```

#### static定义内部类

如果说现在在内部类上使用了static定义，那么这个内部类就变为了“外部类”，static定义的都是独立于类是一个结构，所以该类结构就相当于是一个独立的程序类了。

**注意：static定义的不管是类还是方法只能够访问static成员，所以static定义的内部类只能访问外部类中的static属性和方法。**

这个时候的inner类是一个<font color="lighblue">独立的类</font>，如果此时想要实例化Inner类对象，只需要根据“外部类.内部类”的结构<font color="lighblue">实例化对象</font>即可，格式如下：

<font color="lighblue">外部类.内部类 内部类对象 = new 外部类.内部类()；</font>

```java
class Outer {
    private static final String MSG = "www.mldn.cn";
    static class Inner {
        public void print() {
            System.out.println(Outer.MSG);
        }
    }
}
public class JavaDemo {
    public static void main(String args[]){
        Outer.Inner in = new Outer.Inner();
        in.print();
    }
}
```

#### 方法中定义内部类

内部类可以直接访问外部类的私有属性也可以直接访问方法中的参数，但是对于方法中的参数直接访问是从JDK1.8开始支持的，而在JDK1.8之前，如果方法中定义的内部类要想访问方法中的参数则参数前必须追加final。

```java
class Outer {
    private String msg = "www.mldn.cn" ;
    public void fun(long time) {
        class Inner { //内部类
            public void print() {
                System.out.println(Outer.this.msg);
                System.out.println(time);
            }
        }
        new Inner().print(); //方法中直接实例化内部类对象
    }
}
public class JavaDemo {
    public static void main(String args[]) {
        new Outer().fun(23943342L);
    }
}
```

####  匿名内部类

匿名内部类是一种简化的内部类的处理形式，其主要是在抽象类和接口的子类上使用的。与内部类相比，匿名内部类只是一个没有名字的只能够使用一次的，并且结构固定的一个子类操作。

```java
interface IMessage {
    public void send(String str);
}
public class JavaDemo {
    public static void main(String args[]) {
        IMessage msg = new IMessage() {  //匿名内部类
            public void send(String str) {
                System.out.println(str);
            }
        };
        msg.send("www.mldn.cn");
    }
}
```

### 20.基本数据类型和引用数据类型

Java中的数据类型分为两大类，基本数据类型和引用数据类型。

##### 1、基本数据类型

* ① 整数类型：`long、int、short、byte`
* ② 浮点类型：`float、double`
* ③ 字符类型：`char`
* ④ 布尔类型：`boolean`

| No.  | 数据类型         | 大小/位 |              可表示数据范围              |  默认值  |
| :--: | :--------------- | :-----: | :--------------------------------------: | :------: |
|  1   | byte（字节型）   |    8    |                 -128~127                 |    0     |
|  2   | short（短整型）  |   16    |               -32768~32767               |    0     |
|  3   | int（整型）      |   32    |          -2147483648~2147483647          |    0     |
|  4   | long长整型）     |   64    | -9223372036854775808~9223372036854775807 |    0     |
|  5   | float（单精度）  |   32    |              -3.4E38~3.4E38              |   0.0    |
|  6   | double（双精度） |   64    |             -1.7E308~1.7E308             |   0.0    |
|  7   | char（字符）     |   16    |                  0~255                   | '\u0000' |
|  8   | boolean（布尔）  |    -    |               true或false                |  false   |

##### 2、引用数据类型

引用数据类型非常多，大致包括：
类、 接口类型、 数组类型、 枚举类型、 注解类型、 字符串型

##### 基本数据类型和引用数据类型的区别

**1、存储位置**

**基本变量类型：**

- 在方法中定义的非全局基本数据类型变量的具体内容是<font color="lighblue">存储在栈中</font>的

**引用变量类型：**

- 只要是引用数据类型变量，其具体<font color="lighblue">内容都是存放在堆中</font>的，而<font color="lighblue">栈中存放的是其具体内容所在内存的地址</font>

**2、传递方式**

**基本变量类型**

- 在方法中定义的非全局基本数据类型变量，调用方法时作为参数是按**数值传递**的

**引用变量类型**

- 引用数据类型变量，调用方法时作为参数是按**引用传递**的

### 21.值传递和引用传递

值传递指的是在方法调用时，传递的参数是按值的拷贝传递。

```java
public static void main(String[] args) {
    int a=1;
    change(a);
    System.out.println("交换a后的值:"+a);
}

private static void change(int a) {
    a=1111;
    System.out.println("交换方法里面的a:"+a);
}
//运行输出
//交换方法里面的a:1111
//交换a后的值:1
//按值传递重要特点：传递的是值的拷贝，也就是说传递后就互不相关了。
```

引用传递指的是在方法调用时，传递的参数是按引用进行传递，其实传递的引用的地址，也就是变量所对应的内存空间的地址。

```java
public static void main(String[] args) {
    Person p=new Person();
    p.setAge(25);
    changeAge(p);
    System.out.println("改变年龄后的年龄:"+p.getAge());
}

private static void changeAge(Person p) {
    p.setAge(35);
    System.out.println("改变年龄方法里面的年龄:"+p.getAge());
}
//运行输出
//改变年龄方法里面的年龄:35
//改变年龄后的年龄:35

class Person{
    private int age=10;
    public int getAge() {
   	    return age;
	}
    public void setAge(int age) {
   	    this.age = age;
    }
}
```

传递的是值的引用，也就是说传递前和传递后都指向同一个引用（也就是同一个内存空间），引用类型的传递后的修改会影响原来的值。

### 22.get和post请求的区别

* get请求用来从服务器上获得资源，而post是用来向服务器提交数据；
* get将表单中数据按照name=value的形式，添加到action 所指向的URL 后面，并且两者使用"?"连接，而各个变量之间使用"&"连接；post是将表单中的数据放在HTTP协议的请求头或消息体中，传递到action所指向URL；
* get传输的数据要受到URL长度限制（1024字节）；而post可以传输大量的数据，上传文件通常要使用post方式；
* 使用get时参数会显示在地址栏上，如果这些数据不是敏感数据，那么可以使用get；对于敏感数据还是应用使用post；
* GET请求只能进行URL编码，而POST支持多种编码；
* GET只接受ASCII字符，而POST没有限制

### 23.请求转发和重定向

> ##### https://blog.csdn.net/weixin_37766296/article/details/80375106

**forward又叫转发，redirect叫做重定向**

* **从地址栏显示来说**

  1.forword是服务器内部的重定向，服务器直接访问目标地址的 url网址 

  2.redirect是服务器根据逻辑，发送一个状态码，告诉浏览器重新去请求那个地址，所以地址栏显示的是新的地址。

* **从数据共享来说：**

  1.forward会将request的信息带到被重定向的jsp或者servlet中使用。即可以共享数据

  2.redirect不能共享

* **从效率来说：**

  1.forword效率高，而redirect效率低

* **从本质来说：**

  1.forword转发是服务器上的行为，而redirect重定向是客户端的行为

* **从请求的次数来说：**

  1.forword只有一次请求；而redirect有两次请求

### 23.final关键字

> https://www.cnblogs.com/dolphin0520/p/3736238.html

在Java中，final关键字可以用来修饰<font color="lighblue">类、方法和变量（包括成员变量和局部变量）</font>。

**1.修饰类**

当用final修饰一个类时，表明这个类<font color="lighblue">不能被继承</font>。也就是说，如果一个类你永远不会让他被继承，就可以用final进行修饰。

**2.修饰方法**

使用final方法的原因有两个。第一个原因是<font color="lighblue">把方法锁定</font>，以防任何继承类修改它的含义；第二个原因是<font color="lighblue">效率</font>。只有在想明确禁止 该方法在子类中被覆盖的情况下才将方法设置为final的。

**3.修饰变量**

对于一个final变量，如果是<font color="lighblue">基本数据类型</font>的变量，则其数值一旦在初始化之后便<font color="lighblue">不能更改</font>；如果是<font color="lighblue">引用类型</font>的变量，则在对其初始化之后便<font color="lighblue">不能再让其指向另一个对象，但是它指向的对象的内容是可变的。</font>。

### 24.强引用、软引用、弱引用和虚引用的区别

> https://blog.csdn.net/qq_36517910/article/details/94002492

**1、强引用（StrongReference）**
最普遍的一种引用方式，如String s = “abc”，变量s就是字符串“abc”的强引用，只要强引用存在，则垃圾回收器就不会回收这个对象。

**2、软引用（SoftReference）**
用于描述还有用但非必须的对象，如果内存足够，不回收，如果内存不足，则回收。一般用于实现内存敏感的高速缓存，软引用可以和引用队列ReferenceQueue联合使用，如果软引用的对象被垃圾回收，JVM就会把这个软引用加入到与之关联的引用队列中。

**3、弱引用（WeakReference）**
弱引用和软引用大致相同，弱引用与软引用的区别在于：只具有弱引用的对象拥有更短暂的生命周期。在垃圾回收器线程扫描它所管辖的内存区域的过程中，一旦发现了只具有弱引用的对象，不管当前内存空间足够与否，都会回收它的内存。短时间内通过弱引用取对应的数据，可以取到，当执行过第二次垃圾回收时，将返回null。弱引用主要用于监控对象是否已经被垃圾回收器标记为即将回收的垃圾，可以通过弱引用的isEnQueued方法返回对象是否被垃圾回收器标记。

**4、虚引用（PhantomReference）**
就是形同虚设，与其他几种引用都不同，虚引用并不会决定对象的生命周期。如果一个对象仅持有虚引用，那么它就和没有任何引用一样，在任何时候都可能被垃圾回收器回收。虚引用主要用来跟踪对象被垃圾回收器回收的活动。
虚引用与软引用和弱引用的一个区别在于：虚引用必须和引用队列 （ReferenceQueue）联合使用。当垃圾回收器准备回收一个对象时，如果发现它还有虚引，就会在回收对象的内存之前，把这个虚引用加入到与之关联的引用队列中。



