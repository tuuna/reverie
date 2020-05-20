## Spring的IOC容器初始化流程

IOC容器：bean... newOrderServiceImpl()

HASHMAP对象： Map<String,Object> map = new HashMap<>;

Map.put(“orderServiceImpl”,new OrderServiceImpl())

IOC容器map.get(“orderServiceImpl”);

BeanFactory.getBean(“orderServiceImpl”) ----->map.get(“orderServiceImpl”);

Controller{

   OrderServiceImpl orderServiceImpl; //map.get(“orderServiceImpl”);

```
instanceWrapper = createBeanInstance(beanName, mbd, args);

1,Person person = new Person();
2,populateBean(beanName, mbd, instanceWrapper);
  Person.setName();
3,bean的初始化－－〉IOC  MAP

```

![ioc_process](../../image/ioc_process.png)

定位：这个过程最终会返回一个resoure类来代表bean定义的位置之类的信息；

加载：然后又一个reader类来根据这个位置信息读取bean的定义到内存；

注册：最后用一个map存储所有的bean名字和定义的映射关系



**接口注入：**

接口注入模式因为具备侵入性，它要求组件必须与特定的接口相关联，因此并不被看好，实际使用有限。

**Setter 注入：**

对于习惯了传统 javabean 开发的程序员，通过 setter 方法设定依赖关系更加直观。如果依赖关系较为复杂，那么构造子注入模式的构造函数也会相当庞大，而此时设值注入模式则更为简洁。如果用到了第三方类库，可能要求我们的组件提供一个默认的构造函数，此时构造子注入模式也不适用。

**构造器注入：**

在构造期间完成一个完整的、合法的对象。所有依赖关系在构造函数中集中呈现。依赖关系在构造时由容器一次性设定，组件被创建之后一直处于相对“不变”的稳定状态。只有组件的创建者关心其内部依赖关系，对调用者而言，该依赖关系处于“黑盒”之中。

**注解注入：**

使用注解注入依赖对象不用再在代码中写依赖对象的setter方法或者该类的构造方法，并且不用再配置文件中配置大量的依赖对象，使代码更加简洁，清晰，易于维护。

在Spring IOC编程的实际开发中推荐使用注解的方式进行依赖注入。