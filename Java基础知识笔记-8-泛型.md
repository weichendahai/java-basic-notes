Java基础知识笔记-8-泛型

# 8 泛型

从Java程序设计语言1.0版发布以来，变化最大的部分就是泛型。致使Java SE 5.0中增加泛型机制的主要原因是为了满足在1999年制定的最早的Java规范需求之一（JSR 14)。专家组花费了5年左右的时间用来定义规范和测试实现。

泛型正是我们需要的程序设计手段。使用泛型机制编写的程序代码要比那些杂乱地使用Object变量，然后再进行强制类型转换的代码具有更好的安全性和可读性。泛型对于集合类尤其有用，例如，ArrayList就是一个无处不在的集合类。

至少在表面上看来，泛型很像C++中的模板。与java—样，在C++中，模板也是最先被添加到语言中支持强类型集合的。但是，多年之后人们发现模板还有其他的用武之地。学习完本章的内容可以发现Java中的泛型在程序中也有新的用途。 

## 1.使用泛型的原因

Java集合有一个缺点——把一个对象“丢进”集合里之后，集合就会忘记这个对象的数据类型，当再次取出该对象时，该对象的编译类型就变成了Object类型（在运行时类型没变）

Java集合之所以设计成这样，是因为集合的设计者不知道我们会用集合来保存什么类型的对象，所以他们把集合设计成能保存任何类型的对象，只要求很很好的通用性。但这样做会带来如下两个问题：

- 集合对元素类型没有任何限制，这样可能引发一些问题
- 由于把对象“丢进”集合时，集合丢失了对象的状态信息，集合只知道他盛装的是Object，因为取出集合元素后通常要进行强制类型转换。这种强制类型转换增加了编程的复杂度，也会抛出异常。

泛型在java中有很重要的地位，在面向对象编程及各种设计模式中有非常广泛的应用。

什么是泛型？为什么要使用泛型？

> 泛型，即“参数化类型”。一提到参数，最熟悉的就是定义方法时有形参，然后调用此方法时传递实参。那么参数化类型怎么理解呢？

顾名思义，就是将类型由原来的具体的类型参数化，类似于方法中的变量参数，此时类型也定义成参数形式（可以称之为类型形参），然后在使用/调用时传入具体的类型（类型实参）。

泛型的本质是为了参数化类型（在不创建新的类型的情况下，通过泛型指定的不同类型来控制形参具体限制的类型）。也就是说在泛型使用过程中，

操作的数据类型被指定为一个参数，这种参数类型可以用在类、接口和方法中，分别被称为泛型类、泛型接口、泛型方法。

### 1.1 类型参数的好处

在Java中增加范型类之前，泛型程序设计是用继承实现的。ArrayList类只维护一个Object引用的数组：

```java
public class ArrayList // before generic classes
{
	private Object[] elementData;
	public Object get(int i) { . . . }
	public void add(Object o) { . . . }
}
```

下面是一个例子：

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
这种方法有两个问题。当获取一个值的时候必须进行强制转换。

```java
ArrayList files = new ArrayList();
String filename = (String) files.get(O);
```

此外，这里没有检查错误。可以向程序中添加任何类的对象。ArrayList可以存放任意类型，例子中添加了一个String类型，添加了一个Integer类型，再使用时都以String的方式使用，因此程序崩溃了。为了解决类似这样的问题（在编译阶段就可以解决），泛型应运而生。

我们将第一行声明初始化list的代码更改一下，编译器会在编译阶段就能够帮我们发现类似这样的问题。
```java
List<String> arrayList = new ArrayList<String>();
...
//arrayList.add(100); 在编译阶段，编译器就会报错
```
这使得代码具有更好的可读性。人们一看就知道这个数组列表中包含的是String对象。

> 注释：前面已经提到，在Java SE 7及以后的版本中，构造函数中可以省略泛型类型：
>
> ```java
> ArrayList<String> files = new ArrayList<>();
> ```
>
> 省略的类型可以从变量的类型推断得出。

编译器也可以很好地利用这个信息。当调用get的时候，不需要进行强制类型转换，编译器就知道返回值类型为String，而不是Object：

```java
String filename = files.get(0);
```

