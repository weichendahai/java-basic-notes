Java基础知识笔记-6-接口，lambda表达式与内部类

首先，介绍一下接口(interface)技术，这种技术主要用来描述类具有什么功能，而并不给出每个功能的具体实现。一个类可以实现(implement)一个或多个接口，并在需要接口的地方，随时使用实现了相应接口的对象。了解接口以后，再继续介绍而表达式，这是一种表示可以在将来某个时间点执行的代码块的简洁方法。使用lambda表达式，可以用一种精巧而简洁的方式表示使用回调或变量行为的代码。

接下来，讨论内部类(inner class)机制。理论上讲，内部类有些复杂，内部类定义在另外一个类的内部，其中的方法可以访问包含它们的外部类的域。内部类技术主要用于设计具有相互协作关系的类集合。

## 1 接口
### 1.1 接口概念

在Java程序设计语言中，接口不是类，而是对类的一组需求描述，这些类要遵从接口描述的统一格式进行定义。

在Java语言中，接口有两种意思

- 一是指概念性的接口，即指系统对外提供的所有服务，类的所有能被外部使用者访问的方法构成了类的接口
- 二是指interface关键字定义的实实在在的接口，也称为接口类型。

在面相对象程序设计中，定义一个类必须做什么而不是怎么做有时是很有益的。前面有一个这样的例子：抽象方法为方法定义了签名，但不提供实现方式。子类必须自己实现由其父类定义的抽象方法。这样，抽象方法就指定了方法的接口而不是实现。尽管抽象类和方法很有用，但还可以将这一概念进一步延伸。在java中，可使用关键字interface把类的接口和实现方法完全分开。

使用关键字interface来定义一个接口。接口的定义和类的定义很相似，分为接口的声明和接口体

例如：

```java
interface Printable {
	final int MAX=100;
	void add();
	float sum(float x,float y);
}
```
接口包含接口声明和接口体，和类不同的是，接口声明使用关键字interface来表明自己是一个接口。

接口体包含常量的声明（没有变量）和抽象方法两部分。

**接口体中中只有抽象的方法和普通的方法。而且接口体中所有常量的访问权限一定都是public（允许省略public，final修饰符），所有的抽象方法的访问权限一定都是public（允许省略public，abstract修饰符）例如：**

```java
interface Printable {
	public final int MAX=100;
	public abstract void add();
	public abstract float sum(float x,float y);
}
```
**在JDK8以前的版本中，接口只能包含抽象方法，从JDK8开始，为了提高代码的可重用性，允许在接口中定义默认方法和静态方法。默认方法用default关键字来声明，拥有默认的实现，接口的实现类既可以直接访问默认方法，也可以覆盖它，重新实现该方法，例如下例：**

```java
public interface MyIFC{
	default void method1(){
		System.out.println("default method1");//声明了一个默认方法
	}
	static void method2(){
		System.out.println("static method2");//声明了一个静态方法
	}
	void method3();//声明了一个抽象方法
}
```
以下类实现了上面这个接口，Tester类作为非抽象类，必须实现MyIFC接口的抽象method3()方法。tester的实例可以直接访问在接口中定义的method1()默认方法
```java
public class Tester implements MyIFC{
	public void method3(){//实现接口中的method3()方法
		System.out.println("method3");
	}
	public static void main(String args[]){
		Tester t=new Tester();
		t.method1();//访问接口中的默认方法
		t.method2();//编译出错，Tester实例不能访问MyIFC接口的静态方法
		MyIFC.method2();//合法，可以通过接口的名字来访问它的静态方法
		t.method3();		
	}
}
```
接口中的静态方法只能在接口内部被访问，或者其他程序通过接口的名字来访问它的静态方法，如果试图通过实现接口的类的实例来访问该静态方法，会导致编译错误，例如：

```java
t.method2();//编译出错，Tester实例不能访问MyIFC接口的静态方法
MyIFC.method2();//合法，可以通过接口的名字来访问它的静态方法
```
另外，需要注意的是，在接口中为方法提供默认实现虽然可以提高代码的可重用性，但还是要谨慎地使用这一特性。因为在层次关系比较复杂的软件系统中，这一特性会使程序代码导致歧异和混淆。

**接口没有构造方法，不能被实例化。在接口中定义构造方法是非法的，例如：**

```java
public interface A {
	public A(){//编译出错，接口中不允许定义构造方法
		...
	}
	void method();
}
```
**虽然不允许创建接口的实例，但是允许定义接口类型的引用变量，该变量引用实现了这个接口的类的实例，例如：**

```java
//引用变量t被定义为Photographable接口类型，他引用Camera实例
Photographable t=new Camera();
```

---

### 1.2 实现接口
在Java语言中，**接口由类去实现以便使用接口中的方法，一个类可以实现多个接口，类通过使用关键字implements声明自己实现一个或者多个接口。** 如果实现多个接口，用逗号隔开接口名。  

