Java基础知识笔记-泛型

> 泛型方法和类型通配符的区别/Java7的菱形语法与泛型构造器/设定通配符的下限/泛型方法与泛型重载  补充需要

## 1.概述

Java集合有一个缺点---把一个对象“丢进”集合里之后，集合就会忘记这个对象的数据类型，当再次取出该对象时，该对象的编译类型就变成了Object类型（在运行时类型没变）

Java集合之所以设计成这样，是因为集合的设计者不知道我们会用集合来保存什么类型的对象，所以他们把集合设计成能保存任何类型的对象，只要求很很好的通用性。但这样做会带来如下两个问题：

- 集合对元素类型没有任何限制，这样可能引发一些问题
- 由于把对象“丢进”集合时，集合丢失了对象的状态信息，集合只知道他盛装的是Object，因为取出集合元素后通常要进行强制类型转换。这种强制类型转换增加了编程的复杂度，也会抛出异常。

泛型在java中有很重要的地位，在面向对象编程及各种设计模式中有非常广泛的应用。

什么是泛型？为什么要使用泛型？

> 泛型，即“参数化类型”。一提到参数，最熟悉的就是定义方法时有形参，然后调用此方法时传递实参。那么参数化类型怎么理解呢？

顾名思义，就是将类型由原来的具体的类型参数化，类似于方法中的变量参数，此时类型也定义成参数形式（可以称之为类型形参），然后在使用/调用时传入具体的类型（类型实参）。

泛型的本质是为了参数化类型（在不创建新的类型的情况下，通过泛型指定的不同类型来控制形参具体限制的类型）。也就是说在泛型使用过程中，

操作的数据类型被指定为一个参数，这种参数类型可以用在类、接口和方法中，分别被称为泛型类、泛型接口、泛型方法。

### 1.1 一个例子

一个被举了无数次的例子：

```java
List arrayList = new ArrayList();
arrayList.add("aaaa");
arrayList.add(100);
for(int i = 0; i< arrayList.size();i++){
	String item = (String)arrayList.get(i);
	Log.d("泛型测试","item = " + item);
}
```
毫无疑问，程序的运行结果会以崩溃结束：
```
java.lang.ClassCastException: java.lang.Integer cannot be cast to java.lang.String
```
ArrayList可以存放任意类型，例子中添加了一个String类型，添加了一个Integer类型，再使用时都以String的方式使用，因此程序崩溃了。为了解决类似这样的问题（在编译阶段就可以解决），泛型应运而生。

我们将第一行声明初始化list的代码更改一下，编译器会在编译阶段就能够帮我们发现类似这样的问题。
```java
List<String> arrayList = new ArrayList<String>();
...
//arrayList.add(100); 在编译阶段，编译器就会报错
```
### 1.2 特性

泛型只在编译阶段有效。看下面的代码：

```java
List<String> stringArrayList = new ArrayList<String>();
List<Integer> integerArrayList = new ArrayList<Integer>();

Class classStringArrayList = stringArrayList.getClass();
Class classIntegerArrayList = integerArrayList.getClass();

if(classStringArrayList.equals(classIntegerArrayList)){
	Log.d("泛型测试","类型相同");
}
```
```java
输出结果：D/泛型测试: 类型相同。
```
通过上面的例子可以证明，在编译之后程序会采取去泛型化的措施。也就是说Java中的泛型，只在编译阶段有效。在编译过程中，正确检验泛型结果后，会将泛型的相关信息擦出，并且在对象进入和离开方法的边界处添加类型检查和类型转换的方法。也就是说，泛型信息不会进入到运行时阶段。

对此总结成一句话：泛型类型在逻辑上看以看成是多个不同的类型，实际上都是相同的基本类型。

## 2 泛型的使用
泛型有三种使用方式，分别为：泛型类、泛型接口、泛型方法

### 2.1 泛型类

泛型类型用于类的定义中，被称为泛型类。通过泛型可以完成对一组类的操作对外开放相同的接口。最典型的就是各种容器类，如：List、Set、Map。

泛型类的最基本写法（这么看可能会有点晕，会在下面的例子中详解）：
```java
class 类名称 <泛型标识：可以随便写任意标识号，标识指定的泛型的类型>{
	private 泛型标识 /*（成员变量类型）*/ var; 
	.....
	}
}
```
一个最普通的泛型类：