编译器还知道ArrayList< String> 中add方法有一个类型为String的参数。这将比使用Object类型的参数安全一些。现在， 编译器可以进行检査，避免插入错误类型的对象。例如：

```java
files.add(new File("...")); // can only add String objects to an ArrayList<String>
```

是无法通过编译的。出现编译错误比类在运行时出现类的强制类型转换异常要好得多。

类型参数的魅力在于：使得程序具有更好的可读性和安全性。  

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

对此总结成一句话：泛型类型在逻辑上可以看成是多个不同的类型，实际上都是相同的基本类型。

## 2 泛型的使用
泛型有三种使用方式，分别为：泛型类、泛型接口、泛型方法

### 2.1 泛型类

泛型类型用于类的定义中，被称为泛型类。通过泛型可以完成对一组类的操作对外开放相同的接口。最典型的就是各种容器类，如：List、Set、Map。

泛型类的最基本写法（这么看可能会有点晕，会在下面的例子中详解）：
```java
class 类名称 <泛型标识：可以随便写任意标识号，标识指定的泛型的类型> {
	private 泛型标识 /*（成员变量类型）*/ var; 
	.....
	}
}
```
一个最普通的泛型类：

```java
//此处T可以随便写为任意标识，常见的如T、E(表示集合的元素类型)、K、V(K V分别表示表的关键字与值的类型)等形式的参数常用于表示泛型
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

> 注意：泛型的类型参数只能是类类型，不能是简单类型。不能对确切的泛型类型使用instanceof操作。如下面的操作是非法的，编译时会出错。
>
> ```java
> if(ex_num instanceof Generic<Number>){ }
>```


### 2.2 泛型接口

泛型接口与泛型类的定义及使用基本相同。泛型接口常被用在各种类的生产器中，可以看一个例子：
```java
//定义一个泛型接口
public interface Generator<T> {
    public T next();
}
```
```java
public class Apple<T> {
	private T info;
	public Apple(){}
	//下面方法中使用T类型形参来定义构造器
	public Apple(T info) {
		this.info=info;
	}
	public void setInfo(T info) {
		this.info=info;
	}
	public T getInfo(){
		return this.info;
	}
	public static void main(String[] args) {
		//由于传给T形参的是String，所以构造器参数只能是String
		Apple<String> a1=new Apple<>("苹果");
		System.out.println(a1.getInfo());
		//由于传给T形参的是Double，所以构造器参数只能是Double或者double
		Apple<Double> a2=new Apple<>("5.67");
		System.out.println(a2.getInfo());
	}
}
```

上面程序定义了一个带泛型申明的`Apple<T>`类（不要理会这个类型形参是否具有实际意义），使用`Apple<T>`类时就可为T类型形参传入实际类型，这样就可以生成如`Apple<String>`,`Apple<Double>.`..形式的多个逻辑子类，物理上并不存在。

当实现泛型接口的类，未传入泛型实参时：

```java
/**
 * 未传入泛型实参时，与泛型类的定义相同，在声明类的时候，需将泛型的声明也一起加到类中
 * 即：class FruitGenerator<T> implements Generator<T>{
 * 如果不声明泛型，如：class FruitGenerator implements Generator<T>，编译器会报错："Unknown class"
 */
