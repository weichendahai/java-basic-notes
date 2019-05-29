Java基础知识笔记-9-图形程序设计

## 1 Swing概述
现在，Swing是不对等基于GUI工具箱的正式名字。它已是Java基础类库(Java Foundation Class, JFC)的一部分。完整的JFC十分庞大，其中包含的内容远远大于Swing GUI工具箱。
JFC特性不仅仅包含了Swing组件，而且还包含了一个可访问性API、一个2D API和一个可拖放API。

> 注释：Swing没有完全替代AWT,而是基于AWT架构之上。Swing仅仅提供了能力更加强大的用户界面组件。尤其在采用Swing编写的程序中，还需要使用基本的AWT处
理事件。从现在开始，Swing是指“被绘制的”用户界面类；AWT是指像事件处理这样的窗口工具箱的底层机制。

当然，在用户屏幕上显示基于Swing用户界面的元素要比显示AWT的基于对等体组件的速度慢一些。鉴于以往的经验，对于任何一台现代的计算机来说，微小的速度差别无妨大
碍。另外，由于下列几点无法抗拒的原因，人们选择Swing:
- Swing 拥有一个丰富、便捷的用户界面元素集合。
- Swing 对底层平台依赖的很少，因此与平台相关的bug很少。
- Swing 给予不同平台的用户一致的感觉。

不过，上面第三点存在着一个潜在的问题：如果在所有平台上用户界面元素看起来都一样，那么它们就有可能与本地控件不一样，而这些平台的用户对此可能并不熟悉。

Swing采用了一种很巧妙的方式来解决这个问题。在程序员编写Swing程序时，可以为程序指定专门的“观感”。

此外，Sun开发了一种称为Metal的独立于平台的观感。现在，市场上人们将它称为“Java 观感”。不过，绝大多数程序员还继续沿用Metal这个术语，在本书中也将这样称呼。

有些人批评Metal有点笨重，而在Java SE 5.0中看起来却焕然一新（参见图10-3)。现在，Metal外观支持多种主题，每一种主题的颜色和字体都有微小的变化。默认的主题叫做Oceano在Java SE 6中，Sun改进了对Windows和GTK本地观感的支持。Swing应用程序将会支持色彩主题的个性化设置，逼真地表现着动态按钮和变得十分时尚的滚动条。


## 2 创建框架
在Java中，顶层窗口（就是没有包含在其他窗口中的窗口）被称为框架(frame)。在AWT库中有一个称为Frame的类，用于描述顶层窗口。这个类的Swing版本名为JFrame,它扩展于Frame类。JFrame是极少数几个不绘制在画布上的Swing组件之一。因此，它的修饰部件（按钮、标题栏、图标等）由用户的窗口系统绘制， 而不是由Swing绘制。

> 警告：绝大多数Swing 组件类都以“J”开头，例如，JButton、JFrame等。在Java中有Button和Frame这样的类，但它们属于AWT组件。如果偶然地忘记书写“ J”，程序仍然可以进行编译和运行，但是将Swing和AWT组件混合在一起使用将会导致视觉和行为的不一致。

在本节中，将介绍有关Swing的JFrame的常用方法。程序清单10-1给出了一个在屏幕中显示一个空框架的简单程序
```java
程序清单10-1 simpleframe/SimpleFrameTest.java
package simpleFrame;
import java.awt.*;
import javax.swing.*;
* ©version 1.33 2015-05-12
* author Cay Horstmann

public class SimpleFrameTest
{
	public static void main(String[] args)
	{
		EventQueue.invokeLater(() ->
                        {
				SimpleFrame frame = new SimpleFrame() ;
				frame.setDefaultCloseOperationQFrame.EXIT_0N_CL0SE) ;
				frame.setVisible(true) ;
			})；
	}
}
class SimpleFrame extends ]Frame
{
	private static final int DEFAULT.WIDTH = 300;
	private static final int DEFAULT.HEIGHT = 200;
	public SimpleFrame()
	{
		setSize(DEFAULT_WIDTH , DEFAULTJEICHT) ;
	}
}
```
下面逐行地讲解一下这个程序。

Swing类位于javax.swing包中。包名javax表示这是一个Java扩展包，而不是核心包。出于历史原因Swing类被认为是一个扩展。不过从1.2版本开始，在每个Java SE实现中都包含它。

在默认情况下，框架的大小为0 x 0像素，这种框架没有什么实际意义。这里定义了一个子类SimpleFmme，它的构造器将框架大小设置为300 x 200像素。这是
SimpleFrame和JFrame之间唯一的差别。

在SimpleFrameTest类的main方法中，我们构造了一个SimpleFrame对象使它可见。

在每个Swing程序中，有两个技术问题需要强调。

首先，所有的Swing组件必须由事件分派线程(event dispatch thread)进行配置，线程将鼠标点击和按键控制转移到用户接口组件。下面的代码片断是事件分派线程中的执行代码：
```java
EventQueue.invokeLater(() ->
	{
		statements
	});
```
这一内容将在14章中详细讨论。现在，只需要简单地将其看作启动一个Swing程序的神奇代码即可。

接下来，定义一个用户关闭这个框架时的响应动作。对于这个程序而言，只让程序简单地退出即可。选择这个响应动作的语句是`frame.setDefaultCloseOperation(3Frame. EXIT_0N_CL0SE) ;`

