Java基础知识笔记-7-异常，断言和日志

在理想状态下，用户输入数据的格式永远都是正确的， 选择打开的文件也一定存在，并且永远不会出现bug。迄今为止，本书呈现给大家的代码似乎都处在这样一个理想境界中。 然而，在现实世界中却充满了不良的数据和带有问题的代码，现在是讨论Java程序设计语育 处理这些问题的机制的时候了。 

人们在遇到错误时会感觉不爽。如果一个用户在运行程序期间，由于程序的错误或一些外部环境的影响造成用户数据的丢失，用户就有可能不再使用这个程序了，为了避免这类事情的发生，至少应该做到以下几点： 

- 向用户通告错误； 
- 保存所有的工作结果；
- 允许用户以妥善的形式退出程序。

对于异常情况， 例如，可能造成程序崩溃的错误输入，Java使用一种称为异常处理 (exception handing) 的错误捕获机制处理。Java中的异常处理与C++或Delphi中的异常处理十分类似。本章的第1部分先介绍Java的异常。

在测试期间，需要进行大量的检测以验证程序操作的正确性。 然而，这些检测可能非常耗 时，在测试完成后也不必保留它们，因此，可以将这些检测删掉，并在其他测试需要时将它们粘贴回来，这是一件很乏味的事情。本章的第2部分将介绍如何使用断言来有选择地启用检测。 

当程序出现错误时，并不总是能够与用户或终端进行沟通。此时，可能希望记录下出现的问题，以备日后进行分析。本章的第3部分将讨论标准Java日志框架。

## 1 处理错误

假设在一个Java程序运行期间出现了一个错误。这个错误可能是由于文件包含了错误信息，或者网络连接出现问题造成的，也有可能是因为使用无效的数组下标，或者试图使用 一个没有被赋值的对象引用而造成的。用户期望在出现错误时，程序能够采用一些理智的行为。如果由于出现错误而使得某些操作没有完成，程序应该：

- 返回到一种安全状态，并能够让用户执行一些其他的命令；或者
- 允许用户保存所有操作的结果，并以妥善的方式终止程序

要做到这些并不是一件很容易的事情。其原因是检测（或引发）错误条件的代码通常离那些能够让数据恢复到安全状态，或者能够保存用户的操作结果，并正常地退出程序的代码 很远。异常处理的任务就是将控制权从错误产生的地方转移给能够处理这种情况的错误处理器。为了能够在程序中处理异常情况，必须研究程序中可能会出现的错误和问题，以及哪类问题需要关注。 

1. 用户输入错误

   除了那些不可避免的键盘输入错误外, 有些用户喜欢各行其是，不遵守程序的要求。例如，假设有一个用户请求连接一个URL，而语法却不正确。在程序代码中应该对此进行检查，如果没有检査，网络层就会给出警告。 

2. 设备错误

   硬件并不总是让它做什么，它就做什么。打印机可能被关掉了。网页可能临时性地不能浏览。在一个任务的处理过程中，硬件经常出现问题。例如，打印机在打印过程中可能没有纸了。 

3. 物理限制

   磁盘满了，可用存储空间已被用完。 

4. 代码错误
程序方法有可能无法正确执行。例如，方法可能返回了一个错误的答案，或者错误地调用了其他的方法。计算的数组索引不合法，试图在散列表中查找一个不存在的记录，或者试图让一个空找执行弹出操作，这些都属于代码错误。 

对于方法中的一个错误，传统的做法是返回一个特殊的错误码，由调用方法分析。例如，对于一个从文件中读取信息的方法来说，返回值通常不是标准字符，而是一个-1, 表示文件结束。这种处理方式对于很多异常状况都是可行的。还有一种表示错误状况的常用返回值是null引用。

