---
title: String，StringBuilder，StringBuffer
date: 2024-5-24 11:02:27
tags: 
	- String
	- StringBuilder
	- StringBuffer
categories: java
top_img: /images/bg2.jpg

---

### 1.String类

 1.不能被继承 final class String {} 实现接口CharSequence(字符序列)

 2.字符串本质是一个字符数组，索引从0开始

 3.字符串是常量，一旦被赋值，就不能被改变,String是不可变字符序列

 4.字符串常量池：jdk对String优化。将在堆内存中开辟了一段空间(常量池),所有字符串的字面值 都维护在池中

 String s = "abc";     当定义一个变量s时，jdk首先会在池中寻找有没有"abc",如果没有，则在池中开辟新空间值为"abc" 将新空间的地址赋给变量s

   String s2 = "abc";    当定义一个变量s2时，jdk首先会在池中寻找有没有"abc",如果有，则将池中”abc“的地址 赋给变量s2

5.对字符串进行API方法处理后(substring,replace,trim...)返回的是新字符串，原始字符串不改变

String常用方法

```java
public class StringDemo {
    public static void main(String[] args) {
        //不可变特性
        String s = "abc";
        s = "hello";//没有改变原始对象"abc"的内容，只是将新对象"hello"的地址指向变量s

        //常量池
        String s1 = "abc";
        String s2 = "abc";
        String s3 = new String("abc");
        String s4 = new String("abc");
        System.out.println("s1==s2 :"+(s1==s2));//true
        System.out.println("s3==s4 :"+(s3==s4));//false
        System.out.println("s1==s4 :"+(s1==s4));//false
        // 引用类型 == 比较的是地址  使用equals方法比较的内容
        System.out.println("s3.equals(s4) = " + s3.equals(s4));//true

        System.out.println("----------------常用方法---------------------");
        String str = " Kworld 1#233#3DHDJDSHH#JhbKSSD ";
        //1.求字符串的长度
        System.out.println("str.length() = " + str.length());

        //2.求 K 在字符串中第一次出现的位置
        System.out.println("str.indexOf('K') = " + str.indexOf('K'));
        System.out.println("str.indexOf(\"K\") = " + str.indexOf("K"));

        //3.求 K 在字符串中最后一次出现的位置
        System.out.println("str.lastIndexOf(\"K\") = " + str.lastIndexOf("K"));

        //4.取出 子串world substring(start,end) 截取[start,end)区间段的子串2
        String sonStr = str.substring(2, 7);
        System.out.println("截取后的子串 = " + sonStr);
        System.out.println("原始字符串 = " + str);

        //5.获取 字符串中 第5个位置的字符
        System.out.println("str.charAt(4) = " + str.charAt(4));

        //6.去掉字符串的首尾空格
        System.out.println("str.length() = " + str.length());
        System.out.println("str.trim().length() = " + str.trim().length());


        //7.将字符串中所有字母 变成 大写字符
        System.out.println("str.toUpperCase() = " + str.toUpperCase());
        //8.将字符串中所有字母 变成 小写字符
        System.out.println("str.toLowerCase() = " + str.toLowerCase());

        //9.将明文密码变成密文    \\d代表任意一个数字
        String password = "123456";
        String md5Password = password.replaceAll("\\d", "*");
        System.out.println("md5Password = " + md5Password);

        //将字符串中# 全部变成$
        System.out.println("将字符串中# 全部变成$ = " + str.replace("#", "$"));

        //10.将一个字符串按照 # 进行分割为 字符串数组
        String[] srr = str.split("#");
        System.out.println("Arrays.toString(srr) = " + Arrays.toString(srr));

        //11.判断文件名是否是java文件
        String fileName = "demo.java";
        System.out.println("判断文件名是否是java文件 = " + fileName.endsWith(".java"));

        //12.判断字符串中是否包含 "world"
        System.out.println("判断字符串中是否包含(\"world\") = " + str.contains("world"));

        //13.比较两个字符串内容是否相同 区别大小写
        String a = "hello";
        String b = "HeLLo";
        System.out.println("a.equals(b) = " + a.equals(b));
        //14.比较两个字符串内容是否相同 不区别大小写
        System.out.println("a.equalsIgnoreCase(b) = " + a.equalsIgnoreCase(b));

        String x = "你好";
        //15.将字符串 转换为 字符数组
        char[] crr = x.toCharArray();
        System.out.println("Arrays.toString(crr) = " + Arrays.toString(crr));

        //16.将字符数组 转换为 字符串
        String s5 = new String(crr);

        //按照指定的字符集 进行 编码和解码
        //UTF:一个中文占用3个字节
        //GBK:一个中文占用2个字节
        //如何处理乱码：保证编码和解码使用的字符集是一致的
        //17.将字符串 转换为 字节数组 ==> 解码
        byte[] brr1 = x.getBytes(); //使用平台默认的字符集(UTF-8)进行解码  [-28, -67, -96, -27, -91, -67]
        System.out.println("Arrays.toString(brr1) = " + Arrays.toString(brr1));

        try {
            byte[] brr2 = x.getBytes("GBK");//根据指定的字符集进行解码 [-60, -29, -70, -61]
            System.out.println("Arrays.toString(brr2) = " + Arrays.toString(brr2));
        }catch (UnsupportedEncodingException e){
            System.out.println("系统不支持所写字符集！！！解码失败！！！");
        }

        byte[] bytes = {-60, -29, -70, -61};//GBK格式得到的
        //18.将字节数组 转换为 字符串 ==> 编码
        String s6 = new String(bytes); //使用平台默认的字符集(UTF-8)进行编码
        System.out.println("s6 = " + s6);//乱码

        try {
            String s7 = new String(bytes,"GBK");
            System.out.println("s7 = " + s7); // 不乱码
        }catch (UnsupportedEncodingException e){
            System.out.println("系统不支持所写字符集！！！编码失败！！！");
        }
    }

}

```



