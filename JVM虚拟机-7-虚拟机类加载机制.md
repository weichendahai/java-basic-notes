# 7 虚拟机类加载机制

代码编译的结果从本地机器码转变为字节码，是存储格式发展的一小步，却是编程语言发展的一
大步。
## 7.1 概述
Java虚拟机把描述类的数据从Class文件加载到内存， 并对数据进行校验、 转换解析和初始化， 最终形成可以被虚拟机直接使用的Java类型， 这个过程被称作虚拟机的类加载机制。 与那些在编译时需要进行连接的语言不同， 在Java语言里面， 类型的加载、 连接和初始化过程都是在程序运行期间完成的， 这种策略让Java语言进行提前编译会面临额外的困难， 也会让类加载时稍微增加一些性能开销，但是却为Java应用提供了极高的扩展性和灵活性， Java天生可以动态扩展的语言特性就是依赖运行期动态加载和动态连接这个特点实现的。 例如， 编写一个面向接口的应用程序， 可以等到运行时再指定其实际的实现类， 用户可以通过Java预置的或自定义类加载器， 让某个本地的应用程序在运行时从网络或其他地方上加载一个二进制流作为其程序代码的一部分。 这种动态组装应用的方式目前已广泛应用于Java程序之中， 从最基础的Applet、 JSP到相对复杂的OSGi技术， 都依赖Java语言运行期类加载才得以诞生。

为了避免语言表达中可能产生的偏差， 在正式开始本章以前， 笔者先设立两个语言上的约定：

第一， 在实际情况中， 每个Class文件都有代表着Java语言中的一个类或接口的可能， 后文中直接对“类型”的描述都同时蕴含着类和接口的可能性， 而需要对类和接口分开描述的场景， 笔者会特别指明；

第二， 与前面介绍Class文件格式时的约定一致， 本章所提到的“Class文件”也并非特指某个存在于具体磁盘中的文件， 而应当是一串二进制字节流， 无论其以何种形式存在， 包括但不限于磁盘文件、网络、 数据库、 内存或者动态产生等。
## 7.2 类加载的时机

一个类型从被加载到虚拟机内存中开始， 到卸载出内存为止， 它的整个生命周期将会经历加载（Loading） 、 验证（Verification） 、 准备（Preparation） 、 解析（Resolution） 、 初始化（Initialization） 、 使用（Using） 和卸载（Unloading） 七个阶段， 其中验证、 准备、 解析三个部分统称为连接（Linking） 。 这七个阶段的发生顺序如图7-1所示。  

加载、 验证、 准备、 初始化和卸载这五个阶段的顺序是确定的， 类型的加载过程必须按照这种顺序按部就班地开始， 而解析阶段则不一定： 它在某些情况下可以在初始化阶段之后再开始，这是为了支持Java语言的运行时绑定特性（也称为动态绑定或晚期绑定） 。 请注意， 这里笔者写的是按部就班地“开始”， 而不是按部就班地“进行”或按部就班地“完成”， 强调这点是因为这些阶段通常都是互相交叉地混合进行的， 会在一个阶段执行的过程中调用、 激活另一个阶段。关于在什么情况下需要开始类加载过程的第一个阶段“加载”， 《Java虚拟机规范》 中并没有进行强制约束， 这点可以交给虚拟机的具体实现来自由把握。 但是对于初始化阶段， 《Java虚拟机规范》则是严格规定了有且只有六种情况必须立即对类进行“初始化”（而加载、 验证、 准备自然需要在此之前开始） ：

1） 遇到new、 getstatic、 putstatic或invokestatic这四条字节码指令时， 如果类型没有进行过初始化， 则需要先触发其初始化阶段。 能够生成这四条指令的典型Java代码场景有：

- 使用new关键字实例化对象的时候。
- **读取或设置一个类型的静态字段（被final修饰、 已在编译期把结果放入常量池的静态字段除外）
  的时候。**
- 调用一个类型的静态方法的时候。

2） 使用java.lang.reflect包的方法对类型进行反射调用的时候， 如果类型没有进行过初始化， 则需要先触发其初始化。

3） 当初始化类的时候， 如果发现其父类还没有进行过初始化， 则需要先触发其父类的初始化。

