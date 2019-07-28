Java基础知识笔记-11-Swing用户界面组件
> *这章教程两个版本，一个语法是非lambda表达式版本，另一个是lambda表达式版本*

> # 非lambda表达式版本
# 1 Java Swing概述
Java的java.awt包，即抽象窗口工具包JDK1.2推出后，增加了一个新的javax.swing包，该包提供了更为强大的GUI类，这两个包中一部分类的层次关系的UML类如下图所示

![avatar](
https://github.com/whatsabc/java-basic-notes/blob/master/%E6%8F%92%E5%9B%BE/Component%E7%9A%84%E7%B1%BB%E5%B1%82%E6%AC%A1%E7%BB%93%E6%9E%84.jpg?raw=true)

>Component类的部分子类

学习GUI编程时，必须很好的了解两个概念：容器类(Container)和组建类(Component)。javax.swing包中JComponent类是java.awt包中Container类的一个直接子类，是Component类的一个间接子类，学习GUI编程主要是学习掌握使用Component类的一些重要的子类。  

以下是GUI编程的一些基本知识点：  
- *Java可以把component类的子类或间接自类创建的对象称为一个组件。*  
- *Java可以把container的子类或间接子类创建的对象称为一个容器。*  
- 可以向容器中添加组件。container类提供了一个public方法`add()`,一个容器可以调用这个方法将组建添加到该容器中。  
- 容器调用`removeAll()`方法可以以掉容器中的全部组件，调用`remove(Component c)`方法可以移掉容器中参数C指定的组件。  
- **注意到容器本身也是一个组件，因此可以把一个容器添加到另一个容器中实现容器的嵌套。**
# 2 窗口
一个基于GUI的应用程序应当提供一个能和操作系统直接交互的容器，该容器可以被直接显示、绘制在操作系统控制的平台上，例如显示器上,这样的容器被称作GUI设计中的底层容器。  

Java提供的JFrame类的实例就是一个底层容器(JDialog类的实例也是一个底层容器，见后面的11.6节)，即通常所说的窗口。其他组件必须被添加到底层容器中,以便借助这个底层容器和操作系统进行信息交互。简单地讲，如果应用程序需要一个按钮，并希望用户和按钮交互，即用户单击按钮使程序做出某种相应的操作，那么这个按钮必须出现在底层容器中，否则用户无法看见按钮,更无法让用户和按钮交互。  

**JFrame类是Container类的间接子类。当需要一个窗口时，可使用JFrame或其子类创建个对象。** 窗口也是个容器,可以向窗口添加组件。需要注意的是，窗口默认地被系统添加到显示器屏幕上,因此不允许将一个窗口添加到另一个容器中。
## 2.1 JFrame常用方法
- `JFrame()`: 创建一一个无标题的窗口。
- `JFrame(Strings)`: 创建标题为s的窗口。  
- `public void setisble(boolean b)`:设置窗口是否可见。窗口默认是不可见的。  
- `public void dispose()`:撒销当前窗口，并释放当前窗口所使用的资源。
- `public void setDefaultCloseOperation(int operation)`:该方法用来设置单机窗体右上角的关闭图标后，程序会做出怎样的处理，其中的参数operation取JFrame类中的下列int型static常量，程序根据参数operation取值做出不同的处理。 
`HIDE_ON_CLOSE`：什么也不做 
`DISPOSE_ON_CLOSE`：隐藏当前窗口，并释放窗体占有的其他资源。 
`EXIT_ON_CLOSE`：结束窗口所在的应用程序  
```java
import javax.swing.*;
import java.awt.*;
public class exercise{
	public static void main(String args[]) {
		JFrame windows1=new JFrame("The first windows");
		JFrame windows2=new JFrame("The second windows");
		Container con=windows1.getContentPane();
		con.setBackground(Color.yellow);
		windows1.setBounds(60,100,188,108);
		windows2.setBounds(260,00,188,108);
		windows1.setVisible(true);
		windows1.setDefaultCloseOperation(JFrame.DISPOSE_ON_CLOSE);
		windows2.setVisible(true);
		windows2.setDefaultCloseOperation(JFrame.EXIT_ON_CLOSE);
	}
}
```
注意两次单击关闭窗口，程序运行效果不同。
## 2.2 菜单条，菜单，菜单项
窗口中的菜单项放在菜单里，菜单放在菜单条里。
##### 1.菜单条
**JComponent类的子类`JMenubar`负责创建菜单条，即JMenubar的一个实例就是一个菜单条，JFrame类有一个将菜单条放置到窗口的方法;**
```java
setJMenuBar(JMenuBar bar);
```
该方法将菜单条添加到窗口的顶端，需要注意的是，只能向窗口中添加一个菜单条。
##### 2.菜单
JComponent类的子类`JMenu`负责创建菜单，即JMenu的一个实例就是一个菜单。
##### 3.菜单项
JComponent类的子类`JMenuItem`负责创建菜单项，即JMenuItem的一个实例就是一个菜单项。
##### 4.嵌入子菜单
JMenu是`JMenuItem`的子类，因此菜单本身也是一个菜单项，当把一个菜单看作菜单项添加到某个菜单中，称这样的菜单为子菜单
##### 5.菜单上的图标
我们先用Icon声明一个图标，然后使用其子类ImageIcon类创建一个图标，如：
```java
Icon icon=new ImageIcon("a.gif");
```
然后菜单项调用`setlcon(Icon icon)`方法将图标设置为icon. 

例11.2中在主类Examplell_ 2的main 方法中，用Frame的子类WindowMenu创建一个含有菜单的窗口，效果如图11.3所示。

```java
import javax.swing.*;
import java.awt.event.InputEvent;
import java.awt.event.KeyEvent;
import static javax.swing.JFrame.*;

class WindowMenu extends JFrame {
    JMenuBar menubar;
    JMenu menu,subMenu;
    JMenuItem item1, item2;
    public WindowMenu(){
    }
    public WindowMenu(String s,int x,int y,int w,int h) {
        init(s);
        setLocation(x,y);
        setSize(w,h);
        setVisible(true);
        setDefaultCloseOperation(DISPOSE_ON_CLOSE);
    }
    void init(String s){
        setTitle(s);
        menubar=new JMenuBar();
        menu=new JMenu("菜单");
        subMenu=new JMenu("软件项目");
        item1=new JMenuItem("java话题",new ImageIcon("a.gif"));
        item2=new JMenuItem("动画话题",new ImageIcon("b.gif"));
        item1.setAccelerator(KeyStroke.getKeyStroke('A'));
        item2.setAccelerator(KeyStroke.getKeyStroke(KeyEvent.VK_S,InputEvent.CTRL_MASK));
        menu.add(item1);
        menu.addSeparator();
        menu.add(item2);
        menu.add(subMenu);//把sunmenu作为menu的一个菜单项；
        subMenu.add(new JMenuItem("汽车销售系统",new ImageIcon("c.gif")));
        subMenu.add(new JMenuItem("农场管理系统",new ImageIcon("d.gif")));
        menubar.add(menu);
        setJMenuBar(menubar);
    }
}

public class exercise {
    public static void main(String args[]) {
        WindowMenu win=new WindowMenu("带菜单的窗口",20, 30,200,190);
    }
}
```
# 3 常用组件与布局
可以使用JComponent的子类创建各种组件，利用组件可以完成应用程序与用户的交互。
## 3.1 常用组件
##### 1.文本框
使用JComponent的子类`JTestField`创建文本框，允许用户在文本框输入单行文本。
##### 2.文本区
使用JComponent的子类`JButton`类创建按钮，允许用户单击按钮
##### 4.标签
使用JComponent的子类`JLabel`类创建标签，允许用户单击按钮。
##### 5.选择框
使用JComponent的子类`JCheckBox`类创建选择框，为用户提供多种选择。选择框有两种状态，一种是选中，另一种是未选中，用户通过单该组件切换状态。
##### 6.单选按钮
使用JComponent的子类`JRadioButton`类创单项选择框，为用户提供单项选择。
##### 7.下拉列表
使用JComponent的子类`JComboBox`类创建下拉列表，为用户提供单项选择，用户可以在下拉列表看到第一个选项和他旁边的箭头按钮，当用户单击按钮箭头时，选项列表打开。
##### 8.密码框
可以使用JComponent的子类`JPasswordField`创建密码框，密码框可以使用
```
setEchoChar(char c)
```
方法重新设置回显字符，用户输入密码时，密码框只显示回显字符。  

密码框调用
```java
char[]getPassword()
```
方法可以返回实际的密码。
```java
import java.awt.*;
import javax.swing.*;

class ComponentInWindow extends JFrame{
	JTextField text;
	JButton button;
	JCheckBox checkBox1,checkBox2,checkBox3;
	JRadioButton radio1,radio2;
	ButtonGroup group;
	JComboBox comBox;
	JTextArea area;
	public ComponentInWindow() {
		init();
		setVisible(true);
		setDefaultCloseOperation(JFrame.EXIT_ON_CLOSE);
	}
	void init() {
		setLayout((new FlowLayout()));
		add(new JLabel("文本框"));
		text=new JTextField(10);
		add(text);
		add(new JLabel("按钮"));
		button=new JButton("确定");
		add(button);
		add(new JLabel("选择框"));
		checkBox1=new JCheckBox("喜欢音乐");
		checkBox2=new JCheckBox("Like basketball");
		checkBox3=new JCheckBox("like travel");
		add(checkBox1);
		add(checkBox2);
		add(checkBox3);
		add(new JLabel("单选按钮"));
		group=new ButtonGroup();
		radio1=new JRadioButton("男");
		radio2=new JRadioButton("女");
		group.add(radio1);
		group.add(radio2);
		add(new JLabel("下拉列表"));
		comBox=new JComboBox<>();
		comBox.addItem("选项1");
		comBox.addItem("选项2");
		comBox.addItem("选项3");
		add(comBox);
		add((new JLabel("文本区")));
		area=new JTextArea(6,12);
		add(new JScrollPane(area));
	}
}
public class exercise{
	public static void main(String args[]) {
		ComponentInWindow win=new ComponentInWindow();
		win.setBounds(100,100,310,260);
		win.setTitle("常用组件");
	}
}
```
## 3.2 常用容器
JComponent是Container的子类，因此JComponent子类创建的组建也都是容器，但是我们很少将`JButton,JTeseField,JCheckBox`等组件当容器来使用。JComponent专门提供了一些经常用来添加组件的容器，相对于JFrame底层容器，本节提到的容器习惯性被称为中间容器，中间容器必须被添加到底层容器中才会发生作用

##### 1.JPanel面板
我们经常会使用JPlanel创建一个面板，然后再向这个面板添加组件，然后把这个面板添加到其他的容器中。JPlanel面板的默认布局是FlowLayout布局。
##### 2.滚动窗格JSrollPane
滚动窗格只可以添加一个组件，可以把一个组件放到一个滚动窗格中，然后通过滚动条来操作该组件，JTextArea不自带滚动条，因此我们需要把文本区放入到一个滚动窗格中，例如：
```java
JScrollPane scroll=new JScrollPane(new JTextArea());
```
##### 3.拆分窗格ISplitPane
顾名思义,拆分窗格就是被分成两部分的容器,拆分窗格有两种类型:水平拆分和垂直拆分。水平拆分窗格用一条拆分线把窗格分成左右两部分,左面放一个组件,右面放一组件,拆分线可以水平移动。垂直拆分窗格用一条拆分线把窗格分成上下两部分,上面放个组件,下面放一个组件,拆分线可以垂直移动

JSplitPane的两个常用的构造方法:

```java
JSplitPane(int a, Component b, Component c);
```
>参数a取JSplitPane的静态常量HORIZONTAL_SPLIT或VERTICAL_SPLIT,以解决水平还是垂直拆分,后两个参数决定要放置的组件,当拆分线移动时,组件不是连续变化的
```java
JSplitPane(int a, boolean b, Component c, Component d);
```
参数a取JSplitPane的静态常量HORIZONTAL_SPLIT或VERTICAL_SPLIT,以决定是水平还是垂直拆分。参数b决定当拆分线移动时,组件是否连续变化(true是连续),后两个参数决定要放置的组件, SPlit Pane拆分窗格还可以调用setDividerlocation(int)方法修改拆分线的初始位置
##### 4 JLayeredPane分层窗格
如果添加到容器中的组件经常需要处理重叠问题,就可以考虑将组件添加到分层窗格分层窗格分成5个层,分层窗格使用
```java
add(Component com, int layer);
```
添加组件com,并指定com所在的层,其中参数layer取值JLayeredPane类中的类常量
```
DEFAULT_LAYER、 PALETTE_LAYER、 MODAL_LAYER、 POPUP_LAYES、 DRAG_LAYER
```
DEFAULT LAYER是最底层,添加到DEFAULT_LAYER层的组件如果和其他层的组件发生重叠时,将被其他组件遮挡。 DRAG_LAYER层是最上面的层,如果分层窗格中添加了许多组件,当用户用鼠标移动一个组件时,可以把该组件放到DRAG__LAYER层

这样,用户在移动组件过程中,该组件就不会被其他组件遮挡。添加到同一层上的组件,如发生重叠,后添加的会遮挡先添加的组件。分层窗格调用

```java
public void setLayer(Component c, int layer);
```
可以重新设置组件c所在的层,调用
```java
public int getlayer( Component c)
```
可以获取组件c所在的层数
## 3.3常用布局
当把组件添加到容器中时,希望控制组件在容器中的位置,这就需要学习设计的知识。本节将分别介绍java.awt包中的`FlowLayout` `BorderLayout` `Cardlayout` `GridLayout`布局  

容器可以使用方法`setLayout(布局对象);`设置自己的布局
##### 1.FlowLayout布局
FlowLayout类创建的对象称作FlowLayout型布局。FlowLayout型布局是JPanel容器的默认布局,即JPanel及其子类创建的容器对象,如果不专门为其指定布局,则它们的布局就是Flow Layout型布局

Flow Layout类的一个常用构造方法如下,该构造方法可以创建一个居中对齐的布局对象。例如:

```java
FlowLayout flow=new FlowLayout();
```
如果一个容器con使用这个布局对象
```java
con.setLayout(flow);
```
那么,con可以使用Container类提供的add方法将组件顺序地添加到容器中,组件按照加入的先后顺序从左向右排列,一行排满之后就转到下一行继续从左至右排列,每一行中的组件都居中排列,组件之间的默认水平和垂直间隙是5个像素,组件的大小为默认的最住大小,例如,按钮的大小刚好能保证显示其上面的名字。对于添加到使用Flowlayout布局的容器中的组件,组件调用`setSize(int x,int y);`设置的大小无效,如果需要改变最佳大小,组件需调用
```java
public void setPreferredsize(Dinension preferredsize);
```
设置大小,例如:
```java
button.setPreferredsize(new Dinension(20,20));
```
FlowLayout布局对象调用
```java
setAliginmen(int aligin);
```
方法可以重新设置布局的对齐方式,其中aligin可以取值`ElowLayout.LEFT,FlowLayout.CENTER,FlowLayout.RIGHT`  

FlowLayout布局对象调用`setHgap(int hgap)`方法和`setVgap(intvgap)`可以重新设置水平间隙和垂直间隙。
##### 2.BorderLayout布局
Borderlayout布局是window型容器的默认布局,例如JFrame、JDialog都是Window类的子类,它们的默认布局都是BorderLayout布局,Borderlayout也是一种简单的布局策略,如果一个容器使用这种布局,那么容器空间简单地划分为东、西、南、北、中5个区域,中间的区域最大。每加入一个组件都应该指明把这个组件加在哪个区域中,区域由Borderlayout中的静态常量CENTER、NORTH、SOUTH、WEST、EAST表示,例如,一个使用BorderLayout布局的容器con,可以使用add方法将一个组件b添加到中心区域
```java
con.add(b,BorderLayout.CENTER);
或者：
on.add(BorderLayour.CENTER,b);
```
添加到某个区域的组件将占据整个这个区域,每个区域只能放置一个组件,如果向某个已放置了组件的区域再放置一个组件,那么先前的组件将被后者替换掉,使用BorderLayout布局的容器最多能添加5个组件,如果容器中需要加入超过5个组件,就必须使用容器的嵌套或改用其他的布局策略.
##### 3.CardLayout布局
使用CardLayout的容器可以容纳多个组件,这些组件被层叠放入容器中,最先加入容器的是第一张(在最上面),依次向下排序,使用该布局的特点是,同一时刻容器只能从这些件中选出一个来显示,就像叠“扑克牌”,每次只能显示其中的一张,这个被显示的组件将占据所有的容器空间  

假设有一个容器con,那么,使用Cardlayout的一般步骤如下 

- 创建CardLayout对象作为布局,如
```java
Cardlayout card=new Cardlayout();
```
- 使用容器的setlayout()方法为容器设置布局,如
```java
con.setLayout(card);
```
- 容器调用`add(String s,Component b)`将组件b加入容器,并给出了显示该组件的代号s。组件的代号是一个字符串,和组件的名没有必然联系,但是,不同的组件代号必须互不相同。最先加入con的是第一张,依次排序.
- 创建的布局card用Cardlayout类提供的`show()`方法,显示容器con中组件代号为s的组件`card.show(con,s)`;

也可以按组件加入容器的顺序显示组件: 

`card.first(con)`显示con中的第一个组件; 

`card.last(con)`显示con中最后一个组件; 

`card.next(con)`显示当前正在被显示的组件的下个组件; 

`card.previous(con)`显示当前正在被显示的组件的前一个组件

##### 4.GridLayout布局

GridLayout是使用较多的布局编辑器,其基本布局策略是把容器划分成若干行乘若干列的网格区域,组件就位于这些划分出来的小格中, Gridlayout比较灵活,划分多少网格由程序自由控制,而且组件定位也比较精确,使用GridLayout布局编辑器的一般步骤如下

使用 GridLayout的构造方法
```java
GridLayout(int m,int n);
```
创建布局对象,指定划分网格
的行数m和列数n,例如
```java
Gridlayout grid= new Gridlayout(10,8);
```
使用 GridLayout布局的容器调用方法`add(Component c)`将组件c加入容器,组件进入容器的顺序将按照第一行第一个、第一行第二个行最后一个、第二行第一个、…、最后一行第一个、…、最后一行最后一个.  

使用GridLayout布局的容器最多可添加m×n个组件,GridLayout布局中每个网格都是相同大小并且强制组件与网格的大小相同.  

由于Gridlayout布局中每个网格都是相同大小并且强制组件与网格的大小相同,使得容器中的每个组件也都是相同的大小,显得很不自然。为了克服这个缺点,你可以使用容器套,如,一个容器使用GridLayout布局,将容器分为三行一列的网格,那么你可以把另个容器添加到某个网格中,而添加的这个容器又可以设置为GridLayout布局、FlowLayout布局、CarderLayout布局或BorderLayout布局等,利用这种嵌套方法,可以设计出符合定需要的布局.

## 3.4 选项卡窗格
JTabbedPane创建的对象也是一个容器,由于JTabbedPane在设计GUl程序时比较方便实用,所以单独列出一小节来讲解。  

JTabbedPane创建的对象称为选项卡窗格。JTabbedPane窗格的默认布局是CardLayour布局,并且自带一些选项卡(不需用户添加),这些选项卡与用户添加到JTabbedPane窗格中的组件相对应,也就是说,当用户向JTabbedPane窗格添加一个组件时,JTabbedPane窗格就会自动指定给该组件一个选项卡,单击该选项卡,JTabbedPane窗格将显示对应的组件,选项卡窗格自带的选项卡默认地在该选项卡窗格的顶部,从左向右依次排列,选项卡的顺序和对应的组件的顺序相同。  

JTabbledPane窗格可以使用
```java
add(String text,Component c);
```
方法将组件c添加到JTabbedPane窗格中,并指定和组件c对应的选项卡的文本提示text,使用 JTabbedPane窗格的构造方法
```java
public JTabbedPane(int tabPlacenent);
```
创建的选项卡窗格的选项卡的位置由参数tabPlacement指定,该参数的有效值为`JTabbedPane.TOP`, `JTabbedPane.BOTTOM`, `JTabbedPane.LEFT`和`JTablePane.RIGHT ` 

> 例11.4的 Example14窗口中有一个选项卡窗格,选项卡窗格中又添加了3个不同布局的面板FlowLayoutJPanel,BorderlayoutJPanel和GridLayoutJPanel并设置了相对应的选项卡的文本提示.

```java
import javax.swing.*;
import java.awt.*;
class FlowLayoutJPanel extends JPanel{//JPlane型容器默认布局
	FlowLayoutJPanel(){
		add(new JLabel("FlowLayout布局的面板"));
		add(new JButton(new ImageIcon("dog.jpg")));
		add(new JScrollPane(new JTextArea(12,15)));
	}
}
class GridLayoutJPlanel extends JPanel {//使用较多的布局方式
	public GridLayoutJPlanel() {
		GridLayout gird=new GridLayout(12,12);
		setLayout(gird);
		Label label[][]=new Label[12][12];
		for(int i=0;i<12;i++) {
			for(int j=0;j<12;j++) {
				label[i][j]=new Label();
				if((i+j)%2==0) {
					label[i][j].setBackground(Color.black);
				}
				else
					label[i][j].setBackground(Color.white);
				add(label[i][j]);				
			}
		}
	}
}
class BorderLayoutJPanel extends JPanel{//Windows默认布局
	JButton bSouth,bNorth,bEast,bWest;
	JTextArea bCenter;
	public BorderLayoutJPanel() {
		setLayout(new BorderLayout());// TODO Auto-generated constructor stub
		bSouth=new JButton("南");
		bNorth=new JButton("北");
		bEast=new JButton("东");
		bWest=new JButton("西");
		bCenter=new JTextArea("中心");
		add(bNorth, BorderLayout.NORTH);
		add(bSouth, BorderLayout.SOUTH);
		add(bEast, BorderLayout.EAST);
		add(bWest, BorderLayout.WEST);
		add(bCenter, BorderLayout.CENTER);
		validate();
	}
}
public class exercise extends JFrame{
	JTabbedPane p;
	public exercise(){
		setBounds(100,100,500,300);
		setVisible(true);
		p=new JTabbedPane(JTabbedPane.LEFT);
		p.add("观看FlowLayout", new FlowLayoutJPanel());
		p.add("观看GrilLayot", new GridLayoutJPlanel());
		p.add("观看BorderLayout", new BorderLayoutJPanel());
		p.validate();//确保组件具有有效的布局
		add(p,BorderLayout.CENTER);
		validate();//确保组件具有有效的布局
		setDefaultCloseOperation(JFrame.DISPOSE_ON_CLOSE);
	}
	public static void main(String args[]) {
		new exercise();
	}
}
```
> # lambda表达式版本

上一章主要介绍了如何使用Java中的事件模式。通过学习读者已经初步知道了构造图形用户界面的基本方法。本章将介绍构造功能更加齐全的图形用户界面(GUI) 所需要的一些重要工具。下面，首先介绍Swing的基本体系结构。要想弄清如何有效地使用一些更高级的组件，必须了解底层的东西。然后，再讲述Swing中各种常用的用户界面组件，如文本框、单选按钮以及菜单等。接下来，介绍在不考虑特定的用户界面观感时，如何使用Java中的布局管理 器排列在窗口中的这些组件。最后，介绍如何在Swing中实现对话框。本章囊括了基本的Swing组件，如文本组件、按钮和滑块等，这些都是基本的用户界面组件，使用十分频繁。Swing中的高级组件将在卷Ⅱ中讨论。

# 1 Swing 和模型-视图-控制器设计模式
前面说过，本章将从Swing组件的体系结构开始。首先，我们讨论设计模式的概念，然后再看一下Swing框架中最具影响力的“模型-视图-控制器”模式。
## 1.1 设计模式
在“模型-视图-控制器”模式中，背景是显示信息和接收用户输入的用户界面系统。有关“模型-视图-控制器”模式将在接下来的章节中讲述。这里有几个冲突因素。对于同一数据来说，可能需要同时更新多个可视化表示。例如，为了适应各种观感标准，可能需要改变可视化表示形式；又例如，为了支持语音命令，可能需要改变交互机制。解决方案是将这些功能分布到三个独立的交互组件：模型、视图和控制器。模型-视图-控制器模式并不是AWT和Swing设计中使用的唯一模式。下列是应用的 另外几种模式： 

- 容器和组件是“组合(composite)” 模式
- 带滚动条的面板是“ 装饰器(decorator)” 模式
- 布局管理器是“策略(strategy)” 模式

设计模式的另外一个最重要的特点是它们已经成为文化的一部分。只要谈论起模型-视图-控制器或“装饰器”模式，遍及世界各地的程序员就会明白。因此，模式已经成为探讨设计方案的一种有效方法。

## 1.2 模型-视图-控制器模式

让我们稍稍停顿一会儿，回想一下构成用户界面组件的各个组成部分，例如，按钮、复选框、文本框或者复杂的树形组件等。每个组件都有三个要素： 
- 内容，如：按钮的状态（是否按下)，或者文本框的文本。 
- 外观（颜色，大小等)。 
- 行为（对事件的反应)。 

