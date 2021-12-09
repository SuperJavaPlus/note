

- path环境变量是windows操作系统执行命令时所要搜寻的路径
- 在一个Java源文件中可以声明多个class，但是最多只有一个类声明为public的，而且要求声明为public的类名必须与源文件类名相同
- 定义合法标识符：

​      由26个英文字母大小写，0-9，_或$组成

​      数字不可以开头

​      不可以使用关键字和保留字，但能包含关键字和保留字

​       Java中严格区分大小写，长度无限制

​      标识符不能包含空格

- java名称命名规范


  ​      包名:多单词组成时所有字母都小写:xxxyyyzzz

  ​      类名、接口名:多单词组成时，所有单词的首字母大写: XxxYyyZzz

  ​      变量名、方法名:多单词组成时，第一个单词首字母小写，第二个单词开始每个单词首字母大    写:xxxYyyZzz

- 常量名:所有字母都大写。多单词时每个单词用下划线连接:XXX_ZZZ

- float占用四字节，尾数可以精确到7位有效数字，表示的范围比long大。定义float类型时，变量要以“f”或“F”结尾

- double占八个字节

- 自动类型提升：byte\char\short--》int--》long--》float--》double

- 强制类型转换：需要使用强转符；可能导致精度损失

- 
  
  ```java
  double a1 = 12.6;
          int a2 = (int)a1;
  ```
  
- 整型常量默认类型为int，浮点型常量默认类型为double

- String属于引用数据类型   

- String可以和八种数据类型做运算，且只能做连接运算

- 
  
  ```java
  int num = 10;
  char n = 'a';//97  A=65
  String str = "hello";
          System.out.println(num+n+str);
          System.out.println(n+str+num);
          System.out.println(num+(n+str));
          System.out.println(n+(num+str));
  ```
  
- 运行结果：

  107hello

  ahello10

  10ahello

  a10hello

- 正数的原码，反码，补码都一样

- 负数的反码：是对原码按位取反，只有最高位（符号位）确定为1

- 负数的补码：其反码加1

- 计算机底层都以补码的方式存储数据

- -128的补码：1000 0000 

​       -127的补码：1000 0001

- %：结果的符号与被模数的符号相同

```java
        int m2 = 12;
        int n2 = 5;
        System.out.println(m2%n2);
        int m3 = -12;
        int n3 = 5;
        System.out.println(m3%n3);
        int m4 = 12;
        int n4 = -5;
        System.out.println(m4%n4);
        int m1 = -12;
        int n1 = -5;
        System.out.println(m1%n1);
```

- 结果为：2，-2，2，-2

- 
  
  | a     | b     | a&b   | a&&b(短路与) | a\|b  | a\|\|b | !a（非） | a^b(逻辑异或) |
  | ----- | ----- | ----- | ------------ | ----- | ------ | -------- | ------------- |
  | true  | true  | true  | true         | true  | true   | false    | false         |
  | true  | false | false | false        | true  | true   | false    | true          |
  | false | true  | false | false        | true  | true   | true     | true          |
  | false | false | false | false        | false | false  | true     | false         |
  
- &&符号左边为false时，不会执行符号右边的运算

- ||符号左边为false时，不会执行符号右边的运算

- 位运算符操作的都是整形的数据

- <<在一定范围内，每向左移一位相当于*2

- m =  （m ^ n）^ n

- 使用位运算符交换两个变量的值

```java
        int num1 = 10;
        int num2 = 15;
        num1 = num1 ^ num2;
        num2 = num1 ^ num2;
        num1 = num1 ^ num2;
        System.out.println(num1+","+num2);
```

​             15,10

- 比较三个数的最大值


```java
        int n1 = 10;
        int n2 = 19;
        int n3 = -56;
        int max1 = (n1 > n2)? n1 : n2;
        int max2 = (max1 > n3)? max1 : n3; 
        System.out.println(max2);
```



```java
        int n1 = 10;
        int n2 = 19;
        int n3 = -56;
        int max1 = (n1 > n2)? n1 : ((n2 > n3)? n2 : n3);
        System.out.println("最大值为"+max1);
```

![image-20211007203858687](F:\笔记图片\img\image-20211007203858687.png)

- 流程控制的三种结构：

​       顺序结构  分支结构  循环结构

- 将60转换为16进制


```java
        int i1 = 60;
        int i2 = i1 & 15;
        String j = (i2 > 9)? (char)(i2-10 + 'A')+"": i2+"";
        int i3 = i1 >>> 4;
        i2 = i3 & 15;
        String k = (i2 > 9)? (char)(i2-10 + 'A')+"": i2+"";
        System.out.println(k+j);
```

- [a , b] : (int) (Math.random() * (b - a + 1) + a) 
- switch结构中的表达式只能是 byte，char， short，  int， 枚举类型 ，String类型
- case语句下可以嵌套if语句
- for(初始化条件；循环条件（boolean）；迭代条件      ){


​               循环体

​               }

- 求两个数的最大公约数和最小公倍数

```java
import java.util.Scanner;
class ForTest{
    public staic void main(String args[]){
        Scanner scan = new Scanner(System.in);
        System.out.println("请输入第一个正数");
        int m = scan.nextInt();
        System.out.println(System.in);
        System.out.println("请输入第二个正数");
        int n = scan.nextInt();
        int min = (m < n)? m:n;
        for (int i = min;i >= 1;i--){
            if (m % i == 0 && n % i ==0){
                System.out.println("最大公约数为" + i );
                break;
            }
        }
        int max = (m > n)? m:n;
        for (int j = max; j <= m * n; j++){
            if (j % m ==0 && j % n ==0){
                System.out.println("最小公倍数为" + j);
                break;
            }
        }
        
    }
}  
```

- 三位数的水仙花数

```java
int baiWei = 0;
        int shiWei = 0;
        int geWei = 0;
        for (int i= 100;i < 1000;i++){
            baiWei = i / 100;
            shiWei = i % 100 / 10;
            geWei = i % 100 % 10;
            if (baiWei * baiWei * baiWei + shiWei * shiWei * shiWei + geWei * geWei * geWei == i){
                System.out.println(i);
            }
```

- continue:结束**当次**循环
- break:结束**当前**循环
- 求1-100的质数

```java
        int count = 0;
        label: for (int i = 2;i <= 100;i++ ){
            for (int j = 2;j <=Math.sqrt(i);j++){
                if (i % j == 0){
                    continue label;
                }
            }
            count++;
            System.out.println(i);
        }
        System.out.println(count);
```

- 
  
  ```java
  int[] ids;
  ids = new int[]{1001,1002,1003};//数组的静态初始化：数组的初始化和元素赋值同时进行
  String[] names = new String [5];//数组的动态初始化：数组的初始化与元素的赋值分开进行   
  ```
  
- 二维数组初始化值：

​            **int [][] [] [] arr = new int [4] [5];**

​           外层元素初始化值：地址值

​           内层元素初始化值：与一维数组相同

​          **int [][] [] [] arr = new int [4] [];**

​           外层元素初始化值：null

​          内层元素初始化值：空指针

- 数组的复制     

```java
      int[] arr1 = new int [5] ;

      int[] arr2 = new int [arr1.length];
      for(int i = 0;i < arr1.length;i++){
          arr2[i] = arr1[i];
      }
```

- 冒泡排序

```java
int temp = 0;
for (int i = 1;i < arr.length; i++){
    for (int j = 0;j < arr.length - i;j++){
        if (arr[j] > arr [j + 1]){
            temp = arr[j];
            arr[j] = arr[j + 1];
            arr[j + 1] = temp;
        }
    }
}
```

