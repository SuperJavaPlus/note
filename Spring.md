## 1.Spring

spring理念：使现有的技术更加容易使用，本身是一个大杂烩，整合了现有的技术框架

官网：https://spring.io/projects/spring-framework

GitHup地址：https://github.com/spring-projects/spring-framework



```xml
<dependency>
    <groupId>org.springframework</groupId>
    <artifactId>spring-webmvc</artifactId>
    <version>5.3.9</version>
</dependency>

<dependency>
    <groupId>org.springframework</groupId>
    <artifactId>spring-jdbc</artifactId>
    <version>5.3.9</version>
</dependency>
```



### 1.优点：

- Spring是开源的免费的框架（容器）
- SPring是轻量级的，非入侵式的框架
- 控制反转（IOC），面向切面编程(AOP)
- 支持事务的处理
- 对框架整合的支持

**总结一句话：Sping就是一个轻量级的控制反转和面向切面编程的框架**



### 2.组成

![image-20210830104520208](F:\笔记图片\img\image-20210830104520208.png)

### 3.拓展

- Spring Boot
  - 一个快速开发的脚手架
  - 基于SpringBoot可以快速的开发单个服务
  - 约定大于配置
- Spring Cloud
  - 基于SpringBoot实现的

Spring弊端：**配置十分繁琐**



## 2.IOC理论推导

1. UserDao  接口
2. UserDaoImpl 实现类
3. UserService 业务接口
4. UserServiceImpl 业务实现类

在我们之前的业务中，用户的需求可能会影响我们原来的代码，我们需要根据用户的需求去修改代码如果程序代码量十分大，修改一次的成分代价十分昂贵



我们使用一个Set接口

```java
//利用set进行动态实现值的注入
public void setUserDao(UserDao userDao){
    this.userDao = userDao;
}
```

- 之前，程序是主动创建对象，控制权在程序员手上
- 使用了set注入后，程序不在具有主动性，而是变成了被动的接收对象

这种思想，从本质上解决了问题，程序员不用再去管理对象的创建了。系统的耦合性大大降低这是IOC的原型！



**IOC本质**

控制反转是一种设计思想，DI(依赖注入)是实现IOC的一种方法。没有IOC的程序中，我们使用面向对象编程，对象的创建与对象间的依赖关系完全硬编码在程序中，对象的创建由程序自己控制，控制反转后将对象的创建转移给第三方。



**控制反转是一种通过描述（XML或注解）并通过第三方去生产或获取特定对象的方式。在Spring中实现控制反转的是ioC容器，其实现方法时依赖注入（Denpendency Injection,DI）**



## 3.第一个Spring

**创建实体类**

```java
public class Hello {
    private String str;

    public String getStr() {
        return str;
    }

    public void setStr(String str) {
        this.str = str;
    }

    @Override
    public String toString() {
        return "Hello{" +
                "str='" + str + '\'' +
                '}';
    }
}

```

**编写beans.xml**

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
        http://www.springframework.org/schema/beans/spring-beans.xsd">

    <!-- 使用Spring来创建对象，在Spring中这些都称为Bean
         类型 变量名 = new 类型
         id = 变量名
         class = new的对象
         property：相当于给对象中的属性设置一个值
    -->
     
    <bean id="hello" class="com.xiong.pojo.Hello">
        <!--ref:引用spring容器中创建好的对象-->
        <!--value:具体的值，基本数据类型-->
        <property name="str" value="string"/>
    </bean>
</beans>
```

**测试类**

```java
public class MyTest {
    public static void main(String[] args) {
        //获取ApplicationContext:拿到Spring的容器
        ApplicationContext context = new ClassPathXmlApplicationContext("beans.xml");
        //我们的对象现在都在Spring中管理，我们要使用，直接去里面取出来就可以
        Hello hello = (Hello) context.getBean("hello");
        System.out.println(hello.toString());
    }
}
```



**到了现在，我们彻底不用在程序中改动了，要实现不同的操作，只需要在xml配置文件中进行修改，所谓的IoC,一句话搞定：Spring来创建，管理，装配**



## 4.IoC对象创建的方式

1. 使用无参构造创建对象，默认

2. 假设我们要使用有参构造创建对象

   1. 下标赋值

      ```xml
      <!-- 下标赋值-->
      <bean id="user" class="com.xiong.pojo.User">
         <constructor-arg index="0" value="张三"/>
      </bean>
      ```

   2. 通过类型创建

      ```xml
      <!--不建议使用-->
      <bean id="user" class="com.xiong.pojo.User">
          <constructor-arg type="java.lang.String" value="李四"/>
      </bean>
      ```

   3. 通过参数名来设置

      ```xml
      <bean id="user" class="com.xiong.pojo.User">
          <constructor-arg name="name" value="王五"/>
      </bean>
      ```



总结：在配置文件加载的时候，容器中管理的对象就已经初始化了





## 5.Spring配置说明

### 1.别名

```xml
<!--如果添加了别名，我们也可以使用别名获取到这个对象-->
<alias name="" alias=""/>
```

### 2.Bean的配置

```xml
<!--
    id:bean的唯一标识符,也就是相当于我们学过的对象名
    class:bean对象所对应的权限类名
    name: 就是别名,可以同时取多个别名
