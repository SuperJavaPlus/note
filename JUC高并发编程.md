# JUC高并发编程

## 1.JUC概述

> 什么是JUC

在Java中，线程部分是一个重点，JUC就是java.util.concurrent工具包的简称。这是一个处理线程的工具包，JDK1.5开始出现的

![image-20210924201843412](F:\笔记图片\img\image-20210924201843412.png)



> 线程状态枚举类

- NEW  新建
- RUNNABLE 就绪
- BLOCKED  阻塞
- WAITING  等待（不见不散）
- TIME_WAITING 等待（过时不候）
- TERMINATED 终结



**高并发：**多个线程，同一个时间点，争取同一个资源



> 多线程的四步操作

1. 创建资源类，在资源类中创建属性和方法
2. 在资源类操作方法
   1. 判断
   2. 干活
   3. 通知
3. 创建多个线程，调用资源类的方法
4. 防止虚假唤醒的问题

## 2.卖票问题

```java
/**
 *
 * 1.在高内聚低耦合的情况下，线程操纵（对外暴露的调用方法资源类
 */
public class SaleTickets {
    public static void main(String[] args) {
        Ticket ticket = new Ticket();
        //Thread(Runnable target,String name)
        //第一个参数：实现了runnable接口的类，第二个参数：当前线程的名字
        new Thread(new Runnable() {
            @Override
            public void run() {
                for (int i = 0;i <= 30;i++){
                    ticket.sale();
                }
            }
        },"窗口一").start();
        new Thread(new Runnable() {
            @Override
            public void run() {
                for (int i = 0;i <= 30;i++){
                    ticket.sale();
                }
            }
        },"窗口二").start();
    }
}
class Ticket{  //资源类
    private int number = 30;
    private Lock lock =new ReentrantLock();

    public  void sale(){
        lock.lock();
        try {
            if (number > 0){
                System.out.println(Thread.currentThread().getName() + "\t" +"卖出第:" + (number--) + "还剩下" + number);
            }
        } finally {
            lock.unlock();
        }
    }
}

```

**Lambda表达方法**

```java
public class SaleTickets {
    public static void main(String[] args) {
        Ticket ticket = new Ticket();
        //Thread(Runnable target,String name)
        //第一个参数：实现了runnable接口的类，第二个参数：当前线程的名字
        new Thread(() -> { for (int i = 1; i <= 30; i++) ticket.sale(); }, "窗口一").start();
        new Thread(() -> { for (int i = 1; i <= 30; i++) ticket.sale(); }, "窗口二").start();
        new Thread(() -> { for (int i = 1; i <= 30; i++) ticket.sale(); }, "窗口三").start();
    }
}
class Ticket {  //资源类
    private int number = 30;
    private Lock lock = new ReentrantLock();

    public void sale() {
        lock.lock();
        try {
            if (number > 0) {
                System.out.println(Thread.currentThread().getName() + "\t" + "卖出第:" + (number--) + "还剩下" + number);
            }
        } finally {
            lock.unlock();
        }
    }
}
```

**Lambda表达式**

接口中有且仅有一个方法时，我们可以省略方法名

```java
interface Foo{
    int add(int x,int y);
}
public class LambdaExpress{
    public static void main(String args[]){
        Foo Foo = (x,y) -> {return x + y;}
    }
}
```



## 3.防止线程间的虚假唤醒

```java
public class ThreadWaitedNotifyDemo{
    public static void main(String[] args) {
        AirConditioner airConditioner = new AirConditioner();
        new Thread(()->{for (int i = 1;i <= 10;i++)airConditioner.increment();},"aa").start();
        new Thread(()->{for (int i = 1;i <= 10;i++)airConditioner.decrement();},"bb").start();
    }
}



class AirConditioner {
    private int tem = 0;
    public synchronized void increment(){
        if (tem != 0) {
            try {
                this.wait();
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
        }
        tem++;
        System.out.println(Thread.currentThread().getName() + "\t" + tem);

        //唤醒
        this.notifyAll();
    }

    public synchronized void decrement() {
        if (tem == 0){
            try {
                this.wait();
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
        }
        tem--;
        System.out.println(Thread.currentThread().getName() + "\t" + tem);
        this.notifyAll();
    }
}
```

![image-20210925202229167](F:\笔记图片\img\image-20210925202229167.png)



**多线程交互中**，必须要防止多线程的虚假唤醒，也即判断只用while

![image-20210925201601748](F:\笔记图片\img\image-20210925201601748.png)

例如，在上面的线程通信的实例中我们再加入两个线程

```java
public class ThreadWaitedNotifyDemo{
    public static void main(String[] args) {
        AirConditioner airConditioner = new AirConditioner();
        new Thread(()->{for (int i = 1;i <= 10;i++)airConditioner.increment();},"aa").start();
        new Thread(()->{for (int i = 1;i <= 10;i++)airConditioner.decrement();},"bb").start();
        new Thread(()->{for (int i = 1;i <= 10;i++)airConditioner.increment();},"cc").start();
        new Thread(()->{for (int i = 1;i <= 10;i++)airConditioner.decrement();},"dd").start();
    }
}
```

结果会出现以下情况

![image-20210925202154911](F:\笔记图片\img\image-20210925202154911.png)

所以一定是判断出现了问题，我们来进行分析

1. 假设是aa先抢到了cpu的执行权，进行+1操作，然后notifyall，通知其他线程
2. cc线程抢到了cpu的执行权，此时值为1，cc线程进入等待状态，并释放锁
3. 此时aa又抢到了，此时值仍为1，线程aa阻塞
4. 随后cc又抢到了，此时cc在 this.wait处阻塞,wait方法的特点是，在哪里睡，就在哪里醒，所以被唤醒后if判断无法生效，直接往下执行+1操作

**虚假唤醒的解决办法**：把if循环改为while循环，while循环不论在什么时候睡，什么时候醒，都要进行判断



## 4.Lock锁

```Java
/**
 * 基本步骤：
 *      1.判断
 *      2.干活
 *      3.通知
 */
public class ThreadDemo2 {
    public static void main(String[] args) {
        Share share = new Share();
        new Thread(() -> {
            try {
                for (int i =0;i <= 10;i++)
                share.add();
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
        },"A1").start();
        new Thread(() -> {
            try {
                for (int i =0;i <= 10;i++)
                share.dec();
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
        },"A2").start();
    }
}
class Share{
    private int number = 0;
    private Lock lock = new ReentrantLock();
    private Condition condition = lock.newCondition();
    public void add() throws InterruptedException {
        lock.lock();
        try {
            /**
             * 判断
             */
            while (number != 0){
                condition.await();
            }
            /**
             * 干活
             */
            number++;
            System.out.println(Thread.currentThread().getName() + "::" + number);
            /**
             * 通知
             */
            condition.signalAll();
        }finally {
            lock.unlock();
        }
    }
    public void dec() throws InterruptedException {
        lock.lock();
        try {
            while (number == 0){
                condition.await();
            }
            number--;
            System.out.println(Thread.currentThread().getName() + "::" + number);
            condition.signalAll();
        } finally {
            lock.unlock();
        }
    }
}
```



### 4.1 线程间的定制化通信

```java
public class ThreadDemo3 {
    public static void main(String[] args) {
        Resource resource = new Resource();
        new Thread(() -> {
            for (int i = 1;i <= 10; i++)
            try {
                resource.print5(i);
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
        },"a").start();

        new Thread(() -> {
            for (int i = 1;i <= 10;i++)
            try {
                resource.print10(i);
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
        },"b").start();

        new Thread(() -> {
            for (int i =1;i <= 10;i++)
            try {
                resource.print15(i);
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
        },"c").start();
    }
}
class Resource{
    //自定义标志位
    private int flag = 1;
    private Lock lock = new ReentrantLock();

    //创建三个condition
    private Condition condition1 = lock.newCondition();
    private Condition condition2 = lock.newCondition();
    private Condition condition3 = lock.newCondition();

    //操作方法
    public void print5(int loop) throws InterruptedException {
        lock.lock();
        try {
            while (flag != 1) {
                condition1.await();
            }
            for (int i = 1; i <= 5; i++) {
                System.out.println(Thread.currentThread().getName() + "::" + i + ":轮数：" + loop);
            }
            flag = 2;
            condition2.signal();
        } finally {
            lock.unlock();
        }
    }
        public void print10(int loop) throws InterruptedException {
            lock.lock();
            try {
                while (flag != 2) {
                    condition2.await();
                }
                for (int i = 1; i <= 10; i++) {
                    System.out.println(Thread.currentThread().getName() + "::" + i + ":轮数：" + loop);
                }
                flag = 3;
                condition3.signal();
            } finally {
                lock.unlock();
            }
        }

    public void print15(int loop) throws InterruptedException {
        lock.lock();
        try {
            while (flag != 3) {
                condition3.await();
            }
            for (int i = 1; i <= 15; i++) {
                System.out.println(Thread.currentThread().getName() + "::" + i + ":轮数：" + loop);
            }
            flag = 1;
            condition1.signal();
        } finally {
            lock.unlock();
        }
    }

}
```



- Object类中的wait和notify，notifyAll必须在synchronized内部执行
- await和single必须跟lock和unlock组队一起使用

## 5.集合的线程安全问题

### 5.1 List

```java
public class ThreadDemo4 {
    public static void main(String[] args) {
        List<String> list = new ArrayList<>();

        for (int i = 1;i < 1000; i++){
            new Thread(() -> {
                //向集合中添加内容
                list.add(UUID.randomUUID().toString().substring(0,8));

                //从集合中获取内容
                System.out.println(list);
            },String.valueOf(i)).start();
        }
    }
}
```

出现了如下错误

![image-20210926101458839](F:\笔记图片\img\image-20210926101458839.png)

