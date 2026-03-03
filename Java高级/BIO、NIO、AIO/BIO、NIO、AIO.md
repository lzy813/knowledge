- 在Java的软件设计开发中，通信架构是不可避免的，我们在进行不同系统或者不同进程之间的数据交互，或者在高并发下的通信场景下都需要用到网络通信相关的技术，对于一些经验丰富的程序员来说，Java早期的网络通信架构存在一些缺陷，<font color="red">**其中最令人恼火的是基于性能低下的同步阻塞式的I/O通信（BIO）**</font>，随着互联网开发下通信性能的高要求，Java在2002年开始支持了<font color="red">**非阻塞式的I/O通信技术(NIO)**</font>。



# 一、Java的I/O演进之路

## 1、I/O 模型基本说明

- I/O 模型：就是用什么样的通道或者说是通信模式和架构进行数据的传输和接收，很大程度上决定了程序通信的性能
- Java 共支持 3 种网络编程的/IO 模型：**BIO、NIO、AIO**



### 1.1 BIO

- <font color="red">**同步并阻塞(传统阻塞型)**</font>，服务器实现模式为一个连接一个线程，即客户端有连接请求时服务器端就需要启动一个线程进行处理，如果这个连接不做任何事情会造成不必要的线程开销 

![BIO](图片/BIO.png)



### 1.2 NIO

- <font color="red">**同步非阻塞**</font>，服务器实现模式为一个线程处理多个请求(连接)，即客户端发送的连接请求都会注册到<font color="red">**多路复用器**</font>上，多路复用器轮询到连接有 I/O 请求就进行处理 

![NIO](图片/NIO.png)



### 1.3 AIO

- Java AIO(NIO.2) ：  <font color="red">**异步非阻塞**</font>，服务器实现模式为一个有效请求一个线程，<font color="red">**客户端的I/O请求都是由OS先完成了再通知服务器应用去启动线程进行处理**</font>，一般适用于连接数较多且连接时间较长的应用



### 1.4  适用场景分析

- BIO： 方式适用于连接数目比较小且固定的架构，这种方式对服务器资源要求比较高，并发局限于应用中，JDK1.4以前的唯一选择，但程序简单易理解。
- NIO：方式适用于连接数目多且连接比较短（轻操作）的架构，比如聊天服务器，弹幕系统，服务器间通讯等。编程比较复杂，JDK1.4 开始支持。
- AIO：方式使用于连接数目多且连接比较长（重操作）的架构，比如相册服务器，充分调用 OS 参与并发操作，编程比较复杂，JDK7 开始支持。



# 二、BIO

## 1、基本介绍

- Java BIO 就是传统的 java io  编程，其相关的类和接口在 java.io
- BIO(blocking I/O) ： <font color="red">**同步阻塞**</font>，服务器实现模式为一个连接一个线程，即客户端有连接请求时服务器端就需要启动一个线程进行处理，如果这个连接不做任何事情会造成不必要的线程开销，可以通过线程池机制改善(实现多个客户连接服务器).



## 2、工作机制

![BIO工作机制](图片/BIO工作机制.png)

- 服务器端启动一个 <font color="red">**ServerSocket**</font>，注册端口，调用accpet方法监听客户端的Socket连接。
- 客户端启动 <font color="red">**Socket**</font> 对服务器进行通信，默认情况下服务器端需要对每个客户 建立一个线程与之通讯



## 3、传统的BIO编程实例回顾

- 网络编程的基本模型是Client/Server模型，也就是两个进程之间进行相互通信，其中服务端提供位置信（绑定IP地址和端口），客户端通过连接操作向服务端监听的端口地址发起连接请求，<font color="red">**基于TCP协议下进行三次握手连接**</font>，连接成功后，双方通过网络套接字（Socket）进行通信。
- 传统的同步阻塞模型开发中，服务端ServerSocket负责绑定IP地址，启动监听端口；客户端Socket负责发起连接操作。连接成功后，双方通过输入和输出流进行同步阻塞式通信。
-  基于BIO模式下的通信，<font color="red">**客户端 - 服务端是完全同步**</font>，完全耦合的。
- 客户端Client代码

