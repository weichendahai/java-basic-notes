Java基础知识笔记-12-事件处理

学习组件除了要熟悉组建的属性和功能外，一个更重要的方面是学习怎样处理组建上发生的界面事件，当用户在文本框中输入文本后按回车，单击按钮，在一个下拉式列表中选择一个条目进行一个条目等操作时，都发生界面事件，例如，用户单击一个确定或者取消的按钮，程序可能需要做出不同的处理。

## 1 事件处理模式基础

任何支持GUI的操作环境都要不断地监视按键或点击鼠标这样的事件。操作环境将 这些事件报告给正在运行的应用程序。如果有事件产生，每个应用程序将决定如何对它们做出响应。在Visual Basic这样的语言中， 事件与代码之间有着明确的对应关系。程序员对相关的特定事件编写代码，并将这些代码放置在过程中，通常人们将它们称为事件过程（event procedure) 例如，有一个名为HelpButton的Visual Basic按钮有一个与之关联的HelpButton_Click事件过程。这个过程中的代码将在点击按钮后执行。每个Visual Basic的GUI组件都响应一个固定的事件集，不可能改变Visual Basic组件响应的事件集。

另一方面，如果使用像原始的C这样的语言进行事件驱动的程序设计，那就需要编写代 码来不断地检查事件队列， 以便査询操作环境报告的内容（通常这些代码被放置在包含很多switch语句的循环体中） 。显然，这种方式编写的程序可读性很差，而且在有些情况下，编码的难度也非常大。它的好处在于响应的事件不受限制，而不像Visual Basic这样的语言，将 事件队列对程序员隐藏起来。

Java程序设计环境折中了Visual Basic与原始C的事件处理方式，因此，它既有着强大的功能，又具有一定的复杂性。在AWT所知的事件范围内，完全可以控制事件从事件源 (event source) 例如，按钮或滚动条，到事件监听器（event listener) 的传递过程，并将任何对 象指派给事件监听器。不过事实上，应该选择一个能够便于响应事件的对象。这种事件委托模型（event delegation model) 与Visual Basic那种预定义监听器模型比较起来更加灵活。

事件源有一些向其注册事件监听器的方法。当某个事件源产生事件时，事件源会向为事件注册的所有事件监听器对象发送一个通告。

像Java这样的面向对象语言，都将事件的相关信息封装在一个事件对象（event object) 中。在Java中，所有的事件对象都最终派生于java.util.EventObject类。当然，每个事件类型还有子类，例如，ActionEvent和WindowEvent。

不同的事件源可以产生不同类别的事件。例如，按钮可以发送一个ActionEvent对象,而窗口可以发送WindowEvent对象。

在学习处理事件时,必须很好地掌握事件源、监视器、处理事件的接口这三个概念。

综上所述，下面给出AWT事件处理机制的概要：
- 监听器对象是一个实现了特定监听器接口(listener interface)的类的实例。
- 事件源是一个能够注册监听器对象并发送事件对象的对象。
- 当事件发生时，事件源将事件对象传递给所有注册的监听器。
- 监听器对象将利用事件对象中的信息决定如何对事件做出响应。

##### 1.事件源
能够产生事件的对象都可以成为事件源，如文本框、按钮、下拉式列表等。也就是说，事件源必须是一个对象，而且这个对象必须是Java认为能够发生事件的对象。
##### 2.监视器
我们需要一个对象对事件源进行监视，以便对发生的事件作出处理。
事件源通过调用相应的方法将某个对象注册为自己的监视器。例如，对于文本框,这个方法是:
```
addActionListener(监视器);
```
对于注册了监视器的文本框，在文本框获得输入焦点后,如果用户按回车键，Java运行环境就自动用Actionven类创建一个对象，即发生了ActionEvent事件。也就是说，事件源注册监视器之后，相应的操作就会导致相应的事件地发生，并通知监视器，监视器就会出相应的处理。
##### 3.处理事件的接口

监视器负责处理事件源发生的事件。监视器是一个对象，为了处理事件源发生的事件，监视器这个对象会自动调用一个方法来处理事件。那么监视器去调用哪个方法呢？我们我知道，对象可以调用创建它的那个类中的方法，那么它到底调用该类中的哪个方法呢？Java规定为了让监视器这个对象能对事件源发生的事件进行处理，创建该监视器对象的以实现相应的接口，即必须在类体中重写接口中的所有方法，那么当事件源发生事，监视器就自动调用被类重写的某个接口方法。事件处理模式如图11.6所示。

下面是监听器的一个示例：

```java
ActionListener listener = ...
JButton button = new JButton("0K");
button.addActionListener(listener);
```
现在，只要按钮产生了一个“动作事件”，listener对象就会得到通告。对于按钮来说，正像我们所想到的，动作事件就是点击按钮。

为了实现ActionListener接口，监听器类必须有一个被称为actionPerformed的方法，该方法接收一个ActionEvent对象参数。
```java
class MyListener implements ActionListener {
	public void actionPerforied(ActionEvent event) {
		// reaction to button click goes here
		...
	}
}
```
只要用户点击按钮，JButton对象就会创建一个ActionEvent对象，然后调用listener,action Performed (event)传递事件对象。可以将多个监听器对象添加到一个像按钮这样的事件源中。这样一来，只要用户点击按钮，按钮就会调用所有监听器的actionPerformed方法。

如下图所示：
```
graph LR
A[事件源.addXXXListener]-->|发生|B[XXX事件]
B[XXX事件]-->|通知|A
A-->|监视器回调接口方法|D[class A implements XXXListener 接口方法]
E[类A负责创建监视器,A必须实现XXXListener接口]---A[事件源.addXXXListener]
```

