---
title: Java基础
date: 2023-03-28 09:40:14
tags:
  - JVM
  - java
  - 集合
img: /2023/03/28/java-ji-chu/java-ji-chu/cover.png
cover: true
summary: Java基础
---

# JVM


![1](/2023/03/28/java-ji-chu/java-ji-chu/1.png)先了解个背景：

sjdk6：常量池在方法区中，方法区在堆中

jdk7：常量池在堆中，方法区在堆中

jdk8：常量池在堆中，元空间（由jdk7的方法区改名而来）在堆外

String类型的常量池比较特殊。它的主要使用方法有两种：

-   **直接使用双引号声明出来的String对象会直接存储在常量池中。**
-   **如果不是用双引号声明的String对象，可以使用String提供的intern方法。intern 方法会从字符串常量池中查询当前字符串是否存在，若不存在就会将当前字符串放入常量池中**

栈：
![2](/2023/03/28/java-ji-chu/java-ji-chu/2.png)

场景1：代码执行环境>=jdk7

```java
public static void main(String[] args) {
        String s = new String("1"); //这行会创建两次对象，第一次为在常量池中初始化这个常量。第二个是在堆中初始化s的对象实体，同时指向常量池中的常量。
        s.intern();//这一行相当于没有起作用，因为"1"已经在常量池中了。
        String s2 = "1"; // "在编译期就能发现的常量，会存储在常量池中。
        System.out.println(s == s2); //所以，这里会返回false,因为s指向堆中的对象，而s2指向的是常量池
        //结果为false


        String s3 = new String("1") + new String("1"); //创建了几个对象？ 4个对象。 我们这里要搞清楚s3到底指向了哪里？指向了堆，因为它的结果是通过new 构造出来的。
        s3.intern(); // s3在常量池中创建。
        String s4 = "11"; // s4 指向常量池中11
        System.out.println(s3 == s4); //结果为true，因为s3和s4 都是指向常量池的指针。
    }
```

下面开始进阶一点：如果将`s3.intern()`往下移一行呢? 代码执行环境>=jdk7

```java
public static void main(String[] args) {
        String s = new String("1"); //这行会创建两次对象，第一次为在常量池中初始化这个常量。第二个是在堆中初始化s的对象实体，同时指向常量池中的常量。
        s.intern();//这一行相当于没有起作用，因为"1"已经在常量池中了。
        String s2 = "1"; // "在编译期就能发现的常量，会存储在常量池中。
        System.out.println(s == s2); //所以，这里会返回false,因为s指向堆中的对象，而s2指向的是常量池
        //结果为false


        String s3 = new String("1") + new String("1"); //创建了几个对象？ 2 个 + 1 = 3个对象。 4个对象。
//        s3.intern(); // 由于s3就是在常量池中的，所以执行此方法，我理解也意义不大。
        String s4 = "11"; // s4 指向常量池中11
        s3.intern(); //由于此时常量池中已经存在"11"的常量了，所以s3直接返回对象的指针。
        System.out.println(s3 == s4); //结果为false，因为s4 指向常量池,s3指向堆。
    }
```

  

如果是jdk6的环境呢？

```java
public static void jdk6() {

        String s = new String("1");
        s.intern();
        String s2 = "1";
        System.out.println(s == s2); //false


        String s3 = new String("1") + new String("1"); //s3 指向的是堆中的地址
        s3.intern(); // jdk6同7不同的是，JDK6中，intern()方法会把首次遇到的字符串实例复制到永久代的字符串常量池中存储，返回的也是永久代里面这个字符串实例的引用。可以理解为只复制内容到常量池，不改栈指向堆的指针。
        String s4 = "11"; // s4 指向常量池中11
        System.out.println(s3 == s4); //结果为false，因为s4 指向常量池,s3指向堆。
    }
```

  


Java堆：是线程共享的一块区域，此区域的唯一目的是为了存储对象实例，几乎所有的对象实例都在这里创建（有例外：栈上分配，TLAB）

堆内容的划分：新生代；老年代；永久代（元空间）

新生代又分为 eden区，s0、s1区。

  

常见概念：
新生代垃圾回收：Minor GC / Young GC
老年代回收：Major GC
整堆垃圾回收：Full GC，收集整个堆和方法区
混合收集（Mix）：Mix GC，G1中的概念，收集整个新生代和部分老年代。