```java
//此处T可以随便写为任意标识，常见的如T、E、K、V等形式的参数常用于表示泛型
//在实例化泛型类时，必须指定T的具体类型
public class Generic<T>{ 
	//key这个成员变量的类型为T,T的类型由外部指定  
	private T key;
	public Generic(T key) { //泛型构造方法形参key的类型也为T，T的类型由外部指定,比如String类型
		this.key = key;
	}
	public T getKey(){ //泛型方法getKey的返回值类型为T，T的类型由外部指定
		return key;
	}
}
```
```java
//泛型的类型参数只能是类类型（包括自定义类），不能是简单类型
//传入的实参类型需与泛型的类型参数类型相同，即为Integer.
Generic<Integer> genericInteger = new Generic<Integer>(123456);

//传入的实参类型需与泛型的类型参数类型相同，即为String.
Generic<String> genericString = new Generic<String>("key_vlaue");
Log.d("泛型测试","key is " + genericInteger.getKey());
Log.d("泛型测试","key is " + genericString.getKey());
```
```
12-27 09:20:04.432 13063-13063/? D/泛型测试: key is 123456
12-27 09:20:04.432 13063-13063/? D/泛型测试: key is key_vlaue
```
定义的泛型类，就一定要传入泛型类型实参么？并不是这样，在使用泛型的时候如果传入泛型实参，则会根据传入的泛型实参做相应的限制，此时泛型才会起到本应起到的限制作用。如果不传入泛型类型实参的话，在泛型类中使用泛型的方法或成员变量定义的类型可以为任何的类型。

看一个例子：

```java
Generic generic = new Generic("111111");
Generic generic1 = new Generic(4444);
Generic generic2 = new Generic(55.55);
Generic generic3 = new Generic(false);

Log.d("泛型测试","key is " + generic.getKey());
Log.d("泛型测试","key is " + generic1.getKey());
Log.d("泛型测试","key is " + generic2.getKey());
Log.d("泛型测试","key is " + generic3.getKey());
```
```
D/泛型测试: key is 111111
D/泛型测试: key is 4444
D/泛型测试: key is 55.55
D/泛型测试: key is false
```

> 注意：

泛型的类型参数只能是类类型，不能是简单类型。
不能对确切的泛型类型使用instanceof操作。如下面的操作是非法的，编译时会出错。
```java
	if(ex_num instanceof Generic<Number>){ }
```
### 2.2 泛型接口

泛型接口与泛型类的定义及使用基本相同。泛型接口常被用在各种类的生产器中，可以看一个例子：
```java
//定义一个泛型接口
public interface Generator<T> {
    public T next();
}
```
```java
public class Apple<T>
{
	private T info;
	public Apple(){}
	//下面方法中使用T类型形参来定义构造器
	public Apple(T info)
	{
		this.info=info;
	}
	public void setInfo(T info)
	{
		this.info=info;
	}
	public T getInfo(){
		return this.info;
	}
	public static void main(String[] args)
	{
		//由于传给T形参的是String，所以构造器参数只能是String
		Apple<String> a1=new Apple<>("苹果");
		System.out.println(a1.getInfo());
		//由于传给T形参的是Double，所以构造器参数只能是Double或者double
		Apple<Double> a2=new Apple<>("5.67");
		System.out.println(a2.getInfo());
	}
}
```

上面程序定义了一个带泛型申明的`Apple<T>`类（不要理会这个类型形参是否具有实际意义），使用`Apple<T>`类时就可为T类型形参传入实际类型，这样就可以生成如`Apple<String>`,`Apple<Double>`...形式的多个逻辑子类，物理上并不存在。

当实现泛型接口的类，未传入泛型实参时：

```java
/**
 * 未传入泛型实参时，与泛型类的定义相同，在声明类的时候，需将泛型的声明也一起加到类中
 * 即：class FruitGenerator<T> implements Generator<T>{
 * 如果不声明泛型，如：class FruitGenerator implements Generator<T>，编译器会报错："Unknown class"
 */
class FruitGenerator<T> implements Generator<T>{
	@Override
	public T next() {
		return null;
	}
}
```
当实现泛型接口的类，传入泛型实参时：

```java
/**
 * 传入泛型实参时：
 * 定义一个生产器实现这个接口,虽然我们只创建了一个泛型接口Generator<T>
 * 但是我们可以为T传入无数个实参，形成无数种类型的Generator接口。
 * 在实现类实现泛型接口时，如已将泛型类型传入实参类型，则所有使用泛型的地方都要替换成传入的实参类型
 * 即：Generator<T>，public T next();中的的T都要替换成传入的String类型。
 */
public class FruitGenerator implements Generator<String> {
	private String[] fruits = new String[]{"Apple", "Banana", "Pear"};
	@Override
	public String next() {
		Random rand = new Random();
		return fruits[rand.nextInt(3)];
	}
}
```
#### 并不存在泛型类