## 2 ActionEvent事件
##### 1.ActionEvent事件源
文本框、按钮、菜单项、密码框和单选按钮都可以触发ActionEvent事件，即都可以成为ActionEvent事件的事件源。例如，对于注册了监视器的文本框，在文本框获得输入焦点后，如果用户按回车键，Java运行环境就自动用ActionEvent类创建一个对象，即触发ActionEvent事件;对于注册了监视器的按钮，如果用户单击按钮，就会触发ActionEvent事件;对于注册了监视器的菜单项，如果用户选中该菜单项，就会触发ActionEvent事件;如果用户选择了某个单选按钮，就会触发ActionEvent事件。
##### 2.注册监视器
能触发ActionEvent事件的组件使用
```java
addActionListener(ActionListener listen);
```
将实现ActionListener接口的类的实例注册为事件源的监视器。
##### 3.ActionListener接口
ActionListener接口在java.awt.event包中，该接口中只有一个方法:
```java
public void actionPerformed(ActionEvente);
```
事件源触发ActionEvent事件后，监视器将发现触发的ActionEvent事件，然后调用接口中的方法:
```java
actionPerformed(ActionEvent e);
```
对发生的事件作出处理。当监视器调用actionPerformed(ActionEvent e)方法时，ActionEvent类事先创建的事件对象就会传递给该方法的参数C。
##### 4.ActionEvent类中的方法
ActionEvent类有如下常用的方法。
```java
public ObeceSurce();//该方法是从Enentobiect继承的方法，ActionEvent事件对象调用该方法可以获取发生ActionEvent事件的事件源对象的引用，即BetSource()方法将事件源上转型为Object对象，并返回这个上转型对象的引用，
public String getActionCommand();//ActionEvent对象调用该方法可以获取发生ActionEvent事件时，和该事件相关的一个命令字符串，对于文本框，当发生ActionEvent事件时，文本框中的文本字符串就是和该事件相关的一个命令字符串.
```
### 实例1
例11.5处理文本框上触发的ActionEvent事件。在文本框text中输入字符串回车，监视器负责计算字符串的长度，并在命令行窗口显示字符串的长度。例11.5程序运行效果如图11.7和图11.8所示。
```java
import java.awt.*;
import javax.swing.*;
import java.awt.event.*;

class ReaderListen implements ActionListener{
	public void actionPerformed(ActionEvent e) {
		String str=e.getActionCommand();
		System.out.println(str+"'s length is:"+str.length());
	}
}
class WindowActionEvent extends JFrame{
	JTextField text;
	ReaderListen listener;
	public WindowActionEvent() {
		init();
		setVisible(true);
		setDefaultCloseOperation(JFrame.EXIT_ON_CLOSE);
	}
	void init() {
		setLayout(new FlowLayout());
		text=new JTextField(10);
		listener=new ReaderListen();
		text.addActionListener(listener);//text是事件源，listener是监视器
		add(text);	
	}
}
public class exercise{
	public static void main(String args[]) {
		WindowActionEvent win=new WindowActionEvent();
		win.setBounds(100, 100, 310, 260);
		win.setTitle("处理actionevent事件");
	}
}
```
接下来我们改进这个ReaderListener类，在第五章讲过。利用组合可以让一个对象调用另一个对象，级当前对象可以委托它组合另一个对象调用方法产生行为，因此。可以在创建监视器的类中增加JTextArea类型的成员（即组合JTextArea类型的成员），以便引用，操作WindowsActionArea中的文本区。
```java
import java.awt.*;
import java.awt.event.ActionEvent;
import java.awt.event.ActionListener;
import javax.swing.*;
import java.awt.event.*;

class PoliceListen implements ActionListener{
	JTextField textInput;
	JTextArea textShow;
	public void setJTextField(JTextField text) {
		textInput=text;
	}
	public void setJTextArea(JTextArea area) {
		textShow=area;
	}
	public void actionPerformed(ActionEvent e) {
		String str=textInput.getText();
		textShow.append(str+"'s length is"+str.length()+"\n");
	}
}

class WindowActionEvent extends JFrame{
	JTextField inputText;
	JTextArea textShow;
	JButton button;
	PoliceListen listener;
	public WindowActionEvent() {
		init();
		setVisible(true);
		setDefaultCloseOperation(JFrame.EXIT_ON_CLOSE);
	}
	void init() {
		setLayout(new FlowLayout());
		inputText=new JTextField(10);
		button=new JButton("READ");
		textShow=new JTextArea(5,18);
		listener=new PoliceListen();
		listener.setJTextField(inputText);
		listener.setJTextArea(textShow);
		inputText.addActionListener(listener);
		button.addActionListener(listener);
		add(inputText);
		add(button);
		add(new JScrollPane(textShow));
	}
}

public class exercise {
	public static void main(String args[]) {
		WindowActionEvent win=new WindowActionEvent();
		win.setBounds(100,100,460,360);
		win.setTitle("处理ActionEvent事件");
	}
}
```
Java的事件处理是基于授权模式，即事件源调用方法将某个对象注册为自己的监视器。**处理相应的事件调用相应的接口**

### 实例2
为了加深对事件委托模型的理解，下面以一个响应按钮点击事件的简单示例来说明所需要知道的所有细节。在这个示例中，想要在一个面板中放置三个按钮，添加三个监听器对象用来作为按钮的动作监听器。

在这个情况下，只要用户点击面板上的任何一个按钮，相关的监听器对象就会接收到一个Action Event对象，它表示有个按钮被点击了。在示例程序中，监听器对象将改变面板的背景颜色。

在演示如何监听按钮点击事件之前，首先需要解释如何创建按钮以及如何将它们添加到面板中(有关GUI元素更加详细的内容请参看第12章)。
可以通过在按钮构造器中指定一个标签字符串、一个图标或两项都指定来创建一个按钮。下面是两个示例：
```java
JButton yellowButton = new JButton("Yellow") ;
JButton blueButton = new JButton(new Imagelcon("blue-ball.gif')) ;
```
将按钮添加到面板中需要调用add方法：
```java
JButton yellowButton = new JButton("Yellow") ;
JButton blueButton = new JButton("Blue") ;
JButton redButton = new JButton("Red") ;
buttonPanel.add(yellowButton) ;
buttonPanel.add(blueButton) ;
buttonPanel.add(redButton) ;
```

接下来需要增加让面板监听这些按钮的代码。这需要一个实现了ActionListener接口的类。如前所述，应该包含一个actionPerformed方法，其签名为：
```java
public void actionPerformed(ActionEvent event)
```
> 注释：在按钮示例中，使用的ActionListener接口并不仅限于按钮点击事件。它可以应用于很多情况：
>
> - 当采用鼠标双击的方式选择了列表框中的一个选项时；
> - 当选择一个菜单项时；
> - 当在文本域中按回车键时；
> - 对于一个Timer组件来说，当达到指定的时间间隔时。
>
> 在本章和下一章中，读者将会看到更加详细的内容。
>
> 在所有这些情况下，使用ActionListener接口的方式都是一样的：actionPerformed方法（ActionListener中的唯一方法）将接收一个ActionEvent类型的对象作为参数。这个事件对象包含了事件发生时的相关信息。
> 当按钮被点击时， 希望将面板的背景颜色设置为指定的颜色。这个颜色存储在监听器类中：
>
> ```java
> class ColorAction implements ActionListener {
> 	private Color backgroundColor ;
> 	public ColorAction(Color c) {
> 		backgroundedor = c;
> 	}
> 	public void actionPerformed (ActionEvent event) {
> 		// set panel background color
> 		...
> 	}
> }
> ```

然后，为每种颜色构造一个对象，并将这些对象设置为按钮监听器。
```java
ColorAction yellowAction = new ColorAction(Color.YELLOW) :
ColorAction blueAction = new ColorAction(Color.BLUE) ;
ColorAction redAction = new ColorAction(Color.RED) ;
yellowButton.addActionListener (yellowAction) ;
blueButton.addActionListener(blueAction) ;
redButton.addActionListener(redAction) ;
```
例如，如果一个用户在标有“Yellow”的按钮上点击了一下，yellowAction对象的actionPerformed方法就会被调用。这个对象的backgroundColor实例域被设置为Color.YELLOW，现在就将面板的背景色设置为黄色了。

这里还有一个需要考虑的问题。CobrAction对象不能访问buttonpanel变量。可以采用两种方式解决这个问题。一个是将面板存储在ColorAction对象中，并在ColorAction的构造器中设置它；另一个是将ColorAction作为ButtonFrame类的内部类，这样一来，它的方法就自动地拥有访问外部面板的权限了（有关内部类的详细介绍请参看第6章)。这里使用第二种方法。下面说明一下如何将ColorAction类放置在ButtonFrame类内。
```java
class ButtonFrame extends JFrame {
	private JPanel buttonPanel ;
	...
	private class ColorAction implements ActionListener {
		private Color backgroundColor;
		...
		public void actionPerformed(ActionEvent event) {
			buttonPanel.setBackground(backgroundColor);
		}
	}
}
```
下面仔细地研究actionPerformed方法。在ColorAction类中没有buttonPanel域，但在外部ButtonFrame类中却有

这种情形经常会遇到。事件监听器对象通常需要执行一些对其他对象可能产生影响的操作。可以策略性地将监听器类放置在需要修改状态的那个类中。

