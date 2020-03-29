Java基础知识笔记-4-类对象和方法

# 4 类，对象和方法

## 1 面向对象程序设计概述

### 1.1 类

类是定义对象形式的模板，指定了数据，以及操作数据代码，java使用类的规范来构造对象，而对象是类的实例。因此，类实质上是一系列指定如何构建对象的计划，类是逻辑抽象结构，搞清楚这个问题非常重要，直到类的对象被创建时，内存中才会有类的物理表示。

**封装**（encapsulation, 有时称为数据隐藏）是与对象有关的一个重要概念。从形式上看，封装不过是将数据和行为组合在一个包中，并对对象的使用者隐藏了数据的实现方式。对象中的数据称为**实例域**（instance field), 操纵数据的过程称为**方法**（method )。对于每个特定的类实例（对象）都有一组特定的实例域值。这些值的集合就是这个对象的当前状态（state)。无论何时，只要向对象发送一个消息，它的状态就有可能发生改变。

实现封装的关键在于绝对不能让类中的方法直接地访问其他类的实例域。程序仅通过对象的方法与对象数据进行交互。封装给对象赋予了“黑盒” 特征，这是提高重用性和可靠性的关键。这意味着一个类可以全面地改变存储数据的方式，只要仍旧使用同样的方法操作数据， 其他对象就不会知道或介意所发生的变化。

OOP的另一个原则会让用户自定义Java类变得轻而易举，这就是：可以通过扩展一个类来建立另外一个新的类。事实上，在Java中，所有的类都源自于一个“神通广大的超类”，它就是Object。在下一章中，读者将可以看到有关Object类的详细介绍。

在扩展一个已有的类时，这个扩展后的新类具有所扩展的类的全部属性和方法。在新类中，只需提供适用于这个新类的新方法和数据域就可以了。通过扩展一个类来建立另外一个类的过程称为继承（inheritance，) 有关继承的详细内容请参看下一章

当定义类时，要声明类确切的形式和特性。这是通过指定类所包含的实例变量和操作它们的方法来实现的，但大多数实际的类一般都包含这两者。

### 1.2 对象

要想使用OOP, —定要清楚对象的三个主要特性：
- 对象的行为（behavior）---可以对对象施加哪些操作，或可以对对象施加哪些方法？
- 对象的状态（state）---当施加那些方法时，对象如何响应？
- 对象标识（identity）---如何辨别具有相同行为与状态的不同对象？

有四种显式创建对象的方式：
- 用new语句创建对象，这是最常用的创建对象的方式。
- 运用反射手段，调用`java.lang.Class`或者`java.lang.reflect.Constructor`类的`newInstance()`实例方法
- 调用对象的`clone()`方法
- 运营反序列化手段，调用`java.io.ObjectInputStream`对象的`readObject()`方法，具体见对象的序列化和反序列化

### 1.4 类之间的关系

在类之间， 最常见的关系有

- 依赖 （“uses-a”）
- 聚合（“has-a”）
- 继承（“is-a”）

依赖（dependence),即“uses-a”关系，是一种最明显的、最常见的关系。例如，Order类使用Account类是因为Order对象需要访问Account对象查看信用状态。但是Item类不依赖于Account类，这是因为Item对象与客户账户无关。因此，如果一个类的方法操纵另一个类的对象，我们就说一个类依赖于另一个类。

应该尽可能地将相互依赖的类减至最少。如果类A不知道B的存在，它就不会关心B的任何改变（这意味着B的改变不会导致A产生任何bug)。用软件工程的术语来说，就是让类之间的耦合度最小。

聚合（aggregation),即“has-a”关系，是一种具体且易于理解的关系。例如，一个Order对象包含一些Item对象。聚合关系意味着类A的对象包含类B的对象。

继承（inheritance),即“is-a”关系，是一种用于表示特殊与一般关系的。例如，RushOrdei类由Order类继承而来。在具有特殊性的RushOrder类中包含了一些用于优先处理的特殊方法，以及一个计算运费的不同方法；而其他的方法，如添加商品、生成账单等都是从Order类继承来的。一般而言，如果类A扩展类B,类A不但包含从类B继承的方法，还会拥有一些额外的功能（下一章将详细讨论继承，其中会用较多的篇幅讲述这个重要的概念。

## 2 使用预定义类



## 3 用户自定义类

### 3.1 定义类

> 顶层类是指不是嵌套类的类，嵌套类是指其声明出现在其他类体或接口体中的类

**组成类的方法和变量被称为类的成员。数据成员也被称为实例变量**类体分为两种：一部份是变量的声明，另一部分是方法的定义。

1. 变量的声明
> 在声明变量的时候可以赋值，但是不可以这样赋值

```java
class A {
	int a;
	a=5;
}
```

> 成员变量又分为实例变量和类变量，在声明成员变量时，用关键字static修饰的称作类变量（也被称为静态变量）

2. 方法的定义

实例代码：

```java
Class Lader {
	float above;
	float bottom;
	Float height;
	float computer{
	area=(above+bottom)*height/2;
	Return area;
	}
}
```

### 3.2 对象的声明
```java
Lader lader;
```
**在Java中，对象总是作为引用来储存的，这意味着分配给变量lader的空间只够持有一个Lader对象的地址，**为了给对象中的变量预留空间，我们需要使用new操作符来分配新对象。

**为创建的对象分配变量**

使用new运算符和类的构造方法为声明的对象分配变量。**储存在lader中的值是一个指向内存中该数据结构的引用，而不是该数据结构自身，在Java中，对象总是作为引用来储存的**

上面两步可以写作这样的一步：

```java
Lader lader=new Lader();
```

### 3.3 构造函数

要想使用对象，就必须首先构造对象， 并指定其初始状态。然后，对对象应用方法。在Java程序设计语言中，使用构造器(constructor) 构造新实例。构造器是一种特殊的方法， 用来构造并初始化对象。下面看一个例子。在标准Java库中包含一个Date类。它的对象将描述一个时间点， 例如：“December 31, 1999, 23:59:59 GMT”。

构造函数在创建对象时初始化对象。它与类同名，并且在语法上与方法相似。然而，构造函数没有显式的返回类型，通常，构造函数用来初始化类定义的实例变量，获知型其他创建完整对象所需要的启动过程。

归纳可知构造方法必须满足以下语法规则：
- 方法名必须与类名相同
- 每个类可以有一个以上的构造器
- 构造器可以有0个，1个或多个参数
- 不要声明返回类型
- 不能被static,final,abstract,native修饰，构造方法不能被子类继承，所以用final和abstract修饰没有意义。构造方法用于初始化一个新建的对象，所以用static修饰没有意义，Java语言不支持native类型的构造方法
- 构造器总是伴随着new操作一起调用

例如：
```java
class Myclass(){
    int x;
    Myclass(){
        x=10;
    }
    public int Myclass(){
    }//不是构造方法，有返回值
}
```
上面这个构造函数不带参数，但需要注意的是，构造函数还可以带形参

例如：

```java
class MyClass(){
	int x;
	MyClass(int i){
		x=i;
	}
}
class ParmConsDemo{
	public static void main(String args[]){
		MyClass t1=new MyClass(10);
		MyClass t2=new MyClass(20);
		System.out.println(t1.x+","+t2.x);
	}
}
```

程序输出
```
10,20
```
构造函数定义了一个名为i的形参，用于初始化实例变量x.

例如，当使用下面这条代码创建Employee类实例时：
```java
new Employee("James Bond", 100000, 1950, 1, 1);
```
将会把实例域设置为：
```java
name = "James Bond";
salary = 100000;
hireDay = LocalDate.of(1950, 1, 1); // January 1, 1950
```
构造器与其他的方法有一个重要的不同。构造器总是伴随着new操作符的执行被调用，而不能对一个已经存在的对象调用构造器来达到重新设置实例域的目的。例如，
```java
janes.Employee("James Bond", 250000, 1950, 1, 1); // ERROR
```
将产生编译错误。

> 稍后还会更加详细地介绍有关构造器的内容。现在只需要记住：
- 构造器与类同名
- 每个类可以有一个以上的构造器
- 构造器可以有0个、1个或多个参数
- 构造器没有返回值**也不能使用void声明构造器没有返回值，如果构造器定义了返回值类型，或使用void声明构造器没有返回值，编译不会出错，但是Java会把这个所谓的构造器当成方法来处理，就不再是构造器了**
- 构造器总是伴随着new操作一起调用

