Java基础知识笔记-18-类加载机制与反射

## 1 类的加载，连接和初始化

### 1.1 JVM和类

当调用Java命令运行某个Java程序时，该命令将会启动一个Java虚拟机进程，不管该Java程序有多么复杂，当程序启动了多少个线程，他们都处于该Java虚拟机进程里。正如前面介绍的，同一个JVM的所有线程，所有变量都处于同一个进程里，他们都使用该JVM进程的内存区。当系统出现以下几种情况时，JVM进程将被终止。

- 程序运行到最后正常结束
- 程序运行到使用`System.exit()`或者`Runtime.getRumtime().exit()`代码处结束程序。
- 程序执行过程中遇到未捕获的异常或错误而结束
- 程序所在平台强制结束了JVM进程

下面以类的类变量来说明这个问题。下面程序先定义了一个包含类变量的类

```java
//A.java
public class A {
	//定义该类的类变量
	public static int a=6;
}
```

上面程序中的粗体字代码定义了一个类变量a，接下来定义一个类创建A类的实例，并访问A对象的类变量a

```java
//ATest1.java
public class ATest1 {
	public static void main(String[] args) {
		//创建A类的实例
		A a=new A();
		//让a实例的类变量a的值自加
		a.a++;
		System.out.println(a.a);
	}
}
```

下面程序也创建A对象，并访问其类变量a的值

```java
//ATest.java
public class ATest2 {
	public static void main(String[] args) {
		//创建A类的实例
		A b=new A();
		//输出b实例的类变量a的值
		System.out.println(b.a);
	}
}
```

两个结果第一个输出7第二个输出6，两次运行是两次运行JVM进程，第一次运行JVM结束后，他对A类所做的修改会全部丢失---第二次运行JVM会再次初始化A类

### 1.2 类的加载

当程序主动使用某个类时，如果该类还未被加载到内存中，则系统会通过加载，连接，初始化三个步骤来对该类进行初始化。如果没有意外，JVM将连续完成这三个步骤，所以有时也把这三个步骤统称为类加载或者类初始化。

类加载指的是将类的class文件读入内存，并为之创建一个`java.lang.Class`对象，也就是说，当程序中使用任何类时，系统都会为之建立一个`java.lang.Class`对象

类的加载由类加载器完成，类加载器通常由JVM提供，这些类加载器也是前面所有程序运行的基础，JVM提供的这些类加载器通常被称为系统类加载器。除此之外，开发者还可以通过继承ClassLoader基类来创建自己的类加载器。

通过使用不同的类加载器，可以从不同来源加载类的二进制数据，通常有如下几种来源。

- 从本地文件系统加载class文件，这是前面绝大部分示例程序的类加载方式。
- 从JAR包加载class文件
- 通过网络加载class文件
- 把一个Java源文件动态编译，并执行加载

类加载器同城无须等到“首次使用”该类时才加载该类，Java虚拟机规范允许系统预先加载某些类

### 1.3 类的连接

当类被加载之后，系统为之生成一个对应的Class对象，接着将会进入连接阶段，连接阶段负责把类的二进制数据合并到JRE中，类连接又分为如下三个阶段

- 验证：验证阶段用于检验被加载的类是否有正确的内部结构，并和其他类协调一致
- 准备：类准备阶段则负责为类的类变量分配内存，并设置默认初始值
- 解析：将类的二进制数据中的符号引用替换成直接引用

### 1.4 类的初始化

在类的初始化阶段，虚拟机负责对类进行初始化，主要就是对类变量进行初始化。在Java类中对类变量指定初始值有两种方式：①声明类变量指定初始值；②使用静态初始化块为类变量指定初始值。例如下面代码片段。

```java
public class Test {
	//声明变量a时指定初始值
	static int a=5;
	static int b;
	static int c;
	static{
		//使用静态初始化块为变量b指定初始值
		b=6;
	}
	...
}
```

对于上面代码，程序为类变量a，b都显式指定了初始值，5，6，但是类变量c则没有指定初始值，他将采用默认初始值0。

声明变量时指定初始值，静态初始化块都将被当成类的初始化语句，JVM会按这些语句在程序中的顺序依次执行它们，例如下面的类。

