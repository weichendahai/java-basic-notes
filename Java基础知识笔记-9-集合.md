Java基础知识笔记-9-集合

# 9 集合

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

### 9.2.1 链表
在本书中，有很多示例已经使用了数组以及动态的ArrayList类。然而，数组和数组列表都有一个重大的缺陷。这就是从数组的中间位置删除一个元素要付出很大的代价，其原因是数组中处于被删除元素之后的所有元素都要向数组的前端移动（见图9-6。)在数组中间的位置上插入一个元素也是如此。

另外一个大家非常熟悉的数据结构一链表（linked list)解决了这个问题。尽管数组在连续的存储位置上存放对象引用，但链表却将每个对象存放在独立的结点中。每个结点还存放着序列中下一个结点的引用。在Java程序设计语言中，所有链表实际上都是双向链接的(doubly linked)—即每个结点还存放着指向前驱结点的引用（见图9-7。)

从链表中间删除一个元素是一个很轻松的操作，即需要更新被删除元素附近的链接（见图9-8。) 

你也许曾经在数据结构课程中学习过如何实现链表的操作。在链表中添加或删除元素时，绕来绕去的指针可能已经给人们留下了极坏的印象。如果真是如此的话，就会为Java集合类库提供一个类LinkedList而感到拍手称快。在下面的代码示例中，先添加3个元素，然后再将第2个元素删除：

```java
List<String> staff = new LinkedList<>(); // LinkedList implements List
staff.add("Amy");
staff.add("Bob");
staff.add("Carl");
Iterator iter = staff.iterator();
String first = iter.next();// visit first element
String second = iter.next();//visited visit element
iter.remove(); //remove last visited element
```

但是，链表与泛型集合之间有一个重要的区别。链表是一个有序集合（ordered collection),每个对象的位置十分重要。LinkedList.add方法将对象添加到链表的尾部。但是，常常需要将元素添加到链表的中间。由于迭代器是描述集合中位置的，所以这种依赖于位置的add方法将由迭代器负责。只有对自然有序的集合使用迭代器添加元素才有实际意义。例如，下一节将要讨论的集（set)类型，其中的元素完全无序。因此，在Iterator接口中就没有add方法。相反地，集合类库提供了子接口Listlterator,其中包含add方法：

```java
inerface ListIterator<E> extends Iterator<E>
{
	void add(E element);
	...
}
```

与Collection.add不同，这个方法不返回boolean类型的值，它假定添加操作总会改变链表。

另外，Listlterator接口有两个方法，可以用来反向遍历链表。

```
E previous()
boolean hasPrevious()
```

与next方法一样，previous方法返回越过的对象。

LinkedList类的listIterator方法返回了一个实现了ListIterator接口的迭代器对象。

```java
ListIterator<String> iter = staff.listlterator();
```

Add方法在迭代器位置之前添加一个新对象。例如，下面的代码将越过链表中的第一个元素，并在第二个元素之前添加“ Juliet”（见图 9-9 ):  

```java
List<String> staff = new LinkedListoa();
staff.add("Amy");
staff.add("Bob");
staff.add("Carl");
ListIterator<String> iter = staff.listlterator();
iter.next();// skip past first element
iter.add("Juliet");
```

如果多次调用add方法，将按照提供的次序把元素添加到链表中。它们被依次添加到迭代器当前位置之前。

当用一个刚刚由Iterator方法返回，并且指向链表表头的迭代器调用add操作时，新添加的元素将变成列表的新表头。当迭代器越过链表的最后一个元素时（即hasNext返回false),添加的元素将变成列表的新表尾。如果链表有n个元素，有n+1个位置可以添加新元素。这些位置与迭代器的n+1个可能的位置相对应。例如，如果链表包含3个元素，A、B、C，就有4个位置（标有丨）可以插入新元素：

|ABC
A|BC
AB|C
ABC|

> 注释：在用“光标”类比时要格外小心。remove操作与BACKSPACE键的工作方式不太一样。在调用next之后，remove方法确实与BACKSPACE键一样删除了迭代器左侧的元素。但是，如果调用previous就会将右侧的元素删除掉，并且不能连续调用两次remove
>
> add方法只依赖于迭代器的位置，而remove方法依赖于迭代器的状态。

