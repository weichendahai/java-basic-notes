Java基础知识笔记-5-继承

# 5 继承
继承是一种由已创建的类创建新类的机制，利用继承，我们先创建一个共有属性的一般类，根据一般类再创建具有特殊属性的新类，新类继承一般类的状态和行为，并根据需要增加他自己新的状态和行为，由继承得到的类称为子类，被继承的称为父类。  

**Java中，一个子类只能继承一个父类，不支持多重继承；**

## 1 类，超类和子类
### 1.1 定义子类
格式如下：
```java
class Student extends People{
	...
}
```
关键字extends表明正在构造的新类派生于一个已存在的类。已存在的类称为超类(superclass)、基类(base class)或父类(parent class);新类称为子类( subclass)、派生类(derived class)或孩子类(child class)。超类和子类是Java程序员最常用的两个术语，而了解其他语言的程序员可能更加偏爱使用父类和孩子类，这些都是继承时使用的术语。**一个子类继承的成员变量应当是这个类的完全成员。**

**子类和父类在同一包中的继承性**

子类继承父类中protected和public默认访问级别的成员变量和方法作为继承；

**子类和父类不在同一包中的继承**

子类只继承父类中protected和public访问权限的成员变量和方法作为继承；

### 1.2 覆盖方法（重写）Override
如果子类中定义的一个方法，其名称，返回类型及参数签名正好与父类中的某个方法的名称，返回类型及参数签名相匹配，那么可以说，子类的方法覆盖了父类的方法。

在类层次结构中，当子类的方法与父类的方法具有相同的返回类型和签名时，就称子类中的方法重写了父类中的方法，当在子类中调用被重写的方法时，总是引用子类中定义的方法，而父类中定义的方法将被隐藏。

此处需要注意的是：**方法重写只在两个方法的签名一致时才会发生，如果有不一致的地方，那么两个方法就只能是重载而已**

覆盖方法必须满足多种约束，下面分别介绍

1. 子类方法的名称，参数签名和返回类型必须与父类方法的名称，参数签名和返回类型一致。
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

2. 重写父类的方法时，不可以降低方法的访问权限；

例如，如果父类的某个方法是公开的，子类缩小了父类方法的访问权限，这是无效的方法覆盖。将导致编译错误。

3. 子类方法不能抛出比父类方法更多的异常

4. 方法覆盖只存在于子类和父类之间，在同一个类中方法只能被重载，不能被覆盖。

5. 父类的静态方法不能被子类覆盖为非静态方法。
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
6. 子类可以定义同父类的静态方法同名的静态方法，以便在子类中隐藏父类的静态方法。在编译时，子类定义的静态方法也必须满足和方法覆盖类似的约束：方法的参数签名一致，返回类型一致，不能缩小父类方法的访问权限，不能抛出更多的异常。

7. 父类的非静态方法不能被子类覆盖为静态方法。
参见之前的父类的静态方法不能被子类覆盖为非静态方法
8. 父类的私有方法不能被子类覆盖

9. 父类的抽象方法可以被子类通过两种途径覆盖：一是子类实现父类的抽象方法; 二是子类重新声明父类的抽象方法。
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
10. 父类的非抽象方法可以被覆盖为抽象方法。
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

#### 1.2.1 重写的语法规则

如果子类可以继承父类的某个实例方法，那么子类就有权利重写这个方法。

方法重写是指：

**子类中定义一个方法，这个方法的类型和父类的方法的类型一致或者是父类的方法的类型的子类型（所谓子类型是指，如果父类的方法的类型是“类”，那么允许子类的重写方法的类型是“子类”）一致，并且这个方法的名字，参数个数，参数的类型和父类的方法完全相同。**子类如此定义的方法称作子类重写的方法。


#### 1.2.2 重写的目的

子类通过方法的重写可以隐藏继承的方法，子类通过方法的重写可以把父类的状态和行为改变为自身的状态和行为。

如果父类的方法`f()`可以被子类继承，子类就有权利重写f()，一旦子类重写了父类的方法f()，就隐藏了继承的方法f()，那么子类对象调用方法f()一定是调用的重写方法f()；如果子类没有重写，而是继承了父类的方法f()，那么子类创建的对象当然可以调用f()方法，只不过方法f()产生的行为和父类的相同而已。

重写方法即可以操作继承的成员变量，调用继承的方法，也可以操作子类新声明的成员变量，调用新定义的其他方法，但无法操作被子类隐藏的方法，**如果子类想使用被隐藏的方法或成员变量，必须使用关键字super。**

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

原因是子类重写的方法和父类的方法类型不一致，这样子类就无法隐藏继承的方法，导致子类出现两个方法的名字相同，参数也相同的情况，这是不允许的

### 1.3 子类构造器