### 面向对象

- 类里面包括属性（成员变量 ，域，字段）和方法（成员方法），构造器，代码块，内部类
- 创建类的对象 = 类的实例化
- 调用属性 ：对象.属性      调用方法：对象.方法
- 类和对象的使用：  ①  创建类，设计类的成员         

​                                        ②  构建类的对象

​                                        ③  通过 **对象.属性**， **对象.方法**调用对象的结构

- 成员变量（属性）：直接定义在类{}内
- 局部变量：声明在方法内、方法新参、代码块内、构造器形参、构造器内
- 成员变量（属性）在声明时可以使用权限修饰符，局部变量不可以使用权限修饰符
- 局部变量没有默认初始值
- 局部变量加载到栈空间中，属性（**非static**）加载到堆空间中
- 方法的声明： 权限修饰符   返回值类型   方法名 （形参列表）{方法体}
- return关键字的使用：①结束方法

​                                              ②针对有返回值类型的方法，返回所要的数据

- 方法可以调用当前类中的属性或方法，方法中不可以定义方法
- 方法重载：同一个类，同一个方法名

​                           参数列表不同，参数类型不同，参数个数不同

- 
  
  | 修饰符    | 类内部 | 同一个包 | 不同包的子类 | 同一个工程 |
  | --------- | ------ | -------- | ------------ | ---------- |
  | private   | yes    | no       | no           | no         |
  | 缺省      | yes    | yes      | no           | no         |
  | protected | yes    | yes      | yes          | no         |
  | public    | yes    | yes      | yes          | yes        |
  
- 构造器：权限修饰符   类名 （形参列表） {}

- JavaBean: 类是公共的

​                         有一个无参公共构造器

​                         有属性，且有对应的get和set方法

- import:在源文件中显式的使用import结构导入指定包下的**类、接口**
- 如果使用的类或接口时本包下或使用的是java.lang包下的，则可以省略import结构
- 如果在源文件中使用了不同包下的同名的类，则必须至少有一个类以全类名的方式显示

​                               **包名.类名 对象名 = new 包名.类名（）**

- 如果没有显式的声明一个类的话，则此类继承于Java.lang.Object
- 子类重写的方法的权限修饰符不小于父类被重写的方法的权限修饰符
- 子类不能重写父类中申明为private权限的方法
- 父类被重写的方法返回值类型是void，则子类重写的方法返回值类型也是void

​        父类被重写的方法返回值类型是A类，则子类重写的方法返回值类型是A类或A类的子类

- 父类被重写的方法返回值类型是基本数据类型，则子类重写的方法返回值类型必须是相同的基本数据类型
- 子类重写的方法抛出的异常类型不大于父类被重写方法抛出的异常类型
- 子类和父类同名同参数的方法要么都声明为非static（考虑重写），要么都声明为static（不是重写）
- 在子类的方法或构造器中，通过使用**super.属性**或**super.方法**的方式，显式的调用父类声明的属性或方法
- 当子类和父类定义了同名的属性时，在子类中调用父类中声明的属性时，则必须**显式的使用**
- 当子类重写了父类的方法以后，在子类方法中调用父类被重写的方法时，必须显式的使用**super.方法**
- 在类的构造器中，**this（形参列表）**表示调用**本类中重载**的其他构造器，**super（形参列表）**表示调用**父类中**指定的构造器
- 在构造器的首行，没有显式的声明**this（形参列表）或super（形参列表）**，则默认调用的是父类中的空参的构造器**super（）**
- 子类对象实例化的全过程：

​            1.从结果上来看：

​                      子类继承了父类后，就获取了父类声明中的方法或属性

​                       创建子类的对象，在堆空间中就会加载父类中声明的属性

​             2.从过程上看

​                       通过子类的构造器构造子类时，一定会间接或直接调用其父类的构造器，进而调用父类                

​                  的构造器，知道调用了Java.lang.Object类中的空参构造器为止

- 多态的使用前提：类的继承关系；要有方法的重写；

- 对象的多态性只适用于方法，不适用于属性

- 有了对象的多态性以后，内存中实际上是加载了**子类特有的属性和方法**的，但是由于变量声明为父类类型，导致编译时只能调用父类的属性和方法

- **a instanceof  A**:判断对象a是否是类A的实例。如果是返回true

- 当输出一个对象的引用时，实际上就是调用当前对象的toString（）

- 
  
  | 基本数据类型 | 包装类    |
  | ------------ | --------- |
  | byte         | Byte      |
  | short        | Short     |
  | int          | Integer   |
  | long         | Long      |
  | float        | Float     |
  | double       | Double    |
  | boolean      | Boolean   |
  | char         | Character |
  
- **自动装箱与拆箱**

```java
int num = 10;
Integer in1 = num;//自动装箱
int i1 = in1;//自动拆箱
********************************************
boolean b1 = true;
Boolean b2 = b1;//自动装箱
```

- String-->基本数据类型，包装类：调用**包装类的parseXxxx()**

- 基本数据类型，包装类-->String:调用String类型的**valueof()**

- 
  
  ```java
  //Integer内部定义了IntegerCache结构，IntegerCache中定义了Integer[],保存了-128 ~127范围内的整数。如果使用了自动装箱的方式，给Integer赋值的范围在-128~127内时，可以直接使用数组元素，不用再去new。
  Integer m = 1;
  Integer n = 1;
  System.out.println(m == n);//true
  Integer x = 128;
  Ingeger y = 128;
  System.out.println(x == y);//false
  ```
  
- Static可以修饰属性，方法，内部类，代码块

- static修饰的是静态属性，创建了类的多个对象，多个对象共享同一个静态变量。当通过某一个对象修改静态变量时，会导致其他的对象调用此静态变量时是修改过的

- 静态变量随着类的加载而加载，可以通过**类.静态变量**加载

- 静态变量加载早于对象的创建

- 由于类只会加载一次，静态变量在内存中也会存在一份：存在方法去的静态域中

- 静态方法：随着类的加载而加载，可以通过**类.静态方法**调用

- 静态方法中只能调用静态的方法或属性

- 非静态方法中即可调用静态方法和属性，也可调用非静态的方法和属性

- 静态方法中不能使用**this 和 super**关键字

- 代码块用来初始化类或对象，

- 静态代码块

> 内部可以有输出语句
>
> 随着类的加载而执行，而且只执行一次
>
> 作用：初始化类的信息
>
> 如果一个类中定义了多个静态代码块，则按照声明的先后顺序执行
>
> 静态代码块的执行要优于非静态代码块的执行
>
> 静态代码块内只能调用静态属性和方法

- 动态代码块

>内部可以有输出语句
>
>随着对象的创建而执行
>
>每创建一个对象，就执行一次代码块
>
>作用：可以在创建对象时，对对象的属性进行初始化
>
>如果一个类中定义了多个非静态代码块，则按照声明的先后顺序执行
>
>非静态代码块可以调用静态的属性和方法，或非静态的属性和方法

- final用来修饰类

>此类不能被其他类继承

- final用来修饰方法

> 方法不可以被重写

- final用来修饰变量

> 修饰的变量称为是一个**常量**
>
> 显式赋值，代码块中初始化，构造器中初始化
>
> 修饰局部变量：final修饰形参时，表明此形参是个常量

- static final:全局常量
- 单例模式的饿汉式

```java
public class SingletonTest {
    public static void main(String[] args) {
        Bank b1 = Bank.getInstance();
        Bank b2 = Bank.getInstance();
        System.out.println(b1 == b2);
    }
}
class Bank{
    //构造器私有化
    private Bank(){

    }
    //内部创建类的对象
    private static Bank instance = new Bank();
    //提供公共的方法，返回类的对象
    public static Bank getInstance(){
        return instance;
    }
}
```