class FruitGenerator<T> implements Generator<T> {
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
**并不存在泛型子类**

前面提到可以把`ArrayList<String>`类当成ArrayList的子类，事实上，`ArrayList<String>`类也确实像一种特殊的ArrayList类：该`ArrayList<String>`对象只能添加String对象作为集合元素，但实际上，系统并没有为`ArrayList<String>`对象生成新的class文件，而且也不会把`ArrayList<String>`当成新类来处理。

```java
List<String> l1=new ArrayList<>();
List<Integer> l2=new ArrayList<>();
System.out.println(l1.getclass()==l2.getclass());
```

对于上面的代码，输出false，因为不管泛型的实际类型参数是什么，他们在运行时总有同样的类（class）

不管为泛型的类型形参传入哪一种类型实参，对于Java来说，他们仍然被当成同一个类处理，在内存总也只占用一块内存空间，因此在静态方法，静态初始化块或者静态变量的声明和初始化中不允许使用类型形参，如下例：

```java
public class R<T> {
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

## 3 泛型方法

泛型类，是在实例化类的时候指明泛型的具体类型；泛型方法，是在调用方法的时候指明泛型的具体类型 。

```java
/**
 * 泛型方法的基本介绍
 * @param tClass 传入的泛型实参
 * @return T 返回值为T类型
 * 说明：
 *     1）public与返回值中间<T>非常重要，可以理解为声明此方法为泛型方法。
 *     2）只有声明了<T>的方法才是泛型方法，泛型类中的使用了泛型的成员方法并不是泛型方法。
 *     3）<T>表明该方法将使用泛型类型T，此时才可以在方法中使用泛型类型T。
 *     4）与泛型类的定义一样，此处T可以随便写为任意标识，常见的如T、E、K、V等形式的参数常用于表示泛型。
 */
public <T> T genericMethod(Class<T> tClass) throws InstantiationException,IllegalAccessException {
		T instance = tClass.newInstance();
		return instance;
}

class ArrayAlg
{
	public static <T> T getMiddle(T... a)
	{
		return a[a.length / 2];
	}
}
```

```
Object obj = genericMethod(Class.forName("com.test.test"));
```

这个方法是在普通类中定义的，而不是在泛型类中定义的。然而，这是一个泛型方法，可以从尖括号和类型变量看出这一点。注意，类型变量放在修饰符（这里是public static)的后面，返回类型的前面。

泛型方法可以定义在普通类中，也可以定义在泛型类中。

当调用一个泛型方法时，在方法名前的尖括号中放r入具体的类型：

```java
String middle = ArrayAlg.<String>getMiddle("JohnM", "Q.n", "Public");
```

在这种情况（实际也是大多数情况）下，方法调用中可以省略<\String>类型参数。编译器有足够的信息能够推断出所调用的方法。它用names的类型（即 String[]) 与泛型类型T[]进行匹配并推断出T一定是String。也就是说，可以调用

```java
String middle = ArrayAlg.getHiddle("John", "Q.", "Public");  
```

几乎在大多数情况下，对于泛型方法的类型引用没有问题。 偶尔， 编译器也会提示错误， 此时需要解译错误报告。看一看下面这个示例：

```java
double middle = ArrayAlg.getMiddle(3.14, 1729, 0);
```

错误消息会以晦涩的方式指出（不同的编译器给出的错误消息可能有所不同）：解释这句代码有两种方法，而且这两种方法都是合法的。简单地说，编译器将会自动打包参数为1个Double和2个Integer对象，而后寻找这些类的共同超类型。事实上；找到2个这样的超类型：Number和Comparable接口，其本身也是一个泛型类型。在这种情况下，可以采取的补救措施是将所有的参数写为double值。 

光看上面的例子有的同学可能依然会非常迷糊，我们再通过一个例子，把我泛型方法再总结一下。

```java
public class GenericTest {
	//这个类是个泛型类，在上面已经介绍过
	public class Generic<T> {     
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

## 4 泛型变量的限定

有时，类或方法需要对类型变量加以约束。下面是一个典型的例子。我们要计算数组中的最小元素：

```java
class ArrayAIg
{
	public static <T> T min(T[] a) // almost correct
	{
		if (a == null || a.length = 0) return null;
		T smallest = a[0] ;
		for (int i = 1; i < a.length; i++)
			if (smallest.compareTo(a[i]) > 0) smallest = a[i];
				return smallest;
	}
}
```

但是，这里有一个问题。请看一下min方法的代码内部。变量smallest类型为T，这意味着它可以是任何一个类的对象。怎么才能确信T所属的类有compareTo方法呢？解决这个问题的方案是将T限制为实现了Comparable接口（只含一个方法compareTo的标准接口）的类。可以通过对类型变量T设置限定（bound) 实现这一点：

```java
public static <T extends Comparab1e> T min(T[] a) . . .
```

实际上Comparable接口本身就是一个泛型类型。目前，我们忽略其复杂性以及编译器产生的警告。第8.8节讨论了如何在Comparable接口中适当地使用类型参数。现在，泛型的min方法只能被实现了Comparable接口的类（如String、LocalDate等）的数组调用。由于Rectangle类没有实现Comparable接口，所以调用min将会产生一个编译错误。

> C++注释：在C++中不能对模板参数的类型加以限制。如果程序员用一个不适当的类型实例化一个模板，将会在模板代码中报告一个（通常是含糊不清的）错误消息。

读者或许会感到奇怪——在此为什么使用关键字extends而不是implements?毕竟，Comparable是一个接口。下面的记法

```java
<T extends BoundingType>
```

表示T应该是绑定类型的子类型（subtype)。T和绑定类型可以是类，也可以是接口。选择关键字extends的原因是更接近子类的概念，并且Java的设计者也不打算在语言中再添加一个新的关键字（如sub)。

