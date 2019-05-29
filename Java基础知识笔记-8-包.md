Java基础知识笔记-7-包

# 7 包
一般而言，当命名类的时候，是从名称空间中分配一个名称。名称空间定义了一个声明性的区域。在java中，同一个名称空间中的两个类不能使用相同的名称。这样，再给定的一个名称空间中，每一个类名必然是唯一的包提供了一种为名称空间分区的方法。当在包中定义一个类时，该包的名称将附加到每一个类上，这样就避免了与其他包中具有相同名字的类发生名字冲突。
## 1 包的作用
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

## 2 创建包

**创建包的时候，你需要为这个包取一个合适的名字。之后，如果其他的一个源文件包含了这个包提供的类、接口、枚举或者注释类型的时候，都必须将这个包的声明放在这个源文件的开头。**

**包声明应该在源文件的第一行，每个源文件只能有一个包声明，这个文件中的每个类型都应用于它。**

*如果一个源文件中没有使用包声明，那么其中的类，函数，枚举，注释等将被放在一个无名的包（unnamed package）中。*

### 例子

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
MammalInt.java 文件代码：
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

## 3 包和成员访问
包也参与了java的访问控制机制，元素的可见性取决于它的访问说明-private，public，protected或者默认，也取决于元素所在的包。因此，一个元素的可见性就由它所属的类和所属的包的可见性来决定，这种多层次的访问控制方法适用与丰富的访问权限分表，如下表：

-|private成员|默认成员|protected成员|public成员
---|---|---|---|---
在同一个类中可见|Y|Y|Y|Y
在位于同一个包中的子类中可见|N|Y|Y|Y
在位于同一个包中的非子类可见|N|Y|Y|Y
在位于不同包中的子类可见|N|N|Y|Y
在位于不同包中的非子类可见|N|N|N|Y


## 4 导入包（import关键字）

为了能够使用某一个包的成员，我们需要在Java程序中明确导入该包。使用"import"语句可完成此功能。

在java源文件中import语句应位于package语句之后，所有类的定义之前，可以没有，也可以有多条，其语法格式为：
```java
import package1[.package2…].(classname|*);
```
如果在一个包中，一个类想要使用本包中的另一个类，那么该包名可以省略。

> 例子

下面的payroll包已经包含了Employee类，接下来向payroll包中添加一个Boss类。Boss类引用Employee类的时候可以不用使用payroll前缀，Boss类的实例如下。
```java
//Boss.java 文件代码：
package payroll;
 
public class Boss
{
   public void payEmployee(Employee e)
   {
      e.mailCheck();
   }
}
```
如果Boss类不在payroll包中又会怎样？Boss类必须使用下面几种方法之一来引用其他包中的类。

使用类全名描述，例如：
```java
payroll.Employee
```
用import关键字引入，使用通配符 "*"
```java
import payroll.*;
```
使用import关键字引入 Employee 类:
```java
import payroll.Employee;
```
##### 注意：

类文件中可以包含任意数量的import声明。import声明必须在包声明之后，类声明之前。

在大多数情况下，只导入所需的包，并不必过多地理睬它们。但在发生命名冲突的时候，就不能不注意包的名字了。例如，java.util 和java.sql 包都有日期（ Date) 类。如果在程序中导入了这两个包：
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
> 在C++中，与包机制类似的是命名空间(namespace)。在Java中，package与import语句类似于C++中的namespace和using指令。

## 5 package的目录结构

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
路径名 -> vehicle\Car.java (在 windows 系统中)

通常，一个公司使用它互联网域名的颠倒形式来作为它的包名.例如：互联网域名是runoob.com，所有的包名都以 com.runoob开头。包名中的每一个部分对应一个子目录。

例如：有一个`com.runoob.test`的包，这个包包含一个叫做`Runoob.java`的源文件，那么相应的，应该有如下面的一连串子目录：
....\com\runoob\test\Runoob.java

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

`<path- two>\classe`是`class path`，`package`名字是`com.runoob.test`,而编译器和JVM会在`<path-two>\classes\com\runoob\test`中找 `.class`文件。

一个`class path`可能会包含好几个路径，多路径应该用分隔符分开。默认情况下，编译器和JVM查找当前目录。JAR 文件按包含Java平台相关的类，所以他们的目录默认放在了`class path`中。

## 6 设置CLASSPATH系统变量

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
## 7 JDK中提供的Java基本包

JDK中提供了一些Java基本包，主要包括：
- java.lang包：包含线程类，异常类，系统类，整数类和字符串类等，这些类是编写Java程序经常用到的。这个包是Java虚拟机自动引入的，也就是说，即使没有提供import java.lang.*语句，这个包也会被自动引入。
- java.awt包：抽象窗口工具包，这个包主要用于创建GUI界面的类及绘图类
- java.io包：输入/输出包，包含各种输入输出流，如文件输入流类（FileInputStream类），以及文件输出流类（FileOutputStream）等
- java.util包：提供一些实用类，如日期类和集合类。
- java.net包：提供TCP/IP网络协议，包含Socket类，以及和URL相关的类，这些都用于网络编程
