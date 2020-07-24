# 1. AOP简介

相信大家或多或少的了解过AOP，都知道它是面向切面编程，在网上搜索可以找到很多的解释。这里我用一句话来总结：AOP是能够让我们在不影响原有功能的前提下，为软件**横向扩展**功能。 那么横向扩展怎么理解呢，我们在WEB项目开发中，通常都遵守三层原则，包括控制层（Controller）->业务层（Service）->数据层（dao）,那么从这个结构下来的为纵向，它具体的某一层就是我们所说的横向。我们的AOP就是可以作用于这某一个横向模块当中的所有方法。

我们在来看一下AOP和OOP的区别：AOP是OOP的补充，当我们需要为多个对象引入一个公共行为，比如日志，操作记录等，就需要在每个对象中引用公共行为，这样程序就产生了大量的重复代码，使用AOP可以完美解决这个问题。

接下来介绍一下提到AOP就必须要了解的知识点：

- 切面：拦截器类，其中会定义切点以及通知
- 切点：具体拦截的某个业务点。
- 通知：切面当中的方法，声明通知方法在目标业务层的执行位置，通知类型如下：
  1. 前置通知：@Before 在目标业务方法执行之前执行
  2. 后置通知：@After 在目标业务方法执行之后执行
  3. 返回通知：@AfterReturning 在目标业务方法返回结果之后执行
  4. 异常通知：@AfterThrowing 在目标业务方法抛出异常之后
  5. 环绕通知：@Around 功能强大，可代替以上四种通知，还可以控制目标业务方法是否执行以及何时执行

# 2. 代码中实现举例

上面已经大概的介绍了AOP中需要了解的基本知识，也知道了AOP的好处，那怎么在代码中实现呢？给大家举个例子：我们现在有个学校管理系统，已经实现了对老师和学生的增删改，又新来个需求，说是对老师和学生的每次增删改做一个记录，到时候校长可以查看记录的列表。那么问题来了，怎么样处理是最好的解决办法呢？这里我罗列了三种解决办法，我们来看下他的优缺点。

- 最简单的就是第一种方法，我们直接在每次的增删改的函数当中直接实现这个记录的方法，这样代码的重复度太高，耦合性太强，不建议使用。

- 其次就是我们最长使用的，将记录这个方法抽离出来，其他的增删改调用这个记录函数即可，显然代码重复度降低，但是这样的调用还是没有降低耦合性。

- 这个时候我们想一下AOP的定义，再想想我们的场景，其实我们就是要在不改变原来增删改的方法，给这个系统增加记录的方法，而且作用的也是一个层面的方法。这个时候我们就可以采用AOP来实现了。

**我们来看下代码的具体实现：**

- 首先我定义了一个自定义注解作为切点

```java
@Target(AnnotationTarget.FUNCTION)  //注解作用的范围，这里声明为函数
@Order(Ordered.HIGHEST_PRECEDENCE)  //声明注解的优先级为最高，假设有多个注解，先执行这个
annotation class Hanler(val handler: HandlerType)  //自定义注解类，HandlerType是一个枚举类型，里面定义的就是学生和老师的增删改操作，在这里就不展示具体内容了
```

- 接下来就是要定义切面类了

```java
@Aspect   //该注解声明这个类为一个切面类
@Component
class HandlerAspect{
 
     @Autowired
     private lateinit var handlerService: HandlerService

    @AfterReturning("@annotation(handler)")   //当有函数注释了注解，将会在函数正常返回后在执行我们定义的方法
    fun hanler(hanler: Hanler) {
        handlerService.add(handler.operate.value)   //这里是真正执行记录的方法
    }
}
```

- 最后就是我们本来的业务方法了

```java
/**
* 删除学生方法
*/
@Handler(operate= Handler.STUDENT_DELETE)   //当执行到删除学生方法时，切面类就会起作用了,当学生正常删除后就会执行记录方法，我们就可以看到记录方法生成的数据
fun delete(id：String) {
   studentService.delete(id)
}
```

# 3. AOP实现原理

