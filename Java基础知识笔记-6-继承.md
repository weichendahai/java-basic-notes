Java基础知识笔记-6-继承

# 6 继承
继承是一种由已创建的类创建新类的机制，利用继承，我们先创建一个共有属性的一般类，根据一般类再创建具有特殊属性的新类，新类继承一般类的状态和行为，并根据需要增加他自己新的状态和行为，由继承得到的类称为子类，被继承的称为父类。  

**Java中，一个子类只能继承一个父类，不支持多重继承；**

## 1 继承的基本语法
格式如下：
```java
class Student extends People{
	...
}
```
关键字extends表明正在构造的新类派生于一个已存在的类。已存在的类称为超类(superclass)、基类(base class)或父类(parent class);新类称为子类( subclass)、派生类(derived class)或孩子类(child class)。超类和子类是Java程序员最常用的两个术语，而了解其他语言的程序员可能更加偏爱使用父类和孩子类，这些都是继承时使用的术语。**一个子类继承的成员变量应当是这个类的完全成员。**
### 子类和父类在同一包中的继承性
子类继承父类中protected和public默认访问级别的成员变量和方法作为继承；
### 子类和父类不在同一包中的继承
子类只继承父类中protected和public访问权限的成员变量和方法作为继承；

---
## 3 方法覆盖（重写）Override
如果子类中定义的一个方法，其名称，返回类型及参数签名正好与父类中的某个方法的名称，返回类型及参数签名相匹配，那么可以说，子类的方法覆盖了父类的方法。

在类层次结构中，当子类的方法与父类的方法具有相同的返回类型和签名时，就称子类中的方法重写了父类中的方法，当在子类中调用被重写的方法时，总是引用子类中定义的方法，而父类中定义的方法将被隐藏。

此处需要注意的是：**方法重写只在两个方法的签名一致时才会发生，如果有不一致的地方，那么两个方法就只能是重载而已**

覆盖方法必须满足多种约束，下面分别介绍

#### 1.子类方法的名称，参数签名和返回类型必须与父类方法的名称，参数签名和返回类型一致。
```java
public class Base{
	public void method(){
	}
	public class Sub extends Base{
	public int method(){
		return 0;
	}
}
```
如果子类覆盖了父类的一个方法，然后又定义了一个重载方法，这是合法的。
#### 2.重写父类的方法时，不可以降低方法的访问权限；
例如，如果父类的某个方法是公开的，子类缩小了父类方法的访问权限，这是无效的方法覆盖。将导致编译错误。
#### 3.子类方法不能抛出比父类方法更多的异常
#### 4.方法覆盖只存在于子类和父类之间，在同一个类中方法只能被重载，不能被覆盖。

#### 5.父类的静态方法不能被子类覆盖为非静态方法。
例如:
```java
public Base{
	public static void method() {
	}
}
public class Sub extends Base {
	public void method(){
	}
}
```
#### 6.子类可以定义同父类的静态方法同名的静态方法，以便在子类中隐藏父类的静态方法。在编译时，子类定义的静态方法也必须满足和方法覆盖类似的约束：方法的参数签名一致，返回类型一致，不能缩小父类方法的访问权限，不能抛出更多的异常。

#### 7.父类的非静态方法不能被子类覆盖为静态方法。
参见之前的父类的静态方法不能被子类覆盖为非静态方法
#### 8.父类的私有方法不能被子类覆盖

#### 9.父类的抽象方法可以被子类通过两种途径覆盖：一是子类实现父类的抽象方法; 二是子类重新声明父类的抽象方法。
例如以下代码合法：
```java
public abstract class Base{
	abstract void method1();
	abstract void method2();
}
public abstract class Sub extends Base{
	public void method1() {
	}//实现method1方法，并且扩大访问权限
}
public abstract void method2();//重新申明method2方法，仅仅扩大访问权限，但不实现
```
#### 10.父类的非抽象方法可以被覆盖为抽象方法。
例如：
```java
public class Base{
	void method(){
	}
}
public abstract class Sub extends Base{
	public abstract void method();
}//合法
```
> 重写的几点注意

### 1.重写的语法规则

如果子类可以继承父类的某个实例方法，那么子类就有权利重写这个方法。

方法重写是指：

