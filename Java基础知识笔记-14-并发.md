Java基础知识笔记-14-并发

读者可能已经很熟悉操作系统中的多任务(multitasking): 在同一刻运行多个程序的能力。 例如，在编辑或下载邮件的同时可以打印文件。今天，人们很可能有单台拥有多个CPU的计算机，但是，并发执行的进程数目并不是由CPU数目制约的。操作系统将CPU的时间片分配给每一个进程，给人并行处理的感觉。

多线程程序在较低的层次上扩展了多任务的概念：一个程序同时执行多个任务。通常，每一个任务称为一个线程(thread), 它是线程控制的简称。可以同时运行一个以上线程的程序称为多线程程序(multithreaded)。

那么，多进程与多线程有哪些区别呢？ 本质的区别在于每个进程拥有自己的一整套变量，而线程则共享数据。这听起来似乎有些风险，的确也是这样，在本章稍后将可以看到这个问题。然而，共享变量使线程之间的通信比进程之间的通信更有效、更容易。此外，在有些操作系统中，与进程相比较，线程更“ 轻量级”，创建、撤销一个线程比启动新进程的开销要小得多。

在实际应用中，多线程非常有用。例如，一个浏览器可以同时下载几幅图片。一个Web服务器需要同时处理几个并发的请求。图形用户界面(GUI)程序用一个独立的线程从宿主操作环境中收集用户界面的事件。本章将介绍如何为Java应用程序添加多线程能力。 

## 1 什么是线程
这里从察看一个没有使用多线程的程序开始。用户很难让它执行多个任务。在对其进行剖析之后，将展示让这个程序运行几个彼此独立的多个线程是很容易的。这个程序采用不断地移动位置的方式实现球跳动的动画效果，如果发现球碰到墙壁，将进行重绘。

当点击Start按钮时，程序将从屏幕的左上角弹出一个球，这个球便开始弹跳。Start按钮的处理程序将调用addBall方法。这个方法循环运行1000次move。每调用一次move, 球就会移动一点，当碰到墙壁时，球将调整方向，并重新绘制面板。 
```
Ball ball = new Ball();
panel.add(ball); 
for (int i = 1 ;i <= STEPS;i++) 
{ 
	ball.move(panel.getBounds());
	panel,paint(panel.getCraphics());
	Thread.sleep(DELAY);
}
```
调用Threadsleep不会创建一个新线程，sleep是Thread类的静态方法，用于暂停当前线程的活动。sleep方法可以抛出一个InterruptedException异常。稍后将讨论这个异常以及对它的处理。现在，只是在发生异常时简单地终止弹跳。如果运行这个程序，球就会自如地来回弹跳，但是，这个程序完全控制了整个应用程序。如果你在球完成1000次弹跳之前已经感到厌倦了，并点击Close按钮会发现球仍然还在弹跳。在球自己结束弹跳之前无法与程序进行交互。 

### 1.1 使用线程给其他任务提供机会
可以将移动球的代码放置在一个独立的线程中，运行这段代码可以提高弹跳球的响应能力。实际上，可以发起多个球，每个球都在自己的线程中运行。另外，AWT的事件分派线程(event dispatch thread)将一直地并行运行，以处理用户界面的事件。由于每个线程都有机会得以运行，所以在球弹跳期间，当用户点击Close按钮时，事件调度线程将有机会关注到这个事件，并处理“关闭”这一动作。

这里用球弹跳代码作为示例，让大家对并发处理有一个视觉印象。通常，人们总会提防长时间的计算。这个计算很可能是某个大框架的一个组成部分，例如，GUI或web框架。无论何时框架调用自身的方法都会很快地返回一个异常。如果需要执行一个比较耗时的任务，应当并发地运行任务。

#### 推荐使用实现Runable接口的方式来创建多线程

下面是在一个单独的线程中执行一个任务的简单过程：

- 1)将任务代码移到实现了Runnable接口的类的run方法中。这个接口非常简单，只有一个方法：
```
public interface Runnable 
{
	void run();
} 
```
由于Runnable是一个函数式接口，可以用lambda表达式建立一个实例：
```
Runnable r = () -> { taskcode }; 
```
- 2)由Runnable创建一个Thread对象：
```
Thread t = new Thread(r);
```
- 3)启动线程：`t.start()`;
要想将弹跳球代码放在一个独立的线程中，只需要实现一个类BallRunnable，然后，将动画代码放在run方法中，如同下面这段代码：
```
Runnable r = () -> {
	try 
	{ 
		for (int i = 1 ; i <=: STEPS; i++) 
		{
			ball.move(comp.getBounds()); 
			comp.repaint(); 
			Thread.sleep(DELAY); 
		}
	} catch (InterruptedException e) 
	{ } 
};
Thread t = new Thread(r);
t.start();
```
同样地，需要捕获sleep方法可能抛出的异常InterruptedException。下一节将讨论这个异常。在一般情况下，线程在中断时被终止。因此，当发生 InterruptedException异常时，run方法将结束执行。无论何时点击Start按钮，球会移入一个新线程，仅此而已！现在应该知道如何并行运行多个任务了。本章其余部分将阐述如何控制线程之间的交互。

> Runnable对象仅仅作为Thread对象的target，Runable实现类里包含的run()方法仅作为线程执行体。而实际的线程对象仍然是Thread实例，只是该Thread线程负责执行其target的run()方法。

#### 也可以通过构建一个Thread类的子类定义一个线程，如下所示

- 定义Thread类的子类 ，并重写该类的run()方法，该run()方法的方法体就体现了线程需要完成的任务。因此把run()方法称为线程执行体.。
- 创建Thread子类的实例，即创建了线程对象。
- 调用线程对象的start()方法来启动该线程。

```
class MyThread extends Thread
{
	public void run()
	{
		taskcode
	}
}
```
然后，构造一个子类的对象，并调用start方法。不过，这种方法已不再推荐。应该将要并行运行的任务与运行机制解耦合。如果有很多任务，要为每个任务创建一个独立的线程所付出的代价太大了。可以使用线程池来解决这个问题，有关内容请参看第14.9节。

> 警告：不要调用Thread类或Runnable对象的run方法。直接调用run方法，只会执行同一个线程中的任务，而不会启动新线程。应该调用Thread.start方法。这个方法将创建一个执行run方法的新线程。

## 2 中断线程
当线程的run方法执行方法体中最后一条语句后，并经由执行return语句返冋时，或者出现了在方法中没有捕获的异常时，线程将终止。在Java的早期版本中，还有一个stop方法，其他线程可以调用它终止线程。但是，这个方法现在已经被弃用了。

没有可以强制线程终止的方法。然而，interrupt方法可以用来请求终止线程。

当对一个线程调用interrupt方法时，线程的中断状态将被置位。这是每一个线程都具有的boolean标志。每个线程都应该不时地检査这个标志，以判断线程是否被中断。

要想弄清中断状态是否被置位，首先调用静态的Thread.currentThread方法获得当前线程，然后调用islnterrupted方法：
```
while (!Thread.currentThread().islnterrupted() && more work todo) 
{ 
	domorework 
}
```
但是，如果线程被阻塞，就无法检测中断状态。这是产生InterruptedException异常的地方。当在一个被阻塞的线程(调用sleep或wait)上调用interrupt方法时，阻塞调用将会被InterruptedException异常中断。（存在不能被中断的阻塞I/O调用，应该考虑选择可中断的调用。有关细节请参看卷2的第1章和第3章。）