我们现在了解了代码中如何实现，那么AOP实现的原理是什么呢？之前看了一个博客说到，提到AOP大家都知道他的实现原理是动态代理，显然我之前就是不知道的，哈哈，但是相信阅读文章的你们一定是知道的。

讲到动态代理就不得不说代理模式了， 代理模式的定义：给某一个对象提供一个代理，并由代理对象控制对原对象的引用。代理模式包含如下角色：subject：抽象主题角色，是一个接口。该接口是对象和它的代理共用的接口; RealSubject：真实主题角色，是实现抽象主题接口的类; Proxy:代理角色，内部含有对真实对象RealSubject的引用，从而可以操作真实对象。代理对象提供与真实对象相同的接口，以便代替真实对象。同时，代理对象可以在执行真实对象操作时，附加其他的操作，相当于对真实对象进行封装。

那么代理又分为静态代理和动态代理，这里写两个小的demo，动态代理采用的就是JDK代理。举个例子就是现在一个班上的学生需要交作业，现在由班长代理交作业，那么班长就是代理，学生就是被代理的对象。

## 3.1 静态代理

首先，我们创建一个Person接口。这个接口就是学生（被代理类），和班长（代理类）的公共接口，他们都有交作业的行为。这样，学生交作业就可以让班长来代理执行。

```java
/**
 * 
 * 创建person接口
 */
public interface Person {
    //交作业
    void giveTask();
}
```

Student类实现Person接口，Student可以具体实施交作业这个行为。

```java
public class Student implements Person {
    private String name;
    public Student(String name) {
        this.name = name;
    }
 
    public void giveTask() {
        System.out.println(name + "交语文作业");
    }
}
```

StudentsProxy类，这个类也实现了Person接口，但是还另外持有一个学生类对象，那么他可以代理学生类对象执行交作业的行为。

```java
/**
 *
 * 学生代理类，也实现了Person接口，保存一个学生实体，这样就可以代理学生产生行为
 */
public class StudentsProxy implements Person{
    //被代理的学生
    Student stu;
 
    public StudentsProxy(Person stu) {
        // 只代理学生对象
        if(stu.getClass() == Student.class) {
            this.stu = (Student)stu;
        }
    }
 
    //代理交作业，调用被代理学生的交作业的行为
    public void giveTask() {
        stu.giveTask();
    }
}
```

下面测试一下，看代理模式如何使用：

```java
/**
 * Created by Mapei on 2018/11/7
 */
public class StaticProxyTest {
    public static void main(String[] args) {
        //被代理的学生林浅，他的作业上交有代理对象monitor完成
        Person linqian = new Student("林浅");
 
        //生成代理对象，并将林浅传给代理对象
        Person monitor = new StudentsProxy(linqian);
 
        //班长代理交作业
        monitor.giveTask();
    }
}
```

这里并没有直接通过林浅（被代理对象）来执行交作业的行为，而是通过班长（代理对象）来代理执行了。这就是代理模式。代理模式就是在访问实际对象时引入一定程度的间接性，这里的间接性就是指不直接调用实际对象的方法，那么我们在代理过程中就可以加上一些其他用途。比如班长在帮林浅交作业的时候想告诉老师最近林浅的进步很大，就可以轻松的通过代理模式办到。在代理类的交作业之前加入方法即可。这个优点就可以运用在spring中的AOP，我们能在一个切点之前执行一些操作，在一个切点之后执行一些操作，这个切点就是一个个方法。这些方法所在类肯定就是被代理了，在代理过程中切入了一些其他操作。

## 3.2 动态代理

动态代理和静态代理的区别是，静态代理的的代理类是我们自己定义好的，在程序运行之前就已经变异完成，但是动态代理的代理类是在程序运行时创建的。相比于静态代理，动态代理的优势在于可以很方便的对代理类的函数进行统一的处理，而不用修改每个代理类中的方法。比如我们想在每个代理方法之前都加一个处理方法，我们上面的例子中只有一个代理方法，如果还有很多的代理方法，就太麻烦了，我们来看下动态代理是怎么去实现的。

首先还是定义一个Person接口:

```java
/**
 * 
 * 创建person接口
 */
public interface Person {
    //交作业
    void giveTask();
}
```

接下来是创建需要被代理的实际类，也就是学生类：

```java
/**
 * 
 */
public class Student implements Person {
    private String name;
    public Student(String name) {
        this.name = name;
    }
 
    public void giveTask() {
        System.out.println(name + "交语文作业");
    }
}
```

创建StuInvocationHandler类，实现InvocationHandler接口，这个类中持有一个被代理对象的实例target。InvocationHandler中有一个invoke方法，所有执行代理对象的方法都会被替换成执行invoke方法。

```java
/**
 * 
 */
public class StuInvocationHandler<T> implements InvocationHandler {
    //invocationHandler持有的被代理对象
    T target;
 
    public StuInvocationHandler(T target) {
        this.target = target;
    }
 
    /**
     * proxy:代表动态代理对象
     * method：代表正在执行的方法
     * args：代表调用目标方法时传入的实参
     */
    public Object invoke(Object proxy, Method method, Object[] args) throws Throwable {
        System.out.println("代理执行" +method.getName() + "方法");
        Object result = method.invoke(target, args);
        return result;
    }
}
```

那么接下来我们就可以具体的创建代理对象了。

```java
/**
 * 
 * 代理类
 */
public class ProxyTest {
    public static void main(String[] args) {
 
        //创建一个实例对象，这个对象是被代理的对象
        Person linqian = new Student("林浅");
 
        //创建一个与代理对象相关联的InvocationHandler
        InvocationHandler stuHandler = new StuInvocationHandler<Person>(linqian);
 
        //创建一个代理对象stuProxy来代理linqian，代理对象的每个执行方法都会替换执行Invocation中的invoke方法
        Person stuProxy = (Person) Proxy.newProxyInstance(Person.class.getClassLoader(), new Class<?>[]{Person.class}, stuHandler);
 
        //代理执行交作业的方法
        stuProxy.giveTask();
    }
}
```

我们执行代理测试类，首先我们创建了一个需要被代理的学生林浅，将林浅传入stuHandler中，我们在创建代理对象stuProxy时，将stuHandler作为参数，那么所有执行代理对象的方法都会被替换成执行invoke方法，也就是说，最后执行的是StuInvocationHandler中的invoke方法。所以在看到下面的运行结果也就理所当然了。

那么到这里问题就来了，为什么代理对象执行的方法都会通过InvocationHandler中的invoke方法来执行，带着这个问题，我们需要看一下动态代理的源码，对他进行简单的分析。

上面我们使用Proxy类的newProxyInstance方法创建了一个动态代理对象，看一下他的源码：

```java
public static Object newProxyInstance(ClassLoader loader, Class<?>[] interfaces, InvocationHandler h)
        throws IllegalArgumentException
    {
        Objects.requireNonNull(h);
 
        final Class<?>[] intfs = interfaces.clone();
        final SecurityManager sm = System.getSecurityManager();
        if (sm != null) {
            checkProxyAccess(Reflection.getCallerClass(), loader, intfs);
        }
 
        /*
         * Look up or generate the designated proxy class.
         */
        Class<?> cl = getProxyClass0(loader, intfs);
 
        /*
         * Invoke its constructor with the designated invocation handler.
         */
        try {
            if (sm != null) {
                checkNewProxyPermission(Reflection.getCallerClass(), cl);
            }
 
            final Constructor<?> cons = cl.getConstructor(constructorParams);
            final InvocationHandler ih = h;
            if (!Modifier.isPublic(cl.getModifiers())) {
                AccessController.doPrivileged(new PrivilegedAction<Void>() {
                    public Void run() {
                        cons.setAccessible(true);
                        return null;
                    }
                });
            }
            return cons.newInstance(new Object[]{h});
        } catch (IllegalAccessException|InstantiationException e) {
            throw new InternalError(e.toString(), e);
        } catch (InvocationTargetException e) {
            Throwable t = e.getCause();
            if (t instanceof RuntimeException) {
                throw (RuntimeException) t;
            } else {
                throw new InternalError(t.toString(), t);
            }
        } catch (NoSuchMethodException e) {
            throw new InternalError(e.toString(), e);
        }
  }
```

