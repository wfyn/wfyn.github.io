---
title: 预热
date: 2024-05-23 21:36:04
tags: javase
categories: java
top_img: /images/bg2.jpg
---



闲来无事，写博客



### 1、使用 '==' 而不是 'equals()' 来比较字符串值

 默认情况下也就是从超类Object继承而来的equals方法与‘==’是完全等价的，比较的都是对象的内存地址，但我们可以重写equals方法，使其按照我们的需求的方式进行比较，如String类重写了equals方法，使其比较的是字符的序列，而不再是内存地址。 

### 2、接口实例化的方法

在Java中，接口是不能直接实例化的，因为接口本身是抽象的，不包含具体的实现。但是，可以通过以下几种方式来实例化接口：

1. **通过实现类实例化**： 创建一个实现了该接口的类的对象，并将其赋给接口类型的引用变量。这样，该对象就可以通过接口类型的引用进行访问。

   ```java
   javaIStorage storage = new MyStorageImpl(); // MyStorageImpl是实现了IStorage接口的类
   ```

2. **匿名内部类**： 可以通过创建一个匿名内部类来实现接口，并直接在实例化的过程中提供具体的实现。

   ```java
   javaIStorage storage = new IStorage() {
       @Override
       public void read() {
           // 实现read方法
       }
   
       @Override
       public void write() {
           // 实现write方法
       }
   };
   ```

3. **Lambda表达式**（仅适用于函数式接口）： 如果接口是函数式接口（只包含一个抽象方法），可以使用Lambda表达式来实例化接口。

   ```java
   javaIStorage storage = () -> {
       // Lambda表达式提供的具体实现
   };
   ```

通过以上方法，可以在实际编程中灵活地实例化接口并使用其功能。

