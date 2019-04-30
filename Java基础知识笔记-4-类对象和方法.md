Java基础知识笔记-4-类对象和方法

# 4 类，对象和方法

## 1 类的基础知识
&emsp;&emsp;类是定义对象形式的模板，指定了数据，以及操作数据代码，java使用类的规范来构造对象，而对象是类的实例。因此，类实质上是一系列指定如何构建对象的计划，类是逻辑抽象结构，搞清楚这个问题非常重要，直到类的对象被创建时，内存中才会有类的物理表示。
> 顶层类是指不是嵌套类的类

> 嵌套类是指其声明出现在其他类体或接口体中的类

**组成类的方法和变量被称为类的成员。数据成员也被称为实例变量**
### 1.1 类的基本形式
&emsp;&emsp;当定义类时，要声明类确切的形式和特性。这是通过指定类所包含的实例变量和操作它们的方法来实现的，但大多数实际的类一般都包含这两者。
### 1.2 定义类
&emsp;&emsp;类体分为两种：一部份是变量的声明，另一部分是方法的定义。
#### 1.变量的声明
> 在声明变量的时候可以赋值，但是不可以这样赋值
```
class A {
	int a;
	a=5;
}
```
> 成员变量又分为实例变量和类变量，在声明成员变量时，用关键字static修饰的称作类变量（也被称为静态变量）将在第八节详细讲到

#### 2.方法的定义
实例代码：
```
Class Lader{
	float above;
	float bottom;
	Float height;
	float computer{
	area=(above+bottom)*height/2;
	Return area;
	}
}
```

---
## 2 如何创建对象

要想使用OOP, —定要清楚对象的三个主要特性：
- 对象的行为（behavior)—可以对对象施加哪些操作，或可以对对象施加哪些方法？
- 对象的状态（state )—当施加那些方法时，对象如何响应？
- 对象标识（identity )—如何辨别具有相同行为与状态的不同对象？

有四种显式创建对象的方式：
- 用new语句创建对象，这是最常用的创建对象的方式。
- 运用反射手段，调用java.lang.Class或者java.lang.reflect.Constructor类的newInstance()实例方法
- 调用对象的clone()方法
- 运营反序列化手段，调用java.io.ObjectInputStream对象的readObject()方法，具体见对象的序列化和反序列化

一下重点讲new方法
### 1.对象的声明
```
Lader lader;
```
&emsp;&emsp;**在Java中，对象总是作为引用来储存的，这意味着分配给变量lader的空间只够持有一个Lader对象的地址，**
为了给对象中的变量预留空间，我们需要使用new操作符来分配新对象。

### 2.为创建的对象分配变量

&emsp;&emsp;使用new运算符和类的构造方法为声明的对象分配变量。**储存在lader中的值是一个指向内存中该数据结构的引用，而不是该数据结构自身，在Java中，对象总是作为引用来储存的**
上面两步可以写作这样的一步：
```
Lader lader=new Lader();
```

---
## 3 构造函数

要想使用对象，就必须首先构造对象， 并指定其初始状态。然后，对对象应用方法。在Java程序设计语言中，使用构造器(constructor) 构造新实例。构造器是一种特殊的方法， 用来构造并初始化对象。下面看一个例子。在标准Java库中包含一个Date类。它的对象将描述一个时间点， 例如：“December 31, 1999, 23:59:59 GMT”。

&emsp;&emsp;构造函数在创建对象时初始化对象。它与类同名，并且在语法上与方法相似。然而，构造函数没有显式的返回类型，通常，构造函数用来初始化类定义的实例变量，获知型其他创建完整对象所需要的启动过程。

&emsp;&emsp;归纳可知构造方法必须满足以下语法规则：
- 方法名必须与类名相同
- 不要声明返回类型
- 不能被static final abstract native修饰，构造方法不能被子类继承，所以用final和abstract修饰没有意义。构造方法用于初始化一个新建的对象，所以用static修饰没有意义，Java语言不支持native类型的构造方法

例如：
```
class Myclass(){
    int x;
    Myclass(){
        x=10;
    }
    public int Myclass(){
    }//不是构造方法，有返回值
}

```
> 上面这个构造函数不带参数，但需要注意的是，构造函数还可以带形参