**子类中定义一个方法，这个方法的类型和父类的方法的类型一致或者是父类的方法的类型的子类型（所谓子类型是指，如果父类的方法的类型是“类”，那么允许子类的重写方法的类型是“子类”）一致，并且这个方法的名字，参数个数，参数的类型和父类的方法完全相同。**

子类如此定义的方法称作子类重写的方法。


### 2.重写的目的

*子类通过方法的重写可以隐藏继承的方法，子类通过方法的重写可以把父类的状态和行为改变为自身的状态和行为。*

*如果父类的方法`f()`可以被子类继承，子类就有权利重写f()，一旦子类重写了父类的方法f()，就隐藏了继承的方法f()，那么子类对象调用方法f()一定是调用的重写方法f()；如果子类没有重写，而是继承了父类的方法f()，那么子类创建的对象当然可以调用f()方法，只不过方法f()产生的行为和父类的相同而已。*

重写方法即可以操作继承的成员变量，调用继承的方法，也可以操作子类新声明的成员变量，调用新定义的其他方法，但无法操作被子类隐藏的方法，如果子类想使用被隐藏的方法或成员变量，必须使用关键字super。
例：
```java
class University{
	void enterRule(double math,double english,double chinese){
		double total=math+english+chinese;
		if(total>=200){
			System.out.println("OK");
		else
			System.out.println("No");
		}
	}
}
class ImportantUniversity extend University{
	void enterRule(double math,double english,double chinese){
		double total=math+english+chinese;
		if(total>=245){
			System.out.println("Ok");
		else
			System.out.println("No");
		}
	}
}
public class exercise{
	public static void main(String args[]){
		double math=64,english=76.5,chinese=66;
		ImportantUniversity univer=new ImportantUniversity();
		univer.enterRule(math,english,chinese);
		math=89;
		english=80;
		chinese=86;
		univer=new ImportantUniversity();
		univer.enterRule(math,english,chinese);
	}
}
```
例：
```java
class A {
	float computer(float x,floaty){
		return x+y;
	}
	public int g(int x,int y){
		return x+y;
	}
}

class B extends A{
	float computer(float x,floaty){
		return x*y;
	}
}
public class exercise{
	public static void main(String args[]){
		B b=new B();
		double result=b.computer(8,9);
		System.out.println("调用重写的方法得到的结果是:"+result);
		int m=b.g(12,8);
		System.out.println("调用继承的方法得到的结果是:"+m);
	}
}
```
但是如果子类重写方法时如下就会出现错误：
```java
double computer(float x,float y) {
	return x*y;
}
```
原因是子类重写的方法和父类的方法类型不一致，这样子类就无法隐藏继承的方法，导致子类出现两个方法的名字相同，参数也相同的情况，这是不允许的（见上一章第七节）

---
## 5 super关键字

## 5.1 用super操作被隐藏的的成员变量和方法

在以下场合会出现方法或者变量被屏蔽的情况：
- 在一个方法内，当局部变量和类的成员变量同名，或者局部变量和父类的成员变量同名时，按照变量的作用域规则，只有局部变量在方法内可见。
- 当子类的某个方法覆盖了父类的一个方法时，在子类的范围内，父类的方法不可见
- 当子类定义了和父类同名的成员变量时，在子类的范围内，父类成员变量不可见

> 子类一旦隐藏了继承的成员变量，那么子类创建的对象就不再拥有该变量，该变量将归关键字super所有，同样子类一旦隐藏了继承的方法，那么子类创建的对象就不能调用被隐藏的方法，该方法的调用由关键字super负责。