为了让类实现一个接口， 通常需要下面两个步骤：
- 1)将类声明为实现给定的接口。
- 2)对接口中的所有方法进行定义。

&emsp;&emsp;如A类实现Printable和Addable接口
```java
class A implements Printable,Addable
```
&emsp;&emsp;再例如Animal的子类Dog实现Eatable和sleepable接口
```java
class Dog extends Animal implements Eatable,Sleepable
```
**如果一个非抽象类实现了某个接口，那么这个类必须重写该接口的所有方法，需要注意的是，由于接口中的方法一定是`public abstract方法`**，所以非抽象类在重写接口方法时不仅要去掉abstract修饰给出的方法体，而且方法的访问权限一定要明显的用public来修饰（否则降低了访问权限，这是不允许的）实现接口的非抽象类一定要重写接口的方法，因此也称这个类实现了接口的方法，Java提供的接口都在相应的包中，通过import语句不仅可以引入包中的类，还可以引入包中的接口，例如:

```java
import java.io.*;
```
类重写的接口方法以及接口中的常量可以直接被类的对象调用，而且常量也可以用接口名直接调用。  

接口声明时，如果关键字interface前面加上public，就称它为public接口，可以被任何一个类实现，如果一个接口不加public修饰，那么就称它为友好接口类，友好接口类可以被与该接口在同一个包中实现。  

如果父类实现了某个接口，那么子类也自然实现了接口，子类不必再显式地使用关键字implements声明实现这个接口。  

接口也可以被继承，既可以通过关键字extends声明一个接口是另一个接口的子接口。由于接口中的方法和常量都是public，子接口将继承父接口中的全部方法和常量。  

**注意：如果一个类声明实现某一个接口，但没有重写接口中的所有方法，那么这个类必须是abstracts类**。这句话是指当类实现了某个接口，它必须实现接口中的所有抽象方法，否则这个类必须被定义为抽象类。


例如:
```java
interface Computable{
	final int MAX=100;
	void speak();
	int f(int x);
	float g(float x,float y);
}
abstract class A implements Computable{
	public int f(int x){
		int sum=0;
		for(int i=1;i<=x;i++){
			sum=sum+I;
		}
	return sum
	}
}
```

**理解接口**

当不希望某些类通过继承使得他们具有一些相同的方法时，就可以考虑让这些类去实现相同的接口而不是把他们声明为同一个类的子类。

### 1.3 接口与多态

由接口产生多态就是指不同的类在实现同一个接口时可能具有不同的实现方式，那么接口变量在回调接口方法时就可能具有多种形态。

```java
interface ComputerAverage {
	public double average(double a,double b);
}

class A implements ComputerAverage{
	public double average(double a,double b){
		double aver=0;
		aver=(a+b)/2;
		return aver;
	}
}

class Bi implements ComputerAverage{
	public double arerage(double a,double b){
		double aver=Math.sqrt(a*b);
		return aver;
	}
}

public class exercise{
	public static void main(String args[]){
		ComputerAverage computer;
		double a=11.23;
		double b=22.78;
		computer=new A();
		double result=computer.average(a,b);//算术平均数
		computer=new A();
		double result=computer.average(a,b);//几何平均数
	}
}
```

### 1.4 接口变量做参数

将接口作为值传递：

```java
public class A {
	private TestInterface test;
	public A(TestInterface test) {
		this.test = test; 
		doSth();
	}
	public void doSth() {
		test.systemStr("this is a message!");
	}
}

public Interface TestInterface(){
	void systemStr(String str);
}

public class B implement TestInterface{
	public static void main(String[] args) {
		new A(new B());
	}
    
	@Override
	public void systemStr(String str) {
		System.out(str);
	}
}
```

且看这两个类，一个接口，它们什么关系？

> `class B` 作为程序入口类，实现了`interface TestInterface`，并定义了接口中`systemStr(String str)`。主方法`main()`中实例化`class A`，并将自身`class B`的实例化对象传入。
>
> 在`class A`中，定义了一个接口`TestInterface`类型的成员变量`test`,一个具体的方法`doSth()`。
>
> 其构造方法接受主方法中传来的数据并初始化成员变量`test`。`doSth`方法执行方法`systemStr()`

`class A`的构造方法明明规定接受类型为`TestInterface`，为什么向`class A`的对象中传入的是`class B`的实例？

> 这涉及到java自动转型，`TestInterface test`由编译器自动（向下）转型为`class B`。实际执行的方法是由`class B`继承自`TestInterface`并定义的`systemStr(String str)`方法。
>
> 虽然接受的类型为`TestInterface`，但是`接口不能被实例化`，所以我们要定义一个类来实现接口，并将这个类传递过去。
>
> 我们大可以规定`class A`的构造方法接受类型为`class B`。

**以下内容为类型转换的解释**

> 什么是向上/向下转型？

**首先是什么时候能转型？**

二者必须存在继承关系后才能相互转型

**什么是向上/向下转型？**

向上转型：`继承者类型对象（子）`向`被继承者类型（父）`转型（自动转型）