最后需要说明，set方法用一个新元素取代调用next或 previous 方法返回的上一个元素。例如，下面的代码将用一个新值取代链表的第一个元素： 

```java
ListIterator<String> iter = list.listlterator();
String oldValue = iter.next(); // returns first element
iter.set(newValue); // sets first element to newValue
```

可以想象，如果在某个迭代器修改集合时，另一个迭代器对其进行遍历，一定会出现混乱的状况。例如，一个迭代器指向另一个迭代器刚刚删除的元素前面，现在这个迭代器就是无效的，并且不应该再使用。链表迭代器的设计使它能够检测到这种修改。如果迭代器发现它的集合被另一个迭代器修改了，或是被该集合自身的方法修改了，就会抛出一个ConcurrentModificationException异常。例如，看一看下面这段代码：

```java
List<String> list = ...;
ListIterator<String> iterl = list.listlterator();
ListIterator<String> iter2 = list.listlterator();
iterl.next();
iterl.remove();
iter2.next(); // throws ConcurrentModificationException
```

由于iter2检测出这个链表被从外部修改了，所以对iter2.next的调用抛出了一个Concurrent ModificationException异常。

为了避免发生并发修改的异常，请遵循下述简单规则：可以根据需要给容器附加许多的迭代器，但是这些迭代器只能读取列表。另外，再单独附加一个既能读又能写的迭代器。

有一种简单的方法可以检测到并发修改的问题。集合可以跟踪改写操作（诸如添加或删除元素）的次数。每个迭代器都维护一个独立的计数值。在每个迭代器方法的开始处检查自己改写操作的计数值是否与集合的改写操作计数值一致。如果不一致，抛出一个Concurrent ModificationException异常。

> 注释：对于并发修改列表的检测肴一个奇怪的例外。链表只负责跟踪对列表的结构性修改，例如，添加元素、删除元素。set方法不被视为结构性修改。可以将多个迭代器附加给一个链表，所有的迭代器都调用set方法对现有结点的内容进行修改。在本章后面所介绍的Collections类的许多算法都需要使用这个功能。

现在已经介绍了LinkedList类的各种基本方法。苛以使用Listlterator类从前后两个方向遍历链表中的元素，并可以添加、删除元素。

在上一节已经看到，Collection接口中声明了许多用于对链表操作的有用方法。其中大部分方法都是在LinkedList类的超类AbstractCollection中实现的。例如，toString方法调用了所有元素的toString,并产生了一个很长的格式为[A,B，C]的字符串。这为调试工作提供了便利。可以使用contains方法检测某个元素是否出现在链表中。例如，如果链表中包含一个等于“Harry”的字符串，调用staff.contains("Harry")后将会返回true。

在Java类库中，还提供了许多在理论上存在一定争议的方法。链表不支持快速地随机访问。如果要查看链表中第n个元素，就必须从头开始，越过个元素。没有捷径可走。鉴于这个原因，在程序需要采用整数索引访问元素时，程序员通常不选用链表。尽管如此，LinkedList类还是提供了一个用来访问某个特定元素的get方法：

```java
LinkedList<String> list=...;
String obj=list.get(n);
```

当然，这个方法的效率并不太高。如果发现自己正在使用这个方法，说明有可能对于所要解决的问题使用了错误的数据结构。

列表迭代器接口还有一个方法，可以告之当前位置的索引。实际上，从概念上讲，由于Java迭代器指向两个元素之间的位置，所以可以同时产生两个索引：nextlndex方法返回下一次调用next方法时返回元素的整数索引；previouslndex方法返回下一次调用previous方法时返回元素的整数索引。当然，这个索引只比nextlndex返回的索引值小1。这两个方法的效率非常高，这是因为迭代器保持着当前位置的计数值。最后需要说一下，如果有一个整数索引n,list.listlterator(n)将返回一个迭代器，这个迭代器指向索引为n的元素前面的位置。也就是说，调用next与调用Hst.get(n)会产生同一个元素，只是获得这个迭代器的效率比较低。

如果链表中只有很少几个元素，就完全没有必要为get方法和set方法的开销而烦恼。但是，为什么要优先使用链表呢？使用链表的唯一理由是尽可能地减少在列表中间插入或删除元素所付出的代价。如果列表只有少数几个元素，就完全可以使用ArrayList。

