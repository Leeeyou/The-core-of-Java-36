# 第25讲 \| 谈谈JVM内存区域的划分，哪些区域可能发生OutOfMemoryError?

今天，我将从内存管理的角度，进一步探索 Java 虚拟机（JVM）。垃圾收集机制为我们打理了很多繁琐的工作，大大提高了开发的效率，但是，垃圾收集也不是万能的，懂得 JVM 内部的内存结构、工作机制，是设计高扩展性应用和诊断运行时问题的基础，也是 Java 工程师进阶的必备能力。

今天我要问你的问题是，谈谈 JVM 内存区域的划分，哪些区域可能发生 OutOfMemoryError？

## 典型回答

通常可以把 JVM 内存区域分为下面几个方面，其中，有的区域是以线程为单位，而有的区域则是整个 JVM 进程唯一的。

首先，程序计数器（PC，Program Counter Register）。在 JVM 规范中，每个线程都有它自己的程序计数器，并且任何时间一个线程都只有一个方法在执行，也就是所谓的当前方法。程序计数器会存储当前线程正在执行的 Java 方法的 JVM 指令地址；或者，如果是在执行本地方法，则是未指定值（undefined）。

第二，Java 虚拟机栈（Java Virtual Machine Stack），早期也叫 Java 栈。每个线程在创建时都会创建一个虚拟机栈，其内部保存一个个的栈帧（Stack Frame），对应着一次次的 Java 方法调用。

栈帧中存储着**局部变量表**、**操作数（operand）栈、动态链接、方法正常退出或者异常退出的定义**等。（局部变量表的数量在编译器就已经确认了，存储到了方法的CODE属性中。）

前面谈程序计数器时，提到了当前方法；同理，在一个时间点，对应的只会有一个活动的栈帧，通常叫作当前帧，方法所在的类叫作当前类。如果在该方法中调用了其他方法，对应的新的栈帧会被创建出来，成为新的当前帧，一直到它返回结果或者执行结束。JVM 直接对 Java 栈的操作只有两个，就是对栈帧的压栈和出栈。

第三，堆（Heap），它是 Java 内存管理的核心区域，用来放置 Java 对象实例，**几乎所有**创建的 Java 对象实例都是被直接分配在堆上。堆被所有的线程共享，在虚拟机启动时，我们指定的“Xmx”之类参数就是用来指定最大堆空间等指标。理所当然，堆也是垃圾收集器重点照顾的区域，所以堆内空间还会被不同的垃圾收集器进行进一步的细分，最有名的就是新生代、老年代的划分。

第四，方法区（Method Area）。这也是所有线程共享的一块内存区域，用于存储所谓的元（Meta）数据，例如类结构信息，以及对应的运行时常量池、字段、方法代码等。**由于早期的 Hotspot JVM 实现，很多人习惯于将方法区称为永久代（Permanent Generation）。Oracle JDK 8 中将永久代移除，同时增加了元数据区（Metaspace）。**

> 方法区包含：运行时常量池、类型信息、字段信息、方法信息、类变量、指向类加载器的引用、指向Class实例的引用、方法表。最好贴一张图展示说明。

第五，运行时常量池（Run-Time Constant Pool），这是方法区的一部分。如果仔细分析过反编译的类文件结构，你能看到版本号、字段、方法、超类、接口等各种信息，还有一项信息就是常量池。Java 的常量池可以存放各种常量信息，**不管是编译期生成**的各种字面量，**还是需要在运行时决定**的符号引用，所以它比一般语言的符号表存储的信息更加宽泛。

第六，本地方法栈（Native Method Stack）。它和 Java 虚拟机栈是非常相似的，支持对本地方法的调用，也是每个线程都会创建一个。在 Oracle Hotspot JVM 中，本地方法栈和 Java 虚拟机栈是在同一块儿区域，这完全取决于技术实现的决定，并未在规范中强制。

## 考点分析

这是个 JVM 领域的基础题目，我给出的答案依据的是JVM 规范中运行时数据区定义，这也和大多数书籍和资料解读的角度类似。

JVM 内部的概念庞杂，对于初学者比较晦涩，我的建议是在工作之余，还是要去阅读经典书籍，比如我推荐过多次的《深入理解 Java 虚拟机》。

今天这一讲作为 Java 虚拟机内存管理的开篇，我会侧重于：

* 分析广义上的 JVM 内存结构或者说 Java 进程内存结构。