- 单例模式的懒汉式

```java
public class SingleTest2 {
}
class Order{
    private Order(){

    }
    private static Order instance = null;
    private static Order getInstance(){
        if (instance == null){
            instance = new Order();
        }
        return instance;
    }
}

```

- 属性赋值的位置及先后顺序：**默认初始化-->显式初始化/代码块中初始化-->构造器中初始化-->对象.属性 或 对象.方法初始化**
- **抽象类**

> 此类不能实例化
>
> 抽象类中一定有构造器，便于子类实例化时调用 

- 抽象方法

> 抽象方法只有方法的声明，没有方法体
>
> **包含抽象方法的类一定是一个抽象类**
>
> 若子类重写了父类中的所有抽象方法后，此子类方可实例化。若没有重写，则子类为一个抽象类。

- 匿名子类

```java
Person p = new Person(){      
    /*方法体*/
};
```

- 接口和类是并列的两个结构
- 定义接口的成员

> JDK7之前：只能定义全局常量和方法
>
> ​                     抽象方法
>
> JDK8：除了定义全局常量和抽象方法之外，还可以定义静态方法和默认方法 

- 接口中不能定义构造器，意味着接口不可以实例化
- Java开发中让类实现接口的方式来使用，如果实现类覆盖了接口中所有的抽象方法，则此实现类就可实例化；如果实现类没有覆盖接口中的所有抽象方法，则此实现类仍为抽象类
- 接口与接口之间可以多继承
- 继承实现的**是不是**的关系，接口实现的**能不能**
- 接口中调用的静态方法，只能通过接口来调用
- 如果子类（实现类）继承的父类和实现的接口中声明了同名同参的方法，子类在没有重写此方法的情况下默认调用父类中的同名同参数方法
- 局部内部类

```Java
public class A{
    //成员内部类
    class AA{
        
    }
    public void method(){
         //局部内部类
    class B{
        
    }
    //局部内部类
    {
        class C{
        
       }
    }
    public A(){
        class D{
            
        }
    }
  }
}
```

- 
  
  ```java
  public class InnerClassTest {
      public static void main(String[] args) {
          Animal.Dog dog = new Animal.Dog();  //创建静态成员内部类对象
          dog.eat();
          Animal animal = new Animal();  //创建非静态成员内部类对象
          Animal.Cat cat = animal.new Cat();
          cat.go();
      }
  }
  class Animal{
      static class Dog{
          public void eat(){
              System.out.println("吃骨头");
          }
      }
      class Cat{
          public void go(){
              System.out.println("抓老鼠");
          }
      }
  }
  ```
  
- 在子类（或实现类）的方法中调用父类，接口类被重写的方法

​        **接口名.super.方法名 **  // 调用接口中的默认方法

​        **super.方法名**                //调用的是父类中声明的

#### 



### 异常处理

- try-catch-finally和throws+异常类型

  **try-catch-finally**

```Java
try{
    //可能出现异常的代码
}catch(异常类型1 变量名1){
    //处理异常的方式
}catch(异常类型2 变量名2){
    //处理异常的方式
}
........
    finally{
        //一定会执行的代码
    }
```

- catch中异常类型如果满足子父类关系，子类要声明在父类上面  

- 在try结构中声明的变量出了结构后不能调用

- try-catch-finally处理编译时异常，使得程序在编译时就不再报错，但是运行时仍可能报错

- finally中声明的一定是会被执行的代码，即使catch中又出现了异常，try中有return语句，catch中有return语句等情况

  **throws+异常类型**

- 声明在的声明处，指明此方法执行时可能会抛出的异常类型，一旦方法体执行时，出现异常，仍会在异常代码处生成一个异常类的对象，此对象满足throws后异常类型时，就会被抛出。异常代码的后续代码就不再执行

- 子类重写的方法抛出的异常类型**不大于**父类被重写的方法抛出的异常类型

#### 手动抛出异常

- 关于异常对象的产生：①系统自动生成的异常对象

​                                              ②手动的生成一个异常对象（throw）

####             自定义异常类

- ①自定义的异常类继承于现有的异常类  RuntimeException、Exception

​       ②提供全局常量serialVersionUID

​       ③提供重载的构造器

### 多线程

- 程序是为完成特定的任务，用某种语言编写的一组指令的集合。即指**一段静态的代码**，静态对象

- 进程是程序的一次执行过程，或是正在运行的一个程序，是一个动态的过程。**进程是资源分配的单位**。

- 线程，进程可进一步细化为线程，是一个程序内部的一条执行路径。线程是**调度和执行的单位**。每个线程拥有独立的栈和程序计数器

  > 线程创建

  1. 创建一个继承于Thread类的子类
  2. 重写run（）方法
  3. 创建Thread类子类的对象
  4. 通过此对象调用start（）方法

  > Thread类的有关方法

- start()：启动当前线程，调用当前线程的run()方法

- run()：通常需要重写Thread类中的此方法，将创建的线程要执行的操作声明在此方法中

- currentThread():静态方法，返回执行当前代码的线程

- getName()：返回当前线程的名字

- setName():设置当前线程的名字

- yiled():释放当前CPU的执行权

- join():在线程a中调用线程b的join()方法，线程a进入阻塞状态，直到线程b完全执行以后，线程a才结束阻塞状态

- sleep(long millitime):让当前线程睡眠指定的millitiome毫秒

- isAlive():判断当前线程是否还存活

  > 线程的优先级

- getPriority():获取当前线程的优先级

- setPriority(int p):设置线程的优先级

  > 创建多线程的方式（实现Runnable接口）

  1. 创建一个实现了Runnable接口的类
  2. 实现类去实现Runnable中的抽象方法run()
  3. 创建实现类的对象
  4. 将此对象作为参数传递到Thread的构造器中,创建Thread类的对象
  5. 通过Thread类的对象调用start()

  > 线程的生命周期

- 线程的五种状态：新建，就绪，运行，阻塞，死亡

- 

![image-20210704215455485](C:\Users\dell\AppData\Roaming\Typora\typora-user-images\image-20210704215455485.png)

> 解决线程安全问题

- 同步代码块

  ```java
  synchronized(同步监视器){
        //同步代码块
  }
  //同步监视器：俗称锁，任何一个类的对象都可以充当锁
  ```

- 同步方法

  如果操作共享数据的代码完整的声明在一个方法中，我们不妨将此方法声明为同步的

  - 同步方法仍涉及到同步监视器，只是不需要我们显式的声明
  - 非静态的同步方法，同步监视器是this
  - 静态的同步方法，同步监视器是当前类本身

  > 懒汉式线程安全

- 
  
  ```java
  public class BankTest{
  
  }
  class Bank{
     private Bank(){
         
     }
     private static Bank instance = null;
     public static Bank getInstance(){
         if(instance == null){
             synchronized(Bank.class){
                 if(instance == null){
                     instance = new Bank();
                 }
             }
         }
         return instance;
     }
  }
  ```

  > 线程死锁
  
- 不同的线程分别占用对方需要的同步资源不放弃，都在等待对方放弃自己需要的同步资源，就形成了线程的死锁

  > Lock锁

