Java基础知识笔记-11-Swing用户界面组件
> *这章教程两个版本，一个语法是非lambda表达式，另一个是lambda表达式*

> # 非lambda版本
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

==Java提供的JFrame类的实例就是一个底层容器==
(JDialog类的实例也是一个底层容器，见后面的11.6节)，即通常所说的窗口。其他组件必须被添加到底层容器中,以便借助这个底层容器和操作系统进行信息交互。简单地讲，如果应用程序需要一个按钮，并希望用户和按钮交互，即用户单击按钮使程序做出某种相应的操作，那么这个按钮必须出现在底层容器中，否则用户无法看见按钮,更无法让用户和按钮交互。  

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
```
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
## 2.2菜单条，菜单，菜单项
窗口中的菜单项放在菜单里，菜单放在菜单条里。
##### 1.菜单条
**JComponent类的子类`JMenubar`负责创建菜单条，即JMenubar的一个实例就是一个菜单条，JFrame类有一个将菜单条放置到窗口的方法;**
```
setJMenuBar(JMenuBar bar);
```
该方法将菜单条添加到窗口的顶端，需要注意的是，只能向窗口中添加一个菜单条。
##### 2.菜单
JComponent类的子类`JMenu`负责创建菜单，即JMenu的一个实例就是一个菜单。
##### 3.菜单项
JComponent类的子类`JMenuItem`负责创建菜单项，即JMenuItem的一个实例就是一个菜单项。
##### 4.嵌入子菜单
JMenu是`JMenuItem`的子类，*因此菜单本身也是一个菜单项，当把一个菜单看作菜单项添加到某个菜单中，称这样的菜单为子菜单*
##### 5.菜单上的图标
我们先用Icon声明一个图标，然后使用其子类ImageIcon类创建一个图标，如：
```
Icon icon=new ImageIcon("a.gif");
```
然后菜单项调用`setlcon(Icon icon)`方法将图标设置为icon.  
例11.2中在主类Examplell_ 2的main 方法中，用Frame的子类WindowMenu创建一个含有菜单的窗口，效果如图11.3所示。
```
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
```
char[]getPassword()
```
方法可以返回实际的密码。
```
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
## 3.2常用容器
JComponent是Container的子类，因此JComponent子类创建的组建也都是容器，但是我们很少将`JButton,JTeseField,JCheckBox`等组件当容器来使用。JComponent专门提供了一些经常用来添加组件的容器，，相对于JFrame底层容器，本节提到的容器习惯性被称为中间容器，==中间容器必须被添加到底层容器中才会发生作用==
##### 1.JPanel面板
我们经常会使用JPlanel创建一个面板，然后再向这个面板添加组件，然后把这个面板添加到其他的容器中，。JPlanel面板的默认布局是FlowLayout布局。
##### 2.滚动窗格JSrollPane
滚动窗格只可以添加一个组件，可以把一个组件放到一个滚动窗格中，然后通过滚动条来操作该组件，==JTextArea不自带滚动条，因此我们需要把文本区放入到一个滚动窗格中==，例如：
```
JScrollPane scroll=new JScrollPane(new JTextArea());
```
##### 3.拆分窗格ISplitPane
顾名思义,拆分窗格就是被分成两部分的容器,拆分窗格有两种类型:水平拆分和垂直拆分。水平拆分窗格用一条拆分线把窗格分成左右两部分,左面放一个组件,右面放一组件,拆分线可以水平移动。垂直拆分窗格用一条拆分线把窗格分成上下两部分,上面放个组件,下面放一个组件,拆分线可以垂直移动
JSplitPane的两个常用的构造方法:
```
JSplitPane(int a, Component b, Component c);
```
>参数a取JSplitPane的静态常量HORIZONTAL_SPLIT或VERTICAL_SPLIT,以解决水平还是垂直拆分,后两个参数决定要放置的组件,当拆分线移动时,组件不是连续变化的
```
JSplitPane(int a, boolean b, Component c, Component d);
```
参数a取JSplitPane的静态常量HORIZONTAL_SPLIT或VERTICAL_SPLIT,以决定是水平还是垂直拆分。参数b决定当拆分线移动时,组件是否连续变化(true是连续),后两个参数决定要放置的组件, SPlit Pane拆分窗格还可以调用setDividerlocation(int)方法修改拆分线的初始位置
##### 4 JLayeredPane分层窗格
如果添加到容器中的组件经常需要处理重叠问题,就可以考虑将组件添加到分层窗格分层窗格分成5个层,分层窗格使用
```
add(Component com, int layer);
```
添加组件com,并指定com所在的层,其中参数layer取值JLayeredPane类中的类常量
```
DEFAULT_LAYER、 PALETTE_LAYER、 MODAL_LAYER、 POPUP_LAYES、 DRAG_LAYER
```
DEFAULT LAYER是最底层,添加到DEFAULT_LAYER层的组件如果和其他层的组件发生重叠时,将被其他组件遮挡。 DRAG_LAYER层是最上面的层,如果分层窗格中添加了许多组件,当用户用鼠标移动一个组件时,可以把该组件放到DRAG__LAYER层  
这样,用户在移动组件过程中,该组件就不会被其他组件遮挡。添加到同一层上的组件,如发生重叠,后添加的会遮挡先添加的组件。分层窗格调用
```
public void setLayer(Component c, int layer);
```
可以重新设置组件c所在的层,调用
```
public int getlayer( Component c)
```
可以获取组件c所在的层数
## 3.3常用布局
当把组件添加到容器中时,希望控制组件在容器中的位置,这就需要学习设计的知识。本节将分别介绍java.awt包中的`FlowLayout` `BorderLayout` `Cardlayout` `GridLayout`布局  

容器可以使用方法`setLayout(布局对象);`设置自己的布局
##### 1.FlowLayout布局
FlowLayout类创建的对象称作FlowLayout型布局。FlowLayout型布局是JPanel容器的默认布局,即JPanel及其子类创建的容器对象,如果不专门为其指定布局,则它们的布局就是Flow Layout型布局  
Flow Layout类的一个常用构造方法如下,该构造方法可以创建一个居中对齐的布局对象。例如:
```
FlowLayout flow=new FlowLayout();
```
如果一个容器con使用这个布局对象
```
con.setLayout(flow);
```
那么,con可以使用Container类提供的add方法将组件顺序地添加到容器中,组件按照加入的先后顺序从左向右排列,一行排满之后就转到下一行继续从左至右排列,每一行中的组件都居中排列,组件之间的默认水平和垂直间隙是5个像素,组件的大小为默认的最住大小,例如,按钮的大小刚好能保证显示其上面的名字。对于添加到使用Flowlayout布局的容器中的组件,组件调用`setSize(int x,int y);`设置的大小无效,如果需要改变最佳大小,组件需调用
```
public void setPreferredsize(Dinension preferredsize);
```
设置大小,例如:
```
button.setPreferredsize(new Dinension(20,20));
```
FlowLayout布局对象调用
```
setAliginmen(int aligin);
```
方法可以重新设置布局的对齐方式,其中aligin可以取值`ElowLayout.LEFT,FlowLayout.CENTER,FlowLayout.RIGHT`  

FlowLayout布局对象调用`setHgap(int hgap)`方法和`setVgap(intvgap)`可以重新设置水平间隙和垂直间隙。
##### 2.BorderLayout布局
Border layout布局是window型容器的默认布局,例如JFrame、JDialog都是Window类的子类,它们的默认布局都是BorderLayout布局,Borderlayout也是一种简单的布局策略,如果一个容器使用这种布局,那么容器空间简单地划分为东、西、南、北、中5个区域,中间的区域最大。每加入一个组件都应该指明把这个组件加在哪个区域中,区域由Borderlayout中的静态常量CENTER、NORTH、SOUTH、WEST、EAST表示,例如,一个使用BorderLayout布局的容器con,可以使用add方法将一个组件b添加到中心区域
```
con.add(b,BorderLayout.CENTER);
或者：
on.add(BorderLayour.CENTER,b);
```
添加到某个区域的组件将占据整个这个区域,每个区域只能放置一个组件,如果向某个已放置了组件的区域再放置一个组件,那么先前的组件将被后者替换掉,使用BorderLayout布局的容器最多能添加5个组件,如果容器中需要加入超过5个组件,就必须使用容器的嵌套或改用其他的布局策略.
##### 3.CardLayout布局
使用CardLayout的容器可以容纳多个组件,这些组件被层叠放入容器中,最先加入容器的是第一张(在最上面),依次向下排序,使用该布局的特点是,同一时刻容器只能从这些件中选出一个来显示,就像叠“扑克牌”,每次只能显示其中的一张,这个被显示的组件将占据所有的容器空间  

假设有一个容器con,那么,使用Cardlayout的一般步骤如下  
- 创建 CardLayout对象作为布局,如
```
Cardlayout card=new Cardlayout();
```
- 使用容器的setlayout()方法为容器设置布局,如
```
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
```
GridLayout(int m,int n);
```
创建布局对象,指定划分网格
的行数m和列数n,例如
```
Gridlayout grid= new Gridlayout(10,8);
```
使用 GridLayout布局的容器调用方法`add(Component c)`将组件c加入容器,组件进入容器的顺序将按照第一行第一个、第一行第二个行最后一个、第二行第一个、…、最后一行第一个、…、最后一行最后一个.  

