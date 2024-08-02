# Java BIO/NIO

## BIO

bio是Java提供的面向流的输入输出api

按数据的流向可分为输入流、输出流，按处理数据的单位可分为字节流、字符流，字符流是字节流通过转换流等到的

### 输入流

#### 字节输入流：InputStream

```java
public interface Closeable extends AutoCloseable {}
public abstract class InputStream implements Closeable {}
```

##### InputStream的read方法

- `public abstract int read() throws IOException;`，读取一个字节的数据，返回int类型整数，返回值取值范围为-1~255，-1是文件读取结束的标识。使用-1作为文件结束的标识的原因是，read()方法将读取的一个字节的数据当做无符号整数处理，范围是0~255；
- `public int read(byte b[])`，读取多个字节的数据到给定的byte数组，每次读取都需要将int强转为byte；如果数组长度为0，返回0，如果读到文件尾返回-1，如果读取到数组长度个字节或中途读取到文件尾，返回已读取到字节长度。

##### InputStream子类

- `ByteArrayInputStream`：字节数组输入流
- `ObjectInputStream`：引用类型输入处理流（对象反序列化）
- `PipedInputStream`
- `StringBufferInputStream`：已过时，建议使用StringReader
- `FilterInputStream`：
  - `BufferedInputStream`：字节输入缓冲流
  - `DataInputStream`：数据类型输入流（基本类型+String）
- `SequenceInputStream`
- `FileInputStream`：文件输入流



使用字节输入流读写磁盘文件

```java
public class IOTest {
    public static void main(String[] args) throws IOException {
        File inFile = new File("");
        File outFile = new File("");
        FileInputStream fis = new FileInputStream(inFile);
        FileOutputStream fos = new FileOutputStream(outFile);
        int len;
        // 单字节读写，fis.read()返回的是读取到的字节数据
        while ((len = fis.read()) != -1) {
            fos.write(len)
        }
        // 自定义缓冲区
        byte[] buffer = new byte[1024*10];
        // fis.read(buffer)返回的是获取到的字节数
        while ((len = fis.read(buffer)) != -1) {
            fos.write(buffer, 0, len);
        }
        // 使用缓冲流，和默认缓冲区大小
        BufferedInputStream bis = new BufferedInputStream(fis, 8192);
        BufferedOutputStream bos = new BufferedOutputStream(fos);
        while ((len = bis.read()) != -1) {
            bos.write(len);
        }
    }
}
```

#### 字符输入流：Reader

```java
public abstract class Reader implements Readable, Closeable {}
public class InputStreamReader extends Reader {} // 转换流，接收字节流转换成字符流
```

##### Reader的read方法

- `abstract public int read(char cbuf[], int off, int len)`：读取多个字符到给定的字符数组，返回读取到的字符数，-1是文件结束标识；
- `public int read()`：非抽象方法，调用上面的抽象方法，读取一个字符，返回的是读取到的字符，取值范围是char类型的取值范围0x0000~0xFFFF或-1；

##### Reader的子类

- `BufferedReader`：缓冲字符流，默认缓冲区大小char[8192]
- `InputStreamReader`：转换流，可指定编码格式
  - FileReader：继承了InputStreamReader，这两个类基本没有区别，FileReader使用默认编码格式
- `BufferedReader`：流有readLine方法

使用转换流，从命令行字节输入流System.in中读取命令行字符串

```java
public class IOTest {
    public static void main(String[] args) {
        String s = null;
        Integer i = null;
        //
        InputStreamReader isr = new InputStreamReader(System.in);
        BufferedReader br = new BufferedReader(isr);
        try {
            s = br.readLine();
            System.out.println(s);
            br.close();
        } catch (IOException e) {
            e.printStackTrace();
        }

        Scanner scanner = new Scanner(System.in);
        s = scanner.nextLine();
        i = scanner.nextInt();
    }
}
```

使用字符输入流读取字符文件

```java
public class IOTest {
    public static void main(String[] args) throws IOException {
        File inFile = new File("");
        File outFile = new File("");
        FileInputStream fis = new FileInputStream(inFile);
        FileOutputStream fos = new FileOutputStream(outFile);

        InputStreamReader isr = new InputStreamReader(fis);
        // 转换流也能接收缓冲字节流
        //BufferedInputStream bis = new BufferedInputStream(fis);
        //InputStreamReader isr = new InputStreamReader(bis);
        OutputStreamWriter osw = new OutputStreamWriter(fos);

        // FileReader继承InputStreamReader，封装了转换流，方便使用
        FileReader fr = new FileReader(inFile);
        // 字符缓冲流
        BufferedReader br = new BufferedReader(fr);
        String temp = null;
        while ((temp = br.readLine()) != null) {
            System.out.println(temp);
        }
    }
}
```



### 输出流

#### 字节输出流

#### 字符输出流

### BIO实用代码示例

文件和文件夹的拷贝

