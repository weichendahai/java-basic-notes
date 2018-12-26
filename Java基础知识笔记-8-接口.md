Java基础知识笔记-8-接口

# 接口

&emsp;&emsp;在Java语言中，接口有两种意思
- 一是指概念性的接口，即指系统对外提供的所有服务，类的所有能被外部使用者访问的方法构成了类的接口
- 二是指interface关键字定义的实实在在的接口，也称为接口类型。

&emsp;&emsp;在面相对象程序设计中，定义一个类必须做什么而不是怎么做有时是很有益的。前面有一个这样的例子：抽象方法为方法定义了签名，但不提供实现方式。子类必须自己实现由其父类定义的抽象方法。这样，抽象方法就指定了方法的接口而不是实现。尽管抽象类和方法很有用，但还可以将这一概念进一步延伸。在java中，可使用关键字interface把类的接口和实现方法完全分开。

&emsp;&emsp;使用关键字interface来定义一个接口。接口的定义和类的定义很相似，分为接口的声明和接口体
例如：
```
interface Printable{
	final int MAX=100;
	void add();
	float sum(float x,float y);
}
```
### 1.接口声明
&emsp;&emsp;接口包含接口声明和接口体，和类不同的是，接口声明使用关键字interface来表明自己是一个接口。

### 2.接口体
&emsp;&emsp;接口体包含常量的声明（没有变量）和抽象方法两部分。

