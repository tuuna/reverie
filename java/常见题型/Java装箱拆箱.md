## 自动装箱和拆箱的原理

​	自动装箱时编译器调用valueOf将原始类型值转换成对象，同时自动拆箱时，编译器通过调用类似intValue(),doubleValue()这类的方法将对象转换成原始类型值。

> 包装类作用主要有以下两方面：
> - 编码过程中只接收对象的情况，比如List中只能存入对象，不能存入基本数据类型；比如一个方法的参数是Object时，不能传入基本数据类型，但可以传入对应的包装类；
> - 方便类型之间的转换，比如String和int之间的转换可以通过int的包装类Integer来实现

明白自动装箱和拆箱的原理后，我们带着上面的疑问进行分析下Integer的自动装箱的实现源码。如下：



```java
    public static Integer valueOf(int i) {
        //判断i是否在-128和127之间，存在则从IntegerCache中获取包装类的实例，否则new一个新实例
        if (i >= IntegerCache.low && i <= IntegerCache.high)
            return IntegerCache.cache[i + (-IntegerCache.low)];
        return new Integer(i);
    }


    //使用亨元模式，来减少对象的创建（亨元设计模式大家有必要了解一下，我认为是最简单的设计模式，也许大家经常在项目中使用，不知道他的名字而已）
    private static class IntegerCache {
        static final int low = -128;
        static final int high;
        static final Integer cache[];

        //静态方法，类加载的时候进行初始化cache[],静态变量存放在常量池中
        static {
            // high value may be configured by property
            int h = 127;
            String integerCacheHighPropValue =
                sun.misc.VM.getSavedProperty("java.lang.Integer.IntegerCache.high");
            if (integerCacheHighPropValue != null) {
                try {
                    int i = parseInt(integerCacheHighPropValue);
                    i = Math.max(i, 127);
                    // Maximum array size is Integer.MAX_VALUE
                    h = Math.min(i, Integer.MAX_VALUE - (-low) -1);
                } catch( NumberFormatException nfe) {
                    // If the property cannot be parsed into an int, ignore it.
                }
            }
            high = h;

            cache = new Integer[(high - low) + 1];
            int j = low;
            for(int k = 0; k < cache.length; k++)
                cache[k] = new Integer(j++);

            // range [-128, 127] must be interned (JLS7 5.1.7)
            assert IntegerCache.high >= 127;
        }

        private IntegerCache() {}
    }
```

​	 Integer i1 = 40;  自动装箱，相当于调用了Integer.valueOf(40);方法。
    首先判断i值是否在-128和127之间，如果在-128和127之间则直接从IntegerCache.cache缓存中获取指定数字的包装类；不存在则new出一个新的包装类。
    IntegerCache内部实现了一个Integer的静态常量数组，在类加载的时候，执行static静态块进行初始化-128到127之间的Integer对象，存放到cache数组中。cache属于常量，存放在java的方法区中。