因此，如果在子类中想使用被子类隐藏的成员变量或方法就需要使用关键字super。
```java
class Bank {
	int saveMoney;
	int year;
	double interest;
	public double computerInterest() {
		interest=year*0.035*saveMoney;
		System.out.printf("%d元存在银行%d年的利率是%f元\n",saveMoney,year,interest);
		return interest;
		}
}

class ConstructionBank extends Bank{
	double year;
	public double computerInterest() {
		super.year=(int)year;
		double remainNumber=year-(int)year;
		int day=(int)(remainNumber*1000);
		interest=super.computerInterest()+day*0.0001*saveMoney;
		System.out.printf("%d元存在建设银行%d年%d天的利率是",saveMoney,super.year,day,interest);
		return interest;
	}
}

class BankOfDalian extends Bank{
	double year;
	public double computerInterest() {
		super.year=(int)year;
		double remainNumber=year-(int)year;
		int day=(int)(remainNumber*1000);
		interest=super.computerInterest()+day*0.00012*saveMoney;
		System.out.printf("%d元存在大连银行%d年%d天的利率是",saveMoney,super.year,day,interest);
		return interest;
	}
}

public class exercise{
	public static void main(String args[]) {
		int amount=5000;
		ConstructionBank bank1=new ConstructionBank();
		bank1.saveMoney=amount;
		bank1.year=5.216;
		double interest1=bank1.computerInterest();
		BankOfDalian bank2=new BankOfDalian();
		bank2.saveMoney=amount;
		bank2.year=5.216;
		double interest2=bank2.computerInterest();
		System.out.printf("两个银行利息相差%f元", interest1-interest2);
		
	}
}
```
运行结果：
```

```

### 5.2 使用super调用父类的构造方法

首先在子类调用父类的构造方法的时候，必须遵守以下语法规则：

- 在子类的构造方法中，不能直接通过父类方法名调用父类的构造方法，而是要使用super语句
- 假如在子类的构造方法中有super语句，他必须作为构造方法中的第一条语句

用子类的构造方法创建一个子类的对象时，子类的构造方法总是先调用父类的某个构造方法，也就是说，如果子类的构造方法没有明显的指明使用父类的哪个构造方法，子类就调用父类的不带参数的构造方法。  

由于子类不继承父类的构造方法，因此，子类在其构造方法中需使用super来调用父类的构造方法，而且super必须是子类构造方法中的头一条语句，即如果在子类的构造方法中，没有明显写出super关键字来调用父类的某个构造方法，那么默认有：
```
super();
```
例如：
```java
class Student{
	int number;
	String name;
	Student(){
	}
	Student(int number,String name) {
		this.number=number;
		this.name=name;
		System.out.println("我的名字是："+name+"学号是："+number);
	}
}
class UniverStudent extends Student {
	boolean 婚否;
	UniverStudent(int number,String name,boolean b){
		super(number,name);
		婚否=b;
		System.out.println("婚否="+婚否);
	}
}
public class exercise {
	public static void main(String args[]){
		UniverStudent zhang=new UniverStudent(9901,"何晓玲",false);
	}
}

```
如果在类里定义了一个或多个构造方法，那么Java不提供默认的构造方法（不带参数的构造方法），因此，当我们在父类中定义多个构造方法时，应当包括一个不带任何参数的构造方法（如上面代码中的Student类），以防出现省略super时出现错误。

> 注释：回忆一下，关键字this有两个用途：一是引用隐式参数，二是调用该类其他的构造器，同样，super关键字也有两个用途：一是调用超类的方法，二是调用超类的构造器。在调用构造器的时候，这两个关键字的使用方式很相似。调用构造器的语句只能作为另一个构造器的第一条语句出现。构造参数既可以传递给本类(this)的其他构造器， 也可以传递给超类(super)的构造器。


### 5.3 何时调用构造函数
现在有这样一个问题：当创建父类对象，首先执行哪一个函数，是子类的构造函数还是父类定义的构造函数？答案是在类的层次结构中，构造函数的调用顺序是从父类到子类来进行的，**不仅如此，因为super()必须是子类构造函数中执行的第一条语句，所以无论是否使用super()，构造函数的调用顺序都是相同的。如果没有使用super(),就会执行每一个父类的默认（无形参）构造函数**
```java
class A{
	A(){
		System.out.println("Constructing A.");
	}
}
class B extends A{
	B(){
		System.out.println("Constructing B.");
	}
}
class C extends B{
	C(){
		System.out.println("Constructing C.");
	}
}
class OrderOfConstruction{
	public static void main(String args[]){
		C c=new C();
	}
}
```
程序输出如下：
```
Constructing A.
Constructing B.
Constructing C.
```

> 对象的上转型对象

假设，A是B的父类，当用子类创建一个对象，并把这个对象的引用放到父类的对象中时，例如
```
A a;
a=new B();
```
或者：
```
A a;
B b=new B();
a=b;
```
这时，称对象a是对象b的上传型对象

