---
layout: mypost
title: 站在JVM上看String
categories: [JVM] 
---

**本文所提到的JVM版本为1.8**



# String的基本特性

## 为什么String是个不可变的量？

`private final byte[] value`

String底层是个经过final修饰的byte数组，当然属于不可变的量



## 为什么之前java9String结构有char[]数组变为byte数组？

因为大多数的对象都是拉丁字符，一个字符只需要一个字节的存储空间，那么使用char数组下就会产生空间的浪费。故变为现在的字符数组+一个encoding-flag字段。



## 字符串拼接操作

·常量与常量的拼接结果在常量池，原理是编译期优化
·常量池中不会存在相同内容的变量
·只要其中有一个是变量，结果就在堆中。变量拼接的原理是StringBuilder
·如果拼接的结果调用intern()方法，则主动将常量池中还没有的字符串对象放入池中，并返回此对象地址

如果拼接符号的前后出现了变量，则相当于在堆空间中new String0，具体的内容为拼接的结果
而调用intern方法，则会判断字符串常量池中是否存在JavaEEhadoop值，如果存在则返回常量池中的值，否者就在常量池中创建



## intern（）的使用

intern是一个native方法，调用的是底层C的方法字符串池最初是空的，由String类私有地维护。在调用intern方法时，如果池中已经包含了由equals（object）方法确定的与该字符串对象相等的字符串，则返回池中的字符串。否则，该字符串对象将被添加到池中，并返回对该字符串对象的引用。
如果不是用双引号声明的string对象，可以使用string提供的intern方法：intern方法会从字符串常量池中查询当前字符串是否存在，若不存在就会将当前字符串放入常量池中。



## 常见问题的细致解答

```java
String str1 = "str";
String str2 = "ing";
String str3 = "str" + "ing";//常量池中的对象
String str4 = str1 + str2; //在堆上创建的新的对象
String str5 = "string";//常量池中的对象
System.out.println(str3 == str4);//false
System.out.println(str3 == str5);//true
System.out.println(str4 == str5);//false
```

`str1` 、 `str2` 、 `str3` 、`str5`都属于字符串常量池中的对象。所以他们是可以进行比较的 ，而`str4`是一个堆上的新对象自然是false。          
