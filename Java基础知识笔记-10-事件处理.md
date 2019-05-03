Java基础知识笔记-10-事件处理

# 事件处理
学习组件除了要熟悉组建的属性和功能外，一个更重要的方面是学习怎样处理组建上发生的界面事件，当用户在文本框中输入文本后按回车，单击按钮，在一个下拉式列表中选择一个条目进行一个条目等操作时，都发生界面事件，例如，用户单击一个确定或者取消的按钮，程序可能需要做出不同的处理。
## 1 事件处理模式
在学习处理事件时,必须很好地掌握事件源、监视器、处理事件的接口这三个概念。
##### 1.事件源
能够产生事件的对象都可以成为事件源，如文本框、按钮、下拉式列表等。
也就是说，事件源必须是一个对象，而且这个对象必须是Java认为能够发生事件的对象。
##### 2.监视器
我们需要一个对象对事件源进行监视，以便对发生的事件作出处理。
事件源通过调用相应的方法将某个对象注册为自己的监视器。例如，对于文本框,这个方法是:
```
addActionListener(监视器);
```
对于注册了监视器的文本框，在文本框获得输人焦点后,如果用户按回车键,Java运行环境就自动用Actionven类创建一个对象，即发生了ActionEvent事件。也就是说，事件源注册监视器之后，相应的操作就会导致相应的事件地发生，并通知监视器，监视器就会出相应的处理。
##### 3.处理事件的接口
监视器负责处理事件源发生的事件。监视器是一个对象，为了处理事件源发生的事件，监视器这个对象会自动调用一个方法来处理事件。那么监视器去调用哪个方法呢?我们我知道，对象可以调用创建它的那个类中的方法，那么它到底调用该类中的哪个方法呢？Java规定为了让监视器这个对象能对事件源发生的事件进行处理，创建该监视器对象的以面市明实现相应的接口，即必须在类体中重写接口中的所有方法，那么当事件源发生事，监视器就自动调用被类重写的某个接口方法。事件处理模式如图11.6所示。

```
graph LR
A[事件源.addXXXListener]-->|发生|B[XXX事件]
B[XXX事件]-->|通知|A
A-->|监视器回调接口方法|D[class A implements XXXListener 接口方法]
E[类A负责创建监视器,A必须实现XXXListener接口]---A[事件源.addXXXListener]
```
## 2 ActionEvent事件
##### 1.ActionEvent事件源
文本框、按钮、菜单项、密码框和单选按钮都可以触发ActionEvent事件，即都可以成为ActionEvent事件的事件源。例如，对于注册了监视器的文本框，在文本框获得输人焦点后，如果用户按回车键，Java运行环境就自动用ActionEvent类创建一个对象，即触发ActionEvent事件;对于注册了监视器的按钮，如果用户单击按钮，就会触发ActionEvent事件;对于注册了监视器的菜单项，如果用户选中该菜单项，就会触发ActionEvent事件;如果用户选择了某个单选按钮，就会触发ActionEvent事件。
##### 2.注册监视器
能触发ActionEvent事件的组件使用
```
addActionListener(ActionListener listen);
```
将实现ActionListener接口的类的实例注册为事件源的监视器。
##### 3.ActionListener 接口
ActionListener接口在java.awt.event包中，该接口中只有一个方法:
```
public void actionPerforned(ActionEvente);
```
事件源触发ActionEvent事件后，监视器将发现触发的ActionEvent事件，然后调用接口中的方法:
```
actionPerformed(ActionEvent e);
```
对发生的事件作出处理。当监视器调用actionPerformed(ActionEvent e)方法时，ActionEvent类事先创建的事件对象就会传递给该方法的参数C。
##### 4.ActionEvent类中的方法
ActionEvent类有如下常用的方法。
```
public ObeceSurce();该方法是从Enentobiect继承的方法，ActionEvent事件对象调用
该方法可以获取发生ActionEvent事件的事件源对象的引用，即BetSource()方法将事件
源上转型为Object对象，并返回这个上转型对象的引用，
public String getActionCommand();ActionEvent对象调用该方法可以获取发生
ActionEvent事件时，和该事件相关的一个命令字符串，对于文本框，当发生ActionEvent
事件时，文本框中的文本字符串就是和该事件相关的一个命令字符串.
```
例11.5处理文本框上触发的ActionEvent事件。在文本框text中输人字符串回车，监视器负责计算字符串的长度，并在命令行窗口显示字符串的长度。例11.5程序运行效果如图11.7和图11.8所示。
```
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
```
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