```
graph LR
对象-->新增的变量
对象-->新增的方法
对象-->继承或隐藏的变量
对象-->继承或重写的方法
对象的上传型对象-->继承或隐藏的变量
对象的上传型对象-->继承或重写的方法
```
##### 1.上传型对象不能操作子类新增的成员变量（失去了这部分属性）；不能调用子类新增的方法（失去了一些功能）；
##### 2.上传型对象可以访问子类继承或隐藏的成员变量，也可以调用子类继承的方法或子类重写的实例方法。
上传型对象操作子类继承的方法或子类重写的实例方法，其作用等价于子类对象去调用这些方法。因此，如果子类重写了父类的某个实例方法后，当对象的上传型对象调用这个实例方法时一定调用了子类重写的实例方法。

注意：

1.不要将父类创建的对象和子类对象的上传型对象混淆。 

2.可以将对象的上传型对象在强制转换到一个子类对象，这时，该子类对象又具备了子类所有属性和功能。  

3.不能将父类创建的对象的引用赋值给子类声明的对象  

```java
class leirenyuan{//类人猿
	void crySpeak(String s){
		System.out.println(s);
	}
}
class People extends Leirenyuan{
	void computer(int a,int b){
		int c=a*b;
		System.out.println(c);
	}
	void crySpeak(String s){
		System.out.println("***"+s+"***");
	}
}
public class exercise{
	public static void main(String args[]){
		Leirenyuan monkey=new People();
		monkey.crySpeak("I love this game");
		People people=(People)monkey;
		people.computer(10,10);
	}
}
//***I love this game***
//100
```
这是因为monkey调用的是子类重写的方法crySpeak，需要注意的是:
```
monkey.computer(10,10);
```
是错误的，因为computer方法是子类新增的方法。  

注意：如果子类重写了父类的静态方法，那么子类对象的上传型对象不能调用子类重写的静态方法，只能调用父类的静态方法。

当子类的构造方法没有用super语句显示调用父类的构造方法时，而父类有没有提供默认的构造方法时，就会出现编译错误。

---
## 6 多态
如下面这个例子，显示了饲养员Feeder，食物Feed，动物Animal，以及它的子类的类框图

可以把饲养员 动物和食物看作独立的子系统。Feeder类的定义如下：
```java
public class Feeder{
	public void feed(Animals animal,Food food){
		animal.eat(food);
	}
}
Feeder feeder=new Feeder();
Animal animal=new Dog();
Food food=new Bone();
feeder.feed(animal,food);//给狗喂肉骨肉

animal=new Cat();
food=new Fish();
feeder.feed(animal.food);//给猫喂鱼
```
以上animal变量被定义为Animal类型，但实际上有可能引用Dog或Cat的实例。可见animal变量有多种状态，一会变成猫，一会变成狗，这就是多态的字面含义。

Java语言允许某个实例的引用变量引用子类的实例，而且可以对这个引用变量进行类型转换：
```java
Animal animal=new Dog();
Dog dog=(Dog)animal;//向下转型，把animal类型转换为Dog类型
Creature creature=animal;//向上转型，把animal类型转换为Creature类型
```
如果把引用变量转换为子类类型，称为向下转型，如果把引用变量转换为父类类型，成为向上转型。在进行引用变量的类型转换时，会受到各种限制，而且在通过引用变量访问它所引用的实例的静态属性，静态方法，实例属性，实例方法，以及从父类中继承的方法和属性事，Java虚拟机会采用不同的绑定机制。

---
## 7 继承与多态（重写的方法支持多态性）

当一个类有很多子类时，并且这些子类都重写了父类中的某个方法。那么当我们把子类创建的对象的引用都放到一个父类的对象中，就得到了该对象的一个上传型对象，那么这个上传型对象在调用这个方法时就可能具有多种形态，因为不同的子类在重写父类的方法时可能产生不同的行为。

```java
class Animal{
	void cry(){
	}
}

class Dog extends Animal(){
	void cry(){
		System.out.println("wangwang");
	}
}
class Cat extends Animal(){
	void cry(){
		System.out.println("miaomiao");
	}
}

public class exercise{
	public static void main(String args{]){
		Animal animal;
		animal=new Dog();
		animal.cry();
		animal=new Cat();
		animal.cry();
	}
}
```
多态性就是指父类中的某个方法被其子类重写时，可以各自产生自己的功能行为。

> 面向抽象编程