4） 当虚拟机启动时， 用户需要指定一个要执行的主类（ 包含main()方法的那个类） ， 虚拟机会先初始化这个主类。

5） 当使用JDK 7新加入的动态语言支持时， 如果一个java.lang.invoke.MethodHandle实例最后的解析结果为REF_getStatic、 REF_putStatic、 REF_invokeStatic、 REF_newInvokeSpecial四种类型的方法句柄， 并且这个方法句柄对应的类没有进行过初始化， 则需要先触发其初始化。

6） 当一个接口中定义了JDK 8新加入的默认方法（ 被default关键字修饰的接口方法） 时， 如果有这个接口的实现类发生了初始化， 那该接口要在其之前被初始化。

对于这六种会触发类型进行初始化的场景， 《Java虚拟机规范》 中使用了一个非常强烈的限定语——“有且只有”， 这六种场景中的行为称为对一个类型进行主动引用。 除此之外， 所有引用类型的方式都不会触发初始化， 称为被动引用。 下面举三个例子来说明何为被动引用， 分别见代码清单7-1、 代码清单7-2和代码清单7-3。  

> 被动引用示例1

```java
package org.fenixsoft.classloading;
/**
* 被动使用类字段演示一：
* 通过子类引用父类的静态字段， 不会导致子类初始化
**/
public class SuperClass {
	static {
		System.out.println("SuperClass init!");
	} 
    public static int value = 123;
}
public class SubClass extends SuperClass {
	static {
		System.out.println("SubClass init!");
	}
} 
/**
* 非主动使用类字段演示
**/
public class NotInitialization {
	public static void main(String[] args) {
		System.out.println(SubClass.value);
	}
}
```

上述代码运行之后， 只会输出“SuperClass init！ ”， 而不会输出“SubClass init！ ”。 对于静态字段，只有直接定义这个字段的类才会被初始化， 因通过其子类来引用父类中定义的静态字段， 只会触发父类的初始化而不会触发子类的初始化。 至于是否要触发子类的加载和验证阶段， 在《Java虚拟机规范》 中并未明确规定， 所以这点取决于虚拟机的具体实现。 对于HotSpot虚拟机来说， 可通过-XX：+TraceClassLoading参数观察到此操作是会导致子类加载的。

> 被动引用示例2

```java
package org.fenixsoft.classloading;
/**
* 被动使用类字段演示二：
* 通过数组定义来引用类， 不会触发此类的初始化
**/
public class NotInitialization {
	public static void main(String[] args) {
		SuperClass[] sca = new SuperClass[10];
	}
}
```

为了节省版面， 这段代码复用了代码清单7-1中的SuperClass， 运行之后发现没有输出“SuperClass init！ ”， 说明并没有触发类org.fenixsoft.classloading.SuperClass的初始化阶段。 但是这段代码里面触发了另一个名为“Lorg.fenixsoft.classloading.SuperClass”的类的初始化阶段， 对于用户代码来说， 这并不是一个合法的类型名称， 它是一个由虚拟机自动生成的、 直接继承于java.lang.Object的子类， 创建动作由字节码指令newarray触发。

这个类代表了一个元素类型为org.fenixsoft.classloading.SuperClass的一维数组， 数组中应有的属性和方法（用户可直接使用的只有被修饰为public的length属性和clone()方法） 都实现在这个类里。 Java语言中对数组的访问要比C/C++相对安全， 很大程度上就是因为这个类包装了数组元素的访问[1]， 而C/C++中则是直接翻译为对数组指针的移动。 在Java语言里， 当检查到发生数组越界时会抛出java.lang.ArrayIndexOutOfBoundsException异常， 避免了直接造成非法内存访问。  

> 被动引用示例3

```java
package org.fenixsoft.classloading;
/**
* 被动使用类字段演示三：
* 常量在编译阶段会存入调用类的常量池中， 本质上没有直接引用到定义常量的类， 因此不会触发定义常量的
类的初始化
**/
public class ConstClass {
	static {
		System.out.println("ConstClass init!");
	} 
    public static final String HELLOWORLD = "hello world";
}
/**
* 非主动使用类字段演示
**/
public class NotInitialization {
	public static void main(String[] args) {
		System.out.println(ConstClass.HELLOWORLD);
	}
}
```