#### 接口体中中只有抽象的方法和普通的方法。而且接口体中所有常量的访问权限一定都是public（允许省略public，final修饰符），所有的抽象方法的访问权限一定都是public（允许省略public，abstract修饰符）例如：
```
interface Printable{
	public final int MAX=100;
	public abstract void add();
	public abstract float sum(float x,float y);
}
```
#### 在JDK8以前的版本中，接口只能包含抽象方法，从JDK8开始，为了提高代码的可重用性，允许在接口中定义默认方法和静态方法。默认方法用default关键字来声明，拥有默认的实现，接口的实现类既可以直接访问默认方法，也可以覆盖它，重新实现该方法，例如下例：
```
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
&emsp;&emsp;以下类实现了上面这个接口，Tester类作为非抽象类，必须实现MyIFC接口的抽象method3()方法。tester的实例可以直接访问在接口中定义的method1()默认方法
```
public class Tester implements MyIFC{
	public void method3(){//实现接口中的method3()方法
		System.out.println("method3);
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
&emsp;&emsp;接口中的静态方法只能在接口内部被访问，或者其他程序通过接口的名字来访问它的静态方法，如果试图通过实现接口的类的实例来访问该静态方法，会导致编译错误，例如：

```
t.method2();//编译出错，Tester实例不能访问MyIFC接口的静态方法
MyIFC.method2();//合法，可以通过接口的名字来访问它的静态方法
```
&emsp;&emsp;另外，需要注意的是，在接口中为方法提供默认实现虽然可以提高代码的可重用性，但还是要谨慎地使用这一特性。因为在层次关系比较复杂的软件系统中，这一特性会使程序代码导致歧异和混淆。

#### 接口没有构造方法，不能被实例化。在接口中定义构造方法是非法的，例如：
```
public interface A{
	public A(){//编译出错，接口中不允许定义构造方法
	...
	}
	void method();
}
```
#### 虽然不允许创建接口的实例，但是允许定义接口类型的引用变量，该变量引用实现了这个接口的类的实例，例如：
```
//引用变量t被定义为Photographable接口类型，他引用Camera实例
Photographable t=new Camera();
```

---

## 2 实现接口
&emsp;&emsp;在Java语言中，**接口由类去实现以便使用接口中的方法，一个类可以实现多个接口，类通过使用关键字implements声明自己实现一个或者多个接口。** 如果实现多个接口，用逗号隔开接口名。  

&emsp;&emsp;如A类实现Printable和Addable接口
```
class A implements Printable,Addable
```
&emsp;&emsp;再例如Animal的子类Dog实现Eatable和sleepable接口
```
class Dog extends Animal implements Eatable,Sleepable
```
&emsp;&emsp;**如果一个非抽象类实现了某个接口，那么这个类必须重写该接口的所有方法，需要注意的是，由于接口中的方法一定是`public abstract方法`**，所以非抽象类在重写接口方法时不仅要去掉abstract修饰给出的方法体，而且方法的访问权限一定要明显的用public来修饰（否则降低了访问权限，这是不允许的）实现接口的非抽象类一定要重写接口的方法，因此也称这个类实现了接口的方法，Java提供的接口都在相应的包中，通过import语句不仅可以引入包中的类，还可以引入包中的接口，例如:
```
import java.io.*;
```
&emsp;&emsp;类重写的接口方法以及接口中的常量可以直接被类的对象调用，而且常量也可以用接口名直接调用。  

&emsp;&emsp;接口声明时，如果关键字interface前面加上public，就称它为public接口，可以被任何一个类实现，如果一个接口不加public修饰，那么就称它为友好接口类，友好接口类可以被与该接口在同一个包中实现。  

&emsp;&emsp;如果父类实现了某个接口，那么子类也自然实现了接口，子类不必再显式地使用关键字implements声明实现这个接口。  

&emsp;&emsp;接口也可以被继承，既可以通过关键字extends声明一个接口是另一个接口的子接口。由于接口中的方法和常量都是public，子接口将继承父接口中的全部方法和常量。  

&emsp;&emsp;***注意：如果一个类声明实现某一个接口，但没有重写接口中的所有方法，那么这个类必须是abstracts类***
这句话是指当类实现了某个接口，它必须实现接口中的所有抽象方法，否则这个类必须被定义为抽象类。


例如:
```
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

---
## 3 理解接口
&emsp;&emsp;不同的类可以实现相同的接口，同一个类也可以实现多个接口。  

&emsp;&emsp;**当不希望某些类通过继承使得他们具有一些相同的方法时，就可以考虑让这些类去实现相同的接口而不是把他们声明为同一个类的子类。**  

---
## 5接口回调
&emsp;&emsp;和类一样，接口也是Java中重要的一种数据类型，用接口声明的变量称为接口变量。  
**接口属于引用型变量，接口变量中可以存放实现该接口的类的实例的引用，即存放对象的引用**  
&emsp;&emsp;假如，假设Com是一个接口，那么就可以用Com声明一个变量:
```
Com com;
```
&emsp;&emsp;内存模型如图所示，此时的com是一个空接口，因为com变量中还没有存放实现该接口的类的实例的引用。  

&emsp;&emsp;假设ImpleCom类是实现Com接口，用ImpleCom创建名字为object的对象，那么object对象不仅可以调用ImpleCom类中原有的方法，还可以调用该类实现的接口方法。
```
ImpleCom object=new ImpleCom();
```
&emsp;&emsp;在Java中，接口回调是指可以把实现某一类接口的类创建的的对象的引用赋给该接口声明的接口变量中，那么该接口变量就可以调用被类实现的接口方法。实际上，当接口变量调用被类实现的接口方法时，就是通知相应的对象调用这个方法。  

&emsp;&emsp;**接口回调非常类似我们在之前介绍的上传型对象调用子类重写的方法**
### 5.1 使用接口引用
&emsp;&emsp;你可能会对可以使用接口创建引用变量感到惊讶，可以创建接口引用变量，这样的变量可以引用实现他的接口的任何对象，当通过接口调用一个对象上的方法时，就会执行次对象实现的那个版本上那个的方法，这个过程类似与使用父类去引用访问子类对象。

### 5.2 接口能够扩展
&emsp;&emsp;使用关键字extends，一个接口可以继承另一个接口。扩展接口的语法与继承类的语法一样，当一个类实现继承了其他接口的接口时，他必须在接口继承链中定义的所有方法提供实现方式。
实例代码：
```
interface A{
	void meth1();
	void meth2();
}
interface B extends A{
	void meth3();
}
class MyClass implements B{
	public void meth1(){
		System.out.println("Implement meth1().");
	}
	public void meth2(){
		System.out.println("Implement meth2().");
	}
	public void meth3(){
		System.out.println("Implement meth3().");
	}
class IFExtends{
	public static void main(String args[]){
		MyClass ob=new MyClass();

		ob.meth1();
		ob.meth2();
		ob.meth3();
	}
}
}

```

---
## 6 接口与多态
&emsp;&emsp;由接口产生多态就是指不同的类在实现同一个接口时可能具有不同的实现方式，那么接口变量在回调接口方法时就可能具有多种形态。
```
interface ComputerAverage{
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
---
## 7 接口变量做参数
---
## 8 abstract类与接口的比较
&emsp;&emsp;接口和abstract类的比较如下：

1.abstract类和接口都有abstract方法  
2.接口中只可以有常量，不能有变量；而abstract类中既可以有常量又可以有变量  
3.abstract类中也可以有非abstract方法，接口不可以==   
在设计程序时，应当根据具体的分析方法来确定是使用抽象类还是接口。abstract类除了提供重要的需要子类重写的abstract方法外，也提供了子类需要继承的变量和非abstract方法，如果子类需要重写父类的abstract方法，还需要从父类继承一些变量或继承一些重要的非abstract方法，就可以考虑用abstract。  

&emsp;&emsp;如果某个问题不需要继承，只是需要若干个类给出某些重要的abstract方法的实现细节，就可以考虑使用接口。