```java
import java.io.*;

public class Main {

    public static void main(String[] args) throws Exception {
		// write your code here
        String s = "/Users/bithup/Desktop/test";
        String d = "/Users/bithup/Desktop/test1";
        copyDir(s, d);
    }

    public static void copyFile(String srcPath, String destPath) {
        BufferedInputStream bis = null;
        BufferedOutputStream bos = null;
        byte[] buf = new byte[1024];
        try {
            bis = new BufferedInputStream(new FileInputStream(srcPath));
            bos = new BufferedOutputStream(new FileOutputStream(destPath));
            int len;
            while ((len = bis.read(buf)) > -1) {
                bos.write(buf, 0, len);
            }
            bos.flush();
        } catch (Exception e) {
            e.printStackTrace();
        } finally {
            try {
                bis.close();
                bos.close();
            } catch (Exception e) {
                e.printStackTrace();
            }
        }
    }

    public static void copyDir(String srcPath, String destPath) throws Exception {
        File src = new File(srcPath);
        File dest = new File(destPath);
        if (!src.exists() || !src.isDirectory()) {
            throw new IllegalArgumentException("源文件不存在或不是文件夹！");
        }
        if (!dest.exists()) {
            dest.mkdir();
        }
        File[] files = src.listFiles();
        for (File f : files) {
            if (f.isFile()) {
                copyFile(srcPath+File.separator+f.getName(), destPath+File.separator+f.getName());
            } else if (f.isDirectory()) {
                copyDir(srcPath+File.separator+f.getName(), destPath+File.separator+f.getName());
            }
        }
    }
}
```



## NIO

对同步、异步、阻塞、非阻塞IO概念的理解：

- 阻塞：是指线程阻塞，调用IO方法时线程阻塞，不能执行其他代码，BIO就是阻塞IO；NIO通过事件监听和和轮询机制，使得在调用IO方法之前可以判断IO是否就绪，如果没有就绪可以执行其他逻辑，这样就实现了非阻塞IO；

- 同步：是指发送IO请求的进程主动等待请求结果，这里的等待可以是阻塞也可以是非阻塞，例如使用NIO时，虽然可以判断请求是否就绪，但还是需要一个while循环逻辑，直到请求就绪，再去获取数据，这期间可以执行其他逻辑，但宏观上得到请求结果之前是在等待的。

- 异步：是指发送请求的进程不等待请求结果，而是通过回调机制，在请求结果到来后，通知请求方，AIO就是通过回调机制实现的异步IO

### NIO（同步非阻塞IO）

#### 通道

Channel可分为两类：SelectableChannel(网络)和FileChannel(文件)

Java提供的四种channel，这四个类都是抽象类

- `FileChannel`：用于读取、写入、映射和操作文件的通道
- `DatagramChannel`：通过 UDP 读写网络中的数据通道
- `SocketChannel`：通过 TCP 读写网络中的数据
- `ServerSocketChannel`：可以监听新进来的 TCP 连接，对每一个新来进来的连接都会创建一个 SocketChannel

获取通道的方式：通过FileIputStream、FileOutputStream、RandomAccessFile、DatagramSocket、Socket、ServerSocket类的getChannel()方法获取；Files类的newByteChannel()获取；上述四种channel类中的静态方法open()获取；

#### 缓冲区

Java为七种基本数据类型（除Boolean）都提供对应的缓冲区类，都继承了Buffer类

##### ByteBuffer

```java
public abstract class ByteBuffer extends Buffer implements Comparable<ByteBuffer> {
    final byte[] hb; 
    final int offset;
    boolean isReadOnly;
    // 分配非直接缓冲区（堆缓冲区）
    // class HeapByteBuffer extends ByteBuffer
    public static ByteBuffer allocate(int capacity) {
        if (capacity < 0)
            throw new IllegalArgumentException();
        return new HeapByteBuffer(capacity, capacity);
    }
    // 分配直接缓冲区
    public static ByteBuffer allocateDirect(int capacity) {
        return new DirectByteBuffer(capacity);
    }
}
```

Buffer中的概念

- 容量（capacity）：缓冲区容量，不能为负，不可修改
- 限制（limit）：第一个不可读取的数据的索引
- 位置（position）：下一个要读取或写入的数据的索引
- 标记（mark）：标记一个索引
- 重置（reset）：恢复到之前标记的索引

Buffer读写数据的方法，给定index的读写不会改变position位置

- `get()`：
- `get(byte[] dst)`：
- `get(int index)`：
- `put(byte b)`：
- `put(byte b)`：
- `put(int index, byte b)`：

Buffer其他操作

- allocate：申请分配缓冲区
- flip方法：反转读写模式
- rewind方法：将position设回0
- clear/compact方法：清空所有/已读数据

