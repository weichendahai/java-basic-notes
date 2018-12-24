Java基础知识笔记-5-Java语言中的修饰符

# 5 Java语言中的修饰符
&emsp;&emsp;表中的类仅限于顶层类，而不包括内部类。内部类是指定义在类或方法中的类。

修饰符 | 类 | 成员方法 | 构造方法 | 成员变量 | 局部变量
---|---|---|---|---|---
abstract |Y|Y|-|-|-
static |-|Y|-|Y|-
pubic |Y|Y|Y|Y|-
protected |-|Y|Y|Y|-
private |-|Y|Y|Y|-
final |Y|Y|-|Y|Y

&emsp;&emsp;从上表可以看出：修饰顶层类的修饰符包括：abstract,public,final,而static,protected,private不能修饰顶层类。成员方法和成员变量可以有多种修饰符，而局部变量只能用final来修饰。

## 5.1 访问控制修饰符
&emsp;&emsp;面向对象的基本思想之一是封装实现细节并且公开接口。java语言采用访问控制符来控制类，以及类的方法和访问权限，从而向使用者只暴露接口，但隐藏实现细节，访问控制分四种级别：
- 公开级别：用public修饰，对外公开
- 受保护级别：用protected修饰，向子类及同一个包中的类公开
- 默认级别：没有访问控制修饰符，向同一个包中的类公开
- 私有级别：用Private修饰，只有类本身可以访问，不对外公开

访问级别 | 访问控制修饰符 | 同类 | 同包 | 子类 | 不同的包
---|---|---|---|---|---
公开 |public|Y|Y|Y|Y
受保护 |protected|Y|-|Y|-
默认 |无|Y|Y|-|-
私有 |Private|Y|-|-|-

&emsp;&emsp;成员变量，成员方法和构造方法可以处于4个访问级别中的一个：公开，受保护，默认或者私有。顶层类只可以处于公开或默认访问级别，因此顶层类不能用private和protected来修饰，以下代码会导致编译错误：
```
private class Sample{...};
```
---

## 2 abstract 修饰符
&emsp;&emsp;abstract修饰符可用来修饰类和成员方法;
- 用abstract修饰的类表示抽象类，抽象类位于继承树的抽象层，抽象类不能被实例化，既不允许创建抽象类本身的实例。
- 用abstract修饰的方法表示抽象方法，抽象方法没有方法体，抽象方法用来描述系统具有什么功能，但不提供具体的实现，没有用abstract修饰的方法称为具体方法，具体方法具有方法体。

> abstract类和abstract方法

&emsp;&emsp;用关键字abstract修饰的类称为abstract类（抽象类）  
如：
```
abstract class A{
}
```
&emsp;&emsp;用关键字abstract修饰的方法称为abstract方法（抽象方法），例如
```
abstract int min(int x,int y);
```
&emsp;&emsp;对于abstract方法，只允许声明，不允许实现（没有方法体），而且不允许使用abstract和final同时修饰一个方法和类。

### 使用abstract修饰符需要遵守以下语法规则

#### 1.抽象类中可以没有抽象方法，但包含了抽象方法的类必须被定义为抽象类。如果子类没有实现父类中所有的抽象方法，那么子类也必须定义为抽象类，否则编译会出错。
#### 2.没有抽象静态方法，static和abstract关键字不可在一起使用。
#### 3.抽象类中可以有非抽象的构造方法，创建子类的实例可能会调用这些构造方法。抽象类不能被实例化，然而可以创建一个引用变量，其类型是一个抽象类，并让他引用非抽象的子类的一个实例。

#### 4.抽象类及抽象方法不能被final修饰符修饰，abstract修饰符与final修饰符不能连用，因为抽象类只允许创建其子类，他的抽象方法才能被实现，并且只有他的具体子类才能被实例化，而用final修饰的类不允许拥有子类，用final修饰的方法不允许被子类方法覆盖，因此两者连用，会自相矛盾。

&emsp;&emsp;抽象类的一个重要特征是不允许实例化，比如苹果香蕉是具体类，而水果是抽象类一样。在自然界中并不存在水果类本身的实例，只存在它的具体子类的实例：
```
Fruit fruit=new Apple();
```
&emsp;&emsp;在继承树上，总可以把子类的对象看作父类的对象。当父类是具体类，父类的对象包括父类本身的对象，以及所有具体子类的对象;当父类是抽象类，父类的对象包括所有具体子类的对象，因此，所谓的抽象类不能实例化，是指不能创建抽象类本身的实例，尽管如此，可以创建一苹果对象，并把它看作是水果对象。
#### 5.抽象方法不能被private修饰符修饰，这是因为如果方法是抽象的，表示父类只申明具备某种功能，但没有提供实现，这种方法有待于某个子类去实现它，父类中的abstract方法必须让子类是可见的。否则，在父类中声明一个永远无法实现的方法是无意义的，假如java允许把父类的方法同时用abstract和private修饰，那就意味着在父类中声明一个永远无法实现的方法，所以在java中不允许出现这一情况。