**构造方法只能通过以下方式被调用：**

- 当前类其他构造方法通过this语句调用它
- 当前类的子类的构造方法通过super语句来调用它
- 在程序中通过new语句调用它

### 3.4 隐式参数与显式参数

方法用于操作对象以及存取它们的实例域。例如，方法：

```java
public void raiseSalary(double byPercent)
{
	double raise = salary * byPercent / 100;
	salary += raise;
}
```

将调用这个方法的对象的salary实例域设置为新值。看看下面这个调用：

```java
number007.raiseSalary(5);
```

它的结果将number007.salary域的值增加5%。具体地说，这个调用将执行下列指令：

```java
double raise = nuaber007.salary * 5 / 100;
nuiber007.salary += raise;
```

raiseSalary方法有两个参数。第一个参数称为隐式(implicit)参数，是出现在方法名前的Employee类对象。第二个参数位于方法名后面括号中的数值，这是一个显式(eplicit)参数（有些人把隐式参数称为方法调用的目标或接收者。）

可以看到，显式参数是明显地列在方法声明中的，例如double byPercent。隐式参数没有出现在方法声明中。在每一个方法中，关键字this表示隐式参数。如果需要的话，可以用下列方式编写raiseSalary方法：

```java
public void raiseSalary(double byPercent) {
	double raise = this.salary * byPercent / 100;
	this.salary += raise;
}
```

有些程序员更偏爱这样的风格，因为这样可以将实例域与局部变量明显地区分开来。  

#### 关于this关键字

this是Java中的一个关键字，表示某个对象。**this可以出现在实例方法和构造方法中，但是不可以出现在类方法中。**

**在构造方法中使用this**

```java
public class People {
	int leg,hand;
	String name;
	People(String s) {
		name=s;
		this.init();//可以省略this，写成init()
	}
	void init() {
		leg=2;
		hand=2;
		System.out.println(name+"有"+hand+"只手"+leg+"只脚");
	}
	public static void main(String args[]) {
		People boshi=new People("布什");
	}
}
```

**在实例方法中使用this**

实例方法必须通过对象来调用，不能通过类名来调用；当this出现在实例方法中，代表正在调用该方法的当前对象。

实例方法可以操作类的成员变量，当实例成员变量在实例方法中出现时，默认的格式是：

```
this.成员变量
```

而static成员变量在实例方法中出现时，默认的格式是：

```
类名.成员变量
```

如：

```java
clsaa A {
	int a;
	static int y;
	void f(){
	this.x=100;
	A.y=200;
	}
}
```

当实例成员变量的名字和局部变量的名字相同时，成员变量前面的this.或者类名.就不可省略  

我们知道类的实例方法能调用类的其他方法，对于实例方法的调用的默认格式是：

```
this.方法
```

但是对于类方法调用的默认格式是：

```
类名.方法；
```

例如：

```java
class B {
	void f(){
		this.g();
		B.h();
	}
	void g(){
		System.out.println("ok");
	}
	static void h(){
		System.out.println("hello");
	}
}
```

在上述B类方法中出现了this，this代表调用方法f的当前对象，所以，方法f的方法体中this.g()就是当前对象调用方法g，也就是说，当某个对象调用方法f的过程中，又调用了方法g。由于这种逻辑关系非常明确，一个实例方法调用另一个方法时可以省略方法名字前面的"this."或"类名." 

例如：

```java
class B {
	void f(){
		.g();
		h();
	}
	void g(){
		System.out.println("ok");
	}
	static void h()){
		System.out.println("hello");
	}
}
```

需要注意的是：**this不能出现在类方法中，这是因为，类方法可以通过类名直接调用，这时，可能还没有任何对象诞生。**

### 3.5 体现封装

当对象调用方法时，方法中出现的成员变量就是指分配给该对象的变量。在讲述类的时候我们讲过类中的方法可以操作成员变量。当对象调用方法时，方法中出现的成员变量就是指分配给该对象的变量。

```java
class XiyoujiRenwu {
	float height,weight;  
	String head,ear,hand,foot,nouth;
	void speak(Strings) {
		head="歪着头”;
		Systen.out.printin(s);
	}
}

public class Example5_3 {
	public static void main(String args[]){
		XiyoujiRenwu zhubajie,sunwukong;  //声明对象
		zhubajie = new XiyoujiRenwu();  //为对象分配变量
		sunwukong = new Xiyouj iRenwu();
		zhubajie.height= 1.80f;  //对象给自己的变量赋值
		zhubajie.head="大头";
		zhubajie.ear="一双大耳朵";
		sunwukong.height= 1.62f;  //对象给自己的变量赋值
		sunwukong.weight= 1000f;
		sunwukong.head="秀发飘飘";
		System.out.println("zhubajie的身高:”+ zhubajie.height);
		System.out.printIn(" zhubajie的头:”+ zhubajie.head);
		System.out.println("sunwukong的重量:" + sunnukong.weight);
		System.out.println("sunwukong的头:”+ sunmukong. head);
		zhubajie.speak("俺老猪我想娶媳妇");  //对象调用方法
		Systen.out.println("zhubajie现在的头:”+ zhubajie.head);
		sunmukong.speak("老孙我重1000斤，我想骗八戒背我");  //对象调用方法
		Systen.out.println(" sunwnukong现在的头:”+ sunwukong.head);
	}
}
```

我们知道:类中的方法可以操作成员变量，当对象调用该方法时，方法中出现的成员变量就是指该对象的成员变量。在例5.3中，当对象zhubajie调用过方法speak之后，就将自己的头改成歪着头，后一个对象调用方法后也是这样。

### 3.6 重载构造方法

当通过new语句创建一个对象时，在不同的条件下，对象可能会有不同的初始化行为，例如，对于公司新进来的一个雇员，在开始的时候，有可能他的名字和年龄都是未知的，也有可能仅仅他的名字是已知的，也有可能两者都是已知的。如果姓名是未知的，那么就把姓名改为无名氏，如果年龄是未知的，就把年龄设为-1

可通过重载构造函数来表达对象的多种初始化行为，比如下面的例子的构造方法有三种重载形式，在一个类的多个构造方法中，可能会出现一些重复操作。为了提高代码的可重用性，Java语言允许在一个构造方法中，用this语句来调用另一个构造方法。
```java
public class Employee{
	private String name;
	private int age;
	
	public Employee(String name,int age){
		this.name=name;
		this.age=age;
	}
	
	public Employee(String name){
		this(name,-1);
	}
	public Employee(){
		this("无名氏");
	}
	public void setName(String name){
		this.name=name;
	}
	public String setName(){
		return name;
	}
	public void setAge(int age){
		this.age=age;
	}
	public int getAge{
		return age;
	}
}
```
以下程序分别通过3个构造方法创建了3个Employee对象:
```java
Employee zhangsan=new Employee("张三",25);
Employee zhang=new Employee("张三");
Employee zh=new Employee();
```
#### 3.4.1 关于无参数的构造器
很多类都包含一个无参数的构造函数，对象由无参数构造函数创建时，其状态会设置为适当的默认值。例如，以下是Employee类的无参数构造函数：
```java
public Employee() {
	name = ""
	salary = 0;
	hireDay = LocalDate.now();
}
```
如果在编写一个类时没有编写构造器， 那么系统就会提供一个无参数构造器。这个构造器将所有的实例域设置为默认值。于是， 实例域中的数值型数据设置为0、布尔型数据设置为false、所有对象变量将设置为null。

如果类中提供了至少一个构造器，但是没有提供无参数的构造器，则在构造对象时如果没有提供参数就会被视为不合法。例如，在程序清单4-2中的Employee类提供了一个简单的构造器：
```java
Employee(String name, double salary, int y, int ra, int d)
```
对于这个类，构造默认的雇员属于不合法。也就是，调用
```java
e = new Eraployee();
```
将会产生错误。

> 警告：请记住，仅当类没有提供任何构造器的时候，系统才会提供一个默认的构造器如果在编写类的时候，给出了一个构造器，哪怕是很简单的，要想让这个类的用户能够采用下列方式构造实例：
```java
new ClassName();
```
就必须提供一个默认的构造器（即不带参数的构造器）。当然，如果希望所有域被赋予默认值，可以采用下列格式：
```java
public ClassName() {
}
```
### 3.7 调用另一个构造器