这三个要素之间的关系是相当复杂的，即使对于最简单的组件（如：按钮）来说也是如此。很明显，按钮的外观显示取决于它的观感。Metal按钮的外观与Windows按钮或者Motif按钮的外观就不一样。另外，外观显示还要取决于按钮的状态：当按钮被按下时，按钮需要 被重新绘制成另一种不同的外观。而状态取决于按钮接收到的事件。当用户在按钮上点击时，按钮就被按下。 

当然，在程序中使用按钮时，只需要简单地把它看成是一个按钮，而不需要考虑它的内部工作和特性。毕竟，这些是实现按钮的程序员的工作。无论怎样，实现按钮的程序员就要 对这些按钮考虑得细致一些了。毕竟，无论观感如何，他们必须实现这些按钮和其他用户界 面组件，以便让这些组件正常地工作。为了实现这样的需求，Swing设计者采用了一种很有名的设计模式(design pattern):模型-视图-控制器(model-view-controller)模式。这种设计模式同其他许多设计模式一样，都遵循第5章介绍过的面向对象设计中的一个基本原则： 限制一个对象拥有的功能数量。不要用一个按钮类完成所有的事情，而是应该让一个对象负责组件的观感，另一个对象负责存储内容。模型-视图-控制器（MVC) 模式告诉我们如何实现这种设计，实现三个独立的类：