&emsp;&emsp;注意：abstract类也可以没有abstract方法。  
&emsp;&emsp;如果一个abstract类是abstract类的子类，它可以重写父类的abstract方法，也可以继承这个abstract方法。
> abstract类的子类如果不是abstract的，那么它就可以被实例化，这会导致执行该abstract类的构造器，进而执行其用于实例变量的域初始化器。

---
## 3 final修饰符

&emsp;&emsp;final具有不可改变的含义，它可以用来修饰非抽象类，非抽象对象成员，和变量。

- 用final修饰的类不能被继承，没有子类
- 用final修饰的方法不能被子类的方法覆盖
- 用final修饰的变量表示常量，只能被赋一次值

&emsp;&emsp;final不能用来修饰构造方法，因为“方法覆盖”这一概念仅适用于类的成员方法，而不适用于类的构造方法，父类的构造方法和子类的构造方法之间不存在覆盖关系，因此用final修饰构造方法是无意义的。父类中的private修饰的方法不能被子类的方法覆盖，因此private类型的方法默认是final类型的。

### 3.1 final类

&emsp;&emsp;继承关系的弱点是打破封装，子类能够访问父类的实现细节，而且能以方法覆盖的方式修改实现细节，在以下情况，可以考虑把类定义为final类型，使得这个类不能被继承。
- 不是专门为继承而设计的类，类本身的方法之间有复杂的调用关系，假如随意创建这些类的子类，子类有可能会错误的修改父类的实现细节。
- 处于安全的原因，类的实现细节不允许有任何改动
- 在创建对象模型时，确信这个类不会再被扩展
例如，JDK中的java.lang.String类被定义为final类：
```
public final class String{
    ···
}
```
&emsp;&emsp;如果其他类继承String类，就会导致编译错误

### 3.2 final方法
&emsp;&emsp;在某些情况下，处于安全的原因，子类不允许子类覆盖某个方法，此时可以把这个方法声明为final类型。例如在java.lang.Object类中，getClass()方法为final类型，而equals()方法不是final类型的：
```
public class Object{
    public final Class getClass(){
        ...
    }
    public boolean equal(Object o){
        ...
    }
}
```
&emsp;&emsp;所有Object方法都可以覆盖equals()方法，但不能覆盖getClass()方法。否则就会导致编译错误。
### 3.3 final变量
&emsp;&emsp;用final修饰的变量表示取值不会改变的常量。

&emsp;&emsp;例如在java.lang.Integer类中定义了两个常量：
```
public static final int MAX_VALUE=2147483647;
public staric final int MIN_VALUE=-2147483647;
```
&emsp;&emsp;final变量具有以下特性：
#### 1.final修饰符可以修饰静态变量，实例变量和局部变量，分别表示静态变量，实例变量和局部变量。
#### 2.在成员变量的初始化那一节，曾经提到类的成员变量可以不必显式初始化，但是这不适用于final类型的成员变量。final类型的成员变量必须显式初始化，否则会导致编译错误。

例如：
```
public class Sample{
    static final int a=1;
    static final int b;
    static{
        b=1;//合法
    }
}
```
#### 3.final变量只能赋一次值
例如：
```
public class Sample{
    private final int var1=1
    public Sample(){
        var1=2;  //错误，不允许改变实例变量的值
    }
    public void method(final int param){
        final int var2=1;
        var2+=;  //编译错误
        param++;  //编译错误，不允许改变final类型参数的值
    }
}
```
&emsp;&emsp;以下Sample类的var1实例常量分别在Samlpe()和Sample(int x)这两个构造方法中初始化，这是合法的。因为在创建Sample对象时，只会执行一个构造方法，所以var1实例常量不会被初始化两次：
```
class Sample{
    final int var1;//定义实例常量
    final int var2=0;//定义并初始化var2实例常量
    Sample(){
        var1=1;//初始var1化实例常量
    }
    Sample(int x){
        var1=x;//初始化var1实例常量
    }
}
```
#### 4.如果将引用类型的变量用final修饰，那么该变量只能始终引用一个对象，但可以改变对象的内容，例如：
```
public class Sample{
    public int var;
    public Sample(int var){
        this.var=var;
    }
    public static void main(String args[]){
        final Sample s=new Sample(1);
        s.var=2;//合法，修改引用变量s所引用的Sample对象的var属性

        s=new Sample(2);//编译出错，不能改变引用变量s所引用的Sample对象
    }
}
```
&emsp;&emsp;在程序中通过final修饰符来定义常量，具有以下作用：
- 提高代码的安全性，禁止非法修改取值固定并且不允许改变的数据
- 提高程序代码的可维护性
- 提高程序代码的可读性

---
## 4 static修饰符
&emsp;&emsp;static修饰符可以用来修饰类的成员变量，成员方法和代码块：
- 用static修饰的成员变量表示静态变量，可以直接通过类名来访问。
- 用static修饰的成员方法表示静态方法，可以直接通过类名来访问。
- 用static修饰的程序代码块表示静态代码块，当Java虚拟机加载类时，就会执行该代码块。