例如：
```
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
```
new Eraployee("]ames Bond", 100000, 1950, 1, 1)
```
将会把实例域设置为：
```
name = "James Bond";
salary = 100000;
hireDay = LocalDate.of(1950, 1, 1); // January 1, 1950
```
构造器与其他的方法有一个重要的不同。构造器总是伴随着new操作符的执行被调用，而不能对一个已经存在的对象调用构造器来达到重新设置实例域的目的。例如，
```
janes.EmployeeCJames Bond", 250000, 1950, 1, 1) // ERROR
```
将产生编译错误。

> 稍后还会更加详细地介绍有关构造器的内容。现在只需要记住：
- 构造器与类同名
- 每个类可以有一个以上的构造器
- 构造器可以有0个、1个或多个参数
- 构造器没有返回值
- 构造器总是伴随着new操作一起调用

### 3.1 重载构造方法
&emsp;&emsp;当通过new语句创建一个对象时，在不同的条件下，对象可能会有不同的初始化行为，例如，对于公司新进来的一个雇员，在开始的时候，有可能他的名字和年龄都是未知的，也有可能仅仅他的名字是已知的，也有可能两者都是已知的。如果姓名是未知的，那么就把姓名改为 无名氏，如果年龄是未知的，就把年龄设为-1

&emsp;&emsp;可通过重载构造函数来表达对象的多种初始化行为，比如下面的例子的构造方法有三种重载形式，在一个类的多个构造方法中，可能会出现一些重复操作。为了提高代码的可重用性，Java语言允许在一个构造方法中，用this语句来调用另一个构造方法。
```
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
&emsp;&emsp;以下程序分别通过3个构造方法创建了3个Employee对象:
```
Employee zhangsan=new Employee("张三",25);
Employee zhang=new Employee("张三");
Employee zh=new Employee();
```
#### 3.1.1 关于无参数的构造器
很多类都包含一个无参数的构造函数，对象由无参数构造函数创建时，其状态会设置为适当的默认值。例如，以下是Employee类的无参数构造函数：
```
public Employee()
{
	name = " "
	salary = 0;
	hireDay = LocalDate.now();
}
```
如果在编写一个类时没有编写构造器， 那么系统就会提供一个无参数构造器。这个构造器将所有的实例域设置为默认值。于是， 实例域中的数值型数据设置为0、布尔型数据设置为false、所有对象变量将设置为null。

如果类中提供了至少一个构造器，但是没有提供无参数的构造器，则在构造对象时如果没有提供参数就会被视为不合法。例如，在程序清单4-2中的Employee类提供了一个简单的构造器：
```
Employee(String name, double salary, int y, int ra, int d)
```
对于这个类，构造默认的雇员属于不合法。也就是，调用
```
e = new Eraployee();
```
将会产生错误。

> 警告：请记住，仅当类没有提供任何构造器的时候，系统才会提供一个默认的构造器如果在编写类的时候，给出了一个构造器，哪怕是很简单的，要想让这个类的用户能够采用下列方式构造实例：
```
new ClassName()
```
就必须提供一个默认的构造器（即不带参数的构造器）。当然，如果希望所有域被赋予默认值，可以采用下列格式：
```
public ClassName()
{
}
```
#### 3.1.2 调用另一个构造器
&emsp;&emsp;用this语句来调用其他构造方法时，必须遵循以下语法规则：

- 假如在一个构造方法中使用了this语句，那么它必须作为构造方法的第一条语句，比如下面的构造方法是错误的
```
public Employee(){
	String name="无名氏";
	this(name);//编译错误，this语句必须作为第一条语句
}
```
- 只能在一个构造方法中使用this语句来调用类的其他构造方法，而不能在实例方法中用this语句来调用类的其他构造方法
- 只能用this语句来调用其他构造方法，而不能通过方法名来直接调用构造方法。以下对构造方法的调用是非法的
```
public Employee(){
	String name="无名氏";
	Employee(name);//编译错误，不能通过方法名来直接调用构造方法
```

### 3.2 默认构造方法(默认域初始化)
如果在构造器中没有显式地给域赋予初值， 那么就会被自动地赋为默认值： 数值为0、布尔值为false、对象引用为null。然而， 只有缺少程序设计经验的人才会这样做。确实，如果不明确地对域进行初始化，就会影响程序代码的可读性。

> 无论是否定义，所有的类都有构造函数，因为java自动提供了一个默认的构造函数将所有成员变量初始化为它们的初始值，即0,null,flase，分别用于数值类型，引用类型和布尔类型。当然，一旦定义自己的构造函数，就不会再使用默认的的构造函数了。

### 3.3 子类调用父类的构造方法
&emsp;&emsp;详见下一节的super关键字

### 3.4 构造方法的作用域
&emsp;&emsp;构造方法只能通过以下方式被调用：
- 当前类其他构造方法通过this语句调用它
- 当前类的子类的构造方法通过super语句来调用它
- 在程序中通过new语句调用它

### 3.5 构造方法的访问级别
&emsp;&emsp;构造方法可以处于public，protected，默认和private这四种访问级别之一，本节着重介绍构造方法处于private级别的意义。当构造方法为private级别时，意味着只能在当前类中访问它；在当前类的其他构造方法中可以通过this语句调用它，此处还可以在当前类的成员方法中通过new语句调用它。

&emsp;&emsp;在以下场合之一，可以把类的所有构造方法都声明为private类型:

#### 1.在这个类中仅仅包含一些供其他程序调用的静态方法，没有任何实例方法。其他程序无需创建该类的实例，就能访问类的静态方法。**例如java.lang.Math类就符合这种情况，在Math类中提供了一系列用于数学运算的公共静态方法，为了防止外部程序创建Math类的实例，Math类的唯一构造方法就是private类型的**
> 在之前的abstract修饰符的时候提到过，abstract类型的类也不允许实例化，也许有这样一个疑问，把Math定义为abstract类，不是也能禁止该类被实例化吗？

&emsp;&emsp;需要注意的是，如果一个类是抽象类，意味着他是专门用于被继承的类，可以拥有子类，而且可以创建具体子类的实例。而JDK不希望用户创建Math类的子类，在这种情况下，把类的构造方法定义为private类型更合适。

#### 2.禁止这个类被继承。当一个类的所有构造方法都是private类型时，假如定义了它的子类，那么子类的构造方法无法调用父类的任何构造方法，因此会导致编译错误，这在final类那提到过，把一个类声明为final类型，也能禁止这个类被继承。这两者的区别是：
- 如果一个类允许其他程序用new语句构造它的实例，但不允许拥有子类，那么就把类声明为final类型
- 如果一个类及不允许其他程序用new语句构造它的实例，但又不允许拥有子类，那就把类的所有构造方法声明为private类型

&emsp;&emsp;由于大多数类都允许其他程序用new语句构造它的实例，因此用final修饰符来禁止类被继承的做法更常见

#### 3.这个类需要把构造自身实例的细节封装起来，不允许其他程序通过new语句创建这个类的实例，这个类向其他程序提供了获得自身实力的静态方法，这种方法称为静态工厂方法。

### 3.6 静态域与静态方法
在前面给出的示例程序中， main方法都被标记为static修饰符。下面讨论一下这个修饰符的含义。
#### 3.6.1 静态域
如果将域定义为static, 每个类中只有一个这样的域。而每一个对象对于所有的实例域却都有自己的一份拷贝。例如，假定需要给每一个雇员賦予唯一的标识码。这里给Employee类添加一个实例域id和一个静态域nextld:
```
class Employee
{
	private static int nextld = 1;
	private int id;
}
```
现在， 每一个雇员对象都有一个自己的id域，但这个类的所有实例将共享一个iiextld域。换句话说，如果有1000个Employee类的对象，则有1000个实例域id。但是，只有一个静态域nextld。即使没有一个雇员对象， 静态域nextld也存在。它属于类，而不属于任何独立的对象。

> 注释：在绝大多数的面向对象程序设计语言中， 静态域被称为类域。术语“static”只是沿用了C++的叫法，并无实际意义。

下面实现一个简单的方法：
```
public void setld()
{
	id = nextld;
	nextld++;
}
```
假定为harry设定雇员标识码：
```
harry.setld();
```
harry 的id域被设置为静态域nextld当前的值，并且静态域nextld的值加1:
```
harry.id = Employee.nextld;
Eip1oyee.nextId++;
```
#### 3.6.2静态常量
静态变量使用得比较少，但静态常量却使用得比较多。例如，在Math类中定义了一个静态常量：
```
public class Hath
{
	public static final double PI = 3.14159265358979323846;
}
```
在程序中，可以采用Math.PI的形式获得这个常量。

如果关键字static被省略，PI就变成了Math类的一个实例域。需要通过Math类的对象访问PI，并且每一个Math对象都有它自己的一份PI拷贝。

另一个多次使用的静态常量是System.out。它在System 类中声明：
```
public class System
{
	public static final PrintStream out = ...;
}
...
```
前面曾经提到过，由于每个类对象都可以对公有域进行修改，所以，最好不要将域设计为public。然而，公有常量（即final域）却没问题。因为out被声明为final,所以，不允许再将其他打印流陚给它：
```
System.out = new PrintStrean(...); // Error out is final
```
> 注释： 如果查看一下System 类，就会发现有一个setOut方法，它可以将System.out设置为不同的流。读者可能会感到奇怪，为什么这个方法可以修改final 变量的值。原因在于，setOut方法是一个本地方法，而不是用Java语言实现的。本地方法可以绕过Java语言的存取控制机制。这是一种特殊的方法，在自己编写程序时，不应该这样处理。

#### 3.6.3 静态方法
静态方法是一种不能向对象实施操作的方法。例如，Math类的pow方法就是一静态方法。表达式
```
Math.pow(x, a)
```
不使用任何Math对象。换句话说，没有隐式的参数。可以认为静态方法是没有this参数的方法（非静态的方法中，this参数表示这个方法的隐式参数)。
Employee类的静态方法不能访问Id实例域，因为它不能操作对象。但是，静态方法可以访问自身类中的静态域。下面是使用这种静态方法的一种示例：
```
public static int getNextldO
{
	return nextld; // returns static field
}
```
可以通过类名调用这个方法：
```
int n = Employee.getNextldO;
```
这个方法可以省略关键字字static? 答案是肯定的。但是，需要通过Employee对象的引用调用这个方法。