- 
  
  ```java
  public class LockTest {
      public static void main(String args[]){
          Windows windows = new Windows();
          Thread thread1 = new Thread(windows);
          Thread thread2 = new Thread(windows);
          Thread thread3 = new Thread(windows);
          thread1.setName("窗口1");
          thread2.setName("窗口2");
          thread3.setName("窗口3");
          thread1.start();
          thread2.start();
          thread3.start();
      }
  }
  class Windows implements Runnable{
      private int tickets = 100;
      //实例化ReentranLock
      private ReentrantLock lock = new ReentrantLock();
      @Override
      public void run() {
          while (true){
              try{
                  //调用锁定的方法
                  lock.lock();
                  if (tickets > 0){
                      try {
                          Thread.sleep(100);
                      } catch (InterruptedException e) {
                          e.printStackTrace();
                      }
                      System.out.println(Thread.currentThread().getName() + "卖票，票号为" + tickets);
                      tickets--;
                  }else {
                      break;
                  }
              }finally {
                  //调用解锁方法
                  lock.unlock();
              }
          }
      }
  }
  ```
  
- synchronized与Lock的不同

  1. synchronized机制在执行完相应的同步代码块以后自动的释放同步监视器
  2. Lock是一个接口，synchronized是一个Java关键字
  3. synchronized在发生异常时，会自动释放线程所占有的锁，因此不会有死锁现象发生；而Lock发生异常时，如果没有主动通过unlock去释放解锁，则很容易造成死锁现象，因此使用Lock时需要在finally中释放锁
  4. Lock锁需要手动的启动同步（lock()），同时结束也需要手动的实现（unlock()）
  5. Lock可以让等待锁的线程响应中断，synchronized却不行，使用synchronized时，等待的线程会一直等待下去，不能够响应中断

- wait():一旦执行此方法，当前线程机会进入阻塞状态，并释放同步监视器
- notify():一旦执行此方法，就会唤醒被wait()的线程，如果有多个线程被wait，就唤醒优先级高的
- notifyALL():一旦执行此方法，就会唤醒所有的被wait的线程
- wait(),notify(),notifyALL()三个方法的调用者必须是同步代码块中或同步方法中的同步监视器

> sleep()与wait():

- **相同点**：一旦执行此方法，都可以使得当前的线程进入阻塞状态

- **不同点**：① 两个方法声明的位置不同：Thread类中声明sleep()，Object类中声明wait()

  ​               ② 调用要求不同：sleep()可以在任何场景下调用，wait()必须是在同步代码块中或同步方       中调用

  ​               ③如果两个方法都是用在同步代码块或同步方法中，sleep()不会释放同步监视器，而wait()会释放同步监视器

#### JDK5.0新增线程创建方式

> 实现Callable接口

1. 创建一个实现Callable的实现类
2. 实现call()方法，将此线程需要执行的操作声明在call()方法中
3. 创建Callable接口实现类的对象
4. 将此实现类对象作为参数传递到FuthreTask构造器中，创建FuthreTask的对象
5. 将FuthreTask的对象作为参数传递到Thread类的构造器中，创建Thread对象，并调用start()

```java
import java.util.concurrent.Callable;
import java.util.concurrent.ExecutionException;
import java.util.concurrent.FutureTask;

public class ThreadNew {
    public static void main(String[] args) {
        NumThread numThread = new NumThread();
        FutureTask futureTask = new FutureTask(numThread);
        new Thread(futureTask).start();
        try {
            Object sum = futureTask.get();//get()返回值即为FutherTask构造器参数Callable实现类重写的call（）的返回值
            System.out.println(sum);
        } catch (InterruptedException e) {
            e.printStackTrace();
        } catch (ExecutionException e) {
            e.printStackTrace();
        }
    }
}
class NumThread implements Callable{
    @Override
    public Object call() throws Exception {
        int sum = 0;
        for (int i = 1;i <= 100; i++){
            if (i % 2 == 0){
                System.out.println(i);
                sum += i;
            }
        }
        return sum;
    }
}
```

> 使用线程池

1. 提供指定线程数量的线程池
2. 执行指定的线程的操作，需要提供实现Ruunable接口或Callable接口实现类的对象
3. 关闭连接池

```Java
import java.util.concurrent.ExecutorService;
import java.util.concurrent.Executors;

public class ThreadPoor {
    public static void main(String[] args) {
        ExecutorService service= Executors.newFixedThreadPool(10);
        service.execute(new NumberThread());
//        service.submit()
    }
}
class NumberThread implements Runnable{
    @Override
    public void run() {
        for (int i = 0;i <= 100;i++){
            if (i % 2 ==0){
                System.out.println(i);
            }
        }
    }
}
```

### 常用类

#### String

- String声明为final的，不可被继承
- String实现了Serializable接口：表示字符串是支持序列化的

​                  实现了Comparable接口，表示String可比较大小

- 当对现有的字符串进行连接操作时，需要重新指定内存区域值，不能使用原有的value进行赋值

- 当对字符串重新赋值时，需要重写指定内存区域赋值，不能是同原有的value进行赋值

- 常量与常量的拼接结果在常量池中，且常量池不会存在相同内容的常量

- 只要其中有一个是变量，结果就在堆中

- charAt():返回索引处的字符

- isEmpty():判断是否为空字符串

- toLowerCase():使用默认语言环境，将String所有的字符转换为小写

- toUpperCase():使用默认语言环境，将String所有的字符转换为大写

- trim():忽略前导空白和尾部空白

- equalsIgnoreCase(String anotherString):忽略大小写的情况下比较内容

- caopareTo(String anotherString):比较两个字符串的大小               //**涉及到字符串排序**

- substring(int beginIndex):返回一个新的字符串，它是此字符串从beginIndex开始截取

- boolean endWith(String suffix):测试此字符串是否以指定的后缀结束

- boolean startWith(String prefix):测定字符串是否以指定的前缀开始

- boolean contains(CharSequence s):当且仅当此字符串包含指定的char值序列时，返回true

- int indexOf(String str):返回指定子字符串在此字符串中第一次出现处的索引

- int indexOf(String str,int fromIndex):返回指定子字符串在此字符串中第一次出现的索引

- Sting replace(char oldChar,char newChar):返回一个新的字符串

- String ---->char[]:调用String的toCharArray()

  ```java
   public void test3(){
          String str1 = "abc123";
          char[] charArray = str1.toCharArray();
          for (int i = 0; i < charArray.length; i++){
              System.out.println(charArray[i]);
          }
  ```

- char[]------>String:调用String构造器

  ```java
  char[] arr = new char[]{'a','b','c','1','2','3'};
          String s = new String(arr);
          System.out.println(s);
  ```

- String---->byte[]:调用String的getBytes()

- byte[]---->String:调用String的构造器

#### StringBuffer与StringBuilder

- String:不可变t序列，底层使用char[]存储

​       StringBuffer：可变序列，线程安全，效率低，底层使用char[]存储

​       StringBuilder：可变序列，线程不安全，效率高，底层使用char[]存储

- 扩容问题：如果要添加的数据底层数组盛不下了，那就需要扩容底层的数组。默认情况下，扩容为原来容量的2倍+2，同时将原有数组中的元素复制到新的数组中
- StringBuffer append(xxxx):用于字符串拼接
- StringBuffer delete(int start,int end)():删除指定位置的内容
- public char CharAt(int n,char c):修改指定位置的字符串
- StringBuffer replace(int start,int end,String str):把[start,end)位置替换为str
- StringBuffer insert(int offset,xxx):在指定位置插入xxx
- StringBuffer reverse():把当前字符序列逆转
- public String substring(int start,int end):返回一个从start开始到end索引结束的左闭右开区间的子字符串
- 判断一串字符串在另一串字符串中出现的次数

