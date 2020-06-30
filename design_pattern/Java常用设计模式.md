## **一、简单工厂**

又叫做静态工厂方法（StaticFactory Method）模式，但不属于23种GOF设计模式之一。

简单工厂模式的实质是由一个工厂类根据传入的参数，动态决定应该创建哪一个产品类。

Spring中的BeanFactory就是简单工厂模式的体现，根据传入一个唯一的标识来获得Bean对象，但是否是在传入参数后创建还是传入参数前创建这个要根据具体情况来定。

> ### **简单工厂模式：SimpleFactory**
>
> - 实现方式：BeanFactory。Spring中的BeanFactory就是简单工厂模式的体现，根据传入一个唯一的标识来获得Bean对象，但是否是在传入参数后创建还是传入参数前创建这个要根据具体情况来定。
> - 实质：由一个工厂类根据传入的参数，动态决定应该创建哪一个产品类。
>
> ### **BeanFactory实现原理：**
>
> **bean容器的启动阶段：**
>
> - 读取bean的xml配置文件,将bean元素分别转换成一个BeanDefinition对象。
> - 然后通过BeanDefinitionRegistry将这些bean注册到beanFactory中，保存在它的一个ConcurrentHashMap中。
> - 将BeanDefinition注册到了beanFactory之后，在这里Spring为我们提供了一个扩展的切口，允许我们通过实现接口BeanFactoryPostProcessor 在此处来插入我们定义的代码。典型的例子就是：PropertyPlaceholderConfigurer，我们一般在配置数据库的dataSource时使用到的占位符的值，就是它注入进去的。
>
> **容器中bean的实例化阶段：**
>
> 实例化阶段主要是通过反射或者CGLIB对bean进行实例化，在这个阶段Spring又给我们暴露了很多的扩展点：
>
> - **各种的Aware接口** ，比如 BeanFactoryAware，对于实现了这些Aware接口的bean，在实例化bean时Spring会帮我们注入对应的BeanFactory的实例。
> - **BeanPostProcessor接口** ，实现了BeanPostProcessor接口的bean，在实例化bean时Spring会帮我们调用接口中的方法。
> - **InitializingBean接口** ，实现了InitializingBean接口的bean，在实例化bean时Spring会帮我们调用接口中的方法。
> - **DisposableBean接口** ，实现了BeanPostProcessor接口的bean，在该bean死亡时Spring会帮我们调用接口中的方法。

## **二、工厂方法（Factory Method）**

定义一个用于创建对象的接口，让子类决定实例化哪一个类。Factory Method使一个类的实例化延迟到其子类。

