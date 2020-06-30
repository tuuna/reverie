	假设你的程序中涉及到对一些共享资源的读和写操作，且写操作没有读操作那么频繁。在没有写操作的时候，两个线程同时读一个资源没有任何问题，所以应该允许多个线程能在同时读取共享资源。但是如果有一个线程想去写这些共享资源，就不应该再有其它线程对该资源进行读或写，也就是说：读-读能共存，读-写不能共存，写-写不能共存。这就需要一个读/写锁来解决这个问题。

```
package com.test.test;

/**
 * 用两个int变量实现读写锁
 * @author Think
 *
 */
public class MyReadWriteLock {

    private AtomicInteger readcount = new AtomicInteger(0);
    private AtomicInteger writecount = new AtomicInteger(0);

    public void lockread() throws InterruptedException{
        while(writecount.get() > 0){
            synchronized(this){
                wait();
            }
        }
        readcount.getAndIncrement();
        //进行读取操作
        System.out.println("读操作");
    }

    public void unlockread(){
        readcount.getAndDecrement();
        synchronized(this){
        /*如果有线程在等待获取读锁，同时又有线程在等待获取写锁。如果这时其中一个等待读锁的线程被notify方法唤醒，但因为此时仍有请求写锁的线程存在（writeRequests>0），所以被唤醒的线程会再次进入阻塞状态。然而，等待写锁的线程一个也没被唤醒，就像什么也没发生过一样（译者注：信号丢失现象）。如果用的是notifyAll方法，所有的线程都会被唤醒，然后判断能否获得其请求的锁。用notifyAll还有一个好处。如果有多个读线程在等待读锁且没有线程在等待写锁时，调用unlockWrite()后，所有等待读锁的线程都能立马成功获取读锁 —— 而不是一次只允许一个。*/
            notifyAll();
        }
    }

    public void lockwrite() throws InterruptedException{
        while(writecount.get() > 0){
            synchronized(this){
                wait();
            }
        }
        //之所以在这里先++，是先占一个坑，避免读操作太多，从而产生写的饥饿等待
        writecount.getAndIncrement();
        while(readcount.get() > 0){
            synchronized(this){
                wait();
            }
        }
        //进行写入操作
        System.out.println("写操作");
    }

    public void unlockwrite(){
        writecount.getAndDecrement();
        synchronized(this){
            notifyAll();
        }
    }

    public static void main(String[] args) throws InterruptedException {
        MyReadWriteLock readWriteLock = new MyReadWriteLock();
        for(int i = 0; i < 2; i++){
            Thread2 thread2 = new Thread2(i, readWriteLock);
            thread2.start();
        }

        for (int i = 0; i < 10; i++) {
            Thread1 thread1 = new Thread1(i, readWriteLock);
            thread1.start();
        }

    }

}

class Thread1 extends Thread{
    public int i;
    public MyReadWriteLock readWriteLock;

    public Thread1(int i, MyReadWriteLock readWriteLock) {
        this.i = i;
        this.readWriteLock = readWriteLock;
    }

    @Override
    public void run() {
        try {
            readWriteLock.lockread();
            Thread.sleep(1000);//模拟耗时
            System.out.println("第"+i+"个读任务");
        } catch (InterruptedException e) {
            e.printStackTrace();
        } finally {
            readWriteLock.unlockread();
        }
    }
}


class Thread2 extends Thread{
    public int i;
    public MyReadWriteLock readWriteLock;

    public Thread2(int i, MyReadWriteLock readWriteLock) {
        this.i = i;
        this.readWriteLock = readWriteLock;
    }

    @Override
    public void run() {
        try {
            readWriteLock.lockwrite();
            Thread.sleep(1000);
            System.out.println("第"+i+"个写任务");
        } catch (InterruptedException e) {
            e.printStackTrace();
        } finally {
            readWriteLock.unlockwrite();
        }
    }
```