```java
  public int getCount(String mainStr,String subStr){
        int index;
        int count = 0;
        int mainLength = mainStr.length();
        int subLength = subStr.length();
        if (mainLength > subLength){
            while ((index = mainStr.indexOf(subStr,index) )!= -1){
                count++;
                index += subLength;

            }
            return count;
        }else {
            return 0;
        }

    }
```

####    SimpleDateFormat

```java
    public void test2() throws ParseException {
    Date date = new Date();
    SimpleDateFormat s = new SimpleDateFormat("yyyy-MM-dd hh:mm:ss");
    String s1 = s.format(date);
    System.out.println(s1);
    System.out.println(s.parse(s1));
}
```

- 字符串"2020-09-08"转换为java.sql.Date

  ```java
   public void test3() throws ParseException {
          String date = "2020-09-08";
          SimpleDateFormat simpleDateFormat = new SimpleDateFormat("yyyy-MM-dd");
          Date date1 = simpleDateFormat.parse(date);
          System.out.println(date1);
          java.sql.Date date2 = new java.sql.Date(date1.getTime());
          System.out.println(date2);
      }
  ```

#### DateTimeFormatter

```java
public void test7(){
        DateTimeFormatter dateTimeFormatter = DateTimeFormatter.ofPattern("yyyy-MM-dd hh:mm:ss");
        String format = dateTimeFormatter.format(LocalDateTime.now());
        System.out.println(format);
    }
```



#### comparable

- String,包装类等实现了Comparable接口，重写了CompareTo()方法
- 重写CompareTo()方法规则：如果当前对象this大于形参对象obj，则返回正整数

​                                                          如果当前对象this小于形参对象obj，则返回负整数

​                                                          如果当前对象this等于形参对象obj，则返回零

- 对自定义类来说，如果需要排序，我们可以让自定义类实现Comparable接口，重写CompareTo()方法

#### comparator

- 当元素的类型没有实现java.lang.Comparable接口而又不方便修改代码     或者实现了java.lang.Comparable接口的排序规则不适合当前的操作，那么可以考虑使用Comparator的对象来排序

- 重写compare(Object obj1,Object obj2)方法

- 
  
  ```java
      public void test2(){
          String arr[] = new String[]{"AA","BB","CC","MM","DD","JJ","FF"};
          Arrays.sort(arr, new Comparator() {
              @Override
              public int compare(Object o1, Object o2) {
                  if (o1 instanceof String && o2 instanceof String){
                      String s1 = (String) o1;
                      String s2 = (String) o2;
                      return -s1.compareTo(s2);
                  }
                  throw new RuntimeException("数据有误");
              }
          });
          System.out.println(Arrays.toString(arr));
      }
  ```


### 枚举类与注解

- #### 自定义的枚举类

```java
class Season{
    //声明Season对象的属性
    private final String seasonName;
    private final String seasonDesc;
    //私有化构造器
    private Season(String seasonName,String seasonDesc){
        this.seasonName = seasonName;
        this.seasonDesc = seasonDesc;
    }
    //提供当前枚举类的多个对象
    public static final Season SPRING = new Season("春天","春暖花开");
    public static final Season SUMMER = new Season("夏天","夏日炎炎");
    public static final Season AUTUMN = new Season("秋天","秋高气爽");
    public static final Season WINTER = new Season("冬天","冰天雪地");

    @Override
    public String toString() {
        return "Season{" +
                "seasonName='" + seasonName + '\'' +
                ", seasonDesc='" + seasonDesc + '\'' +
                '}';
    }
}
```

- #### 使用enum

```java
enum Season1{
    SPRING("春天","春暖花开"),
    SUMMER("夏天","夏日炎炎"),
    AUTUMN("秋天","秋高气爽"),
    WINTER("冬天","冰天雪地");


    private final String seasonName;
    private final String seasonDesc;

    private Season1(String seasonName,String seasonDesc){
        this.seasonName = seasonName;
        this.seasonDesc = seasonDesc;
    }
 }
```

- #### Enum类中的常用方法

  values():返回枚举类型的对象数组。该方法可以很方便的便利所有的枚举值

  valueOf(String str):可以把一个字符串转为对应的枚举类对象。要求字符串必须是枚举类对象的名字

  toString():返回当前枚举类对象常量的名称

- #### 注解

  元注解：★Retention:指定所修饰的Annotation的生命周期

  ​				★Target:指明被修饰的注解能用于修饰哪些程序元素

  ​				Douumented:表示所修饰的注解在被javadoc解析时，保留下来

  ​				Inherited:被它修饰的注解将具有继承性

### 集合

- #### collection接口：单列集合

  向Colllection接口的实现类对象中添加obj数据时，要求obj所在类要重写equals()

  | 方法名                        | 作用                                           |
  | ----------------------------- | ---------------------------------------------- |
  | add(Object e)                 | 将元素e添加到集合中去                          |
  | size()                        | 获取添加元素的个数                             |
  | addAll(Collection coll)       | 将coll中的元素添加到当前的集合中               |
  | isEmpty()                     | 判断当前集合是否为空                           |
  | clear()                       | 清空集合元素                                   |
  | contains(Object obj)          | 判断当前集合中是否包含obj                      |
  | containsAll(Collection coll)  | 判断形参coll中所有的元素是否都存在于当前集合中 |
  | remove(Object obj)            | 从当前集合移除obj元素                          |
  | removeAll(Collection coll)    | 从当前集合中移除coll中的元素                   |
  | retainAll(Collection coll)    | 获取当前集合和coll集合的交集，并返回给当前集合 |
  | equals(Object obj)            | 判断当前集合与形参集合是否相同                 |
  | toArray()                     | 集合----->数组                                 |
  | iterator()：next()  hasNext() | 返回Inerator()接口的实例，用于遍历集合元素     |

  ##### 遍历Collection集合

  ```java
  public void test1(){
      Collection coll = new ArrayList();
      coll.add(123);
      coll.add(456);
      coll.add(new String("aaa"));
      coll.add(123);
      Iterator iterator = coll.iterator();
      while (iterator.hasNext()){
          System.out.println(iterator.next());
      }
  
  }
  
  ```

  

  ```java
  public void test2(){
      Collection coll = new ArrayList();
      coll.add(123);
      coll.add(456);
      coll.add(new String("aaa"));
      coll.add(123);
      //for(集合中元素的类型  局部变量 ：集合对象)
      for (Object obj:coll){
          System.out.println(obj);
      }
  }
  ```

  

  ★ 集合对象每次调用iterator()都会得到一个全新的迭代器对象，默认的游标都在第一个元素之前

  - list接口：存储有序可重复的数据

    **ArrayList,LinkedList,Vector的共同点**：三个类都实现了List接口，存储数据的特点相同，存储有序的可重复的数据

    **ArrayList,LinkedList,Vector的不同同点**：

    > ArrayList:作为List接口的主要实现类，线程不安全，效率高，底层使用Object[]存储
    >
    > LinkedList:对于频繁的插入和删除操作使用此效率比较高，底层使用的双向链表存储
    >
    > Vector:作为List接口的古老实现类，线程安全，效率低，底层使用Object[]存储
    
  - set接口：存储无序不可重复的数据

    存放在Set容器中的对象，一定要重写equals()和hashCode()方法

     **HashSet**:作为Set接口的主要实现类；线程是不安全的，可以存储null值。底层是 **数组+链表**

    - 无序性：存储的数据在底层数组中并非按照数组索引的顺序添加，而是根据数据的哈希值决定的
    - 不可重复性：保证添加的元素按equals()判断时，不能返回true，即相同的元素只能添加一个
    - **向HashSet中添加元素**：
      1. 调用元素a所在类的hashCode()方法，计算元素a的哈希值
      2. 此哈希值接着通过某种算法计算出在HashSet底层数组中的存放位置（即为相应的索引位置）
      3. 判断数组此位置上是否已经有元素，如果此位置上没有其他元素，则元素a添加成功；如果此位置上有其他元素b(或以链表形式存在多个元素)，则比较元素a与元素b的哈希值，如果哈希值不相同则元素a添加成功；如果哈希值相同，进而需要调用元素a的equals()方法，equals()返回true,则添加元素a失败。
  
    

     **linkedHashSet**:作为HashSet的子类，遍历其内部数据时，可以按照添加的顺序遍历

     **TreeSet**:可以按照添加的对象指定属性，进行排序

    - 向TreeSet当中添加的数据，要求是同一个类的对象

    - 自然排序中比较两个对象是否相同的标准为compareTo()返回0.

    - treeSet定制排序比较两个对象是否相同标准为compare()返回0.

      ```java
       public void test2(){
              Comparator comparator = new Comparator() {
                  @Override
                  public int compare(Object o1, Object o2) {
                     if (o1 instanceof Person && o2 instanceof Person){
                         Person p1 = (Person) o1;
                         Person p2 = (Person) o2;
                         return Integer.compare(p1.getAge(),p2.getAge());
                     }else {
                         throw new RuntimeException("输入数据有误");
                     }
                  }
              };
              TreeSet set = new TreeSet(comparator);
              set.add(new Person("Tom",12));
              set.add(new Person("Jerry",25));
              set.add(new Person("Jim",35));
              set.add(new Person("Mike",9));
              set.add(new Person("Jack",33));
              set.add(new Person("Jack",56));
              Iterator iterator = set.iterator();
              while (iterator.hasNext()){
                  System.out.println(iterator.next());
              }
          }
      ```
  
  ```java
  public void test3(){
      List list = new ArrayList();
      list.add(new Integer(5));
      list.add(new Integer(1));
      list.add(new Integer(3));
      list.add(new Integer(3));
      list.add(new Integer(6));
      list.add(new Integer(8));
      List list1 = 去除重复数据(list);
      for (Object i : list1){
          System.out.println(i);
      }
  }
  ```
  