~~~java
package com.example.study.bio;

import java.io.OutputStream;
import java.io.PrintStream;
import java.net.Socket;

/**
 目标: Socket网络编程。

 Java提供了一个包：java.net下的类都是用于网络通信。
 Java提供了基于套接字（端口）Socket的网络通信模式，我们基于这种模式就可以直接实现TCP通信。
 只要用Socket通信，那么就是基于TCP可靠传输通信。

 功能1：客户端发送一个消息，服务端接口一个消息，通信结束！！

 创建客户端对象：
 （1）创建一个Socket的通信管道，请求与服务端的端口连接。
 （2）从Socket管道中得到一个字节输出流。
 （3）把字节流改装成自己需要的流进行数据的发送
 创建服务端对象：
 （1）注册端口
 （2）开始等待接收客户端的连接,得到一个端到端的Socket管道
 （3）从Socket管道中得到一个字节输入流。
 （4）把字节输入流包装成自己需要的流进行数据的读取。

 Socket的使用：
 构造器：public Socket(String host, int port)
 方法：  public OutputStream getOutputStream()：获取字节输出流
 public InputStream getInputStream() :获取字节输入流

 ServerSocket的使用：
 构造器：public ServerSocket(int port)

 小结：
 通信是很严格的，对方怎么发你就怎么收，对方发多少你就只能收多少！！

 */
public class Client {
    public static void main(String[] args) {
        try {
            System.out.println("==客户端的启动==");
            // 1、创建一个Socket的通信管道，请求与服务端的端口连接。
            Socket socket = new Socket("127.0.0.1", 9999);
            // 2、从Socket通信管道中得到一个字节输出流。
            OutputStream outputStream = socket.getOutputStream();
            // 3、把字节流改装成自己需要的流进行数据的发送
            PrintStream printStream = new PrintStream(outputStream);
            // 开始发送消息
            printStream.println("我是客户端，你好");
            printStream.flush();
        } catch (Exception e) {
            e.printStackTrace();
        }
    }
}

~~~

- 服务端Server代码

~~~java
package com.example.study.bio;

import java.io.BufferedReader;
import java.io.InputStream;
import java.io.InputStreamReader;
import java.net.ServerSocket;
import java.net.Socket;

/**
 * 目标：客户端发送消息，服务段接收消息
 */
public class Server {
    public static void main(String[] args) {
        try {
            System.out.println("==服务器的启动==");
            // 1、定义一个ServerSocket对象对服务端的端口进行注册
            ServerSocket serverSocket = new ServerSocket(9999);
            // 2、监听客户端的socket连接请求
            Socket socket = serverSocket.accept();
            // 3、从socket管道中得到一个字节输入流对象
            InputStream inputStream = socket.getInputStream();
            // 4、把字节输入流包装成一个缓冲字符输入流
            BufferedReader bufferedReader = new BufferedReader(new InputStreamReader(inputStream));
            // 5、读取数据
            String line ;
            // 这里为什么不能用while，因为客户端发送只有一条，发送完代码就执行完了，所以这里就等不到后续的输入，监听失效导致报错java.net.SocketException: Connection reset（客户端和服务端是同步的，客户端爆炸结束了，服务端也会跟着完蛋）
            if((line = bufferedReader.readLine())!=null){
                System.out.println("服务端收到：" + line);
            }
        } catch (Exception e) {
            e.printStackTrace();
        }
    }
}
~~~

- 在以上通信中，服务端会一致等待客户端的消息，如果客户端没有进行消息的发送，服务端将一直进入阻塞状态。
- 同时服务端是按照行获取消息的，这意味着客户端也必须按照行进行消息的发送，否则服务端将进入等待消息的阻塞状态！



## 4、BIO模式下多发和多收消息