我们建议避免使用以整数索引表示链表中位置的所有方法。如果需要对集合进行随机访问，就使用数组或ArrayList,而不要使用链表。

### 9.2.2 数组列表
在上一节中，介绍了List接口和实现了这个接口的List接口用于描述一个有序集合，并且集合中每个元素的位置十分重要。有两种访问元素的协议：一种是用迭代器，另一种是用get和set方法随机地访问每个元素。后者不适用于链表，但对数组却很有用。集合类库提供了一种大家熟悉的ArrayList类，这个类也实现了List接口。ArrayList封装了一个动态再分配的对象数组。

> 注释：对于一个经验丰富的Java程序员来说，在需要动态数组时，可能会使用Vector类。为什么要用ArrayList取代Vector呢？原因很简单：Vector类的所有方法都是同步的。可以由两个线程安全地访问一个Vector对象。但是，如果由一个线程访问Vector,代码要在同步操作上耗费大量的时间。这种情况还是很常见的。而ArrayList方法不是同步的，因此，建议在不需要同步时使用ArrayList,而不要使用Vector。

### 9.2.3 散列集
链表和数组可以按照人们的意愿排列元素的次序。但是，如果想要査看某个指定的元素，却又忘记了它的位置，就需要访问所有元素，直到找到为止。如果集合中包含的元素很多，将会消耗很多时间。如果不在意元素的顺序，可以有几种能够快速査找元素的数据结构。其缺点是无法控制元素出现的次序。它们将按照有利于其操作目的的原则组织数据。

有一种众所周知的数据结构，可以快速地查找所需要的对象，这就是散列表（hash table）。散列表为每个对象计算一个整数，成为散列码（hash code）。散列码是由对象的实例域产生的一个整数。更准确的说，具有不同数据域的对象将产生不同的散列码。表9-2列出了几个散列码的示例，它们是由String类的hashCode方法产生的。

| 串    | 散列码 |
| ----- | ------ |
| "Lee" | 76268  |
| "lee" | 107020 |
| "eel" | 100300 |

如果自定义类，就要负责实现这个类的hashCode方法。有关hashCode方法的详细内容请参看第5章。注意，自己实现的hashCode方法应该与equals方法兼容，即如果a_equals(b)为true,a与b必须具有相同的散列码。

现在，最重要的问题是散列码要能够快速地计算出来，并且这个计算只与要散列的对象状态有关，与散列表中的其他对象无关。

在Java中，散列表用链表数组实现。每个列表被称为桶（bucket)(参看图9-10。)要想査找表中对象的位置，就要先计算它的散列码，然后与桶的总数取余，所得到的结果就是保存这个元素的桶的索引。例如，如果某个对象的散列码为76268,并且有128个桶，对象应该保存在第108号桶中（76268除以128余108)。或许会很幸运，在这个桶中没有其他元素，此时将元素直接插入到桶中就可以了。

当然，有时候会遇到桶被占满的情况，这也是不可避免的。这种现象被称为散列冲突（hash collision)。这时，需要用新对象与桶中的所有对象进行比较，査看这个对象是否已经存在。如果散列码是合理且随机分布的，桶的数目也足够大，需要比较的次数就会很少。

> 注释：在JavaSE8中，桶满时会从链表变为平衡二叉树。如果选择的散列函数不当，会产生很多冲突，或者如果有恶意代码试图在散列表中填充多个有相同散列码的值，这样就能提高性能。

如果想更多地控制散列表的运行性能，就要指定一个初始的桶数。桶数是指用于收集具有相同散列值的桶的数目。如果要插入到散列表中的元素太多，就会增加冲突的可能性，降低运行性能。

如果大致知道最终会有多少个元素要插入到散列表中，就可以设置桶数。通常，将桶数设置为预计元素个数的75%~150%。有些研究人员认为：尽管还没有确凿的证据，但最好将桶数设置为一个素数，以防键的集聚。标准类库使用的桶数是2的幂，默认值为16(为表大小提供的任何值都将被自动地转换为2的下一个幂)。