上述代码运行之后， 也没有输出“ConstClass init！ ”， 这是因为虽然在Java源码中确实引用了ConstClass类的常量HELLOWORLD， 但其实在编译阶段通过常量传播优化， 已经将此常量的值“hello world”直接存储在NotInitialization类的常量池中， 以后NotInitialization对常量ConstClass.HELLOWORLD的引用， 实际都被转化为NotInitialization类对自身常量池的引用了。 也就是说， 实际上NotInitialization的Class文件之中并没有ConstClass类的符号引用入口， 这两个类在编译成Class文件后就已不存在任何联系了。

接口的加载过程与类加载过程稍有不同， 针对接口需要做一些特殊说明： 接口也有初始化过程，这点与类是一致的， 上面的代码都是用静态语句块“static{}”来输出初始化信息的， 而接口中不能使用“static{}”语句块， 但编译器仍然会为接口生成“<clinit>()”类构造器[2]， 用于初始化接口中所定义的成员变量。 接口与类真正有所区别的是前面讲述的六种“有且仅有”需要触发初始化场景中的第三种：当一个类在初始化时， 要求其父类全部都已经初始化过了， 但是一个接口在初始化时， 并不要求其父接口全部都完成了初始化， 只有在真正使用到父接口的时候（如引用接口中定义的常量） 才会初始化。

[1] 准确地说， 越界检查不是封装在数组元素访问的类中， 而是封装在数组访问的xaload、 xastore字节
码指令中。

[2] 关于类构造器＜clinit＞()和方法构造器＜init＞()的生成过程和作用， 可参见第10章的相关内容。

## 7.3 类的加载过程

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

### 7.3.1 加载

“加载”（Loading） 阶段是整个“类加载”（Class Loading） 过程中的一个阶段， 希望读者没有混淆这两个看起来很相似的名词。 在加载阶段， Java虚拟机需要完成以下三件事情：

1） 通过一个类的全限定名来获取定义此类的二进制字节流。

2） 将这个字节流所代表的静态存储结构转化为方法区的运行时数据结构。

3） 在内存中生成一个代表这个类的java.lang.Class对象， 作为方法区这个类的各种数据的访问入口。

《Java虚拟机规范》 对这三点要求其实并不是特别具体， 留给虚拟机实现与Java应用的灵活度都是相当大的。 例如“通过一个类的全限定名来获取定义此类的二进制字节流”这条规则， 它并没有指明二进制字节流必须得从某个Class文件中获取， 确切地说是根本没有指明要从哪里获取、 如何获取。 仅仅这一点空隙， Java虚拟机的使用者们就可以在加载阶段搭构建出一个相当开放广阔的舞台， Java发展历程中， 充满创造力的开发人员则在这个舞台上玩出了各种花样， 许多举足轻重的Java技术都建立在这一基础之上， 例如：

- 从ZIP压缩包中读取， 这很常见， 最终成为日后JAR、 EAR、 WAR格式的基础。
- 从网络中获取， 这种场景最典型的应用就是Web Applet。
- 运行时计算生成， 这种场景使用得最多的就是动态代理技术， 在java.lang.reflect.Proxy中， 就是用了ProxyGenerator.generateProxyClass()来为特定接口生成形为“*$Proxy”的代理类的二进制字节流。
- 由其他文件生成， 典型场景是JSP应用， 由JSP文件生成对应的Class文件。
- 从数据库中读取， 这种场景相对少见些， 例如有些中间件服务器（如SAP Netweaver） 可以选择
  把程序安装到数据库中来完成程序代码在集群间的分发。
- 可以从加密文件中获取， 这是典型的防Class文件被反编译的保护措施， 通过加载时解密Class文
  件来保障程序运行逻辑不被窥探。
- ……

相对于类加载过程的其他阶段， 非数组类型的加载阶段（准确地说， 是加载阶段中获取类的二进制字节流的动作） 是开发人员可控性最强的阶段。 加载阶段既可以使用Java虚拟机里内置的引导类加载器来完成， 也可以由用户自定义的类加载器去完成， 开发人员通过定义自己的类加载器去控制字节流的获取方式（重写一个类加载器的findClass()或loadClass()方法） ， 实现根据自己的想法来赋予应用程序获取运行代码的动态性。

