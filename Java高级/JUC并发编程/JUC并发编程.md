# 一、进程与线程

## 1、进程和线程

### 1.1 进程

- **程序是由指令和数据组成，但这些指令要运行，数据要读写，就必须将指令加载至CPU，数据加载至内存**。在指令运行过程中还需要用到磁盘、网络等设备。**进程就是用来加载指令、管理内存、管理IO的**
- 当一个程序被运行，从磁盘加载这个程序的代码至内存，这时就开启了一个进程
- 进程就可以视为程序的一个实例。大部分程序可以同时运行多个实例进程（例如记事本、画图、浏览器等），也有的程序只能启动一个实例进程（例如网易云音乐、360安全卫士等）




### 1.2 线程

- 一个进程之内可以分为一到多个线程
- **一个线程就是一个指令流，将指令流中的一条条指令以一定的顺序交给CPU执行**
- Java中，**线程作为最小调度单位，进程作为资源分配的最小单位**，在windows中进程是不活动的，只是作为线程的容器




### 1.3 二者对比

- 进程基本上相互独立的，而线程存在于进程内，是进程的一个子集、
- 进程拥有共享的资源，如内存空间等，供其内部的线程共享
- 进程间通信较为复杂
  - 同一台计算机的进程通信为IPC（Inter-process communication）
  - 不同计算机之间的进程通信，需要通过网络，并遵守共同的协议，例如HTTP
- 线程通信相对简单，因为它们共享进程内的内存，一个例子是多个线程可以访问同一个共享变量
- 线程更轻量，线程上下文切换成本一般上要比进程上下文切换低




## 2、并行和并发

### 2.1 并发

- 单核CPU下，线程实际还是串行执行的。
- 操作系统中有一个组件叫做任务调度器，将CPU的时间片（windows下时间片最小约为15毫秒）分给不同的线程使用，只是由于CPU在线程间（时间片很短）的切换非常快，**人类感觉是同时进行的**。总结就是：**微观串行，宏观并行**
- 一般会将这种**线程轮流使用CPU**的做法叫做并发，**concurrent**

| CPU  | 时间片1 | 时间片2 | 时间片3 | 时间片4 |
| :--: | :-----: | :-----: | :-----: | :-----: |
| core |  线程1  |  线程2  |  线程3  |  线程4  |

![并发](C:\Users\lzy\Desktop\work\Java高级\JUC并发编程\图片\并发.png)



### 2.2 并行

- 多核CPU下，**每个核（core）都可以调度运行线程，这时候线程可以是并行的**，**parallel**

|  CPU  | 时间片1 | 时间片2 | 时间片3 | 时间片4 |
| :---: | :-----: | :-----: | :-----: | :-----: |
| core1 |  线程1  |  线程2  |  线程3  |  线程4  |
| core2 |  线程2  |  线程4  |  线程2  |  线程4  |

![并行](C:\Users\lzy\Desktop\work\Java高级\JUC并发编程\图片\并行.png)



### 2.3 总结

- **并发（concurrent）是同一时间应对（dealing with）多件事情的能力**
- **并行（parallel）是同一时间动手做（doing）多间事情的能力**
- 例子
  - 保姆做饭、打扫卫生、带孩子，她一个人轮流交替做这些事，这就是并发
  - 雇佣了多个保姆，她们一起做这些事，这时既有并发，也有并行（这时会产生竞争，例如锅只有一口，一个人用锅时，另一个人就得等待）
  - 雇佣了仨保姆，一个做饭、一个打扫卫生、一个带孩子，互不干扰，这就是并行




## 3、应用

### 3.1 应用之异步调用

#### 3.1 同步异步

- 从方法调用的角度来看
  - 需要等待结果返回，才能继续运行就是同步
  - 不需要等待结果返回，就能继续运行就是异步
- 注意：同步在多线程中还有另外一层意思，就是让多个线程步调一致



#### 3.2 同步例子

~~~java
package com.example.one;

import java.io.FileReader;

/**
 * 同步等待
 */
public class Sync {

    public static void main(String[] args) {
        // 同步等待
        FileReader.read(Constants.MP4_FULL_PATH);
        System.out.println("do other things");
    }
}
~~~

- 这里就是同步的，只有读取MP4文件的文件流执行完成之后，才会执行打印操作



#### 3.3 异步例子

~~~java
package com.example.one;

import java.io.FileReader;

/**
 * 异步不等待
 */
public class Async {

    public static void main(String[] args) {
        // 异步不等待
        new Thread(() -> FileReader.read(Constants.MP4_FULL_PATH)).start();
        System.out.println("do other things");
    }
}
~~~

- 这里就是异步的，不必非得执行完读取文件的操作，就会执行打印操作



#### 3.4 总结

- **多线程可以让方法执行变为异步的（即不要干巴巴的等着）**，比如读取磁盘文件时，假设读取操作花费了5s，如果没有线程调度机制，这5s调用者什么也做不了，其他代码都得暂停
- 比如在项目中，读取文件需要转换格式等操作比较费时，这时开一个新线程处理视频转换，避免阻塞主线程
- tomcat的异步servlet也是类似的目的，让用户线程处理耗时较长的操作，避免阻塞tomcat的工作线程
- ui程序中，开线程进行其他操作，避免阻塞ui线程



### 3.2 异步之提高效率

#### 3.2.1 案例分析

- 充分利用多核CPU的优势，提高运行效率
- 比如执行计算
  - 计算1花费10ms
  - 计算2花费11ms
  - 计算3花费9ms
  - 汇总需要1ms
- 如果是串行执行，那么总共就需要的时间为 10 + 11 + 9 + 1 = 31ms
- 但是如果是四核CPU，各个核心分别使用线程1执行计算1，线程2执行计算2，线程3执行计算3，那么3个线程是并行的，花费时间只取决于最长的那个线程运行的时间，即11ms，最后加上汇总时间只会花费12ms
- 注意：**需要在多核cpu才能提高效率，单核仍然是轮流执行**



#### 3.2.2 结论

- 单核CPU下，多线程不能实际提高程序运行效率，只是为了能够在不同的任务之间切换，不同线程轮流使用CPU，不至于一个线程总占用CPU，别的线程没法干活
- 多核CPU可以并行跑多个线程，但能否提高程序运行效率还是要分情况的
  - 有些任务，经过精心设计，将任务拆分，并行执行，当然可以提高程序的运行效率。但不是所有计算任务都能拆分
  - 也不是所有任务都需要拆分，任务的目的如果不同，谈拆分和效率没啥意义
- IO操作不占用CPU，只是我们一般拷贝文件使用的是【阻塞IO】，这时相当于线程虽然不用CPU，但需要一直等待IO结束，没能充分利用线程，所以才有后面的【非阻塞IO】和【异步IO】优化





# 二、Java线程

## 1、创建和运行线程

### 1.1 方法一：直接使用Thread

- 语法

~~~java
// 创建线程对象
Thread t = new Thread("线程名") {
    public void run() {
        // 要执行的任务
    }
};
// 给线程设置名字
t.setName("线程名");
// 启动线程
t.start();
~~~

- 例子

~~~java
package com.example.one;

import lombok.extern.slf4j.Slf4j;

/**
 * 线程创建方法1：使用new Thread()方式
 */
@Slf4j
public class Test1 {
    public static void main(String[] args) {
        // 开启新线程，重写run方法
        Thread thread = new Thread("thread1") {
            @Override
            public void run() {
                // 线程中打印日志
                log.debug("running");
            }
        };
        // 或者在后面手动给线程赋名字
        thread.setName("thread1");
        thread.start();

        // 主线程中打印日志
        log.debug("running");
    }
}
~~~

- 输出
  - 2024-06-02 21:50:48 [main][com.example.one.Test1] [DEBUG] - running

  - 2024-06-02 21:50:48 [thread1][com.example.one.Test1] [DEBUG] - running

    ​


### 1.2 方法二：Runnable

- 把【线程】和【任务】（要执行的代码）分开
- Thread代表线程
- Runnable可运行的任务（线程要执行的代码）

~~~java
Runnable runnable = new Runnable() {
     public void run() {
        // 要执行的任务
    }
};
// 创建线程对象
Thread thread = new Thread(runnable, "线程名");
// 启动线程
thread.start();
~~~

- 例子

~~~java
package com.example.one;

import lombok.extern.slf4j.Slf4j;

/**
 * 线程创建方法1：使用new Runnable()方式
 */
@Slf4j
public class Test2 {
    public static void main(String[] args) {
        Runnable runnable = new Runnable() {
            @Override
            public void run() {
                log.debug("running");
            }
        };
        // 创建线程对象
        Thread thread = new Thread(runnable, "thread1");
        thread.start();

        // 主线程中打印日志
        log.debug("running");
    }
}
~~~

- 运行结果
  - 2024-06-02 21:52:36 [main][com.example.one.Test2] [DEBUG] - running
  - 2024-06-02 21:52:36 [thread1][com.example.one.Test2] [DEBUG] - running
- JDK8以后可以用lambda表达式简化

~~~java
Runnable runnable = () -> { log.debug("running"); }
// 创建线程对象
Thread thread = new Thread(runnable, "thread1");
thread.start();
~~~



### 1.3 原理之Thread和Runnable

- Thread类的源码分析
- 总结：
  - 构造Thread实例对象的时候，会传入一个Runnable对象，如果有传入，就用Runnable对象的run方法，所以声明一个Runnable对象，然后在构造Thread实例对象的时候，**就会使用Runnable对象的run方法**
  - 而直接声明一个Thread实例对象的时候，就是直接重写run方法，因为它没有Runnable对象传入，就直接用的重写的方法体
  - **有Runnable对象用Runnable对象的run方法，没有就用的自己重写的run方法体**

~~~java
/**
 * Thread的构造方法，就是传入一个Runnable对象
 */
public Thread(Runnable target, String name) {
	init(null, target, name, 0);
}


/**
 * 上面的init方法，会再调用一个init方法，把Runnable对象传入
 */
private void init(ThreadGroup g, Runnable target, String name, long stackSize) {
	init(g, target, name, stackSize, null, true);
}


/**
 * 上面的init方法，里面会把target这个Runnable对象赋值给类的成员变量
 */
private void init(ThreadGroup g, Runnable target, String name,
                      long stackSize, AccessControlContext acc,
                      boolean inheritThreadLocals)  {
	this.target = target;
}


/**
 * 然后再回过头来看Thread的run方法，它是Runnable属性不为空，就用Runnable对象的run方法
 */
@Override
public void run() {
	if (target != null) {
     	target.run();
    }
}
~~~



### 1.4 方法三：FutureTask

- 语法

~~~java
// 创建任务对象
FutureTask<T> task = new FutureTask<T>(new Callable() {
	@Override
    public Object call() throws Exception {
    	// 方法体，并可以返回一个对象
        return obj;
	}
});

// 创建线程对象，参数一：任务对象；参数二：线程名字
Thread thread = new Thread(task, "t1");
thread.start();

// 用task.get()可以拿到线程的返回值
// 注意：主线程阻塞，只有等待task执行完毕，才会执行这个
Object obj = task.get();
~~~

- 例子

~~~java
package com.example.one;

import lombok.extern.slf4j.Slf4j;

import java.util.concurrent.Callable;
import java.util.concurrent.ExecutionException;
import java.util.concurrent.FutureTask;

/**
 * 使用FutureTask对象创建线程，可以拿到返回值
 */
@Slf4j
public class Test3 {
    public static void main(String[] args) throws ExecutionException, InterruptedException {
        // 创建FutureTask对象
        FutureTask futureTask = new FutureTask(new Callable() {
            @Override
            public Object call() throws Exception {
                log.debug("running...");
                Thread.sleep(1000);
                return 100;
            }
        });
        // 使用FutureTask对象创建线程
        Thread thread = new Thread(futureTask, "t1");
        thread.start();

        log.debug("running...");
        log.debug("{}", futureTask.get());
    }
}
~~~

- 结果：隔1s主线程才会打印这个100，因为在task里面有线程同步睡眠，只有线程执行完毕才会执行这个get()
  - 2024-06-02 22:08:57 [main][com.example.one.Test3] [DEBUG] - running...
  - 2024-06-02 22:08:57 [t1][com.example.one.Test3] [DEBUG] - running...
  - 2024-06-02 22:08:58 [main][com.example.one.Test3] [DEBUG] - 100





## 2、观察多个线程同时运行

- 交替执行
- 谁先谁后，不由我们控制

~~~java
package com.example.one;

import lombok.extern.slf4j.Slf4j;

@Slf4j
public class TestMultiThread {
    public static void main(String[] args) {
        new Thread(() -> {
            while (true) {
                log.debug("t1 running...");
            }
        }, "t1").start();

        new Thread(() -> {
            while (true) {
                log.debug("t2 running...");
            }
        }, "t2").start();
    }
}
~~~

- 结果

![多个线程交替运行](C:\Users\lzy\Desktop\work\Java高级\JUC并发编程\图片\多个线程交替运行.png)



## 3、查看进程线程的方法

### 3.1 windows

- 任务管理器可以查看进程和线程数，也可以用来杀死进程
- tasklist 查看进程
  - **tasklist |findstr "进程名称"**
- java进程可以用jps来查看
- taskkill 杀死进程
  - **taskkill /F /T /pid "进程编号"**  ——强制杀死进程和由他启动的子进程
  - 比如：taskkill /F /T /PID "13668"



### 3.2 Linux

- **ps -ef**：查看所有进程信息
  - ps -ef | grep java：查看跟java相关的进程信息（grep后面带查询关键字）
- **ps -fT -p <pid>**: 查看某个进程（pid）的所有线程
- **kill 进程号**：杀死进程
- **top**：动态查看所有进程
  - top 按大写H切换是否显示线程
  - **top -H -p  <pid>**: 查看某个进程（pid）的所有线程



### 3.3 Java

- **jps**：查看所有Java进程
- **jstack <pid> **: 查看某个Java进程（PID）的所有线程状态
- **jconsole**：查看某个Java进程中线程的运行情况（图形界面）



### 3.4 jconsole使用方法

- 首先在远程服务器上启动代码的时候，添加下面的参数

```bash
java 
-Djava.rmi.server.hostname=10.160.13.111  #远程服务器ip，即本机ip
-Dcom.sun.management.jmxremote #允许JMX远程调用
-Dcom.sun.management.jmxremote.port=12345  #自定义jmx端口号
-Dcom.sun.management.jmxremote.ssl=false  # 是否需要ssl安全连接方式
-Dcom.sun.management.jmxremote.authenticate=false #是否需要秘钥
-jar test.jar 
```

- ​当代码运行起来后，在控制台输入jconsole

![jconsole的使用1](C:\Users\lzy\Desktop\work\Java高级\JUC并发编程\图片\jconsole的使用1.png)

- 然后弹出界面框，选择本地连接或者远程连接

![jconsole的使用2](C:\Users\lzy\Desktop\work\Java高级\JUC并发编程\图片\jconsole的使用2.png)

- 然后就有图形化界面来显示当前代码的堆内存占用情况

![jconsole图形化界面](C:\Users\lzy\Desktop\work\Java高级\JUC并发编程\图片\jconsole图形化界面.png)



## 4、原理之线程运行

### 4.1 栈与栈帧

- **每个线程运行时所需要的内存，称为虚拟机栈**
- **每个栈由多个栈帧（Frame）组成，对应着每次方法调用时所占用的内存，互不干扰**
- **每个线程只能有一个活动栈帧，对应着当前正在执行的那个方法**
- 可以通过-Xss256k设置栈内存大小
- **方法执行完之后，栈帧会自己释放，无需借助垃圾回收机制**

![栈](C:\Users\lzy\Desktop\work\Java高级\JUC并发编程\图片\栈.png)

```java
package com.example.one;

/**
 * 栈与栈帧
 * 执行到哪一个方法，就会给其开辟一个新的栈帧空间
 * 执行完之后，就会释放这个空间
 * 每个线程都有自己独立的栈内存，相互独立互补干扰
 */
public class Test5 {

    public static void main(String[] args) {
        // 这个线程会自己开辟一个栈内存，具体执行哪个方法，就开启哪个栈帧
        new Thread() {
            @Override
            public void run() {
                method1(10);
            }
        }.start();

        // 这是一个main方法的栈内存地址，具体执行到哪就开启哪个栈帧
        method1(10);
    }

    public static void method1(int x) {
        int y = x + 1;
        Object m = method2();
        System.out.println(m);
    }

    public static Object method2() {
        Object n = new Object();
        return n;
    }
}
```

![栈与栈帧](C:\Users\lzy\Desktop\work\Java高级\JUC并发编程\图片\栈与栈帧.png)



###  4.2 线程上下文切换

- **Thread Context Switch**
- 因为以下一些原因导致CPU不再执行当前的线程，转而执行另一个线程的代码
  - 线程的CPU时间片用完
  - 垃圾回收
  - 有更高优先级的线程需要运行
  - 线程自己调用了sleep、yield、wait、join、park、synchronized、lock等方法
- 当Context Switch发生时，需要由操作系统保存当前线程的状态，并恢复另一个线程的状态，Java中对应概念就是**程序计数器**，它的作用是记住下一条jvm指令的执行地址，是线程私有的
  - 状态包括程序计数器、虚拟机栈中每个栈帧的信息，如局部变量、操作数栈、返回地址等
  - **Context Switch频繁发生会影响性能，所以并不是线程越多越好**

![Context Switch](C:\Users\lzy\Desktop\work\Java高级\JUC并发编程\图片\Context Switch.png)



# 三、线程常用方法

## 1、线程常见方法

|   方法名方法名   |                          功能说明                          |                             注意                             | static |
| :--------------: | :--------------------------------------------------------: | :----------------------------------------------------------: | :----: |
|     start()      |       启动一个新线程，在新的线程运行run方法中的代码        | **start方法只是让线程进入就绪，里面代码不一定立即运行（CPU的时间片还没分给它）。每个线程对象的start方法只能调用一次，如果调用多次会出现IllegalThreadStateException** |        |
|      run()       |                  新线程启动后会调用的方法                  | 如果在构造Thread对象时传递了Runnable参数，则线程启动后会调用Runnable中的run方法，否则默认不执行任何操作。但可以创建Thread的子类对象，来覆盖默认行为 |        |
|       join       |                      等待线程运行结束                      | 比如有两个线程，主线程想要拿到一个线程的执行结果，就必须得等到这个线程执行完成，这个时候就可以用join，等待其执行完毕 |        |
|   join(long n)   |              等待线程运行结束，最多等待n毫秒               |                                                              |        |
|     getId()      |                     获取线程长整型的id                     |                            id唯一                            |        |
|    getName()     |                         获取线程名                         |                                                              |        |
| setName(String)  |                         修改线程名                         |                                                              |        |
|  getPriority()   |                       获取线程优先级                       |                                                              |        |
| setPriority(int) |                       修改线程优先级                       | java中规定线程优先级是1~10的整数，较大的优先级能提高该线程被CPU调度的机率 |        |
|    getState()    |                        获取线程状态                        | Java中线程状态是6个enum表示，分别：NEW、RUNNABLE、BLOCKED、WAITING、TIMED_WAITING、TERMINATED |        |
| isInterrupted()  |                       判断是否被打断                       |                    不会清除 **打断标记**                     |        |
|    isAlive()     |               线程是否存活（还没有执行完毕）               |                                                              |        |
|   interrupt()    |                          打断线程                          | 如果被打断线程正在sleep，wait，join会导致被打断的线程抛出InterruptedException，并清除**打断标记**；如果打断的正在运行的线程，则会设置**打断标记**；park的线程被打断，也会设置 |        |
|  interrupted()   |                   判断当前线程是否被打断                   |                     会清除 **打断标记**                      | static |
| currentThread()  |                   获取当前正在执行的线程                   |                                                              | static |
|  sleep(long n)   | 让当前执行的线程休眠n毫秒，休眠时让出cpu的时间片给其他线程 |                                                              | static |
|     yield()      |           提示线程调度器让出当前线程对CPU的使用            |                      主要为了测试和调试                      | static |



## 2、start与run

- 例子1分析：**直接执行run方法**
  - **如果直接调用run方法**，那么就相当于是调用了一个普通的方法，**还是main线程执行的**，并不是新开辟的线程执行
  - **故而也达不到所想要的异步效果**，就相当于全程是main线程执行，走完才能走下一步

~~~java
package com.example.one;

import lombok.SneakyThrows;
import lombok.extern.slf4j.Slf4j;

import java.util.Date;

@Slf4j
public class Test5 {

    public static void main(String[] args) {
        Thread t1 = new Thread("t1") {
            @SneakyThrows
            @Override
            public void run() {
                log.debug("running");
                log.debug(String.valueOf(new Date()));
                Thread.sleep(10000);
            }
        };

        t1.run();
        log.debug("main running");
        log.debug(String.valueOf(new Date()));
    }
}

// 结果如下
// 2023-08-04-22-12 [main] [com.example.one.Test5] [DEBUG] - running
// 2023-08-04-22-12 [main] [com.example.one.Test5] [DEBUG] - Fri Aug 04 22:12:41 CST 2023
// 2023-08-04-22-12 [main] [com.example.one.Test5] [DEBUG] - main running
// 2023-08-04-22-12 [main] [com.example.one.Test5] [DEBUG] - Fri Aug 04 22:12:51 CST 2023
~~~

- 例子2分析：**执行start方法**
  - **调用线程的start方法，才是用开辟的新线程执行**
  - **也能达到异步效果**

~~~java
package com.example.one;

import lombok.SneakyThrows;
import lombok.extern.slf4j.Slf4j;

import java.util.Date;

@Slf4j
public class Test5 {

    public static void main(String[] args) {
        Thread t1 = new Thread("t1") {
            @SneakyThrows
            @Override
            public void run() {
                log.debug("running");
                log.debug(String.valueOf(new Date()));
                Thread.sleep(10000);
            }
        };

        t1.start();
        log.debug("main running");
        log.debug(String.valueOf(new Date()));
    }
}

// 执行结果
// 2023-08-04-22-16 [main] [com.example.one.Test5] [DEBUG] - main running
// 2023-08-04-22-16 [t1] [com.example.one.Test5] [DEBUG] - running
// 2023-08-04-22-16 [t1] [com.example.one.Test5] [DEBUG] - Fri Aug 04 22:16:38 CST 2023
// 2023-08-04-22-16 [main] [com.example.one.Test5] [DEBUG] - Fri Aug 04 22:16:38 CST 2023
~~~

- 例子3分析：**分析调用start方法后的线程状态**
  - 调用start前，是**初始**态（NEW）
  - 调用start后，该线程就进入了就绪状态，可是此时线程调度程序尚未把该线程设置为当前线程，即**当前线程有资格运行，可是尚未运行**（RUNNABLE）

```java
package com.example.two;

import lombok.extern.slf4j.Slf4j;

/**
 * 调用start()方法前，线程状态：NEW
 * 调用start()方法后，线程状态：RUNNABLE
 */
@Slf4j
public class Test2 {
    public static void main(String[] args) {
        Thread t1 = new Thread("t1") {
            @Override
            public void run() {
                log.debug("running...");
            }
        };

        System.out.println(t1.getState());
        t1.start();
        System.out.println(t1.getState());
    }
}


// 运行结果
// NEW：初始状态
// RUNNABLE：就绪状态，这时可以被cpu分配时间片执行
```

- 例子4：**分析start方法不可被多次调用**
  - 会出现异常IllegalThreadStateException

~~~java
package com.example.two;

import lombok.extern.slf4j.Slf4j;

/**
 * start()方法只能调一次，多次调用会报错：java.lang.IllegalThreadStateException
 */
@Slf4j
public class Test3 {
    public static void main(String[] args) {
        Thread t1 = new Thread("t1") {
            @Override
            public void run() {
                log.debug("running...");
            }
        };
        t1.start();
        t1.start();
    }
}

// 运行结果
// Exception in thread "main" java.lang.IllegalThreadStateException
~~~



## 3、sleep和yield

### 3.1 sleep

- **调用sleep会让当前线程从Running进入Timed Waiting状态，在哪个线程里执行就是哪个线程睡眠**

~~~java
package com.example.two;

import lombok.extern.slf4j.Slf4j;

/**
 * Thread.sleep(long n)在哪个线程执行，哪个线程进入休眠
 * 休眠下的线程状态：TIMED_WAITING
 */
@Slf4j
public class Test4 {
    public static void main(String[] args) {
        Thread t1 = new Thread("t1") {
            @Override
            public void run() {
                try {
                    Thread.sleep(2000);
                } catch (InterruptedException exception) {
                    exception.printStackTrace();
                }
            }
        };
        t1.start();

        // 这里加上这块原因：要让主线程让出cpu供t1线程执行，这样才能走到t1的代码
        // 不加的话，可能主线程就先走了，那么t1还没执行到休眠的那行代码，打印就出来了，就是RUNNABLE了
        try {
            Thread.sleep(500);
        } catch (InterruptedException exception) {
            exception.printStackTrace();
        }

        log.debug("t1线程的状态：" + t1.getState());
    }
}

// 结果
// 2023-08-05-19-21 [main] [com.example.one.Test6] [DEBUG] - RUNNABLE
// 2023-08-05-19-18 [main] [com.example.one.Test6] [DEBUG] - TIMED_WAITING

// 原因
// 因为主线程先执行，执行到睡眠后，再执行t1的run方法，然后里面的线程执行睡眠方法，故而进入TIMED_WAITING状态
~~~

- **其他线程可以使用interrupt方法打断正在睡眠的线程，这时sleep方法会抛出InterruptedException**

~~~java
package com.example.one;

import lombok.extern.slf4j.Slf4j;

@Slf4j
public class Test7 {
    public static void main(String[] args) throws InterruptedException {
        Thread t1 = new Thread("t1") {
            @Override
            public void run() {
                log.debug("enter sleep");
                try {
                    Thread.sleep(3000);
                } catch (InterruptedException exception) {
                    log.debug("wake up");
                    exception.printStackTrace();
                }
            }
        };
        t1.start();

        Thread.sleep(1000);
        log.debug("interrupt");
        t1.interrupt();
    }
}

// 结果
// 2023-08-05-19-25 [t1] [com.example.one.Test7] [DEBUG] - enter sleep
// 2023-08-05-19-25 [main] [com.example.one.Test7] [DEBUG] - interrupt
// 2023-08-05-19-25 [t1] [com.example.one.Test7] [DEBUG] - wake up
// java.lang.InterruptedException: sleep interrupted

// 原因
// 主线程执行睡眠方法后，就进入线程t1，打印enter sleep
// 线程t1执行睡眠方法，回到主线程，执行interrupt，然后就会出现InterruptedException异常，打印wake up
~~~

- **睡眠结束后的线程未必会立刻得到执行**，因为可能没有分到cpu时间片
- **建议用TimeUnit的sleep代替Thread的sleep来获得更好的可读性**，因为它可以指定时间单位

~~~java
package com.example.one;

import lombok.SneakyThrows;
import lombok.extern.slf4j.Slf4j;

import java.util.concurrent.TimeUnit;

@Slf4j
public class Test8 {
    public static void main(String[] args) {
        Thread t1 = new Thread("t1") {
            @SneakyThrows
            @Override
            public void run() {
                log.debug("enter");
                TimeUnit.SECONDS.sleep(1);
                log.debug("end");
            }
        };
        t1.start();
    }
}
// 结果
// 2023-08-05 20:04:46 [t1] [com.example.one.Test8] [DEBUG] - enter
// 2023-08-05 20:04:47 [t1] [com.example.one.Test8] [DEBUG] - end
~~~



### 3.2 yield

- 调用yield会让出当前线程从Running进入Runnable就绪状态，然后调度执行其他线程
- **具体的实现依赖于操作系统的任务调度器**
  - 就是让当前线程让出对CPU的使用权，但是如果此时没有其他的线程，那么他可能又会进入运行状态



### 3.3 二者区别

- 状态区别
  - Timed Waiting状态：任务调度器不会把CPU资源分配给他；
  - Runnable就绪状态：任务调度器会把CPU资源分配给他
