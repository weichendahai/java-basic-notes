Java基础知识笔记-14-并发

读者可能已经很熟悉操作系统中的多任务(multitasking): 在同一刻运行多个程序的能力。 例如，在编辑或下载邮件的同时可以打印文件。今天，人们很可能有单台拥有多个CPU的计算机，但是，并发执行的进程数目并不是由CPU数目制约的。操作系统将CPU的时间片分配给每一个进程，给人并行处理的感觉。

多线程程序在较低的层次上扩展了多任务的概念：一个程序同时执行多个任务。通常，每一个任务称为一个线程(thread), 它是线程控制的简称。可以同时运行一个以上线程的程序称为多线程程序（multithreaded)。

那么，多进程与多线程有哪些区别呢？ 本质的区别在于每个进程拥有自己的一整套变量，而线程则共享数据。这听起来似乎有些风险，的确也是这样，在本章稍后将可以看到这个问题。然而，共享变量使线程之间的通信比进程之间的通信更有效、更容易。此外，在有些操作系统中，与进程相比较，线程更“ 轻量级”，创建、撤销一个线程比启动新进程的开销要小得多。

在实际应用中，多线程非常有用。例如，一个浏览器可以同时下载几幅图片。一个Web服务器需要同时处理几个并发的请求。图形用户界面(GUI)程序用一个独立的线程从宿主操作环境中收集用户界面的事件。本章将介绍如何为Java应用程序添加多线程能力。 

## 1 什么是线程
这里从察看一个没有使用多线程的程序开始。用户很难让它执行多个任务。在对其进行剖析之后，将展示让这个程序运行几个彼此独立的多个线程是很容易的。这个程序采用不断地移动位置的方式实现球跳动的动画效果，如果发现球碰到墙壁，将进行重绘

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
调用Threadsleep不会创建一个新线程，sleep是Thread类的静态方法，用于暂停当前线程的活动。sleep方法可以抛出一个IntermptedException异常。稍后将讨论这个异常以及对它的处理。现在，只是在发生异常时简单地终止弹跳。如果运行这个程序，球就会自如地来回弹跳，但是，这个程序完全控制了整个应用程序。如果你在球完成1000次弹跳之前已经感到厌倦了，并点击Close按钮会发现球仍然还在弹跳。在球自己结束弹跳之前无法与程序进行交互。 

### 1.1 使用线程给其他任务提供机会
可以将移动球的代码放置在一个独立的线程中，运行这段代码可以提高弹跳球的响应能力。实际上，可以发起多个球，每个球都在自己的线程中运行。另外，AWT 的事件分派线程(event dispatch thread)将一直地并行运行，以处理用户界面的事件。由于每个线程都有机会 得以运行，所以在球弹跳期间，当用户点击 Close按钮时，事件调度线程将有机会关注到这个事件，并处理“ 关闭” 这一动作。

这里用球弹跳代码作为示例，让大家对并发处理有一个视觉印象。通常，人们总会提防长时间的计算。这个计算很可能是某个大框架的一个组成部分，例如，GUI或web框架。无论何时框架调用自身的方法都会很快地返回一个异常。如果需要执行一个比较耗时的任务，应当并发地运行任务。

下面是在一个单独的线程中执行一个任务的简单过程：

- 1) 将任务代码移到实现了Runnable接口的类的run方法中。这个接口非常简单，只有一个方法：
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
- 2) 由Runnable创建一个Thread对象：
```
Thread t = new Thread(r);
```
- 3) 启动线程：t.start();
要想将弹跳球代码放在一个独立的线程中，只需要实现一个类BallRunnable，然后，将动画代码放在nm方法中，如同下面这段代码：\
```
Runnable r = () -> {
	try 
	{ 
		for (int i = 1 ; i <=: STEPS; i++) 
		{
			ball.move(comp.getBounds()); 
			comp.repaint(); 
			Thread.sieep(DELAY); 
		}
	} catch (InterruptedException e) 
	{ } 
};
Thread t = new Thread(r);
t.start();
```
同样地，需要捕获sleep方法可能抛出的异常InterruptedException。下一节将讨论这个异常。在一般情况下，线程在中断时被终止。因此，当发生 InterruptedException异常时，run方法将结束执行。无论何时点击Start按钮，球会移入一个新线程，仅此而已！现在应该知道如何并行运行多个任务了。本章其余部分将阐述如何控制线程之间的交互。