-->
<bean id="userT" class="com.xiong.pojo.UserT" name="t,u2 u3;u8">
    <property name="name" value="郑小熊"/>
</bean>
```

### 3.Import

这个import一般用于团队开发，它可以将多个配置文件导入合并为一个

假设现在项目中有多个人开发，不同的人负责不同类的开发，不同的类需要注册在不同的bean中，我们可以利用import将所有人的beans.xml合并为一个总的，使用时直接使用总的

![image-20210830160922705](F:\笔记图片\img\image-20210830160922705.png)

**applicationContextBeans**

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
        http://www.springframework.org/schema/beans/spring-beans.xsd">

        <import resource="beans.xml"/>
        <import resource="beans1.xml"/>
        <import resource="beans2.xml"/>

</beans>
```



## 6.DI依赖注入

### 1.构造器注入

前面已经说过

### 2.Set注入

- 依赖注入：set注入！
  - 依赖：bean对象的创建依赖于容器
  - 注入：bean对象的所有属性，有容器注入



环境搭建

1. 复杂类型

   ```java
   public class Address {
       private String address;
   
       public String getAddress() {
           return address;
       }
   
       public void setAddress(String address) {
           this.address = address;
       }
   }
   ```

   ```java
   public class Student {
       private String name;
       private Address address;
       private String[] nook;
       private List<String> hobby;
       private Map<String,String> card;
       private Set<String> games;
       private Properties info;
       private String gf;
   
       public String getName() {
           return name;
       }
   
       public void setName(String name) {
           this.name = name;
       }
   
       public Address getAddress() {
           return address;
       }
   
       public void setAddress(Address address) {
           this.address = address;
       }
   
       public String[] getNook() {
           return nook;
       }
   
       public void setNook(String[] nook) {
           this.nook = nook;
       }
   
       public List<String> getHobby() {
           return hobby;
       }
   
       public void setHobby(List<String> hobby) {
           this.hobby = hobby;
       }
   
       public Map<String, String> getCard() {
           return card;
       }
   
       public void setCard(Map<String, String> card) {
           this.card = card;
       }
   
       public Set<String> getGames() {
           return games;
       }
   
       public void setGames(Set<String> games) {
           this.games = games;
       }
   
       public Properties getInfo() {
           return info;
       }
   
       public void setInfo(Properties info) {
           this.info = info;
       }
   
       public String getGf() {
           return gf;
       }
   
       public void setGf(String gf) {
           this.gf = gf;
       }
   
       @Override
       public String toString() {
           return "Student{" +
                   "name='" + name + '\'' +
                   ", address=" + address +
                   ", nook=" + Arrays.toString(nook) +
                   ", hobby=" + hobby +
                   ", card=" + card +
                   ", games=" + games +
                   ", info=" + info +
                   ", gf='" + gf + '\'' +
                   '}';
       }
   }
   ```

2. 真实测试对象

   ```java
   public class Student {
       private String name;
       private Address address;
       private String[] nook;
       private List<String> hobby;
       private Map<String,String> card;
       private Set<String> games;
       private Properties info;
       private String gf;
       }
   ```