遗憾的是，并不是在任何情况下都能够返回一个错误码。有可能无法明确地将有效数据与无效数据加以区分。一个返回整型的方法就不能简单地通过返回-1表示错误，因为-1很可能是一个完全合法的结果。 正如第5章中所叙述的那样，在Java中，如果某个方法不能够采用正常的途径完整它的 任务，就可以通过另外一个路径退出方法。在这种情况下，方法并不返回任何值， 而是抛出(throw) 一个封装了错误信息的对象。需要注意的是，这个方法将会立刻退出，并不返回任何值。此外，调用这个方法的代码也将无法继续执行，取而代之的是，异常处理机制开始搜索能够处理这种异常状况的异常处理器 (exception handler)。 异常具有自己的语法和特定的继承结构。下面首先介绍一下语法，然后再给出有效地使用这种语言功能的技巧。

### 1.1 异常分类

在Java程序设计语言中，异常对象都是派生于Throwable类的一个实例。稍后还可以看到，如果Java中内置的异常类不能够满足需求，用户可以创建自己的异常类。

图 7-1 是Java异常层次结构的一个简化示意图。

```
		Throwable
Error				Expception
			IOException		RuntimeException
```

需要注意的是，所有的异常都是由Throwable继承而来，但在下一层立即分解为两个分支：Error和Exception

Error类层次结构描述了Java运行时系统的内部错误和资源耗尽错误。应用程序不应该抛出这种类型的对象。如果出现了这样的内部错误，除了通告给用户，并尽力使程序安全地终止之外，再也无能为力了。这种情况很少出现。 

在设计Java程序时，需要关注Exception层次结构。这个层次结构又分解为两个分支：一个分支派生于RuntimeException;另一个分支包含其他异常。划分两个分支的规则是：由程序错误导致的异常属于RuntimeException;而程序本身没有问题，但由于像I/O错误这类问题导致的异常属于其他异常:

派生于RuntimeException的异常包含下面几种情况： 

- 错误的类型转换。 
- 数组访问越界 
- 访问null指针 

不是派生于RuntimeException的异常包括： 

- 试图在文件尾部后面读取数据。
- 试图打开一个不存在的文件。 
- 试图根据给定的字符串查找Class对象，而这个字符串表示的类并不存在

“如果出现RuntimeException异常，那么就一定是你的问题”是一条相当有道理的规则。应该通过检测数组下标是否越界来避免ArraylndexOutOfBoundsException异常；应该通过在使用变量之前检测是否为null来杜绝NullPointerException异常的发生。

如何处理不存在的文件呢？难道不能先检查文件是否存在再打开它吗？嗯，这个文件有可能在你检查它是否存在之前就已经被删除了。因此，“是否存在”取决于环境，而不只是取决于你的代码。

Java语言规范将派生于Error类或RuntimeException类的所有异常称为非受查(unchecked)异常，所有其他的异常称为受查(checked)异常。这是两个很有用的术语，在 后面还会用到。编译器将核查是否为所有的受査异常提供了异常处理器。 

### 1.2 声明受查异常

如果遇到了无法处理的情况，那么Java的方法可以抛出一个异常。这个道理很简单：一个方法不仅需要告诉编译器将要返回什么值，还要告诉编译器有可能发生什么错误。例如， 一段读取文件的代码知道有可能读取的文件不存在，或者内容为空，因此，试图处理文件信息的代码就需要通知编译器可能会抛出IOException类的异常。

方法应该在其首部声明所有可能抛出的异常。这样可以从首部反映出这个方法可能抛出哪类受査异常。例如，下面是标准类库中提供的FilelnputStream类的一个构造器的声明（有关输入和输出的更多信息请参看卷Ⅱ的第2章。）

```
public FilelnputStream(String name) throws FileNotFoundException
```

这个声明表示这个构造器将根据给定的String参数产生一个FilelnputStream对象，但也有可能抛出一个FileNotFoundException异常。如果发生了这种糟糕情况，构造器将不会初始化一个新的FileInputStream对象， 而是抛出一个FileNotFoundException类对象。 如果这个方法真的抛出了这样一个异常对象，运行时系统就会开始搜索异常处理器， 以便知道如何处理FileNotFoundException对象。

在自己编写方法时， 不必将所有可能抛出的异常都进行声明。至于什么时候需要在方法中用throws子句声明异常，什么异常必须使用throws子句声明，需要记住在遇到下面4种情况时应该抛出异常：

