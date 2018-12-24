Java基础知识笔记-6-继承

# 6 继承
&emsp;&emsp;继承是一种由已创建的类创建新类的机制，利用继承，我们先创建一个共有属性的一般类，根据一般类再创建具有特殊属性的新类，新类继承一般类的状态和行为，并根据需要增加他自己新的状态和行为，由继承得到的类称为子类，被继承的称为父类。  
**Java中，一个子类只能继承一个父类，不支持多重继承；**
## 1 继承的基本语法
格式如下：
```
class Student extends People{
…
}
```
**一个子类继承的成员变量应当是这个类的完全成员。**
### 子类和父类在同一包中的继承性
&emsp;&emsp;子类继承父类中protected和public默认访问级别的成员变量和方法作为继承；
### 子类和父类不在同一包中的继承
&emsp;&emsp;子类只继承父类中protected和public访问权限的成员变量和方法作为继承；

---
## 3 方法覆盖（重写）Override
&emsp;&emsp;如果子类中定义的一个方法，其名称，返回类型及参数签名正好与父类中的某个方法的名称，返回类型及参数签名相匹配，那么可以说，子类的方法覆盖了父类的方法。

&emsp;&emsp;在类层次结构中，当子类的方法与父类的方法具有相同的返回类型和签名时，就称子类中的方法重写了父类中的方法，当在子类中调用被重写的方法时，总是引用子类中定义的方法，而父类中定义的方法将被隐藏。

&emsp;&emsp;此处需要注意的是：**方法重写只在两个方法的签名一致时才会发生，如果有不一致的地方，那么两个方法就只能是重载而已**

&emsp;&emsp;覆盖方法必须满足多种约束，下面分别介绍