- 上述案例<font color="red">**只能实现客户端发送消息，服务端接收消息，并不能实现反复的收消息和反复的发消息**</font>，我们只需要在客户端案例中，加上反复按照行发送消息的逻辑即可！案例代码如下：
- 服务端Server



~~~java
package com.example.study.bio;

import java.io.BufferedReader;
import java.io.InputStream;
import java.io.InputStreamReader;
import java.net.ServerSocket;
import java.net.Socket;

/**
 * 目标：客户端发送消息，服务段接收消息
 */
public class Server {
    public static void main(String[] args) {
        try {
            System.out.println("==服务器的启动==");
            // 1、定义一个ServerSocket对象对服务端的端口进行注册
            ServerSocket serverSocket = new ServerSocket(9999);
            // 2、监听客户端的socket连接请求
            Socket socket = serverSocket.accept();
            // 3、从socket管道中得到一个字节输入流对象
            InputStream inputStream = socket.getInputStream();
            // 4、把字节输入流包装成一个缓冲字符输入流
            BufferedReader bufferedReader = new BufferedReader(new InputStreamReader(inputStream));
            // 5、读取数据
            String line ;
            while ((line = bufferedReader.readLine())!=null){
                System.out.println("服务端收到：" + line);
            }
        } catch (Exception e) {
            e.printStackTrace();
        }
    }
}
~~~

- 客户端Client

~~~java
package com.example.study.bio;

import java.io.OutputStream;
import java.io.PrintStream;
import java.net.Socket;
import java.util.Scanner;

/**
 目标: Socket网络编程。

 功能1：客户端可以反复发消息，服务端可以反复收消息

 小结：
 通信是很严格的，对方怎么发你就怎么收，对方发多少你就只能收多少！！

 */
public class Client {
    public static void main(String[] args) {
        try {
            System.out.println("==客户端的启动==");
            // 1、创建一个Socket的通信管道，请求与服务端的端口连接。
            Socket socket = new Socket("127.0.0.1", 9999);
            // 2、从Socket通信管道中得到一个字节输出流。
            OutputStream outputStream = socket.getOutputStream();
            // 3、把字节流改装成自己需要的流进行数据的发送
            PrintStream printStream = new PrintStream(outputStream);
            // 开始发送消息
            Scanner scanner = new Scanner(System.in);
            while (true) {
                System.out.println("请说：");
                String msg = scanner.nextLine();
                printStream.println(msg);
                printStream.flush();
            }
        } catch (Exception e) {
            e.printStackTrace();
        }
    }
}
~~~

- 服务端只能处理一个客户端的请求，因为服务端是单线程的。一次只能与一个客户端进行消息通信。



## 5、BIO模式下接收多个客户端

- 上述案例如果多个客户端同时连接一个服务端出现的问题
  - **服务端只会响应第一个客户端的消息，第二个客户端发送的消息并不会接收**

- 在上述的案例中，一个服务端只能接收一个客户端的通信请求，<font color="red">**那么如果服务端需要处理很多个客户端的消息通信请求应该如何处理呢**</font>，此时我们就需要在服务端引入线程了，也就是说客户端每发起一个请求，服务端就创建一个新的线程来处理这个客户端的请求，这样就实现了一个客户端一个线程的模型，图解模式如下：

![BIO](图片/BIO.png)

- 只需要修改服务端代码即可

~~~java
package com.example.study.bio;

import java.io.BufferedReader;
import java.io.InputStream;
import java.io.InputStreamReader;
import java.net.ServerSocket;
import java.net.Socket;

/**
 目标: Socket网络编程。

 功能1：客户端可以反复发，一个服务端可以接收无数个客户端的消息！！

 小结：
 服务器如果想要接收多个客户端，那么必须引入线程，一个客户端一个线程处理！！

 */