- 1)调用一个抛出受査异常的方法，例如，FilelnputStream构造器。 
- 2)程序运行过程中发现错误，并且利用throw语句抛出一个受查异常（下一节将详细地介绍throw语句）。
- 3)程序出现错误，例如，a\[-1]=0会抛出一个ArraylndexOutOffloundsException这样的非受查异常。
- 4)Java虚拟机和运行时库出现的内部错误。

如果出现前两种情况之一，则必须告诉调用这个方法的程序员有可能抛出异常。为什么？因为任何一个抛出异常的方法都有可能是一个死亡陷阱。如果没有处理器捕获这个异常，当前执行的线程就会结束。

对于那些可能被他人使用的Java方法，应该根据异常规范(exception specification)，在方法的首部声明这个方法可能抛出的异常。

```java
class MyAnimation {
	...
	public Image loadlmage(String s) throws IOException {
		...
	}
}
```

如果一个方法有可能抛出多个受查异常类型，那么就必须在方法的首部列出所有的异常类。每个异常类之间用逗号隔开。如下面这个例子所示： 

```java
class MyAnimation {
	...
	public Image loadlmage(String s) throws FileNotFoundException,EOFException
	{
		...
	}
}
```

但是，不需要声明Java的内部错误，即从Error继承的错误。任何程序代码都具有抛出那些异常的潜能，而我们对其没有任何控制能力。

同样，也不应该声明从RuntimeException继承的那些非受查异常

 ```java
class MyAnimation {
	...
	void drawlmage(int i) throws ArraylndexOutOfBoundsException // bad style
	{
		...
	}
}
 ```

这些运行时错误完全在我们的控制之下。如果特别关注数组下标引发的错误，就应该将更多的时间花费在修正程序中的错误上，而不是说明这些错误发生的可能性上。 

总之，一个方法必须声明所有可能抛出的受查异常，而非受查异常要么不可控制(Error), 要么就应该避免发生(RuntimeException)。如果方法没有声明所有可能发生的受查异常，编 译器就会发出一个错误消息。

当然，从前面的示例中可以知道：除了声明异常之外，还可以捕获异常。这样会使异常不被抛到方法之外，也不需要throws规范。稍后，将会讨论如何决定一个异常是被捕获，还是被抛出让其他的处理器进行处理。

> 警告：如果在子类中覆盖了超类的一个方法，子类方法中声明的受查异常不能比超类方法中声明的异常更通用（也就是说，子类方法中可以抛出更特定的异常，或者根本不抛 出任何异常）特别需要说明的是，如果超类方法没有抛出任何受查异常，子类也不能抛出任何受查异常。例如，如果覆盖JComponent.paintComponent方法，由于超类中这个方法没有抛出任何异常，所以，自定义的paintComponent也不能抛出任何受查异常。 

如果类中的一个方法声明将会抛出一个异常，而这个异常是某个特定类的实例时，则这个方法就有可能抛出一个这个类的异常，或者这个类的任意一个子类的异常。例如，FilelnputStream构造器声明将有可能抛出一个IOExcetion异常，然而并不知道具体是哪种IOException异常。它既可能是IOException异常，也可能是其子类的异常，例如， FileNotFoundException

### 1.3 如何抛出异常

假设在程序代码中发生了一些很糟糕的事情。一个名为readData的方法正在读取一个首部具有下列信息的文件：

```
Content-length: 1024
```

然而，读到 733个字符之后文件就结束了。我们认为这是一种不正常的情况，希望抛出一个异常。 

首先要决定应该抛出什么类型的异常。将上述异常归结为IOException是一种很好的选择。仔细地阅读Java API文档之后会发现：EOFException异常描述的是“在输入过程中，遇到了一个未预期的EOF后的信号”。这正是我们要抛出的异常。下面是抛出这个异常的语句：

```java
throw new EOFException();
```

或者

```java
EOFException e = new EOFException();
throw e;
```



```java
String readData(Scanner in) throws EOFException {
	...
	while(...) {
    	if (!in.hasNext()) // EOF encountered
    	{
    		if (n < len)
            	throw new EOFException();
    	}
    	...
	}
	return s;
}
```

EOFException类还有一个含有一个字符串型参数的构造器。这个构造器可以更加细致的描述异常出现的情况。

