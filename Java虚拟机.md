[TOC]

# Java内存区域（运行时数据区域）

![](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91c2VyLWdvbGQtY2RuLnhpdHUuaW8vMjAxNy85LzQvZGQzYjE1YjNkODgyNmZhZWFlMjA2Mzk3NmZiOTkyMTM_aW1hZ2VWaWV3Mi8wL3cvMTI4MC9oLzk2MC9mb3JtYXQvd2VicC9pZ25vcmUtZXJyb3IvMQ)

## 程序计数器

内存空间小，线程私有。字节码解释器工作是就是通过改变这个计数器的值来选取下一条需要执行指令的字节码指令，分支、循环、跳转、异常处理、线程恢复等基础功能都需要依赖计数器完成。

如果线程正在执行一个 Java 方法，这个计数器记录的是正在执行的虚拟机字节码指令的地址；如果正在执行的是 Native 方法，这个计数器的值则为 (Undefined)。

## Java虚拟机栈

线程私有，生命周期和线程一致。描述的是 Java 方法执行的内存模型：每个方法在执行时都会创建一个栈帧(Stack Frame)用于存储`局部变量表`、`操作数栈`、`动态链接`、`方法出口`等信息。每一个方法从调用直至执行结束，就对应着一个栈帧从虚拟机栈中入栈到出栈的过程。

常见问题：

- StackOverflowError：线程请求的栈深度大于虚拟机所允许的深度。
- OutOfMemoryError：如果虚拟机栈可以动态扩展，而扩展时无法申请到足够的内存。

## 本地方法栈

区别于 Java 虚拟机栈的是，Java 虚拟机栈为虚拟机执行 Java 方法(也就是字节码)服务，而本地方法栈则为虚拟机使用到的 Native 方法服务。也会有 StackOverflowError 和 OutOfMemoryError 异常。

Native方法：由非Java语言实现的方法。

## Java堆

对于绝大多数应用来说，这块区域是 JVM 所管理的内存中最大的一块。**线程共享，主要是存放对象实例和数组**。内部会划分出多个线程私有的分配缓冲区(Thread Local Allocation Buffer, TLAB)。可以位于物理上不连续的空间，但是逻辑上要连续。

常见问题：

- OutOfMemoryError：如果堆中没有内存完成实例分配，并且堆也无法再扩展时，抛出该异常。

## 方法区

属于共享内存区域，存储已被虚拟机加载的类信息、常量、静态变量、即时编译器编译后的代码等数据。

![](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91c2VyLWdvbGQtY2RuLnhpdHUuaW8vMjAxNy85LzQvZGE3N2Q5MDE0Njc4NmMwY2IzZTE3MGI5YzkzNzZhZTQ_aW1hZ2VWaWV3Mi8wL3cvMTI4MC9oLzk2MC9mb3JtYXQvd2VicC9pZ25vcmUtZXJyb3IvMQ)

## 运行时常量池

属于方法区一部分，用于存放编译期生成的各种字面量和符号引用。编译器和运行期(String 的 intern() )都可以将常量放入池中。内存有限，无法申请时抛出 OutOfMemoryError。

## 直接内存

非虚拟机运行时数据区的部分。

# 垃圾回收

## 概述

程序计数器、虚拟机栈、本地方法栈 3 个区域随线程生灭(因为是线程私有)，栈中的栈帧随着方法的进入和退出而有条不紊地执行着出栈和入栈操作。而 Java 堆和方法区则不一样，一个接口中的多个实现类需要的内存可能不一样，一个方法中的多个分支需要的内存也可能不一样，我们只有在程序处于运行期才知道那些对象会创建，这部分内存的分配和回收都是动态的，垃圾回收期所关注的就是这部分内存。

## 垃圾回收算法

### 引用计数法

顾名思义，此种算法会在每一个对象上记录这个对象被引用的次数，只要有任何一个对象引用了次对象，这个对象的计数器就+1，取消对这个对象的引用时，计数器就-1。任何一个时刻，如果该对象的计数器为0，那么这个对象就是可以回收的。