- #### map接口:双列集合，用来存储一对数据

  Map中的key是无序的不可重复的，使用Set存储所有的key

  ​                value是无序的可重复的，使用Collection存储所有的value

  一个键值对：key-value构成了一个Entry对象，Entry是无序的不可重复的，使用Set存储entry对象

  | get(Object key)                     | 获取指定key对应的value            |
  | ----------------------------------- | --------------------------------- |
  | boolean containsKey(Object key)     | 是否包含指定的key                 |
  | boolean containsValue(Object value) | 是否包含指定的value               |
  | int size()                          | 返回map中key-value的个数          |
  | boolean equals(Object obj)          | 判断当前map和参数对象obj是否相等  |
  | Set keySet()                        | 返回所有key构成的Set集合          |
  | Collection values()                 | 返回所有value构成的Collection集合 |
  | Set entrySet()                      | 返回所有key-value构成的Set集合    |
  |                                     |                                   |

  

- ##### HashMap:作为Map的主要实现类，线程不安全，效率高；可以存储null的key和value

  HashMap底层：数组+链表+红黑树

  HashMap底层实现原理：

  **JDK7**:  HashMap map = new HashMap();在实例化以后，底层创建了长度是16的一堆数组Entry[] table

  ​            map.put(key1,value1)：

  ​             首先调用key1所在类的HashCode()计算key1的哈希值，此哈希值经过某种算法以后，得到在        Entry数组中存放的位置；如果此位置上的数据为空，此时key1-value1添加成功；

  ​              如果此位置上的数据不为空，则比较此位置上一个或多个数据与key1的哈希值：如果key1的哈希值与已经存放的数据的哈希值不相同，此时key1-value1添加成功；如果key1的哈希值与已经存放的数据的哈希值相同，则调用key1所在类的equals(key2)，如果返回为false，此时key1-value1添加成功；如果返回为true，使用value1替换value2.

  **JDK8**相较于jdk7在底层实现方面的不同：

  ​      new HashMap：底层没有创建一个长度为16的数组

  ​      jdk8底层数组为Node[]

  ​      首次调用put方法时，底层创建长度为16的数组

  ​      当数组的某一个索引位置上的元素以链表形式存在的**数据个数>8且当前数组的长度 >64时**，此时此索引位置上的所有数据改为使用红黑树存储

  **table**:存储元素的数组，总是2的n次幂

  **entrySet**:存储具体元素的值

  **size**:HashMap中存储键值对的数量

  **modCount**:HashMap狂容和结构改变的次数

  **threshold**:扩容临界值: = 容量 * 填充因子：16 * 0.75

  **TREEIFY_THRESHOLD**:Bucked中链表长度大于该默认值，转化为红黑树：8

  **MIN_TREEIFY_CAPACITY**:桶中的Node被树化时最小的hash容量：64

  **DEFAULT_LOAD_FACTORY**:HashMap的默认加载因子：0.75

  **DEFAULT_INITIAL_CAPACITY**:HashMap的默认容量：16

  - ###### LinkedHashMap 

    - 保证遍历map元素时，可以按照添加的顺序实现遍历。原因：在原有的HashMap底层结构基础上，添加了一对指针，指向前一个和后一个元素
    - 对于频繁的遍历操作，LinkedHashMap执行效率高于HashMap

- ##### TreeMap

  - 保证按照添加的key-value对进行排序，实现排序遍历，此时考虑key的自然排序或定制排序
  - 向TreeMap当中添加的数据，要求是同一个类的对象
  - 底层使用的红黑树

- ##### HashTable:作为古老的实现类，线程安全，效率低；不可以存储null的key和value

  - ###### Properties

    - 常用来处理配置文件。key-value都是String类型

- #### collections工具类常用方法

| 方法                             | 作用                                             |
| -------------------------------- | ------------------------------------------------ |
| reverse(List)                    | 反转List中元素的排序                             |
| shuffle(List)                    | 对list集合元素进行随机排序                       |
| sort(List)/sort(List,Comparator) | 根据元素的自然顺序对指定的List集合元素按升序排序 |
| swap(List,int , int )            | 交换List中i 和 j 处的元素                        |
| int frequency(Collection object) | 返回指定集合中元素出现的个数                     |
| copy(List dest,List src)         | 将src的内容复制到dest中                          |
| synchronized Xxx()               | 解决线程安全                                     |
|                                  |                                                  |
|                                  |                                                  |

```java

/*
copy()方法的使用
*/

public void test1(){
    List list = new ArrayList();
    list.add(123);
    list.add(456);
    list.add(789);
    list.add(789);
    list.add(789);


    List dest = Arrays.asList(new Object[list.size()]);
    Collections.copy(dest,list);
    System.out.println(dest);
}
```

- ### 泛型

  泛型的类型必须是类，不能是基本数据类型。需要用到基本数据类型的位置，那包装类来替换

  如果实例化时没有使用泛型，默认类型为Java.lang.Object

  泛型可能有多个参数

  泛型不同的引用不能相互赋值

  泛型方法：在方法中出现了泛型的结构，泛型参数与类的泛型参数没有任何关系。可以声明为静态方法，原因：泛型参数实在调用方法时确定的，并非是在类实例化时确定的

```java
public <E> List<E> copyMessage(E[] arr){
    ArrayList<E> arrayList = new ArrayList<>();
    for (E e : arr){
        arrayList.add(e);
    }
    return arrayList;
}
```