```java
String gripe = "Content-length: " + len + ", Received: " + n;
throw new EOFException(gripe);
```

在前面已经看到，对于一个已经存在的异常类，将其抛出非常容易，这种情况下：

- 1)找到一个合适的异常类。
- 2)创建这个类的一个对象。 
- 3)将对象抛出。 

一旦方法抛出了异常，这个方法就不可能返回到调用者。也就是说，不必为返回的默认值或错误代码担忧。 

### 1.4 创建异常类

在程序中，可能会遇到任何标准异常类都没有能够充分地描述清楚的问题。在这种情况下，创建自己的异常类就是一件顺理成章的事情了。我们需要做的只是定义一个派生于Exception的类，或者派生于Exception子类的类。例如，定义一个派生于IOException的类。习惯上，定义的类应该包含两个构造器，一个是默认的构造器；另一个是带有详细描述信息的构造器（超类Throwable的toString方法将会打印出这些详细信息，这在调试中非常有用)。

```java
class FileFormatException extends IOException {
	public FileFormatException() {}
	public FileFormatException(String gripe) {
		super(gripe);
	}
} 
```

现在，就可以抛出自己定义的异常类型了。

```java
String readData(BufferedReader in) throws FileFormatException {
	while (...) {
		if (ch == -1 ) // EOF encountered
		{
			if (n < len) 
				throw new FileFornatException();
		}
		...
	}
	return s;
}
```

```java
Throwable();   //构造一个新的Throwabie对象，这个对象没有详细的描述信息。
Throwable(String message);  //构造一个新的throwabie对象，这个对象带有特定的详细描述信息。习惯上，所有派生的异常类都支持一个默认的构造器和一个带有详细描述信息的构造器。 
String getMessage();  //获得Throwabie对象的详细描述信息
```

## 2 捕获异常

到目前为止，已经知道如何抛出一个异常。这个过程十分容易。只要将其抛出就不用理踩了。当然，有些代码必须捕获异常。捕获异常需要进行周密的计划。这正是下面几节要介绍的内容。

### 2.1 捕获异常
如果某个异常发生的时候没有在任何地方进行捕获，那程序就会终止执行，并在控制台上打印出异常信息，其中包括异常的类型和堆栈的内容。对于图形界面程序（applet和应用程序)，在捕获异常之后，也会打印出堆桟的信息，但程序将返回到用户界面的处理循环中 (在调试GUI程序时， 最好保证控制台窗口可见，并且没有被最小化)。

要想捕获一个异常，必须设置try/catch语句块。最简单的try语句块如下所示：

```java
try {
	code
	more code
	more code
} catch (ExceptionType e) {
	handlerfor this type
}
```

 如果在try语句块中的任何代码抛出了一个在catch子句中说明的异常类，那么 

- 1)程序将跳过try语句块的其余代码。
- 2)程序将执行catch子句中的处理器代码。

如果在try语句块中的代码没有拋出任何异常，那么程序将跳过catch子句。

如果方法中的任何代码拋出了一个在catch子句中没有声明的异常类型，那么这个方法就会立刻退出（希望调用者为这种类型的异常设计了catch子句)。

为了演示捕获异常的处理过程，下面给出一个读取数据的典型程序代码： 

```java
public void read(String filename) {
	try {
		InputStream in = new FileInputStream(filename);
		int b;
		while ((b = in.read()3 != -1 )
		{
			process input
		}
	} catch (IOException exception) {
		exception.printStackTrace();
	}
} 
```

需要注意的是，try语句中的大多数代码都很容易理解：读取并处理字节，直到遇到文件结束符为止。正如在Java API中看到的那样，read方法有可能拋出一个IOException异常。在这种情况下，将跳出整个while循环，进入catch子句，并生成一个栈轨迹。对于一个普通的程序来说，这样处理异常基本上合乎情理。还有其他的选择吗？

通常，最好的选择是什么也不做，而是将异常传递给调用者。如果read方法出现了错误, 就让read方法的调用者去操心！如果采用这种处理方式，就必须声明这个方法可能会拋出一个IOException。