public class Server {
    public static void main(String[] args) {
        try {
            System.out.println("==服务器的启动==");
            // 1、定义一个ServerSocket对象对服务端的端口进行注册
            ServerSocket serverSocket = new ServerSocket(9999);
            // 2、定义一个死循环，开始在这里暂停等待接收客户端的连接,得到一个端到端的Socket管道
            while (true) {
                Socket socket = serverSocket.accept();
                new ServerReadThread(socket).start();
                System.out.println(socket.getRemoteSocketAddress() + "上线了！");
            }

        } catch (Exception e) {
            e.printStackTrace();
        }
    }
}

/**
 * 继承线程类，将socket传入，得到新的socket管道对不同的客户端进行通信
 */
class ServerReadThread extends Thread {
    private Socket socket;

    public ServerReadThread(Socket socket) {
        this.socket = socket;
    }

    @Override
    public void run() {
        try{
            //（3）从Socket管道中得到一个字节输入流。
            InputStream is = socket.getInputStream();
            //（4）把字节输入流包装成自己需要的流进行数据的读取。
            BufferedReader br = new BufferedReader(new InputStreamReader(is));
            //（5）读取数据
            String line ;
            while ((line = br.readLine()) != null){
                System.out.println("服务端收到：" + socket.getRemoteSocketAddress()+":"+line);
            }
        } catch (Exception e){
            System.out.println(socket.getRemoteSocketAddress() + "下线了！");
        }
    }
}
~~~

- 1.每个Socket接收到，都会创建一个线程，线程的竞争、切换上下文影响性能；
- 2.每个线程都会占用栈空间和CPU资源；
- 3.并不是每个socket都进行IO操作，无意义的线程处理；
- 4.客户端的并发访问增加时。服务端将呈现1:1的线程开销，访问量越大，系统将发生线程栈溢出，线程创建失败，最终导致进程宕机或者僵死，从而不能对外提供服务。



## 6、伪异步I/O编程

- 在上述案例中：<font color="red">**客户端的并发访问增加时，服务端将呈现1:1的线程开销，访问量越大，系统将发生线程栈溢出，线程创建失败，最终导致进程宕机或者僵死，从而不能对外提供服务**</font>
- 接下来我们采用一个伪异步I/O的通信框架，采用线程池和任务队列实现，当客户端接入时，将客户端的Socket封装成一个Task(该任务实现java.lang.Runnable线程任务接口)交给后端的线程池中进行处理。<font color="red">**JDK的线程池维护一个消息队列和N个活跃的线程，对消息队列中Socket任务进行处理，由于线程池可以设置消息队列的大小和最大线程数，因此，它的资源占用是可控的，无论多少个客户端并发访问，都不会导致资源的耗尽和宕机。**</font>

![BIO伪异步](图片/BIO伪异步.png)

- 服务端代码Server

~~~java
package com.example.study.bio;

import java.io.BufferedReader;
import java.io.InputStream;
import java.io.InputStreamReader;
import java.net.ServerSocket;
import java.net.Socket;
import java.util.concurrent.Executor;

/**
 目标: 伪异步I/O编程

 功能1：交给线程池处理

 小结：
 服务器如果想要接收多个客户端，那么必须引入线程，一个客户端一个线程处理！！

 */
public class Server {
    public static void main(String[] args) {
        try {
            System.out.println("==服务器的启动==");
            // 1、定义一个ServerSocket对象对服务端的端口进行注册
            ServerSocket serverSocket = new ServerSocket(9999);
            // 2、一个服务端只需要对应一个线程池
            HandlerSocketThreadPool handlerSocketThreadPool =
                new HandlerSocketThreadPool(3, 1000);
            // 3、定义一个死循环，开始在这里暂停等待接收客户端的连接,得到一个端到端的Socket管道
            while (true) {
                Socket socket = serverSocket.accept();   // 阻塞式的
                System.out.println("有人上线了！！");
                // 3、把socket对象包装成一个任务交给线程池
                // 每次收到一个客户端的socket请求，都需要为这个客户端分配一个
                // 独立的线程 专门负责对这个客户端的通信！！
                handlerSocketThreadPool.execute(new ReaderClientRunnable(socket));
            }

        } catch (Exception e) {
            e.printStackTrace();
        }
    }
}
~~~

