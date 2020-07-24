## 两个Synchronized方法，两个线程同时访问？

会有以下几种情况：

1、两个方法都没有synchronized修饰，调用时都可进入：方法A和方法B都没有加synchronized关键字时，调用方法A的时候可进入方法B；

2、一个方法有synchronized修饰，另一个方法没有，调用时都可进入：方法A加synchronized关键字而方法B没有加时，调用方法A的时候可以进入方法B；

3、两个方法都加了synchronized修饰，一个方法执行完才能执行另一个：方法A和方法B都加了synchronized关键字时，调用方法A之后，必须等A执行完成才能进入方法B；

4、两个方法都加了synchronized修饰，其中一个方法加了wait（）方法，调用时都可进入：方法A和方法B都加了synchronized关键字时，且方法A加了wait()方法时，调用方法A的时候可以进入方法B；

5、一个添加了synchronized修饰，一个添加了static修饰，调用时都可进入：方法A加了synchronized关键字，而方法B为static静态方法时，调用方法A的时候可进入方法B；

6、两个方法都是静态方法且还加了synchronized修饰，一个方法执行完才能执行另一个：方法A和方法B都是static静态方法，且都加了synchronized关键字，则调用方法A之后，需要等A执行完成才能进入方法B；

7、两个方法都是静态方法且还加了synchronized修饰，分别在不同线程调用不同的方法，还是需要一个方法执行完才能执行另一个：方法A和方法B都是static静态方法，且都加了synchronized关键字，创建不同的线程分别调用A和B，需要等A执行完成才能执行B（因为static方法是单实例的，A持有的是Class锁，Class锁可以对类的所有对象实例起作用）

 

总结：

同一个object中多个方法都加了synchronized关键字的时候，其中调用任意方法之后需等该方法执行完成才能调用其他方法，即同步的，阻塞的；

此结论同样适用于对于object中使用synchronized(this)同步代码块的场景；

synchronized锁定的都是当前对象！

## 多线程synchronized和synchronized static方法区别?

1. synchronized 与static synchronized锁的范围不同,synchronized锁的是对象，static synchronized锁是class对象，两个互相不受影响。可以同时被访问。
2. synchronized static防止多个线程同时访问某一个方法。上面的例子是防止多个线程同时method3 和method4,即使操作资源的不同，因为锁的是Resource.class。class在真个内存中之后一个。所以多个线程不能同时访问。
3. synchronized锁的是对象。针对同一个实例（同一个对象）,一定是同一个实例，多个线程不能同时访问。如果是不同的实例，那么也可以同时访问，锁的是对象(Resrouce的实例,针对同一个实例的多个线程访问必须遵守顺序)。
   