对于数组类而言， 情况就有所不同， 数组类本身不通过类加载器创建， 它是由Java虚拟机直接在内存中动态构造出来的。 但数组类与类加载器仍然有很密切的关系， 因为数组类的元素类型（ElementType， 指的是数组去掉所有维度的类型） 最终还是要靠类加载器来完成加载， 一个数组类（ 下面简称为C） 创建过程遵循以下规则：

- 如果数组的组件类型（ Component Type， 指的是数组去掉一个维度的类型， 注意和前面的元素类型区分开来） 是引用类型， 那就递归采用本节中定义的加载过程去加载这个组件类型， 数组C将被标识在加载该组件类型的类加载器的类名称空间上（ 这点很重要， 在7.4节会介绍， 一个类型必须与类加载器一起确定唯一性）。
- 如果数组的组件类型不是引用类型（ 例如int[]数组的组件类型为int） ， Java虚拟机将会把数组C标记为与引导类加载器关联。
- 数组类的可访问性与它的组件类型的可访问性一致， 如果组件类型不是引用类型， 它的数组类的可访问性将默认为public， 可被所有的类和接口访问到。

加载阶段结束后， Java虚拟机外部的二进制字节流就按照虚拟机所设定的格式存储在方法区之中了， 方法区中的数据存储格式完全由虚拟机实现自行定义， 《Java虚拟机规范》 未规定此区域的具体数据结构。 类型数据妥善安置在方法区之后， 会在Java堆内存中实例化一个java.lang.Class类的对象，这个对象将作为程序访问方法区中的类型数据的外部接口。

加载阶段与连接阶段的部分动作（ 如一部分字节码文件格式验证动作） 是交叉进行的， 加载阶段尚未完成， 连接阶段可能已经开始， 但这些夹在加载阶段之中进行的动作， 仍然属于连接阶段的一部分， 这两个阶段的开始时间仍然保持着固定的先后顺序。  

### 7.3.2 类的连接

当类被加载之后，系统为之生成一个对应的Class对象，接着将会进入连接阶段，连接阶段负责把类的二进制数据合并到JRE中，类连接又分为如下三个阶段

- 验证：验证是连接阶段的第一步， 这一阶段的目的是确保Class文件的字节流中包含的信息符合《Java虚拟机规范》 的全部约束要求， 保证这些信息被当作代码运行后不会危害虚拟机自身的安全。  
- 准备：准备阶段是正式为类中定义的变量（即静态变量， 被static修饰的变量） 分配内存并设置类变量初始值的阶段  
- 解析：解析阶段是Java虚拟机将常量池内的符号引用替换为直接引用的过程  

### 7.3.3 类的初始化

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

## 7.4 类与类加载器

加载指的是将类的class文件读入到内存，并为之创建一个java.lang.Class对象，也就是说，当程序中使用任何类时，系统都会为之建立一个java.lang.Class对象。

类加载器虽然只用于实现类的加载动作， 但它在Java程序中起到的作用却远超类加载阶段。 对于任意一个类， 都必须由加载它的类加载器和这个类本身一起共同确立其在Java虚拟机中的唯一性， 每一个类加载器， 都拥有一个独立的类名称空间。 这句话可以表达得更通俗一些： 比较两个类是否“相等”， 只有在这两个类是由同一个类加载器加载的前提下才有意义， 否则， 即使这两个类来源于同一个Class文件， 被同一个Java虚拟机加载， 只要加载它们的类加载器不同， 那这两个类就必定不相等。

这里所指的“相等”， 包括代表类的Class对象的equals()方法、 isAssignableFrom()方法、 isInstance()方法的返回结果， 也包括了使用instanceof关键字做对象所属关系判定等各种情况。 如果没有注意到类加载器的影响， 在某些情况下可能会产生具有迷惑性的结果， 代码清单7-8中演示了不同的类加载器对instanceof关键字运算的结果的影响。  

### 7.4.1 类加载器简介

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

