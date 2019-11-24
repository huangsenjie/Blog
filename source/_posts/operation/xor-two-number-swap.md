---
title: 为什么两个二进制值能通过异或交换？
date: 2019-11-12 23:08:02
tags:
 - 异或
 - 二进制
 - 两数交换
categories:
 - 位运算
---

在初学编程的时候我们总会通过一些简单的编程题来练习语法。两个数的交换就是常遇到的题目之一，它也常用于各种排序算法中。

### 惯用的两数方式

```java
public class TwoNumSwap {

    public static void main(String[] args) {
        Integer[] two = {1, 2};
        print(two[0], two[1]);
        swap(two);
        print(two[0], two[1]);
    }
    static void swap(Integer[] arr) {
        //通过临时变量来完成交换
        Integer temp = arr[1];
        arr[1] = arr[0];
        arr[0] = temp;
    }

    static void print(Integer a, Integer b) {
        System.out.println("交换前： a=" + a + ",b=" + b);
    }
}
```

ps: 为什么要使用数组?[如何用java来交换两个数](https://huangsenjie.github.io/Blog/2019/11/24/java/java-swap/)

***
输出

交换前： a=1,b=2

交换前： a=2,b=1
***

### 使用异或交换

```java
public class TwoNumSwap {

    public static void main(String[] args) {
        Integer[] two = {1, 2};
        print(two[0], two[1]);
        swapByXor(two);
        print(two[0], two[1]);
    }

    static void swapByXor(Integer[] arr) {
        arr[0] = arr[0] ^ arr[1];
        arr[1] = arr[0] ^ arr[1];
        arr[0] = arr[0] ^ arr[1];
    }

    static void print(Integer a, Integer b) {
        System.out.println("交换前： a=" + a + ",b=" + b);
    }
}
```
***
输出

交换前： a=1,b=2

交换前： a=2,b=1
***

### 为什么异或可以交换两数
在计算机中，当两个数异或得出第三个数时，从三个数中任意取两个数进行异或结果等于第三个数。

由此我们可以得出：
```java
public class TwoNumSwap {

    static void swapByXor(Integer[] arr) {
            //设arr[0] = a, arr[1] = b, a ^ b = c
            //arr[0] = c
            arr[0] = arr[0] ^ arr[1];
            //c ^ b = a, arr[1] = a
            arr[1] = arr[0] ^ arr[1];
            //c ^ a = b, arr[0] = b
            arr[0] = arr[0] ^ arr[1];
    
            //结果: arr[0] = b, arr[1] = a  
        }
}
    
```
### 为什么三个数任意两个异或的结果等于另一个？
此时，我们依然知道异或可以具备了交换两数的性质。但我们又有了新的问题，为什么异或具备这样的性质呢？
我们来看一看关于异或的定义。
---
在计算机中，当两个数异或得出第三个数时，从三个数中任意取两个数进行异或结果等于第三个数。

**重点在于第一句话: 在计算机中。**

---
异或是一种位运算，也就是对二进制值的运算。计算机中所有的数据，指令最终都用二进制表达，所以这句话的意思应该是：在对于二进制的运算中。

我们来看一个例子：
```text
    10101010
 ^  11110000
  ------------
    01011010
```
既然是位运算，我们只需将每个位独立的看，对于二进制值而言，一个位无非就1和0两种情况，两个位进行异或可以很容易的列举出它们的所以结果。

|        |        |        | result |        |        | result |
| :----: | :----: | :----: | :----: | :----: | :----: | :----: |
|   0    |    ^   |    0   |    0   |    ^   |    0   |    0   |
|   0    |    ^   |    1   |    1   |    ^   |    0   |    1   |
|        |        |        |        |    ^   |    1   |    0   |
|   1    |    ^   |    0   |    1   |    ^   |    0   |    1   |
|        |        |        |        |    ^   |    1   |    0   |
|   1    |    ^   |    1   |    0   |    ^   |    1   |    1   |

由上表可以看出，对于任何一个位而言，它都满足从三个数中任意取两个数进行异或结果等于第三个数。故而对于对于任何二进制值的异或运算都可以满足该性质。