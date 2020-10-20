# NIO.2

Java 7对原有NIO进行重大改进，主要包含两方面内容

- 提供了全面的文件IO和文件系统访问支持
- 基于异步Channel的第二个改进



## Path、Paths和Files核心API



NIO.2引入了一个Path接口，Path接口代表一个平台无关的平台路径，除此，NIO.2还提供了Files、Paths两个工具类，其中Files包含大量静态方法来操作文件，Paths提供了两个方法返回Path。

Path接口的常用方法



**Path getFileName()**

功能：返回此路径表示的文件或者目录名称作为Path对象返回，文件名是离根目录最远的元素

参数：无

返回值：Path对象

例子

```java
            Path path = Paths.get("D:/a/b/c");
            
            Path path1 = Paths.get("D:/a/b/c/1.txt");

            Path pathName1 = path.getFileName();
            
            Path pathName2 = path1.getFileName();

            System.out.println(pathName1 + "    " + pathName2);
```

输出

![image-20200421110250155](C:\Users\Bu'l'l'shi't\AppData\Roaming\Typora\typora-user-images\image-20200421110250155.png)

---

**Path getName(int index)**

功能：以Path对象的形式返回此路径的名称元素

参数：

- index：以离根目录最近的元素名称为第一个，索引为0，以此类推

返回值：以Path对象返回的对应元素名称

例子

```java
            Path path = Paths.get("D:/a/b/c");

            Path path1 = Paths.get("D:/a/b/c/1.txt");

            System.out.println(path.getName(1));

            System.out.println(path1.getName(2));
```

输出

![image-20200421110557201](C:\Users\Bu'l'l'shi't\AppData\Roaming\Typora\typora-user-images\image-20200421110557201.png)

---

**int getNameCount()**

功能：返回路径中的名称元素数

参数：无

返回值：名称元素数，除去根目录

例子

```
            Path path = Paths.get("D:/a/b/c");

            Path path1 = Paths.get("D:/a/b/c/1.txt");

            System.out.println(path.getNameCount());

            System.out.println(path1.getNameCount());
```

输出

<img src="C:\Users\Bu'l'l'shi't\AppData\Roaming\Typora\typora-user-images\image-20200421110747591.png" alt="image-20200421110747591" style="zoom:150%;" />

---

**Path getParent()**

功能：获得次路径的父路径

参数：无

返回值：以Path对象形式返回父路径，如果没有父路径，返回null

**注意：**如果是以绝对路径创建Path对象，返回的也是绝对路径，相对路径一样。

例子

```java
            Path path = Paths.get("D:/a/b/c");

            Path path1 = Paths.get("D:/a/b/c/1.txt");

            System.out.println(path.getParent());

            System.out.println(path1.getParent());
```

输出

![image-20200421111101001](C:\Users\Bu'l'l'shi't\AppData\Roaming\Typora\typora-user-images\image-20200421111101001.png)

---

**Path getRoot()**

功能：获得此路径的根路径

参数：无

返回值：此路径的根路径作为Path对象返回，若没有则返回null

例子

```java
            Path path = Paths.get("D:/a/b/c");
            Path path1 = Paths.get("D:/a/b/c/1.txt");
            Path path2 = Paths.get(".");
            System.out.println(path.getRoot());
            System.out.println(path1.getRoot());
            System.out.println(path2.getRoot());
```

输出

![image-20200421111320483](C:\Users\Bu'l'l'shi't\AppData\Roaming\Typora\typora-user-images\image-20200421111320483.png)

----

**boolean isAbsolute()**

功能：判断次路径是否为绝对路径

参数：无

返回值：若是绝对路径返回true，否则false

---

**Path toAbsolutePath()**

功能：获得此路径的绝对路径的Path对象

参数：无

返回值：绝对路径的Path对象

**注意：**如果此路径已经是绝对路径，则此方法就返回此路径

---





Files工具类同样提供了一系列操作文件的方法，包括创建目录，文件，删除目录文件，复制文件，输入输出文件等等

鉴于方法太多，就不写这了，

