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

上一章主要介绍了如何使用Java中的事件模式。通过学习读者已经初步知道了构造图形用户界面的基本方法。本章将介绍构造功能更加齐全的图形用户界面(GUI) 所需要的一些重要工具。下面，首先介绍Swing的基本体系结构。要想弄清如何有效地使用一些更高级的组件，必须了解底层的东西。然后，再讲述Swing中各种常用的用户界面组件，如文本框、单选按钮以及菜单等。接下来，介绍在不考虑特定的用户界面观感时，如何使用Java中的布局管理 器排列在窗口中的这些组件。最后，介绍如何在Swing中实现对话框。本章囊括了基本的Swing组件，如文本组件、按钮和滑块等，这些都是基本的用户界面组件，使用十分频繁。Swing中的高级组件将在卷n中讨论。

# 1 Swing 和模型-视图-控制器设计模式
前面说过，本章将从Swing组件的体系结构开始。首先，我们讨论设计模式的概念，然后再看一下Swing框架中最具影响力的“模型-视图-控制器”模式。
## 1.1 设计模式
在“模型-视图-控制器”模式中，背景是显示信息和接收用户输人的用户界面系统。有关“模型-视图-控制器”模式将在接下来的章节中讲述。这里有几个冲突因素。对于同一数据来说，可能需要同时更新多个可视化表示。例如，为了适应各种观感标准，可能需要 改变可视化表示形式；又例如，为了支持语音命令，可能需要改变交互机制。解决方案是将这些功能分布到三个独立的交互组件：模型、视图和控制器。模型-视图-控制器模式并不是AWT和Swing设计中使用的唯一模式。下列是应用的 另外几种模式： 

- 容器和组件是“组合(composite)” 模式
- 带滚动条的面板是“ 装饰器(decorator)” 模式
- 布局管理器是“策略(strategy)” 模式

设计模式的另外一个最重要的特点是它们已经成为文化的一部分。只要谈论起模型-视图-控制器或“装饰器” 模式，遍及世界各地的程序员就会明白。因此，模式已经成为探讨设计方案的一种有效方法。

## 1.2 模型-视图-控制器模式

让我们稍稍停顿一会儿，回想一下构成用户界面组件的各个组成部分，例如，按钮、复选框、文本框或者复杂的树形组件等。每个组件都有三个要素： 
- 内容，如：按钮的状态（是否按下)，或者文本框的文本。 
- 外观（颜色，大小等)。 
- 行为（对事件的反应)。 

这三个要素之间的关系是相当复杂的，即使对于最简单的组件（如：按钮）来说也是如此。很明显，按钮的外观显示取决于它的观感。Metal按钮的外观与 Windows按钮或者Motif按钮的外观就不一样。另外，外观显示还要取决于按钮的状态：当按钮被按下时，按钮需要 被重新绘制成另一种不同的外观。而状态取决于按钮接收到的事件。当用户在按钮上点击时，按钮就被按下。 

当然，在程序中使用按钮时，只需要简单地把它看成是一个按钮，而不需要考虑它的内部工作和特性。毕竟，这些是实现按钮的程序员的工作。无论怎样，实现按钮的程序员就要 对这些按钮考虑得细致一些了。毕竟，无论观感如何，他们必须实现这些按钮和其他用户界 面组件，以便让这些组件正常地工作。为了实现这样的需求，Swing设计者采用了一种很有名的设计模式(design pattern):模型 -视图 -控制器(model-view-controller)模式。这种设计模式同其他许多设计模式一样，都遵循第5章介绍过的面向对象设计中的一个基本原则： 限制一个对象拥有的功能数量。不要用一个按钮类完成所有的事情，而是应该让一个对象负责组件的观感，另一个对象负责存 储内容。模型-视图-控制器（MVC) 模式告诉我们如何实现这种设计，实现三个独立的类：

- 模型(model): 存储内容。
- 视图(view): 显示内容。
- 控制器(controller): 处理用户输入。 

这个模式明确地规定了三个对象如何进行交互。模型存储内容，它没有用户界面。按钮的内容非常简单，只有几个用来表示当前按钮是否按下，是否处于活动状态的标志等。文本框内容稍稍复杂一些，它是保存当前文本的字符串对象。这与视图显示的内容并不一致---如果内容的长度大于文本框的显示长度，用户就只能看到文本框可以显示的那一部分

模型必须实现改变内容和查找内容的方法。例如，一个文本模型中的方法有：在当前文本中添加或者删除字符以及把当前文本作为一个字符串返回等。记住：模型是完全不可见的。 显示存储在模型中的数据是视图的工作。

> 注释：“模式”这个术语可能不太贴切，因为人们通常把模式视为一个抽象概念的具体表 示。汽车和飞机的设计者构造模式来模拟真实的汽车和飞机。但这种类比可能会使你对模型-视图-控制器模式产生错误的理解。在设计模式中，模型存储完整的内容，视图给出了内容的可视化显示（完整或者不完整）。一个更恰当的比喻应当是模特为画家摆好姿势。
>
> 此时，就要看画家如何看待模特，并由此来画一张画了。那张画是一幅规矩的肖像画， 或是一幅印象派作品，还是一幅立体派作品（以古怪的曲线来描绘四肢）完全取决于画家。 