用this语句来调用其他构造方法时，必须遵循以下语法规则：

- 假如在一个构造方法中使用了this语句，那么它必须作为构造方法的第一条语句，比如下面的构造方法是错误的
```java
public Employee() {
	String name="无名氏";
	this(name);//编译错误，this语句必须作为第一条语句
}
```
- 只能在一个构造方法中使用this语句来调用类的其他构造方法，而不能在实例方法中用this语句来调用类的其他构造方法
- 只能用this语句来调用其他构造方法，而不能通过方法名来直接调用构造方法。以下对构造方法的调用是非法的
```java
public Employee(){
	String name="无名氏";
	Employee(name);//编译错误，不能通过方法名来直接调用构造方法
}
```

### 3.8 类中方法的重载与多态
在java中，同一个类的两个或者多个方法可以共享一个名称，只要它们的形参声明不一样就可以。当这种情况发生时，就称方法被重载了（overloaded)，这一过程称为方法重载。方法重载是java实现多态性的途径之一。

方法重载的意思是一个类中可以有多个方法具有相同的名字，但这些方法的参数必须不相同，即或者是参数的个数不相同，或者是参数的类型不相同。  

必须注意以下重要限制：每个被重载的方法的形参类型和数量必须不同，两个方法仅返回类型不同是不够的。当然，被重载的方法的返回类型也可以是不一样的。当调用被重载的方法时，将执行形参与实参相匹配的那个方法。

重载方法必须满足以下条件：
- 方法名相同
- 方法的参数类型，个数，顺序至少有一项不相同
- 方法的返回类型可以不相同
- 方法的修饰符可以不相同

**方法的返回类型和参数的名字不参与比较，也就是说如果两个方法的名字相同，即使类型不同，也必须保证参数不同。**

> Java中存在两种多态，重载和重写，重写是与继承有关的多态，下一章讨论。    

实例：
```java
class Overload {
	void ovlDemo(){
		System.out.println("No parameters");
	}
	void ovlDemo(int a){
		System.out.println("One parameter"+a);
	}
	int ovlDemo(int a,int b){
		System.out.println("Two parameter"+a+" "+b);
		return a+b;
	}
	double ovlDemo(double a,double b){
		System.out.println("Two double parameter"+a+" "+b);
		return a+b;
	}
}
```
方法重载支持多态性，因为它是java实现单接口，多方法的途径之一。考虑下面的内容，就会理解其中的原因，在不支持方法重载的语言中，每一种方法必须被赋予惟一的名称。

> Tips 什么是签名？在java中，签名指的是方法名及其形参列表，因此，在重载时，一个类的两个方法不能具有相同的签名，注意，签名不包含返回类型，因为java不使用签名进行重载解析。

**关于重载构造函数（需要注意）**

与方法一样，构造函数也可以被重载，这样就可以用不同的方法来构造对象了。

### 3.9 默认域初始化

如果在构造器中没有显式地给域赋予初值， 那么就会被自动地赋为默认值： 数值为0、布尔值为false、对象引用为null。然而， 只有缺少程序设计经验的人才会这样做。确实，如果不明确地对域进行初始化，就会影响程序代码的可读性。

> 无论是否定义，所有的类都有构造函数，因为java自动提供了一个默认的构造函数将所有成员变量初始化为它们的初始值，即0,null,flase，分别用于数值类型，引用类型和布尔类型。当然，一旦定义自己的构造函数，就不会再使用默认的的构造函数了。

### 3.10 构造方法的访问级别
构造方法可以处于public，protected，默认和private这四种访问级别之一，本节着重介绍构造方法处于private级别的意义。当构造方法为private级别时，意味着只能在当前类中访问它；在当前类的其他构造方法中可以通过this语句调用它，此处还可以在当前类的成员方法中通过new语句调用它。

在以下场合之一，可以把类的所有构造方法都声明为private类型：

1. 在这个类中仅仅包含一些供其他程序调用的静态方法，没有任何实例方法。其他程序无需创建该类的实例，就能访问类的静态方法。

**例如java.lang.Math类就符合这种情况，在Math类中提供了一系列用于数学运算的公共静态方法，为了防止外部程序创建Math类的实例，Math类的唯一构造方法就是private类型的**

> 在之前的abstract修饰符的时候提到过，abstract类型的类也不允许实例化，也许有这样一个疑问，把Math定义为abstract类，不是也能禁止该类被实例化吗？

需要注意的是，如果一个类是抽象类，意味着他是专门用于被继承的类，可以拥有子类，而且可以创建具体子类的实例。而JDK不希望用户创建Math类的子类，在这种情况下，把类的构造方法定义为private类型更合适。

2. 禁止这个类被继承。当一个类的所有构造方法都是private类型时，假如定义了它的子类，那么子类的构造方法无法调用父类的任何构造方法，因此会导致编译错误，这在final类那提到过，把一个类声明为final类型，也能禁止这个类被继承。这两者的区别是：
- 如果一个类允许其他程序用new语句构造它的实例，但不允许拥有子类，那么就把类声明为final类型
- 如果一个类及不允许其他程序用new语句构造它的实例，但又不允许拥有子类，那就把类的所有构造方法声明为private类型

由于大多数类都允许其他程序用new语句构造它的实例，因此用final修饰符来禁止类被继承的做法更常见

3. 这个类需要把构造自身实例的细节封装起来，不允许其他程序通过new语句创建这个类的实例，这个类向其他程序提供了获得自身实力的静态方法，这种方法称为静态工厂方法。

### 3.11 对象析构与finalize方法

类是体现封装的一种数据结构，类声明的变量称作对象，对象中负责存放引用，以确保对象可以操作分配给该对象的变量以及调用类中的方法。分配给对象的变量习惯的称作对象的实体。

#### 1.避免使用空对象

没有实体的对象称作空对象，空对象不能使用。

#### 2.垃圾收集

例如

```java
Point p1=new Point(5,15);
Point p2=new Point(8,18);
p1=p2;
```

这个时候输出p1.x是8而不是5，与C++不同，这里的类有构方法，没有析构方法，Java默认有垃圾收集机制。

有些面向对象的程序设计语言，特别是 C++,有显式的析构器方法，其中放置一些当对象不再使用时需要执行的清理代码。在析构器中，最常见的操作是回收分配给对象的存储空间。由于 Java 有自动的垃圾回收器，不需要人工回收内存，所以 Java 不支持析构器。

当然，某些对象使用了内存之外的其他资源，例如，文件或使用了系统资源的另一个对象的句柄。在这种情况下，当资源不再需要时，将其回收和再利用将显得十分重要。

可以为任何一个类添加 finalize 方法。finalize 方法将在垃圾回收器清除对象之前调用。在实际应用中，不要依赖于使用 finalize 方法回收任何短缺的资源，这是因为很难知道这个方法什么时候才能够调用。

如果某个资源需要在使用完毕后立刻被关闭，那么就需要由人工来管理。对象用完时，可以应用一个close方法来完成相应的清理操作。7.2.5 节会介绍如何确保这个方法自动得到调用。 

## 4 静态域与静态方法

在前面给出的示例程序中， main方法都被标记为static修饰符。下面讨论一下这个修饰符的含义。
### 4.1 静态域
如果将域定义为static,每个类中只有一个这样的域。而每一个对象对于所有的实例域却都有自己的一份拷贝。例如，假定需要给每一个雇员賦予唯一的标识码。这里给Employee类添加一个实例域id和一个静态域nextld:
```java
class Employee {
	private static int nextld = 1;
	private int id;
}
```
现在，每一个雇员对象都有一个自己的id域，但这个类的所有实例将共享一个iiextld域。换句话说，如果有1000个Employee类的对象，则有1000个实例域id。但是，只有一个静态域nextld。即使没有一个雇员对象，静态域nextld也存在。它属于类，而不属于任何独立的对象。

> 注释：在绝大多数的面向对象程序设计语言中，静态域被称为类域。术语“static”只是沿用了C++的叫法，并无实际意义。