public class exercise{
	public static void main(String args[]) {
		WindowActionEvent win=new WindowActionEvent();
		win.setBounds(100,100,460,360);
		win.setTitle("处理ActionEvent事件");
	}
}
```
Java的事件处理是基于授权模式，即事件源调用方法将某个对象注册为自己的监视器。
**处理相应的事件调用相应的接口**
## 3 ItemEvent事件
##### 1.ItemEvent事件源
选择框，下拉列表都可以触发ItemEvent事件。选择框提供两种状态，一种是选中，另一种是未选中。对于注册了监视器的选择框，当用户的操作使得选择框从未选中状态变成选中状态，或是相反变化的时候就会触发ItemEvent事件，同样，对于注册了监视器的下拉列表，如果用户选中下拉列表中的某个选项，就会触发ItemEvent事件。
##### 2.注册监视器
能触发ItemEvent事件的组件使用
```
addItemListener(ItemListener listen);
```
将实现ItemListener接口的类的实例注册为事件源的监视器。
##### 3.ItemListener接口
ItemListener接口在java.awt.event包中，该接口只有一个方法;
```
public void itemStateChanged(itemEvent e);
```
事件触发ItemEvent事件后，监视器将发现触发的ItemEvent事件，然后调用接口中的方法：
```
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
```
public void changedUpdate(DocumentEvent e);
public void removeUpdate(DocumentEvent e);
public void insertUpdate(DocumentEvent e);
```
事件源触发DocumentEvent事件后，监视器将发现触发的DocumentEvent事件，然后调用接口中的相应方法对发生的事件做出处理。  

在下面的例子中，在左边输入若干英文单词，另一个文本区会自动对输入的英文单词按照字典顺序排序，也就是说随着用户输入的变化，另一个文本区不断地更新排序。
```
import java.awt.event.*;
import java.io.*;
import javax.swing.event.*;
import javax.swing.*;
import java.util.*;
class PoliceListen implements DocumentListener{
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
任何组件都可以发生鼠标事件，如鼠标进入组件，退出组件，在组建上方单击鼠标，拖动鼠标等都可以触发鼠标事件，即导致MouseEvent类自动创建一个事件对象。
##### 1.使用MouseEvent接口处理鼠标事件
使用MouseListener接口可以处理以下5种操作触发的鼠标事件。
- 在事件源上按下鼠标键
- 在事件源上释放鼠标键
- 在事件源上单击鼠标键
- 鼠标进入事件源
- 鼠标退出事件源
MouseEvent中有下列几个重要方法：
```
getX();//获取鼠标指针在事件源坐标系中的x坐标
getY();//获取鼠标指针在事件源坐标系中的y坐标
getModifiers();//获取鼠标的左键或右键。鼠标的左键和右键分别使用InputEvent类中的常量
BUTTON1_MASK,BUTTON3_MASK来表示；
getClickCount();//获取鼠标被单击的次数
getSource();//获取鼠标事件的事件源
```
事件源注册监视器的方法是addMouseListener(MouseListener listener) MouseListener接口中有如下方法：
```
mousePressed(MouseEvent);//负责处理在组件上按下鼠标键触发的鼠标事件。即
当你在事件源按下鼠标键时监视器调用接口中的这个方法对事件作出处理。

mouseReleased(MouseEvent);//负责处理在组件上释放鼠标键触发的鼠标事件。即
当你在事件源释放鼠标键时，监视器调用接口中的这个方法对事件作出处理。

mouseEntered(MouseEvent);//负责处理鼠标进人组件触发的鼠标事件。即
当鼠标指针进人组件时，监视器调用接口中的这个方法对事件作出处理。

mouseExited(MouseEvent);//负责处理鼠标离开组件触发的鼠标事件。即
当鼠标指针离开容器时，监视器调用接口中的这个方法对事件作出处理。