对象的分配过程：
1. 判断对象是否有逃逸，如果对象没有逃逸，那么就没有必要分配在堆上；
需要通过jvm参数控制开启逃逸分析**(-XX: +DoEscapeAnalysis)**；开启标量替换**(-XX: +EliminateAllocaCons)**
分析对象的作用域，可能被其他方法引用到叫做方法逃逸；可能被其他线程使用叫做线程逃逸
即时编译器可以根据逃逸分析的结果进行诸如锁消除、栈上分配以及标量替换的优化。
先看一下锁消除：如果即时编译器能够判断锁对象不逃逸，那么对该对象的加锁、解锁操作都没有任何意义。
其次在看下什么是栈上分配：由于直接在栈上初始化对象，并通过弹出栈帧来自动回收对象空间，这样就无需借助堆内存的垃圾回收
不过HotSpot并没有采用栈上分配的方式，原因是有很多逻辑使用了"对象只能堆分配"，所以其使用了**标量替换**的技术，简单来说就是判断未逃逸的对象中有哪些属性使用，将这个对象替换为对象中的成员变量，直接在栈上初始化这些成员变量，这个同栈上分配一样，也无需借助堆内存的垃圾回收。
2. 在判断完如果无需栈上分配之后，需要判断是否开启**TLAB**，如果开启，那么需要进行TLAB的判断。（[[https://www.bilibili.com/read/cv10375688 | TLAB全解 ]]）
	TLAB是什么？是线程本地分配缓存区，是线程私有的，线程初始化的时候会创建TLAB。TLAB占据的是Eden区中的一块空间，通常占据1%，为线程划分独立的对象分配空间。
	
	为什么要有TLAB？是怎么使用的？
	TLAB的出现的目的是避免堆上直接分配内存从而避免频繁的锁竞争。
	在检查如果开启了TLAB时，如果开启了，那么判断此线程剩余的TLAB大小时候满足于分配此对象，如果不满足，再判断TLAB的剩余空间是否小于最大浪费空间限制（动态值），如果是，则从Eden区中重新申请一个TLAB空间；否则直接在TLAB外分配。发生GC时，TLAB会被回收。
	-XX:UseTLAB 开启TLAB 默认开启
	![3](/2023/03/28/java-ji-chu/java-ji-chu/3.png)
	
	TLAB有什么缺点，怎么解决？
	TLAB引入后，由于存在TLAB停止的场景，也就是在TLAB的剩余空间小于最大浪费空间限制值下，需要重新申请一个TLAB，那么此时的剩余空间就是一个间隙。或者是GC时，还有TLAB的剩余空间。如果不管这些空间那么GC的时候这些空间就需要额外的检查，影响GC的扫描效率。
	所以如果是我们自己设计一个TLAB的空间，
	1）不能设置这个空间太大或者太小，太小会造成多频次的重新申请TLAB空间，太大会造成空间浪费；
	2）每次申请空间大小最好不要固定，而是动态的。
	3）由于TLAB是由线程创建的，所以需要关注当前gc轮次内的会分配对象的线程数量
	4）也需要关注每个线程会创建对象的数量及所需的空间，即申请TLAB次数。
	综上，实际是缺失一个历史期望值的概念。TLAB中经常使用的是EMA（指数平均数）算法
	新的期望大小 = 堆给TLAB的空间总大小 * 当前分配比例 EMA / 重填次数配置 



我们在了解了这些之后，再来回顾下堆、栈、方法区的关系。
```java
HelloWorld instance = new HelloWorld();
```
这行代码中Instance在栈中的局部变量表中，指向java的堆，java的堆中有对象实例数据，在对象头中有关于此对象的类型信息， 指向方法区。


常见的JVM命令
**Java栈：** **•** -Xss：设置线程最⼤栈空间。 
**Java堆：**
• 【-Xmx】设置堆空间最⼤⼤⼩（新⽣代 + ⽼年代） 
• 【-Xms】设置堆空间初始⼤⼩（新⽣代 + ⽼年代） 
• 【-XX:NewRa6o】设置⽼年代/新⽣代⼤⼩⽐例。
• 【-XX:SurvivorRadio】设置Eden区和S0和S1区⼤⼩⽐例。设置成8，代表Eden:S0:S1=8:1:1 
• 【-XX:UseTLAB】开启tlab空间 
• 【-XX:TLABWasteTargetPercent】设置TLAB占Eden区空间百分⽐ 
• 【-XX:handlePromo6onFailure】是否设置空间分配担保 
• 【-XX:MaxTenuringThreshold】设置默认的晋升年龄 
**PermGen：**
• 【-XX:PermSize】设置永久代初始⼤⼩
• 【-XX:MaxPermSize】设置永久代最⼤可分配空间 
**MetaSpace：** 
• 【-XX:MetaspaceSize】设置元空间初始⼤⼩，触及MetaspaceSize，触发FullGC 
• 【-XX:MaxMetaspaceSize】设置元空间最⼤⼤⼩，默认-1，代表没有限制，当没有本地内存可 ⽤，直接OOM


GC的关注区域为：方法区和堆
两种常见问题：
1.内存泄露（Memory Leak），业务对象不需要了，但是回收不掉
2.内存溢出（OutOfMemory），没有空闲内存了，超出内存上限。

GC的评估指标：
如果是高吞吐量的系统，单位时间内stw时间总和更小
如果是注重低延时的系统，单次stw时间比较小

JVM垃圾回收算法：
1. 标记-清除
分为两个阶段，标记阶段，标记要回收的对象，清除阶段，将标记为确认不可用的对象清除。
缺点：标记和清除的效率都不高并且会产生大量的碎片而导致频繁的回收
2. 标记-复制
先将内存分为大小相等的两块，每次用其中一块，当开始垃圾回收，把存活的对象复制到另一块，然后把这块内存清理。
缺点：需要浪费额外的内存作为复制区；当存活率高时，复制算法效率降低。
3. 标记-整理
把存活的对象往内存的一段移动，然后直接回收边界以外的内存
缺点：移动过程效率较低。

并发与并行
并发：CPU不断地上下文切换，执行多个任务，每个时间点上只有一个任务在执行。
并行：在多核的场景下，每个核都执行，同时进行。

独占与非独占式垃圾回收的区别：在垃圾回收的时候垃圾回收线程是否同应用线程并行执行。

![4](/2023/03/28/java-ji-chu/java-ji-chu/4.png)

新生代的垃圾收集器：Serial GC、Parallel Scavenge GC、ParNew GC
老年代垃圾收集器：Serial Old GC、Parallel Old GC、CMS GC
整堆垃圾收集器：G1

GC的两个难点：
1.对象漏标问题，这就涉及到3色标记法，用于区分哪些对象是要回收的，哪些对象是不要回收的。
白色：未被标记的对象
灰色：自身被标记，成员变量未被标记
黑色：自身和成员变量均已标记完成。

漏标的解决方案：
CMS解决方案：增量更新，关注引用的增加，把黑色重新标记为灰色，下次重新扫描属性。
G1解决方案：关注引用的删除，当B->D消失，要把这个引用推到GC的堆栈，保证D还能被GC扫描到。

2.跨代引用问题
问题点：为了找到年轻代的存活对象，不得不遍历整个老年代；反之亦然。这种方案存在极大的性能浪费，因为跨代引用是极少的。

解决方案：空间换时间。
CMS：CardTable
G1：RememberSet + CardTable

以G1为例，在每个region中都记录了RememberSet。


**CMS和G1的区别**：

CMS：并行和并发的非独占非压缩垃圾回收器。作用于老年代，使用标记整理算法，低延时
G1：并行和并发的非独占压缩垃圾回收期，作用于全堆，使用标记复制算法，低延时；有两种gc方式
Young GC：收集全部的Young区（Eden/Survivor）的垃圾；
Mix GC：收集全部的Young区 及部分老年代的垃圾。




那我们再来解析下gc的日志：

```log
{Heap before GC invocations=2115 (full 0):
 garbage-first heap   total 5570560K, used 4977455K [0x000000066c000000, 0x000000066c205500, 0x00000007c0000000)
  region size 2048K, 81 young (165888K), 0 survivors (0K)
 Metaspace       used 215126K, capacity 233170K, committed 234112K, reserved 1257472K
  class space    used 23373K, capacity 26311K, committed 26496K, reserved 1048576K
2023-03-21T04:52:53.936+0800: 65884.570: [GC pause (G1 Humongous Allocation) (mixed)
Desired survivor size 17825792 bytes, new threshold 15 (max 15)
 (to-space exhausted), 0.0684663 secs]
   [Parallel Time: 48.8 ms, GC Workers: 4]
      [GC Worker Start (ms): Min: 65884572.2, Avg: 65884572.3, Max: 65884572.3, Diff: 0.1]
      [Ext Root Scanning (ms): Min: 10.2, Avg: 11.7, Max: 14.5, Diff: 4.3, Sum: 47.0]
      [Update RS (ms): Min: 20.0, Avg: 21.5, Max: 22.0, Diff: 2.1, Sum: 85.9]
         [Processed Buffers: Min: 266, Avg: 278.2, Max: 287, Diff: 21, Sum: 1113]
      [Scan RS (ms): Min: 0.8, Avg: 0.9, Max: 1.0, Diff: 0.2, Sum: 3.7]
      [Code Root Scanning (ms): Min: 0.0, Avg: 0.0, Max: 0.0, Diff: 0.0, Sum: 0.0]
      [Object Copy (ms): Min: 13.5, Avg: 14.5, Max: 15.6, Diff: 2.1, Sum: 58.1]
      [Termination (ms): Min: 0.0, Avg: 0.0, Max: 0.0, Diff: 0.0, Sum: 0.0]
      [GC Worker Other (ms): Min: 0.0, Avg: 0.0, Max: 0.0, Diff: 0.0, Sum: 0.1]
      [GC Worker Total (ms): Min: 48.7, Avg: 48.7, Max: 48.8, Diff: 0.1, Sum: 194.8]
      [GC Worker End (ms): Min: 65884621.0, Avg: 65884621.0, Max: 65884621.0, Diff: 0.0]
   [Code Root Fixup: 0.4 ms]
   [Code Root Purge: 0.0 ms]
   [Clear CT: 0.8 ms]
   [Other: 18.5 ms]
      [Evacuation Failure: 9.6 ms]
      [Choose CSet: 0.2 ms]
      [Ref Proc: 0.6 ms]
      [Ref Enq: 0.1 ms]
      [Redirty Cards: 0.1 ms]
      [Humongous Reclaim: 4.7 ms]
      [Free CSet: 0.7 ms]
   [Eden: 162.0M(272.0M)->0.0B(270.0M) Survivors: 0.0B->2048.0K Heap: 4861.4M(5440.0M)->3643.8M(5440.0M)]
Heap after GC invocations=2116 (full 0):
 garbage-first heap   total 5570560K, used 3731299K [0x000000066c000000, 0x000000066c205500, 0x00000007c0000000)
  region size 2048K, 1 young (2048K), 1 survivors (2048K)
 Metaspace       used 215126K, capacity 233170K, committed 234112K, reserved 1257472K
  class space    used 23373K, capacity 26311K, committed 26496K, reserved 1048576K
}
 [Times: user=0.20 sys=0.01, real=0.07 secs] 
```

# Java并发

## 并发基础

### 硬件内存架构

![5](/2023/03/28/java-ji-chu/java-ji-chu/5.png)

**CPU、缓存、主内存交互过程**：读写速度从高到低：CPU > 缓存 > 主内存；CPU访问数据时，先读缓存，未命中再访问主内存；CPU写入数据时，先写缓存，然后再刷新主内存。

**缓存一致性**：每个CPU都有独立缓存并且一起共享主内存，涉及同一个内存区域的操作会导致缓存数据不一致。

**乱序执行优化**（out-of-order execution）：为了使处理器内部的运算单元能尽量被充分利用，处理器可能会对输入代码进行乱序执行；乱序执行的最终结果和顺序执行的最终结果一直，但不保证中间态。

### OS的进程、线程

进程：是操作系统进行资源分配的基本单位.

线程：线程是更轻量级的进场，是操作系统进行资源调度的基本单位。

## JMM

### Java内存模型 及 Java线程

![6](/2023/03/28/java-ji-chu/java-ji-chu/6.png)

Java的内存模型主要分为：主内存和工作内存.

主内存：存储Java实例对象，所有线程创建的实例对象都存放在主内存中。

工作内存：每个线程都有其自己的工作内存，工作内存中保存该线程使用遍历的主内存副本，线程对于变量的所有操作都必须在工作内存中进行，而不能直接读写主内存中的数据。不同线程无法访问对方工作内存中的变量，线程间变量值的传递需由主内存来完成。

![7](/2023/03/28/java-ji-chu/java-ji-chu/7.png)

wait、sleep、yield、join区别



### 并发环境下的JMM存在的问题

### 常用关键字语义：volatile，final，synchronized

## JUC

### Unsafe、LockSupport

### AQS

### 线程同步工具类

### JUC的锁

### JUC原子类

### JUC下的线程安全容器

### JUC线程池