直接缓冲区和非直接缓冲区[(21条消息) bio的缓冲区和nio的缓冲区的区别 - CSDN](https://www.csdn.net/tags/MtTaUg3sOTA3MDItYmxvZwO0O0OO0O0O.html)

#### Selector

调用Selector.open()方法等到一个selector，调用channel的register方法注册channel到selector，并返回一个selectionKey

#### SelectionKey

四种事件状态标识和判断事件是否就绪的方法

```java
public static final int OP_READ = 1 << 0;
public static final int OP_WRITE = 1 << 2;
public static final int OP_CONNECT = 1 << 3;
public static final int OP_ACCEPT = 1 << 4;
public final boolean isReadable() { return (readyOps() & OP_READ) != 0;}
public final boolean isWritable() { return (readyOps() & OP_WRITE) != 0;}
public final boolean isConnectable() { return (readyOps() & OP_CONNECT) != 0;}
public final boolean isAcceptable() { return (readyOps() & OP_ACCEPT) != 0;}
```



#### NIO磁盘文件读写

```java
import java.io.*;
import java.nio.MappedByteBuffer;
import java.nio.channels.FileChannel;
import java.nio.charset.Charset;
 
public class Test {
    public static void main(String[] args) {
        File file = new File("D:\\Test04.java");
        try(
                FileInputStream fileInputStream = new FileInputStream(file);
                FileOutputStream fileOutputStream = new FileOutputStream("D:\\Test04.txt");
                FileChannel inChannel =fileInputStream.getChannel();
                FileChannel outChannel = fileOutputStream.getChannel();
                ) {
            MappedByteBuffer buffer = inChannel.map(FileChannel.MapMode.READ_ONLY, 0, file.length());
            //写出操作
            outChannel.write(buffer);
            //翻转，进行读取操作
            buffer.flip();
            //解码
            Charset charset =Charset.defaultCharset();
            System.out.println(charset.decode(buffer));
        } catch (FileNotFoundException e) {
            e.printStackTrace();
        } catch (IOException e) {
            e.printStackTrace();
        }
 
    }
}
```



#### NIO网络数据读写



### AIO（异步非阻塞IO）

nio不支持文件的异步读写，nio只能算使用多路复用技术的非阻塞IO，jdk1.7升级NIO，提供AIO库支持文件异步操作，AIO才是基于事件驱动的异步IO；aio是对nio的补充，提供Asynchronous相关类；Java 异步 IO 提供了两种使用方式，分别是**返回 Future 实例**和使用**回调函数**java.nio.channels.CompletionHandler。

[深入理解Java AIO（一）—— Java AIO的简单使用 - 肥宅快乐码 - 博客园 (cnblogs.com)](https://www.cnblogs.com/fatmanhappycode/p/12388683.html)



## Java Socket编程

### BIO Socket编程

- ServerSocket：创建实例监听某个端口，调用accept接收数据。
- Socket：使用IP和端口创建实例，使用getInputStream/getOutPutStream获取和发送数据。
- Socket发送和读取字符数据需要使用转换流。
- NIO中与ServerSocket和Socket对应的有SocketChannel和ServerSocketChannel

```java
package com.jettech;
import java.io.FileWriter;
import java.io.OutputStreamWriter;
import java.net.Socket;
public class Main {

    public static void main(String[] args) throws Exception {
	// write your code here
        Socket s = new Socket("127.0.0.1", 8080);
        OutputStreamWriter op = new OutputStreamWriter(s.getOutputStream());
        op.write("QUERY TIME ORDER");
        op.close();
        s.close();
    }
}
```



### NIO Socket编程

### AIO Socket编程





## 其他

### Linux的IO模型

- 阻塞：线程一直等待接收数据
- 非阻塞：调用recvfrom，无数据就返回EWOULDBLOCK，反复如此
- I/O复用：select/poll，select顺序扫描f d是否就绪。基于事件驱动的epoll更好。
- 信号驱动：通过系统调用sigaction，立即返回，有数据时，系统为该进程生成SIGIO信号。
- 异步I/O：告知内核读取数据，并在读取完成后通知进程。与信号驱动的区别是，信号驱动是在数据可读取时通知，异步时在数据读取完成（数据从内核复制到进程缓冲区）后通知。
- select和epoll的比较：1.epoll支持打开更多的socket描述符，2.在打开了很多socket，但只有少部分活跃的情况下，e poll效率更高，epoll中活跃的socket会主动调用callback函数。



### BIO和NIO的优缺点

BIO流式API的优点：方便添加过滤器，将一个流转换为另一个流，比较优雅。

NIO在网络高并发场景下才能发挥优势，在普通文件IO场景下对BIO没有优势

---------

IOException和IOError的区别？

大端字节模式，数据的高位存储在低地址处，CPU从低地址向高地址顺序读取时，先读到数据的高位。小端字节模式反之。



读取字符文件，也可使用字节流读取字节数组，然后使用String或StringCoding类进行解码。5、字节缓冲流BufferedInputStream/BufferedOutputStream继承了FilterInputStream/FilterOutputStream，字符缓冲流BufferedRead/BufferedWriter没有继承FilterReader/FilterWriter。

字节缓冲流BufferedInputStream/BufferedOutputStream为什么继承FilterInputStream/FilterOutputStream？