* 谈到 Java 内存模型，不可避免的要涉及 OutOfMemory（OOM）问题，那么在 Java 里面存在哪些种 OOM 的可能性，分别对应哪个内存区域的异常状况呢？

注意，具体 JVM 的内存结构，其实取决于其实现，不同厂商的 JVM，或者同一厂商发布的不同版本，都有可能存在一定差异。我在下面的分析中，还会介绍 Oracle Hotspot JVM 的部分设计变化。

## 知识扩展

首先，为了让你有个更加直观、清晰的印象，我画了一个简单的内存结构图，里面展示了我前面提到的堆、线程栈等区域，并从数量上说明了什么是线程私有，例如，程序计数器、Java 栈等，以及什么是 Java 进程唯一。另外，还额外划分出了直接内存等区域。

![](/assets/微信图片_20180713102310.jpg)

这张图反映了实际中 Java 进程内存占用，与规范中定义的 JVM 运行时数据区之间的差别，它可以看作是运行时数据区的一个超集。毕竟理论上的视角和现实中的视角是有区别的，规范侧重的是通用的、无差别的部分，而对于应用开发者来说，只要是 Java 进程在运行时会占用，都会影响到我们的工程实践。

我这里简要介绍两点区别：

* 直接内存（Direct Memory）区域，它就是我在专栏第 12 讲中谈到的 Direct Buffer 所直接分配的内存，也是个容易出现问题的地方。尽管，在 JVM 工程师的眼中，并不认为它是 JVM 内部内存的一部分，也并未体现 JVM 内存模型中。

* JVM 本身是个本地程序，还需要其他的内存去完成各种基本任务，比如，JIT Compiler 在运行时对热点方法进行编译，就会将编译后的方法储存在 Code Cache 里面；GC 等功能需要运行在本地线程之中，类似部分都需要占用内存空间。这些是实现 JVM JIT 等功能的需要，但规范中并不涉及。

如果深入到 JVM 的实现细节，你会发现一些结论似乎有些模棱两可，比如：

* Java 对象是不是都创建在堆上的呢？

  我注意到有一些观点，认为通过_逃逸分析_，JVM 会在栈上分配那些不会逃逸的对象，这在理论上是可行的，但是取决于 JVM 设计者的选择。据我所知，Oracle Hotspot JVM 中并未这么做，这一点在逃逸分析相关的文档里已经说明，所以可以明确所有的对象实例都是创建在堆上。

  目前很多书籍还是基于 JDK 7 以前的版本，JDK 已经发生了很大变化，Intern 字符串的缓存和静态变量曾经都被分配在永久代上，而永久代已经被元数据区取代。但是，Intern 字符串缓存和静态变量并不是被转移到元数据区，而是直接在堆上分配，所以这一点同样符合前面一点的结论：对象实例都是分配在堆上。

接下来，我们来看看什么是 OOM 问题，它可能在哪些内存区域发生？

首先，**OOM 如果通俗点儿说，就是 JVM 内存不够用了，javadoc 中对OutOfMemoryError的解释是，没有空闲内存，并且垃圾收集器也无法提供更多内存。**

这里面隐含着一层意思是，在抛出 OutOfMemoryError 之前，通常垃圾收集器会被触发，尽其所能去清理出空间，例如：

* 我在专栏第 4 讲的引用机制分析中，已经提到了 JVM 会去尝试回收软引用指向的对象等。

* 在java.nio.BIts.reserveMemory\(\) 方法中，我们能清楚的看到，System.gc\(\) 会被调用，以清理空间，这也是为什么在大量使用 NIO 的 Direct Buffer 之类时，通常建议不要加下面的参数，毕竟是个最后的尝试，有可能避免一定的内存不足问题。

```java
-XX:+DisableExplictGC
```

当然，也不是在任何情况下垃圾收集器都会被触发的，比如，我们去分配一个超大对象，类似一个超大数组超过堆的最大值，JVM 可以判断出垃圾收集并不能解决这个问题，所以直接抛出 OutOfMemoryError。

从我前面分析的数据区的角度，除了程序计数器，其他区域都有可能会因为可能的空间不足发生 OutOfMemoryError，简单总结如下：