ArrayList中的add方法

![image-20210926102410163](F:\笔记图片\img\image-20210926102410163.png)

可以看出是线程不安全的，因此会出现并发修改异常



> 解决方案

1. 使用Vector

   我们来看Vector源码

   ![image-20210926102742066](F:\笔记图片\img\image-20210926102742066.png)

   ![image-20210926102824727](F:\笔记图片\img\image-20210926102824727.png)

   继续点进去看

   ![image-20210926102800574](F:\笔记图片\img\image-20210926102800574.png)

   我们可以看到，该实现类用了synchronized关键字，不会出现并发修改异常

2. 使用工具类Collections

   ![image-20210926103237894](F:\笔记图片\img\image-20210926103237894.png)

   ```java
   public class ThreadDemo4 {
       public static void main(String[] args) {
           List<String> list = Collections.synchronizedList(new ArrayList<>());
   
           for (int i = 1;i < 1000; i++){
               new Thread(() -> {
                   //向集合中添加内容
                   list.add(UUID.randomUUID().toString().substring(0,8));
   
                   //从集合中获取内容
                   System.out.println(list);
               },String.valueOf(i)).start();
           }
       }
   }
   ```

3. 使用CopyOnWriteArrayList

   写时复制技术，支持并发读，但只能独立写

   ![image-20210926103509058](F:\笔记图片\img\image-20210926103509058.png)

   我们来看一下他的源码

   ```java
   public boolean add(E e) {
       final ReentrantLock lock = this.lock;
       lock.lock();
       try {
           Object[] elements = getArray();
           int len = elements.length;
           Object[] newElements = Arrays.copyOf(elements, len + 1);
           newElements[len] = e;
           setArray(newElements);
           return true;
       } finally {
           lock.unlock();
       }
   }
   ```

   由此我们可以看出，CopyOnWriteArrayList在执行写操作时，会先复制一份数组，然后对复制的数组进行修改，最后将旧数组覆盖为新的数组。但是这也造成了两个问题，也就是CopyOnWrite的两个缺点：

   1. 内存占用问题。因为CopyOnWrite的写时复制机制，所以在进行写操作的时候，内存里会同时驻扎两个对象的内存，旧的对象和新写入的对象（注意:在复制的时候只是复制容器里的引用，只是在写的时候会创建新对象添加到新容器里，而旧容器的对象还在使用，所以有两份对象内存）。如果这些对象占用的内存比较大，比如说200M左右，那么再写入100M数据进去，内存就会占用300M，那么这个时候很有可能造成频繁的Yong GC和Full GC。之前我们系统中使用了一个服务由于每晚使用CopyOnWrite机制更新大对象，造成了每晚15秒的Full GC，应用响应时间也随之变长。
   2. 数据的一致性问题，CopyOnWrite容器只能保证数据的最终一致性，不能保证数据的实时一致性。所以如果你希望写入的的数据，马上能读到，请不要使用CopyOnWrite容器。

   

   

   ```java
   public class ThreadDemo4 {
       public static void main(String[] args) {
           List<String> list = new CopyOnWriteArrayList<>();
   
           for (int i = 1;i < 1000; i++){
               new Thread(() -> {
                   //向集合中添加内容
                   list.add(UUID.randomUUID().toString().substring(0,8));
   
                   //从集合中获取内容
                   System.out.println(list);
               },String.valueOf(i)).start();
           }
       }
   }
   ```



### 5.2 Set

```java
public class ThreadDemo5 {
    public static void main(String[] args) {
        Set<String> set = new HashSet<>();

        for (int i = 1;i <= 1000;i++) {
            new Thread(() -> {
                set.add(UUID.randomUUID().toString().substring(0,8));
                System.out.println(set);
            }).start();
        }
    }
}
```

果不其然，又出现了如下异常

![image-20210926110530879](F:\笔记图片\img\image-20210926110530879.png)

我们来看它的底层源码

```java
public boolean add(E e) {
        return map.put(e, PRESENT)==null;
    }
```

add方法是线程不安全的，所以就会出写并发修改异常



> 解决办法

1. 使用CopyOnWriteArraySet

   ```java
   public class ThreadDemo5 {
       public static void main(String[] args) {
           Set<String> set = new CopyOnWriteArraySet<>();
   
           for (int i = 1;i <= 1000;i++) {
               new Thread(() -> {
                   set.add(UUID.randomUUID().toString().substring(0,8));
                   System.out.println(set);
               }).start();
           }
       }
   }
   ```



### 5.3 Map

```java
public class ThreadDemo6 {
    public static void main(String[] args) {
        Map<String,String> map = new HashMap<>();

        for (int i = 1;i <= 100;i++){
            String key = String.valueOf(i);
            new Thread(() -> {
                map.put(key, UUID.randomUUID().toString().substring(0,8));
                System.out.println(map);
            }).start();
        }
    }
}
```

![image-20210926112443863](F:\笔记图片\img\image-20210926112443863.png)



> 解决方案

使用ConcurrentHashMap

```java
public class ThreadDemo6 {
    public static void main(String[] args) {
        Map<String,String> map = new ConcurrentHashMap<>();

        for (int i = 1;i <= 100;i++){
            String key = String.valueOf(i);
            new Thread(() -> {
                map.put(key, UUID.randomUUID().toString().substring(0,8));
                System.out.println(map);
            }).start();
        }
    }
}
```



## 6.多线程锁

### 6.1 多线程8锁

```java
public class ThreadDemo7 {
    public static void main(String[] args) {
        Phone phone = new Phone();
        new Thread(() -> {phone.sendEmail();}).start();
         Thread.sleep(100);
        new Thread(() -> {phone.sendSMS();}).start();
    }
}
class Phone{
    public synchronized void sendEmail(){
        try {
            TimeUnit.SECONDS.sleep(4);
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
        System.out.println("===发邮件===");
    }

    public synchronized void sendSMS(){
        System.out.println("===发短信===");
    }
}
```

输出结果为

![image-20210926165537525](F:\笔记图片\img\image-20210926165537525.png)

**如果此时在资源类中添加一个普通方法**

```java
public class ThreadDemo7 {
    public static void main(String[] args) {
        Phone phone = new Phone();
        new Thread(() -> {phone.sendEmail();}).start();
         Thread.sleep(100);
        new Thread(() -> {phone.hello();}).start();
    }
}
class Phone{
    public synchronized void sendEmail(){
        try {
            TimeUnit.SECONDS.sleep(4);
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
        System.out.println("===发邮件===");
    }

    public synchronized void sendSMS(){
        System.out.println("===发短信===");
    }

    public void hello(){
        System.out.println("===hello===");
    }
}
```

则输出结果为![image-20210926165910965](F:\笔记图片\img\image-20210926165910965.png)

**如果我们创建两个资源类的对象**

```java
public class ThreadDemo7 {
    public static void main(String[] args) {
        Phone phone = new Phone();
        Phone phone1 = new Phone();
        new Thread(() -> {phone.sendEmail();}).start();
         Thread.sleep(100);
        new Thread(() -> {phone1.sendSMS();}).start();
    }
}
class Phone{
    public synchronized void sendEmail(){
        try {
            TimeUnit.SECONDS.sleep(4);
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
        System.out.println("===发邮件===");
    }

    public synchronized void sendSMS(){
        System.out.println("===发短信===");
    }
}
```

则此时结果为![image-20210926170141463](F:\笔记图片\img\image-20210926170141463.png)

**当两个方法为静态同步方法时**

```
public class ThreadDemo7 {
    public static void main(String[] args) {
        Phone phone = new Phone();

        new Thread(() -> {phone.sendEmail();}).start();
         Thread.sleep(100);
        new Thread(() -> {phone.sendSMS();}).start();
    }
}
class Phone{
    public static synchronized void sendEmail(){
        try {
            TimeUnit.SECONDS.sleep(4);
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
        System.out.println("===发邮件===");
    }

    public static synchronized void sendSMS(){
        System.out.println("===发短信===");
    }  
}
```

则此时结果为![image-20210926170558187](F:\笔记图片\img\image-20210926170558187.png)

**两个静态同步方法，两个资源类对象**

```
public class ThreadDemo7 {
    public static void main(String[] args) {
        Phone phone = new Phone();
        Phone phone1 = new Phone();
        new Thread(() -> {phone.sendEmail();}).start();
         Thread.sleep(100);
        new Thread(() -> {phone1.sendSMS();}).start();
    }
}
class Phone{
    public static synchronized void sendEmail(){
        try {
            TimeUnit.SECONDS.sleep(4);
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
        System.out.println("===发邮件===");
    }

    public static synchronized void sendSMS(){
        System.out.println("===发短信===");
    }
}
```

结果为![image-20210926170803681](F:\笔记图片\img\image-20210926170803681.png)

**一个普通同步方法，一个静态同步方法，一个资源类对象**

```java
public class ThreadDemo7 {
    public static void main(String[] args) {
        Phone phone = new Phone();
        new Thread(() -> {phone.sendEmail();}).start();
         Thread.sleep(100);
        new Thread(() -> {phone.sendSMS();}).start();
    }
}
class Phone{
    public static synchronized void sendEmail(){
        try {
            TimeUnit.SECONDS.sleep(4);
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
        System.out.println("===发邮件===");
    }

    public synchronized void sendSMS(){
        System.out.println("===发短信===");
    }
}
```

结果为![image-20210926171117161](F:\笔记图片\img\image-20210926171117161.png)

**一个普通同步方法，一个静态同步方法，两个资源类对象**