3. beans.xml

   ```xml
   <?xml version="1.0" encoding="UTF-8"?>
   <beans xmlns="http://www.springframework.org/schema/beans"
          xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
          xsi:schemaLocation="http://www.springframework.org/schema/beans
           http://www.springframework.org/schema/beans/spring-beans.xsd">
       <bean id="address" class="com.xiong.pojo.Address">
           <property name="address" value="成都"/>
       </bean>
       <bean id="student" class="com.xiong.pojo.Student">
           <!--普通值注入,value-->
           <property name="name" value="郑小熊"/>
           <!--bean注入，ref-->
           <property name="address" ref="address"/>
   
           <!--数组注入-->
           <property name="books">
               <array>
                   <value>红楼梦</value>
                   <value>西游记</value>
                   <value>三国演义</value>
                   <value>水浒传</value>
               </array>
           </property>
   
           <!--list注入-->
           <property name="hobby">
               <list>
                   <value>敲代码</value>
                   <value>听歌</value>
                   <value>玩游戏</value>
                   <value>打篮球</value>
               </list>
           </property>
   
           <!--map注入-->
           <property name="card">
               <map>
                   <entry key="身份证" value="14111111111111111"/>
                   <entry key="银行卡" value="9155415155454545"/>
               </map>
           </property>
   
           <!--set注入-->
           <property name="games">
               <set>
                   <value>LOL</value>
               </set>
           </property>
   
           <!--null-->
           <property name="gf" >
               <null/>
           </property>
   
           <!--properties-->
           <property name="info">
               <props>
                   <prop key="学号">20180999</prop>
                   <prop key="性别">男</prop>
               </props>
           </property>
       </bean>
   
   </beans>
   ```

4. 测试类

   ```java
   public class MyTest {
       @Test
       public void test(){
           ClassPathXmlApplicationContext context = new ClassPathXmlApplicationContext("beans.xml");
           Student student = (Student)context.getBean("student");
           System.out.println(student.toSring());
       }
   }
   ```

### 3.拓展方式注入

我们可以使用p命名控件和c命名空间进行注入



```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:p="http://www.springframework.org/schema/p"
       xmlns:c="http://www.springframework.org/schema/c"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
        http://www.springframework.org/schema/beans/spring-beans.xsd">
    <!--p命名空间注入，可以直接注入属性的值 properties-->
    <bean id="user" class="com.xiong.pojo.User" p:age="12" p:name="张三"/>

    <!--c命名空间注入，可以通过构造器注入 constructor-->
    <bean id="user2" class="com.xiong.pojo.User" c:age="18" c:name="李四"/>
</beans>
```



注意点：p命名和c命名空间不能直接使用，需要导入约束

```xml
xmlns:p="http://www.springframework.org/schema/p"
xmlns:c="http://www.springframework.org/schema/c"
```



### 4.bean的作用域