没有任何语言方面的需求要求一个被中断的线程应该终止。中断一个线程不过是引起它的注意。被中断的线程可以决定如何响应中断。某些线程是如此重要以至于应该处理完异常后，继续执行，而不理会中断。但是，更普遍的情况是，线程将简单地将中断作为一个终止的请求。这种线程的run方法具有如下形式：
```
Runnable r = () -> { 
	try 
	{ 
		while (!Thread.currentThread().islnterrupted0 && more work todo) 
		{
			do morework
		}
	} 
	catch(InterruptedException e) 
	{ 
		// thread was interrupted during sleep or wait
	} 
	finally
	{
		cleanup,ifrequired
	} // exiting the run method terminates the thread 
};
```
如果在每次工作迭代之后都调用sleep方法（或者其他的可中断方法)，islnterrupted检测既没有必要也没有用处。如果在中断状态被置位时调用sleep方法，它不会休眠。相反，它将清除这一状态（！）并拋出InterruptedException。因此，如果你的循环调用sleep，不会检测中断状态。相反，要如下所示捕获 InterruptedException异常：
```
Runnable r = () -> { 
	try 
	{
		while (!Thread.currentThread().islnterrupter() && more work todo) 
		{
			do morework
			Tread.sleep(delay);
		}
	}
	catch(InterruptedException e) 
	{
		// thread was interrupted during sleep
	}
	finally
	{
		cleanup,ifrequired
	} // exiting the run method terminates the thread
};
```
> 注释：有两个非常类似的方法，interrupted和islnterrupted。Interrupted方法是一个静态方法，它检测当前的线程是否被中断。而且，调用interrupted方法会清除该线程的中断状态。另一方面，islnterrupted方法是一个实例方法，可用来检验是否有线程被中断。调用这个方法不会改变中断状态。

在很多发布的代码中会发现InterruptedException异常被抑制在很低的层次上，像这样：
```
void mySubTask() {
	...
	try { sleep(delay); } 
	catch (InterruptedException e) {} // Don't ignore!
	...
} 
```
不要这样做！如果不认为在catch子句中做这一处理有什么好处的话，仍然有两种合理的选择： 

- 在catch子句中调用Thread.currentThread().interrupt()来设置中断状态。于是，调用者可以对其进行检测。 
```
void mySubTask() {
	try { sleep(delay); } 
	catch (InterruptedException e) { Thread.currentThread().interrupt(); }
```
- 或者，更好的选择是，用throws InterruptedException标记你的方法，不采用try语句块捕获异常。于是，调用者（或者，最终的run方法）可以捕获这一异常。
```
void mySubTask() throws InterruptedException
{
	...
	sleep(delay);
	...
}
```

## 3 线程状态

线程可以有如下 6 种状态：
- New (新创建） 
- Runnable (可运行） 
- Blocked (被阻塞） 
- Waiting (等待） 
- Timed waiting (计时等待）
- Terminated (被终止） 

下一节对每一种状态进行解释。要确定一个线程的当前状态，可调用getState方法。 

### 3.1 新创建线程
当用new操作符创建一个新线程时，如newThread(r)，该线程还没有开始运行。这意味着它的状态是new。当一个线程处于新创建状态时，程序还没有开始运行线程中的代码。在线程运行之前还有一些基础工作要做。 

### 3.2 可运行线程
一旦调用start方法，线程处于runnable状态。一个可运行的线桿可能正在运行也可能没有运行，这取决于操作系统给线程提供运行的时间。（Java的规范说明没有将它作为一个单独状态。一个正在运行中的线程仍然处于可运行状态。）

一旦一个线程开始运行，它不必始终保持运行。事实上，运行中的线程被中断，目的是为了让其他线程获得运行机会。线程调度的细节依赖于操作系统提供的服务。抢占式调度系统给每一个可运行线程一个时间片来执行任务。当时间片用完，操作系统剥夺该线程的运行权，并给另一个线程运行机会（见图14-4 )。当选择下一个线程时，操作系统考虑线程的优先级---更多的内容见第4.1节。

现在所有的桌面以及服务器操作系统都使用抢占式调度。但是，像手机这样的小型设备可能使用协作式调度。在这样的设备中，一个线程只有在调用yield方法、或者被阻塞或等待时，线程才失去控制权。

在具有多个处理器的机器上，每一个处理器运行一个线程，可以有多个线程并行运行。当然，如果线程的数目多于处理器的数目，调度器依然采用时间片机制。 

记住，在任何给定时刻，二个可运行的线程可能正在运行也可能没有运行（这就是为什么将这个状态称为可运行而不是运行)。

### 3.3 被阻塞线程和等待线程
当线程处于被阻塞或等待状态时，它暂时不活动。它不运行任何代码且消耗最少的资源。直到线程调度器重新激活它。细节取决于它是怎样达到非活动状态的。

- 当一个线程试图获取一个内部的对象锁（而不是javiutiUoncurrent库中的锁)，而该锁被其他线程持有，则该线程进入阻塞状态（我们在14.5.3节讨论java.util.concurrent锁，在14.5.5节讨论内部对象锁)。当所有其他线程释放该锁，并且线程调度器允许本线程持有它的时候，该线程将变成非阻塞状态。

- 当线程等待另一个线程通知调度器一个条件时，它自己进入等待状态。我们在第14.5.4节来讨论条件。在调用Object.wait方法或Thread.join方法，或者是等待java.util.concurrent库中的Lock或Condition时，就会出现这种情况。实际上，被阻塞状态与等待状态是有很大不同的。 

- 有几个方法有一个超时参数。调用它们导致线程进入计时等待(timed waiting) 状态。这一状态将一直保持到超时期满或者接收到适当的通知。带有超时参数的方法有Thread.sleep和Object.wait、Thread.join、Lock.tryLock以及Condition.await的计时版。

图14-3展示了线程可以具有的状态以及从一个状态到另一个状态可能的转换。当一个线程被阻塞或等待时（或终止时)，另一个线程被调度为运行状态。当一个线程被重新激活（例如，因为超时期满或成功地获得了一个锁)，调度器检查它是否具有比当前运行线程更高的优先级。如果是这样，调度器从当前运行线程中挑选一个，剥夺其运行权，选择一个新的线程运行。

### 3.4 被终止的线程
线程因如下两个原因之一而被终止：

- 因为run方法正常退出而自然死亡。
- 因为一个没有捕获的异常终止了run方法而意外死亡。特别是，可以调用线程的stop方法杀死一个线程。该方法抛出ThreadDeath错误对象,由此杀死线程。但是，stop方法已过时，不要在自己的代码中调用这个方法。
- 直接调用该线程的stop()方法来结束线程----该方法容易导致死锁，通常不推荐使用。

> 注意：当主线程结束时，其他线程不受任何影响，可以调用线程对象的isAlive()方法，当线程处于就绪，运行，阻塞三种状态时，该方法将返回true，当线程处于新建，死亡两种状态时，该方法将返回false

不要试图对一个已近死亡的线程调用start()方法使它重新启动。会抛出异常

