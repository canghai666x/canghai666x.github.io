---
title: 关于java中判断相等的一些思考
date: 2018-11-26
tags: java
---
## 关于java中判断相等的一些思考
前两天，一个网友在群里问了一个问题，关于java的String判断相等。
举个简单的例子
![](https://ws1.sinaimg.cn/large/0063dFB6gy1fxlstcbl72j30gf06hjru.jpg)
输出结果是 
**false**
**false**
而使用equals方法来判断
![](https://ws1.sinaimg.cn/large/0063dFB6gy1fxlss1o40cj30gs06874p.jpg)
结果就是
**true**
**true**
由此，我们知道，在java中想判断两个字符串的内容是否相同，应该使用String对象的equals方法。
但是在这里我们只是知道了**How**，却不知道**Why**。
我对此有了好奇心，为什么java的String这么特殊，明明都是“aaa”，用==来判断就是false，用equals来判断就ture呢？
### 第一个问题，为啥==不可以判断String是否相等
想搞明白这个问题，首先就要去找==的实现。
==是基础运算符，在jdk中并没有，是由虚拟机实现的。
我看了一下The Java® Virtual Machine Specification Java SE 10 Edition，也就是java虚拟机规范。
2.2 Data Types中说
>he Java Virtual Machine operates on two kinds of types: primitive types and reference types

java虚拟机有两种类型，基础数据类型和引用类型。
基础数据类型有8中：byte,short,int,long,char,float,double,boolean.
2.4 Reference Types and Values中说
>here are three kinds of reference types: class types, array types, and interface types. Their values are references to dynamically created class instances, arrays, or class instances or arrays that implement interfaces, respectively.

引用类型有三种，类类型、数组类型、接口类型，他们的值分别是对类和数组的实例或实现的接口的数组的引用。
2.7 Representation of Objects中说
>The Java Virtual Machine does not mandate any particular internal structure for objects.
In some of Oracle’s implementations of the Java Virtual Machine, a reference to a class instance is a pointer to a handle that is itself a pair of pointers: one to a table containing the methods of the object and a pointer to the Class object that represents the type of the object, and the other to the memory allocated from the heap for the object data.

对象的表示：在oracle虚拟机中，对类实例的引用是指向句柄的指针。
现在，我们知道，上面我们创建了两个对象值为“aaa”的字符串，分别交给引用astring和bstring.
这两个引用，也就是指针，指向了不同的实例，两个实例存在于堆中。显然两个实例所在的位置不同，地址不同，引用值也不同。
在Table 2.11.1-A中有java虚拟机支持的指令集
![](https://ws1.sinaimg.cn/large/0063dFB6gy1fxltq47dkkj30gh0nr757.jpg)
在2.11.3中解释了算术指令
>Comparison: dcmpg, dcmpl, fcmpg, fcmpl, lcmp.

比较指令有这些，对比指令集可以看出，reference类型并不支持比较，所以在实际中会将引用类型的值转换为支持比较指令lcmp的long类型比较。

综上所述，==比较两个对象引用比较的是两个对象在堆中的地址，而不是对象的value，而地址不同，指针值转化为long值比较的结果就是**false**了。
### 第二个问题，为啥equals方法可以比较两个String对象的值呢？
查找到String的源码中的equals方法：
![](https://ws1.sinaimg.cn/large/0063dFB6gy1fxltzv0aprj30nj0aldge.jpg)
如果两个对象是同一个，也就是说引用值相同，直接返回true.
不然判断这个对象是否是String的实例，是的话讲目标对象强转为String类型，比较。
先判断编码格式，如果是Latin1编码，使用StringLatin1的equals方法比较两个字符串的value,
如果是utf16编码,使用StringUTF16的equals比较两个字符串对象的value。
![](https://ws1.sinaimg.cn/large/0063dFB6gy1fxlub2lbrvj308p019q2q.jpg)
value是String对象的一个属性，byte[]类型。
String的编码类型：如果String存的全是英文，就用Latin1；如果有中文，就用utf16.
我们先看StringLatin1.equals()的实现。
![](https://ws1.sinaimg.cn/large/0063dFB6gy1fxluesumpaj30iv09ygly.jpg)
逐个比较byte[]的每一位，而byte是基础类型，实际上也是添0转long比较，是可以直接比较的。
好了，如此将两个String的value属性(byte[]类型)按位比较，是可以正确比较的。

至于StringUTF16.euqals()的实现，感兴趣的朋友自己去看看吧。