```java
public class ThreadDemo7 {
    public static void main(String[] args) {
        Phone phone = new Phone();
        Phone phone1 = new Phone();
        new Thread(() -> {phone.sendEmail();}).start();
         Thread.sleep(100);
        new Thread(() -> {phone1.sendSMS();}).start();
    }
}
class Phone{
    public static synchronized void sendEmail(){
        try {
            TimeUnit.SECONDS.sleep(4);
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
        System.out.println("===发邮件===");
    }

    public synchronized void sendSMS(){
        System.out.println("===发短信===");
    }
}
```

结果为![image-20210926171336538](F:\笔记图片\img\image-20210926171336538.png)



我们来进行分析

> synchronized锁的不是当前这个方法，而是当前这个对象，也就是说同一时间段不可能有两个线程同时进到资源类。**同一时间段，只能有一个线程访问资源类里面的一个同步方法**

一个对象里面如果有多个synchronized方法，某一时刻内，只要有一个线程去调用其中的一个synchronized方法了，其他的线程都只能等待。换句话说，只能有唯一一个线程去访问synchronized方法，锁的是当前对象this，被锁定后，其他的线程不能进入到当前对象的其他的sychronized方法

加一个普通方法后发现和同步锁无关

换成两个对象后，不是同一把锁了，所以也不存在线程争夺问题

都换成静态同步方法后，static synchronized锁的是资源类



## 7.公平锁和非公平锁

**非公平锁**：会造成其他线程饿死的情况

​				   效率高

```java
private Lock lock = new ReentrantLock()
private Lock lock = new ReentrantLock(false)
```



**公平锁**：是指多个线程按照申请锁的顺序来获取锁，效率比较低

```java
private Lock lock = new ReentrantLock(true)
```



>ReentrantLock底层源码

```java
public ReentrantLock(boolean fair) {  //判断是否是公平锁
    sync = fair ? new FairSync() : new NonfairSync();
}
```

两者区别

- 公平锁：在并发环境中，每一个线程在获取锁时会先查看此锁维护的等待队列，如果为空，或者当前线程是等待队列的第一个就占有锁，否者就会加入到等待队列中，以后会按照 FIFO 的规则获取锁
- 非公平锁：一上来就尝试占有锁，如果失败在进行排队

## 8.Callable

![image-20210927092247321](F:\笔记图片\img\image-20210927092247321.png)



> Callable和Runnable的区别

1. Callable有返回值，Runnable没有返回值
2. Callabel重写的方法要抛异常
3. 实现方法不一样

![image-20210927093227624](F:\笔记图片\img\image-20210927093227624.png)



![image-20210927095158231](F:\笔记图片\img\image-20210927095158231.png)

![image-20210927095218846](F:\笔记图片\img\image-20210927095218846.png)

![image-20210927095252333](F:\笔记图片\img\image-20210927095252333.png)

![image-20210927094825016](F:\笔记图片\img\image-20210927094825016.png)

```java
public class MyThread{
    public static void main(String[] args) throws ExecutionException, InterruptedException {
        FutureTask futureTask = new FutureTask(new MyThread2());
        new Thread(futureTask,"a").start();
        System.out.println(futureTask.get());
    }
}

class MyThread2 implements Callable<Integer>{

    @Override
    public Integer call() throws Exception {
        System.out.println("come here");
        return 1024;
    }
}
```



> 细节

get方法一般放在最后一行

```java
public class MyThread{
    public static void main(String[] args) throws ExecutionException, InterruptedException {
        FutureTask futureTask = new FutureTask(new MyThread2());
        /**此时我们创建两个线程来访问**/
        new Thread(futureTask,"a").start();
        new Thread(futureTask,"b").start();
        System.out.println(futureTask.get());
    }
}

class MyThread2 implements Callable<Integer>{

    @Override
    public Integer call() throws Exception {
        System.out.println("come here");
        return 1024;
    }
}
```

结果依然是![image-20210927102001465](F:\笔记图片\img\image-20210927102001465.png)

我们来看FutherTask的源码

```java
public FutureTask(Runnable runnable, V result) {
    this.callable = Executors.callable(runnable, result);
    this.state = NEW;       // ensure visibility of callable
}
public FutureTask(Callable<V> callable) {
        if (callable == null)
            throw new NullPointerException();
        this.callable = callable;
        this.state = NEW;       // ensure visibility of callable
    }
```



## 9.JUC强大的辅助类

### 9.1 CountDownLatch

CountDownLatch主要有两个方法，当一个或多个线程调用await方法时，这些线程会阻塞。其他线程调用countDown方法时会将计数器减一（调用cutDown方法的线程不会阻塞），当计数器变为0时，被await的线程会被唤醒，继续执行

```java
public class CountDownLatchDemo {
    public static void main(String[] args) throws InterruptedException {
        CountDownLatch countDownLatch = new CountDownLatch(6);
        for (int i = 1;i <= 6;i++){
            new Thread(() -> {
                System.out.println(Thread.currentThread().getName() + "\t离开教室");
                countDownLatch.countDown(); //每走一个学生，计数器减1
            },String.valueOf(i)).start();
        }
        countDownLatch.await();//计数器为0时，该线程被唤醒
        System.out.println("关门");
    }

}

```

![image-20210927104523337](F:\笔记图片\img\image-20210927104523337.png)

### 9.2 CyclicBarrier（可重复使用的栅栏）

现实生活中我们经常会遇到这样的情景，在进行某个活动前需要等待人全部都齐了才开始。例如吃饭时要等全家人都上座了才动筷子，旅游时要等全部人都到齐了才出发，比赛时要等运动员都上场后才开始。

在JUC包中为我们提供了一个同步工具类能够很好的模拟这类场景，它就是CyclicBarrier类。利用CyclicBarrier类可以实现一组线程相互等待，当所有线程都到达某个屏障点后再进行后续的操作。下图演示了这一过程。