### 7.4.2 双亲委派模型

站在Java虚拟机的角度来看， 只存在两种不同的类加载器： 一种是启动类加载器（BootstrapClassLoader） ， 这个类加载器使用C++语言实现[1]， 是虚拟机自身的一部分； 另外一种就是其他所有的类加载器， 这些类加载器都由Java语言实现， 独立存在于虚拟机外部， 并且全都继承自抽象类java.lang.ClassLoader。

站在Java开发人员的角度来看， 类加载器就应当划分得更细致一些。 自JDK 1.2以来， Java一直保持着三层类加载器、 双亲委派的类加载架构， 尽管这套架构在Java模块化系统出现后有了一些调整变动， 但依然未改变其主体结构， 我们将在7.5节中专门讨论模块化系统下的类加载器。

本节内容将针对JDK 8及之前版本的Java来介绍什么是三层类加载器， 以及什么是双亲委派模型。
对于这个时期的Java应用， 绝大多数Java程序都会使用到以下3个系统提供的类加载器来进行加载。

- 启动类加载器（Bootstrap Class Loader） ： 前面已经介绍过， 这个类加载器负责加载存放在<JAVA_HOME>\lib目录， 或者被-Xbootclasspath参数所指定的路径中存放的， 而且是Java虚拟机能够识别的（按照文件名识别， 如rt.jar、 tools.jar， 名字不符合的类库即使放在lib目录中也不会被加载） 类库加载到虚拟机的内存中。 启动类加载器无法被Java程序直接引用， 用户在编写自定义类加载器时，如果需要把加载请求委派给引导类加载器去处理， 那直接使用null代替即可， 代码清单7-9展示的就是java.lang.ClassLoader.getClassLoader()方法的代码片段， 其中的注释和代码实现都明确地说明了以null值来代表引导类加载器的约定规则。

> 代码清单7-9 ClassLoader.getClassLoader()方法的代码片段

```java
/**
Returns the class loader for the class. Some implementations may use null to represent the bootstrap class load
*/
public ClassLoader getClassLoader() {
	ClassLoader cl = getClassLoader();
	if (cl == null)
		return null;
	SecurityManager sm = System.getSecurityManager();
	if (sm != null) {
		ClassLoader ccl = ClassLoader.getCallerClassLoader();
		if (ccl != null && ccl != cl && !cl.isAncestor(ccl)) {
			sm.checkPermission(SecurityConstants.GET_CLASSLOADER_PERMISSION);
		}
	}
	return cl;
}
```

- 扩展类加载器（Extension Class Loader） ： 这个类加载器是在类sun.misc.Launcher$ExtClassLoader中以Java代码的形式实现的。 它负责加载<JAVA_HOME>\lib\ext目录中， 或者被java.ext.dirs系统变量所指定的路径中所有的类库。 根据“扩展类加载器”这个名称， 就可以推断出这是一种Java系统类库的扩展机制， JDK的开发团队允许用户将具有通用性的类库放置在ext目录里以扩展Java SE的功能， 在JDK 9之后， 这种扩展机制被模块化带来的天然的扩展能力所取代。 由于扩展类加载器是由Java代码实现的， 开发者可以直接在程序中使用扩展类加载器来加载Class文件。
- 应用程序类加载器（Application Class Loader） ： 这个类加载器由sun.misc.Launcher$AppClassLoader来实现。 由于应用程序类加载器是ClassLoader类中的getSystemClassLoader()方法的返回值， 所以有些场合中也称它为“系统类加载器”。 它负责加载用户类路径（ClassPath） 上所有的类库， 开发者同样可以直接在代码中使用这个类加载器。 如果应用程序中没有自定义过自己的类加载器， 一般情况下这个就是程序中默认的类加载器。  

![]()

JDK 9之前的Java应用都是由这三种类加载器互相配合来完成加载的， 如果用户认为有必要， 还可以加入自定义的类加载器来进行拓展， 典型的如增加除了磁盘位置之外的Class文件来源， 或者通过类加载器实现类的隔离、 重载等功能。 这些类加载器之间的协作关系“通常”会如图7-2所示。