```java
public static void method() {
    A a = new A();
}

public static void main(String[] args) {
    method();
}
```

main函数调用method方法，method方法中new了一个A的对象，赋值给局部变量a，此时堆内存中的对象A的实例的计数器就会+1。当方法结束时，局部变量会随之销毁，堆内存中的对象的计数器就会-1。

**存在的问题**

- 无法处理循环引用的情况
- 堆内对象的每一次引用赋值和每一次引用清除，都伴随着加减法的操作，会带来一定的性能开销。

其中循环引用即对象A引用对象B，对象B引用对象A。

```java
class A {
    private B b;
    public void setB(B b) {
        this.b = b;
    }
}

class B {
    private A a = new A();
    public void setA(A a) {
        this.a = a;
    }
}

public void method() {
    A a = new A();
    B b = new B();
    a.setB(b);
    b.setA(a);
}
```

![](https://pic1.zhimg.com/80/v2-3663b4c2a7c8eb93e9e19da45dc31ac8_1440w.jpg)

method方法中，执行完两个set后，method方法结束，图中两条红线引用消失，可以看到，留下两个对象在堆内存中循环引用，但此时已经没有地方在用他们了，造成内存泄漏。

### 可达性分析

可达性分析法定义了一系列称为"GC Roots"的对象作为起始点，从这个起点开始向下搜索，每一条可达路径称为引用链，当一个对象没有任意一条引用链可以到达"GC Roots"时，那么就对这个对象进行第一次"可回收"标记。

**那么什么是GC Root呢？**

可以理解为由堆外指向堆内的引用。

**那么都有哪些对象可以作为GC Roots呢？** 包括如下几种

- 代码中某一方法中的局部变量
- 类变量（静态变量）
- 常量
- 本地方法栈中引用的对象
- 已启动且未停止的线程

用代码来简单的说明一下前三类：

```java
class Test {
    private static A a = new A(); // 静态变量
    public static final String CONTANT = "I am a string"; // 常量

    public static void main(String[] args) {
        A innerA = new A(); // 局部变量
    }
}

class A {
    ...
}

```

<img src="https://camo.githubusercontent.com/6e40c6cd3b554d30a765ea25630f2ef589d263477ed5fa207d603c7f6910e10c/687474703a2f2f66656174686572732e7a7262636f6f6c2e746f702f696d6167652f254535253846254146254538254245254245254536253830254137254535253838253836254536253945253930312e6a7067" style="zoom:67%;" />

首先，类加载器加载Test类，会初始化静态变量a，将常量引用指向常量池中的字符串，完成Test类的加载；

然后，main方法执行，main方法会入虚拟机方法栈，执行main方法会在堆中创建A的对象，并赋值给局部变量innerA。

此时GC Roots状态如下：

[![可达性分析图2](https://camo.githubusercontent.com/60d139ae94bc9aab921219991063e6e0f9b939f4bffc2e42db6995cfeaa1c4a8/687474703a2f2f66656174686572732e7a7262636f6f6c2e746f702f696d6167652f254535253846254146254538254245254245254536253830254137254535253838253836254536253945253930322e6a7067)](https://camo.githubusercontent.com/60d139ae94bc9aab921219991063e6e0f9b939f4bffc2e42db6995cfeaa1c4a8/687474703a2f2f66656174686572732e7a7262636f6f6c2e746f702f696d6167652f254535253846254146254538254245254245254536253830254137254535253838253836254536253945253930322e6a7067)

当main方法执行完出栈后，变为：

[![可达性分析图3](https://camo.githubusercontent.com/ee4279106acace689305d3c0974e55abb1f1c2fb084a43e2e98d8036ec485e99/687474703a2f2f66656174686572732e7a7262636f6f6c2e746f702f696d6167652f254535253846254146254538254245254245254536253830254137254535253838253836254536253945253930332e6a7067)](https://camo.githubusercontent.com/ee4279106acace689305d3c0974e55abb1f1c2fb084a43e2e98d8036ec485e99/687474703a2f2f66656174686572732e7a7262636f6f6c2e746f702f696d6167652f254535253846254146254538254245254245254536253830254137254535253838253836254536253945253930332e6a7067)

第三个对象已经没有引用链可达GC Root，此时，第三个对象被第一次标记。

一个被可达性分析标记为可回收的对象，是有机会进行自救的。

[对象的自救（暂时省略）](https://github.com/Lord-X/awesome-it-blog/blob/master/java/JVM-GC%E5%9E%83%E5%9C%BE%E5%9B%9E%E6%94%B6%E7%AE%97%E6%B3%95-%E5%88%A4%E5%AE%9A%E4%B8%80%E4%B8%AA%E5%AF%B9%E8%B1%A1%E6%98%AF%E5%90%A6%E6%98%AF%E5%8F%AF%E5%9B%9E%E6%94%B6%E7%9A%84%E5%AF%B9%E8%B1%A1.md)

### 复制算法

复制算法会将内存空间分为两块，每次只使用其中一块内存。复制算法同样使用可达性分析法标记除垃圾对象，当GC执行时，会将非垃圾对象复制到另一块内存空间中，并且保证内存上的连续性，然后直接清空之前使用的内存空间。然后如此往复。

我们姑且将这两块内存区域称为from区和to区。

如下图所示，r1和r2作为GC Root对象，经过可达性分析后，标记除黄色对象为垃圾对象。

![img](https://pic3.zhimg.com/80/v2-c413099d3489670ae45201daba22395a_1440w.jpg)



复制过程如下，GC会将五个存活对象复制到to区，并且保证在to区内存空间上的连续性。



![img](https://pic3.zhimg.com/80/v2-3e09c14791e4eb03e791d69ff109f21e_1440w.jpg)



最后，将from区中的垃圾对象清除。



![img](https://pic2.zhimg.com/80/v2-b4aa1c25d2211a9e4eeb1af54463e155_1440w.jpg)

综上述，该算法在存货对象少，垃圾对象多的情况下，非常高效。其好处是不会产生内存碎片，但坏处也是显而易见的，就是直接损失了一半的可用内存。

### 标记压缩算法

标记压缩算法可以解决标记清除算法的内存碎片问题。

其算法可以看作三步： *标记垃圾对象* 清除垃圾对象 * 内存碎片整理

**其过程如下：**

首先标记除垃圾对象（黄色）



![img](https://pic2.zhimg.com/80/v2-0a878541b04514ddc82d55788f7c9595_1440w.jpg)



清除垃圾对象



![img](https://pic3.zhimg.com/80/v2-728b5fcbee68bdcd6b897a2001374a12_1440w.jpg)



内存碎片整理



![img](https://pic3.zhimg.com/80/v2-6360fb871d631ebfb1b3305d4d2bcbf6_1440w.jpg)

### 标记清除法

因为这种算法会产生大量的内存碎片。

标记清除算法的执行过程分为两个阶段：标记阶段、清除阶段。

- 标记阶段会通过可达性分析将不可达的对象标记出来。
- 清除阶段会将标记阶段标记的垃圾对象清除。

标记阶段如图所示：

![img](https://pic2.zhimg.com/80/v2-0a878541b04514ddc82d55788f7c9595_1440w.jpg)

Java堆中，黄色对象为不可达对象，在标记阶段被标记。

下面执行回收算法，执行后如图：

![img](https://pic3.zhimg.com/80/v2-728b5fcbee68bdcd6b897a2001374a12_1440w.jpg)

从上图可以清晰的看出此算法的缺陷，回收后会产生大量不连续的内存空间，即内存碎片。由于Java在分配内存时通常是按连续内存分配，那么当碎片空间不足以分配给新的对象时，就造成了内存浪费。

### 分代算法

分代算法基于复制算法和标记压缩算法。

首先，标记清除算法、复制算法、标记压缩算法都有各自的缺点，如果单独用其中某一算法来做GC，会有很大的问题。

例如，标记清除算法会产生大量的内存碎片，复制算法会损失一半的内存，标记压缩算法的碎片整理会造成较大的消耗。

其次，复制算法和标记压缩算法都有各自适合的使用场景。

复制算法适用于每次回收时，存活对象少的场景，这样就会减少复制量。

标记压缩算法适用于回收时，存活对象多的场景，这样就会减少内存碎片的产生，碎片整理的代价就会小很多。

分代算法将内存区域分为两部分：新生代和老年代。

根据新生代和老年代中对象的不同特点，使用不同的GC算法。

新生代对象的特点是：创建出来没多久就可以被回收（例如虚拟机栈中创建的对象，方法出栈就会销毁）。也就是说，每次回收时，大部分是垃圾对象，所以新生代适用于复制算法。

老年代的特点是：经过多次GC，依然存活。也就是说，每次GC时，大部分是存活对象，所以老年代适用于标记压缩算法。

新生代分为eden区、from区、to区，老年代是一整块内存空间，如下所示：



![img](https://pic3.zhimg.com/80/v2-84c67ca2cac106438b40f00d32261046_1440w.jpg)



**分代算法执行过程**

首先简述一下新生代GC的整个过程（老年代GC会在下面介绍）：新创建的对象总是在eden区中出生，当eden区满时，会触发Minor GC，此时会将eden区中的存活对象复制到from和to中一个没有被使用的空间中，假设是to区（正在被使用的from区中的存活对象也会被复制到to区中）。

有几种情况，对象会晋升到老年代： *超大对象会直接进入到老年代（受虚拟机参数-XX:PretenureSizeThreshold参数影响，默认值0，即不开启，单位为Byte，例如：3145728=3M，那么超过3M的对象，会直接晋升老年代）* 如果to区已满，多出来的对象也会直接晋升老年代 * 复制15次(15岁)后，依然存活的对象，也会进入老年代

此时eden区和from区都是垃圾对象，可以直接清除。

> PS：为什么复制15次(15岁)后，被判定为高龄对象，晋升到老年代呢？ 因为每个对象的年龄是存在对象头中的，对象头用4bit存储了这个年龄数，而4bit最大可以表示十进制的15，所以是15岁。

下面从JVM启动开始，描述GC的过程。

JVM刚启动并初始化完成后，几块内存空间分配完毕，此时状态如上图所示。

- 新创建的对象总是会出生在eden区



![img](https://pic4.zhimg.com/80/v2-f324399e33eec98da65e03bccb2577db_1440w.jpg)



- 当eden区满的时候，会触发一次Minor GC，此时会从from和to区中找一个没有使用的空间，将eden区中还存活的对象复制过去（第一次from和to都是空的，使用from区），被复制的对象的年龄会+1，并清除eden区中的垃圾对象。



![img](https://pic2.zhimg.com/80/v2-1cc8243a003529a5f2411020aa221799_1440w.jpg)



- 程序继续运行，又在eden区产生了新的对象，并产生了一个超大对象，并产生了一个复制后to区放不下的对象



![img](https://pic3.zhimg.com/80/v2-9275b7302784087a2586b988d0e18466_1440w.jpg)



- 当eden区再次被填满时，会再一次触发Minor GC，这次GC会将eden区和from区中存活的对象复制到to区，并且对象年龄+1，超大对象会直接晋升到老年代，to区放不下的对象也会直接晋升老年代。



![img](https://pic4.zhimg.com/80/v2-e0aec19130eff0c06d0d0d9a7c969303_1440w.jpg)



- 程序继续运行，假设经过15次复制，某一对象依然存活，那么他将直接进入老年代。



![img](https://pic1.zhimg.com/80/v2-9b076e16cb2f6ce3439df85700022c4c_1440w.jpg)



- 老年代 Full GC

在进行Minor GC之前，JVM还有一步操作，他会检查新生代所有对象使用的总内存是否小于老年代最大剩余连续内存，如果上述条件成立，那么这次Minor GC一定是安全的，因为即使所有新生代对象都进入老年代，老年代也不会内存溢出。如果上述条件不成立，JVM会查看参数HandlePromotionFailure[1]是否开启（JDK1.6以后默认开启），如果没开启，说明Minor GC后可能会存在老年代内存溢出的风险，会进行一次Full GC，如果开启，JVM还会检查历次晋升老年代对象的平均大小是否小于老年代最大连续内存空间，如果成立，会尝试直接进行Minor GC，如果不成立，老年代执行Full GC。



![img](https://pic3.zhimg.com/80/v2-d08a2b7cea0f90610b5d718bee527aaa_1440w.jpg)



eden区和from区的存活对象会复制到to区，超大对象和to区容纳不下的对象会直接晋升老年代。当eden区满时，触发Minor GC，此时判断老年代剩余连续内存已经小于新生代所有对象占用内存总和，假设HandlePromotionFailure参数开启，JVM还会继续判断老年代剩余连续内存是否大于历次晋升老年代对象的平均大小，如图所示，目前老年代还剩2个空间，如果之前平均每次晋升三个对象到老年代，剩余空间小于平均值，会触发Full GC。

- 老年代回收-标记：



![img](https://pic3.zhimg.com/80/v2-fabb597d08d9b3c7e97bc2c44d1fb5ba_1440w.jpg)



- 老年代回收-清除：



![img](https://pic3.zhimg.com/80/v2-cb68388bda86f0b9e59772da1ff9506e_1440w.jpg)



- 老年代回收-碎片整理：



![img](https://pic4.zhimg.com/80/v2-d7cf03aab254330e11575de079b6908f_1440w.jpg)



**Minor GC存在的问题**

Minor GC的问题在于，新生代的对象可能被老年代引用，而这种情况可达性分析是分析不到的，但这种情况的新生代对象是不应该被回收的。

HotSpot虚拟机提供了一个解决方案：卡表。

这种方法会将老年代内存平均分为很多的卡片，每个卡片都包含一部分对象，然后维护一个卡表，卡表是一个数组，每个元素指向一个卡片，并标识出这个卡片中有没有指向新生代的对象，如果有标识为1。这样一来，Minor GC只需要扫描卡表中标识为1的卡片即可，大大提升了效率。

卡表如下图所示：



![img](https://pic1.zhimg.com/80/v2-f50072c0fc08c9e6a3f1b786c23f74f4_1440w.jpg)



发布于 2019-09-08

# [新生代、老年代、永久代和各种GC](https://www.cnblogs.com/cuijj/p/10499621.html#!comments)



# 项目运行问题
## 消息消费之后，没有销毁
> 有空看一下LinkedBlockingQueue这个队列的

> 目前在用这个消息队列，但是队列相关的对象在不断积聚，没有销毁，可能和他有关系

> 按理说应该会有消费一个，销毁一个。

可能的解决方法：[LinkedBlockingQueue类中的take方法执行后不释放内存问题记录](https://blog.csdn.net/wangchaox123/article/details/103620375?spm=1035.2023.3001.6557&utm_medium=distribute.pc_relevant_bbs_down.none-task-blog-2~all~first_rank_ecpm_v1~rank_v31_ecpm-1.nonecase&depth_1-utm_source=distribute.pc_relevant_bbs_down.none-task-blog-2~all~first_rank_ecpm_v1~rank_v31_ecpm-1.nonecase)

其中：不过GC的工作方式是要等一个方法执行完后，才回收方法体中的数据，由于while(true)和take的线程等待问题，方法永远不可能退出，也就是说，只有手工调GC，他才回收。