在包含多个框架的程序中，不能在用户关闭其中的一个框架时就让程序退出。在默认情况下，用户关闭窗口时只是将框架隐藏起来，而程序并没有终止（在最后一个框架不可见之后，程序再终止，这样处理比较合适，而Swing却不是这样工作的)。

简单地构造框架是不会自动地显示出来的，框架起初是不可见的。这就给程序员了一个机会，可以在框架第一次显示之前往其中添加组件。为了显示框架，main方法需要调用框架的setVisible方法。

在初始化语句结束后，main方法退出。需要注意，退出main并没有终止程序，终止的只是主线程。事件分派线程保持程序处于激活状态，直到关闭框架或调用System.exit方法终止程序。

图10-5中显示的是运行程序清单10-1的结果，它只是一个很枯燥的顶层窗口。在这个图中看到的标题栏和外框装饰（比如，重置窗口大小的拐角）都是由操作系统绘制的，而不是Swing 库。在Windows、GTK 或Mac下运行同样的程序，会得到不同的框架装饰。Swing库负责绘制框架内的所有内容。在这个程序中，只用默认的背景色填充了框架。
> 注释：可以调用frame.setUndecorated(true)关闭所有框架装饰。

## 3 框架定位
超类中继承了许多用于处理框架大小和位置的方法其中最重要的有下面几个：

- setLocation和setBounds方法用于设置框架的位置。
- setlconlmage用于告诉窗口系统在标题栏、任务切换窗口等位置显示哪个图标。
- setTitle用于改变标题栏的文字。
- setResizable利用一个boolean值确定框架的大小是否允许用户改变。

图10-6给出了JFrame类的继承层次。
```
	Object
	   |
	   |
	Component
	   |
	   |
	Container
	   |
	   |
JComponent	Windows
    |		   |
    |		   |
JPanel		Frame
		   |
		   |
		JFrame

```
对Component类（是所有GUI对象的祖先）和Window类(是Frame类的超类）需要仔细地研究一下，从中找到缩放和改变框架的方法。例如，在Component类中的setLocation方法是重定位组件的一个方法。如果调用`setLocation(x, y)`则窗口将放置在左上角水平x 像素，垂直y像素的位置，坐标(0, 0)位于屏幕的左上角。

同样地，Component中的setBounds方法可以实现一步重定位组件(特别是JFrame)大小和位置的操作，例如：
```java
setBounds(x, y, width, height);
```
可以让窗口系统控制窗口的位置， 如果在显示窗口之前调用
```
setLocationByPlatform(true);
```
窗口系统会选用窗口的位置（而不是大小)，通常是距最后一个显示窗口很少偏移量的位置。

> 注释：对于框架来说，setLocation和setBounds中的坐标均相对于整个屏幕。在第12章中将会看到，在容器中包含的组件所指的坐标均相对于容器。

### 3.1 框架属性
组件类的很多方法是以获取/设置方法对形式出现的，例如，Frame类的下列方法：
```java
public String getTitle();
public void setTitle(String title);
```
这样的一个获取/设置方法对被称为一种属性。属性包含属性名和类型。将get或set之后的第一个字母改为小写字母就可以得到相应的属性名。例如，Frame类有一个名为title且类型为String的属性。

从概念上讲，title是框架的一个属性。当设置这个属性时，希望这个标题能够改变用户屏幕上的显示。当获取这个属性时，希望能够返回已经设置的属性值。

我们并不清楚（也不关心）Frame类是如何实现这个属性的。或许只是简单的利用对等框架存储标题。或许有一个实例域：
```java
private String title; // not required for property
```
如果类没有匹配的实例域，我们将不清楚（也不关心）如何实现获取和设置方法。或许只是读、写实例域， 或许还执行了很多其他的操作。例如，当标题发生变化时，通知给窗口系统。

针对get/set约定有一个例外：对于类型为boolean的属性，获取方法由is开头。例如，下面两个方法定义了locationByPlatform属性：
```java
public boolean islocationByPIatforn()
public void setLocationByPIatforra(boolean b)
```
## 4 在组件中显示信息
可以将消息字符串直接绘制在框架中，但这并不是一种好的编程习惯。在Java中，框架被设计为放置组件的容器，可以将菜单栏和其他的用户界面元素放置在其中。在通常情况下，应该在另一组件上绘制信息，并将这个组件添加到框架中。

JFrame的结构相当复杂。在图10-8中给出了JFrame的结构。可以看到，在JFrame中有四层面板。其中的根面板、层级面板和玻璃面板人们并不太关心；它们是用来组织菜单栏和内容窗格以及实现观感的。

Swing程序员最关心的是内容窗格（contentpane)。在设计框架的时候，要使用下列代码将所有的组件添加到内容窗格中：
```java
Container contentPane = frame.getContentPane() ;
Component c = ...;
contentPane.add(c) ;
```
在Java SE 1.4及以前的版本中，JFrame 类中的add 方法抛出了一个异常信息`“Do not use JFrame.add().Use JFrame.getContentPane().add instead”`。如今，JFrame.add方法不再显示

这些提示信息， 只是简单地调用内容窗格的add，因此，可以直接调用
```java
frame.add(c) ;
```
在这里， 打算将一个绘制消息的组件添加到框架中。绘制一个组件，需要定义一个扩展JAComponent的类，并覆盖其中的paintComponent方法。

paintComponent方法有一个Graphics类型的参数，这个参数保存着用于绘制图像和文本的设置，例如，设置的字体或当前的颜色。在Java中，所有的绘制都必须使用Graphics对象，其中包含了绘制图案、图像和文本的方法。
