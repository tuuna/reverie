首先我们需要掌握什么是内存溢出和内存泄漏

内存泄漏：即声明的对象无法被回收，一直存在于内存中，使得占用的内存就像被泄漏消失了一样

内存溢出：即剩余可用的内存空间过小，无法分配出下一步工作需要的内存。

内存泄漏过多必然会导致内存溢出

内存溢出(OOM)会出现在jvm内存的各个区域，程序计数器区是唯一不会发生OOM的地方。这些区域包括：栈、堆、方法区和运行时常量池、本机直接内存。我们来了解各个区域发生OOM的原因和解决，自然也就知道了如何进行排查。

1.堆溢出
堆用于储存对象实例，只要不断的创建对象或者对象无法被回收，达到一定程度必然会产生OOM。产生原因可能有2种情况：

1.存在内存泄漏 2.堆分配得到的内存过小       异常信息会进一步提示“Java Heap Space”。

解决方法：2种情况有不同解决方法，因此首先我们需要分清是哪一个原因导致了OOM。通过参数-xx:HeapDumpOutOfMemoryError可以让jvm在OOM时Dump出当前的内存堆快照，利用内存映像分析工具（如Eclipse Memory Analyzer）进行查看。如果是内存泄漏，通过工具查看泄漏对象，定位到对象位置，进行修正。如果不存在泄漏，即就是内存中的对象确实还必须活着，那就应当检查堆参数（-Xmx 和-Xms）是否过小，针对当前机器是否可以调大。

2.栈溢出
针对栈溢出，java虚拟机规范中描述了2中异常

StackOverflowError:线程请求的栈深度大于虚拟机所允许的最大深度。

OutOfMemoryError：虚拟机在扩展栈时无法申请到足够的空间。异常信息会进一步提示“。。。。create thread”

但其实这2种情况描述的是一个事情，即到底是内存太小，还是已经使用的栈空间太大，无法再申请到栈空间。不同的是，OOM在多线程更容易出现。

解决：StackOverflowError：通过-Xss参数增加栈内存容量（指单个栈容量） 。注意是否有太多的本地变量被定义，减少定义。

            OutOfMemoryError：通过减少最大堆（增加总的栈容量）或减少单个栈容量。此处为何说发生了OOM还要减少单个栈容                                               量呢，多线程OOM发生的原因是因为每个线程申请一个栈，减少单个容量来增加可申请栈的总数，                                                以此避免OOM。

3.方法区和运行时常量池溢出
大量的类或者大量的字面量被定义时会导致方法区和运行时常量池OOM，但出现的可能性比堆OOM要低。出现时会进一步提示“PermGen space”。

解决方法：通过-XX:PermSize 和 -XX:MaxPermSize限制方法区大小

4.本机直接内存溢出
程序中直接或者间接的使用了nio，通过DirectByteBuffer请求内存，内存不足时也有可能产生OOM。明显特征是出现OOM时dump下来的文件很小，没有明显的异常信息。

解决方法： 通过-XX：MaxDirectMemorySize可定义直接内存大小。
————————————————
版权声明：本文为CSDN博主「张虎宇」的原创文章，遵循CC 4.0 BY-SA版权协议，转载请附上原文出处链接及本声明。
原文链接：https://blog.csdn.net/make__It/article/details/86522550