一个类型变量或通配符可以有多个限定，例如：

```java
T extends Comparable & Serializable
```

限定类型用“&”分隔，而逗号用来分隔类型变量。

在Java的继承中，可以根据需要拥有多个接口超类型，但限定中至多有一个类。如果用一个类作为限定，它必须是限定列表中的第一个。

在程序清单8-2的程序中，重新编写了一个泛型方法minmax。这个方法计算泛型数组的最大值和最小值，并返回Pair< T>。

程序清单 8-2 pair2/PairTest2.java

```java
package pair2;

import java.time.*;

/**
* ©version 1.02 2015-06-21
* ©author Cay Horstmann
*/

public class PairTest2
{
	public static void main(String[] args)
	{
		Local Date[] birthdays =
			{
				Local Date.of(1906, 12, 9), // G. Hopper
				Local Date.of(1815, 12, 10), // A. Lovelace
				Local Date.of(1903, 12, 3), // J. von Neumann
				Local Date.of(1910, 6, 22), // K. Zuse
			};
		Pair<LocalDate> mm = ArrayAlg.minmax(birthdays);
		System.out.println("min = " + min.getFirst());
		System.out.println("max = " + mm.getSecond());
	}
}

class ArrayAlg
{
	/**
	Gets the minimum and maximum of an array of objects of type T.
	@param a an array of objects of type T
	©return a pair with the min and max value, or null if a is
	null or empty
	*/
	
	public static <T extends Comparabl> Pair<T> minmax(T[] a)
	{
		if (a == null || a.length == 0) return null ;
		T min = a[0];
		T max = a[0];
		for (int i = 1; i < a.length;i++)
		{
			if (min.compareTo(a[i]) > 0) min = a[i];
			if (max.compareTo(a[i]) < 0) max = a[i];
		}
		return new Pairo<>(min, max);
	}
}  
```

## 5 泛型代码和虚拟机

### 5.1 类型擦除

无论何时定义一个泛型类型，都自动提供了一个相应的原始类型（raw type）。原始类型的名字就是删去类型参数后的泛型类型名。擦除（erased）类型变量，并替换为限定类型（无限定的变量用Object）。

例如，Pair< T> 的原始类型如下所示：

```java
public class Pair
{
	private Object first;
	private Object second;
	public Pair(Object first, Object second)
	{
		this.first = first;
		this.second = second;
    }
	public Object getFirst() { return first; }
	public Object getSecond() { return second; }
	public void setFirst(Object newValue) { first = newValue; }
	public void setSecond(Object newValue) { second = newValue; }
}  
```

因为T是一个无限定的变量，所以直接用Object替换。

结果是一个普通的类，就好像泛型引入Java语言之前已经实现的那样。

在程序中可以包含不N类型的Pair，例如，Pair< String>或Pair< LocalDate>。而擦除类型后就变成原始的Pair类型了。

>C++注释：就这点而言，Java泛型与C++模板有很大的区别。C++中每个模板的实例化产生不同的类型，这一现象称为“模板代码膨账”。Java不存在这个问题的困扰。

原始类型用第一个限定的类型变量来替换，如果没有给定限定就用Object替换。例如，类Pair< T>中的类型变量没有显式的限定，因此，原始类型用Object替换T。假定声明了一个不同的类型。