- 模型(model): 存储内容。
- 视图(view): 显示内容。
- 控制器(controller): 处理用户输入。 

这个模式明确地规定了三个对象如何进行交互。模型存储内容，它没有用户界面。按钮的内容非常简单，只有几个用来表示当前按钮是否按下，是否处于活动状态的标志等。文本框内容稍稍复杂一些，它是保存当前文本的字符串对象。这与视图显示的内容并不一致---如果内容的长度大于文本框的显示长度，用户就只能看到文本框可以显示的那一部分

模型必须实现改变内容和查找内容的方法。例如，一个文本模型中的方法有：在当前文本中添加或者删除字符以及把当前文本作为一个字符串返回等。记住：模型是完全不可见的。显示存储在模型中的数据是视图的工作。

> 注释：“模式”这个术语可能不太贴切，因为人们通常把模式视为一个抽象概念的具体表 示。汽车和飞机的设计者构造模式来模拟真实的汽车和飞机。但这种类比可能会使你对模型-视图-控制器模式产生错误的理解。在设计模式中，模型存储完整的内容，视图给出了内容的可视化显示（完整或者不完整）。一个更恰当的比喻应当是模特为画家摆好姿势。
>
> 此时，就要看画家如何看待模特，并由此来画一张画了。那张画是一幅规矩的肖像画， 或是一幅印象派作品，还是一幅立体派作品（以古怪的曲线来描绘四肢）完全取决于画家。 

模型-视图-控制器模式的一个优点是一个模型可以有多个视图，其中每个视图可以显示全部内容的不同部分或不同形式。 例如，一个HTML编辑器常常为同一内容在同一时刻提供两个视图：一个WYSIWYG (所见即所得）视图和一个“ 原始标记” 视图（见图 12-3 )。当通过某一个视图的控制器对模型进行更新时，模式会把这种改变通知给两个视图。视图得到通知以后就会自动地刷新。当然，对于一个简单的用户界面组件来说，如按钮，不需要为同一模型提供多个视图。

控制器负责处理用户输入事件，如点击鼠标和敲击键盘。然后决定是否把这些事件转化成对模型或视图的改变。例如，如果用户在一个文本框中按下了一个字符键，控制器调用模型中的“插入字符”命令，然后模型告诉视图进行更新，而视图永远不会知道文本为什么改变了。但是如果用户按下了一个光标键，那么控制器会通知视图进行卷屏。卷动视图对实际文本不会有任何影响，因此模型永远不会知道这个事件的发生。 

在程序员使用Swing组件时，通常不需要考虑模型-视图-控制器体系结构。每个用户界面元素都有一个包装器类（如JButton或JTextField) 来保存模型和视图。当需要查询内容 (如文本域中的文本）时， 包装器类会向模型询问并且返回所要的结果。当想改变视图时（例如， 在一个文本域中移动光标位置)， 包装器类会把此请求转发给视图。然而，有时候包装器转发命令并不得力。在这种情况下，就必须直接地与模型打交道（不必直接操作视图--这是观感代码的任务)。

除了“本职工作”外，模型-视图-控制器模式吸引Swing设计者的主要原因是这种模式允许实现可插观感。每个按钮或者文本域的模型是独立于观感的。当然可视化表示完全依赖于特殊观感的用户界面设计，且控制器可以改变它。例如，在一个语音控制设备中，控制器需要处理的各种事件与使用键盘和鼠标的标准计算机完全不同。通过把底层模型与用户界面分离开，Swing设计者就能够重用模型的代码，甚至在程序运行时对观感进行切换。

当然，模式只能作为一种指导性的建议而并没有严格的戒律。没有一种模式能够适用于所有情况。例如，使用“窗户位置”模式（设计模式中并非主要成分）来安排小卧室就不太合适。同样地，Swing设计者发现对于可插观感实现来说，使用模型-视图-控制器模式并非都是完美的。模型容易分离开，每个用户界面组件都有一个模型类。但是，视图和控制器的职责分工有时就不很明显，这样将会导致产生很多不同的类。当然，作为这些类的使用者来说，不必为这些细节费心。前面已经说过，这些类的使用者根本无需为模型操心，仅使用组件包装器类即可。

## 1.3 Swing 按钮的模型-视图-控制器分析

前一章已经介绍了如何使用按钮，当时没有考虑模型、视图和控制器。按钮是最简单的用户界面元素，所以我们从按钮开始学习模型-视图-控制器模式会感觉容易些。对于更复杂的Swing组件来说，所遇到的类和接口都是类似的。

对于大多数组件来说，模型类将实现一个名字以Model结尾的接口，例如，按钮就实现了ButtonModel接口。实现了此接口的类可以定义各种按钮的状态。实际上，按钮并不复杂，在Swing库中有一个名为DefaultButtonModel 的类就实现了这个接口。

读者可以通过查看ButtonModd接口中的特征来了解按钮模型所维护的数据类别。表12-1列出了这些特征。

| 属性名        | 值                                               |
| ------------- | ------------------------------------------------ |
| actionCommand | 与按钮关联的动作命令字符串                       |
| mnemonic      | 按钮的快捷键                                     |
| armed         | 如果按钮被按下且鼠标仍在按钮上则为true           |
| enable        | 如果按钮是可选择的则为true                       |
| pressed       | 如果按钮被按下且鼠标按键没有释放则为true         |
| rollover      | 如果鼠标在按钮之上则为true                       |
| selected      | 如果按钮已经被选择（用于复选框和单选钮) 则为true |

每个JButton对象都存储了一个按钮模型对象，可以用下列方式得到它的引用。 

```java
JButton button = new JButton("Blue");
ButtonModel model = button.getModel();
```

实际上，不必关注按钮状态的零散信息，只有绘制它的视图才对此感兴趣。诸如按钮是否可用这样的重要信息完全可以通过JButton类得到（当然，JButton类也通过向它的模型询问来获得这些信息)。

下面查看ButtonModel接口中不包含的信息。模型不存储按钮标签或者图标。对于一个按钮来说，仅凭模型无法知道它的外观（实际上，在有关单选钮的12.4.2节中将会看到，这种纯粹的设计会给程序员带来一些麻烦)。

需要注意的是，同样的模型（即DefaultButtonModel) 可用于下压按钮、单选按钮、复选框、甚至是菜单项。当然，这些按钮都有各自不同的视图和控制器。当使用Metal观感时，JButton类用 BasicButtonUI类作为其视图；用ButtonUIListener类作为其控制器。通常，每 个 Swing组件都有一个相关的后缀为UI的视图对象，但并不是所有的Swing组件都有专门的控制器对象。在阅读JButton底层工作的简介之后可能会想到：JButton究竟是什么？事实上，它仅仅是一个继承了JComponent的包装器类，JComponent包含了一个DefauUButtonModel对象，一些视图数据（例如按钮标签和图标）和一个负责按钮视图的BasicButtonUI对象。

# 2 布局管理概述



## 2.1 边框布局

## 2.2 网格布局

# 3 文本输入

现在终于可以开始介绍Swing用户界面组件了。首先，介绍具有用户输入和编辑文本功能的组件。文本域(JTextField)和文本区(JTextArea)组件用于获取文本输入。文本域只能接收单行文本的输入，而文本区能够接收多行文本的输入。JPassword也只能接收单行文本的输入，但不会将输入的内容显示出来。 

这三个类都继承于JTextComponent类。由于JTextComponent是一个抽象类，所以不能够构造这个类的对象。另外，在Java中常会看到这种情况。在査看API文档时，发现自己正在寻找的方法实际上来自父类JTextComponent, 而不是来自派生类自身。例如，在一个文本域和文本区内获取（get)、设置（set) 文本的方法实际上都是JTextComponent类中的方法

> javax.swing.text.JTextComponent 1.2 
>
> ```java
> String getText();
> void setText(String text); //获取或设置文本组件中的文本。
> boolean isEditable();
> void setEditabe(boolean b); //获取或设置 editable 特性，这个特性决定了用户是否可以编辑文本组件中的内容。 
> ```

### 3.1 文本域
把文本域添加到窗口的常用办法是将它添加到面板或者其他容器中，这与添加按钮完全一样： 

```JAVA
JPanel panel = new JPanel();
JTextField textField = new JTextField("Default input", 20); panel.add(textField); 
```

这段代码将添加一个文本域，同时通过传递字符串“Default input”进行初始化。构造器的第二个参数设置了文本域的宽度。在这个示例中，宽度值为 20“列”。但是，这里所说的列不是一个精确的测量单位。一列就是在当前使用的字体下一个字符的宽度。如果希望文本域最多能够输入n个字符，就应该把宽度设置为n列。在实际中，这样做效果并不理想，应该将最大输入长度再多设1~2个字符。列数只是给AWT设定首选(preferred)大小的一 个提示。如果布局管理器需要缩放这个文本域，它会调整文本域的大小。在JTextField的构造器中设定的宽度并不是用户能输入的字符个数的上限。用户可以输入一个更长的字符串，但是当文本长度超过文本域长度时输入就会滚动。用户通常不喜欢滚动文本域，因此应该尽 量把文本域设置的宽一些。如果需要在运行时重新设置列数，可以使用setColumns方法。

> 提示：使用setColumns方法改变了一个文本域的大小之后，需要调用包含这个文本框的容器的revalidate方法。
>
> ```java
> textField.setColumns(10);
> panel.revalidate();
> ```
>
> revalidate方法会重新计算容器内所有组件的大小，并且对它们重新进行布局。调用revalidate方法以后， 布局管理器会重新设置容器的大小，然后就可以看到改变尺寸后的 文本域了。revalidate方法是JComponent类中的方法。它并不是马上就改变组件大小，而是给这个组件加一个需要改变大小的标记。这样就避免了多个组件改变大小时带来的重复计算。但是，如果想重新计算一个JFrame中的所有组件，就必须调用validate方法JFrame没有扩展JComponent。 

通常情况下，希望用户在文本域中键入文本（或者编辑已经存在的文本)。文本域一般初始为空白。只要不为JTextField构造器提供字符串参数，就可以构造一个空白文本域：

```java
JTextField textField = new JTextField(20); 
```

可以在任何时候调用setText方法改变文本域中的内容。这个方法是从前面提到的JTextComponent中继承而来的。例如：

```java
textField.setText("Hello!");
```

并且，在前面已经提到，可以调用getText方法来获取用户键入的文本。这个方法返回用户输入的文本。 如果想要将getText方法返回的文本域中的内容的前后空格去掉，就应该调用trim方法：

```java
String text = textField.getText().trim;
```

如果想要改变显示文本的字体，就调用setFont方法。 

> javax.swing.JTextField 1.2 
>
> ```java
> JTextF1eld(1nt cols); //构造一个给定列数的空 JTextField对象。 JTextField(String text, int cols); //构造一个给定列数、给定初始字符串的 JTextField 对象。
> int getColumns();
> void setColumns(int cols); //获取或设置文本域使用的列数。
> ```
>
> javax.swing.JComponent 1.2
>
> ```java
> void revalidate(); //重新计算组件的位置和大小。
> void setFont(Font f); //设置组件的字体。
> ```
>
> java.awt.Component 1.0
>
> ```java
> void validate(); //重新计算组件的位置和大小。如果组件是容器，容器中包含的所有组件的位置和大小 也被重新计算。
> Font getFont(); //获取组件的字体。
> ```

## 3.2 标签和标签组件

标签是容纳文本的组件，它们没有任何的修饰（例如没有边缘)，也不能响应用户输入。可以利用标签标识组件。例如：与按钮不同，文本域没有标识它们的标签 要想用标识符标识这种不带标签的组件，应该 

- 1 ) 用相应的文本构造一个 JLabel 组件。
- 2 ) 将标签组件放置在距离需要标识的组件足够近的地方， 以便用户可以知道标签所标 识的组件。

JLabel的构造器允许指定初始文本和图标，也可以选择内容的排列方式。可以用Swing Constants接口中的常量来指定排列方式。在这个接口中定义了几个很有用的常量，如LEFT、RIGHT、CENTER、NORTH、EAST等。JLabel是实现这个接口的一个Swing类。因此，可以指定右对齐标签：

```java
JLabel label = new JLabel("User name:",SwingConstants.RIGHT); 
```

或者

```java
JLabel label = new JLabel("User name:",JLabel.RIGHT);
```

利用setText和setlcon方法可以在运行期间设置标签的文本和图标。

> 提示：可以在按钮、标签和菜单项上使用无格式文本或HTML文本。我们不推荐在按钮上使用HTML文本---这样会影响观感。但是HTML文本在标签中是非常有效的。只要简单地将标签字符串放置在`<html>...</html>` 中即可：
>
> ```java
> label = new JLabel("<html><b>Required</b> entry:</html>");
> ```
>
> 需要说明的是包含HTML标签的第一个组件需要延迟一段时间才能显示出来，这是 因为需要加载相当复杂的HTML显示代码。