当然，并不是总能够知道需要存储多少个元素的，也有可能最初的估计过低。如果散列表太满，就需要再散列（rehashed)。如果要对散列表再散列，就需要创建一个桶数更多的表，并将所有元素插入到这个新表中，然后丢弃原来的表。装填因子（load factor)决定何时对散列表进行再散列。例如，如果装填因子为0.75(默认值，)而表中超过75%的位置已经填入元素，这个表就会用双倍的桶数自动地进行再散列。对于大多数应用程序来说，装填因子为0.75是比较合理的。

散列表可以用于实现几个重要的数据结构。其中最简单的是set类型。set是没有重复元素的元素集合。set的add方法首先在集中查找要添加的对象，如果不存在，就将这个对象添加进去。

Java集合类库提供了一个HashSet类，它实现了基于散列表的集。可以用add方法添加元素。contains方法已经被重新定义，用来快速地查看是否某个元素已经出现在集中。它只在某个桶中査找元素，而不必查看集合中的所有元素。

散列集迭代器将依次访问所有的桶。由于散列将元素分散在表的各个位置上，所以访问它们的顺序几乎是随机的。只有不关心集合中元素的顺序时才应该使用HashSet。

本节末尾的示例程序（程序清单9-2)将从System.in读取单词，然后将它们添加到集中，最后，再打印出集中的所有单词。例如，可以将Alice in Wonderland(可以从http://www.gutenberg.org找到）的文本输入到这个程序中，并从命令行shell运行：

```
java SetTest < alice30.txt
```

这个程序将读取输入的所有单词，并且将它们添加到散列集中。然后遍历散列集中的不同单词，最后打印出单词的数量（Alice in Wonderland共有5909个不同的单词，包括开头的版权声明）。单词以随机的顺序出现。

> 警告：在更改集中的元素时要格外小心。如果元素的散列码发生了改变，元素在数据结构中的位置也会发生变化。

## 9.3 映射

集是一个集合，它可以快速地查找现有的元素。但是，要查看一个元素，需要有要查找元素的精确副本。这不是一种非常通用的査找方式。通常，我们知道某些键的信息，并想要查找与之对应的元素。映射（map)数据结构就是为此设计的。映射用来存放键/值对。如果提供了键，就能够查找到值。例如，有一张关于员工信息的记录表，键为员工ID，值为Employee对象。在下面的小节中，我们会学习如何使用映射。 

### 9.3.1 基本映射操作
Java类库为映射提供了两个通用的实现：HashMap和TreeMap。这两个类都实现了Map接口。

散列映射对键进行散列，树映射用键的整体顺序对元素进行排序，并将其组织成搜索树。散列或比较函数只能作用于键。与键关联的值不能进行散列或比较。

应该选择散列映射还是树映射呢？与集一样，散列稍微快一些，如果不需要按照排列顺序访问键，就最好选择散列。

下列代码将为存储的员工信息建立一个散列映射：

```java
Map<String,Employee> staff=new HashMap<>(); // HashMap implements Map
Employee harry = new Employee("Harry Hacker");
staff.put("987-98-9996", harry);
...
```

每当往映射中添加对象时，必须同时提供一个键。在这里，键是一个字符串，对应的值是Employee对象。

要想检索一个对象，必须使用（因而，必须记住）一个键。

```java
String id = "987-98-9996";
e = staff.get(id);// gets harry
```

如果在映射中没有与给定键对应的信息， get 将返回 null。

null 返回值可能并不方便。有时可以有一个好的默认值， 用作为映射中不存在的键。然后使用 getOrDefault 方法。 

```java
Map<String, Integer> scores = ...;
int score = scores.get(id, 0); // Gets 0 if the id is not present
```

键必须是唯一的。不能对同一个键存放两个值。如果对同一个键两次调用put方法，第二个值就会取代第一个值。实际上，put将返回用这个键参数存储的上一个值。

remove方法用于从映射中删除给定键对应的元素。size方法用于返回映射中的元素数。

要迭代处理映射的键和值，最容易的方法是使用forEach方法。可以提供一个接收键和值的lambda表达式。映射中的每一项会依序调用这个表达式。

```java
scores.forEach((k, v) ->
	System.out.println("key=" + k + ", value:" + v));
```