- 参数区别
  - sleep需要传递时间参数，用于休眠时间
  - yield不需要传递参数



### 3.4 线程优先级

- 线程优先级会提示（hint）调度器优先调度该线程，但它仅仅是一个提示，调度器可以忽略它
- 如果cpu比较忙，那么优先级高的线程会获得更多的时间片，但cpu闲时，优先级几乎没用

~~~java
package com.example.one;

import lombok.extern.slf4j.Slf4j;

@Slf4j
public class Test9 {
    public static void main(String[] args) {
        Runnable r1 = new Runnable() {
            @Override
            public void run() {
                int count = 0;
                for(;;) {
                    System.out.println("t1------>" + ++count);
                }
            }
        };

        Runnable r2 = new Runnable() {
            @Override
            public void run() {
                int count = 0;
                for(;;) {
                    // 设置yield，让出时间片，可能会导致打印的少
//                    Thread.yield();
                    System.out.println("          t2------>" + ++count);
                }
            }
        };

        Thread t1 = new Thread(r1);
        Thread t2 = new Thread(r2);
        // 设置优先级，优先级高的可能会打印的更多一点
        t1.setPriority(Thread.MIN_PRIORITY);
        t2.setPriority(Thread.MAX_PRIORITY);
        t1.start();
        t2.start();
    }
}

~~~



### 3.5 防止CPU占用100%

- sleep实现
  - **在没有利用cpu来计算时，不要让while(true)空转浪费cpu，这时可以使用yield或者sleep来让出cpu的使用权给其他程序**
  - 结果：不加的时候，cpu占用率一直接近100%，加了之后cpu占用率只差不多在3%
  - 原因：因为加了之后，线程会短暂让出cpu的时间片，这时候别的线程可能会占用cpu，这样就不会一直卡在这个java程序里，别的线程一占用，等到它睡眠醒来后，再进行排队，这样避免了一直占用的问题，所以会大大减少资源浪费

~~~java
while(true) {
    try {
        Thread.sleep(50);
    } catch (InterruptedException exception) {
    	exception.printStackTrace();
    }
}
~~~



## 4、join

### 4.1 方法作用

- 例子分析

~~~java
package com.example.two;

import lombok.SneakyThrows;
import lombok.extern.slf4j.Slf4j;

/**
 * 不加join()方法
 *      因为主线程和线程t1是并行执行的，t1线程需要1s之后才能算出count=10
 *      而主线程一开始就要打印count的结果，所以只会打印count=0
 * 加join()方法
 *      主线程执行到join()方法这时，如果t1没执行完，就会卡在这等待，直到t1执行完才让主线程往下运行，所以打印10
 */
@Slf4j
public class Test8 {

    static int count = 0;

    public static void main(String[] args) throws InterruptedException {
        log.debug("开始");
        Thread t1 = new Thread("t1") {
            @SneakyThrows
            @Override
            public void run() {
                log.debug("开始");
                Thread.sleep(1000);
                log.debug("结束");
                count = 10;
            }
        };
        t1.start();
        // 加上join方法同步
        t1.join();
        log.debug("结果为{}", count);
    }
}
// 结果
// 2023-08-06 16:02:16 [main] [com.example.one.Test10] [DEBUG] - 开始
// 2023-08-06 16:02:16 [t1] [com.example.one.Test10] [DEBUG] - 开始
// 2023-08-06 16:02:16 [main] [com.example.one.Test10] [DEBUG] - 结果为0
// 2023-08-06 16:02:17 [t1] [com.example.one.Test10] [DEBUG] - 结束

// 原因
// 因为主线程和线程t1是并行执行的，t1线程需要1s之后才能算出count=10
// 而主线程y一开始就要打印count的结果，所以只会打印count=0
~~~

- 用join方法便可解决此问题，在t1.start()之后加上join即可
  - **因为join方法就是等待线程运行完毕**
  - 调用谁的join方法，就是等待哪个线程运行结束



### 4.2 应用之同步

```java
package com.example.one;

import lombok.extern.slf4j.Slf4j;

@Slf4j
public class Test11 {

    static int count1 = 0;
    static int count2 = 0;

    public static void main(String[] args) throws InterruptedException {
        Thread t1 = new Thread(() -> {
            try {
                Thread.sleep(1000);
                count1 = 10;
            } catch (InterruptedException exception) {
                exception.printStackTrace();
            }
        });

        Thread t2 = new Thread(() -> {
            try {
                Thread.sleep(2000);
                count2 = 20;
            } catch (InterruptedException exception) {
                exception.printStackTrace();
            }
        });

        t1.start();
        t2.start();

        long start = System.currentTimeMillis();
        log.debug("join begin");
        t1.join();
        log.debug("t1 join end");
        t2.join();
        log.debug("t2 join end");
        long end = System.currentTimeMillis();
        log.debug("count: {}, count2: {}, cost: {}", count1, count2, end - start);
    }
}

// 结果：花费2s时间
// 2023-08-06 21:37:18 [main] [com.example.one.Test11] [DEBUG] - join begin
// 2023-08-06 21:37:19 [main] [com.example.one.Test11] [DEBUG] - t1 join end
// 2023-08-06 21:37:20 [main] [com.example.one.Test11] [DEBUG] - t2 join end
// 2023-08-06 21:37:20 [main] [com.example.one.Test11] [DEBUG] - count: 10, count2: 20, cost: 2016

// 原因分析
// 俩线程是并行执行的，t1执行的同时，t2也在执行，等待t1的同时，也会等待t2，所以只看时间花费最长的，就是2s
```



### 4.3 有时效的join

- 线程执行结束h会导致join结束

~~~java
package com.example.one;

import lombok.extern.slf4j.Slf4j;

@Slf4j
public class Test12 {
    static int count1 = 0;
    public static void main(String[] args) throws InterruptedException {
        Thread t1 = new Thread(() -> {
            try {
                Thread.sleep(2000);
                count1 = 10;
            } catch (InterruptedException exception) {
                exception.printStackTrace();
            }
        });

        long start = System.currentTimeMillis();
        t1.start();

        // 线程执行结束h会导致join结束
        t1.join(1500);
        long end = System.currentTimeMillis();
        log.debug("count1:{}, cost:{}", count1, end - start);
    }
}
// t1.join(1500)结果
// 2023-08-06 21:52:18 [main] [com.example.one.Test12] [DEBUG] - count1:0, cost:1504
// 因为最多等待1.5s，线程需要2s，等不到所以就直接走下一行代码了，所以就是1.5s时间，且这时候count1 = 10这行代码在t1线程中还没走到，所以是0

// t1.join(3000)结果
// 2023-08-06 21:54:38 [main] [com.example.one.Test12] [DEBUG] - count1:10, cost:2012
// 因为线程只需要2s就执行完了，线程都执行完了，所以就不用join再等了，故而只需要2s
~~~



## 5、interrupt

### 5.1 打断阻塞的线程

- **打断sleep、wait、join的线程**
- **打断sleep的线程，会清空打断标记，即重置为false**
  - 原因：一个正在阻塞的线程被打断，因为该线程还在阻塞，不知道到底发生了什么，突然被打断就会抛异常来停止当前的阻塞状态，以应对外面发生的变化。也就是响应中断

~~~java
package com.example.one;

import lombok.extern.slf4j.Slf4j;

@Slf4j
public class Test13 {
    public static void main(String[] args) throws InterruptedException {
        Thread t1 = new Thread(() -> {
            log.debug("sleep");
            try {
                Thread.sleep(1000);
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
        }, "t1");

        t1.start();
        Thread.sleep(500);
        log.debug("interrupt");
        t1.interrupt();
        System.out.println("打断标记：" + t1.isInterrupted());
    }
}
// 输出：
// 2023-08-12 22:08:08 [t1] [com.example.one.Test13] [DEBUG] - sleep
// 2023-08-12 22:08:08 [main] [com.example.one.Test13] [DEBUG] - interrupt
// 打断标记：false
// java.lang.InterruptedException: sleep interrupted
~~~



### 5.2 打断正常运行的线程

- **打断正常线程是打断标记会置为true，然后线程可以继续执行**

~~~java
package com.example.one;

import lombok.extern.slf4j.Slf4j;

@Slf4j
public class Test14 {
    public static void main(String[] args) throws InterruptedException {
        Thread t1 = new Thread(() -> {
            while (true) {
                // 获取当前线程的打断标记
                boolean interrupted = Thread.currentThread().isInterrupted();
                if(interrupted) {
                    log.debug("被打断了，退出循环");
                    break;
                }
            }
        }, "t1");

        t1.start();
        Thread.sleep(1000);
        log.debug("interrupt");
        t1.interrupt();
    }
}
// 2023-08-12 23:00:07 [main] [com.example.one.Test14] [DEBUG] - interrupt
// 2023-08-12 23:00:07 [t1] [com.example.one.Test14] [DEBUG] - 被打断了，退出循环
~~~



### 5.3 两阶段终止模式

- 概念：**在一个线程中“体面”的终止另一个线程，也就是给终止线程一个处理后事的机会**
- 错误思路
  - 使用线程对象的stop()方法停止线程
    - stop方法会真正杀死线程，如果这个时候线程锁住了共享资源，那么当它被杀死后就再也没有机会释放锁，其他线程将永远无法获取锁
  - 使用System.exit(int)方式停止线程
    - 目的仅是停止一个线程，但这种做法会让整个程序都停止
- **应用场景**：
  - 用一个后台的监控线程，去每隔两秒执行一下监控操作
  - 如果有时候不想监控了，点击一个按钮就可以优雅的停止这个监控，这时就需要在另一个线程里停止这个后台线程

![两阶段终止模式](C:\Users\lzy\Desktop\work\Java高级\JUC并发编程\图片\两阶段终止模式.png)

- 代码实现

~~~java
package com.example.one;

import lombok.extern.slf4j.Slf4j;

@Slf4j
public class Test15 {
    public static void main(String[] args) throws InterruptedException {
        TwoPhaseTermination termination = new TwoPhaseTermination();
        // 启用后台监控程序
        termination.start();
        // 3.5s后打断监控
        Thread.sleep(3500);
        termination.stop();
    }
}

@Slf4j
class TwoPhaseTermination {
    private Thread monitor;

    // 启动监控线程
    public void start() {
        monitor = new Thread(() -> {
            while (true) {
                // 获取当前线程
                Thread current = Thread.currentThread();
                if(current.isInterrupted()) {
                    // 当前线程的打断标记为真时，意味着想停止
                    log.debug("料理后事");
                    break;
                } else {
                    // 当前线程不想停止
                    try {
                        // 正常的执行监控记录
                        Thread.sleep(1000);
                        log.debug("执行监控记录");
                    } catch (InterruptedException exception) {
                        exception.printStackTrace();
                        // 如果出现异常，就将打断标记置为真，这样就可以进行优雅退出
                        current.interrupt();
                    }
                }
            }
        });
        monitor.start();
    }

    // 停止监控线程
    public void stop() {
        monitor.interrupt();
    }
}

// 执行结果
// 2023-08-13 16:40:44 [Thread-0] [com.example.one.TwoPhaseTermination] [DEBUG] - 执行监控记录
// 2023-08-13 16:40:45 [Thread-0] [com.example.one.TwoPhaseTermination] [DEBUG] - 执行监控记录
// 2023-08-13 16:40:46 [Thread-0] [com.example.one.TwoPhaseTermination] [DEBUG] - 执行监控记录
// 2023-08-13 16:40:46 [Thread-0] [com.example.one.TwoPhaseTermination] [DEBUG] - 料理后事
// java.lang.InterruptedException: sleep interrupted
~~~



### 5.4 interrupt打断park线程

- **打断park线程，不会清空打断状态**
- park：类似于断点，让线程停下来
  - 执行interrupt之后，不会清空打断标记，然后若后续再有park()，打断标记为true的情况，就不会再停止了
  - 但是若把打断标记置为false后，就可以停止了

~~~java
package com.example.one;

import lombok.extern.slf4j.Slf4j;

import java.util.concurrent.locks.LockSupport;

@Slf4j
public class Test16 {
    public static void main(String[] args) throws InterruptedException {
        Thread t1 = new Thread(() -> {
            log.debug("park");
            LockSupport.park(); // 停止运行后面的代码
            log.debug("unpark");
            log.debug("打断状态1：{}", Thread.currentThread().isInterrupted());
       //     log.debug("打断状态1：{}", Thread.currentThread().interrupted());
            LockSupport.park(); // 停止运行后面的代码
            log.debug("unpark");
            log.debug("打断状态2：{}", Thread.currentThread().isInterrupted());
        }, "t1");

        t1.start();
        Thread.sleep(1000);
        t1.interrupt();
    }
}
// 不加t1.interrupt();
// 只会输出park，后面的代码不会执行
// 2023-08-13 19:53:44 [t1] [com.example.one.Test16] [DEBUG] - park

// 加上t1.interrupt();
// 后面的代码都会执行，且不会将打断标记重置为fasle
// 同时后续再有park，就不会停止了
// 2023-08-13 19:56:09 [t1] [com.example.one.Test16] [DEBUG] - park
// 2023-08-13 19:56:10 [t1] [com.example.one.Test16] [DEBUG] - unpark
// 2023-08-13 19:56:10 [t1] [com.example.one.Test16] [DEBUG] - 打断状态1：true
// 2023-08-13 19:56:10 [t1] [com.example.one.Test16] [DEBUG] - unpark
// 2023-08-13 19:56:10 [t1] [com.example.one.Test16] [DEBUG] - 打断状态2：true


// 加上t1.interrupted();即：将打断标记置为false
// 若将打断标记置为false之后，再有park，还是会停止
// 2023-08-13 19:56:09 [t1] [com.example.one.Test16] [DEBUG] - park
// 2023-08-13 19:56:10 [t1] [com.example.one.Test16] [DEBUG] - unpark
// 2023-08-13 19:56:10 [t1] [com.example.one.Test16] [DEBUG] - 打断状态1：true
~~~



## 6、不推荐的方法

- 这些方法不推荐，已经过时
- **容易破坏同步代码块，造成线程死锁**

|  方法名   | static |      功能说明      |
| :-------: | :----: | :----------------: |
|  stop()   |        |    停止线程运行    |
| suspend() |        | 挂起(暂停)线程运行 |
| resume()  |        |  恢复暂停线程运行  |



## 7、主线程和守护线程

- **默认情况下，Java进程需要等待所有线程都运行结束，才会结束**
- 有一种特殊线程叫做守护线程，**只要其它非守护线程运行结束了，即使守护线程的代码没有执行完，也会强制结束**
- **垃圾回收器线程就是一种守护线程**
- Tomcat中的Accepter和Poller线程（**接收和分发请求**）都是守护线程，所以tomcat接收到shutdown命令后，不会等待它们处理完当前请求
- 调用线程的setDaemon(true)方法，即可将线程设置为守护线程

~~~java
package com.example.one;

import lombok.extern.slf4j.Slf4j;

@Slf4j
public class Test17 {
    public static void main(String[] args) throws InterruptedException {
        Thread t1 = new Thread(() -> {
            while (true) {
                if(Thread.currentThread().isInterrupted()) {
                    break;
                }
            }
            log.debug("t1线程结束");
        },"t1");
//        t1.setDaemon(true);
        t1.start();

        Thread.sleep(1000);
        log.debug("结束");
    }
}
// 设置为守护线程之后，只会打印一个结束，因为主线程一旦结束，守护线程就会停止结束
// 2023-08-13 23:43:08 [main] [com.example.one.Test17] [DEBUG] - 结束

// 不设置为守护线程，程序会打印结束，但是Java程序不会结束，还是会一直运行的
~~~



## 8、线程的五种状态

![线程五种状态](C:\Users\lzy\Desktop\work\Java高级\JUC并发编程\图片\线程五种状态.png)

- 【初始状态】：仅是在语言层面上创建了线程对象，还未与操作系统线程关联
- 【可运行状态】：（就绪状态）指该线程已经被创建（与操作系统线程关联），可以由CPU调度执行
- 【运行状态】：指获取了Cpu时间片运行中的状态
  - 当CPU时间片用完，会从【运行状态】转换至【可运行状态】，会导致线程的上下文切换
- 【阻塞状态】：**调度器不会考虑到把时间片分给它**
  - 如果调用了阻塞API，如BIO读写文件，这时该线程实际不会用到CPU，会导致上下文切换，进入【阻塞状态】
  - 等BIO操作完毕，会由操作系统唤醒阻塞的线程，转换至【可运行状态】
  - 与【可运行状态】的区别：对【阻塞状态】的线程来说，只要它们一直不唤醒，调度器就一直不会考虑调度它们
- 【终止状态】：线程已经执行完毕，生命周期已经结束，不会再转换为其他状态





## 9、线程的六种状态

- **从Java API方式划分**
- 根据Thread.State枚举，分为六种状态

![线程五种状态](C:\Users\lzy\Desktop\work\Java高级\JUC并发编程\图片\线程六种状态.png)

- NEW：线程刚被创建，但是还没有调用start()方法
- RUNNABLE：当调用了start()方法之后，注意：Java API层面的RUNNABLE状态涵盖了操作系统层面的【可运行状态】、【运行状态】和【阻塞状态】（由于BIO导致的线程阻塞，在Java里无法区分，仍然认为是可运行）
- BLOCKED、WAITING、TIMED_WAITING都是Java API层面对【阻塞状态】的细分
- TERMINATED：当线程代码运行结束

~~~java
package com.example.one;

import lombok.extern.slf4j.Slf4j;

@Slf4j
public class Test18 {
    public static void main(String[] args) throws InterruptedException {
        // 创建线程，但是没有调用start()方法，所以是初始化状态，即：NEW
        Thread t1 = new Thread("t1") {
            @Override
            public void run() {
                log.debug("running...");
            }
        };

        // 创建线程，并且调用start()方法，且是死循环，它会一直运行下去，所以是：RUNNABLE
        Thread t2 = new Thread("t2") {
            @Override
            public void run() {
                while (true) {

                }
            }
        };
        t2.start();

        // 创建线程，并且调用start()方法，且只有一行代码，执行完后就没了，所以是销毁：TERMINATED
        Thread t3 = new Thread("t3") {
            @Override
            public void run() {
                log.debug("running...");
            }
        };
        t3.start();

        // 线程在睡眠，且是有时限的睡眠，所以是TIMED_WAITING
        Thread t4 = new Thread("t4") {
            @Override
            public void run() {
                synchronized (Test18.class) {
                    try {
                        Thread.sleep(1000000);
                    } catch (InterruptedException e) {
                        e.printStackTrace();
                    }
                }
            }
        };
        t4.start();

		// 线程会等待t2执行完再执行这个，但是t2是个死循环，所以一直阻塞住，WAITING
        Thread t5 = new Thread("t5") {
            @Override
            public void run() {
                try {
                    t2.join();
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }
            }
        };
        t5.start();

		// t4和t6都是对这个Test18锁，但是t4是先锁住的，故而t6拿不到锁，就是BLOCKED状态
        Thread t6 = new Thread("t6") {
            @Override
            public void run() {
                synchronized (Test18.class) {
                    try {
                        Thread.sleep(1000000);
                    } catch (InterruptedException e) {
                        e.printStackTrace();
                    }
                }
            }
        };
        t6.start();

        Thread.sleep(1000);
        log.debug("t1 state: {}", t1.getState());
        log.debug("t2 state: {}", t2.getState());
        log.debug("t3 state: {}", t3.getState());
        log.debug("t4 state: {}", t4.getState());
        log.debug("t5 state: {}", t5.getState());
        log.debug("t6 state: {}", t6.getState());
    }

}
// 2023-08-14 20:06:55 [t3] [com.example.one.Test18] [DEBUG] - running...
// 2023-08-14 20:06:56 [main] [com.example.one.Test18] [DEBUG] - t1 state: NEW
// 2023-08-14 20:06:56 [main] [com.example.one.Test18] [DEBUG] - t2 state: RUNNABLE
// 2023-08-14 20:06:56 [main] [com.example.one.Test18] [DEBUG] - t3 state: TERMINATED
// 2023-08-14 20:06:56 [main] [com.example.one.Test18] [DEBUG] - t4 state: TIMED_WAITING
// 2023-08-14 20:06:56 [main] [com.example.one.Test18] [DEBUG] - t5 state: WAITING
// 2023-08-14 20:06:56 [main] [com.example.one.Test18] [DEBUG] - t6 state: BLOCKED
~~~



## 10、习题

- 洗水壶，烧开水
- 烧水同时洗茶壶，洗茶杯，拿茶叶
- 两者同步进行

![应用烧水泡茶](C:\Users\lzy\Desktop\work\Java高级\JUC并发编程\图片\应用烧水泡茶.png)

~~~java
package com.example.one;

import lombok.SneakyThrows;
import lombok.extern.slf4j.Slf4j;

@Slf4j
public class Test19 {
    public static void main(String[] args) {
        Thread t1 = new Thread("老王") {
            @SneakyThrows
            @Override
            public void run() {
                log.debug("洗水壶");
                Thread.sleep(1000);
                log.debug("烧开水");
                Thread.sleep(15000);
            }
        };

        Thread t2 = new Thread("小王") {
            @SneakyThrows
            @Override
            public void run() {
                log.debug("洗茶壶");
                Thread.sleep(1000);
                log.debug("洗茶杯");
                Thread.sleep(2000);
                log.debug("拿茶叶");
                Thread.sleep(1000);
                t1.join();
                log.debug("泡茶");
            }
        };

        t1.start();
        t2.start();
    }
}
// 2023-08-14 20:32:33 [老王] [com.example.one.Test19] [DEBUG] - 洗水壶
// 2023-08-14 20:32:33 [小王] [com.example.one.Test19] [DEBUG] - 洗茶壶
// 2023-08-14 20:32:34 [老王] [com.example.one.Test19] [DEBUG] - 烧开水
// 2023-08-14 20:32:34 [小王] [com.example.one.Test19] [DEBUG] - 洗茶杯
// 2023-08-14 20:32:36 [小王] [com.example.one.Test19] [DEBUG] - 拿茶叶
// 2023-08-14 20:32:49 [小王] [com.example.one.Test19] [DEBUG] - 泡茶
~~~



# 四、共享模型之管程

## 1、共享带来的问题

### 1.1 故事

![故事1](C:\Users\lzy\Desktop\work\Java高级\JUC并发编程\图片\故事1.png)

![故事1](C:\Users\lzy\Desktop\work\Java高级\JUC并发编程\图片\故事2.png)

![故事1](C:\Users\lzy\Desktop\work\Java高级\JUC并发编程\图片\故事3.png)

![事1](C:\Users\lzy\Desktop\work\Java高级\JUC并发编程\图片\故事4.png)



### 1.2 Java代码体现

```java
package com.example.two;

import lombok.extern.slf4j.Slf4j;

@Slf4j
public class Test1 {
    static int count = 0;
    public static void main(String[] args) throws InterruptedException {
        Thread t1 = new Thread(() -> {
            for (int i = 0; i < 5000; i++) {
                count++;
            }
        }, "t1");

        Thread t2 = new Thread(() -> {
            for (int i = 0; i < 5000; i++) {
                count--;
            }
        }, "t2");

        t1.start();
        t2.start();
        t1.join();
        t2.join();
        log.debug("count = {}", count);
    }
}

// 结果：count的值每次都不一样，有时正数，有时负数
// 2023-08-14 22:37:32 [main] [com.example.two.Test1] [DEBUG] - count = 603
```



### 1.3 结果分析

- 因为 Java 中对静态变量的自增，自减并不是原子操作，要彻底理解，必须从字节码来进行分析


- 例如对于 i++ 而言（i 为静态变量），实际会产生如下的 JVM 字节码指令：

~~~java
getstatic i // 获取静态变量i的值
iconst_1 // 准备常量1
iadd // 自增
putstatic i // 将修改后的值存入静态变量i
    
getstatic i // 获取静态变量i的值
iconst_1 // 准备常量1
isub // 自减
putstatic i // 将修改后的值存入静态变量i
~~~

- 可以看到`count++` 和 `count--` 操作实际都是需要这个4个指令完成的。Java 的内存模型如下，完成静态变量的自增，自减需要在主内存和工作内存中进行数据交换

![自增自减](C:\Users\lzy\Desktop\work\Java高级\JUC并发编程\图片\自增自减.png)

- 如果是单线程以上 8 行代码是**顺序执行（不会交错）**没有问题：

![自增自减单线程](C:\Users\lzy\Desktop\work\Java高级\JUC并发编程\图片\自增自减单线程.png)

-  但多线程下这 8 行代码可能交错运行：

  - 出现负数的情况：

  ![自增自减多线程负数](C:\Users\lzy\Desktop\work\Java高级\JUC并发编程\图片\自增自减多线程负数.png)
  - 出现正数的情况：

  ![自增自减多线程负数](C:\Users\lzy\Desktop\work\Java高级\JUC并发编程\图片\自增自减多线程正数.png)

  - 总结：**多线程的上下文切换，造成的指令混乱**



### 1.4 临界区

- 一个程序运行多个线程本身是没有问题的
- 问题出现在多个线程访问**共享资源**
  - 多个线程读**共享资源**其实也没有问题
  - 在多个线程对**共享资源**读写操作时发生指令交错，就会出现问题
- 一段代码块内，**如果存在对共享资源的多线程读写操作**，称这块代码为**临界区**

~~~java
static int count = 0;

static void increment() {
    // 临界区
    count++;
}

static void decrement() {
    // 临界区
    count--;
}
~~~



### 1.5 竞态条件

- 多个线程在临界区内执行，由于代码的**执行序列**不同而导致结果无法预测，称之为发生了**竞态条件**





## 2、synchronized 解决方案

### 2.1 应用之互斥

- 阻塞式方式，即俗称的【对象锁】，**它采用互斥的方式让同一时刻至多只有一个线程能持有【对象锁】，其他线程再想获取这个【对象锁】时就会阻塞住**。这样就能保证拥有锁的线程可以安全的执行临界区内的代码，不用担心线程上下文切换
- 注意：java中的互斥和同步都可以采用synchronized关键字来完成，但是还有区别的
  - 互斥是保证临界区竞态条件发生，**同一时刻只能有一个线程执行临界区代码**
  - 同步是由于线程执行的**先后顺序不同**，需要一个线程等待其他线程运行到某个点



### 2.2 语法

- 比如，现在有一个线程1，要访问临界区，同时给一个对象上了一把锁
- 线程2现在想访问临界区，想获取这个对象，发现有锁，就会阻塞(blocked)
- 线程1访问结束后，再会唤醒线程2，让其执行这个访问操作

~~~java
synchronized(对象) { // 线程1，线程2(blocked阻塞)
	// 临界区：受保护的代码
}
~~~

- 解决上述问题

~~~java
package com.example.two;

import lombok.extern.slf4j.Slf4j;

@Slf4j
public class Test1 {

    static int count = 0;
    static Object lock = new Object();//锁

