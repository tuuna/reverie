# 1. 前言

HashMap作为java数据结构中重要的一员，同时拥有高效的查询和插入的优点。近期在做项目的时候我也是领略到了hashmap的强大之处，hashmap为前后台数据的交互提供了非常高的可扩展性和灵活性。键值对的数据结构也天然对json字符串的转换提供良好的支持。这篇文章主要分析HashMap的原理，以及如何自己实现一个HashMap。

# 2. HashMap的原理

## 2.1 什么是HashMap？

基于哈希表的 Map 接口的实现。此实现提供所有可选的映射操作，并允许使用 null 值和 null 键。（除了非同步和允许使用 null 之外，HashMap 类与 Hashtable 大致相同。）此类不保证映射的顺序，特别是它不保证该顺序恒久不变 ——摘自百度百科。

## 2.2 什么是Hash

Hash是一种算法，能将一个任意长度的二进制值通过一个映射关系转换成一个固定长度的二进制值。在HashMap中，哈希算法主要是用于根据key的值算出存放数组的index值。HashMap中的存储都是根据key的哈希值有关的。
哈希算法又被称为散列的过程，那么什么是散列呢？散列即把数据均匀的存放到数组中的各个位置，从而尽量避免出现多个数据存放在一块区间内。

优秀的哈希算法应该具备以下两点：

- 保证散列值非常均匀
- 保证冲突极少出现

哈希函数有很多种，在这里我以除留取余法（取模）为例，首先定义一个数组的长度，假设为16。那么此时的索引为key摸于m，m的取值规则是比数组长度小的最大质数。在这个情况下m为13。由于这种算法会导致这样的情况出现，即不同的key经过哈希运算之后得到了一样的index，如key为2和15的index值都为2，那么此时就需要我们处理冲突了。

Ps. JDK中的hash算法远比这个复杂，在这里我仅仅只是举一个例子方便读者理解。

## 2.3 处理冲突

- 线性探测法
  当冲突产生时，查找下一个索引是否被占用，如果没有，则把数据存到该索引上。
- 链表形式
  由于在HashMap中，单个数据是以entry的形式存储的，而entry中包含了key，value和next指针。那么当冲突产生时，我们就把原先存放到这个位置的数据取出来，然后在这个位置存放新的数据，并且把新数据的next指针设为原数据，也就是说链表头位置的数据永远是最新的数据。

# 3. 实现自己的HashMap

## 3.1 Map接口

HashMap的顶层接口是Map，那么我们自己实现的Map也需要一个接口，在这里我定义接口的名称为MyMap。这个接口中应该含有的方法包括：

- put(K k,V v)
- get(K k)
- size()

和一个内部接口

- Entry

这个内部接口中包含两个方法

- getKey()
- getValue()

代码如下

```
public interface MyMap<K,V> {
    /**
     * put方法
     * @param k
     * @param v
     * @return
     */
    V put(K k, V v);
    /**
     * get方法
     * @param k
     * @return
     */
    V get(K k);

    int size();
    /**
     * Entry内部接口
     * @param <K>
     * @param <V>
     */
    interface Entry<K, V>{
        /**
         * 根据entry对象获取key值
         * @return
         */
        K getKey();
        /**
         * 根据entry对象获取value值
         * @return
         */
        V getValue();

    }
}
```



## 3.2 内部类Entry的实现

接口设计完毕之后，我们需要创建一个类来实现这个接口的方法。我创建了一个名为MyHashMap的类实现MyMap接口。

这里我们需要一个内部类来实现MyMap的内部接口，内部类的实例对象即数组中存储的entry对象，所以我们需要定义三个成员变量，分别是K，V和Next。next的类型就是entry本身，因为它指向的是下一个entry对象。

内部类代码如下

```
class Entry<K, V> implements  MyMap.Entry<K, V> {

        K k;
        
        V v;
        
        Entry<K, V> next;
        
        public Entry(K k, V v, Entry next) {
            this.k = k;
            this.v = v;
            this.next = next;
        }

        @Override
        public K getKey() {
            return k;
        }

        @Override
        public V getValue() {
            return v;
        }
    }
```



## 3.3 定义成员变量

HashMap中含有以下几个成员变量：

- 默认数组长度
- 默认负载因子
- Entry数组
- HashMap的大小

```
//默认数组大小，初始大小为16
private static int defaultLength = 16;
//默认负载因子，为0.75
private static double defaultLoader = 0.75;
//Entry数组
private Entry<K, V>[] table = null;
//HashMap的大小
private int size = 0;
```

## 3.4 定义构造方法

