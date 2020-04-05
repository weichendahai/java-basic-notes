Java基础知识笔记-18-反射

# 18 反射

反射库（reflection library)提供了一个非常丰富且精心设计的工具集，以便编写能够动态操纵Java代码的程序。这项功能被大量地应用于JavaBeans中，它是Java组件的体系结构(有关JavaBeans的详细内容在卷II中阐述)。使用反射，Java可以支持VisualBasic用户习惯使用的工具。特别是在设计或运行中添加新类时，能够快速地应用开发工具动态地查询新添加类的能力。

能够分析类能力的程序称为反射（reflective)。反射机制的功能极其强大，在下面可以看到，反射机制可以用来：

- 在运行时分析类的能力。
- 在运行时查看对象，例如，编写一个toString方法供所有类使用。
- 实现通用的数组操作代码。
- 利用Method对象，这个对象很像中的函数指针。

反射是一种功能强大且复杂的机制。使用它的主要人员是工具构造者，而不是应用程序员。如果仅对设计应用程序感兴趣，而对构造工具不感兴趣，可以跳过本章的剩余部分，稍后再返回来学习。

**反射机制的相关类**

与Java反射相关的类如下：

| 类名          | 用途                                             |
| ------------- | ------------------------------------------------ |
| Class类       | 代表类的实体，在运行的Java应用程序中表示类和接口 |
| Field类       | 代表类的成员变量（成员变量也称为类的属性）       |
| Method类      | 代表类的方法                                     |
| Constructor类 | 代表类的构造方法                                 |

## 1 通过反射查看类信息

Java程序中的许多对象在运行时都会出现两种类型：编译时类型和运行时类型，例如代码Person p=new Student();这行代码将会生成一个p变量，该变量的编译时类型为Person，运行时类型为Student；除此之外，还有更极端的情形，程序在运行时接收到外部传入的一个对象，该对象的编译时类型为Object，但程序又需要调用该对象运行时类型的方法。

为了解决这些问题，程序需要在运行时发现对象和类的真实信息，解决方法有以下两种：

- 第一种做法是假设在编译时和运行时发现对象和类的真实信息。在这种情况下，可以先使用instanceof运算符进行判断

> instanceof主要用来判断一个类是否实现了某个接口，或者判断一个实例对象是否属于一个类。该运算符是二目运算符，左边的操作元是一个对象，右边是一个类，当左边的对象是右边的类或子类创建的对象时，该运算符运算的结果是true,否则是false。
>
> 例：
>
> ```java
>class instanceOfDemo{
> 	public static void main(String []args){
> 		instanceOfDemo t=new instanceOfDemo();
> 		if(t instanceof instanceOfDemo){
> 			System.out.println("t是instanceOfDemo的实例");
> 		}
> 	}
> }
> ```

- 第二种做法是编译时根本无法预知该对象和类可能属于哪些类，程序只依靠运行时信息来发现该对象和类的真实信息，这就必须使用反射。

### 1.1 Class类

Class 是一个类，**一个描述类的类**，封装了描述方法的Method，描述字段的Filed，描述构造器的 Constructor等属性.

每个类被加载后，系统就会为该类生成一个对应的Class对象，通过该Class对象就可以访问到JVM中的这个类。

获取Class对象的三种方式

1.通过类名获取   类名.class

2.通过对象获取   对象名.getClass()

3.通过全类名获取  Class.forName(全类名)

**class类的常用方法**

| 方法                       | 用途                                                   |
| -------------------------- | ------------------------------------------------------ |
| asSubclass(Class<U> clazz) | 把传递的类的对象转换成代表其子类的对象                 |
| Cast                       | 把对象转换成代表类或是接口的对象                       |
| getClassLoader()           | 获得类的加载器                                         |
| getClasses()               | 返回一个数组，数组中包含该类中所有公共类和接口类的对象 |
| getDeclaredClasses()       | 返回一个数组，数组中包含该类中所有类和接口类的对象     |
| forName(String className)  | 根据类名返回类的对象                                   |
| getName()                  | 获得类的完整路径名字                                   |
| newInstance()              | 创建类的实例                                           |
| getPackage()               | 获得类的包                                             |
| getSimpleName()            | 获得类的名字                                           |
| getSuperclass()            | 获得当前类继承的父类的名字                             |
| getInterfaces()            | 获得当前类实现的类或是接口                             |