## 4 线程属性
### 4.1 线程优先级
在Java程序设计语言中，每一个线程有一个优先级。默认情况下，一个线程继承它的父线程的优先级。可以用setPriority方法提高或降低任何一个线程的优先级。可以将优先级设 置为在MIN_PRIORITY(在Thread类中定义为1)与MAX_PRIORITY(定义为10)之间的任何值。NORM_PRIORITY被定义为5。

每当线程调度器有机会选择新线程时，它首先选择具有较高优先级的线程。但是，线程优先级是高度依赖于系统的。当虚拟机依赖于宿主机平台的线程实现机制时，Java线程的优先级被映射到宿主机平台的优先级上，优先级个数也许更多，也许更少。

例如，Windows有7个优先级别。一些Java优先级将映射到相同的操作系统优先级。在Oracle为Linux提供的Java虚拟机中，线程的优先级被忽略一所有线程具有相同的优先级。初级程序员常常过度使用线程优先级。为优先级而烦恼是事出有因的。不要将程序构建为功能的正确性依赖于优先级。 

> 警告：如果确实要使用优先级，应该避免初学者常犯的一个错误。如果有几个高优先级的线程没有进入非活动状态，低优先级的线程可能永远也不能执行。每当调度器决定运行一个新线程时，首先会在具有高优先级的线程中进行选择，尽管这样会使低优先级的线程完全饿死。

```
void setPriority(int newPriority)  //设置线程的优先级。优先级必须在Thread.MIN_PRIORITY与Thread.MAX_PRIORITY之间。一般使用Thread.NORMJ»RIORITY优先级
static int MIN_PRIORITY  //线程的最小优先级。最小优先级的值为1。 
static int N0RM_PRI0RITY  //线程的默认优先级。默认优先级为5
static int MAX_PRIORITY  //线程的最高优先级。最高优先级的值为10
static void yield()  //导致当前执行线程处于让步状态。如果有其他的可运行线程具有至少与此线程同样高 的优先级，那么这些线程接下来会被调度。注意，这是一个静态方法
```
### 4.2 守护线程
有一种线程，他是在后台运行的，他的任务就是为其他的线程提供服务，这种线程被称为守护线程，比如JVM的垃圾回收线程。

可以通过调用

```
t.setDaemon(true);
```
将线程转换为守护线程(daemon thread)。这样一个线程没有什么神奇。守护线程的唯一用途是为其他线程提供服务。计时线程就是一个例子，它定时地发送“计时器嘀嗒”信号给其他 线程或清空过时的高速缓存项的线程。当只剩下守护线程时，虚拟机就退出了，由于如果只剩下守护线程，就没必要继续运行程序了。

守护线程有时会被初学者错误地使用， 他们不打算考虑关机(shutdown) 动作。但是，这是很危险的。守护线程应该永远不去访问固有资源，如文件、数据库，因为它会在任何时候甚至在一个操作的中间发生中断。

### 4.3 未捕获异常处理器
线程的run方法不能抛出任何受查异常，但是，非受査异常会导致线程终止。在这种情况下，线程就死亡了。 

但是，不需要任何catch子句来处理可以被传播的异常。相反，就在线程死亡之前，异常被传递到一个用于未捕获异常的处理器。

该处理器必须属于一个实现`Thread.UncaughtExceptionHandler`接口的类。这个接口只有一个方法。 
```
void uncaughtException(Thread t, Throwable e)
```
可以用`setUncaughtExceptionHandler`方法为任何线程安装一个处理器。也可以用Thread类的静态方法setDefaultUncaughtExceptionHandler为所有线程安装一个默认的处理器。替换处理器可以使用日志API发送未捕获异常的报告到日志文件。

如果不安装默认的处理器，默认的处理器为空。但是，如果不为独立的线程安装处理器，此时的处理器就是该线程的ThreadGroup对象。 

> 注释：线程组是一个可以统一管理的线程集合。默认情况下，创建的所有线程属于相同的线程组，但是，也可能会建立其他的组。现在引入了更好的特性用于线程集合的操作，所以建议不要在自己的程序中使用线程组。 

ThreadGroup类实现Thread.UncaughtExceptionHandler接口。它的uncaughtException方法做如下操作： 

- 1)如果该线程组有父线程组，那么父线程组的uncaughtException方法被调用。 
- 2)否则，如果Thread.getDefaultExceptionHandler方法返回一个非空的处理器，则调用该处理器。 
- 3)否则，如果Throwable 是ThreadDeath的一个实例，什么都不做。 
- 4)否则，线程的名字以及Throwable的栈轨迹被输出到System.err上。这是你在程序中肯定看到过许多次的栈轨迹。 
### 4.4线程睡眠

如果需要让当前正在执行的线程暂停一段时间，进入阻塞状态，则可以通过调用Thread类的静态sleep()方法来实现。sleep()方法有两种重载方式。

```
static void sleep(long millis)  //让当前正在执行的线程暂停millis毫秒，并进入阻塞状态
static void sleep(long millis,int nanos)  ////让当前正在执行的线程暂停millis毫秒+nanos毫秒，并进入阻塞状态
```

当当前线程调用sleep()方法进入阻塞状态后，在睡眠时间段内，该线程不会获得执行的机会，即使系统中没有其他可执行的线程，处于sleep()中的线程也不会执行，因此sleep()方法常用来暂停程序的执行。

## 5 同步
在大多数实际的多线程应用中，两个或两个以上的线程需要共享对同一数据的存取。如果两个线程存取相同的对象，并且每一个线程都调用了一个修改该对象状态的方法，将会发生什么呢？可以想象，线程彼此踩了对方的脚。根据各线程访问数据的次序，可能会产生讹误的对象。这样一个情况通常称为竞争条件(race condition)。
### 5.1 竞争条件的一个例子
为了避免多线程引起的对共享数据的说误，必须学习如何同步存取。在本节中，你会看到如果没有使用同步会发生什么。在下一节中，将会看到如何同步数据存取。在下面的测试程序中，模拟一个有若干账户的银行。随机地生成在这些账户之间转移钱款的交易。每一个账户有一个线程。每一笔交易中，会从线程所服务的账户中随机转移一定数目的钱款到另一个随机账户。模拟代码非常直观。我们有具有transfer方法的Bank类。该方法从一个账户转移一定数目的钱款到另一个账户（还没有考虑负的账户余额)。如下是Bank类的transfer方法的代码。 
```
public void transfer(int from, int to, double amount) 
// CAUTION: unsafe when called from multiple threads 
{
	System.out.print(Thread,currentThread()); 
	accounts[from] -= amount; 
	System.out.printf("%10.2f from %d to %d", amount, from, to);
	accounts[to] += amount;
	System.out.printf('Total Balance: %10.2fXn", getTotalBalance());
} 
```
这里是Runnable类的代码。它的run方法不断地从一个固定的银行账户取出钱款。在每一次迭代中，run方法随机选择一个目标账户和一个随机账户，调用bank对象的transfer方法，然后睡眠。
```
Runnable r = () -> { 
	try
	{
		while (true)
		{
			int toAccount = (int) (bank.size() * Math.random());
			double amount = MAX_AMOUNT * Math.random();
			bank.transfer(fromAccount, toAccount, amount);
			Thread.sleep((int) (DELAY * Math.random()));
		}
	}
	catch (InterruptedExeeption e)
	{
	}
};
```
当这个模拟程序运行时，不清楚在某一时刻某一银行账户中有多少钱。但是，知道所有账户的总金额应该保持不变，因为所做的一切不过是从一个账户转移钱款到另一个账户。在每一次交易的结尾，transfer方法重新计算总值并打印出来。本程序永远不会结束。只能按CTRL+C来终止这个程序。