mouseClicked(MouseEvent);//负责处理在组件上单击鼠标键触发的鼠标事件。即
当单击鼠标时，监视器调用接口中的这个方法对事件做出处理。
```
## 6 焦点事件
组件可以触发焦点事件。组件可以使用
```
addFocusListener(FocusListener listener);
```
注册焦点事件监视器，当组件获得焦点监视器后，如果组件从无输入焦点变成有输入焦点或从有输入焦点变成无输入焦点都会触发FocusEvent事件。创建监视器的类必须要实现FocusListener接口，该接口有两个方法：
```
public void focusGained(FocusEvent e);//监视器从无焦点输入变成有焦点输入
public void focusLost(FocusEvnet e);//监视器从有焦点输入变成无焦点输入
```
用户通过单击组件可以使得组件有输入焦点，同时也使得其他组件变成无输入焦点。
一个组件也可调用
```
public boolean requestFocusInWindow();
```
方法可以获得输入焦点。
## 7 键盘事件
当按下，释放或敲击键盘上一个键时就就触发了键盘事件，在Java事件模式中，必须要有发生事件的事件源。当一个组件处于激活状态时，敲击键盘上一个键就导致这个组件触发键盘事件。当使用KeyListener接口处理键盘事件，有如下3个方法：
```
public void keyPessed(KeyEvent e);
public void keyTyped(KeyEvent e);
public void keyReleased(KeyEvent e);
```
某个组件使用addListener方法注册监视器后，当该组件处于激活状态时，用户按下键盘上的某个键时，触发keyEvent事件，监视器调用keyPressed方法；用户释放键盘上按下的键时，触发keyReleased方法。keyTyped方法是keyPressed和keyReleased方法的组合，当键按下又释放时，监视器调用keyTyped方法。  

用KeyEvent类的
```
public int getKeyCode();
```
方法，可以判断哪个键被按下，敲击或释放，getKeyCode方法返回一个键码值，也可以用KeyEvent类的
```
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
VK_INSERT|插人键
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
当安装某些软件时，经常要求输人序列号码，并且要在几个文本框中依次输入。每个文本框中输入的字符数目都是固定的，当在第一个文本框输入了恰好的字符个数后,输人光标会自动转移到下一个文本框。例11. 11通过处理键盘事件来实现软件序列号的输入。当文本框获得输入焦点后，用户敲击键盘将使得当前文本框输入序列号触发KeyEvent事件，在处理事件时，程序检查文本框中光标的位置，如果光标已经到达指定位置，就将输入焦点转移到下一个文本框。程序运行效果如图11.12所示.
```
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

class Win extends JFrame{
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

从面向对象的角度看，MVC结构可以使程序更具有对象化特性，也更容易维护，
**==在设计程序时，可以将某个对象看作模型，然后为模型提供恰当的显示组件，即视图，为了对用户的操作做出响应，可以选择某个组件做控制器，当组件发生事件后，通过视图修改得到模型中维护着的数据，并让视图更新显示。==**  

如下例：首先编写一个封装三角形的类，然后再编写一个窗口。要求窗口使用三个文本框和一个文本区域为三角形对象中的数据提供视图，其中三个文本框用来显示和更新三角形对象的三个边的长度；文本区对象用来显示三角形的面积。窗口中有一个按钮，用户单击该按钮后，程序用三个文本框中的数据分别作为三角形的三个边的长度，并将计算结果显示在文本区。
```
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
```
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
```
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
```
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
```
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
```
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
```
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
```
public static Color showDialog(Component component,String title,Color initialColor);
```
创建一个有模式的颜色对话框，其中参数component指定颜色对话框可见时的位置，颜色对话框再参数component指定的组件正前方显示出来，如果component为null，颜色对话框在屏幕的正前方显示出来。title指定对话框的标题，initialColor指定颜色对话框返回的初始颜色。用户通过颜色对话框选择颜色后，如果单击“确定”按钮，那么颜色对话框将消失，showDialog()方法返回对话框选择的颜色对象；如果单击“撤销”按钮或关闭图标，那么颜色对话框将消失，showDialog()方法返回null.  
```
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
```
showSaveDialog(Component a);
showOpenDialog(Component a);
```
都可以使对话框可见，只是呈现的外观有所不同，showSaveDialog方法提供保存文件的界面，showOpenDialog方法提供打开文件的界面。上述两个方法中的参数a指定对话框可见时的位置，当a是null时，文件对话框出现在屏幕的中央；如果组件a不为空，文件对话框在组件a的正前面居中显示。  
用户单击文件对话框上的 确定，取消按钮或关闭图标，文件对话框将消失。ShowSaveDialog()或showOpenDialog()方法返回下列常量之一：
```
JFileChooser.APPROVE_OPTION
JFileChooser.CANCEL_OPTION
```
### 10.6 自定义对话框
创建对话框与创建窗口类似，通过建立JDialog的子类来建立一个对话框类，然后这个类的一个实例，即这个子类创建的一个对象，就是一个对话框。对话框是一个容器，它的默认布局是BorderLayout，对话框可以添加组件，实现与用户的交互操作。需要注意的是，对话框可见时，默认的被系统添加到显示器屏幕上，因此不允许将一个对话框添加到另一个容器中。以下是构造对话框的2个常用构造方法。  
- JDialog()构造一个无标题的初始不可见的对话框，对话框依赖一个默认的不可见的窗口，该窗口由Java运行环境提供。
- JDialog(JFrame owner)构造一个无标题的初始不可见的无模式的对话框，owner时对话框依赖的窗口，如果owner取null，对话框依赖一个不可见的窗口，该窗口由Java运行环境支持。