与其他组件一样，标签也可以放置在容器中。这就是说，可以利用前面介绍的技巧将标签放置在任何需要的地方。 

>javax.swing.JLabel1.2
>
>```java
>JLabel(String text);
>JLabel(Icon icon);
>JLabel(String text, int align);
>JLabel(String text, Icon Icon, int align); //构造一个标签。 
>//参数：text标签中的文本
>//	icon标签中的图标
>//	align一个SwingConstants的常量LEFT (默认)、CENTER或者RIGHT
>String getText();
>void setText(String text); //获取或设置标签的文本。
>Icon getIcon();
>void setIcon(Icon Icon); //获取或设置标签的图标。
>```

## 3.3 密码域
密码域是一种特殊类型的文本域。为了避免有不良企图的人看到密码， 用户输入的字符不显示出来。每个输入的字符都用回显字符（echo character) 表示， 典型的回显字符是星号 (*)。Swing 提供了 JPasswordField类来实现这样的文本域。

密码域是另一个应用模型-视图控制器体系模式的例子。密码域采用与常规的文本域相同的模型来存储数据， 但是，它的视图却改为显示回显字符，而不是实际的字符。

>  javax.swing.JPasswordField 1.2
>
> ```java
> JPasswordField(String text, int columns); //构造一个新的密码域对象。
> void setEchoChar(char echo); //为密码域设置回显字符。注意：独特的观感可以选择自己的回显字符。0表示重新设置为默认的回显字符。
> char[] getPassworci(); //返回密码域中的文本。为了安全起见，在使用之后应该覆写返回的数组内容（密码并不是以String的形式返回，这是因为字符串在被垃圾回收器回收之前会一直驻留在虚拟机中）。
> ```

## 3.4 文本区
有时，用户的输入超过一行。正像前面提到的，需要使用JTextArea组件来接收这样的输入。当在程序中放置一个文本区组件时，用户就可以输入多行文本，并用ENTER键换行。每行都以一个“ \n” 结尾。图12-13显示了一个工作的文本区。

在JTextArea组件的构造器中，可以指定文本区的行数和列数。例如：

```java
textArea = new JTextArea(8, 40); // 8 lines of 40 columns each 
```

与文本域一样。出于稳妥的考虑，参数columns应该设置得大一些。另外，用户并不受限于输入指定的行数和列数。当输入过长时，文本会滚动。还可以用setColumns方法改变列数，用setRows方法改变行数。这些数值只是首选大小一布局管理器可能会对文本区进行缩放。

如果文本区的文本超出显示的范围，那么剩下的文本就会被剪裁掉。可以通过开启换行特性来避免裁剪过长的行：

```java
textArea.setLineWrap(true); //long lines are wrapped
```

换行只是视觉效果；文档中的文本没有改变，在文本中并没有插入“ \n”字符。

## 3.5 滚动窗格

在Swing中，文本区没有滚动条。如果需要滚动条，可以将文本区插入到滚动窗格 (scroll pane) 中。

```java
textArea = new JTextArea(8, 40);
JScrollPane scrollPane = new JScratlPane(textArea);
```

现在滚动窗格管理文本区的视图。如果文本超出了文本区可以显示的范围，滚动条就会自动地出现，并且在删除部分文本后，当文本能够显示在文本区范围内时，滚动条会再次自动地消失。滚动是由滚动窗格内部处理的，编写程序时无需处理滚动事件。

这是一种为任意组件添加滚动功能的通用机制，而不是文本区特有的。也就是说，要想为组件添加滚动条，只需将它们放入一个滚动窗格中即可。

程序清单12-2展示了各种文本组件。这个程序只是简单地显示了一个文本域、一个密码域和一个带滚动条的文本区。文本域和密码域都使用了标签。点击“Insert”会将组件中的内容插入到文本区中。

> 注释：JTextArea组件只显示无格式的文本，没有特殊字体或者格式设置。如果想要显示格式化文本（如HTML), 就需要使用JEditorPane类。在卷II将详细讨论。

> 程序清单 12-2 text/TextComponentFrame.java 
>
> ```java
> package text;
> import java.awt.BorderLayout;
> import java.awt.GridLayout;
> import javax.swing.JButton;
> import javax.swing.JFrame;
> import javax.swing.JLabel;
> import javax.swing.JPanel;
> import javax.swing.JPasswordField;
> import javax.swing.JScrollPane;
> import javax.swingJTextArea;
> import javax.swingJTextField;
> import javax.swing.SwingConstants;
> /**
> * A frame with sample text components.
> */
> public class TextComponentFrame extends JFrame
> {
> 	public static final int TEXTAREA.ROWS = 8;
> 	public static final int TEXTAREA.COLUMNS = 20;
> 	public TextComponentFrame()
> 	{
> 		JTextField textField = new JTextField();
> 		JPasswordField passwordField = new JPasswordseld();
> 		JPanel northPanel = new JPanel();
> 		northPanel.setLayout(new CridLayout(2, 2));
> 		northPanel.add(new JLabel("User name:",SwingConstants.RIGHT));
> 		northPanel.add(textField);
> 		northPanel.add(new JLabel("Password:"SwingConstants.RIGHT));
> 		northPanel.add(passwordField);
> 		add(northPanel, BorderLayout.NORTH);
> 		JTextArea textArea = new JTextArea(TEXTAREA_ROWS, TEXTAREA_COLUMNS);
> 		JScrollPane scrollPane = new JScrollPane(textArea);
> 		add(scrollPane, BorderLayout.CENTER);
> 		// add button to append text into the text' area
> 		JPanel southPanel = new JPanel();
> 		JButton insertButton = new JButton("Insert");
> 		southPanel.add(insertButton);
> 		insertButton.addActionListener(event->textArea.append("User name:"+textField.getText()+ "Password:+new String(passwordField.getPassword())+"\n"));
> 		add(southPanel,BorderLayout.SOUTH);
> 		pack();
> 	}
> }
> ```

> javax.swing.JTextArea 1.2
>
> ```java
> JTextArea();
> JTextArea(int rows, int cols);
> JTextArea(String text, int rows, int cols); //构造一个新的文本区对象。
> void setColumns(int cols); //设置文本区应该使用的首选列数。
> void setRows(int rows); //设置文本区应该使用的首选行数。
> void append(String newText); //将给定的文本追加到文本区中已有文本的尾部。
> void setLineWrap(boolean wrap); //打开或关闭换行。
> void setWrapStyleWord(boolean word); //如果word是true, 超长的行会在字边框处换行。如果为false,超长的行被截断而不考虑字边框。
> void SETTABSIZG(int c); //将制表符（tab stop) 设置为c列。注意，制表符不会被转化为空格，但可以让文本对齐到下一个制表符处。
> ```
>
> javax.swing.JScroliPane1.2
>
> ```java
> JScrol1Pane(Component c); //创建一个滚动窗格，用来显示指定组件的内容。当组件内容超过显示范围时，滚动条会自动地出现。
> ```

# 4 选择组件
前面已经讲述了如何获取用户输入的文本。然而，在很多情况下，可能更加愿意给用户几种选项，而不让用户在文本组件中输入数据。使用一组按钮或者选项列表让用户做出选择 (这样也免去了检查错误的麻烦)。在本节中，将介绍如何编写程序来实现复选框、单选按钮、选项列表以及滑块。

## 4.1 复选框

如果想要接收的输入只是“是” 或“非”，就可以使用复选框组件。复选框自动地带有标识标签。用户通过点击某个复选框来选择相应的选项， 再点击则取消选取。当复选框获得焦点时，用户也可以通过按空格键来切换选择。

图12-14所示的程序中有两个复选框，其中一个用于打开或关闭字体倾斜属性，而另一个用于控制加粗属性。注意，第二个复选框有焦点，这一点可以由它周围的矩形框看出。只要用户点击某个复选框，程序就会刷新屏幕以便应用新的字体属性。 

复选框需要一个紧邻它的标签来说明其用途。在构造器中指定标签文本。

```java
bold = new JCheckBox("Bold");
```

可以使用setSelected方法来选定或取消选定复选框。例如：

```java
bold.setSelected(true);
```

isSelected方法将返回每个复选框的当前状态。如果没有选取则为false, 否则为true。当用户点击复选框时将触发一个动作事件。通常，可以为复选框设置一个动作监听器。在下面程序中，两个复选框使用了同一个动作监听器。

```java
ActionListener listener =...
bold.addActionListener(listener);
italic.addActionListener(listener);
```

actionPerformed方法查询bold和italic两个复选框的状态，并且把面板中的字体设置为常规、加粗、倾斜或者粗斜体。

```java
ActionListener listener = event -> {
	int mode = 0;
	if (bold.isSelectedO) mode += Font.BOLD;
	if (italic.isSelectedO) mode += Font.ITALIC;
	label.setFont(new Font(Font.SERIF, mode, FONTSIZE));
};
```

程序清单 12-3 给出了复选框例子的全部代码。

> 程序清单 12-3 checkBox/CheckBoxTest.java
>
> ```java
> package checkBox;
> import java.awt.*;
> import java.awt.event.*;
> import javax.swing.*;
> /**
> * A frame with a sample text label and check boxes for selecting font
> * attributes.
> */
> public class CheckBoxFrame extends JFrame
> {
> 	private JLabel label;
> 	private JCheckBox bold;
> 	private JCheckBox italic;
> 	private static final int FONTSIZE = 24;
> 	public CheckBoxFrame()
> 	{
> 		// add the sample text label
> 		label = new JLabel("The quick brown fox jumps over the lazy dog.");
> 		label.setFont(new Font("Serif", Font.BOLD, FONTSIZE));
> 		add(label, BorderLayout.CENTER);
> 		// this listener sets the font attribute of
> 		// the label to the check box state
> 		ActionListener listener = event -> {
> 			int mode = 0;
> 			if (bold.isSelectedO) mode += Font.BOLD;
> 			if (italic.isSelectedO) mode += Font.ITALIC;
> 			label.setFont(new Font("Serif", mode, FONTSIZE));
> 		};
> 		// add the check boxes
> 		JPanel buttonPanel = new JPanel();
> 		bold = new JCheckBox("Bo1d");
> 		bold.addActionListener(listener);
> 		bold.setSelected(true);
> 		buttonPanel.add(bold);
> 		
> 		italic = new JCheckBox("Italic");
> 		italic.addActionListener(listener);
> 		buttonPanel.add(italic);
> 		
> 		add(buttonPanel, BordedLayout.SOUTH);
> 		pack();
> 	}
> }
> ```

> javax.swing.JCheckBox 1.2
>
> ```java
> JCheckBox(String label);
> JCheckBox(String label,Icon icon); //构造一个复选框， 初始没有被选择。 JCheckBox(String label,boolean state); //用给定的标签和初始化状态构造一个复选框。
> boolean isSelected ();
> void setSelected(boolean state); //获取或设置复选框的选择状态。
> ```

## 4.2 单选钮

在前一个例子中，对于两个复选框，用户既可以选择一个、两个，也可以两个都不选。在很多情况下，我们需要用户只选择几个选项当中的一个。当用户选择另一项的时候， 前一项就自动地取消选择。这样一组选框通常称为单选钮组（RadioButtonGroup), 这是因为这些 按钮的工作很像收音机上的电台选择按钮。当按下一个按钮时，前一个按下的按钮就会自动 弹起。图 12-15给出了一个典型的例子。这里允许用户在多个选择中选择字体的大小，即小、中、大和超大，但是，每次用户只能选择一个。

在Swing中，实现单选钮组非常简单。为单选钮组构造一个ButtonGroup的对象。然后，再将JRadioButton类型的对象添加到按钮组中。按钮组负责在新按钮被按下时，取消前一个被按下的按钮的选择状态。 

```java
ButtonGroup group = new ButtonGroup();
JRadioButton smallButton = new JRadioButton("Small", false);
group.add(smallButton);
JRadioButton mediumButton = new JRadioButton("Medium", true);
group.add(mediumButton);
...
```

构造器的第二个参数为true表明这个按钮初始状态是被选择，其他按钮构造器的这个参数为false。注意，按钮组仅仅控制按钮的行为，如果想把这些按钮组织在一起布局，需要把它们添加到容器中，如JPanel。

单选钮与复选框的外观是不一样的。复选框为正方形，并且如果被选择，这个正方形中会出现一个对钩的符号。单选钮是圆形，选择以后圈内出现一个圆点。 单选钮的事件通知机制与其他按钮一样。当用户点击一个单选钮时， 这个按钮将产生一个动作事件。在示例中，定义了一个动作监听器用来把字体大小设置为特定值： 

```java
ActionListener listener = event ->
	label.setFont(new Font("Serif", Font.PLAIN, size));
```

用这个监听器与复选框中的监听器做一个对比。每个单选钮都对应一个不同的监听器对象。每个监听器都非常清楚所要做的事情---把字体尺寸设置为一个特定值。在复选框示例中，使用的是一种不同的方法，两个复选框共享一个动作监听器。这个监听器调用一个方法来检查两个复选框的当前状态。

对于单选钮可以使用同一个方法吗？可以试一下使用一个监听器来计算尺寸，如：

```java
if (smallButton.isSelected()) size = 8;
else if (mediumButton.isSelected()) size = 12;
...
```