> 注释： 回忆一下，关键字this有两个用途：一是引用隐式参数，二是调用该类其他的构造器 ，同样，super关键字也有两个用途：一是调用超类的方法，二是调用超类的构造器。在调用构造器的时候，这两个关键字的使用方式很相似。调用构造器的语句只能作为另一个构造器的第一条语句出现。构造参数既可以传递给本类（this) 的其他构造器，也可以传递给超类（super) 的构造器。

#### 1.3.1 用super操作被隐藏的的成员变量和方法

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

#### 1.3.2 使用super调用父类的构造方法

首先在子类调用父类的构造方法的时候，必须遵守以下语法规则：

- 在子类的构造方法中，不能直接通过父类方法名调用父类的构造方法，而是要使用super语句
- **假如在子类的构造方法中有super语句，他必须作为构造方法中的第一条语句**

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


#### 1.3.3 何时调用构造函数
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
1. 上传型对象不能操作子类新增的成员变量（失去了这部分属性）；不能调用子类新增的方法（失去了一些功能）；

2. 上传型对象可以访问子类继承或隐藏的成员变量，也可以调用子类继承的方法或子类重写的实例方法。

上传型对象操作子类继承的方法或子类重写的实例方法，其作用等价于子类对象去调用这些方法。因此，如果子类重写了父类的某个实例方法后，当对象的上传型对象调用这个实例方法时一定调用了子类重写的实例方法。

注意：

1. 不要将父类创建的对象和子类对象的上传型对象混淆。 

2. 可以将对象的上传型对象在强制转换到一个子类对象，这时，该子类对象又具备了子类所有属性和功能。  

3. 不能将父类创建的对象的引用赋值给子类声明的对象  

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

### 1.4 继承的利弊和使用原则

- 继承树的层次不可太多
- 继承树的上层为抽象层
- 继承关系最大的弱点：打破封装
- 精心设计专门用于被继承的类
- 区分对象的属性与继承

### 1.5 多态

如下面这个例子，显示了饲养员Feeder，食物Feed，动物Animal，以及它的子类的类框图

可以把饲养员 动物和食物看作独立的子系统。Feeder类的定义如下：
```java
public class Feeder {
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

### 1.6 理解方法调用

弄清楚如何在对象上应用方法调用非常重要。下面假设要调用x.f(args)隐式参数x声明为类C的一个对象。下面是调用过程的详细描述：

1)编译器査看对象的声明类型和方法名。假设调用x.f(param)且隐式参数x声明为C类的对象。需要注意的是：有可能存在多个名字为f,但参数类型不一样的方法。例如，可能存在方法f(int)和方法String)。编译器将会一一列举所有C类中名为f的方法和其超类中访问属性为public且名为f的方法（超类的私有方法不可访问）。

至此，编译器已获得所有可能被调用的候选方法。

2)接下来，编译器将査看调用方法时提供的参数类型。如果在所有名为f的方法中存在一个与提供的参数类型完全匹配，就选择这个方法。这个过程被称为重栽解析（overloading resolution)。例如，对于调用x.f(“Hello”）来说，编译器将会挑选f(String)，而不是f(int)。

由于允许类型转换（int可以转换成 double, Manager可以转换成Employee, 等等，)所以这个过程可能很复杂。如果编译器没有找到与参数类型匹配的方法，或者发现经过类型转换后有多个方法与之匹配，就会报告一个错误。至此，编译器已获得需要调用的方法名字和参数类型。

> 注释：前面曾经说过，方法的名字和参数列表称为方法的签名。例如，f(int)和f(String)是两个具有相同名字，不同签名的方法。如果在子类中定义了一个与超类签名相同的方法，那么子类中的这个方法就覆盖了超类中的这个相同签名的方法。
>
> 不过，返回类型不是签名的一部分，因此，在覆盖方法时，一定要保证返回类型的兼容性。允许子类将覆盖方法的返回类型定义为原返回类型的子类型。例如，假设Employee类有
>
> ```java
> public Employee getBuddy() { ... }
> ```
>
> 经理不会想找这种地位低下的员工。为了反映这一点，在后面的子类Manager中，可以按照如下所示的方式覆盖这个方法
>
> ```java
> public Manager getBuddy() { ... } // OK to change return type
> ```
>
> 我们说，这两个getBuddy方法具有可协变的返回类型。

3 )如果是private方法、static方法、final方法（有关final修饰符的含义将在下一节讲述）或者构造器，那么编译器将可以准确地知道应该调用哪个方法，我们将这种调用方式称为静态绑定（static binding)。与此对应的是，调用的方法依赖于隐式参数的实际类型，并且在运行时实现动态绑定。在我们列举的示例中，编译器采用动态绑定的方式生成一条调用f(String)的指令。

4 )当程序运行，并且采用动态绑定调用方法时，虚拟机一定调用与x所引用对象的实际类型最合适的那个类的方法。假设x的实际类型是D，它是C类的子类。如果D类定义了方法f(String)，就直接调用它；否则，将在D类的超类中寻找f(String)，以此类推。

每次调用方法都要进行搜索，时间开销相当大。因此，虚拟机预先为每个类创建了一个方法表（method table),其中列出了所有方法的签名和实际调用的方法。这样一来，在真正调用方法的时候，虚拟机仅查找这个表就行了。在前面的例子中，虚拟机搜索D类的方法表，以便寻找与调用f(Sting)相K配的方法。这个方法既有可能是D.f(String),也有可能是X.f(String),这里的X是D的超类。这里需要提醒一点，如果调用super.f(param),编译器将对隐式参数超类的方法表进行搜索。

现在，查看一下程序清单5-1 中调用e.getSalary()的详细过程。e声明为Employee类型。Employee类只有一个名叫getSalary的方法，这个方法没有参数。因此，在这里不必担心重载解析的问题。

由于getSalary不是private方法、static方法或final方法，所以将采用动态绑定。虚拟机为Employee和Manager两个类生成方法表。在Employee的方法表中，列出了这个类定义的所有方法：

```java
Employee:
	getName() -> Employee.getName()
	getSalary() -> Employee.getSalary()
	getHireDay() -> Employee.getHireDay()
	raiseSalary(double) -> Employee.raiseSalary(double)