- 线程池代码

~~~java
package com.example.study.bio;

import java.util.concurrent.ArrayBlockingQueue;
import java.util.concurrent.ExecutorService;
import java.util.concurrent.ThreadPoolExecutor;
import java.util.concurrent.TimeUnit;

/**
 * 线程池处理类
 */
public class HandlerSocketThreadPool {
    // 线程池
    private ExecutorService executor;

    /**
     * 声明线程池
     * @param maxPoolSize 最大线程数
     * @param queueSize 阻塞任务队列容量
     */
    public HandlerSocketThreadPool(int maxPoolSize, int queueSize) {
        this.executor = new ThreadPoolExecutor(3, maxPoolSize, 120L, TimeUnit.SECONDS,
            new ArrayBlockingQueue<Runnable>(queueSize));
    }

    public void execute(Runnable task) {
        this.executor.execute(task);
    }
}
~~~

- 将socket包装成任务对象

~~~java
package com.example.study.bio;

import java.io.BufferedReader;
import java.io.InputStream;
import java.io.InputStreamReader;
import java.io.Reader;
import java.net.Socket;

/**
 * 将socket包装成任务对象
 */
public class ReaderClientRunnable implements Runnable {
    private Socket socket;

    public ReaderClientRunnable(Socket socket) {
        this.socket = socket;
    }

    @Override
    public void run() {
        try {
            // 读取一行数据
            InputStream is = socket.getInputStream() ;
            // 转成一个缓冲字符流
            Reader fr = new InputStreamReader(is);
            BufferedReader br = new BufferedReader(fr);
            // 一行一行的读取数据
            String line = null ;
            while((line = br.readLine())!=null){ // 阻塞式的！！
                System.out.println("服务端收到了数据：" + line);
            }
        } catch (Exception e) {
            System.out.println("有人下线了");
        }

    }
}
~~~

- 客户端代码

~~~java
package com.example.study.bio;

import java.io.OutputStream;
import java.io.PrintStream;
import java.net.Socket;
import java.util.Scanner;

/**
 目标: Socket网络编程。

 功能1：客户端可以反复发消息，服务端可以反复收消息

 小结：
 通信是很严格的，对方怎么发你就怎么收，对方发多少你就只能收多少！！

 */
public class Client {
    public static void main(String[] args) {
        try {
            System.out.println("==客户端的启动==");
            // 1、创建一个Socket的通信管道，请求与服务端的端口连接。
            Socket socket = new Socket("127.0.0.1", 9999);
            // 2、从Socket通信管道中得到一个字节输出流。
            OutputStream outputStream = socket.getOutputStream();
            // 3、把字节流改装成自己需要的流进行数据的发送
            PrintStream printStream = new PrintStream(outputStream);
            // 开始发送消息
            Scanner scanner = new Scanner(System.in);
            while (true) {
                System.out.println("请说：");
                String msg = scanner.nextLine();
                printStream.println(msg);
                printStream.flush();
            }
        } catch (Exception e) {
            e.printStackTrace();
        }
    }
}
~~~

- 伪异步io采用了线程池实现，因此避免了为每个请求创建一个独立线程造成线程资源耗尽的问题，但由于底层依然是采用的同步阻塞模型，因此无法从根本上解决问题。
- 如果单个消息处理的缓慢，或者服务器线程池中的全部线程都被阻塞，那么后续socket的i/o消息都将在队列中排队。新的Socket请求将被拒绝，客户端会发生大量连接超时。
  - 比如这里线程池最大三个，当出现第四个客户端的时候，这里就会陷入阻塞，得等到前面的仨有东西下线宕机了自己才能上位



## 7、基于BIO形式下的文件上传

- 支持任意类型文件形式的上传。
- 客户端代码

~~~java
package com.example.study.bio;

import java.io.DataOutputStream;
import java.io.FileInputStream;
import java.io.InputStream;
import java.io.OutputStream;
import java.net.Socket;