在程序运行期间，Java运行时系统始终为所有的对象维护一个被称为运行时的类型标识。这个信息跟踪着每个对象所属的类。虚拟机利用运行时类型信息选择相应的方法执行。

然而，可以通过专门的Java类访问这些信息。保存这些信息的类被称为Class,这个名字很容易让人混淆。Object类中的getClass()方法将会返回一个Class类型的实例。

```java
Employee e;
...
Class cl = e.getClass();
```

如同用一个Employee对象表示一个特定的雇员属性一样，一个Class对象将表示一个特定类的属性。最常用的Class方法是getName。这个方法将返回类的名字。例如，下面这条语句：

```java
System.out.println(e.getClass().getName() + " " + e.getName());
```

如果e是一个雇员，则会打印输出：

```
Employee Harry Hacker
```

如果e是经理， 则会打印输出：

```
Manager Harry Hacker
```

如果类在一个包里，包的名字也作为类名的一部分：

```java
Random generator = new Random():
Class cl = generator.getClass() ;
String name = cl.getName(); // name is set to "java.util.Random"
```

还可以调用静态方法forName获得类名对应的Class对象。

```java
String className = "java.util.Random";
Class cl = Class.forName(className) ;
```

如果类名保存在字符串中，并可在运行中改变，就可以使用这个方法。当然，这个方法只有在className是类名或接口名时才能够执行。否则，forName方法将抛出一个checkedexception(已检查异常）。无论何时使用这个方法，都应该提供一个异常处理器（exceptionhandler)。如何提供一个异常处理器，请参看下一节。

> 提示：在启动时，包含main方法的类被加载。它会加载所有需要的类。这些被加栽的类又要加载它们需要的类，以此类推。对于一个大型的应用程序来说，这将会消耗很多时间，用户会因此感到不耐烦。可以使用下面这个技巧给用户一种启动速度比较快的幻觉。不过，要确保包含main方法的类没有显式地引用其他的类。首先，显示一个启动画面；然后，通过调用Class.forName手工地加载其他的类。

获得Class类对象的第三种方法非常简单。如果T是任意的Java类型（或void关键字)，T.class将代表匹配的类对象。例如：

```java
Classcll = Random.class; // if you import java.util
Class cl2 = int.class;
Class cl3 = Double[].class;
```

请注意， 一个Class对象实际上表示的是一个类型，而这个类型未必一定是一种类。例如，int不是类，但int.class是一个Class类型的对象。

> 注释：Class类实际上是一个泛型类。例如，Employee.class的类型是Class<Employee>。没有说明这个问题的原因是：它将已经抽象的概念更加复杂化了。在大多数实际问题中，可以忽略类型参数，而使用原始的Class类。有关这个问题更详细的论述请参看第8章