​     类A时类B的父类，但G<A>与G<B>不具备子父类关系，二者是并列关系,二者共同的父类是G<?>

- ### IO流

  #### file：声明在java.io包下

  file的一个对象，代表一个文件或一个文件目录（文件夹）

  相对路径：相较于某个路径下，指明的路径

  绝对路径：包含盘符在内的文件或文件目录的路径

  ```java
  public void test1(){
      File file = new File("hello.txt");//方式一
      System.out.println(file);
      File file1 = new File("D:\\workspace","java");//方式二
      System.out.println(file1);
      File file2 = new File(file1,"he.txt");//方式三
      System.out.println(file2);
  }
  ```

  | 方法名                          | 作用                                           |
  | ------------------------------- | ---------------------------------------------- |
  | public String getAbsolutePath() | 获取绝对路径                                   |
  | public String  getPath()        | 获取路径                                       |
  | public String  getName()        | 获取名称                                       |
  | public String  getParent()      | 获取上层文件目录路径，若无，则返回null         |
  | public long length()            | 获取文件长度（字节数）。不能获取目录长度       |
  | public long  lastModified()     | 获取最后一次修改的时间，毫秒值                 |
  | public String[] list()          | 获取指定目录下的所有文件或者文件目录的名称数组 |
  | public File[] listFiles()       | 获取指定目录下的所有文件或者文件目录的File数组 |
  | public boolean isDiretory()     | 判断是否是文件目录                             |
  | public boolean isFile()         | 判断是否是文件                                 |
  | public boolean exists()         | 判断是否存在                                   |
  | public boolean createNewFile()  | 创建文件。若文件存在则不创建，返回false        |
  | public boolean delete()         | 删除文件或文件夹                               |
  | public boolean  mkdir()         | 创建文件目录，如果此文件目录存在，就不创建     |
  | public boolean  mkdirs()        | 创建文件目录，如果上层文件目录不存在，一并创建 |

  #### 流的体系结构
  
  | 抽象基类     | 节点流                                         | 缓冲流               |
  | ------------ | ---------------------------------------------- | -------------------- |
  | InputStream  | FileInputStream(read byte [] buffer)           | BufferedInputStream  |
  | OutputStream | FileOutputStream(write (byte [] buffer,0,len)) | BufferedOutputStream |
  | Reader       | FileReader(read char [] buffer)                | BufferedReader       |
  | Writer       | FileWriter(write (char [] buffer,0,len))       | BufferedWriter       |
  
  1. File类的实例化
  2. 流的实例化
  3. 读入的操作
  4. 资源的关闭
  
  read():返回读入的一个字符，如果达到文件末尾，则返回-1
  
  **读入数据**
  
  ```java
   @Test
      public void test1() {
          FileReader fileReader = null;
          try {
              File file = new File("hello.txt");
              boolean newFile = file.createNewFile();
              fileReader = new FileReader(file);
              int read = fileReader.read();
              while (read != -1){
                  System.out.print((char) read);
                  read = fileReader.read();
              }
  
          }catch (IOException e){
              e.printStackTrace();
          }finally {
  
              try {
                  fileReader.close();
              } catch (IOException e) {
                  e.printStackTrace();
              } 
          }
      }
      @Test
      public void test2() {
          FileReader fileReader = null;
          try {
              File file1 = new File("hello.txt");
              fileReader = new FileReader(file1);
              char[] cbuf = new char[3];
              int len;
              while ((len = fileReader.read(cbuf)) != -1){
                  for (int i = 0;i < len;i++){
                      System.out.print(cbuf[i]);
                  }
                  System.out.println();
              }
          } catch (IOException e) {
              e.printStackTrace();
          } finally {
               try {
              fileReader.close();
          } catch (IOException e) {
              e.printStackTrace();
          }
          }
         
      }
  ```
  
  **写出数据**

```java
 public void test() {
        FileWriter fileWriter = null;
        try {
            //提供File类的对象，指明写出的文件
            File file = new File("hello.txt");
            //提供FileWriter的对象，用于数据的写出
            fileWriter = new FileWriter(file);
            //写出的操作
            fileWriter.write("i have a dream");
            //关闭流
        } catch (IOException e) {
            e.printStackTrace();
        } finally {
            try {
                fileWriter.close();
            } catch (IOException e) {
                e.printStackTrace();
            }
        }
```

**读入写出**

```java
public void FileWriterFileReader() throws IOException{
    FileReader fileReader = null;
    FileWriter fileWriter = null;
    try {
        File srcFile = new File("hello.txt");
        File destFile = new File("hello1.txt");
        fileReader = new FileReader(srcFile);
        fileWriter = new FileWriter(destFile);
        char[] cbuf = new char[5];
        int len;
        while ((len = fileReader.read(cbuf)) != -1){

            fileWriter.write(cbuf,0,len);
            }
    } catch (IOException e) {
        e.printStackTrace();
    } finally {
        try {
            fileWriter.close();
        } catch (IOException e) {
            e.printStackTrace();
        }
        try {
            fileReader.close();
        } catch (IOException e) {
            e.printStackTrace();
        }
    }
}
```

对于文本文件（.txt,.java,.c），使用字符流处理；对于非文本文件使用字节流处理。

- #### 缓冲流

  1. 造文件
  2. 造节点流
  3. 造缓冲流
  4. 读取，写入
  5. 资源关闭（先关闭外层的流，再关闭内层的流）

```java
public void test() throws IOException {
    BufferedReader bufferedReader = null;
    BufferedWriter bufferedWriter = null;
    try {
        File srcFile = new File("hello.txt");
        File destFile = new File("hello2.txt");
        FileReader fileReader = new FileReader(srcFile);
        FileWriter fileWriter = new FileWriter(destFile);
        bufferedReader = new BufferedReader(fileReader);
        bufferedWriter = new BufferedWriter(fileWriter);
        char[] chars = new char[5];
        int len;
        while ((len = bufferedReader.read(chars)) != -1){
            bufferedWriter.write(chars,0,len);
        }
    } catch (IOException e) {
        e.printStackTrace();
    } finally {
        try {
            bufferedReader.close();
        } catch (IOException e) {
            e.printStackTrace();
        }
        try {
            bufferedWriter.close();
        } catch (IOException e) {
            e.printStackTrace();
        }
    }
}
```

InputStreamReader:将一个字节的输入流转化为字符的输入流

OutputStreamWriter:将一个字符的输入流转化为字节的输出流

### 反射

反射的特征：动态性

关于java.lang.Class:

- 类的加载过程：
  - 程序经过javac.exe命令以后，会生成一个或多个字节码文件（.class）结尾。接着我们使用java.exe命令对某个字节码文件进行解释运行。相当于将某个字节码文件加载到内存中。此过程称为类的加载。加载到内存中的类，我们就称为运行时类，此运行时类，就作为Class的一个实例
  - Class的实例对应着一个运行时类
  - 加载到内存中的运行时类，会缓存一定的时间。在此时间之内，我们可以通过不同的方式获取此运行时类。

- #### 创建运行时的类

  ```java
  //调用运行时类的属性
  Class clazz1 = Person.class;
  System.out.println(clazz1);
  //通过运行时类的对象
  Person person = new Person();
  Class clazz2 = person.getClass();
  //调用Class的静态方法：forName(String classpath)
  Class clazz3 = Class.forName("_10._反射.reflection");
  ```