/**
 目标：实现客户端上传任意类型的文件数据给服务端保存起来。

 */
public class Client {
    public static void main(String[] args) {
        try (InputStream is = new FileInputStream("D:\\knowledge\\knowledge\\Java高级\\BIO、NIO、AIO\\图片\\BIO.png")) {
            System.out.println("==客户端的启动==");
            // 1、创建一个Socket的通信管道，请求与服务端的端口连接。
            Socket socket = new Socket("127.0.0.1", 9999);
            // 2、从Socket通信管道中得到一个字节输出流。
            OutputStream outputStream = socket.getOutputStream();
            // 3、把字节输出流包装成一个数据输出流
            DataOutputStream dataOutputStream = new DataOutputStream(outputStream);
            // 4、先发送上传文件的后缀给服务端
            dataOutputStream.writeUTF(".png");
            // 5、把文件数据发送给服务端进行接收
            byte[] buffer = new byte[1024];
            int len;
            while((len = is.read(buffer)) > 0 ){
                dataOutputStream.write(buffer , 0 , len);
            }
            dataOutputStream.flush();
            socket.shutdownOutput();
        } catch (Exception e) {
            e.printStackTrace();
        }
    }
}
~~~

- 服务端代码

~~~java
package com.example.study.bio;

import java.net.ServerSocket;
import java.net.Socket;

/**
 目标：服务端开发，可以实现接收客户端的任意类型文件，并保存到服务端磁盘。
 */
public class Server {
    public static void main(String[] args) {
        try {
            System.out.println("==服务器的启动==");
            // 1、定义一个ServerSocket对象对服务端的端口进行注册
            ServerSocket serverSocket = new ServerSocket(9999);
            // 2、交给一个独立的线程来处理与这个客户端的文件通信需求。
            while (true) {
                Socket socket = serverSocket.accept();
                new ServerReaderThread(socket).start();
            }
        } catch (Exception e) {
            e.printStackTrace();
        }
    }
}
~~~

- 线程执行类代码

```java
package com.example.study.bio;

import java.io.DataInputStream;
import java.io.FileOutputStream;
import java.io.OutputStream;
import java.net.Socket;
import java.util.UUID;

/**
 * 线程执行内容
 */
class ServerReaderThread extends Thread {
    private Socket socket;
    public ServerReaderThread(Socket socket) {
        this.socket = socket;
    }

    @Override
    public void run() {
        try{
            // 1、得到一个数据输入流读取客户端发送过来的数据
            DataInputStream dis = new DataInputStream(socket.getInputStream());
            // 2、读取客户端发送过来的文件类型
            String suffix = dis.readUTF();
            System.out.println("服务端已经成功接收到了文件类型：" + suffix);
            // 3、定义一个字节输出管道负责把客户端发来的文件数据写出去
            OutputStream os = new FileOutputStream("D:\\knowledge\\knowledge\\Java高级\\BIO、NIO、AIO\\图片\\"+
                UUID.randomUUID() + suffix);
            // 4、从数据输入流中读取文件数据，写出到字节输出流中去
            byte[] buffer = new byte[1024];
            int len;
            while((len = dis.read(buffer)) > 0){
                os.write(buffer,0, len);
            }
            os.close();
            System.out.println("服务端接收文件保存成功！");
        } catch (Exception e) {
            e.printStackTrace();
        }
    }
}
```



## 8、BIO模式下的端口转发思想

- 需求：需要实现一个客户端的消息可以发送给所有的客户端去接收。（群聊实现）

![BIO端口转发思想](图片/BIO端口转发思想.png)

- 客户端代码

~~~java
package com.example.study.bio;

import java.io.PrintStream;
import java.net.Socket;
import java.util.Scanner;

/**
 目标：实现客户端的开发

 基本思路：
 1、客户端发送消息给服务端
 2、客户端可能还需要接收服务端发送过来的消息
 */
