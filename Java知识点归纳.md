# Java知识点归纳

## 1 Java中&&和&的区别

Java中&&和&都是表示与的逻辑运算符，都表示逻辑运输符and，当两边的表达式都为true的时候，整个运算结果才为true，否则为false。

&&的短路功能，当第一个表达式的值为false的时候，则不再计算第二个表达式；&则两个表达式都执行。

&可以用作位运算符，当&两边的表达式不是Boolean类型的时候，&表示按位操作。

```java
&&第一个表达式为false
int i = 0;
if(i == 3  && ++i > 0) {
}
System.out.println("i = " + i);
console：i = 0==>第二个表达式没有执行
第一个表达式为false
int i = 0;
if(i == 3  & ++i > 0) {
}
System.out.println("i = "+ i);
console:i = 1 ==>第二个表达式执行了
```

## 2 String,StringBuffer与StringBuilder的区别

String在Java编程中广泛应用，首先从源码进行分析

从这我们可以得知，String底层是一个final类型的字符数组，所以String的值是不可变的，每次对String的操作都会生成新的String对象，造成内存浪费

而StringBuffer和StringBuilder就不一样了，他们两都继承了AbstractStringBuilder抽象类，从AbstractStringBuilder抽象类中我们可以看到他们的底层都是可变的字符数组，所以在进行频繁的字符串操作时，建议使用StringBuffer和StringBuilder来进行操作。
接着看一下他们的继承结构以及部分源码实现

```
[interface]				[interface]
CharSequence			Appendable
	|	|________		 ___|
	|			|		|
String			AbstractStringBuilder
						|
			StringBuilder	StringBuffer
			线程不安全		线程安全
			执行速度快		执行速度慢

```

StringBuilder类在Java 5中被提出，它和StringBuffer之间的最大不同在于StringBuilder 的方法不是线程安全的（不能同步访问）由于StringBuilder相较于StringBuffer有速度优势，所以多数情况下建议使用StringBuilder类。然而在应用程序要求线程安全的情况下，则必须使用 StringBuffer 类。

再详细说一下String，String并不是基本数据类型，而是一个对象。字符串为对象，那么在初始化之前，它的值为null，到这里就有必要提下””、null、new String()三者的区别。null表示string还没有new，也就是说对象的引用还没有创建，也没有分配内存空间给他，而””、new String()则说明了已经new了，只不过内部为空，但是它创建了对象的引用，是需要分配内存空间的。

Java的虚拟机在内存中开辟出一块单独的区域，用来存储字符串对象，这块内存区域被称为字符串缓冲池。那个Java的字符串缓冲池是如何工作的呢？

```
a____________________
b____________________abc


c____________________xyz
对象引用变量		字符串缓冲池
```

```java
String a = "abc";
String b = "abc";
String c = new String("xyz");
```
例如上边的代码：

```java
String a = "abc";
```

创建字符串的时候先查找字符串缓冲池中有没有相同的对象，如果有相同的对象就直接返回该对象的引用，如果没有相同的对象就在字符串缓冲池中创建该对象，然后将该对象的引用返回。对于这一步而言，缓冲池中没有abc这个字符串对象，所以首先创建一个字符串对象，然后将对象引用返回给a。

```java
String b = "abc";
```

这一句也是想要创建一个对象引用变量b使其指向abc这一对象。这时，首先查找字符串缓冲池，发现abc这个对象已经有了，这是就直接将这个对象的引用返回给b，此时a和b就共用了一个对象abc，不过不用担心，a改变了字符串不会影响b，因为字符串都是常量，一旦创建就没办法修改了，除非创建一个新的对象。

```java
String c = new String("xyz");
```

查找字符串缓冲池发现没有xyz这个字符串对象，于是就在字符串缓冲池中创建了一个xyz对象然后再将引用返回。

从上边的分析可以看出，当new一个字符串时并不一定是创建了一个新的对象，有可能是与别的引用变量共同使用了同一个对象。下面看几个常见的有关字符串缓冲池的问题。

创建了几个对象
```java
String a = "abc";
String b = "abc";
String c = new String("xyz");
String d = new String("xyz");
String e = "ab" + "cd";
```

这个程序与上边的程序比较相似，我们分比来看一下：

1、String a = “abc”；这一句由于缓冲池中没有abc这个字符串对象，所以会创建一个对象；

2、String b = “abc”；由于缓冲池中已经有了abc这个对象，所以不会再创建新的对象；

3、String c = new String(“xyz”)；由于没有xyz这个字符串对象，所以会首先创建一个xyz的对象，然后这个字符串对象由作为String的构造方法，在内存中（不是缓冲池中）又创建了一个新的字符串对象，所以一共创建了两个对象；

4、String d = new String(“xyz”)；缓冲池中已有该字符串对象，则缓冲池中不再创建该对象，然后会在内存中创建一个新的字符串对象，所以只创建了一个对象；

5、String e = ”ab” + ”cd”；由于常量的值在编译的时候就被确定了。所以这一句等价于String e = ”abcd”；所以创建了一个对象；

所以创建的对象的个数分别是：1,0,2,1,1。