* 堆内存不足是最常见的 OOM 原因之一，抛出的错误信息是“java.lang.OutOfMemoryError:Java heap space”，原因可能千奇百怪，例如，可能存在内存泄漏问题；也很有可能就是堆的大小不合理，比如我们要处理比较可观的数据量，但是没有显式指定 JVM 堆大小或者指定数值偏小；或者出现 JVM 处理引用不及时，导致堆积起来，内存无法释放等。

* 而对于 Java 虚拟机栈和本地方法栈，这里要稍微复杂一点。如果我们写一段程序不断的进行递归调用，而且没有退出条件，就会导致不断地进行压栈。类似这种情况，JVM 实际会抛出 StackOverFlowError；当然，如果 JVM 试图去扩展栈空间的的时候失败，则会抛出 OutOfMemoryError。

* 对于老版本的 Oracle JDK，因为永久代的大小是有限的，并且 JVM 对永久代垃圾回收（如，常量池回收、卸载不再需要的类型）非常不积极，所以当我们不断添加新类型的时候，永久代出现 OutOfMemoryError 也非常多见，尤其是在运行时存在大量动态类型生成的场合；类似 Intern 字符串缓存占用太多空间，也会导致 OOM 问题。对应的异常信息，会标记出来和永久代相关：“java.lang.OutOfMemoryError: **PermGen space**”。

* 随着元数据区的引入，方法区内存已经不再那么窘迫，所以相应的 OOM 有所改观，出现 OOM，异常信息则变成了：“java.lang.OutOfMemoryError: **Metaspace**”。

* 直接内存不足，也会导致 OOM，这个已经专栏第 11 讲介绍过。

今天是 JVM 内存部分的第一讲，算是我们先进行了热身准备，我介绍了主要的内存区域，以及在不同版本 Hotspot JVM 内部的变化，并且分析了各区域是否可能产生 OutOfMemoryError，以及 OOME 发生的典型情况。

## 思考

关于今天我们讨论的题目你做到心中有数了吗？今天的思考题是，我在试图分配一个 100M bytes 大数组的时候发生了 OOME，但是 GC 日志显示，明明堆上还有远不止 100M 的空间，你觉得可能问题的原因是什么？想要弄清楚这个问题，还需要什么信息呢？

> 如果仅从jvm的角度来看，要看下新生代和老年代的垃圾回收机制是什么。如果新生代是serial，会默认使用copying算法，利用两块eden和survivor来进行处理。但是默认当遇到超大对象时，会直接将超大对象放置到老年代中，而不用走正常对象的存活次数记录。因为要放置的是一个byte数组，那么必然需要申请连续的空间，当空间不足时，会进行gc操作。这里又需要看老年代的gc机制是哪一种。如果是serial old，那么会采用mark compat，会进行整理，从而整理出连续空间，如果还不够，说明是老年代的空间不够，所谓的堆内存大于100m是新+老共同的结果。如果采用的是cms\(concurrent mark sweep\)，那么只会标记清理，并不会压缩，所以内存会碎片化，同时可能出现浮游垃圾。如果是cms的话，即使老年代的空间大于100m，也会出现没有连续的空间供该对象使用。
>
> CMS是一种以最短停顿时间为目标的收集器，使用CMS并不能达到GC效率最高，但它尽可能降低GC时服务的停顿时间。使用标记-清除算法（Mark Sweep），在运行时会产生内存碎片

### Class文件的结构是怎样的？

![](/assets/20141008125149484.png)

### Class文件的常量池与运行时常量池的区别？

![](/assets/20141011111433728.png)

Class文件的常量池存放的是编译器能确定的各种字面量以及符号引用。而运行时常量池可以存放各种常量信息，**不管是编译期生成**的各种字面量，**还是需要在运行时决定**的符号引用，所以它比一般语言的符号表存储的信息更加宽泛。在动态链接中起核心作用。

### Class文件常量池具体是怎样存放数据的？

