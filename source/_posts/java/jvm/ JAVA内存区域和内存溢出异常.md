---
title: JVM-JAVA内存区域和内存溢出异常
date: 2020-09-06 20:29:31
tags: 
 - JAVA
 - JVM
categories:
 - JVM
---

## JAVA内存区域

### 线程私有

#### 程序计数器（Program Counter Register）

定义：当前线程所执行字节码的行号指示器，指向了下一条将要被执行的指令。线程独占

生命周期：与线程相同

PS：
1. 在Native方法中，计数器值为空。

2. 本区域是Jvm规范中唯一没有指定OutOfMemoryError异常的区域。

#### JAVA 虚拟机栈（Java virtual machine stacks）

定义：虚拟机栈是描述方法执行的内存模型，每一个方法对应一个栈帧，方法调用则栈帧入栈，方法返回则栈帧出栈。 线程独占

栈帧：运行方法的基础数据结构-保存了局部变量表，操作数栈，动态链接，方法出口等信息。

局部变量表：存放了编译期可知的各种变量类型-8中基本数据类型，对象引用（Reference）和returnAdress（指向一条字节码的地址）

slot：局部变量表分配内存空间的基本单位，4字节。即Double和Long类型需要两个slot

异常：
StackOverFlowError：线程请求栈深超过虚拟机允许栈深时
OutOfMemoryError：栈深可动态扩展且无法申请足够内存时

#### 本地方法栈

定义：用于执行其他语言的方法

SUN HOTSPOT将其与虚拟机栈合二为一，也会抛出相同的异常

### 线程共享

#### JAVA 堆（JAVA Heap）

定义：用于存放几乎所有new出来的对象，线程共享

生命周期：与虚拟机相同

区域细分：`JAVA堆`是垃圾回收器的主要管理区域，故也称`GC堆（Garbage Collected Heap）`。从内存回收上看，回收期器GC时基本使用分代收集算法。故`JAVA堆`可细分为新生代（Eden区，from Surviver区，to Surviver区），老年代。从内存分配上看，`JAVA堆`可能为线程在堆中划分出线程独立的分配缓冲区（Thread Local Allocation Buffer）

相关参数

|参数名称|含义	|默认值	|描述|
|:--------:   |---   |----- |---|
|-Xms	|初始堆大小	|物理内存的1/64	|默认(MinHeapFreeRatio参数可以调整)空余堆内存小于40%时，JVM就会增大堆直到-Xmx的最大限制.
-Xmx	|最大堆大小	|物理内存的1/4	|默认(MaxHeapFreeRatio参数可以调整)空余堆内存大于70%时，JVM会减少堆直到 -Xms的最小限制

异常：
OutOfMemoryError：堆内存不足以分配新的对象时抛出

#### 方法区（Method Area）

定义：用于存放代码，类对象，常量和静态变量

方法区与永久代：JVM规范并没有定义永久代的概念，永久代是HOTSPOT分代收集扩展至方法区的结果。


异常：
OutOfMemoryError：方法区内存不足以分配是抛出

#### 运行时常量池（Runtime Constant Pool）

定义：方法区的一部分，用于保存各种字面量，符号引用（一般也保存直接引用），相比Class对象常量池，它是动态的，可以在运行时写入新内容（如String.intern）

异常：
OutOfMemoryError：方法区内存不足以分配是抛出

#### 直接内存（Direct Memory）

定义：也称堆外内存，不受堆大小限制，受物理机资源限制。基于通道（Channel）和缓冲（Buffer）的NIO可直接通过调用Native函数分配直接内存，由一个存储于堆中的DirecByteBuffer对象进行操作。

异常：
OutOfMemoryError：直接内存过大导致动态扩展时内存不足

## 对象访问方式

### 句柄访问

reference类型变量中存储对象的句柄地址，句柄对象存储于句柄池中，句柄对象保存了对象的真实数据（堆中）和类型信息（方法区）。
好处：当对象被移动（比如垃圾回收），栈变量不用同步更新。

#### 直接指针

reference类型变量中直接存储堆中对象的地址

好处：减少开销