> 如`子类`转型为`父类`
> 再比如上面程序中的接口实现类向`接口`转型

向下转型：`被继承者类型对象（父）`  向  `继承者类型（子）`  转型（强制转型）

> 比如`父类`转型为  `子类`
> 注意：`接口`不能实例化，没有对象，自然也就不能转型为`接口实现类`。

**什么是自动/强制转型？**

```cpp
public Interface Interface1() {
	public void i11(){
		//dosth
	}
  
	public void i12(){
		//dosth
	}
}

public class Parent() implement Interface1 {
	public void p1(){
		//dosth
	}
  
	public void p2(){
		//dosth
	}

	public void i11(){
		//dosth
	}
  
	public void i12(){
		//dosth
	}
}

public Interface Interface2() {
	public void i21(){
		//dosth
	}
  
	public void i22(){
        //dosth
	}
}
public class Child1() extends Parent implement Interface2 {
	public void c11(){
		//dosth
	}
  
	public void c12(){
		//dosth
	}
  
	public void i21(){
		//dosth
	}
	
    public void i22(){
		//dosth
	}
}

public class Child2() extends Parent{
	public void c21(){
		//dosth
	}
	public void c22(){
		//dosth
	}
}
```

如上程序

>`class Child1`继承`class Parent`，并实现`Interface Interface2;而class Parent`又实现了`Interface Interface1`
>
>`Parent parent = new Child1()`  为 `子类转型为父类`，即`向上转型``new Child1()`  转型后的  父类对象`parent`只能访问与子类`Child1`共有的方法。
>
>如：父类的全部方法【父类自身的`p1()、p2()`和继承自接口的`i11()、i12()`】，子类的方法则不是共有的，不能访问。如果编写`parent.c11();`  或者  `parent.i21()` 则不能通过编译。
>
>你的编辑器可能会提示你这样写`(Child1)parent.c11();`，这就是强制转型（向下转型）
>
>`Child1 child1= (Child1)new Parent()`  为 `父类转型为子类`，即`向下转型new Parent()`转型后的子类对象  `child1`能访问子类`Child1`的全部方法（包括自身方法，继承自父类与接口的方法）。
>
>然而，向上转型是安全的，总是能成功的；向下转型只不安全的，可能会产生`ClassCastExcption`。
>
>比如以下`向下转型`是能成功的：
>
>```php
>Child1 child1= (Child1)new Parent();
>Child2 child1= (Child2)new Parent();
>```
>
>随即，将child1,child2`向上转型`
>
>```php
>Parent parent1 = child1;
>Parent parent2 = child2;
>```
>
>再将parent1,parent2按以下方式`向下转型`是不能成功的
>
>```cpp
>child1 = (Chid1)parent2;
>child2 = (Child2)parent1;
>//或者
>Child1 child11 = (Chid1)parent2;
>Child1 child21 = (Chid2)parent1;
>```
>
>因为这相当于将`Child2`类型强转为`Child1`类型，将`Child1`类型强转为`Child2`类型，它们之间没有继承关系，这是不可行的
>
>当然，以下是可行的
>
>```cpp
>child1 = (Chid1)parent1;
>child2 = (Child2)parent2;
>//或者
>Child1 child11 = (Chid1)parent1;
>Child1 child21 = (Chid2)parent2;
>```
>
>这相当于将`Child1`类型与`Child2`类型转换回去，自然是能成功的。

补充知识：接口不能实例化，为什么可以写成形如以下的格式？

```java
view.setOncickListener(new OnClickListener() {
	@Override
	public void onClick(View v) {});
```

> 这不是将`OnCLickListener`接口实例化，而是匿名内部类的形式。相当于创建了一个implement了`OnCLickListener`的类实例，并将其传递。

什么是内部类？什么是匿名内部类？

内部类：是嵌套在类中的类，根据修饰符的不同，还可分为静态内部类、局部内部类等，定义在方法中的内部类，即使为`static`所修饰，也是局部内部类。

匿名内部类：是一种没有类名的内部类，不使用`class、extends、implements`，没有构造函数，它必须继承其他类或实现其他接口。但是其本质上依然创建了一个继承了其他类或实现了其他接口类实例。

所以，除了以上new 接口()的内部类形式，还有形如`listView.setAdapter(new BaseAdaptetr(){......});`的new 类() 的内部类形式。

大可在`{}`中定义新的成员变量、方法甚至新的内部类，只是这些只能在该内部类中访问罢了。

### 1.5 接口与抽象类

可能会产生这样一个疑问：为什么Java程序设计语言还要不辞辛苦地引入接口概念？为什么不将Comparable直接设计成如下所示的抽象类。

```java
abstract class Comparable // why not?
{
	public abstract int compareTo(Object other);
}
```

然后，Employee类再直接扩展这个抽象类，并提供compareTo方法的实现：

```java
class Employee extends Comparable // why not?
{
	public int compareTo(Object other) { . . . }
}
```