在设计一个系统时，可以通过abstract类中声明若干个abstract方法，表明这些方法在整个系统设计中的重要性，方法体的内容细节由它的非abstract子类去完成。  

例如:

```java
abstract class Geometry{
	public abstract double getArea();
}
```
Pillar类的设计者可以面向Geometry类编写代码，即Pillar类应当把Geometry对象当作自己的成员，该成员可以调用Geometry的子类重写`getArea()`方法，这样，Pillar类就可以将计算底面积的任务指派给Geometry类的子类的实例；  

以下pillar类的设计不再依赖具体类，而是面向Geometry类，即Pillar类中的bottom对象是抽象类Geometry声明的对象，而不是具体类声明的对象，重新设计的pillar类的代码如下：
```java
class Pillar{//柱体
	Geometry bottom;
	double height;
	Pillar(Geometry bottom,double height){
		this.bottom=bottom;
		this.height=height;
	}
	public double getVolume() {
		return bottom.getArea()*height;
	}
}

class Circle extends Geometry{//圆底柱体
	double r;
	Circle(double r){
		this.r=r;
	}
	public double getArea() {
		return(3.14*r*r);
	}
}

class Rectangle extends Geometry{//矩形底柱体
	double a,b;
	Rectangle(double a,double b){
		this.a=a;
		this.b=b;
	}
	public double getArea() {
		return a*b;
	}
}

public class exercise{
	public static void main(String args[]) {
		Pillar pillar;
		Geometry bottom;
		bottom=new Rectangle(12,22);
		pillar=new Pillar(bottom,58);
		System.out.println("矩形底柱体的体积是："+pillar.getVolume());
		bottom=new Circle(10);
		pillar=new Pillar(bottom,58);
		System.out.println("圆底柱体的体积是："+pillar.getVolume());
	}
}
```
关于抽象类和final参见笔记Java基础知识笔记-5-Java语言中的修饰符

> 继承的利弊和使用原则

- 继承树的层次不可太多
- 继承树的上层为抽象层
- 继承关系最大的弱点：打破封装
- 精心设计专门用于被继承的类
- 区分对象的属性与继承

## 8 Object类

Object类是Java中所有类的始祖，在Java中每个类都是由它扩展而来的。但是并不需要这样写：

```java
public class Employee extends Object;
```
如果没有明确地指出超类，Object就被认为是这个类的超类。由于在Java中，每个类都是由Object类扩展而来的，所以，熟悉这个类提供的所有服务十分重要。本章将介绍一些基本的内容，没有提到的部分请参看后面的章节或在线文档（在Object中有几个只在处理线程才会被调用的方法，有关线程内容请参见第14章)。

可以使用Object类型的变量引用任何类型的对象：
```java
Object obj = new Employee("Harry Hacker", 35000);
```
当然，Object类型的变量只能用于作为各种值的通用持有者。要想对其中的内容进行具体的操作，还需要清楚对象的原始类型，并进行相应的类型转换：
```java
Employee e = (Employee) obj;
```
**在Java中，只有基本类型(primitive types)不是对象，例如，数值、字符和布尔类型的值都不是对象。**

所有的数组类塱，不管是对象数组还是基本类型的数组都扩展了Object类。
```java
Employee[] staff = new Employee[10];
obj = staff; // OK
obj = new int[10]; // OK
```

## 9 泛型数组列表

在许多程序设计语言中，特别是在C++语言中，必须在编译时就确定整个数组的大小。程序员对此十分反感，因为这样做将迫使程序员做出一些不情愿的折中。例如，在一个部门中有多少雇员？ 肯定不会超过100人。一旦出现一个拥有150名雇员的大型部门呢？ 愿意为那些仅有10名雇员的部门浪费90名雇员占据的存储空间吗？ 

在Java中，情况就好多了。它允许在运行时确定数组的大小。 

```java
int actualSize =...;
Employee[] staff = new Employee[actualSize];
```
当然，这段代码并没有完全解决运行时动态更改数组的问题。一旦确定了数组的大小，改变它就不太容易了。在Java中， 解决这个问题最简单的方法是使用Java中另外一个被称为ArrayList的类。它使用起来有点像数组，但在添加或删除元素时， 具有自动调节数组容量的功能，而不需要为此编写任何代码。