使用GridLayout布局的容器最多可添加m×n个组件,GridLayout布局中每个网格都是相同大小并且强制组件与网格的大小相同.  

由于Gridlayout布局中每个网格都是相同大小并且强制组件与网格的大小相同,使得容器中的每个组件也都是相同的大小,显得很不自然。为了克服这个缺点,你可以使用容器套,如,一个容器使用GridLayout布局,将容器分为三行一列的网格,那么你可以把另个容器添加到某个网格中,而添加的这个容器又可以设置为GridLayout布局、FlowLayout布局、CarderLayout布局或BorderLayout布局等,利用这种嵌套方法,可以设计出符合定需要的布局.

## 3.4选项卡窗格
JTabbedPane创建的对象也是一个容器,由于JTabbedPane在设计GUl程序时比较方便实用,所以单独列出一小节来讲解。  

JTabbedPane创建的对象称为选项卡窗格。JTabbedPane窗格的默认布局是CardLayour布局,并且自带一些选项卡(不需用户添加),这些选项卡与用户添加到JTabbedPane窗格中的组件相对应,也就是说,当用户向JTabbedPane窗格添加一个组件时,JTabbedPane窗格就会自动指定给该组件一个选项卡,单击该选项卡,JTabbedPane窗格将显示对应的组件,选项卡窗格自带的选项卡默认地在该选项卡窗格的顶部,从左向右依次排列,选项卡的顺序和对应的组件的顺序相同。  