> 注释：可以使用对象调用静态方法。例如，如果harry是一个Employee 对象，可以用`harry.getNextId()`代替`Employee.getNextId()`。不过，这种方式很容易造成混淆 其原因是getNextld方法计算的结果与harry毫无关系。我们建议使用类名，而不是对象来调用静态方法。

在下面两种情况下使用静态方法：
- 一方法不需要访问对象状态，其所需参数都是通过显式参数提供（例如：Math.pow）
- 一个方法只需要访问类的静态域（例如：Employee.getNextld）

> C++注释：Java中的静态域与静态方法在功能上与C++相同。但是，语法书写上却稍有所不同。在C++中，使用：：操作符访问自身作用域之外的静态域和静态方法，如`Math::PI`

> 术语“static”有一段不寻常的历史。起初，C引入关键字static是为了表示退出一个块后依然存在的局部变量在这种情况下，术语“static”是有意义的：变量一直存在，当再次进入该块时仍然存在。随后，static在C中有了第二种含义，表示不能被其他文件访问的全局变量和函数。为了避免引入一个新的关键字，关键字static被重用了。最后，C++第三次重用了这个关键字，与前面赋予的含义完全不一样， 这里将其解释为属于类且不属于类对象的变量和函数。这个含义与Java相同。

---
## 4 引用变量和赋值

### 4.1 对象操作自己的变量（改变属性的值）
```
对象.变量
```
### 4.2 对象调用类中的方法（体现对象的功能）
```
对象.方法
```
### 4.3 体现封装

&emsp;&emsp;当对象调用方法时，方法中出现的成员变量就是指分配给该对象的变量。在讲述类的时候我们讲过类中的方法可以操作成员变量。当对象调用方法时，方法中出现的成员变量就是指分配给该对象的变量。
```
class XiyoujiRenwu{
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
&emsp;&emsp;我们知道:类中的方法可以操作成员变量，当对象调用该方法时，方法中出现的成员变量就是指该对象的成员变量。在例5.3中，当对象zhubajie调用过方法speak之后，就将自己的头改成歪着头，后一个对象调用方法后也是这样。

### 4.4 对象的引用和引申

&emsp;&emsp;类是体现封装的一种数据结构，类声明的变量称作对象，对象中负责存放引用，以确保对象可以操作分配给该对象的变量以及调用类中的方法。分配给对象的变量习惯的称作对象的实体。
#### 1.避免使用空对象
&emsp;&emsp;没有实体的对象称作空对象，空对象不能使用。
#### 2.垃圾收集
例如
```
Point p1=new Point(5,15);
Point p2=new Point(8,18);
P1=p2;
```
&emsp;&emsp;这个时候输出p1.x是8而不是5，与C++不同，这里的类有构方法，没有析构方法，JAVA默认有垃圾收集机制。

---
## 5 参数传值
&emsp;&emsp;方法中最重要的部分之一就是方法的参数，参数属于局部变量，当对象调用方法时，参数被分配到内存空间，并要求调用者向参数传递值，即方法被调用时，参数变量必须有具体的值。
### 5.1 传值机制
&emsp;&emsp;Java中，方法所有的参数都是传值的，也就是说，方法中参数变量的值是调用者指定值的复制。

Java程序设计语言总是采用按值调用。也就是说， 方法得到的是所有参数值的一个拷贝，特别是，方法不能修改传递给它的任何参数变量的内容。
例如， 考虑下面的调用：
```
double percent = 10;
harry.raiseSalary(percent);
```
不必理睬这个方法的具体实现，在方法调用之后，percent的值还是10。

下面再仔细地研究一下这种情况。假定一个方法试图将一个参数值增加至3倍：
```
public static void tripieValue(double x) // doesn't work
{
	x = 3 * x;
}
```
然后调用这个方法：
```
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
```
public static void tripieSalary(Employee x) // works
{
	x.raiseSa1ary(200) ;
}
```
当调用
```
harry = new Employee(...);
tri pieSalary(harry);
```
时，具体的执行过程为：
- x被初始化为harry值的拷贝，这里是一个对象的引用。
- raiseSalary方法应用于这个对象引用。x和harry同时引用的那个Employee 对象的薪金提高了200%。
- 方法结束后，参数变量x不再使用。当然，对象变量harry继续引用那个薪金增至3倍的雇员对象。