前面提到可以把ArrayList<String>类当成ArrayList的子类，事实上，ArrayList<String>类也确实像一种特殊的ArrayList类：该ArrayList<String>对象只能添加String对象作为集合元素，但实际上，系统并没有为ArrayList<String>对象生成新的class文件，而且也不会把ArrayList<String>当成新类来处理。

```java
List<String> l1=new ArrayList<>();
List<Integer> l2=new ArrayList<>();
System.out.println(l1.getclass()==l2.getclass());
```

对于上面的代码，输出false，因为不管泛型的实际类型参数是什么，他们在运行时总有同样的类（class）

不管为泛型的类型形参传入哪一种类型实参，对于Java来说，他们仍然被当成同一个类处理，在内存总也只占用一块内存空间，因此在静态方法，静态初始化块或者静态变量的声明和初始化中不允许使用类型形参，如下例：

```java
public class R<T>
{
	//下面代码错误，不能在静态变量声明中使用类型形参
	static T info;
	T age;
	public void foo(T msg){}
	//下面代码错误，不能在静态方法中使用类型形参
	public static void bar(T msg){}
}
```

由于系统中不会真正生成泛型类，所以instanceof运算符后不能使用泛型类，例如，下面代码错误

```java
java.util.Collection<String> cs=new java.util.ArrayList<>();
//下面代码编译时会引起错误
if(cs instanceof java.util.ArrayList<String>){...}
```



## 3 泛型通配符

我们知道Ingeter是Number的一个子类，同时在特性章节中我们也验证过`Generic<Ingeter>`与`Generic<Number>`实际上是相同的一种基本类型。那么问题来了，在使用`Generic<Number>`作为形参的方法中，能否使用`Generic<Ingeter>`的实例传入呢？在逻辑上类似于`Generic<Number>`和`Generic<Ingeter>`是否可以看成具有父子关系的泛型类型呢？

为了弄清楚这个问题，我们使用`Generic<T>`这个泛型类继续看下面的例子：
```java
public void showKeyValue1(Generic<Number> obj){
	Log.d("泛型测试","key value is " + obj.getKey());
}
```
```java
Generic<Integer> gInteger = new Generic<Integer>(123);
Generic<Number> gNumber = new Generic<Number>(456);

showKeyValue(gNumber);

// showKeyValue这个方法编译器会为我们报错：Generic<java.lang.Integer> 
// cannot be applied to Generic<java.lang.Number>
// showKeyValue(gInteger);
```
通过提示信息我们可以看到`Generic<Integer>`不能被看作为`Generic<Number>`的子类。由此可以看出:同一种泛型可以对应多个版本（因为参数类型是不确定的），不同版本的泛型类实例是不兼容的。

回到上面的例子，如何解决上面的问题？总不能为了定义一个新的方法来处理`Generic<Integer>`类型的类，这显然与java中的多台理念相违背。因此我们需要一个在逻辑上可以表示同时是`Generic<Integer>`和`Generic<Number>`父类的引用类型。由此类型通配符应运而生。

我们可以将上面的方法改一下：
```java
public void showKeyValue1(Generic<?> obj){
	Log.d("泛型测试","key value is " + obj.getKey());
}
```
类型通配符一般是使用`?`代替具体的类型实参，注意了，此处`?`是类型实参，而不是类型形参 。重要说三遍！此处`?`是类型实参，而不是类型形参！此处`?`是类型实参，而不是类型形参 ！再直白点的意思就是，此处的？和Number、String、Integer一样都是一种实际的类型，可以把`?`看成所有类型的父类。是一种真实的类型。

可以解决当具体类型不确定的时候，这个通配符就是`?`；当操作类型时，不需要使用类型的具体功能时，只使用Object类中的功能。那么可以用`?`通配符来表未知类型。

### 3.1 设定类型通配符的上限

当直接使用`List<?>`这种形式时，即表明这个List集合可以是任何泛型List的父类。但是还有一种特殊的情形，程序不希望这个`List<?>`是任何泛型List的父类，只希望他代表某一泛型List的父类。考虑一个简单的绘图程序，下面定义三个形状类。