到底相等不相等

我们在学习Java时就知道两个字符串对象相等的判断要用equal而不能使用==，但是学习了字符串缓冲池以后，应该知道为什么不能用==， 什么情况下==，和equal是等价的，首先，必须知道的是，equal比较的是两个字符串的值是否相等，而==比较的是两个对象的内存地址是否相等，下面我们就通过几个程序来看一下。

实例一：

```java
public static void main(String[] args) { 
	String s1 = "Monday";
	String s2 = "Monday";
	if (s1 == s2) {
		System.out.println("s1 == s2"); 
	}
	else {
		System.out.println("s1 != s2"); 
		}
}
```

输出结果：
```
s1 == s2
```
分析：通过上边的介绍字符串缓冲池，我们知道s1和s2都是指向字符串缓冲池中的同一个对象，所以内存地址是一样的，所以用==可以判断两个字符串是否相等。

实例二：
```java
public static void main(String[] args) { 
	String s1 = "Monday"; 
	String s2 = new String("Monday"); 
	if (s1 == s2) {
		System.out.println("s1 == s2"); 
    }
	else {
		System.out.println("s1 != s2"); 
	}
	if (s1.equals(s2)) {
		System.out.println("s1 equals s2"); 
	}
	else {
		System.out.println("s1 not equals s2"); 
	}
} 
```

输出结果：

```
s1 != s2
s1 equals s2
```

分析：由上边的分析我们知道，String s2 = new String(“Monday”)；这一句话没有在字符串缓冲池中创建新的对象，但是会在内存的其他位置创建一个新的对象，所以s1是指向字符串缓冲池的，s2是指向内存的其他位置，两者的内存地址不同的。

实例三：

```java
public static void main(String[] args) {
	String s1 = "Monday";
	String s2 = new String("Monday");
	s2 = s2.intern();
	if (s1 == s2) {
		System.out.println("s1 == s2"); 
	}
	else {
		System.out.println("s1 != s2"); 
	}
	if (s1.equals(s2)) {
		System.out.println("s1 equals s2"); 
	}
	else {
		System.out.println("s1 not equals s2"); 
	}
}
```

输出结果：

```
s1 == s2
s1 equals s2
```
分析：先来说说intern()这个方法的作用吧，这个方法的作用是返回在字符串缓冲池中的对象的引用，所以s2指向的也是字符串缓冲池中的地址，和s1是相等的。

实例四：

```java
public static void main(String[] args) { 
	String Monday = "Monday";  
	String Mon = "Mon";  
	String  day = "day";  
	System.out.println(Monday == "Mon" + "day");  
	System.out.println(Monday == "Mon" + day);  
}
```

输出结果

```
true
false
```

分析：第一个为什么等于true我们已经说过了，因为两者都是常量所以在编译阶段就已经能确定了，在第二个中，day是一个变量，所以不能提前确定他的值，所以两者不相等，从这个例子我们可以看出，只有+连接的两边都是字符串常量时，引用才会指向字符串缓冲池，否则都是指向内存中的其他地址。

实例五：

```java
public static void main(String[] args) { 
	String Monday = "Monday";  
	String Mon = "Mon";  
	final String  day = "day";  
	System.out.println(Monday == "Mon" + "day");  
	System.out.println(Monday == "Mon" + day);  
}
```

输出结果：

```
true
true
```

分析：加上final后day也变成了常量，所以第二句的引用也是指向的字符串缓冲池。

## 3 Array和ArrayList的区别

可以将 ArrayList想象成一种“会自动扩增容量的Array”。

Array（[]）：最高效；但是其容量固定且无法动态改变；ArrayList：  容量可动态增长；但牺牲效率；当我们基于效率和类型检验，应尽可能使用Array，无法确定数组大小时使用ArrayList。

不过当你试着解决更一般化的问题时，Array的功能就可能过于受限。

Array名称本身实际上是个reference，指向heap之内得某个实际对象。这个对象可经由“Array初始化语法”被自动产生，也可以以new表达式手动产生。Array可做为函数返回值，因为它本身是对象的reference；

对象数组与基本类型数组在运用上几乎一模一样，唯一差别在于，前者持有得是reference，后者直接持有基本型别之值；

例如：

```java
string []staff=new string[100];
int []num=new int[10];
```
对数组的一些基本操作，像排序、搜索与比较等是很常见的。因此在Java中提供了Arrays类协助这几个操作：

```
1.sort()
2.binarySearch(),equals()
3.fill()
4.asList()
```

不过Arrays类没有提供删除方法，而ArrayList中有remove()方法。

## 4 ==和equal的区别

#### 1.’=='运算符

我们通常用’=='来比较两个变量是否相等，当比较的变量是基本类型，且都是数值类型，且’=='比较的是其数值。当比较的是引用变量，只有当他们都指向同一个对象的时候才会返回true。

其不可以用去在类型上比较没有继承关系的的两个变量，编译器会报错。

```java
int it 65 ;
float f = 65.0f
//输出true
System.out.println(it == f);
char ch = 'A';
//输出true
System.out.println(ch == it);
String str1 = new String("hello");
String str2 = new String("hello");
//返回false
System.out.prinln(str1 == str2);
```

