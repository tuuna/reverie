《阿里巴巴JAVA开发手册》中有这样一条：

> 不要在 foreach 循环里进行元素的 add / remove 操作，remove 元素使用 Iterator 方式。

经测试，当在 foreach 循环中 add / remove 集合元素，可能会抛出 ConcurrentModificationException 异常，下面介绍进行详细说明。

### 1. foreach循环

foreach 又称为增强型for循环，通过打印以下代码的class字节码，我们来了解下其内部实现。



```java
List<String> list = new ArrayList<> ();
list.add("a");
list.add("b");

for (String item : list) {
    System.out.println(item);
}
```



foreach 循环内部实际是通过 Iterator 实现的，以上代码等同于：



```java
List<String> list = new ArrayList<> ();
list.add("a");
list.add("b");

for (Iterator<String> i = list.iterator(); i.hasNext(); ) {
    String item = i.next();
    System.out.println(item);
}
```

实际上，foreach 循环仅是 Java 提供的语法糖。编译器隐藏了对 Iterator 的使用，使得 foreach 在语法上较传统 for 循环更加简洁，也不容易出错。下面我们看下 Iterator.

### 2. Iterator

Iterator 接口包含以下几个主要方法：



```java
boolean hasNext();  // 检查是否有下个元素
E next();           // 获取下个元素
void remove();      // 移除当前指向的元素
```

在Java集合框架中，各集合内部都实现了 Iterator 接口，用以对集合元素进行遍历、修改。我们看下 ArrayList 内部的 Iterator 实现。

### 3. ArrayList$Itr

ArrayList 的内部类 Itr 实现了 Iterator 接口，Itr 共有3个成员变量：



```java
 private class Itr implements Iterator<E> {
    int cursor;                      // 下一次遍历的元素的位置
    int lastRet = -1;                // 前一次返回的元素的位置
    int expectedModCount = modCount;
```

值得注意的是 expectedModCount 这个变量，其初始值为 modCount.

#### 3.1 modCount

modCount 是 ArrayList 继承自 AbstractList 的一个变量。在AbstractList的源码注释中，是这样解释这个变量的：

> The number of times this list has been structurally modified. Structural modifications are those that change the size of the list.

翻译成中文大意为：modCount 为 list 的结构变化次数，即 list 的元素数量变化次数。
 查看 ArrayList 的源码，会发现在每次调用 **add()** 和 **remove()** 方法，都会进行 modCount++ 操作。

#### 3.2 expectedModCount

modCount 意为 list 的结构变化次数，而 expectedModCount 可被视为 Itr 内部记录的集合结构变化次数，那么该变量有何作用呢？
 在 Itr 内部有一个 checkForComodification 方法，如下所示：



```java
final void checkForComodification() {
    if (modCount != expectedModCount)
        throw new ConcurrentModificationException();
}
```

当集合的实际结构变化次数 和 Itr 记录的变化次数不相等时，则抛出文章开头提到的 **ConcurrentModificationException** 异常。而在 Itr 的 **next()** 方法 和 **remove()** 中都调用了 **checkForComodification** 方法。

### 4. 结论

由上文，我们可以得出为何不能在 foreach 循环中 add/remove 元素的结论。



```java
List<String> list = new ArrayList<>();
list.add("1");
list.add("2");

for (String item : list) {
    if (item.equals("2")) {
        list.remove(item);
    }
}
```

相当于：



```java
List<String> list = new ArrayList<>();
list.add("1");
list.add("2");

for (Iterator<String> iterator = list.iterator(); iterator.hasNext(); ) {
    String item = iterator.next();
    if (item.equals("2")) {
        list.remove(item);
    }
}
```

1. 在进行了 2 次 add 元素后，ArrayList 内部的 modCount = 2；
2. 在进行 foreach 循环时，隐式调用的 Itr 内部 expectedModCount 同样初始化为 2；
3. 在 foreach 循环中 remove 元素时，由于调用的是 list 的 remove 方法，会使 modCount + 1 = 3.
4. 调用 Iterator.next() 方法获取下个元素前，iterator 检查到 modCount != expectedModCount，抛出 ConcurrentModificationException 异常。

#### 4.1 为什么使用 Iterator 的 remove 方法操作集合元素不会有问题？

同样我们看下 ArrayList 内部 Itr 的 remove 方法的源码：



```java
public void remove() {
    if (lastRet < 0)
        throw new IllegalStateException();
    checkForComodification();

    try {
        ArrayList.this.remove(lastRet);  // 调用集合的remove()方法
        cursor = lastRet;
        lastRet = -1;
        expectedModCount = modCount;    // 更新expectedModCount
    } catch (IndexOutOfBoundsException ex) {
        throw new ConcurrentModificationException();
    }
}
```

实际上，调用 Itr 的 remove() 方法移除集合元素时，首先会调用 ArrayList 的 remove() 方法，再对 expectedModCount 进行更新。在下次调用 Itr.next() 方法获取下个元素时，不会出现 expectedModCount != modCount 的情况。

#### 4.2  Iterator 为什么要检查集合的结构变化次数?

这其实是为了防止多线程并发修改集合，在一个线程遍历集合的同时，另一个线程同时增删集合元素，将无法保证数据的一致性，集合的遍历过程也将被打乱。采用 modCount 机制，在此情景下及时抛出异常，确保同一时间只会有一个线程修改或遍历集合，也即 fail-fast 策略。

