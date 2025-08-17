

 Java进阶

------

[TOC]

## 进程和线程

### 1.Java多线程编程

##### 1.进程和线程

**进程是操作系统中进行保护和资源分配的基本单位**，操作系统分配资源以进程为基本单位。而线程是进程的组成部分，它代表了一条顺序的执行流。

使用多线程最主要的原因是**提高系统的资源利用率**。

##### 2.继承Thread类实现多线程

![](https://note.youdao.com/yws/api/personal/file/A811D60FFBA34A208D0B25AF25C1EF43?method=download&shareKey=d615d21ad4e94e52f52da7ef1a8cee41)

   * Java里面提供有一个java.lang.Thread的程序类，那么一个类只要继承了此类就表示这个类为线程的主体了，同时还需要覆写Thread类中提供的一个<font color="lighblue">run()方法</font>，而这个方法就属于线程的主方法。

     ```java
     class MyThread extends Thread{
     	private String title;
     	public MyThread(String title) {
     		this.title = title;
     	}
       	@Override
       	public void run() {
       		for (int i = 0; i < 10; i++) {
       			System.out.println(this.title + "运行：i="+i);
     		}
       	}
     }
     ```

 多线程要执行的功能都应该在run()方法中进行定义。

* **启用run方法不能被直接调用，必须使用start()方法来启动多线程。**

```java
public class ThreadDemo {
  	public static void main(String[] args) {
  		  new MyThread("线程A").start();
  		  new MyThread("线程B").start();
  		  new MyThread("线程C").start();
  		  new MyThread("线程D").start();
  	}
  }
```

虽然调用了start()方法，但最终执行的是run()方法，并且所有的线程对象都是交替执行的。

线程的启动使用的是start()，但是启动的时候将进入到一种**就绪状态**，现在并没有执行，要等待资源调度。

##### 3.Runnable接口实现多线程


* 此时由于不再继承Thread父类了，那么对于此时的MyThread类也就不再支持有start()这个继承的方法了。

  * 构造方法：public Thread(Runnable target);

* 由于只是实现了Runnable接口对象，所以此时线程主体类上就不再有单继承局限了。

  ```java
  class myThread implements Runnable{
  	private String title;
  	public  myThread(String title) {
  		this.title = title;
  	}
    	@Override
    	public void run() {
    		for (int i = 0; i < 10; i++) {
    			System.out.println(this.title + "运行：i="+i);
  		}
    	}
  }
  
  public class RunnableDemo {
  	public static void main(String[] args) {
  		  Thread threadA = new Thread(new myThread("线程对象A"));
  		  Thread threadB = new Thread(new myThread("线程对象B"));
  		  Thread threadC = new Thread(new myThread("线程对象C"));
  		  threadA.start();//启动多线程
  		  threadB.start();//启动多线程
  		  threadC.start();//启动多线程
  	}
  }
  ```

##### 4.利用Lambda实现多线程定义

```java
public class ThreadDemo {
	public static void main(String[] args) {
        for(int x = 0; x < 3 ; x ++){
            String title = "线程对象-" + x;
            new Thread(()->{
                for(int y = 0; y < 10 ; y++){
                    System.out.println(title+"运行，y="+y);
                }
            }).start();
    }
}
```

##### 5.Thread和Runnable关系

![](https://note.youdao.com/yws/api/personal/file/1C47A63D756C40FEBA7CF6D91A29FFEA?method=download&shareKey=8f95975cb982a96f04aeee7c1e8feb2d)

* 代码结构本身来讲肯定使用Runnable是最方便的，因为其可以避免单继承的局限，同时也可以更好的进行功能的扩充。
* 多线程的设计之中，使用了<font color="lighblue">代理设计模式</font>的结构，用户自定义的线程主体只是负责项目核心功能的实现，而所有的辅助实现全部交由Thread类来处理。
* 在进行Thread启动多线程的时候调用的是start()方法，而后找到的是run()方法，但通过Thread类的构造方法传递了一个Runnable接口对象的时候，那么该接口对象将被Thread类中的<font color="lighblue">target属性</font>所保存，在start()方法执行的时候会调用Thread类中的run()方法，而这个run()方法去调用Runabble接口子类被覆写过的run()方法。
* 多线程开发的本质实质上是在于多个线程可以进行同一资源的抢占，那么Thread主要描述的是线程，而资源的描述是通过Runnable实现的。

![](https://note.youdao.com/yws/api/personal/file/36C9CE72E82547739E63E053871E2CE4?method=download&shareKey=2f409d7369f12ba5c091e734ece9acd7)

##### 6.Callable实现多线程

* 由于Runnable接口有一个缺陷，当线程执行完毕后无法获取一个返回值，所以从JDK1.5之后就提出了一个新的线程实现接口：java.util.concurrent.Callable接口。
* Callable定义的时候可以设置一个泛型，此泛型的类型就是返回数据的类型，这样的好处是可以避免项目下转型所带来的安全隐患。

```java
class myThread implements Callable<String>{
	@Override
	public String call() throws Exception {
		for (int x = 0; x < 10; x++) {
			System.out.println("****** 线程执行、x="+x);
		}
		return "线程执行完毕";
	}
}

public class RunnableDemo {
	public static void main(String[] args) throws Exception{
	    FutureTask<String> task = new FutureTask<String>(new myThread());
        new Thread(task).start();
        System.out.println("线程返回数据"+task.get());
	}
}
```

**请解释Runnable与Callable的区别？**

* Runnable是在JDK1.0的时候提出的多线程的实现接口，而Callable是在JDK1.5之后提出的；
* java.kang.Runnable接口之中只提供有一个run()方法，并且没有返回值；
* java.util.concurrent.Callable接口提供有call()方法，可以有返回值；

### 2.Java线程具有五中基本状态

![](https://img-blog.csdnimg.cn/20190407112603812.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dtNTkyMA==,size_16,color_FFFFFF,t_70)

- <font color="lighblue">新建状态（New）</font>：当线程对象对创建后，即进入了新建状态，如：Thread t = new MyThread();
- <font color="lighblue">就绪状态（Runnable）</font>：当调用线程对象的start()方法（t.start();），线程即进入就绪状态。处于就绪状态的线程，只是说明此线程已经做好了准备，随时等待CPU调度执行，并不是说执行了t.start()此线程立即就会执行；
- <font color="lighblue">运行状态（Running）</font>：当CPU开始调度处于就绪状态的线程时，此时线程才得以真正执行，即进入到运行状态。注：就 绪状态是进入到运行状态的唯一入口，也就是说，线程要想进入运行状态执行，首先必须处于就绪状态中；
- <font color="lighblue">阻塞状态（Blocked）</font>：处于运行状态中的线程由于某种原因，暂时放弃对CPU的使用权，停止执行，此时进入阻塞状态，直到其进入到就绪状态，才 有机会再次被CPU调用以进入到运行状态。根据阻塞产生的原因不同，阻塞状态又可以分为三种：
  - 1.等待阻塞：运行状态中的线程执行wait()方法，使本线程进入到等待阻塞状态；
  - 2.同步阻塞 – 线程在获取synchronized同步锁失败(因为锁被其它线程所占用)，它会进入同步阻塞状态；
  - 3.超时阻塞 – 通过调用线程的sleep()或join()或发出了I/O请求时，线程会进入到阻塞状态。当sleep()状态超时、join()等待线程终止或者超时、或者I/O处理完毕时，线程重新转入就绪状态。
- <font color="lighblue">死亡状态（Dead）</font>：线程执行完了或者因异常退出了run()方法，该线程结束生命周期。

### 3.线程常用操作方法

##### 1.线程的命名与取得

多线程的运行状态不确定，为了获取到一些需要使用的操作，所以线程的名字很重要，在Thread类之中就提供有线程名称。

* <font color="lighblue">构造方法</font>：public Thread(Runnable target,String name);
* <font color="lighblue">设置名字</font>：public final void setName(String name);
* <font color="lighblue">取得名字</font>：public final String getName();

```java
class MyThread implements Runnable{
  	@Override
  	public void run() {
        System.out.println(Thread.currentThread().getName());
  	}
}

public class ThreadDemo {
	public static void main(String[] args) {
		MyThread mt = new MyThread();
		new Thread(mt,"线程C").start();
		new Thread(mt).start();
		new Thread(mt,"线程D").start();
	}
}
/*线程C
Thread-1
线程D*/
```

每当使用java命令执行程序的时候就表示启动了一个JVM的进程，一台电脑上可以同时启动若干个JVM进程，JVM进程都会有各自的线程。

在任何的开发之中，主线程可以创建若干个子线程，**创建子线程的目的是可以将一些复杂的逻辑交给子线程处理。**

```java
public static void main(String[] args) {
		System.out.println("1.执行操作任务一");
		new Thread(()->{ //子线程
			int temp = 0;
			for(int x=0;x<Integer.MAX_VALUE;x++) {
				temp += x;
			}
		}).start();
		System.out.println("2.执行操作任务二");
		System.out.println("n.执行操作任务N");
}
```

**主线程负责处理整体流程，而子线程负责处理耗时操作。**

##### 2.线程的休眠

如果希望某一个线程可以暂缓执行，那么就可以使用<font color="lighblue">休眠</font>的处理，在Thread类之中定义：

* 休眠：public static void sleep(long millis) throws InterruptedException;
* 休眠：public static void sleep(long millis,int nanos) throws InterruptedException;

在进行休眠的时候可能会产生<font color="lighblue">中断异常"InterruptedException"</font>,中断异常属于Exception的子类，所有该子类必须进行处理。

```java
public static void main(String[] args) {
		new Thread(()->{
			for (int i = 0; i < 10; i++) {
				System.out.println(Thread.currentThread().getName()+"i="+i);
				try {
					Thread.sleep(1000);
				} catch (InterruptedException e) {
					// TODO Auto-generated catch block
					e.printStackTrace();
				}
			}
		},"线程对象").start();
	}
```

休眠的主要特点是可以自动实现线程的唤醒，以继续进行后续的处理。但如果你有多个线程对象，休眠也是有先后顺序的。

##### 3.线程中断

线程的休眠是可以被打断的，而这种打断是由其他线程完成的，在Thread类里面提供有这种中断执行的处理方法：

* <font color="lighblue">判断线程是否被中断</font>：public boolean isInterrupted();
* <font color="lighblue">中断线程执行</font>：public void interrupt();

```java
public static void main(String[] args) throws InterruptedException {
		Thread thread = new Thread(()-> {
			System.out.println("***工作72小时，我需要睡觉");
			try {
				Thread.sleep(10000);
				System.out.println("休息完成，继续工作");
			} catch (InterruptedException e) {
				System.out.println("我的休息被打扰");
			}
		});
		thread.start();//开始睡
		Thread.sleep(1000);
		if(!thread.isInterrupted()) { //该线程中断了吗？
			System.out.println("偷偷打扰你休息");
			thread.interrupt();
		}
}
```

所有正在执行的线程都是可以被中断的，中断线程必须进行异常的处理。

##### 4.线程的强制执行

所谓的**线程的强制执行**指的是当满足于某些条件之后，某一个线程对象将可以一直独占资源，一直到该线程的程序执行结束。

* 强制执行：Thread.join()

  ```java
  public static void main(String[] args) throws InterruptedException {
  		Thread mainThread = Thread.currentThread();//获得主线程
  		Thread thread  = new Thread(()-> {
  			for (int x = 0; x < 50; x++) {
  				if (x==3) { //霸道的线程来临
  					try {
  						mainThread.join(); //霸道线程要先执行
  					} catch (InterruptedException e) {
  						e.printStackTrace();
  					}
  				}
  				try {
  					Thread.sleep(100);
  				} catch (InterruptedException e) {
  					e.printStackTrace();
  				}
  				System.out.println(Thread.currentThread().getName()+"执行x="+x);
  			}
  		},"玩耍的线程");
  		thread.start();
  		for (int x = 0; x < 50; x++) {
  			Thread.sleep(100);
  			System.out.println("【霸道的main线程】number="+x);
  		}
  }
  ```

  在进行线程强制执行的时候一定要获取强制执行线程对象之后才可以执行join()的调用。

##### 5.线程的礼让

线程的礼让指的是先将资源让出去让别的线程先执行。

* 礼让：public static void yield()

```java
public static void main(String[] args) throws InterruptedException {
		Thread thread  = new Thread(()-> {
			for (int x = 0; x < 50; x++) {
				if (x%3 == 0) { 
					Thread.yield(); //线程礼让
					System.out.println("###玩耍的线程礼让执行");
				}
				try {
					Thread.sleep(100);
				} catch (InterruptedException e) {
					e.printStackTrace();
				}
				System.out.println(Thread.currentThread().getName()+"执行x="+x);
			}
		},"玩耍的线程");
		thread.start();
		for (int x = 0; x < 50; x++) {
			Thread.sleep(100);
			System.out.println("【霸道的main线程】number="+x);
		}
}
```

礼让执行的时候每一次调用yield()方法都只会礼让一次当前的资源。

##### 6.线程的优先级

从理论上来说，线程的优先级越高越<font color="lighblue">有可能先执行</font>(越有可能先抢占到资源)。

* 设置优先级：public final void setPriority(int newPriority);
* 获取优先级：public final int getPriority()。

在进行优先级定义的时候都是通过int型的数字来完成的，而对于此数字的选择在Thread类里就定义有三个常量

* 最高优先级：public static final int MAX_PRIORITY;
* 中等优先级：public static final int NORM_PRIORITY;
* 最低优先级：public static final int MIN_PRIORITY;

```java
public static void main(String[] args) {
		Runnable run = ()->{
			for (int x = 0; x < 10; x++) {
				try {
					Thread.sleep(100);
				} catch (InterruptedException e) {
					e.printStackTrace();
				}
				System.out.println(Thread.currentThread().getName()+"执行。");
			}
		};
		Thread threadA = new Thread(run,"线程对象A" );
		Thread threadB = new Thread(run,"线程对象B" );
		Thread threadC = new Thread(run,"线程对象C" );
		threadA.setPriority(Thread.MIN_PRIORITY);
		threadA.setPriority(Thread.MIN_PRIORITY);
		threadA.setPriority(Thread.MAX_PRIORITY);
		threadA.start();
		threadB.start();
		threadC.start();
}
```

主线程是一个中等优先级，而默认创建的线程也是中等优先级，为5。

### 4.线程的同步与死锁

##### 1.同步问题

在多线程的处理之中，可以利用Runnable描述多个线程操作的资源，而Thread描述每一个线程对象，于是当多个线程访问同一资源的时候如果处理不当就会产生数据的错误操作。

##### 2.线程同步

利用<font color="lighblue">synchronized关键字</font>定义同步方法或同步代码块，在同步代码块的操作里面的代码只允许一个线程执行。

```java
synchronized(同步对象){
    同步代码操作;
}
```

加入同步处理之后，程序整体的性能下降了。同步实际上会造成性能的降低。

```java
class MyThread implements Runnable {
	private int ticket=10;//总票数
    //同步方法
	public synchronized boolean sale() {//一次只允许一个线程进行访问
		if(this.ticket > 0) {
		try {
			Thread.sleep(100); //模拟网络延迟
		} catch (InterruptedException e) {
			e.printStackTrace();
		}
			System.out.println(Thread.currentThread().getName()+"卖票，ticket="+this.ticket--);
			return true;
		}else {
			System.out.println("****票已经卖光了");
			return false;
		}
	}
	@Override
	public void run() {
		while(this.sale()) {
			this.sale();
	  }
	}
}
public class ThreadDemo{
	public static void main(String[] args) throws Exception{
		MyThread mt = new MyThread();
		new Thread(mt,"票贩子A").start();
		new Thread(mt,"票贩子B").start();
		new Thread(mt,"票贩子C").start();
	}
}
```

##### 3.线程死锁

所谓的<font color="lighblue">死锁</font>指的是若干个线程彼此互相等待的状态。

若干个线程访问同一资源时一定要进行同步处理，而过多的同步会造成死锁。

**线程死锁的四个必要条件：**

**1、互斥条件**

  即一段时间内某资源仅为一个线程所占有，此时若其他线程请求该资源，则请求线程都需要等待。

**2、不可剥夺条件**

  即线程所获得的资源在未使用完毕之前，不能被其他线程强行夺走，只能由获得该资源的线程自己主动释放。

**3、请求与保持条件**（吃着碗里的望着锅里的）

  即线程已经至少保持了一个资源，但又提出了新的资源请求，而该资源已经被其他线程占有，此时请求线程被阻塞，但对自己已获得的资源保持不放。

**4、循环等待条件**

  存在一种线程资源的循环等待链，链中每一个已获得的资源同时被链中下一个线程所请求。

### 5.线程深入

##### 1.停止线程

```java
public class ThreadDemo{
	public static boolean flag = true;
	public static void main(String[] args) throws Exception{
		new Thread(()->{
			long num = 0;
			while(flag) {
				try {
					Thread.sleep(50);
				} catch (InterruptedException e) {
					e.printStackTrace();
				}
				System.out.println(Thread.currentThread().getName()+"正在运行、num"+num++);
			}
		},"执行线程").start();
		Thread.sleep(200);//运行200毫秒
		flag = false;//停止线程
	}
}
```

线程无法立即停止，会在执行中判断flag的内容来完成。

**停止线程的方式：**

* 使用退出标志，使线程正常退出，也就是当 run() 方法完成后线程中止。
* 使用 stop() 方法强行终止线程，但是不推荐使用这个方法，该方法已被**弃用**。
* 使用 interrupt 方法中断线程。

##### 2.守护线程

主线程的程序或者其它的线程还在执行的时候，那么守护线程将一直存在，并且运行在后台状态。

* <font color="lighblue">设置为守护线程</font>：public final void setDaemon(boolean on);
* <font color="lighblue">判断是否为守护线程</font>：public final boolean isDaemon();

```java
public static void main(String[] args) throws Exception{
		Thread userThread = new Thread(()->{
			for (int x = 0; x < 10; x++) {
				try {
					Thread.sleep(100);
				} catch (InterruptedException e) {
					e.printStackTrace();
				}
				System.out.println(Thread.currentThread().getName()+"正在运行、x"+x);
			}
		},"用户线程");
		Thread daemonThread = new Thread(()->{
			for (int x = 0; x < Integer.MAX_VALUE; x++) {
				try {
					Thread.sleep(100);
				} catch (InterruptedException e) {
					e.printStackTrace();
				}
				System.out.println(Thread.currentThread().getName()+"正在运行、x"+x);
			}
		},"守护线程");
		daemonThread.setDaemon(true);//设置为守护线程
		userThread.start();
		daemonThread.start();
}
```

用户线程执行完毕，守护线程也将消失。

##### 3.生产电脑(生产者消费者)

```java
public class ThreadDemo{
	public static void main(String[] args) throws Exception{
		Resource res = new Resource();
		new Thread(new Producer(res)).start();
		new Thread(new Consumer(res)).start();
	}
}
//生产者
class Producer implements Runnable{
	private Resource resource;
    public Producer(Resource resource) {
    	this.resource = resource;
	}
	@Override
	public void run() {
		for (int x = 0; x < 50; x++) {
			try {
				this.resource.make();
			} catch (Exception e) {
				e.printStackTrace();
			}
		}
	};
}
//消费者
class Consumer implements Runnable{
	private Resource resource;
    public Consumer(Resource resource) {
    	this.resource = resource;
	}
	@Override
	public void run() {
		for (int x = 0; x < 50; x++) {
			try {
				this.resource.get();
			} catch (Exception e) {
				e.printStackTrace();
			}
		}
	};
}

class Resource {
	private Computer computer;
	private boolean flag = true;//标记
	public synchronized void make() throws Exception {
		if(this.computer != null) {//已经生产过了
			super.wait();
		}
		Thread.sleep(100);
		this.computer = new Computer("MLDB电脑", 3500.0);
		System.out.println("【生产电脑】"+this.computer);
		super.notifyAll();//唤醒get()方法中休眠的线程
	}
	public synchronized void get() throws Exception {
		if(this.computer == null) {//没有生产过
			super.wait();
		}
		Thread.sleep(10);
		System.out.println("【取走电脑】"+this.computer);
		System.out.println(this.computer);
		this.computer = null;
		super.notify();//make()方法中休眠的线程
	}
}
//商品
class Computer{
	private static int count = 0;//表示生产的个数
	private String name;
	private double price;
	public Computer(String name,double price) {
		this.name = name;
		this.price = price;
		this.count++;
	}
	public String toString() {
		return "【第"+count+"台电脑】"+"电脑名字："+this.name+"价值："+this.price;
	}
}
```

![](https://note.youdao.com/yws/api/personal/file/6452C039B84B48E4AC3906BE9FF29C2B?method=download&shareKey=4c3f7dc82e138bb512637a6b2827dab9)

### 6.wait和sleep方法的区别

- 来自不同的类
  - wait():来自 Object 类； 
  - sleep():来自 Thread 类； 
- 关于锁的释放
  - wait():在等待的过程中会释放锁； 
  - sleep():在等待的过程中不会释放锁 
- 使用的范围
  - wait():必须在synchronized同步代码块中使用； 
  - sleep():可以在任何地方使用； 
- 是否需要捕获异常
  - wait():不需要捕获异常； 
  - sleep():需要捕获异常； 
- 唤醒方式
  - wait(): 需要其他线程的notify() / notifyAll()唤醒； 
  - sleep():时间到或被中断
- 调用位置
  - wait():必须在`synchronized`中；
  - sleep():任意位置；

**sleep：让出CPU资源，不释放锁资源。**

**wait()：让出CPU资源和锁资源。**

`wait()`、`notify()`和`notifyAll()`方法必须在**synchronized**同步块或同步方法中调用。

这三个方法的作用是**协调多个线程对共享资源的访问**，它们的调用需要依赖于**对象的内置锁（monitor lock）**：

- `wait()`：使当前线程释放锁并进入等待状态，需其他线程调用`notify()`唤醒。
- `notify()`/`notifyAll()`：唤醒等待该锁的线程，使其重新竞争锁。

### 7.线程安全

就是**线程同步**的意思，就是当一个程序对一个线程安全的方法或者语句进行访问的时候，其他的不能再对他进行操作了，必须等到这次访问结束以后才能对这个线程安全的方法进行访问。

**多个线程访问同一个对象时，调用这个对象的行为都可以获得正确的结果，那么这个对象就是线程安全的。**

线程安全问题都是由全局变量及静态变量引起的。

* 1.<font color="lighblue">原子性</font>：提供互斥访问，同一时刻只能有一个线程对数据进行操作，（atomic,synchronized）；
* 2.<font color="lighblue">可见性</font>：一个线程对主内存的修改可以及时地被其他线程看到，（synchronized,volatile）；
* 3.<font color="lighblue">有序性</font>：一个线程观察其他线程中的指令执行顺序，由于指令重排序，该观察结果一般杂乱无序，（happens-before原则）。

为保证线程安全，可以添加synchroized关键字，上Lock锁，加volatile关键字。

### 8.synchronized关键字

JDK提供锁分两种：一种是synchronized，依赖JVM实现锁，因此在这个关键字作用对象的作用范围内是同一时刻只能有一个线程进行操作；另一种是LOCK，是JDK提供的代码层面的锁，依赖CPU指令，代表性的是ReentrantLock。

**synchronized修饰的对象有四种：**

（1）修饰代码块，作用于调用的对象；

（2）修饰方法，作用于调用的对象；

（3）修饰静态方法，作用于所有对象；

（4）修饰类，作用于所有对象。

##### synchronized用处

- synchronized保证了线程的<font color="lighblue">原子性</font>。(被保护的代码块是一次被执行的，没有任何线程会同时访问)
- synchronized还保证了<font color="lighblue">可见性</font>。(当执行完synchronized之后，修改后的变量对其他的线程是可见的)
- synchronized还保证了<font color="lighblue">有序性</font>。

Java中的synchronized，通过使用内置锁，来实现对变量的同步操作，进而实现了**对变量操作的原子性和其他线程对变量的可见性**，从而确保了并发情况下的线程安全。

### 9.volatile关键字

**volatile** 关键字的主要作用就是<font color="lighblue">保证变量的可见性和有序性</font>，然后还有一个作用是<font color="lighblue">防止指令重排序</font>。

volatile 修饰的成员变量在每次被线程访问时，都强制从共享内存中重新读取该成员变量的值。而且，当成员变量发生变化时，会强制线程将变化值回写到共享内存。这样在任何时刻，两个不同的线程总是看到某个成员变量的同一个值。

**有序性**

指令重排序是 JVM 为了优化性能，在不影响单线程执行结果的前提下，对指令执行顺序的调整（可能导致多线程环境下执行顺序混乱）。

可以通过volatile、synchronized、lock保证<font color="lighblue">有序性</font>。

#### synchronized关键字和volatile关键字比较

- **volatile关键字**是线程同步的**轻量级实现**，所以**volatile性能肯定比synchronized关键字要好**。但是**volatile关键字只能用于变量而synchronized关键字可以修饰方法以及代码块**。synchronized关键字在JavaSE1.6之后进行了主要包括为了减少获得锁和释放锁带来的性能消耗而引入的偏向锁和轻量级锁以及其它各种优化之后执行效率有了显著提升，**实际开发中使用synchronized关键字还是更多一些**。
- **多线程访问volatile关键字不会发生阻塞，而synchronized关键字可能会发生阻塞**
- **volatile关键字能保证数据的可见性，但不能保证数据的原子性。synchronized关键字两者都能保证。**
- **volatile关键字用于解决变量在多个线程之间的可见性，而synchronized关键字解决的是多个线程之间访问资源的同步性。**

### 10.Lock锁

- Lock方式来获取锁**支持中断、超时不获取、是非阻塞的**
- **提高了语义化**，哪里加锁，哪里解锁都得写出来
- **Lock显式锁可以给我们带来很好的灵活性，但同时我们必须手动释放锁**
- 支持Condition条件对象
- **允许多个读线程同时访问共享资源**

Lock锁和Synchronized锁的性能其实**差别不是很大**！而Synchronized锁用起来又特别简单。**Lock锁还得顾忌到它的特性，要手动释放锁才行**(如果忘了释放，这就是一个隐患)。Lock同样保证了<font color="lighblue">原子性、可见性和有序性</font>。

所以说，我们**绝大部分时候还是会使用Synchronized锁**，用到了Lock锁提及的特性，带来的灵活性才会考虑使用Lock显式锁~

#### Lock 比 Synchronized的区别

Lock 比 Synchronized 的灵活性更高，Lock 可以自主决定什么时候加锁，什么时候释放锁，只需要调用 lock(）和 unlock(）这两个方法就行，同时 Lock 还提供了非阻塞的竞争锁方法tryLock(）方法，这个方法通过返回 true/false 来告诉当前线程是否已经有其他线程正在使用锁。Synchronized 由于是关键字，所以它无法实现非阻塞竞争锁的方法，另外，Synchronized 锁的释放是被动的，就是当 Synchronized 同步代码块执行完以后或者代码出现异常时才会释放。

Lock 提供了公平锁和非公平锁的机制，Synchronized 只提供了一种非公平锁的实现。

#### Lock 接口的主要方法

- `void lock()`: 获取锁，如果锁被占用则阻塞
- `void lockInterruptibly() throws InterruptedException`: 获取锁，但可以被中断
- `boolean tryLock()`: 尝试获取锁，立即返回，获取成功返回 true
- `boolean tryLock(long time, TimeUnit unit) throws InterruptedException`: 在指定时间内尝试获取锁
- `void unlock()`: 释放锁
- `Condition newCondition()`: 创建一个与该锁关联的条件对象

#### ReentrantLock

ReentrantLock 是一种可重入的排他锁，主要用来解决多线程对共享资源竞争的问题。

* 它支持可重入，也就是获得锁的线程在释放锁之前再次去竞争同一把锁的时候，不需要加锁就可以直接访问。 这一特性避免了线程在嵌套调用中因重复获取自身持有的锁而导致的死锁。
* 它支持公平和非公平特性。
* 它提供了阻塞竞争锁和非阻塞竞争锁的两种方法，分别是 lock(）和 tryLock()。

```java
public class LockExample {
    // 创建锁对象
    private final Lock lock = new ReentrantLock();
    private int count = 0;
    
    // 加锁的方法
    public void increment() {
        // 获取锁
        lock.lock();
        try {
            // 临界区代码
            count++;
            System.out.println(Thread.currentThread().getName() + " 增加后: " + count);
        } finally {
            // 确保锁被释放，放在finally中
            lock.unlock();
        }
    }
    
    public static void main(String[] args) {
        LockExample example = new LockExample();
        // 创建多个线程测试
        for (int i = 0; i < 5; i++) {
            new Thread(() -> {
                for (int j = 0; j < 3; j++) {
                    example.increment();
                }
            }, "线程" + (i + 1)).start();
        }
    }
}
```

### 11.线程池

#### 1.线程池的概念

线程池就是首先创建一些线程，它们的集合称为线程池。使用线程池可以很好地提高性能，线程池在系统启动时即创建大量空闲的线程，程序将一个任务传给线程池，线程池就会启动一条线程来执行这个任务，执行结束以后，该线程并不会死亡，而是再次返回线程池中成为空闲状态，等待执行下一个任务。

#### 2.线程池的工作机制

在线程池的编程模式下，任务是提交给整个线程池，而不是直接提交给某个线程，线程池在拿到任务后，就在内部寻找是否有空闲的线程，如果有，则将任务交给某个空闲的线程。

一个线程同时只能执行一个任务，但可以同时向一个线程池提交多个任务。

#### 3.使用线程池的原因

多线程运行时间，系统不断的启动和关闭新线程，成本非常高，会过渡消耗系统资源，以及过渡切换线程的危险，从而可能导致系统资源的崩溃。这时，线程池就是最好的选择了。

#### 4.线程池的优势

第一：**降低资源消耗**。通过重复利用已创建的线程降低线程创建和销毁造成的消耗。

第二：**提高响应速度**。当任务到达时，任务可以不需要等到线程创建就能执行。

第三：**提高线程的可管理性**。线程是稀缺资源，如果无限制地创建，不仅会消耗系统资源，还会降低系统的稳定性，使用线程池可以进行统一分配、调优和监控。

#### 5.线程池的工作流程

![](https://note.youdao.com/yws/api/personal/file/E37E86934C90414696F230CE9D750A7B?method=download&shareKey=906dedaf3b87101ecdd2bab8347cdcf9)

1. 线程在有任务的时候会创建核心的线程数corePoolSize。
2. 当线程满了（有任务但是线程被使用完）不会立即扩容，而是放到阻塞队列中，当阻塞队列满了之后才会继续创建线程。
3. 如果队列满了，线程数达到最大线程数则会执行拒绝策略。
4. 当线程数大于核心线程数时，超过KeepAliveTime(闲置时间)，线程会被回收，最终会保持corePoolSize个线程。

#### 工作原理（任务执行流程）

1.当提交一个任务时，若当前线程数 < 核心线程数，直接创建新线程执行任务；
2.若当前线程数 ≥ 核心线程数，将任务放入队列等待；
3.若队列已满，且当前线程数 < 最大线程数，创建临时线程执行任务；
4.若队列已满且线程数达到最大值，触发拒绝策略。

#### 6.常见的线程池

* <font color="lighblue">ThreadPoolExecutor</font>：自定义线程池，可根据业务需求调整核心参数，灵活性高。
* <font color="lighblue">CachedThreadPool</font>：可缓存的线程池，该线程池中没有核心线程，非核心线程的数量为Integer.max_value，就是无限大，当有需要时创建线程来执行任务，没有需要时回收线程，适用于耗时少，任务量大的情况。
* <font color="lighblue">SecudleThreadPool</font>：定时任务线程池，按照某种特定的计划执行线程中的任务，有核心线程，但也有非核心线程，非核心线程的大小也为无限大。适用于定时任务（如定时备份、周期性检查）。
* <font color="lighblue">SingleThreadExecutor</font>：单线程化线程池，只有一条核心线程来执行任务，适用于需要保证任务顺序执行的场景（如日志记录、单线程事务处理）。
* <font color="lighblue">SingleThreadScheduledExecutor</font>：单线程定时任务池，仅 1 个核心线程，定时任务按顺序执行。适用于需要单线程执行的定时任务（如单线程调度的周期性任务）。
* <font color="lighblue">FixedThreadPool</font>：固定大小线程池，有核心线程，核心线程的即为最大的线程数量，没有非核心线程。适用于任务数量已知、需要控制并发量的场景。若任务过多，队列可能无限增长导致 OOM。

#### 7.核心线程池内部实现了解吗

对于核心的几个线程池，无论是 newFixedThreadPool() 方法，newSingleThreadExecutor() 还是 newCachedThreadPool() 方法，虽然看起来创建的线程有着完全不同的功能特点，但其实内部实现均使用了 ThreadPoolExecutor 实现，其实都只是 ThreadPoolExecutor 类的封装。

```java
    public ThreadPoolExecutor(int corePoolSize,
                              int maximumPoolSize,
                              long keepAliveTime,
                              TimeUnit unit,
                              BlockingQueue<Runnable> workQueue,
                              ThreadFactory threadFactory,
                              RejectedExecutionHandler handler)
```

函数的参数含义如下：

- corePoolSize：指定了线程池中的线程数量
- maximumPoolSize：指定了线程池中的最大线程数量
- keepAliveTime：当线程池线程数量超过 corePoolSize 时，多余的空闲线程的存活时间。即，超过了 corePoolSize 的空闲线程，在多长时间内，会被销毁。
- unit: keepAliveTime 的单位。
- workQueue：任务队列，被提交但尚未被执行的任务。
- threadFactory：线程工厂，用于创建线程，一般用默认的即可。
- handler：拒绝策略。当任务太多来不及处理，如何拒绝任务。

##### 核心参数设计原则

CPU 密集型任务：任务主要消耗 CPU 资源（如数据计算、视频编码），CPU 利用率高。

IO 密集型任务：任务大部分时间在等待 IO 操作（如网络请求、数据库读写、文件 IO），CPU 利用率低。

1. 核心线程数（corePoolSize）
核心线程是线程池长期维持的线程数量，即使空闲也不会销毁。
CPU 密集型：建议设置为 CPU 核心数 ± 1
（例如 4 核 CPU 设置为 3-5，避免线程切换开销抵消并行优势）
IO 密集型：建议设置为 CPU 核心数 × 2
（因为线程大部分时间在等待 IO，多线程可提高利用率）
2. 最大线程数（maximumPoolSize）
最大线程数是线程池可扩展到的最大线程数量（超过核心线程的部分为 “非核心线程”，空闲时会被销毁）。
CPU 密集型：通常与核心线程数相同（corePoolSize = maximumPoolSize），因为增加更多线程会导致 CPU 切换开销增大，反而降低效率。
IO 密集型：可设置为 CPU 核心数 × 5~10（根据 IO 等待时间调整，等待时间越长，可设置越大）。
3. 参考公式
设 CPU 核心数为 N：
CPU 密集型：corePoolSize = N，maximumPoolSize = N
IO 密集型：corePoolSize = 2N，maximumPoolSize = 10N（需结合实际压测调整）

#### 8.拒绝策略

**RejectedExectutionHandler参数字段用于配置绝策略，常用拒绝策略如下**

- AbortPolicy(系统默认) ：丢弃任务并抛出RejectedExecutionException 异常，让你感知到任务被拒绝了，我们可以根据业务逻辑选择重试或者放弃提交等策略。
- DiscardPolicy：也是丢弃任务，但是不抛出异常，相对而言存在一定的风险，因为我们提交的时候根本不知道这个任务会被丢弃，可能造成数据丢失。 
- DiscardOldestPolicy：丢弃队列最前面的任务，然后重新尝试执行任务(重复此过程)，通常是存活时间最长的任务，它也存在一定的数据丢失风险。
- CallerRunsPolicy：既不抛弃任务也不抛出异常，而是将某些任务回退到调用者，让调用者去执行它。

#### 9.ThreadPoolTaskExecutor线程池

> https://zhuanlan.zhihu.com/p/346086161

ThreadPoolTaskExecutor配置

application.properties

```properties
# 核心线程池数
spring.task.execution.pool.core-size=5
# 最大线程池数
spring.task.execution.pool.max-size=10
# 任务队列的容量
spring.task.execution.pool.queue-capacity=5
# 非核心线程的存活时间
spring.task.execution.pool.keep-alive=60
# 线程池的前缀名称
spring.task.execution.thread-name-prefix=god-jiang-task-
```

ThreadConfig.java

```java
//启用异步任务
@Configuration
@EnableAsync
public class AsyncScheduledTaskConfig {

    @Value("${spring.task.execution.pool.core-size}")
    private int corePoolSize;
    @Value("${spring.task.execution.pool.max-size}")
    private int maxPoolSize;
    @Value("${spring.task.execution.pool.queue-capacity}")
    private int queueCapacity;
    @Value("${spring.task.execution.thread-name-prefix}")
    private String namePrefix;
    @Value("${spring.task.execution.pool.keep-alive}")
    private int keepAliveSeconds;
    @Bean
    public Executor myAsync() {
        ThreadPoolTaskExecutor executor = new ThreadPoolTaskExecutor();
        //最大线程数
        executor.setMaxPoolSize(maxPoolSize);
        //核心线程数
        executor.setCorePoolSize(corePoolSize);
        //任务队列的大小
        executor.setQueueCapacity(queueCapacity);
        //线程前缀名
        executor.setThreadNamePrefix(namePrefix);
        //线程存活时间
        executor.setKeepAliveSeconds(keepAliveSeconds);

        /**
         * 拒绝处理策略
         * CallerRunsPolicy()：交由调用方线程运行，比如 main 线程。
         * AbortPolicy()：直接抛出异常。
         * DiscardPolicy()：直接丢弃。
         * DiscardOldestPolicy()：丢弃队列中最老的任务。
         */
        executor.setRejectedExecutionHandler(new ThreadPoolExecutor.AbortPolicy());
        //线程初始化
        executor.initialize();
        return executor;
    }
}
```

在方法上添加@Async注解，然后还需要在@SpringBootApplication启动类或者@Configuration注解类上 添加注解@EnableAsync启动多线程注解，@Async就会对标注的方法开启异步多线程调用，注意，这个方法的类一定要交给Spring容器来管理。

ScheduleTask.java

```java
@Component
public class ScheduleTask {

    SimpleDateFormat sdf = new SimpleDateFormat("yyyy-MM-dd HH:mm:ss");

    @Async("myAsync")
    @Scheduled(fixedRate = 2000)
    public void testScheduleTask() {
        try {
            Thread.sleep(6000);
            System.out.println("Spring1自带的线程池" + Thread.currentThread().getName() + "-" + sdf.format(new Date()));
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
    }

    @Async("myAsync")
    @Scheduled(cron = "*/2 * * * * ?")
    public void testAsyn() {
        try {
            Thread.sleep(1000);
            System.out.println("Spring2自带的线程池" + Thread.currentThread().getName() + "-" + sdf.format(new Date()));
        } catch (Exception ex) {
            ex.printStackTrace();
        }
    }
}
```

![](https://note.youdao.com/yws/api/personal/file/6CDDF77986E844C3ADC0E1B3923D9FA0?method=download&shareKey=f433080ad78bf876c50e82e99c8f2336)

#### 10.@Async异步注解

@Async是 Spring 框架提供的注解，用于实现方法的异步执行，它能显著改变程序的执行模式。

**核心特性**

1. **异步执行**
   被`@Async`标注的方法会脱离当前调用线程，由 Spring 管理的线程池（默认或自定义）中的线程执行，调用方无需等待该方法执行完成即可继续后续操作。
2. **线程管理**
   - 默认使用 Spring 的`SimpleAsyncTaskExecutor`（不推荐生产环境，因为它可能无限制创建线程）。
   - 实际开发中通常会自定义线程池（如`ThreadPoolTaskExecutor`），通过`@Async("自定义线程池Bean名")`指定，以控制线程数量、队列大小等。
3. **使用条件**
   - 需要在配置类上添加`@EnableAsync`开启异步功能。
   - 注解只能用于`public`方法，且不能在同一个类中直接调用（Spring AOP 代理机制限制）。
4. **返回值支持**
   - 无返回值：直接用`void`。
   - 有返回值：需返回`Future`或其实现类（如`CompletableFuture`），用于获取异步执行结果。

| 方案                 | 适用场景                       | 优点                 | 缺点                         |
| -------------------- | ------------------------------ | -------------------- | ---------------------------- |
| 纯同步（无 @Async）  | 数据量小（<100）、强顺序依赖   | 无并发问题，实现简单 | 耗时过长，可能阻塞主线程     |
| 无控制的 @Async      | 无数据库操作的纯计算 / IO 任务 | 效率最高             | 数据库连接耗尽、事务冲突严重 |
| 线程池 + 分批 + 重试 | 数据量大（>100）、有数据库操作 | 平衡效率与安全性     | 实现稍复杂，需调优线程参数   |

**总结**：@Async的核心价值是通过多线程并行提升系统吞吐量，但也引入了并发带来的复杂性（如线程安全、事务同步）。不加@Async的同步执行虽然简单安全，但在处理大量任务时效率较低。实际开发中需根据业务的并发需求、数据一致性要求和性能目标选择合适的执行方式。

**注意**：@Async 方法可以在同一个类中被调用，但默认会失效（同步执行），因为内部调用绕过了 Spring 代理。解决方式是让调用经过代理对象，而最佳实践是将异步方法拆分到独立的类中，既符合单一职责原则，又能避免代理失效问题。

#### 线程安全问题

```java
@Service
public class AsyncDbService {
    @Autowired
    private UserRepository userRepository;

    @Async
    public void addUserIfNotExists(String username) {
        // 步骤1：检查用户是否存在（读操作）
        boolean exists = userRepository.existsByUsername(username);
        if (!exists) {
            // 步骤2：插入新用户（写操作）
            try { Thread.sleep(100); } catch (InterruptedException e) {} // 模拟延迟
            userRepository.save(new User(username));
        }
    }
}

// 测试：同时添加同一个用户
@Test
public void testDb() throws InterruptedException {
    String username = "testUser";
    // 启动3个异步任务，尝试添加同一用户
    for (int i = 0; i < 3; i++) {
        service.addUserIfNotExists(username);
    }
    Thread.sleep(1000);
    long count = userRepository.countByUsername(username);
    System.out.println("用户" + username + "的数量：" + count); // 预期1，实际可能3
}
```

@Async可能会引起线程安全问题，@Async 的线程安全问题本质是多线程并发访问共享资源导致的冲突，而非 @Async 本身的问题。解决的核心是通过 “减少共享”“同步控制” 或 “数据库约束” 等方式，确保共享资源的操作在多线程下的安全性。

#### 如何避免这些问题？

* **避免共享资源**：优先使用局部变量或线程私有变量（如 `ThreadLocal`），减少多线程对共享资源的依赖。
* **同步机制**：对共享资源的操作加锁（`synchronized`、`Lock`），或使用线程安全的容器（`ConcurrentHashMap`、`AtomicInteger`）。
* **数据库层面**：通过唯一索引、乐观锁（`version`）或分布式锁（如 Redis 锁）避免重复操作。
* **控制并发粒度**：使用 `CompletableFuture` 等工具协调异步任务的执行顺序，避免资源竞争。

### 1.日期格式化

为了可以格式化日期，在java.text包中提供有<font color="lighblue">SimpleDateFormat程序类</font>，该类是DateFormat的子类，在该类中提供有如下的方法：

* 【DateFormat继承】<font color="lighblue">将日期格式化</font>：public final String format(Date date);

* 【DateFormat继承】<font color="lighblue">将字符串转为日期</font>：public Date parse(String source) throws ParseException;

* 构造方法：public SimpleDateFormat(String pattern);

  * **日期格式：年(yyyy)、月(MM)、日(dd)、时(HH)、分(mm)、秒(ss)、毫秒(SSS)**

    ```java
    public static void main(String[] args) {
           Date date = new Date();
           SimpleDateFormat sdf = new SimpleDateFormat("yyyy-MM-dd HH:mm:ss.SSS");
           String str = sdf.format(date);
           System.out.println(str);
    //     结果：2020-08-06 09:49:48.029
    }
    ```
    
  * 处理可以将日期格式化为字符串之后，也可以实现字符串与日期之间转换处理。
  
    ```java
    public static void main(String[] args) throws ParseException  {
    	   String birthday = "1999-10-22 11:22:11.111"; 
           SimpleDateFormat sdf = new SimpleDateFormat("yyyy-MM-dd HH:mm:ss.SSS");
           Date date = sdf.parse(birthday);
           System.out.println(date);
    //       Fri Oct 22 11:22:11 CST 1999
    }
    ```

### 2.Comparable比较器

```java
public interface Comparable<T>{
    	/**
    	 * 实现对象的比较处理操作
    	 * @param o 要比较的对象
    	 * @return 当前数据比传入的对象小返回负数，如果大于返回整数，如果等于返回0
    	 */
    	public int compareTo(T o);
}
```

![](https://note.youdao.com/yws/api/personal/file/BB17B39E104E47949EB3C857100A4EEC?method=download&shareKey=57e77498fa84f7778278313b35b6a44d)

排序里面只需要一个<font color="lighblue">compareTo()方法</font>进行排序规则的定义，而后整个Java系统里面就可以为其实现排序处理了。

```java
public class JavaAPIDemmo {
	public static void main(String[] args) throws ParseException  {
	  Person data[] = new Person[] {
		  new Person("小强A", 80),
		  new Person("小强B", 100),
		  new Person("小强C", 50)};
		  Arrays.sort(data);
		  System.out.println(Arrays.toString(data));
	  }
}

class Person implements Comparable<Person>{
	private String name;
	private int age;
	public Person(String name,int age) {
		this.name = name;
		this.age = age;
	}
	@Override
	public int compareTo(Person per) {
		return this.age - per.age; //升序
    }
    @Override
	public String toString() {
		return "【Person类对象】姓名："+this.name+"、年龄："+this.age+"\n";
		}
}
/*
[【Person类对象】姓名：小强C、年龄：50
, 【Person类对象】姓名：小强A、年龄：80
, 【Person类对象】姓名：小强B、年龄：100
]
*/
```

### 3.Comparator比较器

作为Comparable的挽救措施，若在定义类时，没设置排序规则，在后期需要排序，但不能修改类的时候，可以使用Compatator比较器来进行排序。

```java
public class JavaAPIDemmo {
	public static void main(String[] args) throws ParseException  {
	  Person data[] = new Person[] {
		  new Person("小强A", 80),
		  new Person("小强B", 100),
		  new Person("小强C", 50)};
		  Arrays.sort(data,new PersonCompatator());
		  System.out.println(Arrays.toString(data));
	  }
}
	
class PersonCompatator implements  Comparator<Person>{
	@Override
	public int compare(Person p1, Person p2) {
		return p1.getAge()-p2.getAge();//升序
	}
}

class Person {
	private String name;
	private int age;
	public Person(String name,int age) {
		this.name = name;
		this.age = age;
	}
    public String getName() {
		return name;
	}
	public void setName(String name) {
		this.name = name;
	}
	public int getAge() {
		return age;
	}
	public void setAge(int age) {
		this.age = age;
	}
	@Override
	public String toString() {
		return "【Person类对象】姓名："+this.name+"、年龄："+this.age+"\n";
	}
}
```

**面试题：请解释Comparable与Comparator的区别？**

* java.lang.Comparable是在类定义的时候实现的父接口，主要用于定义排序规则，里面只有一个compareTo()的方法。
* java.util.Comparator是挽救的比较器操作，需要设置单独的比较器规则类实现排序，里面有compare()方法。

## 3.字节流和字符流

![](https://img-blog.csdn.net/20170105182342227?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvemhhb3lhbmp1bjY=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

对于服务器或者是客户端而言实质上传递的就是一种数据流的处理形式，而所谓的数据流指的就是字节数据。而对于流的处理形式在java.io包里面提供有两类支持：

* 字节处理流：OutputStream(输出字节流)、InputStream(输入字节流)
* 字符处理流：Writer(输出字符流)、Reader(输入字符流)

### 1.字节输出流：OutputStream

字节的数据是以byte类型为主实现的操作，在进行字节内容输出的时候可以使用OutputStream实现，这个类的基本定义：

```java
public abstract class OutputStream extends Object implements Closeable,Flushable
```

**OutputStream类定义的是一个公共的输出操作标准，而在这个标准里面一共定义有：**

|  No.   |                           方法名称                           |   类型   |         特性         |
| :----: | :----------------------------------------------------------: | :------: | :------------------: |
|   01   |     public abstract void write(int b)throws IOException      |   普通   |   输出单个字节数据   |
|   02   |        public void write(byte[] b)throws IOException         |   普通   |   输出一组字节数据   |
| **03** | **public void write(byte[] b,int off,int len)throws IOException** | **普通** | **输出部分字节数据** |

### 2.字节输入流：InputStream

[InputStream类](https://docs.oracle.com/en/java/javase/14/docs/api/java.base/java/io/InputStream.html)主要实现的就是字节数据读取，该类定义如下：

```java
public abstract class InputStream extends Object implements Closeable
```

**在InputStream类里面定义有如下几个核心方法：**

|  No.   |                           方法名称                           |   类型   |                       特性                       |
| :----: | :----------------------------------------------------------: | :------: | :----------------------------------------------: |
|   01   |        public abstract int read() throws IOException         |   普通   | 读取单个字节数据，如果现在已经读取到底了，返回-1 |
| **02** |      **public int read(byte[] b)  throws IOException**       | **普通** |               **读取一组字节数据**               |
|   03   | public int read(byte[] b, int off, int len) throws IOException |   普通   |         读取一组字节数据(只占数组的部分)         |

```java
public class JavaAPIDemo {
    public static void main(String[] args) throws Exception {
        File file = new File("E:"+File.separator+"hello"+File.separator+"mldn.txt");
        InputStream input = new FileInputStream(file);
        byte data [] = new byte[1024]; //开辟一个缓冲区读取数据
        int len = input.read(data); //读取数据，数据全部保存在字节数组之中，返回读取个数
        System.out.println("【"+new String(data,0,len)+"】");
        input.close();
    }
}
```

### 3.字符输出流：Writer

* 输出字符数组：public void write(char[] cbuf) throws IOException；
* 输出字符串：public void write(String str) throws [IOException]；

### 4.字符输入流：Reader

字符流读取的时候只能够按照数组的形式来实现处理操作。

* 接受数据：public int read(char[] cbuf) throws IOException;

### 5.字符流和字节流的区别

在使用OutputStream类输出的时候如果现在没有使用close()方法关闭输出流发现内容依然可以正常的输出。但如果在使用Writer输出的时候如果现在没有使用close()方法关闭输出流，那么这个时候内容将无法进行输出，因为Writer使用到了缓冲区。当使用close()方法的时候将会出现强制刷新缓冲区的情况，所以这个时候会将内容进行输出，如果没有关闭，那么将无法进行输出操作，所以此时如果在不关闭的情况下要想将全部的内容输出可以使用flush()方法强制清空。

**字节流在进行处理的时候并不会使用到缓冲区，而字符流会使用到缓冲区。另外那个缓冲区的字符流更加适合于中文数据的处理。**

结论：只要是纯文本数据优先使用字符流，除此之外都使用字节流。

### 6.常用的节点流

* 1.文件 ：**FileInputStream 、 FileOutputStrean 、FileReader 、FileWriter** 文件进行处理的节点流
* 2.数组 ：**ByteArrayInputStream、 ByteArrayOutputStream、 CharArrayReader 、CharArrayWriter** 对数组进行处理的节点流

### 7.常用的处理流

* 1.缓冲流：**BufferedInputStrean 、BufferedOutputStream、 BufferedReader、 BufferedWriter** 增加缓冲功能，避免频繁读写硬盘。
* 2.转换流：**InputStreamReader 、OutputStreamReader**实现字节流和字符流之间的转换。

------

## 4.集合

> [可参考CSDN收藏](https://blog.csdn.net/feiyanaffection/article/details/81394745)

![](https://note.youdao.com/yws/api/personal/file/1DAEDB91D81843268E64815C13A33E9A?method=download&shareKey=1e4056915c12d2ea885e8ec76722e191)

![](https://note.youdao.com/yws/api/personal/file/8FE2515E8565455A8C1692B539E01349?method=download&shareKey=de84cb3bd75b0585aac196bb701a915e)

### Collection接口

**Collection** 接口的接口 对象的集合（单列集合）  
├——-**List** 接口：有序，可重复  
│—————-├ **LinkedList** 接口实现类， <font color="lighblue">链表</font>， 插入删除， 没有同步， 线程不安全  
│—————-├ **ArrayList** 接口实现类， <font color="lighblue">数组</font>， 随机访问， 没有同步， 线程不安全  
│—————-└ **Vector** 接口实现类 <font color="lighblue">数组</font>， 同步， 线程安全  
│ ———————-└ **Stack** 是Vector类的实现类  
└——-**Set** 接口： 无序，不可重复  
├—————-└**HashSet** <font color="lighblue">数组+链表+红黑树</font>，使用hash表（数组）存储元素   
│————————└ **LinkedHashSet**  <font color="lighblue">链表+哈希表</font>，链表维护元素的插入次序，哈希表保证元素唯一  
└ —————-**TreeSet** 底层实现为<font color="lighblue">红黑树</font>，元素排好序 

**Map** 接口 键值对的集合 （双列集合）  
├———**Hashtable** <font color="lighblue">数组+链表+红黑树</font>，接口实现类， 同步， 线程安全  
├———**HashMap** <font color="lighblue">数组+链表+红黑树</font>，接口实现类 ，没有同步， 线程不安全  
│—————–├ **LinkedHashMap** <font color="lighblue">双向链表和哈希表</font>实现  
│—————–└ **WeakHashMap**  
├ ——–**TreeMap**  <font color="lighblue">红黑树</font>(自平衡排序二叉树)对所有的key进行排序  
└———**IdentifyHashMap**

[java.util.Collection](https://docs.oracle.com/en/java/javase/14/docs/api/java.base/java/util/Collection.html)是单值集合操作的最大的父接口，在该接口之中定义有所有的单值数据的处理操作，定义有如下的核心操作方法。

|  No.   |                           方法名称                           | 类型     |                  说明                  |
| :----: | :----------------------------------------------------------: | -------- | :------------------------------------: |
| **01** |                 **public boolean add(E e)**                  | **普通** |           **向集合保存数据**           |
|   02   | public boolean addAll([Collection](https://docs.oracle.com/en/java/javase/14/docs/api/java.base/java/util/Collection.html)<? extends [E](https://docs.oracle.com/en/java/javase/14/docs/api/java.base/java/util/Collection.html)> c) | 普通     |              追加一组数据              |
|   03   |                     public void clear()                      | 普通     | 清空集合，让根节点为空，同时执行GC处理 |
|   04   | public boolean contains([Object](https://docs.oracle.com/en/java/javase/14/docs/api/java.base/java/lang/Object.html) o) | 普通     | 查询数据是否存在，需要equals()方法支持 |
|   05   | public boolean remove([Object](https://docs.oracle.com/en/java/javase/14/docs/api/java.base/java/lang/Object.html) o) | 普通     |     数据删除，需要equals()方法支持     |
|   06   |                      public int size()                       | 普通     |              获取数据长度              |
|   07   | public [Object](https://docs.oracle.com/en/java/javase/14/docs/api/java.base/java/lang/Object.html)[] toArray() | 普通     |         将集合变为对象数组返回         |
| **08** | **public [Iterator](https://docs.oracle.com/en/java/javase/14/docs/api/java.base/java/util/Iterator.html)<E> iterator()** | **普通** |     **将集合变为Iterator接口返回**     |

在进行集合操作的时候有两个方法最为常用：【增加】add()、【输出】iterator()，在JDK1.5版本之后提供有了Iterable父接口。

### List接口

List是Collection子接口，其最大的特点是允许保存<font color="lighblue">有重复元素</font>数据，该接口的定义如下：

```java
public interface List<E> extends Collection<E>
```

List接口对Collection接口进行了方法扩充：

|  No.   |               方法名称                |   类型   |           说明           |
| :----: | :-----------------------------------: | :------: | :----------------------: |
| **01** |      **public E get(int index)**      | **普通** | **获取指定索引上的数据** |
|   02   |   public E set(int index,E element)   |   普通   |     修改指定索引数据     |
|   03   | public ListIterator<E> listIterator() |   普通   | 返回ListIterator接口对象 |

**（1）ArrayList**：底层数据结构是数组，查询快，增删慢，线程不安全，效率高，可以存储重复元素

**（2）LinkedList**： 底层数据结构是链表，查询慢，增删快，线程不安全，效率高，可以存储重复元素

**（3）Vector**：底层数据结构是数组，查询快，增删慢，线程安全，效率低，可以存储重复元素

##### ArrayList子类

在Java里面ArrayList类的定义如下：

```java
public class ArrayList<E>  extends AbstractList<E> implements List<E>,RandomAccess, Cloneable, Serializable
```

![](https://note.youdao.com/yws/api/personal/file/231BA7BF96AD47AF82509C5D2AD94912?method=download&shareKey=fb8169a67afecf636763f9f74ca6f319)

ArrayList的CRUD：

```java
List<String> list = new ArrayList<>();
        list.add("hello");//添加
        list.add("world");
        list.add("us");
        list.remove(0);//删除
        list.set(0,"aaa");//修改
        list.get(0);
        for (String o:list){
            System.out.println(o);
        }
```

集合排序：

```java
public class SortTests {
    public static void main(String[] args) {
        List<Integer> list1 = new ArrayList<>();
        list1.add(120);
        list1.add(200);
        list1.add(12);
        list1.add(888);
        System.out.println(list1);
        Collections.sort(list1);//排序
        System.out.println(list1);
        int max = Collections.max(list1);
        int min = Collections.min(list1);
        System.out.println("最大值："+max);
        System.out.println("最小值："+min);

        List<String> list2 = new ArrayList<>();
        list2.add("abc");
        list2.add("dc");
        list2.add("bade");
        System.out.println(list2);
        Collections.sort(list2);//根据ASCII排序
        System.out.println(list2);
    }
}
```

```java
@Autowried
private List<User> list;
public void testList(){
    list = new ArrayList<User>();
    Student user1 = new Student();
    user1.setUsername("bobo");
    user1.setAge(22);
    user1.setSex("男");
    list.add(user1);
    for(User u: list){
        System.out.println(u);
    }
}
```

通过此程序可以发现**List存储的特征**：

* 保存的顺序就是其存储顺序；
* List集合里面允许存在有重复数据。

上面这个例子默认使用了toString输出，在JDK1.8之后在父接口中定义有一个forEach()方法，定义如下：

* 输出支持：default void forEach(Consumer<? super T>action)；

利用**forEach()**方法输出(不是标准输出)

```java
public class JavaAPDemo {
    public static void main(String[] args) {
        //为List父接口
        List<String> all = new ArrayList<>();
        all.add("Hello");
        all.add("Hello");
        all.add("Hello");
        all.add("World");
        //增强型for循环遍历
        for (String o:all){
            System.out.print(o+"、");
        }
        //all.forEach((str)->{
        //    System.out.print(str+"、");
        //});
    }
}
```

| **JDK1.9之前** | **ArrayList默认的构造实际上就会默认开辟大小为10的数组。**    |
| -------------- | ------------------------------------------------------------ |
| **JDK1.9之后** | **ArrayList默认的构造只会使用默认的空数组，使用的时候才会开辟数组，默认数组大小为10.** |

| ArrayList构造(无参)：     | public ArrayList()                        |
| ------------------------- | ----------------------------------------- |
| **ArrayList构造(有参)：** | **public ArrayList(int initialCapacity)** |

Arraylist默认容量10，按1.5倍扩容。扩容的计算方式是向右位移一位，只有在当前值为偶数时，才除以2，奇数时是抹去最后一位，也就是减1，然后除以2。

##### LinkedList子类

**LinkedList** 底层数据结构是链表，查询慢，增删快，线程不安全，效率高，可以存储重复元素，**不会初始化数组大小**。

在Java里面LinkedList类定义如下：

```java
public class LinkedList<E> 
extends AbstractSequentialList<E> 
implements List<E>, Deque<E>, Cloneable, Serializable
```

LinkedList构造方法里并没有提供有像ArrayList那样的初始化大小的方法，而只是提供有无参构造处理：“public LinkedList()”。

**ArrayList和LinkedList有什么区别？**

* ArrayList是数组实现的集合操作，而LinkedList是链表实现的集合操作；
* 在使用List集合中的get()方法根据索引获取数据时，ArrayList的时间复杂度为“O(1)”、而LinkedList的时间复杂度为"O(n)"。
* ArrayList在使用的时候默认的初始化对象数组的大小长度为10，JDK8之后如果空间不足则会采用位运算，**右移**一位，其实就是除以2这个操作，长度变为15，之后依次类推，然后将数据copy到新数组中。如果保存大数据量的时候有可能造成垃圾的产生以及性能的下降，这个时候就可以使用LinkedList。

##### Vector子类

底层数据结构是数组，查询快，增删慢，线程安全，效率低，可以存储重复元素。

Vector是线程同步的，所以它也是线程安全的，而ArrayList是线程异步的，是不安全的。如果不考虑到线程的安全因素，一般用ArrayList效率比较高。

### Set集合

Set最大的特点就是**不允许保存重复元素**，其也就是Collection子接口。

Set集合的定义如下：

```java
public interface Set<E> extends Collection<E>
```

**Set无法使用List的get方法实现指定索引数据的获取。**

**具体实现唯一性的比较过程**

存储元素首先会使用hash()算法函数生成一个int类型hashCode散列值，然后已经的所存储的元素的hashCode值比较，如果hashCode不相等，则所存储的两个对象一定不相等，此时存储当前的新的hashCode值处的元素对象；如果hashCode相等，存储元素的对象还是不一定相等，此时会调用equals()方法判断两个对象的内容是否相等，如果内容相等，那么就是同一个对象，无需存储；如果比较的内容不相等，那么就是不同的对象，就该存储了，此时就要采用哈希的解决地址冲突算法，在当前hashCode值处类似一个新的链表， 在同一个hashCode值的后面存储存储不同的对象，这样就保证了元素的唯一性。

##### HashSet子类

**HashSet**底层数据结构采用<font color="lighblue">哈希表</font>实现，元素**无序且唯一**，线程不安全，效率高，可以存储null元素。

* 元素的唯一性是靠所存储元素类型是否重写hashCode()和equals()方法来保证的。
* HashSet采用哈希算法，底层用数组存储数据。默认初始化容量16，加载因子0.75。

```java
public class HashSet<E>
extends AbstractSet<E>
implements Set<E>, Cloneable, Serializable
```

```java
Set<Integer> set = new HashSet<>();
        set.add(100);//添加
        set.add(200);
        set.remove(100);//删除
        for (Integer b:set){
            System.out.println(b);
        }
```

##### TreeSet子类

**TreeSet**底层数据结构采用<font color="lighblue">红黑树</font>来实现，元素唯一且已经排好序；唯一性同样需要重写hashCode和equals()方法，二叉树结构保证了元素的**有序性**。重写Comparable接口里面的compareTo()方法来进行排序。

TreeSet类的定义：

```java
public class TreeSet<E>
extends AbstractSet<E>
implements NavigableSet<E>, Cloneable, Serializable
```

在使用自定义类对象进行比较处理的时候一定要将该类之中的所有属性都依次进行大小关系的匹配，否则如果某一个或某几个属性相同的时候它也会认为是重复数据，所以**TreeSet是利用Comparable接口来确认重复数据的**。

**重复元素的消除：**

在Java中重复元素的判断处理利用的就是**hashCode()与equals()**两个方法共同作用完成的，而只有在排序要求的情况下(TreeSet)才会利用Comparable接口来实现。

* 对象编码：public int hashCode();
* 对象比较：public boolean equals(Object obj);

在进行重复元素判断的时候首选利用hashCode()进行编码的匹配，如果该编码不存在则表示数据不存在，没有重复；如果该编码存在了，则进一步进行对象比较处理，如果发现重复了，此数据是不会被保存的。

##### LinkedHashSet子类

**LinkedHashSet**底层数据结构采用链表和哈希表共同实现，链表保证了元素的顺序与存储顺序一致，哈希表保证了元素的唯一性。线程不安全，效率高。

### 集合输出

对于集合操作而言，一共有四种输出形式：Iterator迭代输出(95%)，ListIterator双向迭代输出、Enumeration枚举输出(4.8)、foreach输出。

##### Iterator输出

* 获取Iterator输出对象：public Iterator<T> iterator();

**在Iterator接口里面定义有如下的方法：**

|  No  |           方法名称           | 类型 |      描述      |
| :--: | :--------------------------: | :--: | :------------: |
|  01  |   public boolean hasNext()   | 普通 | 判断是否有数据 |
|  02  |       public E next()        | 普通 |  取出当前数据  |
|  03  | public default void remove() | 普通 |      删除      |

```java
public class TestIter {
    public static void main(String[] args) {
        List<String> list = new ArrayList<>();
        for (int i=0;i<10;i++){
            list.add("list"+i);
        }
        //获取迭代器
        Iterator<String> iter = list.iterator();
        while (iter.hasNext()){
            System.out.println(iter.next());
        }
        System.out.println("-------for------");
        for (Iterator<String> iter2 = list.iterator();iter2.hasNext();){
            System.out.println(iter2.next());
        }
    }
}
```

**面试题**：**请解释Collection.remove()与Iterator.remove()的区别？**

* 在进行迭代输出的时候如果使用了Collection.remove()则会造成并发更新异常，导致程序删除出错，而此时只能够利用Iterator.remove()方法实现正常的删除处理。

##### ListIterator输出

使用Iterator进行迭代输出操作有一个特点：只允许由前向后实现输出，而如果进行<font color="lighblue">双向迭代</font>处理，那么就必须依靠Iterator的子接口ListIterator实现。

在ListIterator接口里面定义有如下的操作方法：
* 判断是否有前一个元素：public boolean hasPrevious()；
* 获取当前元素：public E previous()

```java
public class ListTests {
    public static void main(String[] args) {
        List<String> list = new ArrayList<>();
        for (int i=0;i<10;i++){
            list.add("list"+i);
        }
        //集合迭代器，向前遍历
        ListIterator listIterator = list.listIterator(list.size());
        System.out.println("----向前遍历----");
        while (listIterator.hasPrevious()){
            System.out.println(listIterator.previous());
        }
        System.out.println("----向后遍历----");
        while (listIterator.hasNext()){
            System.out.println(listIterator.next());
        }
    }
}
```

##### Enumeration输出

Enumeration只为Vector一个类服务。

##### foreach输出

```java
 List<String> list = new ArrayList<>();
        for (int i=0;i<10;i++){
            list.add("list"+i);
        }
        //获取迭代器
        for (String s : list) {
            System.out.println(s);
        }
```

### Map

在开发中，**Collection集合**保存数据的目的是为了**输出**，**Map集合**保存数据的目的是为**进行key的查找**。

| Map集合类     | key          | value        |
| ------------- | ------------ | ------------ |
| HashMap       | 允许为null   | 允许为null   |
| TreeMap       | 不允许为null | 允许为null   |
| ConcurrentMap | 不允许为null | 不允许为null |
| HashTable     | 不允许为null | 不允许为null |

Map接口是进行二元偶对象保存的最大父接口，该接口定义如下：

```java
public interface Map<K,V>
```

Map核心操作方法：

|  No.   |                 方法名称                  |   类型   |                             |
| :----: | :---------------------------------------: | :------: | --------------------------- |
| **01** |     **public V put(K key, V value)**      | **普通** | **向集合之中保存数据**      |
| **02** |       **public V get(Object key)**        | **普通** | **根据key查询数据**         |
| **03** | **public Set<Map.Entry<K,V>> entrySet()** | **普通** | **将Map集合转为Set集合**    |
|   04   |  public boolean containsKey(Object key)   |   普通   | 查询指定的key是否存在       |
|   05   |          public Set<K> keySet()           |   普通   | 将Map集合中的key转为Set集合 |
|   06   |        public V remove(Object key)        |   普通   | 根据key删除的指定的数据     |

##### HashMap子类

HashMap类的主要特点是<font color="lighblue">无序存储</font>。

```java
public class HashMap<K,V>
extends AbstractMap<K,V>
implements Map<K,V>, Cloneable, Serializable
```

通过HashMap实例化的Map接口可以针对与key或value**保存null的数据**，同时也可以发现即便保存数据的**key重复**，那么也不会出现错误，而是出现**内容的替换**。

**面试题：在进行HashMap的put()操作的时候，如何实现容量的扩充？**

* 在HashMap类里面提供有一个“DEFAULT_INITIAL_CAPACITY”常量，作为初始化的容量配置，作为<font color="lighblue">初始化大小为16个元素</font>。
* 当我们保存的容量超过了一个阈值(DEFAULT_LOAD_FACTOP=<font color="lighblue">0.75f</font>)，相当于容量*阈值=12，保存12个元素的时候容量会扩充。
* 在进行扩充的时候HashMap采用的是<font color="lighblue">成倍扩充</font>模式。

**HashMap是使用了哪些方法来有效解决哈希冲突？**

1. 使用 <font color="lighblue" style="font-weight:bold;">链地址法</font>（使用散列表）来链接拥有相同hash值的数据；

2. 使用 <font color="lighblue" style="font-weight:bold;">2次扰动函数</font>（hash函数）来降低哈希冲突的概率，使得数据分布更平均；

3. <font color="lighblue" style="font-weight:bold;">引入红黑树</font>进一步降低遍历的时间复杂度，使得遍历更快，提高查找效率；

链地址法的基本思想是：每个哈希表节点都有一个next指针，多个哈希表节点可以用next指针构成一个单向链表，被分配到同一个索引上的多个节点可以用这个单向链表连接起来。

**HashMap在JDK1.7和JDK1.8中有哪些不同**

![](https://note.youdao.com/yws/api/personal/file/6983209F21AD4039AA0FF43657A78188?method=download&shareKey=e631c6e5c753cd885ace1534c13173b6)

| 不同                     | JDK 1.7                                                      | JDK 1.8                                                      |
| :----------------------- | :----------------------------------------------------------- | :----------------------------------------------------------- |
| 存储结构                 | 数组 + 链表                                                  | 数组 + 链表 + 红黑树                                         |
| 初始化方式               | 单独函数：`inflateTable()`                                   | 直接集成到了扩容函数`resize()`中                             |
| hash值计算方式           | 扰动处理 = 9次扰动 = 4次位运算 + 5次异或运算                 | 扰动处理 = 2次扰动 = 1次位运算 + 1次异或运算                 |
| 存放数据的规则           | 无冲突时，存放数组；冲突时，存放链表                         | 无冲突时，存放数组；冲突 & 链表长度 <= 8：存放单链表；冲突 & 链表长度 > 8：树化并存放红黑树，当红黑树节点小于等于6时又会退化为链表 |
| 插入数据方式             | 头插法（先讲原位置的数据移到后1位，再插入数据到该位置）      | 尾插法（直接插入到链表尾部/红黑树）                          |
| 扩容后存储位置的计算方式 | 全部按照原来方法进行计算（即hashCode ->> 扰动函数 ->> (h&length-1)） | 按照扩容后的规律计算（即扩容后的位置=原位置 or 原位置 + 旧容量） |

**为什么JDK1.8要用尾插法？**

**使用头插**会改变链表的上的顺序，但是如果**使用尾插**，在扩容时会保持链表元素原本的顺序，就不会出现**链表成环**的问题了。

HashMap子类

```java
System.out.println("----------Map----------");
        Map<Double,String> map = new HashMap();
        map.put(1d,"aaa");//添加
        map.put(2d,"bbb");
        map.put(3d,"ccc");
        Object o = map.get(2d);//查询
        System.out.println(o);
        map.replace(2d,"world");//修改
        map.remove(1d);//删除
        for (Object f:map.entrySet()){
            System.out.println(f);
        }
```

##### LinkedHashMap子类

如果希望Map保存的顺序为增加的顺序，就使用LinkedHashMap，底层是链表实现的。数据量不要太大，不然时间复杂度太高。

##### Hashtable子类

Hashtable定义如下：

```java
public class Hashtable<K,V>
extends Dictionary<K,V>
implements Map<K,V>, Cloneable, Serializable
```

**注意：Hashtable中key与value都不允许为空**

**请解释HashMap与Hashtable的区别**

* HashMap中的方法都属于异步操作(非线程安全)，HashMap允许保存有null数据；
* Hashtable中的方法都属于同步方法(线程安全)，Hashtable不允许保存null，否则会出现NullPointException。

**HashTable 和 ConcurrentHashMap 区别**

* HashTable 使用的是 Synchronized 关键字修饰，ConcurrentHashMap 是JDK1.7 使用了锁分段技术来保证线程安全的。JDK1.8ConcurrentHashMap 取消了 Segment 分段锁，采用 CAS 和 synchronized 来保证并发安全。数据结构跟 HashMap1.8 的结构类似，数组+链表/红黑二叉树。 
* synchronized 只锁定当前链表或红黑二叉树的首节点，这样只要hash不冲突，就不会产生并发，效率又提升 N 倍。
* 一般现在不建议用 HashTable，即使在多线程环境下，现在也有同步的 ConcurrentHashMap 替代。

##### Map.Entry接口

所有的key和value的数据都被封装在Map.Entry接口中，定义如下：

static class Node<K,V> implements Map.Entry<K,V>{}

在这个内部接口总提供有两个重要的操作方法：

* 获取key：public K getKey();
* 获取value：public V getValue();

**Map.Entry的主要作用就是作为一个Key和Value的包装类型使用。**

##### 使用Iterator输出Map集合

![](https://note.youdao.com/yws/api/personal/file/1A72605F3AAE4706BC9A3C64CAE14BE0?method=download&shareKey=6727e5f03d90680af834666e61672c60)

**使用Iterator实现Map集合的输出步骤：**

* 利用Map接口中提供的entrySet()方法将Map集合转为Set集合；
* 利用Set接口中的iterator()方法将Set集合转为Iterator接口实例；
* 利用Iterator进行迭代输出获取每一组Map.Entry对象，随后通过getKey()与getValue()获取数据

```java
Map<Double,String> map = new HashMap();
     map.put(1d,"aaa");//添加
     map.put(2d,"bbb");
     map.put(3d,"ccc");
	for (Object f:map.entrySet()){
         System.out.println(f);
     }
```

## 5.JVM

### 1.跨平台

`.class`文件很明显是**不能直接运行**的，是交**由JVM来解析运行**

- JVM是运行在操作系统之上的，每个操作系统的指令是不同的，而**JDK是区分操作系统的**，只要你的本地系统装了JDK，这个JDK就是能够和当前系统兼容的。
- JDK 中包含了 JVM。当你安装 JDK 时，会同时获得 JVM 以及其他开发所需的工具（如编译器、调试器等）。
- JDK 是 Java 开发的完整工具包，而 JVM 是其中负责运行 Java 程序的核心组件。没有 JVM，Java 程序无法运行；而没有 JDK，就无法开发和编译 Java 程序。
- 而class字节码运行在JVM之上，所以**不用关心class字节码是在哪个操作系统编译的**，只要符合JVM规范，那么，这个字节码文件就是可运行的。
- 所以Java就做到了跨平台--->一次编译，到处运行！

### 2.Server模式和Client模式

- JVM Server模式与client模式启动，最主要的差别在于：-Server模式启动时，速度较慢，但是一旦运行起来后，性能将会有很大的提升。
- JVM工作在Server模式可以大大提高性能，但应用的启动会比client模式慢大概10%。当该参数不指定时，虚拟机启动检测主机是否为服务器，如果是，则以Server模式启动，否则以client模式启动。
- 当JVM用于启动GUI界面的交互应用时适合于使用client模式，当JVM用于运行服务器后台程序时建议用Server模式。

### 3.类的加载

Java类的加载是动态的，它并不会一次性将所有类全部加载后再运行，而是保证程序运行的基础类(像是基类)完全加载到jvm中，至于其他类，**则在需要的时候才加载**。这当然就是为了**节省内存开销**。

### 4.如何将类加载到jvm

class文件是通过**类的加载器**装载到jvm中的

**工作过程：**

- 1、当AppClassLoader加载一个class时，它首先不会自己去尝试加载这个类，而是把类加载请求委派给父类加载器ExtClassLoader去完成。
- 2、当ExtClassLoader加载一个class时，它首先也不会自己去尝试加载这个类，而是把类加载请求委派给BootStrapClassLoader去完成。
- 3、如果BootStrapClassLoader加载失败（例如在$JAVA_HOME/jre/lib里未查找到该class），会使用ExtClassLoader来尝试加载；
- 4、若ExtClassLoader也加载失败，则会使用AppClassLoader来加载
- 5、如果AppClassLoader也加载失败，则会报出异常ClassNotFoundException

其实这就是所谓的**双亲委派模型**。简单来说：如果一个类加载器收到了类加载的请求，它首先不会自己去尝试加载这个类，而是把**请求委托给父加载器去完成，依次向上**。

注意：这里存在的加载器之间的层级关系并不是以继承的方式存在的，而是以组合的方式处理的。

好处：**防止内存中出现多份同样的字节码**(安全性角度)，避免类的重复加载

**加载器加载到jvm中，接下来其实又分了好几个步骤**：加载-->连接(验证，准备，解析)-->初始化-->卸载

在类加载检查通过后，接下来虚拟机**将为新生对象分配内存**。

### 5.JVM的内存结构

- <font color="lighblue">类加载器</font>：如果 **JVM** 想要执行这个 **.class** 文件，我们需要将其装进一个 **类加载器** 中，它就像一个搬运工一样，会把所有的 **.class** 文件全部搬进JVM里面来。
- <font color="lighblue">方法区</font>：存储已**被虚拟机加载的类元数据信息**(元空间)比如类信息，常量，静态变量，编译后代码···等，类加载器将  `.class` 文件搬过来就是先丢到这一块上。
- <font color="lighblue">堆</font>：**存放对象实例**，比如对象实例，数组···等，它和方法区都同属于 **线程共享区域** 。也就是说它们都是 **线程不安全** 的，主要用于存放新创建的对象，几乎所有的对象实例都在这里分配内存。
- <font color="lighblue">栈</font>： 这是我们的代码运行空间。我们编写的每一个方法都会放到 **栈** 里面运行。栈主要用于存储局部变量、方法调用的参数、方法返回地址以及一些临时数据。
- <font color="lighblue">虚拟机栈</font>：虚拟机栈描述的是**Java方法执行的内存结构**：每个方法被执行的时候都会同时创建一个**栈帧**（Stack Frame）用于存储局部变量表、操作栈、动态链接、方法出口等信息
- <font color="lighblue">本地方法栈</font>：本地方法栈则是为虚拟机使用到的**Native方法服务**。
- <font color="lighblue">程序计数器</font>：当前线程所执行的字节码的**行号指示器**，主要就是完成一个加载工作，类似于一个指针一样的，指向下一行我们需要执行的代码。和栈一样，都是 **线程独享** 的，就是说每一个线程都会有自己对应的一块区域而不会存在并发和多线程的问题。

![](https://share.note.youdao.com/yws/api/personal/file/WEB5e09b206fc7d434de6245b91d58df7e8?method=download&shareKey=515eb9f9ede553909d79559072b5502a)

**小总结**

1. Java文件经过编译后变成 .class 字节码文件
2. 字节码文件通过类加载器被搬运到 JVM 虚拟机中
3. 虚拟机主要的5大块：方法区，堆都为线程共享区域，有线程安全问题，栈和本地方法栈和计数器都是独享区域，不存在线程安全问题，而 JVM 的调优主要就是围绕堆，栈两大块进行

**加载过程**

1、通过 `java.exe`运行 `Java3yTest.class`，随后被加载到JVM中，**元空间存储着类的信息**(包括类的名称、方法信息、字段信息..)。
2、然后JVM找到Java3yTest的主函数入口(main)，为main函数创建栈帧，开始执行main函数
3、main函数的第一条命令是 `Java3y java3y=new Java3y();`就是让JVM创建一个Java3y对象，但是这时候方法区中没有Java3y类的信息，所以JVM马上加载Java3y类，把Java3y类的类型信息放到方法区中(元空间)
4、加载完Java3y类之后，Java虚拟机做的第一件事情就是在堆区中为一个新的Java3y实例分配内存, 然后调用构造函数初始化Java3y实例，这个**Java3y实例持有着指向方法区的Java3y类的类型信息**（其中包含有方法表，java动态绑定的底层实现）的引用
5、当使用 `java3y.setName("Java3y");`的时候，JVM**根据java3y引用找到Java3y对象**，然后根据Java3y对象持有的引用定位到方法区中Java3y类的类型信息的**方法表**，获得 `setName()`函数的字节码的地址
6、为 `setName()`函数创建栈帧，开始运行 `setName()`函数

```java
String str = new String("hello");
```

**上面的语句中变量str放在栈上，用new创建出来的字符串对象放在堆上，而"hello"这个字面量是放在方法区的。**

### 6.问题排查

1. 通过<font color="lighblue">jps</font>命令查看Java进程「基础」信息（进程号、主类）。这个命令很常用的就是用来看当前服务器有多少Java进程在运行，它们的进程号和加载主类是啥

2. 通过<font color="lighblue">jstat</font>命令查看Java进程「统计类」相关的信息（类加载、编译相关信息统计，各个内存区域GC概况和统计）。这个命令很常用于看GC的情况

   > https://www.cnblogs.com/sxdcgaq8080/p/11089841.html

3. 通过<font color="lighblue">jinfo</font>命令来查看和调整Java进程的「运行参数」。

4. 通过<font color="lighblue">jmap</font>命令来查看Java进程的「内存信息」。这个命令很常用于把JVM内存信息dump到文件，然后再用MAT( Memory Analyzer tool 内存解析工具)把文件进行分析

5. 通过<font color="lighblue">jstack</font>命令来查看JVM「线程信息」。这个命令用常用语排查死锁相关的问题
6. 还有近期比较热门的<font color="lighblue">Arthas</font>（阿里开源的诊断工具），涵盖了上面很多命令的功能且自带图形化界面。

![](https://note.youdao.com/yws/api/personal/file/BC2C14EBF291430D9F01C36ABBE9334B?method=download&shareKey=73acd55a5a3b41b27606f17b626f55dc)

### 7. GC垃圾回收

> https://mp.weixin.qq.com/s/_AKQs-xXDHlk84HbwKUzOw

![](https://share.note.youdao.com/yws/api/personal/file/WEB18261526eb2ce55f0032bdcfaf7d40b1?method=download&shareKey=ccae099107b2badcb3764e8938964943)

JVM内存会划分为堆内存和非堆内存，堆内存中也会划分为**年轻代**和**老年代**，而非堆内存则为**方法区**(元空间)。

当我们new一个对象后，会先放到Eden划分出来的一块作为存储空间的内存，当Eden空间满了之后，会触发一个叫做**Minor GC**（就是一个发生在年轻代的GC）的操作，存活下来的对象移动到Survivor0区。Survivor0区满后触发 Minor GC，就会将存活对象移动到Survivor1区，此时还会把from和to两个指针交换，这样保证了一段时间内总有一个survivor区为空且to所指向的survivor区为空。经过多次的 Minor GC后仍然存活的对象（**这里的存活判断是15次，对应到虚拟机参数为 -XX:MaxTenuringThreshold 。为什么是15，因为HotSpot会在对象投中的标记字段里记录年龄，分配到的空间仅有4位，所以最多只能记录到15**）会移动到老年代。老年代是存储长期存活的对象的，占满时就会触发我们最常听说的**Full GC**，期间会停止所有线程等待GC的完成。所以对于响应要求高的应用应该尽量去减少发生Full GC从而避免响应超时的问题。

而且当老年区执行了full gc之后仍然无法进行对象保存的操作，就会产生OOM，这时候就是虚拟机中的堆内存不足，原因可能会是堆内存设置的大小过小，这个可以通过参数-Xms、-Xms来调整。也可能是代码中创建的对象大且多，而且它们一直在被引用从而长时间垃圾收集无法收集它们。

#### 对象分配规则

- 对象优先分配在Eden区，如果Eden区没有足够的空间时，虚拟机执行一次Minor GC。
- 大对象直接进入老年代（大对象是指需要大量连续内存空间的对象）。这样做的目的是避免在Eden区和两个Survivor区之间发生大量的内存拷贝（新生代采用复制算法收集内存）。
- 长期存活的对象进入老年代。虚拟机为每个对象定义了一个年龄计数器，如果对象经过了1次Minor GC那么对象会进入Survivor区，之后每经过一次Minor GC那么对象的年龄加1，直到达到阀值对象进入老年区。
- 动态判断对象的年龄。如果Survivor区中相同年龄的所有对象大小的总和大于Survivor空间的一半，年龄大于或等于该年龄的对象可以直接进入老年代。
- 空间分配担保。每次进行Minor GC时，JVM会计算Survivor区移至老年区的对象的平均大小，如果这个值大于老年区的剩余值大小则进行一次Full GC，如果小于检查HandlePromotionFailure设置，如果true则只进行Monitor GC,如果false则进行Full GC。

finalize()是Object类的一个方法、一个对象的finalize()方法只会被系统自动调用一次，经过finalize()方法逃脱死亡的对象，第二次不会再调用。不推荐使用

#### 垃圾回收的主要方法

**标记 - 清除算法**

- 核心步骤：
  1. **标记阶段**：从根对象（如栈引用、静态变量等）出发，遍历所有可达对象（仍在使用的对象），标记为 “存活”。
  2. **清除阶段**：遍历整个内存区域，回收所有未被标记的对象（垃圾），释放其占用的内存。
- **优点**：实现简单，无需移动对象。
- 缺点：
  - 会产生**内存碎片**（回收后空闲内存分散，可能无法分配大对象）。
  - 标记和清除过程都需要遍历整个内存区域，效率较低，尤其在内存对象较多时。
- **应用场景**：适用于对象存活率高、回收频率低的场景（如老年代），典型例子是 CMS（Concurrent Mark Sweep）收集器的老年代回收。

**标记 - 整理算法（Mark-Compact）**

* 核心步骤：
  1. 标记阶段：与 “标记 - 清除” 算法相同，标记所有存活对象。
  2. 整理阶段：将所有存活对象向内存一端移动，集中排列，然后直接清理掉边界外的所有内存。
* 优点：
  * 解决了 “标记 - 清除” 的内存碎片问题（存活对象连续排列）。
  * 内存利用率高（无需划分两个区域）。
* 缺点：
  * 整理阶段需要移动对象，会增加系统开销（尤其是大对象移动）。
  * 移动对象后需更新所有引用该对象的指针，复杂度较高。
  * 应用场景：适用于对象存活率高的区域（如老年代），例如 Parallel Old GC、G1 的 Mixed GC 等。

**复制算法（Copying）**

- 核心步骤：
  1. 将内存划分为两个大小相等的区域（如 From 区和 To 区）。
  2. 仅使用其中一个区域（From 区）分配内存，当该区域满时，将所有存活对象复制到另一个区域（To 区）。
  3. 清空 From 区，交换两个区域的角色（From 变 To，To 变 From），重复使用。
- 优点：
  - 无内存碎片（复制后存活对象连续排列）。
  - 回收效率高（只需处理存活对象，无需遍历全部内存）。
- 缺点：
  - 内存利用率低（仅能使用一半内存）。
  - 若对象存活率高，复制成本会很高（需要频繁复制大量存活对象）。
  - 应用场景：适用于对象存活率低、回收频繁的场景（如新生代），大多数垃圾收集器的新生代都采用这种算法（如 Serial GC、Parallel GC、G1 的 Young GC 等）。

**分代收集算法（Generational Collection）**

- 核心思想：根据对象的存活周期将内存划分为不同区域（代），针对不同代的特点采用不同的回收算法。
  - **新生代**：对象存活时间短、存活率低，采用**复制算法**（高效且无碎片）。
  - **老年代**：对象存活时间长、存活率高，采用**标记 - 清除**或**标记 - 整理**算法（避免频繁复制的开销）。
- **优点**：结合不同算法的优势，提高整体回收效率（新生代回收频繁但成本低，老年代回收少但成本高）。
- **应用场景**：几乎所有现代 JVM 垃圾收集器都采用分代思想（如 Serial GC、Parallel GC、G1 等）。

**总结**

* 标记 - 清除：简单但有碎片，适合低频率回收场景。
* 标记 - 整理：无碎片但需移动对象，适合高存活率场景。
* 复制：高效无碎片但内存利用率低，适合高回收频率、低存活率场景。
* 分代收集：不是独立算法，而是结合前三种算法的优化策略，根据对象生命周期动态选择回收方式，是实际应用中最广泛的方案。

#### 垃圾收集器种类

1.**Serial GC（串行收集器）**

* 特点：单线程执行垃圾收集，收集时会暂停所有用户线程（Stop-The-World，STW）。
* 工作原理：新生代采用复制算法，老年代采用标记 - 整理算法，均由单个线程完成。
* 适用场景：内存较小的客户端应用（如桌面程序），或对响应时间要求不高的场景。
* 启动参数：-XX:+UseSerialGC

2. **Parallel GC（并行收集器）**

- **特点**：多线程执行垃圾收集，提高吞吐量（单位时间内完成的任务量），但仍有 STW 停顿。
- **工作原理**：新生代使用复制算法（多线程并行收集），老年代使用标记 - 整理算法（多线程并行收集）。
- **适用场景**：注重吞吐量的服务器应用（如批处理任务），对响应时间要求不严格。
- **启动参数**：`-XX:+UseParallelGC`（默认新生代并行，老年代串行）；`-XX:+UseParallelOldGC`（老年代也并行，JDK 8 后默认开启）。

3. **CMS（Concurrent Mark Sweep，并发标记清除收集器）**

* 特点：尽可能减少 STW 时间，通过并发方式（与用户线程同时运行）完成大部分收集工作。
* 工作原理：
  * 新生代采用并行复制算法（STW）。
  * 老年代采用 “标记 - 清除” 算法，分为 4 个阶段：初始标记（STW）→ 并发标记 → 重新标记（STW）→ 并发清除
* 优点：响应时间短，适合对延迟敏感的应用（如 Web 服务）。
* 缺点：
  * 并发阶段占用 CPU 资源，可能影响吞吐量。
  * 采用 “标记 - 清除” 算法，会产生内存碎片。
  * 对大堆内存支持较差。
* 启动参数：-XX:+UseConcMarkSweepGC（JDK 9 后 deprecated，JDK 14 移除）。
4. **G1（Garbage-First，垃圾优先收集器）**
* 特点：兼顾吞吐量和响应时间，适用于大堆内存（如 4GB 以上）
* 工作原理：
  * 将堆内存划分为多个大小相等的独立区域（Region），不再区分新生代和老年代（但仍有逻辑划分）。
  *  跟踪每个区域的垃圾量，优先回收垃圾最多的区域（“垃圾优先”）。
  * 采用 “标记 - 整理” 算法，减少内存碎片。
  * 大部分阶段并发执行，仅必要阶段 STW。
* 适用场景：大型应用（如服务器、分布式系统），需要平衡延迟和吞吐量。
* 启动参数：-XX:+UseG1GC（JDK 9 后默认垃圾收集器）。

5. **ZGC（Z Garbage Collector）**
* 特点：超低延迟（STW 时间控制在毫秒级以内），支持超大堆内存（TB 级）。
* 工作原理：
    * 堆内存划分为 Region，支持动态扩容 / 缩容。
    * 采用 “标记 - 复制” 算法，结合颜色指针（Colored Pointers）和读屏障（Load Barrier）技术，实现并发标记和迁移。

* 优点：几乎不影响应用响应时间，适合对延迟极端敏感的场景（如金融交易、实时数据分析）。
* 启动参数：-XX:+UseZGC（JDK 11 引入，JDK 17 成为正式特性）。

6. **Shenandoah GC**

* 特点：低延迟，支持大堆内存，与 ZGC 定位类似。
* 工作原理：
  * 基于 “标记 - 整理” 算法，通过 “ Brooks 指针” 和读屏障实现并发压缩（无需 STW 迁移对象）。
* 优点：压缩阶段并发执行，STW 时间短（通常百微秒级）。
* 注意：Oracle JDK 不包含 Shenandoah，主要在 OpenJDK、RedHat 等发行版中提供。
* 启动参数：-XX:+UseShenandoahGC（JDK 12 引入，JDK 17 成为正式特性）。

**总结**

* 吞吐量优先：Parallel GC（适合批处理）。
* 低延迟优先：CMS（已淘汰）、G1、ZGC、Shenandoah（适合 Web 服务、实时应用）。
* 大堆内存：G1、ZGC、Shenandoah（支持 TB 级内存）。
* 客户端应用：Serial GC（简单、内存占用低）。

### 8.JVM调优参数

-Xmx3550m：设置 JVM 最大可用内存为 3550M。 

-Xms3550m：设置 JVM 初始内存为 3550m。注意：此值一般设置成和-Xmx 相同， 以避免每次垃圾回收完成后 JVM 重新分配内存。 

-Xmn2g：设置年轻代大小为 2G。整个 JVM 内存大小=年轻代大小 + 年老代大小 + 持久代大小。此值对系统性能影响较大，Sun 官方推荐配置为整个堆的 3/8。 

-Xss256k：设置每个线程的栈大小。JDK5.0 以后每个线程栈大小为 1M，以前每个线 程栈大小为 256K。根据应用的线程所需内存大小进行调整。在相同物理内存下，减小这个值能生成更多的线程。 

-XX:NewRatio=4:设置年轻代（包括 Eden 和两个 Survivor 区）与年老代的比值（除去持久代）。设置为 4，则年轻代与年老代所占比值为 1：4。（该值默认为 2） 

-XX:SurvivorRatio=4：设置年轻代中 Eden 区与 Survivor 区的大小比值。设置为 4，则两个 Survivor 区与一个 Eden 区的比值为 2:4。 

## 6.反射

> https://www.sczyh30.com/posts/Java/java-reflection-1/#comments

### 1.反射的概述

JAVA反射机制是在运行状态中，对于任意一个类，都能够知道这个类的所有属性和方法；对于任意一个对象，都能够调用它的任意一个方法和属性；这种动态获取的信息以及动态调用对象的方法的功能称为java语言的反射机制。

要想解剖一个类，必须先要获取到该类的字节码文件对象。而解剖使用的就是Class类中的方法，所以先要获取到每一个字节码文件对应的Class类型的对象。

**反射的原理**

java 虚拟机运行时内存有个叫方法区，主要作用是存储被装载的类的类型信息。每装载一个类的时候，Java 就会创建一个该类的 Class 对象实例。我们就可以通过这个实例，来访问这个类的信息。

### 2.反射的基本应用

**获得Class对象**

1. 使用 Class 类的 `forName` 静态方法:

   ```java
   public static Class<?> forName(String className)
   ​``` 
   比如在 JDBC 开发中常用此方法加载数据库驱动:
   ​```java
    Class.forName(driver);
   ```

2. 直接获取某一个对象的 class

   ```java
   Class<?> klass = int.class;
   Class<?> classInt = Integer.TYPE;
   ```

3. 调用某个对象的 `getClass()` 方法

   ```java
   StringBuilder str = new StringBuilder("123");
   Class<?> klass = str.getClass();
   ```

**判断是否为某个对象的实例**

我们用 `instanceof` 关键字来判断是否为某个类的实例。同时我们也可以借助反射中 Class 对象的 `isInstance()` 方法来判断是否为某个类的实例

**创建实例**

通过反射来生成对象主要有两种方式：

- 类加载器负责读取 Java 字节代码，并转换成 java.lang.Class类的一个实例。每个这样的实例用来表示一个 Java 类。通过此实例的 newInstance()方法就可以创建出该类的一个对象。

```java
Class<?> c = String.class;
Object str = c.newInstance();
```

- 先通过Class对象获取指定的Constructor对象，再调用Constructor对象的newInstance()方法来创建实例。这种方法可以用指定的构造器构造类的实例。

```java
//获取String所对应的Class对象
Class<?> c = String.class;
//获取String类带一个String参数的构造器
Constructor constructor = c.getConstructor(String.class);
//根据构造器创建实例
Object obj = constructor.newInstance("23333");
```

**获取方法**

获取某个Class对象的方法集合，主要有以下几个方法：

getDeclaredMethods方法返回类或接口声明的所有方法，包括公共、保护、默认（包）访问和私有方法，但不包括继承的方法。

```java
public Method[] getDeclaredMethods() throws SecurityException
```

getMethods方法返回某个类的所有公用（public）方法，包括其继承类的公用方法。

```java
public Method[] getMethods() throws SecurityException
```

getMethod方法返回一个特定的方法，其中第一个参数为方法名称，后面的参数为方法的参数对应Class的对象。

```java
public Method getMethod(String name, Class<?>... parameterTypes)
```

**获取构造器信息**

获取类构造器的用法与上述获取方法的用法类似。主要是通过Class类的getConstructor方法得到Constructor类的一个实例，而Constructor类有一个newInstance方法可以创建一个对象实例:

```java
public T newInstance(Object ... initargs)
```

**获取类的成员变量（字段）信息**

- getFiled：访问公有的成员变量
- getDeclaredField：所有已声明的成员变量，但不能得到其父类的成员变量

**调用方法**

当我们从类中获取了一个方法后，我们就可以用 `invoke()` 方法来调用这个方法。

```java
public Object invoke(Object obj, Object... args) throws IllegalAccessException,IllegalArgumentException,InvocationTargetException
```

```java
public class test1 {
    public static void main(String[] args) throws IllegalAccessException, InstantiationException, NoSuchMethodException, InvocationTargetException {
        Class<?> klass = methodClass.class;
        //创建methodClass的实例
        Object obj = klass.newInstance();
        //获取methodClass类的add方法
        Method method = klass.getMethod("add",int.class,int.class);
        //调用method对应的方法 => add(1,4)
        Object result = method.invoke(obj,1,4);
        System.out.println(result);
    }
}
class methodClass {
    public final int fuck = 3;
    public int add(int a,int b) {
        return a+b;
    }
    public int sub(int a,int b) {
        return a+b;
    }
}
```

**利用反射创建数组**

数组在Java里是比较特殊的一种类型，它可以赋值给一个Object Reference。

```java
public static void testArray() throws ClassNotFoundException {
        Class<?> cls = Class.forName("java.lang.String");
        Object array = Array.newInstance(cls,25);
        //往数组里添加内容
        Array.set(array,0,"hello");
        Array.set(array,1,"Java");
        Array.set(array,2,"fuck");
        Array.set(array,3,"Scala");
        Array.set(array,4,"Clojure");
        //获取某一项的内容
        System.out.println(Array.get(array,3));
    }
```

**反射的优点：**

- **可扩展性** ：应用程序可以利用全限定名创建可扩展对象的实例，来使用来自外部的用户自定义类。
- **类浏览器和可视化开发环境** ：一个类浏览器需要可以枚举类的成员。可视化开发环境（如 IDE）可以从利用反射中可用的类型信息中受益，以帮助程序员编写正确的代码。
- **调试器和测试工具** ： 调试器需要能够检查一个类里的私有成员。测试工具可以利用反射来自动地调用类里定义的可被发现的 API 定义，以确保一组测试中有较高的代码覆盖率。

**反射的缺点：**

尽管反射非常强大，但也不能滥用。如果一个功能可以不用反射完成，那么最好就不用。在我们使用反射技术时，下面几条内容应该牢记于心。

- **性能开销** ：反射涉及了动态类型的解析，所以 JVM 无法对这些代码进行优化。因此，反射操作的效率要比那些非反射操作低得多。我们应该避免在经常被执行的代码或对性能要求很高的程序中使用反射。
- **安全限制** ：使用反射技术要求程序必须在一个没有安全限制的环境中运行。如果一个程序必须在有安全限制的环境中运行，如 Applet，那么这就是个问题了。
- **内部暴露** ：由于反射允许代码执行一些在正常情况下不被允许的操作（比如访问私有的属性和方法），所以使用反射可能会导致意料之外的副作用，这可能导致代码功能失调并破坏可移植性。反射代码破坏了抽象性，因此当平台发生改变的时候，代码的行为就有可能也随着变化。

## 7.阻塞队列

当队列中没有数据的情况下，消费者端的所有线程都会被自动阻塞（挂起），直到有数据放入队列。

当队列中填满数据的情况下，生产者端的所有线程都会被自动阻塞（挂起），直到队列中有空的位置，线程被自动唤醒。

1. ArrayBlockingQueue ：由数组结构组成的有界阻塞队列。 公平、非公平

2. LinkedBlockingQueue ：由链表结构组成的有界阻塞队列。 两个独立锁提高并发

3. PriorityBlockingQueue ：支持优先级排序的无界阻塞队列。 compareTo 排序实现优先

4. DelayQueue：使用优先级队列实现的无界阻塞队列。 支持延时、缓存失效、定时任务 

5. SynchronousQueue：不存储元素的阻塞队列。 不存储数据、可用于传递数据

6. LinkedTransferQueue：由链表结构组成的无界阻塞队列。 

7. LinkedBlockingDeque：由链表结构组成的双向阻塞队列。

### **LinkedBlockingQueue**

单向链表结构：仅支持在队列尾部插入元素（如add、put、offer），在头部移除元素（如take、poll），遵循FIFO（先进先出）原则。适用于需要严格FIFO顺序的场景，如任务调度、生产者-消费者模型。

**常用的添加元素函数**

1. offer()：如果发现队列已满无法添加的话，会直接返回false。

   特点：非阻塞，适合快速失败（fail-fast）的场景。可通过重试逻辑实现类似阻塞的效果。

2. offer(E e, long timeout, TimeUnit unit)：在指定超时时间内尝试插入元素，若超时仍未插入，返回 false。

   特点：结合了阻塞和非阻塞的优点，避免无限等待。

3. put()：若向队尾添加元素的时候发现队列已经满了会发生阻塞一直等待空间，以加入元素。

   特点：线程安全，保证原子性。适用于必须插入成功的场景

4. add()：若超出了度列的长度会直接抛出异常：

   特点：行为与 offer() 相同（但异常处理更严格），通常推荐使用 `offer()` 替代。

**从队列中取出并移除头元素的方法**

1. poll()：若队列为空，返回null。

   特点：非阻塞，适合快速失败（fail-fast）的场景。可通过循环或重试逻辑实现类似阻塞的效果。

2. poll(timeout, unit)：在指定超时时间内尝试取出元素，若超时仍无元素，返回 null。

   特点：结合了阻塞和非阻塞的优点，避免无限等待。

3. take()：若队列为空，发生阻塞，等待有元素。

   特点：线程安全，保证原子性。适用于必须获取元素的场景。

4. remove()：若队列为空，抛出NoSuchElementException异常。

   特点：行为与 poll() 相同（但异常处理更严格）。通常推荐使用 poll() 替代。

**返回队列头列表**

1. element（）：返回队列头元素，如果为空则抛出异常NoSuchEleMentException
2. peek（）：返回队列头元素，如果为空则返回null

**注意**：使用take()函数，如果队列中没有数据，则线程wait释放CPU，而poll()则不会等待，直接返回null；同样，空间耗尽时offer()函数不会等待，直接返回false，而put()则会wait，因此如果你使用while(true)来获得队列元素，千万别用poll()，CPU会100%的。

```java
@Service
public class UploadDetectionServiceImpl implements UploadDetectionService, CommandLineRunner {

    @Override
    public void run(String... strings) throws Exception {
        List<ResourceSection> sections = resourceSectionRepository.findWaitStatus(false);
        if (sections.size()>0){
            for (int i=0;i<sections.size();i++){
                QUEUE.add(sections.get(i).getObjectId());
            }
        }
        start();
    }

    public void start() {
        new Thread(() -> {
            while (true) {
                try {
                    String lessonId = QUEUE.take();
                    if (dockerService.canCreateTask()) {
                        ResourceFile file = resourceFileRepository.findUrlById(lessonId);
                        if (file != null){
                            if (file.getUrl() != null) {
                                videoDetectionAsyncService.getCheckDetection(check, file.getUrl());
                            }
                        }
                    } else {
                        QUEUE.add(lessonId);
                        Thread.sleep(180000L);
                    }
                } catch (Exception e) {
                    log.error("VideoQueueServiceImpl error={}", e.getMessage());
                    e.printStackTrace();
                }
            }
        }, "uploadQueueThread").start();
    }

    @Override
    public void put(String lessonId) {
        if (QUEUE.contains(lessonId)){
            log.info(lessonId+"已存在待检测课程队列");
        }else{
            QUEUE.add(lessonId);
            log.info(QUEUE.toString()+"队列添加需检测课程id:"+lessonId);
        }
    }
}
```