然后，我们需要重点关注Class<?> cl = getProxyClass0(loader, intfs)这句代码，这里产生了代理类，这个类就是动态代理的关键，由于是动态生成的类文件，我们将这个类文件打印到文件中。

```java
        byte[] classFile = ProxyGenerator.generateProxyClass("$Proxy0", Student.class.getInterfaces());
        String path = "/Users/mapei/Desktop/okay/65707.class";
 
        try{
            FileOutputStream fos = new FileOutputStream(path);
            fos.write(classFile);
            fos.flush();
            System.out.println("代理类class文件写入成功");
        }catch (Exception e) {
            System.out.println("写文件错误");
        }
```

对这个class文件进行反编译，我们看看jdk为我们生成了什么样的内容：

```java
import java.lang.reflect.InvocationHandler;
import java.lang.reflect.Method;
import java.lang.reflect.Proxy;
import java.lang.reflect.UndeclaredThrowableException;
import proxy.Person;
 
public final class $Proxy0 extends Proxy implements Person
{
  private static Method m1;
  private static Method m2;
  private static Method m3;
  private static Method m0;
  
  /**
  *注意这里是生成代理类的构造方法，方法参数为InvocationHandler类型，看到这，是不是就有点明白
  *为何代理对象调用方法都是执行InvocationHandler中的invoke方法，而InvocationHandler又持有一个
  *被代理对象的实例，就可以去调用真正的对象实例。
  */
  public $Proxy0(InvocationHandler paramInvocationHandler)
    throws 
  {
    super(paramInvocationHandler);
  }
  
  //这个静态块本来是在最后的，我把它拿到前面来，方便描述
   static
  {
    try
    {
      //看看这儿静态块儿里面的住giveTask通过反射得到的名字m3，其他的先不管
      m1 = Class.forName("java.lang.Object").getMethod("equals", new Class[] { Class.forName("java.lang.Object") });
      m2 = Class.forName("java.lang.Object").getMethod("toString", new Class[0]);
      m3 = Class.forName("proxy.Person").getMethod("giveTask", new Class[0]);
      m0 = Class.forName("java.lang.Object").getMethod("hashCode", new Class[0]);
      return;
    }
    catch (NoSuchMethodException localNoSuchMethodException)
    {
      throw new NoSuchMethodError(localNoSuchMethodException.getMessage());
    }
    catch (ClassNotFoundException localClassNotFoundException)
    {
      throw new NoClassDefFoundError(localClassNotFoundException.getMessage());
    }
  }
 
  /**
  * 
  *这里调用代理对象的giveMoney方法，直接就调用了InvocationHandler中的invoke方法，并把m3传了进去。
  *this.h.invoke(this, m3, null);我们可以对将InvocationHandler看做一个中介类，中介类持有一个被代理对象，在invoke方法中调用了被代理对象的相应方法。通过聚合方式持有被代理对象的引用，把外部对invoke的调用最终都转为对被代理对象的调用。
  */
  public final void giveTask()
    throws 
  {
    try
    {
      this.h.invoke(this, m3, null);
      return;
    }
    catch (Error|RuntimeException localError)
    {
      throw localError;
    }
    catch (Throwable localThrowable)
    {
      throw new UndeclaredThrowableException(localThrowable);
    }
  }
 
}
```

看完了动态代理的源码，我们接下来就要看一下Spring中AOP实现的源码是怎样的？

# 4. 部分源码解析

## aop创建代理的源码分析

1. **看一下bean如何被包装为proxy**