非常遗憾，使用抽象类表示通用属性存在这样一个问题：每个类只能扩展于一个类。假设Employee类已经扩展于一个类，例如Person,它就不能再像下面这样扩展第二个类了：

```java
class Employee extends Person, Comparable // Error
```

但每个类可以像下面这样实现多个接口：

```java
class Employee extends Person implements Comparable // OK
```

有些程序设计语言允许一个类有多个超类，例如C++。我们将此特性称为多重继承(multiple inheritance)。而Java的设计者选择了不支持多继承，其主要原因是多继承会让语言本身变得非常复杂（如同C++)，效率也会降低（如同Eiffel)。

实际上，接口可以提供多重继承的大多数好处，同时还能避免多重继承的复杂性和低效性

**abstract类与接口的比较**

接口和abstract类的比较如下：

1. abstract类和接口都有abstract方法  

2. 接口中只可以有常量，不能有变量；而abstract类中既可以有常量又可以有变量  

3. abstract类中也可以有非abstract方法，接口不可以

在设计程序时，应当根据具体的分析方法来确定是使用抽象类还是接口。abstract类除了提供重要的需要子类重写的abstract方法外，也提供了子类需要继承的变量和非abstract方法，如果子类需要重写父类的abstract方法，还需要从父类继承一些变量或继承一些重要的非abstract方法，就可以考虑用abstract。  

如果某个问题不需要继承，只是需要若干个类给出某些重要的abstract方法的实现细节，就可以考虑使用接口。

## 2 接口示例

### 2.1 接口回调

回调（callback) 是一种常见的程序设计模式。在这种模式中，可以指出某个特定事件发生时应该采取的动作。例如，可以指出在按下鼠标或选择某个菜单项时应该采取什么行动。然而，由于至此还没有介绍如何实现用户接口，所以只能讨论一些与上述操作类似，但比较简单的情况。

和类一样，接口也是Java中重要的一种数据类型，用接口声明的变量称为接口变量。 **接口属于引用型变量，接口变量中可以存放实现该接口的类的实例的引用，即存放对象的引用** 

假如，假设Com是一个接口，那么就可以用Com声明一个变量:

```java
Com com;
```

内存模型如图所示，此时的com是一个空接口，因为com变量中还没有存放实现该接口的类的实例的引用。  

假设ImpleCom类是实现Com接口，用ImpleCom创建名字为object的对象，那么object对象不仅可以调用ImpleCom类中原有的方法，还可以调用该类实现的接口方法。

```java
ImpleCom object=new ImpleCom();
```

在Java中，接口回调是指可以把实现某一类接口的类创建的的对象的引用赋给该接口声明的接口变量中，那么该接口变量就可以调用被类实现的接口方法。实际上，当接口变量调用被类实现的接口方法时，就是通知相应的对象调用这个方法。  

**接口回调非常类似我们在之前介绍的上传型对象调用子类重写的方法**

#### 2.2.1 使用接口引用

你可能会对可以使用接口创建引用变量感到惊讶，可以创建接口引用变量，这样的变量可以引用实现他的接口的任何对象，当通过接口调用一个对象上的方法时，就会执行次对象实现的那个版本上那个的方法，这个过程类似与使用父类去引用访问子类对象。

#### 2.2.2 接口能够扩展

使用关键字extends，一个接口可以继承另一个接口。扩展接口的语法与继承类的语法一样，当一个类实现继承了其他接口的接口时，他必须在接口继承链中定义的所有方法提供实现方式。

实例代码：

```java
interface A {
	void meth1();
	void meth2();
}
interface B extends A {
	void meth3();
}
class MyClass implements B {
	public void meth1(){
		System.out.println("Implement meth1().");
	}
	public void meth2() {
		System.out.println("Implement meth2().");
	}
	public void meth3() {
		System.out.println("Implement meth3().");
	}
class IFExtends {
	public static void main(String args[]){
		MyClass ob=new MyClass();

		ob.meth1();
		ob.meth2();
		ob.meth3();
	}
}
```

#### 2.2.3 接口回调实例1

回调(callback)是一种常见的程序设计模式。在这种模式中，可以指出某个特定事件发生时应该采取的动作。例如，可以指出在按下鼠标或选择某个菜单项时应该采取什么行动。然而， 由于至此还没有介绍如何实现用户接口， 所以只能讨论一些与上述操作类似，但比较简单的情况。

在java.swing包中有一个Timer类，可以使用它在到达给定的时间间隔时发出通告。例如，假如程序中有一个时钟，就可以请求每秒钟获得一个通告，以便更新时钟的表盘。在构造定时器时，需要设置一个时间间隔，并告之定时器，当到达时间间隔时需要做些什么操作。

如何告之定时器做什么呢？ 在很多程序设计语言中，可以提供一个函数名，定时器周期性地调用它。但是，在Java 标准类库中的类采用的是面向对象方法。它将某个类的对象传递给定时器，然后，定时器调用这个对象的方法。由于对象可以携带一些附加的信息，所以传递一个对象比传递一个函数要灵活得多。