上面例子中由于"it"，"f"，"ch"都是基本类型且都为数值类型，因此其比较的是数值大小。

而对于"str1"，"str2"，因为它们都是引用型变量，它们分别指向两个通过new关键字创建的String对象，因此"str1"，"str2"不相等。

"hello"和new String("hello")的区别：当Java程序直接使用了字符串直接量(形如"hello"，包括可以在编译时就计算出来的的字符串值)时，JVM将会使用常量池来管理这些字符串；当使用new String("hello")来创建时，JVM会先使用常量池来管理"hello"直接量，再调用String类的构造器来创建一个新的String对象，新创建的的String对象会存放到堆内存中去，也就是说new String("hello")产生了两个字符串对象。

常量池是专门用来管理在编译时被确定并保存在已编译的的.class文件中的一些数据。它包括了关于类，方法，接口中的常量，还包括字符串常量。

JVM常量池保证相同的字符串直接量只有一个，不会产生多个副本。String对象所引用的字符串直接量可以在编译期就确定下来。使用new String()创建的字符串对象是运行时创建出来的，它被保存在运行时内存区(即堆内存中)；

#### 2.equal方法

该方法属于object类，因此所有引用变量都可以调用这个方法来判断是否与其他引用变量相等，但是如果这样的话该方法和"=="来判断两个对象相等的标准和使用没有区别。

很多时候，程序判断两个引用变量是否相等，也希望有一种类似于”值相等“的判断规则，并不严格要求两个引用变量指向同一个对象。这时候可以通过重写equal方法。

**String类就重写了equal方法，该方法可以判断两个字符串对象所引用的字符串直接量是否相等。**

## 5   map的分类和常见的情况

Java为数据结构中的映射定义了一个接口Java.util.Map;它有四个实现类,分别是HashMap Hashtable LinkedHashMap 和TreeMap.

Map主要用于存储健值对，根据键得到值，因此不允许键重复(重复了覆盖了),但允许值重复。

Hashmap是一个最常用的Map,它根据键的HashCode值存储数据，根据键可以直接获取它的值，具有很快的访问速度，遍历时，取得数据的顺序是完全随机的。HashMap最多只允许一条记录的键为Null；允许多条记录的值为Null;HashMap不支持线程的同步，即任一时刻可以有多个线程同时写HashMap；可能会导致数据的不一致。如果需要同步，可以用 Collections的synchronizedMap方法使HashMap具有同步的能力，或者使用ConcurrentHashMap。

Hashtable与HashMap类似,它继承自Dictionary类，不同的是：它不允许记录的键或者值为空；它支持线程的同步，即任一时刻只有一个线程能写Hashtable,因此也导致了Hashtable在写入时会比较慢。

LinkedHashMap是HashMap的一个子类，保存了记录的插入顺序，在用Iterator遍历LinkedHashMap时，先得到的记录肯定是先插入的.也可以在构造时用带参数，按照应用次数排序。在遍历的时候会比HashMap慢，不过有种情况例外，当HashMap容量很大，实际数据较少时，遍历起来可能会比LinkedHashMap慢，因为LinkedHashMap的遍历速度只和实际数据有关，和容量无关，而HashMap的遍历速度和他的容量有关。

TreeMap实现SortMap接口，能够把它保存的记录根据键排序，默认是按键值的升序排序，也可以指定排序的比较器，当用Iterator遍历TreeMap时，得到的记录是排过序的。

一般情况下，我们用的最多的是HashMap，在Map中插入、删除和定位元素，HashMap是最好的选择。但如果您要按自然顺序或自定义顺序遍历键，那么TreeMap会更好。如果需要输出的顺序和输入的相同,那么用LinkedHashMap可以实现,它还可以按读取顺序来排列.  

HashMap是一个最常用的Map，它根据键的hashCode值存储数据，根据键可以直接获取它的值，具有很快的访问速度。HashMap最多只允许一条记录的键为NULL，允许多条记录的值为NULL。

HashMap不支持线程同步，即任一时刻可以有多个线程同时写HashMap，可能会导致数据的不一致性。如果需要同步，可以用Collections的synchronizedMap方法使HashMap具有同步的能力。

Hashtable与HashMap类似，不同的是：它不允许记录的键或者值为空；它支持线程的同步，即任一时刻只有一个线程能写Hashtable，因此也导致了Hashtable在写入时会比较慢。

LinkedHashMap保存了记录的插入顺序，在用Iterator遍历LinkedHashMap时，先得到的记录肯定是先插入的。

在遍历的时候会比HashMap慢TreeMap能够把它保存的记录根据键排序，默认是按升序排序，也可以指定排序的比较器。当用Iterator遍历TreeMap时，得到的记录是排过序的。  

## 6 Java中的hashCode和equals

### 1、关于hashCode