程序清单11-1包含了完整的框架类。无论何时点击任何一个按钮，对应的动作监听器就会修改面板的背景颜色。
```java
//程序清单11-1
package button;

import java.awt.*;
import java.awt.event.*;
import javax.swing.*;
/**
* A frame with a button panel
*/
public class ButtonFrame extends JFrame {
	private JPanel buttonPanel;
	private static final int DEFAULT_WIDTH = 300;
	private static final int DEFAULT_HEICHT = 200;
	public ButtonFrame() {
		setSize(DEFAULT_WIDTH, DEFAULT_HEIGHT);
		// create buttons
		JButton yellowButton = new JButton("Yellow");
		JButton blueButton = new JButton("Blue");
		JButton redButton = new JButton("Red");
		buttonPanel = new JPanel () ;
		// add buttons to panel
		buttonPanel.add(yellowButton) ;
		buttonPanel.add(blueButton) ;
		buttonPanel.add(redButton) ;
		// add panel to frame
		add(buttonPanel);
		// create button actions
		ColorAction yellowAction = new ColorAction(Color.YELLOW);
		ColorAction blueAction = new ColorAction(Color.BLUE) ;
		ColorAction redAction = new ColorAction(Color.RED) ;
		// associate actions with buttons
		yellowButton.addActionListener(yellowAction);
		blueButton.addActionListener(blueAction) ;
		redButton.addActionListener(redAction);
	}
	/**
	* An action listener that sets the panel ' s background color.
	*/
	private class ColorAction implements ActionListener {
		private Color backgroundColor;
		public ColorAction(Color c) {
			backgroundedor = c;
		}
		public void actionPerformed(ActionEvent event) {
			buttonPanel.setBackground(backgroundColor);
		}
	}
}
```
### 2.2 简洁的指定监听器
在上一节中，我们为事件监听器定义了一个类并构造了这个类的3个对象。一个监听器类有多个实例的情况并不多见。更常见的情况是：每个监听器执行一个单独的动作。在这种情况下，没有必要分别建立单独的类。只需要使用一个lambda表达式：
```java
exitButton.addActionListener(event -> System.exit(O));
```
现在考虑这样一种情况：有多个相互关联的动作，如上一节中的彩色按钮。在这种情况下，可以实现一个辅助方法：
```java
public void makeButton(String name, Color backgroundedor) {
	JButton button = new JButton(name);
	buttonPanel.add(button);
	button.addActionListener(event ->
		buttonPanel.setBackground(backgroundColor));
}
```
需要说明， lambda表达式指示参数变量backgroundColor。
然后只需要调用：
```java
makeButton("yellow", Color.YELLOW):
makeButton("blue", Color.BLUE);
makeButton("red", Color.RED);
```
在这里，我们构造了3个监听器对象，分别对应一种颜色，但并没有显式定义一个类。每次调用这个辅助方法时，它会建立实现了ActionListener接口的一个类的实例它的actionPerformed动作会引用实际上随监听器对象存储的backGroundColor值。不过，所有这些会自动完成，而无需显式定义监听器类、实例变量或设置这些变量的构造器。

> 注释：在较老的代码中，通常可能会看到使用匿名类：
>
> ```java
> exitButton.addActionListener(new ActionListener() {
> 	public void actionPerformed(new ActionEvent) {
> 		System.exit(0);
> 	}
> });
> ```
当然，已经不再需要这种繁琐的代码。使用lambda表达式更简单，也更简洁。

> 注释：有些程序员不习惯使用内部类或lambda表达式，而更喜欢创建实现了ActionListener接口的事件源容器，然后这个容器再设置自身作为监听器。如下所示：
>
> ```java
> yellowButton.addActionListener(this);
> blueButton.addActionListener(this);
> redButton.addActionListener(this);
> ```
> 现在这3个按钮不再有单独的监听器。它们共享一个监听器对象， 具体来讲就是框架(frame)。因此，actionPerformed方法必须明确点击了哪个按钮。
> ```java
> class ButtonFrame extends JFrame implements ActionListener {
> 	public void actionPerformed(ActionEvent event) {
> 		Object source = event.getSource();
> 		if (source == yellowButton) ...
> 		else if (source = blueButton) ...
> 		else if (source = redButton ) ...
> 		else ...
> 	}
> }
> ```
> 我们并不建议采用这种策略。

> 注释：lambda表达式出现之前，还可以采用一种机制来指定事件监听器，其事件处理器包含一个方法调用。例如，假设一个按钮监听器需要执行以下调用：
> ```java
> frame.loadData();
> ```
> EventHandler类可以用下面的调用创建这样一个监听器：
> ```java
> EventHandler.create(ActionListener.class,frame,"loadData");
> ```
> 这种方法现在已经成为历史。利用lambda表达式，可以更容易地使用以下调用：
> ```java
> event -> frame.loadData();
> ```
> EventHandler机制的效率也不高，而且比较容易出错。它使用反射来调用方法。出于这个原因，EventHandler.create调用的第二个参数必须属于一个公有类。否则，反射机制就无法确定和调用目标方法

## 3 ItemEvent事件
##### 1.ItemEvent事件源
选择框，下拉列表都可以触发ItemEvent事件。选择框提供两种状态，一种是选中，另一种是未选中。对于注册了监视器的选择框，当用户的操作使得选择框从未选中状态变成选中状态，或是相反变化的时候就会触发ItemEvent事件，同样，对于注册了监视器的下拉列表，如果用户选中下拉列表中的某个选项，就会触发ItemEvent事件。
##### 2.注册监视器
能触发ItemEvent事件的组件使用
```java
addItemListener(ItemListener listen);
```
将实现ItemListener接口的类的实例注册为事件源的监视器。
##### 3.ItemListener接口
ItemListener接口在java.awt.event包中，该接口只有一个方法;
```java
public void itemStateChanged(itemEvent e);
```
事件触发ItemEvent事件后，监视器将发现触发的ItemEvent事件，然后调用接口中的方法：
```java
itemStateChange(ItemEvent e);
```
对发生的事件做出处理。当监视器调用itemStateChange(ItemEvent e)方法时，ItemEvent类事先创建的事件对象就会传递给该方法的参数e。

ItemEvent事件对象除了可以使用getSource()方法返回发生ItemEvent事件的事件源外，也可以使用getItemSelectable()方法返回发生ItemEvent事件的事件源。  

## 4 DocumentEvent事件
##### 1.DocumentEvent事件源
文本区中含有一个实现Document接口的实例，该实例被称作文本区维护的文档，文本区调用getDocument()方法返回维护的文档。文本区维护的文档能触发DocumentEvent事件。需要注意的是，DocumentEvent不在java.awt.event包中，而是在javax.swing.event包中。用户在文本区中进行文本编辑操作，使得文本区中的文本区内容发生变化，将导致文本区维护的文档模型中的数据发生变化，从而导致文本区维护的文档触发DocumentEvent事件。
##### 2.注册监视器
能触发DocumentEvent事件的事件源使用addDocumentListener(DocumentListenerlisten)将实现DocumentListener接口的类的实例注册为事件源的监视器。
##### 3.DocumentListener接口
DocumentListener接口在java.swing.event包中，该接口中有三个方法：
```java
public void changedUpdate(DocumentEvent e);
public void removeUpdate(DocumentEvent e);
public void insertUpdate(DocumentEvent e);
```
事件源触发DocumentEvent事件后，监视器将发现触发的DocumentEvent事件，然后调用接口中的相应方法对发生的事件做出处理。  

在下面的例子中，在左边输入若干英文单词，另一个文本区会自动对输入的英文单词按照字典顺序排序，也就是说随着用户输入的变化，另一个文本区不断地更新排序。
```java
import java.awt.event.*;
import java.io.*;
import javax.swing.event.*;
import javax.swing.*;
import java.util.*;
class PoliceListen implements DocumentListener {
	JTextArea inputText,showText;
	public void setInputText(JTextArea text) {
		inputText=text;
	}
	public void setShowText(JTextArea text) {
		showText=text;
	}
	public void changedUpdate(DocumentEvent e) {
		String str=inputText.getText();
		String regex="[\\s\\d\\p{Punct}]+";//正则表达式
		String words[]=str.split(regex);
		Arrays.sort(words);//按照字典顺序进行排序
		showText.setText(null);
		for(String s:words)
			showText.append(s+",");
	}
	public void removeUpdate(DocumentEvent e) {
		changedUpdate(e);
	}
	public void insertUpdate(DocumentEvent e) {
		changedUpdate(e);
	}
}

class WindowDocument extends JFrame{
	JTextArea inputText,showText;
	PoliceListen listen;
	public WindowDocument() {
		init();// TODO Auto-generated constructor stub
		setLayout(new FlowLayout());
		setVisible(true);
		setDefaultCloseOperation(JFrame.EXIT_ON_CLOSE);
	}
	void init() {
		inputText=new JTextArea(6,8);
		showText=new JTextArea(6,8);
		add(new JScrollPane(inputText));
		add(new JScrollPane(showText));
		listen=new PoliceListen();
		listen.setInputText(inputText);
		listen.setShowText(showText);
		(inputText.getDocument()).addDocumentListener(listen);//向文档注册监视器
	}
}

public class exercise{
	public static void main(String args[]) {
		WindowDocument win=new WindowDocument();
		win.setBounds(10,10,460,360);
		win.setTitle("处理DocumentEvent事件");
	}
}
```