```

实际上，上面列出的方法并不完整，稍后会看到Employee类有一个超类Object，Employee类从这个超类中还继承了许多方法，在此，我们略去了Object方法。

Manager方法表稍微有些不同。其中有三个方法是继承而来的，一个方法是重新定义的，还有一个方法是新增加的。

```java
Manager:
	getName() -> Employee.getName()
	getSalary() -> Manager.getSalary()
	getHireDay() -> Employee.getHireDay()
	raiseSalary(double) -> Employee.raiseSalary(double)
	setBonus(double) -> Manager.setBonus(double)
```

在运行时，调用e.getSalary()的解析过程为：

1 )首先，虚拟机提取e的实际类型的方法表。既可能是Employee、 Manager的方法表，也可能是Employee类的其他子类的方法表。

2 )接下来，虚拟机搜索定义getSalary签名的类。此时，虚拟机已经知道应该调用哪个方法。

3 )最后，虚拟机调用方法。

动态绑定有一个非常重要的特性：无需对现存的代码进行修改，就可以对程序进行扩展。假设增加一个新类 Executive,并且变量e有可能引用这个类的对象，我们不需要对包含调用e.getSalary()的代码进行重新编译。如果e恰好引用一个Executive类的对象，就会自动地调用 Executive.getSalary()方法。

> 警告：在覆盖一个方法的时候，子类方法不能低于超类方法的可见性。特别是， 如果超类方法是public, 子类方法一定要声明为public。经常会发生这类错误：在声明子类方法的时候，遗漏了public修饰符。**此时，编译器将会把它解释为试图提供更严格的访问权限。**  

### 1.7 阻止继承：final类和方法

final具有不可改变的含义，它可以用来修饰非抽象类，非抽象对象成员，和变量。

- 用final修饰的类不能被继承，没有子类
- 用final修饰的方法不能被子类的方法覆盖
- 用final修饰的变量表示常量，只能被赋一次值

final不能用来修饰构造方法，因为“方法覆盖”这一概念仅适用于类的成员方法，而不适用于类的构造方法，父类的构造方法和子类的构造方法之间不存在覆盖关系，因此用final修饰构造方法是无意义的。父类中的private修饰的方法不能被子类的方法覆盖，因此private类型的方法默认是final类型的。

> 将方法或类声明为final主要目的是：确保它们不会在子类中改变语义。例如，Calendar类中的getTime和setTime方法都声明为final。这表明Calendar类的设计者负责实现Date类与日历状态之间的转换，而不允许子类处理这些问题。同样地，String 类也是final类，这意味着不允许任何人定义String的子类。换言之，如果有一个String的引用，它引用的一定是一个String对象，而不可能是其他类的对象。

#### 1.7.1 final类

继承关系的弱点是打破封装，子类能够访问父类的实现细节，而且能以方法覆盖的方式修改实现细节，在以下情况，可以考虑把类定义为final类型，使得这个类不能被继承。**final类中的所有方法自动地成为final方法。**

- 不是专门为继承而设计的类，类本身的方法之间有复杂的调用关系，假如随意创建这些类的子类，子类有可能会错误的修改父类的实现细节。
- 处于安全的原因，类的实现细节不允许有任何改动
- 在创建对象模型时，确信这个类不会再被扩展

例如，JDK中的java.lang.String类被定义为final类：

```java
public final class String {
	···
}
```

如果其他类继承String类，就会导致编译错误

#### 1.7.2 final方法

在某些情况下，处于安全的原因，子类不允许子类覆盖某个方法，此时可以把这个方法声明为final类型。例如在java.lang.Object类中，getClass()方法为final类型，而equals()方法不是final类型的：

```java
public class Object {
	public final Class getClass() {
		...
	}
	public boolean equal(Object o) {
		...
	}
}
```

所有Object方法都可以覆盖equals()方法，但不能覆盖getClass()方法。否则就会导致编译错误。

#### 1.7.3 final变量

用final修饰的变量表示取值不会改变的常量。

例如在java.lang.Integer类中定义了两个常量：

```java
public static final int MAX_VALUE=2147483647;
public staric final int MIN_VALUE=-2147483647;
```

final变量具有以下特性：

1. final修饰符可以修饰静态变量，实例变量和局部变量，分别表示静态变量，实例变量和局部变量。

2. 在成员变量的初始化那一节，曾经提到类的成员变量可以不必显式初始化，但是这不适用于final类型的成员变量。final类型的成员变量必须显式初始化，否则会导致编译错误。

例如：

```java
public class Sample {
	static final int a=1;
	static final int b;
	static {
		b=1;//合法
	}
}
```

3. final变量只能赋一次值

例如：

```java
public class Sample {
	private final int var1=1;
	public Sample() {
		var1=2;  //错误，不允许改变实例变量的值
	}
		public void method(final int param){
		final int var2=1;
		var2+=;  //编译错误
		param++;  //编译错误，不允许改变final类型参数的值
	}
}
```

以下Sample类的var1实例常量分别在Samlpe()和Sample(int x)这两个构造方法中初始化，这是合法的。因为在创建Sample对象时，只会执行一个构造方法，所以var1实例常量不会被初始化两次：

```java
class Sample {
	final int var1;//定义实例常量
	final int var2=0;//定义并初始化var2实例常量
	Sample() {
		var1=1;//初始var1化实例常量
	}
	Sample(int x) {
		var1=x;//初始化var1实例常量
	}
}
```

4. 如果将引用类型的变量用final修饰，那么该变量只能始终引用一个对象，但可以改变对象的内容，例如：

```java
public class Sample {
	public int var;
	public Sample(int var) {
		this.var=var;
	}
	public static void main(String args[]){
		final Sample s=new Sample(1);
		s.var=2;//合法，修改引用变量s所引用的Sample对象的var属性
		s=new Sample(2);//编译出错，不能改变引用变量s所引用的Sample对象
	}
}
```

在程序中通过final修饰符来定义常量，具有以下作用：

- 提高代码的安全性，禁止非法修改取值固定并且不允许改变的数据
- 提高程序代码的可维护性
- 提高程序代码的可读性

### 1.8 强制类型转换

第3章曾经讲过，将一个类型强制转换成另外一个类型的过程被称为类型转换。Java程序设计语言提供了一种专门用于进行类型转换的表示法。例如： 

```java
double x=3.405;
int nx=(int)x;
```

将表达式 x 的值转换成整数类型，舍弃了小数部分。

正像有时候需要将浮点型数值转换成整型数值一样，有时候也可能需要将某个类的对象引用转换成另外一个类的对象引用。对象引用的转换语法与数值表达式的类型转换类似， 仅需要用一对圆括号将目标类名括起来，并放置在需要转换的对象引用之前就可以了。例如：

```java
Manager boss=(Manager)staff[0];
```

进行类型转换的唯一原因是：在暂时忽视对象的实际类型之后，使用对象的全部功能。例如，在managerTest类中，由于某些项是普通雇员，所以staff数组必须是Employee对象的数组。我们需要将数组中引用经理的元素复原成Manager类，以便能够访问新增加的所有变量（需要注意，在前面的示例代码中，为了避免类型转换，我们做了一些特别的处理，即将boss变量存入数组之前，先用Manager对象对它进行初始化。而为了设置经理的奖金，必须使用正确的类型。)

大家知道，在Java中，每个对象变量都属于一个类型。类型描述了这个变量所引用的以及能够引用的对象类型。例如，staff[i]引用一个Employee对象（因此它还可以引用Manager对象。)

将一个值存入变量时，编译器将检查是否允许该操作。将一个子类的引用赋给一个超类变量，编译器是允许的。但将一个超类的引用赋给一个子类变量，必须进行类型转换，这样才能够通过运行时的检査。

如果试图在继承链上进行向下的类型转换，并且“谎报”有关对象包含的内容，会发生什么情况呢？

```java
Manager boss = (Manager)staff[1]; // Error
```

运行这个程序时，Java运行时系统将报告这个错误，并产生一个ClassCastException异常。如果没有捕获这个异常，那么程序就会终止。因此，应该养成这样一个良好的程序设计习惯：在进行类型转换之前，先查看一下是否能够成功地转换。这个过程简单地使用instanceof操作符就可以实现。例如：

```java
if (staff[1] instanceof Manager)
{
	boss = (Manager) staff[1];
	...
}
```

最后， 如果这个类型转换不可能成功，编译器就不会进行这个转换。例如，下面这个类型转换：

```java
String c = (String) staff[1];
```

将会产生编译错误，这是因为String不是Employee的子类。

综上所述：

- 只能在继承层次内进行类型转换。
- 在将超类转换成子类之前，应该使用instanceof进行检查。  

> 注释： 如果 x 为 null , 进行下列测试
>
> ```
> x instanceof C
> ```
>
> 不会产生异常，只是返回 false。之所以这样处理是因为null没有引用任何对象， 当然也不会引用C类型的对象。

实际上，通过类型转换调整对象的类型并不是一种好的做法。在我们列举的示例中，大多数情况并不需要将Employee对象转换成Manager对象，两个类的对象都能够正确地调用getSalary方法，这是因为实现多态性的动态绑定机制能够自动地找到相应的方法。

只有在使用Manager中特有的方法时才需要进行类型转换，例如，setBonus方法。如果鉴于某种原因，发现需要通过Employee对象调用setBomis方法，那么就应该检查一下超类的设计是否合理。重新设计一下超类，并添加setBonus*法才是正确的选择。请记住，只要没有捕获ClassCastException异常，程序就会终止执行。在一般情况下，应该尽量少用类型转换和instanceof运算符。

### 1.9 抽象类

如果自下而上在类的继承层次结构中上移，位于上层的类更具有通用性，甚至可能更加抽象。从某种角度看，祖先类更加通用，人们只将它作为派生其他类的基类，而不作为想使用的特定的实例类。例如，考虑一下对Employee类层次的扩展。一名雇员是一个人，一名学生也是一个人。下面将类Person和类Student添加到类的层次结构中。

为什么要花费精力进行这样高层次的抽象呢？每个人都有一些诸如姓名这样的属性。学生与雇员都有姓名属性， 因此可以将getName法放置在位于继承关系较高层次的通用超类中。

现在，再增加一个getDescription方法，它可以返回对一个人的简短描述。例如：

```
an employee with a salary of $50,000.00
a student majoring in computer science
```

在Employee类和Student类中实现这个方法很容易。但是在Person类中应该提供什么内容呢？除了姓名之外，Person类一无所知。当然，可以让`Person.getDescription()`返回一个空字符串。然而，还有一个更好的方法，就是使用abstract关键字，这样就完全不需要实现这个方法了。

```java
public abstract String getDescription();
// no implementation required
```

为了提高程序的清晰度，包含一个或多个抽象方法的类本身必须被声明为抽象的。

```java
public abstract class Person {
	public abstract String getDescription();
}
```

除了抽象方法之外，抽象类还可以包含具体数据和具体方法。例如，Person类还保存着姓名和一个返回姓名的具体方法。

```java
public abstract class Person {
	private String name;
	public Person(String name) {
		this.name = name;
	}
	public abstract String getDescription();
		public String getName() {
			return name;
	}
}
```

抽象方法充当着占位的角色，它们的具体实现在子类中。扩展抽象类可以有两种选择。一种是在抽象类中定义部分抽象类方法或不定义抽象类方法，这样就必须将子类也标记为抽象类；另一种是定义全部的抽象方法，这样一来，子类就不是抽象的了。

例如，通过扩展抽象Person类，并实现getDescription方法来定义Student类。由于在Student类中不再含有抽象方法，所以不必将这个类声明为抽象的。

类即使不含抽象方法，也可以将类声明为抽象类。

抽象类不能被实例化。也就是说，如果将一个类声明为abstract,就不能创建这个类的对象。例如，表达式

```java
new Person("Vinee Vu")
```

是错误的，但可以创建一个具体子类的对象。

需要注意，可以定义一个抽象类的对象变量，但是它只能引用非抽象子类的对象。例如，

```java
Person p = new Student("Vinee Vu", "Economics");
```

这里的p是一个抽象类Person的变量，Person引用了一个非抽象子类Student的实例

abstract修饰符可用来修饰类和成员方法;

- 用abstract修饰的类表示抽象类，抽象类位于继承树的抽象层，抽象类不能被实例化，既不允许创建抽象类本身的实例。
- 用abstract修饰的方法表示抽象方法，抽象方法没有方法体，抽象方法用来描述系统具有什么功能，但不提供具体的实现，没有用abstract修饰的方法称为具体方法，具体方法具有方法体。

> abstract类和abstract方法

用关键字abstract修饰的类称为abstract类（抽象类） 

如：

```java
abstract class A {
}
```

用关键字abstract修饰的方法称为abstract方法（抽象方法），例如

```java
abstract int min(int x,int y);
```

对于abstract方法，只允许声明，不允许实现（没有方法体），而且不允许使用abstract和final同时修饰一个方法和类。

**使用abstract修饰符需要遵守以下语法规则**

1. 抽象类中可以没有抽象方法，但包含了抽象方法的类必须被定义为抽象类。如果子类没有实现父类中所有的抽象方法，那么子类也必须定义为抽象类，否则编译会出错。

2. 没有抽象静态方法，static和abstract关键字不可在一起使用。

3. 抽象类中可以有非抽象的构造方法，创建子类的实例可能会调用这些构造方法。抽象类不能被实例化，然而可以创建一个引用变量，其类型是一个抽象类，并让他引用非抽象的子类的一个实例。

4. 抽象类及抽象方法不能被final修饰符修饰，abstract修饰符与final修饰符不能连用，因为抽象类只允许创建其子类，他的抽象方法才能被实现，并且只有他的具体子类才能被实例化，而用final修饰的类不允许拥有子类，用final修饰的方法不允许被子类方法覆盖，因此两者连用，会自相矛盾。

**抽象类的一个重要特征是不允许实例化，比如苹果香蕉是具体类，而水果是抽象类一样。在自然界中并不存在水果类本身的实例，只存在它的具体子类的实例：**

```java
Fruit fruit=new Apple();
```

在继承树上，总可以把子类的对象看作父类的对象。当父类是具体类，父类的对象包括父类本身的对象，以及所有具体子类的对象;当父类是抽象类，父类的对象包括所有具体子类的对象，因此，所谓的抽象类不能实例化，是指不能创建抽象类本身的实例，尽管如此，可以创建一苹果对象，并把它看作是水果对象。

5. 抽象方法不能被private修饰符修饰，这是因为如果方法是抽象的，表示父类只申明具备某种功能，但没有提供实现，这种方法有待于某个子类去实现它，父类中的abstract方法必须让子类是可见的。否则，在父类中声明一个永远无法实现的方法是无意义的，假如java允许把父类的方法同时用abstract和private修饰，那就意味着在父类中声明一个永远无法实现的方法，所以在java中不允许出现这一情况。

注意：abstract类也可以没有abstract方法。  

如果一个abstract类是abstract类的子类，它可以重写父类的abstract方法，也可以继承这个abstract方法。

> abstract类的子类如果不是abstract的，那么它就可以被实例化，这会导致执行该abstract类的构造器，进而执行其用于实例变量的域初始化器。

### 1.10 受保护访问

大家都知道，最好将类中的域标记为private,而方法标记为public。任何声明为private的内容对其他类都是不可见的。前面已经看到，这对于子类来说也完全适用，即子类也不能访问超类的私有域。

然而，在有些时候，人们希望超类中的某些方法允许被子类访问，或允许子类的方法访问超类的某个域。为此，需要将这些方法或域声明为protected。例如，如果将超类Employee中的hireDay声明为proteced,而不是私有的，Manager中的方法就可以直接地访问它。

**表中的类仅限于顶层类，而不包括内部类。内部类是指定义在类或方法中的类。**

| 修饰符    | 类   | 成员方法 | 构造方法 | 成员变量 | 局部变量 |
| --------- | ---- | -------- | -------- | -------- | -------- |
| abstract  | Y    | Y        | -        | -        | -        |
| static    | -    | Y        | -        | Y        | -        |
| pubic     | Y    | Y        | Y        | Y        | -        |
| protected | -    | Y        | Y        | Y        | -        |
| private   | -    | Y        | Y        | Y        | -        |
| final     | Y    | Y        | -        | Y        | Y        |

从上表可以看出：修饰顶层类的修饰符包括：abstract,public,final,而static,protected,private不能修饰顶层类。成员方法和成员变量可以有多种修饰符，而局部变量只能用final来修饰。

面向对象的基本思想之一是封装实现细节并且公开接口。java语言采用访问控制符来控制类，以及类的方法和访问权限，从而向使用者只暴露接口，但隐藏实现细节，访问控制分四种级别：**（从上到下，访问控制级别由大到小）**

- 公开级别：用public修饰，对外公开
- 受保护级别：用protected修饰，向子类及同一个包中的类公开
- 默认级别：没有访问控制修饰符，向同一个包中的类公开
- 私有级别：用Private修饰，只有类本身可以访问，不对外公开

| 访问级别 | 访问控制修饰符 | 同类 | 同包 | 子类 | 不同的包 |
| -------- | -------------- | ---- | ---- | ---- | -------- |
| 公开     | public         | Y    | Y    | Y    | Y        |
| 受保护   | protected      | Y    | Y    | Y    | -        |
| 默认     | 无             | Y    | Y    | -    | -        |
| 私有     | Private        | Y    | -    | -    | -        |

成员变量，成员方法和构造方法可以处于4个访问级别中的一个：公开，受保护，默认或者私有。**顶层类只可以处于公开或默认访问级别**，因此顶层类不能用private和protected来修饰，以下代码会导致编译错误：

```java
private class Sample{...};
```

## 2 Object类：所有类的超类

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

> C++ 注释：在 C++ 中没有所有类的根类， 不过，每个指针都可以转换成 void* 指针  

### 2.1 equals方法
Object类中的equals方法用于检测一个对象是否等于另外一个对象。在Object类中，这个方法将判断两个对象是否具有相同的引用。如果两个对象具有相同的引用，它们一定是相等的。从这点上看，将其作为默认操作也是合乎情理的。然而，对于多数类来说，这种判断并没有什么意义。例如，采用这种方式比较两个PrintStream对象是否相等就完全没有意义。然而，经常需要检测两个对象状态的相等性，如果两个对象的状态相等，就认为这两个对象是相等的。

例如，如果两个雇员对象的姓名、薪水和雇佣日期都一样，就认为它们是相等的（在实际的雇员数据库中，比较ID更有意义。利用下面这个示例演示equals方法的实现机制）。

```java
public class Employee {
	...
	public boolean equals(Object otherObject){
		// a quick test to see if the objects are identical
		if (this == otherObject) return true;
        
		// must return false if the explicit parameter is null
		if (otherObject == null) return false;
        
		// if the classes don't match, they can't be equal
		if (getClass() != otherObject.getClass())
			return false;
        
		// now we know otherObject is a non-null Employee
		Employee other = (Employee)otherObject;
        
		// test whether the fields have identical values
		return name.equals(other.name)&&salary==other.salary&&hireDay. equals(other.hireDay);
	}
}
```

getClass方法将返回一个对象所属的类，有关这个方法的详细内容稍后进行介绍。在检测中，只有在两个对象属于同一个类时，才有可能相等。

> 提示：为了防备name或hireDay可能为null的情况，需要使用Objects.equals方法。如果两个参数都为null，Objects.equals(a，b)调用将返回true;如果其中一个参数为null,则返回false;否则，如果两个参数都不为null，则调用a.equals(b)利用这个方法，Employee.equals方法的最后一条语句要改写为：
> 
> ```java
> return Objects.equals(name, other.name)
> && salary == other.salary
>&& Object.equals(hireDay, other.hireDay);  
> ```

在子类中定义equals方法时，首先调用超类的equals。 如果检测失败，对象就不可能相等。如果超类中的域都相等，就需要比较子类中的实例域。

```java
public class Manager extends Employee
    ...
	public boolean equals(Object otherObject)
	{
		if (!super.equals(otherObject)) return false;
		// super.equals checked that this and otherObject belong to the same class
		Manager other = (Manager)otherObject;
		return bonus == other.bonus;
	}
}  
```

### 2.2 相等测试与继承

如果隐式和显式的参数不属于同一个类，equal方法将如何处理呢？这是一个很有争议的问题。在前面的例子中，如果发现类不匹配，equals方法就返冋false: 但是，许多程序员却喜欢使用instanceof进行检测： 

```java
if (!(otherObject instanceof Employee)) return false;
```

这样做不但没有解决otherObject是子类的情况，并且还有可能会招致一些麻烦。这就是建议不要使用这种处理方式的原因所在。Java语言规范要求equals方法具有下面的特性：

1 ) 自反性：对于任何非空引用 x, x.equals() 应该返回true

2 ) 对称性: 对于任何引用 x 和 y, 当且仅当 y.equals(x) 返回 true, x.equals(y) 也应该返回true。

3 ) 传递性： 对于任何引用 x、 y 和 z, 如果 x.equals(y) 返回true， y.equals(z) 返回true,x.equals(z) 也应该返回 true。

4 ) 一致性：如果x和y引用的对象没有发生变化，反复调用 x.equal(y) 应该返回同样的结果。

5 ) 对于任意非空引用 x, x.equals(null) 应该返回false

### 2.3 hashCode方法

散列码（hash code)是由对象导出的一个整型值。散列码是没有规律的。如果x和y是两个不同的对象，x.hashCode()与y.hashCode()基本上不会相同。在表5-1中列出了几个通过调用String类的hashCode方法得到的散列码。

String类使用下列算法计算散列码：

```java
int hash = 0;
for (int i = 0; i < length0；i++)
	hash = 31 * hash + charAt(i);
