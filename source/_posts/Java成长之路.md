---
title: Java成长之路
date: 2022-05-20 10:48:39
tags: 后端技术
---

# Java成长之路

[TOC]

> 闲时之路，忙时有书，阅读丈量世界，一章一篇总关情。



<!--more-->



## 一、Java

### 1.连引用都答不上，凭什么说你是Java服务端开发

**【划重点】在Java中引用包括：**

- FinalReference 强引用
- SoftReference 软引用
- WeakReference 弱引用
- PhantomReference 虚引用

那么为什么会提供这四种引用呢，主要原因有：

- 方便Jvm进行垃圾回收
- 方便开发人员使用，开发人员可以灵活的决定某些对象的生命周期



#### FinalReference 强引用

类似于 Object o = new Object() 这类的引用，创建一个对象后，该引用会被保存在JVM栈中，而且只要强引用存在，垃圾回收器就不会回收掉被引用的对象。

```java
Object o = new Object();
```



#### SoftReference 软引用

软引用关联的对象，在内存不够的情况下，会把这些软引用关联的对象列入垃圾回收范围中，然后进行回收，也就是说软引用并非是完全安全的，在内存不够的情况下是会被垃圾回收器回收掉的。

```java
public class SoftReferenceDemo {
    public static void main(String[] args) {
        int count = 5;
        SoftReference[] softReferences = new SoftReference[count];
        for (int i = 0; i < count; i++) {
            softReferences[i] = new SoftReference<TestObject>(new TestObject("TestObject-" + i));
        }

        for (int i = 0; i < count; i++) {
            Object o = softReferences[i].get();
            if (o == null) {
                System.out.println("null");
            } else {
                System.out.println(((TestObject) o).name);
            }
        }
    }
}

class TestObject {
    public byte[] values;
    public String name;

    public TestObject(String name) {
        this.name = name;
        values = new byte[1024 * 1024 * 500];
    }
}
```

通过注释便可以知道，我这里实例化了多个大对象，然后放入softReferences数组中，之后便遍历打印出其中的对象的命名，打印结果如下:

```
null
null
null
TestObject-3
TestObject-4
```

可以通过结果看出，前面四个对象因为内存不够而被垃圾回收器回收了。

**日常使用**

在我司的项目中，部分是使用软引用来保存从数据库中取出的数据，具体是做了一个中间层的封装，该中间层的作用就是在get出数据的时候会去判断数据是否为null，如果是为null再次从数据库读取，读取后再放入软引用的集合中，这样的做法是可以避免内存溢出。

#### WeakReference 弱引用

弱引用比软引用更弱，被弱引用关联的对象只能存活到发生下一次垃圾回收之前，也就是说当发生GC时，无论当前内存是否足够，都会被回收掉。

```java
public class WeakReferenceDemo {
    public static void main(String[] args) {
        WeakReference<String> stringWeakReference = new WeakReference<>(new String("I am here!"));
        System.out.println(stringWeakReference.get());

        System.gc();

        System.out.println(stringWeakReference.get());
    }
}
```

代码很简短，就是先构建一个弱引用对象，然后在gc前先打印出来证明它存在过，之后手动调用gc，再次打印，可以看出已经没了。运行结果如下

```
I am here!
null

Process finished with exit code 0
```

#### PhantomReference 虚引用

虚引用和上面不同的地方在于，一个对象是否有虚引用的存在，完全不会对其生存时间构成如何影响，并且也无法通过虚引用来获取一个对象的实例，也就是说跟没有引用与之关联一样，在任何时候都可能被垃圾回收器回收。

那么这样就很容易产生疑问了，虚引用的作用又是什么呢？

作用就是能在这个对象被收集器回收时收到一个系统通知，实现追踪垃圾收集器的回收动作，比如在对象被回收的时候，会调用该对象的finalize方法。

在给出相关demo前，要先介绍一个

**ReferenceQueue 引用队列**

ReferenceQueue 引用其实也可以归纳为引用中的一员，可以和上述三种引用类型组合使用【软引用、弱引用、虚引用】。

那么它有何作呢？

在创建Reference时，手动将Queue注册到Reference中，而当该Reference所引用的对象被垃圾收集器回收时，JVM会将该Reference放到该队列中，而我们便可以对该队列做些其他业务，相当于一种通知机制。

```java
public class PhantomReferenceDemo {
    public static void main(String[] args) {
        ReferenceQueue referenceQueue = new ReferenceQueue();

        PhantomReference<Object> phantomReference = new PhantomReference<>(new String("I am here"), referenceQueue);
        System.out.println("phantomReference.get():" + phantomReference.get());

        System.gc();

        try {
            Thread.sleep(1000);
        } catch (InterruptedException e) {

        }

        Reference<?> reference;
        while ((reference = referenceQueue.poll()) != null) {
            if (reference == phantomReference) {
                System.out.println("被回收了...");
            }
        }
    }
}
```

插曲：IDEA打印GC日志的方法，在vm选项中添加`-XX:+PrintGCDetails`

```
phantomReference.get():null
被回收了...

Process finished with exit code 0
```

我们可以从结果中看到先是从引用中get出来的对象为null，证明上面说的无法通过虚引用来获取一个对象的实例，并且在回收后会被放入队列中。

#### 和Reference相关的概念

首先为了方便JVM进行管理，Reference是有状态的，可以分为以下四种状态

- active  一般来说内存一开始被分配的状态，而当被引用的对象的可达性发生变化后gc就会将引用放入pending队列并将其状态改为pending状态。
- pending 指的是准备要被放进pending队列的对象。
- enqueue 指的是对象的内存已经被回收了。
- inactive 这是最终的状态，不能再变为其它状态。

#### JVM怎么知道引用在不在

关于JVM怎么知道引用在不在，这就涉及到了JVM的可达性分析算法了 JVM的可达性分析算法的简单思路就是通过一系列GC Roots作为出发点，向下搜索，搜索所走过的路径称为引用链，当一个对象到GC Roots没有任何引用链，即表明从GC Roots到这个对象不可达时，证明此对象不可用，可被回收。如下图所示

哪些对象可以作为GC Roots呢? 这里给出几个，如下:

- 虚拟机栈中引用的对象
- 方法区中类静态属性引用的对象
- 方法区中常量引用的对象
- 本地方法栈JNI引用的对象

具体的想要深入研究的可以自行百度&谷歌，或者等我后面深入分析。

#### 最后的最后

该篇文章基本解答了【谈谈对Java中几种引用的理解】，如果想要更深入的研究，就要从源码入手了解了。 下次遇见这种面试题，基本上就不慌了，因为实际上只要认真看完该篇文章并且记住几个关键的地方，基本上就不会被面试官问倒了，并且该篇文章后面也解答了【JVM怎么知道引用在不在】和【哪些对象可以作为GC Roots】的问题。







## 二、Golang






## 三、网络

### 3.1 网络协议



- 请详细描述三次握手和四次挥手的过程

  要求熟悉三次握手和四次挥手的机制，要求画出状态图。

  

- 四次挥手中TIME_WAIT状态存在的目的是什么?

  这个问题是画出四次挥手状态图，会引申问你。不排除还会问为什么四次挥手是四次不是二次等问题。最好是把相关问题均掌握。



- TCP是通过什么机制保障可靠性的?

  从四个方面进行回答，ACK确认机制、超时重传、滑动窗口以及流量控制，深入的话要求详细讲出流量控制的机制。



## 四、操作系统



## 五、数据结构与算法



## 六、数据库



## 七、系统设计



## 八、工具



## 九、资料



## 十、编码实践