下面实现一个简单的方法：
```java
public void setld() {
	id = nextld;
	nextld++;
}
```
假定为harry设定雇员标识码：
```java
harry.setld();
```
harry 的id域被设置为静态域nextld当前的值，并且静态域nextld的值加1:
```java
harry.id = Employee.nextld;
Eip1oyee.nextId++;
```

**静态变量的特点**


1. 不同对象的实例变量互不相同

&emsp;&emsp;**分配给不同的对象的实例变量占有不同的内存空间。**

2. 所有对象共享类变量

3. 可以通过类名直接访问类变量

- 类的静态变量在内存中只有一个，Java虚拟机在加载类的过程中为静态变量分配内存，静变量位于方法区，被类的所有实例共享。静态变量可以直接通过类名访问。
- 类的每个实例都有相应的实例变量，每当创建一个类的实例，Java虚拟机就会为实例变量分配一次内存，实例变量位于堆区中。实例变量的生命周期取决于实例的生命周期，当创建实力的时候，实例变量被创建并分配内存，当销毁实例的时候，实例变量被销毁并撤销内存。

改变其中一个对象的类变量就同时改变了其他对象的这个类变量。

### 4.2 静态常量

静态变量使用得比较少，但静态常量却使用得比较多。例如，在Math类中定义了一个静态常量：
```java
public class Hath {
	public static final double PI = 3.14159265358979323846;
}
```
在程序中，可以采用Math.PI的形式获得这个常量。

如果关键字static被省略，PI就变成了Math类的一个实例域。需要通过Math类的对象访问PI，并且每一个Math对象都有它自己的一份PI拷贝。

另一个多次使用的静态常量是System.out。它在System 类中声明：
```java
public class System
{
	public static final PrintStream out = ...;
}
...
```
前面曾经提到过，由于每个类对象都可以对公有域进行修改，所以，最好不要将域设计为public。然而，公有常量（即final域）却没问题。因为out被声明为final,所以，不允许再将其他打印流陚给它：
```java
System.out = new PrintStrean(...); // Error out is final
```
> 注释： 如果查看一下System类，就会发现有一个setOut方法，它可以将System.out设置为不同的流。读者可能会感到奇怪，为什么这个方法可以修改final 变量的值。原因在于，setOut方法是一个本地方法，而不是用Java语言实现的。本地方法可以绕过Java语言的存取控制机制。这是一种特殊的方法，在自己编写程序时，不应该这样处理。

### 4.3 静态方法
静态方法是一种不能向对象实施操作的方法。例如，Math类的pow方法就是一静态方法。表达式
```
Math.pow(x, a)
```
不使用任何Math对象。换句话说，没有隐式的参数。可以认为静态方法是没有this参数的方法（非静态的方法中，this参数表示这个方法的隐式参数)。

Employee类的静态方法不能访问Id实例域，因为它不能操作对象。但是，静态方法可以访问自身类中的静态域。下面是使用这种静态方法的一种示例：

```java
public static int getNextld() {
	return nextld; // returns static field
}
```
可以通过类名调用这个方法：
```java
int n = Employee.getNextld();
```
这个方法可以省略关键字static? 答案是肯定的。但是，需要通过Employee对象的引用调用这个方法。

> 注释：可以使用对象调用静态方法。例如，如果harry是一个Employee对象，可以用`harry.getNextId()`代替`Employee.getNextId()`。不过，这种方式很容易造成混淆，其原因是getNextld方法计算的结果与harry毫无关系。我们建议使用类名，而不是对象来调用静态方法。

在下面两种情况下使用静态方法：
- 一方法不需要访问对象状态，其所需参数都是通过显式参数提供（例如：Math.pow）
- 一个方法只需要访问类的静态域（例如：Employee.getNextld）

> C++注释：Java中的静态域与静态方法在功能上与C++相同。但是，语法书写上却稍有所不同。在C++中，使用：：操作符访问自身作用域之外的静态域和静态方法，如`Math::PI`

> 术语“static”有一段不寻常的历史。起初，C引入关键字static是为了表示退出一个块后依然存在的局部变量在这种情况下，术语“static”是有意义的：变量一直存在，当再次进入该块时仍然存在。随后，static在C中有了第二种含义，表示不能被其他文件访问的全局变量和函数。为了避免引入一个新的关键字，关键字static被重用了。最后，C++第三次重用了这个关键字，与前面赋予的含义完全不一样， 这里将其解释为属于类且不属于类对象的变量和函数。这个含义与Java相同。

**实例方法和静态方法的区别**

1. 对象调用实例方法

当类的字节码文件加载到内存时，类的实例方法不会被分配入口地址，只有该类创建对象后，类中的实例方法才分配入口地址。

需要注意的是，当我们创建第一个对象时，类中的实例方法就分配了入口地址，当再创建对象时，不再分配入口地址，**也就是说，方法的入口地址被所有对象共享，当所有对象都不存在时，方法的入口地址才会被取消。**

2. 类名调用类方法

对于类中的类方法，在该类被加载到内存时，就分配了相应的入口地址，从而类方法不仅可以被类创建的任何对象调用执行，也可以被类名调用执行，**类方法的入口地址直到程序退出才被取消。**  

**和实例方法不同的是，类方法不可以操作实例变量，这是因为在类创建对象之前，实例成员变量还没有分配内存。**

## 5 方法参数（参数传值）

方法中最重要的部分之一就是方法的参数，参数属于局部变量，当对象调用方法时，参数被分配到内存空间，并要求调用者向参数传递值，即方法被调用时，参数变量必须有具体的值。
### 5.1 传值机制
Java中，方法所有的参数都是传值的，也就是说，方法中参数变量的值是调用者指定值的复制。

Java程序设计语言总是采用按值调用。也就是说， 方法得到的是所有参数值的一个拷贝，特别是，方法不能修改传递给它的任何参数变量的内容。

例如， 考虑下面的调用：

```java
double percent = 10;
harry.raiseSalary(percent);
```
不必理睬这个方法的具体实现，在方法调用之后，percent的值还是10。

下面再仔细地研究一下这种情况。假定一个方法试图将一个参数值增加至3倍：
```java
public static void tripieValue(double x) // doesn't work
{
	x = 3 * x;
}
```
然后调用这个方法：
```java
double percent = 10;
tripieValue(percent)
```
不过，并没有做到这一点调用这个方法之后，percent的值还是10。下面看一下具体的执行过程：
- x被初始化为percent 值的一个拷贝（也就是10)
- x被乘以3后等于30。但是percent 仍然是10(如图4-6 所示)。
- 这个方法结束之后，参数变量x不再使用。

然而，方法参数共有两种类型：
- 基本数据类型（数字、布尔值）
- 对象引用。

读者已经看到，一个方法不可能修改一个基本数据类型的参数。而对象引用作为参数就不同了，可以很容易地利用下面这个方法实现将一个雇员的薪金提高两倍的操作：
```java
public static void tripieSalary(Employee x) // works
{
	x.raiseSalary(200) ;
}
```
当调用
```java
harry = new Employee(...);
tri pieSalary(harry);
```
时，具体的执行过程为：
- x被初始化为harry值的拷贝，这里是一个对象的引用。
- raiseSalary方法应用于这个对象引用。x和harry同时引用的那个Employee 对象的薪金提高了200%。
- 方法结束后，参数变量x不再使用。当然，对象变量harry继续引用那个薪金增至3倍的雇员对象。

读者已经看到，实现一个改变对象参数状态的方法并不是一件难事。理由很简单， 方法得到的是对象引用的拷贝，对象引用及其他的拷贝同时引用同一个对象。很多程序设计语言（特别是，C++ 和Pascal）提供了两种参数传递的方式：值调用和引用调用。有些程序员（甚至本书的作者）认为Java程序设计语言对对象采用的是引用调用，实际上，这种理解是不对的。由于这种误解具有一定的普遍性， 所以下面给出一个反例来详细地阐述一下这个问题。