```java
public void read(String filename) throws IOException {
	inputStream in = new FileinputStream(filename);
	int b;
	while ((b = in.readO) != -1 ) {
		process input
	}
} 
```

请记住，编译器严格地执行throws说明符。如果调用了一个抛出受查异常的方法，就必须对它进行处理，或者继续传递。 

哪种方法更好呢？ 通常，应该捕获那些知道如何处理的异常，而将那些不知道怎样处理的异常继续进行传递。

如果想传递一个异常，就必须在方法的首部添加一个throws说明符，以便告知调用者这个方法可能会抛出异常。 

仔细阅读一下Java API文档，以便知道每个方法可能会抛出哪种异常，然后再决定是自己处理，还是添加到throws列表中。对于后一种情况，也不必犹豫。将异常直接交给能够胜任的处理器进行处理要比压制对它的处理更好。

同时请记住，这个规则也有一个例外。前面曾经提到过：如果编写一个覆盖超类的方法，而这个方法又没有抛出异常（如JComponent中的paintComponent)，那么这个方法就必须捕获方法代码中出现的每一个受查异常。不允许在子类的throws说明符中出现超过超类方法所列出的异常类范围。

### 2.2 捕获多个异常
在一个try语句块中可以捕获多个异常类型，并对不同类型的异常做出不同的处理。可以按照下列方式为每个异常类型使用一个单独的catch子句： 

```java
try {
	code that might throw exceptions
}
catch (FileNotFoundException e) {
	emergency action for missing files
}
catch (UnknownHostException e) {
	emergency action for unknown hosts
}
catch (IOException e) {
	emergency action for allother I/Oproblems
}
```

异常对象可能包含与异常本身有关的信息。要想获得对象的更多信息，可以试着使用 

```
e.getHessage()
```

得到详细的错误信息（如果有的话)，或者使用

```
e.getClass().getName()
```

得到异常对象的实际类型。

在Java SE 7中，同一个catch子句中可以捕获多个异常类型。例如，假设对应缺少文件和未知主机异常的动作是一样的，就可以合并catch子句：

```java
try {
	code that might throw exceptions
}
catch (FileNotFoundException|UnknownHostException e) {
	emergency action for missing filesand unknown hosts
}
catch (IOException e) {
	emergency action for all other I/O problems
} 
```

只有当捕获的异常类型彼此之间不存在子类关系时才需要这个特性。 

> 注释：捕获多个异常时，异常变量隐含为final变量。例如，不能在以下子句体中为e赋不同的值： 
>
> ```
> catch (FileNotFoundException|UnknownHostException e) {...}
> ```

> 注释：捕获多个异常不仅会让你的代码看起来更简单，还会更高效。生成的字节码只包含一个对应公共catch子句的代码块。

### 2.3 再次抛出异常与异常链

在 catch 子句中可以抛出一个异常， 这样做的目的是改变异常的类型，： 如果开发了一个供其他程序员使用的子系统， 那么， 用于表示子系统故障的异常类型可能会产生多种解释。ServletException 就是这样一个异常类型的例子。执行 servlet 的代码可能不想知道发生错误的细节原因， 但希望明确地知道 servlet 是否有问题。

下面给出了捕获异常并将它再次抛出的基本方法：

```java
try
{
	access the database
}
catch (SQLException e)
{
	throw new ServletException("database error: " + e.getMessage()) ;
}
```

这里，ServleException用带有异常信息文本的构造器来构造。

不过，可以有一种更好的处理方法，并且将原始异常设置为新异常的“ 原因”： 

```java
try
{
	access the database
}
catch (SQLException e)
{
	Throwable se = new ServletException ("database error");
	se.initCause(e);
	throw se;
}
```

当捕获到异常时， 就可以使用下面这条语句重新得到原始异常：

```java
Throwable e = se.getCause();
```

强烈建议使用这种包装技术。这样可以让用户抛出子系统中的高级异常，而不会丢失原始异常的细节。  

> 提示： 如果在一个方法中发生了一个受查异常， 而不允许抛出它， 那么包装技术就十分有用。我们可以捕获这个受查异常， 并将它包装成一个运行时异常。

