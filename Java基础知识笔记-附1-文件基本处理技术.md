Java基础知识笔记-附1-文件基本处理技术
## 1 File类
创建一个File对象的构造方法有3个：
```java
File(String filename);
File(String directoryPath,String filename);
File(Flie f,String filename);//f是指定一个目录的文件。
```
### 1.1文件的属性
查询文件的属性： 
```java
public String getAbsolutePath();//获取绝对路径 
public String getPath();//获取相对路径 
public String getName();//获取名称 
public long length();//获取长度。字节数 
public long lastModified();//获取最后一次的修改时间，毫秒值 
```
> 例如：
```java
File file = new File("G:\\a\\a.txt"); 
System.out.println("getAbsolutePath:" + file.getAbsolutePath()); 
System.out.println("getPath:" + file.getPath()); 
System.out.println("getName:" + file.getName()); 
System.out.println("length:" + file.length()); 
System.out.println("lastModified:" + file.lastModified()); // 1416471971031 
Date d = new Date(1416471971031L); //格式化时间 
SimpleDateFormat sdf = new SimpleDateFormat("yyyy-MM-dd HH:mm:ss"); 
String s = sdf.format(d); 
System.out.println(s);
```
>来自 <https://blog.csdn.net/m15517986455/article/details/78619481/> 
### 1.2目录
#### 1.创建目录
File对象调用方法: 
```java
public boolean mkdir();
```
创建一个目录，如果创建成功返回true，否则返回false(如果该目录已经存在将返回false.
#### 2.列出目录中的文件
如果File对象是一个目录.那么该对象可以调用下述方法列出该目录下的文件和子目录。
```java
public String[] list();  //用字符串形式返回目录下的全部文件。
public File[] listFiles();  //用File对象形式返回目录下的全部文件。
```
有时需要列出目录下指定类型的文件.例如java.txt等扩展名的文件。可以使用File类的&下述两个方法，列出指定类型的文件。
```java
public String[] list(FilenameFilter obj); //该方法用字符串形式返回目录下的指定类型的所有文件。
public File[] listFiles(FilenameFilter obj);  //该方法用File对象形式返回目录下的指定类型所有文件。
```
上述两个方法的参数FilenameFilter是一个接口，该接口有一个方法:
```java
public boolean accept(File dir, String name);
```
使用list方法时，需向该方法传递一个实现FilenameFilter接口的对象,list方法执行时,参数obj不断回调接口方法accept(File dir,String name),该方法中的参数dir为调用list的当前目录、参数name被实例化目录中的一个文件名，当接口方法返回true时,list方法就将名字为name的文件存放到返回的数组中。  

在例10.2中,列出当前目录(应用程序所在的目录)下全部Java文件的名字。
```java
//Example10 2. java

import java.io.*;
public class Example10_2 {
	public static void main(String args[]) {
		File dir=new File(".");//所有文件
		FileAccept fileAccept = new FileAccept();
		fileAccept. setExtendName(" java );
		String fileName[] = dir. list(fileAccept);
		for(String name:fileName) {
			System. out. printIn(name);
		}
	}
}
```
```java
//FileAccept. java

import java.io.*;
public class FileAccept implements FilenameFilter {
	private String extendName;
	public void setExtendName(String s){
		extendName="."+s;
	}
	public boolean accept(File dir,String name){
		//重写接口中的方法
		return name.endWith(extendName);
	}
}
```
### 1.3文件的创建与删除
当使用File类创建一个文件对象后，例如：
```java
File file=new File("c:\\myletter","letter.txt");
```
如果该目录下没有这个文件，那么文件对象file的调用方法
```java
public boolean creatNewFile();  //可以在当前目录下创建这个文件。
```
文件对象调用
```java
public boolean delete();
```
可以删除当前文件 file.delete();

### 1.4运行可执行文件
当要执行一个本机上的可执行文件时，可以使用java.lang包中的Runtime类，首先使用Runtime类声明一个对象：
```
Runtime ec;
```
然后使用该类的getRuntime()静态方法创建这个对象：
```java
ec=Runtime.getRuntime();
```
## 2 字节流和字符流简介&概要

![image](http://www.runoob.com/wp-content/uploads/2013/12/iostream2xx.png)

java.io包提供了大量的流类，其中
- Java把InputStream抽象类的子类创建的流对象称作字节输入流，Outputstrem抽象类的子类创建的流对象称作字节输出流
- Java把Reader抽象类的子类创建的流对象称作字符输入流，Writer抽象类的子类创建的流对象称作字符输出流。  

针对不同的源或目的地java.io包为程序提供了相应的输入流或输出流，这些输入输出流绝大部分都是InputStream，Outpustream，Reader或Writer的子类,例如，如果需要以字节为单位对磁盘上的文件进行读写操作，就可以分别使用InputStream和OutputStream的子类FileInputStreamFileOutputStream来创建文件输入流和文件输出流。  

本章的后续小节将陆续介绍java.io包提供的重要的、常用的输入输出流。

#### InputStream,OutputStream,Reader,Writer

- InputStream和OutputStream两个是为字节流设计的,主要用来处理字节或二进制对象
- Reader和Writer两个是为字符流（一个字符占两个字节）设计的,主要用来处理字符或字符串.  

>字符流处理的单元为2个字节的Unicode字符，分别操作字符、字符数组或字符串，而字节流处理单元为1个字节，操作字节和字节数组。所以字符流是由Java虚拟机将字节转化为2个字节的Unicode字符为单位的字符而成的，所以它对多国语言支持性比较好，如果是音频文件、图片、歌曲，就用字节流好点，如果是关系到中文（文本）的，用字符流好点。

>所有文件的储存是都是字节（byte）的储存，在磁盘上保留的并不是文件的字符而是先把字符编码成字节，再储存这些字节到磁盘。在读取文件（特别是文本文件）时，也是一个字节一个字节地读取以形成字节序列。

>字节流可用于任何类型的对象，包括二进制对象，而字符流只能处理字符或者字符串；字节流提供了处理任何类型的I/O操作的功能，但它不能直接处理Unicode字符，而字符流就可以字节流是最基本的，所有的InputStrem和OutputStream的子类都是,主要用在处理二进制数据，它是按字节来处理的但实际中很多的数据是文本，又提出了字符流的概念，它是按虚拟机的encode来处理，也就是要进行字符集的转化。这两个之间通过InputStreamReader,OutputStreamWriter来关联，实际上是通过byte\[]和String来关联在实际开发中出现的汉字问题实际上都是在字符流和字节流之间转化不统一而造成的。

### 2.1 InputStream类与OutputStream类

InputStream类提供的read方法以字节为单位顺序地读取源中的数据，只要不关闭流，每次调用read方法就顺序地读取源中的其余内容.直到源的末尾或输入流被关闭。

InputStream类有如下常用的方法。

```java
int read();//输入流调用该方法从源中读取单个字节的数据，该方法返回字节值(0~255之间的一个整数),如果未读出字节就返回-1.
int read(byte b[]);//输入流调用该方法从源中试图读取b.length个字节到b中,返回实际读取的字节数目。如果到达文件的末尾，则返回一1。
int read(byte b[],intoff,intlen);//输入流调用该方法从课中试图读取len个字节到b中,并返回实际读取的字节数目，如果到达文件的末尾，则返回一1,参数ff指定从字节数组的某个位置开始存放读取的数据。
void close();//输入流调用该方法关闭输入流。
long skip(long numBytes);//输入流调用该方法跳过numByes个字节，并返回实际跳过的字节数目。
```
OutStrem流以字节为单位顺序地写文件，只要不关闭流，每次调用write方法顺序地向目的地写入内容，直到流被关闭。

OutputStream类有如下常用的方法

```java
void write(int n);  //输出流调用该方法向输出流写入单个字节。
void write(byte b[]);  //输出流调用该方法向输出流写入一个字节数组。
void wite(byte b[],int off,int len);  //输出流从给定字节数组中起始于偏移量off处取len个字节写到输出流。 
void close();  //关闭输出流
```
读文件：
```java
import java.io.File;
import java.io.FileInputStream;
import java.io.IOException;
import java.io.InputStream;
public class Test12 {
    public static void main(String[] args) throws IOException {
        File f = new File("d:" + File.separator+"test.txt");
        InputStream in=new FileInputStream(f);
        byte[] b=new byte[1024];
        int len=in.read(b);
        in.close();
        System.out.println(new String(b,0,len));
    }
}
```
>来自 <https://blog.csdn.net/qq_25827845/article/details/51525270> 
```java
写文件：
import java.io.File;
import java.io.FileOutputStream;
import java.io.IOException;
import java.io.OutputStream;
public class Test11 {
    public static void main(String[] args) throws IOException {
        File f = new File("d:" + File.separator+"test.txt");
        OutputStream out=new FileOutputStream(f);//如果文件不存在会自动创建
        String str="Hello World";
        byte[] b=str.getBytes();
        out.write(b);//因为是字节流，所以要转化成字节数组进行输出
        out.close();
    }
}
```
```java
import java.io.File;
import java.io.FileOutputStream;
import java.io.IOException;
import java.io.OutputStream;

public class Test11 {
    public static void main(String[] args) throws IOException {
        File f = new File("d:" + File.separator+"test.txt");
        OutputStream out=new FileOutputStream(f);//如果文件不存在会自动创建
        String str="Hello World";
        byte[] b=str.getBytes();
        for(int i=0;i<b.length;i++){
            out.write(b[i]);
        }
        out.close();
    }
}
```
>来自 <https://blog.csdn.net/qq_25827845/article/details/51525270> 
### 2.2 Reader类与Writer类
Reader类提供的read方法以字符为单位顺序地读取源中的数据，只要不关闭流，每次调用read方法就顺序地读取源中的其余内容，直到源的末尾或输入流被关闭。  

Reader类有如下常用的方法。
```java
int read();  //输入流调用该方法从源中读取一个字符，该方法返回一个整数(0~65535之间的一个整数,Unicode字符值)，如果未读出字符就返回-1
int read(char b[]);  //输入流调用该方法从源中读取b.length个字符到字符数组中，返回实际读取的字符数目。如果到达文件的末尾，则返回-1
int read(char b[]，int off, int len);  //输入流调用该方法从源中读取len个字符并存放到字符数组b中,返回实际读取的字符数目。如果到达文件的末尾，则返回-1.其中,off参数指定read方法在字符数组b中的什么地方存放数据。
void close();//输入流调用该方法关闭输入流。
long skip(long numBytes);  //输入流调用该方法跳过numBytes个字符,并返回实隔跳过的字符数目。
```
OutStream流以字符为单位顺序地写文件，只要不关闭流，每次调用write方法就顺序地向目的地写入内容，直到流被关闭。  

Writer类有如下常用的方法

```java
void write(int n);  //向输人流写入一个字符。
void write(byte b[]);  //向输入流写人一个字符数组。
void write(byte b[],int off,int length);  //从给定字符数组中起始于偏移量off处取len个字符写到输出流。
void close();  //关闭输出流。
```
### 2.3  关闭流
流都提供了关闭方法close()，尽管程序结束时会自动关闭所有打开的流，但是当程序使用完流后，显式地关闭任何打开的流仍是一个良好的习惯。如果没有关闭那些被打开的流，那么就可能不允许另一个程序操作这些流所用的资源。  

另外，需要注意的是，在操作系统把程序所写到输出流上的那此字节保存到磁盘之前。有时被存放在内存缓存区中，通过调用close()方法，可以保证操作系统把流缓存区的内容写到他的目的地，即关闭输出流可以把该流所用的缓存区的内容冲洗掉（通常冲洗到磁盘文件上）

## 3 文件字节流
要注意的是：
- InputStream/OutputStream:是基类，它们是抽象类

由于应用程序经常需要和文件打交道,所以InputStream专门提供了读写文件的子类:FileInputStream和FileOutputSream类。如果程序对文件的操作比较简单，例如只是顺序地读写文件,那么就可以使用FilelnputStream和FileOutputSream类创建的流对文件进行读写操作。
### 3.1文件字节输入流 (FileInputStream)

如果需要以字节为单位去读文件，就可以使用FilelnputStream类来创建指向该文件的文件字节输入流。下面是FilelnputStream类的两个构造方法。
```java
FileInputStream(String name);
FileInputStream(File file);
```
第一个构造方法使用给定的文件名name创建一个FilelnputStream对象，第二个构造方法使用File对象创建FilelnputStream对象。参数name和file指定的文件称作输入流的源,输入流通过调用read方法读出源中的数据。  

FileInputStream输入流打开一个到达文件的输入流（源就是这个文件，输入流指向这个文件）。当使用文件输入流构造方法建立通往文件的输入流时,可能会出现错误(也被称为异常)。例如，试图要打开的文件可能不存在。当出现I/O错误，Java生成一个出错信号，它使用一个IOException(IO异常)对象来表示这个出错信号。程序必须在try-catch语句中的try块创建输入流对象，在catch(捕获)块检测并处理这个异常。  

InputStream有如下方法：

```java
int read();//输入流调用该方法从源中读取单个字节的数据，该方法返回字节值(0~255之间的一个整数),如果未读出字节就返回-1.
int read(byte b[]);//输入流调用该方法从源中试图读取b.length个字节到b中,返回实际读取的字节数目。如果到达文件的末尾，则返回一1。
int read(byte b[],intoff,intlen);//输入流调用该方法从课中试图读取len个字节到b中,并返回实际读取的字节数目，如果到达文件的末尾，则返回一1,参数ff指定从字节数组的某个位置开始存放读取的数据。
```

例如，为了读取一个名为hello.txt的文件,建立一个文件输入流对象，如下所示。
```java
try { 
	FileInputStream in = new FileInputStrean("hello.txt");//创建文件字节输入流
}
catch(IOExceptione) {
	System.out.println("File read error:"+e);
}
```
文件字节流可以调用从父类继承的read方法顺序地读取文件，只要不关闭流，每次调用read方法就顺序地读取文件中的其余内容，直到文件的末尾或文件字节输入流被关闭。  

例10.4使用文件字节输入流读取文件，将文件的内容显示在屏幕上。程序运行效果如图10.4所示。
```java
import java.io.*;
public class Example10_4{
	public static void main(String args[]) {
		int n=-1;
		byte [] a=new byte[100];
		try {  
			File f=new File("Example10_4.java");
			FileInputStream in = new FileInputStream(f);
			while((n=in.read(a,0,100))!=-1) {
				String s=new String (a,0,n);
				System.out.print(s);
			}
			in.close();
		}
		catch(IOException e) {
			System.out.println("Eile read Error"+e);
		}
	}
}
```
### 3.2文件字节输出流  (FileOutputStream)
下面是FileOutputStream类的两个构造方法：
```java
FileOutputStream(String name);
FileOutputStrean(File file);
```
第一个构造方法使用给定的文件名name创建一个FileOutputStream对象，第二个构造方法使用File对象创建FileOutputStream对象。  

FileOutputStream流的目的地是文件，所以文件输出流调用write(byte b[])方法把字节写入到件。FileOutputStrean流顺序地向文件写入内容，即只要不关闭流，每次调用write方法就顺序地向文件写入内容，直到流被关闭。  

需要注意的是，如果FileOutputStream流要写的文件不存在。该流将首先创建要写的文件。然后再向文件写入内容，如果要写的文件已经存在，则刷新文件中的内容，然后再顺序地向文件写入内容。  

可以使用FileOutputStream类的下列能选择是否具有刷新功能的构造方法创建指向文件的输出流。
```java
FileOutputStream(String name,boolean append);
FileOutputStream(File file,boolean append);
```
当用构造方法创建指向一个文件的输出流时，如果参数append取值true，输出流不会训新指向的文件(假如文件已存在),输出流的write的方法将从文件的末尾开始向文件写入数据，参数append取值false, 输出流将刷新指向的文件(假如文件已存在)。  

OutputStream类有如下方法：

```java
void write(int n);  //输出流调用该方法向输出流写入单个字节。
void write(byte b[]);  //输出流调用该方法向输出流写入一个字节数组。
void wite(byte b[],int off,int len);  //输出流从给定字节数组中起始于偏移量off处取len个字节写到输出流。 
```

例10.5使用文件字节输出流写文件,将“国庆60周年”和“十一快乐”写入到名字为happy.txt的文件中。

```java
//Example10.5

import java.io.*;
public class Example10_5 {
	public static void main(String args[]) {
		byte[]a="国庆60周年".getBytes();
		byte[]b="十一快乐".getBytes();
		try {
			FileOutputStrean out=new FileOutputStream("happy.txt");  
			out.write(a);
			out.write(b,0,b.length);
			out.close();
		}
		catch(IOException e) {
			System.out.println("Error"+e);
		}
	}
}
```
## 4 文件字符流 (FileReader/FileWriter)

> Reader/Writer:是基类，它们是抽象类

字节输入流和输出流的read和write方法使用字节数组读写数据，即以字节为基本位处理数据。因此，字节流不能很好地操作Unicode字符，例如，一个汉字在文件中占个字节，如果使用字节流，读取不当会出现“乱码”现象。  

> 与FilelnputStream、FileOutputStream字节流相对应的是FileReader,FileWriter

字节流FileReader和FileWriter分别是Reader和Writer的子类，其构造方法分别是:
```java
FileReader(String filename); 
FileReader(File filename);
FileWriter(String filename); 
FileWriter(File filename);
FileWriter(String filename,boolean append); 
FileWriter(File filenane,boolean append);
```
字符输入流和输出流的read和write方法使用字符数组读写数据，即以字符为基本位处理数据。  

Reader类里面包含如下三个方法

```java
int read();  //从输入流中读取单个字符，返回所读取的字符数据（字符数据可直接转换成int类型）
int read(char[] cbuf); //从输入流中最多读取cbuf.length个字符的数据，并将其储存在字符数组cbuf中，返回实际读取的字符数
int read(char[] cbuf,int off,int len);  //从输入类中读取len个字符的数据，并将其储存在字符数组cbuf中，放入cbuf中时，并不是从数组起点开始，而是从off位置开始，返回实际读取的字符数
```

Write类有如下方法：

```java
void write(int n);  //向输人流写入一个字符。
void write(byte b[]);  //向输入流写人一个字符数组。
void write(byte b[],int off,int length);  //从给定字符数组中起始于偏移量off处取len个字符写到输出流。
```

例10.6使用字符输出流将一段文字存入文件，然后再使用字符输入流去读文件。

```java
//[例10.6]
//Example10_6.java
import java.io.*;
public class Example10_6 {
	public static void main(String args[]) {
		String content = "broadsword 勇者无敌";
		try { 
			File f= new File("hello.txt");
			char [] a=content.toCharArray();
			FileWriter out = new FileWriter(f);
			out.write(a,0,a.length);
			out.close();
			FileReader in=new FileReader(f);
			StringBuffer s=new StringBuffer();
			char tom[] = new char[10];
			int n=- 1;
			while((n=in.read(tom,0,10))!=-1){
				String temp=new String (tom,0,n);
				s.append(temp);
			}
			in.close();
			System.out.println(new String(s));
		}
		catch(IOExceptione) {
			Systen.out.println(e.tostring());
		}
	}
}
```
> 注意：对于Writer流，write方法将数据首先写入到缓冲区，每当缓冲区溢出时，缓冲区的内容被自动写入到目的地，如果关闭流，缓冲区的内容会直接被写入到目的地。流调用flush()方法可以立刻冲洗当前缓冲区，即将当前缓冲区的内容写入到目的地。

## 5 缓冲流(属于字符流)

BufferedReader和 BufferedWriter类创建的对象称作缓冲输入输出流，二者增强了读写文件的能力。例如Student.txt是一个学生名单，每个姓名占一行。如果我们想读取名字，那么每次必须读取一行，使用FileReader流很难完成这样的任务，因为，我们不清楚一行有多少个字符，FileReader类没有提供读取一行的方法。  

Java提供了更高级的流： 

BufferedReader流和BufferedWriter，二者的源和目的地必须是字符输入流和字符输出流。因此，如果把字符输入流作为BufferedReader流的源，把字符输出流作为BufferedWriter流的目的地，那么, BufferedReader和Bufferedwriter类创建的流将比字符输入流和字符输出流有更强的读写能力，例如，BufferedReader流就可以按行读取文件。  

BufferedReader类和BufferedWriter的构造方法分别是:
```java
BufferedReader(Reader in);
BufferedWriter(Writer out);
```
BufferedReader流能够读取文本行,方法是 readLine()  

通过向BufferedReader传递一个Reader子类的对象(如Filereader的实例),来创建一个Bufferedreader对象，如:
```java
FileReader inOne=new FileReader("Student. txt");
BufferedReader inTwo BufferedReader(inOne);
```
然后 inTwo流调用 readLine()方法中读取 Student.txt,例如
```java
String strLine=inTwo readLine();
```
类似地,可以将BufferedWriter流和FileWriter流连接在一起，然后使用BufferedWriter流将数据写到目的地，例如
```java
FileWriter tofile= new FileWriter("hello. txt");
BufferedWriter out= BufferedWriter(tofile);
```
然后out使用 BufferedReader类的方法
```java
write(String s, int off, int len)
```
把字符串s写到 hello.txt中,参数off是s开始处的偏移量,len是写入的字符数量  
另外, BufferedWriter流有一个自己独特的向文件写入一个回行符的方法
```java
newLine();
```
可以把 BufferedReader和BufferedWriter称作上层流，把它们指向的字符流称作底层流。Java采用缓存技术将上层流和底层流连接。底层字符输入流首先将数据读入缓存，BufferedReader流再从缓存读取数据；BufferedWriter流将数据写入缓存，底层字符输出流会不断地将缓存中的数据写入到目的地。    

当 BufferedWriter流调用flush()刷新缓存或调用close()方法关闭时，即使缓存没有溢满，底层流也会立刻将缓存的内容写入目的地。

代码实现（先将字符串按行写入文件，在逐行读取文件）
```java
import java.io.*
public class exercise{
	public static void main(String args[]){
		File file =new File("Student.txt");
		String content[]={"商品列表：","电视机，2567元/台","洗衣机，3562元/台","冰箱，6573元/台"};
		try{
			FileWriter outOne=new FileWriter(file);
			BufferedWriter outTwo=new BufferedWriter(outOne);
			for(String str:content){
				outTwo.writer(str);
				outTwo.newLine();//写入一个回车
			}
			outTwo.close();
			outOne.close();
			FileReader inOne=new FileReader(file);
			BufferedReader inTwo=new BufferedReader(inOne);
			String s=null;
			while((s=inTwo.readline())!=null){
				System.out.println(s);
			}
			inOne.close();
			inTwo.close();
		}
		catch(IOException e){
			System.out.println(e);
		}
	}
}
```
## 6 随机流
通过前面的学习我们知道，如果准备读文件,需要建立指向该文件的输人流;如果准备写文件,需要建立指向该文件的输出流。那么，能否建立一个流，通过该流既能读文件也能写文件呢?这正是本节要介绍的随机流。  

RandomAccessFile类创建的流称作随机流，与前面的输人输出流不同的是，RandomAccessFile类既不是InputStream类的子类，也不是OutputStream类的子类。但是RandomAcessFile类创建的流的指向既可以作为流的源，也可以作为流的目的地，换句话说，当准备对一个文件进行读写操作时，可以创建一个指向该文件的随机流即可，这样既可以从这个流中读取文件的数据，也可以通过这个流写人数据到文件。  

以下是RandomAccessFile类的两个构造方法。
```java
RandoAccessFile(String name,String mode);
//参数name用来确定一个文件名，给出创建的流的源，也就是流目的地。参数mode取r(只读)或rw(可读写)，决定创建的流对文件的访问权力。
RandomAccessFile(File file,String mode);
//参数file是一个File对象，给出创建的流的源，也是流目的地。参数mode取r(只读)或rw(可读写)，决定创建的流对文件的访问权力。
```
RandomAccessFile类中有一个方法: 
```java
seek(long a);
```
用来定位RandomAccessFile流的读写位置，其中参数a确定读写位置距离文件开头的字节个数。另外流还可以调用`getFilePointer()`方法获取流的当前读写位置

RandomAccessFile流对文件的读写比顺序读写更为灵活。

```java
//例10.8中把几个int型整数写人到一个名字为tom.dat文件中，然后按相反顺序读出这些数据。
//[例10.8]
//Example10 8. java

import java.io.* ;
public class Exanple10{
	public static void Bain(String args[]) {
		RandorAccessFile inAndOut= null;
		int data[]= {1,2,3,4,5,6,7,8,9,10};
		try{
			inAndOut = new RandomAccessFile("tom.dat","rw");
			for(int i=0;i<data.length;i++) {
				inAndOut.writeInt(data[i]);
			}
			for(long i=data.length-1;i>=0;--){//一个int型数据占4个字节，inAndout从文件的第36个字节读取最后面的一个整数
				inAndout.seek(i*4);
				System.out.println("\t%d",inAnout.readInt());//隔4个字节往前读取个整数
			}
			inAndOut.close();
		}
		catch(I0Exception e){
		} 
	}
}
```
方法 | 描述
---|---
close()|关闭文件
getFilePointer()|	获取当前读写的位置
length()|	获取文件的长度
read()|	从文件中读取一个字节的数据
readBoolean()|	从文件中读取个布尔值，0代表falses，其他值代表true
readByte()|	从文件中读取个字节
rendChar()|	从文件中读取一个字符(2个字节)
readDouble()|	从文件中读取一个双精度浮点值(8个字节)
readFlont()|	从文件中读取一个单精度评浮点值(4个字节)
rendFully(byte b[ ])|	读b. length字节放人数组b,完全填满该数组
readInt()|	从文件中读取一个int值(4个字节)
readLine()|	从文件中读取一个文本行
readlong()|	从文件中读取 个长型值(8个字节)
rendShort()|	从文件中读取 个短型值(2个字节)
readUnsignedByte()|	从文件中读取一个无符号字节(1个字节)
rendUnsignedShort()|	从文件中读取一个无符号短型值(2个字节)
readUTF()|	从文件中读取一个UTF字符串
seek(long position)|	定位读写位置
setLength(long newlength)|	设置文件的长度
skipBytes(int n)|	在文件中跳过给定数量的字节
write(byte b[])|	写b. length个字节到文件
writeBoolean(boolean v)| 	把一个布尔值作为单字节值写入文件
writeByte(int v)|	向文件写入一个字节
writeBytes(String s)| 	向文件写入一个字符串
writeChar(char c)|	向文件写入一个字符
writeChars(String s)|	向文件写入一个作为字符数据的字符串
writeDouble(double v)|	向文件写入一个双精度浮点值
writeFloat(float v)| 	向文件写入一个单精度浮点值
writeInt(int v)| 	向文件写入一个int值
writeLong(long v)|	向文件写入一个长型int值
writeShort(int v)|	向文件写入一个短型int值
writeUTF(String s)|	 写入一个UTF字符串 
需要注意的是，RondomAccessFile流中的`readLine()`方法在读取含有非ASCII字符的文件时（例如含有汉字的文件）会出现乱码的情况，因此，需要把`readLine()`读取的字符串用ios-8859-1重新编码存放到byte数组中，然后再用当前机器的默认编码将该数组转化为字符串，操作如下：  
##### 1.读取
```
String str=in.readLine();
```
##### 2.用ios-8859-1重新编码
```
byte b[]=str.getBytes("iso-8859-1");
```
##### 3.使用当前机器的默认编码将字节数组转化为字符串
```
String content=new String(s);
```
如果当前机器的默认编码时GB2312，那么`String content=new String(b)`等同于`String content=new String(b,"GB2312")` 

例中RondomcessFile流使用readLine()读取文件。
```java
//Example10_9.java
import java.io.*;
public class Example10_9 {
	public static void main(String args[]) {
		RandomAccessFile in= null;
		try{ 
			in = new RandomAccessFile("Example10_ 9. java","rw");
			long length= in.length();//获取文件的长度
			long position=0;
			in.seek(position);//将读取位置定位到文件的起始
			while(position<length) {
				String str=in.readLine();
				byteb[]=str.getBytes("iso-8859-1");
				str=new String(b);
				position=in.getFilePointer();
				System.out.println(str);
			}
		}
		catch(I0Exception e){
		}
	}
} 
```
## 7 数据流
流的源和目标除了可以是文件外，还可以是计算机内存。
### 1.字节数组流
字节数组输入流ByteArrayInputStream和字节数组输出流ByteArrayOutputStream分别使用字节数组作为流的源和目标。

ByteArraylnputStream的构造方法如下。

```java
ByteArrayInputStrean(byte[] buf);
ByteArrayInputStrean(byte[] buf,int offset,int length);
```
第一个构造方法构造的字节数组流的源是参数buf指定的数组的全部字节单元，第二个构造方法构造的字节数组流的源是buf指定的数组从offset处按顺序取length个字节单元，

字节数组输入流调用：
```java
publlc int read();
```
方法可以顺序地从源中读出一个字节，该方法返回读出的字节值;调用
```java
publle Int read(byte[] b, int off,int len);
```
方法可以顺序地从源中读出参数len指定的字节数，并将读出的字节存放到参数b指定的数组中，参数off指定数组b存放读出字节的起始位置，该方法返回实际读出的字节个数。如果未读出字节read方法返回-1。

ByteArayOutputStream流的构造方法如下。
```java
ByteArrayoutputStream ();
ByteArrayoutputStream (int size);
```
第一个构造方法构造的字节数组输出流指向一个默认大小为32字节的缓冲区，如果输出流向缓冲区写人的字节个数大于缓冲区时，缓冲区的容量会自动增加。第二个构造方法构造的字节数组输出流指向的缓冲区的初始大小由参数size指定,如果输出流向缓冲区写入的字节个数大于缓冲区时，缓冲区的容量会自动增加。

字节数组输出流调用
```java
pablic void write(int b);
```
方法可以顺序地向缓冲区写入一个字节;调用
```java
pablic void write(byte[] b, int off,int len);
```
方法可以将参数b中指定的len个字节顺序地写人缓冲区，参数off指定从b中写出的字节起始位置；调用
```java
pablic byte[] toByteArray();
```
法可以返回输出流写入到缓冲区的全部字节。
### 2. 字符数组流

与数组字节流对应的是字符数组流CharArrayReader和CharArrayWriter类，
字符组流分别使用字符数组作为流的源和目标。   

例10.10使用数组流向内存(输出流的缓冲区)写入“国庆60周年”和“中秋快乐”,然后从内存读取曾写入的数据。

```java
import java.io.*
public class exercise{
	public static void main(String args[]){
		try{
			ByteArrayOutputStream outByt=new ByteArrayOutputStream();
			byte[] byteContent="guoqing60zhounian".getBytes();
			outByte.write(byteContent);
			ByteArrayInputStream inByte=new ByteArrayInputStream(outByte.toByteArray());
			byte backByte[]=new byte[outByte.toByteArray().length];
			inByte.read(backByte);
			System.out.println(new String(backByte));
			CharArrayWriter outChar=new CharArrayWriter();
			char [] charContent="zhongqiukuaile".toCharArray();
			outChar.write(charContent);
			CharArrayReader inChar=new CharArrayReader(outChar.toCharArray());
			char backChar[]=new char[outChar.toCharArray().lenght];
			inChar.read(backChar);
			System.out.println(new String(backChar));
		}
		catch(IOExpection exp){
		}
	}
}
```
## 8 数据流
DatalnputStream和DataOuputStream类创建的对象称为数据输入流和数据输出流，这两个流是很有用的流，它们允许程序按着机器无关的风格读取Java原始数据。也就是说，当读取一个数值时，不必再关心这个数值应当是多少个字节。

> 以下是DatalnputStream和DataOutputStream的构造方法。
```java
DataInputstream(InputStream in);  //创建的数据输入流指向一个由参数in指定的底层输入流。
DataOutputStream(OutputStream out); //创建的数据输出流指向一个由参数out指定的底层输出流。
```
表10.2  DatalnpulStream及DataOutputStcam 类的部分方法

方法|描述
---|---
close()|关闭流
readBoolean()|读取一个布尔值
readByte()|读取一个字节
readChar()|读取一个字符
readDouble()|读取一个双精度浮点值
readFloat()|读取个单精度浮点值
readInt()|读取一个int值
readlong()|读取一个长型值
readShort()|读取一个短型值
readUnsignedByte()|读取一个无符号字节
readUnsignedShort()|读取一个无符号短型值
readUTF()|读取一个UTF字符串
skipBytes(int n)|跳过给定数量的字节
writeBoolean(boolean v)|写入一个布尔值
writeBytes(String s)|写入一个字符串
writeChars(String s)|写入字符串
writeDouble(double v)|写入一个双精度浮点值
writeFloat(float v)| 写入一个单精度浮点值
writeInt(int v)|写入一个一个int值
writeLong(long v)|写入一个一个长型值
writeShort(int v)|写入一个一个短型值
writeUTF(String s)|写入一个UTF字符串

## 9 对象流