```java
       	protected Object createProxy(
   		Class<?> beanClass, String beanName, Object[] specificInterceptors, TargetSource targetSource) {
   		
   	if (this.beanFactory instanceof ConfigurableListableBeanFactory) {
   		AutoProxyUtils.exposeTargetClass((ConfigurableListableBeanFactory) this.beanFactory, beanName, beanClass);
   	}
 
       // 1.创建proxyFactory，proxy的生产主要就是在proxyFactory做的
   	ProxyFactory proxyFactory = new ProxyFactory();
   	proxyFactory.copyFrom(this);
 
   	if (!proxyFactory.isProxyTargetClass()) {
   		if (shouldProxyTargetClass(beanClass, beanName)) {
   			proxyFactory.setProxyTargetClass(true);
   		}
   		else {
   			evaluateProxyInterfaces(beanClass, proxyFactory);
   		}
   	}
 
       // 2.将当前bean适合的advice，重新封装下，封装为Advisor类，然后添加到ProxyFactory中
   	Advisor[] advisors = buildAdvisors(beanName, specificInterceptors);
   	for (Advisor advisor : advisors) {
   		proxyFactory.addAdvisor(advisor);
   	}
 
   	proxyFactory.setTargetSource(targetSource);
   	customizeProxyFactory(proxyFactory);
 
   	proxyFactory.setFrozen(this.freezeProxy);
   	if (advisorsPreFiltered()) {
   		proxyFactory.setPreFiltered(true);
   	}
 
       // 3.调用getProxy获取bean对应的proxy
   	return proxyFactory.getProxy(getProxyClassLoader());
   }
```

1. **创建何种类型的Proxy？JDKProxy还是CGLIBProxy？**

```java
	public Object getProxy(ClassLoader classLoader) {
		return createAopProxy().getProxy(classLoader);
	}
    // createAopProxy()方法就是决定究竟创建何种类型的proxy
	protected final synchronized AopProxy createAopProxy() {
		if (!this.active) {
			activate();
		}
        // 关键方法createAopProxy()
		return getAopProxyFactory().createAopProxy(this);
	}
	
    // createAopProxy()
	public AopProxy createAopProxy(AdvisedSupport config) throws AopConfigException {
        // 1.config.isOptimize()是否使用优化的代理策略，目前使用与CGLIB
        // config.isProxyTargetClass() 是否目标类本身被代理而不是目标类的接口
        // hasNoUserSuppliedProxyInterfaces()是否存在代理接口
		if (config.isOptimize() || config.isProxyTargetClass() || hasNoUserSuppliedProxyInterfaces(config)) {
			Class<?> targetClass = config.getTargetClass();
			if (targetClass == null) {
				throw new AopConfigException("TargetSource cannot determine target class: " +
						"Either an interface or a target is required for proxy creation.");
			}
            
            // 2.如果目标类是接口类（目标对象实现了接口），则直接使用JDKproxy
			if (targetClass.isInterface() || Proxy.isProxyClass(targetClass)) {
				return new JdkDynamicAopProxy(config);
			}
            
            // 3.其他情况则使用CGLIBproxy
			return new ObjenesisCglibAopProxy(config);
		}
		else {
			return new JdkDynamicAopProxy(config);
		}
	}
```

1. **getProxy()方法**

```java
   final class JdkDynamicAopProxy implements AopProxy, InvocationHandler, Serializable// JdkDynamicAopProxy类结构，由此可知，其实现了InvocationHandler，则必定有invoke方法，来被调用，也就是用户调用bean相关方法时，此invoke()被真正调用
   // getProxy()
   public Object getProxy(ClassLoader classLoader) {
   	if (logger.isDebugEnabled()) {
   		logger.debug("Creating JDK dynamic proxy: target source is " + this.advised.getTargetSource());
   	}
   	Class<?>[] proxiedInterfaces = AopProxyUtils.completeProxiedInterfaces(this.advised, true);
   	findDefinedEqualsAndHashCodeMethods(proxiedInterfaces);
       
       // JDK proxy 动态代理的标准用法
   	return Proxy.newProxyInstance(classLoader, proxiedInterfaces, this);
   }
```

1. **invoke()方法法**

