**Java 定义了两种异常：**

- **Checked exception**: 继承自 Exception 类是 checked exception。代码需要处理 API 抛出的 checked exception，要么用 catch 语句，要么直接用 throws 语句抛出去。

  - **Unchecked exception**: 也称 RuntimeException，它也是继承自 Exception。但所有 RuntimeException 的子类都有个特点，就是代码不需要处理它们的异常也能通过编译，所以它们称作 unchecked exception。RuntimeException（运行时异常）不需要try...catch...或throws 机制去处理的异常。

    > # Checked Exception
    >
    > Checked Exception 是必须在代码中进行恰当处理的 Exception，而且编译器会强制开发者对其进行处理，否则编译会不通过。你可以使用 `catch` 语句捕获这些 Exception 或者在方法声明处使用 `throws` 语句抛出该异常。
    >
    > 一般来说，Checked Exception 的发生主要是由于一些特殊情况没有考虑到，比如如果网络连接失败会抛出 IOException，但是我们的程序应该能够提前预料到这些可能发生的异常，并对其进行处理，这样程序在运行过程中才不会崩掉，这也是编译器强制开发者对 Checked Exception 进行处理的原因。假设在文件传输的过程中网络出现中断，这时候程序应该能够捕获到这种异常并进行处理（重新尝试传输文件）。
    >
    > # Unchecked Exception
    >
    > Unchecked Exception 的发生有一些是由于开发者代码逻辑错误造成的，比如：NullPointerException 这种异常可以通过检查一个引用是否为 null 来进行避免。
    >
    > 但是也有一些 Unchecked Exception 出现并不是因为开发者程序的问题，这些 Exception 是 `java.lang.Error` 的子类。就像 OutOfMemoryError 可能发生在任意一个示例对象创建时，但我们不可能在每个对象实例创建时都使用 `catch` 块去捕获异常。因此，我们也就不可能预料这些异常的发生，编译器在编译时也无法检测到这些异常。

    **Throw 与 throws**

> throw语句用在方法体内，表示抛出异常，由方法体内的语句处理。
> throws语句用在方法声明后面，表示再抛出异常，由该方法的调用者来处理。
>
>  
>
> throws主要是声明这个方法会抛出这种类型的异常，使它的调用者知道要捕获这个异常。
> throw是具体向外抛异常的动作，所以它是抛出一个异常实例。
>
>  
>
> throws说明你有那个可能，倾向。
> throw的话，那就是你把那个倾向变成真实的了。
>
>  
>
> 同时：
> 1、throws出现在方法函数头；而throw出现在函数体。
> 2、throws表示出现异常的一种可能性，并不一定会发生这些异常；throw则是抛出了异常，执行throw则一定抛出了某种异常。
> 3、两者都是消极处理异常的方式（这里的消极并不是说这种方式不好），只是抛出或者可能抛出异常，但是不会由函数去处理异常，真正的处理异常由函数的上层调用处理。

**异常分类**

–>Throwable是Java语言中所有错误或异常的超类。下一层分为Error和Exception

–>Error类是指Java运行时系统的内部错误和资源耗尽错误。应用程序不会抛出该类对象。如果出现了这样的错误，除了告知用户，剩下的就是尽力使程序安全的终止。

–>Exception又有两个分支，
 一个是运行时异常RuntimeException，如：NullPointerException、ClassCastException；
 一个是检查异常CheckedException，如I/O错误导致的IOException、SQLException。

–>RuntimeException是那些可能在 Java 虚拟机正常运行期间抛出的异常的超类。派生RuntimeException的异常一般包含几个方面：
 1、错误的类型转换
 2、数组访问越界
 3、访问空指针
 如果出现RuntimeException，那么一定是程序员的错误

–>检查异常CheckedException一般是外部错误，这种异常都发生在编译阶段，Java编译器会强制程序去捕获此类异常，即会出现要求你把这段可能出现异常的程序进行try catch，该类异常一般包括几个方面：
 1、试图在文件尾部读取数据
 2、试图打开一个错误格式的URL
 3、试图根据给定的字符串查找class对象，而这个字符串表示的类并不存在

RuntimeException：在定义方法时不需要声明会抛出RuntimeException， 在调用这个方法时不需要捕获这个RuntimeException；总之，未检查异常不需要try…catch…或throws 机制去处理
 CheckedException：定义方法时必须声明所有可能会抛出的exception； 在调用这个方法时，必须捕获它的checked exception，不然就得把它的exception传递下去
 总之，一个方法必须声明所有的可能抛出的受检异常；未检查异常要么不可控制（Error），要么应该避免（RuntimeException）。如果方法没有声明所有的可能发生的受检异常，编译器就会给出错误信息

# 五种常见的运行时异常

NullPointerException
 ArrayIndexOutOfBoundsException
 ClassCastException
 ClassNotFoundException
 IndexOutOfBoundsException
 NumberFormatException
 IllegalArgumentException
 ArithmeticException