> 简单工厂模式的最大优点在于工厂类中包含了必要的逻辑判断，根据客户端的选择条件动态实例化相关的类。但是违背了开放-封闭原则。而工厂方法模式定义了一个用于创建对象的接口，让字类决定实例化哪一个类。使一个类的实例化延迟到子类。(通过加子类来拓展功能)
>
> [BeanFactory和FactoryBean区别](https://www.jianshu.com/p/05c909c9beb0)

Spring中的FactoryBean就是典型的工厂方法模式。如下图：

![img](https://pic4.zhimg.com/80/v2-874cc3faa5aec288cb96dfd580bd0dbf_1440w.jpg)

## **三、单例（Singleton）**

保证一个类仅有一个实例，并提供一个访问它的全局访问点。

Spring中的单例模式完成了后半句话，即提供了全局的访问点BeanFactory。但没有从构造器级别去控制单例，这是因为Spring管理的是是任意的Java对象。

注意两点

- 当instance为null时，进入同步代码块，同时该判断避免了每次都需要进入同步代码块，可以提高效率(double check的作用)
- 在多线程的情况下，可能会引起空指针异常，这是因为如果在如上代码的构造方法中还存在初始化其他资源的情况的话，由于JVM运行时存在指令重排的情况，这些资源在实例化时顺序并无前后关系的约束，那么在这种情况下，就极有可能是instance最先被实例化，而con和socket并未完成实例化，而未完成实例化的实例在调用其方法时将会抛出空指针异常。使用volatile来防止重排序(volatile的作用)

> **1、饿汉式**
>
> 饿汉式是单例模式设计中比较经典的实现方式。实现代码如下：
>
> ```java
> //final不允许被继承
> public final class SingleTonEhangshi {
>     //实例变量
>     private byte[] data = new byte[1024];
> 
>     //在定义实例对象时直接初始化
>     private static SingleTonEhangshi instance = new SingleTonEhangshi();
> 
>     //私有化构造函数，不允许外部NEW
>     private SingleTonEhangshi() {
> 
>     }
> 
>     public static SingleTonEhangshi getInstance() {
>         return instance;
>     }
> }
> ```
>
> 饿汉式的实现关键在于instance作为类变量直接得到了初始化，如果我们主动使用SingleToEhangshi类，那么instance实例将会直接完成创建，包括其中的实例变量也都会得到初始化。
>
> instance作为类变量，在类初始化的过程中会被收集进<clinit>()方法中，而该方法是可以100%地保证同步，也就是说instance在多线程的情况下不可能被初始化两次。但是由于instance被ClassLoader加载后很长一段时间才被使用的话，那就会意味着instance实例所开辟的堆内存会驻留很长的时间。
>
> 总体说来，如果一个类中的成员变量比较少，且占用的内存资源也不多，用饿汉式的方式实现单例模式也未尝不可，只是其无法进行懒加载。
>
> **2、懒汉式**
>
> 所谓懒汉式就是在使用类实例的时候再去创建，也就是说用到的时候我再创建，这样就可以避免类在初始化的时候提前创建过早地占用内存空间。实现代码如下：
>
> ```java
> //final不允许被继承
> public final class SingleTonLhangshi {
>     //实例变量
>     private byte[] data = new byte[1024];
> 
>     //定义实例，但是不直接初始化
>     private static SingleTonLhangshi instance = null;
> 
>     //私有化构造函数，不允许外部NEW
>     private SingleTonLhangshi() {
> 
>     }
> 
>     public static SingleTonLhangshi getInstance() {
>         if (null == instance) {
>             instance = new SingleTonLhangshi();
>         }
>         return instance;
>     }
> }
> ```
>
> 类变量instance=null,因此当类被初始化的时候instance并不会立刻被实例化，而是在getInstance()方法被调用时判断instance实例是否被实例化，如果没有实例化在调用私有构造方法进行实例化操作。
>
> 懒汉式写法在多线程环境下，会存在同一时间多个线程同时看到null==instance的情况，从而导致instance会被实例化多次，从而无法保证单例的唯一性。
>
> **3、懒汉式＋同步方法**
>
> 懒汉式的单例实现方式可以保证实例的懒加载，但是却无法保证实例的唯一性。在多线程环境下由于instance为共享数据，当多个线程访问使用时，需要保证数据的同步性，所以如果需要保证懒汉式实例的唯一性，我们可以通过同步的方式来实现。代码如下：
>
> ```java
> /final不允许被继承
> public final class SingleTonLhangshiSync {
>     //实例变量
>     private byte[] data = new byte[1024];
> 
>     //定义实例，但是不直接初始化
>     private static SingleTonLhangshiSync instance = null;
> 
>     //私有化构造函数，不允许外部NEW
>     private SingleTonLhangshiSync() {
> 
>     }
> 
>     //向getInstance方法加入同步控制，每次只能有一个线程能够进入
>     public static synchronized SingleTonLhangshiSync getInstance() {
>         if (null == instance) {
>             instance = new SingleTonLhangshiSync();
>         }
>         return instance;
>     }
> }
> ```
>
> 采用懒汉式＋数据同步的方法既满足了懒加载又能够100%保证instance实例的唯一性。但是，synchronized关键字的排它性会导致getInstance()方法同一时刻只能被一个线程访问，性能会比较低下。
>
> **4、Double-Check**
>
> Double-Check是一种比较聪明的设计方式，它提供了一种高效的数据同步策略，那就是首次初始化的时候加锁，之后则允许多个线程同时进行getInstance()方法的调用来获得类的实例。代码如下：
>
> ```java
> //final不允许被继承
> public final class SingletonDoubleCheck {
>     //实例变量
>     private byte[] data = new byte[1024];
> 
>     //定义实例，但是不直接初始化
>     private static SingletonDoubleCheck instance = null;
> 
>     Connection con;
>     Socket socket;
> 
>     //私有化构造函数，不允许外部NEW
>     private SingletonDoubleCheck() {
>         this.con = con;//初始化
>         this.socket = socket;//初始化
> 
>     }
>     public static SingletonDoubleCheck getInstance() {
>         //当instance为null时，进入同步代码块，同时该判断避免了每次都需要进入同步代码块，可以提高效率
>         if (null == instance) {
>             //只有一个线程能够获得SingletonDoubleCheck.class关联的monitor
>             synchronized (SingletonDoubleCheck.class) {
>                 //判断如果instance为null则创建
>                 if (null == instance) {
>                     instance = new SingletonDoubleCheck();
>                 }
>             }
>         }
>         return instance;
>     }
> }
> ```
>
> 当两个线程发现null==instance成立时，只有一个线程有资格进入同步代码块，完成对instance的初始化，随后的线程发现null==instance不成立则无须进行任何操作，以后对getInstance的访问就不会再需要进行数据同步了。
>
> 此种方式看起来是既满足了懒加载，又保证了instance实例的唯一性，并且还提供了比较高效的数据同步策略，可以允许多个线程同时对getInstance进行访问。但是这种方式在多线程的情况下，可能会引起空指针异常，这是因为如果在如上代码的构造方法中还存在初始化其他资源的情况的话，由于JVM运行时存在指令重排的情况，这些资源在实例化时顺序并无前后关系的约束，那么在这种情况下，就极有可能是instance最先被实例化，而con和socket并未完成实例化，而未完成实例化的实例在调用其方法时将会抛出空指针异常。
>
> **5、Volatile+Double-Check**
>
> 为了解决Double-Check指令重排导致的空指针问题，可以用volatile关键字防止这种重排序的发生。因此代码只需要稍作修改就能满足多线程下的单例、懒加载以及实例的高效性了。代码如下：
>
> ```java
> //final不允许被继承
> public final class SingletonDoubleCheck {
>     //实例变量
>     private byte[] data = new byte[1024];
> 
>     //定义实例，但是不直接初始化
>     private static volatile SingletonDoubleCheck instance = null;
> 
>     Connection con;
>     Socket socket;
> 
>     //私有化构造函数，不允许外部NEW
>     private SingletonDoubleCheck() {
>         this.con = con;//初始化
>         this.socket = socket;//初始化
> 
>     }
> 
>     public static SingletonDoubleCheck getInstance() {
>         //当instance为null时，进入同步代码块，同时该判断避免了每次都需要进入同步代码块，可以提高效率
>         if (null == instance) {
>             //只有一个线程能够获得SingletonDoubleCheck.class关联的monitor
>             synchronized (SingletonDoubleCheck.class) {
>                 //判断如果instance为null则创建
>                 if (null == instance) {
>                     instance = new SingletonDoubleCheck();
>                 }
>             }
>         }
>         return instance;
>     }
> }
> ```
>
> **6、Holder方式**
>
> Holder方式完全借助了类加载的特点。代码如下：
>
> ```java
> //不允许被继承
> public final class SingletonHolder {
>     //实例变量
>     private byte[] data = new byte[1024];
> 
>     private SingletonHolder() {
> 
>     }
> 
>     //在静态内部类中持有单例类的实例，并且可直接被初始化
>     private static class Holder {
>         private static SingletonHolder instance = new SingletonHolder();
>     }
> 
>     //调用getInstance方法，事实上是获得Holder的instance静态属性
>     public static SingletonHolder getInstance() {
>         return Holder.instance;
>     }
> }
> ```
>
> 在单例类中并没有instance的静态成员，而是将其放到了静态内部类Holder之中，因此单例类在初始化的过程中并不会创建SingletonHolder的实例，内部类Holder中定义了SingletonHolder的静态变量，并且直接进行了实例化，只有当Holder被主动引用的时候才会创建SingletonHolder的实例。
>
> SingletonHolder实例的创建过程在Java程序编译时期收集至<clinit>()方法中，该方法又是同步方法，可以保证内存的可见性、JVM指令的顺序性和原子性。Holder方式的单例模式设计是最好的设计之一，也是目前使用比较广的设计。

## **四、适配器（Adapter）**

将一个类的接口转换成客户希望的另外一个接口。Adapter模式使得原本由于接口不兼容而不能一起工作的那些类可以一起工作。

Spring中在对于AOP的处理中有Adapter模式的例子，见如下图：

![img](https://pic2.zhimg.com/80/v2-2da1ef08adf02f6bba1d2c4337da6755_1440w.jpg)



由于Advisor链需要的是MethodInterceptor（拦截器）对象，所以每一个Advisor中的Advice都要适配成对应的MethodInterceptor对象。

> `类适配器与对象适配器的使用场景一致`，仅仅是实现手段稍有区别，二者主要用于如下场景：
>
> - 想要使用一个已经存在的类，但是它却`不符合现有的接口规范`，导致`无法直接去访问`，这时`创建一个适配器就能间接去访问这个类中的方法`。
> - 我们有一个类，想将其设计为可重用的类（可被多处访问），我们可以创建适配器来将这个类来适配其他没有提供合适接口的类。
>
> 以上两个场景其实就是从两个角度来描述一类问题，那就是要访问的方法不在合适的接口里，一个从接口出发（被访问），一个从访问出发（主动访问）。
>
> `接口适配器使用场景`：
>
> - 想要使用接口中的某个或某些方法，但是`接口中有太多方法`，我们要使用时必须实现接口并实现其中的所有方法，可以使用`抽象类来实现接口`，并不对方法进行实现（仅置空），然后我们再继承这个抽象类来通过重写想用的方法的方式来实现。这个抽象类就是适配器。

这个模式不是特别明白为什么AOP用到了这个

## **五、包装器（Decorator）**

动态地给一个对象添加一些额外的职责。就增加功能来说，Decorator模式相比生成子类更为灵活。



![img](https://pic2.zhimg.com/80/v2-d84defad09f1ba1aa0d8d417576908d1_1440w.jpg)



Spring中用到的包装器模式在类名上有两种表现：一种是类名中含有Wrapper，另一种是类名中含有Decorator。基本上都是动态地给一个对象添加一些额外的职责。

## **六、代理（Proxy）**

为其他对象提供一种代理以控制对这个对象的访问。

从结构上来看和Decorator模式类似，但Proxy是控制，更像是一种对功能的限制，而Decorator是增加职责。

![img](https://pic3.zhimg.com/80/v2-d89e399909af337d3007bc4087ce81a6_1440w.jpg)

Spring的Proxy模式在aop中有体现，比如JdkDynamicAopProxy和Cglib2AopProxy。

## **七、观察者（Observer）**

定义对象间的一种一对多的依赖关系，当一个对象的状态发生改变时，所有依赖于它的对象都得到通知并被自动更新。

![img](https://pic1.zhimg.com/80/v2-cc7413358a174324b9c5628668d5ec74_1440w.jpg)

Spring中Observer模式常用的地方是listener的实现。如ApplicationListener。

## **八、策略（Strategy）**

定义一系列的算法，把它们一个个封装起来，并且使它们可相互替换。本模式使得算法可独立于使用它的客户而变化。

Spring中在实例化对象的时候用到Strategy模式，见如下图：

![img](https://pic1.zhimg.com/80/v2-b3cb4396f2d33ba3055b22c335ef4008_1440w.jpg)

在SimpleInstantiationStrategy中有如下代码说明了策略模式的使用情况：

![img](https://pic2.zhimg.com/80/v2-2de4db5bf7daa13562bfa1b77b7347e5_1440w.jpg)

## **九、模板方法（Template Method）**

定义一个操作中的算法的骨架，而将一些步骤延迟到子类中。Template Method使得子类可以不改变一个算法的结构即可重定义该算法的某些特定步骤。

Template Method模式一般是需要继承的。这里想要探讨另一种对Template Method的理解。Spring中的JdbcTemplate，在用这个类时并不想去继承这个类，因为这个类的方法太多，但是我们还是想用到JdbcTemplate已有的稳定的、公用的数据库连接，那么我们怎么办呢？我们可以把变化的东西抽出来作为一个参数传入JdbcTemplate的方法中。但是变化的东西是一段代码，而且这段代码会用到JdbcTemplate中的变量。怎么办？那我们就用回调对象吧。在这个回调对象中定义一个操纵JdbcTemplate中变量的方法，我们去实现这个方法，就把变化的东西集中到这里了。然后我们再传入这个回调对象到JdbcTemplate，从而完成了调用。这可能是Template Method不需要继承的另一种实现方式吧。

以下是一个具体的例子：

JdbcTemplate中的execute方法：



![img](https://pic1.zhimg.com/80/v2-5d55f0d9c6efb0235a56f3f7f2a78d28_1440w.jpg)

JdbcTemplate执行execute方法：

![img](https://pic2.zhimg.com/80/v2-975e75a6ff67460de52471c14565a6b1_1440w.jpg)

**十、桥接方法（Template Method）**　

​		桥接（Bridge）是用于把抽象化与实现化解耦，使得二者可以独立变化。这种类型的设计模式属于结构型模式，它通过提供抽象化和实现化之间的桥接结构，来实现二者的解耦。

　　这种模式涉及到一个作为桥接的接口，使得实体类的功能独立于接口实现类。这两种类型的类可被结构化改变而互不影响。

　　个人理解：桥接是一个接口，它与一方应该是绑定的，也就是解耦的双方中的一方必然是继承这个接口的，这一方就是实现方，而另一方正是要与这一方解耦的抽象方，如果不采用桥接模式，一般我们的处理方式是直接使用继承来实现，这样双方之间处于强链接，类之间关联性极强，如要进行扩展，必然导致类结构急剧膨胀。采用桥接模式，正是为了避免这一情况的发生，将一方与桥绑定，即实现桥接口，另一方在抽象类中调用桥接口（指向的实现类），这样桥方可以通过实现桥接口进行单方面扩展，而另一方可以继承抽象类而单方面扩展，而之间的调用就从桥接口来作为突破口，不会受到双方扩展的任何影响。

　　下面的实例能真正体现着一点：

　　实例准备：我们假设有一座桥，桥左边为A，桥右边为B，A有A1，A2，A3等，表示桥左边的三个不同地方，B有B1，B2，B3等，表示桥右边的三个不同地方，假设我们要从桥左侧A出发到桥的右侧B，我们可以有多重方案，A1到B1，A1到B2，A1到B3，A2到B1...等等，以此为例，代码如下：

桥接口：Qiao

```
1 public interface Qiao {
2     //目的地B
3     void targetAreaB();
4 }
```

目的地B1,B2,B3：

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

```
 1 /**
 2  * 目的地B1
 3  */
 4 public class AreaB1 implements Qiao {
 5 
 6     @Override
 7     public void targetAreaB() {
 8         System.out.println("我要去B1");
 9     }
10 
11 }
12 
13 /**
14  * 目的地B2
15  */
16 public class AreaB2 implements Qiao {
17 
18     @Override
19     public void targetAreaB() {
20         System.out.println("我要去B2");
21     }
22 
23 }
24 
25 /**
26  * 目的地B3
27  */
28 public class AreaB3 implements Qiao {
29 
30     @Override
31     public void targetAreaB() {
32         System.out.println("我要去B3");
33     }
34 
35 }
```

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

抽象来源地A：AreaA

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

```
1 public abstract class AreaA {
2     //引用桥接口
3     Qiao qiao;
4     //来源地
5     abstract void fromAreaA();
6 }
```

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

来源地A1，A2，A3：

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

```
 1 /**
 2  * 来源地A1
 3  */
 4 public class AreaA1 extends AreaA {
 5 
 6     @Override
 7     void fromAreaA() {
 8         System.out.println("我来自A1");
 9     }
10     
11 }
12 
13 /**
14  * 来源地A2
15  */
16 public class AreaA2 extends AreaA {
17 
18     @Override
19     void fromAreaA() {
20         System.out.println("我来自A2");
21     }
22 
23 }
24 
25 /**
26  * 来源地A3
27  */
28 public class AreaA3 extends AreaA {
29 
30     @Override
31     void fromAreaA() {
32         System.out.println("我来自A3");
33     }
34 
35 }
```

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

测试类：Clienter

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

```
1 public class Clienter {
2     public static void main(String[] args) {
3         AreaA a = new AreaA2();
4         a.qiao = new AreaB3();
5         a.fromAreaA();
6         a.qiao.targetAreaB();
7     }
8 }
```

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

运行结果：

```
我来自A2
我要去B3
```

　　如何，只要你认真看完了实例，你就明白了这种模式的好处，现在我们要添加来源地和目的地，只要继续继承AreaA和实现Qiao即可，之前我所说的绑定，正式此处将桥与目的地绑定在一起，使用一个接口完成。

　　其实要完成桥接模式，注意点并不多，重在理解模式的使用场景。

　　注意点：

　　　　1、定义一个桥接口，使其与一方绑定，这一方的扩展全部使用实现桥接口的方式。

　　　　2、定义一个抽象类，来表示另一方，在这个抽象类内部要引入桥接口，而这一方的扩展全部使用继承该抽象类的方式。

　　其实我们可以发现桥接模式应对的场景有方向性的，桥绑定的一方都是被调用者，属于被动方，抽象方属于主动方。

　　其实我的JDK提供的JDBC数据库访问接口API正是经典的桥接模式的实现者，接口内部可以通过实现接口来扩展针对不同数据库的具体实现来进行扩展，而对外的仅仅只是一个统一的接口调用，调用方过于抽象，可以将其看做每一个JDBC调用程序（这是真实实物，当然不存在抽象）

　　下面来理解一下开头的概念：

　　桥接（Bridge）是用于把抽象化与实现化解耦，使得二者可以独立变化。这种类型的设计模式属于结构型模式，它通过提供抽象化和实现化之间的桥接结构，来实现二者的解耦。

　　这种模式涉及到一个作为桥接的接口，使得实体类的功能独立于接口实现类。这两种类型的类可被结构化改变而互不影响。

 

　　理解：此处抽象化与实现化分别指代实例中的双方，而且实现化对应目的地方（通过实现桥接口进行扩展），抽象方对应来源地方（通过继承抽象类来进行扩展），如果我们不使用桥接模式，我们会怎么想实现这个实例呢？很简单，我们分别定义来源地A1、A2、A3类和目的地B1、B2、B3，然后具体的实现就是，A1到B1一个类，A1到B2一个类，等，如果我们要扩展了A和B ,要直接增加An类和Bn类，如此编写不说类内部重复性代码多，而且还会导致类结构的急剧膨胀，最重要的是，在通过继承实现路径的时候，会造成双方耦合性增大，而这又进一步加剧了扩展的复杂性。使用桥结构模式可以很好地规避这些问题：重在解耦。