然而，更愿意使用各自独立的动作监听器，因为这样可以将尺寸值与按钮紧密地绑定在一起。

> 注释：如果有一组单选钮，并知道它们之中只选择了一个。要是能够不查询组内所有的按钮就可以很快地知道哪个按钮被选择的话就好了。由于ButtonGroup对象控制着所有的按钮，所以如果这个对象能够给出被选择的按钮的引用就方便多了。事实上，ButtonGroup类中有一个getSelection方法，但是这个方法并不返回被选择的单选钮，而是返回附加在那个按钮上的模型ButtonModel的引用。对于我们来说，ButtonModel中的方法没有什么实际的应用价值。ButtonModel接口从ItemSelectable接口继承了一个getSelectedObject方法，但是这个方法没有用，它返回null。getActionCommand方法看起来似乎可用，这是因为一个单选钮的“动作命令”是它的文本标签，但是它的模型的动作命令是null。只有在通过setActionCommand命令明确地为所有单选钮设定动作命令后，才能够通过调用方法
>
> ```java
> buttonGroup.getSelection().getActionCommand()
> ```
>
> 获得当前选择的按钮的动作命令。 

> 程序清单 12-4是一个用于选择字体大小的完整程序，它演示了单选钮的工作过程。 
>
> ```java
> package radioButton;
> import java.awt.*;
> import java.awt.event.*;
> import javax.swing.*;
> /**
> * A frame with a sample text label and radio buttons for selecting font sizes. 
> */
> public class RadioButtonPrame extends JFrame
> {
> 	private JPanel buttonPanel:
> 	private ButtonGroup group;
> 	private JLabel label;
> 	private static final int DEFAULT.SIZE = 36;
> 	public RadioButtonFrame()
> 	{
> 		// add the sample text label
> 		label = newJLabel("The quick brown fox jumps over the lazy dog.");
> 		label.setFont(new Font("Serif", Font.PLAIN, DEFAULT_SIZE));
> 		add(label, BorderLayout.CENTER);
> 		// add the radio buttons buttonPanel = new JPanel();
> 		group = new BtittonGroup();
> 		addRadioButton( "Small", 8);
> 		addRadioButton( "Medium", 12);
> 		addRadioButton( "Large", 18);
> 		addRadioButton( "Extra large", 36);
> 		add(buttonPanel,BorderLayout.SOUTH);
> 		pack();
> 	}
> 	/**
> 	* Adds a radio button that sets the font size of the sample text.
> 	* @param name the string to appear on the button
> 	*@param size the font size that this button sets
> 	**/
> 	public void addRadioButton(String name, int size)
> 	{
> 		boolean selected = size == DEFAULT_SIZE;
> 		JRadioButton button = new JRadioButton(name, selected);
> 		group.add(button);
> 		buttonPanel.add(button);
> 		// this listener sets the label font size
> 		ActionListener listener = event -> label.setFont(new Font("Serif", Font.PLAIN, size));
> 		button.addActionListener(listener);
> 	}
> }
> ```

> javax.swing.JRadioButton 1.2
>
> ```java
> JRadioButton(String label, Icon icon); //构造一个单选钮， 初始没有被选择。 JRadioButton(String label, boolean state); //用给定的标签和初始状态构造一个单选钮。 
> ```

> javax.swing.ButtonGroup 1.2
>
> ```java
> void add(AbstractButton b); //将按钮添加到组中。
> ButtonModel getSelection(); //返回被选择的按钮的按钮模型。
> ```

> javax.swing.ButtonModel 1.2
>
> ```java
> String getActionCommand(); //返回按钮模型的动作命令。 
> ```

> javax.swing.AbstractButton 1.2
>
> ```java
> void setActionCommand(String s); //设置按钮及其模型的动作命令
> ```

## 4.3 边框

如果在一个窗口中有多组单选按钮，就需要用可视化的形式指明哪些按钮属于同一组。Swing提供了一组很有用的边框（borders) 来解决这个问题。可以在任何继承了JComponent的组件上应用边框。最常用的用途是在一个面板周围放置一个边框，然后用其他用户界面元素（如单选钮）填充面板。

有几种不同的边框可供选择，但是使用它们的步骤完全一样。

1 ) 调用 BorderFactory 的静态方法创建边框。下面是几种可选的风格

- 凹斜面
- 凸斜面
- 蚀刻
- 直线
- 蒙版
- 空（只是在组件外围创建一些空白空间）

2 ) 如果愿意的话，可以给边框添加标题，具体的实现方法是将边框传递给`BroderFactory.createTitledBorder`

3 ) 如果确实想把一切凸显出来，可以调用下列方法将几种边框组合起来使用 `BorderFactory.createCompoundBorder`

4 ) 调用JComponent类中setBorder方法将结果边框添加到组件中。 

例如，下面代码说明了如何把一个带有标题的蚀刻边框添加到一个面板上：

```java
Border etched = BorderFactory.createEtchedBorder();
Border titled = BorderFactory.createTitledBorder(etched, "A Title");
panel.setBorder(titled);
```

运行程序清单12-5中的程序可以看到各种边框的外观。

不同的边框有不同的用于设置边框的宽度和颜色的选项。详情请参看API注释。偏爱使用边框的人都很欣赏这一点，SoftBevelBorder 类用于构造具有柔和拐角的斜面边框，LineBorder类也能够构造圆拐角。这些边框只能通过类中的某个构造器构造，而没有BorderFactory方法。

> javax.swing.BorderFactory 1.2
>
> ```java
> static Border createLineBorder(Co1or color);
> static Border createLineBorder(Color color, int thickness); //创建一个简单的直线边框。
> static MatteBorder createMatteBorder(int top, int left, int bottom, int right, Color color);
> static MatteBorder createMatteBorder(int top, int left, int bottom, int right, Icon tilelcon); //创建一个用 color 颜色或一个重复（repeating) 图标填充的粗的边框。
> static Border createEmptyBorder();
> static Border createEmptyBorder(int top, int left, int bottom, int right); //创建一个空边框。
> static Border createEtchedBorder();
> static Border createEtchedBorder(Color highlight,Color shadow);
> static Border createEtchedBorder(int type);
> static Border createEtchedBorder(int type,Color highlight,Color shadow);
> 
> //创建一个具有 3D 效果的直线边框。
> //参数： highlight, shadow 用于3D效果的颜色
> //type EtchedBorder.RAISED 和 EtchedBorder.LOWERED 之一
> static Border createBevelBorder (int type);
> static Border createBevelBorder(int type, Color highlight, Color shadow); static Border createLoweredBevelBorder();
> static Border createRaisedBevelBorder();
> //创建一个具有凹面或凸面效果的边框。
> //参数：type   BevelBorder.LOWERED 和 BevelBorder.RAISED 之一
> //highlight,shadow    用于 3D 效果的颜色
> static TitledBorder createTitledBorder(String title);
> static TitledBorder createTitledBorder(Border border);
> static TitledBorder createTitledBorder(Border border, String title);
> static TitledBorder createTitledBorder(Border border, String title, int justification, Int position);
> static TitledBorder createTitledBorder( Border border, String title, int justification, int position, Font font);
> static Tit edBorder createTitledBorder(Border border, String title, int justification, int position, Font font, Color color);
> //创建一个具有给定特性的带标题的边框。 
> //参数: title	 标题字符串
> //border	用标题装饰的边框
> //justification	 TitledBorder常量LEFT、CENTER、 RIGHT、 LEADING、 TRAILING 或 DEFAULT_JUSTIFICATION (left) 之一
> //position	TitledBorder常量ABOVE—TOP、TOP、BELOW—TOP、ABOVE_ BOTTOM、 BOTTOM、 BELOW_BOTTOM 或 DEFAULT— POSITION (top) 之一
> //font	 标题的字体
> //color	  标题的颜色
> static CompoundBorder createCompoundBorder(Border outsideBorder, Border insideBorder); //将两个边框组合成一个新的边框。 position font color 
> ```

> javax.swing,border.SoftBevelBorder 1.2 
>
> ```java
> SoftBevelBorder(int type)
> SoftBevelBorder(int type, Color highlight, Color shadow); //创建一个带有柔和边角的斜面边框。
> //参数：type    BevelBorder.LOWERED和BevelBorder.RAISED之一
> //highlight,shadow    用于3D效果的颜色 
> ```

> javax.swing.border.LineBorder 1.2 
>
> ```java
> public LineBorder(Color color, int thickness, boolean roundedCorners); //用指定的颜色和粗细创建一个直线边框。如果roundedCorners为true，则边框有圆角。
> ```

> javax.swing.JComponent 1.2
>
> ```java
> void setBorder(Border border); //设置这个组件的边框
> ```

## 4.4 组合框

如果有多个选择项， 使用单选按钮就不太适宜了，其原因是占据的屏幕空间太大。这时就可以选择组合框。当用户点击这个组件时，选择列表就会下拉出来，用户可以从中选择一项（见图 12-17 )。

如果下拉列表框被设置成可编辑（ editable), 就可以像编辑文本一样编辑当前的选项内容。鉴于这个原因，这种组件被称为组合框（combo box), 它将文本域的灵活性与一组预定义的选项组合起来。JComboBox类提供了组合框的组件。 

在Java SE 7中，JComboBox 类是一个泛型类。例如，JComboBox<String> 包含String类型的对象，JComboBox<Integer> 包含整数。

调用setEditable方法可以让组合框可编辑。注意，编辑只会影响当前项，而不会改变列表内容。

可以调用getSelectedltem方法获取当前的选项，如果组合框是可编辑的，当前选项则是可以编辑的。不过，对于可编辑组合框， 其中的选项可以是任何类型，这取决于编辑器（即 由编辑器获取用户输人并将结果转换为一个对象)。关于编辑器的讨论请参见卷n中的第6 章。如果你的组合框不是可编辑的， 最好调用

```java
combo.getltemAt(combo.getSelectedlndex()) 
```

这会为所选选项提供正确的类型。

在示例程序中，用户可以从字体列表（Serif, SansSerif，Monospaced 等）中选择一种字体，用户也可以键入其他的字体。

可以调用addltem方法增加选项。在示例程序中，只在构造器中调用了addltem方法，实际上，可以在任何地方调用它。

```java
JComboBox<String> faceCombo = new JConboBoxo();
faceCombo.addItem("Serif");
faceCombo.addltem("SansSerif");
```

这个方法将字符串添加到列表的尾部。可以利用 insertltemAt方法在列表的任何位置插入一个新选项： 

```
faceCombo.insertltemAt( "Monospaced", 0) ;// add at the beginning 
```

可以增加任何类型的选项，组合框可以调用每个选项的 toString 方法显示其内容。

如果需要在运行时删除某些选项，可以使用removeltem或者removeltemAt方法，使用哪个方法将取决于参数提供的是想要删除的选项内容，还是选项位置。

```
faceCombo.removeltem("Monospaced");
faceCombo.removeltemAt(0)； // remove first item
```

调用removeAllltems方法将立即移除所有的选项。

> 提示：如果需要往组合框中添加大量的选项，addltem方法的性能就显得很差了。取而代之的是构造一个DefaultComboBoxModel, 并调用addElement方法进行加载， 然后再调用JComboBox 中的 setModel 方法。

当用户从组合框中选择一个选项时，组合框就将产生一个动作事件。为了判断哪个选项被选择，可以通过事件参数调用getSource方法来得到发送事件的组合框引用，接着调用getSelectedltem方法获取当前选择的选项。需要把这个方法的返回值转化为相应的类型，通常是String型。

```java
ActionListener listener = event ->
	label.setFont(new Font(faceCoibo.getltenAt(faceConbo.setSelectedlndex()),Font.PLAIN,DEFAULT_SIZE));
```

程序清单12-6给出了完整的代码。

> 注释： 如果希望持久地显示列表， 而不是下拉列表， 就应该使用JList 组件。在卷Ⅱ的第 6 章中将介绍 JList

> 程序清单 12-6 comboBox/ComboBoxFrame.java
>
> ```java
> package comboBox;
> import java.awt.BorderLayout;
> import java.awt.Font;
> import javax.swing.JComboBox;
> import javax.swing.JFrame;
> import javax.swing.JLabel;
> import javax.swing.JPanel;
> /**
> * A frame with a sample text label and a combo box for selecting font faces.
> */
> public class ComboBoxFrame extends JFrame
> {
> 	private JComboBox<String> faceCombo;
> 	private JLabel label;
> 	private static final int DEFAULT.SIZE = 24;
> 	public ComboBoxFrame()
> 	{
> 		// add the sample text label
> 		label = new JLabel("The quick brown fox jumps over the lazy dog.");
> 		label.setPont(new FontCSerif, Font.PLAIN, DEFAULT.SEE));
> 		add(1abel,BorderLayout.CENTER);
> 		// make a combo box and add face names
> 		faceCombo = new JComboBox<>();
> 		faceCombo.addltern("Serif");
> 		faceCombo.addItem("SansSerif");
> 		faceCombo.addItem("Monospaced");
> 		faceCombo.addltem("Dialog");
> 		faceCombo.addltem("Dialoglnput");
> 		// the combo box listener changes the label font to the selected face name
> 		faceCombo.addActionListener(event ->
> 			label.setFont(
> 				new Font(faceCombo.getltemAt(faceCombo.getSelectedlndex()), Font.PLAIN, DEFAULT.SIZE)));
> 		// add combo box to a panel at the frame's southern border
> 		JPanel comboPanel = new JPanel();
> 		comboPanel.add(faceCombo);
> 		add(comboPanel, BorderLayout.SOUTH);
> 		pack();
> 	}
> }
> ```