    public static void main(String[] args) throws InterruptedException {
        Thread t1 = new Thread(() -> {
            for (int i = 0; i < 5000; i++) {
                synchronized (lock) {   // 给临界区上锁
                    count++;
                }
            }
        }, "t1");

        Thread t2 = new Thread(() -> {
            for (int i = 0; i < 5000; i++) {
                synchronized (lock) {   // 给临界区上锁
                    count--;
                }
            }
        }, "t2");

        t1.start();
        t2.start();
        t1.join();
        t2.join();
        log.debug("count = {}", count);
    }
}

// 2023-08-15 10:57:52 [main] [com.example.two.Test1] [DEBUG] - count = 0
~~~



### 2.3 理解

![synchornized类比理解](C:\Users\lzy\Desktop\work\Java高级\JUC并发编程\图片\synchornized类比理解.png)

- 用图来表示
  - 线程2先获取锁，然后执行自减，然后上下文切换，并没有将数据写回
  - 线程1尝试获取锁，但是被阻塞，于是上下文切换
  - 线程2再分到时间片，继续刚刚的操作，写回数据，并释放锁
  - 唤醒线程1，拿到锁，并执行自增操作，完事后释放锁

![synchornized图解](C:\Users\lzy\Desktop\work\Java高级\JUC并发编程\图片\synchornized图解.png)



### 2.4 总结

- **synchronized**实际上**利用对象锁保证了临界区代码的原子性**，临界区内的代码在外界看来是不可分割的，不会被线程切换所打断
- **原则上：**
  - 锁对象建议使用**共享资源**
  - 在实例方法中使用**this** 作为锁对象，锁住的 this 正好是共享资源
  - 在静态方法中使用**类名 .class 字节码**作为锁对象，因为静态成员属于类，被所有实例对象共享，所以需要**锁住类**



### 2.5 思考

- 如果把synchronized(obj)放在for循环的外面, 如何理解?
  - for 循环也是一个原子操作，表现出**原子性**。即：5000次循环会产生20000条指令，这20000条指令是一个整体
- 如果t1.synchronized(obj1)，而 t2.synchronized(obj2)会怎么运行?
  - 因为 t1，t2 拿到不是同一把对象锁，会出现线程安全问题 – **必须要是同一把对象锁。**
- 如果t1.synchronized(obj) 而 t2 没有加会怎么样 ?
  - 因为 t2 没有加锁，所以 t2，不需要获取t1的锁，直接就可以执行下面的代码，仍然会出现线程安全问题



### 2.6 面向对象改进

- 在实例方法中使用**this** 作为锁对象，锁住的 this 正好是共享资源

~~~java
package com.example.two;

import lombok.extern.slf4j.Slf4j;

@Slf4j
public class Test2 {
    public static void main(String[] args) throws InterruptedException {
        Room room = new Room();
        Thread t1 = new Thread(() -> {
            for (int i = 0; i < 5000; i++) {
                room.increment();
            }
        }, "t1");

        Thread t2 = new Thread(() -> {
            for (int i = 0; i < 5000; i++) {
                room.decrement();
            }
        }, "t2");

        t1.start();
        t2.start();
        t1.join();
        t2.join();
        log.debug("count = {}", room.getCount());
    }
}

class Room {
    private int count = 0;

    public void increment() {
        synchronized (this) {
            count++;
        }
    }

    public void decrement() {
        synchronized (this) {
            count--;
        }
    }

    public int getCount() {
        return count;
    }
}
// 打印为：0
~~~



## 3、方法上的synchronized

- 锁对象建议使用**共享资源**
- 在实例方法中使用**this** 作为锁对象，锁住的 this 正好是共享资源

~~~java
class Test {
    public synchronized void test() {
        
    }
}
// 等价于：在实例方法中使用this作为锁对象，锁住的this正好是共享资源(类的实例对象)
class Test {
    public void test() {
        synchronized(this) {
            
        }
    }
}
~~~

- 在静态方法中使用**类名 .class 字节码**作为锁对象，因为静态成员属于类，被所有实例对象共享，所以需要**锁住类**

~~~java
class Test {	
    public synchronized static void test() {
        
    }
}
// 等价于：在静态方法中使用类名.class 字节码作为锁对象，因为静态成员属于类，被所有实例对象共享，所以需要锁住类
class Test {
    public static void test() {
        synchronized(Test.class) {
            
        }
    }
}
~~~

- 没有加synchronized的方法就好比不遵守规则的人，不去老实排队（好比翻窗户进去的）




## 4、线程八锁

- **其实就是考察synchronized锁住的是哪个对象**
- 情况1：**12**或**21**
  - Number类中成员方法a和b都加了synchronized关键字，锁住的是this，即Number的实例对象：number
  - 所以锁住的是同一个对象，会有互斥效果
  - 所以看cpu调度器先调度哪个，就会出现哪种情况，12或者21


~~~java
package com.example.two;

import lombok.extern.slf4j.Slf4j;

@Slf4j
public class Test3 {
    public static void main(String[] args) {
        Number number = new Number();
        new Thread(() -> {
            log.debug("begin");
            number.a();
        }).start();
        new Thread(() -> {
            log.debug("begin");
            number.b();
        }).start();
    }
}

@Slf4j
class Number {
    public synchronized void a() {
        log.debug("1");
    }

    public synchronized void b() {
        log.debug("2");
    }
}
// 2023-08-17 13:17:07 [Thread-0] [com.example.two.Test3] [DEBUG] - begin
// 2023-08-17 13:17:07 [Thread-1] [com.example.two.Test3] [DEBUG] - begin
// 2023-08-17 13:17:07 [Thread-0] [com.example.two.Number] [DEBUG] - 1
// 2023-08-17 13:17:07 [Thread-1] [com.example.two.Number] [DEBUG] - 2
~~~

- 情况2：**1s_12**或**2_1s_1**
  - sleep不会释放锁，且二者锁住的都是Number的实例对象：number，所以是互斥的
  - 所以看先后，如果是先调度t1，就是睡眠1s，然后打印12
  - 如果是先调度2，就先打印2，然后1s后打印1

~~~java
package com.example.two;

import lombok.extern.slf4j.Slf4j;

@Slf4j
public class Test3 {
    public static void main(String[] args) {
        Number number = new Number();
        new Thread(() -> {
            log.debug("begin");
            number.a();
        }).start();
        new Thread(() -> {
            log.debug("begin");
            number.b();
        }).start();
    }
}

@Slf4j
class Number {
    public synchronized void a() {
        try {
            Thread.sleep(1000);
            log.debug("1");
        } catch (InterruptedException exception) {
            exception.printStackTrace();
        }
    }

    public synchronized void b() {
        log.debug("2");
    }
}
// 2023-08-17 13:25:00 [Thread-0] [com.example.two.Test3] [DEBUG] - begin
// 2023-08-17 13:25:00 [Thread-1] [com.example.two.Test3] [DEBUG] - begin
// 2023-08-17 13:25:01 [Thread-0] [com.example.two.Number] [DEBUG] - 1
// 2023-08-17 13:25:01 [Thread-1] [com.example.two.Number] [DEBUG] - 2
~~~

- 情况3：**23_1s_1**或**32_1s_1**或**3_1s_12**
  - 线程1和线程2对同一个对象上锁，即number实例对象，线程3没有上锁，所以12互斥，3不互斥，3跟12并行执行
  - 当先调度1时，睡眠1s，就让给3了，然后1上锁了，3执行完必须等待1执行完，再执行2，所以3_1s_12
  - 当不先调度1时，就是23随机，当2执行完，才能执行1，所以是23_1s_1或32_1s_1

```java
package com.example.two;

import lombok.extern.slf4j.Slf4j;

@Slf4j
public class Test3 {
    public static void main(String[] args) {
        Number number = new Number();
        new Thread(() -> {
            log.debug("begin");
            number.a();
        }).start();
        new Thread(() -> {
            log.debug("begin");
            number.b();
        }).start();
        new Thread(() -> {
            log.debug("begin");
            number.c();
        }).start();
    }
}

@Slf4j
class Number {
    public synchronized void a() {
        try {
            Thread.sleep(1000);
            log.debug("1");
        } catch (InterruptedException exception) {
            exception.printStackTrace();
        }
    }

    public synchronized void b() {
        log.debug("2");
    }

    public void c() {
        log.debug("3");
    }
}
// 2023-08-17 13:51:03 [Thread-0] [com.example.two.Test3] [DEBUG] - begin
// 2023-08-17 13:51:03 [Thread-2] [com.example.two.Test3] [DEBUG] - begin
// 2023-08-17 13:51:03 [Thread-1] [com.example.two.Test3] [DEBUG] - begin
// 2023-08-17 13:51:03 [Thread-2] [com.example.two.Number] [DEBUG] - 3
// 2023-08-17 13:51:04 [Thread-0] [com.example.two.Number] [DEBUG] - 1
// 2023-08-17 13:51:04 [Thread-1] [com.example.two.Number] [DEBUG] - 2
```

- 情况4：**2_1s_1**
  - 线程1和线程2锁的不是同一个对象，一个锁的是number1，一个是number2，所以不会出现互斥的情况
  - 所以只有一种情况，先打印2，1s后再打印1

~~~java
package com.example.two;

import lombok.extern.slf4j.Slf4j;

@Slf4j
public class Test3 {
    public static void main(String[] args) {
        Number number1 = new Number();
        Number number2 = new Number();
        new Thread(() -> {
            log.debug("begin");
            number1.a();
        }).start();
        new Thread(() -> {
            log.debug("begin");
            number2.b();
        }).start();

    }
}

@Slf4j
class Number {
    public synchronized void a() {
        try {
            Thread.sleep(1000);
            log.debug("1");
        } catch (InterruptedException exception) {
            exception.printStackTrace();
        }
    }

    public synchronized void b() {
        log.debug("2");
    }
}
// 2023-08-17 17:53:10 [Thread-0] [com.example.two.Test3] [DEBUG] - begin
// 2023-08-17 17:53:10 [Thread-1] [com.example.two.Test3] [DEBUG] - begin
// 2023-08-17 17:53:10 [Thread-1] [com.example.two.Number] [DEBUG] - 2
// 2023-08-17 17:53:11 [Thread-0] [com.example.two.Number] [DEBUG] - 1
~~~

- 情况5：**2_1s_1**
  - 线程1和线程2锁的不是同一个对象，一个锁的是number实例对象，一个是Number类对象，所以不会出现互斥的情况
  - 所以只有一种情况，先打印2，1s后再打印1

~~~java
package com.example.two;

import lombok.extern.slf4j.Slf4j;

@Slf4j
public class Test3 {
    public static void main(String[] args) {
        Number number1 = new Number();
        new Thread(() -> {
            log.debug("begin");
            number1.a();
        }).start();
        new Thread(() -> {
            log.debug("begin");
            number1.b();
        }).start();

    }
}

@Slf4j
class Number {
    public static synchronized void a() {
        try {
            Thread.sleep(1000);
            log.debug("1");
        } catch (InterruptedException exception) {
            exception.printStackTrace();
        }
    }

    public synchronized void b() {
        log.debug("2");
    }
}
// 2023-08-17 18:12:15 [Thread-0] [com.example.two.Test3] [DEBUG] - begin
// 2023-08-17 18:12:15 [Thread-1] [com.example.two.Test3] [DEBUG] - begin
// 2023-08-17 18:12:15 [Thread-1] [com.example.two.Number] [DEBUG] - 2
// 2023-08-17 18:12:16 [Thread-0] [com.example.two.Number] [DEBUG] - 1
~~~

- 情况6：**1s_12**或**2_1s_1**
  - Number类中成员方法a和b都加了synchronized关键字，锁住的是Number类对象
  - 所以锁住的是同一个对象，会有互斥效果
  - 所以看cpu调度器先调度哪个，就会出现哪种情况，12或者21

~~~java
package com.example.two;

import lombok.extern.slf4j.Slf4j;

@Slf4j
public class Test3 {
    public static void main(String[] args) {
        Number number = new Number();
        new Thread(() -> {
            log.debug("begin");
            number.a();
        }).start();
        new Thread(() -> {
            log.debug("begin");
            number.b();
        }).start();

    }
}

@Slf4j
class Number {
    public static synchronized void a() {
        try {
            Thread.sleep(1000);
            log.debug("1");
        } catch (InterruptedException exception) {
            exception.printStackTrace();
        }
    }

    public static synchronized void b() {
        log.debug("2");
    }
}
// 2023-08-17 18:36:33 [Thread-0] [com.example.two.Test3] [DEBUG] - begin
// 2023-08-17 18:36:33 [Thread-1] [com.example.two.Test3] [DEBUG] - begin
// 2023-08-17 18:36:34 [Thread-0] [com.example.two.Number] [DEBUG] - 1
// 2023-08-17 18:36:34 [Thread-1] [com.example.two.Number] [DEBUG] - 2
~~~

- 情况7：**2_1s_1**
  - 线程1和线程2锁的不是同一个对象，一个锁的是number实例对象，一个是Number类对象，所以不会出现互斥的情况
  - 所以只有一种情况，先打印2，1s后再打印1

~~~java
package com.example.two;

import lombok.extern.slf4j.Slf4j;

@Slf4j
public class Test3 {
    public static void main(String[] args) {
        Number number1 = new Number();
        Number number2 = new Number();
        new Thread(() -> {
            log.debug("begin");
            number1.a();
        }).start();
        new Thread(() -> {
            log.debug("begin");
            number2.b();
        }).start();
    }
}

@Slf4j
class Number {
    public static synchronized void a() {
        try {
            Thread.sleep(1000);
            log.debug("1");
        } catch (InterruptedException exception) {
            exception.printStackTrace();
        }
    }

    public static void b() {
        log.debug("2");
    }
}
// 2023-08-17 18:39:17 [Thread-0] [com.example.two.Test3] [DEBUG] - begin
// 2023-08-17 18:39:17 [Thread-1] [com.example.two.Test3] [DEBUG] - begin
// 2023-08-17 18:39:17 [Thread-1] [com.example.two.Number] [DEBUG] - 2
// 2023-08-17 18:39:18 [Thread-0] [com.example.two.Number] [DEBUG] - 1
~~~

- 情况8：**1s_12**或**2_1s_1**
  - 俩锁的都是类对象，类对象只有一个，所以是同一个
  - 所以要么先**1s_12**或**2_1s_1**

~~~java
package com.example.two;

import lombok.extern.slf4j.Slf4j;

@Slf4j
public class Test3 {
    public static void main(String[] args) {
        Number number1 = new Number();
        Number number2 = new Number();
        new Thread(() -> {
            log.debug("begin");
            number1.a();
        }).start();
        new Thread(() -> {
            log.debug("begin");
            number2.b();
        }).start();
    }
}

@Slf4j
class Number {
    public static synchronized void a() {
        try {
            Thread.sleep(1000);
            log.debug("1");
        } catch (InterruptedException exception) {
            exception.printStackTrace();
        }
    }

    public static synchronized void b() {
        log.debug("2");
    }
}
// 2023-08-17 18:40:44 [Thread-0] [com.example.two.Test3] [DEBUG] - begin
// 2023-08-17 18:40:44 [Thread-1] [com.example.two.Test3] [DEBUG] - begin
// 2023-08-17 18:40:45 [Thread-0] [com.example.two.Number] [DEBUG] - 1
// 2023-08-17 18:40:45 [Thread-1] [com.example.two.Number] [DEBUG] - 2
~~~



## 5、变量的线程安全分析

### 5.1 成员变量和静态变量是否线程安全

- 如果他们没有共享，则线程安全
- 如果被共享了，根据它们的状态是否能够改变，分为两种
  - 如果只有读操作，则线程安全
  - 如果有读写操作，则这段代码是临界区，需要考虑线程安全



### 5.2 局部变量是否线程安全

- 局部变量是线程安全的
- 但局部变量引用的对象则未必
  - 如果该对象没有逃离方法的作用范围，则是线程安全的
  - 如果该对象逃离方法的作用范围，则需要考虑线程安全，比如return对象



### 5.3 局部变量线程安全分析

~~~java
public static void test() {
    int i = 10;
    i++;
}
~~~

- 每个线程调用test()方法时局部变量i，**会在每个线程的栈帧内存中被创建多份**，因此不存在共享

![局部变量线程安全](C:\Users\lzy\Desktop\work\Java高级\JUC并发编程\图片\局部变量线程安全.png)



### 5.4 局部变量引用线程安全分析

- 如果**局部变量引用的对象逃离方法的范围**，那么**要考虑线程安全问题**
- 循环创建了2个线程，在线程体里面都调用了method1方法，在method1方法中又循环调用了100次method2,method3方法。方法2,3都使用到了成员变量arrayList，此时的问题就是: 1个线程它会循环调用100次方法2和3, 一共有2个线程，此时2个线程操作的共享资源就是arrayList成员变量，而且还进行了读写操作， 必然会造成线程不安全的问题

~~~java
package com.example.two;

import java.util.ArrayList;

public class Test4 {
    public static void main(String[] args) {
        UnsafeTest unsafeTest = new UnsafeTest();
        for (int i = 0; i < 2; i++){
            new Thread(()->{
                unsafeTest.method1();
            },"线程"+i).start();
        }
    }
}

class UnsafeTest{
    ArrayList<String> arrayList = new ArrayList<>();
    public void method1(){
        for (int i = 0; i < 100; i++) {
            method2();
            method3();
        }
    }
    private void method2() {
        arrayList.add("1");
    }
    private void method3() {
        arrayList.remove(0);
    }
}

// Exception in thread "线程1" java.lang.IndexOutOfBoundsException: Index: 0, Size: 0
~~~

- 分析
  - 无论哪个线程中的method2引用的都是同一个对象中的list成员变量
  - method3和method2分析相同

![成员变量引用安全分析](C:\Users\lzy\Desktop\work\Java高级\JUC并发编程\图片\成员变量引用安全分析.png)、

- 改进
  - 可以将list修改成局部变量，**局部变量存放在栈帧中，栈帧又存放在虚拟机栈中，虚拟机栈是作为线程私有的**
  - 因为method1方法，将arrayList传给method2,method3方法，**此时他们三个方法共享这同一个arrayList**，此时不会被其他线程访问到，所以**不会出现线程安全问题**，因为**这三个方法使用的同一个线程**。
  - 在外部，创建了100个线程，每个线程都会调用method1方法，**然后都会再重新创建一个新的arrayList对象**，这个新对象再传递给method2,method3方法

~~~java
class SafeTest {
    public void method1() {
        ArrayList<String> arrayList = new ArrayList<>();
        for (int i = 0; i < 100; i++) {
            method2(arrayList);
            method3(arrayList);
        }
    }

    private void method2(List<String> arrayList) {
        arrayList.add("1");
    }

    private void method3(List<String> arrayList) {
        arrayList.remove(0);
    }
}
~~~

![局部成员变量](C:\Users\lzy\Desktop\work\Java高级\JUC并发编程\图片\局部成员变量.png)



### 5.5 private和public修饰

- 方法访问修饰符带来的思考： 如果把method2和method3 的方法修改为public 会不会导致线程安全问题；分情况：
- 情况1：有其它线程调用 method2 和 method3
  - **只修改为public修饰，此时不会出现线程安全的问题**，即使线程2调用method2、3方法，给2、3方法传过来的list对象也是线程2调用method1方法时，传递给method2、3的list对象，不可能是线程1调用method1方法传的对象。
- 情况2：在情况1 的基础上，为ThreadSafe 类添加子类，子类覆盖method2 或 method3方法，即如下所示： 从这个例子可以看出private或 final提供【安全】的意义所在，请体会开闭原则中的【闭】。

~~~java
/**
 * 光修改public修饰，线程1调用method1，它会在method1中创建一个新的list
 * 即使现在线程2调用method2，那么它需要传入一个新的list当作入参，咋都不可能是method1中创建的list
 */
class ThreadSafe {
    public final void method1(int loopNumber) {
        ArrayList<String> list = new ArrayList<>();
        for (int i = 0; i < loopNumber; i++) {
            method2(list);
            method3(list);
        }
    }
    private void method2(ArrayList<String> list) {
        list.add("1");
    }
    public void method3(ArrayList<String> list) {
        list.remove(0);
    }
}

/**
 * 但是如果现在新建了一个子类继承，重写了这个方法
 * 线程1调用了method1，method1里面又调用了method3，传入的就是method1声明的list，而子类方法重新开了个线程操作list，操作的就是和method1里面的list，所以会出现线程安全问题
 */
class ThreadSafeSubClass extends ThreadSafe{
    @Override
    public void method3(ArrayList<String> list) {
        new Thread(() -> {
            list.remove(0);
        }).start();
    }
}
~~~

- 总结
  - **如果改为public，此时子类可以重写父类的方法，在子类中开线程来操作list对象，此时就会出现线程安全问题：子类和父类共享了list对象**
  - **如果改为private，子类就不能重写父类的私有方法, 也就不会出现线程安全问题; 所以所private修饰符是可以避免线程安全问题.**
  - **所以如果不想子类重写父类的方法的时候, 我们可以将父类中的方法设置为private, final修饰的方法, 此时子类就无法影响父类中的方法了!**





### 5.6 常见线程安全类

- String
- Integer
- StringBuffer
- Random
- Vector
- Hashtable
- java.util.concurrent包下的类(JUC)
- 这里所说的线程安全是指：**多个线程调用它们同一个实例的某个方法时，是线程安全的**

~~~java
// 以下是线程安全的
Hashtable table = new Hashtable();

new Thread(()->{
	// put方法增加了synchronized
 	table.put("key", "value1");
}).start();

new Thread(()->{
 	table.put("key", "value2");
}).start();
~~~

- 也可以理解为
  - 它们的每个方法都是原子的（方法都被加上了synchronized）
  - 但是**注意**多个方法组合就不是原子的，**所以可能会出现线程安全问题**



### 5.7 组合出现线程安全问题

- 比如下面Hashtable实例对象，get和put操作都是原子的，带着synchronized
- 但是组合起来用的时候就会出现线程安全问题

~~~java
Hashtable table = new Hashtable();
// 线程1，线程2
if( table.get("key") == null) {
 table.put("key", value);
}
~~~

- 比如线程1，先get，发现是null
- 这时候发生上下文切换，然后线程2，get也是null，然后就执行put
- 然后再切换到线程1，这时候执行put，就会把线程2put的值进行覆盖
- 发生线程安全问题

![组合线程不安全](C:\Users\lzy\Desktop\work\Java高级\JUC并发编程\图片\组合线程不安全.png)



### 5.8 不可变类线程安全性

- Integer、String等都是**不可变类**，因为其**内部的状态不可以改变**，因此**它们的方法都是线程安全的**, **都被final修饰**，**不能被继承**
- 虽然String 有 replace，substring 等方法【可以】改变值，其实调用**这些方法返回的已经是一个新创建的对象了** (在字符串常量池中当修改了String的值，它不会再原有的基础上修改，而是会重新开辟一个空间来存储)。



### 5.9 实例分析

- 实例1：**Servlet运行在Tomcat环境下并只有一个实例**，因此会被Tomcat的多个线程共享使用，因此存在成员变量的共享问题

~~~java
public class MyServlet extends HttpServlet {
	 // 是否安全？  否：HashMap不是线程安全的，HashTable是
	 Map<String,Object> map = new HashMap<>();
	 // 是否安全？  是: String 为不可变类，线程安全
	 String S1 = "...";
	 // 是否安全？ 是
	 final String S2 = "...";
	 // 是否安全？ 否：不是常见的线程安全类
	 Date D1 = new Date();
	 // 是否安全？  否：引用值D2不可变，但是日期里面的其它属性比如年月日可变。与字符串的最大区别是Date里面的属性可变。
	 final Date D2 = new Date();
 
	 public void doGet(HttpServletRequest request,HttpServletResponse response) {
	  // 使用上述变量
	 }
}
~~~

- 实例2

~~~java
public class MyServlet extends HttpServlet {
	 // 是否安全    不是：UserServiceImpl只有一份，会有多个线程共享
	 private UserService userService = new UserServiceImpl();
	 
	 public void doGet(HttpServletRequest request, HttpServletResponse response) {
	 	userService.update(...);
	 }
}

public class UserServiceImpl implements UserService {
	 // 是否安全     不是：共享资源
	 private int count = 0;
	 
	 public void update() {
         // 临界区
	 	count++;
	 }
}
~~~

- 实例3

~~~java
@Aspect
@Component 
public class MyAspect {
    // 是否安全？不安全, 因为MyAspect是单例的，所以这个变量是会被共享的
    // 解决：使用@Aroud环绕通知，把start设置为局部变量
    private long start = 0L;

    @Before("execution(* *(..))")
    public void before() {
        start = System.nanoTime();
    }

    @After("execution(* *(..))")
    public void after() {
        long end = System.nanoTime();
        System.out.println("cost time:" + (end-start));
    }
}
~~~

- 实例4：
  - 此例是典型的三层模型调用，MyServlet UserServiceImpl UserDaoImpl类都只有一个实例，**UserDaoImpl类中没有成员变量，update方法里的变量引用的对象不是线程共享的**，所以是线程安全的；**UserServiceImpl类中只有一个线程安全的UserDaoImpl类的实例，那么UserServiceImpl类也是线程安全的，同理 MyServlet也是线程安全的**
  - Servlet调用Service, Service调用Dao这三个方法使用的是同一个线程。
  - **跟那个不可变有异曲同工之妙**

~~~java
public class MyServlet extends HttpServlet {
	 // 是否安全    是：UserService不可变，虽然有一个成员变量,
	 			// 但是是私有的, 没有地方修改它
	 private UserService userService = new UserServiceImpl();
	 
	 public void doGet(HttpServletRequest request, HttpServletResponse response) {
	 	userService.update(...);
	 }
}

public class UserServiceImpl implements UserService {
	 // 是否安全     是：Dao不可变, 其没有成员变量
	 private UserDao userDao = new UserDaoImpl();
	 
	 public void update() {
	 	userDao.update();
	 }
}

public class UserDaoImpl implements UserDao { 
	 // 是否安全   是：没有成员变量，无法修改其状态和属性
	 public void update() {
	 	String sql = "update user set password = ? where username = ?";
	 	// 是否安全   是：不同线程创建的conn各不相同，都在各自的栈内存中
	 	try (Connection conn = DriverManager.getConnection("","","")){
	 	// ...
	 	} catch (Exception e) {
	 	// ...
	 	}
	 }
}
~~~

- 实例5：UserDaoImpl类中有成员变量，那么**多个线程可以对成员变量conn 同时进行操作**，故是不安全

~~~java
public class MyServlet extends HttpServlet {
    // 是否安全
    private UserService userService = new UserServiceImpl();

    public void doGet(HttpServletRequest request, HttpServletResponse response) {
        userService.update(...);
    }
}

public class UserServiceImpl implements UserService {
    // 是否安全
    private UserDao userDao = new UserDaoImpl();
    public void update() {
       userDao.update();
    }
}

public class UserDaoImpl implements UserDao {
    // 是否安全: 不安全; 当多个线程,共享conn, 一个线程拿到conn,刚创建一个连接赋值给conn, 此时另一个线程进来了, 直接将conn.close
    // 另一个线程恢复了, 拿到conn干事情, 此时conn都被关闭了, 出现了问题
    private Connection conn = null;
    public void update() throws SQLException {
        String sql = "update user set password = ? where username = ?";
        conn = DriverManager.getConnection("","","");
        // ...
        conn.close();
    }
}
~~~

- 实例6：UserServiceImpl类的**update方法中UserDao**是作为**局部变量**存在的，所以**每个线程访问的时候都会新建有一个UserDao对象**，新建的对象是线程独有的，所以是**线程安全的**

~~~java
public class MyServlet extends HttpServlet {
    // 是否安全
    private UserService userService = new UserServiceImpl();
    public void doGet(HttpServletRequest request, HttpServletResponse response) {
        userService.update(...);
    }
}
public class UserServiceImpl implements UserService {
    public void update() {
    	// 作为局部变量，每个线程都会新建，所以安全
        UserDao userDao = new UserDaoImpl();
        userDao.update();
    }
}
public class UserDaoImpl implements UserDao {
    // 是否安全
    private Connection = null;
    public void update() throws SQLException {
        String sql = "update user set password = ? where username = ?";
        conn = DriverManager.getConnection("","","");
        // ...
        conn.close();
    }
}
~~~

- 实例7：**私有变量sdf**被暴露出去了, **发生了逃逸**

```java
public abstract class Test {
    public void bar() {
        // 是否安全：不安全，因为它把sdf暴露给了抽象方法，抽象方法会重写，就可能会出现问题
        SimpleDateFormat sdf = new SimpleDateFormat("yyyy-MM-dd HH:mm:ss");
        foo(sdf);
    }
    public abstract foo(SimpleDateFormat sdf);
    public static void main(String[] args) {
        new Test().bar();
    }
}

// 其中foo的行为是不确定的，可能导致不安全的发生，被称之为外星方法，因为foo方法可以被重写，导致线程不安全。
// 在String类中就考虑到了这一点，String类是final的，子类不能重写它的方法。
public void foo(SimpleDateFormat sdf) {
    String dateStr = "1999-10-11 00:00:00";
    for (int i = 0; i < 20; i++) {
        new Thread(() -> {
            try {
                sdf.parse(dateStr);
            } catch (ParseException e) {
                e.printStackTrace();
            }
        }).start();
    }
}
```



## 6、习题

### 6.1 卖票练习

~~~java
package com.example.two;

import lombok.extern.slf4j.Slf4j;

import java.util.ArrayList;
import java.util.List;
import java.util.Random;
import java.util.Vector;

/**
 * 卖票
 */
@Slf4j
public class Test6 {
    // 模拟多人售票
    public static void main(String[] args) throws InterruptedException {
        TicketWindow ticketWindow = new TicketWindow(1000);

        // 卖出的票数统计
        List<Integer> amountList = new Vector<>();
        List<Thread> threadList = new ArrayList<>();

        for (int i = 0; i < 2000; i++) {
            Thread thread = new Thread(() -> {
                // 买票
                int amount = ticketWindow.sell(randomAmount());
                amountList.add(amount);
            });
            threadList.add(thread);
            thread.start();
        }

        // 等待所有线程执行完毕
        for (Thread thread : threadList) {
            thread.join();
        }

        // 统计剩余票数
        log.debug("剩余的票数：{}", ticketWindow.getCount());
        // 统计卖出的票数
        log.debug("卖出的票数：{}", amountList.stream().mapToInt(i -> i).sum());
    }

