# NIO



当时用BufferReader读取输入流数据时，如果没有读到有效数据，那么程序会在此处阻塞该线程的执行(使用InputStream的read方法读取输入流数据时，如果数据源没有数据，也会阻塞)，也就是说前面说的输入流输出流都是阻塞式的输入输出，不仅如此，传统的输入流输出流都是通过字节的移动来处理的，既然不直接处理字节流，底层也还是使用字节处理，也就是说，面向流的输入/输出系统一次只能处理一个字符，因此通常效率不高。

从jdk1.4，Java提供了一系列改进的输入输出新功能，这些功能被统称为新IO，新增了许多用于处理输入输出的类，这些类都放在java.nio包及其子包下。

## Java新IO概述



新IO和传统IO目的相同，用于输入/输出，但是新IO使用了不同的方式来处理输入输出。新IO采用内存映射文件的方式来处理输入输出，新IO将文件或者文件部分映射到内存中，这样就可以像访问内存一样来访问文件(这种方式模拟了操作系统的虚拟内存概念)，通过这种方式，进行输入输出要快得多。



Java中与新IO相关的包如下。

- java.nio：主要包含各种与Buffer相关的类
- java.nio.channels：主要包含与Channels和Selector相关的类
- java.nio.charset：主要包含与字符集相关的类
- java.nio.channels.spi：主要包含与Channels相关的服务提供者编程接口
- java.nio.charset.spi：包含与字符集相关的服务提供者编程接口

Channel(通道)和Buffer(缓冲)是新IO的两个核心对象，Channel是对传统输入输出系统的模拟，在新IO系统中所有数据都需要通过通道传输；Channel与传统的InputStream和OutputStream最大的区别在于它提供了一个map()方法，通过这个方法可以将”一块数据“映射到内存中。

如果说传统输入输出是面向流的处理，那么新IO就是面向块的处理。



Buffer可以被理解成容器，它本质是数组，发送到Channel中的所有对象都必须放在Buffer中，而从Channel读取的数据也必须先放到Buffer。

除了Channel和Buffer之外，新IO还提供了用于将Unicode字符串应设成字节序列以及逆映射的操作的Charset类,也提供了用于支持非阻塞式输入输出的Selector类。

##　使用Buffer



从内部结构看，Buffer就像一个数组，可以保存多个类型相同的数据，Buffer是一个抽象类，最常用的子类是ByteBuffer，他可以在底层字节数组进行get/set操作，除了ByteBuffer外，对应其他基本数据类型(boolean除外)都有对应Buffer类:CharBuffer、ShortBuffer、IntBuffer、LongBuffer、FloatBuffer和DoubleBuffer。

上面这些Buffer类，除了ByteBuffer类外，他们都采用相同或者相似的方法管理数据，这些Buffer类都没有提供构造器，通过如下一个方法来得到XxxBuffer对象