下面是典型的输出: 
```
Thread[Thread-11,5,main] 588.48 from 11to 44 Total Balance: 100000.00
Thread[Thread-12,5,main] 976.11from 12 to 22 Total Balance: 100000.00
Thread[Thread-14,5,main] 521.51 from 14 to 22 Total Balance: 100000.00
Thread[Thread-13,5,main] 359.89 from 13 to 81Total Balance: 100000.00
...
Thread[Thread-36,5,main] 401.71from 36 to 73 Total Balance: 99291.06
Thread[Thread-35,5,main] 691.46 from 35 to 77 Total Balance: 99291.06
Thread[Thread-37,5,main] 78.64 from 37 to 3 Total Balance: 99291.06
Thread[Thread-34,5,main] 197.11from 34 to 69 Total Balance: 99291.06
Thread[Thread-36,5,main] 85.96 from 36 to 4 Total Balance: 99291.06 
Thread[Thread-4,5,main]Thread[Thread-33,5,main] 7.31 from 31to 32 Total Balance: 99979.24 
	627.50 from 4 to 5 Total Balance: 99979.24
```
正如前面所示，出现了错误。在最初的交易中，银行的余额保持在$100000, 这是正确的，因为共100个账户，每个账户$1000。但是，过一段时间，余额总量有轻微的变化。当运行这个程序的时候，会发现有时很快就出错了，有时很长的时间后余额发生混乱。这样的状态不会带来信任感，人们很可能不愿意将辛苦挣来的钱存到这个银行。程序清单14-5和程序清单14-6中的程序提供了完整的源代码。看看是否可以从代码中找出问题。下一节将解说其中奥秘。

### 5.2 竞争条件详解
上一节中运行了一个程序，其中有几个线程更新银行账户余额。一段时间之后，错误不知不觉地出现了，总额要么增加，要么变少。当两个线程试图同时更新同一个账户的时候，这个问题就出现了。假定两个线程同时执行指令`accounts[to] += amount`; 问题在于这不是原子操作。该指令可能被处理如下：

- 1)将accounts\[to]加载到寄存器。
- 2)增加amount。
- 3)将结果写回accounts\[to]。

现在，假定第1个线程执行步骤1和2, 然后，它被剥夺了运行权。假定第2个线程被唤醒并修改了accounts数组中的同一项。然后，第1个线程被唤醒并完成其第3步。这样，这一动作擦去了第二个线程所做的更新。于是，总金额不再正确。我们的测试程序检测到这一讹误。（当然，如果线程在运行这一测试时被中断，也有可能会出现失败警告！） 

出现这一讹误的可能性有多大呢？这里通过将打印语句和更新余额的语句交织在一起执行，增加了发生这种情况的机会。

如果删除打印语句，讹误的风险会降低一点，因为每个线程在再次睡眠之前所做的工作很少，调度器在计算过程中剥夺线程的运行权可能性很小。但是，讹误的风险并没有完全消失。如果在负载很重的机器上运行许多线程，那么，即使删除了打印语句，程序依然会出错。这种错误可能会几分钟、几小时或几天出现一次。坦白地说，对程序员而言，很少有比无规律出现错误更糟的事情了。

真正的问题是transfer方法的执行过程中可能会被中断。如果能够确保线程在失去控制之前方法运行完成，那么银行账户对象的状态永远不会出现讹误。

### 5.3 锁对象
有两种机制防止代码块受并发访问的干扰。Java语言提供一个synchronized关键字达 到这一目的，并且Java SE 5.0引入了ReentrantLock类。synchronized关键字自动提供一个锁以及相关的“条件”，对于大多数需要显式锁的情况，这是很便利的。但是，我们相信在读者分別阅读了锁和条件的内容之后，理解 synchronized关键字是很轻松的事情。java.util.concurrent框架为这些基础机制提供独立的类，在此以及第14.5.4节加以解释这个内容。读者理解了这些构建块之后，将讨论第14.5.5节。 

用ReentrantLock保护代码块的基本结构如下： 
```
myLock.lock(); // a ReentrantLock object 
try 
{
	critical section
}
finally
{
	myLock.unlock();// make sure the lock is unlocked even if an exception is thrown
} 
```
这一结构确保任何时刻只有一个线程进入临界区。一旦一个线程封锁了锁对象，其他任何线程都无法通过lock语句。当其他线程调用lock时，它们被阻塞，直到第一个线程释放锁对象。

> 警告：把解锁操作括在finally子句之内是至关重要的。如果在临界区的代码抛出异常，锁必须被释放。否则，其他线程将永远阻塞。 

> 注释：如果使用锁，就不能使用带资源的try语句。首先，解锁方法名不是close。不过，即使将它重命名，带资源的try语句也无法正常工作。它的首部希望声明一个新变量。但是如果使用一个锁，你可能想使用多个线程共享的那个变量（而不是新变量）。 

让我们使用一个锁来保护Bank类的transfer方法。
```
public class Bank
{
	private Lock bankLock = new ReentrantLock();// ReentrantLock implements the Lock interface
	public void transfer(int from, int to, int amount)
	{
		bankLock.lock();
		try
		{
			System.out.print(Thread.currentThread());
			accounts[from] -= amount;
			System.out.printf(" %10.2f from %A to %d", amount, from, to);
			accounts[to] += amount;
			System.out.printf(" Total Balance: %10.2f%n", getTotalBalance());
		}
		finally
		{
			banklock.unlock();
		}
	}
} 
```
假定一个线程调用transfer,在执行结束前被剥夺了运行权。假定第二个线程也调用transfer,由于第二个线程不能获得锁，将在调用lock方法时被阻塞。它必须等待第一个线程完成transfer方法的执行之后才能再度被激活。当第一个线程释放锁时，那么第二个线程才能开始运行(见图 14-5)。

尝试一下。添加加锁代码到transfer方法并且再次运行程序。你可以永远运行它，而银行的余额不会出现讹误。 

注意每一个Bank对象有自己的ReentrantLock对象。如果两个线程试图访问同一个Bank对象，那么锁以串行方式提供服务。但是，如果两个线程访问不同的Bank 对象，每一个线程得到不同的锁对象，两个线程都不会发生阻塞。本该如此，因为线程在操纵不同的Bank实例的时候，线程之间不会相互影响。

锁是可重入的，因为线程可以重复地获得已经持有的锁。锁保持一个持有计数(hold count)来跟踪对lock方法的嵌套调用。线程在每一次调用lock都要调用unlock来释放锁。由于这一特性，被一个锁保护的代码可以调用另一个使用相同的锁的方法。 

例如，transfer方法调用getTotalBalance方法，这也会封锁bankLock对象，此时bankLock对象的持有计数为2。当getTotalBalance方法退出的时候，持有计数变回1。当transfer方法退出的时候，持有计数变为0。线程释放锁。通常，可能想要保护需若干个操作来更新或检查共享对象的代码块。要确保这些操作完成后，另一个线程才能使用相同对象。

> 警告：要留心临界区中的代码，不要因为异常的抛出而跳出临界区。如果在临界区代码 结束之前抛出了异常，finally子句将释放锁，但会使对象可能处于一种受损状态。 