> 警告：鉴于历史原getName方法在应用于数组类型的时候会返回一个很奇怪的名字：
>
> - Double[]class.getName()返回“[Ljava.lang.Double;’’
> - int[].class.getName()返回“[I”，

虚拟机为每个类型管理一个Class对象。因此，可以利用==运算符实现两个类对象比较的操作。例如，

```java
if (e.getClass()==Employee.class) . . .
```

还有一个很有用的方法newlnstance( )， 可以用来动态地创建一个类的实例例如，

```java
e.getClass().newlnstance();
```

创建了一个与e具有相同类类型的实例。newlnstance方法调用默认的构造器（没有参数的构造器）初始化新创建的对象。如果这个类没有默认的构造器，就会抛出一个异常

将forName与newlnstance配合起来使用，可以根据存储在字符串中的类名创建一个对象

```java
String s = "java.util.Random";
Object m = Class.forName(s) .newlnstance();
```

> 注释：如果需要以这种方式向希望按名称创建的类的构造器提供参数，就不要使用上面那条语句，而必须使用Constructor类中的newlnstance方法。

> C++注释：newlnstance方法对应C++中虚拟构造器的习惯用法。然而，C++中的虚拟构造器不是一种语言特性，需要由专门的库支持。Class类与C++中的type_info类相似，getClass方法与C++中的typeid运算符等价。但Java中的Class比C++中的type_info的功能强。C++中的type_info只能以字符串的形式显示一个类型的名字，而不能创建那个类型的对象

### 1.2 从Class中获取信息

在java.lang.reflect包中有三个类Field、Method和Constructor分别用于描述类的域、方法和构造器。这三个类都有一个叫做getName的方法，用来返回项目的名称。Held类有一个getType方法，用来返回描述域所属类型的Class对象。Method和Constructor类有能够报告参数类型的方法，Method类还有一个可以报告返回类型的方法。这<个类还有一个叫做getModifiers的方法，它将返回一个整型数值，用不同的位开关描述public和static这样的修饰符使用状况。另外，还可以利用java.lang.reflect包中的Modifiei类的静态方法分析getModifiers返回的整型数值。例如，可以使用Modifier类中的isPublic、isPrivate或isFinal判断方法或构造器是否是public、private或final。我们需要做的全部工作就是调用Modifier类的相应方法，并对返回的整型数值进行分析，另外，还可以利用Modifier.toString方法将修饰符打印出来。

Class类中的getFields、getMethods和getConstructors方法将分别返回类提供的public域、方法和构造器数组，其中包括超类的公有成员。Class类的getDeclareFields、getDeclareMethods和getDeclaredConstructors方法将分别返回类中声明的全部域、方法和构造器，其中包括私有和受保护成员，但不包括超类的成员。

Class类提供了大量的实例方法来获取该Class对象所对应类的详细信息，Class类大致包含如下方法，下面每个方法都包括多个重载的版本。

```java
packagecom.test.reflection;

import java.lang.annotation.Annotation;
import java.lang.reflect.Constructor;
import java.lang.reflect.Field;
import java.lang.reflect.Method;
import java.lang.reflect.Type;

import com.test.annotation.MyField;
import com.test.annotation.MyMothed;
import com.test.annotation.MyParam;
import com.test.annotation.MyTable;
@SuppressWarnings("all")
public class TestReflection01 {
	public static void main(String[] args) throws NoSuchFieldException, SecurityException, NoSuchMethodException {
		
		try {
			Class clazz=Class.forName("com.test.reflection.User");
			
			/**
			 * 1,获取类名
			 */
			//getName()：获取全类名
			System.out.println(clazz.getName());
			//com.test.reflection.User
			
			
			//getSimpleName():获取类名（不包括包名）
			System.out.println(clazz.getSimpleName());
			//User
			
			//getCanonicalName():返回底层的规范名称，对于普通类和getName没区别，内部类或array有区别
			System.out.println(clazz.getCanonicalName());
			//com.test.reflection.User
			
			/**
			 * 1.1 类注解的获取
			 */
			//getAnnotation()：获取类的指定注解
			Annotation annotation = clazz.getAnnotation(MyTable.class);
			System.out.println("指定类注解"+annotation);
			
			//getAnnotations():获取类的所有注解
			Annotation[] annotations = clazz.getAnnotations();
			for(Annotation a:annotations){
				System.out.println("所有类注解"+a);
			}
			
			//返回直接存在于此元素上的所有注释,不包括继承的注解
			Annotation[] aaa = clazz.getDeclaredAnnotations();
			for(Annotation aasa:aaa){
				System.out.println(aasa);
			}
			
			//////////////////////////////////////////////////////////////////////////
			/**
			 * 2,属性的获取
			 */
			//getField：获取public修饰的制定属性，包括从父类继承的属性，没找到会抛出NoSuchFieldException
			System.out.println(clazz.getField("love"));
			
			//getFields:获取public修饰的属性，包括从父类继承的属性
			Field[] fields = clazz.getFields();
			for(Field f: fields){
				System.out.println(f);
			}
			
			//getDeclaredField():获取类所有所有修饰范围的制定属性属性
			Field declaredField = clazz.getDeclaredField("name");
			System.out.println(declaredField);
			
			//getDeclaredFields():获取类所有的属性
			Field[] declaredFields = clazz.getDeclaredFields();
			for(Field df:declaredFields){
				System.out.println(df);
			}
			
			/**
			 * 2.1属性注解的获取
			 */
			//getAnnotation():获取该属性的指定注解
			MyField myField = declaredField.getAnnotation(MyField.class);
			System.out.println(myField);
				//2.1.1获取注解的值
				System.out.println(myField.field());
			
			
			//getAnnotations():获取该属性所有注解
			Annotation[] annotations2 = declaredField.getAnnotations();
			for(Annotation a2:annotations2 ){
				System.out.println(a2);
			}
			
			//获取该属性所有注解，不包括继承的注解
			Annotation[] declaredAnnotations = declaredField.getDeclaredAnnotations();
			for(Annotation das2:declaredAnnotations){
				System.out.println(das2);
			}
			
			//////////////////////////////////////////////////////////////////////////
			
			/**
			 * 3,方法的获取
			 */
			//getMethod():制定public方法的获取,第一个参数为方法名，
			//第二个为方法参数类型，没找到会抛出NoSuchMethodException
			Method method = clazz.getMethod("setName", String.class);
			System.out.println(method);
			//com.test.reflection.User.setName(java.lang.String)
			
			//getMethods():返回所有public修饰的方法，包括父类中的方法
			Method[] methods = clazz.getMethods();
			for(Method m:methods){
				System.out.println(m);
			}
			
			//getDeclaredMethod():返回所有修饰范围的指定方法
			Method love = clazz.getDeclaredMethod("love",String.class);
			System.out.println(love);
			
			//getDeclaredMethods():返回所有关键词修饰的方法
			Method[] declaredMethods = clazz.getDeclaredMethods();
			for(Method dm:declaredMethods){
				System.err.println(dm);
			}
			/**
			 * 3.1 获取方法的注解
			 */
			//getAnnotation()://获取方法的指定注解
			MyMothed mothedAnnotation2 = method.getAnnotation(MyMothed.class);
			System.out.println(mothedAnnotation2);
			//获取方法注解的值
			System.out.println(mothedAnnotation2.value());
			
			//getAnnotations():获取方法的所有注解
			Annotation[] annotations3 = method.getAnnotations();
			for(Annotation a3:annotations3){
				System.out.println("+++++++++++"+a3);
			}
			
			//getDeclaredAnnotations():获取方法的所有注解不包括继承的注解
			Annotation[] declaredAnnotations2 = method.getDeclaredAnnotations();
			for(Annotation da2:declaredAnnotations2){
				System.out.println(da2);
			}
			//获取方法默认值
			System.out.println("获取方法默认值"+method.getDefaultValue());
			
			
			/**
			 * 3.2 获取方法的参数
			 */
			//getParameterTypes():获取方法所有参数类型
			Class<?>[] parameterTypes = method.getParameterTypes();
			for(Class<?> type:parameterTypes){
				System.out.println("获取方法所有参数类型"+type);
				//获取方法所有参数类型class java.lang.String
				//获取方法所有参数类型int
			}
			
			//getGenericParameterTypes():获取泛型参数类型
			Type[] genericParameterTypes = method.getGenericParameterTypes();
			for(Type t: genericParameterTypes){
				System.out.println(t);
				//class java.lang.String
				//int
			}
			
			//getParameterAnnotations():获取方法参数所有注解
			Annotation[][] pas = method.getParameterAnnotations();
			for(Annotation[] p:pas){
				for(Annotation s:p){
					System.out.println(s);
					//@com.test.annotation.MyParam(value=敏敏特穆尔)
				}
			}
			
			//////////////////////////////////////////////////////////////////////////
			/**
			 * 4,构造函数的获取
			 */
			//getConstructor():获取，public修饰的构造函数
			Constructor constructor = clazz.getConstructor();
			System.out.println(constructor);
			//com.test.reflection.User()
			
			//constructors():获取，public修饰的构造函数
			Constructor[] constructors = clazz.getConstructors();
			for(Constructor c:constructors){
				System.out.println(c);
			}
			
			//getDeclaredConstructor():返回所有关键词修饰的指定构造方法,参数为空时，访问无参构造
			Constructor declaredConstructor = clazz.getDeclaredConstructor();
			System.out.println(declaredConstructor);
			
			//getDeclaredConstructors():返回所有关键词修饰的所有构造方法,参数为空时，访问无参构造
			Constructor[] declaredConstructors = clazz.getDeclaredConstructors();
			for(Constructor dc:declaredConstructors){
				System.out.println(dc);
			}
			
			
		} catch (ClassNotFoundException e) {
			e.printStackTrace();
		}
	}
}
```

- **类中其他重要的方法**

| 方法                                                         | 用途                             |
| ------------------------------------------------------------ | -------------------------------- |
| isAnnotation()                                               | 如果是注解类型则返回true         |
| isAnnotationPresent(Class<? extends Annotation> annotationClass) | 如果是指定类型注解类型则返回true |
| isAnonymousClass()                                           | 如果是匿名类则返回true           |
| isArray()                                                    | 如果是一个数组类则返回true       |
| isEnum()                                                     | 如果是枚举类则返回true           |
| isInstance(Object obj)                                       | 如果obj是该类的实例则返回true    |
| isInterface()                                                | 如果是接口类则返回true           |
| isLocalClass()                                               | 如果是局部类则返回true           |
| isMemberClass()                                              | 如果是内部类则返回true           |

### 1.3 Java8 新增的方法参数反射

Java 8在java.lang.reflect包下新增了一个Executable抽象基类，该对象代表可执行的类成员，该类派生了Constructor、Method两个子类。

Executable基类提供了大量方法来获取修饰该方法或构造器的注解信息；还提供了isVarArgs()方法用于判断该方法或构造器是否包含数量可变的形参，以及通过getModifiers()方法来获取该方法或构造器的修饰符。除此之外，Executable提供了如下两个方法来获取该方法或参数的形参个数及形参名。

`int getParameterCount()`：获取该构造器或方法的形参个数。

`Parameter[] getParameters()`：获取该构造器或方法的所有形参。

上面第二个方法返回了一个Parameter[]数组，Parameter也是Java 8新增的API，每个Parameter对象代表方法或构造器的一个参数。Parameter也提供了大量方法来获取声明该参数的泛型信息。

- getModifiers():获取修饰该形参的修饰符
- String getName():获取形参名
- Type getParamterizedType():获取带泛型的形参类型
- Class<?> getType():获取形参类型
- boolean isNamePresent():该方法返回该类的class文件中是否包含了方法的形参名信息
- boolean isVarArgs():该方法用于判断该参数是否为个数可变的形参

需要指出的是，使用javac命令编译Java源文件时，默认生成的class文件并不包含方法的形参名信息，因此调用isNamePresent()方法会返回false，调用getName()方法也不能得到该参数的形参名。如果希望javac命令编译Java源文件时可以保留形参信息，则需要为该命令指定-parameters选项。

如下程序示范了Java 8中的参数反射功能

```java
import java.lang.reflect.*;
import java.util.*;
 
class Test {
	public void replace(String str, List<String> list){}
}
public class MethodParameterTest {
	public static void main(String[] args)throws Exception {
		// 获取String的类
		Class<Test> clazz = Test.class;
		// 获取String类的带两个参数的replace()方法
		Method replace = clazz.getMethod("replace", String.class, List.class);
		// 获取指定方法的参数个数
		System.out.println("replace方法参数个数：" + replace.getParameterCount());
		// 获取replace的所有参数信息
		Parameter[] parameters = replace.getParameters();
		int index = 1;
		// 遍历所有参数
		for (Parameter p : parameters) {
			if (p.isNamePresent()) {
				System.out.println("---第" + index++ + "个参数信息---");
				System.out.println("参数名：" + p.getName());
				System.out.println("形参类型：" + p.getType());
				System.out.println("泛型类型：" + p.getParameterizedType());
			}
		}
	}
}
```

```java
E:\Java\疯狂java讲义\codes\18\18.3>javac -parameters -d .MethodParameterTest.java
E:\Java\疯狂java讲义\codes\18\18.3>java MethodParameterTest
replace方法参数个数：2
---第1个参数信息---
参数名：str
形参类型：class java.lang.String
泛型类型：class java.lang.String
---第2个参数信息---
参数名：list
形参类型：interface java.util.List
泛型类型：java.util.List<java.lang.String>
```

## 2 利用反射生成并操作对象

### 2.1 创建对象

## 3 使用反射生成JDK动态代理

如果不懂动态代理就无法深入理解当下最流行的诸多框架的原理，如spring。首先

要理解动态代理首先要理解代理模式

**什么是代理模式？**

有一个打印机的类

```java
public class Printer {
	public void print(){
		System.out.println("打印！");
	}
}
```

如果想在打印之前先记录一下日志怎么做？

最简单的方法：在打印的功能前面直接加上记录日志的功能。

```java
public class Printer {
	public void print(){
		System.out.println("记录日志！");
		System.out.println("打印！");
	}
}
```

看上去好像没有问题，但是我们修改了打印机的源代码，**破坏了面向对象的开闭原则**，有可能影响到其它功能。怎么解决呢？很容易可以想到，既然不能修改原来的代码，那我新建一个类吧。

```java
public class LogPrinter extends Printer {
	public void print(){
		System.out.println("记录日志！");
		System.out.println("打印！");
	}
}
```

这个类继承了打印机的类，**重写了打印机的print方法，提供了记录日志的功能**，以后需要打印机的时候使用这个类就好。问题似乎得到了解决，我们可以在这个解决方案的基础上进一步的优化：

先抽象出一个接口:

```java
public interface IPrinter {
	void print();
}
```

打印机类实现这个接口:

```java
public class Printer implements IPrinter {
	public void print(){
		System.out.println("打印！");
	}
}
```

**创建打印机代理类**也实现该接口，在构造函数中将打印机对象传进去，实现接口的打印方法时调用打印机对象的打印方法并在前面加上记录日志的功能:

```java
public class PrinterProxy implements IPrinter {
	private IPrinter printer;
	public PrinterProxy(){
		this.printer = new printer();
	}
	@Override
	public void print() {
		System.out.println("记录日志");
		printer.print();
	}
}
```

实例：

```java
public class Test {
	public static void main(String[] args) {
		PrinterProxy proxy = new PrinterProxy();
		proxy.print();
	}
}
```

结果出来了：

```text
记录日志
打印
```

**以后我们就可以直接实例化PrinterProxy对象调用它的打印方法了，这就是静态代理模式，通过抽象出接口让程序的扩展性和灵活性更高了。**

但是静态代理并不是完美无缺的，如果我的打印机类中还有别的方法，也需要加上记录日志的功能，就不得不将记录日志的功能写n遍。进一步如果我还有电视机，电冰箱的类里面的所有方法也需要加上记录日志的功能，那要重复的地方就更多了。

解决的方式就是使用动态代理，要想不重复写记录日志的功能，**针对每一个接口实现一个代理类的做法肯定不可行了**，可不可以让这些代理类的对象**自动生成**呢？

JDK提供了**invocationHandler**接口和**Proxy**类，借助这两个工具可以达到我们想要的效果。

invocationHandler接口上场：

```java
//Object proxy:被代理的对象 
//Method method:要调用的方法 
//Object[] args:方法调用时所需要参数 
public interface InvocationHandler {
	public Object invoke(Object proxy, Method method, Object[] args) throws Throwable;
}
```

接口里只有一个方法invoke，这个方法非常重要，先混个脸熟，稍后解释。

Proxy类上场，它里面有一个很重要的方法newProxyInstance：

```java
//CLassLoader loader:被代理对象的类加载器 
//Class<?> interfaces:被代理类全部的接口 
//InvocationHandler h:实现InvocationHandler接口的对象 
public static Object newProxyInstance(ClassLoader loader, Class<?>[] interfaces, InvocationHandler h) throws IllegalArgumentException 
```

**调用Proxy的newProxyInstance方法可以生成代理对象**

一切准备就绪动态代理模式千呼万唤始出来：

接口IPrinter和该接口的实现类Printer的代码同前。

*实现一个类，该类用来创建代理对象，***它实现了InvocationHandler接口**：

```java
public class ProxyHandler implements InvocationHandler {
	private Object targetObject;//被代理的对象
	//将被代理的对象传入获得它的类加载器和实现接口作为Proxy.newProxyInstance方法的参数。
	public  Object newProxyInstance(Object targetObject){
		this.targetObject = targetObject;
		//targetObject.getClass().getClassLoader()：被代理对象的类加载器
		//targetObject.getClass().getInterfaces()：被代理对象的实现接口
		//this 当前对象，该对象实现了InvocationHandler接口所以有invoke方法，通过invoke方法可以调用被代理对象的方法
		return Proxy.newProxyInstance(targetObject.getClass().getClassLoader(),targetObject.getClass().getInterfaces(),this);
	}
	//该方法在代理对象调用方法时调用
	@Override
	public Object invoke(Object proxy, Method method, Object[] args) throws Throwable {
		System.out.println("记录日志");
		return method.invoke(targetObject,args);
	}
}
```

被代理的对象targetObject可以通过方法参数传进来：

```java
public Object newProxyInstance(Object targetObject){
	this.targetObject=targetObject;
```

我们重点来分析一下这段代码：

```java
return Proxy.newProxyInstance(targetObject.getClass().getClassLoader(),targetObject.getClass().getInterfaces(),this);
```

**动态代理对象就是通过调用这段代码被创建并返回的**。

方法有三个参数：

第一个参数：

 targetObject.getClass().getClassLoader()：targetObject对象的类加载器。

第二个参数:

targetObject.getClass().getInterfaces()：targetObject对象的所有接口

第三个参数:

this：也就是当前对象即实现了InvocationHandler接口的类的对象，**在调用方法时会调用它的invoke方法。**

再来看一下这段代码：

```java
public Object invoke(Object proxy, Method method, Object[] args) throws Throwable {
	//在这里可以通过判断方法名来决定执行什么功能
	System.out.println("记录日志");
	//调用被代理对象的方法
	return method.invoke(targetObject, args);
}
```

这个方法就是生成的代理类中的方法被调用时会去自动调用的方法，可以看到在这个方法中调用了被代理对象的方法: method.invoke(targetObject, args);

**我们可以在这里加上需要的业务逻辑，比如调用方法前记录日志功能.**

见证奇迹的时刻到了：

```java
public class Test {
	public static void main(String[] args){
		ProxyHandler proxyHandler=new ProxyHandler();
		IPrinter printer=(IPrinter)proxyHandler.newProxyInstance(new Printer());
		printer.print();
	}
}
```

打印结果：

```text
记录日志
打印
```

当执行printer.print();时会自动调用invoke方法，很多初学者不理解为什么能调用这个方法，回忆一下创建代理对象的时候是通过

```text
return Proxy.newProxyInstance(targetObject.getClass().getClassLoader(),targetObject.getClass().getInterfaces(),this);
```

来创建的，方法的第三个参数this是实现了InvocationHandler接口的对象，InvocationHandler接口有invoke方法。

将被代理的对象作为参数传入就可以执行里面的任意方法，所有的方法调用都通过invoke来完成。不用对每个方法进行处理，动态代理是不是很简洁。

**代理模式的定义：**代理模式给某一个对象提供一个代理对象，并由代理对象控制对原对象的引用，通俗的来讲代理模式就是我们生活中常见的中介**，动态代理和静态代理的区别在于静态代理我们需要手动的去实现目标对象的代理类，而动态代理可以在运行期间动态的生成代理类。**