`**static XxxBuffer allocate(int capacity)**来创建一个容量为capacity的XxxBuffer对象。

实际使用较多的是ByteBuffer和CharBuffer类，其他Buffer子类较少用到，其中ByteBuffer类还有一个子类：MappedByteBuffer，它用于表示Channel将磁盘文件部分或者全部内容映射到内存中得到的结果，通常MappedByteBuffer对象由Channel得map方法返回。



在Buffer中有三个重要概念：

- 容量(capacity)：缓冲区的容量，表示该Buffer的最大数据容量，不可以为负值，创建后不能更改
- 界限(limit)：第一不应该被读出或者写入的缓冲区位置索引，即位于limit后的数据既不可以被读，也不可被写
- 位置(position)：用于指明下一个可以被读出或者写入的缓冲区位置索引(类似于IO流的记录指针)，当时用Buffer从Channel读取数据时，position的值恰好等于已经读到了多少数据。初始化后，position的值为0。

除此之外，Buffer还支持一个可选的标记mark，Buffer允许直接将position定位到该mark处。

这些值的关系为

**0≤mark≤position≤limit≤capacity**





![image-20200420150359162](D:\各种文档\个人文档\学习总结\Java\输入输出\image\image-20200420150359162.png)



如图可看出这些值的关系。

Buffer的主要作用就是装入数据，然后输出数据，开始时，**position为0，limit为capacity**，程序通过put方法像Buffer中放入数据，

Buffer中一些常用的方法：



**XxxBuffer flip()**

功能：将limit设置为position所在位置，然后设position为0

参数：无

返回值：调用该方法的Buffer对象

**注：**

该方法使得Buffer的读写指针移到开始位置，即调用flip方法后，Buffer为输出数据做好准备

---

**XxxBuffer clear()**

功能：将limit设为capacity，然后设position为0

参数：无

返回值：调用该方法的Buffer对象

**注：**

- 该方法使得Buffer的读写指针移到开始位置，即调用clear方法后，Buffer为读取数据做好准备
- **该方法不会清除Buffer中的内容，只会改变limit和position的值**

---

**int  capacity()**

功能：获得Buffer的capacity大小

参数：无

返回值：Buffer的capacity

---

**boolean hasRemaining()**

功能：判断当前位置(position)和界限(limit)之间是否还有元素可供处理

参数：无

返回值：

有元素需要处理，返回true，无返回false

---

**int limit()**

功能：返回Buffer界限的位置

参数：无

返回值：Buffer的limit

---

**Buffer limit(int newPs)**

功能：重新设置limit的位置

参数：

- newPs:新limit的位置

返回值：此Buffer对象

---

**Buffer mark()**

功能：在当前Buffer的position设置一个mark

参数：无

返回值：当前Buffer对象

**注意**

- mark只能在0到position之间
- 设置了mark，只是在position位置做一个标记，需要调用reset方法将position转到mark位置

---

**int position()**

功能：获得Buffer的position值

参数：无

返回值：Buffer的position

---

**Buffer position(int newPs)**

功能:设置Buffer的position

参数：

- newPs：需要设置的newPs位置

返回值：返回修改了position的Buffer对象

---

**int remaining()**

功能：返回当前位置和界限之间的元素个数。

参数：无

返回值：返回元素个数

---

**Buffer reset()**

功能：将position转到mark所在位置

参数：无

返回值：此Buffer对象

---

**Buffer rewind()**

功能：将位置设为0，取消设置的mark

参数：无

返回值：此Buffer对象

---



除了这些方法外，Buffer的所有子类还提供了两个重要方法，put()和get()，用于向Buffer中放入数据和从Buffer中取出数据，当时用这两个方式时，Buffer即支持对单个数据的访问，也支持对批量数据的访问(以数组作为参数)。

使用put()和get()来访问Buffer中的数据时，分为相对和绝对两种

- 相对：从position开始读取或写入数据，然后位置position按元素个数增加
- 直接根据索引向Buffer中读取或写入数据，position不受影响，值不改变

tong

下面是Buffer的常规操作。

```java
public  class  Main  {