首先， 编写一个交换两个雇员对象的方法：
```java
public static void swap(Employee x , Employee y) // doesn't work
	Employee temp = x;
	x = y;
	y = temp;
}
```
如果Java对对象采用的是按引用调用，那么这个方法就应该能够实现交换数据的效果：
```java
Employee a = new Employee("Alice", . . .);
Employee b = new Employee("Bob", . . .);
swap(a, b);
// does a now refer to Bob, b to Alice?
```
但是，方法并没有改变存储在变量a和b中的对象引用。swap方法的参数x和y被初始化为两个对象引用的拷贝，这个方法交换的是这两个拷贝。
```java
// x refers to Alice, y to Bob
Employee temp = x;
x = y;
y = temp;
// now x refers to Bob, y to Alice
```
最终，白费力气。在方法结束时参数变量x和y被丢弃了。原来的变量a和b仍然引用这个方法调用之前所引用的对象，这个过程说明：Java程序设计语言对对象采用的不是引用调用，实际上，对象引用是按值传递的。**（所谓值传递，就是将实际参数值的副本传入方法内，而参数本身不会受到任何影响）**

下面总结一下Java中方法参数的使用情况：
- 一个方法不能修改一个基本数据类型的参数（即数值型或布尔型）。
- 一个方法可以改变一个对象参数的状态。
- 一个方法不能让对象参数引用一个新的对象。

程序清单4-4中的程序给出了相应的演示。在这个程序中， 首先试图将一个值参数的值提高两倍，但没有成功：
```
Testing tripleValue:
Before: percent=10.0
End of method: x:30.0
After: percent=10.0
```
随后， 成功地将一个雇员的薪金提高了两倍：
```
Testing tripleSalary:
Before: salary=50000.0
End of method: salary=150000.0
After: salary=150000.0
```
方法结束之后，harry引用的对象状态发生了改变。这是因为这个方法可以通过对象引用的拷贝修改所引用的对象状态。

最后，程序演示了swap方法的失败效果：
```
Testing swap:
Before: a=Alice
Before: b=Bob
End of method: x=Bob
End of method: y=Alice
After: a=Alice
After: b=Bob
```
可以看出，参数变量x和y交换了，但是变量a和b没有受到影响。
```java
程序清单4-4 ParamTest/ParamTest.java
/**
* This program demonstrates parameter passing in Java.
* ©version 1.00 2000-01-27
* author Cay Horstmann
**/
public class ParamTest {
	public static void main(String[] args) {
	/*
	* Test 1: Methods can't modify numeric parameters
	*/
	System.out.println("Testing tripieValue:") ;
	double percent = 10;
	System.out.println("Before: percent " + percent) ;
	tripieValue(percent) ;
	System.out.println("After: percent=" + percent) ;

	/*
	* Test 2: Methods can change the state of object parameters
	*/
	System.out.println("\nTesting tripleSalary:")；
	Employee harry = new Employee("Harry", 50000) ;

	System.out.println("Before: salary=" + harry.getSalary()) ;
	tripieSalary(harry) ;
	System.out.println("After: salary=" + harry.getSal ary()) ;
	/*
	* Test 3: Methods can ' t attach new objects to object parameters
	*/
	System.out.println("\nTesting swap:")；
	Employee a = new Employee("Alice", 70000) ;
	Employee b = new Employee("Bob", 60000) ;
	System,out.println("Before: a=" + a.getNameQ);
	System,out.println("Before: b=" + b.getNameO) ;
	swap(a, b);
	System,out.println("After: a=" + a.getNameO) ;
	System.out.println("After: b=" + b.getNameO) ;
	public static void tripieValue(double x) // doesn't work {
		x = 3 * x;
		System.out.println('End of method: x=" + x);
	}
	public static void tripieSalary(Employee x) // works {
		x.raiseSalary(200);
		System.out.println("End of method: salary=" + x.getSalary()) ;
	public static void swap(Employee x , Employee y) {
		Employee temp = x;
		x = y;
		y = temp;
		System, out.println("End of method: x=" + x.getName()) ;
		System.out.println("End of method: y=" + y.getName());
	}
	class Employee // simplified Employee class 
	{
		private String name;
		private double salary;
		public Employee(String n, double s) {
			name = n;
			salary = s;
		}
		public String getName() {
			return name;
		}
		public double getSalary() {
			return salary;
		}
		public void raiseSalary(double byPercent) {
			double raise = salary * byPercent / 100;
			salary += raise;
		}
	}
}
```

### 5.2 基本数据类型参数的传值
```java
package test1;
import java.util.Scanner;

class Circle {
	double radius,area;
	Circle(){
	}
	Circle(double r){
		radius=r;
	}
	void setRadius(double r) {
		if(r>0) {
			radius=r;
		}
	}
	double getRadius() {
		return radius;
	}
	double getArea() {
		area=3.14*radius*radius;
		return area;
	}
}

class Circular{
	Circle bottom;
	double height;
	Circular(Circle c,double h){
		bottom=c;
		height=h;
	}
	double getVolme() {
		return bottom.getArea()*height/3.0;
	}
	double getBottomRadius() {
		return bottom.getRadius();
	}
	public void setBottomRadius(double r) {
		bottom.setRadius(r);
	}
}


public class exercise {
	public static void main(String args[]) {
		Circle circle=new Circle(10);
		System.out.println("main方法中circle的引用："+circle);
		System.out.println("main方法中circle的半径："+circle.getRadius());
		Circular circular=new Circular(circle,20);
		System.out.println("circular圆锥的bottom的引用："+circular.bottom);
		System.out.println("圆锥的bottom的半径："+circular.getVolme());
		System.out.println("圆锥的体积："+circular.getVolme());
		double r=8888;
		System.out.println("圆锥更改底园bottom的半径："+r);
		circular.setBottomRadius(r);
		System.out.println("圆锥的bottom的半径："+circular.getBottomRadius());
		System.out.println("圆锥的体积："+circular.getVolme());
		System.out.println("main方法中circle的半径"+circle.getRadius());
		System.out.println("main方法中circle的引用将会发生变化");
		circle=new Circle(1000);
		System.out.println("现在mian方法中circle的引用："+circle);
		System.out.println("main方法中circle的引用："+circle.getRadius());
		System.out.println("但是不影响circular圆锥的bottom的引用");
		System.out.println("circular圆锥的bottom引用"+circular.bottom);
		System.out.println("圆锥的bottom的半径："+circular.getBottomRadius());
	}
}
```

## 6 包

一般而言，当命名类的时候，是从名称空间中分配一个名称。名称空间定义了一个声明性的区域。在java中，同一个名称空间中的两个类不能使用相同的名称。这样，再给定的一个名称空间中，每一个类名必然是唯一的包提供了一种为名称空间分区的方法。当在包中定义一个类时，该包的名称将附加到每一个类上，这样就避免了与其他包中具有相同名字的类发生名字冲突。

Java允许使用包（package）将类组织起来。借助于包可以方便地组织自己的代码，并将 自己的代码与别人提供的代码库分开管理。

标准的Java类库分布在多个包中，包括java.lang、java.util和java.net等。标准的Java包具有一个层次结构。如同硬盘的目录嵌套一样，也可以使用嵌套层次组织包。所有标准的Java包都处于java和javax包层次中。

使用包的主要原因是确保类名的唯一性。假如两个程序员不约而同地建立了Employee类。只要将这些类放置在不同的包中，就不会产生冲突。事实上，为了保证包名的绝对唯一性，Sun公司建议将公司的因特网域名（这显然是独一无二的）以逆序的形式作为包名，并且对于不同的项目使用不同的子包。例如，horstmann.com是本书作者之一注册的域名。逆序形式为com.horstmann。这个包还可以被进一步地划分成子包，如 com.horstmann.corejava。

从编译器的角度来看，嵌套的包之间没有任何关系。例如，java.util包与java.util.jar包毫无关系。每一个都拥有独立的类集合

### 6.1 包的作用

- 1.把功能相似或相关的类或接口组织在同一个包中，方便类的查找和使用。
- 2.如同文件夹一样，包也采用了树形目录的存储方式。同一个包中的类名字是不同的，不同的包中的类的名字是可以相同的，当同时调用两个不同包中相同类名的类时，应该加上包名加以区别。因此，包可以避免名字冲突。
- 3.包也限定了访问权限，拥有包访问权限的类才能访问某个包中的类。

Java使用包（package）这种机制是为了防止命名冲突，访问控制，提供搜索和定位类（class）、接口、枚举（enumerations）和注释（annotation）等。

包语句的语法格式为：

```java
package pkg1[．pkg2[．pkg3…]];
```

例如,一个Something.java 文件它的内容

```java
package net.java.util;
public class Something{
   ...
}
```