```java
Test.java
public class Test {
	static {
		//使用静态初始化块为变量b指定初始值
		b=6;
		System.out.println("--------");
	}
	//声明变量a时指定初始值
	static int a=5;
	static int b=9;
	static int c;
	public static void main(String[] args){
		System.out.println(Test.b);
	}
}
```

上面的代码先在静态初始化块中为b变量赋值，此时类变量b的值为6；接着程序向下执行，直到程序再次为变量b赋值。最后变量b的值为9。

JVM初始化一个类包含如下几个步骤

1. 假如这个类还没有被加载和连接，则程序先加载并连接该类
2. 假如该类的直接父类还没有被初始化，则先初始化其直接父类
3. 假如类中有初始化语句，则系统依次执行这些初始化语句

当执行第2个步骤时，系统对直接父类的初始化步骤也遵循1-3，如果又有直接父类，再重复1-3步骤，确保要使用类的所有父类（直接父类或者间接父类）都被初始化。

### 1.5 类初始化的时机

**当Java程序首次通过下面6种方式来使用某个类或接口时，系统就会初始化该类或接口**

- 创建类的实例。为某个类创建实例的方法包括：使用new操作符来创建实例，通过反射来创建实例，通过反序列化的方式来创建实例
- 调用某个类的类方法（静态方法）
- 访问某个类或接口的类变量，或者为该类变量赋值
- 使用反射方式来强制创建某个类或接口对应的java.lang.Class对象
- 初始化某个类的子类。
- 直接使用java.exe命令来运行某个主类。当运行某个主类时，程序会先初始化该主类

除此之外，下面的几种情形需要特别指出

对于一个final型的类变量，如果该类变量的值在编译时就可以确定下来，那么这个类变量相当于“宏变量”。Java编译器会在编译时直接把这个类变量出现的地方替换成它的值，因此即使程序使用该静态类变量，也不会导致类的初始化。如下示例程序

```java
class MyTest {
	static {
		System.out.println("静态初始化块...");
	}
	//使用一个字符串直接量为static final的类变量赋值
	static final String compileConstant="疯狂Java讲义";
}
public class CompileConstantTest {
	public static void main(String[] args) {
		//访问，输出MyTest中的compileConstant类变量
		System.out.println(MyTest.compileConstant); //①
	}
}
```

上面程序的MyTest类章有一个compileConstant的类变量，该变量使用了final修饰，而且它的值可以在编译时确定下来，因此compileConstant会被当成“宏变量”处理。程序中所有使用compileConstant的地方都会被直接替换成它的值---也就是说，上面程序中①处的代码在编译时就会被替换成“疯狂Java讲义”，所有①行代码不会导致初始化MyTest类。

反之，如果final修饰的类变量不能在编译时确定下来，则必须等到运行时才可以确定该类变量的值，如果通过该类来访问它的类变量，就会导致该类被初始化。例如将上面程序中定义compileConstant的代码改为如下

```java
//采用系统当前时间为static final类变量赋值
static final String compileConstant=System.currentTimeMills()+"";
```

因为上面定义的cimpileConstant类变量的值必须在运行时才可以确定，所以①处的粗体字代码必须保留对MyTest类的类变量的引用，这行代码就变成了使用MyTest的类变量，这将导致MyTest类被初始化。

当使用ClassLoader类的`loadClass()`方法来加载某个类时，该方法只是加载该类，并不会执行该类的初始化。使用Class的`forName()`静态方法才会导致强制初始化该类。例如以下代码：

```java
class Tester {
	static {
		Systen.out.println("Tester类的静态初始化块...");
	}
	public class ClassLoaderTest {
		public static void main(String[] args) throws ClassNotFoundException {
			ClassLoader cl=ClassLoader.getSystemClassLoader();
			//下面语句仅仅是加载Tester类
			cl.loadClass("Tester");
			System.out.println("系统加载Tester类");
			//下面语句才会初始化Tester类
			Class.forName("Tester");
		}
	}
}
```

## 2 类加载器

加载指的是将类的class文件读入到内存，并为之创建一个java.lang.Class对象，也就是说，当程序中使用任何类时，系统都会为之建立一个java.lang.Class对象。