```java
Shape.java
//定义一个抽象类Shape
public abstract class Shape
{
	public abstract void draw(Canvas c);
}
```

```java
Circle.java
public class Circle extends Shape
{
	//实现画图方法，以打印字符串来模拟画图方法实现
	public void draw(Canvas c)
	{
		System.out.println("在画布"+c+"上画一个圆");
	}
}
```

```java
Canvas.java
public class Rectangle extends Shape
{
	实现画图方法，以打印字符串来模拟画图方法的实现
	public void draw(Canvas c)
	{
		System.out.println("把一个矩形画在画布"+c+"上");
	}
}
```

接下来定义一个Canvas类，该画布类可以画数量不等的形状（Shape子类的对象），那应该如何定义这个Canvas类呢？考虑如下的Canvas实现类。

```java
Canvas.java
public class Canvas
{
	public void drawAll(List<Shape> shapes)
	{
		for(Shape s:shapes)
			s.draw(this);
	}
}
```

注意上面的`drawAll()`方法的形参类型是`List<Shape>`，而`List<Circle>`并不是`List<Shape>`的子类型，因此，下面的代码会编译错误

```java
List<Circle> circleList=new ArrayList<>();
Canvas c=new Canvas();
//不能把List<canvas>当成List<Shape>来使用，所以下面代码引起编译错误
c.drawAll(circleList);
```

关键在于`List<Circle>`并不是`List<Shape>>`的子类型，所以不能把`List<Circle>`对象当成`List<Shape>`使用。为了表示`List<Circle>`的父类，可以考虑使用`List<?>`，把Canvas改成如下形式

```java
Canvas.java
public class Canvas
{
	public void drawAll(List<?> shapes)
	{
		for(Shape s:shapes)
			s.draw(this);
	}
}
```

上面程序使用了通配符来表示所有的类型，上面的`drawAll()`方法可以接受`List<Circle>`对象作为参数，问题是上面的方法实现体显得极为臃肿而繁琐：使用了泛型还需要进行强制类型转换。

实际上需要一种泛型表示方法。发可以表示所有的Shape泛型List的父类，为了满足这种需求，Java泛型提供了被限制的泛型通配符。被限制的泛型通配符表示如下

```java
//它表示所有Shape泛型List的父类
List<? extends Shape>
```

上面的Canvas程序改为如下形式：

```java
Canvas.java
public class Canvas
{
	public void drawAll(List<? extends Shape> shapes)
	{
		for(Shape s:shapes)
			s.draw(this);
	}
}
```

将Canvas改为如上形式，就可以把`List<Circle>`对象当成`List<? extends Shape>`使用。即`List<? extends Shape>`可以表示`List<Circle>`，`List<Rectange>`的父类---只要List后尖括号里的类型是Shape的子类型即可。

`List<? extends Shape>`是受限制通配符的例子，此处的`?`代表一个未知的类型，就像前面看到的通配符一样。但是此处的这个未知类型一定是Shape子类型或者它本身，因此可以把Shape称为这个通配符的上限。

类似的，由于程序无法确定那个这个受限制的统配符的具体类型，所以不能把Shape对象及其子类的对象加入这个泛型集合中。例如，下面的代码就是错误的。

```java
public void addRectangle(List<? extends Shape> shapes)
{
	//下面代码引起编译错误
	shapes.add(0,new Rectangle());
}
```

与使用普通通配符相似的是，`shapes.add()`的第二个参数类型是`? extends Shape`，它表示Shape未知的子类，程序无法确定这个类型是什么，所以无法将任何对象添加到这种集合中。

### 3.2 设定类型形参的上限

Java泛型不仅允许在使用通配符形参是设定上限，而且可以在定义类型形参时设定上限，用于表示传给该类型形参的实际类型要么是该上限类型，要么是该上限的子类，如下面的例子

```java
public class Apple<T extends Number>
T col;
public static void main(String[] args)
{
	Apple<Integer> ai=new Apple<>();
	Apple<Double> ad=new Apple<>();
	//下面的代码将引发编译异常，下面代码试图把String类型传给T形参
	//但String不是Number的子类型，所以引起编译错误
	Apple<String> as=new Apple<>();
}
```

上面程序定义了一个Apple泛型类，但是该类的类型形参上限是Number类，这表明使用Apple类时为T形参传入的实际类型参数只能是Number或者他的子类。