> java.util.concurrent.locks.Lock 5.0 
>
> ```
> void lock() 获取这个锁；如果锁同时被另一个线程拥有则发生阻塞。 
> void unlock() 释放这个锁。
> ```

> java.util.concurrent.locks.ReentrantLock 5.0
>
> ```
> ReentrantLock() 构建一个可以被用来保护临界区的可重入锁。
> ReentrantLock(boolean fair) 构建一个带有公平策略的锁。一个公平锁偏爱等待时间最长的线程。但是，这一公平的保证将大大降低性能。所以，默认情况下，锁没有被强制为公平的。
> ```
>
> 

> 警告： 听起来公平锁更合理一些，但是使用公平锁比使用常规锁要慢很多。 只有当你确实了解自己要做什么并且对于你要解决的问题有一个特定的理由必须使用公平锁的时候，才可以使用公平锁。即使使用公平锁，也无法确保线程调度器是公平的。如果线程调度 器选择忽略一个线程，而该线程为了这个锁已经等待了很长时间，那么就没有机会公平地处理这个锁了。

### 5.4 条件对象
通常，线程进入临界区，却发现在某一条件满足之后它才能执行。要使用一个条件对象来管理那些已经获得了一个锁但是却不能做有用工作的线程。在这一节里，我们介绍Java库中条件对象的实现。（由于历史的原因， 条件对象经常被称为条件变量（conditional variable)。 ）现在来细化银行的模拟程序。我们避免选择没有足够资金的账户作为转出账户。注意不能使用下面这样的代码：

```
 if (bank.getBalance(from) >= amount) 
 	bank.transfer(from, to, amount);
```

 当前线程完全有可能在成功地完成测试，且在调用transfer方法之前将被中断。

```
 if (bank.getBalance(from) >= amount)
 // thread night be deactivated at this point
 	bank.transfer(from, to, amount);
```

在线程再次运行前，账户余额可能已经低于提款金额。必须确保没有其他线程在本检査余额 与转账活动之间修改余额。通过使用锁来保护检査与转账动作来做到这一点： 

```
public void transfer(int from, int to,int amount) 
{ 
	bankLock.lock(); 
	try 
	{ 
		while (accounts[from] < amount) 
		{ 
			// wait
			...
		}
		// transfer funds
		...
	}
	finally 
	{ 
		bankLock.unlock();
	}
} 
```

现在，当账户中没有足够的余额时，应该做什么呢？等待直到另一个线程向账户中注入了资金。但是，这一线程刚刚获得了对bankLock的排它性访问，因此别的线程没有进行存款操作的机会。这就是为什么我们需要条件对象的原因。

一个锁对象可以有一个或多个相关的条件对象。你可以用newCondition方法获得一个条件对象。习惯上给每一个条件对象命名为可以反映它所表达的条件的名字。例如，在此设置一个条件对象来表达“余额充足”条件。 

```
class Bank 
{
	private Condition sufficientFunds;
	...
	public Bank()
	{
		...
		sufficientFunds = bankLock.newCondition();
	}
}
```

如果transfer方法发现余额不足，它调用

```
sufficientFunds.await();
```

当前线程现在被阻塞了，并放弃了锁。我们希望这样可以使得另一个线程可以进行增加账户余额的操作。

等待获得锁的线程和调用await方法的线程存在本质上的不同。一旦一个线程调用await方法，它进入该条件的等待集。当锁可用时，该线程不能马上解除阻塞。相反，它处于阻塞状态，直到另一个线程调用同一条件上的signalAll方法时为止。

 当另一个线程转账时，它应该调用

```
sufficientFunds.signalAll();
```

这一调用重新激活因为这一条件而等待的所有线程。当这些线程从等待集当中移出时，它们再次成为可运行的，调度器将再次激活它们。同时，它们将试图重新进入该对象。一旦锁成为可用的，它们中的某个将从await调用返回，获得该锁并从被阻塞的地方继续执行。此时，线程应该再次测试该条件。由于无法确保该条件被满足---signalAll方法仅仅是通知正在等待的线程：此时有可能已经满足条件，值得再次去检测该条件。

> 注释： 通常，对await的调用应该在如下形式的循环体中
>
> ```
> while (I(ok to proceed))
> 	condition.await(); 
> ```

至关重要的是最终需要某个其他线程调用signalAll方法。当一个线程调用await时，它没有办法重新激活自身。它寄希望于其他线程。如果没有其他线程来重新激活等待的线程，它就永远不再运行了。这将导致令人不快的死锁(deadlock) 现象。如果所有其他线程被阻塞，最后一个活动线程在解除其他线程的阻塞状态之前就调用await方法，那么它也被阻塞。没有任何线程可以解除其他线程的阻塞，那么该程序就挂起了。

应该何时调用signalAll呢？经验上讲，在对象的状态有利于等待线程的方向改变时调用signalAll。例如，当一个账户余额发生改变时，等待的线程会应该有机会检查余额。在例子中，当完成了转账时，调用signalAll方法。 

```
public void transfer(int from, int to, int amount) 
{
	bankLock.lock()；
	try
	{
		while (accounts[from] < amount)
			sufficientFunds.await()；
			// transfer funds sufficientFunds.signalAll()；
	}
	finally
	{
		bankLock.unlock();
	}
} 
```

注意调用signalAll不会立即激活一个等待线程。它仅仅解除等待线程的阻塞，以便这些线程可以在当前线程退出同步方法之后，通过竞争实现对对象的访问。 

另一个方法signal, 则是随机解除等待集中某个线程的阻塞状态。这比解除所有线程的 阻塞更加有效，但也存在危险。如果随机选择的线程发现自己仍然不能运行，那么它再次被阻塞。如果没有其他线程再次调用signal, 那么系统就死锁了。

> 警告：当一个线程拥有某个条件的锁时，它仅仅可以在该条件上调用await、signalAll或signal方法。

如果你运行程序清单14-7中的程序，会注意到没有出现任何错误。总余额永远是$100 000。

没有任何账户曾出现负的余额（但是，你还是需要按下CTRL+C键来终止程序）。你可能还注意到这个程序运行起来稍微有些慢---这是为同步机制中的簿记操作所付出的代价。

实际上，正确地使用条件是富有挑战性的。在开始实现自己的条件对象之前，应该考虑使用14.10节中描述的结构。 