链接———>[Files工具类](https://www.apiref.com/java11-zh/java.base/java/nio/file/Files.html#copy(java.nio.file.Path,java.nio.file.Path,java.nio.file.CopyOption...))



## 使用FileVisitor遍历文件和目录



Files工具类提供了两个方法来遍历文件和子目录

**Path walkFileTree(Path start, FileVisitor<? super Path> visitor)**

功能：遍历start路径下的所有文件和子目录

参数：

- start：需要遍历的根目录
- visitor：文件访问器

返回值：返回起始文件，即start路径对应Path对象

----

**Path walkFileTree(Path start, Set<FileVisitOption> options, int maxDepth, FileVisitor<? super Path> visitor)**

功能：遍历start路径下所有文件和子目录

参数：

- start：需要遍历的起始目录
- options：遍历选项
- maxDepth：遍历深度
- visitor：文件访问器

返回值：返回起始文件，即start路径对应Path对象

---

其中两个方法中都有FileVisitor<? super Path>，文件访问器，在遍历文件和子目录时都会触发FileVisitor中相应的方法

FileVisitor定义了四个方法

- FileVisitResult postVisitDirectory(T dir, IOException exc)：访问子目录**之后**触发该方法
- FileVisitResult preVisitDirectory(T dir, BasicFileAttributes attrs)：访问子目录**之前**触发该方法
- FileVisitResult visitFile(T file, BasicFileAttributes attrs)：访问file文件时触发该方法
- FileVisitResult preVisitDirectory(T file, IOException exc)：访问file文件失败后触发该方法

上面四个方法都返回一个FileVisitResult对象，它是一个枚举类，代表了访问之后的后续行为

- CONTINUE：继续访问
- SKIP_SIBLINGS：继续访问，但不访问该文件或目录的兄弟文件或者目录
- SKIP_SUBTREE：继续访问，但不访问该文件或目录的子目录树
- TERMINATE：终止访问





其中第二个方法中，有一个Set<FileVisitOption>   options参数，用来定义文件树遍历选项，它是一个枚举类，只有一个常量：FOLLOW_LINKS，跟随符号链接。

符号链接：在windows上就是快捷方式，包含一条以绝对路径或相对路径的形式指向其他文件或者目录，对符号链接文件进行读写会表现为直接对目标文件进行操作，但是如果删除一个符号链接，它指向的目标文件不受影响。

还有一个参数maxDepth，就是遍历的深度

这两个方法以深度优先遍历文件，故设置了遍历深度，程序遍历到指定深度就会回退



实际编程中不需要为FileVisitor的四个方法都提供实现，可以通过继承SimpleFileVisitor(FileVisitor的实现类)来实现自己的文件访问器，这样就可以根据需要、选择性重写指定方法。

例子

```java
import java.io.*;
import java.nio.file.*;
import java.nio.file.attribute.BasicFileAttributes;

public  class  Main  {


        public  static  void  main(String[]  args) throws Exception{

            //遍历D盘a目录下的所有文件和子目录
            Path path = Path.of("D:/a");

            Path path1 = Files.walkFileTree(path,
                    new SimpleFileVisitor<Path>()
                    {
                        //访问子目录之前触发该方法
                        @Override
                        public FileVisitResult preVisitDirectory(Path dir, BasicFileAttributes attrs) throws IOException {
                            System.out.println("正在访问" + dir + "路径");

                            return super.preVisitDirectory(dir, attrs);
                        }

                        //访问文件之前触发该方法
                        @Override
                        public FileVisitResult visitFile(Path file, BasicFileAttributes attrs) throws IOException {

                            System.out.println("正在访问"  + file + "文件");

                            return super.visitFile(file, attrs);


                        }
                    });

        }
}
```

输出

![image-20200421121054150](C:\Users\Bu'l'l'shi't\AppData\Roaming\Typora\typora-user-images\image-20200421121054150.png)







## 使用WatchService监控文件变化



以前Java版本中，如果程序需要监听文件变化，可以考虑启动一条后台线程，这条线程每隔一段时间去”遍历“一次指定目录的文件，如果发现遍历结果与上次结果不同，就会认为文件发生变化，但这种方式很烦碎，性能也不好。

NIO.2提供了一个方法来监听文件系统的变化

register(WatchService watcher, WatchEvent<?> ... events):用watcher监听该path代表的目录下的文件变化，

events指定要监听哪些类型的事件。

在这个方法中，WatchService代表一个文件系统监听服务，它负责监听path代表的目录下的文件变化，一旦使用register方法完成注册后，就可以调用WatchService的三个方法来获取被监听的目录的文件变化事件

- WatchKey poll()：获取WatchKey，如果没有WatchKey发生就立即返回null
- WatchKey poll(long timeout, TimeUnit unit)：尝试等待timeout时间就获取下一个WatchKey
- WatchKey take()：获取下一个WatchKey，如果没有WatchKey发生就一直等待

如果程序需要一直监控。则应该选择使用take()方法

例子：示范使用WatchService监控D盘a目录下文件的变化

```java
import java.io.*;
import java.nio.file.*;
import java.nio.file.attribute.*;

public class WatchServiceTest
{
	public static void main(String[] args)
		throws Exception
	{
		// 获取文件系统的WatchService对象
		WatchService watchService = FileSystems.getDefault()
			.newWatchService();
		// 为C:盘根路径注册监听
		Paths.get("C:/").register(watchService,
			StandardWatchEventKinds.ENTRY_CREATE,
			StandardWatchEventKinds.ENTRY_MODIFY,
			StandardWatchEventKinds.ENTRY_DELETE);
		while (true)
		{
			// 获取下一个文件改动事件
			WatchKey key = watchService.take();    // ①
			for (WatchEvent<?> event : key.pollEvents())
			{
				System.out.println(event.context() +" 文件发生了 "
					+ event.kind()+ "事件！");
			}
			// 重设WatchKey
			boolean valid = key.reset();
			// 如果重设失败，退出监听
			if (!valid)
			{
				break;
			}
		}
	}
}

```

![image-20200421133721588](D:\各种文档\个人文档\学习总结\Java\输入输出\image\image-20200421133721588.png)





## 访问文件属性



Java 7的NIO.2提供了大量工具类来简单读取修改文件属性，这些工具类分为两大类

- XxxAttributeView：代表某种文件属性的视图
- XxxAttributes:代表某种文件属性的"集合"

程序一般通过XxxAttributeView对象来获取XxxAttributes

在这些工具类中，FileAttributeView其他XxxAttributeView的父接口，有以下几个XxxAttributeView

- AclFileAttributeView：通过AclFileAttributeView，开发者可以为特定文件设置ACL(Access Control List)及文件所有者属性，它的getAcl()方法返回List<AclEntry>对象，该返回值代表了该文件的权限集，通过setAcl(List)方法可以修改该文件的ACL.
- BasicFileAttributeView：它可以获取或修改文件的基本属性，包括文件最后修改时间，最后访问时间，创建时间，大小等等，他的readArrtibutes()方法返回一个BasicFileAttributes对象，对文件夹基本属性的修改是通过BasicFileAttributes对象完成的
- DosFileAttributeView：它主要获取或修改文件的DOS相关属性，比如文件是否可读，是否隐藏，是否为系统文件，是否为存档文件等，他的readAttributes()方法获取一个DosFileAttributes对象，对这些属性的修改其实是由DosFileAttributes对象来完成的
- FileOwnerAttributeView：它主要用于获取或修改文件的所有者，他的getOwner()方法返回一个UsePrincipal对象来代表文件所有者，也可以调用setOwner(UsePrincipal owner)方法来改变文件所有者
- PosixFileAttributeView：它主要用于获取或修改POSIX(Porable Operating System InterFace of INIX)属性，它的readArrtibutes()方法返回一个PosixFileAttributes对象，该对象可用于获取或修改文件的所有者，组所有者，访问权限信息，这个View只在UNIX,Linux系统上有用
- UserDefinedFileAttributeView：它可以让开发者为文件设置一些自定义属性



例子

```java
package Hello;

import java.io.*;
import java.nio.file.*;
import java.nio.file.attribute.BasicFileAttributeView;
import java.nio.file.attribute.BasicFileAttributes;
import java.nio.file.attribute.FileOwnerAttributeView;
import java.util.Date;

public  class  Main  {


        public  static  void  main(String[]  args) throws Exception{

            Path testpath = Paths.get("D:\\a\\b\\c\\1.txt");

            BasicFileAttributeView basicView = Files.getFileAttributeView(testpath, BasicFileAttributeView.class);

            BasicFileAttributes basicAttributes = basicView.readAttributes();

            System.out.println("创建时间为" + new Date(basicAttributes.creationTime().toMillis()));

            System.out.println("最后访问时间为" + new Date(basicAttributes.lastAccessTime().toMillis()));

            System.out.println("最后修改时间为" + new Date(basicAttributes.lastModifiedTime().toMillis()));

            System.out.println("文件大小为" + basicAttributes.size() + "个字节");

            FileOwnerAttributeView ownerView = Files.getFileAttributeView(testpath, FileOwnerAttributeView.class);

            System.out.println("该文件所属用户" + ownerView.getOwner());

        }
}
```

输出

![image-20200421140431872](D:\各种文档\个人文档\学习总结\Java\输入输出\image\image-20200421140431872.png)