当然，定时器需要知道调用哪一个方法，并要求传递的对象所属的类实现了java.awt.event包的ActionListener接口。下面是这个接口：

```java
public interface ActionListener {
	void actionPerformed(ActionEvent event);
}
```

当到达指定的时间间隔时，定时器就调用actionPerformed方法。

假设希望每隔10秒钟打印一条信息“ At the tone, the time is...”， 然后响一声，就应该定义一个实现ActionListener接口的类，然后将需要执行的语句放在actionPerformed方法中。

```java
class TimePrinter implements ActionListener {
	public void actionPerformed(ActionEvent event) {
		System.out.println("At the tone, the time is " + new Date())；
		Toolkit.getDefaultToolkit().beep();
	}
}
```

需要注意actionPerformed方法的ActionEvent参数。这个参数提供了事件的相关信息，例如，产生这个事件的源对象。有关这方面的详细内容请参看第8章。在这个程序中，事件的详细信息并不重要，因此，可以放心地忽略这个参数。

接下来，构造这个类的一个对象，并将它传递给Timer构造器。

```java
ActionListener listener = new TimePrinter();
Timer t = new Timer(10000, listener) ;
```

Timer构造器的第一个参数是发出通告的时间间隔，它的单位是毫秒。这里希望每隔10秒钟通告一次。第二个参数是监听器对象。

最后，启动定时器：

```java
t.start();
```

每隔10秒钟，下列信息显示一次，然后响一声铃。

```
At the tone, the time is Wed Apr 13 23:29:08 PDT 2016
```

程序清单6-3给出了定时器和监听器的操作行为。在定时器启动以后，程序将弹出一个消息对话框，并等待用户点击Ok按钮来终止程序的执行。在程序等待用户操作的同时，每隔10秒显示一次当前的时间。

运行这个程序时要有一些耐心。程序启动后，将会立即显示一个包含“Quit program?”字样的对话框，10秒钟之后，第1条定时器消息才会显示出来。

需要注意，这个程序除了导入javax.swing.* 和java.util.\*外，还通过类名导入了javax.swing.Timer。这就消除了javax.swing.Timer与java.util.Timer之间产生的二义性。这里的java.util.Timer是一个与本例无关的类，它主要用于调度后台任务。

#### 2.2.4 接口回调实例2

举例：老板分派给员工做事，员工做完事情后需要给老板回复，老板对其做出反应。

上面是个比较经典的例子，下面用代码实现上述例子：

- 1 先定义一个接口

```java
package JieKouHuiDiao;
//定义一个接口
public interface JieKou {
	public void show();
}
```

- 2 定义一个Boss类实现接口

```java
package JieKouHuiDiao;
public class Boss implements JieKou {
//定义一个老板实现接口
	@Override
	public void show() {
		System.out.println("知道了");
	} 
}
```

- 3 定义一个员工Employee类

```java
package JieKouHuiDiao;
public class Employee {
	//接口属性，方便后边注册
	JieKou jiekou;
	//注册一个接口属性，等需要调用的时候传入一个接口类型的参数，即本例中的Boss和Employee，
	public Employee zhuce(JieKou jiekou,Employee e){
		this.jiekou=jiekou;
		return e;
	}
	public void dosomething(){
		System.out.println("拼命做事，做完告诉老板");
		//接口回调，如果没有注册调用，接口中的抽象方法也不会影响dosomething
		jiekou.show();
	}
}
```

- 4 测试类

```java
package JieKouHuiDiao;
public class Test {
	public static void main(String[] args) {
    	Employee e=new Employee();
    	//需要调用的时候先注册,传入Boss类型对象和员工参数
    	Employee e1=e.zhuce(new Boss(),e);
    	e1.dosomething();
	}
}
```

通过上面的例子和代码应该有个比较初步的了解了，接口回调还有使用匿名内部类来实现

### 2.2 Comparator接口

我们已经了解了如何对一个对象数组排序，前提是这些对象是实现了Comparable接口的类的实例, 例如，可以对一个字符串数组排序，因为String类实现了
`Comparable<String>`,而且`String.compareTo`方法可以按字典顺序比较字符串。

现在假设我们希望按长度递增的顺序对字符串进行排序，而不是按字典顺序进行排序。肯定不能让String类用两种不同的方式实现compareTo方法---更何况，String类也不应由我们来修改。

要处理这种情况，Arrays.Sort方法还有第二个版本，有一个数组和一个比较器(comparator)作为参数，比较器是实现了Comparator接口的类的实例。
```java
public interface Comparators {
	int compare(T first, T second);
}
```
要按长度比较字符串，可以如下定义一个实现`Comparator<String>`的类：