```java
public class Interval <T extends Comparable & Serializable> implements Serializable
{
	private T lower;
	private T upper;
	public Interval (T first, T second)
	{
		if (first.compareTo(second) <= 0) { lower = first; upper = second; }
		else { lower = second; upper = first; }
	}
}
```

原始类型 Interval 如下所示：

```java
public class Interval implements Serializable
{
	private Comparable lower;
	private Comparable upper;
	public Interval (Comparable first, Comparable second) { . . . }
}
```

> 注释：读者可能想要知道切换限定：classInterval<T extends Serializable & Comparable>会发生什么。 如果这样做，原始类型用Serializable替换T, 而编译器在必要时要向Comparable插入强制类型转换。为了提高效率，应该将标签（tagging) 接口（即没有方法的接口）放在边界列表的末尾。  

### 5.2 翻译泛型表达式
当程序调用泛型方法时，如果擦除返回类型，编译器插入强制类型转换。例如，下面这个语句序列

```java
Pair<Employee> buddies = ...;
Employee buddy = buddies.getFirst();
```

擦除getFirst的返回类型后将返回Object类型。编译器自动插入Employee的强制类型转换。也就是说，编译器把这个方法调用翻译为两条虚拟机指令：

- 对原始方法Pair.getFirst的调用。
- 将返回的Object类型强制转换为Employee类型。

当存取一个泛型域时也要插入强制类型转换。假设Pair类的first域和second域都是公有的（也许这不是一种好的编程风格，但在Java中是合法的)。表达式：

```java
Employee buddy = buddies.first;
```

也会在结果字节码中插入强制类型转换。

### 5.3 翻译泛型方法

### 5.4 调用遗留代码

## 6 约束与局限性

在下面几节中，将阐述使用Java泛型时需要考虑的一些限制。大多数限制都是由类型擦除引起的。

### 6.1 不能用基本类型实例化类型参数

不能用类型参数代替基本类型。因此，没有Pair< double>，只有Pair< Double>。当然，其原因是类型擦除。擦除之后，Pair类含有Object类型的域，而Object不能存储double值

这的确令人烦恼。但是，这样做与Java语言中基本类型的独立状态相一致。这并不是一个致命的缺陷——只有8种基本类型，当包装器类型（wrappertype)不能接受替换时，可以使用独立的类和方法处理它们。

### 6.2 运行时类型查询只适用于原始类型
虚拟机中的对象总有一个特定的非泛型类型。因此，所有的类型查询只产生原始类型。

例如：

```java
if (a instanceof Pair<String>) // Error
```

实际上仅仅测试 a 是否是任意类型的一个 Pair。下面的测试同样如此：

```java
if (a instanceof Pair<T>) // Error
```

或强制类型转换：

```java
Pair<String> p = (Pair<String>) a; // Warning-can only test that a is a Pair
```

为提醒这一风险，试图查询一个对象是否属于某个泛型类型时，倘若使用instanceof会得到一个编译器错误，如果使用强制类型转换会得到一个警告。

同样的道理，getClass方法总是返回原始类型。例如：

```java
Pair<String> stringPair = . . .;
Pair<Employee> employeePair = . . .;
if (stringPair.getClass() == employeePair.getClass()) // they are equal
```

其比较的结果是 true, 这是因为两次调用 getClass 都将返回 Pair.class 。  

### 6.3 不能创建参数化类型的数组
不能实例化参数化类型的数组， 例如：

```java
Pair<String>[] table = new Pair<String>[10]; // Error
```

这有什么问题呢？ 擦除之后，table的类型是Pair[]。可以把它转换为Object[]:

```java
Object[] objarray = table;
```

数组会记住它的元素类型，如果试图存储其他类型的元素，就会抛出一个ArrayStoreException异常：

```java
objarray[0] = "Hello"; // Error component type is Pair
```

不过对于泛型类型，擦除会使这种机制无效。以下赋值：

```java
objarray[0] = new Pair <Employee>();
```

能够通过数组存储检査，不过仍会导致一个类型错误。出于这个原因，不允许创建参数化类型的数组。

需要说明的是，只是不允许创建这些数组，而声明类型为Pair< String>[]的变量仍是合法的。不过不能用new Pair< String>[10]初始化这个变量。