> 注释：也可以通过构建一个Thread类的子类定义一个线程，如下所示
```
class MyThread extends Thread
{
	public void run()
	{
		taskcode
	}
}
```
然后，构造一个子类的对象，并调用start方法。不过，这种方法已不再推荐。应该将要并行运行的任务与运行机制解耦合。如果有很多任务，要为每个任务创建一个独 立的线程所付出的代价太大了。可以使用线程池来解决这个问题，有关内容请参看第14.9节。

> 警告：不要调用Thread类或Runnable对象的run方法。直接调用run方法，只会执行同一个线程中的任务，而不会启动新线程。应该调用Thread.start方法。这个方法将创建一 个执行run方法的新线程。

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
但是，如果线程被阻塞，就无法检测中断状态。这是产生InterruptedException异常的地方。当在一个被阻塞的线程(调用sleep或wait)上调用interrupt方法时，阻塞调用将会被Interrupted Exception异常中断。（存在不能被中断的阻塞I/O调用，应该考虑选择可中断的调用。有关细节请参看卷2的第1章和第3章。）

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
如果在每次工作迭代之后都调用sleep方法（或者其他的可中断方法)，islnterrupted 检测既没有必要也没有用处。如果在中断状态被置位时调用sleep方法，它不会休眠。相反，它将清除这一状态（！）并拋出IntemiptedException。因此，如果你的循环调用sleep，不会检测中断状态。相反，要如下所示捕获 InterruptedException异常：
```
Runnable r = () -> { 
	try 
	{
		while (!Thread.currentThread().islnterrupted0 && more work todo) 
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
> 注释：有两个非常类似的方法，interrupted和islnterrupted。Interrupted方法是一个静态方法，它检测当前的线程是否被中断。而且，调用 interrupted方法会清除该线程的中断状态。另一方面，islnterrupted方法是一个实例方法，可用来检验是否有线程被中断。调用这个方法不会改变中断状态。

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
void mySubTaskO {
try { sleep(delay); } catch (InterruptedException e) { Thread.currentThreadO-interruptO; }
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
当用 new 操作符创建一个新线程时，如 newThread(r)，该线程还没有开始运行。这意味 着它的状态是 new。当一个线程处于新创建状态时，程序还没有开始运行线程中的代码。在线程运行之前还有一些基础工作要做。 

### 3.2 可运行线程
一旦调用start方法，线程处于runnable状态。一个可运行的线桿可能正在运行也可能没有运行，这取决于操作系统给线程提供运行的时间。（Java的规范说明没有将它作为一个单独状态。一个正在运行中的线程仍然处于可运行状态。）

一旦一个线程开始运行，它不必始终保持运行。事实上，运行中的线程被中断，目的是为了让其他线程获得运行机会。线程调度的细节依赖于操作系统提供的服务。抢占式调度系统给每一个可运行线程一个时间片来执行任务。当时间片用完，操作系统剥夺该线程的运行权，并给另一个线程运行机会（见图 14-4 )。当选择下一个线程时，操作系统考虑线程的优先级---更多的内容见第4.1节。

现在所有的桌面以及服务器操作系统都使用抢占式调度。但是，像手机这样的小型设备可能使用协作式调度。在这样的设备中，一个线程只有在调用yield方法、或者被阻塞或等待时，线程才失去控制权。

在具有多个处理器的机器上，每一个处理器运行一个线程，可以有多个线程并行运行。当然，如果线程的数目多于处理器的数目，调度器依然采用时间片机制。 

记住，在任何给定时刻，二个可运行的线程可能正在运行也可能没有运行（这就是为什么将这个状态称为可运行而不是运行)。

### 3.3 被阻塞线程和等待线程
当线程处于被阻塞或等待状态时，它暂时不活动。它不运行任何代码且消耗最少的资 源。直到线程调度器重新激活它。细节取决于它是怎样达到非活动状态的。

- 当一个线程试图获取一个内部的对象锁（而不是javiutiUoncurrent 库中的锁)，而该 锁被其他线程持有，则该线程进入阻塞状态（我们在14.5.3节讨论java.util.concurrent锁，在14.5.5节讨论内部对象锁)。当所有其他线程释放该锁，并且线程调度器允许本线程持有它的时候，该线程将变成非阻塞状态。

- 当线程等待另一个线程通知调度器一个条件时，它自己进入等待状态。我们在第14.5.4节来讨论条件。在调用Object.wait方法或Thread.join方法，或者是等待java.util.concurrent库中的Lock或Condition时，就会出现这种情况。实际上，被阻塞状态与等待状态是有很大不同的。 

- 有几个方法有一个超时参数。调用它们导致线程进入计时等待(timed waiting) 状态。这一状态将一直保持到超时期满或者接收到适当的通知。带有超时参数的方法有Thread.sleep和Object.wait、Thread.join、Lock,tryLock以及Condition.await的计时版。图14-3展示了线程可以具有的状态以及从一个状态到另一个状态可能的转换。当一个线程被阻塞或等待时（或终止时)，另一个线程被调度为运行状态。当一个线程被重新激活（例如，因为超时期满或成功地获得了一个锁)，调度器检查它是否具有比当前运行线程更高的优先级。如果是这样，调度器从当前运行线程中挑选一个，剥夺其运行权，选择一个新的线程运行。

### 3.4 被终止的线程
线程因如下两个原因之一而被终止：

- 因为run方法正常退出而自然死亡。
- 因为一个没有捕获的异常终止了run方法而意外死亡。特别是，可以调用线程的stop方法杀死一个线程。该方法抛出ThreadDeath错误对象,由此杀死线程。但是，stop方法已过时，不要在自己的代码中调用这个方法。

## 4 线程属性
### 4.1 线程优先级
在Java程序设计语言中，每一个线程有一个优先级。默认情况下，一个线程继承它的父线程的优先级。可以用setPriority方法提高或降低任何一个线程的优先级。可以将优先级设 置为在MIN_PRIORITY(在Thread类中定义为1)与MAX_PRIORITY(定义为10)之间的任何值。NORM_PRIORITY被定义为5。

每当线程调度器有机会选择新线程时，它首先选择具有较高优先级的线程。但是，线程优先级是高度依赖于系统的。当虚拟机依赖于宿主机平台的线程实现机制时，Java线程的优先级被映射到宿主机平台的优先级上，优先级个数也许更多，也许更少。

例如，Windows 有7个优先级别。一些Java优先级将映射到相同的操作系统优先级。在Oracle为Linux提供的Java虚拟机中，线程的优先级被忽略一所有线程具有相同的优先级。初级程序员常常过度使用线程优先级。为优先级而烦恼是事出有因的。不要将程序构建为功能的正确性依赖于优先级。 

> 警告：如果确实要使用优先级，应该避免初学者常犯的一个错误。如果有几个高优先级的线程没有进入非活动状态，低优先级的线程可能永远也不能执行。每当调度器决定运行一个新线程时，首先会在具有高优先级的线程中进行选择，尽管这样会使低优先级的线程完全饿死。

```
void setPriority(int newPriority)
设置线程的优先级。优先级必须在Thread.MIN_PRIORITY与Thread.MAX_PRIORITY之间。一般使用Thread.NORMJ»RIORITY优先级