    // Random线程安全
    static Random random = new Random();

    // 随机1~5
    public static int randomAmount() {
        return random.nextInt(5) + 1;
    }
}



/**
 * 售票窗口
 */
class TicketWindow {
    private int count;

    public TicketWindow(int count) {
        this.count = count;
    }

    // 获取余票数量
    public int getCount() {
        return count;
    }

    // 售票
    public int sell(int amount) {
        if (this.count >= amount) {
            this.count -= amount;
            return amount;
        } else {
            return 0;
        }
    }
}


// 执行结果
// 2023-12-27 21:00:40 [main] [com.example.two.Test6] [DEBUG] - 剩余的票数：0
// 2023-12-27 21:00:40 [main] [com.example.two.Test6] [DEBUG] - 卖出的票数：1006
~~~

- 结果分析：

  - 因为TicketWindow创建的对象是公用的，所以处于临界区
  - 然后TicketWindow.sell()方法，会对TicketWindow对象的成员变量搞出读写操作
  - 所以就会出现线程不安全的问题

- 解决方案

  - 在sell()方法上加上synchronized关键字，这样就是对TicketWindow对象加锁
  - 多个线程访问的时候，都必须等待锁的释放才能进行读写操作，从而解决线程安全问题

  ~~~java
  // 加上synchronized关键字后
  // 2023-12-27 21:14:22 [main] [com.example.two.Test6] [DEBUG] - 剩余的票数：0
  // 2023-12-27 21:14:22 [main] [com.example.two.Test6] [DEBUG] - 卖出的票数：1000
  ~~~





### 6.2 转账练习

```java
package com.example.two;

import lombok.extern.slf4j.Slf4j;

import java.util.Random;

/**
 * 转账联系
 */
@Slf4j
public class Test7 {
    public static void main(String[] args) throws InterruptedException {
        Account a = new Account(1000);
        Account b = new Account(1000);
        Thread thread1 = new Thread(() -> {
            for (int i = 0; i < 2000; i++) {
                a.transfer(b, randomAmount());
            }
        }, "t1");
        Thread thread2 = new Thread(() -> {
            for (int i = 0; i < 2000; i++) {
                b.transfer(a, randomAmount());
            }
        }, "t2");
        thread1.start();
        thread2.start();
        thread1.join();
        thread2.join();
        // 查看2000次转账的总金额
        log.debug("总金额：{}", a.getMoney() + b.getMoney());
    }

    // Random线程安全
    static Random random = new Random();

    // 随机1~100
    public static int randomAmount() {
        return random.nextInt(100) + 1;
    }

}


// 账户
class Account {
   private int money;

    public int getMoney() {
        return money;
    }

    public void setMoney(int money) {
        this.money = money;
    }

    public Account(int money) {
        this.money = money;
    }

    // 转账
    public void transfer(Account target, int amount) {
        if (this.money >= amount) {
            this.setMoney(this.getMoney() - amount);
            target.setMoney(target.getMoney() + amount);
        }
    }
}

// 执行结果
// 2023-12-27 21:28:16 [main] [com.example.two.Test7] [DEBUG] - 总金额：6677
```

- 结果分析

  - Account.transfer方法，会让target对象的money变量和自己的money变量产生读写操作
  - 所以是线程不安全的

- 如果在方法上加上synchronized关键字可行码？**不可行**

  - 原因：临界区是a和b变量两个，调用一次方法，会有两个对象的读写
  - 如果加上之后，就相当于单独给a或者b加了锁，那么调用对应的方法时，锁的不是同一个对象，没用

- 真正的解决方案：在类上加锁，给类加锁，这样让两个对象的money加了同一个锁，让整体变成原子性

  ~~~java
  // 转账
  public void transfer(Account target, int amount) {
  	synchronized (Account.class) {
  		if (this.money >= amount) {
  			this.setMoney(this.getMoney() - amount);
  			target.setMoney(target.getMoney() + amount);
  		} 
  	}
  }
  // 结果
  // 2023-12-27 21:34:17 [main] [com.example.two.Test7] [DEBUG] - 总金额：2000
  ~~~





## 7、Monitor概念

### 7.1 对象头

- 通常情况下，创建的对象都由两部分组成，一部分是对象头，一部分就是它的那些成员变量
- 以普通32位虚拟机为例
  - 普通对象：
    - 对象头为8个字节32位
    - Klass Word：是一个指针，指向这个对象从属的类别（即：通过这个可以知道这个对象是什么类型）
    - Mark Word：一些各种各样的信息
    - Integer：8(对象头的8个) + 4(value的4个) = 12个字节  int：4个字节
  - 数组对象：
    - 多一个数组长度的4个字节

![对象头](C:\Users\lzy\Desktop\work\Java高级\JUC并发编程\图片\对象头.png)

![mark word结构](C:\Users\lzy\Desktop\work\Java高级\JUC并发编程\图片\mark word结构.png)

 

### 7.2 Monitor

- Monitor被翻译成**监视器**或**管程**
- 每个Java对象都可以关联一个Monitor对象，如果使用synchronized给对象上锁（重量级）之后，该对象头的Mark Word中就被设置指向Monitor对象的指针
- 结构如下

![Monitor结构](C:\Users\lzy\Desktop\work\Java高级\JUC并发编程\图片\Monitor结构.png)

- 过程
  - 刚开始Monitor中Owner为null
  - 当Thread-2执行synchronized(obj)就会将Monitor的所有者Owner置为Thread-2，Monitor中只能有一个Owner
  - 在Thread-2上锁过程中，如果Thread-3，Thread-4，Thread-5也来执行synchronized(obj)，就会进入EntryList(**BLOCKED状态**)
  - Thread-2执行完同步代码块的内容，然后唤醒EntryList中等待的线程来竞争锁，竞争是非公平的
  - 图中WaitSet中的Thread-0，Thread-1是之前获得过锁，但条件不满足进入WAITING状态的线程
- 注意：
  - synchronized必须是进入同一个对象的monitor才有上述效果
  - 不加synchronized的对象不会关联监视器，不遵从上述规则



### 7.3 原理之字节码解释

~~~java
static final Object lock = new Object();
static int counter = 0;

public static void main(String[] args) {
    synchronized(lock) {
        counter++;
    }
}
~~~

- 对应的字节码
  - 正常情况：6~16，执行完，就将lock对象的MarkWord重置，并顺着去唤醒Monitor的EntryList
  - 不正常情况：执行6~16出异常，跳到19，同样跟上面一样释放锁，然后抛出异常

![Monitor字节码解释](C:\Users\lzy\Desktop\work\Java高级\JUC并发编程\图片\Monitor字节码解释.png)



## 8、synchronized进阶

### 8.1 小故事类比

- 故事角色 
  - 老王 - JVM 
  - 小南 - 线程 
  - 小女 - 线程 
  - 房间 - 对象 
  - 房间门上 - 防盗锁 - Monitor 
  - 房间门上 - 小南书包 - 轻量级锁 
  - 房间门上 - 刻上小南大名 - 偏向锁 
  - 批量重刻名 - 一个类的偏向锁撤销到达 20 阈值 
  - 不能刻名字 - 批量撤销该类对象的偏向锁，设置该类不可偏向 


- 小南要使用房间保证计算不被其它人干扰（原子性），最初，他用的是防盗锁，当上下文切换时，锁住门。这样即使他离开了，别人也进不了门，他的工作就是安全的。 


- 但是，很多情况下没人跟他来竞争房间的使用权。小女是要用房间，但使用的时间上是错开的，小南白天用，小女晚上用。每次上锁太麻烦了，有没有更简单的办法呢？ 


- 小南和小女商量了一下，约定不锁门了，而是谁用房间，谁把自己的书包挂在门口，但他们的书包样式都一样，因此每次进门前得翻翻书包，看课本是谁的，如果是自己的，那么就可以进门，这样省的上锁解锁了。万一书包不是自己的，那么就在门外等，并通知对方下次用锁门的方式。 


- 后来，小女回老家了，很长一段时间都不会用这个房间。小南每次还是挂书包，翻书包，虽然比锁门省事了，但仍然觉得麻烦。 


- 于是，小南干脆在门上刻上了自己的名字：【小南专属房间，其它人勿用】，下次来用房间时，只要名字还在，那么说明没人打扰，还是可以安全地使用房间。如果这期间有其它人要用这个房间，那么由使用者将小南刻的名字擦掉，升级为挂书包的方式。 


- 同学们都放假回老家了，小南就膨胀了，在 20 个房间刻上了自己的名字，想进哪个进哪个。后来他自己放假回老家了，这时小女回来了（她也要用这些房间），结果就是得一个个地擦掉小南刻的名字，升级为挂书包的方式。老王觉得这成本有点高，提出了一种批量重刻名的方法，他让小女不用挂书包了，可以直接在门上刻上自己的名字 


- 后来，刻名的现象越来越频繁，老王受不了了：算了，这些房间都不能刻名了，只能挂书包





### 8.2 轻量级锁

- 轻量级锁的使用场景：如果一个对象虽然有多线程要加锁，但加锁的时间是错开的（也就是没有竞争），那么可以使用轻量级锁来优化
- 轻量级锁对使用者是透明的，即语法仍然是 synchronized
- 假设有两个方法同步块，利用同一个对象加锁

~~~java
static final Object obj = new Object();

public static void method1() {
    synchronized( obj ) {
        // 同步块 A
        method2();
    }
}

public static void method2() {
    synchronized( obj ) {
        // 同步块 B
    }
}
~~~

- 过程

  - 创建 锁记录（Lock Record）对象，每个线程的栈帧都会包含一个**锁记录**的结构，内部可以存储锁定对象的Mark Word 

  ![轻量锁1](C:\Users\lzy\Desktop\work\Java高级\JUC并发编程\图片\synchronized\轻量锁1.png)

  - 让锁记录中 Object reference 指向锁对象，并尝试用 cas 替换 Object 的 Mark Word，将 Mark Word 的值存入锁记录（互换）

  ![轻量锁1](C:\Users\lzy\Desktop\work\Java高级\JUC并发编程\图片\synchronized\轻量锁2.png)

  - 如果 cas 替换成功，对象头中存储了 **锁记录地址和状态 00（代表轻量锁）**，表示由该线程给对象加锁，这时图示如下

  ![轻量锁1](C:\Users\lzy\Desktop\work\Java高级\JUC并发编程\图片\synchronized\轻量锁3.png)

  - 如果 cas 失败，有两种情况 


  - - 如果是其它线程已经持有了该 Object 的轻量级锁，这时表明有竞争，进入锁膨胀过程 
    - 如果是自己执行了 synchronized 锁重入，那么再添加一条 Lock Record 作为重入的计数

  ![轻量锁1](C:\Users\lzy\Desktop\work\Java高级\JUC并发编程\图片\synchronized\轻量锁4.png)

  - 当退出 synchronized 代码块（第二个锁解锁时）如果有取值为 null 的锁记录，表示有重入，这时重置锁记录，表示重入计数减一

  ![轻量锁1](C:\Users\lzy\Desktop\work\Java高级\JUC并发编程\图片\synchronized\轻量锁5.png)

  - 当退出 synchronized 代码块（第一个锁解锁时）锁记录的值不为 null，这时使用 cas 将 Mark Word 的值恢复给对象头 


  - - 成功，则解锁成功 
    - 失败，说明轻量级锁进行了锁膨胀或已经升级为重量级锁，进入重量级锁解锁流程

  ​

### 8.3 锁膨胀

-  如果在尝试加轻量级锁的过程中，CAS 操作无法成功，这时一种情况就是有其它线程为此对象加上了轻量级锁（有竞争），**这时需要进行锁膨胀，将轻量级锁变为重量级锁**

~~~java
static Object obj = new Object();
public static void method1() {
    synchronized( obj ) {
        // 同步块
    }
}
~~~

- 过程

  - 当 Thread-1 进行轻量级加锁时，Thread-0 已经对该对象加了轻量级锁

  ![锁膨胀1](C:\Users\lzy\Desktop\work\Java高级\JUC并发编程\图片\synchronized\锁膨胀1.png)

  - 这时 Thread-1 加轻量级锁失败，进入锁膨胀流程 


  - - 即为 Object 对象申请 Monitor 锁，让 Object 指向重量级锁地址 
    - 然后自己进入 Monitor 的 EntryList BLOCKED

  ![锁膨胀1](C:\Users\lzy\Desktop\work\Java高级\JUC并发编程\图片\synchronized\锁膨胀2.png)

  - 当 Thread-0 退出同步块解锁时，使用 cas 将 Mark Word 的值恢复给对象头，失败。这时会进入重量级解锁流程，即按照 Monitor 地址找到 Monitor 对象，设置 Owner 为 null，唤醒 EntryList 中 BLOCKED 线程





### 8.4 自旋优化

- 重量级锁竞争的时候，还可以使用自旋**(循环尝试获取重量级锁)**来进行优化，如果当前线程自旋成功（即这时候持锁线程已经退出了同步块，释放了锁），这时当前线程就可以避免阻塞。 (进入阻塞再恢复,会发生上下文切换,比较耗费性能)


- 自旋重试成功的情况

![自旋优化1](C:\Users\lzy\Desktop\work\Java高级\JUC并发编程\图片\synchronized\自旋优化1.png)

- 自旋重试失败的情况

![自旋优化2](C:\Users\lzy\Desktop\work\Java高级\JUC并发编程\图片\synchronized\自旋优化2.png)

- **自旋会占用 CPU 时间，单核 CPU 自旋就是浪费，多核 CPU 自旋才能发挥优势。** 
- 在 Java 6 之后自旋锁是自适应的，比如对象刚刚的一次自旋操作成功过，那么认为这次自旋成功的可能性会高，就多自旋几次；反之，就少自旋甚至不自旋，总之，比较智能。 
- Java 7 之后不能控制是否开启自旋功能





### 8.5 偏向锁

- **轻量级锁在没有竞争时（就自己这个线程），每次重入仍然需要执行 CAS 操作。** 


- Java 6 中引入了偏向锁来做进一步优化：**只有第一次使用 CAS 将线程 ID 设置到对象的 Mark Word 头，之后发现这个线程 ID 是自己的就表示没有竞争，不用重新 CAS**。以后只要不发生竞争，这个对象就归该线程所有 

~~~java
static final Object obj = new Object();

public static void m1() {
    synchronized( obj ) {
        // 同步块 A
        m2();
    }
}

public static void m2() {
    synchronized( obj ) {
        // 同步块 B
        m3();
    }
}

public static void m3() {
    synchronized( obj ) {
        // 同步块 C
    }
}
~~~

- 上述代码执行状态

  - 轻量锁：每次锁重入都会执行cas操作，即：用锁记录替换markword

    ![偏向锁1](C:\Users\lzy\Desktop\work\Java高级\JUC并发编程\图片\synchronized\偏向锁1.png)

  - 偏向锁：第一次加锁时，用ThreadID替换markword，后续只需要检查ThreadID是不是自己就可以判断

    ![偏向锁1](C:\Users\lzy\Desktop\work\Java高级\JUC并发编程\图片\synchronized\偏向锁2.png)



#### 8.5.1 偏向状态

- 对象头格式

![64位对象头](C:\Users\lzy\Desktop\work\Java高级\JUC并发编程\图片\synchronized\64位对象头.png)

一个对象创建时： 

- 如果开启了偏向锁（默认开启），那么对象创建后，markword 值为 0x05 即**最后3位为 101**，这时它的 thread、epoch、age 都为 0 
- 偏向锁是默认是延迟的，不会在程序启动时立即生效，如果想避免延迟，可以加 VM 参数 **-XX:BiasedLockingStartupDelay=0** 来 **禁用延迟** 
- 如果没有开启偏向锁，那么对象创建后，markword 值为 0x01 即最后 3 位为 001，这时它的 hashcode、age 都为 0，第一次用到 hashcode 时才会赋值



#### 8.5.2 测试延迟性

- 偏向锁是默认是延迟的，不会在程序启动时立即生效
- 如果想避免延迟，可以加 VM 参数 **-XX:BiasedLockingStartupDelay=0** 来 **禁用延迟**

~~~java
public static void main(String[] args) throws IOException {
    Dog d = new Dog();
    ClassLayout classLayout = ClassLayout.parseInstance(d);
 
    log.debug("生效前");
	System.out.println(classLayout.toPrintableSimple(true));
    
    Thread.sleep(4000);
    
    log.debug("生效后");
    System.out.println(classLayout.toPrintableSimple(true));
}
~~~

- 不加VM参数结果
  - 因为默认是有延迟的，所以刚开始没生效，即后三位为001，正常状态
  - 睡眠4s后，偏向锁生效，后三位为101

~~~bash
# 生效前
# 00000000 00000000 00000000 00000000 00000000 00000000 00000000 00000001 
# 生效后
# 00000000 00000000 00000000 00000000 00000000 00000000 00000000 00000101
~~~

- 加上VM参数结果
  - 将延迟关闭，所以刚开始就生效了，所以后三位一直为101

```bash
# 生效前
# 00000000 00000000 00000000 00000000 00000000 00000000 00000000 00000101
# 生效前
# 00000000 00000000 00000000 00000000 00000000 00000000 00000000 00000101
```



#### 8.5.3 测试偏向锁

~~~java
public static void main(String[] args) throws IOException {
    Dog d = new Dog();
    ClassLayout classLayout = ClassLayout.parseInstance(d);
 
    log.debug("前");
	System.out.println(classLayout.toPrintableSimple(true));
    
    synchronized (d) {
    	log.debug("synchronized 中");
        System.out.println(classLayout.toPrintableSimple(true));
    }
    
    log.debug("后");
    System.out.println(classLayout.toPrintableSimple(true));
}
~~~

- 结果分析
  - 刚开始创建对象，把延迟关了，所以偏向锁生效，后三位为101
  - 上锁中，就是把线程ID给存入markword的前54位
  - 解锁后，没发生竞争，这个对象还是给当前线程在用，即以后这个对象就从属于主线程，所以后三位还是101，前面的54位还是主线程ID，除非是其他线程来用了

~~~bash
# 前
# 00000000 00000000 00000000 00000000 00000000 00000000 00000000 00000101 
# 中
# 00000000 00000000 00000000 00000000 00011111 11101011 11010000 00000101 
# 后
# 00000000 00000000 00000000 00000000 00011111 11101011 11010000 00000101
~~~



#### 8.5.4 禁用偏向锁

- 在上面测试代码运行时在添加 VM 参数 **-XX:-UseBiasedLocking**  **禁用偏向锁**


- 结果分析
  - 没偏向锁，所以一开始为001
  - 加锁后，没偏向锁，**所以加的是轻量锁，后面为00**，前面的62位就是锁记录的地址指针
  - 锁释放后，轻量锁是需要释放的，所以又回到正常状态，回到001

~~~bash
# synchronized 前
# 00000000 00000000 00000000 00000000 00000000 00000000 00000000 00000001 
# synchronized 中
# 00000000 00000000 00000000 00000000 00100000 00010100 11110011 10001000 
# synchronized 后
# 00000000 00000000 00000000 00000000 00000000 00000000 00000000 00000001
~~~



#### 8.5.5 测试hashcode

~~~java
public static void main(String[] args) throws IOException {
    Dog d = new Dog();
    d.hashcode();  // 会禁用掉对象的偏向锁
    ClassLayout classLayout = ClassLayout.parseInstance(d);
 
    log.debug("前");
	System.out.println(classLayout.toPrintableSimple(true));
    
    synchronized (d) {
    	log.debug("synchronized 中");
        System.out.println(classLayout.toPrintableSimple(true));
    }
    
    log.debug("后");
    System.out.println(classLayout.toPrintableSimple(true));
}
~~~

- 结果分析
  - 调用了hashcode()方法，会禁用掉偏向锁。**所以后面三位为001，中间的31位存储的就是哈希码**
  - 上锁中，加的就是轻量锁了，所以后面为00
  - 解锁后，回到正常状态，后面为001，中间为哈希码

~~~bash
# synchronized 前
# 00000000 00000000 00000000 00000101 01000111 10011110 00000000 00000001 
# synchronized 中
# 00000000 00000000 00000000 00000000 00100000 00010100 11110011 10001000 
# synchronized 后
# 00000000 00000000 00000000 00000101 01000111 10011110 00000000 00000001
~~~

- **为什么调用hashcode()方法后，会禁用掉对象的偏向锁**
  - **因为偏向锁前面54位存的是线程ID，如果调用hashcode方法后，生成的31位hashcode码没地放，所以只能撤销偏向锁**，把偏向锁撤销，存放hashcode码
- 那为什么轻量锁和重量锁不会出现这种情况
  - 因为轻量锁的hashcode会存储在线程的锁记录里，重量锁的会存在monitor对象里





#### 8.5.6 撤销(偏向) - 调用对象 hashCode 