![img](https://img-blog.csdnimg.cn/20181218144511688)

![image-20210927105048359](F:\笔记图片\img\image-20210927105048359.png)

```java
public class CyclicBarrierDemo {
    public static void main(String[] args) {
        CyclicBarrier cyclicBarrier = new CyclicBarrier(7,() -> {
            System.out.println("召唤神龙");
        });

        for (int i = 1;i <= 7;i++){
            final int tem = i;
            new Thread(() -> {
                System.out.println(Thread.currentThread().getName() + "\t集齐了第：" + tem + "颗龙珠");
                try {
                    cyclicBarrier.await();
                } catch (InterruptedException e) {
                    e.printStackTrace();
                } catch (BrokenBarrierException e) {
                    e.printStackTrace();
                }
            }).start();
        }
    }
}
```



### 9.3 Semaphore(操作系统中的PV信号操作)

在信号量上我们定义两种操作：

acquire(获取)：当一个线程调用acquire操作时，他要们通过成功获取信号量（信号量减1），要么一直等待下去，直到有线程释放信号量，或者超市，**相当于操作系统中的P操作**

release(释放)： 实际上会将信号量的加1，然后唤醒等待的线程，**相当于操作系统中的V操作**



信号量主要用于两个目的，一个用于多个共享资源的互斥使用，另一个用于并发线程数的控制

```java
public class SemaphoreDemo {
    public static void main(String[] args) {
        Semaphore semaphore = new Semaphore(3); //模拟资源类：有3个空车位

        for (int i = 1;i <= 6;i++){
            new Thread(() -> {
                try {
                    semaphore.acquire();   //占用，停车位的数量减少1
                    System.out.println(Thread.currentThread().getName() + "\t占到了车位");
                    TimeUnit.SECONDS.sleep(3);
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }finally {
                    semaphore.release();
                    System.out.println(Thread.currentThread().getName() + "\t离开了车位");
                }
            },String.valueOf(i)).start();
        }
    }
}
```

![image-20210927111627594](F:\笔记图片\img\image-20210927111627594.png)



**同步信号量机制（前V后P）**

```java
public class 同步信号量 {
    public static void main(String[] args) {
        Semaphore semaphore = new Semaphore(0);
        同步信号量 t = new 同步信号量();

        new Thread(() -> {
            t.before();
            semaphore.release();
        }).start();

        new Thread(() -> {
            try {
                semaphore.acquire();
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
            t.after();
        }).start();
    }

    public void before(){
        System.out.println("先执行");
    }
    public void after(){
        System.out.println("后执行");
    }
}
```



## 10.读写锁

- 独占锁：指该锁一次只能被一个线程持有
- 共享锁：该锁可以被多个线程持有

对于 ReentrantLock 和 synchronized 都是独占锁；对与 ReentrantReadWriteLock 其读锁是共享锁而写锁是独占锁。读锁的共享可保证并发读是非常高效的，读写、写读和写写的过程是互斥的。



多个线程同时读一个资源类没有任何问题，所以为了满足并发量，读取共享资源应该可以同时进行

但是如果一个线程想去写共享资源，就不应该再有其他线程可以对该资源进行**读或写**

读-读能共存

读-写不能共存

写-写不能共存

```java
public class ReadWriteLock {
    public static void main(String[] args) {
        Cache cache = new Cache();
        //读
        for (int i = 1; i <= 5;i++){
            final int tem = i;
            new Thread(() -> {cache.put(String.valueOf(tem),tem + " ");},String.valueOf(i)).start();
        }

        //写
        for (int i = 1;i <= 5;i++){
            final int tem = i;
            new Thread(() -> {cache.get(String.valueOf(tem));},String.valueOf(i)).start();
        }
    }
}

class Cache{
    private volatile Map<String,Object> map = new HashMap<>();

    public void put(String key,Object value){
        System.out.println(Thread.currentThread().getName() + "\t开始写入" + key);
        try {
            TimeUnit.SECONDS.sleep(3);
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
        map.put(key,value);
        System.out.println(Thread.currentThread().getName() + "\t写入成功" );
    }

    public void get(String key){
        System.out.println(Thread.currentThread().getName() + "\t开始读取" );
        Object result = map.get(key);
        System.out.println(Thread.currentThread().getName() + "\t读取成功"  + result);
    }
}
```

![image-20210927144708711](F:\笔记图片\img\image-20210927144708711.png)

可以看到在线程还没成功写入的情况下，就开始读取，出现了错误

我们可以加读写锁

```java
public class ReadWriteLockDemo {
    public static void main(String[] args) {
        Cache cache = new Cache();
        //读
        for (int i = 1; i <= 5;i++){
            final int tem = i;
            new Thread(() -> {cache.put(String.valueOf(tem),tem + " ");},String.valueOf(i)).start();
        }

        //写
        for (int i = 1;i <= 5;i++){
            final int tem = i;
            new Thread(() -> {cache.get(String.valueOf(tem));},String.valueOf(i)).start();
        }
    }
}

class Cache{
    private volatile Map<String,Object> map = new HashMap<>();
    private ReadWriteLock readWriteLock = new ReentrantReadWriteLock();
    public void put(String key,Object value){
        readWriteLock.writeLock().lock();
        try {
            System.out.println(Thread.currentThread().getName() + "\t开始写入" + key);
            TimeUnit.SECONDS.sleep(1);
        } catch (InterruptedException e) {
            e.printStackTrace();
        }finally {
            readWriteLock.writeLock().unlock();
        }
        map.put(key,value);
        System.out.println(Thread.currentThread().getName() + "\t写入成功" );
    }

    public void get(String key){
        readWriteLock.readLock().lock();
        try {
            System.out.println(Thread.currentThread().getName() + "\t开始读取" );
            Object result = map.get(key);
            System.out.println(Thread.currentThread().getName() + "\t读取成功"  + result);
        }finally {
            readWriteLock.readLock().unlock();
        }
    }
}
```



## 11.线程池

### 11.1 BlockingQueue(阻塞队列)

所谓阻塞，在某些线程情况下会挂起线程（即阻塞），一旦条件满足，被挂起的线程又会自动被唤醒



当阻塞队列是空的，从队列中获取元素的操作将会被阻塞

当阻塞队列是满的，向队列中添加元素的操作将会被阻塞



> 常用的阻塞队列

- ArrayBlockingQueue：是一个基于数组结构的有界阻塞队列，此队列按 FIFO（先进先出）对元素进行排序。
- LinkedBlokcingQueue：是一个基于链表结构的阻塞队列，此队列按 FIFO（先进先出）对元素进行排序，吞吐量通常要高于 ArrayBlockingQueue。
- SynchronousQueue：是一个不存储元素的阻塞队列，每个插入操作必须等到另一个线程调用移除操作，否则插入操作一直处于阻塞状态，吞吐量通常要高于 LinkedBlokcingQueue。



> 我们为什么需要阻塞队列

好处是我们不需要关心什么时候需要阻塞线程，什么时候需要唤醒线程，因为这一切阻塞队列都给你一手包办了

| 方法类型 | 抛出异常  | 特殊值（有返回值，不报异常） | 阻塞   | 超时等待           |
| -------- | --------- | ---------------------------- | ------ | ------------------ |
| 插入     | add(e)    | offer(e) 插入不成功返回false | put(e) | offer(e,time,unit) |
| 移除     | remove()  | poll()  移除不成功就返回null | take() | poll(time,unit)    |
| 检查队首 | element() | peek()                       | 不可用 | 不可用             |

- 行为解释：
  - 抛异常：如果操作不能马上进行，则抛出异常
  - 特定的值：如果操作不能马上进行，将会返回一个特殊的值，一般是 true 或者 false
  - 阻塞：如果操作不能马上进行，操作会被阻塞
  - 超时：如果操作不能马上进行，操作会被阻塞指定的时间，如果指定时间没执行，则返回一个特殊值，一般是 true 或者 false
- 插入方法：
  - add(E e)：添加成功返回true，失败抛 IllegalStateException 异常
  - offer(E e)：成功返回 true，如果此队列已满，则返回 false
  - put(E e)：将元素插入此队列的尾部，如果该队列已满，则一直阻塞
- 删除方法：
  - remove(Object o) ：移除指定元素,成功返回true，失败返回false
  - poll()：获取并移除此队列的头元素，若队列为空，则返回 null
  - take()：获取并移除此队列头元素，若没有元素则一直阻塞
- 检查方法：
  - element() ：获取但不移除此队列的头元素，没有元素则抛异常
  - peek() :获取但不移除此队列的头；若队列为空，则返回 null





### 11.2 线程池

> 线程池的优势：

线程池做的工作主要是控制运行的线程数量，处理过程中将任务放入队列，然后在线程创建后启动这些任务，如果线程数量超过了最大数量，超出数量的线程排队等候，等其他线程执行完毕，再从队列中取出任务来执行

主要特点：**线程复用；控制最大并发数；管理线程**

第一：降低了资源的消耗。通过重复利用已创建的线程降低线程创建和销毁造成的消耗

第二：提高响应速度。当任务到达时，任务可以不需要等待线程的创建就能立即执行

第三：提高线程的可管理性。线程是稀缺资源，如果无限制的创建，不仅会消耗系统资源，还会降低系统的稳定性，使用线程池可以进行统一的分配，调优和监控



### 11.3 线程池的使用

![image-20210927163821725](F:\笔记图片\img\image-20210927163821725.png)

Java8的线程池是通过Executor框架实现的

![image-20210927164334230](F:\笔记图片\img\image-20210927164334230.png)

我们可以通过辅助工具类Executors获得ThreadPoolExecutor,通过ThreadPoolExecutor我们就可以获得线程池



> 线程池的三大方法

```java
public class MyThreadPool {
    public static void main(String[] args) {
//        ExecutorService threadPool = Executors.newFixedThreadPool(5);  //固定大小一池五个受理  类似一个银行五个受理窗口
//        ExecutorService threadPool1 = Executors.newSingleThreadExecutor();//一个工作线程
        ExecutorService threadPool2 = Executors.newCachedThreadPool(); //一池n线程
        try {//模拟有十个顾客来银行办理业务，目前池子里面有5个工作人员提供服务
            for (int i = 1;i <= 10;i++){
                threadPool2.execute(() -> {
                    System.out.println(Thread.currentThread().getName() + "\t办理业务");
                });
            }
        }catch (Exception e){
            e.printStackTrace();
        }finally {
            threadPool2.shutdown();
        }
    }
}
```

> 面试题

**你在工作中单一的、固定数的和可变的三种创建线程池的方法，你用哪个多，超级大坑？**

如果读者对Java中的阻塞队列有所了解的话，看到这里或许就能够明白原因了。

Java中的BlockingQueue主要有两种实现，分别是ArrayBlockingQueue 和 LinkedBlockingQueue。

ArrayBlockingQueue是一个用数组实现的有界阻塞队列，必须设置容量。

LinkedBlockingQueue是一个用链表实现的有界阻塞队列，容量可以选择进行设置，不设置的话，将是一个无边界的阻塞队列，最大长度为Integer.MAX_VALUE。

这里的问题就出在：不设置的话，将是一个无边界的阻塞队列，最大长度为Integer.MAX_VALUE。也就是说，如果我们不设置LinkedBlockingQueue的容量的话，其默认容量将会是Integer.MAX_VALUE。

而newFixedThreadPool中创建LinkedBlockingQueue时，并未指定容量。此时，LinkedBlockingQueue就是一个无边界队列，对于一个无边界队列来说，是可以不断的向队列中加入任务的，这种情况下就有可能因为任务过多而导致内存溢出问题。

上面提到的问题主要体现在newFixedThreadPool和newSingleThreadExecutor两个工厂方法上，并不是说newCachedThreadPool和newScheduledThreadPool这两个方法就安全了，这两种方式创建的最大线程数可能是Integer.MAX_VALUE，而创建这么多线程，必然就有可能导致OOM。



### 11.4 ThreadPoolExecutor底层原理

![image-20210928141457970](F:\笔记图片\img\image-20210928141457970.png)

![image-20210928141516184](F:\笔记图片\img\image-20210928141516184.png)

![image-20210928142019891](F:\笔记图片\img\image-20210928142019891.png)



### 11.5 线程池的七大参数，四大拒绝策略

![image-20210928142145052](F:\笔记图片\img\image-20210928142145052.png)

- corePoolSize:线程池中的常驻核心线程数
- maximumPoolSize:线程池中能够容纳同时执行的最大线程数，此值必须大于等于1
- keepAliveTime:多余的空闲线程的存活时间。当前池中的线程数量超过corePoolSize时，当空闲时间达到keepAliveTime时，多余线程会被销毁直到只剩下corePoolSize个线程为止
- unit:keepAliveTime的单位
- workQueue:：一个阻塞队列，用来存储等待执行的任务，当线程池中的线程数超过它的corePoolSize的时候，线程会进入阻塞队列进行阻塞等待。通过workQueue，线程池实现了阻塞功能
- ThreadFactory:线程工厂，用来创建线程
- hadler:表示当前拒绝处理任务时的策略

### **拒绝策略**

- AbortPolicy:丢弃任务并抛出RejectedExecutionException

- CallerRunsPolicy：只要线程池未关闭，该策略直接在调用者线程中，运行当前被丢弃的任务。显然这样做不会真的丢弃任务，但是，任务提交线程的性能极有可能会急剧下降。（回退策略，从哪里来就回哪里去）

  ![image-20210928160921181](F:\笔记图片\img\image-20210928160921181.png)

  此时maximumPoolSize+workQueue小于当前任务数，执行该拒绝策略

  ![image-20210928161031002](F:\笔记图片\img\image-20210928161031002.png)

  

- DiscardOldestPolicy：丢弃队列中最老的一个请求，也就是即将被执行的一个任务，并尝试再次提交当前任务。

- DiscardPolicy：丢弃任务，不做任何处理。如果允许任务丢失，这是最好的一种策略

![](F:\笔记图片\img\image-20210928144154311.png)







### 11.6 线程池的工作原理

如果当前线程池中的线程数目小于corePoolSize，则每来一个任务，就会创建一个线程去执行这个任务；

如果当前线程池中的线程数目>=corePoolSize，则每来一个任务，会尝试将其添加到任务缓存队列当中，若添加成功，则该任务会等待空闲线程将其取出去执行；若添加失败（一般来说是任务缓存队列已满），则会尝试创建新的线程去执行这个任务；如果当前线程池中的线程数目达到maximumPoolSize，则会采取任务拒绝策略进行处理；

如果线程池中的线程数量大于 corePoolSize时，如果某线程空闲时间超过keepAliveTime，线程将被终止，直至线程池中的线程数目不大于corePoolSize；如果允许为核心池中的线程设置存活时间，那么核心池中的线程空闲时间超过keepAliveTime，线程也会被终止。



**线程池的关闭**

ThreadPoolExecutor提供了两个方法，用于线程池的关闭，分别是shutdown()和shutdownNow()，其中：

- shutdown()：不会立即终止线程池，而是要等所有任务缓存队列中的任务都执行完后才终止，但再也不会接受新的任务

- shutdownNow()：立即终止线程池，并尝试打断正在执行的任务，并且清空任务缓存队列，返回尚未执行的任务

> 生产中如何设置合理参数

阿里巴巴Java开发手册中规定线程池不允许使用Executors去创建，而是通过ThreadPoolExecutors

的方式

![image-20210928160041675](F:\笔记图片\img\image-20210928160041675.png)

![image-20210928160051669](F:\笔记图片\img\image-20210928160051669.png)



### 11.7 最后杂谈                                                   

**如何选择线程池数量**

**线程池的大小决定着系统的性能，过大或者过小的线程池数量都无法发挥最优的系统性能。**

当然线程池的大小也不需要做的太过于精确，只需要避免过大和过小的情况。一般来说，确定线程池的大小需要考虑CPU的数量，内存大小，任务是CPU密集型还是IO密集型等因素

**合理配置线程池你是如果考虑的？**

- CPU 密集型
  - CPU 密集的意思是该任务需要大量的运算，而没有阻塞，CPU 一直全速运行。
  - CPU 密集型任务尽可能的少的线程数量，一般为 CPU 核数 + 1 个线程的线程池。
- IO 密集型
  - 由于 IO 密集型任务线程并不是一直在执行任务，可以多分配一点线程数，如 CPU * 2 。
  - 也可以使用公式：CPU 核数 / (1 - 阻塞系数)；其中阻塞系数在 0.8 ～ 0.9 之间。

在Java中使用

```
int ncpus = Runtime.getRuntime().availableProcessors();
```

获取CPU的数量。



## 12.volatile

> **谈谈对volatile的理解**

- volatile是Java虚拟机提供的轻量级的同步机制，主要有三大特征
  - 保证可见性
  - 不保证原子性
  - 禁止指令重排



> **谈谈JMM**（Java内存模型）

本身是一种抽象的模型，并不真实存在，它描述的是一组规则或规范，通过这组规范定义了程序中各个变量（包含实例字段，静态字段和构成数组对象的元素）的访问方式

- **JMM关于同步的规定**
  - 线程解锁前，必须把共享变量的值刷新回主内存
  - 线程加锁前，必须读取主内存的最新值到自己的工作内存
  - 加锁解锁必须使同一把锁
  
- 由于JVM运行程序的实体是线程，而每个线程创建时JVM都会为其创建一个工作内存（有些地方称为栈空间），工作内存是每个线程的私有数据区域，而Java内存模型中规定所有变量都存储在主内存，主内存是共享内存区域，所有线程都可以访问。**但线程对变量的操作必须在工作内存中进行，首先要将变量从主内存拷贝到自己的工作内存空间，然后对变量进行操作，操作完成后再将变量写回主内存**，不能直接操作主内存中的变量，各个线程中的工作内存中存储着主内存中的变量副本拷贝，因此不同的线程间无法访问对方的工作内存，线程间的通信必须通过主内存来完成。

  ![img](http://img.cuzz.site/2020/%E5%86%85%E5%AD%98%E6%A8%A1%E5%9E%8B.png)

- JMM三大特性：

  - 可见性
  - 原子性
  - 有序性

> 可见性代码验证

```java
class MyData{
    int number = 0;
    public void addTo(){
        this.number = 60;
    }
}
public class VolatileDemo1 {
    public static void main(String[] args) {
        MyData myData = new MyData();
        new Thread(() -> {
            System.out.println(Thread.currentThread().getName() + "\tcome in");
            try {
                TimeUnit.SECONDS.sleep(3);
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
            myData.addTo();
            System.out.println(Thread.currentThread().getName() + "\tupdate number value");
        },"AAA").start();

        while (myData.number == 0){
            //main线程一直等待，直到number的值不再等于0
        }
        System.out.println(Thread.currentThread().getName() + "\t over");
    }
}
```

![image-20210929101134323](F:\笔记图片\img\image-20210929101134323.png)

此时没有对变量使用volatile关键字，没有可见性

```java
class MyData{
    volatile int  number = 0;
    public void addTo(){
        this.number = 60;
    }
}
public class VolatileDemo1 {
    public static void main(String[] args) {
        MyData myData = new MyData();
        new Thread(() -> {
            System.out.println(Thread.currentThread().getName() + "\tcome in");
            try {
                TimeUnit.SECONDS.sleep(3);
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
            myData.addTo();
            System.out.println(Thread.currentThread().getName() + "\tupdate number value");
        },"AAA").start();

        while (myData.number == 0){
            //main线程一直等待，直到number的值不再等于0
        }
        System.out.println(Thread.currentThread().getName() + "\t over");
    }
}
```

![image-20210929101459775](F:\笔记图片\img\image-20210929101459775.png)

我们对变量加了volatile关键字之后，保证了可见性



> 原子性

- 原子性指的是不可分割，也即某个线程正在做某个具体业务时，中间不可以被加塞或者被分割，需要整体完整，要么同时成功，要么同时失败

```java
class MyData{
    volatile int  number = 0;
    //此时number前面是加了volatile关键字的
    public void addPlusPlus(){
        number++;
    }
}
public class VolatileDemo1 {
    public static void main(String[] args) {
        MyData myData = new MyData();
        for (int i = 1;i <= 20;i++){
            new Thread(() -> {
                for (int j = 0; j < 1000;j++){
                    myData.addPlusPlus();
                }
            },String.valueOf(i)).start();
        }
        while (Thread.activeCount() > 2){
            Thread.yield(); //礼让线程
        }
        System.out.println(myData.number);
    }
```

![image-20210929103710691](F:\笔记图片\img\image-20210929103710691.png)

我们可以看到此时结果不等于两万，由此证明了volatile不保证原子性

- number++在多线程环境下是非线程安全的

- n++被分为3步操作

  1. 得到n的值
  2. 执行加1操作，在各自的工作空间中
  3. 把累加后的值返回

- 解决办法：

  - 加sychronized(不建议)

  - 使用原子类 AtomicInteger

    ```java
    class MyData{
        AtomicInteger atomicInteger = new AtomicInteger();
        public void addAtomic(){
            atomicInteger.getAndIncrement();
        }
    }
    public class VolatileDemo1 {
        public static void main(String[] args) {
            MyData myData = new MyData();
            for (int i = 1;i <= 20;i++){
                new Thread(() -> {
                    for (int j = 0; j < 1000;j++){
                        myData.addAtomic();
                    }
                },String.valueOf(i)).start();
            }
            while (Thread.activeCount() > 2){
                Thread.yield(); //礼让线程
            }
            System.out.println(myData.atomicInteger);
        }
    ```



> 禁止指令排序

- 多线程环境中线程交替执行，由于编译器优化重排的存在，两个线程中使用的变量能否保证一致是无法确定的，结果无法预测

- 处理器在进行重排序时必须考虑指令之间的数据依赖性

volatile实现禁止指令重排优化，从而避免了多线程环境下程序出现乱序执行的现象

先了解一下概念，内存屏障又称内存栅栏，是一个CPU指令，他的作用有两个：

1. 保证特定操作的执行顺序
2. 保证某些变量的内存可见性

由于编译器核处理器都能执行指令重排优化，如果在指令间插入一天内存屏障（Memory Barrier）则会告诉编译器和CPU，不管什么指令都不能和这条Memory Barrier指令重排序，也就是说通过**插入内存屏障禁止在内存屏障前后的指令执行重排序优化。**内存屏障的另外一个作用是强制刷出各种CPU的缓存数据，因此任何CPU上的线程都能读取到这些数据的最新版本

下面是保守策略下，volatile写插入内存屏障后生成的指令序列示意图：

![img](http://img.cuzz.site/2020/0e75180bf35c40e2921493d0bf6bd684_th.png)

下面是在保守策略下，volatile读插入内存屏障后生成的指令序列示意图：

![img](http://img.cuzz.site/2020/20200910141550.png)



- 工作内存与主内存同步延迟现象导致的可见性问题：可以使用synchronized或volatile关键字解决，他们都可以是一个线程修改后的变量立即对其他线程可见



**你在哪些地方用过volatile**

- 单例模式

  ```java
  public class SingletonDemo {
      public static void main(String[] args) {
          for (int i = 0; i < 10; i++) {
              new Thread(() -> {SingleTon.getInstance();}).start();
          }
      }
  }
  
  //DCL模式：Double check lock 双端检索机制
  class SingleTon{
      private static SingleTon instance = null;
  
      private SingleTon(){
          System.out.println("只能创建一次对象");
      }
  
      public static SingleTon getInstance() {
          if (instance == null) {
              synchronized (SingleTon.class) {
                  if (instance == null) {
                      instance = new SingleTon();
                  }
              }
          }
          return instance;
      }
  }
  ```

  双端检索机制不一定线程安全，因为有指令重排的存在，加入volatile可以禁止指令重排

  原因是在于某一个线程执行到第一次检测，读取到的 instance 不为 null 时，**instance 的引用对象可能还没有完成初始化。**`instance = new Singleton() `可以分为以下三步完成。

  ```
  memory = allocate();  // 1.分配对象空间
  instance(memory);     // 2.初始化对象
  instance = memory;    // 3.设置instance指向刚分配的内存地址，此时instance != null
  ```

  步骤 2 和步骤 3 不存在依赖关系，而且无论重排前还是重排后程序的执行结果在单线程中并没有改变，因此这种优化是允许的，发生重排。

  ```
  memory = allocate();  // 1.分配对象空间
  instance = memory;    // 3.设置instance指向刚分配的内存地址，此时instance != null，但对象还没有初始化完成
  instance(memory);     // 2.初始化对象
  ```

  **所以，当一条线程访问的instance不为空的时候，由于instance未必初始化完成，也就造成了线程安全的问题**



## 12.CAS

> CAS是什么

CAS是比较并交换



```java
public class CASDemo {
    public static void main(String[] args) {
        AtomicInteger atomicInteger = new AtomicInteger(5);
        System.out.println(atomicInteger.compareAndSet(5, 9) + "\t修改后的值位为" + atomicInteger.get());
        System.out.println(atomicInteger.compareAndSet(5,1024) + "\t修改后的值位为" + atomicInteger.get());
    }
}
```

![image-20210929161426978](F:\笔记图片\img\image-20210929161426978.png)

- 如果线程的期望值跟主物理内存的真实值一样，就修改为线程的更新值，并返回true
- 如果线程的期望值跟主物理内存的真实值不一样，则修改失败，并获取现在主物理内存的真实值



### 12.1 CAS底层原理

```java
public final int getAndIncrement() {
        return unsafe.getAndAddInt(this, valueOffset, 1);
    }

/**
this:代表当前对象
valueoffset：内存偏移量，内存地址
**/
```

引出一个问题，unsafe是什么？

**1.unsafe**

​	是CAS的核心类，由于Java方法无法直接访问底层系统，需要通过本地（native）方法来访问，Unsafe相当于一个后门，基于该类可以直接操作特定内存的数据。**unsafe类在sun.misc包中**，其内部方法可以像c的指针一样直接操作内存，因为Java中CAS的操作的执行依赖于unsafe类的方法

**注意unsafe类中的所有方法都是native修饰的，也就是说unsafe类中的方法都直接调用操作系统底层资源执行相应的任务**

2.valueoffset，表示该变量值在内存中的偏移地址，因为unsafe就是根据内存编译地址获取数据的

3.变量value用volatile修饰，保证了多线程间的内存可见性



CAS是什么？

- CAS全称compareAndSwap，它是一条CPU并发原语
- 它的功能是判断内存某一位置的值是否是预期值，如果是则更改它，整个过程是原子的
- CAS 并发原体现在 JAVA 语言中就是 sun.misc.Unsafe 类中的各个方法。调用 UnSafe 类中的 CAS 方法，JVM 会帮我们实现出 CAS 汇编指令。这是一种完全依赖硬件的功能，通过它实现了原子操作。由于 CAS 是一种系统源语，源语属于操作系统用语范畴，是由若干条指令组成，用于完成某一个功能的过程，并且原语的执行必须是连续的，在执行的过程中不允许被中断，也就是说 CAS 是一条原子指令，不会造成所谓的数据不一致的问题。

- 分析一下getAndAddInt这个方法

  ```java
  // unsafe.getAndAddInt
  public final int getAndAddInt(Object obj, long valueOffset, long expected, int val) {
      int temp;
      do {
          temp = this.getIntVolatile(obj, valueOffset);  // 获取快照值
      } while (!this.compareAndSwap(obj, valueOffset, temp, temp + val));  // 如果此时 temp 没有被修改，就能退出循环，否则重新获取
      return temp;
  }
  ```

  - temp = this.getIntVolatile(obj, valueOffset);   **根据当前对象以及当前对象的内存偏移地址得到最新值**
  - !this.compareAndSwap(obj, valueOffset, temp, temp + val)；**得到最新值之后和预期值进行比较，如果等于预期值，则进行  +val 操作，此时这句话的结果为false，跳出循环，修改成功**
  - 如果最新值不等于预期值，则!this.compareAndSwap(obj, valueOffset, temp, temp + val)这句话的返回结果为true，进入循环，重新获得当前最新的值，再进行比较，直至更新成功



> AtomicInterger为什么用CAS而不用Sychronized

- sychronized同一时间段只允许一个线程来访问，保证了一致性，但并发性下降
- CAS既保证了一致性，又提高了一致性



> CAS的缺点

- 循环时间长，开销大
- 只能保证**一个**共享变量的原子操作，对于多个共享变量操作时，循环CAS就无法保证操作的原子性，这个时候只能用加锁来保证
- 引出ABA问题



### 12.2 CAS的ABA问题

ABA问题：狸猫换太子

CAS算法实现一个重要前提需要取出内存中某时刻的数据并在当下时刻比较并替换，那么在这个时间差内会导致数据的变化

```java
public class ABADemo {
    static AtomicReference<Integer> atomicReference = new AtomicReference<>(100);
    public static void main(String[] args) {
        new Thread(() -> {
            System.out.println(atomicReference.compareAndSet(100, 101));
            System.out.println(atomicReference.compareAndSet(101, 100));
        }).start();

        new Thread(() -> {
            try {
                TimeUnit.SECONDS.sleep(1);
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
            System.out.println(atomicReference.compareAndSet(100,2021));
        }).start();
    }
}
```

这就是所谓的ABA问题



**如何解决ABA问题**

时间戳原子引用

```java
static AtomicStampedReference<Integer> atomicStampedReference = new AtomicStampedReference<>(100,1);
public static void main(String[] args) {
    new Thread(() -> {
        int tamped = atomicStampedReference.getStamp();
        System.out.println("第一次拿到的版本号为" + "\t" + tamped);
        try {
            TimeUnit.SECONDS.sleep(1);
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
        System.out.println(atomicStampedReference.compareAndSet(100, 101, atomicStampedReference.getStamp(), atomicStampedReference.getStamp() + 1));
        System.out.println("第二次拿到的版本号为" + "\t" + atomicStampedReference.getStamp());
        System.out.println(atomicStampedReference.compareAndSet(101, 100, atomicStampedReference.getStamp(), atomicStampedReference.getStamp() + 1));
        System.out.println("第三次拿到的版本号为" + "\t" + atomicStampedReference.getStamp());
    }).start();

    new Thread(() -> {
        int temped = atomicStampedReference.getStamp();
        try {
            TimeUnit.SECONDS.sleep(4);
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
        System.out.println("是否修改成功?" + "\t" + atomicStampedReference.compareAndSet(100, 2021, temped, temped + 1));
        System.out.println("当前最新版本号" + atomicStampedReference.getStamp());
    }).start();
}
```



## 13.可重入锁与不可重入锁

- 可重入锁：又名递归锁，指的是同一线程外层函数获得锁之后，内层递归函数仍然能获取该锁的代码，在同一个线程在外层方法获取锁的时候，在进入内层方法会自动获取锁。也即是说，线程可以进入任何一个它已拥有的锁所同步着的代码块，**可重入锁的作用是防止死锁**
- 不可重入锁：所谓不可重入锁，即若当前线程执行某个方法已经获取了该锁，那么在方法中尝试再次获取锁时，就会获取不到被阻塞



> 代码实现

- 可重入锁

  ```java
  public class ReenterLockDemo {
      public static void main(String[] args) {
          Phone phone = new Phone();
          new Thread(() -> {
              phone.sendSMS();
          },"t1").start();
  
          new Thread(() -> {
              phone.sendSMS();
          },"t2").start();
      }
  }
  
  class Phone{
      private Lock lock = new ReentrantLock();
      public void sendSMS(){
          lock.lock();
          System.out.println(Thread.currentThread().getName() + "\t invoke sendSMS");
          sendEmail();
          lock.unlock();
      }
      public void sendEmail(){
          lock.lock();
          System.out.println(Thread.currentThread().getName() + "\t invoke sendEmail");
          lock.unlock();
      }
  
  }
  ```



**synchronized与ReenterLock都是可重入锁**



## 14.自旋锁

- 自旋锁是指尝试获取锁的线程**不会立即阻塞，而是采用循环的方式去尝试获取锁**，这样的好处是减少线程上下文切换的消耗，缺点是循环会消耗CPU

```java
public class SpinLockDemo {
    //原子引用线程
    AtomicReference<Thread> atomicReference = new AtomicReference<>();
    public static void main(String[] args) {
        SpinLockDemo spinLockDemo = new SpinLockDemo();
        new Thread(() -> {
            spinLockDemo.myLock();
            try {
                TimeUnit.SECONDS.sleep(5);
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
            spinLockDemo.myUnlock();
        },"AA").start();

        try {
            TimeUnit.SECONDS.sleep(1);
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
        new Thread(() -> {
            spinLockDemo.myLock();
            try {
                TimeUnit.SECONDS.sleep(1);
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
            spinLockDemo.myUnlock();
        },"BB").start();
    }

    public void myLock(){
        Thread thread = Thread.currentThread();
        System.out.println(thread.getName() + "\t" + "come in ^o^");
        while (!atomicReference.compareAndSet(null,thread)) {

        }
    }

    public void myUnlock() {
        Thread thread = Thread.currentThread();
        atomicReference.compareAndSet(thread,null);
        System.out.println(thread.getName() + "\t invoked myUnlock");
    }
}
```



## 15.AQS

### 15.1 LockSupport是什么

- locksupport是用来创建锁和其他同步类的基本线程阻塞原语。
- LockSupport类使用了一种名为**Permit**的概念来做到阻塞和唤醒线程的功能，每一个线程都有一个许可
- permit只有两个值1和0，默认是0，可以把许可看成是一种（0，1）信号量，但与信号量不同的是，许可的累加上限是1

> LockSupport中的park()和unpark()的作用分别是阻塞线程和解除阻塞线程

![image-20211005115353366](F:\笔记图片\img\image-20211005115353366.png)

- permit默认是0，所以一开始调用park()方法时，当前线程就会被阻塞，直到别的线程将当前线程的permit设置为1时，park()方法会被唤醒，然后会将permit再次设置为0并返回
- 调用unpark()方法后，就会将thread线程的许可设置成1（注意多次调用unpark方法许可不会累加，permit的值还为1）会自动唤醒thread线程，即之前阻塞中的LockSupport.park()方法会立即返回



> 案例分析

```java
public class LockSupportDemo {
    public static void main(String[] args) {
     
        Thread a = new Thread(() -> {
            System.out.println("------------a come in");
            LockSupport.park(); //直接被阻塞，等待通知，它通过需要许可证
            System.out.println("-------------被唤醒");
        },"A");
        a.start();
        try {
            TimeUnit.SECONDS.sleep(3);
        } catch (InterruptedException e) {
            e.printStackTrace();
        }

        Thread b = new Thread(() -> {
            LockSupport.unpark(a);
            System.out.println("----------通知");
        },"B");
        b.start();
    }
}
```

运行结果

![image-20211005155640290](F:\笔记图片\img\image-20211005155640290.png)



**为什么可以先唤醒线程后阻塞线程?**

- 因为unpark获得了一个permit，之后再调用park()，就可以名正言顺的凭证消费，故不会阻塞



**为什么唤醒两次后阻塞两次，但最终结果还会阻塞线程？**

- 因为凭证的数量最多为1，连续两次调用unpark()和调用一次unpark效果一样，只会增加一个凭证，而调用两次park()却需要消费两个凭证，证不够，不能放行



### 15.2 AQS能干什么

- AbstractQueuedSynchronizer，它就是Java的一个抽象类，它的出现是为了解决多线程竞争共享资源而引发的安全问题，细致点说AQS具备一套线程阻塞等待以及被唤醒时锁分配的机制，这个机制AQS是用CLH队列锁实现的，即将暂时获取不到锁的线程加入到队列中，队列是双向队列。

![](F:\笔记图片\img\image-20211005162739220.png)



**进一步理解锁和同步器的关系**

- 锁：面向锁的使用者，定义了程序员和锁交互的使用层API，隐藏了实现细节，你调用即可
- 同步器：面向锁的实现者

> 为什么需要AQS

- 抢到资源的线程直接使用处理业务逻辑，抢不到资源的必然涉及一种排队等候机制。抢占资源失败的线程继续去等待（类似银行业务办理窗口都满了，暂时没有受理窗口的顾客只能去**候客区排队等候**），**但等候线程仍然保留获取锁的可能且获取锁的流程仍在继续**

  既然说到了**排队等候机制**，那么就一定会有某种队列形成，这样的队列是什么数据结构呢？

  如果共享资源被占用，**就需要一定的阻塞等待唤醒机制来保证锁分配**，这个机制主要用CLH队列的变体实现的，将暂时获取不到锁的线程加入到队列中，这个队列就是AQS的抽象表现。

  `它将请求共享资源的线程封装成队列节点(Node)，通过CAS，自旋一次LockSupport.park的方式，维护state变量的状态，使并发达到同步的控制效果`

### 15.3 AQS底层结构

![](https://img-blog.csdnimg.cn/20200131221816944.jpg?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2xzdW53aW5n,size_16,color_FFFFFF,t_70)

- ![image-20211005165143250](F:\笔记图片\img\image-20211005165143250.png)

  有阻塞就会有排队，实现排队必然需要队列

- ![image-20211005170133594](F:\笔记图片\img\image-20211005170133594.png)

  该内部类就是载体，装的就是一个个的线程.**队列中每个排队的个体就是一个一个的node**

  Node类的内部结构

  ```java
  //共享模式
  static final Node SHARED = new Node();
  
  //独占模式
  static final Node EXCLUSIVE = null;
  
  //线程被取消了
  static final int CANCELLED =  1;
  
  //后继线程需要被唤醒
  static final int SIGNAL    = -1;
  
  //等待condition唤醒
  static final int CONDITION = -2;
  
  //共享式同步状态获取将会无条件的传播下去
  static final int PROPAGATE = -3;
  
  //初始为0，状态是上面几种
  volatile int waitStatus;
  
  //前置节点
  volatile Node prev;
  
  //
  volatile Node next;
  
  volatile Thread thread;
  
  Node nextWaiter;
  
  final boolean isShared() {
      return nextWaiter == SHARED;
  }
  ```

- ![image-20211005171139844](F:\笔记图片\img\image-20211005171139844.png)

  AQS的同步状态State成员变量





![image-20211005174401858](F:\笔记图片\img\image-20211005174401858.png)

**AQS同步队列基本结构**

![image-20211005174342595](F:\笔记图片\img\image-20211005174342595.png)



### 15.4 从ReentrantLock解读AQS

![image-20211006161841025](F:\笔记图片\img\image-20211006161841025.png)

#### **lock()方法**

```java
public class AQSDemo {
    public static void main(String[] args) {
        ReentrantLock lock = new ReentrantLock();
        new Thread(() -> {
            lock.lock();
            try {
                System.out.println("-----A Thread come in");
                TimeUnit.SECONDS.sleep(10);
            } catch (InterruptedException e) {
                e.printStackTrace();
            } finally {
                lock.unlock();
            }
        },"A").start();

        new Thread(() -> {
            lock.lock();
            try {
                System.out.println("-----B Thread come in");
            }finally {
                lock.unlock();
            }
        },"B").start();


        new Thread(() -> {
            lock.lock();
            try {
                System.out.println("-----B Thread come in");
            }finally {
                lock.unlock();
            }
        },"C").start();
    }
}
```

> 只有一个线程A时（非公平锁）

![image-20211006165911505](F:\笔记图片\img\image-20211006165911505.png)

![image-20211006170120354](F:\笔记图片\img\image-20211006170120354.png)

![image-20211006170208935](F:\笔记图片\img\image-20211006170208935.png)

![image-20211006170248549](F:\笔记图片\img\image-20211006170248549.png)

```java
 final void lock() {
     //希望该对象内存偏移量的位置值为0，如果真实值和期望值一样，占用该锁，并将state改为1
            if (compareAndSetState(0, 1))
                //将占用当前锁的线程设置为当前线程
                setExclusiveOwnerThread(Thread.currentThread());
     
            else
                acquire(1);
        }


 protected final boolean compareAndSetState(int expect, int update) {
        // See below for intrinsics setup to support this
        return unsafe.compareAndSwapInt(this, stateOffset, expect, update);
    }


protected final void setExclusiveOwnerThread(Thread thread) {
        exclusiveOwnerThread = thread;
    }
```



> 当另一个线程B再次加入时

![image-20211006171528352](F:\笔记图片\img\image-20211006171528352.png)

![image-20211006171539128](F:\笔记图片\img\image-20211006171539128.png)

![image-20211006171548690](F:\笔记图片\img\image-20211006171548690.png)

![image-20211006171557333](F:\笔记图片\img\image-20211006171557333.png)

![image-20211006175403767](F:\笔记图片\img\image-20211006175403767.png)

![image-20211006175418038](F:\笔记图片\img\image-20211006175418038.png)

![image-20211006175431833](F:\笔记图片\img\image-20211006175431833.png)

![image-20211007101043746](F:\笔记图片\img\image-20211007101043746.png)

![image-20211007101707606](F:\笔记图片\img\image-20211007101707606.png)

![image-20211007101950425](F:\笔记图片\img\image-20211007101950425.png)

```java
public final void acquire(int arg) {
    if (!tryAcquire(arg) &&
        acquireQueued(addWaiter(Node.EXCLUSIVE), arg))
        selfInterrupt();
}

protected final boolean tryAcquire(int acquires) {
            return nonfairTryAcquire(acquires);
        }

//此时因为已经有一个线程抢占锁，所以该处acquires值为1
final boolean nonfairTryAcquire(int acquires) {
    		//当前线程
            final Thread current = Thread.currentThread();
    		//获取AQS中的state，此时资源被占用，state的值为1
            int c = getState();
            if (c == 0) {
                if (compareAndSetState(0, acquires)) {
                    setExclusiveOwnerThread(current);
                    return true;
                }
            }
    		//判断当前线程和目前持有锁的线程是否是同一个，持有锁的线程为A，当前线程B，显然不相				同
            else if (current == getExclusiveOwnerThread()) {
                //此处就比如A刚释放锁，然后又再一次抢到了该锁，（可重入锁）
                int nextc = c + acquires;
                if (nextc < 0) // overflow
                    throw new Error("Maximum lock count exceeded");
                setState(nextc);
                return true;
            }
            return false;
        }

//此时才代表B线程正式的入队
    private Node addWaiter(Node mode) {
        //Thread.currentThread():当前的线程B线程   
        Node node = new Node(Thread.currentThread(), mode);
        // Try the fast path of enq; backup to full enq on failure
        Node pred = tail; //尾节点
        //此时队列中尾节点指向为null
        if (pred != null) {
            node.prev = pred;
            if (compareAndSetTail(pred, node)) {
                pred.next = node;
                return node;
            }
        }
        //B进入队列
        enq(node);
        return node;
    }


private Node enq(final Node node) {
    	//自旋锁
        for (;;) {
            Node t = tail;
            //第一次进来尾节点为null，第二次进来尾节点指向傀儡节点，不为null
            if (t == null) { // 初始化
                //注意！！！此时队列的第一个节点并不是我们的B线程的Node，而是系统new出来的一					个空node，作为占位符
               //该头节点waitState = 0，Thread = null，是一个傀儡节点/哨兵节点，作用是占位
                if (compareAndSetHead(new Node()))
                    //尾节点也指向傀儡节点
                    tail = head;
            } else {
                //将当前线程添加到尾节点
                node.prev = t;
                //修改尾节点为当前的线程
                if (compareAndSetTail(t, node)) {
                    t.next = node;
                    return t;
                }
            }
        }
    }

//arg是请求的数量
final boolean acquireQueued(final Node node, int arg) {
        boolean failed = true;
        try {
            //是否被打断
            boolean interrupted = false;
            //自旋锁
            //假设第一次B获取锁失败，第二次获取锁仍旧失败
            for (;;) {
                //找上一个节点
                final Node p = node.predecessor();
                //头节点是哨兵节点
                //尝试获取锁，如果获取成功，就将当前节点设置为head
                if (p == head && tryAcquire(arg)) {
                    
                    setHead(node);
                    p.next = null; // help GC
                    failed = false;
                    return interrupted;
                }
                //获取锁失败，就需要阻塞了
                //shouldParkAfterFailedAcquire(p, node) 检查上一个节点的状态，如果是					SIGNAL就阻塞，如果不是就设置为SIGNAL
                
                if (shouldParkAfterFailedAcquire(p, node) &&
                    parkAndCheckInterrupt())
                    interrupted = true;
            }
        } finally {
            if (failed)
                cancelAcquire(node);
        }
    }


 private static boolean shouldParkAfterFailedAcquire(Node pred, Node node) {
     	//此时pred.waitStatus = 0
        int ws = pred.waitStatus;
     	//第二次进来ws = -1 = Node.SIGNAL,直接返回true
        if (ws == Node.SIGNAL)
            return true;
        if (ws > 0) {
            do {
                node.prev = pred = pred.prev;
            } while (pred.waitStatus > 0);
            pred.next = node;
        } else {
            //第一次将pred.waitStatus值变为了 -1 
            compareAndSetWaitStatus(pred, ws, Node.SIGNAL);
        }
     	//第一次返回false
        return false;
    }


private final boolean parkAndCheckInterrupt() {
    	//线程B在这里被阻塞
    	//到了这里B才算是真正的入队
        LockSupport.park(this);
        return Thread.interrupted();
    }
```

> 当第三个线程加入时（此时一共有三个线程）同样也是非公平锁为例

![image-20211007113354968](F:\笔记图片\img\image-20211007113354968.png)

![image-20211007113403077](F:\笔记图片\img\image-20211007113403077.png)

![image-20211007113431282](F:\笔记图片\img\image-20211007113431282.png)

![image-20211007113441208](F:\笔记图片\img\image-20211007113441208.png)

![image-20211007113512540](F:\笔记图片\img\image-20211007113512540.png)

![image-20211007113521325](F:\笔记图片\img\image-20211007113521325.png)

![image-20211007113716908](F:\笔记图片\img\image-20211007113716908.png)



![image-20211007114313740](F:\笔记图片\img\image-20211007114313740.png)

![image-20211007114337667](F:\笔记图片\img\image-20211007114337667.png)

![image-20211007114425612](F:\笔记图片\img\image-20211007114425612.png)

```java
public final void acquire(int arg) {
        if (!tryAcquire(arg) &&
            acquireQueued(addWaiter(Node.EXCLUSIVE), arg))
            selfInterrupt();
    }


//此时C的状态与B的一样，只有A线程抢到了锁，会返回false
final boolean nonfairTryAcquire(int acquires) {
    final Thread current = Thread.currentThread();
    int c = getState();
    if (c == 0) {
        if (compareAndSetState(0, acquires)) {
            setExclusiveOwnerThread(current);
            return true;
        }
    }
    else if (current == getExclusiveOwnerThread()) {
        int nextc = c + acquires;
        if (nextc < 0) // overflow
            throw new Error("Maximum lock count exceeded");
        setState(nextc);
        return true;
    }
    return false;
}


private Node addWaiter(Node mode) {
    	//此时该节点为C
        Node node = new Node(Thread.currentThread(), mode);
        // Try the fast path of enq; backup to full enq on failure
    	//设置尾节点为pred
        Node pred = tail;
    	//此时队列中有傀儡节点和B节点，尾节点不为空
        if (pred != null) {
            node.prev = pred;
            //设置当前线程C为尾节点
            if (compareAndSetTail(pred, node)) {
                pred.next = node;
                //返回
                return node;
            }
        }
        enq(node);
        return node;
    }


final boolean acquireQueued(final Node node, int arg) {
        boolean failed = true;
        try {
            boolean interrupted = false;
            for (;;) {
                final Node p = node.predecessor();
                //前一个节点为B B不是头节点
                if (p == head && tryAcquire(arg)) {
                    setHead(node);
                    p.next = null; // help GC
                    failed = false;
                    return interrupted;
                }
                //第一次返回false
                //第二次返回true，阻塞线程
                if (shouldParkAfterFailedAcquire(p, node) &&
                    parkAndCheckInterrupt())
                    interrupted = true;
            }
        } finally {
            if (failed)
                cancelAcquire(node);
        }
    }


private static boolean shouldParkAfterFailedAcquire(Node pred, Node node) {
        int ws = pred.waitStatus;
        if (ws == Node.SIGNAL)
            return true;
        if (ws > 0) {
            do {
                node.prev = pred = pred.prev;
            } while (pred.waitStatus > 0);
            pred.next = node;
        } else {
            compareAndSetWaitStatus(pred, ws, Node.SIGNAL);
        }
        return false;
    }



private final boolean parkAndCheckInterrupt() {
        LockSupport.park(this);
        return Thread.interrupted();
    }
```



#### unLock方法

![image-20211007152617864](F:\笔记图片\img\image-20211007152617864.png)

![image-20211007152640545](F:\笔记图片\img\image-20211007152640545.png)

![image-20211007152708452](F:\笔记图片\img\image-20211007152708452.png)

![image-20211007152721410](F:\笔记图片\img\image-20211007152721410.png)

![image-20211007154007216](F:\笔记图片\img\image-20211007154007216.png)

![image-20211007154112842](F:\笔记图片\img\image-20211007154112842.png)

![image-20211007154535806](F:\笔记图片\img\image-20211007154535806.png)

![image-20211007154623654](F:\笔记图片\img\image-20211007154623654.png)

![image-20211007155421844](F:\笔记图片\img\image-20211007155421844.png)

```java
public final boolean release(int arg) {
    	//返回true
        if (tryRelease(arg)) {
            Node h = head;
            if (h != null && h.waitStatus != 0)
                unparkSuccessor(h);
            return true;
        }
        return false;
    }

protected final boolean tryRelease(int releases) {
    //当前state = 1，releases = 1
    int c = getState() - releases;
    if (Thread.currentThread() != getExclusiveOwnerThread())
        throw new IllegalMonitorStateException();
    boolean free = false;
    if (c == 0) {
        free = true;
        //设置占用当前锁的线程为null
        setExclusiveOwnerThread(null);
    }
    //恢复状态位
    setState(c);
    //free = true
    return free;
}


private void unparkSuccessor(Node node) {
    int ws = node.waitStatus;  // -1
    if (ws < 0)
        //将傀儡节点的ws修改为0
        compareAndSetWaitStatus(node, ws, 0);
    Node s = node.next;  //指向B节点
    if (s == null || s.waitStatus > 0) {
        s = null;
        for (Node t = tail; t != null && t != node; t = t.prev)
            if (t.waitStatus <= 0)
                s = t;
    }
    if (s != null)
        //唤醒B节点
        LockSupport.unpark(s.thread);
}



private final boolean parkAndCheckInterrupt() {
    LockSupport.park(this);
    return Thread.interrupted();  //false
}



for (;;) {
    //此时node为B
    final Node p = node.predecessor();  //p为傀儡节点
    if (p == head && tryAcquire(arg)) {
        setHead(node);
        p.next = null; // help GC
        failed = false;
        return interrupted;
    }
    if (shouldParkAfterFailedAcquire(p, node) &&
        parkAndCheckInterrupt())
        interrupted = true;
}


protected final boolean tryAcquire(int acquires) {
    return nonfairTryAcquire(acquires);
	
}


final boolean nonfairTryAcquire(int acquires) {
    final Thread current = Thread.currentThread();
    // state = 0
    int c = getState();
    if (c == 0) {
       // 将state设置为1
        if (compareAndSetState(0, acquires)) {
            //设置当前占用线程为B
            setExclusiveOwnerThread(current);
            return true;
        }
    }
    else if (current == getExclusiveOwnerThread()) {
        int nextc = c + acquires;
        if (nextc < 0) // overflow
            throw new Error("Maximum lock count exceeded");
        setState(nextc);
        return true;
    }
    return false;
}

//将当前节点打造为新的傀儡/头节点
private void setHead(Node node) {
    head = node;
    node.thread = null;
    node.prev = null;
}
```



![image-20211007160000090](F:\笔记图片\img\image-20211007160000090.png)

![image-20211007160055653](F:\笔记图片\img\image-20211007160055653.png)

![image-20211007160148613](F:\笔记图片\img\image-20211007160148613.png)