模型-视图-控制器模式的一个优点是一个模型可以有多个视图，其中每个视图可以显示全部内容的不同部分或不同形式。 例如，一个HTML编辑器常常为同一内容在同一时刻提供两个视图：一个 WYSIWYG (所见即所得）视图和一个“ 原始标记” 视图（见图 12-3 )。当通过某一个视图的控制器对模型进行更新时，模式会把这种改变通知给两个视图。视图得到通知以后就会自动地刷新。当然，对于一个简单的用户界面组件来说，如按钮，不需要为同一模型提供多个视图。

控制器负责处理用户输入事件，如点击鼠标和敲击键盘。然后决定是否把这些事件转化成对模型或视图的改变。例如，如果用户在一个文本框中按下了一个字符键，控制器调用模型中的“插入字符”命令，然后模型告诉视图进行更新，而视图永远不会知道文本为什么改变了。但是如果用户按下了一个光标键，那么控制器会通知视图进行卷屏。卷动视图对实际文本不会有任何影响，因此模型永远不会知道这个事件的发生。 

在程序员使用Swing组件时，通常不需要考虑模型-视图-控制器体系结构。每个用户界面元素都有一个包装器类（如JButton或JTextField) 来保存模型和视图。当需要查询内容 (如文本域中的文本）时， 包装器类会向模型询问并且返回所要的结果。当想改变视图时（例 如， 在一个文本域中移动光标位置)， 包装器类会把此请求转发给视图。然而，有时候包装器转发命令并不得力。在这种情况下，就必须直接地与模型打交道（不必直接操作视图--这是观感代码的任务)。

除了“本职工作”外，模型-视图-控制器模式吸引Swing设计者的主要原因是这种模式允许实现可插观感。每个按钮或者文本域的模型是独立于观感的。当然可视化表示完全依赖于特殊观感的用户界面设计，且控制器可以改变它。例如，在一个语音控制设备中，控制器需要处理的各种事件与使用键盘和鼠标的标准计算机完全不同。通过把底层模型与用户界面分离开， Swing设计者就能够重用模型的代码，甚至在程序运行时对观感进行切换。

当然，模式只能作为一种指导性的建议而并没有严格的戒律。没有一种模式能够适用于所有情况。例如，使用“窗户位置”模式（设计模式中并非主要成分）来安排小卧室就不太合适。同样地，Swing设计者发现对于可插观感实现来说，使用模型-视图-控制器模式并非都是完美的。模型容易分离开，每个用户界面组件都有一个模型类。但是，视图和控制器的职责分工有时就不很明显，这样将会导致产生很多不同的类。当然，作为这些类的使用者来说，不必为这些细节费心。前面已经说过，这些类的使用者根本无需为模型操心，仅使用组件包装器类即可。

## 1.3 Swing 按钮的模型-视图-控制器分析

前一章已经介绍了如何使用按钮，当时没有考虑模型、视图和控制器。按钮是最简单的用户界面元素，所以我们从按钮开始学习模型-视图-控制器模式会感觉容易些。对于更复杂的Swing组件来说，所遇到的类和接口都是类似的。

对于大多数组件来说，模型类将实现一个名字以Model结尾的接口，例如，按钮就实现了ButtonModel接口。实现了此接口的类可以定义各种按钮的状态。实际上，按钮并不复杂，在Swing库中有一个名为DefaultButtonModel 的类就实现了这个接口。

读者可以通过查看ButtonModd接口中的特征来了解按钮模型所维护的数据类别。表12-1列出了这些特征。

| 属性名        | 值                                                |
| ------------- | ------------------------------------------------- |
| actionCommand | 与按钮关联的动作命令字符串                        |
| mnemonic      | 按钮的快捷键                                      |
| armed         | 如果按钮被按下且鼠标仍在按钮上则为true            |
| enable        | 如果按钮是可选择的则为 true                       |
| pressed       | 如果按钮被按下且鼠标按键没有释放则为 true         |
| rollover      | 如果鼠标在按钮之上则为 true                       |
| selected      | 如果按钮已经被选择（用于复选框和单选钮) 则为 true |

每个JButton对象都存储了一个按钮模型对象，可以用下列方式得到它的引用。 

```
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

现在终于可以开始介绍Swing用户界面组件了。首先，介绍具有用户输入和编辑文本功能的组件。文本域(JTextField)和文本区(JTextArea)组件用于获取文本输入。文本域只能接收单行文本的输入，而文本区能够接收多行文本的输入。JPassword 也只能接收单行文本的输入，但不会将输入的内容显示出来。 

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
textField.setText(Hello!");
```

并且，在前面已经提到，可以调用getText方法来获取用户键入的文本。这个方法返回用户输入的文本。 如果想要将getText方法返回的文本域中的内容的前后空格去掉，就应该调用trim方法：

```java
String text = textField.getText().trim;
```

如果想要改变显示文本的字体，就调用 setFont 方法。 

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

JLabel的构造器允许指定初始文本和图标，也可以选择内容的排列方式。可以用Swing Constants接口中的常量来指定排列方式。在这个接口中定义了几个很有用的常量，如LEFT、RIGHT，CENTER、NORTH、EAST等。JLabel 是实现这个接口的一个Swing类。因此，可以指定右对齐标签：

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