在一种更极端的情况下，程序需要为类型形参设定多个上限（至多有一个父类上限，可以有多个接口上限），表明该类型形参必须是其父类的子类（或者本身），并且实现多个上限接口。如下代码所示

```java
//表明T类型必须是Number类或者他的子类，并且必须实现Java.io.Serializable接口
public class Apple<T extends Bumber&java.io.Serializable>
{...}
```

与类同时继承父类，实现接口类似的是，为类型形参指定多个上限时，所有的接口必须位于类上限之后，也就是说，如果需要为类型形参指定类上限，类上线必须位于第一位。

## 4 泛型方法

在java中，泛型类的定义非常简单，但是泛型方法就比较复杂了。

> ## 版本1

### 4.1 定义泛型方法

假设需要实现这样一个方法---该方法负责将一个Object数组的所有元素添加到一个Collection集合中。考虑采用下面的代码

```java
static void fromArrayToCollection(Object[] a,Collection<Object> c)
{
	for(Object o:a)
	{
		c.add(o);
	}
}
```

上面定义的方法没有任何问题，关键在于方法中的c形参，他的数据类型是`Collection<Object>`。正如前面所介绍的，`Collection<String>`不是`Collection<Object>`的子类型----所以这个方法的功能非常有限，它只能将`Object[]`数组的元素复制为Object（Object的子类不行）的Collection集合中，即下面的代码将引起编译错误

```java
String[] atrArr={"a","b"};
List<String> strList=new ArrayList<>();
//Collection<String>对象不能当成Collection<Object>使用，下面的代码会出现编译错误
fromArrayToCollection(strArr,strList);
```

可见上面方法的参数类型不可以使用`Collection<String>`，那使用通配符`Collection<?>`是否可行呢？显然也不行，因为Java不允许把对象放进一个未知类型的集合中。

为了解决这个问题，可以使用Java提供的泛型方法，就是在声明方法时定义一个或多个类型形参。

将上面的方法改成如下形式

```java
static <T> void fromArrayToCollection(T[] a,Collection<T> c)
{
	for(T o:a)
	{
		c.add(o);
	}
}
```

可以发现，泛型方法的方法签名比普通方法签名多了类型形参声明，类型形参声明以括号括起来，多个类型形参之间以逗号隔开，所有的类型形参声明妨碍方法修饰符和方法返回类型之间。

下面的程序示范了完整的用法

```java
public class GenericMethodTest
{
	//声明一个泛型方法，该泛型方法中带一个T类型形参
	static <T> void fromArrayToCollection(T[] a,Collection<T> c)
	{
		for(T o:a)
		{
			c.add(o);
		}
	}
	public static void main(String[] args)
	{
		Object[] oa=new Object[100];
		Collection<Object> co=new ArrayList<>();
		//下面代码中T代表Object类型
		fromArrayToCollection(oa,co);
		
		String[] sa=new String[100];
		Collection<String> co=new ArrayList<>();
		//下面代码中T代表String类型
		fromArrayToCollection(sa,cs);
		
		//下面代码中T代表Object类型
		fromArrayToCollection(sa,co);
		
		Integer[] ia=new Integer[100];
		Float[] fa=new Float[100];
		Number[] na=new Number[100];
		Collection<Number> cn=new ArrayList<>();
		
		//下面代码中T代表Number类型
		fromArrayToCollection(ia,cn);
		//下面代码中T代表Number类型
		fromArrayToCollection(fa,cn);
		//下面代码中T代表Number类型
		fromArrayToCollection(na,cn);
		//下面代码中T代表Object类型
		fromArrayToCollection(na,co);
		//下面代码中T代表String类型，但是na是一个Number数组
		//因为Number既不是String类型，又不是他的子类，编译错误
		//fromArrayToCollection(na,cs);
	}
}
```



与类，接口中使用泛型参数不同的是，方法中的泛型参数无须显式传入实际类型参数，如上面程序所示，当程序调用fromArrayToCollection()方法时，无须在调用该方法前传入String，Object等类型，但系统仍然可以知道类型形参的数据类型，因为编译器根据实参推断类型形参的值，它通常推断出最直接的类型参数，例如

```java
fromArrayToCollection(sa,cs);
```

上面代码中cs是一个`Collection<String>`类型，与方法定义时的`fromArrayToCollection(T[] a,Collection<T> c)`进行比较---只比较泛型参数，不难发现T类型形参代表的实际类型是String类型

对于如下调用代码

```java
fromArrayToCollection(ia,cn);
```

