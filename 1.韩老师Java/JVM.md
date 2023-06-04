## JAVA内存区域

JDK1.7

![Java 运行时数据区域（JDK1.8 之前）](https://guide-blog-images.oss-cn-shenzhen.aliyuncs.com/github/javaguide/java/jvm/java-runtime-data-areas-jdk1.7.png)

JDK1.8

![Java 运行时数据区域（JDK1.8 之后）](https://guide-blog-images.oss-cn-shenzhen.aliyuncs.com/github/javaguide/java/jvm/java-runtime-data-areas-jdk1.8.png)



JDK1.8改变：

​	在JDK8以前是放在JVM内存中的，由永久代实现。**JDK8后方法区不放在jvm内存(堆)中，放到了系统内存，可以不受JVM内存大小参数的限制。**

​	将原来放在方法区的**字符串常量池**和**静态变量**都转移到了Java堆中。其他信息放到系统内存（云空间）。



![img](https://img2020.cnblogs.com/other/1218593/202108/1218593-20210817193628421-1597678926.png)

常见面试题 ：

- 介绍下 Java 内存区域（运行时数据区）
- Java 对象的创建过程（五步，建议能默写出来并且要知道每一步虚拟机做了什么）
- 对象的访问定位的两种方式（句柄和直接指针两种方式）



## JVM 垃圾回收



![hotspot-heap-structure](https://snailclimb.gitee.io/javaguide/docs/java/jvm/pictures/hotspot-heap-structure.png)



新生代内存(Young Generation)，分为Eden，Survivor区。

老生代(Old Generation) ，即Tenured区域。

永久代(Permanent Generation)，**JDK8被移除堆外。**



​	JVM在为新生代中的对象分配内存时，会先将内存分配到Eden区中。当Eden区满时，JVM会启动垃圾回收机制，将Eden区中不再使用的对象回收掉。在这个过程中，JVM会将仍然存活的对象复制到Survivor区中。每次垃圾回收之后，Survivor区中会有一些对象仍然存活，而另一些对象则被回收掉。当Survivor区被填满后，JVM会将其中存活的对象转移到老年代中。

​	一般来说，Eden区的空间大小相对比较小，通常只占整个堆空间的一小部分。Survivor区的总大小通常也比较小，因为它们只是作为Eden区的一个缓冲区，用于存储存活下来的对象，而不是作为长期存储对象的空间。而老年代的空间大小则相对较大，因为老年代中的对象生命周期比较长，需要有足够的空间来存储它们。

​	总的来说，老年代的空间大小通常比较大，因为它需要存储长生命周期的对象，而且老年代中的对象数量和大小也可能会随着应用程序的运行时间不断增加。而Eden区和Survivor区的空间大小通常较小，因为它们只是作为对象分配和垃圾回收的中转区域，需要在不断的垃圾回收中得以清空，从而为新的对象分配提供空间。



常见面试题 ：

- 如何判断对象是否死亡（两种方法）。
- 简单的介绍一下强引用、软引用、弱引用、虚引用（虚引用与软引用和弱引用的区别、使用软引用能带来的好处）。
- 如何判断一个常量是废弃常量
- 如何判断一个类是无用的类
- 垃圾收集有哪些算法，各自的特点？
- HotSpot 为什么要分为新生代和老年代？
- 常见的垃圾回收器有哪些？
- 介绍一下 CMS,G1 收集器。
- Minor Gc 和 Full GC 有什么不同呢？