| Scope                                                        | Description                                                  |
| :----------------------------------------------------------- | :----------------------------------------------------------- |
| [singleton](https://docs.spring.io/spring-framework/docs/5.2.0.RELEASE/spring-framework-reference/core.html#beans-factory-scopes-singleton) | （默认）将单个 bean 定义范围限定为每个 Spring IoC 容器的单个对象实例。 |
| [prototype](https://docs.spring.io/spring-framework/docs/5.2.0.RELEASE/spring-framework-reference/core.html#beans-factory-scopes-prototype) | 将单个 bean 定义范围限定为任意数量的对象实例。               |
| [request](https://docs.spring.io/spring-framework/docs/5.2.0.RELEASE/spring-framework-reference/core.html#beans-factory-scopes-request) | 将单个 bean 定义范围限定为单个 HTTP 请求的生命周期。也就是说，每个 HTTP 请求都有自己的 bean 实例，该 bean 实例是在单个 bean 定义的后面创建的。仅在 web-aware Spring 的上下文中有效`ApplicationContext`。 |
| session                                                      | 将单个 bean 定义范围限定为 HTTP 的生命周期`Session`。仅在 web-aware Spring 的上下文中有效`ApplicationContext`。 |
| [application](https://docs.spring.io/spring-framework/docs/5.2.0.RELEASE/spring-framework-reference/core.html#beans-factory-scopes-application) | 将单个 bean 定义范围限定为`ServletContext`. 仅在 web-aware Spring 的上下文中有效`ApplicationContext`。 |
| [websocket](https://docs.spring.io/spring-framework/docs/5.2.0.RELEASE/spring-framework-reference/web.html#websocket-stomp-websocket-scope) | 将单个 bean 定义范围限定为`WebSocket`. 仅在 web-aware Spring 的上下文中有效`ApplicationContext`。 |

1. 单例模式（singleton）(Spring默认机制)

   ![image-20210831091200055](F:\笔记图片\img\image-20210831091200055.png)

```xml
<bean id="user2" class="com.xiong.pojo.User" c:age="18" c:name="李四" scope="singleton"/>
```

2. 原型模式：**每次从容器中get的时候都会产生一个新对象**

   ![image-20210831091141941](F:\笔记图片\img\image-20210831091141941.png)

```xml
<bean id="accountService" class="com.something.DefaultAccountService" scope="prototype"/>
```

3. 其余的request，session，application  这些只能在web开发中使用到





## 7.Bean的自动装配

- 自动装配是Spring满足bean依赖的一种方式
- Spring会在上下文中自动寻找，并自动给bean装配属性



在Spring中有三种自动装配的方式：

1. 在xml中显示的配置
2. 在java中显示配置
3. 隐式的自动装配【重要】



### 1.测试

1. 环境搭建

   比如一个人有两个宠物



### 2.ByName

```xml
<bean id="cat" class="com.xiong.pojo.Cat"/>
<bean id="dog" class="com.xiong.pojo.Dog"/>
<!--
byName:会自动在容器上下文中查找和自己对象set方法 后面的值对应的 beanid
-->
<bean id="person" class="com.xiong.pojo.Person" autowire="byName">
    <property name="name" value="郑小熊~"/>
</bean>
```

### 3.ByType自动装配

```xml
<bean id="cat" class="com.xiong.pojo.Cat"/>
<bean id="dog" class="com.xiong.pojo.Dog"/>
<!--byType:会自动在容器上下文中查找和自己对象属性类型相同的bean
    -->
<bean id="person" class="com.xiong.pojo.Person" autowire="byType">
    <property name="name" value="郑小熊~"/>
</bean>
```



小结：

- byname需要保证所有bean的id唯一，并且这个需要和自动注入的属性的set方法的值一致

- bytype需要保证bean的class唯一，并且这个bean需要和自动注入的属性的值一致



### 4.使用注解实现自动装配

要使用注解须知：

1. 导入约束  context约束
2. 配置注解的支持  :context:annotation-config </beans>

```xml
<?xml version="1.0" encoding="UTF-8"?>** <beans xmlns="http://www.springframework.org/schema/beans"    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"    xmlns:context="http://www.springframework.org/schema/context"    xsi:schemaLocation="http://www.springframework.org/schema/beans        https://www.springframework.org/schema/beans/spring-beans.xsd        http://www.springframework.org/schema/context        https://www.springframework.org/schema/context/spring-context.xsd">     <context:annotation-config/> </beans>
```



#### @Autowired

直接在属性上使用即可，也可以在set方式上使用

使用@Autowired我们可以不用编写Set方法，前提是你这个自动装配的属性在IOC（Spring）容器中存在且符合名字byname

`科普：`

@Nullable:字段标记了这个注解，说明这个字段可以为null

```java
public @interface Autowired {
    boolean required() default true;
}


//如果显示定义了 Autowired的required的属性为false，说明这个对象可以为null
```



测试代码

```java
public class Person {
    @Autowired
    private Cat cat;
    @Autowired
    private Dog dog;
    private String name;
}
```

如果@Autowired自动装配的环境比较复杂自动装配无法通过一个注解完成的时候，我们可以使用@Qualifier(value = "xxx")配合@Autowired使用，指定唯一的bean对象注入

```xml
<bean id="cat111" class="com.xiong.pojo.Cat"/>
<bean id="cat222" class="com.xiong.pojo.Cat"/>
<bean id="dog111" class="com.xiong.pojo.Dog"/>
<bean id="dog222" class="com.xiong.pojo.Dog"/>
<bean id="person" class="com.xiong.pojo.Person"/>
```

```java
public class Person {
    @Autowired
    @Qualifier(value = "cat111")
    private Cat cat;
    @Autowired
    @Qualifier(value = "dog111")
    private Dog dog;
    private String name;
}
```



@Resource

```java
public class Person {
   @Resource(name = "cat111")
    private Cat cat;
   
   @Resource(name = "dog222")
    private Dog dog;
    private String name;
}
```

小结

@Resource和@Autowired的区别

- 都是用来自动装配的，都可以放在属性字段上
- @Autowired注解由Spring提供，只按照byType注入
- @resource注解由J2EE提供，默认按照byName自动注入。
- @Autowired默认按类型进行装配，@Resource默认按照名称进行装配。



## 8.使用注解开发

在Spring4之后，要使用注解开发，必须要保证aop的包导入了

使用注解需要导入context约束，增加注解的支持





`@Component`

组件，放在类上，说明这个类被Spring管理了，就是Bean

```java
@Component
public class User {
    public String name = "张三";
}
```

```xml
<!--指定要扫描的包，这个包下的注解就会生效-->
<context:component-scan base-package="com.xiong.pojo"/>
<context:annotation-config/>
```

> 属性如何注入

```java
@Component
public class User {
    //相当于<property name = "name" value = "xxx"/>
    @Value("xxxx")
    public String name;
}
```

> 衍生的注解

@Component有几个衍生的注解，我们在web开发中，会按照mvc三层架构分层

- dao:【@Repository】

- service【@Service】

- controller【@Controller】

  这四个注解功能都是一样的，都是代表将某个类注册到Spring中，装配bean

> 作用域

@Scope



小结：

xml与注解：

- xml更加万能，适用于任何场合，维护简单方便
- 注解不是自己的类使用不了，维护相当复杂

最佳实践：xml用来管理bean,注解只负责完成属性的注入



## 9.使用java的方式配置Spring

我们现在要完全不使用Spring的xml的配置了，全权交给java来做

JavaConfig是Spring的一个子项目，在Spring4之后，他成为了一个核心功能



实体类

```java
@Component
public class User {
    private String name;

    public String getName() {
        return name;
    }

    @Value("张三")
    public void setName(String name) {
        this.name = name;
    }

    @Override
    public String toString() {
        return "User{" +
                "name='" + name + '\'' +
                '}';
    }
}
```

配置文件

```java
//代表这是一个配置类，就和我们之前看的beans.xml一样
@Configuration
@ComponentScan("com.xiong.pojo")
public class Config {

    //注册一个bean,就相当于我们之前写的一个bean标签
    //这个方法的名字就相当于bean标签中的id属性
    //这个方法的返回值就相当于bean标签中的class属性
    @Bean
    public User getUser(){
        return new User();
    }
}
```

测试类

```java
public class MyTest {
    @Test
    public void test(){
        //如果完全使用了配置类方式去做，我们就只能通过 AnnotationConfig上下文来获取容器，通过配置类的class对象加载！
        AnnotationConfigApplicationContext context = new AnnotationConfigApplicationContext(Config.class);
        User getUser = context.getBean("getUser", User.class);
        System.out.println(getUser.getName());
    }
}
```



## 10.代理模式

为什么要学习代理模式？因为这就是springAOP的底层【SpringAOP和SpringMVC】

代理模式的分类：

- 静态代理
- 动态代理

![image-20210831150806307](F:\笔记图片\img\image-20210831150806307.png)

### 1.静态代理

角色分析：

- 抽象的角色：一般会使用接口或者抽象类
- 真实角色：被代理的角色
- 代理角色：代理真实角色，代理真实角色后，我们一般会做一些附属操作
- 客户：访问代理对象的人



> 步骤

1. 接口

   ```java
   public interface Rent {
       public void rent();
   }
   ```

2. 真实角色

   ```java
   public class Host implements Rent{
   
       public void rent() {
           System.out.println("房东要出租房子");
       }
   }
   ```

3. 代理角色

   ```java
   public class Proxy implements Rent{
       private Host host;
   
       public Proxy() {
       }
   
       public Proxy(Host host) {
           hetong();
           this.host = host;
           fare();
       }
   
       public void rent() {
           host.rent();
       }
   
       public void hetong(){
           System.out.println("签合同");
       }
   
       public void fare(){
           System.out.println("收中介费");
       }
   }
   ```

4. 客户端访问代理角色

   ```java
   public class Client {
       public static void main(String[] args) {
           //房东要租房子
           Host host = new Host();
           //中介帮房东租房子,代理角色一般会有附属操作
           Proxy proxy = new Proxy(host);
   
           proxy.rent();
       }
   }
   ```

代理模式的好处：

- 可以使真是角色的操作更加纯粹，不用再去关注一些公共的业务
- 公共业务交给了代理角色，实现了业务的分工
- 公共业务发生扩展的时候方便集中管理

缺点：

- 一个真实角色会产生一个代理对象，代码量翻倍，开发效率变低



### 2.又一个静态代理的实例

```java
//接口
public interface UserService {
     void add();
     void delete();
     void update();
     void query();
}
```

```java
//真实对象
public class UserServiceImpl implements UserService{
    public void add() {
        System.out.println("增加了一个用户");
    }

    public void delete() {
        System.out.println("删除了一个用户");
    }

    public void update() {
        System.out.println("更新了一个用户");
    }

    public void query() {
        System.out.println("查找了一个用户");
    }
}
```

```java
//代理对象
public class UserServiceProxy implements UserService{
    private UserServiceImpl service;

    public void setService(UserServiceImpl service){
        this.service = service;
    }

    public void add() {
        log("add");
        service.add();
    }

    public void delete() {
        log("delete");
        service.delete();
    }

    public void update() {
        log("update");
        service.update();
    }

    public void query() {
        log("query");
        service.query();
    }

    //增加一个日志打印的方法
    public void log(String msg){
        System.out.println("使用了" + msg + "方法");
    }
}
```

```java
public class Client {
    public static void main(String[] args) {
        UserServiceImpl userService = new UserServiceImpl();

        UserServiceProxy userServiceProxy = new UserServiceProxy();
        userServiceProxy.setService(userService);
        userServiceProxy.add();
    }
}

```



聊聊AOP

![image-20210831161537510](F:\笔记图片\img\image-20210831161537510.png)



### 3.动态代理

- 动态代理和静态代理角色一样
- 动态代理的类是动态生成的，不是我们直接写好的
- 动态代理分为两大类：基于接口的动态代理，基于类的动态代理
  - 基于接口----->JDK动态代理
  - 基于类------>cglib
  - java字节码实现：javasist

需要了解两个类：Proxy 代理,InvocationHandler：调用处理程序



要想实现动态代理需要解决的问题？

1. 如何根据加载到内存中的被代理类，动态的创建一个代理类及其对象

2. 当通过代理类的对象调用方法时，如何动态的调用被代理类的同名方法

```java
public class ProxyTest {

}
class ProxyFactory{

    //解决如何根据加载到内存中的被代理类，动态的创建一个代理类及其对象
    //调用此方法，返回一个代理类的对象
    public static Object getProxyInstance(Object obj){//obj:被代理类的对象
        MyInvocationHandler handler = new MyInvocationHandler();
        handler.bind(obj);
        return Proxy.newProxyInstance(obj.getClass().getClassLoader(),obj.getClass().getInterfaces(),handler);
    }
}
class MyInvocationHandler implements InvocationHandler{

    private Object obj;//赋值时，也需要使用被代理类的对象赋值
    public void bind(Object obj){
        this.obj = obj;
    }
    //当我们通过代理类的对象，调用方法a时，就会自动的调用如下的方法invoke()
    //将被代理类要执行的方法a的功能就声明在invoke()方法中

    //Object proxy：代理类的对象
    public Object invoke(Object proxy, Method method, Object[] args) throws Throwable {
        //method即为代理类对象调用的方法，此方法也就作为被代理类调用的方法
        //obj:被代理类的对象
        Object returnValue = method.invoke(obj,args);
        //上述方法的返回值就作为invoke方法中的返回值
        return returnValue;
    }

    public static void main(String[] args) {
        Host host = new Host();
        Rent proxyInstance =(Rent) ProxyFactory.getProxyInstance(host);
        //当我们通过代理类对象调用方法时，会自动地调用被代理类中同名的方法
        proxyInstance.rent();

    }
}
```



## 11.AOP

### 1.什么是AOP

AOP意为：面向切面编程，通过预编译方式和运行期间动态代理实现程序功能的统一维护的一种技术。AOP时OOP的后续，是软件开发中的一个热点，也是Spring框架中的要给重要内容，是函数式编程的一种厌衍生泛型。利用AOP可以对业务逻辑的各个部分进行隔离，从而使得业务逻辑各部分之间的耦合度降低，提高程序的可重用性，同时提高了开发效率



### 2.AOP在Spring中的作用

提供声明事务；允许用户自定义切面



### 3.使用Spring实现AOP

使用AOP织入我们需要导入一个依赖包

```xml
<dependencies>
    <dependency>
        <groupId>org.aspectj</groupId>
        <artifactId>aspectjweaver</artifactId>
        <version>1.9.6</version>
    </dependency>
</dependencies>
```



方式一：使用Spring接口

1. 实体类

```java
public interface UserService {
    void add();
    void delete();
    void update();
    void query();
}
```

```java
public class UserServiceImpl implements UserService{
    public void add() {
        System.out.println("增加了一个用户");
    }

    public void delete() {
        System.out.println("删除了一个用户");
    }

    public void update() {
        System.out.println("更新了一个用户");
    }

    public void query() {
        System.out.println("查找了一个用户");
    }
}
```

```java
public class Log implements MethodBeforeAdvice {

    //method要执行:要执行的目标对象的方法
    //objects：参数
    //target:目标对象
    public void before(Method method, Object[] args, Object target) throws Throwable {
        System.out.println(target.getClass().getName() + "的" + method.getName() + "被执行了");
    }

}
```

```java
public class AfterLog implements AfterReturningAdvice{

    public void afterReturning(Object returnValue, Method method, Object[] args, Object target) throws Throwable {
        System.out.println("使用了" + method.getName() + "方法，返回值为" + returnValue);
    }
}
```

2. 配置xml

   ```xml
   <?xml version="1.0" encoding="UTF-8"?>
   <beans xmlns="http://www.springframework.org/schema/beans"
          xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
          xmlns:aop="http://www.springframework.org/schema/aop"
          xsi:schemaLocation="http://www.springframework.org/schema/beans
           http://www.springframework.org/schema/beans/spring-beans.xsd
           http://www.springframework.org/schema/aop
           http://www.springframework.org/schema/aop/spring-aop.xsd">
       <!--注册bean-->
       <bean id="userService" class="com.xiong.service.UserServiceImpl"/>
       <bean id="afterlog" class="com.xiong.log.AfterLog"/>
       <bean id="log" class="com.xiong.log.Log"/>
   
   <!--    方式一：使用原生的spring AOP接口-->
       <!--配置AOP 需要导入AOP相关的约束-->
       <aop:config>
           <!--切入点         execution(修饰词，返回值，类名，方法名，参数)-->
           <aop:pointcut id="pointcut" expression="execution(* com.xiong.service.UserServiceImpl.*(..))"/>
   
           <!--执行环绕增加！-->
           <aop:advisor advice-ref="log" pointcut-ref="pointcut"/>
           <aop:advisor advice-ref="afterlog" pointcut-ref="pointcut"/>
       </aop:config>
   
   </beans>
   ```



方式二：使用自定义类来实现

```java
public class PointDiy {
    public void before(){
        System.out.println("执行之前======================");
    }
    public void after(){
        System.out.println("执行之后======================");
    }
}
```

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:aop="http://www.springframework.org/schema/aop"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
        http://www.springframework.org/schema/beans/spring-beans.xsd
        http://www.springframework.org/schema/aop
        http://www.springframework.org/schema/aop/spring-aop.xsd">
    <!--注册bean-->
    <bean id="userService" class="com.xiong.service.UserServiceImpl"/>
    <bean id="afterlog" class="com.xiong.log.AfterLog"/>
    <bean id="log" class="com.xiong.log.Log"/>
        <bean id="pointdiy" class="com.xiong.diy.PointDiy"/>

    <aop:config>
        <!--自定义切面，ref要引用的类-->
        <aop:aspect ref="pointdiy">
            <!--切入点-->
            <aop:pointcut id="pointcut" expression="execution(* com.xiong.service.UserServiceImpl.*(..))"/>
            <!--通知-->
            <aop:before method="before" pointcut-ref="pointcut"/>
            <aop:after method="after" pointcut-ref="pointcut"/>
        </aop:aspect>
    </aop:config>
</beans>
```



方式三：使用注解配置

```java
//使用注解方式实现AOP
@Aspect //标注这个类是一个切面
public class AnnotationPointCut {
    @Before("execution(* com.xiong.service.UserServiceImpl.*(..))")
    public void before(){
        System.out.println("执行之前======================");
    }
    @After("execution(* com.xiong.service.UserServiceImpl.*(..))")
    public void after(){
        System.out.println("执行之后======================");
    }
    //在环绕增强中我们可以给定一个参数，代表我们要获取处理切入的点
    @Around("execution(* com.xiong.service.UserServiceImpl.*(..))")
    public void around(ProceedingJoinPoint jp) throws Throwable {
        System.out.println("环绕前");
        //执行方法
        jp.proceed();
        System.out.println("环绕后");
    }
}
```

测试

```java
public class MyTest {
    @Test
    public void test(){
        ClassPathXmlApplicationContext context = new ClassPathXmlApplicationContext("springapplicationContext.xml");

        //动态代理代理的是接口
        UserService service = context.getBean("userService", UserService.class);
        service.add();
    }
}
```

结果：

![image-20210901114321845](F:\笔记图片\img\image-20210901114321845.png)



## 12.整合Mybatis

步骤：

1. 导入相关的jar包
2. 编写配置文件
3. 测试



### 1.方式一：使用 SqlSession

1.编写数据源

```xml
<!--DataSource:使用spring的数据源替换mybatis的配置
    我们这里使用Spring提供的JDBC
-->
<bean id="dataSource" class="org.springframework.jdbc.datasource.DriverManagerDataSource">
    <property name="driverClassName" value="com.mysql.jdbc.Driver"/>
    <property name="url" value="jdbc:mysql://localhost:3306/mybatis"/>
    <property name="username" value="root"/>
    <property name="password" value="123456"/>
</bean>
```

2.sqlSessionFactory

```xml
<!--sqlSessionFactory-->
<bean id="sqlSessionFactory" class="org.mybatis.spring.SqlSessionFactoryBean">
    <property name="dataSource" ref="dataSource"/>
    <!--绑定mybatis配置文件-->
        <!--mybatis配置文件的地址-->
    <property name="configLocation" value="classpath:mybatis-config.xml"/>
    <property name="mapperLocations" value="classpath:com/xiong/mapper/*.xml"/>
</bean>
```

3.sqlSessionTemplate

```xml
<!--SqlSessionTemplate: 就是我们使用的SqlSession-->
<bean id="sqlSession" class="org.mybatis.spring.SqlSessionTemplate">
    <!--只能用构造器注入sqlSessionFactory，因为它没有set方法-->
    <constructor-arg index="0" ref="sqlSessionFactory"/>
</bean>
```

4.需要给接口加实现类

```java
public class UserMapperImpl implements UserMapper{

    //我们的所有操作都是用sqlSessionTemplate执行；
    private SqlSessionTemplate sqlSession;

    public void setSqlSession(SqlSessionTemplate sqlSession) {
        this.sqlSession = sqlSession;
    }

    public List<User> queryUser() {
        UserMapper mapper = sqlSession.getMapper(UserMapper.class);
        return mapper.queryUser();

    }
}
```

5.将自己写的实现类注入到Spring中

```xml
<bean id="usermapperimpl" class="com.xiong.mapper.UserMapperImpl">
    <property name="sqlSession" ref="sqlSession"/>
</bean>
```

6.测试使用即可

```java
public class MyTest {
    @Test
    public void test() {
        ApplicationContext context = new ClassPathXmlApplicationContext("spring-dao.xml");
        UserMapper usermapper = context.getBean("usermapperimpl", UserMapper.class);
        for (User user : usermapper.queryUser()) {
            System.out.println(user);
        }
    }
}
```



## 2.方式二：SqlSessionDaoSupport

```java
/*
不同于第一种方法的第4,5步，该类继承了SqlSessionDaoSupport
*/

public class UserMapperImpl2 extends SqlSessionDaoSupport implements UserMapper {
    public List<User> queryUser() {
        return getSqlSession().getMapper(UserMapper.class).queryUser();
    }
}

/*
将该类注入到bean中
<bean id = "userMapperImpl2" class = "com.xiong.mapper.UserMapperImpl">
    <property name = "sqlSessionFactory" ref = "sqlSessionFactory"/>
</bean>
*/
```



## 13.声明式事务

### 1.回顾事务

- 要么都成功，要么都失败
- 确保完整性和一致性



事务的ACID原理

- 原子性
- 一致性
- 隔离性
  - 多个业务可能操作同一个资源，防止数据损坏
- 持久性
  - 事务一旦提交，无论系统发生什么问题，结果都不会被影响，被持久化的写道存储器中



### 2.spring中的事务管理

- 声明式事务：AOP
- 编程式事务：需要在代码中，进行事务的管理

```xml
 <!--配置声明式事务-->
    <bean id="transactionManager" class="org.springframework.jdbc.datasource.DataSourceTransactionManager">
        <constructor-arg ref="dataSource" />
    </bean>
    <!--结合AOP实现事务的织入-->

    <!--配置事务通知-->
    <tx:advice id="txAdvice" transaction-manager="transactionManager">
        <!--给哪些方法配置事务-->
        <tx:attributes>
            <tx:method name="*" propagation="REQUIRED"/>
        </tx:attributes>
    </tx:advice>

<!----------------------以上为配置声明式事务的固定写法---------------------------->

    <!--配置事务切入-->
    <aop:config>
        <aop:pointcut id="pointcut" expression="execution(* com.xiong.mapper.*.*(..))"/>
        <aop:advisor advice-ref="txAdvice" pointcut-ref="pointcut"/>
    </aop:config>
</beans>
```



为什么需要事务？

- 如果不配置事务，可能存在数据提交不一致的情况
- 如果我们不在Spring中去配置声明式事务，我们就需要在代码中手动配置事务
- 事务在项目的开发中十分重要，涉及到数据的一致性和完整性

