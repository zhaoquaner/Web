# RandomAccessFile

RandomAccessFile是Java输入/输出体系功能最丰富的文件内容访问类，它既可以读取文件内容，也可以写入文件内容，与普通输入/输出类不同的是，它支持“任意访问”，即程序可以从文件的任何位置开始写入或者读取。

RandomAccessFile允许自由定位记录指针，如果程序需要向已经存在的文件追加内容，则需要使用RandomAccessFile类。

当新创建一个RandomAccessFile对象时，文件记录指针位于开头（也就是0处），RandomAccessFile包含了两个方法来操作文件记录指针：



**long getFilePointer()**

功能：得到文件记录指针目前的位置

参数：无

返回值：返回记录指针位置

---

**void seek(long pos)**

功能：将文件指针定位到pos位置

参数：

- pos:想要定位的文件位置

返回值：无

---

RandomAccessFile既可以读取文件内容也可以写入文件，所有它也包含了InputStream的三个read()方法，和OutputStream的三个write()方法，用法和原来一样。

除此之外，RandomAccessFile还包含一系列readXxx()方法和writeXxx()方法来完成输入输出。





RandomAccessFile本身有两个构造器

**RandomAccessFile( File file, String mode)**和**RandomAccessFile( String name, String mode)**

前一个使用String参数来指定文件名，后一个使用File来指定文件本身。

还有一个mode参数，指定文件以什么形式打开，有四个值：

- "r" :以只读方式打开文件，如果执行写入，会出现异常
- "rw"：以读写方式打开文件，如果文件不存在，则会创建该文件
- "rws"：以读写方式打开文件，相对于"rw"模式，还要求对文件内容或元数据的每个更新都同步写入到底层存储设备。
- "rwd"：以读写方式打开文件，相对于"rw"模式，还要求对文件内容的每个更新都同步写入到底层存储设备。


例子：使用RandomAccessFile来访问指定的中间部分数据

```java
import java.io.*;

public  class  hello  {


        public  static  void  main(String  args[])  throws IOException
        {
                try(RandomAccessFile raf = new RandomAccessFile("poem.txt", "r"))
                {
                        System.out.println(raf.getFilePointer());

                        raf.seek(7);

                        byte[] bbuf = new byte[1024];

                        int hasread = 0;

                        while((hasread = raf.read(bbuf)) > 0 )
                        {
                                System.out.println(new String(bbuf, 0, hasread));
                        }

                }
                catch (IOException e)
                {
                        e.printStackTrace();
                }

        }
}
```



例子：向指定文件后追加内容：

```java
import java.io.*;

public  class  hello  {


        public  static  void  main(String  args[])  throws IOException
        {
                try(RandomAccessFile raf = new RandomAccessFile("poem.txt", "rw"))
                {
                        raf.seek(raf.length());

                        raf.write("我爱唐诗！\r\n".getBytes());

                }
                catch (IOException e)
                {
                        e.printStackTrace();
                }

        }

}

```



**注意**

RandomAccessFile依然不能向文件指定位置插入内容，如果直接将文件指针移动到中间某位置然后输出，新输出的内容会覆盖文件指针后面的内容，如果要插入，应该先把插入点后面的内容读入缓冲区，然后插入数据，最后把缓冲区内容追加到文件后面。

例子：实现了向指定文件、指定位置插入内容的功能：

```java
package Hello;

import java.io.*;

public  class  hello  {

        public static void insert(String filename, long pos, String insertContent)throws IOException
        {
                File tmp = File.createTempFile("tmp", null);

                tmp.deleteOnExit();

                try(RandomAccessFile raf = new RandomAccessFile("poem.txt", "rw");
                    FileInputStream tmpIn = new FileInputStream(tmp);
                    FileOutputStream tmpOut = new FileOutputStream(tmp))
                {

                        raf.seek(pos);

                        byte[] bbuf = new byte[64];

                        int hasread = 0;

                        while((hasread = raf.read(bbuf)) > 0)
                        {
                                tmpOut.write(bbuf, 0, hasread);
                        }

                        raf.seek(pos);

                        raf.write(insertContent.getBytes());

                        while((hasread = tmpIn.read(bbuf)) > 0)
                        {
                                raf.write(bbuf, 0, hasread);
                        }


                }

        }


        public  static  void  main(String  args[])  throws IOException
        {
                insert("poem.txt", 8, "插入的内容\r\n");

        }




}

```