上面的cn时`Collection<Number>`类型，与此方法的方法签名进行比较----只比较泛型参数，不难发现T类型形参代表了Number类型

为了防编译器能准确的推断出泛型方法中类型形参的类型，不要制造迷惑

```java
public class ErrorTest
{
	//声明一个泛型方法，在该泛型方法中带一个T类型形参
	static <T> void test(Collection<T> from,Collection<T> to)
	{
		for(T ele:from)
		{
			to.add(ele);
		}
		public static void main(String[] args)
		{
			List<Object> as=new ArrayList<>();
			List<String> ao=new ArrayList<>();
			//下面的代码将产生编译错误
			test(as,ao);
		}
	}
}
```

上面程序中定义了test()方法，该方法用于将前一个集合里的元素复制到下一个集合中，该方法中的两个形参from,to类型都是`Collection<T>`，这要求嗲用该方法时两个集合实参中的泛型类型相同，负责编译器无法准确地推断出泛型方法中类型形参的类型。

上面程序中调用test方法传入了两个实际参数，其中as的数据类型是`List<String>`，而ao的数据类型是`List<Object>`，与泛型方法签名进行对比：`test(Collection<T> a,Collection<T> c)`，编译器无法正确识别T所代表的实际类型。为了避免这种错误，可以将该方法改成如下形式：

```java
public class ErrorTest
{
	//声明一个泛型方法，在该泛型方法中带一个T类型形参
	static <T> void test(Collection<? extends T> from,Collection<T> to)
	{
		for(T ele:from)
		{
			to.add(ele);
		}
		public static void main(String[] args)
		{
			List<Object> ao=new ArrayList<>();
			List<String> as=new ArrayList<>();
			//下面的代码正常
			test(as,ao);
		}
	}
}
```

上面代码改变了test()方法签名，将该方法的前一个形参类型改为Collection<? extends T>，这种采用类型通配符的表示方法，只要test()方法的前一个Collection集合里的和元素类型最后一个Collction集合里元素类型恶子集即可。

*那么这里产生了一个问题：到底何时使用泛型方法？何时使用类型通配符呢？接下来详细介绍泛型方法和类型通配符的区别*

### 4.2 泛型方法和类型通配符的区别

大多数时候可以使用泛型方法来替代类型通配符。例如，对于Java的Collection接口中的两个方法的定义：

```java
public interface Collection<E>
{
	boolean containsAll(Collection<?> c);
	boolean addAll(Collection<? extends E> c);
}
```

上面集合中的两个方法的形参都采用了类型通配符的形式，也可以采用泛型方法的形式，如下所示：

```java
public interface Collection<E>
{
	<T> boolean containsAll(Collection<T> c);
	<T extends E> boolean addAll(Collection<T> c);
}
```

上面方法使用了`<T extends E>`泛型类型，这时定义类型形参时设定上限（其中E是Collection接口里定义的类型形参，在该接口里E可以当成普通类型使用）。

上面两个方法中类型形参T只使用了一次，类型形参T产生的唯一效果是可以在不同的调用点传入不同的实际类型。对于这种情况，应该使用通配符：通配符就是被设计用来支持灵活的子类化的。

泛型方法允许类型新参被用来表示方法的一个或者多个参数之间的类型依赖关系，或者方法返回值与参数之间的类型依赖关系。如果没有这样的类型依赖关系，就不应该使用泛型方法。

------

> ## 版本2

尤其是我们见到的大多数泛型类中的成员方法也都使用了泛型，有的甚至泛型类中也包含着泛型方法，这样在初学者中非常容易将泛型方法理解错了。
泛型类，是在实例化类的时候指明泛型的具体类型；泛型方法，是在调用方法的时候指明泛型的具体类型 。

```java
/**
 * 泛型方法的基本介绍
 * @param tClass 传入的泛型实参
 * @return T 返回值为T类型
 * 说明：
 *     1）public 与 返回值中间<T>非常重要，可以理解为声明此方法为泛型方法。
 *     2）只有声明了<T>的方法才是泛型方法，泛型类中的使用了泛型的成员方法并不是泛型方法。
 *     3）<T>表明该方法将使用泛型类型T，此时才可以在方法中使用泛型类型T。
 *     4）与泛型类的定义一样，此处T可以随便写为任意标识，常见的如T、E、K、V等形式的参数常用于表示泛型。
 */
public <T> T genericMethod(Class<T> tClass)throws InstantiationException,
	IllegalAccessException{
		T instance = tClass.newInstance();
		return instance;
}
```