```java
class LengthComparator implements Comparator<String> {
	public int compare(String first, String second) {
		return first.length() - second.length();
	}
}
```
具体完成比较时，需要建立一个实例：
```java
Comparator<String> comp = new LengthComparator();
if (comp.compare(words[i], words[j]) > 0)...
```
将这个调用与words[i].compareTo(words[j]) 做比较。这个compare方法要在比较器对象上调用，而不是在字符串本身上调用。
> 注释：尽管LengthComparator对象没有状态，不过还是需要建立这个对象的一个实例。我们需要这个实例来调用compare方法---它不是一个静态方法。

要对一个数组排序，需要为Arrays.sort方法传入一个LengthComparator对象：
```java
String[] friends = { "Peter", "Paul", "Mary" };
Arrays,sort(friends, new LengthComparator()):
```
现在这个数组可能是["Paul", "Mary", "Peter"] 或["Mary", "Paul", "Peter"]。

以后我们会了解，利用lambda表达式可以更容易地使用Comparator。

## 3 lambda表达式
现在可以来学习lambda表达式，这是这些年来Java语言最让人激动的一个变化。你会了解如何使用lambda表达式采用一种简洁的语法定义代码块，以及如何编写处理lambda表达式的代码。

### 3.1 学习lambda表达式之前：一些简单的例子

#### 1.1 Lambda表达式的语法

基本语法:
```java
(parameters) -> expression
```
或
```java
(parameters) ->{ statements; }
```
下面是Java lambda表达式的简单例子:
```java
// 1. 不需要参数,返回值为 5  
() -> 5  
// 2. 接收一个参数(数字类型),返回其2倍的值  
x -> 2 * x  
// 3. 接受2个参数(数字),并返回他们的差值  
(x, y) -> x – y  
// 4. 接收2个int型整数,返回他们的和  
(int x, int y) -> x + y  
// 5. 接受一个 string 对象,并在控制台打印,不返回任何值(看起来像是返回void)  
(String s) -> System.out.print(s)  
```
更多外链到：https://www.cnblogs.com/franson-2016/p/5593080.html

### 3.2 为什么引入lambda表达式

lambda表达式是一个可传递的代码块，可以在以后执行一次或多次。具体介绍语法（以及解释这个让人好奇的名字）之前，下面先退一步，观察一下我们在Java中的哪些地方用过这种代码块。

已经了解了如何按指定时间间隔完成工作。将这个工作放在一个ActionListener的actionPerformed方法中：
```java
class Worker implements ActionListener {
	public void actionPerformed(ActionEvent event) {
		// do some work
	}
}
```
想要反复执行这个代码时， 可以构造Worker类的一个实例。然后把这个实例提交到一个Timer对象。这里的重点是actionPerformed方法包含希望以后执行的代码。

或者可以考虑如何用一个定制比较器完成排序。如果想按长度而不是默认的字典顺序对字符串排序，可以向sort方法传入一个Comparator对象：
```java
class LengthComparator implements Comparator<String> {
	public int compare(String first, String second) {
		return first.length() - second.length();
	}
}
...
Arrays.sort(strings, new LengthComparator()) ;
```
compare方法不是立即调用。实际上，在数组完成排序之前，sort方法会一直调用compare方法，只要元素的顺序不正确就会重新排列元素。将比较元素所需的代码段放在sort方法中，这个代码将与其余的排序逻辑集成（你可能并不打算重新实现其余的这部分逻辑）

这两个例子有一些共同点，都是将一个代码块传递到某个对象（一个定时器，或者一个sort方法)。这个代码块会在将来某个时间调用。

到目前为止，在Java中传递一个代码段并不容易，不能直接传递代码段，Java是一种面向对象语言，所以必须构造一个对象这个对象的类需要有一个方法能包含所需的代码。

在其他语言中，可以直接处理代码块。Java设计者很长时间以来一直拒绝增加这个特性。毕竟，Java的强大之处就在于其简单性和一致性。如果只要一个特性能够让代码稍简洁一些，就把这个特性增加到语言中， 这个语言很快就会变得一团糟，无法管理。不过，在另外那些语言中，并不只是创建线程或注册按钮点击事件处理器更容易；它们的大部分API都更简单、更一致而且更强大。在Java中，也可以编写类似的API利用类对象实现特定的功能，不过这种API使用可能很不方便。

就现在来说，问题已经不是是否增强Java来支持函数式编程，而是要如何做到这一点。设计者们做了多年的尝试，终于找到一种适合Java的设计。下一节中，你会了解Java SE8中如何处理代码块。

### 3.3 lambda表达式的语法

再来考虑上一节讨论的排序例子。我们传入代码来检查一个字符串是否比另一个字符串短。这里要计算：
```
first.length() - second.length()
```
first和second是什么？它们都是字符串。Java是一种强类型语言，所以我们还要指定它们的类型：
```java
(String first, String second)
	-> first.length() - second.length()
```
这就是你看到的第一个表达式。lambda表达式就是一个代码块，以及必须传入代码的变量规范。