有时你可能只想记录一个异常， 再将它重新抛出， 而不做任何改变：  

```java
try
{
	access the database
}
catch (Exception e)
{
	logger.log(level, message, e);
	throw e;
}
```

在Java SE 7之前，这种方法存在一个问题。假设这个代码在以下方法中：

```
public void updateRecord() throws SQLException
```

Java编译器查看catch块中的throw语句，然后查看e的类型，会指出这个方法可以抛出任何Exception而不只是SQLException。现在这个问题已经有所改进。编译器会跟踪到e来自try块。假设这个try块中仅有的已检査异常是SQLException实例，另外，假设e在catch块中未改变，将外围方法声明为throws SQLException就是合法的。

### 2.4 finally 子句

当代码抛出一个异常时，就会终止方法中剩余代码的处理，并退出这个方法的执行。如果方法获得了一些本地资源，并且只有这个方法自己知道，又如果这些资源在退出方法之前 必须被回收，那么就会产生资源回收问题。一种解决方案是捕获并重新抛出所有的异常。但是，这种解决方案比较乏味，这是因为需要在两个地方清除所分配的资源。一个在正常的代码中；另一个在异常代码中。

Java有一种更好的解决方案，这就是finally子句。下面将介绍Java中如何恰当地关闭一个文件。如果使用Java编写数据库程序，就需要使用同样的技术关闭与数据库的连接。在卷Ⅱ的第4章中可以看到更加详细的介绍。当发生异常时，恰当地关闭所有数据库的连接是非常重要的。

不管是否有异常被捕获，finally子句中的代码都被执行。在下面的示例中，程序将在所有情况下关闭文件。

```java
InputStream in = new FileInputStream(...); 
try {
	//1
	code that might throwexceptions
	//2
}
catch (IOException e) {
	// 3
	show error message 
	// 4 
}
finally {
	// 5
	in.close();
}
//6
```

在上面这段代码中，有下列3种情况会执行finally子句：

- 1)代码没有抛出异常。在这种情况下，程序首先执行try语句块中的全部代码，然后执 行finally子句中的代码。随后，继续执行try语句块之后的第一条语句。也就是说，执行标注的1、2、5、6处。 

- 2)抛出一个在catch子句中捕获的异常。在上面的示例中就是IOException异常。在这种 情况下，程序将执行try语句块中的所有代码，直到发生异常为止。此时，将跳过try语句块中的剩余代码，转去执行与该异常匹配的catch子句中的代码，最后执行finally子句中的代码。如果catch子句没有抛出异常，程序将执行try语句块之后的第一条语句。在这里，执行标注1、3、4、5、6处的语句。如果catch子句抛出了一个异常，异常将被抛回这个方法的调用者。在这里，执行标注1、3、5处的语句。
- 3)代码抛出了一个异常，但这个异常不是由catch子句捕获的。在这种情况下，程序将 执行try语句块中的所有语句，直到有异常被抛出为止。此时，将跳过try语句块中的剩余代码，然后执行finally子句中的语句，并将异常抛给这个方法的调用者。在这里，执行标注1、5处的语句。

try语句可以只有finally子句，而没有catch子句。例如，下面这条try语句：

```java
InputStream in = ...;
try {
	code that might throw exceptions
}
finally {
	in.close();
} 
```

无论在try语句块中是否遇到异常，finally子句中的in.close()语句都会被执行。当然, 如果真的遇到一个异常，这个异常将会被重新抛出，并且必须由另一个catch子句捕获。

事实上，我们认为在需要关闭资源时，用这种方式使用finally子句是一种不错的选择。

下面的提示将给出具体的解释。


> 提示： 这里，强烈建议解搞合try/catch和try/finally语句块。这样可以提高代码的清晰度。例如：
>
> ```java
> InputStrean in = ...;
> try {
> 	try {
> 		code that might throw exceptions
> 	}
> 	finally {
> 		in.close();
> 	}
> }
> catch (IOException e) {
> 	show error message
> }
> ```
> 
> 内层的try语句块只有一个职责，就是确保关闭输入流。外层的try语句块也只有一个职责，就是确保报告出现的错误。这种设计方式不仅清楚，而且还具有一个功能，就是将会报告finally子句中出现的错误。