读者已经看到，实现一个改变对象参数状态的方法并不是一件难事。理由很简单， 方法得到的是对象引用的拷贝，对象引用及其他的拷贝同时引用同一个对象。很多程序设计语言（特别是，C++ 和Pascal）提供了两种参数传递的方式：值调用和引用调用。有些程序员（甚至本书的作者）认为Java程序设计语言对对象采用的是引用调用，实际上，这种理解是不对的。由于这种误解具有一定的普遍性， 所以下面给出一个反例来详细地阐述一下这个问题。

首先， 编写一个交换两个雇员对象的方法：
```
public static void swap(Employee x , Employee y) // doesn't work
	Employee temp = x;
	x = y;
	y = temp;
}
```
如果Java对对象采用的是按引用调用，那么这个方法就应该能够实现交换数据的效果：
```
Employee a = new Employee("Alice", . . .);
Employee b = new Employee("Bob", . . .);
swap(a, b);
// does a now refer to Bob, b to Alice?
```
但是，方法并没有改变存储在变量a和b中的对象引用。swap方法的参数x和y被初始化为两个对象引用的拷贝，这个方法交换的是这两个拷贝。
```
// x refers to Alice, y to Bob
Employee temp = x;
x = y;
y = temp;
// now x refers to Bob, y to Alice
```
最终，白费力气。在方法结束时参数变量x和y被丢弃了。原来的变量a和b仍然引用这个方法调用之前所引用的对象，这个过程说明：Java程序设计语言对对象采用的不是引用调用，实际上，对象引用是按值传递的。

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
```
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
	class Employee // simplified Employee class {
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
```

### 5.2 基本数据类型参数的传值
```
package test1;
import java.util.Scanner;

class Circle{
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

---
## 6 深入介绍new运算符
&emsp;&emsp;new运算符的基本形式如下：
```
class-var=new class-name(arg-list);
```
&emsp;&emsp;这里，class-var是要创建的类类型的变量，class-name是被初始化的类的类名。圆括号包含的实参列表（可以为空）前面的类名指定了类的构造函数。如果类不定义自己的构造函数，那么new将使用java默认的构造函数。因此，new可以创建任何类型的对象。new对象返回对新创建对象的引用。

&emsp;&emsp;内存是有限的，由于内存不足，new可能无法为对象分配内存，如果出现这种情况，就会发生运行时异常。

---

## 7 垃圾收集

> *前面已经记录，这次又一次重申*

### 7.1 避免使用空对象
&emsp;&emsp;没有实体的对象称作空对象，空对象不能使用。
### 7.2 垃圾收集
例如
```
Point p1=new Point(5,15);
Point p2=new Point(8,18);
P1=p2;
```
&emsp;&emsp;这个时候输出p1.x是8而不是5，与C++不同，这里的类有构方法，没有析构方法，JAVA默认有垃圾收集机制。

---
## 8 实例成员与类成员
### 8.1 实例变量和类变量的声明
&emsp;&emsp;类体中包括变量的声明和方法的定义

&emsp;&emsp;成员变量又分为实例变量和类变量，在声明成员变量时，用关键字static修饰的称作类变量（也被称为静态变量）
```
class Dog{
	float x;
	static int y;
}
```
### 8.2 实例变量和类变量的区别
#### 1.不同对象的实例变量互不相同
&emsp;&emsp;**分配给不同的对象的实例变量占有不同的内存空间。**
#### 2.所有对象共享类变量
#### 3.可以通过类名直接访问类变量
- 类的静态变量在内存中只有一个，Java虚拟机在加载类的过程中为静态变量分配内存，静变量位于方法区，被类的所有实例共享。静态变量可以直接通过类名访问。
- 类的每个实例都有相应的实例变量，每当创建一个类的实例，Java虚拟机就会为实例变量分配一次内存，实例变量位于堆区中。实例变量的生命周期取决于实例的生命周期，当创建实力的时候，实例变量被创建并分配内存，当销毁实例的时候，实例变量被销毁并撤销内存。

&emsp;&emsp;改变其中一个对象的类变量就同时改变了其他对象的这个类变量。

### 8.3 实例方法和类方法的定义
&emsp;&emsp;用关键字static修饰的称作类方法

> 见Java基础教程笔记-5-Java语言中的修饰符 4.2 static方法

### 8.4 实例方法和类方法的区别
#### 1.对象调用实例方法
&emsp;&emsp;当类的字节码文件加载到内存时，类的实例方法不会被分配入口地址，只有该类创建对象后，类中的实例方法才分配入口地址。

&emsp;&emsp;需要注意的是，当我们创建第一个对象时，类中的实例方法就分配了入口地址，当再创建对象时，不再分配入口地址，**也就是说，方法的入口地址被所有对象共享，当所有对象都不存在时，方法的入口地址才会被取消。**

#### 2.类名调用类方法
&emsp;&emsp;对于类中的类方法，在该类被加载到内存时，就分配了相应的入口地址，从而类方法不仅可以被类创建的任何对象调用执行，也可以被类名调用执行，**类方法的入口地址直到程序退出才被取消。**  

&emsp;&emsp;**和实例方法不同的是，类方法不可以操作实例变量，这是因为在类创建对象之前，实例成员变量还没有分配内存。**
```
class Village{
	static int treeAmount;
	int peopleNumber;
	String name;
	Village(String s){
		name=s;
	}
	void treePlanting(int n) {
		treeAmount=n+treeAmount;
		System.out.println(name+"植树"+n+"棵");
	}
	void feelTree(int n) {
		if(treeAmount-n>0){
			treeAmount=treeAmount-n;
			System.out.println(name+"伐木"+n+"棵");
		}
		else {
			System.out.println("无树木可伐");
		}
	}
	static int loolTreeAmount() {
		return treeAmount;
	}
	void addPeopleNumber(int n) {
		peopleNumber=n+peopleNumber;
		System.out.println(name+"增加了"+n+"人");
	}
}