#### 1.子类方法的名称，参数签名和返回类型必须与父类方法的名称，参数签名和返回类型一致。
```
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
&emsp;&emsp;例如，如果父类的某个方法是公开的，子类缩小了父类方法的访问权限，这是无效的方法覆盖。将导致编译错误。
#### 3.子类方法不能抛出比父类方法更多的异常
#### 4.方法覆盖只存在于子类和父类之间，在同一个类中方法只能被重载，不能被覆盖。

#### 5.父类的静态方法不能被子类覆盖为非静态方法。
例如:
```
public Base{
	public static void method(){
	}
}
public class Sub extends Base{
	public void method(){
	}
}
```
#### 6.子类可以定义同父类的静态方法同名的静态方法，以便在子类中隐藏父类的静态方法。在编译时，子类定义的静态方法也必须满足和方法覆盖类似的约束：方法的参数签名一致，返回类型一致，不能缩小父类方法的访问权限，不能抛出更多的异常。

#### 7.父类的非静态方法不能被子类覆盖为静态方法。
&emsp;&emsp;参见之前的父类的静态方法不能被子类覆盖为非静态方法
#### 8.父类的私有方法不能被子类覆盖

#### 9.父类的抽象方法可以被子类通过两种途径覆盖：一是子类实现父类的抽象方法; 二是子类重新声明父类的抽象方法。
例如以下代码合法：
```
public abstract class Base{
	abstract void method1();
	abstract void method2();
}
public abstract class Sub extends Base{
	public void method1(){
	}//实现method1方法，并且扩大访问权限
	public abstract void method2();//重新申明method2方法，仅仅扩大访问权限，但不实现
```
#### 10.父类的非抽象方法可以被覆盖为抽象方法。
例如：
```
public class Base{
	void method(){
	}
public abstract class Sub extends Base{
	public abstract void method();
}//合法
```
> 重写的几点注意

### 1.重写的语法规则

&emsp;&emsp;如果子类可以继承父类的某个实例方法，那么子类就有权利重写这个方法。  
&emsp;&emsp;方法重写是指：
**子类中定义一个方法，这个方法的类型和父类的方法的类型一致或者是父类的方法的类型的子类型（所谓子类型是指，如果父类的方法的类型是“类”，那么允许子类的重写方法的类型是“子类”）一致，并且这个方法的名字，参数个数，参数的类型和父类的方法完全相同。**
子类如此定义的方法称作子类重写的方法。


### 2.重写的目的

&emsp;&emsp; **子类通过方法的重写可以隐藏继承的方法，**子类通过方法的重写可以把父类的状态和行为改变为自身的状态和行为。  

&emsp;&emsp;*如果父类的方法`f()`可以被子类继承，子类就有权利重写f()，一旦子类重写了父类的方法f()，就隐藏了继承的方法f()，那么子类对象调用方法f()一定是调用的重写方法f()；如果子类没有重写，而是继承了父类的方法f()，那么子类创建的对象当然可以调用f()方法，只不过方法f()产生的行为和父类的相同而已。*  

&emsp;&emsp;重写方法即可以操作继承的成员变量，调用继承的方法，也可以操作子类新声明的成员变量，调用新定义的其他方法，但无法操作被子类隐藏的方法，如果子类想使用被隐藏的方法或成员变量，必须使用关键字super。
例：
```
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
```
class A{
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
&emsp;&emsp;但是如果子类重写方法时如下就会出现错误：
```
double computer(float x,float y){
	return x*y;
}
```
&emsp;&emsp;原因是子类重写的方法和父类的方法类型不一致，这样子类就无法隐藏继承的方法，导致子类出现两个方法的名字相同，参数也相同的情况，这是不允许的（见上一章第七节）

---
## 5 super关键字

## 5.1用super操作被隐藏的的成员变量和方法

&emsp;&emsp;在以下场合会出现方法或者变量被屏蔽的情况：
- 在一个方法内，当局部变量和类的成员变量同名，或者局部变量和父类的成员变量同名时，按照变量的作用域规则，只有局部变量在方法内可见。
- 当子类的某个方法覆盖了父类的一个方法时，在子类的范围内，父类的方法不可见
- 当子类定义了和父类同名的成员变量时，在子类的范围内，父类成员变量不可见

> 子类一旦隐藏了继承的成员变量，那么子类创建的对象就不再拥有该变量，该变量将归关键字super所有，同样子类一旦隐藏了继承的方法，那么子类创建的对象就不能调用被隐藏的方法，该方法的调用由关键字super负责。

&emsp;&emsp;因此，如果在子类中想使用被子类隐藏的成员变量或方法就需要使用关键字super。
```
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

&emsp;&emsp;首先在子类调用父类的构造方法的时候，必须遵守以下语法规则：

- 在子类的构造方法中，不能直接通过父类方法名调用父类的构造方法，而是要使用super语句
- 假如在子类的构造方法中有super语句，他必须作为构造方法中的第一条语句

&emsp;&emsp;用子类的构造方法创建一个子类的对象时，子类的构造方法总是先调用父类的某个构造方法，也就是说，如果子类的构造方法没有明显的指明使用父类的哪个构造方法，子类就调用父类的不带参数的构造方法。  

&emsp;&emsp;由于子类不继承父类的构造方法，因此，子类在其构造方法中需使用super来调用父类的构造方法，而且super必须是子类构造方法中的头一条语句，即如果在子类的构造方法中，没有明显写出super关键字来调用父类的某个构造方法，那么默认有：
```
super();
```
例如：
```
class Student{
	int number;String name;
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
&emsp;&emsp;如果在类里定义了一个或多个构造方法，那么Java不提供默认的构造方法（不带参数的构造方法），因此，当我们在父类中定义多个构造方法时，应当包括一个不带任何参数的构造方法（如上面代码中的Student类），以防出现省略super时出现错误。

### 5.3 何时调用构造函数
&emsp;&emsp;现在有这样一个问题：当创建父类对象，首先执行哪一个函数，是子类的构造函数还是父类定义的构造函数？答案是在类的层次结构中，构造函数的调用顺序是从父类到子类来进行的，**不仅如此，因为super()必须是子类构造函数中执行的第一条语句，所以无论是否使用super()，构造函数的调用顺序都是相同的。如果没有使用super(),就会执行每一个父类的默认（无形参）构造函数**
```
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

&emsp;&emsp;假设，A是B的父类，当用子类创建一个对象，并把这个对象的引用放到父类的对象中时，例如
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
&emsp;&emsp;这时，称对象a是对象b的上传型对象

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
&emsp;&emsp;上传型对象操作子类继承的方法或子类重写的实例方法，其作用等价于子类对象去调用这些方法。因此，如果子类重写了父类的某个实例方法后，当对象的上传型对象调用这个实例方法时一定调用了子类重写的实例方法。

注意：
1.不要将父类创建的对象和子类对象的上传型对象混淆。  
2.可以将对象的上传型对象在强制转换到一个子类对象，这时，该子类对象又具备了子类所有属性和功能。  
3.不能将父类创建的对象的引用赋值给子类声明的对象  
```
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
&emsp;&emsp;这是因为monkey调用的是子类重写的方法crySpeak，需要注意的是:
```
monkey.computer(10,10);
```
&emsp;&emsp;是错误的，因为computer方法是子类新增的方法。  

&emsp;&emsp;注意：如果子类重写了父类的静态方法，那么子类对象的上传型对象不能调用子类重写的静态方法，只能调用父类的静态方法。

---
## 6 多态
&emsp;&emsp;如下面这个例子，显示了饲养员Feeder，食物Feed，动物Animal，以及它的子类的类框图

可以把饲养员 动物和食物看作独立的子系统。Feeder类的定义如下：
```
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
&emsp;&emsp;以上animal变量被定义为Animal类型，但实际上有可能引用Dog或Cat的实例。可见animal变量有多种状态，一会变成猫，一会变成狗，这就是多态的字面含义。

Java语言允许某个实例的引用变量引用子类的实例，而且可以对这个引用变量进行类型转换：
```
Animal animal=new Dog();
Dog dog=(Dog)animal;//向下转型，把animal类型转换为Dog类型
Creature creature=animal;//向上转型，把animal类型转换为Creature类型
```
&emsp;&emsp;如果把引用变量转换为子类类型，称为向下转型，如果把引用变量转换为父类类型，成为向上转型。在进行引用变量的类型转换时，会受到各种限制，而且在通过引用变量访问它所引用的实例的静态属性，静态方法，实例属性，实例方法，以及从父类中继承的方法和属性事，Java虚拟机会采用不同的绑定机制。

---
## 7 继承与多态（重写的方法支持多态性）

&emsp;&emsp;当一个类有很多子类时，并且这些子类都重写了父类中的某个方法。那么当我们把子类创建的对象的引用都放到一个父类的对象中，就得到了该对象的一个上传型对象，那么这个上传型对象在调用这个方法时就可能具有多种形态，因为不同的子类在重写父类的方法时可能产生不同的行为。
```
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
&emsp;&emsp;多态性就是指父类中的某个方法被其子类重写时，可以各自产生自己的功能行为。

> 面向抽象编程

&emsp;&emsp;在设计一个系统时，可以通过abstract类中声明若干个abstract方法，表明这些方法在整个系统设计中的重要性，方法体的内容细节由它的非abstract子类去完成。  
例如:
```
abstract class Geometry{
	public abstract double getArea();
}
```
&emsp;&emsp;Pillar类的设计者可以面向Geometry类编写代码，即Pillar类应当把Geometry对象当作自己的成员，该成员可以调用Geometry的子类重写`getArea()`方法，这样，Pillar类就可以将计算底面积的任务指派给Geometry类的子类的实例；  

&emsp;&emsp;以下pillar类的设计不再依赖具体类，而是面向Geometry类，即Pillar类中的bottom对象是抽象类Geometry声明的对象，而不是具体类声明的对象，重新设计的pillar类的代码如下：
```
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
&emsp;&emsp;关于抽象类和final参见笔记 Java基础知识笔记-5-Java语言中的修饰符

> 继承的利弊和使用原则

- 继承树的层次不可太多
- 继承树的上层为抽象层
- 继承关系最大的弱点：打破封装
- 精心设计专门用于被继承的类
- 区分对象的属性与继承