> 注释：可以声明通配类型的数组，然后进行类型转换：Pair< String>[] table = (Pair< String>[]) new Pair< ?>[10];
>
> 结果将是不安全的。如果在table[0]中存储一个Pair< Employee>,然后对table[0].
> getFirst()调用一个String方法，会得到一个ClassCastException异常。

> 提示：如果需要收集参数化类型对象，只有一种安全而有效的方法：使用ArrayList:ArrayList< Pair < String>>

### 6.6 关于泛型数组

就像不能实例化一个泛型实例一样，也不能实例化数组。不过原因有所不同，毕竟数组会填充null值，构造时看上去是安全的。不过， 数组本身也有类型，用来监控存储在虚拟机中的数组。这个类型会被擦除。 例如， 考虑下面的例子：

```java
public static <T extends Comparable〉T[] minmax(T[] a) { T[] mm = new T[2]; . . . } // Error
```

类型擦除会让这个方法永远构造Comparable[2]数组。

如果数组仅仅作为一个类的私有实例域，就可以将这个数组声明为Object[]，并且在获取元素时进行类型转换。例如

ArrayList类可以这样实现：

```java
public class ArrayList<E>
{
	private Object[] elements;
    ...
	@SuppressWarnings("unchecked") public E get (int n) { return (E) elements[n]; }
	public void set (int n, E e) { elements[n] = e; } // no cast needed
}
```

实际的实现没有这么清晰：

```java
public class ArrayList<E>
{
	private E[] elements;
	public ArrayList() { elements = (E[]) new Object[10]; }
}
```

这里，强制类型转换E[]是一个假象，而类型擦除使其无法察觉。

由于minmax方法返回T[]数组，使得这一技术无法施展， 如果掩盖这个类型会有运行时错误结果。假设实现代码：

```java
public static <T extends Comparable> T[] minmax(T... a)
{
	Object[] mm = new Object[2];
	return (T[]) mm; // compiles with warning
}
```

调用

```java
String[] ss = ArrayAlg.minmax("Tom", "Dick", "Harry");
```

编译时不会有任何警告。当Object[]引用赋给Comparable[]变量时，将会发生ClassCastException异常。

在这种情况下，最好让用户提供一个数组构造器表达式：

```java
String[] ss = ArrayAlg.minmax(String[]::new,"Tom", "Dick", "Harry");
```

构造器表达式Stringxnew指示一个函数，给定所需的长度，会构造一个指定长度的String数组。

minmax方法使用这个参数生成一个有正确类型的数组：

```java
public static <T extends Comparable> T[] minmax(IntFunction<T[]> constr, T... a)
{
	T[] mm = constr.apply(2);
    ...
}
```

比较老式的方法是利用反射，调用Array.newlnstance:

```java
public static <T extends Comparable> T[] minmax(T... a)
{
	T[] mm = (T[]) Array.newlnstance(a.getClass().getComponentType() , 2);
	...
}
```

ArrayList类的toArray方法就没有这么幸运。它需要生成一个T[] 数组，但没有成分类型。因此，有下面两种不同的形式：

```
Object[] toArray()
T[] toArray(T[] result)
```

第二个方法接收一个数组参数。如果数组足够大，就使用这个数组。否则，用result的成分类型构造一个足够大的新数组。

## 7 泛型类型的继承规则
在使用泛型类时，需要了解一些有关继承和子类型的准则。下面先从许多程序员感觉不太直观的情况开始。考虑一个类和一个子类， 如 Employee 和 Manager。Pair< Manager> 是Par< Employee> 的一个子类吗？ 答案是“ 不是”， 或许人们会感到奇怪。例如， 下面的代码将不能编译成功：

```java
Manager[] topHonchos = ...;
Pair<Employee> result = ArrayAlg.ininmax(topHonchos) ; // Error
```

minmax方法返回Pair< Manager>，而不是Pair< Employee>，并且这样的赋值是不合法的。

无论S与T有什么联系 （如图8-1所示，) 通常，Pair< S>与Pair< T> S有什么联系。

这一限制看起来过于严格，但对于类型安全非常必要。假设允许将 Pair< Manager> 转换为Pair< Employee>。考虑下面代码： 