```
由于hashCode方法定义在Object类中，因此每个对象都有一个默认的散列码，其值为对象的存储地址。来看下面这个例子。

```java
String s = "Ok";
StringBuilder sb = new StringBuilder(s);
System.out.println(s.hashCode() + " " + sb.hashCode());
String t = new String("Ok");
StringBuilder tb = new StringBuilder(t);
System.out.println(t.hashCode() + " " + tb.hashCode());  
```

| 对象 | 散列码   |
| ---- | -------- |
| s    | 2556     |
| sb   | 20526976 |
| t    | 2556     |
| tb   | 20527144 |

请注意，字符串s与t拥有相同的散列码，这是因为字符串的散列码是由内容导出的。而字符串缓冲sb与t却有着不同的散列码，**这是因为在StringBuffer类中没有定义hashCode方法，它的散列码是由Object类的默认hashCode方法导出的对象存储地址。**

如果重新定义equals方法，就必须重新定义hashCode方法，以便用户可以将对象插入到散列表中（有关散列表的内容将在第9章中讨论)。  

## 3 泛型数组列表

在许多程序设计语言中，特别是在C++语言中，必须在编译时就确定整个数组的大小。程序员对此十分反感，因为这样做将迫使程序员做出一些不情愿的折中。例如，在一个部门中有多少雇员？肯定不会超过100人。一旦出现一个拥有150名雇员的大型部门呢？愿意为那些仅有10名雇员的部门浪费90名雇员占据的存储空间吗？

在Java中，情况就好多了。它允许在运行时确定数组的大小。

```java
int actualSize =...;
Employee[] staff = new Employee[actualSize];
```
当然，这段代码并没有完全解决运行时动态更改数组的问题。一旦确定了数组的大小，改变它就不太容易了。在Java中，解决这个问题最简单的方法是使用Java中另外一个被称为ArrayList的类。它使用起来有点像数组，但在添加或删除元素时，具有自动调节数组容量的功能，而不需要为此编写任何代码。

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


> API java.util.ArrayList<\E> 1.2
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

### 3.1 访问数组列表元素

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

### 3.2 类型化与原始数组列表的兼容性
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

## 4 对象包装器与自动装箱

有时，需要将int这样的基本类型转换为对象。所有的基本类型都有一个与之对应的类。例如，Integer类对应基本类型Int。通常，这些类称为包装器（wrapper)这些对象包装器类拥有很明显的名字：Integer、Long、Float、Double、Short、Byte、Character、Void和Boolean(前6个类派生于公共的超类Number)。对象包装器类是不可变的，即一旦构造了包装器，就不允许更改包装在其中的值。同时，对象包装器类还是final,因此不能定义它们的子类。

假设想定义一个整型数组列表。而尖括号中的类型参数不允许是基本类型，也就是说，不允许写成ArrayList<int>。这里就用到了Integer对象包装器类。我们可以声明一个Integer对象的数组列表。

```
ArrayList<Integer> list = new ArrayList<>();
```

> 警告：由于每个值分别包装在对象中，所以ArrayList<lnteger> 的效率远远低于int[ ]数组。因此，应该用它构造小型集合，其原因是此时程序员操作的方便性要比执行效率更加重要。  

幸运的是，有一个很有用的特性，从而更加便于增添int类型的元素到ArrayList<Integer>中。下面这个调用

```java
list.add(3);
```

将自动的转换成

```java
list.add(Integer.valueOf(3));
```

这种变换被称为自动装箱。

相反地，当将一个Integer对象赋给一个int值时，将会自动地箱也就是说，编译器将下列语句：

```java
int n = list.get(i);
```

翻译成

```java
int n = list.get(i).intValue();
```

甚至在算术表达式中也能够自动地装箱和拆箱。例如，可以将自增作符应用于一个包装器引用：

```java
Integer n = 3;
n++;
```

编译器将自动地插入一条对象拆箱的指令，然后进行自增计算，最后再将结果装箱。大多数情况下，容易有一种假象，即基本类型与它们的对象包装器是一样的，只是它们的相等性不同。大家知道，==运算符也可以应用于对象包装器对象，只不过检测的是对象是否指向同一个存储区域，因此，下面的比较通常不会成立：

```java
Integer a = 1000;
Integer b = 1000;
if (a = b) . . .
```
然而，Java实现却有可能（may)让它成立。如果将经常出现的值包装到同一个对象中，这种比较就有可能成立。这种不确定的结果并不是我们所希望的。解决这个问题的办法是在两个包装器对象比较时调用equals方法。  

> 注释：自动装箱规范要求boolean、byte、char<=127，介于-128 ~ 127之间的short和int被包装到固定的对象中。例如，如果在前面的例子中将a和b初始化为100，对它们进行比较的结果一定成立。

关于自动装箱还有几点需要说明。首先，由于包装器类引用可以为null, 所以自动装箱有可能会抛出一个NullPointerException异常：

```java
Integer n = null;
System.out.println(2*n); //Throw NullPointerException
```

另外，如果在一个条件表达式中混合使用Integer和Double类型，Integer值就会拆箱，提升为double, 再装箱为Double:

```java
Integer n = 1;
Double x = 2.0;
System.out.println(true ? n : x); // Prints 1.0
```

最后强调一下，装箱和拆箱是编译器认可的，而不是虚拟机。编译器在生成类的字节码时， 插入必要的方法调用。虚拟机只是执行这些字节码。使用数值对象包装器还有另外一个好处。Java设计者发现，可以将某些基本方法放置在包装器中，例如，将一个数字字符串转换成数值。

要想将字符串转换成整型， 可以使用下面这条语句：

```java
int x = Integer.parseInt(s);
```

这里与Integer对象没有任何关系，parselnt是一个静态方法。但Integer类是放置这个方法的一个好地方

API注释说明了Integer类中包含的一些重要方法。其他数值类也实现了相应的方法