那么它的路径应该是`net/java/util/Something.java`这样保存的。package(包)的作用是把不同的java程序分类保存，更方便的被其他java程序调用。  

一个包（package）可以定义为一组相互联系的类型（类、接口、枚举和注释），为这些类型提供访问保护和命名空间管理的功能。  

以下是一些Java中的包：

```java
java.lang-打包基础的类
java.io-包含输入输出功能的函数
```

开发者可以自己把一组类和接口等打包，并定义自己的包。而且在实际开发中这样做是值得提倡的，当你自己完成类的实现之后，将相关的类分组，可以让其他的编程者更容易地确定哪些类、接口、枚举和注释等是相关的。

**由于包创建了新的命名空间（namespace），所以不会跟其他包中的任何名字产生命名冲突。使用包这种机制，更容易实现访问控制，并且让定位相关类更加简单。**

### 6.2 创建包

**创建包的时候，你需要为这个包取一个合适的名字。之后，如果其他的一个源文件包含了这个包提供的类、接口、枚举或者注释类型的时候，都必须将这个包的声明放在这个源文件的开头。**

**包声明应该在源文件的第一行，每个源文件只能有一个包声明，这个文件中的每个类型都应用于它。**

*如果一个源文件中没有使用包声明，那么其中的类，函数，枚举，注释等将被放在一个无名的包（unnamed package）中。*

#### 例子

让我们来看一个例子，这个例子创建了一个叫做animals的包。通常使用小写的字母来命名避免与类、接口名字的冲突  

在 animals 包中加入一个接口（interface）：

```java
Animal.java 文件代码：
/* 文件名: Animal.java */
package animals;//该文件属于animals包

interface Animal {
   public void eat();
   public void travel();
}
```

接下来，在同一个包中加入该接口的实现：

```java
//MammalInt.java 文件代码：
package animals;

/* 文件名 : MammalInt.java */
public class MammalInt implements Animal{
   public void eat(){
      System.out.println("Mammal eats");
   }
   public void travel(){
      System.out.println("Mammal travels");
   } 
   public int noOfLegs(){
      return 0;
   }
   public static void main(String args[]){
      MammalInt m = new MammalInt();
      m.eat();
      m.travel();
   }
}
```

然后，编译这两个文件，并把他们放在一个叫做animals的子目录中。 用下面的命令来运行：

```
$ mkdir animals
$ cp Animal.class  MammalInt.class animals
$ java animals/MammalInt

Mammal eats
Mammal travel
```

### 6.3 包和成员访问

包也参与了java的访问控制机制，元素的可见性取决于它的访问说明private，public，protected或者默认，也取决于元素所在的包。因此，一个元素的可见性就由它所属的类和所属的包的可见性来决定，这种多层次的访问控制方法适用与丰富的访问权限分表，如下表：

| private成员                  | 默认成员 | protected成员 | public成员 |
| ---------------------------- | -------- | ------------- | ---------- |
| 在同一个类中可见             | Y        | Y             | Y          |
| 在位于同一个包中的子类中可见 | N        | Y             | Y          |
| 在位于同一个包中的非子类可见 | N        | Y             | Y          |
| 在位于不同包中的子类可见     | N        | N             | Y          |
| 在位于不同包中的非子类可见   | N        | N             | N          |


### 6.4 导入包（import关键字）

为了能够使用某一个包的成员，我们需要在Java程序中明确导入该包。使用"import"语句可完成此功能。

在java源文件中import语句应位于package语句之后，所有类的定义之前，可以没有，也可以有多条，其语法格式为：

```java
import package1[.package2…].(classname|*);
```

如果在一个包中，一个类想要使用本包中的另一个类，那么该包名可以省略。

> 例子
>
> 下面的payroll包已经包含了Employee类，接下来向payroll包中添加一个Boss类。Boss类引用Employee类的时候可以不用使用payroll前缀，Boss类的实例如下。
>
> ```java
> //Boss.java 文件代码：
> package payroll;
> 
> public class Boss
> {
> public void payEmployee(Employee e)
> {
>    e.mailCheck();
> }
> }
> ```
>
> 如果Boss类不在payroll包中又会怎样？Boss类必须使用下面几种方法之一来引用其他包中的类。
>
> 使用类全名描述，例如：
>
> ```java
> payroll.Employee
> ```
>
> 用import关键字引入，使用通配符 "*"
>
> ```java
> import payroll.*;
> ```
>
> 使用import关键字引入 Employee 类:
>
> ```java
> import payroll.Employee;
> ```

**注意：**

类文件中可以包含任意数量的import声明。import声明必须在包声明之后，类声明之前。

但是，需要注意的是，只能使用星号（\*）导入一个包，而不能使用import java.*或import java.\*.\*导入以java为前缀的所有包。 

**在大多数情况下，只导入所需的包，并不必过多地理睬它们。但在发生命名冲突的时候，就不能不注意包的名字了。例如，java.util 和java.sql包都有日期（Date) 类。如果在程序中导入了这两个包：**

```java
import java.util .*;
import java.sql .*;
```

在程序使用Date类的时候，就会出现一个编译错误：

```
Date today; // Error java.util .Date or java.sql .Date?
```

此时编译器无法确定程序使用的是哪一个Date类。可以采用增加一个特定的import语句来解决这个问题

```java
import java.util .*;
import java.sql .*;
import java.util .Date;
```

如果这两个Date类都需要使用，又该怎么办呢？ 答案是，在每个类名的前面加上完整的包名。

```java
java.util .Date deadline = new java.util .Date() ;
java.sql .Date today = new java.sql .Date(...) ;
```

在包中定位类是编译器（ compiler) 的工作。类文件中的字节码肯定使用完整的包名来引用其他类。

> C++注释：C++程序员经常将import与#include弄混。实际上，这两者之间并没有共同之处。在C++中，必须使用include将外部特性的声明加栽进来，这是因为C++编译器无法查看任何文件的内部， 除了正在编译的文件以及在头文件中明确包含的文件。Java编译器可以查看其他文件的内部， 只要告诉它到哪里去查看就可以了
> 在Java中，通过显式地给出包名，如java.util.Date，就可以不使用import; 而在C++中，无法避免使用include指令。Import语句的唯一的好处是简捷。可以使用简短的名字而不是完整的包名来引用一个类。例如，在import java.util.* ( 或import java.util.Date) 语句之后，可以仅仅用Date引用java.util.Date类。
>
> 在C++中，与包机制类似的是命名空间(namespace)。在Java中，package与import语句类似于C++中的namespace和using指令。

### 6.5 将类放入包中

要想将一个类放入包中，就必须将包的名字放在源文件的开头，包中定义类的代码之前。例如，Employee.java开头是这样的：

```java
package com.horstiman.corejava;
public class Employee {
	...
} 
```

如果没有在源文件中放置package语句，这个源文件中的类就被放置在一个默认包 (defaulf package)中。默认包是一个没有名字的包。在此之前，我们定义的所有类都在默认包中。

将包中的文件放到与完整的包名匹配的子目录中。例如，com.horstmann.corejava包中的所有源文件应该被放置在子目录com/horstmann/corejava (Windows中com\horstmann\corejava) 中。编译器将类文件也放在相同的目录结构中。

### 6.6 package的目录结构

类放在包中会有两种主要的结果：

- 包名成为类名的一部分，正如我们前面讨论的一样。
- 包名必须与相应的字节码所在的目录结构相吻合。

下面是管理你自己java中文件的一种简单方式：

将类、接口等类型的源码放在一个文本中，这个文件的名字就是这个类型的名字，并以`.java`作为扩展名。例如：

```java
// 文件名 :  Car.java
package vehicle;
public class Car {
   // 类实现  导入包
}
```

接下来，把源文件放在一个目录中，这个目录要对应类所在包的名字。

```
....\vehicle\Car.java
```

现在，正确的类名和路径将会是如下样子：

> 类名 -> vehicle.Car
> 路径名 -> vehicle\Car.java (在 windows 系统中)

通常，一个公司使用它互联网域名的颠倒形式来作为它的包名.例如：互联网域名是runoob.com，所有的包名都以 com.runoob开头。包名中的每一个部分对应一个子目录。