>
>
>从字面上意思来说，notify是唤醒一个处于该对象wait的线程
>而notifyAll是唤醒所有处于该对象wait的线程。
>但是唤醒不等于就能执行了，需要得到锁对象才能有权利继续执行，而锁只有一把，所以多个线程被唤醒时需要争取该锁。
>那你也许会想，既然只有一把锁，那我每次用notify唤醒一个不就行了，这样都省的争了，notifyAll还有存在的意义吗？
>
>
>
>事实并不是这样的，这里我就直接用知乎的回答来阐述这个问题。大家一起学习。
>
>> 先说两个概念：锁池和等待池
>>
>> - 锁池:假设线程A已经拥有了某个对象(注意:不是类)的锁，而其它的线程想要调用这个对象的某个synchronized方法(或者synchronized块)，由于这些线程在进入对象的synchronized方法之前必须先获得该对象的锁的拥有权，但是该对象的锁目前正被线程A拥有，所以这些线程就进入了该对象的锁池中。
>> - 等待池:假设一个线程A调用了某个对象的wait()方法，线程A就会释放该对象的锁后，进入到了该对象的等待池中
>>   [Reference：java中的锁池和等待池](http://blog.csdn.net/emailed/article/details/4689220)
>>   然后再来说notify和notifyAll的区别
>> - 如果线程调用了对象的 wait()方法，那么线程便会处于该对象的等待池中，等待池中的线程不会去竞争该对象的锁。
>> - 当有线程调用了对象的 notifyAll()方法（唤醒所有 wait 线程）或 notify()方法（只随机唤醒一个 wait 线程），被唤醒的的线程便会进入该对象的锁池中，锁池中的线程会去竞争该对象锁。也就是说，调用了notify后只要一个线程会由等待池进入锁池，而notifyAll会将该对象等待池内的所有线程移动到锁池中，等待锁竞争
>> - 优先级高的线程竞争到对象锁的概率大，假若某线程没有竞争到该对象锁，它还会留在锁池中，唯有线程再次调用 wait()方法，它才会重新回到等待池中。而竞争到对象锁的线程则继续往下执行，直到执行完了 synchronized 代码块，它会释放掉该对象锁，这时锁池中的线程会继续竞争该对象锁。
>>   [Reference：线程间协作：wait、notify、notifyAll](http://wiki.jikexueyuan.com/project/java-concurrency/collaboration-between-threads.html)
>>   综上，所谓唤醒线程，另一种解释可以说是将线程由等待池移动到锁池，notifyAll调用后，会将全部线程由等待池移到锁池，然后参与锁的竞争，竞争成功则继续执行，如果不成功则留在锁池等待锁被释放后再次参与竞争。而notify只会唤醒一个线程。
>>   有了这些理论基础，后面的notify可能会导致**死锁**，而notifyAll则不会的例子也就好解释了
>
>notify会产生死锁？可能你无法理解为什么，就用程序来表达吧。
>
>```
>package lock;
>
>import java.util.concurrent.TimeUnit;
>
>public class TestNotifyAndNotifyAll {
>
>       public static void main(String[] args) {
>           Object co = new Object();
>           System.out.println(co);
>
>           for (int i = 0; i < 5; i++) {
>               MyThread t = new MyThread("Thread" + i, co);
>               t.start();
>           }
>
>           try {
>               TimeUnit.SECONDS.sleep(2);
>               System.out.println("-----Main Thread notify-----");
>               synchronized (co) {
>                   co.notify();
>               }
>
>               TimeUnit.SECONDS.sleep(2);
>               System.out.println("Main Thread is end.");
>
>           } catch (InterruptedException e) {
>               e.printStackTrace();
>           }
>       }
>
>       static class MyThread extends Thread {
>           private String name;
>           private Object co;
>
>           public MyThread(String name, Object o) {
>               this.name = name;
>               this.co = o;
>           }
>
>           @Override
>           public void run() {
>               System.out.println(name + " is waiting.");
>               try {
>                   synchronized (co) {
>                       co.wait();
>                       System.out.println(name +"get lock again");
>                   }
>                   System.out.println(name + " has been notified.");
>               } catch (InterruptedException e) {
>                   e.printStackTrace();
>               }
>           }
>       }
>}
>```
>
>*运行该程序会发生死锁，因为main线程中途只唤醒了一个线程，而当那个线程运行完之后就释放了锁，却没有唤醒其余等待的线程进入锁池竞争锁。所以就
>造成了该现象。解决方法就是讲main线程中的notify改为notifyAll或者在MyThread的run方法中在退出同步块之前进行notify()
>**
>notifyAll should be used most of the time. If you are not sure which to use, then use notifyAll.Please see explanation that follows.
>大部分情况下都用notifyAll即可，如果你实在不确定用哪个，那就用notifyAll。