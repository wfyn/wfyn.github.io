---
title: java基本类型和包装类
date: 2024-5-24 13:37:41
tags:
  - 基本数据类型
  - 堆、栈和常量池
  - 包装类
categories: 学习
top_img: /images/bg2.jpg
---



### 1.基本数据类型

数值类型
整型
byte 1字节 2^7-1
short 2字节 2^15-1
int 4字节 2^31-1
long 8字节 2^63-1 需要在值后面指定L
浮点型
float 4字节 单精度浮点型，最大7位精度 需要在值后面指定F
double 8字节 双精度浮点，最大16位精度
布尔类型
boolean 只有true和false
字符类型
char 0~65535 赋值用单引号 ‘A

原文链接：https://blog.csdn.net/baichrn/article/details/119025540

### 2.堆、栈常量池

 整型常量池 -128~127



### 3.包装类

 

```java
 Integer i1 = 40;
  Integer i2 = 40;
  Integer i3 = 0;
  Integer i4 = new Integer(40);
  Integer i5 = new Integer(40);
  Integer i6 = new Integer(0);

  System.out.println("i1=i2   " + (i1 == i2));
  System.out.println("i1=i2+i3   " + (i1 == i2 + i3));
  System.out.println("i1=i4   " + (i1 == i4));
  System.out.println("i4=i5   " + (i4 == i5));
  System.out.println("i4=i5+i6   " + (i4 == i5 + i6));
  System.out.println("40=i5+i6   " + (40 == i5 + i6));

----结果----
(1)i1=i2   true
(2)i1=i2+i3   true
(3)i1=i4   false
(4)i4=i5   false
(5)i4=i5+i6   true
(6)40=i5+i6   true
```



这段代码涉及了自动装箱和拆箱的概念，以及 Java 中 Integer 类型的缓存范围。

1. `Integer i1 = 40;` 和 `Integer i2 = 40;`：在 Java 中，对于数值在 **-128 到 127** 之间的 Integer 对象，会进行缓存，因此 `i1` 和 `i2` 实际上是引用了同一个 Integer 对象。所以 `(i1 == i2)` 为 true。
2. `(i1 == i2 + i3)`：`i2 + i3` 会进行自动拆箱，得到的是一个 int 类型的值，即 40。因此 `(i1 == 40)`，由于 i1 也是一个 Integer 对象，所以会进行自动装箱，然后比较对象引用，结果也是 true。
3. `(i1 == i4)`：`i4` 是通过 `new Integer(40)` 显式创建的一个新的 Integer 对象，而 `i1` 是从缓存中取得的对象，它们的引用不同，因此 `(i1 == i4)` 为 false。
4. `(i4 == i5)`：同样，`i4` 和 `i5` 是分别通过 `new Integer(40)` 创建的两个不同的对象，它们的引用也不同，因此 `(i4 == i5)` 为 false。
5. 自动拆箱
6. `(40 == i5 + i6)`：同样，`i5 + i6` 会进行自动拆箱，得到的是一个 int 类型的值，即 40，然后 40 会自动装箱成一个 Integer 对象，而 `i5 + i6` 所得到的对象与 40 的引用是相同的，因此 `(40 == i5 + i6)` 为 true。

```java
package com.igeek.javase.ch07.numbers;

/**
 * @Author fengqq
 * @Description 包装类
 * @Date 2024/5/24  11:24
 *
 * 基本类型  对应的包装类
 * byte     Byte
 * short    Short
 * int      Integer
 * long     Long
 * float    Float
 * double   Double
 * char     Character
 * boolean  Boolean
 *
 * 1.（位于java.lang包中）
 * 2. 整型常量池 -128~127
 * 3. 自动装箱/自动拆箱  jdk5，不要频繁的发生拆装箱
 *      自动装箱：将基本数组类型的值 自动转换为 包装类型         int-->Integer  等价于 new Integer(int i)
 *      自动拆箱: 将包装类型 自动转换为 对应的 基本数组类型的值   Integer-->int  等价于 int a = intValue()
 *
 * 4. 基本数据类型和字符串进行转换
 *
 * 5.static String toBinaryString(int i) 在基地 2无符号整数返回整数参数的字符串表示形式。
 *  static String toHexString(int i)     在基地 16无符号整数返回整数参数的字符串表示形式。
 *  static String toOctalString(int i)   在基地 8无符号整数返回整数参数的字符串表示形式。
 *
 */
public class IntegerDemo {
    public static void main(String[] args) {
        //自动装箱：将基本数组类型的值 自动转换为 包装类型
        Integer i1 = 10;
        Integer i2 = 10;
        Integer i3 = 128;//隐式装箱 new Integer(128)
        Integer i4 = 128;//隐式装箱 new Integer(128)
        Integer i5 = new Integer(10);//手动装箱
        Integer i6 = new Integer(10);//手动装箱

        System.out.println("i1==i2 = " + (i1 == i2));//true
        System.out.println("i3==i4 = " + (i3 == i4));//false
        System.out.println("i5==i6 = " + (i5 == i6));//false
        System.out.println("i1==i5 = " + (i1 == i5));//false

        //自动拆箱
        int a = i5;
        System.out.println("a = " + a);

        //参与运算时 会自动拆箱
        Integer n = 20;
        n++;//先自动拆箱 --> ++ --> 最后自动装箱
        int b = 10;
        n+=b;//Integer n = n+b;  //先自动拆箱 --> n+b --> 最后自动装箱

        int c = n+10;//拆箱

        System.out.println("------------自动拆箱---------------");
        Integer x1 = 10;
        int x2 = 10;
        Integer x3 = new Integer(10);
        System.out.println(x1 == x2); //true  Integer == int     将Integer自动拆箱为int 用int==int比较
        System.out.println(x1 == x3); //false Integer == Integer 用地址比较
        System.out.println(x1.equals(x3)); //true
        System.out.println(x2 == x3); //true


        System.out.println("---------基本数据类型和字符串进行转换-----------");
        int m = 100;
        //int --> String
        String s1 = m+"";
        String s2 = String.valueOf(m);
        String s3 = Integer.toString(m);

        //String --> int
        String str = "123";
        //如果字符串中包含非法字符 则转换为整数类型时 会产生异常 NumberFormatException
        int n1 = Integer.parseInt(str);//默认使用十进制来解析字符串

        String str2 = "a";
        int n2 = Integer.parseInt(str2,16);//使用16进制来解析字符串
        System.out.println("n2 = " + n2);

        //String --> Integer
        Integer t = Integer.valueOf("123");

        //String --> double
        double v = Double.parseDouble("6.12");


    }
}

```