1. hashCode的存在主要是用于查找的快捷性，如Hashtable，HashMap等，hashCode是用来在散列存储结构中确定对象的存储地址的
2. 如果两个对象相同，就是适用于equals(Java.lang.Object) 方法，那么这两个对象的hashCode一定要相同
3. 如果对象的equals方法被重写，那么对象的hashCode也尽量重写，并且产生hashCode使用的对象，一定要和equals方法中使用的一致，否则就会违反上面提到的第2点
4. 两个对象的hashCode相同，并不一定表示两个对象就相同，也就是不一定适用于equals(Java.lang.Object) 方法，只能够说明这两个对象在散列存储结构中，如Hashtable，他们“存放在同一个篮子里“

**再归纳一下就是hashCode是用于查找使用的，而equals是用于比较两个对象的是否相等的。**

以下对hashCode的解读：

> 1.hashcode是用来查找的，如果你学过数据结构就应该知道，在查找和排序这一章有
>
> 例如内存中有这样的位置
>
> 0  1  2  3  4  5  6  7
>
> 而我有个类，这个类有个字段叫ID,我要把这个类存放在以上8个位置之一，如果不用hashcode而任意存放，那么当查找时就需要到这八个位置里挨个去找，或者用二分法一类的算法。
>
> 但如果用hashcode那就会使效率提高很多。
>
> 我们这个类中有个字段叫ID,那么我们就定义我们的hashcode为ID％8，然后把我们的类存放在取得得余数那个位置。比如我们的ID为9，9除8的余数为1，那么我们就把该类存在1这个位置，如果ID是13，求得的余数是5，那么我们就把该类放在5这个位置。这样，以后在查找该类时就可以通过ID除 8求余数直接找到存放的位置了。
>
> 2.但是如果两个类有相同的hashcode怎么办那（我们假设上面的类的ID不是唯一的），例如9除以8和17除以8的余数都是1，那么这是不是合法的，回答是：可以这样。那么如何判断呢？在这个时候就需要定义equals了。
>
> 也就是说，我们先通过hashcode来判断两个类是否存放某个桶里，但这个桶里可能有很多类，那么我们就需要再通过equals来在这个桶里找到我们要的类。
>
> 那么。重写了equals()，为什么还要重写hashCode()呢？
>
> 想想，你要在一个桶里找东西，你必须先要找到这个桶啊，你不通过重写hashcode()来找到桶，光重写equals()有什么用啊

### **2、关于equals**

1.equals和==

==用于比较引用和比较基本数据类型时具有不同的功能：比较基本数据类型，如果两个值相同，则结果为true，而在比较引用时，如果引用指向内存中的同一对象，结果为true;

equals()作为方法，实现对象的比较。由于==运算符不允许我们进行覆盖，也就是说它限制了我们的表达。因此我们复写equals()方法，达到比较对象内容是否相同的目的。而这些通过==运算符是做不到的。

2.object类的equals()方法的比较规则为：如果两个对象的类型一致，并且内容一致，则返回true,这些类有：

```java
Java.io.file,Java.util.Date,Java.lang.string,包装类（Integer,Double等）
String s1=new String("abc");
String s2=new String("abc");
System.out.println(s1==s2);
System.out.println(s1.equals(s2));
运行结果为false true
```

## 7 HashMap的实现原理

### 1. HashMap概述

HashMap是基于哈希表的Map接口的非同步实现。此实现提供所有可选的映射操作，并允许使用null值和null键。此类不保证映射的顺序，特别是它不保证该顺序恒久不变。

在Java编程语言中，最基本的结构就是两种，一个是数组，另外一个是模拟指针（引用），所有的数据结构都可以用这两个基本结构来构造的，HashMap也不例外。HashMap实际上是一个“链表散列”的数据结构，即数组和链表的结合体。

HashMap底层就是一个数组结构，数组中的每一项又是一个链表。当新建一个HashMap的时候，就会初始化一个数组。

其中Java源码如下：

```java
/**
 * The table, resized as necessary. Length MUST Always be a power of two.
 */
transient Entry[] table;
static class Entry<K,V> implements Map.Entry<K,V> {
	final K key;
	V value;
	Entry<K,V> next;
	final int hash;
	...
}
```

可以看出，Entry就是数组中的元素，每个 Map.Entry 其实就是一个key-value对，它持有一个指向下一个元素的引用，这就构成了链表。

### 2、HashMap实现存储和读取

1）存储

```java
public V put(K key, V value) {
	// HashMap允许存放null键和null值。
	// 当key为null时，调用putForNullKey方法，将value放置在数组第一个位置。
	if (key == null)
		return putForNullKey(value);
		// 根据key的keyCode重新计算hash值。
		int hash = hash(key.hashCode());
		// 搜索指定hash值在对应table中的索引。
		int i = indexFor(hash, table.length);
		// 如果 i 索引处的 Entry 不为 null，通过循环不断遍历 e 元素的下一个元素。
		for (Entry<K,V> e = table[i]; e != null; e = e.next) {
			Object k;
			if (e.hash == hash && ((k = e.key) == key || key.equals(k))) {
				// 如果发现已有该键值，则存储新的值，并返回原始值
				V oldValue = e.value;
				e.value = value;
				e.recordAccess(this);
				return oldValue;
			}
		}
		// 如果i索引处的Entry为null，表明此处还没有Entry。
		modCount++;
		// 将key、value添加到i索引处。
		addEntry(hash, key, value, i);
		return null;
}
```