例如：有一个`com.runoob.test`的包，这个包包含一个叫做`Runoob.java`的源文件，那么相应的，应该有如下面的一连串子目录：`...\com\runoob\test\Runoob.java`

编译的时候，编译器为包中定义的每个类、接口等类型各创建一个不同的输出文件，输出文件的名字就是这个类型的名字，并加上`.class`作为扩展后缀。 例如：

```java
// 文件名: Runoob.java
package com.runoob.test;
public class Runoob { 
}
class Google { 
}
```

现在，我们用`-d`选项来编译这个文件，如下：

```
$javac -d . Runoob.java
```

这样会像下面这样放置编译了的文件：

```
.\com\runoob\test\Runoob.class
.\com\runoob\test\Google.class
```

你可以像下面这样来导入所有`\com\runoob\test\`中定义的类、接口等：

```
import com.runoob.test.*;
```

编译之后的`.class`文件应该和`.java`源文件一样，它们放置的目录应该跟包的名字对应起来。但是，并不要求`.class`文件的路径跟相应的`.java`的路径一样。你可以分开来安排源码和类的目录。

```
<path-one>\sources\com\runoob\test\Runoob.java
<path-two>\classes\com\runoob\test\Google.class
```

这样，你可以将你的类目录分享给其他的编程人员，而不用透露自己的源码。用这种方法管理源码和类文件可以让编译器和java虚拟机（JVM）可以找到你程序中使用的所有类型。

类目录的绝对路径叫做`class path`。设置在系统变量`CLASSPATH`中。编译器和java虚拟机通过将`package`字加到`class path`后来构造`.class`文件的路径。

`<path- two>\ classe`是`class path`，`package`名字是`com.runoob.test`,而编译器和JVM会在`<path-two>\classes\com\runoob\test`中找 `.class`文件。

一个`class path`可能会包含好几个路径，多路径应该用分隔符分开。默认情况下，编译器和JVM查找当前目录。JAR文件按包含Java平台相关的类，所以他们的目录默认放在了`class path`中。

### 6.7 设置CLASSPATH系统变量

用下面的命令显示当前的CLASSPATH变量：

```
Windows 平台（DOS 命令行下）:C:\> set CLASSPATH
UNIX 平台（Bourne shell 下）:# echo $CLASSPATH
```

删除当前CLASSPATH变量内容：

```
Windows 平台（DOS 命令行下）：C:\> set CLASSPATH=
UNIX 平台（Bourne shell 下）：# unset CLASSPATH; export CLASSPATH
```

设置CLASSPATH变量:

```
Windows 平台（DOS 命令行下）： C:\> set CLASSPATH=C:\users\jack\java\classes
UNIX 平台（Bourne shell 下）：# CLASSPATH=/home/jack/java/classes; export CLASSPATH
```

---

### 6.8 JDK中提供的Java基本包

JDK中提供了一些Java基本包，主要包括：

- java.lang包：包含线程类，异常类，系统类，整数类和字符串类等，这些类是编写Java程序经常用到的。这个包是Java虚拟机自动引入的，也就是说，即使没有提供import java.lang.*语句，这个包也会被自动引入。
- java.awt包：抽象窗口工具包，这个包主要用于创建GUI界面的类及绘图类
- java.io包：输入/输出包，包含各种输入输出流，如文件输入流类（FileInputStream类），以及文件输出流类（FileOutputStream）等
- java.util包：提供一些实用类，如日期类和集合类。
- java.net包：提供TCP/IP网络协议，包含Socket类，以及和URL相关的类，这些都用于网络编程

## 7 文档注释

JDK包含一个很有用的工具， 叫做javadoc, 它可以由源文件生成一个HTML文档。

如果在源代码中添加以专用的定界符/** 开始的注释， 那么可以很容易地生成一个看上去具有专业水准的文档。这是一种很好的方式，因为这种方式可以将代码与注释保存在一个地方。如果将文档存入一个独立的文件中， 就有可能会随着时间的推移，出现代码和注释不一致的问题。然而，由于文档注释与源代码在同一个文件中，在修改源代码的同时，重新运行javadoc就可以轻而易举地保持两者的一致性。
### 7.1 注释的插入
javadoc 实用程序(utility)从下面几个特性中抽取信息：
- 包
- 公有类与接口
- 公有的和受保护的构造器及方法
 公有的和受保护的域

应该为上面几部分编写注释、注释应该放置在所描述特性的前面。注释以/** 开始，并以*/结束。

每个/** ...\*/文档注释在标记之后紧跟着自由格式文本(free-form text)。标记由@开始，如`@author`或`@param`。

自由格式文本的第一句应该是一个概要性的句子。javadoc实用程序自动地将这些句子抽取出来形成概要页。

在自由格式文本中， 可以使用HTML修饰符，例如，用于强调的`<em>...</eitf>`、用于着重强调的`<strong>...</strong>`以及包含图像的`<img...>`等。不过，一定不要使用`<hl>`或`<hr>`, 因为它们会与文档的格式产生冲突。若要键入等宽代码，需使用`{@code...}`而不是`<code>...</code>`—这样一来，就不用操心对代码中的`<字符转义>`了。

### 7.2 类注释
类注释必须放在import语句之后，类定义之前。

下面是一个类注释的例子：
```java
/**
* A {©code Card} object represents a playing card , such
* as "Queen of Hearts". A card has a suit (Diamond, Heart ,
* Spade or Club) and a value (1 = Ace, 2 . . . 10, 11 = Jack,
* 12 = Queen , 13 = King)
*/
public class Card {
	...
}
```
> 注释： 没有必要在每一行的开始用星号\*， 例如， 以下注释同样是合法的：
```java
/**
A <code>Card< / code> object represents a playing card , such
as "Queen of Hearts". A card has a suit (Diamond， Heart ,
Spade or Club) and a value (1 = Ace, 2 . . . 10, 11 = jack ,
12 = Queen, 13 = King) .
*/
```
然而，大部分IDE提供了自动添加星号*, 并且当注释行改变时，自动重新排列这些星号的功能。

### 7.3 方法注释
每一个方法注释必须放在所描述的方法之前。除了通用标记之外，还可以使用下面的标记：
- `@param`变量描述
这个标记将对当前方法的“param”（参数）部分添加一个条目。这个描述可以占据多行，并可以使用HTML标记。一个方法的所有@param标记必须放在一起。
- `@return`描述
这个标记将对当前方法添加“return”（返回）部分。这个描述可以跨越多行，并可以使用HTML标记。
- `@throws`类描述
这个标记将添加一个注释，用于表示这个方法有可能抛出异常。
### 7.4 域注释
只需要对公有域（通常指的是静态常量）建立文档。例如,
```java
/**
* The "Hearts" card suit
*/
public static final int HEARTS = 1;
```
### 7.5 通用注释
下面的标记可以用在类文档的注释中。
- `@author`姓名
  这个标记将产生一个"author"（作者）条目。可以使用多个`@author`标记，每个`@author`标记对应一个作者

- `@version`

  这个标记将产生一个"version"（版本）条目。这里的文本可以是对当前版本的任何描述。
  下面的标记可以用于所有的文档注释中。

- `@sinee` 文本
  这个标记将产生一个"since"（始于）条目。这里的text可以是对引人特性的版本描述。例如，`@since version 1.7.10`

- `@deprecated`
  这个标记将对类、方法或变量添加一个不再使用的注释。文本中给出了取代的建议。

  例如，

  `@deprecated Use <code> setVIsible(true)</code> instead`通过`@see`和`@link`标记，可以使用超级链接，链接到javadoc文档的相关部分或外部文档。
  
- `@see`引用
  这个标记将在“see also”部分增加一个超级链接。它可以用于类中，也可以用于方法中。这里的引用可以选择下列情形之一：

  ```html
  package.class#feature label
  <a href="...">lable</a>
  "test"
  ```

  第一种情况是最常见的。只要提供类、方法或变量的名字，javadoc就在文档中插入一个超链接。例如，

  ```java
  @see com.horstraann.corejava.Employee#raiseSalary(double)
  ```

  建立一个链接到`com.horstmann.corejava.Employee`类的`raiseSalary(double)`方法的超链接。可以省略包名，甚至把包名和类名都省去，此时，链接将定位于当前包或当前类

  需要注意，一定要使用井号（#)，而不要使用句号（.）分隔类名与方法名，或类名与变量名。Java编译器本身可以熟练地断定句点在分隔包、子包、类、内部类与方法和变量时的不同含义。但是javadoc实用程序就没有这么聪明了，因此必须对它提供帮助。

  如果@see标记后面有一个<字符，就需要指定一个超链接。可以超链接到任何URL。例如：

  ```java
  @see <a href="m«w.horstmann .com/corejava.html">The Core Java home page</a>
  ```

  在上述各种情况下， 都可以指定一个可选的标签(label)作为链接锚(link anchor)如果省略了label,用户看到的锚的名称就是目标代码名或URL。

  如果@see 标记后面有一个双引号（"）字符， 文本就会显示在“ see also” 部分。

  例如，

  ```java
  @see "Core Java 2 volume 2"
  ```

  可以为一个特性添加多个`@see`标记，但必须将它们放在一起。
- 如果愿意的话， 还可以在注释中的任何位置放置指向其他类或方法的超级链接， 以及插入一个专用的标记，例如，

  ```java
  {@link package.class#feature label}
  ```

  这里的特性描述规则与`@see`标记规则一样。
### 7.6 包与概述注释
可以直接将类、方法和变量的注释放置在Java源文件中，只要用/** ...\*/ 文档注释界定就可以了。但是，要想产生包注释，就需要在每一个包目录中添加一个单独的文件。可以有如下两个选择：
- 1)提供一个以`package.html`命名的HTML文件。在标记`<body>...</body>`之间的所有文本都会被抽取出来。
- 2)提供一个以`package-info.java`命名的Java 文件。这个文件必须包含一个初始的以`/**` 和`*/`界定的Javadoc注释，跟随在一个包语句之后。它不应该包含更多的代码或注释。

还可以为所有的源文件提供一个概述性的注释。这个注释将被放置在一个名为overview.html的文件中，这个文件位于包含所有源文件的父目录中。标记`<body>... </body>`之间的所有文本将被抽取出来。当用户从导航栏中选择“Overview” 时，就会显示出这些注释内容。
### 7.7 注释的抽取
这里，假设HTML文件将被存放在目录docDirectory下。执行以下步骤：
- 1)切换到包含想要生成文档的源文件目录。如果有嵌套的包要生成文档，例如com.horstmann.corejava, 就必须切换到包含子目录com的目录（如果存在overview.html文件的话，这也是它的所在目录)。
- 2)如果是一个包，应该运行命令:
```
javadoc -d docDirectory nameOfPackage
```
或对于多个包生成文档，运行:
```
javadoc -d docDirectory nameOfPackage\ nameOfPackage . . .
```
如果文件在默认包中，就应该运行：
```
javadoc -d docDirectory *. java
```
如果省略了`-d docDirectory`选项，那HTML文件就会被提取到当前目录下。这样有可能会带来混乱，因此不提倡这种做法。

可以使用多种形式的命令行选项对javadoc程序进行调整。例如，可以使用-author和-version选项在文档中包含`@author`和`@version`标记（默认情况下，这些标记会被省略)。另一个很有用的选项是-link, 用来为标准类添加超链接。例如，如果使用命令
```
javadoc -link http://docs.oracle.eom/:javase/8/docs/api *.java
```
那么，所有的标准类库类都会自动地链接到Oracle网站的文档。如果使用-linksource选项，则每个源文件被转换为HTML (不对代码着色，但包含行编号)，并且每个类和方法名将转变为指向源代码的超链接。

有关其他的选项， 请查阅javadoc实用程序的联机文档，http://docs.orade.eom/javase/8/docs/guides/javadoc

> 注释：如果需要进一步的定制，例如，生成非HTML 格式的文档，可以提供自定义的doclet, 以便生成想要的任何输出形式。显然，这是一种特殊的需求，有关细节内容请查阅 http://docs.oracle.com/javase/8/docs/guides/javadoc/doclet/overview.html 的联机文档。

## 8 类设计技巧

我们不会面面俱到，也不希望过于沉闷，所以这一章结束之前，简单地介绍几点技巧。应用这些技巧可以使得设计出来的类更具有OOP的专业水准。 

1. 一定要保证数据私有

    这是最重要的；绝对不要破坏封装性。有时候，需要编写一个访问器方法或更改器方法，但是最好还是保持实例域的私有性。很多惨痛的经验告诉我们，数据的表示形式很可能会改变，但它们的使用方式却不会经常发生变化。当数据保持私有时，它们的表示形式的变化不会对类的使用者产生影响，即使出现bug也易于检测。

2. 一定要对数据初始化

   Java不对局部变量进行初始化，但是会对对象的实例域进行初始化。最好不要依赖于系统的默认值，而是应该显式地初始化所有的数据，具体的初始化方式可以是提供默认值，也可以是在所有构造器中设置默认值。

3. 不要在类中使用过多的基本类型

   就是说，用其他的类代替多个相关的基本类型的使用。这样会使类更加易于理解且易于修改。例如，用一个称为Address的新的类替换一个Customer类中以下的实例域： 

   ```java
   private String street;
   private String city;
   private String state;
   private int zip;
   ```

    这样，可以很容易处理地址的变化，例如，需要增加对国际地址的处理。 

4. 不是所有的域都需要独立的域访问器和域更改器

   或许，需要获得或设置雇员的薪金。而一旦构造了雇员对象，就应该禁止更改雇用日 期，并且在对象中，常常包含一些不希望别人获得或设置的实例域，例如，在Address类中，存放州缩写的数组。 

5. 将职责过多的类进行分解

   这样说似乎有点含糊不清，究竟多少算是“过多” ？每个人的看法不同。但是，如果明 显地可以将一个复杂的类分解成两个更为简单的类，就应该将其分解（但另一方面，也不要走极端。设计10个类，每个类只有一个方法，显然有些矫枉过正了）
   
   下面是一个反面的设计示例。 
   
   ```java
   public class CardDeck // bad design  
   {
   	private int[] value;
   	private int[] suit;
   	public CardDeck(){. . .}
   	public void shuffle() { ... }
   	public int getTopValue() { ...}
   	public int getTopSuit() { ... }
   	public void draw() { ... }
   } 
   ```
   
   实际上，这个类实现了两个独立的概念：一副牌（含有 shuffle 方法和 draw方法）和一张牌（含有查看面值和花色的方法)。另外，引入一个表示单张牌的Card类。现在有两个类， 每个类完成自己的职责：
   
   ```java
   public class CardDeck {
   	private Card[] cards;
   	public CardDeck() { ... }
   	public void shuffle() { ... }
   	public Card getTopO { ... }
   	public void draw() { ...}
   }
   public class Card {
   	private int value;
   	private int suit;
   	public Card(int aValue, int aSuit){ ... }
   	public int getValue() { ... }
   	public int getSuit() { ... }
   }
   ```
   
6. 类名和方法名要能够体现它们的职责

   与变量应该有一个能够反映其含义的名字一样，类也应该如此（在标准类库中，也存在着一些含义不明确的例子，如：Date类实际上是一个用于描述时间的类)。 

   命名类名的良好习惯是采用一个名词(Order)、前面有形容词修饰的名词(RushOrder) 或动名词（有“-ing”后缀）修饰名词（例如，BillingAddress)。对于方法来说，习惯是访问器方法用小写get开头(getSalary), 更改器方法用小写的set开头(setSalary) 

7. 优先使用不可变的类

    LocalDate类以及java.time包中的其他类是不可变的----没有方法能修改对象的状态。类似plusDays的方法并不是更改对象，而是返回状态已修改的新对象。 

    更改对象的问题在于，如果多个线程试图同时更新一个对象，就会发生并发更改。其结果是不可预料的。如果类是不可变的，就可以安全地在多个线程间共享其对象。

    因此，要尽可能让类是不可变的，这是一个很好的想法。对于表示值的类，如一个字符串或一个时间点，这尤其容易。计算会生成新值，而不是更新原来的值。 

    当然，并不是所有类都应当是不可变的。如果员工加薪时让raiseSalary方法返回一个新的Employee对象，这会很奇怪。

    本章介绍了Java这种面向对象语言的有关对象和类的基础知识。为了真正做到面向对象，程序设计语言还必须支持继承和多态。Java提供了对这些特性的支持，具体内容将在下一章中介绍。
