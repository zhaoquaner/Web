# Java的IO流

在Java中把不同的输入输出源(键盘，文件，网络连接等)抽象表述为”流“，通过流的方式允许Java程序使用相同方式访问不同的输入/输出源。stream是从起源到接收的有序数据。

## 流的分类



按照不同分类标准，可以将流分成不同类型。

1. 按照流的流向来分，分为输入流和输出流

    1. 输入流：只能从中读取数据，不能写入数据
    2. 输出流：只能从中写入数据，不能读取数据

    **注**：

    此处输入输出是相对内存来说的，数据从硬盘或其他设备到内存是输入，从内存到其他设备是输出。

    

    Java的输入流主要由InputStream和Reader作为基类，输出流由OutputStream和Writer作为基类。它们都是抽象基类，无法直接创建实例。

    

    

2. 按照流的操作数据单元不同，分为字符流和字节流

    1. 字节流：操作的数据单元是8位的字节
    2. 字符流：操作的数据单元是16位的字符

    除了操作数据单元不同，字符流和字节流的用法几乎一样
    字节流主要由InputStream和OutputStream作为基类，而字符流则主要由Reader和Writer作为基类。

    

3. 按照流的角色来分，分为节点流和处理流

    1. 节点流：从一个特定的IO设备(如磁盘、网络)读/写数据的流，称为节点流，也被称为低级流。
    2. 处理流：对一个已存在的流就行连接或封装，通过封装后的流来实现数据读/写功能。处理流被称为高级流。

    注：

    当时用处理流进行输入输出时，程序不会直接连接到实际的数据源，没有和实际的输入输出节点相连接，使用处理流的一个好处就是，只要使用相同的处理流，程序就可以采用完全相同的输入输出代码来访问不同数据源。



## 流的概念模型



Java把所有设备的有序数据抽象成流模型，简化了输入输出处理。

Java的IO流共涉及40多个类，非常规则，而且彼此之间存在紧密联系，Java的IO流的40多个类都是从如下的4个基类派生出来的：

- InputStream/Reader：所有输入流的基类，前者是字节输入流，后者字符输入流
- OutputStream/Writer：所有输出流基类，前者字节输出流，后者字符输出流


其中，处理流的功能主要体现在两个方面：

- 性能的提高：主要以增加缓冲的方式提高输入/输出效率。
- 操作的便捷：处理流提高了一系列便捷的方式来一次输入/输出大批量的内容。

处理流可以”嫁接“在任何已经存在的流的的基础上，这允许Java采用相同代码来访问不同输入/输出设备的数据流。



## 字节流和字符流


### InputStream和Reader

这是所有输入流的抽象基类，它们本身并不能创建实例来执行，但是它们是所有输入流的模板，包含的方法是所有输入流都可使用的方法。



InputStream里包含三个方法：



**int read()**

功能：从输入流读取单个字节。

参数：无

返回值：返回读取的字节数据(字节数据可直接转换为int类型)

---

**int read(byte[] b)**

功能：从输入流读取b.length个字节的数据，并储存在数组b中。

参数：byte数组

返回值：返回实际读取的字节数

---

**int read(byte[] b, int off, int len)**

功能：从输入流读取len个字节的数据，存储在数组b中，不是从数组起点开始，而是从off位置开始。

参数：

| 参数     | 说明                        |
| -------- | --------------------------- |
| byte[] b | 将读取的数据存入数组b中     |
| int off  | 从数组的off位置开始存       |
| int len  | 从输入流读取len个字节的数据 |

返回值：返回实际读取的字节数

---



Reader里也包含三个方法



**int read()**

功能：从输入流读取单个字符。

参数：无

返回值：返回读取的字符数据(字节数据可直接转换为int类型)

---

**int read(char[] cbuf)**

功能：从输入流读取cbuf.length个字符的数据，并储存在数组cbuf中。

参数：char数组

返回值：返回实际读取的字符数

---

**int read(char[] cbuf int off, int len)**

功能：从输入流读取len个字符的数据，存储在数组cbuf中，不是从数组起点开始，而是从off位置开始。

参数：

| 参数      | 说明                        |
| --------- | --------------------------- |
| char cbuf | 将读取的数据存入数组cbuf中  |
| int off   | 从数组的off位置开始存       |
| int len   | 从输入流读取len个字符的数据 |

返回值：返回实际读取的字符数



可以发现，两个基类的功能基本一样，只是一个是字节，一个是字符

当read方法返回-1时，表明到了输入流的结束点