&emsp;&emsp;被static所修饰的成员变量和成员方法表明归某个类所有，它不依赖于类的特定实例，被类的所有实例共享，只要这个类被加载，java虚拟机就能根据类名在运行时数据区的方法区内定位到它们。

### 4.1 static变量
&emsp;&emsp;成员变量有两种，一种是被static修饰的变量叫类变量，或者静态变量，一种是没有被static修饰的变量，叫实例变量。

区别如下：

- 静态变量在内存中只有一个备份，运行时java虚拟机只为静态变量分配一次内存，在加载类的过程中完成静态变量的内存分配，可以直接通过类名访问静态变量。

- 对于实例变量，没创建一个实例，就会为实例变量分配一次内存，实例变量可以在内存中有多个备份，互不影响。

&emsp;&emsp;在类的内部，可以在任何方法内直接访问静态变量，在其他类中，可以通过某个类的类名来访它他的静态变量。

例如：

```
public class Sample{
    public static int number;//定义一个静态变量
    public void method(){
        int x=number;//在类的内部访问number静态变量
    }
}
public class Sample{
    public void method(){
        int x=Sample.number;//通过类名来访问number静态变量
    }
}
```

&emsp;&emsp;static变量在某种程度上与其他语言比如C语言中的全局变量相似，Java语言不支持不属于任何类的全局变量就，静态变量提供了这一功能，他有两个作用：
- 能被类的所有实例共享，可作为实例之间进行交流的共享数据
- 如果类的所有实例都包括一个相同的常量属性，可以把这个属性定义为静态常量类型，从而节省内存空间。

### 4.2 static方法
&emsp;&emsp;成员方法分为静态方法和实例方法，用static修饰的方法叫做静态方法或者类方法。静态方法和静态变量一样，不需要创建类的实例，可以直接通过类名来访问，例如：
```
public class Sample{
    public static int add(int x,int y){//静态方法
        return x+y;
    }
    public class Sample{
    public void Sample{
        public void method(){
            int result=Sample.add(1,2);//调用Sample类的add()静态
            System.out.println("result");
        }
    }
}

```
#### 1.静态方法可访问的内容

&emsp;&emsp;因为静态方法不需要通过它所属的类的任何实例就会被调用，因此静态方法中不能使用this关键字，也不能直接访问所属类的实例变量和实例方法，但是可以直接访问所属类的静态变量和静态方法。

#### 2.实例方法可以访问的内容
&emsp;&emsp;如果一个类没有被static修饰，那么它就是实例方法。在实例方法中可以直接访问所属类的静态变量，静态方法，实例变量，实例方法。

&emsp;&emsp;实例方法的例子多次已举，不必再举

#### 3.静态方法必须被实现
&emsp;&emsp;静态方法用来表示某个类所特有的功能，这种功能的实现不依赖于类的具体实例，也不依赖于它的子类。既然如此，当前类必须为静态方法提供实现。换句话说，一个静态方法不能被定义为抽象方法。以下方法的定义是非法的：
```
static abstract void method();//编译出错，不能连用
```
&emsp;&emsp;如果一个方法是静态的，它就必须自己实现该方法；如果一个方法是抽象的，那么它就只表示类所具有的功能，但不会实现它，在子类中才会实现它。

#### 4.作为程序入口的main()方法是静态方法

&emsp;&emsp;这样使得java虚拟机只要加载了main()方法所属的类，就能执行main方法，而无须先创建这个类的实例。

&emsp;&emsp;在main()静态方法中不能直接访问实例变量和实例方法。比如如下编译错误：
```
public class Sample{
    int x;
    void method(){
    }
    public static void main(String args[]){
        x=9;//编译错误
        this.x=9;//编译错误
        method();//编译错误
        this.method();//编译错误
    }
}
```
正确的做法是通过Sample实例的引用来访问实例方法和实例变量
```
public class Sample{
    int x;
    public static void main(String args[]){
        Sample s=new Sample();
        s.x=9;
        s.method();//合法
    }
}
```
#### 5.方法的字节码都位于方法区
&emsp;&emsp;不管是实例方法，还是静态方法，他们的字节码都位于方方法区，Java编译器把Java方法的源程序代码编译成二进制的编码，成为字节码，Java虚拟机的解析器能够解析这种字节码。

    注意：以下两点只需要了解，不需要掌握

### 4.3 static代码块
&emsp;&emsp;类中可以包含静态代码块，它不存在与任何方法体中，Java虚拟机加载时，会执行这些静态代码块，如果类中包含多个代码块，那么Java虚拟机按他们在类中出现的顺序依次执行他们，每个静态代码块只会被执行一次。
### 4.4 用static进行静态导入
&emsp;&emsp;从JDK5开始引入了静态导入语法，其目的是为了在需要经常访问同一个类的方法或成员变量的场合，简化程序代码。