图7-2中展示的各种类加载器之间的层次关系被称为类加载器的“双亲委派模型（Parents Delegation Model） ”。 双亲委派模型要求除了顶层的启动类加载器外， 其余的类加载器都应有自己的父类加载器。 不过这里类加载器之间的父子关系一般不是以继承（Inheritance） 的关系来实现的， 而是通常使用组合（Composition） 关系来复用父加载器的代码。

读者可能注意到前面描述这种类加载器协作关系时， 笔者专门用双引号强调这是“通常”的协作关系。 类加载器的双亲委派模型在JDK 1.2时期被引入， 并被广泛应用于此后几乎所有的Java程序中， 但它并不是一个具有强制性约束力的模型， 而是Java设计者们推荐给开发者的一种类加载器实现的最佳实践。

双亲委派模型的工作过程是： 如果一个类加载器收到了类加载的请求， 它首先不会自己去尝试加载这个类， 而是把这个请求委派给父类加载器去完成， 每一个层次的类加载器都是如此， 因此所有的加载请求最终都应该传送到最顶层的启动类加载器中， 只有当父加载器反馈自己无法完成这个加载请求（它的搜索范围中没有找到所需的类） 时， 子加载器才会尝试自己去完成加载。

使用双亲委派模型来组织类加载器之间的关系， 一个显而易见的好处就是Java中的类随着它的类加载器一起具备了一种带有优先级的层次关系。 例如类java.lang.Object， 它存放在rt.jar之中， 无论哪一个类加载器要加载这个类， 最终都是委派给处于模型最顶端的启动类加载器进行加载， 因此Object类在程序的各种类加载器环境中都能够保证是同一个类。 反之， 如果没有使用双亲委派模型， 都由各个类加载器自行去加载的话， 如果用户自己也编写了一个名为java.lang.Object的类， 并放在程序的ClassPath中， 那系统中就会出现多个不同的Object类， Java类型体系中最基础的行为也就无从保证， 应用程序将会变得一片混乱。 如果读者有兴趣的话， 可以尝试去写一个与rt.jar类库中已有类重名的Java类， 将会发现它可以正常编译， 但永远无法被加载运行[2]。

双亲委派模型对于保证Java程序的稳定运作极为重要， 但它的实现却异常简单， 用以实现双亲委派的代码只有短短十余行， 全部集中在java.lang.ClassLoader的loadClass()方法之中， 如代码清单7-10所示。  

代码清单7-10 双亲委派模型的实现

```java
protected synchronized Class<?> loadClass(String name, boolean resolve) throws ClassNotFoundException
{
	// 首先， 检查请求的类是否已经被加载过了
	Class c = findLoadedClass(name);
	if (c == null) {
		try {
			if (parent != null) {
				c = parent.loadClass(name, false);
			} else {
				c = findBootstrapClassOrNull(name);
			}
		} catch (ClassNotFoundException e) {
		// 如果父类加载器抛出ClassNotFoundException
		// 说明父类加载器无法完成加载请求
		}
		if (c == null) {
			// 在父类加载器无法加载时
			// 再调用本身的findClass方法来进行类加载
			c = findClass(name);
		}
	}
	if (resolve) {
		resolveClass(c);
	}
	return c;
}
```

这段代码的逻辑清晰易懂： 先检查请求加载的类型是否已经被加载过， 若没有则调用父加载器的loadClass()方法， 若父加载器为空则默认使用启动类加载器作为父加载器。 假如父类加载器加载失败，抛出ClassNotFoundException异常的话， 才调用自己的findClass()方法尝试进行加载。

[1] 这里只限于HotSpot， 像MRP、 Maxine这些虚拟机， 整个虚拟机本身都是由Java编写的， 自然BootstrapClassLoader也是由Java语言而不是C++实现的。 退一步说， 除了HotSpot外的其他两个高性能虚拟机JRockit和J9都有一个代表Bootstrap ClassLoader的Java类存在， 但是关键方法的实现仍然是使用JNI回调到C（而不是C++） 的实现上， 这个Bootstrap ClassLoader的实例也无法被用户获取到。 在JDK 9以后， HotSpot虚拟机也采用了类似的虚拟机与Java类互相配合来实现Bootstrap ClassLoader的方式， 所以在JDK 9后HotSpot也有一个无法获取实例的代表Bootstrap ClassLoader的Java类存在了。