ArrayList是一个采用类型参数（type parameter) 的泛型类（generic class)。为了指定数组列表保存的元素对象类型，需要用一对尖括号将类名括起来加在后面，例如，`ArrayList <Employee>`。在第8章中将可以看到如何自定义一个泛型类， 这里并不需要了解任何技术细节就可以使ArrayList类型。 

下面声明和构造一个保存Employee对象的数组列表：

```java
ArrayList<Employee> staff = new ArrayList<Employee>();
```

两边都使用类型参数Employee，这有些繁琐。Java SE 7中， 可以省去右边的类型参数：

```
ArrayList<Employee> staff = new ArrayList();
```

这被称为“菱形”语法，因为空尖括号<>就像是一个菱形。可以结合new操作符使用菱形语法。编译器会检查新值是什么。如果赋值给一个变量，或传递到某个方法，或者从某个方法返回，编译器会检査这个变量、 参数或方法的泛型类型，然后将这个类型放在<>中。在这个例子中，`new ArrayList<>()`将赋至一个类型为`ArrayList<Employee>`的变量，所以泛型类型为Employee。

> 注释：Java SE5.0以前的版本没有提供泛型类，而是有一个ArrayList类，其中保存类型为Object的元素，它是“自适应大小”的集合。如果一定要使用老版本的Java, 则需要将所有的后缀 <...> 删掉，在Java SE 5.0以后的版本中，没有后缀<...>仍然可以使用 ArrayList, 它将被认为是一个删去了类型参數的“原始”类型

> 注释：在Java的老版本中，程序员使用Vector类实现动态数组。不过，ArrayList类更加有效，没有任何理由一定要使用Vector类

使用add方法可以将元素添加到数组列表中。例如，下面展示了如何将雇员对象添加到数组列表中的方法：

```java
staff.add(new Employee("Harry Hacker",...));
staff.add(new Eraployee("Tony Tester",...));
```

数组列表管理着对象引用的一个内部数组。最终， 数组的全部空间有可能被用尽。这就显现出数组列表的操作魅力： 如果调用add且内部数组已经满了，数组列表就将自动地创建一个更大的数组，并将所有的对象从较小的数组中拷贝到较大的数组中。

如果已经清楚或能够估计出数组可能存储的元素数量，就可以在填充数组之前调用ensureCapacity方法： `staff.ensureCapacity(100);` 这个方法调用将分配一个包含100个对象的内部数组。然后调用100次add, 而不用重新分配空间。

另外，还可以把初始容量传递给ArrayList构造器：

```
ArrayList<Employee> staff = new ArrayListo(100);
```

> 警告：分配数组列表，如下所示：
>
> ```java
> new ArrayListo(100) // capacity is 100
> ```
>
> 它与为新数组分配空间有所不同：
>
> ```java
> new Employee[100] // size is 100
> ```
>
> 数组列表的容量与数组的大小有一个非常重要的区别。如果为数组分配100个元素的存储空间，数组就有100个空位置可以使用。而容量为100个元素的数组列表只是拥有保存100个元素的潜力（实际上，重新分配空间的话，将会超过100), 但是在最初，甚至完成初始化构造之后，数组列表根本就不含有任何元素。size方法将返回数组列表中包含的实际元素数目。例如，
>
> ```java
> staff.size()
> ```

将返回staff数组列表的当前元素数量，它等价于数组a的a.length。

一旦能够确认数组列表的大小不再发生变化，就可以调用trimToSize方法。这个方法将存储区域的大小调整为当前元素数量所需要的存储空间数目。垃圾回收器将回收多余的存储空间。

一旦整理了数组列表的大小，添加新元素就需要花时间再次移动存储块，所以应该在确认不会添加任何元素时，再调用trimToSize。 

> C++注释：ArrayList类似于C++的vector模板。ArrayList与vector都是泛型类型。但是C++的vector模板为了便于访问元素重载了[ ]运算符。由于Java没有运算符重栽，所以必须调用显式的方法。此外，C++向量是值拷贝。如果a和b是两个向量，賦值操作a = b将会构造一个与b长度相同的新向量a, 并将所有的元素由b拷贝到a, 而在Java中，这条赋值语句的操作结果是让a和b引用同一个数组列表。