它们两个基类分别有用于读取文件的输入流：FileInputStream和FileReader,他们都是节点流——与指定文件关联。

例子：

```java
import java.io.*;

public class FileInputStreamTest
{
	public static void main(String[] args) throws IOException
	{
		// 创建字节输入流
		var fis = new FileInputStream("FileInputStreamTest.java");
		// 创建一个长度为1024的“竹筒”
		var bbuf = new byte[1024];
		// 用于保存实际读取的字节数
		var hasRead = 0;
		// 使用循环来重复“取水”过程
		while ((hasRead = fis.read(bbuf)) > 0)
		{
			// 取出“竹筒”中水滴（字节），将字节数组转换成字符串输入！
			System.out.print(new String(bbuf, 0, hasRead));
		}
		// 关闭文件输入流，放在finally块里更安全
		fis.close();
	}
}
```

**注意**：

上面创建了一个1024字节的数组，但源文件不到1024个字节，只需要执行一次read即可，但如果创建较小长度的字节数组，程序运行时再输出中文注释时可能会出现乱码——因为Windows保存文件默认用GBK编码方法，这种方式下，每个中文字符占两个字节，如果只读到了半个中文字符，会出现乱码。

程序可以使用close()方法来关闭输入流，与JDBC编程一样，程序里打开的文件IO资源不属于内存资源，垃圾回收机制无法回收，应该显式关闭文件IO资源,IO资源类都实现了AutoCloseable接口，可以通过自动关闭资源的try语句来关闭IO流。



InoutStream和Reader还支持如下几个方法移动记录指针：



**void mark(int readAheadLimit)**

功能：在记录指针当前位置记录一个标记

参数：

readAheadLimit: 在标记位置变为无效之前可以读取的最大字节数限制。

返回值:无

---

**boolean markSupported()**

功能：判断此输入流是否支持mark操作，即是否支持记录标记。

参数：无

返回值：

支持返回true，否则false

---

**void reset()**

功能：将该流的记录指针重新定位到上一次记录标记的mark位置

参数：无

返回值：无

---

**long skip(long n)**

功能：从记录指针开始，跳过n个字节/字符

参数：需要跳过的字符/字节数

返回值：实际跳过的字符/字节数

---





**重要**：**Windows默认使用GBK编码保存文件，在GBK编码中，一个中文字符占两个字节，中文标点两个字节，英文字母和标				点占一个字节。且在文本文件中，每行末尾有\r\n，各占一个字节。**
				**Linux系统默认使用UTF-8编码，在UTF-8编码中，一个中文字符和标点占三个字节，英文字母和标点占一个字节。
				IDEA中默认使用UTF-8编码**





### OutputStream和Writer

这两个类也非常相似，两个流都提供了三个方法。



**void write(int c)**

功能：将字节/字符输出到输出流。

参数：c可以代表字节，也可以代表字符。

返回值：无

---

**void write(byte[]/char[] buf)**

功能：将字节数组/字符数组中的数据输出到指定输出流。

参数：要输出的字节数组/字符数组的数据

返回值：无

---

**void write(byte[]/char[] buf, int off, int len)**

功能：将字节数组/字符数组中从off位置开始，长度为len的数据输出到指定输出流。

参数：

- byte[]/char[]:要输出的字符数组/字节数组
- off：开始输出的数组位置
- len：要输出的字符/字节长度

返回值：无

---



因为字符流以字符为基本操作单位，所以Writer可以用字符串来代替字符数组，即以String对象为参数，Writer里还包含两个方法：



**void write(String str)**

功能：将字符串中的数据输出到指定输出流。

参数：要输出的字符串

返回值：无

---

**void write(String src, int off, int len)**

功能：将字符串中从off位置开始，长度为len的数据输出到指定输出流。

参数：

- src：要输出的字符串
- off：开始输出的数组位置
- len：要输出的字符/字节长度

返回值：无

---

例子：

```java
import java.io.*;

public class FileOutputStreamTest
{
	public static void main(String[] args)
	{
		try (
			// 创建字节输入流
			var fis = new FileInputStream("FileOutputStreamTest.java");
			// 创建字节输出流
			var fos = new FileOutputStream("newFile.txt"))
		{
			var bbuf = new byte[32];
			var hasRead = 0;
			// 循环从输入流中取出数据
			while ((hasRead = fis.read(bbuf)) > 0)
			{
				// 每读取一次，即写入文件输出流，读了多少，就写多少。
				fos.write(bbuf, 0, hasRead);
			}
		}
		catch (IOException ioe)
		{
			ioe.printStackTrace();
		}
	}
}
```