[2] 即使自定义了自己的类加载器， 强行用defineClass()方法去加载一个以“java.lang”开头的类也不会成功。 如果读者尝试这样做的话， 将会收到一个由Java虚拟机内部抛出的“java.lang.SecurityException：Prohibited package name： java.lang”异常。  

### 7.4.3 破坏双亲委派模型
上文提到过双亲委派模型并不是一个具有强制性约束的模型， 而是Java设计者推荐给开发者们的类加载器实现方式。 在Java的世界中大部分的类加载器都遵循这个模型， 但也有例外的情况， 直到Java模块化出现为止， 双亲委派模型主要出现过3次较大规模“被破坏”的情况。

双亲委派模型的第一次“被破坏”其实发生在双亲委派模型出现之前——即JDK 1.2面世以前的“远古”时代。 由于双亲委派模型在JDK 1.2之后才被引入， 但是类加载器的概念和抽象类java.lang.ClassLoader则在Java的第一个版本中就已经存在， 面对已经存在的用户自定义类加载器的代码， Java设计者们引入双亲委派模型时不得不做出一些妥协， 为了兼容这些已有代码， 无法再以技术手段避免loadClass()被子类覆盖的可能性， 只能在JDK 1.2之后的java.lang.ClassLoader中添加一个新的protected方法findClass()， 并引导用户编写的类加载逻辑时尽可能去重写这个方法， 而不是在loadClass()中编写代码。 上节我们已经分析过loadClass()方法， 双亲委派的具体逻辑就实现在这里面，按照loadClass()方法的逻辑， 如果父类加载失败， 会自动调用自己的findClass()方法来完成加载， 这样既不影响用户按照自己的意愿去加载类， 又可以保证新写出来的类加载器是符合双亲委派规则的。

双亲委派模型的第二次“被破坏”是由这个模型自身的缺陷导致的， 双亲委派很好地解决了各个类加载器协作时基础类型的一致性问题（越基础的类由越上层的加载器进行加载） ， 基础类型之所以被称为“基础”， 是因为它们总是作为被用户代码继承、 调用的API存在， 但程序设计往往没有绝对不变的完美规则， 如果有基础类型又要调用回用户的代码， 那该怎么办呢？

这并非是不可能出现的事情， 一个典型的例子便是JNDI服务， JNDI现在已经是Java的标准服务，它的代码由启动类加载器来完成加载（在JDK 1.3时加入到rt.jar的） ， 肯定属于Java中很基础的类型了。 但JNDI存在的目的就是对资源进行查找和集中管理， 它需要调用由其他厂商实现并部署在应用程序的ClassPath下的JNDI服务提供者接口（Service Provider Interface， SPI） 的代码， 现在问题来了， 启动类加载器是绝不可能认识、 加载这些代码的， 那该怎么办？

为了解决这个困境， Java的设计团队只好引入了一个不太优雅的设计： 线程上下文类加载器（Thread Context ClassLoader） 。 这个类加载器可以通过java.lang.Thread类的setContext-ClassLoader()方法进行设置， 如果创建线程时还未设置， 它将会从父线程中继承一个， 如果在应用程序的全局范围内都没有设置过的话， 那这个类加载器默认就是应用程序类加载器。

有了线程上下文类加载器， 程序就可以做一些“舞弊”的事情了。 JNDI服务使用这个线程上下文类加载器去加载所需的SPI服务代码， 这是一种父类加载器去请求子类加载器完成类加载的行为， 这种行为实际上是打通了双亲委派模型的层次结构来逆向使用类加载器， 已经违背了双亲委派模型的一般性原则， 但也是无可奈何的事情。 Java中涉及SPI的加载基本上都采用这种方式来完成， 例如JNDI、JDBC、 JCE、 JAXB和JBI等。 不过， 当SPI的服务提供者多于一个的时候， 代码就只能根据具体提供者的类型来硬编码判断， 为了消除这种极不优雅的实现方式， 在JDK 6时， JDK提供了java.util.ServiceLoader类， 以META-INF/services中的配置信息， 辅以责任链模式， 这才算是给SPI的加载提供了一种相对合理的解决方案。