        public  static  void  main(String[]  args) throws Exception{

                CharBuffer buff = CharBuffer.allocate(8);

                System.out.println("capacity : " + buff.capacity());

                System.out.println("limit : " + buff.limit());

                System.out.println("position : " + buff.position());

                buff.put('a');

                buff.put('b');

                buff.put('c');

                System.out.println("加入三个元素后的position：" + buff.position());

                buff.flip();

                System.out.println("执行完flip方法后 limit = " + buff.limit() + "  position = " + buff.position());

                System.out.println("取出第一个元素: " + buff.get());

                System.out.println("取出第一个元素后，position位置为" + buff.position());

                buff.clear();

                System.out.println("执行完clear方法后 limit = " + buff.limit() + "   position = " + buff.position());

                System.out.println("执行clear后，缓冲区内容并没有被清除，取出第三个元素:" + buff.get(2));

                System.out.println("执行绝对读取后，position = " + buff.position());

        }
}
```

输出结果为：

![image-20200420153133317](D:\各种文档\个人文档\学习总结\Java\输入输出\image\image-20200420153133317.png)





通过allocate()方法创建的Buffer对象是普通的Buffer，ByteBuffer还提供了一个allocateDirect方法来创建直接Buffer，直接Buffer的创建成本比普通Buffer的创建成本高，但直接Buffer的读取效率更高。

**提示**

由于直接Buffer创建成本很高，所以直接Buffer只适用于生存期长的Buffer，而不适用与短生存期、一次用完就丢弃的Buffer。

而且只有ByteBuffer提供了allocateDirect()方法，所以只能在ByteBuffer级别上创建直接Buffer，如果希望使用其他类型，则应该将该Buffer转为其他类型的Buffer。

直接Buffer和普通Buffer用法基本相同。





## 使用Channel



Channel类似于传统的流对象，但与传统流对象有两个主要区别：

- Channel可以直接将指定文件的部分或者全部映射成Buffer
- 程序不能直接访问Channel中的数据，读取、写入都不可以，必须写通过Buffer，如果要写入Channel，必须先写入Buffer，通过Buffer写入Channel，读取也是一样

Java为Channel接口提供了DatagramChannel、FileChannel、Pipe.SinkChannel、Pipe.SourceChannel、SelectableChannel、ServerSocketChannel和SocketChannel等实现类，这里主要介绍FileChannel的用法。新IO的Channel是按功能来分类的，Pipe.SinkChannel、Pipe.SourceChannel是用于支持线程通信的管道Channel，ServerSocketChannel和SocketChannel是用于网络通信的管道Channel。



注意，所有的Channel都不应该通过构造器来创建，而是通过传统节点InputStream，OutputStream的getChannel()方法来返回对应Channel，不同节点流返回的Channel类型不同。FileInputStream和FileutputStream返回的Channel是FileChannel，PipedInputStream和PipedOutputStream返回的Channel是PipeChannel。



Channel最常用的三类方法是map(),read(),write()，其中map()方法用于将Channel对应的部分或者全部数据映射成ByteBuffer，而read()和write()都有一系列重载形式。



map()的方法签名为 **MappedByteBuffer map(FileChannel.MapMode mode, long position, long size)**

第一个参数是执行映射时的模式，有三个取值

- FileChannel.MapMode.PRIVATE：私有模式(写时复制)
- FileChannel.MapMode.READ_ONLY:只读模式
- FileChannel.MapMode.READ_WRITE：读写模式

第二个参数决定映射数据的起始位置，第三个是数据的长度

例子：直接将FileChannel的全部数据映射成ByteBuffer

```java
import java.io.*;
import java.nio.*;
import java.nio.channels.*;
import java.nio.charset.*;

public class FileChannelTest
{
	public static void main(String[] args)
	{
		var f = new File("FileChannelTest.java");
		try (
			// 创建FileInputStream，以该文件输入流创建FileChannel
			var inChannel = new FileInputStream(f).getChannel();
			// 以文件输出流创建FileBuffer，用以控制输出
			var outChannel = new FileOutputStream("a.txt").getChannel())
		{
			// 将FileChannel里的全部数据映射成ByteBuffer
			MappedByteBuffer buffer = inChannel.map(FileChannel
				.MapMode.READ_ONLY, 0, f.length());   // ①
			// 使用GBK的字符集来创建解码器
			Charset charset = Charset.forName("GBK");
			// 直接将buffer里的数据全部输出
			outChannel.write(buffer);     // ②
			// 再次调用buffer的clear()方法，复原limit、position的位置
			buffer.clear();
			// 创建解码器(CharsetDecoder)对象
			CharsetDecoder decoder = charset.newDecoder();
			// 使用解码器将ByteBuffer转换成CharBuffer
			CharBuffer charBuffer = decoder.decode(buffer);
			// CharBuffer的toString方法可以获取对应的字符串
			System.out.println(charBuffer);
		}
		catch (IOException ex)
		{
			ex.printStackTrace();
		}
	}
}

```

虽然FileChannel可以读取也可以写入，但是FileInputStream创建的FileChannel只能读，FileOutputStream创建的FileChannel只能写。



RandAccessFile中也包含了一个getChannel()方法，RandAccessFile打开文件的模式决定返回的FileChannel是只读的还是读写的。

例子：对a.txt的文件内容进行复制，并追加在文件后面。

```java
import java.io.*;
import java.nio.*;
import java.nio.channels.*;