```
package synch;
import java.util.*;
import java.util.concurrent.locks.*;

/**
* A bank with a number of bank accounts that uses locks for serializing access.
* ©version 1.30 2004-08-01
* ©author Cay Horstmann
*/

public class Bank
{
	private final double[] accounts;
	private Lock bankLock;
	private Condition sufficientFunds;

	/**
    * Constructs the bank. 
	* @param n the number of accounts 
	* @param initialBalance the initial balance for each account
	*/

	public Bank(int n, double initialBalance)
	{
		accounts = new double[n];
		Arrays.fill(accounts, initialBalance);
		bankLock = new ReentrantLock();
		sufficientFunds = bankLock.newCondition()；
		/** 
		* Transfers money from one account to another. 
		* @param from the account to transfer from
		* @param to the account to transfer to
		* @paran amount the amount to transfer
		*/
		public void transfer(int from, int to, double amount) throws InterruptedException
		{
			bankLock.lock()；
			try 
			{
				while (accounts[from] < amount)
					sufficientFunds.await()；
				System.out.print(Thread.currentThread());
				accounts[from] -= amount;
				System.out.printf(" %10.2f from %6 to %d", amount, from, to);
				accounts[to] += amount;
				System.out.printf("Total Balance: %10.2f%n", getTotalBalance());
				sufficientFunds.signalAll();
			}
			finally
			{
				bankLock.unlock();
			}
		}
		/**
		* Gets the sum of all account balances.
		* ©return the total balance 
		*/ 
		public double getTotalBalance()
		{
			bankLock.lock();
			try
			{
				double sum = 0;
				for (double a : accounts)
					sum += a;
				return sum;
			}
			finally
			{
				bankLock.unlock();
			}
		}
		/**
		* Gets the number of accounts in the bank.
		* ©return the number of accounts
		*/
		public int size()
		{
			return accounts.length;
		}
}
```

### 5.5 synchronized关键字

在前面一节中，介绍了如何使用Lock和Condition对象。在进一步深入之前，总结一下有关锁和条件的关键之处：

- 锁用来保护代码片段，任何时刻只能有一个线程执行被保护的代码。
- 锁可以管理试图进入被保护代码段的线程。
- 锁可以拥有一个或多个相关的条件对象。
- 每个条件对象管理那些已经进入被保护的代码段但还不能运行的线程。 

Lock和Condition接口为程序设计人员提供了高度的锁定控制。然而，大多数情况下，并不需要那样的控制，并且可以使用一种嵌入到Java语言内部的机制。从1.0版开始，Java中的每一个对象都有一个内部锁。如果一个方法用synchronized关键字声明，那么对象的锁将保护整个方法。也就是说，要调用该方法，线程必须获得内部的对象锁。 

换句话说，

```
public synchronized void method()
{
	method body
}
```

等价于

```
public void method()
{
	this.intrinsidock.lock();
	try
	{
		method body
	}
	finally
	{
		this.intrinsicLock.unlock();
	}
} 
```

例如，可以简单地声明Bank类的transfer方法为synchronized, 而不是使用一个显式的锁。内部对象锁只有一个相关条件。wait方法添加一个线程到等待集中，`notifyAll/notify`方法解除等待线程的阻塞状态。换句话说，调用`wait`或`notityAll`等价于 

```
intrinsicCondition.await();
intrinsicCondition.signalAll();
```

> 注释：`wait`、`notifyAll`以及`notify`方法是Object类的final方法。Condition方法必须被命名为`await`、`signalAll`和`signal`以便它们不会与那些方法发生冲突。 

例如，可以用Java实现Bank类如下：

```
class Bank
{
	private double[] accounts;
	public synchronized void transfer(int from，int to, int amount) throws InterruptedException
	{
		while (accounts[from] < amount)
			wait(); // wait on intrinsic object lock's single condition
		accounts[from] -= amount;
		accounts[to] += amount;
		notifyAll()；// notify all threads waiting on the condition
	}
	public synchronized double getTotalBalance() { ... }
}
```

可以看到，使用synchronized关键字来编写代码要简洁得多。当然，要理解这一代码，你必须了解每一个对象有一个内部锁， 并且该锁有一个内部条件。由锁来管理那些试图进入synchronized方法的线程，由条件来管理那些调用wait的线程。

> 提示：Synchronized方法是相对简单的。但是，初学者常常对条件感到困惑。在使用wait/notifyAll之前，应该考虑使用第14.10节描述的结构之一。 

将静态方法声明为synchronized也是合法的。如果调用这种方法，该方法获得相关的类对象的内部锁。例如，如果Bank类有一个静态同步的方法，那么当该方法被调用时，Bankxlass对象的锁被锁住。因此，没有其他线程可以调用同一个类的这个或任何其他的同步静态方法。

内部锁和条件存在一些局限。包括： 

- 不能中断一个正在试图获得锁的线程。
- 试图获得锁时不能设定超时。
- 每个锁仅有单一的条件， 可能是不够的。

在代码中应该使用哪一种？Lock和Condition对象还是同步方法？下面是一些建议：

- 最好既不使用Lock/Condition也不使用synchronized关键字。在许多情况下你可以使 用java.util.concurrent包中的一种机制，它会为你处理所有的加锁。例如，在14.6节，你会看到如何使用阻塞队列来同步完成一个共同任务的线程。还应当研究一下并行流，有关内容参见卷n第1章。
- 如果synchronized关键字适合你的程序，那么请尽量使用它，这样可以减少编写的代码数量，减少出错的几率。程序清单14-8给出了用同步方法实现的银行实例。
- 如果特别需要Lock/Condition结构提供的独有特性时，才使用Lock/Condition。

> 程序清单14-8 synch2/Bank.java

```
package synch2;
import java.util.*;
/** 
* A bank with a number of bank accounts that uses synchronization primitives. 
* ©version 1.30 2004-08-01 s * ©author Cay Horstmann 
*/
public class Bank
{
	private final doublet[] accounts;
	/**
	* Constructs the bank.
	* @parain n the number of accounts
	* @param initialBalance the initial balance for each account
	*/
	public Bank(int n, double initialBalance)
	{
		accounts = new double[n];
		Arrays.fill (accounts, initialBalance);
	}
	/** Transfers money from one account to another.
	* @param from the account to transfer from
	* @param to the account to transfer to
	* @param amount the amount to transfer
	*/
	public synchronized void transfer(int from, int to, double amount) throws InterruptedException
	{
		while (accounts[from] < amount)
			wait();
		System.out.print(Thread.currentThread());
		accounts[from] -= amount;
		System.out.printf(" %10.2f from %d to %d", amount, from, to);
		accounts[to] += amount;
		System.out.printf(" Total Balance: %10.2f%n", getTotalBalanceO);
		notifyAll();
	}
	/**
	* Gets the sum of all account balances.
	* return the total balance
	*/
	public synchronized double getTotalBalance()
	{
		double sum = 0;
		for (double a : accounts)
			sum += a;
		return sum;
	}
	/**
	* Gets the number of accounts in the bank.
	* ©return the number of accounts
	*/
	public int size()
	{
		return accounts.length;
	}
}
```

> java.lang.Object 1.0 

```
void notifyAll()  //解除那些在该对象上调用wait方法的线程的阻塞状态。该方法只能在同步方法或同步块内部调用。如果当前线程不是对象锁的持有者，该方法拋出一个IllegalMonitorStateException异常。

void notify()  //随机选择一个在该对象上调用wait方法的线程，解除其阻塞状态。该方法只能在一个同步方法或同步块中调用。如果当前线程不是对象锁的持有者，该方法抛出一个IllegalMonitorStateException异常。

void wait()  //导致线程进入等待状态直到它被通知。该方法只能在一个同步方法中调用。如果当前线程不是对象锁的持有者，该方法拋出一个IllegalMonitorStateException异常。

void wait(long millis)
void wait(long millis, int nanos)  //导致线程进入等待状态直到它被通知或者经过指定的时间。这些方法只能在一个同步方法中调用。如果当前线程不是对象锁的持有者该方法拋出一个IllegalMonitorStateException异常。
	参数	millis	毫秒数
		nanos	纳秒数，<1 000 000 
```

### 5.6 同步阻塞