### 2.StringBuffer与StringBuilder

```java
public class StringBufferDemo {
    public static void main(String[] args) {
        StringBuffer sbf = new StringBuffer(); //""
        //末尾追加“abc”
        sbf.append("abc");
        System.out.println("sbf = " + sbf);//"abc'
        //末尾追加“def”
        sbf.append("def");
        System.out.println("sbf = " + sbf);//"abcdef"

        //大量api方法返回值都是this 通常写成链式编程
        sbf.append("h").append("q").append("w");
        System.out.println("sbf = " + sbf);


        System.out.println("--------------------------");
        appendForString();
        appendForStringBuffer();
        appendForStringBuilder();
    }

    //测试String拼接操作
    public static void appendForString(){
        long start = System.currentTimeMillis();
        String result = "";
        for (int i = 0; i < 10000; i++) {
            result+=i; //每次拼接会产生一个新对象  10000个对象
        }
        System.out.println("result = " + result);

        long end = System.currentTimeMillis();
        System.out.println("测试String拼接操作的执行时间:"+(end-start));
    }

    //测试StringBuffer拼接操作
    public static void appendForStringBuffer(){
        long start = System.currentTimeMillis();
        StringBuffer result = new StringBuffer("");
        for (int i = 0; i < 10000; i++) {
            result.append(i); //每次拼接返回是原始对象  1个对象
        }
        System.out.println("result = " + result);

        long end = System.currentTimeMillis();
        System.out.println("测试StringBuffer拼接操作的执行时间:"+(end-start));
    }


    //测试StringBuilder拼接操作
    public static void appendForStringBuilder(){
        long start = System.currentTimeMillis();
        StringBuilder result = new StringBuilder("");
        for (int i = 0; i < 10000; i++) {
            result.append(i); //每次拼接返回是原始对象  1个对象
        }
        System.out.println("result = " + result);

        long end = System.currentTimeMillis();
        System.out.println("测试StringBuilder拼接操作的执行时间:"+(end-start));
    }
}

```

### 3. 面试题：String StringBuffer StringBuilder 区别

####   String:

 	1. 不可变字符串

  	2. 线程安全  因为不可变final char[] value,所以 每个线程 对字符串进行操作后返回的是一个新字符串

​	  3. 执行拼接等，效率低，大量浪费有限的内存空间

  

####     StringBuffer:

​    1. 可变字符串  对字符串进行操作后返回的是原始对象

​    2. 多线程并发访问下，线程安全

​    3. API方法中 添加 synchronized关键字(悲观锁)

​    4. 效率低

​    5. 使用场景：大量拼接且有竞争的情况优先考虑StringBuffer

  

####     StringBuilder:

​    1. 可变字符串  对字符串进行操作后返回的是原始对象

​    2. 多线程并发访问下，线程不安全，对于同一个共享变量 会出现竞争问题

​    3. 效率高

​    4. 使用场景：大量拼接且单线程下优先考虑StringBuilder