为什么起这个名字呢？ 很多年前，那时还没有计算机，逻辑学家Alonzo Church想要形式化地表示能有效计算的数学函数。（奇怪的是，有些函数已经知道是存在的，但是没有人知道该如何计算这些函数的值。）他使用了希腊字母lambda(λ)来标记参数如果他知道Java API, 可能就会写为
```java
λfirst.λsecond.first.length() - second.length()
```
你已经见过Java中的一种lambda表达式形式：参数，箭头（->) 以及一个表达式。如果代码要完成的计算无法放在一个表达式中，就可以像写方法一样，把这些代码放在括号中，并包含显式的return语句。例如：
```java
(String first, String second) -> {
		if (first.length() < second.length()) return -1;
		else if (first.length() > second.length()) return 1;
		else return 0;
	}
```
即使lambda表达式没有参数，仍然要提供空括号，就像无参数方法一样：
```java
0 -> { for (int i = 100; i >= 0;i ) System.out.println(i); }
```
如果可以推导出一个lambda表达式的参数类型，则可以忽略其类型。例如：
```java
Comparator<String> comp
	= (first, second) // Same as (String first, String second)
		-> first.length() - second.length();
```
在这里，编译器可以推导出first和second必然是字符串，因为这个lambda表达式将赋给一个字符串比较器。（下一节会更详细地分析这个赋值。）

如果方法只有一参数， 而且这个参数的类型可以推导得出，那么甚至还可以省略小括号：
```java
ActionListener listener = event ->
	System.out.println("The time is " + new Date()");
	// Instead of (event) -> . . . or (ActionEvent event) -> . . .
```
无需指定lambda表达式的返回类型。lambda表达式的返回类型总是会由上下文推导得出。例如，下面的表达式
```java
(String first, String second) -> first.length() - second.length()
```
可以在需要int类型结果的上下文中使用。

> 注释：如果一个lambda表达式只在某些分支返回一个值，而在另外一些分支不返回值，这是不合法的。例如，`(int x)-> { if (x >= 0) return 1; } `就不合法。

程序清单6-6中的程序显示了如何在一个比较器和一个动作监听器中使用lambda表达式。
```java
程序清单6-6 lambda/LambdaTest.java
package lambda;
import java.util.*;
import javax.swing.*;
import javax.swing.Timer;
/**
* This program demonstrates the use of lambda expressions.
* ©version 1.0 2015-05-12
* ©author Cay Horstmann
*/
public class LambdaTest {
	public static void main(String[] args) {
		String[] planets = new String[] { "Mercury" , "Venus", "Earth" , "Mars" , "Jupiter" , "Saturn", "Uranus", "Neptune" };
		System.out.println(Arrays.toString(planets));
		System.out.println("Sorted in dictionary order:") ;
		Arrays.sort(planets) ;
		System.out.println (Arrays.toString(planets)) ;
		System.out.println ("Sorted by length:");
		Arrays.sort(planets, (first , second) -> first .length() - second .length()) ;
		System.out.println(Arrays.toString(planets)) ;
		Timer t = new Timer(1000, event ->
			System.out.println ("The time is " + new Date())) ;
		t.start() ;
		// keep program running until user selects "0k"
		JOptionPane.showMessageDialog (nul1 , "Quit program?");
		System.exit (O) ;
	}
}
```
### 3.4 函数式接口

前面已经讨论过，Java中已经有很多封装代码块的接口，如ActionListener或Comparator，lambda表达式与这些接口是兼容的，

对于只有一个抽象方法的接口，需要这种接口的对象时，就可以提供一个lambda表达式。这种接口称为函数式接口(functional interface)。

> 你可能想知道为什么函数式接口必须有一个抽象方法。不是接口中的所有方法都是抽象的吗？实际上，接口完全有可能重新声明Object类的方法，如toString 或clone,这些声明有可能会让方法不再是抽象的。（Java API中的一些接口会重新声明Object方法来附加javadoc注释Comparator AP丨就是这样一个例子。）更重要的是，在JavaSE 8中，接口可以声明非抽象方法。

为了展示如何转换为函数式接口，下面考虑Arrays.sort方法。它的第二个参数需要一个Comparator实例，Comparator就是只有一个方法的接口，所以可以提供一个lambda表达式：

```java
Arrays.sort (words ,
	(first , second) -> first.length() - second.length()) ;
```
在底层，Arrays.sort方法会接收实现了Comparator<\String>的某个类的对象。在这个对象上调用compare方法会执行这个lambda表达式的体。这些对象和类的管理完全取决于具体实现，与使用传统的内联类相比，这样可能要高效得多。最好把lambda表达式看作是一个函数，而不是一个对象，另外要接受lambda表达式可以传递到函数式接口
	
lambda表达式可以转换为接口，这一点让lambda表达式很有吸引力。具体的语法很简短。下面再来看一个例子：