> javax.swing.JComboBox 1.2
>
> ```java
> boolean isEditable();
> void setEditable(boo1ean b ); //获取或设置组合框的可编辑特性。
> void addItem(Object item); //把一个选项添加到选项列表中。
> void insertltemAtCObject item, int index); //将一个选项添加到选项列表的指定位置。
> void removeItem(Object item); //从选项列表中删除一个选项。
> void removeItemAt(int index ); //删除指定位置的选项。
> void removeAllItems(); //从选项列表中删除所有选项。
> Object getSelectedItem(); //返回当前选择的选项。
> ```

## 4.5 滑动条
组合框可以让用户从一组离散值中进行选择。滑动条允许进行连续值的选择，例如，从1~100之间选择任意数值。

通常，可以使用下列方式构造滑动条：

```java
JSlider slider = new JSlider(min, max, initialValue); 
```

如果省略最小值、最大值和初始值，其默认值分别为0、100和50。

或者如果需要垂直滑动条，可以按照下列方式调用构造器：

```java
JSlider slider = new JSIider(SwingConstants.VERTICAL, min, max, initialValue);
```

这些构造器构造了一个无格式的滑动条， 如图12-18最上面的滑动条所示。下面看一下如何为滑动条添加装饰。

当用户滑动滑动条时， 滑动条的值就会在最小值和最大值之间变化。当值发生变化时，ChangeEvent就会发送给所有变化的监听器。为了得到这些改变的通知，需要调用addChangeListener方法并且安装一个实现了ChangeListener接口的对象。这个接口只有一个方法 StateChanged。在这个方法中，可以获取滑动条的当前值：

```java
ChangeListener listener = event -> {
	JSlider slider = (JSlider) event.getSource();
	int value = slider.getValue();
	...
};
```

可以通过显示标尺（tick)对滑动条进行修饰。例如，在示例程序中，第二个滑动条使用了下面的设置：

```java
slider.setMajorTickSpacing(20);
slider.setMinorTickSpacing(5);
```

上述滑动条在每20个单位的位置显示一个大标尺标记，每5个单位的位置显示一个小标尺标记。所谓单位是指滑动条值，而不是像素。 

这些代码只设置了标尺标记，要想将它们显示出来，还需要调用： 

```java
slider.setPaintTicks(true);
```

大标尺和小标尺标记是相互独立的。例如，可以每 20 个单位设置一个大标尺标记，同时每 7 个单位设置一个小标尺标记， 但是这样设置，滑动条看起来会显得非常凌乱。

可以强制滑动条对齐标尺。这样一来，只要用户完成拖放滑动条的操作， 滑动条就会立即自动地移到最接近的标尺处。激活这种操作方式需要调用：

```java
slider.set5napToTicks(true);
```

> 注释： “对齐标尺”的行为与想象的工作过程并不太一样。在滑动条真正对齐之前，改变监听器报告的滑动条值并不是对应的标尺值。如果点击了滑动条附近，滑动条将会向点 击的方向移动一小段距离，“对夺标尺”的滑块并不移动到下一个标尺处。

可以调用下列方法为大标尺添加标尺标记标签（tick mark labels)： 

```java
slider.setPaintLabels(true);
```

例如， 对于一个范围为 0 到 100 的滑动条， 如果大标尺的间距是 20, 每个大标尺的标签 就应该分别是 0、20、40、60、80 和 100。

还可以提供其他形式的标尺标记，如字符串或者图标（见图 12-18 )。这样做有些烦琐。 首先需要填充一个键为 Integer类型且值为Component 类型的散列表。 然后再调用setLabelTable方法，组件就会放置在标尺标记处。通常组件使用的是几此以对象。下面代码 说明了如何将标尺标签设置为 A、B、C、D、E 和 F。 

```java
Hashtable<Integer, Component> labelTable = new Hashtable<Integer, Components>(); labelTable.put(0, new JLabel("A"));
labelTable.put(20, new Jabel("B"));
labelTable.put(100, new Jabel("F”);
slider,setLabelTable(labelTable);
```

关于散列表的详细介绍， 参考第 9 章。

程序清单12-7显示了如何创建用图标作为标尺标签的滑动条。

> 提示： 如果标尺的标记或者标签不显示，请检查一下是否调用了setPaintTicks(true)和setPaintLabels(true)。

在图 12-18 中， 第 4 个滑动条没有轨迹。要想隐藏滑动条移动的轨迹，可以调用： 

```java
slider.setPaintTrack(false);
```

图 12-18 中第 5 个滑动条是逆向的，调用下列方法可以实现这个效果：

```java
slider.setlnverted(true);
```

示例程序演示了所有不同视觉效果的滑动条。每个滑动条都安装了一个改变事件监听器，它负责把当前的滑动条值显示到框架底部的文本域中

> 程序清单 12-7 slider/SliderFrame.java
>
> ```java
> package slider;
> import java.awt.*;
> import java.util.*;
> import javax.swing.*;
> import javax.swing.event.*;
> /**
> * A frame with many sliders and a text field to show slider values. */
> public class SliderFrame extends JFrame
> {
> 	private JPanel sliderPanel;
> 	private JTextField textField;
> 	private ChangeListener listener;
> 	public Slide JFrame()
> 	{
> 		sliderPanel = new JPanel();
> 		sliderPanel.setLayout(new GridBagLayout());
> 		// common listener for all sliders
> 		listener = event -> {
> 		// update text field when the slider value changes
> 			JSlider source = (JSIider) event.getSource();
> 			textField.setText("" + source.getValue());
> 		};
> 		// add a plain slider
> 		JSlider slider = new JSlider();
> 		addSlider(slider, "Plain");
> 		// add a slider with major and minor ticks
> 		slider = new JSlider();
> 		slider.setPaintTicks(true);
> 		slider.setMajorTickSpacing(20);
> 		slider.setMinorTickSpacing(5);
> 		addSlider(slider, "Ticks");
> 		// add a slider that snaps to ticks
> 		slider = new JSlider();
> 		slider.setPaintTicks(true);
> 		slider.setSnapToTicks(true);
> 		slider.setMajorTickSpacing(20);
> 		slider.setMinorTickSpacing(5);
> 		addSlider(slider, "Snap to ticks");
> 		// add a slider with no track
> 		slider = new JSlide();
> 		slider.setPaintTicks(true);
> 		slider.setMajorTickSpacing(20);
> 		slider.setMinorTickSpacing(5);
> 		slider.setPaintTrack(false);
> 		addSlider(slider, "No track")；
> 		// add an inverted slider
> 		slider = new JSlider();
> 		slider.setPaintTicks(true);
> 		slider.setMajorTickSpacing(20);
> 		slider.setMinorTickSpacing(5);
> 		slider.setlnverted(true);
> 		addSlider(slider, "Inverted");
> 		// add a slider with numeric labels
> 		slider = new JSlider();
> 		slider.setPaintTicks(true);
> 		slider.setPaintLabels(true);
> 		siider.setMajorTickSpacing(20);
> 		slider.setMinorTickSpacing(5);
> 		addSlider(slider, "Labels");
> 		// add a slider with alphabetic labels
> 		slider = new JSlider();
> 		siider.setPaintLabels(true);
> 		slider.setPaintTicks(true);
> 		slider.setMajorTickSpacing(20);
> 		slider.setMinorTickSpacing(5);
> 		Dictionary<lnteger, Component〉labelTable = new Hashtable<>();
> 		labelTable.put(0，new JLabel("A"));
> 		labelTable.put(20, new JLabel("B"));
> 		labelTable,put(40, new JLabel("C"));
> 		labelTable.put(60, new JLabel("D"));
> 		labelTable,put(80, new JLabel("E"));
> 		labelTable.put(100, new JLabel("F"));
> 		slider.setLabelTable(labelTable);
> 		addSlider(slider, "Custom labels");
> 		// add a slider with icon labels
> 		slider = new JSlider();
> 		slider.setPaintTicks(true);
> 		slider.setPaintLabels(true);
> 		siider.setSnapToTicks(true);
> 		slider.setMajorTickSpacing(20); 
> 		slider.setMinorTickSpacing(20);
> 		labelTable = new Hashtable<Integer, Component>();
> 		// add card images
> 		labelTable.put(0, new JLabel(new Imagelcon("nine.gif")));
> 		labelTable,put(20, new JLabel(new ImagelconC"ten.gif")));
> 		labelTable,put(40, new JLabel(new Imagelcon("jack,gif")));
> 		labelTable,put(60, new JLabel(new Imagelcon("queen,gif")));
> 		labelTable,put(80, new JLabel(new Imagelcon("king.gif")));
> 		labelTable,put(100, new JLabel(new Imagelcon("ace.gif")));
> 		slider,setLabelTable(labelTable);
> 		addSlider(slider, "Icon labels");
> 		// add the text field that displays the slider value
> 		textField = new JTextField();
> 		add(s1iderPane1, BorderLayout.CENTER);
> 		add(textField, BorderLayout.SOUTH);
> 		pack();
> 	}
> 	/**
> 	* Adds a slider to the slider panel and hooks up the listener
> 	* @param s the slider
> 	* @param description the slider description
> 	*/
> 	public void addSlider(Slider s, String description)
> 	{
> 		s.addChangeListenerflistener);
> 		jPanel panel = new JPanel();
> 		panel.add(s);
> 		panel.add(new JLabel(description));
> 		panel.setAlignmentX(Component.LEFT_ALIGNMENT);
> 		GridBagConstraints gbc = new CridBagConstraintsO;
> 		gbc.gridy = sliderPanel.getComponentCountO；
> 		gbc.anchor = gridBagConstraints.WEST;
> 		slidePanel.add(panel, gbc);
> 	}
> }
> ```

>  javax.swing.JSlider 1.2
>
> ```java
> JSlider();
> JS1ider(int direction);
> JS1ider(int min, int max);
> JS1ider( int min, int max, int initialValue);
> JS1ider(int direction, int min, int max, int initialValue); //用给定的方向、最大值、 最小值和初始化值构造一个水平滑动条。 
> //参数： direction     SwingConstants.HORIZONTAL或 SwingConstants.VERTICAL之一。默认为水平。
> //min, max   滑动条的最大值、最小值。默认值为0到100。
> //initialValue   滑动条的初始化值。默认值为50。
> void setPaintTicks(boolean b); //如果b为true, 显示标尺。
> void setMajorTickSpacing(int units);
> void setMinorTickSpacing(int units); //用给定的滑动条单位的倍数设置大标尺和小标尺。
> void setPaintLabels(boolean b); //如果b是ture，显示标尺标签。
> void setLabelTab e(Dictionary table); //设置用于作为标尺标签的组件。表中的每一个键/值对都采用new Integer(value)/component的格式
> void setSnapToTicks(boolean b); //如果b是true，每一次调整后滑块都要对齐到最接近的标尺处。
> void setPaintTrack(boolean b); //如果b是ture，显示滑动条滑动的轨迹。
> ```

# 5 菜 单
前面介绍了几种最常用的可以放到窗口内的组件， 如：各种按钮、文本域以及组合框等。 Swing还提供了一些其他种类的用户界面兀素，下拉式菜单就是 GUI 应用程序中很常见的一种。

位于窗口顶部的菜单栏 （menubar) 包括了下拉菜单的名字。点击一个名字就可以打开包含菜单项 （ menu items) 和子菜单（submenus) 的菜单。 当用户点击菜单项时，所有的菜单都会被关闭并且将一条消息发送给程序。图12-19显示了一个带子菜单的典型菜单。

## 5.1 菜单创建

创建菜单是一#非常容易的事情。首先要创建一个菜单栏： 

```java
JMenuBar menuBar = newJMenuBar(); 
```

菜单栏是一个可以添加到任何位置的组件。通常放置 图 12-19 带有子菜单的菜单在框架的顶部。可以调用setJMenuBar方法将菜单栏添加到框架上：

```java
frame.setJMenuBar(menuBar);
```

 需要为每个菜单建立一个菜单对象：

```java
JMenu editMenu = new JMenu("Edit");
```

然后将顶层菜单添加到菜单栏中： 

```java
menuBar.add(editMenu);
```

 向菜单对象中添加菜单项、分隔符和子菜单：

```java
JMenuItem pasteltem = new JMenuItem("Paste");
editMenu.add(pasteltem);
editMenu.addSeparator();
JMenu optionsMenu = ...; // a submenu
editMenu.add(optionsMenu);
```

可以看到图12-19中分隔符位于Paste和Read-only菜单项之间。当用户选择菜单时，将触发一个动作事件。这里需要为每个菜单项安装一个动作监听器。 

```java
ActionListener listener = ...;
pastelteiaddActionListener(listener);
```

 可以使用JMenu.add(Striiigs)方法将菜单项插入到菜单的尾部，例如： 