## 5 MouseEvent事件
任何组件都可以发生鼠标事件，如鼠标进入组件，退出组件，在组建上方单击鼠标，拖动鼠标等都可以触发鼠标事件，即导致`MouseEvent`类自动创建一个事件对象。
##### 1.使用MouseEvent接口处理鼠标事件
使用MouseListener接口可以处理以下5种操作触发的鼠标事件。
- 在事件源上按下鼠标键
- 在事件源上释放鼠标键
- 在事件源上单击鼠标键
- 鼠标进入事件源
- 鼠标退出事件源

MouseEvent中有下列几个重要方法：
```java
getX();//获取鼠标指针在事件源坐标系中的x坐标
getY();//获取鼠标指针在事件源坐标系中的y坐标
getModifiers();//获取鼠标的左键或右键。鼠标的左键和右键分别使用InputEvent类中的常量BUTTON1_MASK,BUTTON3_MASK来表示；
getClickCount();//获取鼠标被单击的次数
getSource();//获取鼠标事件的事件源
```
事件源注册监视器的方法是
```java
addMouseListener(MouseListener listener) 
```
当用户点击鼠标按钮时，将会调用三个监听器方法：鼠标第一次被按下时调用`mousePressed`；鼠标被释放时调用`mouseReleased`；最后调用`mouseClicked`。如果只对最终的点击事件感兴趣， 就可以忽略前两个方法。用`MouseEvent`类对象作为参数，调用getX和getY方法可以获得鼠标被按下时鼠标指针所在的x和y坐标。要想区分单击、双击和三击(!)，需要使用`getClickCount`方法。

有些用户界面设计者喜欢让用户采用鼠标点击与键盘修饰符组合(例如，CONTROL+SHIFT+CLICK)的方式进行操作。我们感觉这并不是一种值得赞许的方式。如果对此持有不同的观点，可以看一看同时检测鼠标按键和键盘修饰符所带来的混乱。

可以采用位掩码来测试已经设置了哪个修饰符。在最初的API中，有两个按钮的掩码与两个键盘修饰符的掩码一样，即
```java
BUTT0N2_MASK == ALT_MASK
BUTT0N3_MASK == META_MASK
```
这样做是为了能够让用户使用仅有一个按钮的鼠标通过按下修饰符键来模拟按下其他鼠标键的操作。然而，在Java SE 1.4中，建议使用一种不同的方式。有下列掩码：
```
BUTT0N1_D0WN_MASK
BUTT0N2_D0WN_MASK
BUTT0N3_D0WN_MASK
SHIFT_DOWN_MASK
CTRL_DOWN_MASK
ALT_DOWN_MASK
ALT_CRAPH_DOWN_MASK
META_DOWN_MASK
```
getModifiersEx方法能够准确地报告鼠标事件的鼠标按钮和键盘修饰符。

需要注意，在Windows环境下，使用`BUTT0N3_D0WN_MASK`检测鼠标右键（非主要的）的状态。例如，可以使用下列代码检测鼠标右键是否被按下：
```java
if ((event.getModifiersEx() & InputEvent.BUTT0N3_D0WN_MASK) != 0)
... // code for right click
```

MouseListener接口中有如下方法：
```java
mousePressed(MouseEvent);//负责处理在组件上按下鼠标键触发的鼠标事件。即当你在事件源按下鼠标键时监视器调用接口中的这个方法对事件作出处理。
mouseReleased(MouseEvent);//负责处理在组件上释放鼠标键触发的鼠标事件。即当你在事件源释放鼠标键时，监视器调用接口中的这个方法对事件作出处理。
mouseEntered(MouseEvent);//负责处理鼠标进入组件触发的鼠标事件。即当鼠标指针进入组件时，监视器调用接口中的这个方法对事件作出处理。
mouseExited(MouseEvent);//负责处理鼠标离开组件触发的鼠标事件。即当鼠标指针离开容器时，监视器调用接口中的这个方法对事件作出处理。
mouseClicked(MouseEvent);//负责处理在组件上单击鼠标键触发的鼠标事件。即当单击鼠标时，监视器调用接口中的这个方法对事件做出处理。
```
### 实例
在列举的简单示例中， 提供了`mousePressed`和`mouseClicked`方法。当鼠标点击在所有小方块的像素之外时，就会绘制一个新的小方块。这个操作是在`mousePressed`方法中实现的，这样可以让用户的操作立即得到响应，而不必等到释放鼠标按键。如果用户在某个小方块中双击鼠标，就会将它擦除。由于需要知道点击次数，所以这个操作将在`mouseClicked`方法中实现。
```java
public void mousePressed (MouseEvent event) {
	current = find(event.getPoint());
	if (current null) // not inside a square
		add(event.getPoint()) ;
}
public void mouseClicked(MouseEvent event) {
	current = find(event.getPoint())；
	if (current != null && event.getClickCount() >= 2)
		remove(current):
}
```
当鼠标在窗口上移动时，窗口将会收到一连串的鼠标移动事件。请注意：有两个独立的接口`MouseListener`,`MouseMotionListener`。这样做有利于提高效率。

当用户移动鼠标时，只关心鼠标点击(clicks)的监听器就不会被多余的鼠标移动(moves)所困扰。这里给出的测试程序将捕获鼠标动作事件，以便在光标位于一个小方块之上时变成另外一种形状（十字)。实现这项操作需要使用Cursor类中的`getPredefinedCursor`方法。表11-3列出了在Windows环境下鼠标的形状和方法对应的常量。

下面是示例程序中`MouseMotionListener`类的`mouseMoved`方法：
```java
public void mouseMoved(MouseEvent event) {
	if (find(event.getPointO) = null )
		setCursor(Cursor.getDefaultCursor ()) ;
	else
		setCursor(Cursor.getPredefinedCursor(Cursor.CROSSHAIR_CURS0R)) ;
}
```
> 注释：还可以利用Toolkit类中的`createCustomCursor`方法自定义光标类型：
>
> ```java
> Toolkit tk = Toolkit.getDefaultToolkit();
> Image img = tk.getImage("dynamite.gif");
> Cursor dynamiteCursor = tk.createCustomCursor(img, new Point (10 , 10) , "dynamite stick");
> ```
>
> createCustomCursor的第一个参数指向光标图像。第二个参数给出了光标的“热点”偏移。第三个参数是一个描述光标的字符串。这个字符串可以用于访问性支持，例如，可以将光标形式读给视力受损或没有在屏幕前面的人。
如果用户在移动鼠标的同时按下鼠标，就会调用`mouseMoved`而不是调用`mouseDmgged`。在测试应用程序中，用户可以用光标拖动小方块。在程序中，仅仅用拖动的矩形更新当前光标位置。然后，重新绘制画布，以显示新的鼠标位置。
```java
public void mouseDragged(MouseEvent event) {
	if (current != null) {
		int x = event .getX();
		int y = event.getY();
		current .setFrame(x - SIDELENGTH / 2 , y - SIDELENGTH / 2, SIDELENCTH , SIDELENCTH) ;
		repaint();
	}
}
```
> 注释：只有鼠标在一个组件内部停留才会调用`mouseMoved`方法。然而，即使鼠标拖动到组件外面，`mouseDragged`方法也会被调用。