```java
    //使用了JDK动态代理模式，真正的方法执行在invoke()方法里，看到这里在想一下上面动态代理的例子，是不是就完全明白Spring源码实现动态代理的原理了。
			public Object invoke(Object proxy, Method method, Object[] args) throws Throwable {
		MethodInvocation invocation;
		Object oldProxy = null;
		boolean setProxyContext = false;
 
		TargetSource targetSource = this.advised.targetSource;
		Class<?> targetClass = null;
		Object target = null;
 
		try {
            // 1.以下的几个判断，主要是为了判断method是否为equals、hashCode等Object的方法
			if (!this.equalsDefined && AopUtils.isEqualsMethod(method)) {
				// The target does not implement the equals(Object) method itself.
				return equals(args[0]);
			}
			else if (!this.hashCodeDefined && AopUtils.isHashCodeMethod(method)) {
				// The target does not implement the hashCode() method itself.
				return hashCode();
			}
			else if (method.getDeclaringClass() == DecoratingProxy.class) {
				// There is only getDecoratedClass() declared -> dispatch to proxy config.
				return AopProxyUtils.ultimateTargetClass(this.advised);
			}
			else if (!this.advised.opaque && method.getDeclaringClass().isInterface() &&
					method.getDeclaringClass().isAssignableFrom(Advised.class)) {
				// Service invocations on ProxyConfig with the proxy config...
				return AopUtils.invokeJoinpointUsingReflection(this.advised, method, args);
			}
 
			Object retVal;
 
			if (this.advised.exposeProxy) {
				// Make invocation available if necessary.
				oldProxy = AopContext.setCurrentProxy(proxy);
				setProxyContext = true;
			}
 
			// May be null. Get as late as possible to minimize the time we "own" the target,
			// in case it comes from a pool.
			target = targetSource.getTarget();
			if (target != null) {
				targetClass = target.getClass();
			}
			// 2.获取当前bean被拦截方法链表
			List<Object> chain = this.advised.getInterceptorsAndDynamicInterceptionAdvice(method, targetClass);
 
			// 3.如果为空，则直接调用target的method
			if (chain.isEmpty()) {
				Object[] argsToUse = AopProxyUtils.adaptArgumentsIfNecessary(method, args);
				retVal = AopUtils.invokeJoinpointUsingReflection(target, method, argsToUse);
			}
            // 4.不为空，则逐一调用chain中的每一个拦截方法的proceed，这里的一系列执行的原因以及proceed执行的内容，我 在这里就不详细讲了，大家感兴趣可以自己去研读哈
			else {
				// We need to create a method invocation...
				invocation = new ReflectiveMethodInvocation(proxy, target, method, args, targetClass, chain);
				// Proceed to the joinpoint through the interceptor chain.
				retVal = invocation.proceed();
			}
 
			...
			return retVal;
		}
	}
	}
```

**静态代理类优缺点**
优点：业务类只需要关注业务逻辑本身，保证了业务类的重用性。这是代理的共有优点。
缺点：
1）代理对象的一个接口只服务于一种类型的对象，如果要代理的方法很多，势必要为每一种方法都进行代理，静态代理在程序规模稍大时就无法胜任了。
2）如果接口增加一个方法，除了所有实现类需要实现这个方法外，所有代理类也需要实现此方法。增加了代码维护的复杂度。
另外，如果要按照上述的方法使用代理模式，那么真实角色(委托类)必须是事先已经存在的，并将其作为代理对象的内部属性。但是实际使用时，一个真实角色必须对应一个代理角色，如果大量使用会导致类的急剧膨胀；此外，如果事先并不知道真实角色（委托类），该如何使用代理呢？这个问题可以通过Java的动态代理类来解决。

**动态代理类优缺点**
优点
1.动态代理类的字节码在程序运行时由Java反射机制动态生成，无需程序员手工编写它的源代码。
2.动态代理类不仅简化了编程工作，而且提高了软件系统的可扩展性，因为Java 反射机制可以生成任意类型的动态代理类。

缺点
JDK的动态代理机制只能代理实现了接口的类，而不能实现接口的类就不能实现JDK的动态代理，cglib是针对类来实现代理的，他的原理是对指定的目标类生成一个子类，并覆盖其中方法实现增强，但因为采用的是继承，所以不能对final修饰的类进行代理。