 - 调用了对象的 hashCode，但偏向锁的对象 MarkWord 中存储的是线程 id，如果调用 hashCode 会导致偏向锁被撤销 
   - 轻量级锁会在锁记录中记录 hashCode 
   - 重量级锁会在 Monitor 中记录 hashCode 



#### 8.5.7 撤销(偏向) - 其它线程(错开)使用对象 

- 当有其它线程使用偏向锁对象时，会将偏向锁升级为轻量级锁

~~~java
private static void test2() throws InterruptedException {
    Dog d = new Dog();
    
    Thread t1 = new Thread(() -> {  
        log.debug(ClassLayout.parseInstance(d).toPrintableSimple(true));
        synchronized (d) {
            log.debug(ClassLayout.parseInstance(d).toPrintableSimple(true));
        }
        log.debug(ClassLayout.parseInstance(d).toPrintableSimple(true));
        
        // 加这个，是为了让线程错开执行，不然有竞争就是重量锁了
        synchronized (TestBiased.class) {
            TestBiased.class.notify();
        }
        
        // 如果不用 wait/notify 使用 join 必须打开下面的注释
        // 因为：t1 线程不能结束，否则底层线程可能被 jvm 重用作为 t2 线程，底层线程 id 是一样的
        /*try {
            System.in.read();
        } catch (IOException e) {
            e.printStackTrace();
        }*/
        
    }, "t1");
    t1.start();
    
    Thread t2 = new Thread(() -> {
        // 加这个，是为了让线程错开执行，不然有竞争就是重量锁了，他会等上面执行完才执行下面
        synchronized (TestBiased.class) {
            try {
                TestBiased.class.wait();
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
        }
        
        log.debug(ClassLayout.parseInstance(d).toPrintableSimple(true));
        synchronized (d) {
            log.debug(ClassLayout.parseInstance(d).toPrintableSimple(true));
        }
        log.debug(ClassLayout.parseInstance(d).toPrintableSimple(true));
    }, "t2");
    t2.start();
}
~~~

- 结果分析
  - 刚开始创建对象，就是偏向锁的状态，后面仨位101
  - t1给其上锁后，前面的54位记录t1的线程ID
  - t1解锁后，偏向锁的性质决定他前面54位还是t1的线程ID，当前继续从属t1
  - t2开始执行时，锁的状态还是t1的
  - t2上锁时，发现有竞争，偏向锁失效，开始升级为轻量锁，后两位变成00
  - t2解锁时，按照轻量锁解，因为偏向锁已经失效了，所以变为正常状态001

![偏向锁失效1](C:\Users\lzy\Desktop\work\Java高级\JUC并发编程\图片\synchronized\偏向锁失效1.png)



#### 8.5.8 撤销(偏向) - 调用wait/notify

- **因为wait/notify是只有重量级锁才有**
- 一旦调用，都会将锁升级为重量级锁

~~~java

public static void main(String[] args) throws InterruptedException {
    Dog d = new Dog();
    
    Thread t1 = new Thread(() -> {
        log.debug(ClassLayout.parseInstance(d).toPrintableSimple(true));
        synchronized (d) {
            log.debug(ClassLayout.parseInstance(d).toPrintableSimple(true));
            try {
                d.wait();
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
            log.debug(ClassLayout.parseInstance(d).toPrintableSimple(true));
        }
    }, "t1");
    t1.start();
    
    new Thread(() -> {
        try {
            Thread.sleep(6000);
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
        synchronized (d) {
            log.debug("notify");
            d.notify();
        }
    }, "t2").start();
}
~~~

- 结果分析
  - 刚创建是偏向的，所以是101
  - t1上锁后，前面31位存储t1的线程ID，末尾三位还是101，偏向锁
  - 调用wait/notify之后，偏向锁失效，升级为重量锁，末尾变10

~~~java
[t1] - 00000000 00000000 00000000 00000000 00000000 00000000 00000000 00000101 
[t1] - 00000000 00000000 00000000 00000000 00011111 10110011 11111000 00000101 
[t2] - notify 
[t1] - 00000000 00000000 00000000 00000000 00011100 11010100 00001101 11001010
~~~



#### 8.5.9 批量重偏向

- 如果**对象虽然被多个线程访问，但没有竞争**，这时偏向了线程 T1 的对象仍有机会重新偏向 T2，重偏向会重置对象的 Thread ID 
- **当(某类型对象)撤销偏向锁阈值超过 20 次后**，jvm 会这样觉得，我是不是偏向错了呢，于是会在给(所有这种类型的状态为偏向锁的)对象加锁时重新偏向至新的加锁线程
- 举个例子通俗来讲，就是有30个对象，都偏向了t1，然后这时t2要用，那么每次都会撤销这个偏向，达到20次后，jvm就会批量的把剩下的这种类型的对象全部偏向给t2，以节省资源

~~~java
private static void test3() throws InterruptedException {
    
    Vector<Dog> list = new Vector<>();
    
    Thread t1 = new Thread(() -> {
        for (int i = 0; i < 30; i++) {
            Dog d = new Dog();
            list.add(d);
            synchronized (d) {
                log.debug(i + "\t" + ClassLayout.parseInstance(d).toPrintableSimple(true));
            }
        }
        synchronized (list) {
            list.notify();
        }
    }, "t1");
    t1.start();

    Thread t2 = new Thread(() -> {
        synchronized (list) {
            try {
                list.wait();
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
        }
        log.debug("===============> ");
        for (int i = 0; i < 30; i++) {
            Dog d = list.get(i);
            log.debug(i + "\t" + ClassLayout.parseInstance(d).toPrintableSimple(true));
            synchronized (d) {
                log.debug(i + "\t" + ClassLayout.parseInstance(d).toPrintableSimple(true));
            }
            log.debug(i + "\t" + ClassLayout.parseInstance(d).toPrintableSimple(true));
        }
    }, "t2");
    t2.start();
}
~~~

- 结果分析
  - t1里面，给30个对象加锁，都是偏向t1的，所以结尾101，前面31位都是t1的线程ID
  - 轮到t2执行时，前19个对象的三种状态
    - 加锁前：结尾101（偏向锁），线程ID存的是t1的
    - 加锁中：有竞争，锁升级为轻量锁，结尾00
    - 解锁后：回归正常状态，001
  - 从第20个对象开始，发生批量重偏向，对象都会偏向给t2，三种状态如下
    - 加锁前：结尾101（偏向锁），线程ID存的是t1的
    - 加锁中：因为发生锁偏向，所以无需撤销，就没升级为轻量锁，结尾为101（偏向锁），线程ID为t2
    - 解锁后：锁偏向t2，结尾为101（偏向锁），线程ID为t2

~~~JAVA
[t1] - 0 00000000 00000000 00000000 00000000 00011111 11110011 11100000 00000101 
[t1] - 1 00000000 00000000 00000000 00000000 00011111 11110011 11100000 00000101 
[t1] - 2 00000000 00000000 00000000 00000000 00011111 11110011 11100000 00000101 
[t1] - 3 00000000 00000000 00000000 00000000 00011111 11110011 11100000 00000101 
[t1] - 4 00000000 00000000 00000000 00000000 00011111 11110011 11100000 00000101 
[t1] - 5 00000000 00000000 00000000 00000000 00011111 11110011 11100000 00000101 
[t1] - 6 00000000 00000000 00000000 00000000 00011111 11110011 11100000 00000101 
[t1] - 7 00000000 00000000 00000000 00000000 00011111 11110011 11100000 00000101 
[t1] - 8 00000000 00000000 00000000 00000000 00011111 11110011 11100000 00000101 
[t1] - 9 00000000 00000000 00000000 00000000 00011111 11110011 11100000 00000101 
[t1] - 10 00000000 00000000 00000000 00000000 00011111 11110011 11100000 00000101 
[t1] - 11 00000000 00000000 00000000 00000000 00011111 11110011 11100000 00000101 
[t1] - 12 00000000 00000000 00000000 00000000 00011111 11110011 11100000 00000101 
[t1] - 13 00000000 00000000 00000000 00000000 00011111 11110011 11100000 00000101 
[t1] - 14 00000000 00000000 00000000 00000000 00011111 11110011 11100000 00000101 
[t1] - 15 00000000 00000000 00000000 00000000 00011111 11110011 11100000 00000101 
[t1] - 16 00000000 00000000 00000000 00000000 00011111 11110011 11100000 00000101 
[t1] - 17 00000000 00000000 00000000 00000000 00011111 11110011 11100000 00000101 
[t1] - 18 00000000 00000000 00000000 00000000 00011111 11110011 11100000 00000101 
[t1] - 19 00000000 00000000 00000000 00000000 00011111 11110011 11100000 00000101 
[t1] - 20 00000000 00000000 00000000 00000000 00011111 11110011 11100000 00000101 
[t1] - 21 00000000 00000000 00000000 00000000 00011111 11110011 11100000 00000101 
[t1] - 22 00000000 00000000 00000000 00000000 00011111 11110011 11100000 00000101 
[t1] - 23 00000000 00000000 00000000 00000000 00011111 11110011 11100000 00000101 
[t1] - 24 00000000 00000000 00000000 00000000 00011111 11110011 11100000 00000101 
[t1] - 25 00000000 00000000 00000000 00000000 00011111 11110011 11100000 00000101 
[t1] - 26 00000000 00000000 00000000 00000000 00011111 11110011 11100000 00000101 
[t1] - 27 00000000 00000000 00000000 00000000 00011111 11110011 11100000 00000101 
[t1] - 28 00000000 00000000 00000000 00000000 00011111 11110011 11100000 00000101 
[t1] - 29 00000000 00000000 00000000 00000000 00011111 11110011 11100000 00000101 
[t2] - ===============> 
[t2] - 0 00000000 00000000 00000000 00000000 00011111 11110011 11100000 00000101 
[t2] - 0 00000000 00000000 00000000 00000000 00100000 01011000 11110111 00000000 
[t2] - 0 00000000 00000000 00000000 00000000 00000000 00000000 00000000 00000001 
[t2] - 1 00000000 00000000 00000000 00000000 00011111 11110011 11100000 00000101 
[t2] - 1 00000000 00000000 00000000 00000000 00100000 01011000 11110111 00000000 
[t2] - 1 00000000 00000000 00000000 00000000 00000000 00000000 00000000 00000001 
[t2] - 2 00000000 00000000 00000000 00000000 00011111 11110011 11100000 00000101 
[t2] - 2 00000000 00000000 00000000 00000000 00100000 01011000 11110111 00000000 
[t2] - 2 00000000 00000000 00000000 00000000 00000000 00000000 00000000 00000001 
[t2] - 3 00000000 00000000 00000000 00000000 00011111 11110011 11100000 00000101 
[t2] - 3 00000000 00000000 00000000 00000000 00100000 01011000 11110111 00000000 
[t2] - 3 00000000 00000000 00000000 00000000 00000000 00000000 00000000 00000001 
[t2] - 4 00000000 00000000 00000000 00000000 00011111 11110011 11100000 00000101 
[t2] - 4 00000000 00000000 00000000 00000000 00100000 01011000 11110111 00000000 
[t2] - 4 00000000 00000000 00000000 00000000 00000000 00000000 00000000 00000001 
[t2] - 5 00000000 00000000 00000000 00000000 00011111 11110011 11100000 00000101 
[t2] - 5 00000000 00000000 00000000 00000000 00100000 01011000 11110111 00000000 
[t2] - 5 00000000 00000000 00000000 00000000 00000000 00000000 00000000 00000001 
[t2] - 6 00000000 00000000 00000000 00000000 00011111 11110011 11100000 00000101 
[t2] - 6 00000000 00000000 00000000 00000000 00100000 01011000 11110111 00000000 
[t2] - 6 00000000 00000000 00000000 00000000 00000000 00000000 00000000 00000001 
[t2] - 7 00000000 00000000 00000000 00000000 00011111 11110011 11100000 00000101
[t2] - 7 00000000 00000000 00000000 00000000 00100000 01011000 11110111 00000000 
[t2] - 7 00000000 00000000 00000000 00000000 00000000 00000000 00000000 00000001 
[t2] - 8 00000000 00000000 00000000 00000000 00011111 11110011 11100000 00000101 
[t2] - 8 00000000 00000000 00000000 00000000 00100000 01011000 11110111 00000000 
[t2] - 8 00000000 00000000 00000000 00000000 00000000 00000000 00000000 00000001 
[t2] - 9 00000000 00000000 00000000 00000000 00011111 11110011 11100000 00000101 
[t2] - 9 00000000 00000000 00000000 00000000 00100000 01011000 11110111 00000000 
[t2] - 9 00000000 00000000 00000000 00000000 00000000 00000000 00000000 00000001 
[t2] - 10 00000000 00000000 00000000 00000000 00011111 11110011 11100000 00000101 
[t2] - 10 00000000 00000000 00000000 00000000 00100000 01011000 11110111 00000000 
[t2] - 10 00000000 00000000 00000000 00000000 00000000 00000000 00000000 00000001 
[t2] - 11 00000000 00000000 00000000 00000000 00011111 11110011 11100000 00000101 
[t2] - 11 00000000 00000000 00000000 00000000 00100000 01011000 11110111 00000000 
[t2] - 11 00000000 00000000 00000000 00000000 00000000 00000000 00000000 00000001 
[t2] - 12 00000000 00000000 00000000 00000000 00011111 11110011 11100000 00000101 
[t2] - 12 00000000 00000000 00000000 00000000 00100000 01011000 11110111 00000000 
[t2] - 12 00000000 00000000 00000000 00000000 00000000 00000000 00000000 00000001 
[t2] - 13 00000000 00000000 00000000 00000000 00011111 11110011 11100000 00000101 
[t2] - 13 00000000 00000000 00000000 00000000 00100000 01011000 11110111 00000000 
[t2] - 13 00000000 00000000 00000000 00000000 00000000 00000000 00000000 00000001 
[t2] - 14 00000000 00000000 00000000 00000000 00011111 11110011 11100000 00000101 
[t2] - 14 00000000 00000000 00000000 00000000 00100000 01011000 11110111 00000000 
[t2] - 14 00000000 00000000 00000000 00000000 00000000 00000000 00000000 00000001 
[t2] - 15 00000000 00000000 00000000 00000000 00011111 11110011 11100000 00000101 
[t2] - 15 00000000 00000000 00000000 00000000 00100000 01011000 11110111 00000000 
[t2] - 15 00000000 00000000 00000000 00000000 00000000 00000000 00000000 00000001 
[t2] - 16 00000000 00000000 00000000 00000000 00011111 11110011 11100000 00000101 
[t2] - 16 00000000 00000000 00000000 00000000 00100000 01011000 11110111 00000000 
[t2] - 16 00000000 00000000 00000000 00000000 00000000 00000000 00000000 00000001 
[t2] - 17 00000000 00000000 00000000 00000000 00011111 11110011 11100000 00000101 
[t2] - 17 00000000 00000000 00000000 00000000 00100000 01011000 11110111 00000000 
[t2] - 17 00000000 00000000 00000000 00000000 00000000 00000000 00000000 00000001 
[t2] - 18 00000000 00000000 00000000 00000000 00011111 11110011 11100000 00000101 
[t2] - 18 00000000 00000000 00000000 00000000 00100000 01011000 11110111 00000000 
[t2] - 18 00000000 00000000 00000000 00000000 00000000 00000000 00000000 00000001 
[t2] - 19 00000000 00000000 00000000 00000000 00011111 11110011 11100000 00000101 
[t2] - 19 00000000 00000000 00000000 00000000 00011111 11110011 11110001 00000101 
[t2] - 19 00000000 00000000 00000000 00000000 00011111 11110011 11110001 00000101 
[t2] - 20 00000000 00000000 00000000 00000000 00011111 11110011 11100000 00000101 
[t2] - 20 00000000 00000000 00000000 00000000 00011111 11110011 11110001 00000101 
[t2] - 20 00000000 00000000 00000000 00000000 00011111 11110011 11110001 00000101 
[t2] - 21 00000000 00000000 00000000 00000000 00011111 11110011 11100000 00000101 
[t2] - 21 00000000 00000000 00000000 00000000 00011111 11110011 11110001 00000101 
[t2] - 21 00000000 00000000 00000000 00000000 00011111 11110011 11110001 00000101 
[t2] - 22 00000000 00000000 00000000 00000000 00011111 11110011 11100000 00000101 
[t2] - 22 00000000 00000000 00000000 00000000 00011111 11110011 11110001 00000101 
[t2] - 22 00000000 00000000 00000000 00000000 00011111 11110011 11110001 00000101 
[t2] - 23 00000000 00000000 00000000 00000000 00011111 11110011 11100000 00000101 
[t2] - 23 00000000 00000000 00000000 00000000 00011111 11110011 11110001 00000101 
[t2] - 23 00000000 00000000 00000000 00000000 00011111 11110011 11110001 00000101 
[t2] - 24 00000000 00000000 00000000 00000000 00011111 11110011 11100000 00000101 
[t2] - 24 00000000 00000000 00000000 00000000 00011111 11110011 11110001 00000101 
[t2] - 24 00000000 00000000 00000000 00000000 00011111 11110011 11110001 00000101
[t2] - 25 00000000 00000000 00000000 00000000 00011111 11110011 11100000 00000101 
[t2] - 25 00000000 00000000 00000000 00000000 00011111 11110011 11110001 00000101 
[t2] - 25 00000000 00000000 00000000 00000000 00011111 11110011 11110001 00000101 
[t2] - 26 00000000 00000000 00000000 00000000 00011111 11110011 11100000 00000101 
[t2] - 26 00000000 00000000 00000000 00000000 00011111 11110011 11110001 00000101 
[t2] - 26 00000000 00000000 00000000 00000000 00011111 11110011 11110001 00000101 
[t2] - 27 00000000 00000000 00000000 00000000 00011111 11110011 11100000 00000101 
[t2] - 27 00000000 00000000 00000000 00000000 00011111 11110011 11110001 00000101 
[t2] - 27 00000000 00000000 00000000 00000000 00011111 11110011 11110001 00000101 
[t2] - 28 00000000 00000000 00000000 00000000 00011111 11110011 11100000 00000101 
[t2] - 28 00000000 00000000 00000000 00000000 00011111 11110011 11110001 00000101 
[t2] - 28 00000000 00000000 00000000 00000000 00011111 11110011 11110001 00000101 
[t2] - 29 00000000 00000000 00000000 00000000 00011111 11110011 11100000 00000101 
[t2] - 29 00000000 00000000 00000000 00000000 00011111 11110011 11110001 00000101 
[t2] - 29 00000000 00000000 00000000 00000000 00011111 11110011 11110001 00000101
~~~



#### 8.5.10 批量撤销

- 当**撤销偏向锁阈值超过 40** 次后，jvm 会这样觉得，自己确实偏向错了，根本就不该偏向。**于是整个类的所有对象都会变为不可偏向的，新建的该类型对象也是不可偏向的** 

~~~java
static Thread t1,t2,t3;
private static void test4() throws InterruptedException {
    Vector<Dog> list = new Vector<>();
    
    int loopNumber = 39;
    t1 = new Thread(() -> {
        for (int i = 0; i < loopNumber; i++) {
            Dog d = new Dog();
            list.add(d);
            synchronized (d) {
                log.debug(i + "\t" + ClassLayout.parseInstance(d).toPrintableSimple(true));
            }
        }
        LockSupport.unpark(t2);
    }, "t1");
    t1.start();
    
    t2 = new Thread(() -> {
        LockSupport.park();
        log.debug("===============> ");
        for (int i = 0; i < loopNumber; i++) {
            Dog d = list.get(i);
            log.debug(i + "\t" + ClassLayout.parseInstance(d).toPrintableSimple(true));
            synchronized (d) {
                log.debug(i + "\t" + ClassLayout.parseInstance(d).toPrintableSimple(true));
            }
            log.debug(i + "\t" + ClassLayout.parseInstance(d).toPrintableSimple(true));
        }
        LockSupport.unpark(t3);
    }, "t2");
    t2.start();
    
    t3 = new Thread(() -> {
        LockSupport.park();
        log.debug("===============> ");
        for (int i = 0; i < loopNumber; i++) {
            Dog d = list.get(i);
            log.debug(i + "\t" + ClassLayout.parseInstance(d).toPrintableSimple(true));
            synchronized (d) {
                log.debug(i + "\t" + ClassLayout.parseInstance(d).toPrintableSimple(true));
            }
            log.debug(i + "\t" + ClassLayout.parseInstance(d).toPrintableSimple(true));
        }
    }, "t3");
    t3.start();
    
    t3.join();
    log.debug(ClassLayout.parseInstance(new Dog()).toPrintableSimple(true));
}
~~~

- 结果分析
  - t1给39个对象加锁，偏向锁，结尾101，线程存的t1的
  - t2：
    - 前19个都是正常的撤销，加锁前101，线程id是t1；加锁升级为轻量锁，解锁回归正常001
    - 从第19个开始都偏向t2，加锁前101，线程id是t2；加锁中线程id为t2，结尾101；解锁还是t2
  - t3：
    - 前19个对象因为已经撤销了，所以加锁前001；加锁为轻量锁00，解锁回归正常001
    - 从第20个开始都正常的撤销，加锁前101，线程id是t2；加锁升级为轻量锁，解锁回归正常001
  - 新创建的对象：这时候撤销次数已经达到了40次，所以对偏向锁进行批量撤销，后续新创建的对象也不会有偏向，状态001，正常的，**没有偏向**
    - 若loopNumber=38时，次数没达到，所以下次创建的对象还是偏向的，101

~~~java
[t1] - 0 00000000 00000000 00000000 00000000 00011111 11110011 11100000 00000101 
...
...
...
[t1] - 39 00000000 00000000 00000000 00000000 00011111 11110011 11100000 00000101 
[t2] - ===============>  
[t2] - ===============> 
[t2] - 0 00000000 00000000 00000000 00000000 00011111 11110011 11100000 00000101 
[t2] - 0 00000000 00000000 00000000 00000000 00100000 01011000 11110111 00000000 
[t2] - 0 00000000 00000000 00000000 00000000 00000000 00000000 00000000 00000001 
...
...
...
[t2] - 18 00000000 00000000 00000000 00000000 00011111 11110011 11100000 00000101 
[t2] - 18 00000000 00000000 00000000 00000000 00100000 01011000 11110111 00000000 
[t2] - 18 00000000 00000000 00000000 00000000 00000000 00000000 00000000 00000001 
[t2] - 19 00000000 00000000 00000000 00000000 00011111 11110011 11100000 00000101 
[t2] - 19 00000000 00000000 00000000 00000000 00011111 11110011 11110001 00000101 
...
...
...
[t2] - 39 00000000 00000000 00000000 00000000 00011111 11110011 11100000 00000101 
[t2] - 39 00000000 00000000 00000000 00000000 00011111 11110011 11110001 00000101 
[t2] - 39 00000000 00000000 00000000 00000000 00011111 11110011 11110001 00000101
[t3] - ===============>
[t3] - ===============> 
[t3] - 0 00000000 00000000 00000000 00000000 00000000 00000000 00000000 00000001 
[t3] - 0 00000000 00000000 00000000 00000000 00100000 01011000 11111110 00000000 
[t3] - 0 00000000 00000000 00000000 00000000 00000000 00000000 00000000 00000001 
...
...
...
[t3] - 18 00000000 00000000 00000000 00000000 00000000 00000000 00000000 00000001  
[t3] - 18 00000000 00000000 00000000 00000000 00100000 01011000 11110111 00000000 
[t3] - 18 00000000 00000000 00000000 00000000 00000000 00000000 00000000 00000001 
[t3] - 19 00000000 00000000 00000000 00000000 00011111 11110011 11100000 00000101 
[t3] - 19 00000000 00000000 00000000 00000000 00100000 01011000 11111110 00000000 
[t3] - 19 00000000 00000000 00000000 00000000 00000000 00000000 00000000 00000001
...
...
...
[t3] - 39 00000000 00000000 00000000 00000000 00011111 11110011 11100000 00000101 
[t3] - 39 00000000 00000000 00000000 00000000 00100000 01011000 11111110 00000000 
[t3] - 39 00000000 00000000 00000000 00000000 00000000 00000000 00000000 00000001
[main] - ===============>
[main] - ===============>     
[t3] - 00000000 00000000 00000000 00000000 00000000 00000000 00000000 00000001
~~~



### 8.6 锁消除

- 锁消除： **JIT即时编译器会对字节码做进一步优化**
- 通俗来说，就是如果加锁和不加锁效果一样，那么就会优化掉
- **-XX:-EliminateLocks**：JIT即时编译器，默认开启

~~~java
@Fork(1)
@BenchmarkMode(Mode.AverageTime)
@Warmup(iterations=3)
@Measurement(iterations=5)
@OutputTimeUnit(TimeUnit.NANOSECONDS)
public class MyBenchmark {
    static int x = 0;
    @Benchmark
    public void a() throws Exception {
        x++;
    }
    @Benchmark
    public void b() throws Exception {
        //这里的o是局部变量,不会被共享,JIT做热点代码优化时会做锁消除
        Object o = new Object();
        synchronized (o) {
            x++;
        }
    }
}
~~~

- java -jar benchmarks.jar，发现两部分的差别并不大,甚至b加了锁比a没加锁还快

![锁消除1](C:\Users\lzy\Desktop\work\Java高级\JUC并发编程\图片\synchronized\锁消除1.png)

- java -XX:-EliminateLocks -jar benchmarks.jar，使用 -XX:-EliminateLocks禁用锁消除后就会发现 b性能比a差劲多了

  ![锁消除1](C:\Users\lzy\Desktop\work\Java高级\JUC并发编程\图片\synchronized\锁消除2.png)




## 9、wait\notify

### 9.1 故事类比

- 由于条件不满足，小南不能继续进行计算 
- 但小南如果一直占用着锁，其它人就得一直阻塞，效率太低

![故事1](C:\Users\lzy\Desktop\work\Java高级\JUC并发编程\图片\wait\故事1.png)

- 于是老王单开了一间休息室（调用 wait 方法），让小南到休息室（WaitSet）等着去了，但这时锁释放开，其它人可以由老王随机安排进屋 
- 直到小M将烟送来，大叫一声 [ 你的烟到了 ] （调用 notify 方法）

![故事1](C:\Users\lzy\Desktop\work\Java高级\JUC并发编程\图片\wait\故事2.png)

- 小南于是可以离开休息室，重新进入竞争锁的队列

![故事1](C:\Users\lzy\Desktop\work\Java高级\JUC并发编程\图片\wait\故事3.png)



### 9.2 原理

![原理](C:\Users\lzy\Desktop\work\Java高级\JUC并发编程\图片\wait\原理.png)

- Owner 线程发现条件不满足，调用 wait 方法，即可进入 WaitSet 变为 WAITING 状态 
- BLOCKED 和 WAITING 的线程都处于阻塞状态，不占用 CPU 时间片 
- BLOCKED 线程会在 Owner 线程释放锁时唤醒 
- WAITING 线程会在 Owner 线程调用 notify 或 notifyAll 时唤醒，**但唤醒后并不意味者立刻获得锁，仍需进入EntryList 重新竞争**



### 9.3 api

-  **obj.wait()** 让进入 object 监视器的线程到 waitSet 等待 
- **obj.wait()** 方法会释放对象的锁，进入 WaitSet 等待区，从而让其他线程就机会获取对象的锁。无限制等待，直到notify 为止 
- **obj.wait(long n)** 有时限的等待, 到 n 毫秒后结束等待，或是被 notify
- **obj.notify()** 在 object 上正在 waitSet 等待的线程中挑一个唤醒 
- **obj.notifyAll()** 让 object 上正在 waitSet 等待的线程全部唤醒


- 它们都是线程之间进行协作的手段，都属于Object 对象的方法。**必须获得此对象的锁，才能调用这几个方法**


- 例子1
  - 结果分析：api都是obj的，所以必须获得此锁才能调用，否则就会报错

~~~java
package com.example.three;

import lombok.extern.slf4j.Slf4j;

@Slf4j
public class Test1 {

    static final Object lock = new Object();
    public static void main(String[] args) {
        try {
            lock.wait();
        } catch (InterruptedException exception) {
            exception.printStackTrace();
        }
    }
}
// Exception in thread "main" java.lang.IllegalMonitorStateException
~~~

- 例子2
  - 结果分析：获取到了锁，所以正常调用，是没有问题的

~~~java
package com.example.three;

import lombok.extern.slf4j.Slf4j;

@Slf4j
public class Test1 {

    static final Object lock = new Object();
    public static void main(String[] args) {
        synchronized (lock) {
            try {
                lock.wait();
            } catch (InterruptedException exception) {
                exception.printStackTrace();
            }
        }
    }
}
// 正常执行
~~~

- 例子3

~~~java
package com.example.three;

import lombok.extern.slf4j.Slf4j;

@Slf4j
public class Test1 {

    static final Object lock = new Object();
    public static void main(String[] args) throws InterruptedException {
        new Thread(() -> {
            synchronized (lock) {
                log.debug("执行....");
                try {
                    lock.wait(); // 让线程在obj上一直等待下去
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }
                log.debug("其它代码....");
            }
        }, "t1").start();

        new Thread(() -> {
            synchronized (lock) {
                log.debug("执行....");
                try {
                    lock.wait(); // 让线程在obj上一直等待下去
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }
                log.debug("其它代码....");
            }
        }, "t2").start();

        // 主线程两秒后执行
        Thread.sleep(2000);
        log.debug("唤醒 lock 上其它线程");
        synchronized (lock) {
            lock.notify(); // 唤醒obj上一个线程，随机的
            // obj.notifyAll(); // 唤醒obj上所有等待线程
        }
    }
}
// lock.notify()的结果：唤醒一个
// 2024-01-08 20:48:02 [t1] [com.example.three.Test1] [DEBUG] - 执行....
// 2024-01-08 20:48:02 [t2] [com.example.three.Test1] [DEBUG] - 执行....
// 2024-01-08 20:48:04 [main] [com.example.three.Test1] [DEBUG] - 唤醒 lock 上其它线程
// 2024-01-08 20:48:04 [t1] [com.example.three.Test1] [DEBUG] - 其它代码....

// lock.notifyAll()的结果：唤醒所有
// 2024-01-08 20:48:02 [t1] [com.example.three.Test1] [DEBUG] - 执行....
// 2024-01-08 20:48:02 [t2] [com.example.three.Test1] [DEBUG] - 执行....
// 2024-01-08 20:48:04 [main] [com.example.three.Test1] [DEBUG] - 唤醒 lock 上其它线程
// 2024-01-08 20:48:04 [t1] [com.example.three.Test1] [DEBUG] - 其它代码....
// 2024-01-08 20:48:04 [t2] [com.example.three.Test1] [DEBUG] - 其它代码....
~~~

- 例子4

~~~java
package com.example.three;

import lombok.extern.slf4j.Slf4j;

@Slf4j
public class Test1 {

    static final Object lock = new Object();
    public static void main(String[] args) throws InterruptedException {
        new Thread(() -> {
            synchronized (lock) {
                log.debug("执行....");
                try {
                    lock.wait(2000); // 等待2s，如果过了2s就会自动唤醒，中途有notify也会唤醒
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }
                log.debug("其它代码....");
            }
        }, "t1").start();
    }
}

// 2024-01-08 20:56:37 [t1] [com.example.three.Test1] [DEBUG] - 执行....
// 2024-01-08 20:56:39 [t1] [com.example.three.Test1] [DEBUG] - 其它代码....
~~~

- 例子5

~~~java
package com.example.three;

import lombok.extern.slf4j.Slf4j;

@Slf4j
public class Test1 {

    static final Object lock = new Object();
    public static void main(String[] args) throws InterruptedException {
        new Thread(() -> {
            synchronized (lock) {
                log.debug("执行....");
                try {
                    lock.wait(2000); //  等待2s，如果过了2s就会自动唤醒
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }
                log.debug("其它代码....");
            }
        }, "t1").start();

        // 主线程1s后执行
        Thread.sleep(1000);
        log.debug("唤醒 lock 上其它线程");
        synchronized (lock) {
            lock.notify(); // 唤醒obj上一个线程，随机的
        }
    }
}

// 2024-01-08 20:58:20 [t1] [com.example.three.Test1] [DEBUG] - 执行....
// 2024-01-08 20:58:21 [main] [com.example.three.Test1] [DEBUG] - 唤醒 lock 上其它线程
// 2024-01-08 20:58:21 [t1] [com.example.three.Test1] [DEBUG] - 其它代码....
~~~



## 10、wait\sleep

### 10.1 区别

- sleep 是 Thread 方法，而 wait 是 Object 的方法 
- sleep 不需要强制和 synchronized 配合使用，但 wait 需要和 synchronized 一起用 
- **sleep 在睡眠的同时，不会释放对象锁的，但 wait 在等待的时候会释放对象锁** 
- 它们状态都是TIMED_WAITING

~~~java
package com.example.three;

import lombok.extern.slf4j.Slf4j;

@Slf4j
public class Test2 {
    static final Object lock = new Object();
    public static void main(String[] args) throws InterruptedException {
        new Thread(() -> {
            synchronized (lock) {
                log.debug("获得锁");
                try {
                    Thread.sleep(20000);
                    // lock.wait(20000);
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }
            }
        }, "t1").start();

        // 主线程1s后执行
        Thread.sleep(1000);
        synchronized (lock) {
            log.debug("获得锁");
        }
    }
}
// 如果使用的是Thread.sleep():sleep不会释放锁，所以要等20s主线程才能获得锁，然后执行代码
// 2024-01-08 21:09:17 [t1] [com.example.three.Test2] [DEBUG] - 获得锁
// 2024-01-08 21:09:37 [main] [com.example.three.Test2] [DEBUG] - 获得锁

// 如果使用的是lock.wait(20000)：wait会释放锁，所以在主线程睡眠1s后，就可以获取锁，然后执行代码
// 2024-01-08 21:11:16 [t1] [com.example.three.Test2] [DEBUG] - 获得锁
// 2024-01-08 21:11:17 [main] [com.example.three.Test2] [DEBUG] - 获得锁
~~~



### 10.2 step1

- 场景：
  - 小南线程必须要有烟，才能干活
  - 小南没烟的时候，用的sleep，这样不会释放锁，其他线程没法执行
  - 送烟的送到了之后，小南就可以干活了，然后释放锁，其他线程才能干活

~~~java
package com.example.three;

import lombok.extern.slf4j.Slf4j;

@Slf4j
public class Test3 {
    static final Object room = new Object();
    static boolean hasCigarette = false;        // 是否有烟
    static boolean hasTakeout = false;          // 是否送到外卖

    public static void main(String[] args) throws InterruptedException {
        new Thread(() -> {
            synchronized (room) {
                log.debug("有烟没？[{}]", hasCigarette);
                if (!hasCigarette) {
                    log.debug("没烟，睡会儿！");
                    try {
                        Thread.sleep(2000);
                    } catch (InterruptedException exception) {
                        exception.printStackTrace();
                    }
                    log.debug("有烟没？[{}]", hasCigarette);
                    if (hasCigarette) {
                        log.debug("可以开始干活了");
                    }
                }
            }
        },"小南").start();

        for (int i = 0; i < 5; i++) {
            new Thread(() -> {
                synchronized (room) {
                    log.debug("可以开始干活了");
                }
            },"其他人").start();
        }

        Thread.sleep(1000);
        new Thread(() -> {
            // 这里能不能加 synchronized (room)？ 不能
            // 理由：加了之后，小南一直占着锁，那么送烟的就没法获取到锁，就一直执行不了送烟代码
            hasCigarette = true;
            log.debug("烟到了噢！");
        }, "送烟的").start();
    }
}
// 2024-01-08 21:30:47 [小南] [com.example.three.Test3] [DEBUG] - 有烟没？[false]
// 2024-01-08 21:30:47 [小南] [com.example.three.Test3] [DEBUG] - 没烟，睡会儿！
// 2024-01-08 21:30:48 [送烟的] [com.example.three.Test3] [DEBUG] - 烟到了噢！
// 2024-01-08 21:30:49 [小南] [com.example.three.Test3] [DEBUG] - 有烟没？[true]
// 2024-01-08 21:30:49 [小南] [com.example.three.Test3] [DEBUG] - 可以开始干活了
// 2024-01-08 21:30:49 [其他人] [com.example.three.Test3] [DEBUG] - 可以开始干活了
// 2024-01-08 21:30:49 [其他人] [com.example.three.Test3] [DEBUG] - 可以开始干活了
// 2024-01-08 21:30:49 [其他人] [com.example.three.Test3] [DEBUG] - 可以开始干活了
// 2024-01-08 21:30:49 [其他人] [com.example.three.Test3] [DEBUG] - 可以开始干活了
// 2024-01-08 21:30:49 [其他人] [com.example.three.Test3] [DEBUG] - 可以开始干活了
~~~

- 其它干活的线程，都要一直阻塞，效率太低 
- 小南线程必须睡足 2s 后才能醒来，就算烟提前送到，也无法立刻醒来 
- 加了 synchronized (room) 后，就好比小南在里面反锁了门睡觉，烟根本没法送进门，main 没加 synchronized 就好像 main 线程是翻窗户进来的 
- sleep妨碍其它人干活     解决方法，使用 wait - notify 



### 10.3 step2

- wait替代sleep

~~~java
package com.example.three;

import lombok.extern.slf4j.Slf4j;

@Slf4j
public class Test3 {
    static final Object room = new Object();
    static boolean hasCigarette = false;        // 是否有烟
    static boolean hasTakeout = false;          // 是否送到外卖

    public static void main(String[] args) throws InterruptedException {
        new Thread(() -> {
            synchronized (room) {
                log.debug("有烟没？[{}]", hasCigarette);
                if (!hasCigarette) {
                    log.debug("没烟，睡会儿！");
                    try {
                        room.wait(2000);
                    } catch (InterruptedException exception) {
                        exception.printStackTrace();
                    }
                    log.debug("有烟没？[{}]", hasCigarette);
                    if (hasCigarette) {
                        log.debug("可以开始干活了");
                    }
                }
            }
        },"小南").start();

        for (int i = 0; i < 5; i++) {
            new Thread(() -> {
                synchronized (room) {
                    log.debug("可以开始干活了");
                }
            },"其他人").start();
        }

        Thread.sleep(1000);
        new Thread(() -> {
           synchronized (room) {
           		hasCigarette = true;
        		log.debug("烟到了噢！");
        		room.notify();
    		}
        }, "送烟的").start();
    }
}

// 2024-01-08 21:36:00 [小南] [com.example.three.Test3] [DEBUG] - 有烟没？[false]
// 2024-01-08 21:36:00 [小南] [com.example.three.Test3] [DEBUG] - 没烟，睡会儿！
// 2024-01-08 21:36:00 [其他人] [com.example.three.Test3] [DEBUG] - 可以开始干活了
// 2024-01-08 21:36:00 [其他人] [com.example.three.Test3] [DEBUG] - 可以开始干活了
// 2024-01-08 21:36:00 [其他人] [com.example.three.Test3] [DEBUG] - 可以开始干活了
// 2024-01-08 21:36:00 [其他人] [com.example.three.Test3] [DEBUG] - 可以开始干活了
// 2024-01-08 21:36:00 [其他人] [com.example.three.Test3] [DEBUG] - 可以开始干活了
// 2024-01-08 21:36:01 [送烟的] [com.example.three.Test3] [DEBUG] - 烟到了噢！
// 2024-01-08 21:36:02 [小南] [com.example.three.Test3] [DEBUG] - 有烟没？[true]
// 2024-01-08 21:36:02 [小南] [com.example.three.Test3] [DEBUG] - 可以开始干活了
~~~

- 解决了其它干活的线程阻塞的问题：wait释放锁，其他线程就可以拿到锁，然后执行代码了
- **但如果有其它线程也在等待条件呢？**



### 10.4 step3

~~~java
package com.example.three;

import lombok.extern.slf4j.Slf4j;

@Slf4j
public class Test3 {
    static final Object room = new Object();
    static boolean hasCigarette = false;        // 是否有烟
    static boolean hasTakeout = false;          // 是否送到外卖

    public static void main(String[] args) throws InterruptedException {
        new Thread(() -> {
            synchronized (room) {
                log.debug("有烟没？[{}]", hasCigarette);
                if (!hasCigarette) {
                    log.debug("没烟，先歇会！");
                    try {
                        room.wait();
                    } catch (InterruptedException e) {
                        e.printStackTrace();
                    }
                }
                log.debug("有烟没？[{}]", hasCigarette);
                if (hasCigarette) {
                    log.debug("可以开始干活了");
                } else {
                    log.debug("没干成活...");
                }
            }
        }, "小南").start();

        new Thread(() -> {
            synchronized (room) {
                Thread thread = Thread.currentThread();
                log.debug("外卖送到没？[{}]", hasTakeout);
                if (!hasTakeout) {
                    log.debug("没外卖，先歇会！");
                    try {
                        room.wait();
                    } catch (InterruptedException e) {
                        e.printStackTrace();
                    }
                }
                log.debug("外卖送到没？[{}]", hasTakeout);
                if (hasTakeout) {
                    log.debug("可以开始干活了");
                } else {
                    log.debug("没干成活...");
                }
            }
        }, "小女").start();

        Thread.sleep(1000);
        new Thread(() -> {
            synchronized (room) {
                hasTakeout = true;
                log.debug("外卖到了噢！");
                room.notify();
            }
        }, "送外卖的").start();
    }

}
// 2024-01-08 21:40:14 [小南] [com.example.three.Test3] [DEBUG] - 有烟没？[false]
// 2024-01-08 21:40:14 [小南] [com.example.three.Test3] [DEBUG] - 没烟，先歇会！
// 2024-01-08 21:40:14 [小女] [com.example.three.Test3] [DEBUG] - 外卖送到没？[false]
// 2024-01-08 21:40:14 [小女] [com.example.three.Test3] [DEBUG] - 没外卖，先歇会！
// 2024-01-08 21:40:15 [送外卖的] [com.example.three.Test3] [DEBUG] - 外卖到了噢！
// 2024-01-08 21:40:15 [小南] [com.example.three.Test3] [DEBUG] - 有烟没？[false]
// 2024-01-08 21:40:15 [小南] [com.example.three.Test3] [DEBUG] - 没干成活...
~~~

- 会发生虚假唤醒，因为notify是从waitEntry中随机唤醒一个，外卖送到了，唤醒了小南，但是小南还是没烟，所以执行不了代码
- **notify 只能随机唤醒一个 WaitSet 中的线程，这时如果有其它线程也在等待，那么就可能唤醒不了正确的线程，称之为【虚假唤醒】** 
- 发生虚假唤醒: 解决方法，改为 notifyAll



### 10.5 step4

~~~java
package com.example.three;

import lombok.extern.slf4j.Slf4j;

@Slf4j
public class Test3 {
    static final Object room = new Object();
    static boolean hasCigarette = false;        // 是否有烟
    static boolean hasTakeout = false;          // 是否送到外卖

    public static void main(String[] args) throws InterruptedException {
        new Thread(() -> {
            synchronized (room) {
                log.debug("有烟没？[{}]", hasCigarette);
                if (!hasCigarette) {
                    log.debug("没烟，先歇会！");
                    try {
                        room.wait();
                    } catch (InterruptedException e) {
                        e.printStackTrace();
                    }
                }
                log.debug("有烟没？[{}]", hasCigarette);
                if (hasCigarette) {
                    log.debug("可以开始干活了");
                } else {
                    log.debug("没干成活...");
                }
            }
        }, "小南").start();

        new Thread(() -> {
            synchronized (room) {
                Thread thread = Thread.currentThread();
                log.debug("外卖送到没？[{}]", hasTakeout);
                if (!hasTakeout) {
                    log.debug("没外卖，先歇会！");
                    try {
                        room.wait();
                    } catch (InterruptedException e) {
                        e.printStackTrace();
                    }
                }
                log.debug("外卖送到没？[{}]", hasTakeout);
                if (hasTakeout) {
                    log.debug("可以开始干活了");
                } else {
                    log.debug("没干成活...");
                }
            }
        }, "小女").start();

        Thread.sleep(1000);
        new Thread(() -> {
            synchronized (room) {
                hasTakeout = true;
                log.debug("外卖到了噢！");
                room.notifyAll();
            }
        }, "送外卖的").start();
    }

}
// 2024-01-08 21:42:52 [小南] [com.example.three.Test3] [DEBUG] - 有烟没？[false]
// 2024-01-08 21:42:52 [小南] [com.example.three.Test3] [DEBUG] - 没烟，先歇会！
// 2024-01-08 21:42:52 [小女] [com.example.three.Test3] [DEBUG] - 外卖送到没？[false]
// 2024-01-08 21:42:52 [小女] [com.example.three.Test3] [DEBUG] - 没外卖，先歇会！
// 2024-01-08 21:42:53 [送外卖的] [com.example.three.Test3] [DEBUG] - 外卖到了噢！
// 2024-01-08 21:42:53 [小女] [com.example.three.Test3] [DEBUG] - 外卖送到没？[true]
// 2024-01-08 21:42:53 [小女] [com.example.three.Test3] [DEBUG] - 可以开始干活了
// 2024-01-08 21:42:53 [小南] [com.example.three.Test3] [DEBUG] - 有烟没？[false]
// 2024-01-08 21:42:53 [小南] [com.example.three.Test3] [DEBUG] - 没干成活...
~~~

- 小南小女都唤醒了，但是小南就是白醒了
- 用 notifyAll 仅解决某个线程的唤醒问题，但使用 if + wait 判断仅有一次机会，一旦条件不成立，就没有重新判断的机会了 
- notifyAll唤醒了所有,但使用if+wait仅有一次机会,解决方法，一旦条件不成立，就没有重新判断的机会了.解决办法: 用 while + wait，当条件不成立，再次 wait



### 10.6 step5

~~~java
package com.example.three;

import lombok.extern.slf4j.Slf4j;

@Slf4j
public class Test3 {
    static final Object room = new Object();
    static boolean hasCigarette = false;        // 是否有烟
    static boolean hasTakeout = false;          // 是否送到外卖

    public static void main(String[] args) throws InterruptedException {
        new Thread(() -> {
            synchronized (room) {
                log.debug("有烟没？[{}]", hasCigarette);
                while (!hasCigarette) {
                    log.debug("没烟，先歇会！");
                    try {
                        room.wait();
                    } catch (InterruptedException e) {
                        e.printStackTrace();
                    }
                }
                log.debug("有烟没？[{}]", hasCigarette);
                if (hasCigarette) {
                    log.debug("可以开始干活了");
                } else {
                    log.debug("没干成活...");
                }
            }
        }, "小南").start();

        new Thread(() -> {
            synchronized (room) {
                Thread thread = Thread.currentThread();
                log.debug("外卖送到没？[{}]", hasTakeout);
                while (!hasTakeout) {
                    log.debug("没外卖，先歇会！");
                    try {
                        room.wait();
                    } catch (InterruptedException e) {
                        e.printStackTrace();
                    }
                }
                log.debug("外卖送到没？[{}]", hasTakeout);
                if (hasTakeout) {
                    log.debug("可以开始干活了");
                } else {
                    log.debug("没干成活...");
                }
            }
        }, "小女").start();

        Thread.sleep(1000);
        new Thread(() -> {
            synchronized (room) {
                hasTakeout = true;
                log.debug("外卖到了噢！");
                room.notifyAll();
            }
        }, "送外卖的").start();
    }

}
// 2024-01-08 21:44:37 [小南] [com.example.three.Test3] [DEBUG] - 有烟没？[false]
// 2024-01-08 21:44:37 [小南] [com.example.three.Test3] [DEBUG] - 没烟，先歇会！
// 2024-01-08 21:44:37 [小女] [com.example.three.Test3] [DEBUG] - 外卖送到没？[false]
// 2024-01-08 21:44:37 [小女] [com.example.three.Test3] [DEBUG] - 没外卖，先歇会！
// 2024-01-08 21:44:38 [送外卖的] [com.example.three.Test3] [DEBUG] - 外卖到了噢！
// 2024-01-08 21:44:38 [小女] [com.example.three.Test3] [DEBUG] - 外卖送到没？[true]
// 2024-01-08 21:44:38 [小女] [com.example.three.Test3] [DEBUG] - 可以开始干活了
// 2024-01-08 21:44:38 [小南] [com.example.three.Test3] [DEBUG] - 没烟，先歇会！
~~~



### 10.7 总结用法

~~~java
synchronized(lock) {
    while(条件不成立) {
        lock.wait();
    }
    // 干活
}

//另一个线程
synchronized(lock) {
    lock.notifyAll();
}
~~~



## 11、设计模式-同步模式之保护性暂停

### 11.1 定义

- 即 Guarded Suspension，**用在一个线程等待另一个线程的执行结果** 
- 要点 
  - 有一个结果需要从一个线程传递到另一个线程，让他们关联同一个 GuardedObject 
  - 如果有结果不断从一个线程到另一个线程那么可以使用消息队列（见生产者/消费者） 
  - JDK 中，join 的实现、Future 的实现，采用的就是此模式 
  - 因为要等待另一方的结果，因此归类到同步模式 

![同步模式之保护性暂停定义](C:\Users\lzy\Desktop\work\Java高级\JUC并发编程\图片\wait\同步模式之保护性暂停定义.png)



### 11.2 例子

- download下载代码

~~~java
package com.example.util;

import java.io.BufferedReader;
import java.io.InputStreamReader;
import java.net.HttpURLConnection;
import java.net.URL;
import java.nio.charset.StandardCharsets;
import java.util.ArrayList;
import java.util.List;

public class Downloader {
    public static List<String> download() throws Exception {
        HttpURLConnection connection = (HttpURLConnection) new URL("https://www.baidu.com/").openConnection();
        List<String> lines = new ArrayList<>();
        try (BufferedReader reader = new BufferedReader(new InputStreamReader(connection.getInputStream(), StandardCharsets.UTF_8))) {
            String line;
            while ((line = reader.readLine()) != null) {
                lines.add(line);
            }
        }
        return lines;
    }
}
~~~

- 应用

~~~java
package com.example.three;

import com.example.util.Downloader;
import lombok.extern.slf4j.Slf4j;

import java.util.List;

@Slf4j
public class Test4 {
    // 线程1 等待 线程2 的结果
    public static void main(String[] args) {
        GuardedObject guardedObject = new GuardedObject();
        new Thread(() -> {
            // 等待结果
            log.debug("等待结果");
            List<String> list = (List<String>) guardedObject.get();
            log.debug("结果大小为：{}", list.size());
        }, "t1").start();

        new Thread(() -> {
            log.debug("执行下载");
            // 执行下载，产生结果
            try {
                List<String> list = Downloader.download();
                guardedObject.set(list);
            } catch (Exception e) {
                e.printStackTrace();
            }
        }, "t2").start();
    }

}

class GuardedObject {
    // 结果
    private Object response;

    // 获取结果
    public Object get() {
        synchronized (this) {
            // 没有结果
            while (response == null) {
                try {
                    this.wait();
                } catch (InterruptedException exception) {
                    exception.printStackTrace();
                }
            }
            return response;
        }
    }

    // 产生结果
    public void set(Object response) {
        synchronized (this) {
            // 给结果成员变量赋值
            this.response = response;
            this.notifyAll();
        }
    }
}
// 2024-01-09 22:15:21 [t1] [com.example.three.Test4] [DEBUG] - 等待结果
// 2024-01-09 22:15:21 [t2] [com.example.three.Test4] [DEBUG] - 执行下载
// 2024-01-09 22:15:24 [t1] [com.example.three.Test4] [DEBUG] - 结果大小为：3
~~~

- 结果分析：
  - t1线程需要t2线程产生的结果
  - 当t1调用GuardedObject的get方法时，发现没有结果，就进入wait状态，释放锁
  - t2产生数据后，唤醒t1，然后让t1打印结果
- 好处：
  - **原来用的join，必须等待线程执行结束。而这个模式，不需要强制等待另一个线程执行完**
  - **用join的话，那么等待线程的结果变量必须是全局的，否则没法实现，而这个可以用局部变量**



### 11.3 增加超时效果

- 情况1
  - 比如最大等待时间2s，刚循环进去，经历时间0s，所以要等待的时间为2s，往下执行，wait(2s)。2s后超过最大等待时间醒了，所以经历的时间为2s，还需等待时间为0s，满足条件退出循环
- 情况2
  - 最大等待时间为2s，循环进去，经历时间0s，所以要等待的时间为2s，往下执行，wait(2s)。但是1s后被notify唤醒，所以经历的时间为1s，还需等待1s，不满足条件，继续循环，wait(1s)。1s后超过最大时间，经历的时间为2s，还需等待时间为0s，满足条件退出循环

~~~java
// 增加超时效果
class GuardedObject {
    // 结果
    private Object response;

    // 获取结果
    // timeout 表示要等待多久 2000
    public Object get(long timeout) {
        synchronized (this) {
            // 开始时间 15:00:00
            long begin = System.currentTimeMillis();
            // 经历的时间
            long passedTime = 0;
            while (response == null) {
                // 这一轮循环应该等待的时间
                long waitTime = timeout - passedTime;
                // 经历的时间超过了最大等待时间，退出循环
                if (waitTime <= 0) {
                    break;
                }
                try {
                    this.wait(waitTime);                // 虚假唤醒 15:00:01
                } catch (InterruptedException exception) {
                    exception.printStackTrace();
                }
                // 求得经历的时间
                passedTime = System.currentTimeMillis() - begin;    // 15:00:02  1s
            }
            return response;
        }
    }

    // 产生结果
    public void set(Object response) {
        synchronized (this) {
            // 给结果成员变量赋值
            this.response = response;
            this.notifyAll();
        }
    }
}
~~~



### 11.4 超时测试

- 在最大等待时间内：t1最大等待2s，1s后t2产生结果，所以t1在1s后输出结果

~~~java
package com.example.three;

import lombok.extern.slf4j.Slf4j;

@Slf4j
public class Test4 {
    // 线程1 等待 线程2 的结果
    public static void main(String[] args) {
        GuardedObject guardedObject = new GuardedObject();
        new Thread(() -> {
            log.debug("begin");
            Object response = guardedObject.get(2000);
            log.debug("结果为：{}", response);
        }, "t1").start();

        new Thread(() -> {
            log.debug("begin");
            try {
                Thread.sleep(1000);
            } catch (InterruptedException exception) {
                exception.printStackTrace();
            }
            guardedObject.set(new Object());
        }, "t2").start();
    }
}
// 2024-01-09 22:47:31 [t1] [com.example.three.Test4] [DEBUG] - begin
// 2024-01-09 22:47:31 [t2] [com.example.three.Test4] [DEBUG] - begin
// 2024-01-09 22:47:32 [t1] [com.example.three.Test4] [DEBUG] - 结果为：java.lang.Object@33935965
~~~

- 在最大等待时间外：t1最大等待2s，3s后t2才能产生结果，所以t1在等待2s后就不等了，输出null

~~~java
package com.example.three;

import lombok.extern.slf4j.Slf4j;

@Slf4j
public class Test4 {
    // 线程1 等待 线程2 的结果
    public static void main(String[] args) {
        GuardedObject guardedObject = new GuardedObject();
        new Thread(() -> {
            log.debug("begin");
            Object response = guardedObject.get(2000);
            log.debug("结果为：{}", response);
        }, "t1").start();

        new Thread(() -> {
            log.debug("begin");
            try {
                Thread.sleep(3000);
            } catch (InterruptedException exception) {
                exception.printStackTrace();
            }
            guardedObject.set(new Object());
        }, "t2").start();
    }
}
// 2024-01-09 22:55:12 [t1] [com.example.three.Test4] [DEBUG] - begin
// 2024-01-09 22:55:12 [t2] [com.example.three.Test4] [DEBUG] - begin
// 2024-01-09 22:55:14 [t1] [com.example.three.Test4] [DEBUG] - 结果为：null
~~~

- 还没到最大等待时间，同时也没有产生对象
  - 最大等待时间2s，1s后t2没产生对象，但是唤醒了t1
  - t1进入循环，发现是虚假唤醒，所以继续循环，然后等2s后结束

~~~java
package com.example.three;

import lombok.extern.slf4j.Slf4j;

@Slf4j
public class Test4 {
    // 线程1 等待 线程2 的结果
    public static void main(String[] args) {
        GuardedObject guardedObject = new GuardedObject();
        new Thread(() -> {
            log.debug("begin");
            Object response = guardedObject.get(2000);
            log.debug("结果为：{}", response);
        }, "t1").start();

        new Thread(() -> {
            log.debug("begin");
            try {
                Thread.sleep(1000);
            } catch (InterruptedException exception) {
                exception.printStackTrace();
            }
            guardedObject.set(null);
        }, "t2").start();
    }
}
// 2024-01-09 22:57:22 [t1] [com.example.three.Test4] [DEBUG] - begin
// 2024-01-09 22:57:22 [t2] [com.example.three.Test4] [DEBUG] - begin
// 2024-01-09 22:57:24 [t1] [com.example.three.Test4] [DEBUG] - 结果为：null
~~~



## 12、join原理

### 12.1 源码

- 传递值小于0报异常
- 等于0时，循环查看线程是否还是存活，存活的话进行无限期等待
- 大于0时，就跟上面的超时逻辑一样了

```java
public final synchronized void join(long millis)
throws InterruptedException {
    long base = System.currentTimeMillis();		// 开始时间
    long now = 0;

    if (millis < 0) {
        throw new IllegalArgumentException("timeout value is negative");
    }

    if (millis == 0) {
        while (isAlive()) {
            wait(0);
        }
    } else {
        while (isAlive()) {
            long delay = millis - now;	// 还需等待时间
            if (delay <= 0) {
                break;
            }
            wait(delay);
            now = System.currentTimeMillis() - base;	// 经历的时间
        }
    }
}
```



### 12.2 扩展

- **多任务版GuardedObject **
  - 图中 Futures 就好比居民楼一层的信箱（每个信箱有房间编号），左侧的 t0，t2，t4 就好比等待邮件的居民，右侧的 t1，t3，t5 就好比邮递员 
  - 如果需要在多个类之间使用 GuardedObject 对象，作为参数传递不是很方便，因此设计一个用来解耦的中间类，这样不仅能够解耦【结果等待者】和【结果生产者】，还能够同时支持多个任务的管理

![多任务版扩展](C:\Users\lzy\Desktop\work\Java高级\JUC并发编程\图片\wait\多任务版扩展.png)

~~~java
package com.example.three;

import lombok.extern.slf4j.Slf4j;

import java.util.Hashtable;
import java.util.Map;
import java.util.Set;

@Slf4j
public class Test4 {
    public static void main(String[] args) throws InterruptedException {
        for (int i = 0; i < 3; i++) {
            new Person().start();
        }

        Thread.sleep(1000);

        // 获取信件编号
        Set<Integer> ids = Mailbox.getIds();
        for (Integer id : ids) {
            new Postman(id, "信件：" + id).start();
        }
    }
}

/**
 * 居民类
 */
@Slf4j
class Person extends Thread {
    @Override
    public void run() {
        // 收信
        GuardedObject guardedObject = Mailbox.createGuardedObject();
        log.debug("开始收信 id: {}", guardedObject.getId());
        Object mail = guardedObject.get(5000);
        log.debug("收到信 id: {}, 内容: {}", guardedObject.getId(), mail);
    }
}

/**
 * 邮递员类
 */
@Slf4j
class Postman extends Thread {
    private int id;         //
    private String mail;

    public Postman(int id, String mail) {
        this.id = id;
        this.mail = mail;
    }

    @Override
    public void run() {
        // 送信
        GuardedObject guardedObject = Mailbox.getGuardedObject(id);
        log.debug("送信 id: {}, 内容: {}", guardedObject.getId(), mail);
        guardedObject.set(mail);
    }
}


/**
 * 邮箱，用于中间解耦
 */
class Mailbox {
    private static Map<Integer, GuardedObject> boxes = new Hashtable<>();

    private static int id = 1;
    // 产生唯一id
    private static synchronized int generateId() {
        return id++;
    }

    // 产生GuardedObject对象
    public static GuardedObject createGuardedObject() {
        GuardedObject go = new GuardedObject(generateId());
        boxes.put(go.getId(), go);
        return go;
    }

    // 根据id获取GuardedObject对象
    public static GuardedObject getGuardedObject(int id) {
        return boxes.remove(id);
    }

    // 获取所有id
    public static Set<Integer> getIds() {
        return boxes.keySet();
    }
}


/**
 * 增加超时效果
 */
class GuardedObject {

    // 编号，唯一标识，用于区分GuardedObject
    private int id;

    public GuardedObject(int id) {
        this.id = id;
    }

    public int getId() {
        return id;
    }

    // 结果
    private Object response;

    // 获取结果
    // timeout 表示要等待多久 2000
    public Object get(long timeout) {
        synchronized (this) {
            // 开始时间 15:00:00
            long begin = System.currentTimeMillis();
            // 经历的时间
            long passedTime = 0;
            while (response == null) {
                // 这一轮循环应该等待的时间
                long waitTime = timeout - passedTime;
                // 经历的时间超过了最大等待时间，退出循环
                if (waitTime <= 0) {
                    break;
                }
                try {
                    this.wait(waitTime);                // 虚假唤醒 15:00:01
                } catch (InterruptedException exception) {
                    exception.printStackTrace();
                }
                // 求得经历的时间
                passedTime = System.currentTimeMillis() - begin;    // 15:00:02  1s
            }
            return response;
        }
    }

    // 产生结果
    public void set(Object response) {
        synchronized (this) {
            // 给结果成员变量赋值
            this.response = response;
            this.notifyAll();
        }
    }
}
// 2024-01-10 22:09:16 [Thread-2] [com.example.three.Person] [DEBUG] - 开始收信 id: 3
// 2024-01-10 22:09:16 [Thread-0] [com.example.three.Person] [DEBUG] - 开始收信 id: 1
// 2024-01-10 22:09:16 [Thread-1] [com.example.three.Person] [DEBUG] - 开始收信 id: 2
// 2024-01-10 22:09:17 [Thread-4] [com.example.three.Postman] [DEBUG] - 送信 id: 2, 内容: 信件：2
// 2024-01-10 22:09:17 [Thread-5] [com.example.three.Postman] [DEBUG] - 送信 id: 1, 内容: 信件：1
// 2024-01-10 22:09:17 [Thread-1] [com.example.three.Person] [DEBUG] -收到信 id: 2, 内容: 信件：2
// 2024-01-10 22:09:17 [Thread-3] [com.example.three.Postman] [DEBUG] - 送信 id: 3, 内容: 信件：3
// 2024-01-10 22:09:17 [Thread-0] [com.example.three.Person] [DEBUG] -收到信 id: 1, 内容: 信件：1
// 2024-01-10 22:09:17 [Thread-2] [com.example.three.Person] [DEBUG] -收到信 id: 3, 内容: 信件：3
~~~

- 代码理解
  - **这里是邮递员和居民一一对应**
  - GuardedObject相当于一个个信件，用于链接居民和邮递员
  - Mailbox相当于一个信箱，没封信件都有编号放到了里面
  - Person相当于一个个居民，我在家里等我对应的信件
  - Postman相当于一个个邮递员，我去邮箱里找对应编号的信件，然后送出去，送出去之后，居民就能拿到自己的信件



## 13、设计模式-**(异步)模式之生产者/消费者**

### 13.1 定义

要点 

- 与前面的保护性暂停中的 GuardObject 不同，不需要产生结果和消费结果的线程一一对应 
- 消费队列可以用来平衡生产和消费的线程资源 
- 生产者仅负责产生结果数据，不关心数据该如何处理，而消费者专心处理结果数据 
- 消息队列是有容量限制的，满时不会再加入数据，空时不会再消耗数据 
- JDK 中各种阻塞队列，采用的就是这种模式

![消费者生产者](C:\Users\lzy\Desktop\work\Java高级\JUC并发编程\图片\wait\消费者生产者.png)



### 13.2 实现

~~~java
package com.example.three;

import lombok.extern.slf4j.Slf4j;

import java.util.LinkedList;

@Slf4j
public class Test5 {
    public static void main(String[] args) {
        MessageQueue messageQueue = new MessageQueue(2);

        // 生产者
        for (int i = 0 ; i < 3; i++) {
            int id = i;
            new Thread(() -> {
                try {
                    messageQueue.put(new Message(id, "内容"+id));
                } catch (InterruptedException exception) {
                    exception.printStackTrace();
                }
            }, "生产者"+i).start();
        }

        // 消费者
        new Thread(() -> {
            try {
                while (true) {
                    Thread.sleep(1000);
                    Message take = messageQueue.take();
                }
            } catch (InterruptedException exception) {
                exception.printStackTrace();
            }
        }, "消费者").start();
    }
}


/**
 * 消息队列
 */
@Slf4j
class MessageQueue {
    private LinkedList<Message> list = new LinkedList();
    private int capacity;

    public MessageQueue(int capacity) {
        this.capacity = capacity;
    }

    // 消费消息
    public Message take() throws InterruptedException {
        synchronized (list) {
            // 如果队列为空，则进入等待
            while (list.isEmpty()) {
                log.debug("队列为空，消费者线程进入等待！");
                list.wait();
            }
            // 从队列头部获取消息并返回
            Message message = list.removeFirst();
            log.debug("已消费消息：{}", message);
            list.notifyAll();       // 消费了消息，证明可以有生产了，就唤醒生产者
            return message;
        }
    }

    // 生产消息
    public void put(Message message) throws InterruptedException {
        synchronized (list) {
            // 如果队列已满，则进入等待
            while (list.size() == capacity) {
                log.debug("队列已满，生产者线程进入等待！");
                list.wait();
            }
            // 在队列尾部加上消息
            list.addLast(message);
            log.debug("已生产消息：{}", message.getId());
            list.notifyAll();   // 生产了消息，证明可以有消费了，就唤醒消费者
        }
    }
}


/**
 * 消息体
 */
class Message {
    private int id;
    private Object info;

    public Message(int id, Object info) {
        this.id = id;
        this.info = info;
    }

    public int getId() {
        return id;
    }

    public Object getInfo() {
        return info;
    }
}
// 2024-01-10 22:46:51 [生产者2] [com.example.three.MessageQueue] [DEBUG] - 已生产消息：2
// 2024-01-10 22:46:51 [生产者0] [com.example.three.MessageQueue] [DEBUG] - 已生产消息：0
// 2024-01-10 22:46:51 [生产者1] [com.example.three.MessageQueue] [DEBUG] - 队列已满，生产者线程进入等待！
// 2024-01-10 22:46:52 [消费者] [com.example.three.MessageQueue] [DEBUG] - 已消费消息：com.example.three.Message@6627f0ea
// 2024-01-10 22:46:52 [生产者1] [com.example.three.MessageQueue] [DEBUG] - 已生产消息：1
// 2024-01-10 22:46:53 [消费者] [com.example.three.MessageQueue] [DEBUG] - 已消费消息：com.example.three.Message@68e0125e
// 2024-01-10 22:46:54 [消费者] [com.example.three.MessageQueue] [DEBUG] - 已消费消息：com.example.three.Message@6cdf9b9a
// 2024-01-10 22:46:55 [消费者] [com.example.three.MessageQueue] [DEBUG] - 队列为空，消费者线程进入等待！
~~~

- 代码分析
  - 生产两个消息达到上限，所以生产者进入wait等待
  - 1s后消费者消费了1个，唤醒生产者，又生产了一个
  - 然后1s又消费了一个，直到消费完，消费者进入wait等待



## 14、park/unpark

### 14.1 基本使用

- 它们是 LockSupport 类中的方法
  - 暂停当前线程：LockSupport.park();
  - 恢复某个线程的运行：LockSupport.unpark(暂停线程对象)
- LockSupport.unpark(暂停线程对象)既可以在park之前使用，也可以在之后使用，调用之后不会暂停
- park时，线程是wait阻塞状态

~~~java
package com.example.three;

import lombok.extern.slf4j.Slf4j;

import java.util.concurrent.locks.LockSupport;

@Slf4j
public class Test6 {
    public static void main(String[] args) throws InterruptedException {
        Thread t1 = new Thread(() -> {
            log.debug("start...");
            try {
                Thread.sleep(1000);
            } catch (InterruptedException exception) {
                exception.printStackTrace();
            }
            log.debug("park...");
            LockSupport.park();
            log.debug("resume...");
        },"t1");
        t1.start();

        t1.sleep(2000);
        log.debug("unpark...");
        LockSupport.unpark(t1);
    }
}
// t1睡1s，主线程睡2s的情况
// 2024-01-10 22:59:52 [t1] [com.example.three.Test6] [DEBUG] - start...
// 2024-01-10 22:59:53 [t1] [com.example.three.Test6] [DEBUG] - park...
// 2024-01-10 22:59:54 [main] [com.example.three.Test6] [DEBUG] - unpark...
// 2024-01-10 22:59:54 [t1] [com.example.three.Test6] [DEBUG] - resume...

// t1睡2s，主线程睡1s的情况
// 2024-01-10 23:00:35 [t1] [com.example.three.Test6] [DEBUG] - start...
// 2024-01-10 23:00:36 [main] [com.example.three.Test6] [DEBUG] - unpark...
// 2024-01-10 23:00:37 [t1] [com.example.three.Test6] [DEBUG] - park...
// 2024-01-10 23:00:37 [t1] [com.example.three.Test6] [DEBUG] - resume...
~~~



### 14.2 特点

与 Object 的 wait & notify 相比 

- wait，notify 和 notifyAll 必须配合 Object Monitor 一起使用，而 park，unpark 不必
- park & unpark 是以线程为单位来【阻塞】和【唤醒】线程，而 notify 只能随机唤醒一个等待线程，notifyAll是唤醒所有等待线程，就不那么【精确】 
- park & unpark 可以先 unpark，而 wait & notify 不能先 notify 



### 14.3 底层原理

- 每个线程都有自己的一个Parker 对象，由三部分组成 `_counter` ， `_cond` 和`_mutex` 

- 打个比喻 

  - 线程就像一个旅人，Parker 就像他随身携带的背包，`_cond`条件变量就好比背包中的帐篷。 `_counter`就好比背包中的备用干粮（0 为耗尽，1 为充足） 
  - 调用 park 就是要看需不需要停下来歇息 


  - - 如果备用干粮耗尽，那么钻进帐篷歇息 
    - 如果备用干粮充足，那么不需停留，继续前进 


  - 调用 unpark，就好比令干粮充足 （_counter变为1）


  - - 如果这时线程还在帐篷，就唤醒让他继续前进 
    - 如果这时线程还在运行，那么下次他调用 park 时，仅是消耗掉备用干粮，不需停留，继续前进 


  - - - 因为背包空间有限，多次调用 unpark 仅会补充一份备用干粮,也就是多次unpark后只会让紧跟着的一次park失效



#### 14.3.1 先调用park

![park_unpark1](C:\Users\lzy\Desktop\work\Java高级\JUC并发编程\图片\wait\park_unpark1.png)

\1. 当前线程调用 Unsafe.park() 方法 

\2. 检查 _counter ，本情况为 0，这时，获得 _mutex 互斥锁 

\3. 线程进入 _cond 条件变量阻塞 

\4. 设置 _counter = 0



#### 14.3.2 再调用unpark

![park_unpark1](C:\Users\lzy\Desktop\work\Java高级\JUC并发编程\图片\wait\park_unpark2.png)

\1. 调用 Unsafe.unpark(Thread_0) 方法，设置 _counter 为 1 

\2. 唤醒 _cond 条件变量中的 Thread_0 

\3. Thread_0 恢复运行 

\4. 设置 _counter 为 0



#### 14.3.3 先调用unpark 再调用park

![park_unpark1](C:\Users\lzy\Desktop\work\Java高级\JUC并发编程\图片\wait\park_unpark3.png)

\1. 调用 Unsafe.unpark(Thread_0) 方法，设置 _counter 为 1 

\2. 当前线程调用 Unsafe.park() 方法 

\3. 检查 _counter ，本情况为 1，这时线程无需阻塞，继续运行 

\4. 设置 _counter 为 0 



## 15、重新理解线程状态转换

- 概览图

![线程状态概览图](C:\Users\lzy\Desktop\work\Java高级\JUC并发编程\图片\wait\线程状态概览图.png)



### 15.1 NEW --> RUNNABLE

- 当调用 t.start() 方法时，由 NEW --> RUNNABLE



### 15.2 RUNNABLE <--> WAITING

- **t 线程**用 **synchronized(obj)** 获取了对象锁后 

  - 调用 obj.wait() 方法时，**t 线程**从 RUNNABLE --> WAITING 
  - 调用 obj.notify() ， obj.notifyAll() ， t.interrupt() 时 


  - - 竞争锁成功，**t 线程**从WAITING --> RUNNABLE 
    - 竞争锁失败，**t 线程**从WAITING --> BLOCKED

~~~java
public class TestWaitNotify {
    final static Object obj = new Object();
    
    public static void main(String[] args) {
        
        new Thread(() -> {
            synchronized (obj) {
                log.debug("执行....");
                try {
                    obj.wait();
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }
                log.debug("其它代码...."); // 断点
            }
        },"t1").start();
        
        new Thread(() -> {
            synchronized (obj) {
                log.debug("执行....");
                try {
                    obj.wait();
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }
                log.debug("其它代码...."); // 断点
            }
        },"t2").start();
        
        sleep(0.5);
        log.debug("唤醒 obj 上其它线程");
        synchronized (obj) {
            obj.notifyAll(); // 唤醒obj上所有等待线程 断点
        }
        
    }
}
~~~



### 15.3 RUNNABLE <--> WAITING

- **当前线程**调用 t.join() 方法时，**当前线程**从 RUNNABLE --> WAITING 


- - 注意是**当前线程**在**t 线程对象**的监视器上等待 （即：当前线程进入WAITING ，而不是t进入WAITING ）


- **t 线程**运行结束，或调用了**当前线程**的 interrupt() 时，**当前线程**从 WAITING --> RUNNABLE



### 15.4 RUNNABLE <--> WAITING

- 当前线程调用 LockSupport.park() 方法会让当前线程从 RUNNABLE --> WAITING 
- 调用 LockSupport.unpark(目标线程) 或调用了线程 的 interrupt() ，会让目标线程从 WAITING -->RUNNABLE 



### 15.5 RUNNABLE <--> TIMED_WAITING

**t ****线程**用 synchronized(obj) 获取了对象锁后 

- 调用 obj.wait(long n) 方法时，**t 线程**从 RUNNABLE --> TIMED_WAITING 
- **t 线程**等待时间超过了 n 毫秒，或调用 obj.notify() ， obj.notifyAll() ， t.interrupt() 时 


- - 竞争锁成功，**t 线程**从TIMED_WAITING --> RUNNABLE 
  - 竞争锁失败，**t 线程**从TIMED_WAITING --> BLOCKED



### 15.6 RUNNABLE <--> TIMED_WAITING

- **当前线程**调用 t.join(long n) 方法时，**当前线程**从 RUNNABLE --> TIMED_WAITING 


- - 注意是**当前线程**在**t 线程对象**的监视器上等待 


- **当前线程**等待时间超过了 n 毫秒，或**t 线程**运行结束，或调用了**当前线程**的 interrupt() 时，**当前线程**从 TIMED_WAITING --> RUNNABLE



### 15.7 RUNNABLE <--> TIMED_WAITING

- 当前线程调用 Thread.sleep(long n) ，当前线程从 RUNNABLE --> TIMED_WAITING 
- **当前线程**等待时间超过了 n 毫秒，**当前线程**从TIMED_WAITING --> RUNNABLE



### **15.8 RUNNABLE <--> TIMED_WAITING**

- 当前线程调用 LockSupport.parkNanos(long nanos) 或 LockSupport.parkUntil(long millis) 时，**当前线 程**从 RUNNABLE --> TIMED_WAITING 
- 调用 LockSupport.unpark(目标线程) 或调用了线程 的 interrupt() ，或是等待超时，会让目标线程从 TIMED_WAITING--> RUNNABLE



### 15.9 RUNNABLE <--> BLOCKED

- **t 线程**用synchronized(obj) 获取对象锁时如果竞争失败，从RUNNABLE --> BLOCKED 
- 持 obj 锁线程的同步代码块执行完毕，会唤醒该对象上所有 BLOCKED的线程重新竞争，如果其中 **t 线程**竞争 成功，从 BLOCKED --> RUNNABLE ，其它失败的线程仍然BLOCKED 



### 15.10 RUNNABLE --> TERMINATED 

- 当前线程所有代码运行完毕，进入 TERMINATED







## 16、多把锁&活跃性

### 16.1 多把锁

- 多把不相干的锁 
  - 一间大屋子有两个功能：睡觉、学习，互不相干。 
  - 现在小南要学习，小女要睡觉，但如果只用一间屋子（一个对象锁）的话，那么并发度很低 
  - 解决方法是准备多个房间（多个对象锁）

~~~java
class BigRoom {
    
    public void sleep() {
        synchronized (this) {
            log.debug("sleeping 2 小时");
            Sleeper.sleep(2);
        }
    }
    
    public void study() {
        synchronized (this) {
            log.debug("study 1 小时");
            Sleeper.sleep(1);
        }
    }
}
~~~

- 执行

~~~java
BigRoom bigRoom = new BigRoom();

new Thread(() -> {
    bigRoom.study();
},"小南").start();

new Thread(() -> {
    bigRoom.sleep();
},"小女").start();

// 12:13:54.471 [小南] c.BigRoom - study 1 小时
// 12:13:55.476 [小女] c.BigRoom - sleeping 2 小时
~~~

- 改进

~~~java
class BigRoom {
    private final Object studyRoom = new Object();
    private final Object bedRoom = new Object();
    
    public void sleep() {
        synchronized (bedRoom) {
            log.debug("sleeping 2 小时");
            Sleeper.sleep(2);
        }
    }
    
    public void study() {
        synchronized (studyRoom) {
            log.debug("study 1 小时");
            Sleeper.sleep(1);
        }
    }
}

// 12:15:35.069 [小南] c.BigRoom - study 1 小时
// 12:15:35.069 [小女] c.BigRoom - sleeping 2 小时
~~~

- **将锁的粒度细分** 
  - **好处，是可以增强并发度** 
  - **坏处，如果一个线程需要同时获得多把锁，就容易发生死锁**



### 16.2 活跃性

#### 16.2.1 死锁

- 有这样的情况：一个线程需要同时获取多把锁，这时就容易发生死锁 
- 示例
  - **t1线程** 获得 **A对象** 锁，接下来想获取 **B对象** 的锁
  - **t2线程** 获得 **B对象** 锁，接下来想获取 **A对象** 的锁

```java
package com.example.three;

import lombok.extern.slf4j.Slf4j;

@Slf4j
public class Test7 {
    public static void main(String[] args) {
        Object A = new Object();
        Object B = new Object();

        Thread t1 = new Thread(() -> {
            synchronized (A) {
                log.debug("lock A");
                try {
                    Thread.sleep(1000);
                } catch (InterruptedException exception) {
                    exception.printStackTrace();
                }
                synchronized (B) {
                    log.debug("lock B");
                    log.debug("操作...");
                }
            }
        }, "t1");

        Thread t2 = new Thread(() -> {
            synchronized (B) {
                log.debug("lock B");
                try {
                    Thread.sleep(500);
                } catch (InterruptedException exception) {
                    exception.printStackTrace();
                }
                synchronized (A) {
                    log.debug("lock A");
                    log.debug("操作...");
                }
            }
        }, "t2");

        t1.start();
        t2.start();
    }
}
// 2024-01-21 17:10:46 [t1] [com.example.three.Test7] [DEBUG] - lock A
// 2024-01-21 17:10:46 [t2] [com.example.three.Test7] [DEBUG] - lock B
```



#### 16.2.2 定位死锁

- 检测死锁可以使用 jconsole工具，或者使用 jps 定位进程 id，再用 jstack 定位死锁

![定位死锁1](C:\Users\lzy\Desktop\work\Java高级\JUC并发编程\图片\wait\定位死锁1.png)

- 可以查看出线程状态、第几行出的问题、等待11d8的锁，锁住了11e8

![定位死锁2](C:\Users\lzy\Desktop\work\Java高级\JUC并发编程\图片\wait\定位死锁2.png)

- 还会打印一个java级别的死锁信息

![定位死锁3](C:\Users\lzy\Desktop\work\Java高级\JUC并发编程\图片\wait\定位死锁3.png)

- 也可以用jconsole

![定位死锁4](C:\Users\lzy\Desktop\work\Java高级\JUC并发编程\图片\wait\定位死锁4.png)

- **避免死锁要注意加锁顺序** 
- 另外如果由于某个线程进入了死循环，导致其它线程一直等待，对于这种情况 linux 下可以通过 top 先定位到CPU 占用高的 Java 进程，再利用 **top -Hp 进程id** 来定位是哪个线程，最后再用 jstack 排查



#### 16.2.3 哲学家就餐问题

![哲学家就餐](C:\Users\lzy\Desktop\work\Java高级\JUC并发编程\图片\wait\哲学家就餐.png)

- 有五位哲学家，围坐在圆桌旁。 
  - 他们只做两件事，思考和吃饭，思考一会吃口饭，吃完饭后接着思考。 
  - 吃饭时要用两根筷子吃，桌上共有 5 根筷子，每位哲学家左右手边各有一根筷子。 
  - 如果筷子被身边的人拿着，自己就得等待 


- 筷子类

~~~java
class Chopstick {
    String name;

    public Chopstick(String name) {
        this.name = name;
    }

    @Override
    public String toString() {
        return "筷子{" + name + '}';
    }
}
~~~

- 哲学家类

~~~java
@Slf4j
class Philosopher extends Thread {
    Chopstick left;
    Chopstick right;

    public Philosopher(String name, Chopstick left, Chopstick right) {
        super(name);
        this.left = left;
        this.right = right;
    }

    private void eat() throws InterruptedException {
        log.debug("eating...");
        Thread.sleep(1000);
    }

    @SneakyThrows
    @Override
    public void run() {
        while (true) {
            // 获得左手筷子
            synchronized (left) {
                // 获得右手筷子
                synchronized (right) {
                    // 吃饭
                    eat();
                }
                // 放下右手筷子
            }
            // 放下左手筷子
        }
    }
}
~~~

- 就餐

~~~java
package com.example.three;

import lombok.SneakyThrows;
import lombok.extern.slf4j.Slf4j;
public class Test4 {
    public static void main(String[] args) {
        Chopstick c1 = new Chopstick("1");
        Chopstick c2 = new Chopstick("2");
        Chopstick c3 = new Chopstick("3");
        Chopstick c4 = new Chopstick("4");
        Chopstick c5 = new Chopstick("5");

        new Philosopher("苏格拉底", c1, c2).start();
        new Philosopher("柏拉图", c2, c3).start();
        new Philosopher("亚里士多德", c3, c4).start();
        new Philosopher("赫拉克利特", c4, c5).start();
        new Philosopher("阿基米德", c5, c1).start();
    }
}
~~~

- 结果
  - 执行不多会，就执行不下去了

~~~java
12:33:15.575 [苏格拉底] c.Philosopher - eating... 
12:33:15.575 [亚里士多德] c.Philosopher - eating... 
12:33:16.580 [阿基米德] c.Philosopher - eating... 
12:33:17.580 [阿基米德] c.Philosopher - eating... 
// 卡在这里, 不向下运行
~~~

- 使用 jconsole 检测死锁，发现

~~~java
-------------------------------------------------------------------------
名称: 阿基米德
状态: cn.itcast.Chopstick@1540e19d (筷子1) 上的BLOCKED, 拥有者: 苏格拉底
总阻止数: 2, 总等待数: 1
    
堆栈跟踪:
cn.itcast.Philosopher.run(TestDinner.java:48)
 - 已锁定 cn.itcast.Chopstick@6d6f6e28 (筷子5)
-------------------------------------------------------------------------
名称: 苏格拉底
状态: cn.itcast.Chopstick@677327b6 (筷子2) 上的BLOCKED, 拥有者: 柏拉图
总阻止数: 2, 总等待数: 1
    
堆栈跟踪:
cn.itcast.Philosopher.run(TestDinner.java:48)
 - 已锁定 cn.itcast.Chopstick@1540e19d (筷子1)
-------------------------------------------------------------------------
名称: 柏拉图
状态: cn.itcast.Chopstick@14ae5a5 (筷子3) 上的BLOCKED, 拥有者: 亚里士多德
总阻止数: 2, 总等待数: 0
    
堆栈跟踪:
cn.itcast.Philosopher.run(TestDinner.java:48)
 - 已锁定 cn.itcast.Chopstick@677327b6 (筷子2)
-------------------------------------------------------------------------
名称: 亚里士多德
状态: cn.itcast.Chopstick@7f31245a (筷子4) 上的BLOCKED, 拥有者: 赫拉克利特
总阻止数: 1, 总等待数: 1
    
堆栈跟踪:
cn.itcast.Philosopher.run(TestDinner.java:48)
 - 已锁定 cn.itcast.Chopstick@14ae5a5 (筷子3)
-------------------------------------------------------------------------
名称: 赫拉克利特
状态: cn.itcast.Chopstick@6d6f6e28 (筷子5) 上的BLOCKED, 拥有者: 阿基米德
总阻止数: 2, 总等待数: 0
    
堆栈跟踪:
cn.itcast.Philosopher.run(TestDinner.java:48)
 - 已锁定 cn.itcast.Chopstick@7f31245a (筷子4)
~~~

- **这种线程没有按预期结束，执行不下去的情况，归类为【活跃性】问题，除了死锁以外，还有活锁和饥饿者两种情况**



#### 16.2.4 活锁

- **活锁出现在两个线程互相改变对方的结束条件，最后谁也无法结束**

~~~java
public class TestLiveLock {
    static volatile int count = 10;
    static final Object lock = new Object();
    
    public static void main(String[] args) {
        new Thread(() -> {
            // 期望减到 0 退出循环
            while (count > 0) {
                sleep(0.2);
                count--;
                log.debug("count: {}", count);
            }
        }, "t1").start();
        
        new Thread(() -> {
            // 期望超过 20 退出循环
            while (count < 20) {
                sleep(0.2);
                count++;
                log.debug("count: {}", count);
            }
        }, "t2").start();
        
    }
}
~~~

- 结果分析
  - t1一直在减，t2一直在增，所以会导致俩的while结束条件一直得不到满足
  - 所以这就会一直执行下去，这就是活锁



#### 16.2.5 饥饿

- 很多教程中把饥饿定义为，一个线程由于优先级太低，始终得不到 CPU 调度执行，也不能够结束
- 先来看看使用顺序加锁的方式解决之前的死锁问题

![顺序加锁1](C:\Users\lzy\Desktop\work\Java高级\JUC并发编程\图片\wait\顺序加锁1.png)

- 顺序加锁的解决方案

![顺序枷锁2](C:\Users\lzy\Desktop\work\Java高级\JUC并发编程\图片\wait\顺序枷锁2.png)

- 但顺序加锁容易产生饥饿问题，例如：哲学家就餐时

~~~java
new Philosopher("苏格拉底", c1, c2).start();
new Philosopher("柏拉图", c2, c3).start();
new Philosopher("亚里士多德", c3, c4).start();
new Philosopher("赫拉克利特", c4, c5).start();
// new Philosopher("阿基米德", c5, c1).start();
new Philosopher("阿基米德", c1, c5).start(); //线程饥饿
~~~

- 结果就是
  - 赫拉克利特 一直在吃，其他的几个吃的很少





## 17、ReentrantLock

### 17.1 ReentrantLock

相对于 synchronized 它具备如下特点 

- 可中断 
- 可以设置超时时间 
- 可以设置为公平锁 
- 支持多个条件变量 

与 synchronized 一样，都支持可重入 

基本语法:

~~~java
// 获取锁
reentrantLock.lock();
try {
    // 临界区
} finally {
    // 释放锁
    reentrantLock.unlock();
}
~~~



### 17.2 可重入

- 可重入是指同一个线程如果首次获得了这把锁，那么因为它是这把锁的拥有者，因此有权利再次获取这把锁 
- 如果是不可重入锁，那么第二次获得锁时，自己也会被锁挡住

~~~java
static ReentrantLock lock = new ReentrantLock();

public static void main(String[] args) {
    method1();
}

public static void method1() {
    lock.lock();
    try {
        log.debug("execute method1");
        method2();
    } finally {
        lock.unlock();
    }
}

public static void method2() {
    lock.lock();
    try {
        log.debug("execute method2");
        method3();
    } finally {
        lock.unlock();
    }
}

public static void method3() {
    lock.lock();
    try {
        log.debug("execute method3");
    } finally {
        lock.unlock();
    }
}
// 17:59:11.862 [main] c.TestReentrant - execute method1 
// 17:59:11.865 [main] c.TestReentrant - execute method2 
// 17:59:11.865 [main] c.TestReentrant - execute method3
~~~

- 结果分析：因为是可重入锁，所以执行到method2、method3不会锁住而执行不了，故而是三行都打印




### 17.3 可打断lockInterruptibly

- 使用lockInterruptibly()方法，就可以通过interrupt()来打断了，让其不再继续傻等下去
- 如果没有竞争，就会获取到锁，正常执行流程
- 如果有竞争，就会进入阻塞队列等待，但是可以被打断，让其不再继续傻等
- **lock()方法不能被打断，lockInterruptibly()才可以，这样可以避免死锁，防止死等**


~~~java
package com.example.three;

import lombok.extern.slf4j.Slf4j;

import java.util.concurrent.locks.ReentrantLock;

@Slf4j
public class Test9 {
    public static void main(String[] args) {
        ReentrantLock lock = new ReentrantLock();

        Thread t1 = new Thread(() -> {
            log.debug("启动...");

            try {
                //没有竞争就会获取锁
                //有竞争就进入阻塞队列等待,但可以被打断
                lock.lockInterruptibly();
                //lock.lock(); //不可打断
            } catch (InterruptedException e) {
                e.printStackTrace();
                log.debug("等锁的过程中被打断");
                return;
            }

            try {
                log.debug("获得了锁");
            } finally {
                lock.unlock();
            }
        }, "t1");

        lock.lock();
        log.debug("获得了锁");
        t1.start();

        try {
            Thread.sleep(1000);
            log.debug("执行打断");
            t1.interrupt();
        } catch (InterruptedException exception) {
            exception.printStackTrace();
        } finally {
            lock.unlock();
        }
    }
}
// 2024-02-24 14:03:06 [main] [com.example.three.Test9] [DEBUG] - 获得了锁
// 2024-02-24 14:03:06 [t1] [com.example.three.Test9] [DEBUG] - 启动...
// 2024-02-24 14:03:07 [main] [com.example.three.Test9] [DEBUG] - 执行打断
// 2024-02-24 14:03:07 [t1] [com.example.three.Test9] [DEBUG] - 等锁的过程中被打断
~~~



### 17.4 可超时

- **锁可以设置超时时间，如果在时间内能获得锁就继续完成业务，获取不到就进入等待队列，同时可以被打断**
- 执行tryLock()方法会有一个返回值，为true就是获取到锁，fasle就是获取不到
- 不加参数就是立即失败，加俩参数：(时间，单位)就是等待时间
- 立即失败例子如下：

~~~java
package com.example.three;

import lombok.extern.slf4j.Slf4j;

import java.util.concurrent.locks.ReentrantLock;

@Slf4j
public class Test9 {
    public static void main(String[] args) {
        ReentrantLock lock = new ReentrantLock();

        Thread t1 = new Thread(() -> {
            log.debug("启动...");
            if (!lock.tryLock()) {
                log.debug("获取立刻失败，返回");
                return;
            }
            try {
                log.debug("获得了锁");
            } finally {
                lock.unlock();
            }
        }, "t1");

        lock.lock();
        log.debug("获得了锁");
        t1.start();

        try {
            Thread.sleep(2000);
        } catch (InterruptedException exception) {
            exception.printStackTrace();
        } finally {
            lock.unlock();
        }
    }
}
// 2024-02-24 14:16:09 [main] [com.example.three.Test9] [DEBUG] - 获得了锁
// 2024-02-24 14:16:09 [t1] [com.example.three.Test9] [DEBUG] - 启动...
// 2024-02-24 14:16:09 [t1] [com.example.three.Test9] [DEBUG] - 获取立刻失败，返回
~~~

- 尝试一定时间失败

~~~java
package com.example.three;

import lombok.extern.slf4j.Slf4j;

import java.util.concurrent.TimeUnit;
import java.util.concurrent.locks.ReentrantLock;

@Slf4j
public class Test9 {
    public static void main(String[] args) {
        ReentrantLock lock = new ReentrantLock();

        Thread t1 = new Thread(() -> {
            log.debug("启动...");

            try {
                if (!lock.tryLock(1, TimeUnit.SECONDS)) {
                    log.debug("获取等待 1s 后失败，返回");
                    return;
                }
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
            try {
                log.debug("获得了锁");
            } finally {
                lock.unlock();
            }
        }, "t1");

        lock.lock();
        log.debug("获得了锁");
        t1.start();

        try {
            Thread.sleep(2000);
        } catch (InterruptedException exception) {
            exception.printStackTrace();
        } finally {
            lock.unlock();
        }
    }
}
// 2024-02-24 14:17:16 [main] [com.example.three.Test9] [DEBUG] - 获得了锁
// 2024-02-24 14:17:16 [t1] [com.example.three.Test9] [DEBUG] - 启动...
// 2024-02-24 14:17:17 [t1] [com.example.three.Test9] [DEBUG] - 获取等待 1s 后失败，返回
~~~

- 尝试一定时间成功

~~~java
package com.example.three;

import lombok.extern.slf4j.Slf4j;

import java.util.concurrent.TimeUnit;
import java.util.concurrent.locks.ReentrantLock;

@Slf4j
public class Test9 {
    public static void main(String[] args) {
        ReentrantLock lock = new ReentrantLock();

        Thread t1 = new Thread(() -> {
            log.debug("启动...");

            try {
                if (!lock.tryLock(2, TimeUnit.SECONDS)) {
                    log.debug("获取等待 1s 后失败，返回");
                    return;
                }
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
            try {
                log.debug("获得了锁");
            } finally {
                lock.unlock();
            }
        }, "t1");

        lock.lock();
        log.debug("获得了锁");
        t1.start();

        try {
            Thread.sleep(1000);
        } catch (InterruptedException exception) {
            exception.printStackTrace();
        } finally {
            lock.unlock();
        }
    }
}
// 2024-02-24 14:18:38 [main] [com.example.three.Test9] [DEBUG] - 获得了锁
// 2024-02-24 14:18:38 [t1] [com.example.three.Test9] [DEBUG] - 启动...
// 2024-02-24 14:18:39 [t1] [com.example.three.Test9] [DEBUG] - 获得了锁
~~~



### 17.5 解决哲学家就餐问题

- 筷子

~~~java
class Chopstick extends ReentrantLock {
    String name;

    public Chopstick(String name) {
        this.name = name;
    }

    @Override
    public String toString() {
        return "筷子{" + name + '}';
    }
}
~~~

- 哲学家

~~~java
@Slf4j
class Philosopher extends Thread {
    Chopstick left;
    Chopstick right;

    public Philosopher(String name, Chopstick left, Chopstick right) {
        super(name);
        this.left = left;
        this.right = right;
    }

    private void eat() throws InterruptedException {
        log.debug("eating...");
        Thread.sleep(1000);
    }

    @SneakyThrows
    @Override
    public void run() {
        while (true) {
            // 尝试获得左手筷子
            if (left.tryLock()) {
                try {
                    // 尝试获得右手筷子
                    if (right.tryLock()) {
                        try {
                            eat();
                        } finally {
                            right.unlock();
                        }
                    }
                } finally {
                    left.unlock();
                }
            }
        }
    }
}
~~~

- 将筷子类继承ReentrantLock这个，那么在哲学家类中，就可以用锁的tryLock了
- 如果没获取到锁，会释放自己原有的锁，这样就不会导致死锁问题，字然就解决了就餐问题了



### 17.6 公平锁

- 公平: 先来就能先执行
- 不公平: 不保证先来就先执行
- **ReentrantLock 默认是不公平的**，可以通过构造方法传入一个boolean值来决定是否开启


- 语法

~~~java
package com.example.three;

import lombok.extern.slf4j.Slf4j;

import java.util.concurrent.locks.Condition;
import java.util.concurrent.locks.ReentrantLock;

@Slf4j
public class Test10 {

    static ReentrantLock lock = new ReentrantLock();
    public static void main(String[] args) throws InterruptedException {
        // 创建一个新的条件变量（休息室）
        Condition condition1 = lock.newCondition();
        Condition condition2 = lock.newCondition();

        lock.lock();
        // 进入休息室等待
        condition1.await();

        // 唤醒
        condition1.signal();
        condition1.signalAll();
    }
}
~~~

- 注意点：
  - await 前需要获得锁 
  - await 执行后，会释放锁，进入 conditionObject 等待 
  - await 的线程被唤醒（或打断、或超时）去重新竞争 lock 锁 
  - 竞争 lock 锁成功后，从 await 后继续执行 
- 例子

~~~java
package com.example.three;

import lombok.extern.slf4j.Slf4j;

import java.util.concurrent.locks.Condition;
import java.util.concurrent.locks.ReentrantLock;

@Slf4j
public class Test10 {

    static ReentrantLock lock = new ReentrantLock();

    static Condition waitCigaretteQueue = lock.newCondition();
    static Condition waitbreakfastQueue = lock.newCondition();

    static volatile boolean hasCigrette = false;
    static volatile boolean hasBreakfast = false;

    public static void main(String[] args) throws InterruptedException {

        new Thread(() -> {
            try {
                lock.lock();
                while (!hasCigrette) {
                    try {
                        waitCigaretteQueue.await();
                    } catch (InterruptedException e) {
                        e.printStackTrace();
                    }
                }
                log.debug("等到了它的烟");
            } finally {
                lock.unlock();
            }
        }).start();

        new Thread(() -> {
            try {
                lock.lock();
                while (!hasBreakfast) {
                    try {
                        waitbreakfastQueue.await();
                    } catch (InterruptedException e) {
                        e.printStackTrace();
                    }
                }
                log.debug("等到了它的早餐");
            } finally {
                lock.unlock();
            }
        }).start();

        Thread.sleep(1000);
        sendBreakfast();
        Thread.sleep(1000);
        sendCigarette();
    }

    private static void sendCigarette() {
        lock.lock();
        try {
            log.debug("送烟来了");
            hasCigrette = true;
            waitCigaretteQueue.signal();
        } finally {
            lock.unlock();
        }
    }

    private static void sendBreakfast() {
        lock.lock();
        try {
            log.debug("送早餐来了");
            hasBreakfast = true;
            waitbreakfastQueue.signal();
        } finally {
            lock.unlock();
        }
    }
}
// 2024-02-25 22:30:02 [main] [com.example.three.Test10] [DEBUG] - 送早餐来了
// 2024-02-25 22:30:02 [Thread-1] [com.example.three.Test10] [DEBUG] - 等到了它的早餐
// 2024-02-25 22:30:03 [main] [com.example.three.Test10] [DEBUG] - 送烟来了
// 2024-02-25 22:30:03 [Thread-0] [com.example.three.Test10] [DEBUG] - 等到了它的烟
~~~



## 18、同步模式之顺序控制

### 18.1 固定运行顺序

- 比如，必须先 2 后 1 打印 



#### 18.1.1 wait notify 版

~~~java
// 用来同步的对象
static Object obj = new Object();
// t2 运行标记， 代表 t2 是否执行过
static boolean t2runed = false;

public static void main(String[] args) {
    
    Thread t1 = new Thread(() -> {
        synchronized (obj) {
            // 如果 t2 没有执行过
            while (!t2runed) { 
                try {
                    // t1 先等一会
                    obj.wait(); 
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }
            }
        }
        System.out.println(1);
    });
    
    Thread t2 = new Thread(() -> {
        System.out.println(2);
        synchronized (obj) {
            // 修改运行标记
            t2runed = true;
            // 通知 obj 上等待的线程（可能有多个，因此需要用 notifyAll）
            obj.notifyAll();
        }
    });
    
    t1.start();
    t2.start();
}
~~~

- 结果分析：
  - 如果先线程1，那么t2runed为fasle，进入waitSet等待；然后执行线程2，释放锁并唤醒线程1，t2runed置为true
  - 先线程2，执行完，t2runed置为true，再执行线程1便可直接执行



#### 18.1.2 Park Unpark 版

- 可以看到，实现上很麻烦： 
  - 首先，需要保证先 wait 再 notify，否则 wait 线程永远得不到唤醒。因此使用了『运行标记』来判断该不该wait 
  - 第二，如果有些干扰线程错误地 notify 了 wait 线程，条件不满足时还要重新等待，使用了 while 循环来解决此问题 
  - 最后，唤醒对象上的 wait 线程需要使用 notifyAll，因为『同步对象』上的等待线程可能不止一个 


- 可以使用 LockSupport 类的 park 和 unpark 来简化上面的题目：

~~~java
Thread t1 = new Thread(() -> {
    try { Thread.sleep(1000); } catch (InterruptedException e) { }
    // 当没有『许可』时，当前线程暂停运行；有『许可』时，用掉这个『许可』，当前线程恢复运行
    LockSupport.park();
    System.out.println("1");
});

Thread t2 = new Thread(() -> {
    System.out.println("2");
    // 给线程 t1 发放『许可』（多次连续调用 unpark 只会发放一个『许可』）
    LockSupport.unpark(t1);
});

t1.start();
t2.start();
~~~

- park 和 unpark 方法比较灵活，他俩谁先调用，谁后调用无所谓。
- 并且是以线程为单位进行『暂停』和『恢复』,不需要『同步对象』和『运行标记』



### 18.2 交替输出

- 线程 1 输出 a 5 次，线程 2 输出 b 5 次，线程 3 输出 c 5 次。现在要求输出 abcabcabcabcabc 怎么实现 



#### 18.2.1 wait notify 版

~~~java
package com.example.three;

import lombok.extern.slf4j.Slf4j;

@Slf4j
public class Test11 {

    public static void main(String[] args) {
        SyncWaitNotify syncWaitNotify = new SyncWaitNotify(1, 5);
        new Thread(() -> {
            syncWaitNotify.print(1, 2, "a");
        }).start();

        new Thread(() -> {
            syncWaitNotify.print(2, 3, "b");
        }).start();

        new Thread(() -> {
            syncWaitNotify.print(3, 1, "c");
        }).start();
    }

}

// print  flag	next
//   a     1     2
//   b     2 	 3
//   c     3 	 1
class SyncWaitNotify {
    private int flag;
    private int loopNumber;

    public SyncWaitNotify(int flag, int loopNumber) {
        this.flag = flag;
        this.loopNumber = loopNumber;
    }

    public void print(int waitFlag, int nextFlag, String str) {
        for (int i = 0; i < loopNumber; i++) {
            synchronized (this) {
                while (this.flag != waitFlag) {
                    try {
                        this.wait();
                    } catch (InterruptedException e) {
                        e.printStackTrace();
                    }
                }

                System.out.print(str);
                flag = nextFlag;
                this.notifyAll();
            }
        }
    }
} 

// abcabcabcabcabc
// 如果执行标记flag等于当前的话，就打印，反之就进入wait等待；当其他线程执行完之后，唤醒wait中的线程，并把flag置为下一个，这样其他俩对应的那个就能打印，而另一个还是进入wait等待；等到这个执行完，再次唤醒，这个就能执行了
~~~



#### 18.2.2 Lock 条件变量版

~~~java
package com.example.three;

import lombok.extern.slf4j.Slf4j;

import java.util.concurrent.locks.Condition;
import java.util.concurrent.locks.ReentrantLock;

@Slf4j
public class Test12 {
    public static void main(String[] args) throws InterruptedException {

        AwaitSignal awaitSignal = new AwaitSignal(5);
        Condition a = awaitSignal.newCondition();
        Condition b = awaitSignal.newCondition();
        Condition c = awaitSignal.newCondition();

        new Thread(() -> {
            awaitSignal.print("a", a, b);
        }).start();

        new Thread(() -> {
            awaitSignal.print("b", b, c);
        }).start();

        new Thread(() -> {
            awaitSignal.print("c", c, a);
        }).start();

        Thread.sleep(1000);

        awaitSignal.lock();
        try {
            System.out.println("开始...");
            a.signal();
        } finally {
            awaitSignal.unlock();
        }

    }
}


class AwaitSignal extends ReentrantLock {
    private int loopNumber;

    public AwaitSignal(int loopNumber) {
        this.loopNumber = loopNumber;
    }

    // 参数1 打印内容， 参数2 进入哪一间休息室, 参数3 下一间休息室
    public void print(String str, Condition current, Condition next) {
        for (int i = 0; i < loopNumber; i++) {
            lock();
            try {
                current.await();
                System.out.print(str);
                next.signal();
            } catch (InterruptedException e) {
                e.printStackTrace();
            } finally {
                unlock();
            }
        }
    }
}
// abcabcabcabcabc
// 搞三个休息室，每个锁住进入自己的休息室，然后执行lock，如果是当前的获取到锁了，就可以直接打印，不是就进入自己等待队列中。当这个线程打印完，就去唤醒下一个休息室的，便可让对应的起来工作了
~~~



#### 18.2.3 park/unpark版

~~~java
package com.example.three;

import java.util.concurrent.locks.LockSupport;

public class Test13 {

    static Thread t1;
    static Thread t2;
    static Thread t3;

    public static void main(String[] args) {
        ParkUnpark pu = new ParkUnpark(5);

        t1 = new Thread(() -> {
            pu.print("a", t2);
        });
        t2 = new Thread(() -> {
            pu.print("b", t3);
        });
        t3 = new Thread(() -> {
            pu.print("c", t1);
        });
        t1.start();
        t2.start();
        t3.start();

        LockSupport.unpark(t1);
    }
}

class ParkUnpark {

    private int loopNumber;

    public ParkUnpark(int loopNumber) {
        this.loopNumber = loopNumber;
    }

    public void print(String str, Thread next) {
        for (int i = 0; i < loopNumber; i++) {
            LockSupport.park();
            System.out.print(str);
            LockSupport.unpark(next);
        }
    }

}
// abcabcabcabcabc
// 利用unpark唤醒下一个线程
~~~



# 五、共享模型之内存

## 1、Java 内存模型

- **JMM 即 Java Memory Model，它定义了主存、工作内存抽象概念，底层对应着 CPU 寄存器、缓存、硬件内存、CPU 指令优化等**。 
  - 工作内存：每个线程自己私有信息存储的位置
  - 主存：所有共享信息存储的位置


- JMM 体现在以下几个方面 
  - 原子性 - 保证指令不会受到线程上下文切换的影响 
  - 可见性 - 保证指令不会受 cpu 缓存的影响 
  - 有序性 - 保证指令不会受 cpu 指令并行优化的影响





### 1.1 可见性

- 退不出的循环
- 先来看一个现象，main 线程对 run 变量的修改对于 t 线程不可见，导致了 t 线程无法停止：

~~~java
package com.example.four;

import lombok.extern.slf4j.Slf4j;

@Slf4j
public class Test1 {
    static boolean run = true;

    public static void main(String[] args) throws InterruptedException {

        Thread t = new Thread(()->{
            while(run){
                // ....
            }
        });
        t.start();
 
        Thread.sleep(1000);
        log.debug("停止 t");
        run = false; // 线程t不会如预想的停下来
    }
}
// 线程t不会如预想的停下来
~~~

- 原因

  - 初始状态， t 线程刚开始从主内存读取了 run 的值到工作内存。

    ![可见性1](C:\Users\lzy\Desktop\work\Java高级\JUC并发编程\图片\共享模型之内存\可见性1.png)

  - 因为 t 线程要频繁从主内存中读取 run 的值，**JIT 编译器会将 run 的值缓存至自己工作内存中的高速缓存中，减少对主存中 run 的访问，提高效率**

  ![可见性1](C:\Users\lzy\Desktop\work\Java高级\JUC并发编程\图片\共享模型之内存\可见性2.png)
  - 1 秒之后，main 线程修改了 run 的值，并同步至主存，而 t 是从自己工作内存中的高速缓存中读取这个变量的值，结果永远是旧值

  ![可见性1](C:\Users\lzy\Desktop\work\Java高级\JUC并发编程\图片\共享模型之内存\可见性3.png)

- **总结：一个线程对主存的数据进行了修改，对于另外一个线程不可见**


- 解决方案1：volatile
  - volatile（易变关键字）：它可以用来修饰成员变量和静态成员变量，**他可以避免线程从自己的工作缓存中查找变量的值，必须到主存中获取它的值，线程操作volatile变量都是直接操作主存**

~~~java
package com.example.five;

import lombok.extern.slf4j.Slf4j;

@Slf4j
public class Test1 {
    volatile static boolean run = true;

    public static void main(String[] args) throws InterruptedException {
        Thread t = new Thread(()->{
            while(run){
                // ....
            }
        });
        t.start();

        Thread.sleep(1000);
        log.debug("停止 t");
        run = false; // 线程t不会如预想的停下来
    }
}
// 1s后会停止
~~~

- 解决方案2：synchronized
  - **某一个线程进入synchronized代码块前后，线程会获得锁，清空工作内存，从主内存拷贝共享变量最近的值到工作内存成为副本，执行代码，将修改后的副本值刷回主内存中，线程释放锁**

```java
package com.example.five;

import lombok.extern.slf4j.Slf4j;

@Slf4j
public class Test2 {
    static boolean run = true;
    final static Object lock = new Object();    // 锁对象

    public static void main(String[] args) throws InterruptedException {
        Thread t = new Thread(()->{
            while(run){
                // ....
                synchronized (lock) {
                    if (!run) {
                        break;
                    }
                }
            }
        });
        t.start();

        Thread.sleep(1000);
        log.debug("停止 t");
        synchronized (lock) {
            run = false;
        }
    }
}
```



### 1.2 可见性vs原子性

- 前面例子体现的实际就是可见性，它保证的是在多个线程之间，一个线程对 volatile 变量的修改对另一个线程可见，不能保证原子性，仅用在一个写线程，多个读线程的情况： 
  - 上例从字节码理解是这样的：

![可见性vs原子性1](C:\Users\lzy\Desktop\work\Java高级\JUC并发编程\图片\共享模型之内存\可见性vs原子性1.png)

- 比较一下之前线程安全时举的例子：两个线程一个 i++ 一个 i-- ，volatile只能保证看到最新值，不能解决指令交错

![可见性vs原子性1](C:\Users\lzy\Desktop\work\Java高级\JUC并发编程\图片\共享模型之内存\可见性vs原子性2.png)

- **注意**  
  - **synchronized 语句块既可以保证代码块的原子性，也同时保证代码块内变量的可见性。但缺点是synchronized 是属于重量级操作，性能相对更低** 
  - 如果在前面示例的死循环中加入 System.out.println() 会发现即使不加 volatile 修饰符，线程 t 也能正确看到对 run 变量的修改了，想一想为什么？
    - **因为其内部包含了synchronized 的使用**

![sout](C:\Users\lzy\Desktop\work\Java高级\JUC并发编程\图片\共享模型之内存\sout.png)





### 1.3 模式之两阶段终止 

~~~java
package com.example.five;

import lombok.extern.slf4j.Slf4j;

/**
 * 两阶段中止模式之volatile使用
 */
@Slf4j
public class Test3 {
    public static void main(String[] args) throws InterruptedException {
        TwoPhaseTermination termination = new TwoPhaseTermination();
        // 启用后台监控程序
        termination.start();
        // 3.5s后打断监控
        Thread.sleep(3500);
        termination.stop();
    }
}

@Slf4j
class TwoPhaseTermination {
    private Thread monitor;
    // 停止标志
    private volatile boolean stop = false;

    // 启动监控线程
    public void start() {
        monitor = new Thread(() -> {
            while (true) {
                if(stop) {
                    // 停止标志为真时
                    log.debug("料理后事");
                    break;
                } else {
                    // 当前线程不想停止
                    try {
                        // 正常的执行监控记录
                        Thread.sleep(1000);
                        log.debug("执行监控记录");
                    } catch (InterruptedException exception) {
                        log.debug("料理后事");
                        break;
                    }
                }
            }
        });
        monitor.start();
    }

    // 停止监控线程
    public void stop() {
        stop = true;
        monitor.interrupt();
    }
}

// 2024-07-01 22:51:50 [Thread-0] [com.example.five.TwoPhaseTermination] [DEBUG] - 执行监控记录
// 2024-07-01 22:51:51 [Thread-0] [com.example.five.TwoPhaseTermination] [DEBUG] - 执行监控记录
// 2024-07-01 22:51:52 [Thread-0] [com.example.five.TwoPhaseTermination] [DEBUG] - 执行监控记录
// 2024-07-01 22:51:52 [Thread-0] [com.example.five.TwoPhaseTermination] [DEBUG] - 料理后事
~~~

- 为什么这里去掉了volatile也能停止呢
  - 因为：sleep、log、sout这三个都会去主存更新最新值，这样就不用加volatile也能停止





### 1.4 同步模式之Balking(犹豫模式)

- Balking （犹豫）模式用在一个线程发现另一个线程或本线程已经做了某一件相同的事，那么本线程就无需再做了，直接结束返回


~~~java
public class MonitorService {
    
    // 用来表示是否已经有线程已经在执行启动了
    private volatile boolean starting;
    
    public void start() {
        log.info("尝试启动监控线程...");
        synchronized (this) {
            if (starting) {
                return;
            }
            starting = true;
        }
        
        // 真正启动监控线程...
    }
}
~~~

- 例子解析：如果有两个线程同时执行start方法，第一个进来执行到if判断，此时starting还没有为true，那么第二个线程只能等待，等线程1执行完之后锁释放，线程2判断发现starting已经是true，就不会再有后续执行了
- 它还经常用来实现线程安全的单例

~~~java
public final class Singleton {
    
    private Singleton() { }
    
    private static Singleton INSTANCE = null;
    public static synchronized Singleton getInstance() {
        if (INSTANCE != null) {
            return INSTANCE;
        }
        
        INSTANCE = new Singleton();
        return INSTANCE;
    }
}

// 用synchronized让程序只会创建一个实例
~~~

- 对比一下保护性暂停模式：保护性暂停模式用在一个线程等待另一个线程的执行结果，当条件不满足时线程等待。



### 1.5 有序性

#### 1.5.1 指令重排

- **JVM会在不影响正确性的前提下，可以调整语句的执行顺序**

- 例子

  ```java
  static int i;
  static int j;

  // 在某个线程内执行如下赋值操作
  i = ...; 
  j = ...;
  ```

  - 可以看到，至于是先执行 i 还是 先执行 j ，对最终的结果不会产生影响。所以，上面代码真正执行时，既可以是

  ```java
  i = ...; 
  j = ...;
  ```

  - 也可以是

  ```java
  j = ...;
  i = ...;
  ```

- **这种特性称之为『指令重排』，多线程下『指令重排』会影响正确性。**



#### 1.5.2 为什么会出现指令重排

##### 1.5.2.1 名词

- ​



#### 1.5.3 案例分析

```java
int num = 0;
boolean ready = false;

// 线程1 执行此方法
public void actor1(I_Result r) {
    if(ready) {
        r.r1 = num + num;
    } else {
        r.r1 = 1;
    }
}

// 线程2 执行此方法
public void actor2(I_Result r) { 
    //这里可能发生指令重排序
    num = 2;
    ready = true; 
}
```

- I_Result 是一个对象，有一个属性 r1 用来保存结果，问，可能的结果有几种？ 
  - 情况1：线程1 先执行，这时 ready = false，所以进入 else 分支结果为 1 
  - 情况2：线程2 先执行 num = 2，但没来得及执行 ready = true，线程1 执行，还是进入 else 分支，结果为1 
  - 情况3：线程2 执行到 ready = true，线程1 执行，这回进入 if 分支，结果为 4（因为 num 已经执行过了） 
  - **情况4（指令重排）**：线程2 这里可能发生指令重排：即JVM认为这里num=2和ready=true这两个赋值操作谁先执行谁后执行不影响结果正确性，故而先执行了ready=true，切换到线程1，进入 if 分支，此时num还是初始值0，相加为 0，再切回线程2 执行 num = 2



#### 1.5.3 解决方案

- **volatile修饰的变量，可以禁用指令重排**

```java
int num = 0;
volatile boolean ready = false;

public void actor1(I_Result r) {
    if(ready) {
        r.r1 = num + num;
    } else {
        r.r1 = 1;
    }
}

public void actor2(I_Result r) {
    num = 2;
    ready = true;
}
```

- 用volatile修饰ready这个变量，就不会发生指令重排的现象







































# 七、共享模型之不可变

























# 八、线程池

## 1、背景

### 1.1 线程池是什么

- 顾名思义，线程池就是线程的池子，用来**管理和复用线程**。它可以在应用程序中有效地管理线程的生命周期、调度和执行。
- 线程池包含一组预先创建的线程，这些线程可以被动态分配给执行任务，从而避免了不断创建和销毁线程的开销。线程池通常用于执行大量的异步任务，提高多线程程序的性能和稳定性



### 1.2 优势

- **降低资源消耗**：通过重复利用已创建的线程降低线程创建和销毁造成的消耗。


- **提高响应速度**：当任务到达时，任务可以不需要等到线程创建就能立即执行。


- **提高线程的可管理性**：线程是稀缺资源，如果无限制地创建，不仅会消耗系统资源，还会降低系统的稳定性，使用线程池可以进行统一分配、调优和监控



## 2、自定义线程池