在HashMap中默认数组长度和默认负载因子都是可以自定义的，那么我们定义一个可以自定义数组长度和负载因子的构造方法。

```
 /**
 * 自定义默认长度和负载因子
 * @param length
 * @param loader
 */
public MyHashMap(int length, double loader) {
    defaultLength = length;
    defaultLoader = loader;
    //初始化数组
    table = new Entry[defaultLength];
}
```



如果不指定数组长度和负载因子则使用默认值

```
/**
 * 使用默认值
 */
public MyHashMap() {
    this(defaultLength, defaultLoader);
}
```



## 3.5定义哈希函数

上文已经提过了，哈希函数我们使用除留取余法。定义一个整型m，m的取值应该是一个比数组长度小的最大质数，为了简化算法我取数组的长度作为m的值。以key的哈希值模于m，得到index的值并且返回。

```
 /**
 * 自定义哈希算法
 * 根据key的哈希值得到一个index索引，即存放到数组中的下标
 * @param k
 * @return
 */
private int getKey(K k) {
    int m = defaultLength;
    int index = k.hashCode() % m;
    return index >= 0 ? index : -index;
}
```

最后返回的时候用了一个三元运算符，是为了要确保index的值必须是一个正数。

## 3.6 实现put方法

首先，我们需要通过哈希算法得到数组的下标，然后把一个包含键值对以及next指针的entry对象存到该位置中。

在存入数组之前我们需要判断当前索引中是否已经存在数据。根据不同情况，做出不同的存储处理，代码中的注释有详细的解释。

```
public V put(K k, V v) {
        //根据key和哈希算法算出数组下标
        int index = getKey(k);
        Entry<K, V> entry = table[index];
        //判断entry是否为空
        if (entry == null) {
            /*
            * 如果entry为空，则代表当前位置没有数据。
            * new一个entry对象，内部存放key，value。
            * 此时next指针没有值，因为这个位置上只有一个entry对象
            * */
            table[index] = new Entry(k, v, null);
            //map的大小加1
            size++;
        } else {
            /*
            * 如果entry不为空，则代表当前位置已经有数据了
            * new一个entry对象，内部存放key，value。
            * 把next指针设置为原本的entry对象并且把数组中的数据替换为新的entry对象
            * */
            table[index] = new Entry<K, V>(k, v, entry);
        }
        return table[index].getValue();
    }
```

## 3.7 实现HashMap的扩容

HashMap的扩容机制是：当map的大小大于默认长度*默认负载因子，那么数组的长度会翻倍，数组中的数据会重新散列然后再存放。那么在原先的put方法中，需要先判断是否达到扩容的标准在进行执行下面的代码。如果达到扩容的标准则需要调用扩容的方法。代码如下：

```
//数组的扩容
private void expand() {
    //创建一个大小是原来两倍的entry数组
    Entry<K, V>[] newTable = new Entry[2 * defaultLength];
    //重新散列
    rehash(newTable);
}
//重新散列的过程
private void rehash(Entry<K,V>[] newTable) {
    //创建一个list用于装载HashMap中所有的entry对象
    List<Entry<K, V>> list = new ArrayList<Entry<K, V>>();

    //遍历整个数组
    for(int i=0; i<table.length;i++) {
        //如果数组中的某个位置没有数据，则跳过
        if (table[i] == null) {
            continue;
        }
        //通过递归的方式将所有的entry对象装载到list中
        findEntryByNext(table[i],list);
        if (list.size() > 0) {
            //把size重置
            size = 0;
            //把默认长度设置为原来的两倍
            defaultLength = 2 * defaultLength;

            table = newTable;
            for (Entry<K, V> entry : list) {
                if (entry.next != null) {
                    //把所有entry的next指针置空
                    entry.next = null;
                }
                //对新table进行散列
                put(entry.getKey(), entry.getValue());
            }
        }
    }
}

private void findEntryByNext(Entry<K, V> entry,List<Entry<K, V>> list ) {
    if (entry != null && entry.next != null) {
        list.add(entry);
        //递归调用
        findEntryByNext(entry.next,list);
    }else {
        list.add(entry);
    }
}
```



因为在put方法中加上了对扩容标准的判断，原先的put方法需要修改