### 2.1 类加载器简介

类的加载由类加载器完成，类加载器通常由JVM提供，这些类加载器也是前面所有程序运行的基础，JVM提供的这些类加载器通常被称为系统类加载器。除此之外，开发者可以通过继承ClassLoader基类来创建自己的类加载器。

通过使用不同的类加载器，可以从不同来源加载类的二进制数据，通常有如下几种来源。

- 从本地文件系统加载class文件，这是前面绝大部分示例程序的类加载方式。

- 从JAR包加载class文件，这种方式也是很常见的，前面介绍JDBC编程时用到的数据库驱动类就放在JAR文件中，JVM可以从JAR文件中直接加载该class文件。
- 通过网络加载class文件。
- 把一个Java源文件动态编译，并执行加载。类加载器通常无须等到“首次使用”该类时才加载该类，Java虚拟机规范允许系统预先加载某些类。

正如一个对象有一个唯一的标识一样，一个载入JVM的类也有一个唯一的标识。**在Java中一个类用其全限定类名（包括包名和类名）作为标识；但在JVM中，一个类用其全限定类名和其类加载器作为其唯一标识。**

例如，如果在pg的包中有一个名为p的类，被类加载器ClassLoader的实例kl负责加载，则该p类对应的Class对象在JVM中表示为（p,pg,kl）。这意味着两个类加载器加载的同名类（p,pg,kl）和（p,pg,kl2）是不同的，他们所加载的类也是完全不同，互不兼容的。

即便一个最简单的helloworld程序：

```csharp
public class SayHello {
	public void justSayHello(){
		String str = "hello world!";
		System.out.println(str);
	}

	public static void main(String[] args){
		SayHello instance = new SayHello();
		instance.justSayHello();
	}
}
```

也会用到至少3个类加载器实例：

- 根类加载器（Bootstrap ClassLoader）负责加载Java的核心类，非常特殊，**它不是java.lang.ClassLoader的子类，而是由JVM自身实现的**
- 拓展类加载器（Extension ClassLoader）负责加载JRE的扩展目录（%JAVA_HOME%/jre/lib/ext或者由java.ext.dirs系统属性指定的目录）中JAR包的类
- 应用类加载器（Application ClassLoader）负责在JVM启动时加载来自java命令的-classpath选项，java.class.path系统属性，或CLASSPATH环境变量所指定的JAR包和类路劲。程序可以通过ClassLoader的静态方法getSystemClassLoader()来获取系统类加载器。如果没有特别指定，则用户自定义的类加载器都以类加载器作为父加载器。

### 2.2 类加载机制

- 全盘负责：所谓全盘负责，就是当一个类加载器负责加载某个Class时，该Class所依赖和引用其他Class也将由该类加载器负责载入，除非显示使用另外一个类加载器来载入。
- 双亲委派：所谓的双亲委派，则是先让父类加载器试图加载该Class，只有在父类加载器无法加载该类时才尝试从自己的类路径中加载该类。通俗的讲，就是某个特定的类加载器在接到加载类的请求时，首先将加载任务委托给父加载器，依次递归，如果父加载器可以完成类加载任务，就成功返回；只有父加载器无法完成此加载任务时，才自己去加载。
- 缓存机制。缓存机制将会保证所有加载过的Class都会被缓存，当程序中需要使用某个Class时，类加载器先从缓存区中搜寻该Class，只有当缓存区中不存在该Class对象时，系统才会读取该类对应的二进制数据，并将其转换成Class对象，存入缓冲区中。这就是为很么修改了Class后，必须重新启动JVM，程序所做的修改才会生效的原因。

系统类加载器AppClassLoader的实例，扩展类加载器是ExtClassLoader的实例，这两个都是URLClassLoader的实例。

### 2.4 URLClassLoader类

Java为ClassLoader提供了一个URLClassLoader实现类，该类也是系统类加载器和扩展类加载器的父类（此处是父类，而不是父类加载器，这里是类与类之间的继承关系），URLClassLoader功能比较强大，它既可以从本地文件系统获取二进制文件来加载类，也可以从远程主机获取二进制文件来加载类。

实际上应用程序中可以直接使用URLClassLoader来加载类，URLClassLoader类提供了如下两个构造器：