public class exercise{
	public static void main(String args[]) {
		Village zhaoZhuang,maJiaZhi;
		zhaoZhuang=new Village("赵庄");
		maJiaZhi=new Village("马家河子");
		zhaoZhuang.peopleNumber=100;
		maJiaZhi.peopleNumber=150;
		Village.treeAmount=200;
		int lefttree=Village.treeAmount;
		System.out.println("森林中有"+lefttree+"棵树");
		zhaoZhuang.treePlanting(50);
		maJiaZhi.treePlanting(100);
		System.out.println("森林中有"+Village.treeAmount+"棵树");
	}
}
```
---
## 9 方法重载与多态
&emsp;&emsp;在java中，同一个类的两个或者多个方法可以共享一个名称，只要它们的形参声明不一样就可以。当这种情况发生时，就称方法被重载了（overloaded)，这一过程称为方法重载。方法重载是java实现多态性的途径之一。

&emsp;&emsp;方法重载的意思是一个类中可以有多个方法具有相同的名字，但这些方法的参数必须不相同，即或者是参数的个数不相同，或者是参数的类型不相同。  

&emsp;&emsp;必须注意以下重要限制：每个被重载的方法的形参类型和数量必须不同，两个方法仅返回类型不同是不够的。当然，被重载的方法的返回类型也可以是不一样的。当调用被重载的方法时，将执行形参与实参相匹配的那个方法。

重载方法必须满足以下条件：
- 方法名相同
- 方法的参数类型，个数，顺序至少有一项不相同
- 方法的返回类型可以不相同
- 方法的修饰符可以不相同

&emsp;&emsp;**方法的返回类型和参数的名字不参与比较，也就是说如果两个方法的名字相同，即使类型不同，也必须保证参数不同。**

> Java中存在两种多态，重载和重写，重写是与继承有关的多态，下一章讨论。    

实例：
```
class Overload{
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
&emsp;&emsp;方法重载支持多态性，因为它是java实现单接口，多方法的途径之一。考虑下面的内容，就会理解其中的原因，在不支持方法重载的语言中，每一种方法必须被赋予惟一的名称。

> Tips 什么是签名？

&emsp;&emsp;在java中，签名指的是方法名及其形参列表，因此，在重载时，一个类的两个方法不能具有相同的签名，注意，签名不包含返回类型，因为java不使用签名进行重载解析。

### 关于重载构造函数（需要注意）
&emsp;&emsp;与方法一样，构造函数也可以被重载，这样就可以用不同的方法来构造对象了。

---
## 10 this关键字(隐式参数与显式参数)

&emsp;&emsp;this是Java中的一个关键字，表示某个对象。  
&emsp;&emsp;**this可以出现在实例方法和构造方法中，但是不可以出现在类方法中。**

方法用于操作对象以及存取它们的实例域。例如，方法：
```
public void raiseSalary(double byPercent)
{
	double raise = salary * byPercent / 100;
	salary += raise;
}
```
将调用这个方法的对象的salary实例域设置为新值。看看下面这个调用：
```
number007. raiseSalary(5) ;
```
它的结果将number007.salary域的值增加5%。具体地说，这个调用将执行下列指令：
```
double raise = nuaber007.salary * 5 / 100;
nuiber007.salary += raise;
```
raiseSalary 方法有两个参数。第一个参数称为隐式(implicit)参数，是出现在方法名前的Employee类对象。第二个参数位于方法名后面括号中的数值，这是一个显式(eplicit)参数（有些人把隐式参数称为方法调用的目标或接收者。）

可以看到，显式参数是明显地列在方法声明中的，例如double byPercent。隐式参数没有出现在方法声明中。在每一个方法中，关键字this表示隐式参数。如果需要的话，可以用下列方式编写raiseSalary方法：
```
public void raiseSalary(double byPercent)
{
	double raise = this.salary * byPercent / 100;
	this.sal ary += raise;
}
```

### 10.1 在构造方法中使用this
```
public class People{
	int leg,hand;
	String name;
	People(String s){
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
### 10.2 在实例方法中使用this
&emsp;&emsp;实例方法必须通过对象来调用，不能通过类名来调用；当this出现在实例方法中，代表正在调用该方法的当前对象。  

&emsp;&emsp;实例方法可以操作类的成员变量，当实例成员变量在实例方法中出现时，默认的格式是：
```
this.成员变量
```
而static成员变量在实例方法中出现时，默认的格式是：
```
类名.成员变量
```
如：
```
clsaa A{
	int a;
	static int y;
	void f(){
	this.x=100;
	A.y=200;
	}
}
```
&emsp;&emsp;当实例成员变量的名字和局部变量的名字相同时，成员变量前面的this.或者类名.就不可省略  

&emsp;&emsp;我们知道类的实例方法能调用类的其他方法，对于实例方法的调用的默认格式是：
```
this.方法
```
&emsp;&emsp;但是对于类方法调用的默认格式是：
```
类名.方法；
```
例如：
```
class B{
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
&emsp;&emsp;在上述B类方法中出现了this，this代表调用方法f的当前对象，所以，方法f的方法体中this.g()就是当前对象调用方法g，也就是说，当某个对象调用方法f的过程中，又调用了方法g。由于这种逻辑关系非常明确，一个实例方法调用另一个方法时可以省略方法名字前面的"this."或"类名."  
例如：
```
class B{
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
&emsp;&emsp;需要注意的是：**this不能出现在类方法中，这是因为，类方法可以通过类名直接调用，这时，可能还没有任何对象诞生。**

## 11 文档注释
JDK包含一个很有用的工具， 叫做javadoc, 它可以由源文件生成一个HTML文档。

如果在源代码中添加以专用的定界符/** 开始的注释， 那么可以很容易地生成一个看上去具有专业水准的文档。这是一种很好的方式，因为这种方式可以将代码与注释保存在一个地方。如果将文档存入一个独立的文件中， 就有可能会随着时间的推移，出现代码和注释不一致的问题。然而，由于文档注释与源代码在同一个文件中，在修改源代码的同时，重新运行javadoc就可以轻而易举地保持两者的一致性。
### 11.1 注释的插入
javadoc 实用程序(utility)从下面几个特性中抽取信息：
- 包
- 公有类与接口
- 公有的和受保护的构造器及方法
 公有的和受保护的域

应该为上面几部分编写注释、注释应该放置在所描述特性的前面。注释以/** 开始，并以*/结束。

每个/** ...\*/文档注释在标记之后紧跟着自由格式文本(free-form text)。标记由@开始，如@author或@param。

自由格式文本的第一句应该是一个概要性的句子。javadoc实用程序自动地将这些句子抽取出来形成概要页。

在自由格式文本中， 可以使用HTML修饰符，例如，用于强调的\<em>...\</eitf>、用于着重强调的\<strong>...\</strong>以及包含图像的\<img...>等。不过，一定不要使用\<hl>或\<hr>, 因为它们会与文档的格式产生冲突。若要键入等宽代码，需使用{@code...} 而不是\<code>...\</code>—这样一来，就不用操心对代码中的<字符转义>了。

### 11.2 类注释
类注释必须放在import语句之后，类定义之前。

下面是一个类注释的例子：
```
/**
* A {©code Card} object represents a playing card , such
* as "Queen of Hearts". A card has a suit (Diamond, Heart ,
* Spade or Club) and a value (1 = Ace, 2 . . . 10, 11 = Jack,
* 12 = Queen , 13 = King)
*/
public class Card
{
...
}
```
> 注释： 没有必要在每一行的开始用星号\*， 例如， 以下注释同样是合法的：
```
/**
A <code>Card< / code> object represents a playing card , such
as "Queen of Hearts". A card has a suit (Diamond， Heart ,
Spade or Club) and a value (1 = Ace, 2 . . . 10, 11 = jack ,
12 = Queen, 13 = King) .
*/
```
然而， 大部分IDE 提供了自动添加星号*, 并且当注释行改变时， 自动重新排列这
些星号的功能。
### 11.3 方法注释
每一个方法注释必须放在所描述的方法之前。除了通用标记之外，还可以使用下面的标记：
- @param 变量描述
这个标记将对当前方法的“param ”（参数）部分添加一个条目。这个描述可以占据多行，并可以使用HTML标记。一个方法的所有@param标记必须放在一起。
- @return 描述
这个标记将对当前方法添加“return”（返回）部分。这个描述可以跨越多行，并可以使用HTML标记。
- ©throws 类描述
这个标记将添加一个注释，用于表示这个方法有可能抛出异常。
### 11.4 域注释
只需要对公有域（通常指的是静态常量）建立文档。例如,
```
/**
* The "Hearts" card suit
*/
public static final int HEARTS = 1;
```
### 11.5 通用注释
下面的标记可以用在类文档的注释中。
- @author 姓名
这个标记将产生一个"author"（作者）条目。可以使用多个@author标记，每个@author标记对应一个作者
- @version
这个标记将产生一个"version"（版本）条目。这里的文本可以是对当前版本的任何描述。
下面的标记可以用于所有的文档注释中。
- @sinee 文本
这个标记将产生一个"since"（始于）条目。这里的text 可以是对引人特性的版本描述。例如，©since version 1.7.10
- @deprecated
这个标记将对类、方法或变量添加一个不再使用的注释。文本中给出了取代的建议。

例如，`@deprecated Use <code> setVIsible(true)</code> instead`通过@see和@link标记，可以使用超级链接，链接到javadoc文档的相关部分或外
部文档。
- @see引用
这个标记将在“see also”部分增加一个超级链接。它可以用于类中，也可以用于方法中。这里的引用可以选择下列情形之一：
```
package, class#feature label
<a href="...n>lable/a>
"test"
```
第一种情况是最常见的。只要提供类、方法或变量的名字，javadoc就在文档中插入一个超链接。例如，
```
@see com.horstraann.corejava.Employee#raiseSalary(double)
```
建立一个链接到com.horstmann.corejava.Employee类的raiseSalary(double)方法的超链接。可以省略包名，甚至把包名和类名都省去，此时，链接将定位于当前包或当前类

需要注意，一定要使用井号（#)，而不要使用句号（.）分隔类名与方法名，或类名与变量名。Java编译器本身可以熟练地断定句点在分隔包、子包、类、内部类与方法和变量时的不同含义。但是javadoc 实用程序就没有这么聪明了，因此必须对它提供帮助。

如果@see 标记后面有一个<字符，就需要指定一个超链接。可以超链接到任何URL。例如：
```
@see <a href="m«w.horstmann .com/corejava.htinl">The Core ]ava home page</a>
```
在上述各种情况下， 都可以指定一个可选的标签(label)作为链接锚(link anchor)如果省略了label,用户看到的锚的名称就是目标代码名或URL。
如果@see 标记后面有一个双引号（"）字符， 文本就会显示在“ see also” 部分。

例如，
```
Isee "Core Java 2 volume 2"
```
可以为一个特性添加多个@see标记，但必须将它们放在一起。
- 如果愿意的话， 还可以在注释中的任何位置放置指向其他类或方法的超级链接， 以及插入一个专用的标记，例如，
```
{@link package, classifeature label}
```
这里的特性描述规则与@see标记规则一样。
### 11.6 包与概述注释
可以直接将类、方法和变量的注释放置在Java源文件中，只要用/** ...\*/ 文档注释界定就可以了。但是，要想产生包注释，就需要在每一个包目录中添加一个单独的文件。可以有如下两个选择：
- 1)提供一个以package.html 命名的HTML 文件。在标记<body>—</body>之间的所有文本都会被抽取出来。
- 2)提供一个以package-info.java 命名的Java 文件。这个文件必须包含一个初始的以/** 和*/ 界定的Javadoc 注释，跟随在一个包语句之后。它不应该包含更多的代码或注释。

还可以为所有的源文件提供一个概述性的注释。这个注释将被放置在一个名为overview,html的文件中，这个文件位于包含所有源文件的父目录中。标记<body>... </body>2间的所有文本将被抽取出来。当用户从导航栏中选择“ Overview” 时，就会显示出这些注释内容。
### 11.7 注释的抽取
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
如果省略了-d docDirectory 选项，那HTML 文件就会被提取到当前目录下。这样有可能会带来混乱，因此不提倡这种做法。

可以使用多种形式的命令行选项对javadoc程序进行调整。例如，可以使用-author和-version选项在文档中包含@author和@version标记（默认情况下，这些标记会被省略)。另一个很有用的选项是-link, 用来为标准类添加超链接。例如，如果使用命令
```
javadoc -link http://docs.oracle.eom/:javase/8/docs/api *.java
```
那么，所有的标准类库类都会自动地链接到Oracle 网站的文档。如果使用-linksource 选项，则每个源文件被转换为HTML (不对代码着色，但包含行编
号)，并且每个类和方法名将转变为指向源代码的超链接。

有关其他的选项， 请查阅javadoc实用程序的联机文档，http://docs.orade.eom/javase/8/docs/guides/javadoc

> 注释：如果需要进一步的定制，例如，生成非HTML 格式的文档， 可以提供自定义的doclet, 以便生成想要的任何输出形式。显然， 这是一种特殊的需求， 有关细节内容请查阅http://docs.oracle.com/javase/8/docs/guides/javadoc/doclet/overview.html 的联机文档。