```java
editMenu.add("Paste");
```

Add方法返回创建的子菜单项。可以采用下列方式获取它，并添加监听器：

```java
JMenuItem pasteltem = editMenu.add("Paste"); pasteltem.addActionListener(listener);
```

在通常情况下，菜单项触发的命令也可以通过其他用户界面元素（如工具栏上的按钮) 激活。在第11章中，已经看到了如何通过 Action对象来指定命令。通常，采用扩展抽象类 AbstractAction来定义一个实现 Action 接口的类。这里需要在AbstractAction对象的构造器中 指定菜单项标签并且覆盖 actionPerformed方法来获得菜单动作处理器。例如：

```java
Action exitAction = new AbstractAction("Exit") // menu item text goes here
{
	public void actionPerformed(ActionEvent event)
	{
		// action code goes here
		System.exit(0);
	}
};
```

 然后将动作添加到菜单中：

```
JMenuItem exitltem = fileMenu.add(exitAction);
```

这个命令利用动作名将一个菜单项添加到菜单中。这个动作对象将作为它的监听器。上面这条语句是下面两条语句的快捷形式：

```java
JMenuItem exitltem = new JMenuItem(exitAction); fileMenu.add(exitltem);
```

> javax.swing.JMenu1.2 
>
> ```java
> JMenu(String label); //用给定标签构造一个菜单。
> JMenuItem add(JMenuItem item); //添加一个菜单项（或一个菜单)。
> JMenuItem add(String label); //用给定标签将一个菜单项添加到菜单中， 并返回这个菜单项。
> JMenuItem add(Action a); //用给定动作将一个菜单项添加到菜单中， 并返回这个菜单项。
> void addSeparator(); //将一个分隔符行（separatorline)添加到菜单中。 JMenuItem insert(JMenuItem menu, int index); //将一个新菜单项（或子菜单）添加到菜单的指定位置。
> JMenuItem insert(Action a, int index); //用给定动作在菜单的指定位置添加一个新菜单项。
> void insertSeparator(int index); //将一个分隔符添加到菜单中。 
> //参数:index   添加分隔符的位置
> void remove(int index);
> void remove(JMenuItem item); //从菜单中删除指定的菜单项。 
> ```

> javax.swing.JMenuItem1.2 
>
> ```java
> JMenuItem(String label); //用给定标签构造一个菜单项。 
> JMenuItemCAction a)1.3 为给定动作构造一个菜单项。
> ```

> javax.swing.AbstractButton1.2 
>
> ```java
> void setAction(Action a)1.3 为这个按钮或菜单项设置动作
> ```

>  javax.swing.JFrame1.2
>
> ```java
> void setJMenuBar(JMenuBar menubar); //为这个框架设置菜单栏。
> ```

## 5.2 菜单项中的图标

菜单项与按钮很相似。实际上，JMenuItem类扩展了AbstractButton类。与按钮一样，菜单可以包含文本标签、图标，也可以两者都包含。既可以利用 JMenuItem(String，Icon) 或者 JMenuItem(Icon) 构造器为菜单指定一个图标，也可以利用JMenuItem类中的setlcon方法 (继承自AbstractButton类）指定一个图标。例如：

```
JMenuItem cutltem = new JMenuItem("Cut", new Imagelcon("cut.gif"));
```

图12-19展示了具有图标的菜单项。正如所看到的，在默认情况下，菜单项的文本被放置在 图标的右侧。如果喜欢将文本放置在左侧，可以调用JMenuItem类中的setHorizontalTextPosition方法（继承自 AbstractButton 类）设置。例如： 

```
cutltem.setHorizontalTextPosition(SwingConstants.LEFT); 
```

这个调用把菜单项文本移动到图标的左侧。 也可以将一个图标添加到一个动作上： 

```
cutAction.putValue(Action.SMALL.ICON, new Imagelcon("cut.gif"));
```

当使用动作构造菜单项时，Action.NAME值将会作为菜单项的文本，而Action.SMALL_ ICON将会作为图标。 另外，可以利用AbstractAction构造器设置图标：

```java
cutAction = new AbstractAction("Cut", new Imagelcon("cut.gif"))
{
	public void actionPerformed(ActionEvent event)
	{
		...
	}
};
```

> javax.swing.JMenuItem1.2
>
> ```java
> JMenuItem(String label, Icon icon); //用给定的标签和图标构造一个菜单项。 
> ```

> javax.swing.AbstractButton1.2
>
> ```java
> void setHorizontalTextPosition(int pos); //设置文本对应图标的水平位置。
> //参数：pos   SwingConstants.RIGHT(文本在图标的右侧)或SwingConstants.LEFT
> ```
>
> javax.swing.AbstractAction1.2
>
> ```java
> AbstractAction(String name, Icon smallIcon); //用给定的名字和图标构造一个抽象的动作。
> ```

## 5.3 复选框和单选钮菜单项

复选框和单选钮菜单项在文本旁边显示了一个复选框或一个单选钮（参见图 12-19)。当用户选择一个菜单项时，菜单项就会自动地在选择和未选择间进行切换。除了按钮装饰外，同其他菜单项的处理一样。例如，下面是创建复选框菜单项的代码： 

```java
JCheckBoxHenuItem readonlyltem = new JCheckBoxMenuItem("Read-only"); optionsMenu.add(readonlyltem);
```

单选钮菜单项与普通单选钮的工作方式一样，必须将它们加人到按钮组中。当按钮组中的一个按钮被选中时，其他按钮都自动地变为未选择项。 

```java
ButtonGroup group = new ButtonGroup();
JRadioButtonMenuItem insertltem = new JRadioButtonMenuItem("Insert");
insertltem.setSelected(true);
JRadioButtonMenuItem overtypeltem = new JRadioButtonMenuItem("Overtype")； group.add(insertltem);
group.add(overtypeltem);
optionsMenu.add(insertltem);
optionsMenu.add(overtypeltem); 
```

使用这些菜单项，不需要立刻得到用户选择菜单项的通知。而是使用isSelected方法来测试菜单项的当前状态（当然，这意味着应该保留一个实例域保存这个菜单项的引用）。使用 setSelected方法设置状态。

> javax.swing.JCheckBoxMenultem 1.2
>
> ```
> JCheckBoxMenuItem(String label); //用给定的标签构造一个复选框菜单项。 JCheckBoxMenuItem(String label, boolean state); //用给定的标签和给定的初始状态（true 为选定）构造一个复选框菜单。
> ```

> 因javax.swing.JRadioButtonMenultem 1.2
>
> ```
> JRadioButtonMenuItem(String label); //用给定的标签构造一个单选钮菜单项。
> JRadioButtonMenuItemCString label , boolean state); //用给定的标签和给定的初始状态（true 为选定）构造一个单选钮菜单项。
> ```

> javax.swing.AbstractButton 1.2
>
> ```
> boolean isSelected();
> void setSelected(boo1ean state); //获取或设置这个菜单项的选择状态（true 为选定)。
> ```

## 5.4 弹出菜单

弹出菜单（pop-up menu) 是不固定在菜单栏中随处浮动的菜单（参见图 12-20 )。

创建一个弹出菜单与创建一个常规菜单的方法类似，但是弹出菜单没有标题。

```
 JPopupMenu popup = new JPopupMenu();
```

然后用常规的方法添加菜单项： 

```
JMenuItem item = new JMenuItem("Cut");
item.addActionListener(1istener);
popup.add(item);
```

弹出菜单并不像常规菜单栏那样总是显示在框架的顶部，必须调用show方法菜单才能显示出来。调用时需要给 出父组件以及相对父组件坐标的显示位置。例如：

```java
popup.show(panel, x, y);
```

通常，当用户点击某个鼠标键时弹出菜单。 这就是所谓的弹出式触发器（pop-up trigger)。在Windows或者Linux中，弹出式触发器是鼠标右键。要想在用户点击某一个组件时弹出菜单， 需要按照下列方式调用方法：

```java
component.setComponentPopupMenu(popup);
```

偶尔会遇到在一个含有弹出菜单的组件中放置一个组件的情况。这个子组件可以调用下 列方法继承父组件的弹出菜单。调用：

```java
child.setlnheritsPopupMenu(true);
```

> javax.swing.JPopupMenu 1.2 
>
> ```java
> void show(Component c, int x, int y); //显示一个弹出菜单。
> //参数： c  显示弹出菜单的组件
> //x,y  弹出菜单左上角的坐标（C 的坐标空间内）
> boolean isPopupTrigger(MouseEvent event) 1.3 //如果鼠标事件是弹出菜单触发器，则返回 true。 
> ```

> java.awt.event.MouseEvent 1.1
>
> ```
> boolean isPopupTrigger(); //如果鼠标事件是弹出菜单触发器， 则返回 true。
> ```

> javax.swing.JComponent 1.2
>
> ```java
> JPopupMenu getComponentPopupMenu( ) 5.0
> void setComponentPopupMenu(JPopupMenu popup) 5.0 //获取或设置用于这个组件的弹出菜单。
> boolean getlnheritsPopupMenu( ) 5.0
> void setInheritsPopupMenu(boolean b) 5.0 //获取或设置 inheritsPopupMenu 特性。 如果这个特性被设置或这个组件的弹出菜单为null, 则应用上一级弹出菜单。
> ```

## 5.5 快捷键和加速器
对于有经验的用户来说，通过快捷键来选择菜单项会感觉更加便捷。可以通过在菜单项的构造器中指定一个快捷字母来为菜单项设置快捷键：

```
JMenuItem aboutltem = new JMenuItem("About", 'A');
```

快捷键会自动地显示在菜单项中，并带有一条下划线 (如图 12-21 所示)。例如，在上面的例子中，菜单项中的标签为“About”，字母A带有一个下划线。当显示菜单时，用户只需要按下“ A” 键就可以这个选择菜单项（如果快捷 字母没有出现在菜单项标签字符串中， 同样可以按下快捷键选择菜单项，只是快捷键没有显示出来。很自然，这种不可见的快捷键没有提示效果)。

有时候不希望在菜单项的第一个快捷键字母下面加下划线。例如，如果在菜单项“Save As”中使用快捷键“A”，则在第二个“A” （Save As) 下面加下划线更为合理。可以调用 setDisplayedMnemonicIndex方法指定希望加下划线的字符。 

如果有一个Action对象，就可以把快捷键作为Action.MNEMONIC_KEY的键值添加到对象中。如：

```java
cutAction.putValue(Action,MNEMONIC_KEY, new Integer('A'));
```

只能在菜单项的构造器中设定快捷键字母，而不是在菜单构造器中。如果想为菜单设置快捷键，需要调用setMnemonic方法：

```
JMenu helpMenu = new JMenu("Help");
helpMenu.setMnemonic('H');
```

可以同时按下ALT键和菜单的快捷键来实现在菜单栏中选择一个顶层菜单的操作。例如：按下ALT+H可以从菜单中选择Help菜单项。

可以使用快捷键从当前打开的菜单中选择一个子菜单或者菜单项。而加速器是在不打开 菜单的情况下选择菜单项的快捷键。例如： 很多程序把加速器CTRL+O和CTRL+S关联到File菜单中的Open和Save菜单项。可以使用setAccelerator将加速器键关联到一个菜单项上。这个方法使用 Keystroke类型的对象作为参数。例如：下面的调用将加速器CTRL+O关联到Openltem菜单项。

```
openItem.setAccelerator(KeyStroke.getKeyStroke("ctrl 0"));
```

当用户按下加速器组合键时，就会自动地选择相应的菜单项，同时激活一个动作事件，这与手工地选择这个菜单项一样。

加速器只能关联到菜单项上，不能关联到菜单上。加速器键并不实际打开菜单。它将直接地激活菜单关联的动作事件。

从概念上讲，把加速器添加到菜单项与把加速器添加到Swing组件上所使用的技术十分类似（在第 11 章中讨论了这个技术)。但是，当加速器添加到菜单项时，对应的组合键就会自动地显示在相应的菜单上（见图 12-22 )。

> 注释：在Windows下，ALT+F4用于关闭窗口。 但这不是Java程序设定的加速键。这是操作系统定义的快捷键。这个组合键总会触发活动窗口的WindowClosing事件，而不管菜单上是否有Close菜单项

> javax.swing.JMenultem 1.2
>
> ```java
> JMenuItem(String label, int mnemonic); //用给定的标签和快捷键字符构造一个菜单项。
> //参数：label  菜单项的标签
> //mnemonic  菜单项的快捷键字符，在标签中这个字符下面会有一个下划线。
> void setAccelerator(Keystroke k); //将k设置为这个菜单项的加速器。加速器显示在标签的旁边。
> ```

> javax.swing.AbstractButton 1.2
>
> ```java
> void setMnemonic(int mnemonic); //设置按钮的快捷字符。该字符会在标签中以下划线的形式显示。
> void setDisplayedMnemoniclndex(int index) 1.4 将按钮文本中的index字符设定为带下划线。如果不希望第一个出现的快捷键字符带下划线， 就可以使用这个方法。
> ```

## 5.6 启用和禁用菜单项