根据hash值得到这个元素在数组中的位置（即下标），如果数组该位置上已经存放有其他元素了，那么在这个位置上的元素将以链表的形式存放，新加入的放在链头，最先加入的放在链尾。如果数组该位置上没有元素，就直接将该元素放到此数组中的该位置上。

hash(int h)方法根据key的hashCode重新计算一次散列。此算法加入了高位计算，防止低位不变，高位变化时，造成的hash冲突。

```java
static int hash(int h) {
	h ^= (h >>> 20) ^ (h >>> 12);
	return h ^ (h >>> 7) ^ (h >>> 4);
}
```

我们可以看到在HashMap中要找到某个元素，需要根据key的hash值来求得对应数组中的位置。如何计算这个位置就是hash算法。前面说过HashMap的数据结构是数组和链表的结合，所以我们当然希望这个HashMap里面的元素位置尽量的分布均匀些，尽量使得每个位置上的元素数量只有一个，那么当我们用hash算法求得这个位置的时候，马上就可以知道对应位置的元素就是我们要的，而不用再去遍历链表，这样就大大优化了查询的效率。

根据上面put方法的源代码可以看出，当程序试图将一个key-value对放入HashMap中时，程序首先根据该key的 hashCode()返回值决定该Entry的存储位置：如果两个Entry的key的hashCode()返回值相同，那它们的存储位置相同。如果这两个Entry的key通过equals比较返回true，新添加Entry的value将覆盖集合中原有Entry的value，但key不会覆盖。如果这两个Entry的key通过equals比较返回false，新添加的Entry将与集合中原有Entry形成 Entry链，而且新添加的Entry位于Entry链的头部——具体说明继续看addEntry()方法的说明。

**通过这种方式就可以高效的解决HashMap的冲突问题。**

2）读取

```java
public V get(Object key) {
	if (key == null)
		return getForNullKey();
	int hash = hash(key.hashCode());
	for (Entry<K,V> e = table[indexFor(hash, table.length)];e != null;e = e.next) {
		Object k;
		if (e.hash == hash && ((k = e.key) == key || key.equals(k)))
			return e.value;
	}
	return null;
}
```

从HashMap中get元素时，首先计算key的hashCode，找到数组中对应位置的某一元素，然后通过key的equals方法在对应位置的链表中找到需要的元素。

3）归纳起来简单地说，HashMap在底层将key-value当成一个整体进行处理，这个整体就是一个Entry对象。HashMap底层采用一个Entry[]数组来保存所有的key-value对，当需要存储一个Entry对象时，会根据hash算法来决定其在数组中的存储位置，在根据equals方法决定其在该数组位置上的链表中的存储位置；当需要取出一个Entry时，也会根据hash算法找到其在数组中的存储位置，再根据equals方法从该位置上的链表中取出该Entry。

### 3、HashMap的resize

当hashmap中的元素越来越多的时候，碰撞的几率也就越来越高（因为数组的长度是固定的），所以为了提高查询的效率，就要对hashmap的数组进行扩容，数组扩容这个操作也会出现在ArrayList中，所以这是一个通用的操作，很多人对它的性能表示过怀疑，不过想想我们的“均摊”原理，就释然了，而在hashmap数组扩容之后，最消耗性能的点就出现了：原数组中的数据必须重新计算其在新数组中的位置，并放进去，这就是resize。

那么hashmap什么时候进行扩容呢？当hashmap中的元素个数超过数组大小\*loadFactor时，就会进行数组扩容，loadFactor的默认值为0.75，也就是说，默认情况下，数组大小为16，那么当hashmap中元素个数超过16\*0.75=12的时候，就把数组的大小扩展为2\*16=32，即扩大一倍，然后重新计算每个元素在数组中的位置，而这是一个非常消耗性能的操作，所以如果我们已经预知hashmap中元素的个数，那么预设元素的个数能够有效的提高hashmap的性能。比如说，我们有1000个元素new HashMap(1000), 但是理论上来讲new HashMap(1024)更合适，不过上面annegu已经说过，即使是1000，hashmap也自动会将其设置为1024。 但是new HashMap(1024)还不是更合适的，因为0.75\*1000 < 1000, 也就是说为了让0.75\*size > 1000, 我们必须这样new HashMap(2048)才最合适，既考虑了&的问题，也避免了resize的问题。

**总结：HashMap的实现原理：**

1. **利用key的hashCode重新hash计算出当前对象的元素在数组中的下标**
2. **存储时，如果出现hash值相同的key，此时有两种情况。(1)如果key相同，则覆盖原始值；(2)如果key不同（出现冲突），则将当前的key-value放入链表中**
3. **获取时，直接找到hash值对应的下标，在进一步判断key是否相同，从而找到对应值。**
4. **理解了以上过程就不难明白HashMap是如何解决hash冲突的问题，核心就是使用了数组的存储方式，然后将冲突的key的对象放入链表中，一旦发现冲突就在链表中做进一步的对比。**

## 8  为什么重写equals还要重写hashcode？