- `URLClassLoader(URL[] urls)`：使用默认的父类加载器创建一个ClassLoader对象，该对象将从urls所指定的系列路径来查询、并加载类。

- `URLClassLoader(URL[] urls, ClassLoader parent)`：使用指定的父类加载器创建一个ClassLoader对象，其他功能前一个构造器相同。

二 实战

1 代码

```java
import java.sql.*;
import java.util.*;
import java.net.*;

public class URLClassLoaderTest
{
	private static Connection conn;
	// 定义一个获取数据库连接方法
	public static Connection getConn(String url ,
		String user , String pass) throws Exception {
		if (conn == null)
		{
			// 创建一个URL数组
			URL[] urls = {new URL("file:mysql-connector-java-5.1.30-bin.jar")};
			// 以默认的ClassLoader作为父ClassLoader，创建URLClassLoader
			URLClassLoader myClassLoader = new URLClassLoader(urls);
			// 加载MySQL的JDBC驱动，并创建默认实例
			Driver driver = (Driver)myClassLoader.loadClass("com.mysql.jdbc.Driver").newInstance();
			// 创建一个设置JDBC连接属性的Properties对象
			Properties props = new Properties();
			// 至少需要为该对象传入user和password两个属性
			props.setProperty("user" , user);
			props.setProperty("password" , pass);
			// 调用Driver对象的connect方法来取得数据库连接
			conn = driver.connect(url , props);
		}
		return conn;
	}
	public static void main(String[] args)throws Exception {
		System.out.println(getConn("jdbc:mysql://localhost:3306/mysql" , "root" , "123456"));
	}
}
```
2 运行
```java
E:\Java\疯狂java讲义\codes\18\18.2>java URLClassLoaderTest
com.mysql.jdbc.JDBC4Connection@3d646c37
```

## 3 通过反射查看类信息

Java程序中的许多对象在运行时都会出现两种类型：编译时类型和运行时类型，例如代码Person p=new Student();这行代码将会生成一个p变量，该变量的编译时类型为Person，运行时类型为Student；除此之外，还有更极端的情形，程序在运行时接收到外部传入的一个对象，该对象的编译时类型为Object，但程序又需要调用该对象运行时类型的方法。

为了解决这些问题，程序需要在运行时发现对象和类的真实信息，解决方法有以下两种：

- 第一种做法是假设在编译时和运行时发现对象和类的真实信息。在这种情况下，可以先使用instanceof运算符进行判断

> instanceof主要用来判断一个类是否实现了某个接口，或者判断一个实例对象是否属于一个类。
>
> 该运算符是二目运算符，左边的操作元是一个对象，右边是一个类，当左边的对象是右边的类或子类创建的对象时，该运算符运算的结果是true,否则是false。
>
> 例：
>
> ```java
> class instanceOfDemo{
> 	public static void main(String []args){
> 		instanceOfDemo t=new instanceOfDemo();
> 		if(t instanceof instanceOfDemo){
> 			System.out.println("t是instanceOfDemo的实例");
> 		}
> 	}
> }
> ```

### 3.1 获得Class对象

每个类被加载后，系统就会为该类生成一个对应的Class对象，通过该Class对象就可以访问到JVM中的这个类。在Java程序中获得Class对象通常有下列四种方式：

- 使用Class类的forName(String className) 静态方法，该静态方法需要传入类的全限定名称字符串。
- 调用某个类的class属性来获得该类对应的class对象。
- 调用某个对象的getClass()方法。
- 调用ClassLoader实例对象的loadClass方法。

### 3.2 从Class中获取信息

Class类提供了大量的实例方法来获取该Class对象所对应类的详细信息，Class类大致包含如下方法，下面每个方法都包括多个重载的版本。

```java
package com.test.reflection;

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
			
			////////////////////////////////////////////////////////////////////////////////////////////
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
			
			//////////////////////////////////////////////////////////////////////////////////////////
			
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
			
			////////////////////////////////////////////////////////////////////////////////////////////
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

### 3.3 Java8新增的方法参数反射

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

## 4 利用反射生成并操作对象

### 4.1 创建对象

## 5 使用反射生成JDK动态代理

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