```java
Pair<Manager> managerBuddies = new Pair<>(ceo, cfo);
Pair<Employee> employeeBuddies = managerBuddies; // illegal , but suppose it wasn't
employeeBuddies.setFirst(lowlyEmployee);
```

显然，最后一句是合法的。但是employeeBuddies和managerBuddies引用了同样的对象。现在将CFO和一个普通员工组成一对，这对于说应该是不可能的。

> 注释：必须注意泛型与Java数组之间的重要区别。可以将一个Manager[] 数组賦给一个类型为Employee[]的变量：
>
> ```java
> Manager[] managerBuddies = { ceo, cfo };
> Employee[] employeeBuddies = managerBuddies; // OK
> ```
>
> 然而，数组带有特别的保护。如果试图将一个低级别的雇员存储到employeeBuddies[0]，虚拟机将会抛出ArrayStoreException异常。  

泛型类可以扩展或实现其他的泛型类。就这一点而言，与普通的类没有什么区别。例如，ArrayList< T>类实现List< T>接口。这意味着，一个ArrayList< Manager>可以被转换为一个List< Manager>。但是，如前面所见，一个ArrayList< Manager>不是一个ArrayList < Employee>或List< Employee>。

## 8 泛型通配符

### 8.1 通配符概念
通配符类型中，允许类型参数变化。例如，通配符类型

```java
Pair<? extends Employe>
```

表示任何泛型Pair类型，它的类型参数是Employee的子类，如Pair< Manager>，但不是Pair< String>。

假设要编写一个打印雇员对的方法， 像这样：

```java
public static void printBuddies(Pair<Employee> p)
{
	Employee first = p.getFirst();
	Employee second = p.getSecond();
	Systefn.out.println(first.getName() + " and " + second.getName() + " are buddies.");
}
```

正如前面讲到的，不能将 Pair< Manager> 传递给这个方法，这一点很受限制。解决的方法很简单：使用通配符类型：

```java
public static void printBuddies(Pair<? extends Employee> p)
```

类型Pair< Manager> 是Pair< ? extends Employee>的子类型

使用通配符会通过Pair< ? extends Employee>的引用破坏Pair< Manager>吗？

```java
Pair<Manager> managerBuddies = new Pairo<>(ceo, cfo) ;
Pair<? extends Employee> wildcardBuddies = managerBuddies; // OK
wi1dcardBuddies.setFirst(lowlyEnployee); // compile-time error
```

这可能不会引起破坏。对 setFirst 的调用有一个类型错误。要了解其中的缘由，请仔细看一看类型 Pair< ? extends Employee>。其方法似乎是这样的：

```java
? extends Employee getFirst()
void setFirst(? extends Employee)
```

这样将不可能调用 setFirst 方法。编译器只知道需要某个Employee的子类型，但不知道具体是什么类型。它拒绝传递任何特定的类型。毕竟？不能用来匹配。

使用getFirst就不存在这个问题： 将getFirst的返回值赋给一个Employee的引用完全合法。

这就是引入有限定的通配符的关键之处。现在已经有办法区分安全的访问器方法和不安全的更改器方法了。

### 8.3 无限定通配符
还可以使用无限定的通配符，例如，Pair< ?>。初看起来，这好像与原始的Pair类型一样。实际上， 有很大的不同。类型Pair< ?>有以下方法：

```java
? getFirst()
void setFirst(7)
```

getFirst 的返回值只能赋给一个Object。setFirst方法不能被调用， 甚至不能用Object调用。Pair< ?> 和Pair本质的不同在于：可以用任意Object对象调用原始Pair类的setObject方法。

> 注释：可以调用 setFirst(null)

为什么要使用这样脆弱的类型？它对于许多简单的操作非常有用。例如，下面这个方法将用来测试一个pair是否包含一个null 引用，它不需要实际的类型。

```java
public static boolean hasNulls(Pair<?> p)
{
	return p.getFirst() = null || p.getSecond() = null;
}
```

通过将hasNulls转换成泛型方法，可以避免使用通配符类型：

```java
public static <T> boolean hasNulls(Pair<T> p)
```

但是，带有通配符的版本可读性更强。  