> API java.util.ArrayList<E> 1.2
>
> ```java
> ArrayList<E>( ) //构造一个空数组列表。
> ArrayList<E>(int initialCapacity) //用指定容量构造一个空数组列表。
> //参数：initalCapacity 数组列表的最初容量
>     
> boolean add(E obj) //在数组列表的尾端添加一个元素。永远返回true。
> //参数：obj 添加的元素
>     
> int size() //返回存储在数组列表中的当前元素数量。(这个值将小于或等于数组列表的容量。)
> void ensureCapacity(int capacity) //确保数组列表在不重新分配存储空间的情况下就能够保存给定数量的元素。
> //参数：capacity 需要的存储容量
>     
> void trimToSize() //将数组列表的存储容量削减到当前尺寸。
> ```

### 9.1 访问数组列表元素

很遗憾， 天下没有免费的午餐。 数组列表自动扩展容量的便利增加了访问元素语法的复杂程度。 其原因是ArrayList类并不是Java程序设计语言的一部分；它只是一个由某些人编写且被放在标准库中的一个实用类。

使用get和set方法实现访问或改变数组元素的操作，而不使用人们喜爱的[ ]语法格式。

例如，要设置第i个元素，可以使用：

```java
staff.set(i, harry);
```

它等价于对数组a的元素赋值（数组的下标从 0开始)： 

```java
a[i] = harry;
```

> 警告：只有i小于或等于数组列表的大小时， 才能够调用`list.set(i,x)`。例如，下面这段代码是错误的：
>
> ```java
> ArrayList<Employee> list = new ArrayListo(100); // capacity 100，size 0 list.set(0, x); // no element 0 yet
> ```
>
>  使用add方法为数组添加新元素， 而不要使用set方法， 它只能替换数组中已经存在的元素内容。 

使用下列格式获得数组列表的元素: 

```java
Employee e = staff.get(i);
```

等价于：

```java
Employee e = a[i];
```

> 注释：没有泛型类时，原始的ArrayList类提供的get方法别无选择只能返回Object, 因此，get方法的调用者必须对返回值进行类型转换：
>
> ```java
> Employee e = (Employee)staff.get(i);
> ```
>
>  原始的ArrayList存在一定的危险性。它的add和set方法允许接受任意类型的对象。对于下面这个调用
>
> ```java
> staff.set(i, "Harry Hacker");
> ```
>
> 编译不会给出任何警告， 只有在检索对象并试图对它进行类型转换时，才会发现有问题。如果使用`ArrayList<Employee>`, 编译器就会检测到这个错误。

下面这个技巧可以一举两得，既可以灵活地扩展数组，又可以方便地访问数组元素。首先，创建一个数组，并添加所有的元素。

```java
ArrayList<X> list = new ArrayList();
while (...) {
	x = ...;
	list.add(x);
}
```

执行完上述操作后，使用toArray方法将数组元素拷贝到一个数组中。

```java
X[] a = new X[list.size()];
list.toArray(a);
```

除了在数组列表的尾部追加元素之外，还可以在数组列表的中间插入元素，使用带索引参数的add方法。

```java
int n = staff.size()/2;
staff.add(n, e);
```

为了插入一个新元素，位于 n之后的所有元素都要向后移动一个位置。如果插入新元素后，数组列表的大小超过了容量， 数组列表就会被重新分配存储空间。 同样地，可以从数组列表中间删除一个元素。

```java
Employee e = staff.remove(n);
```

位于这个位置之后的所有元素都向前移动一个位置，并且数组的大小减1

对数组实施插入和删除元素的操作其效率比较低。对于小型数组来说，这一点不必担心。但如果数组存储的元素数比较多，又经常需要在中间位置插入、删除元素， 就应该考虑使用链表了。有关链表操作的实现方式将在第9章中讲述。

可以使用“foreach”循环遍历数组列表： 

```java
for (Employee e : staff)
	dosomethingwith e
```

这个循环和下列代码具有相同的效果 

```java
for (int i = 0; i < staff.size(); i++) {
	Employee e = staff.get(i);
	dosomething with e
}
```

程序清单5-11是对第4章中EmployeeTest做出修改后的程序。在这里，将Employee[ ]数组替换成了`ArrayList<Employee>`。请注意下面的变化：

- 不必指出数组的大小。
- 使用add将任意多的元素添加到数组中。
- 使用size()替代length计算元素的数目。
- 使用a.get(i)替代a[i]访问元素。