```
Object obj = genericMethod(Class.forName("com.test.test"));
```
### 4.1 泛型方法的基本用法

光看上面的例子有的同学可能依然会非常迷糊，我们再通过一个例子，把我泛型方法再总结一下。

```java
public class GenericTest {
	//这个类是个泛型类，在上面已经介绍过
	public class Generic<T>{     
		private T key;
		public Generic(T key) {
			this.key = key;
		}
		//我想说的其实是这个，虽然在方法中使用了泛型，但是这并不是一个泛型方法。
		//这只是类中一个普通的成员方法，只不过他的返回值是在声明泛型类已经声明过的泛型。
		//所以在这个方法中才可以继续使用 T 这个泛型。
		public T getKey(){
			return key;
		}
		/**
		* 这个方法显然是有问题的，在编译器会给我们提示这样的错误信息"cannot reslove symbol E"
		* 因为在类的声明中并未声明泛型E，所以在使用E做形参和返回值类型时，编译器会无法识别。
		public E setKey(E key){
			this.key = keu
		}
		*/
	}
	/** 
	* 这才是一个真正的泛型方法。
	* 首先在public与返回值之间的<T>必不可少，这表明这是一个泛型方法，并且声明了一个泛型T
	* 这个T可以出现在这个泛型方法的任意位置.
	* 泛型的数量也可以为任意多个 
	*    如：public <T,K> K showKeyName(Generic<T> container){
	*        ...
	*        }
	*/
	public <T> T showKeyName(Generic<T> container){
		System.out.println("container key :" + container.getKey());
		//当然这个例子举的不太合适，只是为了说明泛型方法的特性。
		T test = container.getKey();
		return test;
	}

	//这也不是一个泛型方法，这就是一个普通的方法，只是使用了Generic<Number>这个泛型类做形参而已。
	public void showKeyValue1(Generic<Number> obj){
		Log.d("泛型测试","key value is " + obj.getKey());
	}

	//这也不是一个泛型方法，这也是一个普通的方法，只不过使用了泛型通配符?
	//同时这也印证了泛型通配符章节所描述的，?是一种类型实参，可以看做为Number等所有类的父类
	public void showKeyValue2(Generic<?> obj){
		Log.d("泛型测试","key value is " + obj.getKey());
	}
	
	/**
	* 这个方法是有问题的，编译器会为我们提示错误信息："UnKnown class 'E' "
	* 虽然我们声明了<T>,也表明了这是一个可以处理泛型的类型的泛型方法。
	* 但是只声明了泛型类型T，并未声明泛型类型E，因此编译器并不知道该如何处理E这个类型。
	public <T> T showKeyName(Generic<E> container){
		...
	}  
	
	*/
	
	/**
	* 这个方法也是有问题的，编译器会为我们提示错误信息："UnKnown class 'T' "
	* 对于编译器来说T这个类型并未项目中声明过，因此编译也不知道该如何编译这个类。
	* 所以这也不是一个正确的泛型方法声明。
	public void showkey(T genericObj){
	}
	*/
	
	public static void main(String[] args) {
	
	}
}
```
### 4.2 类中的泛型方法

当然这并不是泛型方法的全部，泛型方法可以出现杂任何地方和任何场景中使用。但是有一种情况是非常特殊的，当泛型方法出现在泛型类中时，我们再通过一个例子看一下

```java
public class GenericFruit {
	class Fruit{
		@Override
		public String toString() {
			return "fruit";
		}
	}

	class Apple extends Fruit{
		@Override
		public String toString() {
			return "apple";
		}
	}

	class Person{
		@Override
		public String toString() {\
			return "Person";
		}
	}

	class GenerateTest<T>{
		public void show_1(T t){
			System.out.println(t.toString());
		}

		//在泛型类中声明了一个泛型方法，使用泛型E，这种泛型E可以为任意类型。可以类型与T相同，也可以不同。
		//由于泛型方法在声明的时候会声明泛型<E>，因此即使在泛型类中并未声明泛型，编译器也能够正确识别泛型方法中识别的泛型。
		public <E> void show_3(E t){
			System.out.println(t.toString());
		}
	
		//在泛型类中声明了一个泛型方法，使用泛型T，注意这个T是一种全新的类型，可以与泛型类中声明的T不是同一种类型。
		public <T> void show_2(T t){
			System.out.println(t.toString());
		}
	}
	public static void main(String[] args) {
		Apple apple = new Apple();
		Person person = new Person();

		GenerateTest<Fruit> generateTest = new GenerateTest<Fruit>();
		//apple是Fruit的子类，所以这里可以
		generateTest.show_1(apple);
		//编译器会报错，因为泛型类型实参指定的是Fruit，而传入的实参类是Person
		//generateTest.show_1(person);

		//使用这两个方法都可以成功
		generateTest.show_2(apple);
		generateTest.show_2(person);

		//使用这两个方法也都可以成功
		generateTest.show_3(apple);
		generateTest.show_3(person);
	}
}
```
### 4.3 泛型方法与可变参数