还有两个鼠标事件方法：`mouseEntered`和`mouseExited`。这两个方法是在鼠标进入或移出组件时被调用。

最后， 解释一下如何监听鼠标事件。鼠标点击由`mouseClicked`过程报告，它是MouseListener接口的一部分。由于大部分应用程序只对鼠标点击感兴趣，而对鼠标移动并不感兴趣，但鼠标移动事件发生的频率又很高，因此将鼠标移动事件与拖动事件定义在一个称为`MouseMotionListener`的独立接口中。

在示例程序中，对两种鼠标事件类型都感兴趣。这里定义了两个内部类：`MouseHandler`和`MouseMotionHandler`。`MouseHandler`类扩展于`MouseAdapter`类，这是因为它只定义了5个`MouseListener`方法中的两个方法。`MouseMotionHandler`实现了`MouseMotionListener`接口，并定义了这个接口中的两个方法。程序清单11-4是这个程序的清单。
```java
//程序清单11*4 mouse/MouseFrame.java
package mouse;
import javax.swing.*;
/**
* A frame containing a panel for testing mouse operations
*/
public class MouseFrame extends JFrame {
	public MouseFrame() {
		add (new MouseComponent()) ;
		pack() ;
	}
}
```
```java
//程序清单11-5 mouse/MouseComponent.java
package mouse;
import java.awt.*;
import java.awt.event.*;
import java.awt.geom.*;
import java.util.*;
import javax.swing.*;
/**
* A component with mouse operations for adding and removing squares.
*/
public class MouseComponent extends JComponent {
	private static final int DEFAULT.WIDTH = 300;
	private static final int DEFAULT.HEICHT = 200;
	private static final int SIDELENCTH = 10;
	private ArrayList<Rectangle2D> squares;
	private Rectangle2D current ; // the square containing the mouse cursor
	public MouseComponent () {
		squares = new ArrayList() ;
		current = null ;
		addMouseListener(new MouseHandler());
		addMouseMotionListener(new MouseMotionHandler());
	}
	public Dimension getPreferredSize() { 
		return new Dimension(DEFAULT_WIDTH, DEFAULT_HEIGHT); 
	}
	public void paintComponent(Graphics g) {
		Graphics2D g2 = (Graphics2D) g;
		// draw all squares
		for (Rectangle2D r : squares)
			g2.draw(r);
	}
	/**
	* Finds the first square containing a point.
	* @param p a point
	* ©return the first square that contains p
	*/
	public Rectangle2D find(Point2D p) {
		for (Rectangle2D r : squares) {
			if (r.contains(p)) return r;
		}
		return null;
	}
	/**
	* Adds a square to the collection.
	* @param p the center of the square
	*/
	public void add(Point2D p) {
		double x = p.getX();
		double y = p.getY();
		current = new Rectangle2D.Double(x-SIDELENCTH/2, y-SIDELENCTH/2, SIDELENCTH, SIDELENCTH);
		squares.add(current);
		repaint();
	}
	/**
	* Removes a square from the collection.
	* iparan s the square to remove
	*/
	public void remove(Rectangle2D s) {
		if (s = null) return;
		if (s == current) current = null;
		squares.remove(s);
		repaint();
	}
	private class MouseHandler extends MouseAdapter {
		public void mousePressed(MouseEvent event) {
			// add a new square if the cursor isn't inside a square
			current = find(event.getPoint());
			if (current == null) add(event,getPoint());
		}
		public void mousedieked(MouseEvent event) {
			// remove the current square if double clicked
			current = find(event.getPoint());
			if (current != null && event.getClickCount() >= 2)
				remove(current);
		}
	}
	private class MouseMotionHandler implements MouseMotionListener {
		public void mouseMoved(MouseEvent event) {
			// set the mouse cursor to cross hairs if it is inside
			// a rectangle
			if (find(event.getPoint()) == null)
				setCursor(Cursor.getDefaultCursor());
			else
				setCursor(Cursor.getPredefinedCursor(Cursor.CROSSHAIR_CURSOR));
		}
		public void mouseDragged(MouseEvent event) {
			if (current != null) {
				int x = event.getX();
				int y = event.getY();
				// drag the current rectangle to center it at (x, y)
				current.setFrame(x-SIDELENCTH/2, y-SIDELENGTH/2, SIDELENCTH, SIDELENGTH);
				repaint();
			}
		}
	}
}
```

> java awt.event.MouseEvent 1.1
>
> ```java
> int getX();
> int getY();
> Point getPoint();//返回事件发生时， 事件源组件左上角的坐标x(水平)和y(竖直)，或点信息。
> int getClickCount();//返回与事件关联的鼠标连击次数(“连击” 所指定的时间间隔与具体系统有关)。
> ```

> java awt.event.InputEvent 1.1
>
> ```java
> int getModifiersEx() 1.4
> //返回事件扩展的或“按下”（down) 的修饰符。
> 
> 使用下面的掩码值检测返回值：
> BUTT0N1_D0WN_MASK
> BUTT0N2_D0WN_MASK
> BUn0N3_D0WN_MASK
> SHIFT_DOWN_MASK
> CTRL_DOWN_MASK
> ALT_DOWN_MASK
> ALT_GRAPH_DOWN_MASK
> META.DOWN.MASK
> 
> static String getModifiersExText(int modifiers) 1.4
> //返回用给定标志集描述的扩展或“ 按下” （down) 的修饰符字符串， 例如“Shift+Buttonl” 
> ```

> java.awt.Toolkit 1.0
>
> ```java
> public Cursor createCustomCursor(Image image,Point hotSpot,String name) 1.2
> //创建一个新的定制光标对象。
> 
> 参数:
> image 光标活动时显示的图像
> hotSpot 光标热点（箭头的顶点或十字中心）
> name 光标的描述， 用来支持特殊的访问环境
> ```

> java.awtComponent 1.0
>
> ```java
> public void setCursor(Cursor cursor);	1.1
> //用光标图像设置给定光标
> ```
## AWT事件继承层次
弄清了事件处理的工作过程之后，作为本章的结束，总结一下AWT事件处理的体系架构。前面已经提到，Java事件处理采用的是面向对象方法，所有的事件都是由java.util包中的EventObject类扩展而来的（公共超类不是Event, 它是旧事件模型中的事件类名。尽管现在不赞成使用旧的事件模型，但这些类仍然保留在Java库中)。

有些Swing组件将生成其他事件类型的事件对象；它们都直接扩展于EventObject, 而不是AWTEvent。

事件对象封装了事件源与监听器彼此通信的事件信息。在必要的时候，可以对传递给监听器对象的事件对象进行分析。在按钮例子中，是借助getSource和getActionCommand方法实现对象分析的。