> 警告：当finally子句包含return语句时，将会出现一种意想不到的结果„ 假设利用return语句从try语句块中退出。在方法返回前，finally子句的内容将被执行。如果finally子句中也有一个return语句，这个返回值将会覆盖原始的返回值。请看一个复杂的例子：
>
> ```java
> public static int f(int n) {
> 	try {
> 		int r = n * n;
> 		return r;
> 	}
> 	finally {
> 		if (n = 2)
> 			return 0;
> 	}
> } 
> ```
> 
> 如果调用f(2), 那么try语句块的计算结果为r=4,并执行return语句然而，在方法真正返回前，还要执行finally子句。finally子句将使得方法返回0, 这个返回值覆盖了原 始的返回值4。

有时候，finally子句也会带来麻烦。例如，清理资源的方法也有可能抛出异常。假设希望能够确保在流处理代码中遇到异常时将流关闭。

```java
InputStreai in = ...;
try {
	code that might throw exceptions
}
finally {
	in.close();
}
```

现在，假设在try语句块中的代码抛出了一些非IOException的异常，这些异常只有这个方法的调用者才能够给予处理。执行finally语句块，并调用close方法。而clos 方法本身也有可能抛出IOException异常。当出现这种情况时，原始的异常将会丢失，转而抛出close方法的异常。 

这会有问题，因为第一个异常很可能更有意思。如果你想做适当的处理，重新抛出原来的异常，代码会变得极其繁琐。如下所示：

```java
InputStream in = ...;
Exception ex = null;
try {
	try {
		code that might throw exceptions
	} catch (Exception e) {
		ex=e;
		throw e;
	}
}
finally {
	try {
		in.close()；
	} catch (Exception e) {
		if (ex = null)
			throw e;
	}
}
```

幸运的是，下一节你将了解到，Java SE 7中关闭资源的处理会容易得多。

## 4 使用断言

### 4.1 断言的概念

假设确性某个属性符合要求，并且代码的执行依赖于这个属性。例如，需要计算

```
double y=Math.sqrt(x);
```

我们确信，这里的 X 是一个非负数值。原因是：X 是另外一个计算的结果， 而这个结果不可能是负值；或者 X 是一个方法的参数，而这个方法要求它的调用者只能提供一个正整数。然而，还是希望进行检查， 以避免让“ 不是一个数” 的数值参与计算操作。当然，也可以抛出一个异常：  

```java
if(x<0) throw new IllegalArgumentException("x<0");
```

但是这段代码会一直保留在程序中， 即使测试完毕也不会自动地删除。如果在程序中含有大量的这种检查，程序运行起来会相当慢。

断言机制允许在测试期间向代码中插入一些检査语句。当代码发布时，这些插入的检测语句将会被自动地移走。  

Java语言引入了关键字assert。这个关键字有两种形式：  

```
assert 条件;
```

和

```
assert 条件：表达式;
```

这两种形式都会对条件进行检测， 如果结果为false, 则抛出一个AssertionError异常。在第二种形式中，表达式将被传入AssertionError的构造器， 并转换成一个消息字符串。

> 注释：“ 表达式” 部分的唯一目的是产生一个消息字符串。AssertionError 对象并不存储表达式的值， 因此， 不可能在以后得到它。正如 JDK 文档所描述的那样： 如果使用表达式的值， 就会鼓励程序员试图从断言中恢复程序的运行， 这不符合断言机制的初衷。

要想断言x是一个非负数值， 只需要简单地使用下面这条语句

```java
assert x>=0;
```

或者将x的实际值传递给AssertionError对象， 从而可以在后面显示出来。

```java
assert x>=0:x;
```

> C++ 注释：C 语言中的assert宏将断言中的条件转换成一个字符串。 当断言失败时，这个字符串将会被打印出来。例如，若 assert(x>=0) 失败， 那么将打印出失败条件“ x>=0”。在 Java 中， 条件并不会自动地成为错误报告中的一部分。 如果希望看到这个条件， 就必须将它以字符串的形式传递给 AssertionError 对象：`assert x >= 0": x >= 0"`