再看一个泛型方法和可变参数的例子：
```java
public <T> void printMsg( T... args){
	for(T t : args){
		Log.d("泛型测试","t is " + t);
	}
}
```
```
printMsg("111",222,"aaaa","2323.4",55.55);
```
### 4.4 静态方法与泛型

静态方法有一种情况需要注意一下，那就是在类中的静态方法使用泛型：静态方法无法访问类上定义的泛型；如果静态方法操作的引用数据类型不确定的时候，必须要将泛型定义在方法上。

即：如果静态方法要使用泛型的话，必须将静态方法也定义成泛型方法 。

```java
public class StaticGenerator<T> {
	....
	....
	/**
	* 如果在类中定义使用泛型的静态方法，需要添加额外的泛型声明（将这个方法定义成泛型方法）
	* 即使静态方法要使用泛型类中已经声明过的泛型也不可以。
	* 如：public static void show(T t){..},此时编译器会提示错误信息：
	"StaticGenerator cannot be refrenced from static context"
	*/
	public static <T> void show(T t){

	}
}
```
### 4.5 泛型方法总结

泛型方法能使方法独立于类而产生变化，以下是一个基本的指导原则：

无论何时，如果你能做到，你就该尽量使用泛型方法。也就是说，如果使用泛型方法将整个类泛型化，那么就应该使用泛型方法。另外对于一个static的方法而已，无法访问泛型类型的参数。所以如果static方法要使用泛型能力，就必须使其成为泛型方法。

## 5 关于泛型数组

看到了很多文章中都会提起泛型数组，经过查看sun的说明文档，在java中是”不能创建一个确切的泛型类型的数组”的。

也就是说下面的这个例子是不可以的：
```java
List<String>[] ls = new ArrayList<String>[10];  
```
而使用通配符创建泛型数组是可以的，如下面这个例子：
```java
List<?>[] ls = new ArrayList<?>[10];
```
这样也是可以的：
```java
List<String>[] ls = new ArrayList[10];
```
下面使用Sun的一篇文档的一个例子来说明这个问题：

```java
List<String>[] lsa = new List<String>[10]; // Not really allowed.    
Object o = lsa;    
Object[] oa = (Object[]) o;    
List<Integer> li = new ArrayList<Integer>();    
li.add(new Integer(3));    
oa[1] = li; // Unsound, but passes run time store check    
String s = lsa[1].get(0); // Run-time error: ClassCastException.
```

> 这种情况下，由于JVM泛型的擦除机制，在运行时JVM是不知道泛型信息的，所以可以给oa[1]赋上一个ArrayList而不会出现异常，但是在取出数据的时候却要做一次类型转换，所以就会出现ClassCastException，如果可以进行泛型数组的声明，上面说的这种情况在编译期将不会出现任何的警告和错误，只有在运行时才会出错。而对泛型数组的声明进行限制，对于这样的情况，可以在编译期提示代码有类型安全问题，比没有任何提示要强很多。

下面采用通配符的方式是被允许的:数组的类型不可以是类型变量，除非是采用通配符的方式，因为对于通配符的方式，最后取出数据是要做显式的类型转换的。

```java
List<?>[] lsa = new List<?>[10]; // OK, array of unbounded wildcard type.    
Object o = lsa;    
Object[] oa = (Object[]) o;    
List<Integer> li = new ArrayList<Integer>();    
li.add(new Integer(3));    
oa[1] = li; // Correct.    
Integer i = (Integer) lsa[1].get(0); // OK 
```
> 最后

本文中的例子主要是为了阐述泛型中的一些思想而简单举出的，并不一定有着实际的可用性。另外，一提到泛型，相信大家用到最多的就是在集合中，其实，在实际的编程过程中，自己可以使用泛型去简化开发，且能很好的保证代码质量。