> ![AWT事件类的继承关系图](https://github.com/whatsabc/java-basic-notes/blob/master/%E6%8F%92%E5%9B%BE/AWT%E4%BA%8B%E4%BB%B6%E7%B1%BB%E7%9A%84%E7%BB%A7%E6%89%BF%E5%85%B3%E7%B3%BB%E5%9B%BE.jpg?raw=true)

对于有些AWT事件类来说，Java程序员并不会实际地使用它们。例如，AWT将会把PaintEvent对象插入事件队列中，但这些对象并没有传递给监听器。Java程序员并不监听绘图事件，如果希望控制重新绘图操作，就需要覆盖paintComponent方法。另外，AWT还可以生成许多只对系统程序员有用的事件，用于提供表义语言的输入系统以及自动检测机器人等。在此，将不讨论这些特殊的事件类型。

### 1语义事件和底层事件
AWT将事件分为底层(low-level)事件和语义(semantic)事件。语义事件是表示用户动作的事件，例如，点击按钮；因此，ActionEvent是一种语义事件。底层事件是形成那些事件的事件。在点击按钮时，包含了按下鼠标、连续移动鼠标、抬起鼠标（只有鼠标在按钮区中抬起才引发）事件。或者在用户利用TAB键选择按钮，并利用空格键激活它时，发生的敲击键盘事件。同样，调节滚动条是一种语义事件，但拖动鼠标是底层事件。

下面是java.awt.event包中最常用的语义事件类：
- ActionEvent (对应按钮点击、菜单选择、选择列表项或在文本框中ENTER);
- AdjustmentEvent (用户调节滚动条)；
- ItemEvem (用户从复选框或列表框中选择一项）。

常用的5个底层事件类是：
- KeyEvent (一个键被按下或释放)；
- MouseEvent (鼠标键被按下、释放、移动或拖动)；
- MouseWheelEvent (鼠标滚轮被转动)；
- FocusEvent (某个组件获得焦点或失去焦点)；
- WindowEvent (窗口状态被改变）。

下列接口将监听这些事件。
```
ActionListener
AdjustmentListener
FocusListener
ItemListener
KeyListener
MouseListener
MouseMoti onListener
MouseWheelListener
WindowListener
WindowFocusListener
WindowStateListener
```
有几个AWT 监听器接口包含多个方法，它们都配有一个适配器类，在这个类中实现了相应接口中的所有方法，但每个方法没有做任何事情（有些接口只包含一个方法，因此，就没有必要为它们定义适配器类了）。下面是常用的适配器类：
```
FocusAdapter	MouseMotionAdapter
KeyAdapte	WindowAdapter
MouseAdapter
```
表11-4显示了最重要的AWT监听器接口、事件和事件源。

> 表11-4事件处理总结
![事件处理总结表1](https://github.com/whatsabc/java-basic-notes/blob/master/%E6%8F%92%E5%9B%BE/%E4%BA%8B%E4%BB%B6%E5%A4%84%E7%90%86%E6%80%BB%E7%BB%93%E8%A1%A81.jpg?raw=true)
![事件处理总结表2](https://github.com/whatsabc/java-basic-notes/blob/master/%E6%8F%92%E5%9B%BE/%E4%BA%8B%E4%BB%B6%E5%A4%84%E7%90%86%E6%80%BB%E7%BB%93%E8%A1%A82.jpg?raw=true)

## 6 焦点事件
组件可以触发焦点事件。组件可以使用
```java
addFocusListener(FocusListener listener);
```
注册焦点事件监视器，当组件获得焦点监视器后，如果组件从无输入焦点变成有输入焦点或从有输入焦点变成无输入焦点都会触发FocusEvent事件。创建监视器的类必须要实现FocusListener接口，该接口有两个方法：
```java
public void focusGained(FocusEvent e);//监视器从无焦点输入变成有焦点输入
public void focusLost(FocusEvnet e);//监视器从有焦点输入变成无焦点输入
```
用户通过单击组件可以使得组件有输入焦点，同时也使得其他组件变成无输入焦点。
一个组件也可调用
```java
public boolean requestFocusInWindow();
```
方法可以获得输入焦点。
## 7 键盘事件
当按下，释放或敲击键盘上一个键时就就触发了键盘事件，在Java事件模式中，必须要有发生事件的事件源。当一个组件处于激活状态时，敲击键盘上一个键就导致这个组件触发键盘事件。当使用KeyListener接口处理键盘事件，有如下3个方法：
```java
public void keyPessed(KeyEvent e);
public void keyTyped(KeyEvent e);
public void keyReleased(KeyEvent e);
```
某个组件使用addListener方法注册监视器后，当该组件处于激活状态时，用户按下键盘上的某个键时，触发keyEvent事件，监视器调用keyPressed方法；用户释放键盘上按下的键时，触发keyReleased方法。keyTyped方法是keyPressed和keyReleased方法的组合，当键按下又释放时，监视器调用keyTyped方法。  

用KeyEvent类的
```java
public int getKeyCode();
```
方法，可以判断哪个键被按下，敲击或释放，getKeyCode方法返回一个键码值，也可以用KeyEvent类的
```java
public char getKeyChar();
```
判断哪个键被按下，敲击或释放，getKeyChar()方法返回键上的字符。

键码|键
---|---
VK_F1-VK_F12|功能键F1~F12
VK_LEFT|向左箭头键
VK_RIGHT|向右箭头键
VK_UP|向上箭头键
VK_DOWN|向下箭头键
VK_KP_UP|小键盘的向上箭头键
VK_KP_DOWN|小键盘的向下箭头键
VK_KP_LEFT|小键盘的向左箭头键
VK_KP_RIGHT|小键盘的向右箭头键
VK_END|END键
VK_HOME|HOME键
VK_PAGE DOWN|向后题页键
VK_PAGE UP|向前距页键
VK_PRINTSCREEN|打印屏幕键
VK_SCROLL LOCK|滚动锁定键
VK_CAPS LOCK|大写锁定键
VK NUM LOCK|数字锁定键
PAUSE|暂停键
VK_INSERT|插入键
VK_DELETE|删除键  
VK_ENTER|回车键
VK_TAB|制表符键  
VK_BACK_SPACE|退格键  
VK_ESCAPE|Esc键  
VK_CANCEL|取消键  
VK_CLEAR|清除键 
VK_SHIFT|Shift键  
VK_CONTROL|Ctrl键
VK_ALT|Alt键
VK_PAUSE|暂停键
VK_SPACE|空格键
VK_COMMA|逗号键
VK_SEMICOLON|分号键
VK_PERIOD|.键
VK_SLASH|/键
VK_BACK SLASH |\键
VK_0~VK_9|0~9键
VK_A~VK_Z|A~z健
VK_OPEN_BRACKET|\[键
VK_CLOSE_BACKET|]键
VK_UNMPAD0-VK_NUMPAD9 |小健盘上的0至9键
VK_QUOTE|单引号'键
VK_BACK_QUOTE|单引号'健



当安装某些软件时，经常要求输入序列号码，并且要在几个文本框中依次输入。每个文本框中输入的字符数目都是固定的，当在第一个文本框输入了恰好的字符个数后,输入光标会自动转移到下一个文本框。例11. 11通过处理键盘事件来实现软件序列号的输入。当文本框获得输入焦点后，用户敲击键盘将使得当前文本框输入序列号触发KeyEvent事件，在处理事件时，程序检查文本框中光标的位置，如果光标已经到达指定位置，就将输入焦点转移到下一个文本框。程序运行效果如图11.12所示.

```java
import java.awt.event.*;
import javax.swing.*;
import java.awt.*;
class Police implements KeyListener,FocusListener{
	public void keyPressed(KeyEvent e) {
		JTextField t=(JTextField)e.getSource();//返回的当前动作所指向的对象，包含对象的所有信息
		// 文本框获取事件源
		if(t.getCaretPosition()>=6) {//返回文本插入符的位置。插入符的位置被限制在 0 和文本最后一个字符（包括）之间。如果没有设置文本或插入符，则默认插入符的位置为 0。
			t.transferFocus();//等同于transferFocus(false) 转移焦点
		}
	}
	public void keyTyped(KeyEvent e) {
	}
	public void keyRealeased(KeyEvent e) {
	}
	public void focusGained(FocusEvent e) {
		JTextField text=(JTextField)e.getSource();
		text.setText(null);
	}
	public void focusLost(FocusEvent e) {
	}
}

class Win extends JFrame {
	JTextField text[]=new JTextField[3];
	Police police;
	JButton b;
	Win(){
		setLayout(new FlowLayout());
		police=new Police();
		for(int i=0;i<3;i++) {
			text[i]=new JTextField(7);
			text[i].addKeyListener(police);//监视键盘事件
			text[i].addFocusListener(police);//监视焦点事件
			add(text[i]);
		}
		b=new JButton("确定");
		add(b);
		text[0].requestFocusInWindow();//是为text[0]文本框初始化一个输入焦点，也就是说当你生成窗口时，不需要用鼠标点击文本框就可以在该文本框text[0]中输入数值。
		setVisible(true);
		setDefaultCloseOperation(JFrame.EXIT_ON_CLOSE);
	}
}

public class exercise{
	public static void main(String args[]) {
		Win win=new Win();
		win.setTitle("输入序列号");
		win.setBounds(10,10,460,360);
	}
}
```
## 8 匿名类实例或窗口做监视器

## 9 使用MVC结构
模型-视图-控制器，简称MVC，MVC是一种先进的设计结构，是TrygveReenskaug教授于1978年最早开发的一个基本结构，其目的是以会话形式提供方便的GUI支持，MVC首先出现在Smalltalk编程语言中。

MVC是一种通过三个不同部分构造一个软件或组件的理想办法。

- 模型，用于储存数据的对象
- 视图，为模型提供数据显示的对象
- 控制器，处理用户的交互操作，对于用户的操作做出响应，让模型和视图进行必要的交互，即通过视图修改，获取模型中的数据；当模型中的数据变化时，让视图更新显示。  

从面向对象的角度看，MVC结构可以使程序更具有对象化特性，也更容易维护，**在设计程序时，可以将某个对象看作模型，然后为模型提供恰当的显示组件，即视图，为了对用户的操作做出响应，可以选择某个组件做控制器，当组件发生事件后，通过视图修改得到模型中维护着的数据，并让视图更新显示。**  

如下例：首先编写一个封装三角形的类，然后再编写一个窗口。要求窗口使用三个文本框和一个文本区域为三角形对象中的数据提供视图，其中三个文本框用来显示和更新三角形对象的三个边的长度；文本区对象用来显示三角形的面积。窗口中有一个按钮，用户单击该按钮后，程序用三个文本框中的数据分别作为三角形的三个边的长度，并将计算结果显示在文本区。
```java
import java.awt.*;
import java.awt.event.*;
import javax.swing.*;

class Triangle{
	double sideA,sideB,sideC,area;
	boolean isTriange;
	public String getArea() {
		if(isTriange) {
			double p=(sideA+sideB+sideC)/2.0;
			area=Math.sqrt(p*(p-sideA)*(p-sideB)*(p-sideC));
			return String.valueOf(area);
		}
		else {
			return "无法计算面积";
		}
	}
	public void setA(double a) {
		sideA=a;
		if(sideA+sideB>sideC&&sideA+sideC>sideB&&sideB+sideB>sideA) {
			isTriange=true;
		}
		else
			isTriange=false;
	}
	public void setB(double b) {
		sideB=b;
		if(sideA+sideB>sideC&&sideA+sideC>sideB&&sideB+sideB>sideA) {
			isTriange=true;
		}
		else
			isTriange=false;
	}
	public void setC(double c) {
		sideC=c;
		if(sideA+sideB>sideC&&sideA+sideC>sideB&&sideB+sideB>sideA) {
			isTriange=true;
		}
		else
			isTriange=false;
	}
}

class WindowTriangle extends JFrame implements ActionListener{
	Triangle triangle;
	JTextField textA,textB,textC;
	JTextArea showArea;
	JButton controlButton;
	WindowTriangle() {
		init();// TODO Auto-generated constructor stub
		setVisible(true);
		setDefaultCloseOperation(JFrame.EXIT_ON_CLOSE);
	}
	void init(){
		triangle=new Triangle();
		textA=new JTextField(5);
		textB=new JTextField(5);
		textC=new JTextField(5);
		showArea=new JTextArea();
		controlButton=new JButton("计算三角形面积");
		JPanel pNorth=new JPanel();
		pNorth.add(new JLabel("边A："));
		pNorth.add(textA);
		pNorth.add(new JLabel("边B："));
		pNorth.add(textB);
		pNorth.add(new JLabel("边C："));
		pNorth.add(textC);
		pNorth.add(controlButton);
		controlButton.addActionListener(this);
		add(pNorth,BorderLayout.NORTH);
		add(new JScrollPane(showArea), BorderLayout.CENTER);
	}
	public void actionPerformed(ActionEvent e) {
		try {
			double a=Double.parseDouble(textA.getText().trim());
			double b=Double.parseDouble(textB.getText().trim());
			double c=Double.parseDouble(textC.getText().trim());
			triangle.setA(a);//更新数据
			triangle.setB(b);
			triangle.setC(c);
			String area=triangle.getArea();
			showArea.append("这个三角形的面积是：");
			showArea.append(area+"\n");//更新视图
		}
		catch(Exception ex) {
			showArea.append("\n"+ex+"\n");
		}
	}
}

public class exercise{
	public static void main(String args[]) {
		WindowTriangle win=new WindowTriangle();
		win.setTitle("使用MVC结构");
		win.setBounds(100,100,620,260);
	}
}
```
## 10 对话框
JDialog类和JFrame类都是window的子类，二者的实例都是底层容器，但是二者有相似之处也有不同的地方，主要区别是，JDialog类创建的对话框必须要依赖某个窗口。 

对话框分为无模式和有模式两种。如果一个对话框是有模式的对话框，那么当这个对话框处于激活状态时，只让程序响应对话框内部的事件，而且将堵塞其他线程的执行，用户不能再激活对话框所在程序中的其他窗口，知道该对话框消失不可见。无模式对话框处于激活状态时，能再激活其他窗口，也不阻塞其他线程的执行。 

**注意：进行一个重要的操作之前，通过弹出一个有模式的对话框表明操作的重要性**

### 10.1 消息对话框
消息对话框是有模式对话框，进行一个重要的操作之前，最好能弹出一个消息对话框。可以用javax.swing包中的JOptionPane类的静态方法：
```java
public static void showMessageDialog(Component parentComponent,String message,String title,int messageType);
```
创建一个消息对话框，其中参数parentComponent指定对话框可见时的位置，如果parentComponent为null，对话框会在屏幕的正前方显示出来；如果组件parentComponent不空，对话框在组件parentComponent的正前面居中显示。message指定对话框上显示的消息；title指定对话框的标题；
messageType取下列有效值：
```
JOptionPane.INFORMATION_MESSAGE
JOptionPane.WARING_MESSAGE
JOptionPane.ERROR_MESSAGE
JOptionPane.QUESTION_MESSAGE
JOptionPane.PLAIN_MESSAGE
```
这些值可以改变对话框的外观

**在下面的例子中：要求用户在文本框中只能输入英文字母，在输入非英文字母时，弹出消息提示框。**

```java
import java.awt.event.*;
import java.awt.*;
import javax.swing.*;

class WindowMess extends JFrame implements ActionListener{
	JTextField inputEnglish;
	JTextArea show;
	String regex="[a-zZ-Z]+";
	public WindowMess() {
		inputEnglish=new JTextField(10);// TODO Auto-generated constructor stub
		show=new JTextArea();
		inputEnglish.addActionListener(this);
		add(inputEnglish,BorderLayout.NORTH);
		add(show,BorderLayout.CENTER);
		setVisible(true);
		setDefaultCloseOperation(JFrame.EXIT_ON_CLOSE);
	}
	public void actionPerformed(ActionEvent e) {
		if(e.getSource()==inputEnglish) {
		    //e.getSource()就能得到这个jbutton，得到点击的是谁，这个主要是应用于，
		    //同一个监听类多个事件源添加事件，在处理的时候需要知道是谁，如果你的监
		    //听类只有一个事件源，则没必要去做这儿处理。
			String str=inputEnglish.getText();
			if(str.matches(regex)) {
				show.append(str+",");
			}
			else {//弹出警告消息对话框
				JOptionPane.showMessageDialog(this, "您输入了非法字符", "消息对话框", JOptionPane.WARNING_MESSAGE);;
				inputEnglish.setText(null);
			}
		}
	}
}

public class exercise{
	public static void main(String args[]) {
		WindowMess win=new WindowMess();
		win.setTitle("带消息对话框的窗口");
		win.setBounds(80,90,200,300);
	}
}
```
### 10.2 输入对话框
输入对话框包含供公户输入文本的文本框，一个确认和取消按钮，是有模式对话框。当对话框可见时，要求用户输入一个字符串。javax.swing包中的JOptionPane类的静态方法：
```java
public static String showInputDialog(Component parentComponent,Object message,String title,int messageType);
```
参数messageType可取的有效值是JOptionPane中的类常量：
```
ERROR_MESSAGE
INFORMATION_MESSAGE
WARING_MESSAGE
QUESTION_MESSAGE
PLAIN_MESSAGE
```
**在下面的例子中，用户在输入对话框中输入若干个数字，如果单击输入对话框上的确定按钮，程序将计算这些数字的和。**
```java
import java.awt.event.*;
import java.awt.*;
import javax.swing.*;
import java.util.*;

class WindowInput extends JFrame implements ActionListener{
	JTextArea showResult;
	JButton openInput;
	public WindowInput() {
		openInput=new JButton("弹出输入对话框");// TODO Auto-generated constructor stub
		showResult=new JTextArea();
		add(openInput,BorderLayout.NORTH);
		add(new JScrollPane(showResult), BorderLayout.CENTER);
		openInput.addActionListener(this);
		setVisible(true);
		setDefaultCloseOperation(JFrame.EXIT_ON_CLOSE);
	}
	public void actionPerformed(ActionEvent e) {
		String str=JOptionPane.showInputDialog(this, "输入数字，用空格分隔", "输入对话框", JOptionPane.PLAIN_MESSAGE);
		if(str!=null) {
			Scanner scanner=new Scanner(str);
			double sum=0;
			int k=0;
			while(scanner.hasNext()) {
				try {
					double number=scanner.nextDouble();
					if(k==0)
						showResult.append(""+number);
					else
						showResult.append("+"+number);
					sum=sum+number;
					k++;
				}
				catch(InputMismatchException exp) {
					String t=scanner.next();
				}
			}
			showResult.append("="+sum+"\n");
		}
	}
}

public class exercise{
	public static void main(String args[]) {
		WindowInput win=new WindowInput();
		win.setTitle("带输入对话框的窗口");
		win.setBounds(80,90,200,300);
	}
}
```
### 10.3确认对话框
确认对话框是有模式对话框，可以用javax.swing包中的JOptionPane类的静态方法：
```java
public static int showConfirmDialog(Component parentComponent,Object message,String title,int optionType);
```
optionType取下列有效值：
```
JOption.YES_NO_OPTION
JOption.YES_NO_CANCEL_OPTION
JOption.YES_CANCEL_OPTION
```
这些值可以给出对话框的外观，例如，取值：JOptionPane.YES_NO_OPTION时，确认对话框的外观上会有YES和NO两个按钮。当确认对话框消失后，showConfrimDialog方法就会返回下列整数值之一：
```
JOptionPane.YES_OPTION
JOptionPane.NO_OPTION
JOptionPane.CANCEL_OPTION
JOptionPane.OK_OPTION
JOptionPane.CLOSED_OPTION
```
在下面的例子中，用户在为文本框中输入账户名字，按回车键后，将弹出一个确认对话框。如果单击确认对话框上的 是，就将名字放入文本区。
```java
import java.awt.event.*;
import java.awt.*;
import javax.swing.*;

class WindowEnter extends JFrame implements ActionListener{
	JTextField inputName;
	JTextArea save;
	public WindowEnter() {
		inputName=new JTextField(22);// TODO Auto-generated constructor stub
		inputName.addActionListener(this);
		save=new JTextArea();
		add(inputName,BorderLayout.NORTH);
		add(new JScrollPane(save), BorderLayout.CENTER);
		setVisible(true);
		setDefaultCloseOperation(JFrame.EXIT_ON_CLOSE);
	}
	public void actionPerformed(ActionEvent e) {
		String s=inputName.getText();
		int n=JOptionPane.showConfirmDialog(this, "确认是否正确","确认对话框",JOptionPane.YES_NO_CANCEL_OPTION);
		if(n==JOptionPane.YES_OPTION) {//n=0时
			save.append("\n"+s);
		}
		else if(n==JOptionPane.NO_OPTION) {//n=1时
			inputName.setText(null);
		}
	}
}

public class exercise{
	public static void main(String args[]) {
		WindowEnter win=new WindowEnter();
		win.setTitle("带确认对话框的窗口");
		win.setBounds(80,90,200,300);
	}
}
```
### 10.4 颜色对话框
可以用javax.swing包中的JColorChooser类的静态方法：
```java
public static Color showDialog(Component component,String title,Color initialColor);
```
创建一个有模式的颜色对话框，其中参数component指定颜色对话框可见时的位置，颜色对话框再参数component指定的组件正前方显示出来，如果component为null，颜色对话框在屏幕的正前方显示出来。title指定对话框的标题，initialColor指定颜色对话框返回的初始颜色。用户通过颜色对话框选择颜色后，如果单击“确定”按钮，那么颜色对话框将消失，showDialog()方法返回对话框选择的颜色对象；如果单击“撤销”按钮或关闭图标，那么颜色对话框将消失，showDialog()方法返回null.  
```java
import java.awt.event.*;
import java.awt.*;
import javax.swing.*;
class WindowColor extends JFrame implements ActionListener{
	JButton button;
	public WindowColor() {
		button=new JButton("打开颜色对话框");// TODO Auto-generated constructor stub
		button.addActionListener(this);
		setLayout(new FlowLayout());
		add(button);
		setVisible(true);
		setDefaultCloseOperation(JFrame.EXIT_ON_CLOSE);
	}
	public void actionPerformed(ActionEvent e) {
		Color newColor=JColorChooser.showDialog(this, "调色板", getContentPane().getBackground());
		//getContentPane()的作用是初始化一个容器，用来在容器上添加一些控件
		if(newColor!=null) {
			getContentPane().setBackground(newColor);		
		}
	}
}

public class exercise{
	public static void main(String args[]) {
		WindowColor win=new WindowColor();
		win.setTitle("带颜色对话框的窗口");
		win.setBounds(80,90,200,300);
	}
}
```
### 10.5 文件对话框
文件对话框是一个从文件中选择文件的界面，javax.swing包中的JFileChooser类可以创建文件对话框，使用该类的构造方法JFileChooser()创建初始不可见的有模式的文件对话框。然后用文件对话框调用下述两个方法：
```java
showSaveDialog(Component a);
showOpenDialog(Component a);
```
都可以使对话框可见，只是呈现的外观有所不同，showSaveDialog方法提供保存文件的界面，showOpenDialog方法提供打开文件的界面。上述两个方法中的参数a指定对话框可见时的位置，当a是null时，文件对话框出现在屏幕的中央；如果组件a不为空，文件对话框在组件a的正前面居中显示。

用户单击文件对话框上的 确定，取消按钮或关闭图标，文件对话框将消失。ShowSaveDialog()或showOpenDialog()方法返回下列常量之一：

```java
JFileChooser.APPROVE_OPTION
JFileChooser.CANCEL_OPTION
```
### 10.6 自定义对话框
创建对话框与创建窗口类似，通过建立JDialog的子类来建立一个对话框类，然后这个类的一个实例，即这个子类创建的一个对象，就是一个对话框。对话框是一个容器，它的默认布局是BorderLayout，对话框可以添加组件，实现与用户的交互操作。需要注意的是，对话框可见时，默认的被系统添加到显示器屏幕上，因此不允许将一个对话框添加到另一个容器中。以下是构造对话框的2个常用构造方法。  
- JDialog()构造一个无标题的初始不可见的对话框，对话框依赖一个默认的不可见的窗口，该窗口由Java运行环境提供。
- JDialog(JFrame owner)构造一个无标题的初始不可见的无模式的对话框，owner时对话框依赖的窗口，如果owner取null，对话框依赖一个不可见的窗口，该窗口由Java运行环境支持。