JTabbledPane窗格可以使用
```
add(String text,Component c);
```
方法将组件c添加到JTabbedPane窗格中,并指定和组件c对应的选项卡的文本提示text,使用 JTabbedPane窗格的构造方法
```
public JTabbedPane(int tabPlacenent);
```
创建的选项卡窗格的选项卡的位置由参数tabPlacement指定,该参数的有效值为`JTabbedPane.TOP`, `JTabbedPane.BOTTOM`, `JTabbedPane.LEFT`和`JTablePane.RIGHT ` 

> 例11.4的 Example14窗口中有一个选项卡窗格,选项卡窗格中又添加了3个不同布局的面板FlowLayoutJPanel,BorderlayoutJPanel和GridLayoutJPanel并设置了相对应的选项卡的文本提示.

```
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
> # lambda版本

上一章主要介绍了如何使用Java中的事件模式。通过学习读者已经初步知道了构造图形用户界面的基本方法。本章将介绍构造功能更加齐全的图形用户界面(GUI) 所需要的一些重要工具。下面，首先介绍Swing的基本体系结构。要想弄清如何有效地使用一些更高级的组件，必须了解底层的东西。然后，再讲述Swing中各种常用的用户界面组件，如文本框、单选按钮以及菜单等。接下来，介绍在不考虑特定的用户界面观感时，如何使用Java中的布局管理 器排列在窗口中的这些组件。最后，介绍如何在Swing中实现对话框。本章囊括了基本的Swing组件，如文本组件、按钮和滑块等，这些都是基本的用户界面组件，使用十分频繁。Swing中的高级组件将在卷n中讨论。

# 12.1 Swing 和模型-视图-控制器设计模式
前面说过，本章将从Swing组件的体系结构开始。首先，我们讨论设计模式的概念，然后再看一下Swing框架中最具影响力的“模型-视图-控制器”模式。
## 12.1.1 设计模式
在“模型-视图-控制器”模式中，背景是显示信息和接收用户输人的用户界面系统。有关“模型-视图-控制器”模式将在接下来的章节中讲述。这里有几个冲突因素。对于同一数据来说，可能需要同时更新多个可视化表示。例如，为了适应各种观感标准，可能需要 改变可视化表示形式；又例如，为了支持语音命令，可能需要改变交互机制。解决方案是将这些功能分布到三个独立的交互组件：模型、视图和控制器。模型-视图-控制器模式并不是AWT和Swing设计中使用的唯一模式。下列是应用的 另外几种模式： 

- 容器和组件是“组合(composite)” 模式
- 带滚动条的面板是“ 装饰器(decorator)” 模式
- 布局管理器是“策略(strategy)” 模式

设计模式的另外一个最重要的特点是它们已经成为文化的一部分。只要谈论起模型-视图-控制器或“装饰器” 模式，遍及世界各地的程序员就会明白。因此，模式已经成为探讨设计方案的一种有效方法。

## 12.1.2 模型-视图-控制器模式

让我们稍稍停顿一会儿，回想一下构成用户界面组件的各个组成部分，例如，按钮、复选框、文本框或者复杂的树形组件等。每个组件都有三个要素： 
- 内容，如：按钮的状态（是否按下)，或者文本框的文本。 
- 外观（颜色，大小等)。 
- 行为（对事件的反应)。 

这三个要素之间的关系是相当复杂的，即使对于最简单的组件（如：按钮）来说也是如此。很明显，按钮的外观显示取决于它的观感。Metal按钮的外观与 Windows按钮或者Motif按钮的外观就不一样。另外，外观显示还要取决于按钮的状态：当按钮被按下时，按钮需要 被重新绘制成另一种不同的外观。而状态取决于按钮接收到的事件。当用户在按钮上点击时，按钮就被按下。 

当然，在程序中使用按钮时，只需要简单地把它看成是一个按钮，而不需要考虑它的内部工作和特性。毕竟，这些是实现按钮的程序员的工作。无论怎样，实现按钮的程序员就要 对这些按钮考虑得细致一些了。毕竟，无论观感如何，他们必须实现这些按钮和其他用户界 面组件，以便让这些组件正常地工作。为了实现这样的需求，Swing设计者采用了一种很有名的设计模式(design pattern):模型 -视图 -控制器(model-view-controller)模式。这种设计模式同其他许多设计模式一样，都遵循第5章介绍过的面向对象设计中的一个基本原则： 限制一个对象拥有的功能数量。不要用一个按钮类完成所有的事情，而是应该让一个对象负责组件的观感，另一个对象负责存 储内容。模型-视图-控制器（MVC) 模式告诉我们如何实现这种设计，实现三个独立的类：

- 模型(model): 存储内容。
- 视图(view): 显示内容。
- 控制器(controller): 处理用户输入。 

这个模式明确地规定了三个对象如何进行交互。模型存储内容，它没有用户界面。按钮的内容非常简单，只有几个用来表示当前按钮是否按下，是否处于活动状态的标志等。文本框内容稍稍复杂一些，它是保存当前文本的字符串对象。这与视图显示的内容并不一致---如果内容的长度大于文本框的显示长度，用户就只能看到文本框可以显示的那一部分，