static int MIN_PRIORITY 
线程的最小优先级。最小优先级的值为1。 

static int N0RM_PRI0RITY 线程的默认优先级。默认优先级为5

static int MAX_PRIORITY 线程的最高优先级。最高优先级的值为10

static void yield()
导致当前执行线程处于让步状态。如果有其他的可运行线程具有至少与此线程同样高 的优先级，那么这些线程接下来会被调度。注意，这是一个静态方法
```
### 4.2 守护线程
可以通过调用
```
t.setDaemon(true);
```
将线程转换为守护线程（daemon thread)。这样一个线程没有什么神奇。守护线程的唯一用途是为其他线程提供服务。计时线程就是一个例子，它定时地发送“计时器嘀嗒”信号给其他 线程或清空过时的高速缓存项的线程。当只剩下守护线程时，虚拟机就退出了，由于如果只剩下守护线程，就没必要继续运行程序了。

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
## 5 同步
在大多数实际的多线程应用中，两个或两个以上的线程需要共享对同一数据的存取。如果两个线程存取相同的对象，并且每一个线程都调用了一个修改该对象状态的方法，将会发生什么呢？可以想象，线程彼此踩了对方的脚。根据各线程访问数据的次序，可能会产生讹误的对象。这样一个情况通常称为竞争条件(race condition)。