HashMap中，如果要比较key是否相等，要同时使用这两个函数！因为自定义的类的hashcode()方法继承于Object类，其hashcode码为默认的内存地址，这样即便有相同含义的两个对象，比较也是不相等的。HashMap中的比较key是这样的，先求出key的hashcode()，比较其值是否相等，若相等再比较equals()，若相等则认为他们是相等的。若equals()不相等则认为他们不相等。如果只重写hashcode()不重写equals()方法，当比较equals()时只是看他们是否为同一对象（即进行内存地址的比较），所以必定要两个方法一起重写。HashMap用来判断key是否相等的方法，其实是调用了HashSet判断加入元素 是否相等。重载hashCode()是为了对同一个key，能得到相同的Hash Code，这样HashMap就可以定位到我们指定的key上。重载equals()是为了向HashMap表明当前对象和key上所保存的对象是相等的，这样我们才真正地获得了这个key所对应的这个键值对。  

## 9 方法的重载与覆盖(重写)的的区别

方法的重载：

- 方法名相同

- 参数不同（参数个数不同，参数类型不同，参数相同，类型不同）

- 返回值类型可同可不同

方法的覆盖(重写)：

- **在子类集成父类是发生，对从父类中继承的方法进行改造**
- 方法名相同
- 参数相同(参数个数、类型、顺序相同)
- 返回值类型相同
- 子类覆盖方法的访问权限不小于父类中被覆盖方法的访问权限

**重载实现的是编译时的多态性，重写实现的是运行时的多态性。**

## 10   内部类可以引用他包含类的成员吗，如果可以，有没有什么限制吗？
一个内部类对象可以访问创建它的外部类对象的内容，内部类如果不是static的，那么它可以访问创建它的外部类对象的所有属性。内部类如果是static的，即为nested class，那么它只可以访问创建它的外部类对象的所有static属性。一般普通类只有public或package的访问修饰，而内部类可以实现static，protected，private等访问修饰。当从外部类继承的时候，内部类是不会被覆盖的，它们是完全独立的实体，每个都在自己的命名空间内，如果从内部类中明确地继承，就可以覆盖原来内部类的方法。

## 11 接口和抽象类的区别是什么？
Java提供和支持创建抽象类和接口。它们的实现有共同点，不同点在于： 

- 接口中所有的方法隐含的都是抽象的。而抽象类则可以同时包含抽象和非抽象的方法。 
- 类可以实现很多个接口，但是只能继承一个抽象类 
- **类可以不实现抽象类和接口声明的所有方法，当然，在这种情况下，类也必须得声明成是抽象的。**
- 抽象类可以在不提供接口方法实现的情况下实现接口。 
- Java接口中声明的变量默认都是final的。抽象类可以包含非final的变量。 
- Java接口中的成员函数默认是public的。抽象类的成员函数可以是private，protected或者是public。 
- 接口是绝对抽象的，不可以被实例化。抽象类也不可以被实例化，但是，如果它包含main方法的话是可以被调用的。 

## 12 extends和super泛型限定符

（1）泛型中上界和下界的定义

上界<? extend Fruit>

下界<? super Apple>

（2）上界和下界的特点

上界的list只能get，不能add（确切地说不能add出除null之外的对象，包括Object）

下界的list只能add，不能get

（3）示例代码

```java
import Java.util.ArrayList;
import Java.util.List;

class Fruit {}
class Apple extends Fruit {}
class Jonathan extends Apple {}
class Orange extends Fruit {}

public class CovariantArrays {
	public static void main(String[] args) {
		//上界
		List<? extends Fruit> flistTop = new ArrayList<Apple>();
		flistTop.add(null);
		//add Fruit对象会报错
		//flist.add(new Fruit());
		Fruit fruit1 = flistTop.get(0);

		//下界
		List<? super Apple> flistBottem = new ArrayList<Apple>();
		flistBottem.add(new Apple());
		flistBottem.add(new Jonathan());
		//get Apple对象会报错
		//Apple apple = flistBottem.get(0);
	}
}
```

（4）上界<? extend Fruit> ，表示所有继承Fruit的子类，但是具体是哪个子类，无法确定，所以调用add的时候，要add什么类型，谁也不知道。但是get的时候，不管是什么子类，不管追溯多少辈，肯定有个父类是Fruit，所以，我都可以用最大的父类Fruit接着，也就是把所有的子类向上转型为Fruit。

下界<? super Apple>，表示Apple的所有父类，包括Fruit，一直可以追溯到老祖宗Object 。那么当我add的时候，我不能add Apple的父类，因为不能确定List里面存放的到底是哪个父类。但是我可以add Apple及其子类。因为不管我的子类是什么类型，它都可以向上转型为Apple及其所有的父类甚至转型为Object 。但是当我get的时候，Apple的父类这么多，我用什么接着呢，除了Object，其他的都接不住。

所以，归根结底可以用一句话表示，那就是编译器可以支持向上转型，但不支持向下转型。具体来讲，我可以把Apple对象赋值给Fruit的引用，但是如果把Fruit对象赋值给Apple的引用就必须得用cast。