public class RandomFileChannelTest
{
	public static void main(String[] args)
		throws IOException
	{
		var f = new File("a.txt");
		try (
			// 创建一个RandomAccessFile对象
			var raf = new RandomAccessFile(f, "rw");
			// 获取RandomAccessFile对应的Channel
			FileChannel randomChannel = raf.getChannel())
		{
			// 将Channel中所有数据映射成ByteBuffer
			ByteBuffer buffer = randomChannel.map(FileChannel
				.MapMode.READ_ONLY, 0, f.length());
			// 把Channel的记录指针移动到最后
			randomChannel.position(f.length());
			// 将buffer中所有数据输出
			randomChannel.write(buffer);
		}
	}
}
```





程序也可以使用Channel和Buffer传统的”用竹筒多次重复取水“的方式，代码如下

```java
import java.io.*;
import java.nio.*;
import java.nio.channels.*;
import java.nio.charset.*;

public class ReadFile
{
	public static void main(String[] args)
		throws IOException
	{
		try (
			// 创建文件输入流
			var fis = new FileInputStream("ReadFile.java");
			// 创建一个FileChannel
			FileChannel fcin = fis.getChannel())
		{
			// 定义一个ByteBuffer对象，用于重复取水
			ByteBuffer bbuff = ByteBuffer.allocate(256);
			// 将FileChannel中数据放入ByteBuffer中
			while (fcin.read(bbuff) != -1)
			{
				// 锁定Buffer的空白区
				bbuff.flip();
				// 创建Charset对象
				Charset charset = Charset.forName("GBK");
				// 创建解码器(CharsetDecoder)对象
				CharsetDecoder decoder = charset.newDecoder();
				// 将ByteBuffer的内容转码
				CharBuffer cbuff = decoder.decode(bbuff);
				System.out.print(cbuff);
				// 将Buffer初始化，为下一次读取数据做准备
				bbuff.clear();
			}
		}
	}
}
```



## 字符集和Charset



计算机中的文件、数据、图片等等是一种表面现象，在计算机内部它们都是以二进制序列存储的，对于文本文件，我们之所以可以看到一个个的字符，是因为系统将二进制序列转换为了字符。这个过程有两个概念：编码(Encode)和解码(Decode)。通常，把明文的字符序列转换成计算机理解的二进制序列称为编码，把二进制序列转换成普通人看得懂的明文字符串为解码。

Java默认使用Unicode字符集，但很多操作系统并不是用Unicode字符集，那么当从系统中读取数据到Java程序中时，就可能出现乱码问题。

Java提供了Charset来处理字节序列和字符序列的转换关系，该类包含了创建解码器和编码器的方法，还提供了获取Charset所支持的字符集的方法，Charset类是不可变类。

常用的字符集：

- GBK：windows操作系统默认字符集，简体中文字符集
- BIG5：繁体中文字符集
- UTF-8：linux系统默认字符集，8位UCS转换格式

可以通过Chatrset的forName()方法创建对应的Charset对象，参数就是字符集的别名

如`Charset cs = Charset.forName("GBK");`

获得了Charset对象后，就可以通过该对象的newDecoder()和newEncoder()方法来获得CharsetDecoder和CharsetEncoder对象，分别是该Charset对象的解码器和编码器。这两个对象分别有decode()和encode()方法来对字节序列和字符序列解码和编码。

例子

````java
import java.nio.*;
import java.nio.charset.*;

public class CharsetTransform
{
	public static void main(String[] args)
		throws Exception
	{
		// 创建简体中文对应的Charset
		Charset cn = Charset.forName("GBK");
		// 获取cn对象对应的编码器和解码器
		CharsetEncoder cnEncoder = cn.newEncoder();
		CharsetDecoder cnDecoder = cn.newDecoder();
		// 创建一个CharBuffer对象
		CharBuffer cbuff = CharBuffer.allocate(8);
		cbuff.put('孙');
		cbuff.put('悟');
		cbuff.put('空');
		cbuff.flip();
		// 将CharBuffer中的字符序列转换成字节序列
		ByteBuffer bbuff = cnEncoder.encode(cbuff);
		// 循环访问ByteBuffer中的每个字节
		for (var i = 0; i < bbuff.capacity(); i++)
		{
			System.out.print(bbuff.get(i) + " ");
		}
		// 将ByteBuffer的数据解码成字符序列
		System.out.println("\n" + cnDecoder.decode(bbuff));
	}
}
````

上面程序分别实现了将ByteBuffer转换成CharBuffer，和CharBuffer转换成ByteBuffer的功能。

实际上，Charset类也提供了三个方法

**CharBuffer decode(ByteBuffer bb)**:将ByteBuffer中的字节序列转换成字符序列的快捷方法

**ByteBuffer encode(CharBuffer bb)**:将CharBuffer中的字符序列转换成字节序列的快捷方法

**ByteBuffer encode(String str)**:将String字符序列转换成字节序列的快捷方法

也就是说，获取Charset对象以后，如果仅仅是需要简单的编码和解码操作，不需要创建对应的解码器和编码器，直接调用Charset对象的方法即可。

在String类里也提供了一个getBytes(String charset)方法，该方法返回byte[],即使用指定字符集将字符串转换成字节序列





## 文件锁



FileLock文件锁，在操作系统中很常见，如果多个程序同时访问、修改同一个文件，但是因为文件数据不同步而出现问题，给文件加一个锁，同一时间，只有一个程序可以访问该文件，或者所有程序都可以读取此文件，但不能同时写入，这就解决了同步问题。

文件锁是进程级别的，不是线程级别的，文件锁可以解决多个进程并发访问，但不能解决多线程并发访问的问题。

这就是说，当使用文件锁时，同一进程的多个线程可以同时访问修改该文件。

文件锁是当前程序所属的JVM实例持有的，一旦获取到文件锁，要释放文件锁有三种方法

- 调用FileLock类的release方法
- 关闭对应的FileChannel对象
- 当前JVM退出

文件锁分为两类

- 排他锁：又叫做独占锁，对文件加上排他锁后，该进程可以对文件进行读写，该进程独占此文件，其他进程不能读写该文件，直到该进程释放文件锁。
- 共享锁：某个进程对文件加共享锁，其他进程也可以访问该文件，**但是这些进程都只能读此文件，不能写**，即读操作是共享的，但写操作是独占的，线程安全，其他进程不能获得该文件的排他锁，可以获得该文件的共享锁。



在FileChannel提供的lock/tryLock方法可以获得文件锁FileLock对象,lock和tryLock的区别是：当lock方法试图锁定某个文件时，如果无法得到文件锁，程序将一直阻塞；而tryLock是尝试锁定文件，它将直接返回而不是阻塞，如果获得了文件锁，则该方法返回该文件锁，否则返回null

如果要锁定文件的部分内容，使用**lock(long position, long size, boolean shared)**和**tryLock(long position, long size, boolean shared)**

所以一共有四种获得文件锁的方式

- **lock()**：对整个文件加锁，默认为排他锁
- **lock(long position, long size, boolean shared)**：自定义加锁方式，前两个指定加锁文件的起始位置和大小，第三个参数，如果为true，则是共享锁，false则为排他锁
- **tryLock()**：对整个文件加锁，默认排他锁，非阻塞式
- **tryLock(long position, long size, boolean shared)**：自定义加锁方式，参数与第二个方法一样，非阻塞式

FileLock其他常用的两个方法

- **boolean isShared()**：判断此文件锁是否为共享锁
- **boolean isValid()**：判断此文件是否还有效

**注意：**

文件锁虽然可以用于控制并发访问，但对于高并发访问的情形，还是推荐使用数据库保存程序信息



关于文件锁注意的几点

- 在某些平台上，文件锁仅仅是建议性的，并不是强制性的， 这意味着即使一个程序不能获得文件锁，也可对该文件读写
- 在某些平台上，不能同步地锁定一个文件并把它映射到内存
- 文件锁是由Java虚拟机持有，如果两个Java程序使用同一个虚拟机运行，则他们不能对同一个文件进行加锁
- 在某些平台关闭FileChannel时，会释放虚拟机在该文件上的所有锁，因此避免对同一个被锁定的文件打开多个FileChannel











