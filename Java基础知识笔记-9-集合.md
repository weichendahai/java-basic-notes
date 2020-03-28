Java基础知识笔记-9-集合

## 9 集合

## 9.1 Java 集合框架
Java最初版本只为最常用的数据结构提供了很少的一组类：Vector、Stack、Hashtable BitSet与Enumeration接口，其中的Enumeration接口提供了一种用于访问任意容器中各个元素的抽象机制。这是一种很明智的选择，但要想建立一个全面的集合类库还需要大量的时间和高超的技能。

随着Java SE 1.2 的问世，设计人员感到是推出一组功能完善的数据结构的时机了。面对一大堆相互矛盾的设计策略，他们希望让类库规模小且易于学习，而不希望像C++的“标准模版库”（即STL)那样复杂，但却又希望能够得到STL率先推出的“泛型算法”所具有的优点。他们希望将传统的类融人新的框架中。与所有的集合类库设计者一样，他们必须做出一些艰难的选择，于是，在整个设计过程中，他们做出了一些独具特色的设计决定。本节将介绍Java集合框架的基本设计，展示使用它们的方法，并解释一些颇具争议的特性背后的考虑。

### 9.1.1 将集合的接口与实现分离
与现代的数据结构类库的常见情况一样，Java集合类库也将接口（interface)与实现(implementation)分离。首先，看一下人们熟悉的数据结构队列（queue)是如何分离的。

队列接口指出可以在队列的尾部添加元素，在队列的头部删除元素，并且可以査找队列中元素的个数。当需要收集对象，并按照“先进先出”的规则检索对象时就应该使用队列

队列接口的最简形式可能类似下面这样：
```java
public interface Queue<E> // a simplified form of the interface in the standard library
{
	void add(E element);
	E remove();
	int size();
}
```
这个接口并没有说明队列是如何实现的。队列通常有两种实现方式：一种是使用循环数组；另一种是使用链表

每一个实现都可以通过一个实现了Queue接口的类表示。
```java
public class CircularArrayQueue<E> implements Queue<E> // not an actual library class
	private int head;
	private int tail;
	CircularArrayQueue(int capacity) { . . . }
	public void add(E element) { . . . }
	public E remove() { . . . }
	public int size() { . . . }
	private E[] elements;
	}
public class LinkedListQueue<E> implements Queue<E> // not an actual library class
{
	private Link head;
	private Link tail;
	LinkedListQueue() { . . . }
	public void add(E element) { . . . }
	public E remove() { . . . }
	public int size() { . . . }
}
```

> 注释：实际上，Java类库没有名为CircularArrayQueue和LinkedListQueue的类。这里，只是以这些类作为示例，解释一下集合接口与实现在概念上的不同。如果需要一个循环数组队列，就可以使用ArrayDeque类。如果需要一个链表队列，就直接使用LinkedList类，这个类实现了Queue接口。

当在程序中使用队列时，一旦构建了集合就不需要知道究竟使用了哪种实现。因此，只有在构建集合对象时，使用具体的类才有意义。可以使用接口类型存放集合的引用。

```java
Queue<Customer> expresslane = new CircularArrayQueue<>(100);
expressLane.add(new Customer("Harry"));
```
利用这种方式，一旦改变了想法，可以轻松地使用另外一种不同的实现。只需要对程序的一个地方做出修改，即调用构造器的地方。如果觉得LinkedListQueue是个更好的选择，就将代码修改为：
```java
Queue<Custoaer> expressLane = new LinkedListQueue();
expressLane.add(new Custonier("Harry"));
```
为什么选择这种实现，而不选择那种实现呢？接口本身并不能说明哪种实现的效率究竟如何。循环数组要比链表更高效，因此多数人优先选择循环数组。然而，通常这样做也需要付出一定的代价。

循环数组是一个有界集合，即容量有限。如果程序中要收集的对象数量没有上限，就最好使用链表来实现。

在研究API文档时，会发现另外一组名字以Abstract开头的类，例如，AbstractQueue。这些类是为类库实现者而设计的。如果想要实现自己的队列类（也许不太可能，)会发现扩展AbstractQueue类要比实现Queue接口中的所有方法轻松得多。

### 9.1.2 Collection 接口
在Java类库中，集合类的基本接口是Collection接口。这个接口有两个基本方法
```java
public interface Collection<E>
{
	boolean add(E element);
	Iterator<E> iterator();
	...
}
```
除了这两个方法之外，还有几个方法，将在稍后介绍。

add方法用于向集合中添加元素。如果添加元素确实改变了集合就返回true,如果集合没有发生变化就返回false。例如，如果试图向集中添加一个对象，而这个对象在集中已经存在，这个添加请求就没有实效，因为集中不允许有重复的对象。