public class Client {
    public static void main(String[] args) {
        try{
            // 1、创建于服务端的Socket链接
            Socket socket = new Socket("127.0.0.1" , 9999);
            // 4、分配一个线程为客户端socket服务接收服务端发来的消息
            new ClientReaderThread(socket).start();

            // 2、从当前socket管道中得到一个字节输出流对应的打印流
            PrintStream ps = new PrintStream(socket.getOutputStream());
            // 3、接收用户输入的消息发送出去
            Scanner sc = new Scanner(System.in);
            while (true) {
                String msg = sc.nextLine();
                ps.println("波妞："+msg);
                ps.flush();
            }
        }catch (Exception e){
            e.printStackTrace();
        }
    }
}
~~~

- 客户端线程接收方法

~~~java
package com.example.study.bio;

import java.io.BufferedReader;
import java.io.InputStreamReader;
import java.net.Socket;

/**
 * 客户端的线程实现方法
 */
public class ClientReaderThread extends Thread {
    private Socket socket;
    public ClientReaderThread(Socket socket) {
        this.socket = socket;
    }

    @Override
    public void run() {
        try {
            BufferedReader br = new BufferedReader(new InputStreamReader(socket.getInputStream()));
            String msg;
            while ((msg = br.readLine())!=null){
                System.out.println(msg);
            }
        } catch (Exception e) {
            e.printStackTrace();
        }
    }

}
~~~

- 服务端代码

~~~java
package com.example.study.bio;

import java.net.ServerSocket;
import java.net.Socket;
import java.util.ArrayList;
import java.util.List;

/**
 目标：BIO模式下的端口转发思想-服务端实现。

 服务端实现的需求：
 1、注册端口
 2、接收客户端的socket连接，交给一个独立的线程来处理。
 3、把当前连接的客户端socket存入到一个所谓的在线socket集合中保存
 4、接收客户端的消息，然后推送给当前所有在线的socket接收。
 */
public class Server {
    // 定义一个静态集合
    public static List<Socket> allSocketOnline = new ArrayList<>();

    public static void main(String[] args) {
        try {
            System.out.println("==服务器的启动==");
            // 1、定义一个ServerSocket对象对服务端的端口进行注册
            ServerSocket serverSocket = new ServerSocket(9999);
            // 2、定义一个循环，获取所有的socket
            while (true) {
                Socket socket = serverSocket.accept();
                // 把登录成功的客户端socket存到线程集合里去
                allSocketOnline.add(socket);
                // 为当前登录成功的客户端socket分配一个独立的线程来处理并与之通信
                new ServerReaderThread(socket).start();
            }
        } catch (Exception e) {
            e.printStackTrace();
        }
    }
}

~~~

- 服务端的发送代码

~~~java
package com.example.study.bio;

import java.io.BufferedReader;
import java.io.InputStreamReader;
import java.io.PrintStream;
import java.net.Socket;

/**
 * 服务端的线程实现方法
 */
public class ServerReaderThread extends Thread {
    private Socket socket;
    public ServerReaderThread(Socket socket) {
        this.socket = socket;
    }

    @Override
    public void run() {
        try {
            // 1、从socket获取当前客户端的输入流
            BufferedReader br = new BufferedReader(new InputStreamReader(socket.getInputStream()));
            String msg;
            while ((msg = br.readLine()) != null) {
                // 2、服务端接收到客户端消息之后，是愮推送给当前的所有在线socket
                sendMsgToAllClient(msg);
            }
        } catch (Exception e) {
            System.out.println("当前有人下线");
            // 从在线socket集合中移除此socket
            Server.allSocketOnline.remove(socket);
        }
    }

    /**
     * 把当前客户端发来的消息推送给全部在线的socket
     * @param msg
     */
    private void sendMsgToAllClient(String msg) throws Exception {
        for (Socket sk : Server.allSocketOnline) {
            PrintStream ps = new PrintStream(sk.getOutputStream());
            ps.println(msg);
            ps.flush();
        }
    }

}
~~~



## 9、基于BIO模式下即时通信