## 13 请说明”static”关键字是什么意思？Java中是否可以覆盖(override)一个private或者是static的方法？
“static”关键字表明一个成员变量或者是成员方法可以在没有所属的类的实例变量的情况下被访问。 

Java中static方法不能被覆盖，因为方法覆盖是基于运行时动态绑定的，而static方法是编译时静态绑定的。static方法跟类的任何实例都不相关，所以概念上不适用。

## 14 String为什么不可变？
不可变对象是指一个对象的状态在对象被创建之后就不再变化。不可改变的意思就是说：不能改变对象内的成员变量，包括基本数据类型的值不能改变，引用类型的变量不能指向其他的对象，引用类型指向的对象的状态也不能改变。

String 不可变是因为在 JDK 中 String 类被声明为一个 final 类，且类内部的 value 字节数组也是 final 的，只有当字符串是不可变时字符串池才有可能实现，字符串池的实现可以在运行时节约很多 heap 空间，因为不同的字符串变量都指向池中的同一个字符串；如果字符串是可变的则会引起很严重的安全问题，譬如数据库的用户名密码都是以字符串的形式传入来获得数据库的连接，或者在 socket 编程中主机名和端口都是以字符串的形式传入，因为字符串是不可变的，所以它的值是不可改变的，否则黑客们可以钻到空子改变字符串指向的对象的值造成安全漏洞；因为字符串是不可变的，所以是多线程安全的，同一个字符串实例可以被多个线程共享，这样便不用因为线程安全问题而使用同步，字符串自己便是线程安全的；因为字符串是不可变的所以在它创建的时候 hashcode 就被缓存了，不变性也保证了 hash 码的唯一性，不需要重新计算，这就使得字符串很适合作为 Map 的键，字符串的处理速度要快过其它的键对象，这就是 HashMap 中的键往往都使用字符串的原因。

## 15 List,Map,Set三个接口存取元素个特点

List以特定索引来存取元素，可以有重复元素。Set不能存放重复元素（用对象的equals()方法来区分元素是否重复）。Map保存键值对（key-value pair）映射，映射关系可以是一对一或多对一。Set和Map容器都有基于哈希存储和排序树的两种实现版本，基于哈希存储的版本理论存取时间复杂度为O(1)，而基于排序树版本的实现在插入或删除元素时会按照元素或元素的键（key）构成排序树从而达到排序和去重的效果。 

## 16 阐述ArrayList,Vector,LinkedList的存储性能和特性

ArrayList 和Vector都是使用数组方式存储数据，此数组元素数大于实际存储的数据以便增加和插入元素，它们都允许直接按序号索引元素，但是插入元素要涉及数组元素移动等内存操作，所以索引数据快而插入数据慢，Vector中的方法由于添加了synchronized修饰，因此Vector是线程安全的容器，但性能上较ArrayList差，因此已经是Java中的遗留容器。LinkedList使用双向链表实现存储（将内存中零散的内存单元通过附加的引用关联起来，形成一个可以按序号索引的线性结构，这种链式存储方式与数组的连续存储方式相比，内存的利用率更高），按序号索引数据需要进行前向或后向遍历，但是插入数据时只需要记录本项的前后项即可，所以插入速度较快。Vector属于遗留容器（Java早期的版本中提供的容器，除此之外，Hashtable、Dictionary、BitSet、Stack、Properties都是遗留容器），已经不推荐使用，但是由于ArrayList和LinkedListed都是非线程安全的，如果遇到多个线程操作同一个容器的场景，则可以通过工具类Collections中的synchronizedList方法将其转换成线程安全的容器后再使用（这是对装潢模式的应用，将已有对象传入另一个类的构造器中创建新的对象来增强实现）。

## 17 请判断List,Set,Map是否继承自Collection接口？

List、Set 是，Map 不是。Map是键值对映射容器，与List和Set有明显的区别，而Set存储的零散的元素且不允许有重复元素（数学中的集合也是如此），List是线性结构的容器，适用于按数值索引访问元素的情形。 

## 18 常用的结合类以及主要方法

常用的集合类是List和Map

List的具体实现包括ArrayList和Vector，他们是可变大小的列表，比较适合构建，存储和操作任何类型对象的元素列表。List适用于按数值索引访问元素的类型。

Map提供了一个更通用的元素存储方法。Map集合类用于存储元素对（称作“键”和“值”），其中每一个键映射到一个值。

## 19 Collection和Collections的区别

Collection是集合类呃上级接口，继承他的接口主要有Set和List

Collection是针对集合类的一个帮助类，他提供一系列静态方法实现对各种集合的搜索，排序，线程安全化等操作。

## 20 ArrayList和LinkedList的区别

ArrayList是基于索引的数据接口，它的底层是数组。它可以以O(1)时间复杂度对元素进行随机访问。与此对应，LinkedList是以元素列表的形式存储它的数据，每一个元素都和它的前一个和后一个元素链接在一起，在这种情况下，查找某个元素的时间复杂度是O(n)。

相对于ArrayList，LinkedList的插入，添加，删除操作速度更快，因为当元素被添加到集合任意位置的时候，不需要像数组那样重新计算大小或者是更新索引。 

