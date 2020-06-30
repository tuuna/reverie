​	假设你的程序中涉及到对一些共享资源的读和写操作，且写操作没有读操作那么频繁。在没有写操作的时候，两个线程同时读一个资源没有任何问题，所以应该允许多个线程能在同时读取共享资源。但是如果有一个线程想去写这些共享资源，就不应该再有其它线程对该资源进行读或写，也就是说：读-读能共存，读-写不能共存，写-写不能共存。这就需要一个读/写锁来解决这个问题。

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