正如刚刚讨论的，每一个Java对象有一个锁。线程可以通过调用同步方法获得锁。还有另一种机制可以获得锁，通过进入一个同步阻塞。当线程进入如下形式的阻塞：

```
synchronized (obj) // this is the syntax for a synchronized block 
{
	critical section
}
```

 于是它获得Obj的锁。有时会发现“特殊的”锁，例如：

```
public class Bank
{
	private doublet[] accounts;
	private Object lock = new Object();
	public void transfer(int from, int to, int amount)
	{
		synchronized (lock) // an ad-hoc lock
		{
			accounts[from] -= amount;
			accounts[to] += amount;
		}
		System.out.println(...)
	}
}
```

在此，`lock`对象被创建仅仅是用来使用每个Java对象持有的锁。 

有时程序员使用一个对象的锁来实现额外的原子操作，实际上称为客户端锁定(clientside locking)  例如，考虑Vector类，一个列表，它的方法是同步的。现在，假定在`Vector<Double>`中存储银行余额。这里有一个`transfer`方法的原始实现：

```
public void transfer(Vector<Double> accounts, int from, int to, int amount)// Error
{
	accounts.set(from, accounts.get(from)-amount);
	accounts.set(to, accounts.get(to)+ amount);
	System.out.println(...);
} 
```

Vector类的get和set方法是同步的，但是，这对于我们并没有什么帮助。在第一次对get的调用已经完成之后，一个线程完全可能在transfer方法中被剥夺运行权。于是，另一个线程可能在相同的存储位置存入不同的值。但是，我们可以截获这个锁： 

```
public void transfer(Vector<Double> accounts, int from, int to, int amount)
{
	synchronized(accounts)
	{
		accounts.set(from, accounts.get(from)- amount);
		accounts.set(to, accounts.get(to)+ amount);
	}
	System.out.println(...);
} 
```

这个方法可以工作，但是它完全依赖于这样一个事实，Vector类对自己的所有可修改方法都使用内部锁。然而，这是真的吗？Vector类的文档没有给出这样的承诺。不得不仔细研究源代码并希望将来的版本能介绍非同步的可修改方法。如你所见，客户端锁定是非常脆弱的，通常不推荐使用。

### 5.7 监视器概念

锁和条件是线程同步的强大工具，但是，严格地讲，它们不是面向对象的。多年来，研究人员努力寻找一种方法，可以在不需要程序员考虑如何加锁的情况下，就可以保证多线程的安全性。最成功的解决方案之一是监视器(monitor), 这一概念最早是由PerBrinchHansen和TonyHoare在20世纪70年代提出的。用Java的术语来讲，监视器具有如下特性： 

- 监视器是只包含私有域的类。
- 每个监视器类的对象有一个相关的锁。
- 使用该锁对所有的方法进行加锁。换句话说，如果客户端调用`obj.method()`, 那么obj对象的锁是在方法调用开始时自动获得，并且当方法返回时自动释放该锁。因为所有的域是私有的，这样的安排可以确保一个线程在对对象操作时， 没有其他线程能访问该域。 
- 该锁可以有任意多个相关条件

监视器的早期版本只有单一的条件，使用一种很优雅的句法。可以简单地调用await accounts[from] >= balance而不使用任何显式的条件变量。然而，研究表明盲目地重新测试条件是低效的。显式的条件变量解决了这一问题。每一个条件变量管理一个独立的线程集。 

Java设计者以不是很精确的方式采用了监视器概念，Java中的每一个对象有一个内部的锁和内部的条件。如果一个方法用synchronized关键字声明，那么，它表现的就像是一个监视器方法。通过调用`wait/notifyAll/notify`来访问条件变量。

 然而，在下述的3个方面Java对象不同于监视器，从而使得线程的安全性下降：

- 域不要求必须是 private。
- 方法不要求必须是 synchronized。
- 内部锁对客户是可用的。 

这种对安全性的轻视激怒了Per Brinch Hansen。他在一次对原始Java中的多线程的严厉评论中，写道：“这实在是令我震惊，在监视器和并发Pascal出现四分之一个世纪后，Java的这种不安全的并行机制被编程社区接受。这没有任何益处。” [Java’ s Insecure Parallelism, ACM SIGPLANNotices 34:38-45, April 1999.]

### 5.8 Volatile 域
有时，仅仅为了读写一个或两个实例域就使用同步，显得开销过大了。毕竟，什么地方能出错呢？遗憾的是，使用现代的处理器与编译器，出错的可能性很大。 

- 多处理器的计算机能够暂时在寄存器或本地内存缓冲区中保存内存中的值。结果是，运行在不同处理器上的线程可能在同一个内存位置取到不同的值。
- 编译器可以改变指令执行的顺序以使吞吐量最大化。这种顺序上的变化不会改变代码语义，但是编译器假定内存的值仅仅在代码中有显式的修改指令时才会改变。然而，内存的值可以被另一个线程改变！ 

如果你使用锁来保护可以被多个线程访问的代码， 那么可以不考虑这种问题。编译 器被要求通过在必要的时候刷新本地缓存来保持锁的效应，并且不能不正当地重新排序 指令。详细的解释见JSR 133的Java内存模型和线程规范（参看http://www.jcp.org/en/jsr/detail?id=133）该规范的大部分很复杂而且技术性强，但是文档中也包含了很多解释得很清晰的例子。在http://www-106.ibm.com/developerworks/java/library/j-jtp02244_html有Brian Goetz写的一个更易懂的概要介绍。 

> 注释：Brian Goetz给出了下述 “同步格言”：“如果向一个变量写入值，而这个变量接下来可能会被另一个线程读取，或者，从一个变量读值，而这个变量可能是之前被另一个线程写入的，此时必须使用同步”。 

volatile关键字为实例域的同步访问提供了一种免锁机制。如果声明一个域为volatile, 那么编译器和虚拟机就知道该域是可能被另一个线程并发更新的。

例如，假定一个对象有一个布尔标记done, 它的值被一个线程设置却被另一个线程査询，如同我们讨论过的那样，你可以使用锁：

```
private boolean done;
public synchronized boolean isDone(){
	return done;
}
public synchronized void setDone() {
	done = true;
}
```

或许使用内部锁不是个好主意。如果另一个线程已经对该对象加锁，isDone和setDone方法可能阻塞。如果注意到这个方面，一个线程可以为这一变量使用独立的Lock。但是，这也会带来许多麻烦。

在这种情况下，将域声明为volatile是合理的： 

```
private volatile boolean done;
public boolean isDone(){
	return done;
}
public void setDone(){
	done = true;
} 
```

> 警告：Volatile 变量不能提供原子性。例如，方法 
>
> ```
> public void flipDone()
> {
> 	done = !done;
> } // not atomic
> ```
>
>  不能确保翻转域中的值。不能保证读取、翻转和写入不被中断。 

### 5.9 final 变置
上一节已经了解到，除非使用锁或volatile修饰符，否则无法从多个线程安全地读取一个域。

还有一种情况可以安全地访问一个共享域，即这个域声明为final时。考虑以下声明：

```
final Map<String, Double〉accounts = new HashKap<>()；
```

其他线程会在构造函数完成构造之后才看到这个accounts变量。

如果不使用 final，就不能保证其他线程看到的是accounts更新后的值，它们可能都只是看到null, 而不是新构造的 HashMap。