```
public V put(K k, V v) {
    //判断size是否达到扩容的标准
    if (size >= defaultLength * defaultLoader) {
        expand();
    }

    //根据key和哈希算法算出数组下标
    int index = getKey(k);
    Entry<K, V> entry = table[index];
    //判断entry是否为空
    if (entry == null) {
        /*
        * 如果entry为空，则代表当前位置没有数据。
        * new一个entry对象，内部存放key，value。
        * 此时next指针没有值，因为这个位置上只有一个entry对象
        * */
        table[index] = new Entry(k, v, null);
        size++;

    } else {
        /*
        * 如果entry不为空，则代表当前位置已经有数据了
        * new一个entry对象，内部存放key，value。
        * 把next指针设置为原本的entry对象并且把数组中的数据替换为新的entry对象
        * */
        table[index] = new Entry<K, V>(k, v, entry);
    }
    return table[index].getValue();
}
```



## 3.8 实现get方法

上文提到了，由于hash算法可能会导致相同的索引中包含了不同的entry对象，我们需要通过对比key值的方式来找到我们真正要的那个entry对象。代码如下：

```
public V get(K k) {
    //获取此key对应的entry对象所存放的索引index
    int index = getKey(k);
    //非空判断
    if (table[index] == null) {
        return null;
    }
    //调用方法找到真正的value值并返回。
    return findValueByEqualKey(k,table[index]);
}

 /**
 *
 * 通过递归比较key值的方式找到真正我们要找的value值
 * @param k
 * @param entry
 * @return
 */
public V findValueByEqualKey(K k , Entry<K,V> entry) {

    /*
    * 如果传进来的key等于这个entry的key值，说明这个就是我们要找的entry对象
    * 那么直接返回这个entry的value
    * */
    if (k == entry.getKey() || k.equals(entry.getKey())) {
        return entry.getValue();
    } else {
        /*
        * 如果不相等，说明这个不是我们要找的entry对象，
        * 通过递归的方式去比较它的next指针中的entry的key值，来找到真正的entry对象
        * */
        if (entry.next != null) {
            return findValueByEqualKey(k, entry.next);
        }

    }
    return entry.getValue();
}
```



由于size方法非常简单，直接返回size即可，我直接贴一下代码

```
//返回HashMap的大小
public int size() {
    return size;
}
```



到了这里，HashMap的基本功能已经都写完了，包括put方法、get方法和扩容机制。那么下面我们来测试一下看看自己写的map能否实现功能。

# 4. 测试MyHashMap

写一个测试类，对比一下我们的HashMap和jdk自带的HashMap，看看输出是否一样。

测试类代码

```
/**
 * @Author: Yiheng Chen
 * @Description: Map测试类
 * @Date: Created in 4:33 2017/9/1
 * @Modified by:
 */
public class MyMapTest {
    public static void main(String[] args) {

        MyHashMap<String,String > map = new MyHashMap();

        Long t1 = System.currentTimeMillis();
        for (int i=0; i<1000;i++) {
            map.put("key" + i, "value" + i);
        }

        for (int i = 0; i < 1000; i++) {
            System.out.println("key: " + "key" + i +"---"+ "value: " + map.get("key" + i));
        }
        Long t2 = System.currentTimeMillis();

        System.out.println("MyHashMap耗时："+(t2-t1));
        System.out.println("-------------------HashMap-------------------------" );

        Map<String,String > hashMap = new HashMap();

        Long t3 = System.currentTimeMillis();
        for (int i=0; i<1000;i++) {
            hashMap.put("key" + i, "value" + i);
        }

        for (int i = 0; i < 1000; i++) {
            System.out.println("key: " + "key" + i + "---"+ "value: " + hashMap.get("key" + i));
        }
        Long t4 = System.currentTimeMillis();
        System.out.println("JDK的HashMap耗时："+(t4-t3));

    }
}
```

部分结果的截取：

```
key: key980---value: value980
key: key981---value: value981
key: key982---value: value982
key: key983---value: value983
key: key984---value: value984
key: key985---value: value985
key: key986---value: value986
key: key987---value: value987
key: key988---value: value988
key: key989---value: value989
key: key990---value: value990
key: key991---value: value991
key: key992---value: value992
key: key993---value: value993
key: key994---value: value994
key: key995---value: value995
key: key996---value: value996
key: key997---value: value997
key: key998---value: value998
key: key999---value: value999
```



可以发现MyHashMap的输出和JDK的HashMap是完全一样的，那么说明了这个自己实现的HashMap功能已经基本实现。

然后我又在这个基础上测试了一下耗时，结果如下：

```
MyHashMap耗时：30
-----------------------
JDK的HashMap耗时：19
```



性能上自然是JDK的HashMap更强大，这个毋庸置疑，JDK中的HashMap可以说是最优化的map实现了，我们自己实现的肯定不如人家的。但是这个性能已经是不错的了。

至此，我们已经完整的实现了一个自己的HashMap，希望这篇文章对你有帮助。