- 

  反射的特征：动态性

  关于java.lang.Class:

  - 类的加载过程：

    - 程序经过javac.exe命令以后，会生成一个或多个字节码文件（.class）结尾。接着我们使用java.exe命令对某个字节码文件进行解释运行。相当于将某个字节码文件加载到内存中。此过程称为类的加载。加载到内存中的类，我们就称为运行时类，此运行时类，就作为Class的一个实例
    - Class的实例对应着一个运行时类
    - 加载到内存中的运行时类，会缓存一定的时间。在此时间之内，我们可以通过不同的方式获取此运行时类。

    ```java
    //调用运行时类的属性
    Class clazz1 = Person.class;
    System.out.println(clazz1);
    //通过运行时类的对象
    Person person = new Person();
    Class clazz2 = person.getClass();
    //调用Class的静态方法：forName(String classpath)
    Class clazz3 = Class.forName("_10._反射.reflection");
    ```

| 方法名                    | 作用                                                         |
| ------------------------- | ------------------------------------------------------------ |
| newInstance()             | 调用此方法，创建对应的运行时类的对象。内部调用了运行时类的空参构造器 |
| getFileds()               | 获取当前运行时类及其父类中声明为public访问权限的属性         |
| getDeclaredFields()       | 获取当前运行时类当中声明的所有属性（不包含父类中的属性）     |
| getModifiers()            | 得到权限修饰符                                               |
| getType()                 | 得到数据类型                                                 |
| getName()                 | 得到变量名                                                   |
| getMethods()              | 获取当前运行时类及其所有父类中声明为public权限的方法         |
| getDeclaredMethods()      | 获取当前运行时类当中声明的所有方法                           |
| getAnnotations()          | 获取方法声明的注解                                           |
| getConstructors()         | 获取当前运行时类中声明为public的构造器                       |
| getDeclaredConstructors() | 获取当前运行时类中所有的构造器                               |
| getGenericSuperclass()    | 获取运行时类父类的泛型                                       |
|                           |                                                              |

- #### 调用运行时类的指定结构

- ##### 调用属性

  ```java
  public void test2() throws Exception {
      //1.创建运行时类的对象
      Class clazz = Person.class;
      Person person = (Person) clazz.newInstance();
      // 2.获取指定的属性:getDeclaredField():获取运行时类的指定变量名的属性
      Field name = clazz.getDeclaredField("name");
      //3.保证当前属性是可访问的
      name.setAccessible(true);
      //4.设置对象的此属性值
      name.set(person,"Tom");
      System.out.println(name.get(person));
  
  
  }
  ```

- ##### 调用方法

  ```java
   public void test3() throws Exception {
          //创建运行时类的对象
          Class clazz = Person.class;
          Person person = (Person) clazz.newInstance();
          //1.获取某个指定的方法
          //getDeclaredMethod()  参数一：指明获取方法的名称   参数二：指明获取方法的形参列表
          Method show = clazz.getDeclaredMethod("show", String.class);
          //保证当前方法是可访问的
          show.setAccessible(true);
          //2.invoke()   参数一：方法的调用者   参数二：给方法形参赋值的实参，invoke方法的返回值即为对应类中调用的调用方法的返回值
          show.invoke(person,"CHN");
  
      }
  ```

- ##### 调用运行时类的指定的构造器

  ```java
  public void test4() throws Exception {
          Class clazz = Person.class;
          Person person =(Person) clazz.newInstance();
          //获取指定的构造器getDeclaredConstructor（）  参数：指明构造器的形参列表
          Constructor declaredConstructor = clazz.getDeclaredConstructor(String.class,int.class);
  
          //保证此构造器是可访问的
          declaredConstructor.setAccessible(true);
  
          //调用此构造器创建运行时类的对象
          Person person1 = (Person) declaredConstructor.newInstance("Jerry",22);
          System.out.println(person1);
  
      }
  ```

  

### 网络编程

TCP

客户端：

1. 连接服务器
2. 发送消息

服务器：

1. 建立服务的端口
2. 等待用户的连接accept
3. 接受用户的消息

```java
public class TCPClient {
    public static void main(String[] args) {
        Socket socket = null;
        OutputStream outputStream = null;
        try {
            InetAddress IP = InetAddress.getByName("127.0.0.1");
            socket = new Socket(IP,8899);

            outputStream = socket.getOutputStream();

            outputStream.write("你好".getBytes());
        } catch (Exception e) {
            e.printStackTrace();
        }finally {
            if (outputStream != null){
                try {
                    outputStream.close();
                } catch (IOException e) {
                    e.printStackTrace();
                }
            }
            if (socket != null){
                try {
                    socket.close();
                } catch (IOException e) {
                    e.printStackTrace();
                }
            }

        }
    }
}
```

```java
public class TCPServer {
    public static void main(String[] args) {
        ServerSocket serverSocket = null;
        Socket accept = null;
        InputStream inputStream = null;
        ByteArrayOutputStream baos = null;
        try {
            serverSocket = new ServerSocket(8899);
            accept = serverSocket.accept();

            inputStream = accept.getInputStream();

            baos = new ByteArrayOutputStream();
            byte[] buffer = new byte[1024];
            int len;
            while ((len = inputStream.read(buffer)) != -1){
                baos.write(buffer,0,len);
            }

            System.out.println(baos.toString());


        } catch (IOException e) {
            e.printStackTrace();
        }finally {
            if (baos != null){
                try {
                    baos.close();
                } catch (IOException e) {
                    e.printStackTrace();
                }
            }

            if (inputStream != null){
                try {
                    inputStream.close();
                } catch (IOException e) {
                    e.printStackTrace();
                }
            }

            if (accept != null){
                try {
                    accept.close();
                } catch (IOException e) {
                    e.printStackTrace();
                }
            }

            if (serverSocket != null){
                try {
                    serverSocket.close();
                } catch (IOException e) {
                    e.printStackTrace();
                }
            }

        }
    }
}
```



UDP

```java
public class UDPClientDemo {
    public static void main(String[] args) throws Exception {
        //1.建立一个socket
        DatagramSocket datagramSocket = new DatagramSocket();
        
        //2.建一个包
        String msg = "你好啊";
        InetAddress inetAddress = InetAddress.getByName("localhost");
        
        //数据  数据长度  发送给谁  端口号
        DatagramPacket packet = new DatagramPacket(msg.getBytes(), 0, msg.getBytes().length, inetAddress, 9090);

        //3.发送包
        datagramSocket.send(packet);
        
        
        //关闭流
        datagramSocket.close();
        
    }
}
```



循环发送

```java
public class UdpSenderDemo01 {
    public static void main(String[] args) throws Exception {
        DatagramSocket socket = new DatagramSocket(8888);

        //准备数据：控制台读取System.in

        BufferedReader reader = new BufferedReader(new InputStreamReader(System.in));
        while (true){
            String data = reader.readLine();
            byte[] datas = data.getBytes();

            DatagramPacket packet = new DatagramPacket(datas,0,datas.length,new InetSocketAddress("localhost",6666));

            socket.send(packet);

            if (data.equals("bye")){
                break;
            }
        }


        socket.close();
    }
}
```

```java
public class UdpReceiveDemo01 {
    public static void main(String[] args) throws Exception {
        DatagramSocket socket = new DatagramSocket(6666);
        while (true){

            //准备接收的数据
            byte[] buffer = new byte[1024];
            DatagramPacket packet = new DatagramPacket(buffer,0,buffer.length);
            socket.receive(packet); //阻塞式接受包裹

            //断开连接
            byte[] data = packet.getData();
            String receiveData = new String(data,0,data.length);
            System.out.println(receiveData);
            if (receiveData.trim().equals("bye")){
                break;
            }



        }
        socket.close();
    }
}
```