双亲委派模型的第三次“被破坏”是由于用户对程序动态性的追求而导致的， 这里所说的“动态性”指的是一些非常“热”门的名词： 代码热替换（Hot Swap） 、 模块热部署（Hot Deployment） 等。 说白了就是希望Java应用程序能像我们的电脑外设那样， 接上鼠标、 U盘， 不用重启机器就能立即使用，鼠标有问题或要升级就换个鼠标， 不用关机也不用重启。 对于个人电脑来说， 重启一次其实没有什么大不了的， 但对于一些生产系统来说， 关机重启一次可能就要被列为生产事故， 这种情况下热部署就对软件开发者， 尤其是大型系统或企业级软件开发者具有很大的吸引力。

早在2008年， 在Java社区关于模块化规范的第一场战役里， 由Sun/Oracle公司所提出的JSR-294[1]、 JSR-277[2]规范提案就曾败给以IBM公司主导的JSR-291（即OSGi R4.2） 提案。 尽管Sun/Oracle并不甘心就此失去Java模块化的主导权， 随即又再拿出Jigsaw项目迎战， 但此时OSGi已经站稳脚跟，成为业界“事实上”的Java模块化标准[3]。 曾经在很长一段时间内， IBM凭借着OSGi广泛应用基础让Jigsaw吃尽苦头， 其影响一直持续到Jigsaw随JDK 9面世才算告一段落。 而且即使Jigsaw现在已经是Java的标准功能了， 它仍需小心翼翼地避开OSGi运行期动态热部署上的优势， 仅局限于静态地解决模块间封装隔离和访问控制的问题， 这部分内容笔者在7.5节中会继续讲解， 现在我们先来简单看一看OSGi是如何通过类加载器实现热部署的。

OSGi实现模块化热部署的关键是它自定义的类加载器机制的实现， 每一个程序模块（OSGi中称为Bundle） 都有一个自己的类加载器， 当需要更换一个Bundle时， 就把Bundle连同类加载器一起换掉以实现代码的热替换。 在OSGi环境下， 类加载器不再双亲委派模型推荐的树状结构， 而是进一步发展为更加复杂的网状结构， 当收到类加载请求时， OSGi将按照下面的顺序进行类搜索：

1） 将以java.*开头的类， 委派给父类加载器加载。

2） 否则， 将委派列表名单内的类， 委派给父类加载器加载。

3） 否则， 将Import列表中的类， 委派给Export这个类的Bundle的类加载器加载。

4） 否则， 查找当前Bundle的ClassPath， 使用自己的类加载器加载。

5） 否则， 查找类是否在自己的Fragment Bundle中， 如果在， 则委派给Fragment Bundle的类加载器加载。

6） 否则， 查找Dynamic Import列表的Bundle， 委派给对应Bundle的类加载器加载。

7） 否则， 类查找失败。

上面的查找顺序中只有开头两点仍然符合双亲委派模型的原则， 其余的类查找都是在平级的类加载器中进行的， 关于OSGi的其他内容， 笔者就不再展开了。

本节中笔者虽然使用了“被破坏”这个词来形容上述不符合双亲委派模型原则的行为， 但这里“被破坏”并不一定是带有贬义的。 只要有明确的目的和充分的理由， 突破旧有原则无疑是一种创新。 正如OSGi中的类加载器的设计不符合传统的双亲委派的类加载器架构， 且业界对其为了实现热部署而带来的额外的高复杂度还存在不少争议， 但对这方面有了解的技术人员基本还是能达成一个共识， 认为OSGi中对类加载器的运用是值得学习的， 完全弄懂了OSGi的实现， 就算是掌握了类加载器的精粹。

[1] JSR-294： Improved Modularity Support in the Java Programming Language（Java编程语言中的改进模块性支持） 。

[2] JSR-277： Java Module System（Java模块系统） 。

[3] 如果读者对Java模块化之争或者OSGi本身感兴趣， 欢迎阅读笔者的另一本书《深入理解OSGi：Equinox原理、 应用与最佳实践》 。  

## 7.5 URLClassLoader类

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
