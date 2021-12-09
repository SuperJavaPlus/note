# JVM

## 1.JVM与Java体系结构

### 1.1 jvm概述

![img](https://www.yuque.com/api/filetransfer/images?url=https%3A%2F%2Fgitee.com%2Fvectorx%2FImageCloud%2Fraw%2Fmaster%2Fimg%2F20210507094858.png&sign=48daf13ca731d299f571ec2e91c307b4fb5d792a0ce4ca1e7da49c17abc9e824)



每个语言都需要转换成字节码文件，最后转换的字节码文件都能通过Java虚拟机运行和处理

![img](https://www.yuque.com/api/filetransfer/images?url=https%3A%2F%2Fgitee.com%2Fvectorx%2FImageCloud%2Fraw%2Fmaster%2Fimg%2F20210507101259.png&sign=24b92570d7e121efcd8759aa5fab1d3aee14a4ca5e7097cb9ddbd75d8553660f)

- Java虚拟机平台上可以运行非Java语言编写的程序
- Java虚拟机只关心字节码文件



> 字节码

- 我们平时所说的字节码，指的是用Java语言编译成的字节码。准确的说任何能在jvm平台上执行的字节码格式都是一样的。所以应该统称为：**jvm字节码**



**jvm的特点：**

- 一次编译，到处运行
- 自动内存管理
- 自动垃圾回收功能

**jvm的位置**

![img](https://www.yuque.com/api/filetransfer/images?url=https%3A%2F%2Fgitee.com%2Fvectorx%2FImageCloud%2Fraw%2Fmaster%2Fimg%2F20210507103919.png&sign=7645ca444991b5d50bdf44ae39d8e748b73bb257233479e0703295fcf9189715)

jvm是运行在操作系统上的，它与硬件没有直接的交互

![img](https://www.yuque.com/api/filetransfer/images?url=https%3A%2F%2Fgitee.com%2Fvectorx%2FImageCloud%2Fraw%2Fmaster%2Fimg%2F20210507104032.png&sign=4b2cf1c3010337729dcbe08881ea0723bb28a377b38c0994fd89bfff0d55212a)



### 1.2 jvm的整体结构

![img](https://www.yuque.com/api/filetransfer/images?url=https%3A%2F%2Fgitee.com%2Fvectorx%2FImageCloud%2Fraw%2Fmaster%2Fimg%2F20210507104104.png&sign=6e625f14612e6a22b3effa843fe5d16f3cc29d27a179bdd2799b2e5b12dccfd5)

- HotSpot VM是目前市面上高性能虚拟机的代表作之一
- 它采用解释器与即使编译器并存的架构
- 在今天，Java程序的运行性能早已脱胎换骨，已经达到了可以和C++程序一较高下的地步

**在内存当中，多线程共享方法区和堆**

**高级语言翻译成机器指令的过程主要是由执行引擎执行的**



### 1.3 Java代码执行流程

![img](https://www.yuque.com/api/filetransfer/images?url=https%3A%2F%2Fgitee.com%2Fvectorx%2FImageCloud%2Fraw%2Fmaster%2Fimg%2F20210507124722.png&sign=8f4e06b2070a3b3396eea3e796e011a815a034de378a9370f9564b9f850b527d)

- 解释执行：逐行的对字节码指令进行解释执行
- 编译执行：把经常执行的代码缓存下来，下次可以直接调用



### 1.4 jvm架构模型

- Java编译器输入的指令流基本上是一种基于栈的指令集架构，另外一种指令集架构则是基于寄存器的指令集架构

具体来说这两种架构之间的区别：

- 基于栈式架构的特点：

  - 设计和实现更简单，适用于资源受限的系统
  - 避开了寄存器分配难题:使用零地址指令方式分配
  - 指令流中的指令大部分是零地址指令，其执行过程依赖于操作栈。指令集更小，编译器更容易实现
  - 不需要硬件支持，可以执行更好，更好实现跨平台

- 基于寄存器架构的特点：

  - 典型的应用式x86的二进制指令集
  - 指令架构完全依赖硬件，可移植性差
  - 性能优秀和执行更高效
  - 花费更少的指令去完成一项操作
  - 在大部分情况下，基于寄存器架构的指令往往都以一地址指令，二地址指令和三地址指令为主，而基于栈式的架构指令集却是以零地址指令为主

  

```java
public class StackStructerTest {
    public static void main(String[] args) {
        int i = 2;
        int j = 3;
        int k = i + j;
    }
}
```

![image-20211007164833794](F:\笔记图片\img\image-20211007164833794.png)

由于**跨平台性的设计，Java的指令都是根据栈来设计的**。不同CPU的架构不同，所以不能设计为基于寄存器的



> 栈

- 跨平台性，指令集小，指令多；执行性能比寄存器差



### 1.5 jvm的生命周期

**虚拟机的启动**

- Java虚拟机的启动是通过引导类加载器（bootstrap class loader）创建一个初始类来完成的，这个类是由虚拟机的具体实现指定的

**虚拟机的执行**

- 一个运行中的Java虚拟机有着一个清晰的任务：执行Java程序
- 程序开始时它就运行，程序结束时它就停止
- **执行一个所谓的Java程序的时候，真真正正在执行的是一个叫做Java虚拟机的进程**

**虚拟机的退出**

- 程序正常执行结束
- 程序在执行过程中遇到了异常或错误而异常终止
- 由于操作系统出现错误而导致Java虚拟机进程终止
- 某线程调用Runtime类或System类的exit方法，或Runtime类的halt方法，并且Java安全管理器也允许这次exit或halt方法
- 除此之外，JNI（Java Native Interface）规范描述了用JNI Invocation API来加载或卸载 Java虚拟机时，Java虚拟机的退出情况。



## 2.类加载子系统

### 2.1 内存结构概述

![img](https://www.yuque.com/api/filetransfer/images?url=https%3A%2F%2Fgitee.com%2Fvectorx%2FImageCloud%2Fraw%2Fmaster%2Fimg%2F20210507115101.png&sign=b265af5978a9436b3da0651363c098465e23837046eb41b179b441ffefa98f54)

![img](https://www.yuque.com/api/filetransfer/images?url=https%3A%2F%2Fgitee.com%2Fvectorx%2FImageCloud%2Fraw%2Fmaster%2Fimg%2F20210507115120.png&sign=a8fcd892a19a599cecb1d341290a0de09c0b23affdbac5ff74e49f5c6583a4ac)



### 2.2 概述类的加载器及类加载过程

![img](https://www.yuque.com/api/filetransfer/images?url=https%3A%2F%2Fgitee.com%2Fvectorx%2FImageCloud%2Fraw%2Fmaster%2Fimg%2F20210507135719.png&sign=ad97219346b0165fdef2b2ffdf1fcbc7452a1a395f3a29d3f56733156252e380)

- 类加载器子系统负责从文件系统或者网络中加载Class文件，class文件在文件开头有特定的文件标识。

- ClassLoader只负责class文件的加载，至于它是否可以运行，则由Execution Engine决定。

- 加载的类信息存放于一块称为方法区的内存空间。除了类的信息外，方法区中还会存放运行时常量池信息，可能还包括字符串字面量和数字常量（这部分常量信息是Class文件中常量池部分的内存映射）

![img](https://www.yuque.com/api/filetransfer/images?url=https%3A%2F%2Fgitee.com%2Fvectorx%2FImageCloud%2Fraw%2Fmaster%2Fimg%2F20210507140219.png&sign=298162dc2eafb00f9364d803f9d600f3a75f42896018d7ad8626c305c666c71c)

- class file存在于本地硬盘上，可以理解为设计师画在纸上的模板，而最终这个模板在执行的时候是要加载到JVM当中来根据这个文件实例化出n个一模一样的实例。

- class file加载到JVM中，被称为DNA元数据模板，放在方法区。

- 在.class文件->JVM->最终成为元数据模板，此过程就要一个运输工具（类装载器Class Loader），扮演一个快递员的角色。

**类加载过程**

```Java
public class HelloLoder {
    public HelloLoder() {
    }

    public static void main(String[] args) {
        System.out.println("谢谢ClassLoder加载我");
        System.out.println("啊吧啊吧啊吧阿巴");
    }
}
```

![img](https://www.yuque.com/api/filetransfer/images?url=https%3A%2F%2Fgitee.com%2Fvectorx%2FImageCloud%2Fraw%2Fmaster%2Fimg%2F20210507140759.png&sign=afc6d8365e98d20e40a84f7a06b7b1d39016769e983105f2f125a7b55f043f58)

![img](https://www.yuque.com/api/filetransfer/images?url=https%3A%2F%2Fgitee.com%2Fvectorx%2FImageCloud%2Fraw%2Fmaster%2Fimg%2F20210507205501.png&sign=08d23987aba2d91d640c0782cf8b58afa9b692a7ad142a81124ca6b908423cf5)

> **加载阶段**

- 通过一个类的全限定名获取定义此类的二进制字节流
- 将这个字节流所代表的静态存储结构转化为方法区（7之前的永久代，7之后的元空间）的运行时数据结构
- 在**内存中生成一个代表这个类的java.lang.Class对象**，作为方法区这个类的各种数据的访问入口



**补充：加载class文件的方式**

- 从`本地系统`中直接加载

- 通过`网络`获取，典型场景：Web Applet

- 从zip`压缩包`中读取，成为日后jar、war格式的基础

- 运行时计算生成，使用最多的是：`动态代理技术`

- 由其他文件生成，典型场景：`JSP应用`

- 从专有`数据库`中提取.class文件，比较少见

- 从`加密文件`中获取，典型的防Class文件被反编译的保护措施



> **链接阶段**

**验证（Verify）**

- 目的在于确保Class文件的字节流中包含信息符合当前虚拟机要求，保证被加载类的正确性，不会危害虚拟机自身安全
- 主要包括四种验证：文件格式验证，源数据验证，字节码验证，符号引用验证

**准备（Prepare）**

- 为类变量分配内存并且设置该类变量的默认初始值，即零值
- 这里不包含用final修饰的static（常量），因为final在编译的时候就会分配了，准备阶段会显式初始化
- **这里不会为实例变量分配初始化**（还未创建对象），类变量会分配在方法区中，而实例变量会随着对象一起分配到Java堆中

**解析（Resolve）**

- 将常量池内的符号引用转换为直接引用的过程
- 事实上，解析操作往往会伴随着JVM在执行完初始化之后再执行。
- 符号引用就是一组符号来描述所引用的目标。符号引用的字面量形式明确定义在《java虚拟机规范》的Class文件格式中。直接引用就是直接指向目标的指针、相对偏移量或一个间接定位到目标的句柄。
- 解析动作主要针对类或接口、字段、类方法、接口方法、方法类型等。对应常量池中的CONSTANT_Class_info，CONSTANT_Fieldref_info、CONSTANT_Methodref_info等。



> **初始化阶段**

- 初始化阶段就是执行类构造器方法<clinit>()的过程。

- 此方法不需定义，是javac编译器自动收集类中的**所有类变量的赋值动作和静态代码块**中的语句合并而来。

- 构造器方法中指令按语句在源文件中出现的顺序执行。

- <clinit>()不同于类的构造器。（关联：构造器是虚拟机视角下的<init>()）

- 若该类具有父类，JVM会保证子类的<clinit>()执行前，父类的<clinit>()已经执行完毕。

- 虚拟机必须保证一个类的<clinit>()方法在多线程下被同步加锁。

```Java
public class ClassInitTest {
    private static int num = 1;
    static {
        num = 2;
        number = 10;
    }
    private static int number = 20;//prepare:number = 0 ---> initial 20 ---->10
    public static void main(String[] args) {
        System.out.println(num);
    }
}
```

![image-20211008213628066](F:\笔记图片\img\image-20211008213628066.png)



**情况一**

```java
public class ClientTest {
    private int a = 1;
    public static void main(String[] args) {
        int b = 2;
        
    }
}
```

![image-20211008214534574](F:\笔记图片\img\image-20211008214534574.png)

所以如果没有类变量的赋值动作和静态代码块是不会生成<clinit>方法的

**任何一个类声明以后，内部至少存在一个构造器，构造器对应 <init>方法**



### 2.3 类加载器分类

- JVM支持两种类型的加载器，分别为**引导类加载器（BootStrap Class Loader）**和**自定义类加载器**
- 从概念上讲，自定义类的加载器一般指的是程序中由开发人员自定义的一类类加载器，但是Java虚拟机规范却没有这么定义，而是 **将所有派生于抽象类ClassLoader的类加载器都划分为自定义类加载器**。

无论类加载器的类型如何划分，在程序中我们最常见的类加载器始终只有3个，如下所示：

![img](https://www.yuque.com/api/filetransfer/images?url=https%3A%2F%2Fgitee.com%2Fvectorx%2FImageCloud%2Fraw%2Fmaster%2Fimg%2F20210507211444.png&sign=5346d86b9d1fafbab6313e578127605463d6162208b5336cc50e2621e00beb4a)

**这里的四者之间的关系是包含关系，不是上层下层，也不是子父类继承的关系**

```java
public class ClassLoaderTest {
    public static void main(String[] args) {
        //获取系统类加载器
        ClassLoader systemClassLoader = ClassLoader.getSystemClassLoader();
        System.out.println(systemClassLoader);

        //通过系统类加载器获取其上层（扩展类）
        ClassLoader extClassLoader = systemClassLoader.getParent();
        System.out.println(extClassLoader);
        //获取扩展类加载器的上层
        ClassLoader parent = extClassLoader.getParent();
        System.out.println(parent);

        //自定义类的加载器默认使用系统类加载器
        ClassLoader classLoader = ClassLoaderTest.class.getClassLoader();
        System.out.println(classLoader);

        //String 是由引导类加载器加载的 ---> Java核心类库都是由引导类加载器加载的
        ClassLoader classLoader1 = String.class.getClassLoader();
        System.out.println(classLoader1);

    }
}
```

![image-20211009201444906](F:\笔记图片\img\image-20211009201444906.png)



#### 2.3.1 虚拟机自带的加载器

**启动类加载器（引导类加载器 Bootstrap ClassLoader）**

- 这个类加载器是由C/C++实现的，嵌套在JVM内部
- 它用来加载Java的核心库（Java_HOME/jre/lib/rt.jar、resources.jar或sun.boot.class.path路径下的内容），用于提供JVM自身需要的类
- 并不继承java.lang.ClassLoader
- 加载扩展类和应用程序类加载器，并指定为他们的父类加载器
- 出于安全考虑，Bootstrap 启动类加载器只加载包名为java javax sun等开头的类



**扩展类加载器（Extension ClassLoader）**

- java语言编写，由sun.misc.Launcher$ExtClassLoader实现。
- 派生于ClassLoader类
- 父类加载器为启动类加载器
- 从java.ext.dirs系统属性所指定的目录中加载类库，或从JDK的安装目录的jre/1ib/ext子目录（扩展目录）下加载类库。**如果用户创建的JAR放在此目录下，也会自动由扩展类加载器加载。**



**应用程序类加载器（系统类加载器，AppClassLoader）**

- Java语言编写，由sun.misc.Launcher$AppClassLoader实现
- 派生于ClassLoader类
- 父类加载器为扩展类加载器
- 它负责加载环境变量（自定义类）classpath或系统属性，java.class.path指定路径下的类
- **该类加载时程序中默认的类加载器**，一般来说，Java应用的类都是由它来完成加载
- 通过ClassLoader#getSystemclassLoader() 方法可以获取到该类加载器



#### 2.3.2 用户自定义的类加载器

- 再Java日常应用程序开发中，类的加载几乎是由上述三种类加载器相互配合执行的，在必要时，我们还可以自定义类加载器，来指定类的加载方式
- 为什么需要自定义类的加载器
  - 隔离加载类
  - 修改类的加载方式
  - 扩展加载源
  - 防止源码泄露



用户自定义类加载器实现步骤：

1. 开发人员可以通过继承抽象类ava.lang.ClassLoader类的方式，实现自己的类加载器，以满足一些特殊的需求

1. 在JDK1.2之前，在自定义类加载器时，总会去继承ClassLoader类并重写loadClass() 方法，从而实现自定义的类加载类，但是在JDK1.2之后已不再建议用户去覆盖loadclass() 方法，而是建议把自定义的类加载逻辑写在findClass()方法中

1. 在编写自定义类加载器时，如果没有太过于复杂的需求，可以直接继承URLClassLoader类，这样就可以避免自己去编写findClass()  方法及其获取字节码流的方式，使自定义类加载器编写更加简洁。



### 2.4 ClassLoader的使用说明

ClassLoader是一个抽象类，其后所有的类加载器都继承自ClassLoader（不包括启动类加载器）

![img](https://www.yuque.com/api/filetransfer/images?url=https%3A%2F%2Fgitee.com%2Fvectorx%2FImageCloud%2Fraw%2Fmaster%2Fimg%2F20210507212318.png&sign=130be0176a03cf690be978a05c1eef52958ffc5a09b5f7ab83fe7c13f582b0af)

![img](https://www.yuque.com/api/filetransfer/images?url=https%3A%2F%2Fgitee.com%2Fvectorx%2FImageCloud%2Fraw%2Fmaster%2Fimg%2F20210507212334.png&sign=86647f239353327f8b359f8f69c0c4b904a54c5c2670cd44510fd00b5653ff1f)



**获取ClassLoader的途径**

-  方式一：获取当前ClassLoader  

```java
clazz.getClassLoader()
```

-  方式二：获取当前线程上下文的ClassLoader  

```java
Thread.currentThread().getContextClassLoader()
```

-  方式三：获取系统的ClassLoader  

```java
ClassLoader.getSystemClassLoader()
```

-  方式四：获取调用者的ClassLoader  

```java
DriverManager.getCallerClassLoader()
```



### 2.5 双亲委派机制

Java虚拟机对class文件采用的是`按需加载`的方式，也就是说当需要使用该类时才会将它的class文件加载到内存生成class对象。而且加载某个类的class文件时，Java虚拟机采用的是`双亲委派模式`，即把请求交由父类处理，它是一种任务委派模式。



> 工作原理

1. 如果一个类加载器收到了类加载请求，它并不会自己先去加载，而是把这个请求委托给父类的加载器去执行
2. 如果父类加载器还存在其他加载器，则进一步向上委托，依次递归，请求最终将到达顶层的启动类加载器；
3. 如果父类加载器可以完成类加载任务，就成功返回，倘若父类加载器无法完成此加载任务，子加载器才会自己去尝试加载，这就是双亲委派模式

![img](https://www.yuque.com/api/filetransfer/images?url=https%3A%2F%2Fgitee.com%2Fvectorx%2FImageCloud%2Fraw%2Fmaster%2Fimg%2F20210507212811.png&sign=13ae045a27d55160ff512d7f5c1956cf0213869dc0c84090b506e23a533c7baf)





当我们加载jdbc.jar 用于实现数据库连接的时候，首先我们需要知道的是 jdbc.jar是基于SPI接口进行实现的，所以在加载的时候，会进行双亲委派，最终从根加载器中加载 SPI核心类，然后在加载SPI接口类，接着在进行反向委派，通过线程上下文类加载器进行实现类jdbc.jar的加载。

![img](https://www.yuque.com/api/filetransfer/images?url=https%3A%2F%2Fgitee.com%2Fvectorx%2FImageCloud%2Fraw%2Fmaster%2Fimg%2F20210507212827.png&sign=dc5348150ee30bdbca34207bc75a8957e9b37c9089bfa2ad4ce9a29eafe085a0)



> 优势：

- 避免类的重复加载
- 保护程序安全，防止核心API被随意篡改



**沙箱安全机制**

自定义String类，但是在加载自定义String类的时候会率先使用引导类加载器加载，而引导类加载器在加载的过程中会先加载jdk自带的文件（rt.jar包中java\lang\String.Class），报错信息说没有main方法就是因为加载的是rt.jar包中的String类。这样可以保证对Java核心源代码的保护，这就是**沙箱安全机制**



### 2.6 其他

- 在JVM中表示两个Class对象是否为同一个类存在两个必要条件：
  - 类的完整类名必须一致，包括包名
  - 加载这个类的ClassLoader（指ClassLoader实例对象）必须相同

- 换句话说，在JVM中，即使这两个类对象（Class对象）来源同一个Class文件，被同一个虚拟机所加载，但只要加载它们的类加载器实例对象不同，那么这两个类对象也是不相等的



**对类加载器的引用**

JVM必须知道一个类型是由启动加载器加载的还是由用户类加载器加载的。**如果一个类型是由用户类加载器加载的，那么JVM会将这个类加载器的一个引用作为类型信息的一部分保存在方法区中。**当解析一个类型到另一个类型的引用的时候，JVM需要保证这两个类型的类加载器是相同的。



**类的主动使用和被动使用**

Java程序对类的使用方式分为：主动使用和被动使用。



主动使用，又分为七种情况：

-  创建类的实例 

-  访问某个类或接口的静态变量，或者对该静态变量赋值 

-  调用类的静态方法 

-  反射（比如：Class.forName（"com.atguigu.Test"）） 

-  初始化一个类的子类 

-  Java虚拟机启动时被标明为启动类的类 

-  JDK 7 开始提供的动态语言支持：
  java.lang.invoke.MethodHandle实例的解析结果
  REF_getStatic、REF_putStatic、REF_invokeStatic句柄对应的类没有初始化，则初始化 



除了以上七种情况，其他使用Java类的方式都被看作是**对类的被动使用**，都**不会导致类的初始化**。



## 3.运行时数据区概述及线程

### 3.1 运行时数据区

当我们通过前面的：类的加载-> 验证 -> 准备 -> 解析 -> 初始化 这几个阶段完成后，就会用到执行引擎对我们的类进行使用，同时执行引擎将会使用到我们运行时数据区

![img](https://www.yuque.com/api/filetransfer/images?url=https%3A%2F%2Fgitee.com%2Fvectorx%2FImageCloud%2Fraw%2Fmaster%2Fimg%2F20210509173051.png&sign=5e61e9ad4c4fee1452f21e1f1521de1faefba049a3f3e2b0a690eaa67ef3ea95)



我们把大厨后面的东西（切好的菜，刀，调料），比作是运行时数据区。而厨师可以类比于执行引擎，将通过准备的东西进行制作成精美的菜品

![image-20211013204727322](F:\笔记图片\img\image-20211013204727322.png)



内存是非常重要的系统资源，是硬盘和CPU的中间仓库及桥梁，承载着操作系统和应用程序的实时运行JVM内存布局规定了Java在运行过程中内存申请、分配、管理的策略，保证了JVM的高效稳定运行。**不同的JVM对于内存的划分方式和管理机制存在着部分差异**。结合JVM虚拟机规范，来探讨一下经典的JVM内存布局。

![image-20211013205908790](F:\笔记图片\img\image-20211013205908790.png)

![img](https://www.yuque.com/api/filetransfer/images?url=https%3A%2F%2Fgitee.com%2Fvectorx%2FImageCloud%2Fraw%2Fmaster%2Fimg%2F20210509173114.png&sign=e8fbfedc5d6d6756987e4670915f9859b7ff946653866bd60c97da1704fd7f73)



Java虚拟机定义了若干种程序运行期间会使用到的运行时数据区，其中有一些会随着虚拟机启动而创建，随着虚拟机退出而销毁。另外一些则是与线程一一对应的，这些与线程一一对应的数据区域会随着线程开始和结束而创建和销毁

灰色为线程私有的，红色的为多个线程共享的。即

- 每个线程：独立包括程序计数器，本地方法栈，Java虚拟机栈
- 线程空间共享：堆，对外内存（永久代或元空间，代码缓存）

![image-20211013211214155](F:\笔记图片\img\image-20211013211214155.png)



每个JVM只有一个Runtime实例。即为运行时环境，相当于内存结构的中间的那个框框：运行时环境。

![img](https://www.yuque.com/api/filetransfer/images?url=https%3A%2F%2Fgitee.com%2Fvectorx%2FImageCloud%2Fraw%2Fmaster%2Fimg%2F20210509173411.png&sign=f765ecefe2a5f6a02ae2c3c4d1ec6149b4052a295227e674866b6cbca0c2ac19)



### 3.2 线程

线程是一个程序里的运行单元。JVM允许一个应用有多个线程并行的执行。 在Hotspot JVM里，每个线程都与操作系统的本地线程直接映射。



当一个Java线程准备好执行以后，此时一个操作系统的本地线程也同时创建。Java线程执行终止后，本地线程也会回收。



操作系统负责所有线程的安排调度到任何一个可用的CPU上。一旦本地线程初始化成功，它就会调用Java线程中的run()方法。



### 3.3 程序计数器（PC寄存器）

VM中的程序计数寄存器（Program Counter Register）中，Register的命名源于CPU的寄存器，寄存器存储指令相关的现场信息。CPU只有把数据装载到寄存器才能够运行。这里，并非是广义上所指的物理寄存器，或许将其翻译为PC计数器（或指令计数器）会更加贴切（也称为程序钩子），并且也不容易引起一些不必要的误会。**JVM中的PC寄存器是对物理PC寄存器的一种抽象模拟。**



**作用**

PC寄存器用来存储指向下一条指令的地址，也即将要执行的指令代码。由执行引擎读取下一条命令



![img](https://www.yuque.com/api/filetransfer/images?url=https%3A%2F%2Fgitee.com%2Fvectorx%2FImageCloud%2Fraw%2Fmaster%2Fimg%2F20210509175630.png&sign=245e01d0030362b5a55a62da2111a283b872749d5bbc36991f9e3f80afba2024)



- 它是很小的一块很小的内存空间，小到可以几乎忽略不计。也是运行速度最快的存储区域
- 在JVM规范中，每个线程都有自己的程序计数器，是线程私有的，生命周期与线程的生命周期保持一致
- 任何时间一个线程都只有一个方法在执行，也就是所谓的当前方法。程序计数器会存储当前线程正在执行的Java方法的JVM指令地址；或者，如果实在执行native方法，则是未指定值
- 它是程序控制流的指示器，分支、循环、跳转、异常处理、线程恢复等基础功能都需要依赖这个计数器来完成。
- 字节码解释器工作时就是通过改变这个计数器的值来选取下一条需要执行的字节码指令。
- 它是唯一一个在Java虚拟机规范中没有规定任何OutofMemoryError情况的区域。



> 案例

```java
public class PCRegisterTest {
    public static void main(String[] args) {
        int i = 10;
        int j = 20;
        int k = i + j;

        String s = "abc";
        System.out.println(i);
        System.out.println(k);
    }
}
```

![image-20211015203705221](F:\笔记图片\img\image-20211015203705221.png)



**使用PC寄存器存储字节码指令地址有什么用呢？为什么使用PC寄存器记录当前线程的执行地址呢？**

因为CPU不停的切换各个线程，这时候切换回来之后，就得知道接着从哪开始继续执行

JVM的字节码解释器就需要通过改变PC寄存器的值来明确下一条应该执行什么样的字节码指令

![img](https://www.yuque.com/api/filetransfer/images?url=https%3A%2F%2Fgitee.com%2Fvectorx%2FImageCloud%2Fraw%2Fmaster%2Fimg%2F20210509175648.png&sign=6f63db00880ba75813621a570405cd0f713ccff09eb87adebde7e128b1655b0a)

**PC寄存器为什么被设置为线程私有？**

我们都知道所谓的多线程在一个特定的时间段内只会执行其中某一个线程的方法，CPU会不停地做任务切换，这样必然导致经常中断或恢复，如何保证分毫无差呢？**为了能够准确地记录各个线程正在执行的当前字节码指令地址，最好的办法自然是为每一个线程都分配一个PC寄存器**，这样一来各个线程之间便可以进行独立计算，从而不会出现相互干扰的情况。



由于CPU时间片轮限制，众多线程在并发执行过程中，任何一个确定的时刻，一个处理器或者多核处理器中的一个内核，只会执行某个线程中的一条指令。



这样必然导致经常中断或恢复，如何保证分毫无差呢？每个线程在创建后，都会产生自己的程序计数器和栈帧，程序计数器在各个线程之间互不影响。



**CPU时间片**



CPU时间片即CPU分配给各个程序的时间，每个线程被分配一个时间段，称作它的时间片。



在宏观上：俄们可以同时打开多个应用程序，每个程序并行不悖，同时运行。



但在微观上：由于只有一个CPU，一次只能处理程序要求的一部分，如何处理公平，一种方法就是引入时间片，每个程序轮流执行。

![img](https://www.yuque.com/api/filetransfer/images?url=https%3A%2F%2Fgitee.com%2Fvectorx%2FImageCloud%2Fraw%2Fmaster%2Fimg%2F20210509175655.png&sign=cc657e83afb995a47f32136846dd9c3d82296e743196868f6acaf57287ac28dd)



### 3.4 虚拟机栈

#### 3.4.1 虚拟机的概述

由于跨平台性的设计，Java的指令都是根据栈来设计的。不同平台CPU架构不同，所以不能设计为基于寄存器的。



**优点是跨平台，指令集小，编译器容易实现，缺点是性能下降，实现同样的功能需要更多的指令**



> 内存中的栈与堆

**栈是运行时的单位，而堆是存储的单位**

- 栈解决程序的运行问题，即程序如何执行，或者说如何处理数据
- 堆解决的是数据存储的问题，即数据怎么放，放在哪儿



**Java虚拟机栈是什么？**

- Java虚拟机栈，早期也叫Java 栈。每个线程在创建时都会创建一个虚拟机栈，其内部保存一个个的栈帧，对应着一次次的Java方法调用
- 是线程私有的



**生命周期**

- 生命周期与线程一致



**作用**

- 主管Java程序的运行，它保存方法的局部变量（8种基本数据类型、对象的引用地址），部分结果，并参与方法的调用和返回

![image-20211015215324132](F:\笔记图片\img\image-20211015215324132.png)



**栈的特点（优点）**

- 栈是一种快速有效的分配存储方式，访问速度仅次于程序计数器
- JVM直接堆Java栈的操作只有两个：
  - 每个方法执行，伴随着进栈
  - 执行结束后的出栈工作
- 对于栈来说不存在垃圾回收问题



###### 面试题：开发中遇到哪些异常？

**栈中可能出现的异常**

Java 虚拟机规范允许Java栈的大小是动态的或者是固定不变的。

-  如果采用固定大小的Java虚拟机栈，那每一个线程的Java虚拟机栈容量可以在线程创建的时候独立选定。如果线程请求分配的栈容量超过Java虚拟机栈允许的最大容量，Java虚拟机将会抛出一个StackOverflowError 异常。 

-  如果Java虚拟机栈可以动态扩展，并且在尝试扩展的时候无法申请到足够的内存，或者在创建新的线程时没有足够的内存去创建对应的虚拟机栈，那Java虚拟机将会抛出一个 OutOfMemoryError 异常。 



**设置栈内存大小**

我们可以使用参数-Xss来设置线程的最大栈空间，栈的大小直接决定了函数调用的最大可达深度



#### 3.4.2 栈的存储结构

- 每个线程都有自己的栈，**栈中的数据都是以栈帧的格式存在**

- 在这个线程上正在执行的每个方法都各自对应一个栈帧
- 栈帧是一个内存区块，是一个数据集，维系着方法执行过程中的各种数据信息



#### 3.4.3 栈运行原理

- JVM直接对Java栈的操作有两个，就是对栈帧的压栈和出栈，遵循先进后出和后进先出的原则
- 在一条活动线程中，一个时间点上，只会有一个活动的栈帧。即只有当前正在执行的方法的栈帧（栈顶栈帧）是有效的，**这个栈帧被称为当前栈帧**（Current Frame），与当前栈帧相对应的方法就是**当前方法**（Current Method），定义这个方法的类就是**当前类**（Current Class）。
- 执行引擎运行的所有字节码指令只针对当前栈帧进行操作
- 如果在该方法中调用了其他方法，对应的新的栈帧就会被创建出来，放在栈的顶端，称为新的当前栈帧

![img](https://www.yuque.com/api/filetransfer/images?url=https%3A%2F%2Fgitee.com%2Fvectorx%2FImageCloud%2Fraw%2Fmaster%2Fimg%2F20210509190221.png&sign=d9052977c32da931af7239b76885d8a469edd12130f3c274dc22157e6988ef9f)



- 不同的线程中所包含的栈帧是不允许存在相互引用的，即不可能在一个栈帧中引用另外一个线程的栈帧
- 如果当前方法调用了其他方法，方法返回之际，当前栈帧会传回此方法的执行结果给前一个栈帧，接着，虚拟机会丢弃当前栈帧，使得前一个栈帧重新成为当前栈帧
- Java方法有两种返回函数的方式，一种是正常的函数返回，使用return指令；另一种是抛出异常，不管使用哪种方式，都会导致栈帧弹出



#### 3.4.4 栈桢的内部结构

每个栈帧中存储着：

- 局部变量表（Local Variables）
- 操作数栈(Operand Stack/表达式栈) 
- 动态链接（指向运行时常量池的方法引用）
- 方法返回值
- 一些附加信息

![img](https://www.yuque.com/api/filetransfer/images?url=https%3A%2F%2Fgitee.com%2Fvectorx%2FImageCloud%2Fraw%2Fmaster%2Fimg%2F20210509190228.png&sign=0dc7f9e12f5277b5e3a1ec7490fb7f14b51fbf06c442e054744b9dea0457a8e5)



并行每个线程下的栈都是私有的，因此每个线程都有自己各自的栈，并且每个栈里面都有很多栈帧，栈帧的大小主要由局部变量表 和 操作数栈决定的

![img](https://www.yuque.com/api/filetransfer/images?url=https%3A%2F%2Fgitee.com%2Fvectorx%2FImageCloud%2Fraw%2Fmaster%2Fimg%2F20210509190232.png&sign=0b4ab2082957f9df79a343a365c0ee77ad3f8e937b4c9bad55ab5be9f38d1423)



**局部变量表**

局部变量表也被称之为局部变量数组或本地变量表

- **定义为一个数字数组，主要用于存储方法参数和定义在方法体内的局部变量**，这些数据类型包括各类基本数据类型、对象引用（reference），以及returnAddress类型。 

- 由于局部变量表是建立在线程的栈上，是线程的私有数据，因此不存在数据安全问题 

- **局部变量表所需的容量大小是在编译期确定下来的**，并保存在方法的Code属性的maximum local variables数据项中。在方法运行期间是不会改变局部变量表的大小的。 

- **方法嵌套调用的次数由栈的大小决定**。一般来说，栈越大，方法嵌套调用次数越多。对一个函数而言，它的参数和局部变量越多，使得局部变量表膨胀，它的栈帧就越大，以满足方法调用所需传递的信息增大的需求。进而函数调用就会占用更多的栈空间，导致其嵌套调用次数就会减少。 

-  **局部变量表中的变量只在当前方法调用中有效**，在方法执行使，虚拟机通过使用局部变量表完成参数值到参数列表的传递过程。**当方法调用结束后，随着方法栈帧的销毁，局部变量表也会随之销毁**



#### 3.4.5 关于slot的理解

- 局部变量表，最基本的存储单元是Slot（变量槽） 

-  参数值的存放总是在局部变量数组的index0开始，到数组长度-1的索引结束。 

-  局部变量表中存放编译期可知的各种基本数据类型（8种），引用类型（reference），returnAddress类型的变量。 

-  在局部变量表里，32位以内的类型只占用一个slot（包括returnAddress类型），64位的类型（long和double）占用两个slot。 

-  byte、short、char 在存储前被转换为int，boolean也被转换为int，0表示false，非0表示true。 

-  JVM会为局部变量表中的每一个Slot都分配一个访问索引，通过这个索引即可成功访问到局部变量表中指定的局部变量值 

-  当一个实例方法被调用的时候，它的方法参数和方法体内部定义的局部变量将会按照顺序被复制到局部变量表中的每一个slot上 

-  如果需要访问局部变量表中一个64bit的局部变量值时，只需要使用前一个索引即可。（比如：访问long或doub1e类型变量） 

-  如果当前帧是由**构造方法或者实例方法**创建的，那么**该对象引用this将会存放在index为0的slot处，其余的参数按照参数表顺序继续排列**。 

![img](https://www.yuque.com/api/filetransfer/images?url=https%3A%2F%2Fgitee.com%2Fvectorx%2FImageCloud%2Fraw%2Fmaster%2Fimg%2F20210509190245.png&sign=50fba3cb1775c74899783f72558d91d04cac4d43bb237de462c1d33c5fb8cf8e)

**栈帧中的局部变量表中的槽位是可以重用的**，如果一个局部变量过了其作用域，那么在其作用域之后申明的新的局部变就很有可能会复用过期局部变量的槽位，从而达到节省资源的目的。

```java
public class SlotTest {
    public void test() {
        int a = 0;
        {
            int b = 0;
            b = a + 1;
        }
        //此时变量c使用之前已经销毁的变量b占据的slot的位置
        int c = a + 1;
    }
}
```



**静态变量和局部变量的对比**

成员变量：

- 类变量表有两次赋值的机会，第一次是在**准备**阶段，为类变量默认赋值；第二次是在**初始化**阶段为类变量显式赋值，即静态代码块赋值
- 实例变量随着对象的创建会在堆空间中分配实例变量的空间，并进行默认赋值



局部变量：

- 在使用前是必须要显示赋值的，否则编译不通过





> 补充说明

在栈帧中，与性能调优关系最为密切的部分就是前面提到的局部变量表。在方法执行时，虚拟机使用局部变量表完成方法的传递。



**局部变量表中的变量也是重要的垃圾回收根节点，只要被局部变量表中直接或间接引用的对象都不会被回收。**



#### 3.4.6 操作数栈

- 每一个独立的栈除了包含局部变量表之外，还包含一个后进先出的操作数栈，也可以称之为表达式栈

- 操作数栈，在方法执行的过程中，根据字节码指令，往栈中写入数据或提取数据，即入栈/出栈

  - 某些字节码指令将值压入操作数栈，其余的字节码指令将操作数取出栈。使用它们后再把结果压入栈

  - 比如：复制，交换，求和等操作

    ![image-20211019110308861](F:\笔记图片\img\image-20211019110308861.png)

- 如果被调用的方法有返回值的话，其返回值将会被压入当前栈帧的操作数栈中，并更新PC寄存器中下一条需要执行的字节码指令
- 操作数栈中元素的数据类型必须与字节码指令的序列严格匹配，这由编译器在编译期间进行验证，同时在类的加载过程中的类检验阶段的数据流分析阶段再次验证
- 另外，我们说Java虚拟机的**解释引擎是基于栈的执行引擎**，其中的栈指的就是操作数栈





- 操作数栈，主要用于**保存计算过程的中间结果，同时作为计算过程中变量临时的存储空间。**



- 操作数栈就是JVM执行引擎的一个工作区，当一个方法刚开始执行的时候，一个新的栈帧也会随之被创建出来，这个方法的**操作数栈是空的**。



- 每一个操作数栈都会拥有一个明确的栈深度用于存储数值，其所需的最大深度在编译期就定义好了，保存在方法的Code属性中，为max_stack的值。



栈中的任何一个元素都是可以任意的Java数据类型

- 32bit的类型占用一个栈单位深度

- 64bit的类型占用两个栈单位深度



- 操作数栈并非采用索引的方式来进行数据访问的，而只能通过标准的入栈和出栈操作来完成一次数据访问



> 代码追踪

```java
public class OperandTest {
    public void testAddOperation() {
        byte i = 15;
        int j = 8;
        int k = i + j;
    }
}
```

```java
 0 bipush 15
 2 istore_1
 3 bipush 8
 5 istore_2
 6 iload_1
 7 iload_2
 8 iadd
 9 istore_3
10 return

```

![img](https://www.yuque.com/api/filetransfer/images?url=https%3A%2F%2Fgitee.com%2Fvectorx%2FImageCloud%2Fraw%2Fmaster%2Fimg%2F20210509194808.png&sign=e39d411cdf65df62c27378f65d3bfe6fa5263ba7b41e9bc8b09cf52c197ce88e)

![img](https://www.yuque.com/api/filetransfer/images?url=https%3A%2F%2Fgitee.com%2Fvectorx%2FImageCloud%2Fraw%2Fmaster%2Fimg%2F20210509194813.png&sign=d9e568b58de4d07a53a011c2c9716c1378d4e3ec1f07e5304c29684ac0778254)

![img](https://www.yuque.com/api/filetransfer/images?url=https%3A%2F%2Fgitee.com%2Fvectorx%2FImageCloud%2Fraw%2Fmaster%2Fimg%2F20210509194816.png&sign=fdf5e5c4cbcc66bed0ef71f8c9894c8d9075cab6c7d40ba46cc2208bbd3bff71)

![img](https://www.yuque.com/api/filetransfer/images?url=https%3A%2F%2Fgitee.com%2Fvectorx%2FImageCloud%2Fraw%2Fmaster%2Fimg%2F20210509194819.png&sign=af84a5541405c35d86535175438f275f57ec62152dcc5966dae151597e947d11)

![img](https://www.yuque.com/api/filetransfer/images?url=https%3A%2F%2Fgitee.com%2Fvectorx%2FImageCloud%2Fraw%2Fmaster%2Fimg%2F20210509194822.png&sign=b10c314c105b03705b9ea73e78034e44e0f024b2875344f16247e3d174e3e225)

![img](https://www.yuque.com/api/filetransfer/images?url=https%3A%2F%2Fgitee.com%2Fvectorx%2FImageCloud%2Fraw%2Fmaster%2Fimg%2F20210509194824.png&sign=7a63fcf1bb198e6c73b75eaafc35ff7af047ad74e9a9d4050a16fde08bdee0d6)

![img](https://www.yuque.com/api/filetransfer/images?url=https%3A%2F%2Fgitee.com%2Fvectorx%2FImageCloud%2Fraw%2Fmaster%2Fimg%2F20210509194827.png&sign=781092a0f280ddef714f8d9a60f2569c9661d59d5839ea29fe26b15bd73f85ec)

![img](https://www.yuque.com/api/filetransfer/images?url=https%3A%2F%2Fgitee.com%2Fvectorx%2FImageCloud%2Fraw%2Fmaster%2Fimg%2F20210509194829.png&sign=ad034921f6bd4c9e9f7ed55b9ed350d2245cfde449bea763d026a6af5f9ec5e5)



#### 3.4.7 栈顶缓存技术

前面提过，基于栈式架构的虚拟机所使用的零地址指令更加紧凑，但完成一项操作的时候必然需要**使用更多的入栈和出栈指令**，这同时也就意味着将需要更多的指令分派（instruction dispatch）次数和内存读/写次数。



由于操作数是存储在内存中的，因此频繁地执行内存读/写操作必然会影响执行速度。为了解决这个问题，HotSpot JVM的设计者们提出了栈顶缓存（Tos，Top-of-Stack Cashing）技术，**将栈顶元素全部缓存在物理CPU的寄存器中，以此降低对内存的读/写次数，提升执行引擎的执行效率**。



#### 3.4.8 动态链接（指向运行时方法的常量池的引用）

动态链接、方法返回地址、附加信息 ： 有些地方被称为帧数据区



每一个栈帧内部都包含一个**指向运行时常量池中该栈帧所属方法的引用**。包含这个引用的目的就是为了支持当前方法的代码能够实现动态链接（Dynamic Linking）。比如：invokedynamic指令



在Java源文件被编译到字节码文件中时，**所有的变量和方法引用都作为符号引用**（Symbolic Reference）保存在class文件的常量池里。比如：描述一个方法调用了另外的其他方法时，就是通过常量池中指向方法的符号引用来表示的，那么**动态链接的作用就是为了将这些符号引用转换为调用方法的直接引用。**

![image-20211019145701933](F:\笔记图片\img\image-20211019145701933.png)



> 为什么需要运行时常量池？

常量池的作用：就是为了提供一些符号和常量，便于指令的识别



##### 1. 方法的调用

在JVM中，将符号引用转换为调用方法的直接引用与方法的绑定机制相关



**静态链接**

当一个字节码文件被装载进JVM内部时，如果被调用的**目标方法在编译期可知，且运行期保持不变时**，这种情况下降调用方法的符号引用转换为直接引用的过程称之为静态链接



**动态链接**

如果被**调用的方法在编译期无法被确定下来，只能够在程序运行期**将调用的方法的符号转换为直接引用，由于这种引用转换过程具备动态性，因此也被称之为动态链接。



静态链接和动态链接不是名词，而是动词，这是理解的关键。



对应的方法的绑定机制为：早期绑定（Early Binding）和晚期绑定（Late Binding）**。绑定是一个字段、方法或者类在符号引用被替换为直接引用的过程，这仅仅发生一次。**



**早期绑定**

早期绑定就是指被调用的目标方法如果在编译期可知，且运行期保持不变时，即可将这个方法与所属的类型进行绑定，这样一来，由于明确了被调用的目标方法究竟是哪一个，因此也就可以使用静态链接的方式将符号引用转换为直接引用。



**晚期绑定**

如果被调用的方法在编译期无法被确定下来，只能够在程序运行期根据实际的类型绑定相关的方法，这种绑定方式也就被称之为晚期绑定。



##### 2. 虚方法和非虚方法

- 如果方法在编译期就确定了具体的调用版本，这个版本在运行时是不可变的。这样的方法称为非虚方法。



- 静态方法、私有方法、final方法、实例构造器、父类方法都是非虚方法。其他方法称为虚方法。



虚拟机中提供了以下几条方法调用指令：



> 普通调用指令：

- `invokestatic：调用静态方法，解析阶段确定唯一方法版本`

- `invokespecial：调用方法、私有及父类方法，解析阶段确定唯一方法版本`

- invokevirtual：调用所有虚方法

- invokeinterface：调用接口方法



> 动态调用指令：

- invokedynamic：动态解析出需要调用的方法，然后执行



前四条指令固化在虚拟机内部，方法的调用执行不可人为干预，而invokedynamic指令则支持由用户确定方法版本。**其中invokestatic指令和invokespecial指令调用的方法称为非虚方法，其余的（fina1修饰的除外）称为虚方法**。



**关于invokednamic指令**



-  JVM字节码指令集一直比较稳定，一直到Java7中才增加了一个invokedynamic指令，这是Java为了实现「动态类型语言」支持而做的一种改进。 

-  但是在Java7中并没有提供直接生成invokedynamic指令的方法，需要借助ASM这种底层字节码工具来产生invokedynamic指令。直到Java8的Lambda表达式的出现，invokedynamic指令的生成，在Java中才有了直接的生成方式。 

-  Java7中增加的动态语言类型支持的本质是对Java虚拟机规范的修改，而不是对Java语言规则的修改，这一块相对来讲比较复杂，增加了虚拟机中的方法调用，最直接的受益者就是运行在Java平台的动态语言的编译器。 



> 动态类型语言和静态类型语言



动态类型语言和静态类型语言两者的区别就在于对类型的检查是在编译期还是在运行期，满足前者就是静态类型语言，反之是动态类型语言。



说的再直白一点就是，**静态类型语言是判断变量自身的类型信息；动态类型语言是判断变量值的类型信息，变量没有类型信息，变量值才有类型信息，这是动态语言的一个重要特征。**



##### 3.方法重写的本质



**Java 语言中方法重写的本质：**

1. 找到操作数栈顶的第一个元素所执行的对象的实际类型，记作C。

1. 如果在类型C中找到与常量中的描述符合简单名称都相符的方法，则进行访问权限校验，如果通过则返回这个方法的直接引用，查找过程结束；如果不通过，则返回java.lang.IllegalAccessError 异常。

1. 否则，按照继承关系从下往上依次对C的各个父类进行第2步的搜索和验证过程。

1. 如果始终没有找到合适的方法，则抛出java.1ang.AbstractMethodsrror异常。



**IllegalAccessError介绍**

程序试图访问或修改一个属性或调用一个方法，这个属性或方法，你没有权限访问。一般的，这个会引起编译器异常。这个错误如果发生在运行时，就说明一个类发生了不兼容的改变。





##### 4.方法的调用：虚方法表

在面向对象的编程中，会很频繁的使用到动态分派，如果在每次动态分派的过程中都要重新在类的方法元数据中搜索合适的目标的话就可能影响到执行效率。因此，为了提高性能，JVM采用在类的方法区建立一个虚方法表 （virtual method table）（非虚方法不会出现在表中）来实现。使用索引表来代替查找。



每个类中都有一个虚方法表，表中存放着各个方法的实际入口。



虚方法表是什么时候被创建的呢？



虚方法表会在类加载的链接阶段被创建并开始初始化，类的变量初始值准备完成之后，JVM会把该类的方法表也初始化完毕。

![image-20211019163610698](F:\笔记图片\img\image-20211019163610698.png)





#### 3.4.9 方法返回地址

- 存储该方法的PC寄存器的值
- 一个方法结束，有两种方式
  - 正常执行完成
  - 出现未处理的异常，非正常退出
- 无论通过哪种方式退出，在方法退出后都返回到该方法被调用的位置。方法正常退出时，**调用者的pc计数器的值作为返回地址，即调用该方法的指令的下一条指令的地址。**而通过异常退出的，返回地址是要通过异常表来确定，栈帧中一般不会保存这部分信息。
- 本质上，方法的退出就是当前栈帧出栈的过程。此时，需要恢复上层方法的局部变量表，操作数栈，将返回值压入调用者栈帧的操作数栈，设置PC寄存器值等，让调用者方法继续执行下去
- `正常完成出口和异常完成出口的区别在于：通过异常完成出口退出不会给他的上层调用者产生任何返回值`



#### 3.4.10 一些附加信息

- 栈帧中还允许携带与Java虚拟机实现相关的一些附加信息，例如，对程序调试提供支持的信息



### 3.5 本地方法栈

#### 本地方法接口

简单地讲，一个Native Method是一个Java调用非Java代码的接囗。一个Native Method是这样一个Java方法：该方法的实现由非Java语言实现，比如C。这个特征并非Java所特有，很多其它的编程语言都有这一机制，比如在C中，你可以用extern "c" 告知c编译器去调用一个c的函数。

在定义一个native method时，并不提供实现体（有些像定义一个Java interface），因为其实现体是由非java语言在外面实现的。



本地接口的作用是融合不同的编程语言为Java所用，它的初衷是融合C/C++程序。

![img](https://www.yuque.com/api/filetransfer/images?url=https%3A%2F%2Fgitee.com%2Fvectorx%2FImageCloud%2Fraw%2Fmaster%2Fimg%2F20210509205225.png&sign=937e009fea7a9ddae222816c8e85ffdd6335bd805edbe7775d6c4b0f6763aee5)



> **为什么使用native方法？**

ava使用起来非常方便，然而有些层次的任务用Java实现起来不容易，或者我们对程序的效率很在意时，问题就来了。

- **与Java环境的交互**

`有时Java应用需要与Java外面的环境交互，这是本地方法存在的主要原因`。你可以想想Java需要与一些底层系统，如操作系统或某些硬件交换信息时的情况。本地方法正是这样一种交流机制：它为我们提供了一个非常简洁的接口，而且我们无需去了解Java应用之外的繁琐的细节。

- **与操作系统的交互**

JVM支持着Java语言本身和运行时库，它是Java程序赖以生存的平台，它由一个解释器（解释字节码）和一些连接到本地代码的库组成。然而不管怎样，它毕竟不是一个完整的系统，它经常依赖于一底层系统的支持。这些底层系统常常是强大的操作系统。`通过使用本地方法，我们得以用Java实现了jre的与底层系统的交互，甚至JVM的一些部分就是用c写的`。还有，如果我们要使用一些Java语言本身没有提供封装的操作系统的特性时，我们也需要使用本地方法。

- **Sun's Java**

`Sun的解释器是用C实现的，这使得它能像一些普通的C一样与外部交互`。jre大部分是用Java实现的，它也通过一些本地方法与外界交互。例如：类java.lang.Thread的setPriority()方法是用Java实现的，但是它实现调用的是该类里的本地方法setPriority()。这个本地方法是用C实现的，并被植入JVM内部，在Windows 95的平台上，这个本地方法最终将调用Win32 setPriority() ApI。这是一个本地方法的具体实现由JVM直接提供，更多的情况是本地方法由外部的动态链接库（external dynamic link library）提供，然后被JVw调用。

- **现状**

目前该方法使用的越来越少了，除非是与硬件有关的应用，比如通过Java程序驱动打印机或者Java系统管理生产设备，在企业级应用中已经比较少见。因为现在的异构领域间的通信很发达，比如可以使用Socket通信，也可以使用Web Service等等，不多做介绍。

- Java虚拟机栈用于管理Java方法的调用，而本地方法栈用于管理本地方法的调用

- 本地方法栈，也是线程私有的

- 允许被实现成固定或者是可扩展的内存大小（在内存溢出方面是相同的）

  - 如果线程请求分配的栈容量超过本地方法栈允许的最大容量，Java虚拟机将会抛出一个StackOverflowError异常
  - 如果本地方法栈可以动态扩展，并且在尝试扩展的时候无法申请到足够的内存，或者在创建新的线程时没有足够的内存去创建对应的本地方法栈

- 本地方法使用C语言实现的

- 它的具体做法是Native Method Stack中登记native方法，在Execution Engine 执行时加载本地方法库。

  ![img](https://www.yuque.com/api/filetransfer/images?url=https%3A%2F%2Fgitee.com%2Fvectorx%2FImageCloud%2Fraw%2Fmaster%2Fimg%2F20210509205255.png&sign=950e4118fe781fd67e7f1f705600dfcc8079567efebde76da2d774837954c53d)



当某个线程调用一个本地方法时，它就进入了一个全新的并且不再受虚拟机限制的世界。它和虚拟机拥有同样的权限。



- 本地方法可以通过本地方法接口来访问虚拟机内部的运行时数据区。

- 它甚至可以直接使用本地处理器中的寄存器

- 直接从本地内存的堆中分配任意数量的内存。



并不是所有的JVM都支持本地方法。因为Java虚拟机规范并没有明确要求本地方法栈的使用语言、具体实现方式、数据结构等。如果JVM产品不打算支持native方法，也可以无需实现本地方法栈。

在Hotspot JVM中，直接将本地方法栈和虚拟机栈合二为一。



### 3.6 堆空间

#### 3.6.1 堆的核心概述

- 一个进程对应一个JVM实例，一个进程有多个线程，多个线程共享堆空间和方法区

- 一个JVM实例只存在一个堆内存，堆也是Java内存管理的核心区域
- Java堆区在JVM启动的时候就被创建了，其空间大小也就确定了。是JVM管理的最大一块内存空间
  - 堆内存大小是可以调节的
- Java虚拟机规范规定，堆可以处于物理上不连续的内存空间中，但在逻辑上它应该被视为连续的
- 所有的线程共享Java堆，**在这里还可以划分线程私有的缓冲区**（Thread Local Allocation Buffer,TALB）



- 《Java虚拟机规范》中对Java堆的描述是：所有的对象实例以及数组都应当在运行时分配在堆上。（`The heap is the run-time data area from which memory for all class instances and arrays is allocated`）



- 数组和对象可能永远不会存储在栈上，因为栈帧中保存引用，这个引用指向对象或者数组在堆中的位置。



- 在方法结束后，堆中的对象不会马上被移除，仅仅在垃圾收集的时候才会被移除。



- 堆，是GC（Garbage Collection，垃圾收集器）执行垃圾回收的重点区域。

![img](https://www.yuque.com/api/filetransfer/images?url=https%3A%2F%2Fgitee.com%2Fvectorx%2FImageCloud%2Fraw%2Fmaster%2Fimg%2F20210510105611.png&sign=917b4ccdedbfc5d1ff3de9e1204de54c38b461f82d0fbb9dca4ab29ebe03830e)



#### 3.6.2 堆内存细分

现代垃圾收集器大部分都基于分代收集理论设计，对空间细分为

- Java7及之前堆内存**逻辑上**分为三部分：新生区 + 养老区 + 永久区

  - Young Generation Space `新生区`  Young/New
    - 又被划分为Eden区和Survivor区
  - Tenure generation space ` 养老区`  Old/Tenure
  - Permanent Space `永久区`

- Java 8及之后堆内存逻辑上分为三部分：新生区+养老区+元空间

  - oung Generation Space `新生区` Young/New 又被划分为Eden区和Survivor区

  - Tenure generation space `养老区` Old/Tenure

  - Meta Space `元空间 `Meta

> 约定：新生区 <=>新生代   养老区<=>老年代<=>老年区     永久区<=>永久代



**JDK1.7**

![img](https://www.yuque.com/api/filetransfer/images?url=https%3A%2F%2Fgitee.com%2Fvectorx%2FImageCloud%2Fraw%2Fmaster%2Fimg%2F20210510105619.png&sign=4890a8ec82867ea2718de9f8ed335485c392a0a9583b0e31ec0bf70d61c8faa2)

**JDK1.8**

![img](https://www.yuque.com/api/filetransfer/images?url=https%3A%2F%2Fgitee.com%2Fvectorx%2FImageCloud%2Fraw%2Fmaster%2Fimg%2F20210510105627.png&sign=39407e92b3c87f852f2ff1b9a8f23a407a82462063c56cdd5313d9845c1f8b9c)



#### 3.6.3 设置堆内存大小

- Java堆区用于存储Java对象实例，那么堆的大小在JVM启动时就已经设定好了，大家可以通过选项"-Xmx"和"-Xms"来进行设置。

  

  - “-Xms"用于表示堆区（年轻代+老年代）的起始内存，等价于`-XX:InitialHeapSize`
    - -X是JVM运行参数
    - ms是memory 参数

  - “-Xmx"则用于表示堆区（年轻代+老年代）的最大内存，等价于`-XX:MaxHeapSize`

  

  一旦堆区中的内存大小超过“-Xmx"所指定的最大内存时，将会抛出OutOfMemoryError异常。

  

  通常会将-Xms和-Xmx两个参数配置相同的值，其目的是为了能够在Java垃圾回收机制清理完堆区后不需要重新分隔计算堆区的大小，从而提高性能。

- 默认情况下，初始内存大小：物理内存大小 / 64

  ​					最大内存大小：物理电脑内存 / 4



> 查看设置的参数

-XX:+PrintGCDetails



#### 3.6.4 OOM的说明与举例

存放的数据超过最大堆空间，就会发生OOM（OutOfMemoryError）

```java
public class OOMTest {
    public static void main(String[] args) {
        ArrayList<Picture> list = new ArrayList<>();

        while (true) {
            try {
                TimeUnit.SECONDS.sleep(1);
            }catch (Exception e) {
                e.printStackTrace();
            }
            list.add(new Picture(new Random().nextInt(1024 * 1024)));
        }
    }
}

class Picture {
    private byte[] pixels;
    public Picture(int length) {
        this.pixels = new byte[length];
    }
}
```

![image-20211023165816326](F:\笔记图片\img\image-20211023165816326.png)

![image-20211023165830944](F:\笔记图片\img\image-20211023165830944.png)



#### 3.6.5 年轻代与老年代

- 存储在JVM的Java对象可以被划分为两类
  - 一类是生命周期较短的瞬时对象，这类对象的创建和消亡都非常迅速
  - 另外一类对象的生命周期却非常长，在某些极端的情况下还能够与JVM的生命周期保持一致
- Java堆区进一步细分的话，可以划分为年轻代和老年代
  - 其中年轻代又可以划分为伊甸园区，幸存者0区和幸存者1区（也叫from区，to区）

![img](https://www.yuque.com/api/filetransfer/images?url=https%3A%2F%2Fgitee.com%2Fvectorx%2FImageCloud%2Fraw%2Fmaster%2Fimg%2F20210510105632.png&sign=0d67c1a2bf703c11c6a715b9454b2a7d52a2882ada19399fa7efa4e26f01eba0)



下面这参数开发中一般不会调

![img](https://www.yuque.com/api/filetransfer/images?url=https%3A%2F%2Fgitee.com%2Fvectorx%2FImageCloud%2Fraw%2Fmaster%2Fimg%2F20210510105638.png&sign=028814ebd1a2c37b88a1404f552458d6533607c222b54edc488d7fe2a7142d07)

- 配置新生代与老年代在堆结构的占比

  - 默认`-XX:NewRatio=2`，表示新生代占1，老年代占2，新生代占整个堆的1/3

  - 可以修改`-XX:NewRatio=4`，表示新生代占1，老年代占4，新生代占整个堆的1/5

- 在HotSpot中，Eden空间和另外两个survivor空间缺省所占的比例是8：1：1

  当然开发人员可以通过选项“`-xx:SurvivorRatio`”调整这个空间比例。比如`-xx:SurvivorRatio=8`

  **几乎所有的Java对象都是在Eden区被new出来的。绝大部分的Java对象的销毁都在新生代进行了。**

  - IBM公司的专门研究表明，新生代中80%的对象都是“朝生夕死”的。

  可以使用选项"`-Xmn`"设置新生代最大内存大小，这个参数一般使用默认值就可以了。

  

  ![img](https://www.yuque.com/api/filetransfer/images?url=https%3A%2F%2Fgitee.com%2Fvectorx%2FImageCloud%2Fraw%2Fmaster%2Fimg%2F20210510105854.png&sign=e44862d57ce45d2ac831f1291413114955c5593d85d1b07eab8847c5d330801d)





#### 3.6.6 图解对象分配过程

为新对象分配内存是一件非常严谨和复杂的任务，JVM的设计者们不仅需要考虑内存如何分配、在哪里分配等问题，并且由于内存分配算法与内存回收算法密切相关，所以还需要考虑GC执行完内存回收后是否会在内存空间中产生内存碎片。

1.  new的对象先放伊甸园区。此区有大小限制。 

2. **当伊甸园的空间填满时，程序又需要创建对象，JVM的垃圾回收器将对伊甸园区进行垃圾回收（MinorGC）**，将伊甸园区中的不再被其他对象所引用的对象进行销毁。再加载新的对象放到伊甸园区 

3. 然后将伊甸园中的剩余对象移动到幸存者0区。 

4. 如果再次触发垃圾回收，此时上次幸存下来的放到幸存者0区的，如果没有回收，就会放到幸存者1区。 

5. 如果再次经历垃圾回收，此时会重新放回幸存者0区，接着再去幸存者1区。 

6. 啥时候能去养老区呢？可以设置次数。默认是15次。 

- - 可以设置参数：进行设置`-Xx:MaxTenuringThreshold= N`

7. **在养老区，相对悠闲。当养老区内存不足时，再次触发GC：Major GC，进行养老区的内存清理** 

8. 若养老区执行了Major GC之后，发现依然无法进行对象的保存，就会产生OOM异常。  

![img](https://www.yuque.com/api/filetransfer/images?url=https%3A%2F%2Fgitee.com%2Fvectorx%2FImageCloud%2Fraw%2Fmaster%2Fimg%2F20210510111316.jpg&sign=f2e661799c41721982db281dca0fcd3a1f2d69c71e819009ab70d06d99a35e7b)

![img](https://www.yuque.com/api/filetransfer/images?url=https%3A%2F%2Fgitee.com%2Fvectorx%2FImageCloud%2Fraw%2Fmaster%2Fimg%2F20210510112916.png&sign=c298ad0551de199ae65cbd6587b32b72a641a4901d5ea66c7b689c4a8d9d96c9)



> 总结

- 针对幸存者s0,s1区的总结：复制之后有交换，谁空谁是to
- 关于垃圾回收：频繁在新生区收集，很少在养老区收集，几乎不在永久区/元空间收集



#### 3.6.7 Minor GC,Major GC和Full GC

VM在进行GC时，并非每次都对上面三个内存区域一起回收的，大部分时候回收的都是指新生代。



针对Hotspot VM的实现，它里面的GC按照回收区域又分为两大种类型：一种是部分收集（Partial GC），一种是整堆收集（FullGC）



- 部分收集：不是完整收集整个Java堆的垃圾收集。其中又分为： 

- - 新生代收集（Minor GC / Young GC）：只是新生代的垃圾收集

- - 老年代收集（Major GC / Old GC）：只是老年代的圾收集。 

- - - 目前，只有CMSGC会有单独收集老年代的行为。

- - - **注意，很多时候Major GC会和Full GC混淆使用，需要具体分辨是老年代回收还是整堆回收。**

- - 混合收集（MixedGC）：收集整个新生代以及部分老年代的垃圾收集。 

- - - 目前，只有G1 GC会有这种行为

- 整堆收集（Full GC）：收集整个java堆和方法区的垃圾收集。



> 年轻代GC（Minor GC）触发机制

- 当年轻代空间不足时，就会触发MinorGC，这里的**年轻代满指的是Eden代满**，Survivor满不会引发GC。（每次Minor GC会清理年轻代的内存。） 

-  因为`Java对象大多都具备朝生夕灭的特性`，所以Minor GC非常频繁，一般回收速度也比较快。这一定义既清晰又易于理解。 

-  Minor GC会引发STW，暂停其它用户的线程，等垃圾回收结束，用户线程才恢复运行 

![img](https://www.yuque.com/api/filetransfer/images?url=https%3A%2F%2Fgitee.com%2Fvectorx%2FImageCloud%2Fraw%2Fmaster%2Fimg%2F20210510112924.png&sign=0e4acedad5986dfb6ecd956f85294e4434ad5c1f3e0e0757688a12fb6173695c)



> 老年代GC（Major GC）触发机制

- 指发生在老年代的GC，对象从老年代消失时，我们说 “Major GC” 或 “Full GC” 发生了 

- 出现了Major Gc，经常会伴随至少一次的Minor GC（但非绝对的，在Paralle1 Scavenge收集器的收集策略里就有直接进行MajorGC的策略选择过程） 
  - 也就是在老年代空间不足时，会先尝试触发Minor Gc。如果之后空间还不足，则触发Major GC

-  Major GC的速度一般会比Minor GC慢10倍以上，STW的时间更长 

-  如果Major GC后，内存还不足，就报OOM了 



> Full  GC触发机制

触发Full GC执行的情况有如下五种：

1. 调用System.gc()时，系统建议执行Full GC，但是不必然执行

2. 老年代空间不足

3. 方法区空间不足

4. 通过Minor GC后进入老年代的平均大小大于老年代的可用内存

5. 由Eden区、survivor space0（From Space）区向survivor space1（To Space）区复制时，对象大小大于To Space可用内存，则把该对象转存到老年代，且老年代的可用内存小于该对象大小



说明：Full GC 是开发或调优中尽量要避免的。这样暂时时间会短一些 



#### 3.6.8 堆空间分代思想

**为什么需要把Java堆分代？不分代就不能正常工作了么？**

- 经研究，不同对象的生命周期不同。70%-99%的对象是临时对象。

  - 新生代：有Eden、两块大小相同的survivor（又称为from/to，s0/s1）构成，to总为空。

  - 老年代：存放新生代中经历多次GC仍然存活的对象。

![img](https://www.yuque.com/api/filetransfer/images?url=https%3A%2F%2Fgitee.com%2Fvectorx%2FImageCloud%2Fraw%2Fmaster%2Fimg%2F20210510113449.png&sign=9f344d5ee985097574286dba56bdeb2bd943b102a3de6704983381cbd84fd4b7)



其实不分代完全可以，分代的唯一理由就是优化GC性能。如果没有分代，那所有的对象都在一块，就如同把一个学校的人都关在一个教室。GC的时候要找到哪些对象没用，这样就会对堆的所有区域进行扫描。而很多对象都是朝生夕死的，如果分代的话，把新创建的对象放到某一地方，当GC的时候先把这块存储“朝生夕死”对象的区域进行回收，这样就会腾出很大的空间出来。



![img](https://www.yuque.com/api/filetransfer/images?url=https%3A%2F%2Fgitee.com%2Fvectorx%2FImageCloud%2Fraw%2Fmaster%2Fimg%2F20210510113454.png&sign=461d10883a6092f8f49c8a953c67c729cc11e60dd3ddaca4ad16867ee05d4a5b)



#### 3.6.9 内存分配策略

- 如果对象在Eden出生并经过第一次Minor GC后仍然存活，并且能被Survivor容纳的话，将被移动到survivor空间中，并将对象年龄设为1。对象在survivor区中每熬过一次MinorGC，年龄就增加1岁，当它的年龄增加到一定程度（默认为15岁，其实每个JVM、每个GC都有所不同）时，就会被晋升到老年代
- 对象晋升老年代的年龄阀值，可以通过选项`-XX:MaxTenuringThreshold`来设置



针对不同年龄段的对象分配原则如下所示：

- 优先分配到Eden

- 大对象直接分配到老年代（尽量避免程序中出现过多的大对象）

- 长期存活的对象分配到老年代

- 动态对象年龄判断：如果survivor区中相同年龄的所有对象大小的总和大于Survivor空间的一半，年龄大于或等于该年龄的对象可以直接进入老年代，无须等到`MaxTenuringThreshold`中要求的年龄。

- 空间分配担保： `-XX:HandlePromotionFailure`



#### 3.6.X 为对象分配内存（TLAB）

**为什么有TLAB？**

-  堆区是线程共享区域，任何线程都可以访问到堆区中的共享数据 

-  由于对象实例的创建在JVM中非常频繁，因此在并发环境下从堆区中划分内存空间是线程不安全的 

-  为避免多个线程操作同一地址，需要使用加锁等机制，进而影响分配速度。 



**什么是TLAB**

- 从==内存模型==而不是垃圾收集的角度，对Eden区域继续进行划分，==JVM为每个线程分配了一个私有缓存区域，它包含在Eden空间内==。 
- 多线程同时分配内存时，使用TLAB可以避免一系列的非线程安全问题，同时还能够提升内存分配的吞吐量，因此我们可以将这种内存分配方式称之为快速分配策略。 
-  据我所知所有OpenJDK衍生出来的JVM都提供了TLAB的设计。 

![img](https://www.yuque.com/api/filetransfer/images?url=https%3A%2F%2Fgitee.com%2Fvectorx%2FImageCloud%2Fraw%2Fmaster%2Fimg%2F20210510114112.png&sign=b65e56e340f2d5754a39f84755cdf1136758e77539498608f123b75bf7bf4054)



- 尽管不是所有的对象实例都能够在TLAB中成功分配内存，==但JVM确实是将TLAB作为内存分配的首选==。 

-  在程序中，开发人员可以通过选项“`-XX:UseTLAB`”设置是否开启TLAB空间。 

-  默认情况下，==TLAB空间的内存非常小，仅占有整个Eden空间的1%==，当然我们可以通过选项 “`-XX:TLABWasteTargetPercent`” 设置TLAB空间所占用Eden空间的百分比大小。 

-  一旦对象在TLAB空间分配内存失败时，JVM就会尝试着通过使用加锁机制确保数据操作的原子性，从而直接在Eden空间中分配内存。 



> 堆空间参数设置

官网地址：https://docs.oracle.com/javase/8/docs/technotes/tools/windows/java.html

```
// 详细的参数内容会在JVM下篇：性能监控与调优篇中进行详细介绍，这里先熟悉下
-XX:+PrintFlagsInitial  //查看所有的参数的默认初始值
-XX:+PrintFlagsFinal  //查看所有的参数的最终值（可能会存在修改，不再是初始值）
-Xms  //初始堆空间内存（默认为物理内存的1/64）
-Xmx  //最大堆空间内存（默认为物理内存的1/4）
-Xmn  //设置新生代的大小。（初始值及最大值）
-XX:NewRatio  //配置新生代与老年代在堆结构的占比
-XX:SurvivorRatio  //设置新生代中Eden和S0/S1空间的比例
-XX:MaxTenuringThreshold  //设置新生代垃圾的最大年龄
-XX:+PrintGCDetails //输出详细的GC处理日志
//打印gc简要信息：①-Xx：+PrintGC ② - verbose:gc
-XX:HandlePromotionFalilure：//是否设置空间分配担保
```

在发生Minor GC之前，==虚拟机会检查老年代最大可用的连续空间是否大于新生代所有对象的总空间==。

- 如果大于，则此次Minor GC是安全的

- 如果小于，则虚拟机会查看`-XX:HandlePromotionFailure`设置值是否允担保失败。 

- - 如果`HandlePromotionFailure=true`，那么会继续检查老年代最大可用连续空间是否大于历次晋升到老年代的对象的平均大小。 

- - - 如果大于，则尝试进行一次Minor GC，但这次Minor GC依然是有风险的；

- - - 如果小于，则改为进行一次Full GC。

- - 如果`HandlePromotionFailure=false`，则改为进行一次Full Gc。



在JDK6 Update24（JDK7）之后，HandlePromotionFailure参数不会再影响到虚拟机的空间分配担保策略，观察openJDK中的源码变化，虽然源码中还定义了HandlePromotionFailure参数，但是在代码中已经不会再使用它。JDK6 Update 24之后的规则变为只要老年代的连续空间大于新生代对象总大小或者历次晋升的平均大小就会进行Minor GC，否则将进行FullGC。



#### 3.6.XI 堆是分配对象的唯一选择么

- 在《深入理解Java虚拟机》中关于Java堆内存有这样一段描述：

  随着JIT编译期的发展与逃逸分析技术逐渐成熟，栈上分配、标量替换优化技术将会导致一些微妙的变化，所有的对象都分配到堆上也渐渐变得不那么“绝对”了。

- 在Java虚拟机中，对象是在Java堆中分配内存的，这是一个普遍的常识。但是，有一种特殊情况，那就是如果经过逃逸分析（Escape Analysis）后发现，一个对象并没有逃逸出方法的话，那么就可能被优化成栈上分配.。这样就无需在堆上分配内存，也无须进行垃圾回收了。这也是最常见的堆外存储技术。

- 此外，前面提到的基于OpenJDK深度定制的TaoBaoVM，其中创新的GCIH（GC invisible heap）技术实现off-heap，将生命周期较长的Java对象从heap中移至heap外，并且GC不能管理GCIH内部的Java对象，以此达到降低GC的回收频率和提升GC的回收效率的目的。



如何将堆上的对象分配到栈，需要使用逃逸分析手段。

这是一种可以有效减少Java程序中同步负载和内存堆分配压力的跨函数全局数据流分析算法。

通过逃逸分析，Java Hotspot编译器能够分析出一个新的对象的引用的使用范围从而决定是否要将这个对象分配到堆上。

逃逸分析的基本行为就是分析对象动态作用域：

- 当一个对象在方法中被定义后，对象只在方法内部使用，则认为没有发生逃逸。

- 当一个对象在方法中被定义后，它被外部方法所引用，则认为发生逃逸。例如作为调用参数传递到其他地方中。

==如何快速判断是否发生了逃逸分析，就看new的对象实体是否有可能在方法外被调用==

```java
public void my_method() {
    V v = new V();
    // use v
    // ....
    v = null;
}

/**没有发生逃逸的对象，则可以分配到栈上，随着方法执行的结束，栈空间就被移除，每个栈里面包含了很多栈帧**/
```



```Java
public static StringBuffer createStringBuffer(String s1, String s2) {
    StringBuffer sb = new StringBuffer();
    sb.append(s1);
    sb.append(s2);
    return sb;
}

/**发生逃逸，返回值可能被外部使用**/
```

```Java
public static String createStringBuffer(String s1, String s2) {
    StringBuffer sb = new StringBuffer();
    sb.append(s1);
    sb.append(s2);
    return sb.toString();
}

/**这样可以避免发生逃逸**/
```



**参数设置**

在JDK 6u23 版本之后，HotSpot中默认就已经开启了逃逸分析

如果使用的是较早的版本，开发人员则可以通过：

- 选项“`-XX:+DoEscapeAnalysis`"显式开启逃逸分析

- 通过选项“`-XX:+PrintEscapeAnalysis`"查看逃逸分析的筛选结果

**结论**：开发中能使用局部变量的，就不要使用在方法外定义。





#### 3.6.XII 代码优化

**栈上分配**

- 将堆分配转换为栈上分配。如果一个对象在子程序中被分配，要使指向该对象的指针永远不会逃逸，对象可能是栈分配的候选，而不是堆分配

**同步省略**

- 如果一个对象被发现只能从一个线程被访问到，那么对于这个对象的操作可以不考虑同步



线程同步的代价是相当高的，同步的后果是降低并发性和性能。

在动态编译同步块的时候，JIT编译器可以借助逃逸分析来==判断同步块所使用的锁对象是否只能够被一个线程访问而没有被发布到其他线程==。如果没有，那么JIT编译器在编译这个同步块的时候就会取消对这部分代码的同步。这样就能大大提高并发性和性能。这个取消同步的过程就叫同步省略，也叫==锁消除==。



**标量替换**

- 有的对象可能不需要作为一个连续的内存结构存在也可以被访问到，那么对象的部分（或全部）可以不存储在内存，而是存储在CPU寄存器中





==标量（scalar）==是指一个无法再分解成更小的数据的数据。Java中的原始数据类型就是标量。

相对的，那些还可以分解的数据叫做==聚合量（Aggregate）==，Java中的对象就是聚合量，因为他可以分解成其他聚合量和标量。

在JIT阶段，如果经过逃逸分析，发现一个对象不会被外界访问的话，那么经过JIT优化，就会把这个对象拆解成若干个其中包含的若干个成员变量来代替。这个过程就是标量替换。



#### 小结

- 年轻代是对象的诞生、成长、消亡的区域，一个对象在这里产生、应用，最后被垃圾回收器收集、结束生命。

- 老年代放置长生命周期的对象，通常都是从survivor区域筛选拷贝过来的Java对象。当然，也有特殊情况，我们知道普通的对象会被分配在TLAB上；如果对象较大，JVM会试图直接分配在Eden其他位置上；如果对象太大，完全无法在新生代找到足够长的连续空闲空间，JVM就会直接分配到老年代。当GC只发生在年轻代中，回收年轻代对象的行为被称为MinorGc。

- 当GC发生在老年代时则被称为MajorGc或者FullGC。一般的，MinorGc的发生频率要比MajorGC高很多，即老年代中垃圾回收发生的频率将大大低于年轻代



### 3.7 方法区

从线程角度来看

![img](https://www.yuque.com/api/filetransfer/images?url=https%3A%2F%2Fgitee.com%2Fvectorx%2FImageCloud%2Fraw%2Fmaster%2Fimg%2F20210510141133.png&sign=5bab1bc197964380ce7a6b558039a993eed34ab4905d94ea3472527188adda07)



#### 3.7.1 栈，堆，方法区之间的交互

![img](https://www.yuque.com/api/filetransfer/images?url=https%3A%2F%2Fgitee.com%2Fvectorx%2FImageCloud%2Fraw%2Fmaster%2Fimg%2F20210510141516.png&sign=b5ec0731653ec5956c3f23972b2bf9269aca689b46645af04d92d05be5cc6234)



**方法区的理解**

官方文档 [Chapter 2. The Structure of the Java Virtual Machine (oracle.com)](https://docs.oracle.com/javase/specs/jvms/se8/html/jvms-2.html#jvms-2.5.4)

《Java虚拟机规范》中明确说明：“尽管所有的方法区在逻辑上是属于堆的一部分，但一些简单的实现可能不会选择去进行垃圾收集或者进行压缩。”但对于HotSpotJVM而言，方法区还有一个别名叫做Non-Heap（非堆），目的就是要和堆分开。

所以，==方法区看作是一块独立于Java堆的内存空间==。

![img](https://www.yuque.com/api/filetransfer/images?url=https%3A%2F%2Fgitee.com%2Fvectorx%2FImageCloud%2Fraw%2Fmaster%2Fimg%2F20210510195403.png&sign=bcfc0d6aa77b9707f092dd091ae40c84192b75ea9d52f3a40f8846016b79a905)



- 方法区（Method Area）与Java堆一样，是各个线程共享的内存区域。

- 方法区在JVM启动的时候被创建，并且它的实际的物理内存空间中和Java堆区一样都可以是不连续的。

- 方法区的大小，跟堆空间一样，可以选择固定大小或者可扩展。

- 方法区的大小决定了系统可以保存多少个类，如果系统定义了太多的类，导致方法区溢出，虚拟机同样会抛出内存溢出错误：`java.lang.OutOfMemoryError: PermGen space` 或者`java.lang.OutOfMemoryError: Metaspace` 

- - 加载大量的第三方的jar包；Tomcat部署的工程过多（30~50个）；大量动态的生成反射类

- 关闭JVM就会释放这个区域的内存。



#### 3.7.2 Hotspot中方法区的演进

|        | 默认初始大小 | 默认最大大小            | 存储内容                                                     |
| ------ | ------------ | ----------------------- | ------------------------------------------------------------ |
| 永久代 | 20.75M       | 32位机器64M/64位机器82M | 类型信息<br/>运行时常量池<br/>域信息<br/>方法信息<br/>JIT代码缓存<br/>静态变量（jdk6及以前，jdk7时移到堆了） |
| 元空间 | 21M          | -1（即无限制）          | 类型信息<br/>运行时常量池<br/>域信息<br/>方法信息<br/>JIT代码缓存 |

- 类型信息：类的修饰符等

- 运行时常量池：为节省内存，会把常量放进常量池（可看作一张表）

- 域信息：属性

- 方法信息：方法

- 静态变量：类变量

  - 常量池表与运行时常量池

    常量池表：是class文件的一部分，在类加载之后存放到运行时常量池中

    运行时常量池：具备动态性



在jdk7及以前，习惯上把方法区，称为永久代。jdk8开始，使用元空间取代了永久代。

![img](https://www.yuque.com/api/filetransfer/images?url=https%3A%2F%2Fgitee.com%2Fvectorx%2FImageCloud%2Fraw%2Fmaster%2Fimg%2F20210510142517.png&sign=bcdbb062083523423a688e98f95dacbdee5614ca5ff42552240cffe22f6b74ab)



本质上，方法区和永久代并不等价。仅是对hotspot而言的。《Java虚拟机规范》对如何实现方法区，不做统一要求。例如：BEA JRockit / IBM J9 中不存在永久代的概念。



现在来看，当年使用永久代，不是好的idea。导致Java程序更容易OOM（超过`-XX:MaxPermsize`上限）

![img](https://www.yuque.com/api/filetransfer/images?url=https%3A%2F%2Fgitee.com%2Fvectorx%2FImageCloud%2Fraw%2Fmaster%2Fimg%2F20210510142658.png&sign=1aeffd67d9a18fedec9fe3020256dc6713492f69b01d6a71b2be49c901d7066f)

而到了JDK8，终于完全废弃了永久代的概念，改用与JRockit、J9一样在本地内存中实现的元空间（Metaspace）来代替

![img](https://www.yuque.com/api/filetransfer/images?url=https%3A%2F%2Fgitee.com%2Fvectorx%2FImageCloud%2Fraw%2Fmaster%2Fimg%2F20210510142722.png&sign=9a717a11263cf2cf377a93a711e67420226eb5460c9e95dc597c560c398a82ff)



元空间的本质和永久代类似，都是对JVM规范中方法区的实现。不过元空间与永久代最大的区别在于：==元空间不在虚拟机设置的内存中，而是使用本地内存==

永久代、元空间二者并不只是名字变了，内部结构也调整了

根据《Java虚拟机规范》的规定，如果方法区无法满足新的内存分配需求时，将抛出OOM异常



#### 3.7.3 设置方法区的大小

方法区的大小不必是固定的，JVM可以根据应用的需要动态调整。



**jdk7及以前**



- 通过来设置永久代初始分配空间。默认值是20.75M`-XX:Permsize`

- 通过来设定永久代最大可分配空间。32位机器默认是64M，64位机器模式是82M`-XX:MaxPermsize`

- 当JVM加载的类信息容量超过了这个值，会报异常`OutOfMemoryError:PermGen space`。

![img](https://www.yuque.com/api/filetransfer/images?url=https%3A%2F%2Fgitee.com%2Fvectorx%2FImageCloud%2Fraw%2Fmaster%2Fimg%2F20210510195521.png&sign=addda9a2575d60aec024e369c5bb7f9c34e5d1758307766ff5055ab56ec9cf35)



**JDK8以后**



- 元数据区大小可以使用参数 `-XX:MetaspaceSize` 和 `-XX:MaxMetaspaceSize`指定

- 默认值依赖于平台。windows下，`-XX:MetaspaceSize=21M -XX:MaxMetaspaceSize=-1//即没有限制`。

- 与永久代不同，如果不指定大小，默认情况下，虚拟机会耗尽所有的可用系统内存。如果元数据区发生溢出，虚拟机一样会抛出异常`OutOfMemoryError:Metaspace`

- `-XX:MetaspaceSize`：设置初始的元空间大小。对于一个64位的服务器端JVM来说，其默认的`-XX:MetaspaceSize`值为21MB。这就是初始的高水位线，一旦触及这个水位线，Full GC将会被触发并卸载没用的类（即这些类对应的类加载器不再存活），然后这个高水位线将会重置。新的高水位线的值取决于GC后释放了多少元空间。如果释放的空间不足，那么在不超过`MaxMetaspaceSize`时，适当提高该值。如果释放空间过多，则适当降低该值。

- 如果初始化的高水位线设置过低，上述高水位线调整情况会发生很多次。通过垃圾回收器的日志可以观察到Full GC多次调用。为了避免频繁地GC，建议将`-XX:MetaspaceSize`设置为一个相对较高的值。





**如何解决OOM**

1.  要解决OOM异常或heap space的异常，一般的手段是首先通过内存映像分析工具（如Eclipse Memory Analyzer）对dump出来的堆转储快照进行分析，重点是确认内存中的对象是否是必要的，也就是要先分清楚到底是出现了内存泄漏（Memory Leak）还是内存溢出（Memory Overflow） 

2. 如果是内存泄漏，可进一步通过工具查看泄漏对象到GC Roots的引用链。于是就能找到泄漏对象是通过怎样的路径与GCRoots相关联并导致垃圾收集器无法自动回收它们的。掌握了泄漏对象的类型信息，以及GCRoots引用链的信息，就可以比较准确地定位出泄漏代码的位置。 

3.  如果不存在内存泄漏，换句话说就是内存中的对象确实都还必须存活着，那就应当检查虚拟机的堆参数（`-Xmx`与`-Xms`），与机器物理内存对比看是否还可以调大，从代码上检查是否存在某些对象生命周期过长、持有状态时间过长的情况，尝试减少程序运行期的内存消耗。 



#### 3.7.4 方法区内部结构

![img](https://www.yuque.com/api/filetransfer/images?url=https%3A%2F%2Fgitee.com%2Fvectorx%2FImageCloud%2Fraw%2Fmaster%2Fimg%2F20210510144845.png&sign=8e1b2ef90e2977eb08c81e6a0dbb38747d45ca92012bb38d0ef3f2636d4550b4)

《深入理解Java虚拟机》书中对方法区（Method Area）存储内容描述如下：

它用于存储已被虚拟机加载的类型信息、常量、静态变量、即时编译器编译后的代码缓存等。

![img](https://www.yuque.com/api/filetransfer/images?url=https%3A%2F%2Fgitee.com%2Fvectorx%2FImageCloud%2Fraw%2Fmaster%2Fimg%2F20210510144850.png&sign=37ff12b236eae1a4c188cdd988e3f7b366ceb9f52eeaf3666420acef85249b1a)



**类型信息**

对每个加载的类型（类class、接口interface、枚举enum、注解annotation），JVM必须在方法区中存储以下类型信息：

1. 这个类型的完整有效名称（全名=包名.类名）

2. 这个类型直接父类的完整有效名（对于interface或是java.lang.object，都没有父类）

3. 这个类型的修饰符（public，abstract，final的某个子集）

4. 这个类型直接接口的一个有序列表



**域（Field）信息**

JVM必须在方法区中保存类型的所有域的相关信息以及域的声明顺序。

域的相关信息包括：域名称、域类型、域修饰符（public，private，protected，static，final，volatile，transient的某个子集）



**方法（Method）信息**

JVM必须保存所有方法的以下信息，同域信息一样包括声明顺序：

1. 方法名称

2. 方法的返回类型（或void）

3. 方法参数的数量和类型（按顺序）

4. 方法的修饰符（public，private，protected，static，final，synchronized，native，abstract的一个子集）

5. 方法的字节码（bytecodes）、操作数栈、局部变量表及大小（abstract和native方法除外）

6. 异常表（abstract和native方法除外） 

- - 每个异常处理的开始位置、结束位置、代码处理在程序计数器中的偏移地址、被捕获的异常类的常量池索引



**non-final的类变量**

- 静态变量和类关联在一起，随着类的加载而加载，他们成为类数据在逻辑上的一部分

- 类变量被类的所有实例共享，即使没有类实例时，你也可以访问它



==对于static final 修饰的变量（实际上为全局常量），在编译期就已经确定了值并且不能被修改==



#### 3.7.5运行时常量池VS常量池

![img](https://www.yuque.com/api/filetransfer/images?url=https%3A%2F%2Fgitee.com%2Fvectorx%2FImageCloud%2Fraw%2Fmaster%2Fimg%2F20210510145605.png&sign=85e659659ac7a0b7ae91c7e150247998f7d178d8673449cd0f4db78063b3e733)

- 方法区，内部包含了运行时常量池

- 字节码文件，内部包含了常量池

- 要弄清楚方法区，需要理解清楚ClassFile，因为加载类的信息都在方法区。

- 要弄清楚方法区的运行时常量池，需要理解清楚ClassFile中的常量池。



官方文档：https://docs.oracle.com/javase/specs/jvms/se8/html/jvms-4.html



![img](https://www.yuque.com/api/filetransfer/images?url=https%3A%2F%2Fgitee.com%2Fvectorx%2FImageCloud%2Fraw%2Fmaster%2Fimg%2F20210510145702.png&sign=95c8272ac950984ffd3e747a6324d65a623d2de5be2357f3bf2fb4ad9016ccec)

一个有效的字节码文件中除了包含类的版本信息、字段、方法以及接口等描述符信息外，还包含一项信息就是常量池表（Constant Pool Table），包括各种字面量和对类型、域和方法的符号引用

> 为什么需要常量池？

一个java源文件中的类、接口，编译后产生一个字节码文件。而Java中的字节码需要数据支持，通常这种数据会很大以至于不能直接存到字节码里，换另一种方式，可以存到常量池，这个字节码包含了指向常量池的引用。在动态链接的时候会用到运行时常量池，之前有介绍。



比如：如下的代码：



```java
public class SimpleClass {
    public void sayHello() {
        System.out.println("hello");
    }
}
```

虽然只有194字节，但是里面却使用了String、System、PrintStream及Object等结构。这里的代码量其实很少了，如果代码多的话，引用的结构将会更多，这里就需要用到常量池了。



> 常量池中有什么?

击中常量池内存储的数据类型包括：

- 数量值

- 字符串值

- 类引用

- 字段引用

- 方法引用



例如下面这段代码：

```java
public class MethodAreaTest2 {
    public static void main(String args[]) {
        Object obj = new Object();
    }
}
```

`Object obj = new Object();`将会被翻译成如下字节码：

```java
0: new #2  // Class java/lang/Object
1: dup
2: invokespecial // Method java/lang/Object "<init>"() V
```

> 小结

常量池、可以看做是一张表，虚拟机指令根据这张常量表找到要执行的类名、方法名、参数类型、字面量等类型



- 运行时常量池（Runtime Constant Pool）是方法区的一部分。

- 常量池表（Constant Pool Table）是Class文件的一部分，==用于存放编译期生成的各种字面量与符号引用，这部分内容将在类加载后存放到方法区的运行时常量池中==。

- 运行时常量池，在加载类和接口到虚拟机后，就会创建对应的运行时常量池。

- JVM为每个已加载的类型（类或接口）都维护一个常量池。池中的数据项像数组项一样，是通过索引访问的。

- 运行时常量池中包含多种不同的常量，包括编译期就已经明确的数值字面量，也包括到运行期解析后才能够获得的方法或者字段引用。此时不再是常量池中的符号地址了，这里换为真实地址。

- 运行时常量池，相对于Class文件常量池的另一重要特征是：==具备动态性==。

- 运行时常量池类似于传统编程语言中的符号表（symboltable），但是它所包含的数据却比符号表要更加丰富一些。

- 当创建类或接口的运行时常量池时，如果构造运行时常量池所需的内存空间超过了方法区所能提供的最大值，则JVM会抛OutOfMemoryError异常。



#### 3.7.6 方法区使用举例

```java
public class MethodAreaDemo {
    public static void main(String args[]) {
        int x = 500;
        int y = 100;
        int a = x / y;
        int b = 50;
        System.out.println(a+b);
    }
}
```



![img](https://www.yuque.com/api/filetransfer/images?url=https%3A%2F%2Fgitee.com%2Fvectorx%2FImageCloud%2Fraw%2Fmaster%2Fimg%2F20210510151437.png&sign=951cfea1a76e9b8ba54b69cbface1efcd31d241b564ec12ebdb64adc7aa4ef05)

![img](https://www.yuque.com/api/filetransfer/images?url=https%3A%2F%2Fgitee.com%2Fvectorx%2FImageCloud%2Fraw%2Fmaster%2Fimg%2F20210510151505.png&sign=299014db1665470b867781443ef5154782e79a38429303df805bc566feef3924)

![img](https://www.yuque.com/api/filetransfer/images?url=https%3A%2F%2Fgitee.com%2Fvectorx%2FImageCloud%2Fraw%2Fmaster%2Fimg%2F20210510151523.png&sign=0483eb40d942345620f88c6fed33b8f49f7d325a1e04f051d882f82b37bbe207)

![img](https://www.yuque.com/api/filetransfer/images?url=https%3A%2F%2Fgitee.com%2Fvectorx%2FImageCloud%2Fraw%2Fmaster%2Fimg%2F20210510151610.png&sign=cc3fdd4397ced4b5ebabbd8051a3c240e3afd81c39e120519a205667cc87cb29)

![img](F:\笔记图片\img\imagesurl=https%3A%2F%2Fgitee.com%2Fvectorx%2FImageCloud%2Fraw%2Fmaster%2Fimg%2F20210510151651.png&sign=f4912e9aefe33401f5b4ff287ccf907acaf915177e468a95144716651f494428)

![img](https://www.yuque.com/api/filetransfer/images?url=https%3A%2F%2Fgitee.com%2Fvectorx%2FImageCloud%2Fraw%2Fmaster%2Fimg%2F20210510151713.png&sign=0cd1ce05d9649d63706c0e5f83cd7d4dc5cfeafdead9efc31ef42773d9e9b4f6)

![img](https://www.yuque.com/api/filetransfer/images?url=https%3A%2F%2Fgitee.com%2Fvectorx%2FImageCloud%2Fraw%2Fmaster%2Fimg%2F20210510151755.png&sign=ee07c3096295971d4d9605160517a8b3a124bd545d826998d988893759afe261)

![img](https://www.yuque.com/api/filetransfer/images?url=https%3A%2F%2Fgitee.com%2Fvectorx%2FImageCloud%2Fraw%2Fmaster%2Fimg%2F20210510151830.png&sign=9c3f29264c10e7e40b2125e7cb7b99e32d9465e7a14bd262df5fde912ccbc271)

![img](https://www.yuque.com/api/filetransfer/images?url=https%3A%2F%2Fgitee.com%2Fvectorx%2FImageCloud%2Fraw%2Fmaster%2Fimg%2F20210510151919.png&sign=1476795e4acb99de2d03a42ca930319bb1f4a59f3392267352e8338b5d17893d)

![img](https://www.yuque.com/api/filetransfer/images?url=https%3A%2F%2Fgitee.com%2Fvectorx%2FImageCloud%2Fraw%2Fmaster%2Fimg%2F20210510151953.png&sign=62f861ca5ab0161853c39bbaf8d1e7b9247df770e7aced514b41608668df654a)

![img](https://www.yuque.com/api/filetransfer/images?url=https%3A%2F%2Fgitee.com%2Fvectorx%2FImageCloud%2Fraw%2Fmaster%2Fimg%2F20210510195617.png&sign=2eeadc8bfc762a46da42e8e40354e34ebfea30c1b667f2785c4e39e3c5354bbe)

![img](https://www.yuque.com/api/filetransfer/images?url=https%3A%2F%2Fgitee.com%2Fvectorx%2FImageCloud%2Fraw%2Fmaster%2Fimg%2F20210510152104.png&sign=bbd14a4b0acce8cba3682c9bde11254e3e7a94c671768fbffa4bf6c3248a7f09)

![img](https://www.yuque.com/api/filetransfer/images?url=https%3A%2F%2Fgitee.com%2Fvectorx%2FImageCloud%2Fraw%2Fmaster%2Fimg%2F20210510152139.png&sign=b093d11cd339f6960c64a1fa2ef5daf55216992929e02bfa748b4c5b4b0f8d70)

![img](https://www.yuque.com/api/filetransfer/images?url=https%3A%2F%2Fgitee.com%2Fvectorx%2FImageCloud%2Fraw%2Fmaster%2Fimg%2F20210510195826.png&sign=c21982e350c286ff8b661e54fa201710c6371a67b0dc4294871ef8e186855f3c)

![img](https://www.yuque.com/api/filetransfer/images?url=https%3A%2F%2Fgitee.com%2Fvectorx%2FImageCloud%2Fraw%2Fmaster%2Fimg%2F20210510195913.png&sign=ae696c1adff2462ceecca1978bb097d740b0fbd39a05e3c4a2a8c3e7fa4c59cc)

![img](https://www.yuque.com/api/filetransfer/images?url=https%3A%2F%2Fgitee.com%2Fvectorx%2FImageCloud%2Fraw%2Fmaster%2Fimg%2F20210510152246.png&sign=f1663822bb08778b2380c58df7e449ea16280965b283d8d5260347413a9776d0)



#### 3.7.7 方法区的演进细节

首先明确，只有HotSpot才有永久代

| JDK1.6及之前 | ==有永久代，静态变量存放在永久代上==                         |
| ------------ | ------------------------------------------------------------ |
| **JDK1.7**   | ==**有永久代，但已逐步去永久代，字符串常量池、静态变量移除，存放在堆中**== |
| **JDK1.8**   | **==无永久代，类型信息，字段，常量，方法保存在本地内存的元空间中，但字符串常量池，静态变量仍然在堆中==** |



![img](https://www.yuque.com/api/filetransfer/images?url=https%3A%2F%2Fgitee.com%2Fvectorx%2FImageCloud%2Fraw%2Fmaster%2Fimg%2F20210510162615.png&sign=eb201bcc10a31832db815ef942725ddbf29c2793f32ff04c70d80b116fcda74e)



![img](https://www.yuque.com/api/filetransfer/images?url=https%3A%2F%2Fgitee.com%2Fvectorx%2FImageCloud%2Fraw%2Fmaster%2Fimg%2F20210510162620.png&sign=d87666813c41ae50f39af7c578effe21b507448b3c38a8da7610c6e8e052fa2f)



![img](https://www.yuque.com/api/filetransfer/images?url=https%3A%2F%2Fgitee.com%2Fvectorx%2FImageCloud%2Fraw%2Fmaster%2Fimg%2F20210510162628.png&sign=aa875b9fb719513c18ecdc75180b848f58602b727d813f0bd4005dfaa066ba14)



> **永久代为什么被元空间替换**

官网地址：[JEP 122: Remove the Permanent Generation (java.net)](http://openjdk.java.net/jeps/122)

单纯来说就是==JRockit是和HotSpot融合后的结果，因为JRockit没有永久代，所以他们不需要配置永久代==





随着Java8的到来，HotSpot VM中再也见不到永久代了。但是这并不意味着类的元数据信息也消失了。这些数据被移到了一个与堆不相连的本地内存区域，这个区域叫做元空间（Metaspace）。

由于类的元数据分配在本地内存中，元空间的最大可分配空间就是系统可用内存空间。

这项改动是很有必要的，原因有：

-  ==为永久代设置空间大小是很难确定的==。在某些场景下，如果动态加载类过多，容易产生Perm区的oom。比如某个实际Web工 程中，因为功能点比较多，在运行过程中，要不断动态加载很多类，经常出现致命错误。 
  而元空间和永久代之间最大的区别在于：元空间并不在虚拟机中，而是使用本地内存。 因此，默认情况下，元空间的大小仅受本地内存限制。 
- ==对永久代进行调优是很困难的==。



> **StringTable为什么要调整位置**

jdk7中将StringTable放到了堆空间中。==因为永久代的回收效率很低，在full gc的时候才会触发。而full gc是老年代的空间不足、永久代不足时才会触发。这就导致StringTable回收效率不高。而我们开发中会有大量的字符串被创建，回收效率低，导致永久代内存不足。==放到堆里，能及时回收内存。



> **静态变量放在哪**

```java
/**
 * 静态引用对应的对象实体始终都存在堆空间（即new出来的都存放在堆中）
 * jdk7:
 * -Xms200m -Xmx200m -XX:PermSize=300m -XX:MaxPermSize=300m -XX:+PrintGCDetails
 * jdk8:
 * -Xms200m -Xmx200m-XX:MetaspaceSize=300m -XX:MaxMetaspaceSize=300m -XX:+PrintGCDetails
 */
public class StaticFieldTest {
    private static byte[] arr = new byte[1024 * 1024 * 100];
    public static void main(String[] args) {
        System.out.println(StaticFieldTest.arr);
        
        try {
            Thread.sleep(1000000);
        } catch (InterruptedException e){
            e.printStackTrace();
        }
    }
}
```



```java
/**
 * staticobj、instanceobj、Localobj存放在哪里？
 */
public class StaticobjTest {
    static class Test {
        static ObjectHolder staticobj = new ObjectHolder();
        ObjectHolder instanceobj = new ObjectHolder();
        void foo(){
            ObjectHolder localobj = new ObjectHolder();
            System.out.println("done");
        }    
    }
    private static class ObjectHolder{
        public static void main(String[] args) {
            Test test = new StaticobjTest.Test();
            test.foo();
        }
    }
}
```

使用JHSDB工具进行分析，这里细节略掉

![img](https://www.yuque.com/api/filetransfer/images?url=https%3A%2F%2Fgitee.com%2Fvectorx%2FImageCloud%2Fraw%2Fmaster%2Fimg%2F20210510164814.png&sign=9a6548675f7af809a86af5a649ca45912d7318b26d5c74aa0a17dac4da047b13)

staticobj随着Test的类型信息存放在方法区，instanceobj随着Test的对象实例存放在Java堆，localobject则是存放在foo()方法栈帧的局部变量表中。

测试发现：三个对象的数据在内存中的地址都落在Eden区范围内，所以结论：==只要是对象实例必然会在Java堆中分配。==

接着，找到了一个引用该staticobj对象的地方，是在一个java.lang.Class的实例里，并且给出了这个实例的地址，通过Inspector查看该对象实例，可以清楚看到这确实是一个java.lang.Class类型的对象实例，里面有一个名为staticobj的实例字段：

从《Java虚拟机规范》所定义的概念模型来看，==所有Class相关的信息都应该存放在方法区之中，但方法区该如何实现，《Java虚拟机规范》并未做出规定，这就成了一件允许不同虚拟机自己灵活把握的事情。JDK7及其以后版本的HotSpot虚拟机选择把静态变量与类型在Java语言一端的映射class对象存放在一起，存储于Java堆之中，从我们的实验中也明确验证了这一点==



#### 3.7.8 方法区的垃圾回收

有些人认为方法区（如HotSpot虚拟机中的元空间或者永久代）是没有垃圾收集行为的，其实不然。《Java虚拟机规范》对方法区的约束是非常宽松的，提到过可以不要求虚拟机在方法区中实现垃圾收集。事实上也确实有未实现或未能完整实现方法区类型卸载的收集器存在（如JDK 11时期的ZGC收集器就不支持类卸载）。 ==一般来说这个区域的回收效果比较难令人满意，尤其是类型的卸载，条件相当苛刻。但是这部分区域的回收有时又确实是必要的。==以前Sun公司的Bug列表中，曾出现过的若干个严重的Bug就是由于低版本的HotSpot虚拟机对此区域未完全回收而导致内存泄漏



==方法区的垃圾收集主要回收两部分内容：常量池中废弃的常量和不再使用的类型==

- 先来说说方法区内常量池之中主要存放的两大类常量：字面量和符号引用。字面量比较接近Java语言层次的常量概念，如文本字符串、被声明为final的常量值等。而符号引用则属于编译原理方面的概念，包括下面三类常量：

- 类和接口的全限定名

- 字段的名称和描述符

- 方法的名称和描述符



HotSpot虚拟机对常量池的回收策略是很明确的，只要常量池中的常量没有被任何地方引用，就可以被回收。

回收废弃常量与回收Java堆中的对象非常类似。

判定一个常量是否“废弃”还是相对简单，而要判定一个类型是否属于“不再被使用的类”的条件就比较苛刻了。需要同时满足下面三个条件：

-  该类所有的实例都已经被回收，也就是Java堆中不存在该类及其任何派生子类的实例。 

-  加载该类的类加载器已经被回收，这个条件除非是经过精心设计的可替换类加载器的场景，如OSGi、JSP的重加载等，否则通常是很难达成的。 

-  该类对应的java.lang.Class对象没有在任何地方被引用，无法在任何地方通过反射访问该类的方法。 



Java虚拟机被允许对满足上述三个条件的无用类进行回收，这里说的仅仅是“被允许”，而并不是和对象一样，没有引用了就必然会回收。关于是否要对类型进行回收，HotSpot虚拟机提供了`-Xnoclassgc`参数进行控制，还可以使用`-verbose:class` 以及 `-XX:+TraceClassLoading`、`-XX:+TraceClassUnLoading`查看类加载和卸载信息

在大量使用反射、动态代理、CGLib等字节码框架，动态生成JSP以及OSGi这类频繁自定义类加载器的场景中，通常都需要Java虚拟机具备类型卸载的能力，以保证不会对方法区造成过大的内存压力。



#### 3.7.9 总结

![img](https://www.yuque.com/api/filetransfer/images?url=https%3A%2F%2Fgitee.com%2Fvectorx%2FImageCloud%2Fraw%2Fmaster%2Fimg%2F20210510165830.png&sign=96b3293a7cb8a79247a54beaee78a45619a6c6ab8e645b486980f9419cb95976)



#### 常见面试题

> 百度：
>
> 说一下JVM内存模型吧，有哪些区？分别干什么的？
>
>  
>
> 蚂蚁金服：
>
>  
>
> Java8的内存分代改进 JVM内存分哪几个区，每个区的作用是什么？
>
>  
>
> 一面：JVM内存分布/内存结构？栈和堆的区别？堆的结构？为什么两个survivor区？
>
>  
>
> 二面：Eden和survior的比例分配
>
>  
>
> 小米：
>
>  
>
> jvm内存分区，为什么要有新生代和老年代
>
>  
>
> 字节跳动：
>
>  
>
> 二面：Java的内存分区
>
>  
>
> 二面：讲讲vm运行时数据库区 什么时候对象会进入老年代？
>
>  
>
> 京东：
>
>  
>
> JVM的内存结构，Eden和Survivor比例。
>
>  
>
> JVM内存为什么要分成新生代，老年代，持久代。
>
>  
>
> 新生代中为什么要分为Eden和survivor。
>
>  
>
> 天猫：
>
>  
>
> 一面：Jvm内存模型以及分区，需要详细到每个区放什么。
>
>  
>
> 一面：JVM的内存模型，Java8做了什么改
>
>  
>
> 拼多多：
>
>  
>
> JVM内存分哪几个区，每个区的作用是什么？
>
>  
>
> 美团：
>
>  
>
> java内存分配 jvm的永久代中会发生垃圾回收吗？
>
>  
>
> 一面：jvm内存分区，为什么要有新生代和老年代？

 



## 4.对象的实例化内存布局与访问定位

### 4.1 对象的实例化

![img](https://www.yuque.com/api/filetransfer/images?url=https%3A%2F%2Fgitee.com%2Fvectorx%2FImageCloud%2Fraw%2Fmaster%2Fimg%2F20210510220702.png&sign=80c9df04dbd0f049a9842bc91fe08dfb3c841a844ad99c33af6ab47970c47e4f)

- new：最常见的方式、Xxx的静态方法，XxxBuilder/XxxFactory的静态方法

- Class的newInstance方法：反射的方式，只能调用空参的构造器，权限必须是public

- Constructor的newInstance(XXX)：反射的方式，可以调用空参、带参的构造器，权限没有要求

- 使用clone()：不调用任何的构造器，要求当前的类需要实现Cloneable接口，实现clone()

- 使用序列化：从文件中、从网络中获取一个对象的二进制流

- 第三方库 Objenesis



### 4.2 对象创建的过程

![img](https://www.yuque.com/api/filetransfer/images?url=https%3A%2F%2Fgitee.com%2Fvectorx%2FImageCloud%2Fraw%2Fmaster%2Fimg%2F20210510220744.png&sign=5517e0cc559c33c8de130754b8ae3d6d7a4f0689604fcebd6080c4408e12312d)

1. > 判断对象对应的类是否加载，链接，初始化

   虚拟机遇到一条new指令，首先去检查这个指令的参数能否在Metaspace的常量池中定位到一个类的符号引用，并且检查这个符号引用代表的类是否已经被加载，解析和初始化（即判断类元信息是否存在）。

   如果没有，那么在双亲委派模式下，使用当前类加载器以ClassLoader + 包名 + 类名为key进行查找对应的 .class文件；

   - 如果没有找到文件，则抛出ClassNotFoundException异常

   - 如果找到，则进行类加载，并生成对应的Class对象

2. > 为对象分配内存

   首先计算对象占用空间的大小，接着在堆中划分一块内存给新对象。如果实例成员变量是引用变量，仅分配引用变量空间即可，即4个字节大小

   **如果内存规整**：虚拟机将采用的是指针碰撞法（Bump The Point）来为对象分配内存。

   - 意思是所有用过的内存在一边，空闲的内存放另外一边，中间放着一个指针作为分界点的指示器，分配内存就仅仅是把指针指向空闲那边挪动一段与对象大小相等的距离罢了。如果垃圾收集器选择的是Serial ，ParNew这种基于压缩算法的，虚拟机采用这种分配方式。一般使用带Compact（整理）过程的收集器时，使用指针碰撞。

   **如果内存不规整**：虚拟机需要维护一个空闲列表（Free List）来为对象分配内存。

   - 已使用的内存和未使用的内存相互交错，那么虚拟机将采用的是空闲列表来为对象分配内存。意思是虚拟机维护了一个列表，记录上那些内存块是可用的，再分配的时候从列表中找到一块足够大的空间划分给对象实例，并更新列表上的内容。

   

   选择哪种分配方式由Java堆是否规整所决定，而Java堆是否规整又由所采用的垃圾收集器是否带有压缩整理功能决定。

3. > 解决并发问题

   - 采用CAS失败重试、区域加锁保证更新的原子性

   - 每个线程预先分配一块TLAB：通过设置 `-XX:+UseTLAB`参数来设定

4. > 初始化分配到内存

   所有属性设置默认值，保证对象实例字段在不赋值时可以直接使用

5. > 给对象设置对象头

   将对象的所属类（即类的元数据信息）、对象的HashCode和对象的GC信息、锁信息等数据存储在对象的对象头中。这个过程的具体设置方式取决于JVM实现。

6. > 执行init方法进行初始化

   在Java程序的视角看来，初始化才正式开始。初始化成员变量，执行实例化代码块，调用类的构造方法，并把堆内对象的首地址赋值给引用变量。

   

   因此一般来说（由字节码中跟随invokespecial指令所决定），new指令之后会接着就是执行方法，把对象按照程序员的意愿进行初始化，这样一个真正可用的对象才算完成创建出来。



### 4.3 对象的内存布局

![img](https://www.yuque.com/api/filetransfer/images?url=https%3A%2F%2Fgitee.com%2Fvectorx%2FImageCloud%2Fraw%2Fmaster%2Fimg%2F20210510224327.png&sign=10c5f3c8761585c96b99b53e56ed7c84c535ce4238621bc7cce36816ef328f1b)



![img](https://pic.imgdb.cn/item/60194fe03ffa7d37b3817838.png)



### 4.4 对象的访问定位

JVM是如何通过栈帧中的对象的引用访问到其内部的对象实例的呢？

==**句柄访问**==

![img](https://pic.imgdb.cn/item/601952943ffa7d37b382a345.png)

好处：在对象被清理时只需要改变句柄池到对象实例的指针，栈中无需改变

坏处：拿到对象的实例数据需要访问两次，速度慢



==**直接指针**==

![img](https://pic.imgdb.cn/item/6019529f3ffa7d37b382a8ea.png)

好处：只需要一次访问就可拿到对象，因此Hotspot VM使用这种

坏处：在对象被清理时需要改变栈中的指针



## 5.直接内存

不是虚拟机运行时数据区的一部分，也不是《Java虚拟机规范》中定义的内存区域。直接内存是在Java堆外的、直接向系统申请的内存区间。来源于NIO，通过存在堆中的DirectByteBuffer操作Native内存。通常，访问直接内存的速度会优于Java堆，即读写性能高。

- 因此出于性能考虑，读写频繁的场合可能会考虑使用直接内存。

- Java的NIO库允许Java程序使用直接内存，用于数据缓冲区



![img](https://pic.imgdb.cn/item/601954493ffa7d37b3838de6.png)![img](https://pic.imgdb.cn/item/601954683ffa7d37b3839897.png)



- 直接内存也可能导致OOM
- 由于直接内存在堆外，因此它的大小不会直接受限于-Xmx指定的最大堆大小，但是系统内存是有限的，Java堆和直接内存的总和依然受限于操作系统给出的最大内存
- 缺点
  - 分配回收成本高
  - 不受JVM内存回收管理
- 直接内存大小可以通过MaxDirectMemorySize设置
- 如果不指定，默认与堆的最大值-Xmx参数值一致



## 6.执行引擎

### 6.1 执行引擎概述

执行引擎属于JVM的下层，里面包括==解释器、及时编译器、垃圾回收器==

![img](https://www.yuque.com/api/filetransfer/images?url=https%3A%2F%2Fgitee.com%2Fvectorx%2FImageCloud%2Fraw%2Fmaster%2Fimg%2F20210511090403.png&sign=36d338cbc3f3e290777c1ef52cf6affe347cf9c55ff614bbc2a532087bea0f11)

执行引擎是Java虚拟机核心的组成部分之一。



“虚拟机”是一个相对于“物理机”的概念，这两种机器都有代码执行能力，其区别是物理机的执行引擎是直接建立在处理器、缓存、指令集和操作系统层面上的，而虚拟机的执行引擎则是由软件自行实现的，因此可以不受物理条件制约地定制指令集与执行引擎的结构体系，能够执行那些不被硬件直接支持的指令集格式。



JVM的主要任务是==负责装载字节码到其内部==，但字节码并不能够直接运行在操作系统之上，因为字节码指令并非等价于本地机器指令，它内部包含的仅仅只是一些能够被JVM所识别的字节码指令、符号表，以及其他辅助信息。

![img](https://www.yuque.com/api/filetransfer/images?url=https%3A%2F%2Fgitee.com%2Fvectorx%2FImageCloud%2Fraw%2Fmaster%2Fimg%2F20210511090550.png&sign=461d37cb07a6de9aa9791adab64ed37acfee1cbb7919b0f36b7dfcc6f6d2868e)

那么，如果想要让一个Java程序运行起来，执行引擎（Execution Engine）的任务就是将==字节码指令解释/编译为对应平台上的本地机器指令.==才可以。简单来说，JVM中的执行引擎充当了将高级语言翻译为机器语言的译者。



### 6.2 执行引擎工作流程

1. 执行引擎在执行的过程中究竟需要执行什么样的字节码指令完全依赖于PC寄存器。

2. 每当执行完一项指令操作后，PC寄存器就会更新下一条需要被执行的指令地址。

3. 当然方法在执行的过程中，执行引擎有可能会通过存储在局部变量表中的对象引用准确定位到存储在Java堆区中的对象实例信息，以及通过对象头中的元数据指针定位到目标对象的类型信息

![img](https://www.yuque.com/api/filetransfer/images?url=https%3A%2F%2Fgitee.com%2Fvectorx%2FImageCloud%2Fraw%2Fmaster%2Fimg%2F20210511090722.png&sign=a73055ecc7408cd73151b1b3de34b99198a9ac866059485e712d5fdf84850e18)



从外观上来看，所有的Java虚拟机的执行引擎输入，输出都是一致的：输入的是字节码二进制流，处理过程是字节码解析执行的等效过程，输出的是执行过程。



### 6.3 Java代码编译和执行的过程

![img](https://www.yuque.com/api/filetransfer/images?url=https%3A%2F%2Fgitee.com%2Fvectorx%2FImageCloud%2Fraw%2Fmaster%2Fimg%2F20210511090933.png&sign=7ce3187bb11ab0e315323ed6b32c79cfdce2ad9989917c3c468540523a848b0c)

大部分的程序代码转换成物理机的目标代码或虚拟机能执行的指令集之前，都需要经过上图中的各个步骤



Java代码编译是由Java源码编译器（前端编译器）来完成，流程图如下所示：

![img](https://www.yuque.com/api/filetransfer/images?url=https%3A%2F%2Fgitee.com%2Fvectorx%2FImageCloud%2Fraw%2Fmaster%2Fimg%2F20210511091014.png&sign=5efb9227c6e5b76de4653a87e2c321b23513cfa948ba91be7755f48ed5995373)



Java字节码的执行是由JVM执行引擎（后端编译器）来完成，流程图 如下所示

![img](https://www.yuque.com/api/filetransfer/images?url=https%3A%2F%2Fgitee.com%2Fvectorx%2FImageCloud%2Fraw%2Fmaster%2Fimg%2F20210511091120.png&sign=ae5635beaa7797154f566f2e5cf36123ab95b66771c51f221ff2c722f8d32e22)



> 什么是解释器，什么是JIT编译器

解释器：当Java虚拟机启动时会根据预定义的规范对字节码采用==逐行解释的方式执行==，将每条字节码文件中的内容“翻译”为对应平台的本地机器指令执行。



JIT（Just In Time Compiler）编译器：就是虚拟机将源代码直接编译成和本地机器平台相关的机器语言。



> 为什么Java是半解释半编译型语言

JDK1.0时代，将Java语言定位为“解释执行”还是比较准确的。再后来，Java也发展出可以直接生成本地代码的编译器。现在JVM在执行Java代码的时候，通常都会将解释执行与编译执行二者结合起来进行。

![img](https://www.yuque.com/api/filetransfer/images?url=https%3A%2F%2Fgitee.com%2Fvectorx%2FImageCloud%2Fraw%2Fmaster%2Fimg%2F20210511091215.png&sign=91861760462d20c5c5864d361c9030c9c26180bad315b71bf372fe2e8366a748)



> 机器码、指令、汇编语言

**机器码**

- 各种用二进制编码方式表示的指令，叫做机器指令码。开始，人们就用它采编写程序，这就是机器语言。
- 机器语言虽然能够被计算机理解和接受，但和人们的语言差别太大，不易被人们理解和记忆，并且用它编程容易出差错。
- 用它编写的程序一经输入计算机，CPU直接读取运行，因此和其他语言编的程序相比，执行速度最快。
- 机器指令与CPU紧密相关，所以不同种类的CPU所对应的机器指令也就不同。



**指令**

- 由于机器码是有0和1组成的二进制序列，可读性实在太差，于是人们发明了指令。

- 指令就是把机器码中特定的0和1序列，简化成对应的指令（一般为英文简写，如mov，inc等），可读性稍好

- 由于不同的硬件平台，执行同一个操作，对应的机器码可能不同，所以不同的硬件平台的同一种指令（比如mov），对应的机器码也可能不同。



不同的硬件平台，各自支持的指令，是有差别的。因此每个平台所支持的指令，称之为对应平台的指令集。 如常见的



- x86指令集，对应的是x86架构的平台

- ARM指令集，对应的是ARM架构的平台



**指令集**

- 不同的硬件平台，各自支持的指令，是有差别的。因此每个平台所支持的指令，称之为对应平台的指令集。 如常见的

  - x86指令集，对应的是x86架构的平台

  - ARM指令集，对应的是ARM架构的平台



**汇编语言**

由于指令的可读性还是太差，于是人们又发明了汇编语言。



在汇编语言中，用==助记符（Mnemonics）代替机器指令的操作码，用<mark地址符号（Symbol）或标号（Label）代替指令或操作数的地址==。在不同的硬件平台，汇编语言对应着不同的机器语言指令集，通过汇编过程转换成机器指令。



由于计算机只认识指令码，所以用汇编语言编写的程序还必须翻译成机器指令码，计算机才能识别和执行。



**高级语言**

为了使计算机用户编程序更容易些，后来就出现了各种高级计算机语言。高级语言比机器语言、汇编语言更接近人的语言



当计算机执行高级语言编写的程序时，==仍然需要把程序解释和编译成机器的指令码==。完成这个过程的程序就叫做解释程序或编译程序。



![img](https://www.yuque.com/api/filetransfer/images?url=https%3A%2F%2Fgitee.com%2Fvectorx%2FImageCloud%2Fraw%2Fmaster%2Fimg%2F20210511092009.png&sign=ac4eb6c3276d02f8752d03b3f961219730505ceddefb618ac9f9ab10387fbee3)





**字节码**

字节码是一种中间状态（中间码）的二进制代码（文件），它比机器码更抽象，需要直译器转译后才能成为机器码



字节码主要为了实现特定软件运行和软件环境、与硬件环境无关。



字节码的实现方式是通过编译器和虚拟机器。编译器将源码编译成字节码，特定平台上的虚拟机器将字节码转译为可以直接执行的指令。字节码典型的应用为：Java bytecode



![img](https://www.yuque.com/api/filetransfer/images?url=https%3A%2F%2Fgitee.com%2Fvectorx%2FImageCloud%2Fraw%2Fmaster%2Fimg%2F20210511092338.png&sign=6582e6d287974808e4480e16afa7a36e84e64d4fc7b70a25e527111880abc9a3)



### 6.4 JIT编译器

**Java代码的分类**

- 第一种是将源代码编译成字节码文件，然后在运行时通过解释器将字节码文件转为机器码执行 

-  第二种是编译执行（直接编译成机器码，但是要知道不同机器上编译的机器码是不一样，而字节码是可以跨平台的）。现代虚拟机为了提高执行效率，会使用即时编译技术（JIT，Just In Time）将方法编译成机器码后再执行 



HotSpot VM是目前市面上高性能虚拟机的代表作之一。==它采用解释器与即时编译器并存的架构。在Java虚拟机运行时，解释器和即时编译器能够相互协作，各自取长补短，尽力去选择最合适的方式来权衡编译本地代码的时间和直接解释执行代码的时间。==

在今天，Java程序的运行性能早已脱胎换骨，已经达到了可以和C/C++ 程序一较高下的地步。



**问题来了**

有些开发人员会感觉到诧异，既然HotSpot VM中已经内置JIT编译器了，那么为什么还需要再使用解释器来“拖累”程序的执行性能呢？比如JRockit VM内部就不包含解释器，字节码全部都依靠即时编译器编译后执行。

首先明确： ==当程序启动后，解释器可以马上发挥作用，省去编译的时间，立即执行。 编译器要想发挥作用，把代码编译成本地代码，需要一定的执行时间。但编译为本地代码后，执行效率高==。

所以： 尽管JRockit VM中程序的执行性能会非常高效，但程序在启动时必然需要花费更长的时间来进行编译。对于服务端应用来说，启动时间并非是关注重点，但对于那些看中启动时间的应用场景而言，或许就需要采用解释器与即时编译器并存的架构来换取一个平衡点。==在此模式下，当Java虚拟器启动时，解释器可以首先发挥作用，而不必等待即时编译器全部编译完成后再执行，这样可以省去许多不必要的编译时间。随着时间的推移，编译器发挥作用，把越来越多的代码编译成本地代码，获得更高的执行效率。==

同时，解释执行在编译器进行激进优化不成立的时候，作为编译器的“逃生门”。



> HotSpot JVM执行方式

当虚拟机启动的时候，==解释器可以首先发挥作用，而不必等待即时编译器全部编译完成再执行，这样可以省去许多不必要的编译时间。并且随着程序运行时间的推移，即时编译器逐渐发挥作用，根据热点探测功能，将有价值的字节码编译为本地机器指令，以换取更高的程序执行效率。==



### 6.5 编译概念解释

Java 语言的“编译期”其实是一段“不确定”的操作过程，因为它可能是指一个前端编译器（其实叫“编译器的前端”更准确一些）把.java文件转变成.class文件的过程；

也可能是指虚拟机的后端运行期编译器（JIT编译器，Just In Time Compiler）把字节码转变成机器码的过程。

还可能是指使用静态提前编译器（AOT编译器，Ahead of Time Compiler）直接把.java文件编译成本地机器代码的过程。



-  前端编译器：Sun的Javac、Eclipse JDT中的增量式编译器（ECJ）。 

-  JIT编译器：HotSpot VM的C1、C2编译器。 

-  AOT 编译器：GNU Compiler for the Java（GCJ）、Excelsior JET。 



### 6.6 热点代码及探测技术

当然是否需要启动JIT编译器将字节码直接编译为对应平台的本地机器指令，则需要根据代码被调用执行的频率而定。关于那些需要被编译为本地代码的字节码，也被称之为“热点代码”，JIT编译器在运行时会针对那些频繁被调用的“热点代码”做出深度优化，将其直接编译为对应平台的本地机器指令，以此提升Java程序的执行性能。



==一个被多次调用的方法，或者是一个方法体内部循环次数较多的循环体都可以被称之为“热点代码”==，因此都可以通过JIT编译器编译为本地机器指令。由于这种编译方式发生在方法的执行过程中，因此被称之为栈上替换，或简称为OSR（On Stack Replacement）编译。



一个方法究竟要被调用多少次，或者一个循环体究竟需要执行多少次循环才可以达到这个标准？必然需要一个明确的阈值，JIT编译器才会将这些“热点代码”编译为本地机器指令执行。==这里主要依靠热点探测功能==。



目前HotSpot VM所采用的热点探测方式是==基于计数器的热点探测。==



采用基于计数器的热点探测，HotSpot VM将会为每一个方法都建立2个不同类型的计数器，分别为==方法调用计数器（Invocation Counter）和回边计数器（Back Edge Counter）。==

- 方法调用计数器用于统计方法的调用次数

- 回边计数器则用于统计循环体执行的循环次数



#### 6.6.1 方法调用计数器

==这个计数器就用于统计方法被调用的次数，它的默认阀值在Client模式下是1500次，在Server模式下是10000次。超过这个阈值，就会触发JIT编译。==



这个阀值可以通过虚拟机参数 `-XX:CompileThreshold`来人为设定。



==当一个方法被调用时，会先检查该方法是否存在被JIT编译过的版本，如果存在，则优先使用编译后的本地代码来执行。如果不存在已被编译过的版本，则将此方法的调用计数器值加1，然后判断方法调用计数器与回边计数器值之和是否超过方法调用计数器的阀值。如果已超过阈值，那么将会向即时编译器提交一个该方法的代码编译请求。==

![img](https://www.yuque.com/api/filetransfer/images?url=https%3A%2F%2Fgitee.com%2Fvectorx%2FImageCloud%2Fraw%2Fmaster%2Fimg%2F20210511095530.png&sign=418bff52de9c1b21a51fcb06c5ad8faeab631a7ff99efc5e918aac85310b71b1)

**热点衰减**

如果不做任何设置，==方法调用计数器统计的并不是方法被调用的绝对次数，而是一个相对的执行频率，即一段时间之内方法被调用的次数。当超过一定的时间限度，如果方法的调用次数仍然不足以让它提交给即时编译器编译，那这个方法的调用计数器就会被减少一半，==这个过程称为方法调用计数器热度的衰减（Counter Decay），而这段时间就称为此方法统计的半衰周期（Counter Half Life Time）



进行热度衰减的动作是在虚拟机进行垃圾收集时顺便进行的，可以使用虚拟机参数 `-XX:-UseCounterDecay` 来关闭热度衰减，让方法计数器统计方法调用的绝对次数，这样，只要系统运行时间足够长，绝大部分方法都会被编译成本地代码。



另外，可以使用`-XX:CounterHalfLifeTime`参数设置半衰周期的时间，单位是秒。



#### 6.6.2 回边计数器

它的作用是==统计一个方法中循环体代码执行的次数==，在字节码中遇到控制流向后跳转的指令称为“回边”（Back Edge）。显然，建立回边计数器统计的目的就是为了触发OSR编译。

![img](https://www.yuque.com/api/filetransfer/images?url=https%3A%2F%2Fgitee.com%2Fvectorx%2FImageCloud%2Fraw%2Fmaster%2Fimg%2F20210511100031.png&sign=e9dacd48b722984ee107ab756545f8196717ac69091f08dc73f09869d3bca157)



### 6.7 HotSpotVM 可以设置程序执行方法

缺省情况下HotSpot VM是采用解释器与即时编译器并存的架构，当然开发人员可以根据具体的应用场景，通过命令显式地为Java虚拟机指定在运行时到底是完全采用解释器执行，还是完全采用即时编译器执行。如下所示：



- `-Xint`：完全采用解释器模式执行程序；

- `-Xcomp`：完全采用即时编译器模式执行程序。如果即时编译出现问题，解释器会介入执行

- `-Xmixed`：采用解释器+即时编译器的混合模式共同执行程序。





### 6.8 HotSpotVM中 JIT 分类

JIT的编译器还分为了两种，分别是C1和C2，==在HotSpot VM中内嵌有两个JIT编译器，分别为Client Compiler和Server Compiler，但大多数情况下我们简称为C1编译器 和 C2编译器==。开发人员可以通过如下命令显式指定Java虚拟机在运行时到底使用哪一种即时编译器，如下所示：



- `-client`：指定Java虚拟机运行在Client模式下，并使用C1编译器；C1编译器会对字节码进行简单和可靠的优化，耗时短，以达到更快的编译速度。

- `-server`：指定Java虚拟机运行在server模式下，并使用C2编译器。C2进行耗时较长的优化，以及激进优化，但优化的代码执行效率更高。



分层编译（Tiered Compilation）策略：==程序解释执行（不开启性能监控）可以触发C1编译，将字节码编译成机器码，可以进行简单优化，也可以加上性能监控，C2编译会根据性能监控信息进行激进优化。==



不过在Java7版本之后，一旦开发人员在程序中显式指定命令“-server"时，默认将会开启分层编译策略，由C1编译器和C2编译器相互协作共同来执行编译任务。



#### 6.8.1 C1 和 C2编译器不同的优化策略



在不同的编译器上有不同的优化策略，C1编译器上主要有方法内联、去虚拟化、冗余消除。

- 方法内联：将引用的函数代码编译到引用点处，这样可以减少栈帧的生成，减少参数传递以及跳转过程

- 去虚拟化：对唯一的实现类进行内联

- 冗余消除：在运行期间把一些不会执行的代码折叠掉



C2的优化主要是在全局层面，逃逸分析（前面讲过，并不成熟）是优化的基础。基于逃逸分析在C2上有如下几种优化：

- 标量替换：用标量值代替聚合对象的属性值

- 栈上分配：对于未逃逸的对象分配对象在栈而不是堆

- 同步消除：清除同步操作，通常指synchronized



## 7.StringTable

### 7.1 String的基本特性

- String：字符串，使用一对""引起来表示

- String声明为final的，不可被继承

- String实现了Serializable接口：表示字符串是支持序列化的。

- String实现了Comparable接口：表示string可以比较大小

- String在jdk8及以前内部定义了final char[] value用于存储字符串数据。JDK9时改为byte[]





String：代表不可变的字符序列。简称：不可变性。

- 当对字符串重新赋值时，需要重写指定内存区域赋值，不能使用原有的value进行赋值。

- 当对现有的字符串进行连接操作时，也需要重新指定内存区域赋值，不能使用原有的value进行赋值。

- 当调用string的replace()方法修改指定字符或字符串时，也需要重新指定内存区域赋值，不能使用原有的value进行赋值。

通过字面量的方式（区别于new）给一个字符串赋值，此时的字符串值声明在字符串常量池中。

字符串常量池是不会存储相同内容的字符串的

String的String Pool是一个固定大小的Hashtable，默认值大小长度是1009。如果放进String Pool的String非常多，就会造成Hash冲突严重，从而导致链表会很长，而链表长了后直接会造成的影响就是当调用String.intern时性能会大幅下降。

使用`-XX:StringTablesize`可设置StringTable的长度

-  在jdk6中StringTable是固定的，就是1009的长度，所以如果常量池中的字符串过多就会导致效率下降很快。StringTablesize设置没有要求 

-  在jdk7中，StringTable的长度默认值是60013，StringTablesize设置没有要求 

-  在JDK8中，设置StringTable长度的话，1009是可以设置的最小值 



> String在jdk9中的存储结构的变更

官网地址：[JEP 254: Compact Strings (java.net)](http://openjdk.java.net/jeps/254)

**动机**

目前String类的实现将字符存储在一个char数组中，每个字符使用两个字节（16位）。从许多不同的应用中收集到的数据表明，字符串是堆使用的主要组成部分，此外，大多数字符串对象只包含Latin-1字符。这些字符只需要一个字节的存储空间，因此这些字符串对象的内部字符数组中有一半的空间没有被使用。

**说明**

我们建议将String类的内部表示方法从UTF-16字符数组改为字节数组加编码标志域。新的String类将根据字符串的内容，以ISO-8859-1/Latin-1（每个字符一个字节）或UTF-16（每个字符两个字节）的方式存储字符编码。编码标志将表明使用的是哪种编码。



与字符串相关的类，如AbstractStringBuilder、StringBuilder和StringBuffer将被更新以使用相同的表示方法，HotSpot VM的内在字符串操作也是如此。

这纯粹是一个实现上的变化，对现有的公共接口没有变化。目前没有计划增加任何新的公共API或其他接口。

迄今为止所做的原型设计工作证实了内存占用的预期减少，GC活动的大幅减少，以及在某些角落情况下的轻微性能倒退。

结论：String再也不用char[] 来存储了，改成了byte [] 加上编码标记，节约了一些空间

```java
public final class String implements java.io.Serializable, Comparable<String>, CharSequence {
    @Stable
    private final byte[] value;
}
```



### 7.2 String内存分配

在Java语言中有8种基本数据类型和一种比较特殊的类型String。这些类型为了使它们在运行过程中速度更快、更节省内存，都提供了一种常量池的概念。



常量池就类似一个Java系统级别提供的缓存。8种基本数据类型的常量池都是系统协调的，String类型的常量池比较特殊。它的主要使用方法有两种。

-  直接使用双引号声明出来的String对象会直接存储在常量池中。 

-  如果不是用双引号声明的String对象，可以使用String提供的intern()方法。这个后面重点谈 



==Java 6及以前，字符串常量池存放在永久代==

==Java 7中 Oracle的工程师对字符串池的逻辑做了很大的改变，即将字符串常量池的位置调整到Java堆内==

-  所有的字符串都保存在堆（Heap）中，和其他普通对象一样，这样可以让你在进行调优应用时仅需要调整堆大小就可以了。 

-  字符串常量池概念原本使用得比较多，但是这个改动使得我们有足够的理由让我们重新考虑在Java 7中使用`String.intern()`。 

==Java8元空间，字符串常量在堆==

![img](https://www.yuque.com/api/filetransfer/images?url=https%3A%2F%2Fgitee.com%2Fvectorx%2FImageCloud%2Fraw%2Fmaster%2Fimg%2F20210511105955.png&sign=2fc39e101663fa00b69dbf49fe51298119013092a8d49c8c06a509da8b0917ea)

![img](https://www.yuque.com/api/filetransfer/images?url=https%3A%2F%2Fgitee.com%2Fvectorx%2FImageCloud%2Fraw%2Fmaster%2Fimg%2F20210511110013.png&sign=7ab140cd4c703cde052fcf3aa68b799a0607f4878388eb79c64b1111dee2f33d)



**StringTable为什么要调整？**

官网地址：[Java SE 7 Features and Enhancements (oracle.com)](https://www.oracle.com/java/technologies/javase/jdk7-relnotes.html#jdk7changes)

> **Synopsis:** In JDK 7, interned strings are no longer allocated in the permanent generation of the Java heap, but are instead allocated in the main part of the Java heap (known as the young and old generations), along with the other objects created by the application. This change will result in more data residing in the main Java heap, and less data in the permanent generation, and thus may require heap sizes to be adjusted. Most applications will see only relatively small differences in heap usage due to this change, but larger applications that load many classes or make heavy use of the `String.intern()` method will see more significant differences.



简介：在JDK 7中，内部字符串不再分配在Java堆的永久代中，而是分配在Java堆的主要部分（称为年轻代和老年代），与应用程序创建的其他对象一起。这种变化将导致更多的数据驻留在主Java堆中，而更少的数据在永久代中，因此可能需要调整堆的大小。大多数应用程序将看到由于这一变化而导致的堆使用的相对较小的差异，但加载许多类或大量使用String.intern()方法的大型应用程序将看到更明显的差异。



### 7.3 String的基本操作

```java
@Test
public void test1() {
    System.out.print1n("1"); //2321
    System.out.println("2");
    System.out.println("3");
    System.out.println("4");
    System.out.println("5");
    System.out.println("6");
    System.out.println("7");
    System.out.println("8");
    System.out.println("9");
    System.out.println("10"); //2330
    System.out.println("1"); //2321
    System.out.println("2"); //2322
    System.out.println("3");
    System.out.println("4");
    System.out.println("5");
    System.out.print1n("6");
    System.out.print1n("7");
    System.out.println("8");
    System.out.println("9");
    System.out.println("10");//2330
}
```

Java语言规范里要求完全相同的字符串字面量，应该包含同样的Unicode字符序列（包含同一份码点序列的常量），并且必须是指向同一个String类实例。



```Java
class Memory {
    public static void main(String[] args) {//line 1
        int i= 1;//line 2
        Object obj = new Object();//line 3
        Memory mem = new Memory();//Line 4
        mem.foo(obj);//Line 5
    }//Line 9
    private void foo(Object param) {//line 6
        String str = param.toString();//line 7
        System.out.println(str);
    }//Line 8
}
```

![img](https://www.yuque.com/api/filetransfer/images?url=https%3A%2F%2Fgitee.com%2Fvectorx%2FImageCloud%2Fraw%2Fmaster%2Fimg%2F20210511111612.png&sign=e98745f247fe1b525d76bfd0c1c3e031526340454b0c2b4362bbea04322a5f97)



### 7.4 字符串拼接操作

- 常量与常量的拼接结果在常量池，原理是编译期优化

- 常量池中不会存在相同内容的变量

- 只要其中有一个是变量，结果就在堆中。变量拼接的原理是StringBuilder

- 如果拼接的结果调用intern()方法，则主动将常量池中还没有的字符串对象放入池中，并返回此对象地址



```Java
public class StringTest2 {
    public static void main(String[] args) {
        String s1 = "a" + "b" + "c";
        String s2 = "abc";
        System.out.println(s1 == s2);
        System.out.println(s1.equals(s2));
    }
}
```

![image-20211108113524559](F:\笔记图片\img\image-20211108113524559.png)





```Java
@Test
public void test() {
    String s1 = "JavaEE";
    String s2 = "hadoop";
    String s3 = "JavaEEhadoop";
    String s4 = "JavaEE" + "hadoop";
    String s5 = s1 + "hadoop";
    String s6 = "JavaEE" + s2;
    String s7 = s1 + s2;

    System.out.println(s3 == s4); //true
    System.out.println(s3 == s5); //false
    System.out.println(s3 == s6); //false
    System.out.println(s3 == s7); //false
    System.out.println(s5 == s6); //false
    System.out.println(s5 == s7); //flase
    System.out.println(s6 == s7); //false
    
    String s8 = s6.intern();
    System.out.println(s3 == s8); //true

}
```

![image-20211108151624294](F:\笔记图片\img\image-20211108151624294.png)

我们可以看出来 s1 + s2的执行细节

1. new了一个StringBuilder
2. 调用append()方法，拼接"JavaEE"
3. 调用append()方法，拼接"hadoop"
4. 调用toString()方法 ---->约等于于 new String("JavaEEhadoop")



```Java
/**
字符串拼接操作不一定是StringBuilder
如果拼接符号左右两边都是字符串常量或者常量引用，则仍然是编译器优化，即非StringBuilder方式
**/
@Test
public void test2() {
    final String s1 = "a";
    final String s2 = "b";
    String s3 = "ab";

    String s4 = s1 + s2;
    System.out.println(s4 == s3);
}

/**这里的s1 和 s2不再是变量，而是一个常量的引用，所以应该为true**/
```

![image-20211108152501136](F:\笔记图片\img\image-20211108152501136.png)



```Java
/**
 * 提会执行的效率
 */
@Test
public void test3() {
    long start = System.currentTimeMillis();
    method1(100000); //3549
    method2(100000); //4
    long end = System.currentTimeMillis();
    System.out.println("花费的时间为" + (end - start));
}
public void method1(int times) {
    String src = "";
    for (int i = 0;i < times;i++) {
        src = src + "a"; //每次循环都会创建StingBuilder，String，花费时间
    }
}

public void method2(int times) {
    StringBuilder stringBuilder = new StringBuilder();
    for (int i = 0;i < times;i++) {
        stringBuilder.append("a");
    }
}
```

==通过StirngBuilder的append()方法效率要远高于String的字符串拼接方式==

**StringBuilder的append()方法：**

- 自始至终只创建了一个StringBuilder对象

- 在实际开发中，如果基本确定添加的字符串长度不高于某个限定值 maxNumber，建议使用构造器

  ```java 
  StringBuilder s = new StringBuilder(maxNumber)
  ```

  

**String拼接**

- 每次都会创建新的StringBuilder对象和String对象
- 内存中由于创建了较多的StringBuilder对象和String对象，内存占用多，如果进行GC，需要花费额外的时间



### 7.5 Intern()的使用

![image-20211108155156434](F:\笔记图片\img\image-20211108155156434.png)

![image-20211108155213093](F:\笔记图片\img\image-20211108155213093.png)

- 如果不是用双引号声明的String对象，可以使用String提供的intern方法，它会从字符串常量池中查询当前字符串是否存在，若不存在就会将当前字符串放入常量池中。

  ```Java
  String myInfo = new string("I love atguigu").intern();
  ```

- 也就是说，如果在任意字符串上调用String.intern方法，那么其返回结果所指向的那个类实例，必须和直接以常量形式出现的字符串实例完全相同。因此，下列表达式的值必定是true

  ```Java
  ("a"+"b"+"c").intern() == "abc"
  ```



通俗点讲，Interned string就是确保字符串在内存里只有一份拷贝，这样可以节约内存空间，加快字符串操作任务的执行速度。注意，这个值会被存放在字符串内部池（String Intern Pool）



**如何保证变量s指向的是字符串常量池中的数据呢**

- 方法一：String s = "aaaa";

- 方式二：String s = new String("aaa").intern();

  ​			   String s = new StringBuilder("aaa").toString().intern();

  ​				........

  **关键是调用intern()方法**



### 7.6 intern()的使用 JDK6 VS JDK7/8

```Java
@Test
public void test() {
    /**
    会创建两个对象，一个对象是new关键字在堆空间创造出来的
    			另一个对象是在字符串常量池中创建的
    **/
    String st = new String("ab");
}
```

![image-20211108161058922](F:\笔记图片\img\image-20211108161058922.png)





```Java
@Test
public void test2() {
    /**
    会创建5个对象
    第一个对象：new StringBuilder()
    第二个对象：new String("a")
    第三个对象：常量池中的a
    第四个对象：new String("b")
    第五个对象：常量池中的b
    第六个对象：new String("ab")  注意！！！toString()的"ab"并不在字符串常量池中，没有生成"ab"具体可以看下边的				  toString()的字节码
    **/
    String st = new String("a") + new String("b");
}
```

![image-20211108161517539](F:\笔记图片\img\image-20211108161517539.png)

==在调用StringBuilder的toString()方法时，还会产生一个新的字符串对象==

![image-20211108162610610](F:\笔记图片\img\image-20211108162610610.png)



```Java
public class StringIntern {
    public static void main(String[] args) {
        String s1 = new String("1");
        s1.intern();
        String s2 = "1";
        System.out.println(s1 == s2); 

        //字符串常量池中不存在 "11"
        String s3 = new String("1") + new String("1");
        
        
        
        //在字符串常量池中生成"11"，但是jdk6与jdk7还有不同
        /**
        jdk6:会创建一个新的对象，且还会有新的地址
        jdk7:字符串常量池在堆空间中，为了堆空间的节省，并没有真正的在字符串常量池中创建"11"对象，而是记录的是"11"对象			  的地址，也就是创建一个指向堆空间中new String("11")的地址的指针
        **/
        s3.intern();  
        
        
        String s4 = "11"; //s4变量记录的地址使用的是上一行代码在字符串常量池中生成的"11"的地址
        System.out.println(s3 == s4); 

    }
}
```

==**jdk6的时候**==

![image-20211108163848965](F:\笔记图片\img\image-20211108163848965.png)

==**jdk7/8**==

![image-20211108164047698](F:\笔记图片\img\image-20211108164047698.png)

![img](https://www.yuque.com/api/filetransfer/images?url=https%3A%2F%2Fgitee.com%2Fvectorx%2FImageCloud%2Fraw%2Fmaster%2Fimg%2F20210511152244.png&sign=00cbebbfeb7bb53c1026d056e7f69dc77297aeb7264103ee3c13c72f63b51faf)



![img](https://www.yuque.com/api/filetransfer/images?url=https%3A%2F%2Fgitee.com%2Fvectorx%2FImageCloud%2Fraw%2Fmaster%2Fimg%2F20210511152302.png&sign=2287bff0520f34041ffa287f8d263756aae7d16293e3bbb1c82d9efaa46769a9)



```Java
//jdk8
@Test
public void test() {
    String s1 = new String("a") + new String("b");
    String s2 = "ab";
    s1.intern();
    System.out.println(s1 == s2); //flase    
}

@Test
public void test() {
    String s1 = new String("a") + new String("b");
    s1.intern();
    String s2 = "ab";
    System.out.println(s1 == s2); //true   
}
```



**intern()的使用**

- jdk1.6中：
  - 如果字符串常量池中有，则并不会放入。返回已有的串池中的对象的地址
  - 如果没有，则会把==此对象复制一份==，放入串池，并返回串池中的对象地址
- jdk1.7起
  - 如果串池中有，则并不会放入。返回已有串池中的对象的地址
  - 如果没有，则会把==对象的引用地址复制一份，==放入串池，并返回串池中的引用地址



**对于程序中大量存在的字符串，其中存在很多重复字符串时，使用intern()方法可以节省内存空间**



### 7.7 StringTable垃圾回收问题

```Java
public class StringGCTest {
    public static void main(String[] args) {
        for (int i = 0;i < 100000;i++) {
            String.valueOf(i).intern();
        }
    }
}
```

![image-20211108201225010](F:\笔记图片\img\image-20211108201225010.png)



### 7.8 G1中的String去重操作

官网地址：[JEP 192: String Deduplication in G1 (java.net)](http://openjdk.java.net/jeps/192)

目前，许多大规模的Java应用程序在内存上遇到了瓶颈。测量表明，在这些类型的应用程序中，大约25%的Java堆实时数据集被String'对象所消耗。此外，这些 "String "对象中大约有一半是重复的，其中重复意味着 "string1.equals(string2) "是真的。在堆上有重复的String'对象，从本质上讲，只是一种内存的浪费。这个项目将在G1垃圾收集器中实现自动和持续的`String'重复数据删除，以避免浪费内存，减少内存占用。

注意这里说的重复，指的是在堆中的数据，而不是常量池中的，因为常量池中的本身就不会重复



背景：对许多Java应用（有大的也有小的）做的测试得出以下结果：

​	●堆存活数据集合里面string对象占了25%

​	●堆存活数据集合里面重复的string对象有13.5%

​	●string对象的平均长度是45



许多大规模的Java应用的瓶颈在于内存，测试表明，在这些类型的应用里面，Java堆中存活的数据集合差不多25%是String对象。更进一步，这里面差不多一半string对象是重复的，重复的意思是说： stringl.equals(string2)= true。==堆上存在重复的String对象必然是一种内存的浪费==。这个项目将在G1垃圾收集器中实现自动持续对重复的string对象进行去重，这样就能避免浪费内存。



**实现**

1. 当垃圾收集器工作的时候，会访问堆上存活的对象。对每一个访问的对象都会检查是否是候选的要去重的String对象

2. 如果是，把这个对象的一个引用插入到队列中等待后续的处理。一个去重的线程在后台运行，处理这个队列。处理队列的一个元素意味着从队列删除这个元素，然后尝试去重它引用的string对象。

3. 使用一个hashtable来记录所有的被String对象使用的不重复的char数组。当去重的时候，会查这个hashtable，来看堆上是否已经存在一个一模一样的char数组。

4. 如果存在，String对象会被调整引用那个数组，释放对原来的数组的引用，最终会被垃圾收集器回收掉。

5. 如果查找失败，char数组会被插入到hashtable，这样以后的时候就可以共享这个数组了。



**命令行选项**

```shell
# 开启String去重，默认是不开启的，需要手动开启。 
UseStringDeduplication(bool)  
# 打印详细的去重统计信息 
PrintStringDeduplicationStatistics(bool)  
# 达到这个年龄的String对象被认为是去重的候选对象
StringpeDuplicationAgeThreshold(uintx)
```



## 8.垃圾回收概述

### 8.1 垃圾回收概述

**什么是垃圾**

垃圾就是指==在运行程序中没有被任何指针指向的对象==，这个对象就是需要被回收的垃圾

- 垃圾收集，不是Java语言的伴生产物。早在1960年，第一门开始使用内存动态分配和垃圾收集技术的Lisp语言诞生。

- 关于垃圾收集有三个经典问题：

  - 哪些内存需要回收？

  - 什么时候回收？

  - 如何回收？

- 垃圾收集机制是Java的招牌能力，==极大地提高了开发效率==。如今，垃圾收集几乎成为现代语言的标配，即使经过如此长时间的发展，Java的垃圾收集机制仍然在不断的演进中，不同大小的设备、不同特征的应用场景，对垃圾收集提出了新的挑战，这当然也是面试的热点。

- 如果不及时对内存的垃圾进行清理，那么，这些垃圾对象所占的内存空间会一直保留到应用程序结束，被保留的空间无法被其他对象引用。甚至可能会==导致内存溢出==



**为什么需要GC**

对于高级语言来说，==一个基本认知是如果不进行垃圾回收，内存迟早都会被消耗完==，因为不断地分配内存空间而不进行回收，就好像不停地生产生活垃圾而从来不打扫一样。

除了释放没用的对象，垃圾回收也可以清除内存里的记录碎片。碎片整理将所占用的堆内存移到堆的一端，==以便JVM将整理出的内存分配给新的对象。==

随着应用程序所应付的业务越来越庞大、复杂，用户越来越多，==没有GC就不能保证应用程序的正常进行。==而经常造成STW的GC又跟不上实际的需求，所以才会不断地尝试对GC进行优化。



### 8.2 早期的垃圾回收

在早期的C/C++时代，垃圾回收基本上是手工进行的。开发人员可以使用new关键字进行内存申请，并使用delete关键字进行内存释放。比如以下代码：



```c
MibBridge *pBridge= new cmBaseGroupBridge();
//如果注册失败，使用Delete释放该对象所占内存区域
if (pBridge->Register(kDestroy) != NO ERROR）
  delete pBridge;
```



这种方式可以灵活控制内存释放的时间，==但是会给开发人员带来频繁申请和释放内存的管理负担。==倘若有一处内存区间由于程序员编码的问题忘记被回收，那么就会产生==内存泄漏==，垃圾对象永远无法被清除，随着系统运行时间的不断增长，垃圾对象所耗内存可能持续上升，直到出现内存溢出并造成应用程序崩溃。

现在，除了Java以外，C#、Python、Ruby等语言都使用了自动垃圾回收的思想，也是未来发展趋势，可以说这种自动化的内存分配和来及回收方式已经成为了线代开发语言必备的标准。



### 8.3 Java垃圾回收机制

- 自动内存管理，无需开发人员手动参与内存的分配与回收，这样降低内存泄漏和内存溢出的风险
  - 没有垃圾回收器，java也会和cpp一样，各种悬垂指针，野指针，泄露问题让你头疼不已。

- 自动内存管理机制，将程序员从繁重的内存管理中释放出来，可以更专心地专注于业务开发

- oracle官网关于垃圾回收的介绍 https://docs.oracle.com/javase/8/docs/technotes/guides/vm/gctuning/toc.html



对于Java开发人员而言，自动内存管理就像是一个黑匣子，如果过度依赖于“自动”，那么这将会是一场灾难，==最严重的就会弱化Java开发人员在程序出现内存溢出时定位问题和解决问题的能力。==



此时，了解JVM的自动内存分配和内存回收原理就显得非常重要，只有在真正了解JVM是如何管理内存后，我们才能够在遇见outofMemoryError时，快速地根据错误异常日志定位问题和解决问题。



当需要排查各种内存溢出、内存泄漏问题时，当垃圾收集成为系统达到更高并发量的瓶颈时，我们就必须对这些“自动化”的技术==实施必要的监控和调节。==



GC主要关注于 方法区 和堆中的垃圾收集

![img](https://www.yuque.com/api/filetransfer/images?url=https%3A%2F%2Fgitee.com%2Fvectorx%2FImageCloud%2Fraw%2Fmaster%2Fimg%2F20210511184103.png&sign=0de67cfe8210611e02d80dee2ab727c0ba9db294d5124541cde19007b7322a76)

- 垃圾回收器可以对年轻代回收，也可以对老年代回收，甚至是全堆和方法区回收
  - 其中，==**Java堆是垃圾收集器的工作重点**==
- 从次数上讲
  - 频繁收集年轻代
  - 较少收集老年代
  - 基本不动元空间





## 9.垃圾回收算法概述

**对象存活判断**

在堆里存放着几乎所有的Java对象实例，在GC执行垃圾回收之前，首先需要区分出内存中哪些是存活对象，哪些是已经死亡的对象。==只有被标记为己经死亡的对象，GC才会在执行垃圾回收时，释放掉其所占用的内存空间，因此这个过程我们可以称为垃圾标记阶段。==

那么在JVM中究竟是如何标记一个死亡对象呢？简单来说，==当一个对象已经不再被任何的存活对象继续引用时，就可以宣判为已经死亡。==

判断对象存活一般有两种方式：==引用计数算法和可达性分析算法。==

### 9.1垃圾标记阶段

#### 9.1.1 引用计数算法

引用计数算法（Reference Counting）比较简单，==对每个对象保存一个整型的引用计数器属性。用于记录对象被引用的情况。==



==对于一个对象A，只要有任何一个对象引用了A，则A的引用计数器就加1；当引用失效时，引用计数器就减1。只要对象A的引用计数器的值为0，即表示对象A不可能再被使用，可进行回收。==

**优点**：实现简单，垃圾对象便于辨识；判定效率高，回收没有延迟性。

**缺点**：

- 它需要单独的字段存储计数器，这样的做法增加了存储空间的开销。

- 每次赋值都需要更新计数器，伴随着加法和减法操作，这增加了时间开销。

- 引用计数器有一个严重的问题，即无法处理==循环引用==的情况。这是一条致命缺陷，==导致在Java的垃圾回收器中没有使用这类算法。==



> 循环引用

当p的指针断开的时候，内部的引用形成一个循环，这就是循环引用

![img](https://www.yuque.com/api/filetransfer/images?url=https%3A%2F%2Fgitee.com%2Fvectorx%2FImageCloud%2Fraw%2Fmaster%2Fimg%2F20210512105328.png&sign=3fe7869a3c59fda65e46cb064cf5f69f5fe8bd17fd37401f398ebfbd1ac1037d)



#### 9.1.2 可达性分析算法（根搜索算法、追踪性垃圾收集算法）

- 相对于引用计数算法而言，==可达性分析算法不仅同样具备实现简单和执行高效等特点，更重要的是该算法可以有效地解决在引用计数算法中循环引用的问题，防止内存泄漏的发生。==
- 相较于引用计数算法，这里的可达性分析就是Java、C#选择的。==这种类型的垃圾收集通常也叫作追踪性垃圾收集（Tracing Garbage Collection）==
- 所谓"GCRoots”根集合==就是一组必须活跃的引用。==



**基本思路**

- 可达性分析算法是以根对象集合（GCRoots）为起始点，==按照从上至下的方式搜索被根对象集合所连接的目标对象是否可达==。

- 使用可达性分析算法后，内存中的存活对象都会被根对象集合直接或间接连接着，搜索所走过的路径称为==引用链==（Reference Chain）

- 如果目标对象没有任何引用链相连，则是不可达的，就意味着该对象己经死亡，可以标记为垃圾对象。

- 在可达性分析算法中，只有能够被根对象集合直接或者间接连接的对象才是存活对象。

![img](https://www.yuque.com/api/filetransfer/images?url=https%3A%2F%2Fgitee.com%2Fvectorx%2FImageCloud%2Fraw%2Fmaster%2Fimg%2F20210511195541.png&sign=5515e43926caba557ea039a09f73861954269b2442c54306b22e90d1104a9d8f)



**在Java语言中，GC Roots包含以下几类元素**

- 虚拟机栈中引用的对象 

- - 比如：各个线程被调用的方法中使用到的参数、局部变量等。

- 本地方法栈内JNI（通常说的本地方法）引用的对象

- 方法区中类静态属性引用的对象 

- - 比如：Java类的引用类型静态变量

- 方法区中常量引用的对象 

- - 比如：字符串常量池（String Table）里的引用

- 所有被同步锁synchronized持有的对象

- Java虚拟机内部的引用。 

- - 基本数据类型对应的Class对象，一些常驻的异常对象（如：NullPointerException、OutOfMemoryError），系统类加载器。

- 反映java虚拟机内部情况的JMXBean、JVMTI中注册的回调、本地代码缓存等。![img](https://www.yuque.com/api/filetransfer/images?url=https%3A%2F%2Fgitee.com%2Fvectorx%2FImageCloud%2Fraw%2Fmaster%2Fimg%2F20210512105351.png&sign=aec5a85e96bacf50bc94369001f5a0c1c203a80c55cc36f2d646b11c1e3d0b99)



除了这些固定的GC Roots集合以外，根据用户所选用的垃圾收集器以及当前回收的内存区域不同，还可以有其他对象“临时性”地加入，共同构成完整GC Roots集合。比如：分代收集和局部回收（PartialGC）。



如果只针对Java堆中的某一块区域进行垃圾回收（比如：典型的只针对新生代），必须考虑到内存区域是虚拟机自己的实现细节，更不是孤立封闭的，这个区域的对象完全有可能被其他区域的对象所引用，这时候就需要一并将关联的区域对象也加入GCRoots集合中去考虑，才能保证可达性分析的准确性。



小技巧：由于Root采用栈方式存放变量和指针，所以如果一个指针，它保存了堆内存里面的对象，但是自己又不存放在堆内存里面，那它就是一个Root。



**注意**

- 如果要使用可达性分析算法来判断内存是否可回收，那么分析工作必须在一个能保障一致性的快照中进行。这点不满足的话分析结果的准确性就无法保证。

- 这点也是导致GC进行时必须“stop The World”的一个重要原因。

  即使是号称（几乎）不会发生停顿的CMS收集器中，枚举根节点时也是必须要停顿的。



### 9.2 对象的finalization机制

- Java语言提供了对象终止（finalization）机制来==允许开发人员提供对象被销毁之前的自定义处理逻辑==。



- 当垃圾回收器发现没有引用指向一个对象，即：==垃圾回收此对象之前，总会先调用这个对象的finalize()方法。==



- ==finalize() 方法允许在子类中被重写，用于在对象被回收时进行资源释放。通常在这个方法中进行一些资源释放和清理的工作，比如关闭文件、套接字和数据库连接等。==



- ==永远不要主动调用某个对象的finalize()方法应该交给垃圾回收机制调用==。理由包括下面三点：
  - 在finalize()时可能会导致对象复活。
  - finalize()方法的执行时间是没有保障的，它完全由GC线程决定，极端情况下，若不发生GC，则finalize()方法将没有执行机会。
  - 一个糟糕的finalize()会严重影响Gc的性能。



- 从功能上来说，finalize()方法与C中的析构函数比较相似，但是Java采用的是基于垃圾回收器的自动内存管理机制，所以finalize()方法在本质上不同于C中的析构函数。



- 由于finalize()方法的存在，==虚拟机中的对象一般处于三种可能的状态==。



如果从所有的根节点都无法访问到某个对象，说明对象己经不再使用了。一般来说，此对象需要被回收。但事实上，也并非是“非死不可”的，这时候它们暂时处于“缓刑”阶段。一个无法触及的对象有可能在某一个条件下“复活”自己，如果这样，那么对它的回收就是不合理的，为此，定义虚拟机中的对象可能的三种状态。如下：

- ==可触及的：==从根节点开始，可以到达这个对象。

- ==可复活的：==对象的所有引用都被释放，但是对象有可能在finalize()中复活。

- ==不可触及的==：对象的finalize()被调用，并且没有复活，那么就会进入不可触及状态。不可触及的对象不可能被复活，因为finalize()只会被调用一次。

以上3种状态中，是由于finalize()方法的存在，进行的区分。只有在对象不可触及时才可以被回收。



**具体过程**

判定一个对象objA是否可回收，至少要经历两次标记过程：

1. 如果对象objA到GC Roots没有引用链，则进行第一次标记。

2. 进行筛选，判断此对象是否有必要执行finalize()方法

3. 如果对象objA没有重写finalize()方法，或者finalize()方法已经被虚拟机调用过，则虚拟机视为“没有必要执行”，objA被判定为不可触及的。

4. 如果对象objA重写了finalize()方法，且还未执行过，那么objA会被插入到F-Queue队列中，由一个虚拟机自动创建的、低优先级的Finalizer线程触发其finalize()方法执行。

5. ==finalize()方法是对象逃脱死亡的最后机会==，稍后GC会对F-Queue队列中的对象进行第二次标记。==如果objA在finalize()方法中与引用链上的任何一个对象建立了联系，那么在第二次标记时，objA会被移出“即将回收”集合==。==之后，对象会再次出现没有引用存在的情况。在这个情况下，finalize方法不会被再次调用，对象会直接变成不可触及的状态==，也就是说，一==个对象的finalize方法只会被调用一次==。



```Java
public class CanReliveObj {
    public static CanReliveObj obj;

    //此方法只能被调用一次
    @Override
    protected void finalize() throws Throwable{
        super.finalize();
        System.out.println("调用当前类重写的finalize()方法");
        obj = this; //当前待回收的对象在finalize()方法中与引用链上的一个对象obj建立了联系
    }

    public static void main(String[] args) {
        try {
            obj = new CanReliveObj();

            obj = null;
            System.gc();
            System.out.println("第一次gc");
            //因为Finailzer线程优先级很低，暂停两秒，以等待它
            TimeUnit.SECONDS.sleep(2);
            if (obj == null) {
                System.out.println("obj is dead");
            }else {
                System.out.println("obj is still alive");
            }
            System.out.println("第二次 gc");

            obj = null;
            System.gc();
            System.out.println("第一次gc");
            //因为Finailzer线程优先级很低，暂停两秒，以等待它
            TimeUnit.SECONDS.sleep(2);
            if (obj == null) {
                System.out.println("obj is dead");
            }else {
                System.out.println("obj is still alive");
            }
            System.out.println("第二次 gc");
        } catch (Exception e) {
            e.printStackTrace();
        }
    }
}
```

![image-20211109145430557](F:\笔记图片\img\image-20211109145430557.png)



我们再来看看没有重写finalize()方法的

```Java
public class CanReliveObj {
    public static CanReliveObj obj;


    public static void main(String[] args) {
        try {
            obj = new CanReliveObj();

            obj = null;
            System.gc();
            System.out.println("第一次gc");
            //因为Finailzer线程优先级很低，暂停两秒，以等待它
            TimeUnit.SECONDS.sleep(2);
            if (obj == null) {
                System.out.println("obj is dead");
            }else {
                System.out.println("obj is still alive");
            }
            System.out.println("第二次 gc");

            obj = null;
            System.gc();
            System.out.println("第一次gc");
            //因为Finailzer线程优先级很低，暂停两秒，以等待它
            TimeUnit.SECONDS.sleep(2);
            if (obj == null) {
                System.out.println("obj is dead");
            }else {
                System.out.println("obj is still alive");
            }
            System.out.println("第二次 gc");
        } catch (Exception e) {
            e.printStackTrace();
        }
    }
}
```

![image-20211109145557806](F:\笔记图片\img\image-20211109145557806.png)





### 9.3 垃圾清除阶段

当成功区分出内存中存活对象和死亡对象后，GC接下来的任务就是执行垃圾回收，释放掉无用对象所占用的内存空间，以便有足够的可用内存空间为新对象分配内存。



目前在JVM中比较常见的三种垃圾收集算法是==标记一清除算法（Mark-Sweep）、复制算法（copying）、标记-压缩算法（Mark-Compact）==

#### 9.3.1 标记-清除算法

标记-清除算法（Mark-Sweep）是一种非常基础和常见的垃圾收集算法，该算法被J.McCarthy等人在1960年提出并并应用于Lisp语言。



**执行过程**

当堆中的有效内存空间（available memory）被耗尽的时候，就会停止整个程序（也被称为stop the world），然后进行两项工作，第一项则是标记，第二项则是清除



-  ==标记==：Collector从引用根节点开始遍历，标记所有被引用的对象。一般是在对象的Header中记录为可达对象。 

-  ==清除==：Collector对堆内存从头到尾进行线性的遍历，如果发现某个对象在其Header中没有标记为可达对象，则将其回收 



![img](https://www.yuque.com/api/filetransfer/images?url=https%3A%2F%2Fgitee.com%2Fvectorx%2FImageCloud%2Fraw%2Fmaster%2Fimg%2F20210512105450.png&sign=bb60d73d24c3981515db6c2bae1a77ab93977aea46f1281502eec61da27228b5)



**缺点**

- 标记-清除算法效率不高
- 在进行GC的时候，需要停止整个应用程序，用户体验较差
- 这种方式清理出来的内存空间不是连续的，产生内存碎片，需要维护一个空闲列表



**何为清除**

- 这里所谓的清除并不是真的置空，==而是把需要清除的对象地址保存在空闲的地址列表里。下次有新对象需要加载时，判断垃圾的位置空间是否够，如果够，就存放覆盖原有的地址。==



#### 9.3.2 复制算法

为了解决标记-清除算法在垃圾收集效率方面的缺陷，M.L.Minsky于1963年发表了著名的论文，“使用双存储区的Lisp语言垃圾收集器CA LISP Garbage Collector Algorithm Using Serial Secondary Storage）”。M.L.Minsky在该论文中描述的算法被人们称为复制（Copying）算法，它也被M.L.Minsky本人成功地引入到了Lisp语言的一个实现版本中。



**核心思想**

将活着的内存空间分为两块，每次只使用其中一块，在垃圾回收时将正在使用的内存中的存活对象复制到未被使用的内存块中，之后清除正在使用的内存块中的所有对象，交换两个内存的角色，最后完成垃圾回收

![img](https://www.yuque.com/api/filetransfer/images?url=https%3A%2F%2Fgitee.com%2Fvectorx%2FImageCloud%2Fraw%2Fmaster%2Fimg%2F20210512105456.png&sign=6e633f3671ed74664c1e803b41ae24ab219e466354e10ceac8bbba719492b28a)



**优点**

- 没有标记和清除过程，实现简单，运行高效

- 复制过去以后保证空间的连续性，不会出现“碎片”问题。



**缺点**

- 此算法的缺点也是很明显的，就是需要两倍的内存空间。

- 对于G1这种分拆成为大量region的GC，复制而不是移动，意味着GC需要维护region之间对象引用关系，不管是内存占用或者时间开销也不小





==特别的，如果系统中的垃圾对象很多，复制算法需要复制的存活对象数量并不会太大，或者说非常低才行==

**应用场景**

在新生代，对常规应用的垃圾回收，一次通常可以回收70% - 99% 的内存空间。回收性价比很高。所以现在的商业虚拟机都是用这种收集算法回收新生代。

![img](https://www.yuque.com/api/filetransfer/images?url=https%3A%2F%2Fgitee.com%2Fvectorx%2FImageCloud%2Fraw%2Fmaster%2Fimg%2F20210512105501.png&sign=0083bb760fdee49781d21275a84bf0ae42489c1bce280f3295a31fe44b0114bc)



#### 9.3.3 标记 - 压缩算法

复制算法的高效性是建立在存活对象少、垃圾对象多的前提下的。这种情况在新生代经常发生，但是在老年代，更常见的情况是大部分对象都是存活对象。如果依然使用复制算法，由于存活对象较多，复制的成本也将很高。因此，基于老年代垃圾回收的特性，需要使用其他的算法。



标记一清除算法的确可以应用在老年代中，但是该算法不仅执行效率低下，而且在执行完内存回收后还会产生内存碎片，所以JVM的设计者需要在此基础之上进行改进。标记-压缩（Mark-Compact）算法由此诞生。



1970年前后，G.L.Steele、C.J.Chene和D.s.Wise等研究者发布标记-压缩算法。在许多现代的垃圾收集器中，人们都使用了标记-压缩算法或其改进版本。



**执行过程**

1.  第一阶段和标记清除算法一样，从根节点开始标记所有被引用对象 

2.  第二阶段将所有的存活对象压缩到内存的一端，按顺序排放。 

3.  之后，清理边界外所有的空间。 

![img](https://www.yuque.com/api/filetransfer/images?url=https%3A%2F%2Fgitee.com%2Fvectorx%2FImageCloud%2Fraw%2Fmaster%2Fimg%2F20210512105506.png&sign=45970afd59203ef467dba0bf246876bf2a333e52dcb0e9d99d8d1c9923693925)

**优点**

- 消除了标记-清除算法当中，内存区域分散的缺点，我们需要给新对象分配内存时，JVM只需要持有一个内存的起始地址即可。

- 消除了复制算法当中，内存减半的高额代价。



**缺点**

- 从效率上来说，标记-整理算法要低于复制算法。

- 移动对象的同时，如果对象被其他对象引用，则还需要调整引用的地址

- 移动过程中，需要全程暂停用户应用程序。即：STW





标记-压缩算法的最终效果等同于标记-清除算法执行完成后，再进行一次内存碎片整理，因此，也可以把它称为==标记-清除-压缩（Mark-Sweep-Compact）算法。==

二者的本质差异在于标记-清除算法是一种==非移动式的回收算法==，标记-压缩是移动式的。是否移动回收后的存活对象是一项优缺点并存的风险决策。可以看到，标记的存活对象将会被整理，按照内存地址依次排列，而未被标记的内存会被清理掉。如此一来，当我们需要给新对象分配内存时，JVM只需要持有一个内存的起始地址即可，这比维护一个空闲列表显然少了许多开销。



**指针碰撞**

如果内存空间以规整和有序的方式分布，即已用和未用的内存都各自一边，彼此之间维系着一个记录下一次分配起始点的标记指针，当为新对象分配内存时，只需要通过修改指针的偏移量将新对象分配在第一个空闲内存位置上，这种分配方式就叫做指针碰撞（Bump tHe Pointer）。



### 9.4 对比三种算法

|          | 标记-清除算法      | 标记-压缩算法      | 复制算法                                   |
| -------- | ------------------ | ------------------ | ------------------------------------------ |
| 速度     | 中等               | 最慢               | 最快                                       |
| 空间开销 | 少（但会堆积碎片） | 少（不会堆积碎片） | 通常需要存活对象的两倍大小（不会产生碎片） |
| 移动对象 | 否                 | 是                 | 是                                         |



### 9.5 分代收集算法

前面所有这些算法中，并没有一种算法可以完全替代其他算法，==它们都具有自己独特的优势和特点。分代收集算法应运而生。==



分代收集算法，是基于这样一个事实：不同的对象的生命周期是不一样的。因此，==不同生命周期的对象可以采取不同的收集方式==，以便提高回收效率。一般是把Java堆分为新生代和老年代，这样就可以根据各个年代的特点使用不同的回收算法，以提高垃圾回收的效率。



在Java程序运行的过程中，会产生大量的对象，其中有些对象是与业务信息相关，==比如Http请求中的Session对象、线程、Socket连接==，这类对象跟业务直接挂钩，因此==生命周期比较长==。但是还有一些对象，主要是程序运行过程中生成的临时变量，这些对象生命周期会比较短，比如：String对象，由于其不变类的特性，系统会产生大量的这些对象，有些对象甚至只用一次即可回收。



==目前几乎所有的GC都采用分代手机算法执行垃圾回收的。==



==在HotSpot中，基于分代的概念，GC所使用的内存回收算法必须结合年轻代和老年代各自的特点。==



**年轻代**

年轻代特点：==区域相对老年代较小，对象生命周期短、存活率低，回收频繁。==



这种情况复制算法的回收整理，速度是最快的。复制算法的效率只和当前存活对象大小有关，因此很适用于年轻代的回收。而复制算法内存利用率不高的问题，通过hotspot中的两个survivor的设计得到缓解。



**老年代**

老年代特点：==区域较大，对象生命周期长、存活率高，回收不及年轻代频繁。==



这种情况存在大量存活率高的对象，复制算法明显变得不合适。==一般是由标记-清除或者是标记-清除与标记-整理的混合实现。==

- Mark阶段的开销与存活对象的数量成正比。

- Sweep阶段的开销与所管理区域的大小成正相关。

- Compact阶段的开销与存活对象的数据成正比。



==以HotSpot中的CMS回收器为例，CMS是基于Mark-Sweep实现的，对于对象的回收效率很高。而对于碎片问题，CMS采用基于Mark-Compact算法的Serial Old回收器作为补偿措施：当内存回收不佳（碎片导致的Concurrent Mode Failure时），将采用Serial Old执行Full GC以达到对老年代内存的整理。==



分代的思想被现有的虚拟机广泛使用。几乎所有的垃圾回收器都区分新生代和老年代



### 9.6 增量收集算法

上述现有的算法，在垃圾回收过程中，应用软件将处于一种Stop the World的状态。在Stop the World状态下，应用程序所有的线程都会挂起，暂停一切正常的工作，等待垃圾回收的完成。==如果垃圾回收时间过长，应用程序会被挂起很久，将严重影响用户体验或者系统的稳定性==。为了解决这个问题，即对实时垃圾收集算法的研究直接导致了增量收集（Incremental Collecting）算法的诞生。



**基本思想**

如果一次性将所有的垃圾进行处理，需要造成系统长时间的停顿，那么就可以让垃圾收集线程和应用程序线程交替执行。==每次，垃圾收集线程只收集一小片区域的内存空间，接着切换到应用程序线程。依次反复，直到垃圾收集完成。==



总的来说，==增量收集算法的基础仍是传统的标记-清除和复制算法。增量收集算法通过对线程间冲突的妥善处理，允许垃圾收集线程以分阶段的方式完成标记、清理或复制工作==



**缺点**

- 使用这种方式，由于在垃圾回收过程中，间断性地还执行了应用程序代码，所以能减少系统的停顿时间。但是，因为线程切换和上下文转换的消耗，会使得垃圾回收的总体成本上升，==造成系统吞吐量的下降。==



### 9.7 分区算法

一般来说，在相同条件下，堆空间越大，一次Gc时所需要的时间就越长，有关GC产生的停顿也越长。为了更好地控制GC产生的停顿时间，将一块大的内存区域分割成多个小块，根据目标的停顿时间，每次合理地回收若干个小区间，而不是整个堆空间，从而减少一次GC所产生的停顿。



==分代算法将按照对象的生命周期长短划分成两个部分，分区算法将整个堆空间划分成连续的不同小区间。==

==每一个小区间都独立使用，独立回收。这种算法的好处是可以控制一次回收多少个小区间。==

![img](https://www.yuque.com/api/filetransfer/images?url=https%3A%2F%2Fgitee.com%2Fvectorx%2FImageCloud%2Fraw%2Fmaster%2Fimg%2F20210512105517.png&sign=6a9fbf44d2dd17e525b75cb6f6ce788108ccdf1dd3475d85214ec4cecbbd8fd9)



## 10.垃圾回收相关的概念

### 10.1 System.gc()的理解

在默认情况下，==通过system.gc()或者Runtime.getRuntime().gc() 的调用，会显式触发Full GC，同时对老年代和新生代进行回收，尝试释放被丢弃对象占用的内存。==



然而System.gc() 调用附带一个免责声明，无法保证对垃圾收集器的调用。(不能确保立即生效)



JVM实现者可以通过System.gc() 调用来决定JVM的GC行为。而一般情况下，垃圾回收应该是自动进行的，无须手动触发，否则就太过于麻烦了。在一些特殊情况下，如我们正在编写一个性能基准，我们可以在运行之间调用System.gc()

```Java
public class SystemGCTest {
    public static void main(String[] args) {
        new SystemGCTest();
        System.gc();// 提醒JVM的垃圾回收器执行gc，但是不确定是否马上执行gc
        // 与Runtime.getRuntime().gc();的作用一样
        
        System.runFinalization();//强制执行使用引用的对象的finalize()方法
    }

    @Override
    protected void finalize() throws Throwable {
        super.finalize();
        System.out.println("SystemGCTest 重写了finalize()");
    }
}
```



### 10.2 内存泄漏与内存溢出

**内存溢出**

内存溢出相对于内存泄漏来说，尽管更容易被理解，但是同样的，内存溢出也是引发程序崩溃的罪魁祸首之一。



由于GC一直在发展，所有一般情况下，除非应用程序占用的内存增长速度非常快，造成垃圾回收已经跟不上内存消耗的速度，否则不太容易出现ooM的情况。



大多数情况下，GC会进行各种年龄段的垃圾回收，实在不行了就放大招，来一次独占式的Full GC操作，这时候会回收大量的内存，供应用程序继续使用。



javadoc中对OutOfMemoryError的解释是，==没有空闲内存，并且垃圾收集器也无法提供更多内存。==



首先说没有空闲内存的情况：说明Java虚拟机的堆内存不够。原因有二：

- ==Java虚拟机的堆内存设置不够。==
  比如：可能存在内存泄漏问题；也很有可能就是堆的大小不合理，比如我们要处理比较可观的数据量，但是没有显式指定JVM堆大小或者指定数值偏小。我们可以通过参数`-Xms` 、`-Xmx`来调整。 

- ==代码中创建了大量大对象，并且长时间不能被垃圾收集器收集（存在被引用）==
  对于老版本的Oracle JDK，因为永久代的大小是有限的，并且JVM对永久代垃圾回收（如，常量池回收、卸载不再需要的类型）非常不积极，所以当我们不断添加新类型的时候，永久代出现OutOfMemoryError也非常多见，尤其是在运行时存在大量动态类型生成的场合；类似intern字符串缓存占用太多空间，也会导致OOM问题。对应的异常信息，会标记出来和永久代相关：“`java.lang.OutOfMemoryError: PermGen space`"。
  随着元数据区的引入，方法区内存已经不再那么窘迫，所以相应的ooM有所改观，出现OOM，异常信息则变成了：“`java.lang.OutofMemoryError:Metaspace`"。直接内存不足，也会导致OOM。 

这里面隐含着一层意思是，在抛出OutOfMemoryError之前，通常垃圾收集器会被触发，尽其所能去清理出空间。

- 例如：在引用机制分析中，涉及到JVM会去尝试回收软引用指向的对象等。

- 在`java.nio.BIts.reserveMemory()`方法中，我们能清楚的看到，`System.gc()`会被调用，以清理空间。



当然，也不是在任何情况下垃圾收集器都会被触发的

- 比如，我们去分配一个超大对象，类似一个超大数组超过堆的最大值，JVM可以判断出垃圾收集并不能解决这个问题，所以直接抛出OutOfMemoryError。



**内存泄漏**

也称作“存储渗漏”。==严格来说，只有对象不会再被程序用到了，但是GC又不能回收他们的情况，才叫内存泄漏。==



但实际情况很多时候一些不太好的实践（或疏忽）会导致对象的生命周期变得很长甚至导致00M，也可以叫做宽泛意义上的“内存泄漏”。



尽管内存泄漏并不会立刻引起程序崩溃，但是一旦发生内存泄漏，程序中的可用内存就会被逐步蚕食，直至耗尽所有内存，最终出现OutOfMemory异常，导致程序崩溃。



注意，==这里的存储空间并不是指物理内存，而是指虚拟内存大小，这个虚拟内存大小取决于磁盘交换区设定的大小==。

![img](F:\笔记图片\img\imagesurl=https%3A%2F%2Fgitee.com%2Fvectorx%2FImageCloud%2Fraw%2Fmaster%2Fimg%2F20210512115039.png&sign=52f56597486db14696a801fa2993984dc8a681a5af33f1dd8b66dee1af8ac91a)



**举例**

1.  单例模式
   单例的生命周期和应用程序是一样长的，所以单例程序中，如果持有对外部对象的引用的话，那么这个外部对象是不能被回收的，则会导致内存泄漏的产生。 

2. 一些提供close的资源未关闭导致内存泄漏
   数据库连接（dataSourse.getConnection() ），网络连接（socket）和io连接必须手动close，否则是不能被回收的。 



### 10.3 Stop The World

Stop-the-World，==简称STW，指的是GC事件发生过程中，会产生应用程序的停顿==。停顿产生时整个应用程序线程都会被暂停，没有任何响应，有点像卡死的感觉，这个停顿称为STW。



可达性分析算法中枚举根节点（GC Roots）会导致所有Java执行线程停顿。

- 分析工作必须在一个能确保一致性的快照中进行

- 一致性指整个分析期间整个执行系统看起来像被冻结在某个时间点上

- 如果出现分析过程中对象引用关系还在不断变化，则分析结果的准确性无法保证

被STW中断的应用程序线程会在完成GC之后恢复，频繁中断会让用户感觉像是网速不快造成电影卡带一样，所以我们需要减少STW的发生。

==STW事件和采用哪款GC无关，所有的GC都有这个事件。==

哪怕是G1也不能完全避免Stop-the-World情况发生，只能说垃圾回收器越来越优秀，回收效率越来越高，尽可能地缩短了暂停时间。

STW是JVM在后台自动发起和自动完成的。在用户不可见的情况下，把用户正常的工作线程全部停掉。

开发中不要用System.gc() 会导致Stop-the-World的发生。



### 10.4 程序的并行与并发

**并发**（Concurrent）

在操作系统中，是指==一个时间段中==有几个程序都处于已启动运行到运行完毕之间，==且这几个程序都是在同一个处理器上运行==。



**并发不是真正意义上的“同时进行”，只是CPU把一个时间段划分成几个时间片段（时间区间），然后在这几个时间区间之间来回切换，由于CPU处理的速度非常快，只要时间间隔处理得当，即可让用户感觉是多个应用程序同时在进行。**



![img](https://www.yuque.com/api/filetransfer/images?url=https%3A%2F%2Fgitee.com%2Fvectorx%2FImageCloud%2Fraw%2Fmaster%2Fimg%2F20210512115049.png&sign=eda65d1ce421200282ca035e88e77ecb3ab1ef4e153cd50e3d540681aa1bf0aa)



**并行**

- 当系统有一个以上CPU时，当一个CPU执行一个进程时，另一个CPU可以执行另一个进程，两个进程互不抢占CPU资源，可以同时进行，我们称之为并行（Parallel）。



- 其实决定并行的因素不是CPU的数量，而是CPU的核心数量，比如一个CPU多个核也可以并行。



- 适合科学计算，后台处理等弱交互场景



![img](https://www.yuque.com/api/filetransfer/images?url=https%3A%2F%2Fgitee.com%2Fvectorx%2FImageCloud%2Fraw%2Fmaster%2Fimg%2F20210512115053.png&sign=24918ca7a79d13c42f86c025922c153dcdaee3b2ae10d5556e721e961e1f3a9d)



- 并发，指的是多个事情，==在同一时间段内同时发生了==。 

-  并行，指的是多个事情，==在同一时间点上同时发生了。==

-  并发的多个任务之间是互相抢占资源的。 

-  并行的多个任务之间是不互相抢占资源的。 

-  只有在多CPU或者一个CPU多核的情况中，才会发生并行。 

-  否则，看似同时发生的事情，其实都是并发执行的。 





### 10.5 垃圾回收的并行与并发

**并行**

指多条垃圾收集线程并行工作，但此时用户线程仍处于等待状态。如ParNew、Parallel Scavenge、Parallel Old；

**串行**

相较于并行的概念，==单线程执行。==如果内存不够，则程序暂停，启动JVM垃圾回收器进行垃圾回收。回收完，再启动程序的线程。

![img](https://www.yuque.com/api/filetransfer/images?url=https%3A%2F%2Fgitee.com%2Fvectorx%2FImageCloud%2Fraw%2Fmaster%2Fimg%2F20210512115056.png&sign=95f4dd9b111d05594685382b2edadc7588bc6d623fb6ffddd6303b910243f8e7)



**并发**

==指用户线程与垃圾收集线程同时执行==（但不一定是并行的，可能会交替执行），垃圾回收线程在执行时不会停顿用户程序的运行。用户程序在继续运行，而垃圾收集程序线程运行于另一个CPU上；如：CMS、G1

![img](https://www.yuque.com/api/filetransfer/images?url=https%3A%2F%2Fgitee.com%2Fvectorx%2FImageCloud%2Fraw%2Fmaster%2Fimg%2F20210512115102.png&sign=ed522c70799bcb90303c978637623bdfaa765e7ef9a7f8e6043901d9f4a0a9dd)



### 10.6 安全点和安全区域

1. 安全点

   程序执行时并非在所有地方都能停顿下来开始GC，只有在特定的位置才能停顿下来开始GC，这些位置称为“安全点（Safepoint）”。

   Safe Point的选择很重要，如果太少可能导致GC等待的时间太长，如果太频繁可能导致运行时的性能问题。大部分指令的执行时间都非常短暂，通常会根据“是否具有让程序长时间执行的特征”为标准。比如：选择一些执行时间较长的指令作为Safe Point，如方法调用、循环跳转和异常跳转等。

   

   如何在GC发生时，检查所有线程都跑到最近的安全点停顿下来呢？

   两种方式：

   - 抢先式中断（不再使用了）

     首先中断所有的线程，如果有线程不在安全点，就恢复他，让其跑到安全点再中断。

   - 主动式中断

     各个线程跑到安全点后主动轮询是否有中断信号，如果有，则自己中断挂起。

2. 安全区域

   Safepoint 机制保证了程序执行时，在不太长的时间内就会遇到可进入GC的Safepoint。但是，程序“不执行”的时候呢？例如线程处于Sleep 状态或Blocked 状态，这时候线程无法响应JVM的中断请求，“走”到安全点去中断挂起，JVM也不太可能等待线程被唤醒。对于这种情况，就需要安全区域（Safe Region）来解决。

   ==安全区域是指在一段代码片段中，对象的引用关系不会发生变化，在这个区域中的任何位置开始Gc都是安全的。我们也可以把Safe Region看做是被扩展了的Safepoint。==

   

   执行时：

   1. 当线程运行到Safe Region的代码时，首先标识已经进入了Safe Relgion，如果这段时间内发生GC，JVM会忽略标识为Safe Region状态的线程

   2. 当线程即将离开Safe Region时，会检查JVM是否已经完成GC，如果完成了，则继续运行，否则线程必须等待直到收到可以安全离开Safe Region的信号为止；



### 10.7 再谈引用

我们希望能描述这样一类对象：当内存空间还足够时，则能保留在内存中；如果内存空间在进行垃圾收集后还是很紧张，则可以抛弃这些对象。

【既偏门又非常高频的面试题】强引用、软引用、弱引用、虚引用有什么区别？具体使用场景是什么？



在JDK1.2版之后，Java对引用的概念进行了扩充，将引用分为：强引用（Strong Reference）、软引用（Soft Reference）、弱引用（Weak Reference）、虚引用（Phantom Reference）这4种引用强度依次逐渐减弱。



除强引用外，其他3种引用均可以在java.lang.ref包中找到它们的身影。如下图，显示了这3种引用类型对应的类，开发人员可以在应用程序中直接使用它们。

![img](https://www.yuque.com/api/filetransfer/images?url=https%3A%2F%2Fgitee.com%2Fvectorx%2FImageCloud%2Fraw%2Fmaster%2Fimg%2F20210512115107.png&sign=a6c80b4a1932f9935ec00976a069f5b9662ed489af2f158150a8964479d08ca6)



Reference子类中只有终结器引用是包内可见的，其他3种引用类型均为public，可以在应用程序中直接使用



- 强引用（StrongReference）：最传统的“引用”的定义，是指在程序代码之中普遍存在的引用赋值，即类似“`Object obj = new Object()`”这种引用关系。==无论任何情况下，只要强引用关系还存在，垃圾收集器就永远不会回收掉被引用的对象。==

- 软引用（SoftReference）：在系统将要发生内存溢出之前，将会把这些对象列入回收范围之中进行第二次回收。如果这次回收后还没有足够的内存，才会抛出内存流出异常。

- 弱引用（WeakReference）：被弱引用关联的对象只能生存到下一次垃圾收集之前。当垃圾收集器工作时，无论内存空间是否足够，都会回收掉被弱引用关联的对象。

- 虚引用（PhantomReference）：一个对象是否有虚引用的存在，完全不会对其生存时间构成影响，也无法通过虚引用来获得一个对象的实例。==为一个对象设置虚引用关联的唯一目的就是能在这个对象被收集器回收时收到一个系统通知。==





#### 10.7.1 强引用  - 不回收

- 在Java程序中，最常见的引用类型是强引用（==普通系统99%以上都是强引用==），也就是我们最常见的普通对象引用，==也是默认的引用类型。==



- 当在Java语言中使用new操作符创建一个新的对象，并将其赋值给一个变量的时候，这个变量就成为指向该对象的一个强引用。



- ==强引用的对象是可触及的，垃圾收集器就永远不会回收掉被引用的对象。==



- 对于一个普通的对象，如果没有其他的引用关系，只要超过了引用的作用域或者显式地将相应（强）引用赋值为nu11，就是可以当做垃圾被收集了，当然具体回收时机还是要看垃圾收集策略。



- 相对的，软引用、弱引用和虚引用的对象是软可触及、弱可触及和虚可触及的，在一定条件下，都是可以被回收的。所以，==强引用是造成Java内存泄漏的主要原因之一。==



- 强引用的特点
  - 强引用可以直接访问目标对象。
  - 强引用所指向的对象在任何时候都不会被系统回收，虚拟机宁愿抛出OOM异常，也不会回收强引用所指向对象。
  - 强引用可能导致内存泄漏。





#### 10.7.2 软引用-内存不足即回收

软引用是用来描述一些还有用，但非必需的对象。==只被软引用关联着的对象，在系统将要发生内存溢出异常前，会把这些对象列进回收范围之中进行第二次回收，如果这次回收还没有足够的内存，才会抛出内存溢出异常。==



==软引用通常用来实现内存敏感的缓存==。比如：高速缓存就有用到软引用。如果还有空闲内存，就可以暂时保留缓存，当内存不足时清理掉，这样就保证了使用缓存的同时，不会耗尽内存。



垃圾回收器在某个时刻决定回收软可达的对象的时候，会清理软引用，并可选地把引用存放到一个引用队列（Reference Queue）。



类似弱引用，只不过Java虚拟机会尽量让软引用的存活时间长一些，迫不得已才清理。



在JDK1.2版之后提供了java.lang.ref.SoftReference类来实现软引用

```Java
public class SoftReferenceTest {
    public static class User {
        public User(int id,String name) {
            this.id = id;
            this.name = name;
        }

        int id;
        String name;

        @Override
        public String toString() {
            return "User{" +
                    "id=" + id +
                    ", name='" + name + '\'' +
                    '}';
        }
    }


    public static void main(String[] args) {
        //创建对象，建立软引用
        SoftReference<User> softReference = new SoftReference<>(new User(1,"zgx"));

        //上面的一行代码等价于如下的三行代码
//        User user = new User(1,"zgx");
//        SoftReference<User> softReference1 = new SoftReference<>(user);
//        user = null;

        //从软引用中重新获得强引用对象
        System.out.println(softReference.get());
        System.gc();
        System.out.println("After GC");
        System.out.println(softReference.get());

        try {
            byte[] b = new byte[1024 * 1024 * 7];
        }finally {
            //再次从软引用中获取数据
            System.out.println(softReference.get());
        }

    }
}
```



#### 10.7.3 弱引用 - 发现即回收

==弱引用也是用来描述那些非必需对象，只被弱引用关联的对象只能生存到下一次垃圾收集发生为止。在系统GC时，只要发现弱引用，不管系统堆空间使用是否充足，都会回收掉只被弱引用关联的对象。==



==但是，由于垃圾回收器的线程通常优先级很低，因此，并不一定能很快地发现持有弱引用的对象。在这种情况下，弱引用对象可以存在较长的时间。==



弱引用和软引用一样，在构造弱引用时，==也可以指定一个引用队列==，当弱引用对象被回收时，就会加入指定的引用队列，通过这个队列可以跟踪对象的回收情况。



软引用、弱引用都非常适合来保存那些可有可无的缓存数据。如果这么做，当系统内存不足时，这些缓存数据会被回收，不会导致内存溢出。而当内存资源充足时，这些缓存数据又可以存在相当长的时间，从而起到加速系统的作用。



在JDK1.2版之后提供了WeakReference类来实现弱引用

```Java
public class WeakReferenceTest {
    public static class User {
        int id;
        String name;
        public User(int id,String name) {
            this.id = id;
            this.name = name;
        }

        @Override
        public String toString() {
            return "User{" +
                    "id=" + id +
                    ", name='" + name + '\'' +
                    '}';
        }
    }

    public static void main(String[] args) {
        WeakReference<User> user = new WeakReference<>(new User(1,"zgx"));

        System.out.println(user.get());
        System.gc();
        System.out.println("After GC");
        System.out.println(user.get());
    }
}
```

![image-20211110160220088](F:\笔记图片\img\image-20211110160220088.png)



#### 10.7.4 虚引用 - 对象回收跟踪

也称为“幽灵引用”或者“幻影引用”，是所有引用类型中最弱的一个。



一个对象是否有虚引用的存在，完全不会决定对象的生命周期。如果一个对象仅持有虚引用，那么它和没有引用几乎是一样的，随时都可能被垃圾回收器回收。



它不能单独使用，也无法通过虚引用来获取被引用的对象。当试图通过虚引用的get()方法取得对象时，总是null



==为一个对象设置虚引用关联的唯一目的在于跟踪垃圾回收过程。比如：能在这个对象被收集器回收时收到一个系统通知。==



虚引用必须和引用队列一起使用。虚引用在创建时必须提供一个引用队列作为参数。当垃圾回收器准备回收一个对象时，如果发现它还有虚引用，就会在回收对象后，将这个虚引用加入引用队列，以通知应用程序对象的回收情况。



由于虚引用可以跟踪对象的回收时间，因此，也可以将一些资源释放操作放置在虚引用中执行和记录。



在JDK1.2版之后提供了PhantomReference类来实现虚引用。

```Java
Object obj = new Object(); // 声明强引用
ReferenceQueue phantomQueue = new ReferenceQueue();
PhantomReference<Object> sf = new PhantomReference<>(obj, phantomQueue);
obj = null;
```



#### 10.7.5 终结器引用

它用于实现对象的finalize() 方法，也可以称为终结器引用。无需手动编码，其内部配合引用队列使用。



在GC时，终结器引用入队。由Finalizer线程通过终结器引用找到被引用对象调用它的finalize()方法，第二次GC时才回收被引用的对象





## 11.垃圾回收器

### 11.1 GC分类与性能指标

- 垃圾收集器没有在规范中进行过多的规定，可以由不同的厂商、不同版本的JVM来实现。



- 由于JDK的版本处于高速迭代过程中，因此Java发展至今已经衍生了众多的GC版本。



- 从不同角度分析垃圾收集器，可以将GC分为不同的类型。



按==线程数==分，可以分为==串行垃圾回收器和并行垃圾回收器==。

![img](https://www.yuque.com/api/filetransfer/images?url=https%3A%2F%2Fgitee.com%2Fvectorx%2FImageCloud%2Fraw%2Fmaster%2Fimg%2F20210512201148.png&sign=97d1548c67249e71ff4a48c8ab553df236ecbec29c8d17cf7686aaa879118b09)

串行回收指的是在同一时间段内只允许有一个CPU用于执行垃圾回收操作，此时工作线程被暂停，直至垃圾收集工作结束。

- 在诸如单CPU处理器或者较小的应用内存等硬件平台不是特别优越的场合，串行回收器的性能表现可以超过并行回收器和并发回收器。所以，串行回收默认被应用在客户端的Client模式下的JVM中

- 在并发能力比较强的CPU上，并行回收器产生的停顿时间要短于串行回收器。

和串行回收相反，并行收集可以运用多个CPU同时执行垃圾回收，因此提升了应用的吞吐量，不过并行回收仍然与串行回收一样，采用独占式，使用了“Stop-the-World”机制。





按照==工作模式==分，可以分为==并发式垃圾回收器和独占式垃圾回收器==。

- 并发式垃圾回收器与应用程序线程交替工作，以尽可能减少应用程序的停顿时间。

- 独占式垃圾回收器（Stop the world）一旦运行，就停止应用程序中的所有用户线程，直到垃圾回收过程完全结束。

![img](https://www.yuque.com/api/filetransfer/images?url=https%3A%2F%2Fgitee.com%2Fvectorx%2FImageCloud%2Fraw%2Fmaster%2Fimg%2F20210512201152.png&sign=7f8dffbef14a00a577d3a30dcca8cb672f8180a49b5fe3742b780cdf8fa58b38)



按==碎片处理方式==分，可分为==压缩式垃圾回收器和非压缩式垃圾回收器。==

- 压缩式垃圾回收器会在回收完成后，对存活对象进行压缩整理，消除回收后的碎片。

- 非压缩式的垃圾回收器不进行这步操作。



按==工作的内存区间==分，==又可分为年轻代垃圾回收器和老年代垃圾回收器。==



**评估GC的性能指标**

- ==吞吐量：运行用户代码的时间占总运行时间的比例==
  - 总运行时间 = 程序运行的时间 + 内存回收的时间
- 垃圾收集开销：吞吐量的补数，垃圾收集所用时间与总运行时间的比例。
- ==暂停时间：执行垃圾收集时，程序的工作线程被暂停的时间==
- 收集频率：相对于应用程序的执行，收集操作发生的频率。

- ==内存占用：Java堆区所占的内存大小==。

- 快速：一个对象从诞生到被回收所经历的时间。





吞吐量、暂停时间、内存占用 这三者共同构成一个“不可能三角”。==三者总体的表现会随着技术进步而越来越好。一款优秀的收集器通常最多同时满足其中的两项。==

这三项里，==暂停时间的重要性日益凸显==。因为随着硬件发展，内存占用多些越来越能容忍，硬件性能的提升也有助于降低收集器运行时对应用程序的影响，即提高了吞吐量。而内存的扩大，对延迟反而带来负面效果。

简单来说，主要抓住两点：==吞吐量、暂停时间==



**吞吐量**

吞吐量就是CPU用于运行用户代码的时间与CPU总消耗时间的比值，即吞吐量 = 运行用户代码时间 /（运行用户代码时间+垃圾收集时间）。比如：虚拟机总共运行了100分钟，其中垃圾收集花掉1分钟，那吞吐量就是99%。



这种情况下，应用程序能容忍较高的暂停时间，因此，高吞吐量的应用程序有更长的时间基准，快速响应是不必考虑的



吞吐量优先，意味着在单位时间内，STW的时间最短：0.2 + 0.2 = 0.4

![img](https://www.yuque.com/api/filetransfer/images?url=https%3A%2F%2Fgitee.com%2Fvectorx%2FImageCloud%2Fraw%2Fmaster%2Fimg%2F20210512201156.png&sign=47fea20e41902224e93230764ce7cdc630e02577b3bbf32907d9c6d0228026d7)



**暂停时间**

“暂停时间”是指一个时间段内应用程序线程暂停，让GC线程执行的状态。



例如，GC期间100毫秒的暂停时间意味着在这100毫秒期间内没有应用程序线程是活动的。



暂停时间优先，意味着尽可能让单次STW的时间最短：0.1 + 0.1 + 0.1 + 0.1 + 0.1 = 0.5

![img](https://www.yuque.com/api/filetransfer/images?url=https%3A%2F%2Fgitee.com%2Fvectorx%2FImageCloud%2Fraw%2Fmaster%2Fimg%2F20210512201158.png&sign=8428b2b141b94dfb759abde7ff5a6fec88ae53df815550f0d809bbcafb3b9b17)





高吞吐量较好因为这会让应用程序的最终用户感觉只有应用程序线程在做“生产性”工作。直觉上，吞吐量越高程序运行越快。

低暂停时间（低延迟）较好因为从最终用户的角度来看不管是GC还是其他原因导致一个应用被挂起始终是不好的。这取决于应用程序的类型，==有时候甚至短暂的200毫秒暂停都可能打断终端用户体验==。因此，具有低的较大暂停时间是非常重要的，特别是对于一个交互式应用程序。

不幸的是”高吞吐量”和”低暂停时间”是一对相互竞争的目标（矛盾）。

- 因为如果选择以吞吐量优先，那么必然需要降低内存回收的执行频率，但是这样会导致GC需要更长的暂停时间来执行内存回收。

- 相反的，如果选择以低延迟优先为原则，那么为了降低每次执行内存回收时的暂停时间，也只能频繁地执行内存回收，但这又引起了年轻代内存的缩减和导致程序吞吐量的下降。

在设计（或使用）GC算法时，我们必须确定我们的目标：==一个GC算法只可能针对两个目标之一（即只专注于较大吞吐量或最小暂停时间），或尝试找到一个二者的折衷。==

现在标准：==在最大吞吐量优先的情况下，降低停顿时间==



### 11.2 不同垃圾回收器的概述

垃圾收集机制是Java的招牌能力，极大地提高了开发效率。这当然也是面试的热点。

![image-20211111103129658](F:\笔记图片\img\image-20211111103129658.png)

==7种经典垃圾回收器==

- 串行回收器：Serial、Serial Old

- 并行回收器：ParNew、Parallel Scavenge、Parallel old

- 并发回收器：CMS、G1



**7款经典垃圾回收器与垃圾分代之间的关系**

![img](https://www.yuque.com/api/filetransfer/images?url=https%3A%2F%2Fgitee.com%2Fvectorx%2FImageCloud%2Fraw%2Fmaster%2Fimg%2F20210512201224.png&sign=cf2f9d20c380b35fb2aa2bf2142aa778c2b4f9b972a400b05413699d8a52224b)

- 新生代收集器：Serial、ParNew、Parallel Scavenge； 

-  老年代收集器：Serial Old、Parallel Old、CMS； 

-  整堆收集器：G1； 



**垃圾收集器的组合关系**

![img](https://www.yuque.com/api/filetransfer/images?url=https%3A%2F%2Fgitee.com%2Fvectorx%2FImageCloud%2Fraw%2Fmaster%2Fimg%2F20210512201227.png&sign=5f37911072ee9e7c82ce2e8b70a0536dfd96c27b61bc164a8c4df887bb9daea7)

1. 两个收集器间有连线，表明它们可以搭配使用：Serial/Serial Old、Serial/CMS、ParNew/Serial Old、ParNew/CMS、Parallel Scavenge/Serial Old、Parallel Scavenge/Parallel Old、G1；

2. 其中Serial Old作为CMS出现"`Concurrent Mode Failure`"失败的后备预案。

3. （红色虚线）由于维护和兼容性测试的成本，在JDK 8时将Serial+CMS、ParNew+Serial Old这两个组合声明为废弃（JEP173），并在JDK9中完全取消了这些组合的支持（JEP214），即：移除。

4. （绿色虚线）JDK14中：弃用Parallel Scavenge和Serialold GC组合（JEP366）

5. （绿色虚框）JDK14中：删除CMS垃圾回收器（JEP363）



**为什么要有很多收集器，一个不够吗？**

==因为Java的使用场景很多，移动端，服务器等。所以就需要针对不同的场景，提供不同的垃圾收集器，提高垃圾收集的性能。==

==虽然我们会对各个收集器进行比较，但并非为了挑选一个最好的收集器出来。没有一种放之四海皆准、任何场景下都适用的完美收集器存在，更加没有万能的收集器。所以我们选择的只是对具体应用最合适的收集器。==



**如何查看默认的垃圾收集器**

`-XX:+PrintCommandLineFlags`：查看命令行相关参数（包含使用的垃圾收集器）

使用命令行指令：`jinfo -flag 相关垃圾回收器参数 进程ID`





### 11.3 Serial回收器：串行回收

==Serial收集器是最基本、历史最悠久的垃圾收集器了==。JDK1.3之前回收新生代唯一的选择。



Serial收集器作为==HotSpot中client模式下的默认新生代垃圾收集器。==



Serial收集器采用==复制算法、串行回收和"stop-the-World"机制==的方式执行内存回收。



除了年轻代之外，Serial收集器还提供用于执行老年代垃圾收集的Serial Old收集器。Serial Old收集器同样也采用了==串行回收和"Stop the World"机制，====只不过内存回收算法使用的是标记-压缩算法。==

- Serial old是运行在Client模式下默认的老年代的垃圾回收器

- Serial 0ld在Server模式下主要有两个用途：==① 与新生代的Parallel scavenge配合使用 ② 作为老年代CMS收集器的后备垃圾收集方案==

![img](https://www.yuque.com/api/filetransfer/images?url=https%3A%2F%2Fgitee.com%2Fvectorx%2FImageCloud%2Fraw%2Fmaster%2Fimg%2F20210512201232.png&sign=2583630ffbc073a4da9bc69713e9f666ef07d199deb6df9b66769c046556b3c7)



这个收集器是一个单线程的收集器，==但它的“单线程”的意义并不仅仅说明它只会使用一个CPU或一条收集线程去完成垃圾收集工作，更重要的是在它进行垃圾收集时，必须暂停其他所有的工作线程，直到它收集结束（Stop The World）==



**优势**

- ==简单而高效（与其他收集器的单线程比）==，对于限定单个CPU的环境来说，Serial收集器由于没有线程交互的开销，专心做垃圾收集自然可以获得最高的单线程收集效率。运行在Client模式下的虚拟机是个不错的选择。

- 在用户的桌面应用场景中，可用内存一般不大（几十MB至一两百MB），可以在较短时间内完成垃圾收集（几十ms至一百多ms），只要不频繁发生，使用串行回收器是可以接受的。



在HotSpot虚拟机中，使用`-XX:+UseSerialGC`参数可以指定年轻代和老年代都使用串行收集器。等价于新生代用Serial GC，且老年代用Serial Old GC



这种垃圾收集器大家了解，现在已经不用串行的了。而且在限定单核cpu才可以用。现在都不是单核的了。

对于交互较强的应用而言，这种垃圾收集器是不能接受的。一般在Java web应用程序中是不会采用串行垃圾收集器的。



### 11.4 ParNew垃圾回收器：并行回收

如果说Serial GC是年轻代中的单线程垃圾收集器，==那么ParNew收集器则是Serial收集器的多线程版本==。

Par是Parallel的缩写，New：只能处理的是新生代



ParNew 收集器除了==采用并行回收的方式执行内存回收外，两款垃圾收集器之间几乎没有任何区别==。==ParNew收集器在年轻代中同样也是采用复制算法、"Stop-the-World"机制。==



ParNew 是很多JVM运行在==Server模式==下新生代的默认垃圾收集器。

![img](https://www.yuque.com/api/filetransfer/images?url=https%3A%2F%2Fgitee.com%2Fvectorx%2FImageCloud%2Fraw%2Fmaster%2Fimg%2F20210512201237.png&sign=7c0ebb89f36a9af0b118e7e3fe9793dea8789f563db30d6422e1557d24c22867)

- 对于新生代，回收次数频繁，使用并行方式高效。

- 对于老年代，回收次数少，使用串行方式节省资源。（CPU并行需要切换线程，串行可以省去切换线程的资源）



由于ParNew收集器是基于并行回收，那么是否可以断定ParNew收集器的回收效率在任何场景下都会比serial收集器更高效？

- ParNew 收集器运行在==多CPU的环境下，由于可以充分利用多CPU、多核心等物理硬件资源优势，可以更快速地完成垃圾收集，提升程序的吞吐量。==

- 但是在==单个CPU的环境==下，ParNew收集器不比Serial 收集器更高效。==虽然Serial收集器是基于串行回收，但是由于CPU不需要频繁地做任务切换，因此可以有效避免多线程交互过程中产生的一些额外开销。==



因为除Serial外，目前只有ParNew GC能与CMS收集器配合工作



在程序中，开发人员可以通过选项"`-XX:+UseParNewGC`"手动指定使用ParNew收集器执行内存回收任务。它表示年轻代使用并行收集器，不影响老年代。

`-XX:ParallelGCThreads`限制线程数量，默认开启和CPU数据相同的线程数。



### 11.5 Parallel Scavenge回收器：吞吐量优先

HotSpot的年轻代中除了拥有ParNew收集器是基于并行回收的以外，Parallel Scavenge收集器同样也采用了==复制算法、并行回收和"Stop the World"机制。==



那么Parallel 收集器的出现是否多此一举？

- 和ParNew收集器不同，==ParallelScavenge收集器的目标则是达到一个可控制的吞吐量（Throughput），它也被称为吞吐量优先的垃圾收集器。==

- ==自适应调节策略==也是Parallel Scavenge与ParNew一个重要区别。



高吞吐量则可以高效率地利用CPU时间，尽快完成程序的运算任务，==主要适合在后台运算而不需要太多交互的任务==。因此，常见在服务器环境中使用。例如，那些执行批量处理、订单处理、工资支付、科学计算的应用程序。



Parallel 收集器在JDK1.6时提供了用于执行老年代垃圾收集的Parallel Old收集器，用来代替老年代的Serial Old收集器。



==Parallel Old收集器采用了标记-压缩算法，但同样也是基于并行回收和"Stop-the-World"机制。==



![img](https://www.yuque.com/api/filetransfer/images?url=https%3A%2F%2Fgitee.com%2Fvectorx%2FImageCloud%2Fraw%2Fmaster%2Fimg%2F20210512201240.png&sign=e02ed931e14f2642bb7aae379ac60e3bc0e9859fda07f758daa224739488ebdd)



在==程序吞吐量优先的应用场景中，Parallel 收集器和Parallel Old收集器的组合==，在Server模式下的内存回收性能很不错。

在Java8中，默认是此垃圾收集器。



**参数配置**

-  `-XX:+UseParallelGC` 手动指定年轻代使用Parallel并行收集器执行内存回收任务。 

-  `-XX:+UseParallelOldGC` 手动指定老年代都是使用并行回收收集器。 

- - 分别适用于新生代和老年代。默认jdk8是开启的。

- - 上面两个参数，默认开启一个，另一个也会被开启。（互相激活）

-  `-XX:ParallelGCThreads` 设置年轻代并行收集器的线程数。一般地，最好与CPU数量相等，以避免过多的线程数影响垃圾收集性能。 ![img](https://g.yuque.com/gr/latex?ParallelGCThreads%20%3D%20%20%5Cbegin%7Bcases%7D%20%20CPU%5C_Count%20%26%20%5Ctext%20(CPU%5C_Count%20%3C%3D%208)%20%5C%5C%20%203%20%2B%20(5%20*%20CPU%EF%BC%BFCount%20%2F%208)%20%26%20%5Ctext%20(CPU%5C_Count%20%3E%208)%20%20%5Cend%7Bcases%7D%20%0A) 

-  `-XX:MaxGCPauseMillis` 设置垃圾收集器最大停顿时间（即STw的时间）。单位是毫秒。 

- - 为了尽可能地把停顿时间控制在MaxGCPauseMills以内，收集器在工作时会调整Java堆大小或者其他一些参数。

- - 对于用户来讲，停顿时间越短体验越好。但是在服务器端，我们注重高并发，整体的吞吐量。所以服务器端适合Parallel，进行控制。

- - 该参数使用需谨慎。

-  `-XX:GCTimeRatio` 垃圾收集时间占总时间的比例（=1/（N+1））。用于衡量吞吐量的大小。 

- - 取值范围（0, 100）。默认值99，也就是垃圾回收时间不超过1%。

- - 与前一个`-XX:MaxGCPauseMillis`参数有一定矛盾性。暂停时间越长，Radio参数就容易超过设定的比例。

-  `-XX:+UseAdaptivesizePolicy` 设置Parallel Scavenge收集器具有自适应调节策略 

- - 在这种模式下，年轻代的大小、Eden和Survivor的比例、晋升老年代的对象年龄等参数会被自动调整，已达到在堆大小、吞吐量和停顿时间之间的平衡点。

- - 在手动调优比较困难的场合，可以直接使用这种自适应的方式，仅指定虚拟机的最大堆、目标的吞吐量（`GCTimeRatio`）和停顿时间（`MaxGCPauseMills`），让虚拟机自己完成调优工作。



### 11.6 CMS回收器-低延迟

在JDK1.5时期，==Hotspot推出了一款在强交互应用中几乎可认为有划时代意义的垃圾收集器：CMS（Concurrent-Mark-Sweep）收集器==，这款收集器是HotSpot虚拟机中第一款真正意义上的并发收集器，==它第一次实现了让垃圾收集线程与用户线程同时工作。==



CMS收集器的关注点是尽可能缩短垃圾收集时用户线程的停顿时间。停顿时间越短（低延迟）就越适合与用户交互的程序，良好的响应速度能提升用户体验。

- 目前很大一部分的Java应用集中在互联网站或者B/S系统的服务端上，这类应用尤其重视服务的响应速度，希望系统停顿时间最短，以给用户带来较好的体验。CMS收集器就非常符合这类应用的需求。



CMS的垃圾收集算法采用==标记-清除算法，并且也会"Stop-the-World"==



不幸的是，CMS作为老年代的收集器，却无法与JDK1.4.0中已经存在的新生代收集器Parallel Scavenge配合工作，所以在JDK1.5中使用CMS来收集老年代的时候，新生代只能选择ParNew或者Serial收集器中的一个。



在G1出现之前，CMS使用还是非常广泛的。一直到今天，仍然有很多系统使用CMS GC。



![img](https://www.yuque.com/api/filetransfer/images?url=https%3A%2F%2Fgitee.com%2Fvectorx%2FImageCloud%2Fraw%2Fmaster%2Fimg%2F20210512201243.png&sign=58fa83846c183bca27bd6679a78803e261ed896dd745300820b6db31f7851907)

CMS整个过程比之前的收集器要复杂，整个过程分为4个主要阶段，即初始标记阶段、并发标记阶段、重新标记阶段和并发清除阶段

- **初始标记**（Initial-Mark）阶段：在这个阶段中，程序中所有的工作线程都将会因为“Stop-the-World”机制而出现短暂的暂停，这个阶段的主要任务==仅仅只是标记出GCRoots能直接关联到的对象==。一旦标记完成之后就会恢复之前被暂停的所有应用线程。由于直接关联对象比较小，所以这里的速度非常快。

- **并发标记**（Concurrent-Mark）阶段：从GC Roots的直接关联对象开始遍历整个对象图的过程，这个过程耗时较长但是不需要停顿用户线程，可以与垃圾收集线程一起并发运行。

- **重新标记**（Remark）阶段：由于在并发标记阶段中，程序的工作线程会和垃圾收集线程同时运行或者交叉运行，因此==为了修正并发标记期间，因用户程序继续运作而导致标记产生变动的那一部分对象的标记记录，==这个阶段的停顿时间通常会比初始标记阶段稍长一些，但也远比并发标记阶段的时间短。

- **并发清除**（Concurrent-Sweep）阶段：此阶段==清理删除掉标记阶段判断的已经死亡的对象，释放内存空间==。由于不需要移动存活对象，所以这个阶段也是可以与用户线程同时并发的



尽管CMS收集器采用的是并发回收（非独占式），但是在其初始化标记和再次标记这两个阶段中仍然需要执行“Stop-the-World”机制暂停程序中的工作线程，不过暂停时间并不会太长，因此可以说明目前所有的垃圾收集器都做不到完全不需要“stop-the-World”，只是尽可能地缩短暂停时间。



==由于最耗费时间的并发标记与并发清除阶段都不需要暂停工作，所以整体的回收是低停顿的。==



另外，由于在垃圾收集阶段用户线程没有中断，==所以在CMS回收过程中，还应该确保应用程序用户线程有足够的内存可用==。因此，CMS收集器不能像其他收集器那样等到老年代几乎完全被填满了再进行收集，而是当堆内存使用率达到某一阈值时，便开始进行回收，以确保应用程序在CMS工作过程中依然有足够的空间支持应用程序运行。要是CMS运行期间预留的内存无法满足程序需要，就会出现一次“`Concurrent Mode Failure`” 失败，这时虚拟机将启动后备预案：临时启用Serial Old收集器来重新进行老年代的垃圾收集，这样停顿时间就很长了。



CMS收集器的垃圾收集算法采用的是标记清除算法，这意味着每次执行完内存回收后，由于被执行内存回收的无用对象所占用的内存空间极有可能是不连续的一些内存块，不可避免地将会产生一些内存碎片。那么CMS在为新对象分配内存空间时，将无法使用指针碰撞（Bump the Pointer）技术，而只能够选择空闲列表（Free List）执行内存分配。



**有人会觉得既然Mark Sweep会造成内存碎片，那么为什么不把算法换成Mark Compact？**

- 答案其实很简单，因为当并发清除的时候，用Compact整理内存的话，原来的用户线程使用的内存还怎么用呢？要保证用户线程能继续执行，前提的它运行的资源不受影响嘛。Mark Compact更适合“Stop the World” 这种场景下使用



**CMS的优点**

- 并发收集

- 低延迟



**CMS的缺点**

- ==会产生内存碎片==，导致并发清除后，用户线程可用的空间不足。在无法分配大对象的情况下，不得不提前触发FullGC。

- ==CMS收集器对CPU资源非常敏感==。在并发阶段，它虽然不会导致用户停顿，但是会因为占用了一部分线程而导致应用程序变慢，总吞吐量会降低。

- ==CMS收集器无法处理浮动垃圾。==可能出现“`Concurrent Mode Failure`"失败而导致另一次Full GC的产生。==在并发标记阶段由于程序的工作线程和垃圾收集线程是同时运行或者交叉运行的，那么在并发标记阶段如果产生新的垃圾对象，CMS将无法对这些垃圾对象进行标记，最终会导致这些新产生的垃圾对象没有被及时回收，从而只能在下一次执行GC时释放这些之前未被回收的内存空间。==

**参数设置**

-  `-XX:+UseConcMarkSweepGC`手动指定使用CMS收集器执行内存回收任务。
  开启该参数后会自动将`-xx:+UseParNewGC`打开。即：ParNew（Young区用）+CMS（Old区用）+ Serial Old的组合。 

-  `-XX:CMSInitiatingOccupanyFraction` 设置堆内存使用率的阈值，一旦达到该阈值，便开始进行回收。 

- - JDK5及以前版本的默认值为68，即当老年代的空间使用率达到68%时，会执行一次CMS回收。JDK6及以上版本默认值为92%

- - 如果内存增长缓慢，则可以设置一个稍大的值，大的阀值可以有效降低CMS的触发频率，减少老年代回收的次数可以较为明显地改善应用程序性能。反之，如果应用程序内存使用率增长很快，则应该降低这个阈值，以避免频繁触发老年代串行收集器。因此通过该选项便可以有效降低Ful1Gc的执行次数。

-  `-XX:+UseCMSCompactAtFullCollection` 用于指定在执行完Full GC后对内存空间进行压缩整理，以此避免内存碎片的产生。不过由于内存压缩整理过程无法并发执行，所带来的问题就是停顿时间变得更长了。 

-  `-XX:CMSFullGCsBeforeCompaction` 设置在执行多少次Full GC后对内存空间进行压缩整理。 

-  `-XX:ParallelcMSThreads` 设置CMS的线程数量。 

- - CMS默认启动的线程数是（ParallelGCThreads+3）/4，ParallelGCThreads是年轻代并行收集器的线程数。当CPU资源比较紧张时，受到CMS收集器线程的影响，应用程序的性能在垃圾回收阶段可能会非常糟糕。



**HotSpot有这么多的垃圾回收器，那么如果有人问，Serial GC、Parallel GC、Concurrent Mark Sweep GC这三个Gc有什么不同呢？**

请记住以下口令：

- 如果你想要最小化地使用内存和并行开销，请选Serial GC；

- 如果你想要最大化应用程序的吞吐量，请选Parallel GC；

- 如果你想要最小化GC的中断或停顿时间，请选CMS GC。



### 11.7 G1垃圾回收器：区域化分代化

**既然我们已经有了前面几个强大的GC，为什么还要发布Garbage First（G1）？**

- 原因就在于应用程序所应对的业务越来越庞大、复杂，用户越来越多，没有GC就不能保证应用程序正常进行，而经常造成STW的GC又跟不上实际的需求，所以才会不断地尝试对GC进行优化。G1（Garbage-First）垃圾回收器是在Java7 update4之后引入的一个新的垃圾回收器，是当今收集器技术发展的最前沿成果之一。

- 与此同时，为了适应现在不断扩大的内存和不断增加的处理器数量，进一步降低暂停时间（pause time），同时兼顾良好的吞吐量。



==官方给G1设定的目标是在延迟可控的情况下获得尽可能高的吞吐量，所以才担当起“全功能收集器”的重任与期望。==





**为什么名字叫 Garbage First(G1)呢？**

- 因为G1是一个并行回收器，它把堆内存分割为很多不相关的区域（Region）（物理上不连续的）。使用不同的Region来表示Eden、幸存者0区，幸存者1区，老年代等。

  G1 GC有计划地避免在整个Java堆中进行全区域的垃圾收集。G1跟踪各个Region里面的垃圾堆积的价值大小（回收所获得的空间大小以及回收所需时间的经验值），在后台维护一个优先列表，每次根据允许的收集时间，优先回收价值最大的Region。

  ==由于这种方式的侧重点在于回收垃圾最大量的区间（Region），所以我们给G1一个名字：垃圾优先（Garbage First）。==

  

  

  G1（Garbage-First）是一款面向服务端应用的垃圾收集器，==主要针对配备多核CPU及大容量内存的机器==，以极高概率满足GC停顿时间的同时，还兼具高吞吐量的性能特征。

  在JDK1.7版本正式启用，移除了Experimenta1的标识，是JDK9以后的默认垃圾回收器，取代了CMS回收器以及Parallel+Parallel Old组合。被Oracle官方称为“==全功能的垃圾收集器”==。

  与此同时，CMS已经在JDK9中被标记为废弃（deprecated）。在jdk8中还不是默认的垃圾回收器，需要使用`-XX:+UseG1GC`来启用。



#### 11.7.1 G1回收器的特点

与其他GC收集器相比，G1使用了全新的分区算法，其特点如下所示：

**并行与并发**

- 并行性：G1在回收期间，可以有多个GC线程同时工作，有效利用多核计算能力。此时用户线程STW

- 并发性：G1拥有与应用程序交替执行的能力，部分工作可以和应用程序同时执行，因此，一般来说，不会在整个回收阶段发生完全阻塞应用程序的情况



**分代收集**

- 从分代上看，==G1依然属于分代型垃圾回收器==，它会区分年轻代和老年代，年轻代依然有Eden区和Survivor区。但从堆的结构上看，它不要求整个Eden区、年轻代或者老年代都是连续的，也不再坚持固定大小和固定数量。

- ==将堆空间分为若干个区域（Region），这些区域中包含了逻辑上的年轻代和老年代。==

- 和之前的各类回收器不同，==它同时兼顾年轻代和老年代。==对比其他回收器，或者工作在年轻代，或者工作在老年代；

![img](https://www.yuque.com/api/filetransfer/images?url=https%3A%2F%2Fgitee.com%2Fvectorx%2FImageCloud%2Fraw%2Fmaster%2Fimg%2F20210512201255.png&sign=4c212d3d87399a862ba4b799e9a77a240fc2f17baa871164552c89141d36b050)



**空间整合**

- CMS：==“标记-清除”算法、内存碎片、若干次Gc后进行一次碎片整理==

- G1将内存划分为一个个的region。==内存的回收是以region作为基本单位的。Region之间是复制算法，但整体上实际可看作是标记-压缩（Mark-Compact）算法，两种算法都可以避免内存碎片。==这种特性有利于程序长时间运行，分配大对象时不会因为无法找到连续内存空间而提前触发下一次GC。==尤其是当Java堆非常大的时候，G1的优势更加明显。==



**可预测的停顿时间模型（即：软实时soft real-time）**

这是G1相对于CMS的另一大优势，G1除了追求低停顿外，还能建立可预测的停顿时间模型，能让使用者明确指定在一个长度为M毫秒的时间片段内，消耗在垃圾收集上的时间不得超过N毫秒。

- 由于分区的原因，G1可以只选取部分区域进行内存回收，这样缩小了回收的范围，因此对于全局停顿情况的发生也能得到较好的控制。

- G1跟踪各个Region里面的垃圾堆积的价值大小（回收所获得的空间大小以及回收所需时间的经验值），在后台维护一个优先列表，每次根据允许的收集时间，==优先回收价值最大的Region。==保证了G1收集器在有限的时间内可以获取尽可能高的收集效率。

- 相比于CMSGC，G1未必能做到CMS在最好情况下的延时停顿，但是最差情况要好很多。



> 缺点

相较于CMS，G1还不具备全方位、压倒性优势。比如在用户程序运行过程中，G1无论是为了垃圾收集产生的内存占用（Footprint）还是程序运行时的额外执行负载（Overload）都要比CMS要高。

从经验上来说，在小内存应用上CMS的表现大概率会优于G1，而G1在大内存应用上则发挥其优势。平衡点在6-8GB之间。



#### 11.7.2 G1参数设置

- `-XX:+UseG1GC`：手动指定使用G1垃圾收集器执行内存回收任务

- `-XX:G1HeapRegionSize` 设置每个Region的大小。值是2的幂，范围是1MB到32MB之间，目标是根据最小的Java堆大小划分出约2048个区域。默认是堆内存的1/2000。

- `-XX:MaxGCPauseMillis` 设置期望达到的最大GC停顿时间指标（JVM会尽力实现，但不保证达到）。默认值是200ms（人的平均反应速度）

- `-XX:+ParallelGCThread` 设置STW工作线程数的值。最多设置为8（上面说过Parallel回收器的线程计算公式，当CPU_Count > 8时，ParallelGCThreads 也会大于8）

- `-XX:ConcGCThreads` 设置并发标记的线程数。将n设置为并行垃圾回收线程数（ParallelGCThreads）的1/4左右。

- `-XX:InitiatingHeapOccupancyPercent` 设置触发并发GC周期的Java堆占用率阈值。超过此值，就触发GC。默认值是45。



#### 11.7.3 G1回收器常见的操作步骤

G1的设计原则就是简化JVM性能调优，开发人员只需要简单的三步即可完成调优：

- 第一步：开启G1垃圾收集器

- 第二步：设置堆的最大内存

- 第三步：设置最大的停顿时间

G1中提供了三种垃圾回收模式：Young GC、Mixed GC和Full GC，在不同的条件下被触发。



#### 11.7.4 G1回收器适用的场景

面向==服务端应用，针对具有大内存、多处理器的机器。==（在普通大小的堆里表现并不惊喜）



最主要的应用是需要低GC延迟，并具有大堆的应用程序提供解决方案；如：在堆大小约6GB或更大时，可预测的暂停时间可以低于0.5秒；（G1通过每次只清理一部分而不是全部的Region的增量式清理来保证每次GC停顿时间不会过长）。



用来替换掉JDK1.5中的CMS收集器；在下面的情况时，使用G1可能比CMS好：



- 超过50%的Java堆被活动数据占用；

- 对象分配频率或年代提升频率变化很大；

- GC停顿时间过长（长于0.5至1秒）



HotSpot垃圾收集器里，除了G1以外，其他的垃圾收集器使用内置的JVM线程执行GC的多线程操作，而G1 GC可以采用应用线程承担后台运行的GC工作，即当JVM的GC线程处理速度慢时，系统会调用应用程序线程帮助加速垃圾回收过程。



#### 11.7.5  分区region：化整为零

使用G1收集器时，它将整个Java堆划分成约2048个大小相同的独立Region块，每个Region块大小根据堆空间的实际大小而定，整体被控制在1MB到32MB之间，且为2的N次幂，即1MB，2MB，4MB，8MB，16MB，32MB。可以通过`-XX:G1HeapRegionSize`设定。==所有的Region大小相同，且在JVM生命周期内不会被改变。==



虽然还保留有新生代和老年代的概念，但新生代和老年代不再是物理隔离的了，它们都是一部分Region（不需要连续）的集合。通过Region的动态分配方式实现逻辑上的连续。

![img](https://www.yuque.com/api/filetransfer/images?url=https%3A%2F%2Fgitee.com%2Fvectorx%2FImageCloud%2Fraw%2Fmaster%2Fimg%2F20210512201300.png&sign=e86f6a8faf7210db39e6ceb9fed217034e996d33d148089376bafc3739f210a4)



一个region有可能属于Eden，Survivor或者Old/Tenured内存区域。但是一个region只可能属于一个角色。图中的E表示该region属于Eden内存区域，S表示属于survivor内存区域，O表示属于Old内存区域。图中空白的表示未使用的内存空间。



G1垃圾收集器还增加了一种新的内存区域，==叫做Humongous内存区域，如图中的H块。主要用于存储大对象，如果超过1.5个region，就放到H。==



设置H的原因：对于堆中的对象，默认直接会被分配到老年代，但是如果它是一个短期存在的大对象就会对垃圾收集器造成负面影响。为了解决这个问题，G1划分了一个Humongous区，它用来专门存放大对象。==如果一个H区装不下一个大对象，那么G1会寻找连续的H区来存储。为了能找到连续的H区，有时候不得不启动Full GC。G1的大多数行为都把H区作为老年代的一部分来看待。==



每个Region都是通过指针碰撞来分配空间<img src="https://www.yuque.com/api/filetransfer/images?url=https%3A%2F%2Fgitee.com%2Fvectorx%2FImageCloud%2Fraw%2Fmaster%2Fimg%2F20210512201303.png&sign=8e20d77248cf28ffbeed88ab66b789935810477d90a840f188247eed4864e008" alt="img" style="zoom:100%;" />



#### 11.7.6 G1回收器垃圾回收过程

G1GC的垃圾回收过程主要包括如下三个环节：

-  年轻代GC（Young GC） 

-  老年代并发标记过程（Concurrent Marking） 

-  混合回收（Mixed GC）
  （如果需要，单线程、独占式、高强度的Full GC还是继续存在的。它针对GC的评估失败提供了一种失败保护机制，即强力回收。） 



![img](https://www.yuque.com/api/filetransfer/images?url=https%3A%2F%2Fgitee.com%2Fvectorx%2FImageCloud%2Fraw%2Fmaster%2Fimg%2F20210512201306.png&sign=01d1619a2974131a0f72cdbb37270b8e7d7d29a6e4efd58a8563018ac740c8b2)



顺时针，Young gc -> Young gc + Concurrent mark->Mixed GC顺序，进行垃圾回收。



- 应用程序分配内存，当年轻代的Eden区用尽时开始年轻代回收过程；G1的年轻代收集阶段是一个==并行的独占式（并行：多个垃圾回收线程同时运行，独占：会产生STW）==收集器。==在年轻代回收期，G1GC暂停所有应用程序线程，启动多线程执行年轻代回收。然后从年轻代区间移动存活对象到Survivor区间或者老年区间，也有可能是两个区间都会涉及。==

- 当堆内存使用达到一定值（默认45%）时，开始老年代并发标记过程。

- 标记完成马上开始混合回收过程。对于一个混合回收期，G1 GC从老年区间移动存活对象到空闲区间，这些空闲区间也就成为了老年代的一部分。和年轻代不同，老年代的G1回收器和其他GC不同，==G1的老年代回收器不需要整个老年代被回收，一次只需要扫描/回收一小部分老年代的Region就可以了。同时，这个老年代Region是和年轻代一起被回收的。==



举个例子：一个Web服务器，Java进程最大堆内存为4G，每分钟响应1500个请求，每45秒钟会新分配大约2G的内存。G1会每45秒钟进行一次年轻代回收，每31个小时整个堆的使用率会达到45%，会开始老年代并发标记过程，标记完成后开始四到五次的混合回收。



**Remembered Set(记忆集)**

-  一个对象被不同区域引用的问题 

-  一个Region不可能是孤立的，一个Region中的对象可能被其他任意Region中对象引用，判断对象存活时，是否需要扫描整个Java堆才能保证准确？ 

-  在其他的分代收集器，也存在这样的问题（而G1更突出）回收新生代也不得不同时扫描老年代？ 

-  这样的话会降低MinorGC的效率； 

> **解决方法：**

- 无论G1还是其他分代收集器，JVM都是使用Remembered Set来避免全局扫描：

- 每个Region都有一个对应的Remembered Set；

- 每次Reference类型数据写操作时，都会产生一个Write Barrier暂时中断操作；

- 然后检查将要写入的引用指向的对象是否和该Reference类型数据在不同的Region（其他收集器：检查老年代对象是否引用了新生代对象）；

- 如果不同，通过CardTable把相关引用信息记录到引用指向对象的所在Region对应的Remembered Set中；

- 当进行垃圾收集时，在GC根节点的枚举范围加入Remembered Set；就可以保证不进行全局扫描，也不会有遗漏。

![img](https://www.yuque.com/api/filetransfer/images?url=https%3A%2F%2Fgitee.com%2Fvectorx%2FImageCloud%2Fraw%2Fmaster%2Fimg%2F20210512201310.png&sign=4c094431eef0322e1c8b412d084588434191d02b86161fbca719084c56bdf7f3)



##### 1.年轻代GC

- JVM启动时，G1 先准备好Eden区，程序在运行过程中不断创建对象到Eden区，当Eden空间耗尽时，G1会启动一次年轻代垃圾回收过程。

- 年轻代垃圾回收只会回收Eden区和Survivor区。

- YGC时，首先G1停止应用程序的执行（Stop一The一World），G1创建回收集（Collection Set），回收集是指需要被回收的内存分段的集合，年轻代回收过程的回收集包含年轻代Eden区和Survivor区所有的内存分段。

  ![image-20211112105722225](F:\笔记图片\img\image-20211112105722225.png)

- 然后开始如下回收过程：

  - **第一阶段，扫描根**。 根是指static变量指向的对象，正在执行的方法调用链条上的局部变量等。根引用连同RSet记录的外部引用作为扫描存活对象的入口。

  - **第二阶段，更新RSet**

    处理dirty card queue（ 见备注）中的card，更新RSet。 此阶段完成后，RSet可 以准确的反映老年代对所在的内存分段中对象的引用。

    - dirty card queue: 对于应用程序的引用赋值语句object.field=object，JVM会在之前和之后执行特殊的操作以在dirty card queue中入队一个保存了对象引用信息的card。在年轻代回收的时候， G1会对Dirty Card Queue中所有的card进行处理，以更新RSet，保证RSet实时准确的反映引用关系。 那为什么不在引用赋值语句处直接更新RSet呢？这是为了性能的需要，RSet的处理需要线程同步，开销会很大，使用队列性能会好很多。

  - **第三阶段，处理RSet**。 识别被老年代对象指向的Eden中的对象，这些被指向的Eden中的对象被认为是存活的对象。

  - **第四阶段，复制对象**。 此阶段，对象树被遍历，Eden区 内存段中存活的对象会被复制到Survivor区中空的内存分段，Survivor区内存段中存活的对象如果年龄未达阈值，年龄会加1，达到阀值会被会被复制到01d区中空的内存分段。如果Survivor空间不够，Eden空间的 部分数据会直接晋升到老年代空间。

  - **第五阶段，处理引用**。 处理Soft，Weak， Phantom， Final， JNI Weak等引用。最终Eden空间的数据为空，GC停止工作，而目标内存中的对象都是连续存储的，没有碎片，所以复制过程可以达到内存整理的效果，减少碎片。





##### 2.并发标记过程

- 初始标记阶段：标记从根节点直接可达的对象。这个阶段是STW的，并且会触发一.次年轻代GC。
- 根区域扫描（Root Region Scanning） ： G1 GC扫描Survivor区 直接可达的老年代区域对象，并标记被引用的对象。这一过程必 须在young GC之前完成。
- 并发标记（Concurrent Marking）： 在整个堆中进行并发标记（和应用程序并发执行），此过程可能被young GC中断。在并发标记阶段，若发现区域对象中的所有对象都是垃圾，那这个区域会被立即回收。同时，并发标记过程中，会计算每个区域的对象活性（区域中存活对象的比例）。
- 再次标记（Remark）： 由 于应用程序持续进行，需要修正上一次的标记结果。是STW的。G1中采用了比CMS更快的初始快照算法：snapshot一at一the一beginning （SATB）。
- 独占清理（cleanup，STW）：计算各个区域的存活对象和GC回收比例，并进行排序，识别可以混合回收的区域。为下阶段做铺垫。是STW的。
  - ➢这个阶段并不会实际上去做垃圾的收集
- 并发清理阶段：**识别并清理完全空闲的区域**。

##### 3. 混合回收

![img](https://pic.imgdb.cn/item/6031dad65f4313ce25f2c112.png)当越来越多的对象晋升到老年代oldregion时，为了避免堆内存被耗尽，虚拟机会触发一个混合的垃圾收集器，即Mixed GC， 该算法并不是一个0ldGC，除了回收整个Young Region，还会回收一部分的0ldRegion。这里需要注意：是一部分老年代， 而不是全部老年代。可以选择哪些0ldRegion进行收集，从而可以对垃圾回收的耗时时间进行控制。也要注意的是Mixed GC并不是Fu1l GC。

- 并发标记结束以后，老年代中百分百为垃圾的内存分段被回收了，部分为垃圾的内存分段被计算了出来。默认情况下，这些老年代的内存分段会分8次（可以通过一XX： G1MixedGCCountTarget设置）被回收。
- 混合回收的回收集（Collection Set） 包括八分之一的老年代内存分段，Eden区内存分段，Survivor区内存分段。混合回收的算法和年轻代回收的算法完全一样，只是回收集多了老年代的内存分段。具体过程请参考上面的年轻代回收过程。
- 由于老年代中的内存分段默认分8次回收，G1会优先回收垃圾多的内存分段。垃圾占内存分段比例越高的，越会被先回收。**并且有一个阈值会决定内存分段是否被回收**，一xX： G1MixedGCLiveThresholdPercent，默认为65%，意思是垃圾占内存分段比例要达到65%才会被回收。如果垃圾占比太低，意味着存活的对象占比高，在复制的时候会花费更多的时间。
- 混合回收并不一定要进行8次。有一个阈值一Xx： G1HeapWastePercent，默认值为10%，意思是允许整个堆内存中有10%的空间被浪费，意味着如果发现可以回收的垃圾占堆内存的比例低于10%，则不再进行混合回收。因为GC会花费很多的时间但是回收到的内存却很少。



##### 4.full GC

G1的初衷就是要避免Full GC的出现。但是如果上述方式不能正常工作，G1会停止应用程序的执行（Stop一 The一World），使用单线程的内存回收算法进行垃圾回收，性能会非常差，应用程序停顿时间会很长。 要避免Full GC的发生，一旦发生需要进行调整。什么时候会发生Full GC呢？比如堆内存太小，当G1在复制存活对象的时候没有空的内存分段可用，则会回退到full gc， 这种情况可以通过增大内存解决。  导致G1Full GC的原因可能有两个：

- 1.Evacuation的时候没有足够的to一 space来存放晋升的对象；
- 2.并发处理过程完成之前空间耗尽。



### 11.8 垃圾回收器总结

| 垃圾收集器           | 分类      | 作用位置      | 算法           | 特点           | 应用场景           |
| -------------------- | --------- | ------------- | -------------- | -------------- | ------------------ |
| Serial GC            | 串行      | 新生代        | 复制           | 高响应速度优先 | 单CPU              |
| Serial Old GC        | 串行      | 老年代        | 标记-整理      | 高响应速度优先 | 单CPU              |
| ParNew GC            | 并行      | 新生代        | 复制           | 高响应速度优先 | 多CPU与CMS搭配     |
| Parallel Scavenge GC | 并行      | 新生代        | 复制           | 高吞吐量优先   | 后台运算（不交互） |
| Parallel Old GC      | 并行      | 老年代        | 标记-整理      | 高吞吐量优先   | 后台运算（不交互） |
| CMS                  | 并发      | 老年代        | 标记-清除      | 高响应速度优先 | 互联网或B/S业务    |
| G1                   | 并行/并发 | 新生代/老年代 | 复制/标记-整理 | 高响应速度优先 | 服务端             |

**怎么选择垃圾回收器**

- Java垃圾收集器的配置对于JVM优化来说是一个很重要的选择，选择合适的垃圾收集器可以让JVM的性能有一个很大的提升。
- 怎么选择垃圾收集器？
  - 1.优先调整堆的大小让JVM自适应完成。
  - 2.如果内存小于100M，使用串行收集器
  - 3.如果是单核、单机程序，并且没有停顿时间的要求，串行收集器
  - 4.如果是多CPU、需要高吞吐量、允许停顿时间超过1秒，选择并行或者JVM自己选择
  - 5.如果是多CPU、追求低停顿时间，需快速响应（比如延迟不能超过1秒，如互联网应用），使用并发收集器
  - 官方推荐G1，性能高。现在互联网的项目，基本都是使用G1。
- 最后需要明确一一个观点：
  - 1.没有最好的收集器，更没有万能的收集；
  - 2.调优永远是针对特定场景、特定需求，不存在一劳永逸的收集器