当然，对这个映射表的操作并不是线程安全的。如果多个线程在读写这个映射表，仍然需要进行同步。

### 5.11 死锁
锁和条件不能解决多线程中的所有问题。考虑下面的情况：

```
账户 1: $200
账户 2: $300
线程 1: 从账户 1 转移 $300 到账户 2
线程 2: 从账户 2 转移 $400 到账户 1
```

如图14-6所示，线程1和线程2都被阻塞了。因为账户1以及账户2中的余额都不足以进行转账，两个线程都无法执行下去。

有可能会因为每一个线程要等待更多的钱款存入而导致所有线程都被阻塞。这样的状态称为死锁(deadlock)。

在这个程序里，死锁不会发生，原因很简单。每一次转账至多$1 000。因为有100个账户，而且所有账户的总金额是 $100 000, 在任意时刻，至少有一个账户的余额髙于$1 000。从该账户取钱的线程可以继续运行。

但是，如果修改run方法，把每次转账至多$1 000的限制去掉，死锁很快就会发生。试试看。将`NACCOUNTS`设为10。每次交易的金额上限设置为`2*INITIAL_BALANCE`, 然后运行该程序。程序将运行一段时间后就会挂起。

导致死锁的另一种途径是让第i个线程负责向第i个账户存钱，而不是从第i个账户取钱。 这样一来，有可能将所有的线程都集中到一个账户上，每一个线程都试图从这个账户中取出大于该账户余额的钱。试试看。在SynchBankTest程序中，转用TransferRunnable类的run方法。在调用transfer时，交换fromAccount和toAccount。运行该程序并查看它为什么会立即死锁。 

还有一种很容易导致死锁的情况： 在SynchBankTest程序中， 将signalAll方法转换为signal, 会发现该程序最终会挂起（将 NACCOUNTS设为10可以更快地看到结果）。signalAll通知所有等待增加资金的线程，与此不同的是signal方法仅仅对一个线程解锁。如果该线程不能继续运行，所有的线程可能都被阻塞。考虑下面这个会发生死锁的例子。

```
账户1 ：$1990 
所有其他账户：每一个 $990
线程 1: 从账户 1 转移 $995 到账户 2
所有其他线程： 从他们的账户转移 $995 到另一个账户
```

显然，除了线程 1, 所有的线程都被阻塞，因为他们的账户中没有足够的余额。

线程1继续执行，运行后出现如下状况:

```
账户 1: $995 账户 2: $1985 所有其他账户：每个 $990
```

然后，线程1调用signal。signal方法随机选择一个线程为它解锁。假定它选择了线程3。该线程被唤醒，发现在它的账户里没有足够的金额，它再次调用await。但是，线程1仍在运行，将随机地产生一个新的交易，例如，

```
线程1 ：从账户 1 转移 $997 到账户 2 
```

现在，线程1也调用await, 所有的线程都被阻塞。系统死锁。 问题的起因在于调用signal。它仅仅为一个线程解锁， 而且，它很可能选择一个不能继续运行的线程（在我们的例子中，线程2必须把钱从账户2中取出）遗憾的是，Java编程语言中没有任何东西可以避免或打破这种死锁现象。必须仔细设计程序，以确保不会出现死锁。 

### 5.12 线程局部变量

### 5.13 锁测试与超时

线程在调用lock方法来获得另一个线程所持有的锁的时候，很可能发生阻塞。应该更加谨慎地申请锁。tryLock方法试图申请一个锁，在成功获得锁后返回true, 否则，立即返回 false, 而且线程可以立即离开去做其他事情。 

```
if (myLock.tryLock())
{
	// now the thread owns the lock
	try {
		... 
	}
	finally {
		myLock.unlock();
	}
}
else
	// do something else
```

可以调用tryLock时，使用超时参数，像这样：

```
if (myLock.tryLock(100, TineUnit.MILLISECONDS)) ... 
```

TimeUnit是一 枚举类型，可以取的值包括`SECONDS`、`MILLISECONDS`,` MICROSECONDS`和`NANOSECONDS`。

lock方法不能被中断。如果一个线程在等待获得一个锁时被中断，中断线程在获得锁之前一直处于阻塞状态。如果出现死锁，那么，lock方法就无法终止。

然而，如果调用带有用超时参数的tryLock, 那么如果线程在等待期间被中断，将抛出InterruptedException异常。这是一个非常有用的特性，因为允许程序打破死锁。

也可以调用locklnterruptibly方法。它就相当于一个超时设为无限的tryLock方法。 

在等待一个条件时，也可以提供一个超时： 

```
myCondition.await(100, TineUniBILLISECONDS)) 
```

如果一个线程被另一个线程通过调用signalAU或signal激活，或者超时时限已达到，或者线程被中断，那么await方法将返回。

如果等待的线程被中断，await方法将抛出一个InterruptedException异常。在你希望出现这种情况时线程继续等待（可能不太合理)，可以使用awaitUninterruptibly方法代替 await。 

> java.util.concurrent.locks.Lock 5.0 

```
boolean tryLock() //尝试获得锁而没有发生阻塞；如果成功返回真。这个方法会抢夺可用的锁，即使该锁有公平加锁策略，即便其他线程已经等待很久也是如此。
boolean tryLock(long time, TimeUnit unit) //尝试获得锁，阻塞时间不会超过给定的值；如果成功返回 true。 
void lockInterruptibly() //获得锁，但是会不确定地发生阻塞。如果线程被中断，抛出一个InterruptedException异常。
```

> java.util.concurrent.locks.Condition 5.0

```
boolean await(long time, TimeUnit unit) //进入该条件的等待集，直到线程从等待集中移出或等待了指定的时间之后才解除阻塞。如果因为等待时间到了而返回就返回false, 否则返回true。
void awaitUninterruptibly() //进入该条件的等待集，直到线程从等待集移出才解除阻塞。如果线程被中断，该方法 不会抛出InterruptedException异常。
```

## 6  阻塞队列

现在，读者已经看到了形成Java并发程序设计基础的底层构建块。然而，对于实际编程来说，应该尽可能远离底层结构。使用由并发处理的专业人士实现的较高层次的结构要方便得多、要安全得多。

对于许多线程问题，可以通过使用一个或多个队列以优雅且安全的方式将其形式化。生产者线程向队列插入元素， 消费者线程则取出它们。使用队列，可以安全地从一个线程向另 一个线程传递数据。例如，考虑银行转账程序，转账线程将转账指令对象插入一个队列中，而不是直接访问银行对象。另一个线程从队列中取出指令执行转账。只有该线程可以访问该银行对象的内部。因此不需要同步。（当然，线程安全的队列类的实现者不能不考虑锁和条件，但是， 那是他们的问题而不是你的问题。

当试图向队列添加元素而队列已满，或是想从队列移出元素而队列为空的时候，阻塞队列(blocking queue)导致线程阻塞。在协调多个线程之间的合作时，阻塞队列是一个有用的工具。工作者线程可以周期性地将中间结果存储在阻塞队列中。其他的工作者线程移出中间结果并进一步加以修改。队列会自动地平衡负载。如果第一个线程集运行得比第二个慢， 第二个线程集在等待结果时会阻塞。如果第一个线程集运行得快，它将等待第二个队列集赶上 来。表14-1给出了阻塞队列的方法