LinkedList比ArrayList更占内存，因为LinkedList为每一个节点存储了两个引用，一个指向前一个元素，一个指向下一个元素。

## 21 HashMap和Hashtable的区别

**HashMap简介**

1.HashMap是基于哈希表实现的，每一个元素是一个key-value对，其内部通过单链表解决冲突问题，容量不足（超过了阀值）时，同样会自动增长。

2.HashMap是非线程安全的，只是用于单线程环境下，多线程环境下可以采用concurrent并发包下的concurrentHashMap。

3.HashMap 实现了Serializable接口，因此它支持序列化，实现了Cloneable接口，能被克隆。

4.HashMap存数据的过程是：HashMap内部维护了一个存储数据的Entry数组，HashMap采用链表解决冲突，每一个Entry本质上是一个单向链表。当准备添加一个key-value对时，首先通过hash(key)方法计算hash值，然后通过indexFor(hash,length)求该key-value对的存储位置，计算方法是先用hash&0x7FFFFFFF后，再对length取模，这就保证每一个key-value对都能存入HashMap中，当计算出的位置相同时，由于存入位置是一个链表，则把这个key-value对插入链表头。

5.HashMap中key和value都允许为null。key为null的键值对永远都放在以table[0]为头结点的链表中。

**Hashtable简介**

Hashtable同样是基于哈希表实现的，同样每个元素是一个key-value对，其内部也是通过单链表解决冲突问题，容量不足（超过了阀值）时，同样会自动增长。

Hashtable也是JDK1.0引入的类，是线程安全的，能用于多线程环境中。

Hashtable同样实现了Serializable接口，它支持序列化，实现了Cloneable接口，能被克隆。

**相同点和不同点**

相同点：实现原理相同，功能相同，底层都是哈希表结构，查询速度快，在很多情况下可以互用

不同点：

1.Hashtable是早期提供的接口，HashMap是新版JDK提供的接口。

2.Hashtable继承Dictionary类，HashMap实现Map接口。

3.Hashtable线程安全，在多线程并发的环境下，可以直接使用Hashtable，不需要自己为它的方法实现同步。HashMap线程非安全，如果多个线程同时访问一个哈希映射，而其中至少一个线程从结构上修改了该映射，则它必须保持外部同步。

4.**key和value都是对象，不能包含重复key，但是可以包含重复的value**。Hashtable中，key和value都不允许null值，HashMap允许null作为键，这样的键只有一个；可以有一个或多个键对应的值为null。当get()方法返回null值时，可能是 HashMap中没有该键，也可能使该键所对应的值为null。因此，在HashMap中不能由get()方法来判断HashMap中是否存在某个键， 而应该用containsKey()方法来判断。

5.计算hash值的方式不同

①：HashMap有个hash方法重新计算了key的hash值,因为hash冲突变高，所以通过一种方法重算hash值的方法：
```java
static final int hash(Object key) {
	int h;
	return (key == null) ? 0 : (h = key.hashCode()) ^ (h >>> 16);
}
```
注意这里计算hash值，先调用hashCode方法计算出来一个hash值，再将hash与右移16位后相异或，从而得到新的hash值。
**②：Hashtable通过计算key的hashCode()**来得到hash值就为最终hash值。

6.它们计算索引位置方法不同：
HashMap在求hash值对应的位置索引时，index = (n - 1) & hash。将哈希表的大小固定为了2的幂，因为是取模得到索引值，故这样取模时，不需要做除法，只需要做位运算。位运算比除法的效率要高很多。

HashTable在求hash值位置索引时计算index的方法：


```
int index = (hash & 0x7FFFFFFF) % tab.length;
```
&0x7FFFFFFF的目的是为了将负的hash值转化为正值，因为hash值有可能为负数，而&0x7FFFFFFF后，只有符号位改变，而后面的位都不变。

## 22 ArrayList和LinkedList的区别，并说明如果一直在list的尾部添加元素，哪种方式效率高？

ArrayList采用数组数组实现的，查找效率比LinkedList高。LinkedList采用双向链表实现的，插入和删除的效率比ArrayList要高。一直在list的尾部添加元素，LinkedList效率要高。 

## 23 Java同步与异步
多线程并发时，多个线程同时请求同一个资源，必然导致此资源的数据不安全，A线程修改了B线程的处理的数据，而B线程又修改了A线程处理的数理。显然这是由于全局资源造成的，有时为了解决此问题，优先考虑使用局部变量，退而求其次使用同步代码块，出于这样的安全考虑就必须牺牲系统处理性能，加在多线程并发时资源挣夺最激烈的地方，这就实现了线程的同步机制

- 同步：A线程要请求某个资源，但是此资源正在被B线程使用中，因为同步机制存在，A线程请求不到，怎么办，A线程只能等待下去
- 异步：A线程要请求某个资源，但是此资源正在被B线程使用中，因为没有同步机制存在，A线程
  仍然请求的到，A线程无需等待

显然，同步最最安全，最保险的。而异步不安全，容易导致死锁，这样一个线程死掉就会导致整个进程崩溃，但没有同步机制的存在，性能会有所提升。