在有些时候，某个特定的菜单项可能只能够在某种特定的环境下才可用。例如，当文档以只读方式打开时，Save菜单项就没有意义。当然，可以使用JMemremove方法将这个菜单项从菜单中删掉，但用户会对菜单内容的不断变化感到奇怪。然而，可以将这个菜单项设为禁用状态，以便屏蔽掉这些暂时不适用的命令。被禁用的菜单项被显示为灰色，不能被选择它（见图 12-23 )。 

启用或禁用菜单项需要调用setEnabled方法： 

```
saveltem.setEnabled(false);
```

启用和禁用菜单项有两种策略。每次环境发生变化就对相关的菜单项或动作调用setEnabled。例如： 只要当文档以只读方式打开，就禁用Save和Save As菜单项。另一种 方法是在显示菜单之前禁用这些菜单项。这里必须为“菜单选中” 事件注册监听器。javax.swing.event包定义了MenuListener接口，它包含三个方法：

```java
void menuSelected(MenuEvent event)
void menuDeselected(MenuEvent event)
void menuCanceled(MenuEvent event)
```

由于在菜单显示之前调用menuSelected方法，所以可以在这个方法中禁用或启用菜单项。下面代码显示了只读复选框菜单项被选择以后，如何禁用Save和Save As动作。

```java
public void menuSelected(MenuEvent event)
{
	saveAction.setEnabled(!readonlyltem.isSelected());
	saveAsAction.setEnabled(!readonlyltem.isSelected());
}
```

警告：在显示菜单之前禁用菜单项是一种明智的选择， 但这种方式不适用于带有加速键的菜单项。这是因为在按下加速键时并没有打开菜单，因此动作没有被禁用，致使加速键还会触发这个行为。 

> javax.swing.JMenultem 1.2
>
> ```
> void setEnabled(boolean b); //启用或禁用菜单项。
> ```

> javax.swing.event.MenuListener 1.2
>
> ```
> void menuSelected(MenuEvent e); //在菜单被选择但尚未打开之前调用。 void menuDeselected(MenuEvent e); //在菜单被取消选择并且已经关闭之后被调用。 •void menuCanceled(MenuEvent e); //当菜单被取消时被调用。例如， 用户点击菜单以外的区域。
> ```

程序清单12-8是创建一组菜单的示例程序。这个程序演示了本节介绍的所有特性，包括： 嵌套菜单、禁用菜单项、复选框和单选钮菜单项、弹出菜单以及快捷键和加速器。

> 程序清单 12-8 menu/MenuFrame.java
>
> ```java
> package menu;
> import java.awt.event.*;
> import javax.swing.*;
> /**
> * A frame with a sample menu bar.
> */
> public class MenuFrame extends JFrame
> {
> 	private static final int DEFAULT_WIDTH = 300;
> 	private static final int DEFAULTJEICHT = 200;
> 	private Action saveAction;
> 	private Action saveAsAction;
> 	private JCheckBoxMenuItem readonlyltem;
> 	private JPopupMenu popup;
> 	/**
> 	* A sample action that prints the action name to System.out
> 	*/
> 	class TestAction extends AbstractAction
> 	{
> 		public TestAction(String name)
> 		{
> 			super(name);
> 		}
> 		public void actionPerformed(ActionEvent event)
> 		{
> 			System.out.println(getValue(Action.NAME) + " selected.");
> 		}
> 	}
> 	public MenuFrame()
> 	{
> 		setSize(DEFAULT.WIDTH, DEFAULTJEIGHT);
> 		JMenu fileMenu = new JMenu("FileH);
> 		fileMenu.add(new TestAction("New"));
> 		// demonstrate accelerators
> 		JMenuItem openltem = fileMenu.add(new TestAction("0pen"));
> 		openltem.setAccelerator(KeyStroke.getKeyStroke("ctrl 0"));
> 		fileMenu.addSeparator();
> 		saveAction = new TestAction("Save");
> 		JMenuItem saveltem = fileMenu.add(saveAction);
> 		saveltem.setAccelerator(KeyStroke.getKeyStroke("ctrl S"));
> 		saveAsAction = new TestAction("Save As");
> 		fileMenu.add(saveAsAction);
> 		fileMenu.addSeparator();
> 		fileMenu.add(new AbstractAction("Exit")
> 		{
> 			public void actionPerformed(ActionEvent event)
> 			{
> 				System,exit(0);
> 			}
> 		});
> 		// demonstrate checkbox and radio button menus
> 		readonlyltem = new JCheckBoxMenuItem("Read-only");
> 		readonlyltem.addActionListener(new ActionListener()
> 		{
> 			public void actionPerformed(ActionEvent event)
> 			{
> 				boolean saveOk = !readonlyltem.isSelected();
> 				saveAction.setEnabled(saveOk);
> 				saveAsAction.setEnabled(saveOk);
> 			}
> 		});
> 		ButtonCroup group = new ButtonGroup();
> 		JRadioButtonMenuIteni insertltem = new JRadioButtonMenuItem("Insert");
> 		insertltem.setSelected(true);
> 		JRadioButtonMenuItem overtypeltem = new JRadioButtonMenuItem("Overtype");
> 		group.add(insertltem);
> 		group.add(overtypeltem);
> 		// demonstrate icons
> 		Action cutAction = new TestAction("Cut");
> 		cutAction.putValue(Action.SMALL_IC0N, new Imagelcon("cut.gif"));
> 		Action copyAction = new TestAction("Copy");
> 		copyAction.putValue(Action.SMALL_IC0N, new Imagelcon("copy.gif")); Action pasteAction = new TestAction("Paste");
> 		pasteAction.putValue(Action.SMALL_ICON, new ImageIcon("paste.gif"));
> 		JMenu editMenu = new JMenu("Edit");
> 		editMenu.add(cutAction);
> 		editMenu.add(copyAction);
> 		editMenu.add(pasteAction);
> 		// demonstrate nested menus
> 		JMenu optionMenu = new JMenu("Options");
> 		optionMenu.add(readonlyltem);
> 		optionMenu.addSeparator();
> 		optionMenu.add(insertltem);
> 		optionMenu.add(overtypeltem);
> 		editMenu.addSeparator();
> 		editMenu.add(optionMenu);
> 		// demonstrate mnemonics
> 		JMenu helpMenu = new JMenu("Help");
> 		helpMenu.setMnemonic('H');
> 		JMenuItem indexltem = new JMenuItem("Index");
> 		indexltem.setMnemonic(T);
> 		helpMenu.add(indexltem);
> 		// you can also add the mnemonic key to an action
> 		Action aboutAction = new TestAction("About");
> 		aboutAction.putValue(Action.MNEM0NIC_KEY, new Integer('A')); 
> 		helpMenu.add(aboutAction);
> 		// add all top-level menus to menu bar 
> 		JMenuBar menuBar = new JMenuBar();
> 		setJMenuBar(menuBar);
> 		menuBar.add(fileMenu);
> 		menuBar.add(editMenu);
> 		menuBar.add(helpMenu);
> 		// demonstrate pop-ups
> 		popup = new JPopupMenu();
> 		popup.add(cutAction);
> 		popup.add(copyAction);
> 		popup.add(pasteAction);
> 		JPanel panel = new JPanel();
> 		panel.setComponentPopupMenu(popup);
> 		add(panel);
> 	}
> }
> ```

 ## 5.7 工具栏

工具栏是在程序中提供的快速访问常用命令的按钮栏， 如图 12-24 所示。

工具栏的特殊之处在于可以将它随处移动。可以将它拖拽到框架的四个边框上，如图12-25所示。释放鼠标按钮后，工具栏将会停靠在新的位置上，如图12-26所示。

> 注释： 工具栏只有位于采用边框布局或者任何支持 North、 East、South 和 West 约束布局 管理器的容器内才能够被拖拽。 

工具栏可以完全脱离框架。 这样的工具栏将包含在自己的框架中， 如图 12-27 所示。当 关闭包含工具栏的框架时， 它会冋到原始的框架中。

编写创建工具栏的代码非常容易，并且可以将组件添加到工具栏中：

```java
JToolBar bar = new JToolBar();
bar.add(blueButton);
```

JToolBar类还有一个用来添加Action对象的方法，可以用Action对象填充工具栏：

```java
bar.add(blueAction);
```

这个动作的小图标将会出现在工具栏中。可以用分隔符将按钮分组：

```java
bar.addSeparator();
```

例如，图12-24中的工具栏有一个分隔符，它位于第三个按钮和第四个按钮之间。然后，将工具栏添加到框架中：

```java
add(bar, BorderLayout.NORTH);
```

当工具栏没有停靠时，可以指定工具栏的标题：

```java
bar = new JToolBar(titleString);
```

在默认情况下，工具栏最初为水平的。如果想要将工具栏垂直放置，可以使用下列代码： 

```java
bar = new JToolBar(SwingConstants.VERTICAL)
```

或者

```java
bar = new JToolBar(titleString, SwingConstants.VERTICAL)
```

按钮是工具栏中最常见的组件类型。然而工具栏中的组件并不仅限如此。例如，可以往工具栏中加入组合框。

## 5.8 工具提示

工具栏有一个缺点，这就是用户常常需要猜测按钮上小图标按钮的含义。为了解决这个问题，用户界面设计者发明了工具提示（tooltips)。当光标停留在某个按钮上片刻时，工具提 示就会被激活。工具提示文本显示在一个有颜色的矩形里。 当用户移开鼠标时，工具提示就会自动地消失。如图12-28所示。

在Swing中，可以调用setToolText方法将工具提不添加到Component上：

```java
exitButton.setToolTipText("Exit");
```

还有一种方法是，如果使用Action对象，就可以用SHORT_DESCRIPTION关联工具提示： 

```java
exitAction.putValue(Action.SHORTJESCRIPnON, "Exit");
```

程序清单12-9说明了如何将一个Action对象添加到菜单和工具栏中。注意，动作名在菜单中就是菜单项名，而在工具栏中就是简短的说明。 

> 程序清单 12-9 tooBar/TooBarFrame.java
>
> ```java
> package toolBar;
> import java.awt.*;
> import java.awt.event.*;
> import javax.swing.*;
> /**
> * A frame with a toolbar and menu for color changes.
> */
> public class ToolBarFrame extends JFrame
> {
> 	private static final int DEFAULT_WIDTH = 300;
> 	private static final int DEFAULT一HEIGHT = 200;
> 	private JPanel panel;
> 	public ToolBarFrame()
> 	{
> 		setSize(DEFAULT_WIDTH, DEFAULTJEICHT);
> 		// add a panel for color change
> 		panel = new JPanel();
> 		add(panel, BorderLayout.CENTER); 
> 		// set up actions
> 		Action blueAction = new ColorAction("Blue", new Imagelcon("blue-ball.gif"), Color.BLUE);
> 		Action yellowAction = new ColorAction("Yellow", new Imagelcon("yel1ow-bal1.gif"),Color.YELLOW);
> 		Action redAction = new ColorAction("Red", new Imagelcon("red-ball,gif"),Color.RED);
> 		Action exitAction = new AbstractAction("Exit", new Imagelcon("exit,gif"))
> 		{
> 			public void actionPerformed(ActionEvent event)
> 			{
> 				System.exit(O);
> 			}
> 		};
> 		exitAction.putValue(Action.SH0RT_JESCRIPTI0N, "Exit");
> 		// populate toolbar
> 		JToolBar bar = new JToolBar();
> 		bar.add(blueAction);
> 		bar.add(yellowAction);
> 		bar.add(redAction);
> 		bar.addSeparator();
> 		bar.add(exitAction);
> 		add(bar, BorderLayout.NORTH);
> 		// populate menu
> 		JMenu menu = new JMenu("Color");
> 		menu.add(yellowAction);
> 		menu.add(blueAction);
> 		menu.add(redAction);
> 		menu.add(exitAction);
> 		JMenuBar menuBar = new JMenuBar();
> 		menuBar.add(menu);
> 		setjMenuBar(menuBar);
> 	}
> 		/**
> 		* The color action sets the background of the frame to a given color.
> 		*/
> 	class ColorAction extends AbstractAction
> 	{
> 		public ColorAction(String name, Icon icon, Color c)
> 		{
> 			putValue(Action.NAME, name);
> 			putValue(Action.SMALL_ICON, icon);
> 			putValue(Action.SHORTJESCRIPTION, name + " background");
> 			putValue("Color", c);
> 		}
> 		public void actionPerformed(ActionEvent event)
> 		{
> 			Color c = (Color) getValue("Color");
> 			panel.setBackground(c);
> 		}
> 	}
> }
> ```

> javax.swing.JToolBar 1.2
>
> ```java
> JToolBar();
> JToolBar(String titlestring);
> JToolBar(int orientation);
> JToolBar(String titeString, int orientation); //用给定的标题字符串和方向构造一个工具栏。Orientation可以是SwingConstants.HORIZONTAL (默认）或SwingConstants.VERTICAL。
> JButton add(Action a); //用给定的动作名、图标、简要的说明和动作回调构造一个工具栏中的新按钮。
> void addSeparator(); //将一个分隔符添加到工具栏的尾部。
> ```

> javax.swing.JComponent 1.2
>
> ```java
> void setToolTipText(String text); //设置当鼠标停留在组件上时显示在工具提示中的文本
> ```

# 6 复杂的布局管理

# 7 对话框