[Class文件中的常量池](https://blog.csdn.net/luanlouis/article/details/40148053)

[Class文件中的常量池详解（上）](https://blog.csdn.net/luanlouis/article/details/39960815)

[Class文件中的常量池详解（下）](https://blog.csdn.net/luanlouis/article/details/40301985)

### javac命令和javap命令的使用

javac是用来编译.java文件的。javap主要用于帮助开发者深入了解Java编译器的机制，有-c 和 -v 两个选项。-c 分解方法代码，即显示每个方法具体的字节码；-v 指定显示更进一步的详细信息。

```java
package jvm;

import java.util.Date;

public class ConstantPoolTest {

    private final int a = 10;  
    private final int b = 10;  
    private float c = 11f;  
    private float d = 11f;  
    private float e = 11f;
    private long f = -6076574518398440533L;
    private long g = -6076574518398440533L;
    private double h = 10.1234567890D;
    private double i = 10.1234567890D;
    private String s1 = "JVM principle";
    private String s2 = "JVM principle";
    private Date date =new Date();

    private String name;
    private int age;

    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }

    public int getAge() {
        return age;
    }

    public void setAge(int age) {
        this.age = age;
    }
}
```

javap后如下所示：

```java
D:\Project\IntellJ2\DemoIO\src\jvm>javap -v ConstantPoolTest.class
Classfile /D:/Project/IntellJ2/DemoIO/src/jvm/ConstantPoolTest.class
  Last modified 2018-8-31; size 1097 bytes
  MD5 checksum 399e413b50c9f22646af81e7165ac046
  Compiled from "ConstantPoolTest.java"
public class jvm.ConstantPoolTest
  minor version: 0
  major version: 52
  flags: ACC_PUBLIC, ACC_SUPER
Constant pool:
   #1 = Methodref          #25.#62        // java/lang/Object."<init>":()V
   #2 = Fieldref           #24.#63        // jvm/ConstantPoolTest.a:I
   #3 = Fieldref           #24.#64        // jvm/ConstantPoolTest.b:I
   #4 = Float              11.0f
   #5 = Fieldref           #24.#65        // jvm/ConstantPoolTest.c:F
   #6 = Fieldref           #24.#66        // jvm/ConstantPoolTest.d:F
   #7 = Fieldref           #24.#67        // jvm/ConstantPoolTest.e:F
   #8 = Long               -6076574518398440533l
  #10 = Fieldref           #24.#68        // jvm/ConstantPoolTest.f:J
  #11 = Fieldref           #24.#69        // jvm/ConstantPoolTest.g:J
  #12 = Double             10.123456789d
  #14 = Fieldref           #24.#70        // jvm/ConstantPoolTest.h:D
  #15 = Fieldref           #24.#71        // jvm/ConstantPoolTest.i:D
  #16 = String             #72            // JVM principle
  #17 = Fieldref           #24.#73        // jvm/ConstantPoolTest.s1:Ljava/lang/String;
  #18 = Fieldref           #24.#74        // jvm/ConstantPoolTest.s2:Ljava/lang/String;
  #19 = Class              #75            // java/util/Date
  #20 = Methodref          #19.#62        // java/util/Date."<init>":()V
  #21 = Fieldref           #24.#76        // jvm/ConstantPoolTest.date:Ljava/util/Date;
  #22 = Fieldref           #24.#77        // jvm/ConstantPoolTest.name:Ljava/lang/String;
  #23 = Fieldref           #24.#78        // jvm/ConstantPoolTest.age:I
  #24 = Class              #79            // jvm/ConstantPoolTest
  #25 = Class              #80            // java/lang/Object
  #26 = Utf8               a
  #27 = Utf8               I
  #28 = Utf8               ConstantValue
  #29 = Integer            10
  #30 = Utf8               b
  #31 = Utf8               c
  #32 = Utf8               F
  #33 = Utf8               d
  #34 = Utf8               e
  #35 = Utf8               f
  #36 = Utf8               J
  #37 = Utf8               g
  #38 = Utf8               h
  #39 = Utf8               D
  #40 = Utf8               i
  #41 = Utf8               s1
  #42 = Utf8               Ljava/lang/String;
  #43 = Utf8               s2
  #44 = Utf8               date
  #45 = Utf8               Ljava/util/Date;
  #46 = Utf8               name
  #47 = Utf8               age
  #48 = Utf8               <init>
  #49 = Utf8               ()V
  #50 = Utf8               Code
  #51 = Utf8               LineNumberTable
  #52 = Utf8               getName
  #53 = Utf8               ()Ljava/lang/String;
  #54 = Utf8               setName
  #55 = Utf8               (Ljava/lang/String;)V
  #56 = Utf8               getAge
  #57 = Utf8               ()I
  #58 = Utf8               setAge
  #59 = Utf8               (I)V
  #60 = Utf8               SourceFile
  #61 = Utf8               ConstantPoolTest.java
  #62 = NameAndType        #48:#49        // "<init>":()V
  #63 = NameAndType        #26:#27        // a:I
  #64 = NameAndType        #30:#27        // b:I
  #65 = NameAndType        #31:#32        // c:F
  #66 = NameAndType        #33:#32        // d:F
  #67 = NameAndType        #34:#32        // e:F
  #68 = NameAndType        #35:#36        // f:J
  #69 = NameAndType        #37:#36        // g:J
  #70 = NameAndType        #38:#39        // h:D
  #71 = NameAndType        #40:#39        // i:D
  #72 = Utf8               JVM principle
  #73 = NameAndType        #41:#42        // s1:Ljava/lang/String;
  #74 = NameAndType        #43:#42        // s2:Ljava/lang/String;
  #75 = Utf8               java/util/Date
  #76 = NameAndType        #44:#45        // date:Ljava/util/Date;
  #77 = NameAndType        #46:#42        // name:Ljava/lang/String;
  #78 = NameAndType        #47:#27        // age:I
  #79 = Utf8               jvm/ConstantPoolTest
  #80 = Utf8               java/lang/Object
{
  public jvm.ConstantPoolTest();
    descriptor: ()V
    flags: ACC_PUBLIC
    Code:
      stack=3, locals=1, args_size=1
         0: aload_0
         1: invokespecial #1                  // Method java/lang/Object."<init>":()V
         4: aload_0
         5: bipush        10
         7: putfield      #2                  // Field a:I
        10: aload_0
        11: bipush        10
        13: putfield      #3                  // Field b:I
        16: aload_0
        17: ldc           #4                  // float 11.0f
        19: putfield      #5                  // Field c:F
        22: aload_0
        23: ldc           #4                  // float 11.0f
        25: putfield      #6                  // Field d:F
        28: aload_0
        29: ldc           #4                  // float 11.0f
        31: putfield      #7                  // Field e:F
        34: aload_0
        35: ldc2_w        #8                  // long -6076574518398440533l
        38: putfield      #10                 // Field f:J
        41: aload_0
        42: ldc2_w        #8                  // long -6076574518398440533l
        45: putfield      #11                 // Field g:J
        48: aload_0
        49: ldc2_w        #12                 // double 10.123456789d
        52: putfield      #14                 // Field h:D
        55: aload_0
        56: ldc2_w        #12                 // double 10.123456789d
        59: putfield      #15                 // Field i:D
        62: aload_0
        63: ldc           #16                 // String JVM principle
        65: putfield      #17                 // Field s1:Ljava/lang/String;
        68: aload_0
        69: ldc           #16                 // String JVM principle
        71: putfield      #18                 // Field s2:Ljava/lang/String;
        74: aload_0
        75: new           #19                 // class java/util/Date
        78: dup
        79: invokespecial #20                 // Method java/util/Date."<init>":()V
        82: putfield      #21                 // Field date:Ljava/util/Date;
        85: return
      LineNumberTable:
        line 5: 0
        line 7: 4
        line 8: 10
        line 9: 16
        line 10: 22
        line 11: 28
        line 12: 34
        line 13: 41
        line 14: 48
        line 15: 55
        line 16: 62
        line 17: 68
        line 18: 74

  public java.lang.String getName();
    descriptor: ()Ljava/lang/String;
    flags: ACC_PUBLIC
    Code:
      stack=1, locals=1, args_size=1
         0: aload_0
         1: getfield      #22                 // Field name:Ljava/lang/String;
         4: areturn
      LineNumberTable:
        line 24: 0

  public void setName(java.lang.String);
    descriptor: (Ljava/lang/String;)V
    flags: ACC_PUBLIC
    Code:
      stack=2, locals=2, args_size=2
         0: aload_0
         1: aload_1
         2: putfield      #22                 // Field name:Ljava/lang/String;
         5: return
      LineNumberTable:
        line 28: 0
        line 29: 5

  public int getAge();
    descriptor: ()I
    flags: ACC_PUBLIC
    Code:
      stack=1, locals=1, args_size=1
         0: aload_0
         1: getfield      #23                 // Field age:I
         4: ireturn
      LineNumberTable:
        line 32: 0

  public void setAge(int);
    descriptor: (I)V
    flags: ACC_PUBLIC
    Code:
      stack=2, locals=2, args_size=2
         0: aload_0
         1: iload_1
         2: putfield      #23                 // Field age:I
         5: return
      LineNumberTable:
        line 36: 0
        line 37: 5
}
SourceFile: "ConstantPoolTest.java"
```