```java
Timer t = new Timer(1000, event ->
	{
		System.out.println("At the tone, the time is " + new DateO);
		Toolkit.getDefaultToolkit().beep();
	});
```
与使用实现了ActionListener接口的类相比，这个代码可读性要好得多。实际上，在Java 中，对lambda表达式所能做的也只是能转换为函数式接口。在其他支
持函数字面量的程序设计语言中，可以声明函数类型(如（String, String) -> int )、声明这些类型的变量，还可以使用变量保存函数表达式。不过，Java设计者还是决定保持我们熟悉的接口概念，没有为Java语言增加函数类型。

*lambda表达式未完待续*

## 4 内部类
### 内部类的简单实例
前面已经知道，类可以有两种重要的成员：变量成员和方法，实际上Java还允许类可以有另一种成员：内部类。

Java支持在一个类中声明另一个类，这样的类叫做内部类。而包含内部类的类称为内部类的外嵌类。内部类的外嵌类的成员变量在内部类中仍然有效，内部类中的方法也可以调用外嵌类中的方法。  

内部类中的类体不可以声明类变量和类方法，外嵌类的类体中可以用内部类声明对象，作为外嵌类的成员。

内部类仅供它的外嵌类使用，其他类不可以用某个类的内部类声明对象。另外，由于内部类的外嵌类的成员变量在内部类中仍然有效，使得内部类和外嵌类的交互更加方便。 

例如某种类型的农场饲养了一种特殊种类的牛，但不希望其他农场饲养这种特殊种类的牛，那么这种类型的农场就可以创建这总特殊牛的类作为自己的内部类。
```java
class RedCowFrom{
	String fromName;
	RedCow cow;
	RedCowFrom(){
	}
	RedCowFrom(String s){
		cow=new RedCow(150,112,5000);
		fromName=s;
	}
	public void showCowMess(){
		cow.speak();
	}
	class RedCow{
		String cowName="hongniu";
		int height,weight,price;
		RedCow(int h,int w,int p){
			heigh=h;
			weight=w;
			price=p;
		}
		void speak() {
			System.out.println(cowName+"height is:"+height+"height is:"+height+"price is:"+price);
		}
	}
}
public class exercise{
	public static void main(String args[]){
		RedCowFrom from=new RedCowFrom("cow1");
		from.showCowMess();
	}
}
```
内部类(inner class)是定义在另一个类中的类。为什么需要使用内部类呢？其主要原因有以下三点：
- 内部类方法可以访问该类定义所在的作用域中的数据， 包括私有的数据。
- 类可以对同一个包中的其他类隐藏起来。

要定义一个回调函数且不想编写大量代码时，使用匿名(anonymous)内部类比较便捷。我们将这个比较复杂的内容分几部分介绍。
- 在6.4.1 节中，给出一个简单的内部类， 它将访问外围类的实例域。
- 在6.4.2 节中，给出内部类的特殊语法规则。
- 在6.4.3 节中，领略一下内部类的内部，探讨一下如何将其转换成常规类。过于拘谨的读者可以跳过这一节。
- 在6.4.4 节中，讨论局部内部类，它可以访问外围作用域中的局部变量。
- 在6.4.5 节中，介绍匿名内部类，说明在Java有lambda表达式之前用于实现回调的基本方法。
- 最后在6.4.6 节中，介绍如何将静态内部类嵌套在辅助类中。

> C++ 注释：
>
> C++ 有嵌套类。一个被嵌套的类包含在外围类的作用域内。下面是一个典型的例子，一个链表类定义了一个存储结点的类和一个定义迭代器位置的类。
>
> ```java
> class LinkedList {
> public:
> 	class Iterator // a nested class
> 	{
> 		public: void;
> 		insert(int x);
> 		int erase();
> 		...
> 	};
> private:
> 	class Link // a nested class
> 	{
> 		public:
> 			Link* next;
> 			int data;
> 	};
>     ...
> };
> ```
>
> 嵌套是一种类之间的关系，而不是对象之间的关系。一个LinkedList对象并不包含Iterator类型或Link类型的子对象。
>
> 嵌套类有两个好处：命名控制和访问控制。由于名字Iterator嵌套在LinkedList类的内部， 所以在外部被命名为LinkedList::Iterator，这样就不会与其他名为Iterator的类 发生冲突。在Java中这个并不重要，因为Java包已经提供了相同的命名控制。需要注意的是，Link类位于LinkedList类的私有部分，因此，Link对其他的代码均不可见。鉴于此情况，可以将Link的数据域设计为公有的，它仍然是安全的。这些数据域只能被LinkedList类（具有访问这些数据域的合理需要）中的方法访问，而不会暴露给其他的代码。在Java中，只有内部类能够实现这样的控制。
>
> 然而，Java内部类还有另外一个功能，这使得它比C++的嵌套类更加丰富，用途更加广泛。内部类的对象有一个隐式引用，它引用了实例化该内部对象的外围类对象。通过这个指针，可以访问外围类对象的全部状态。在本章后续内容中，我们将会看到有关这个Java机制的详细介绍
>
> 在Java中，static内部类没有这种附加指针，这样的内部类与C++中的嵌套类很相似。