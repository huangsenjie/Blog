---
title: 线程的基本属性和使用
p: java/thread/basic-concepts
date: 2020-06-18 13:58:17
categories:
 - JAVA
tags: 
 - java
 - 多线程
---

# JAVA中的线程

## 创建与启动线程

JAVA通过Thread表示一个线程，通过两种方法可以创建和启动一个线程

### 1. 继承Thread对象， 重写其Run方法

```java
public class InheritThread extends Thread {
    @Override
    public void run() {
        //实现线程执行逻辑
    }
}
```

Java类只支持单继承，当类已有父类时可通过实现Runnable来创建线程

### 2. 实现Runnable接口， 实现其Run方法

```java
public class ImplementsRunnable implements Runnable{
    public void run() {
        //实现线程执行逻辑
    }
}
```

### 3. 启动线程

```java
public class Main {
    public static void main(String[] args) {
        InheritThread inheritThread = new InheritThread();
        Thread implementsRunnable = new Thread(new ImplementsRunnable());
        //调用start启动线程
        inheritThread.start();
        implementsRunnable.start();
    }
}
```

> 还可以通过线程池的发生来调度线程

## 线程的基本属性和方法

### 1. ID

ID是线程的唯一标识，创建线程时自动生成

```
public long getId()
```

### 2. Name

Name是线程的名称，默认情况下位Thread-{ID}

```
public final synchronized void setName(String name)
public final String getName()
```

### 3. Daemon

守护线程是其他线程的辅助线程，例如垃圾回收线程。当程序的所以非守护线程死亡时，程序结束

```
public final void setDaemon(boolean on)
public final boolean isDaemon()
```

### 4. Priority

会被映射到操作系统中的优先级，可能会出现不同优先级但在操作系统中是相同优先级的情况。
值为1到10，默认为5

```
public final void setPriority(int newPriority)
public final int getPriority()
```

### 4. isAlive

判断线程是否结束

```
public final native boolean isAlive()
```

### 5. state

线程从创建到死亡，整个生命周期中会历经不同的状态

```java
public enum State {
        /**
         * 线程对象创建，还没有调用start方法
         */
        NEW,

        /**
         * 线程处于可执行状态， 正在执行中或等待执行调度
         */
        RUNNABLE,

        /**
         * 等待获取Synchronized同步代码块锁时处于的状态
         * 当线程调用Wait释放锁，被其他线程notify唤醒后因为还需要重新获取锁，
         * 可能会进入此状态
         */
        BLOCKED,

        /**
         * 当前线程调用一下方法时：
         *   1. Object.wait
         *   2. Thread.join
         *   3. LockSupport.park
         * 线程处于WAITING状态
         */
        WAITING,

        /**
         * 当线程调用以下方法并指定等待时间时，处于TIMED_WAITING状态：
         *   1.Thread.sleep
         *   2.Object.wait
         *   3.Thread.join
         *   4.LockSupport.parkNanos
         *   5.LockSupport.parkUntil
         */
        TIMED_WAITING,

        /**
         * 线程执行结束时状态
         */
        TERMINATED;
    }
```

### 6. sleep

使线程等待一段时间，不释放锁

```
public static native void sleep(long millis)
public static void sleep(long millis, int nanos)
```

### 6. yield

向调度器建议可以优先执行其他线程

```
public static native void yield()
```