iterator方法用于返回一个实现了Iterator 接口的对象。可以使用这个迭代器对象依次访问集合中的元素。下一节讨论迭代器。

### 9.1.3 迭代器
Iterator接口包含4个方法：
```java
public interface Iterator<E>
{
	E next();
	boolean hasNext();
	void remove();
	default void forEachRemaining(Consumer<? super E> action);
}
```

通过反复调用next方法，可以逐个访问集合中的每个元素。但是，如果到达了集合的末尾，next方法将抛出一个NoSuchElementException。因此，需要在调用next之前调用hasNext方法。如果迭代器对象还有多个供访问的元素，这个方法就返回true。如果想要査看集合中的所有元素，就请求一个迭代器，并在hasNext返回true时反复地调用next方法。例如：

```java
Collection<String> c = . . .;
Iterator<String> iter = c.iterator();
while (iter.hasNext())
{
	String element = iter.next();
	do something with element
} 
```
用“foreach”循环可以更加简练地表示同样的循环操作：
```java
for (String element : c)
{
	do something with element
}
```

编译器简单地将“foreach”循环翻译为带有迭代器的循环。

“for each”循环可以与任何实现了Iterable接口的对象一起工作，这个接口只包含一个抽象方法：

```java
public interface Iterable<E>
	Iterator<E> iterator();
}
```
Collection接口扩展了Iterable接口。因此，对于标准类库中的任何集合都可以使用“for each”循环。

Java集合类库中的迭代器与其他类库中的迭代器在概念上有着重要的区别。在传统的集合类库中，例如，C++的标准模版库，迭代器是根据数组索引建模的。如果给定这样一个迭代器，就可以查看指定位置上的元素，就像知道数组索引i就可以査看数组元素a[i]—样。不需要查找元素，就可以将迭代器向前移动一个位置。这与不需要执行査找操作就可以通过i++将数组索引向前移动一样。但是，Java迭代器并不是这样操作的。查找操作与位置变更是紧密相连的。查找一个元素的唯一方法是调用next,而在执行查找操作的同时，迭代器的位置随之向前移动。

因此，应该将Java迭代器认为是位于两个元素之间。当调用next时，迭代器就越过下一个元素，并返回刚刚越过的那个元素的引用（见图9-3)

Iterator接口的remove方法将会删除上次调用next方法时返回的元素。在大多数情况下，在决定删除某个元素之前应该先看一下这个元素是很具有实际意义的。然而，如果想要删除指定位置上的元素，仍然需要越过这个元素。例如，下面是如何删除字符串集合中第一个元素的方法：
```java
Iterator<String> it = c.iterator();
it.next(); // skip over the first element
it.remove(); // now remove it
```

更重要的是，对next方法和remove方法的调用具有互相依赖性。如果调用remove之前没有调用next将是不合法的。如果这样做，将会抛出一个IllegalStateException异常。

如果想删除两个相邻的元素，不能直接地这样调用：
```java
it.remove();
it.remove0;// Error
```
相反地，必须先调用next越过将要删除的元素。
```java
it,remove();
it.next0;
it.remove(); // OK
```

## 9.2 具体的集合
表9-1展示了Java类库中的集合，并简要描述了每个集合类的用途（为简单起见，省略了将在第14章中介绍的线程安全集合)。在表9-1中，除了以Map结尾的类之外，其他类都实现了Collection接口，而以Map结尾的类实现了Map接口。映射的内容将在9.3节介绍。

表9-1 Java库中的具体集合

| 集合类型        | 描 述                                                |
| --------------- | ---------------------------------------------------- |
| ArrayList       | 一种可以动态增长和缩减的索引序列                     |
| LinkedList      | 一种可以在任何位置进行高效地插入和删除操作的有序序列 |
| ArrayDeque      | 一种用循环数组实现的双端队列                         |
| HashSet         | 一种没有重复元素的无序集合                           |
| TreeSet         | —种有序集                                            |
| EnumSet         | 一种包含枚举类型值的集                               |
| LinkedHashSet   | 一种可以记住元素插入次序的集                         |
| PriorityQueue   | 一种允许高效删除最小元素的集合                       |
| HashMap         | 一种存储键/值关联的数据结构                          |
| TreeMap         | —种键值有序排列的映射表                              |
| EnumMap         | 一种键值属于枚举类型的映射表                         |
| LinkedHashMap   | 一种可以记住键 / 值项添加次序的映射表                |
| WeakHashMap     | 一种其值无用武之地后可以被垃圾回收器回收的映射表     |
| IdentityHashMap | 一种用=而不是用equals比较键值的映射表                |