> 程序清单 5-11 arrayList/ArrayListTestjava
>
> ```java
> package arrayList;
> import java.util.*;
> /**
> * This program demonstrates the ArrayList class.
> * ©version 1.11 2012-01-26
> * ©author Cay Horstmann
> */
> public class ArrayListTest {
> 	public static void main(String[] args) {
> 		// fill the staff array list with three Employee objects
> 		ArrayList<Employee> staff = new ArrayList<>();
> 		staff.add(new Employee("Carl Cracker", 75000, 1987, 12, 15));
> 		staff.add(new Employee("Harry Hacker", 50000, 1989, 10, 1));
> 		staff.add(new Employee("Tony Tester", 40000, 1990, 3, 15));
> 		// raise everyone's salary by 5%
> 		for (Employee e : staff)
> 			e.raiseSalary(5);
> 		// print out information about all Employee objects
> 		for (Employee e : staff)
> 			System,out.println("name: " + e.getName() + ".salary: " + e.getSalary() + ",hi reDay=" + e.getHireDay());
> 	}
> }
> ```
>
> API|java.util.ArrayList<T> 1.2
>
> ```java
> void set(int index,E obj) //设置数组列表指定位置的元素值， 这个操作将覆盖这个位置的原有内容。
> 参数： index 位置（必须介于 0 ~ size()-l 之间）
> 	obj 新的值
> 	
> E get(int index) //获得指定位置的元素值。
> 参数： index 获得的元素位置（必须介于 0 ~ size()-l 之间）
> 
> void add(int index,E obj) //向后移动元素，以便插入元素。
> 参数： index 插入位置（必须介于 0 〜 size()-l 之间）
> 	obj 新兀素
> 
> E remove(int index) //删除一个元素，并将后面的元素向前移动。被删除的元素由返回值返回。
> 参数：index 被删除的元素位置（必须介于 0 〜 size()-l 之间）
> ```

### 9.2 类型化与原始数组列表的兼容性
在你自己的代码中，你可能更愿意使用类型参数来增加安全性。这一节中，你会了解如何与没有使用类型参数的遗留代码交互操作。

假设有下面这个遗留下来的类：

```java
public class EmployeeDB {
	public void update(ArrayList list) {
		...
	}
	public ArrayList find(String query) {
		...
	}
}
```

可以将一个类型化的数组列表传递给update方法，而并不需要进行任何类型转换。

```java
ArrayList<Employee>staff = ...;
employeeDB.update(staff);
```

也可以将staff对象传递给update方法。

> 警告：尽管编译器没有给出任何错误信息或警告，但是这样调用并不太安全。在update方法中， 添加到数组列表中的元素可能不是Employee类型。在对这些元素进行检索时就会出现异常。听起来似乎很吓人，但思考一下就会发现，这与在Java中增加泛型之前是 一样的，虚拟机的完整性绝对没有受到威胁。在这种情形下，既没有降低安全性，也没有受益于编译时的检查。 

相反地，将一个原始ArrayList赋给一个类型化ArrayList会得到一个警告。 

```java
ArrayList<Employee> result = employeeDB.find(query); // yields warning 0
```

> 注释：为了能够看到警告性错误的文字信息，要将编译选项置为`-Xlint:unchecked`。

使用类型转换并不能避免出现警告。

```java
ArrayList<Employee> result = (ArrayList<Employee>) employeeDB.find(query); // yields another warning
```

这样，将会得到另外一个警告信息，指出类型转换有误。

这就是Java中不尽如人意的参数化类型的限制所带来的结果。鉴于兼容性的考虑，编译器在对类型转换进行检査之后，如果没有发现违反规则的现象，就将所有的类型化数组列表转换成原始ArrayList对象。在程序运行时，所有的数组列表都是一样的，即没有虚拟机中的类型参数。因此，类型转换（ArrayList)和(`ArrayList<Employee>`) 将执行相同的运行时检查。

在这种情形下，不必做什么，只要在与遗留的代码进行交叉操作时，研究一下编泽器的警告性提示，并确保这些警告不会造成太严重的后果就行了。

一旦能确保不会造成严重的后果，可以用`@SuppressWamings("unchecked")`标注来标记这个变量能够接受类型转换，如下所示：

```java
@SuppressWarnings("unchecked") ArrayList<Employee> result = (ArrayList<Employee>) employeeDB.find(query); // yields another warning
```