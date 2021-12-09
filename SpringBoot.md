## 1.SpringBoot

Spring Boot 以**约定大于配置的核心思想**，默认帮我们进行了很多设置，多数 Spring Boot 应用只需要很少的 Spring 配置。同时它集成了大量常用的第三方库配置（例如 Redis、MongoDB、Jpa、RabbitMQ、Quartz 等等），Spring Boot 应用中这些第三方库几乎可以零配置的开箱即用。



**SpringBoot的主要优点：**

- 为所有的Spring开发者更快的入门
- 开箱即用，提供各种默认配置来简化项目配置
- 内嵌式容器简化Web项目
- 没有冗余代码生成和XML配置



**什么是微服务？**

微服务是一种架构风格，他要求我们在开发一个应用的时候，这个应用必须构建成一系列小服务的组合，可以通过http方式进行互通。



## 2.第一个SpringBoot程序

- 可以在官网直接下载后，导入idea开发
- 直接使用idea创建一个springboot项目



> pom.xml

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 https://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>
    <!--父依赖-->
    <parent>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-parent</artifactId>
        <version>2.5.4</version>
        <relativePath/> <!-- lookup parent from repository -->
    </parent>
    <groupId>com.xiong</groupId>
    <artifactId>springboot-01-helloworld</artifactId>
    <version>0.0.1-SNAPSHOT</version>
    <name>springboot-01-helloworld</name>
    <description>Demo project for Spring Boot</description>
    <properties>
        <java.version>1.8</java.version>
    </properties>
    <dependencies>
        <!--web场景启动器-->
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
        </dependency>
        <!--springboot单元测试-->
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-test</artifactId>
            <scope>test</scope>
        </dependency>
    </dependencies>
        <!--打包插件-->
    <build>
        <plugins>
            <plugin>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-maven-plugin</artifactId>
            </plugin>
        </plugins>
    </build>

</project>
```



> 编写一个http接口

在主程序的**同级目录下**新建一个包controller，创建一个HelloController

```java
@Controller
@RequestMapping("/hello")
public class HelloController {
    @GetMapping("/h1")
    @ResponseBody
    public String test(){
        return "hello";
    }
}
```

编写完毕后从主程序启动

![image-20210907143106170](F:\笔记图片\img\image-20210907143106170.png)

![image-20210907143342752](F:\笔记图片\img\image-20210907143342752.png)



简单几步就完成了web接口的开发



## 3.原理初探

### 1.自动配置

**pom.xml**

- springboot-dependencies:核心依赖在父工程中

- 我们在写或者引入一些springboot依赖的时候，不需要指定版本，就因为有这些版本仓库

- 如果要改变默认的版本号,需要在pom.xml中

  ```xml
  <properties>
  	<xxxx.version>版本号</xxxx.version>
  </properties>
  
  <!--maven的就近优先原则-->
  ```

**启动器**：说白了就是springboot启动场景

```
见到很多 spring-boot-start-*  *就代表某种场景
只要引入了starter，这个场景的所有常规需要的依赖我们都自动引入

*-spring-boot-start:第三方为我们提供的简化开发的启动器

```

```xml
<!--所有的场景启动器最底层的依赖-->
<dependency>
      <groupId>org.springframework.boot</groupId>
      <artifactId>spring-boot-starter</artifactId>
      <version>2.5.4</version>
      <scope>compile</scope>
    </dependency>
```



```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-web</artifactId>
</dependency>

<!--会帮我们导入所有的web环境依赖-->
```

- springboot会将所有的功能场景，都变成一个个的启动器
- 如果我们要使用什么功能，就只需要找到对应的启动器就可以了

**主程序**：

- 主程序所在的包及其下边所有的子包里面的组件都会被默认扫描，无需配置以前的包扫描

- 想要改变扫描路径

  - @SpringBootApplication(scanBasePackges = "`指定要扫描的基础包`")

  - ```
    @SpringBootConfiguration
    @EnableAutoConfiguration
    @ComponentScan("指定扫描的路径")
    ```

- 各种配置拥有默认值
  - 默认配置最终都是映射到MultipartProperties
  - 配置文件的值最终会绑定每个类上，这个类会在容器中创建对象
- 按需加载所有自动配置项
  - 引入哪个场景，这个场景的自动配置才会开启
  - SpringBoot所有的自动配置功能都在spring-boot-autoconfigure包里面



### 2.容器功能

#### 2.1 组件添加

1. @configuration

   - 基本使用

     ```java
     /**
      * 1.配置类里面使用@Bean标注方法上给容器注册组件，默认也是单实例的
      * 2.配置类本身也是组件
      * 3.proxyBeanMethods:代理bean的方法
      * 4.proxyBeanMethods为true时，代理对象调用方法，springboot总会检查这个组件是否在容器中存在，保持组件单实例
      */
     @Configuration() //告诉springboot这是个配置类
     public class MyConfig {
         @Bean   //给容器中添加组件，以方法名作为组件的id,返回类型就是组件类型，返回的对象就是组件在容器中的实例
         public User user01(){
             return new User("张三",18);
         }
         @Bean("tom")
         public Pet TomCcat(){
             return new Pet("tom");
         }
     }
     ```

   - Full模式与Lite模式

     - 最佳实战;
       - 配置类组件之间无yilaiguanxi用Lite模式加速容器启动过程，减少判断
       - 配置类组件之间有依赖关系，方法会被调用得到之前的单实例组件，用Full模式

2. @Import

   ```
   @Import({xxx.class},{xxx.class}.....): 给容器中自动创建出多个类型的组件，默认组件的名字就是全类名
   ```

3. @Conditional

   条件装配：满足Conditional指定的条件，则进行条件注入



#### 2.2 配置绑定

1. @Compoent 和 @ConfigurationProperties

   ```java
   /**
    *只有在容器中的组件，才会拥有springboot提供的强大功能
    */
   @Component
   @ConfigurationProperties(prefix = "mycar")
   public class Car {
       private String brand;
       private Integer price;
   
       public String getBrand() {
           return brand;
       }
   
       public void setBrand(String brand) {
           this.brand = brand;
       }
   
       public Integer getPrice() {
           return price;
       }
   
       public void setPrice(Integer price) {
           this.price = price;
       }
   
       @Override
       public String toString() {
           return "Car{" +
                   "brand='" + brand + '\'' +
                   ", price=" + price +
                   '}';
       }
   }
   ```

   ```properties
   mycar.brand=BYD
   mycar.price=100000
   ```

2. 在配置类上添加@EnableConfigurationProperties，bean类上添加@ConfigurationProperties

   ```java
   @ConfigurationProperties(prefix = "mycar")
   public class Car {
       private String brand;
       private Integer price;
   
       public String getBrand() {
           return brand;
       }
   
       public void setBrand(String brand) {
           this.brand = brand;
       }
   
       public Integer getPrice() {
           return price;
       }
   
       public void setPrice(Integer price) {
           this.price = price;
       }
   
       @Override
       public String toString() {
           return "Car{" +
                   "brand='" + brand + '\'' +
                   ", price=" + price +
                   '}';
       }
   }
   ```

   ```java
   
   //启动指定类的ConfigurationProperties
   	//进入Car查看，将Car的配置文件对应的值和Mycar绑定起来
   	//并把Car加入到Ioc容器中
   @EnableConfigurationProperties(Car.class)
   public class MyConfig {
       @Bean   //给容器中添加组件，以方法名作为组件的id,返回类型就是组件类型，返回的对象就是组件在容器中的实例
   
       public User user01(){
           return new User("张三",18);
       }
       @Bean
       public Pet TomCat(){
           return new Pet("tom");
       }
   }
   ```



### 3.自动配置原理入门

#### 3.1 引导加载自动配置类

```java
@SpringBootConfiguration
@EnableAutoConfiguration
@ComponentScan(excludeFilters = { @Filter(type = FilterType.CUSTOM, classes = TypeExcludeFilter.class),
      @Filter(type = FilterType.CUSTOM, classes = AutoConfigurationExcludeFilter.class) })
public @interface SpringBootApplication {

  ========================================================================
 
```

>@SpringBootConfiguration

```java
//代表当前是个配置类
@Configuration
@Indexed
public @interface SpringBootConfiguration {
    @AliasFor(
        annotation = Configuration.class
    )
    boolean proxyBeanMethods() default true;
}
```

>@ComponentScan

指定要扫描哪些



> @EnableAutoConfiguration

```java
@AutoConfigurationPackage
@Import(AutoConfigurationImportSelector.class)
public @interface EnableAutoConfiguration {
```

1. ​	**@AutoConfigurationPackage**  ：自动配置包，导入了默认包规则

   ```java
   @Import(AutoConfigurationPackages.Registrar.class) //给容器中导入一个组件
   public @interface AutoConfigurationPackage {
       
   //利用Registrar给容器中导入一系列组件
   //将指定的一个包下的所有组件导入进来，main程序所在的包下
   ```

2. **@Import(AutoConfigurationImportSelector.class)**

   ```java
   //1.利用getAutoConfigurationEntry(annotationMetadata)给容器中导入组件
   //2.调用getCandidateConfigurations(annotationMetadata, attributes)获取到所有需要导入容器中的配置类
   //3.利用工厂加载Map<String, List<String>> loadSpringFactories(ClassLoader classLoader)
   //4.从META-INF/Spring.factories位置来加载一个文件 ，默认扫描我们当前系统里面所有META-INF/Spring.factories位置的文件
   ```

   ![image-20210908144941034](F:\笔记图片\img\image-20210908144941034.png)



#### 3.2 按需开启自动配置项

虽然我们所有场景的自动配置启动的时候默认全部加载。

按照条件装配原则，最终会按需配置



## 4.yaml语法

语法格式

```yaml
k:
	v1:
	v2:
	
#例如:key: 空格 value
studetnt:
	name: 张三
	age: 15
	
#行内写法
student {name: 张三,age: 15}

#数组写法
pets:
	-pig
	-dog
	-cat
	
pets: [cat,dog,cat]
```

yaml可以直接给实体类赋值



### 1.给属性赋值

创建实体类

```java
@Component
@ConfigurationProperties(prefix = "person")
@Data
@AllArgsConstructor
@NoArgsConstructor
public class Person {
    private String name;
    private Integer age;
    private boolean happy;
    private Date birth;
    private Map<String,Object> maps;
    private List<Object> lists;
    private Dog dog;
}
```

```java
@Data
@AllArgsConstructor
@NoArgsConstructor
@Component
public class Dog {
    @Value("旺财")
    private String name;
    @Value("3")
    private Integer age;
}
```

编写yaml文件

```yaml
person:
  name: 张三
  age: 18
  happy: true
  birth: 2021/09/08
  maps: {k1: v1,k2: v2}
  lists:
    -code
    -music
    -sport
  dog:
    name: 旺财
    age: 3
```



绑定文件和类

- ```
  @ConfigurationProperties(prefix = "xxx")
  ```

- ```
  @PropertySource("xxxxxx")
  ```



### 2.JSR303校验

> 常见参数

```
@NotNull(message="名字不能为空")
private String userName;
@Max(value=120,message="年龄最大不能查过120")
private int age;
@Email(message="邮箱格式错误")private String email;


空检查
@Null  验证对象是否为null
@NotNull    验证对象是否不为null, 无法查检长度为0的字符串
@NotBlank   检查约束字符串是不是Null还有被Trim的长度是否大于0,只对字符串,且会去掉前后空格.
@NotEmpty    检查约束元素是否为NULL或者是EMPTY.


Booelan检查
@AssertTrue  验证 Boolean 对象是否为 true
@AssertFalse 验证 Boolean 对象是否为 false    


长度检查
@Size(min=, max=) 验证对象（Array,Collection,Map,String）长度是否在给定的范围之内  @Length(min=, max=) string is between min and max included.


日期检查
@Past       验证 Date 和 Calendar 对象是否在当前时间之前  
@Future     验证 Date 和 Calendar 对象是否在当前时间之后  
@Pattern    验证 String 对象是否符合正则表达式的规则
.......等等除此以外，我们还可以自定义一些数据校验规则
```



### yaml的多环境配置

![image-20210909110810854](F:\笔记图片\img\image-20210909110810854.png)

当有多个yaml配置文件时，默认按照如图所示的优先级



```yaml
server:
  port: 8081
spring:
  profiles:
    active: dev
---

server:
  port: 8082
spring:
  profiles: dev
---
server:
  port: 8083
spring:
  profiles: test
```



## 5.自动配置原理在理解

> @SpringBootApplication

作用：标注在某个类上说明这个类是SpringBoot的主配置类 ， SpringBoot就应该运行这个类的main方法来启动SpringBoot应用；

进入这个注解：可以看到上面还有很多其他注解！

```java
@SpringBootConfiguration
@EnableAutoConfiguration
@ComponentScan(excludeFilters = { @Filter(type = FilterType.CUSTOM, classes = TypeExcludeFilter.class),
      @Filter(type = FilterType.CUSTOM, classes = AutoConfigurationExcludeFilter.class) })
public @interface SpringBootApplication {
```

>@ComponentScan

这个注解在Spring中很重要 ,它对应XML配置中的元素。

作用：自动扫描并加载符合条件的组件或者bean ， 将这个bean定义加载到IOC容器中

>@SpringBootConfiguration

作用：SpringBoot的配置类 ，标注在某个类上 ， 表示这是一个SpringBoot的配置类；

我们继续进去这个注解查看

```java
@Configuration
@Indexed
public @interface SpringBootConfiguration {
    @AliasFor(
        annotation = Configuration.class
    )
    boolean proxyBeanMethods() default true;
}
```

这里的 @Configuration，说明这是一个配置类 ，配置类就是对应Spring的xml 配置文件；

里面的 @Component 这就说明，启动类本身也是Spring中的一个组件而已，负责启动应用！

我们回到 SpringBootApplication 注解中继续看。



>@EnableAutoConfiguration

**@EnableAutoConfiguration ：开启自动配置功能**

以前我们需要自己配置的东西，而现在SpringBoot可以自动帮我们配置 ；@EnableAutoConfiguration告诉SpringBoot开启自动配置功能，这样自动配置才能生效；

点进注解接续查看：

**@AutoConfigurationPackage ：自动配置包**

```
@Import(AutoConfigurationPackages.Registrar.class)
public @interface AutoConfigurationPackage {
```

**@import** ：Spring底层注解@import ， 给容器中导入一个组件

Registrar.class 作用：将主启动类的所在包及包下面所有子包里面的所有组件扫描到Spring容器 ；

这个分析完了，退到上一步，继续看



**@Import({AutoConfigurationImportSelector.class}) ：给容器导入组件 ；**

AutoConfigurationImportSelector ：自动配置导入选择器，那么它会导入哪些组件的选择器呢？我们点击去这个类看源码：

1、这个类中有一个这样的方法

```java
//获取候选的配置
protected List<String> getCandidateConfigurations(AnnotationMetadata metadata, AnnotationAttributes attributes) {
     //这里的getSpringFactoriesLoaderFactoryClass（）方法
    //返回的就是我们最开始看的启动自动导入配置文件的注解类；EnableAutoConfiguration
   List<String> configurations = SpringFactoriesLoader.loadFactoryNames(getSpringFactoriesLoaderFactoryClass(),
         getBeanClassLoader());
   Assert.notEmpty(configurations, "No auto configuration classes found in META-INF/spring.factories. If you "
         + "are using a custom packaging, make sure that file is correct.");
   return configurations;
}
```

2.这个方法又调用了  SpringFactoriesLoader 类的静态方法！我们进入SpringFactoriesLoader类loadFactoryNames() 方法

```java
public static List<String> loadFactoryNames(Class<?> factoryType, @Nullable ClassLoader classLoader) {
    ClassLoader classLoaderToUse = classLoader;
    if (classLoader == null) {
        classLoaderToUse = SpringFactoriesLoader.class.getClassLoader();
    }

    String factoryTypeName = factoryType.getName();
   
    return
//这里他又调用了loadSpringFactories方法  	
        (List)loadSpringFactories(classLoaderToUse).getOrDefault(factoryTypeName, Collections.emptyList());
}
```

3.我们继续点开loadSpringFactories

```java
private static Map<String, List<String>> loadSpringFactories(ClassLoader classLoader) {
   //获得classloader,我们返回可以看到这里得到的就是EnableAutoConfiguration标注的类本身
    Map<String, List<String>> result = (Map)cache.get(classLoader);
    if (result != null) {
        return result;
    } else {
        HashMap result = new HashMap();

        try {
            //去获取一个资源 "META-INF/spring.factories"
            Enumeration urls = classLoader.getResources("META-INF/spring.factories");
			//将读取到的资源遍历，封装成为一个Properties
            while(urls.hasMoreElements()) {
                URL url = (URL)urls.nextElement();
                UrlResource resource = new UrlResource(url);
                Properties properties = PropertiesLoaderUtils.loadProperties(resource);
                Iterator var6 = properties.entrySet().iterator();

                while(var6.hasNext()) {
                    Entry<?, ?> entry = (Entry)var6.next();
                    String factoryTypeName = ((String)entry.getKey()).trim();
                    String[] factoryImplementationNames = StringUtils.commaDelimitedListToStringArray((String)entry.getValue());
                    String[] var10 = factoryImplementationNames;
                    int var11 = factoryImplementationNames.length;

                    for(int var12 = 0; var12 < var11; ++var12) {
                        String factoryImplementationName = var10[var12];
                        ((List)result.computeIfAbsent(factoryTypeName, (key) -> {
                            return new ArrayList();
                        })).add(factoryImplementationName.trim());
                    }
                }
            }
```

4.发现一个多次出现的文件，spring.factories,全局搜索他

![](F:\笔记图片\img\image-20210909151342464.png)

5.随便点入一个类来看

![image-20210909151405473](F:\笔记图片\img\image-20210909151405473.png)

可以看到是配置类，而且都注入了bean

所以，自动配置真正实现是从classpath中搜寻所有的META-INF/spring.factories配置文件 ，并将其中对应的 org.springframework.boot.autoconfigure. 包下的配置项，通过反射实例化为对应标注了 @Configuration的JavaConfig形式的IOC容器配置类 ， 然后将这些都汇总成为一个实例并加载到IOC容器中。



springboot帮助我们自动装配（@xxxautoConfiguration）

装配完之后会从xxxPorperties中取默认值

如果要修改默认值，按照对应的规则在配置文件中修改（@ConfigurationProperties(prefix = "")）



> 精髓

1、SpringBoot启动会加载大量的自动配置类

2、我们看我们需要的功能有没有在SpringBoot默认写好的自动配置类当中；

3、我们再来看这个自动配置类中到底配置了哪些组件；（只要我们要用的组件存在在其中，我们就不需要再手动配置了）

4、给容器中自动配置类添加组件的时候，会从properties类中获取某些属性。我们只需要在配置文件中指定这些属性的值即可；

**xxxxAutoConfigurartion：自动配置类；**给容器中添加组件

**xxxxProperties:封装配置文件中相关属性；**





## 6.SpringBoot Web开发

springboot帮我们自动配置了什么，我们能不能修改？能修改哪些东西？

- xxxAutoConfiguration向容器中自动配置组件
- xxxProperties:自动配置类，装配配置文件中自定义的一些内容



要解决的问题：

- 导入静态资源
- 首页
- 模板引擎
- 装配和拓展springmvc
- 视图解析器
- 增删改查
- 拦截器



### 1.静态资源

1. 在springboot中我们可以采用以下方式处理静态资源
   - webjars		`localhost:8080/webjars/`
   - /resources,/static,public,/META-INF/resources      `localhost:8080/ + 静态资源名`


2. 静态资源访问的优先级

![image-20210910155728041](F:\笔记图片\img\image-20210910155728041.png)

原理：静态映射/**	

​		请求进来，先去找controller能不能处理，不能处理的所有请求又都交给静态资源处理器。静态资源能找到就访问，找不到返回404

> 改变默认的静态资源路径

```
Spring:
 resources:
  static-locations: classpath:/haha/
```

> 静态资源访问前缀

​	默认无前缀

```yaml
spring:
  mvc:
    static-path-pattern: /res/**
```

当前项目 + static-path-pattern + 静态资源名 

### 2.首页如何定制

- 静态资源目录下   index.html
  - 可以配置静态资源路径
  - 但是不可以配置静态资源的访问前缀，否则导致index.html不能被默认访问
- controller能处理/index请求的

```java
private Resource getIndexHtml(String location) {
   return getIndexHtml(this.resourceLoader.getResource(location));
}

private Resource getIndexHtml(Resource location) {
   try {
      Resource resource = location.createRelative("index.html");
      if (resource.exists() && (resource.getURL() != null)) {
         return resource;
      }
   }
   catch (Exception ex) {
   }
   return null;
}
```

根据这段源码可知，在静态资源目录下找找到名为index.html的页面就为主页

如果页面在templates中

```java
//在templates目录下所有的页面，只能通过controller来跳转
//这个需要模板引擎的支持
@Controller
public class IndexController {
    @RequestMapping("/index")
    public String test(){
        return "index";
    }
}
```

### 3.静态资源配置管理

- springboot启动默认加载 xxxxAutoConfiguration(自动配置类)
- SpringMVC的配置功能大部分集中在WebMvcAutoConfiguration

```yaml
spring:
	resources:
		add-mapping: false   禁用所有静态资源规则
		
```


@RequestAttribute：获取request域属性

```java
@Controller
public class RequestController {
    @GetMapping("/goto")
    public String goToPage(HttpServletRequest request){

        request.setAttribute("msg","成功了");
        request.setAttribute("code",200);
        return "forward:/success";  //转发到success请求
    }

    @GetMapping("/success")
    @ResponseBody
    public Map<String, Object> success(@RequestAttribute("msg") String msg,
                                       @RequestAttribute("code") Integer code,
                                       HttpServletRequest request){
        Object msg1 = request.getAttribute("msg");

        Map<String,Object> map = new HashMap<>();
        map.put("注解_msg",msg);
        map.put("方法_msg",msg1);
        return map;
    }
}
```

### 4.thymeleaf模板引擎

模板引擎的作用就是我们来写一个页面模板，比如有些值呢，是动态的，我们写一些表达式。而这些值，从哪来呢，就是我们在后台封装一些数据。然后把这个模板和这个数据交给我们模板引擎，模板引擎按照我们这个数据帮你把这表达式解析、填充到我们指定的位置，然后把这个数据最终生成一个我们想要的内容给我们写出去，这就是我们这个模板引擎，不管是jsp还是其他模板引擎，都是这个思想。只不过呢，就是说不同模板引擎之间，他们可能这个语法有点不一样。其他的我就不介绍了，我主要来介绍一下SpringBoot给我们推荐的Thymeleaf模板引擎，这模板引擎呢，是一个高级语言的模板引擎，他的这个语法更简单。而且呢，功能更强大。





只要需要使用thymeleaf，只需要导入对应的依赖就可以了！

```java
private static final Charset DEFAULT_ENCODING = StandardCharsets.UTF_8;

public static final String DEFAULT_PREFIX = "classpath:/templates/";

public static final String DEFAULT_SUFFIX = ".html";
```

根据Thymeleaf.propeties源码可知，我们只需要将html页面放在templates目录下即可



**thymeleaf语法**

首先我们要导入头文件约束

```html
<html xmlns:th="http://www.thymeleaf.org">
```

```html
<body>
<!--所有的html元素都可以被thymeleaf替换：th:元素名-->
<div th:text="${msg}"></div>
```



```
${}:普通表达式
@{}:url
~{}:片段表达式
if then else:(if)?(then)?(else)
```



### 4.MVC配置原理

```
/*
如果您希望保留Spring Boot MVC功能，并且希望添加其他MVC配置（拦截器、格式化程序、视图控制器和其他功能），则可以添加自己
的@configuration类，类型为webmvcconfiguer，但不添加@EnableWebMvc。如果希望提供
RequestMappingHandlerMapping、RequestMappingHandlerAdapter或ExceptionHandlerExceptionResolver的自定义
实例，则可以声明WebMVCregistrationAdapter实例来提供此类组件。
*/
```

比如我们自定义一个视图解析器

```java
@Configuration
public class MyConfiguration implements WebMvcConfigurer {

    @Bean
    public MyViewResolver myViewResolver(){
        return new MyViewResolver();
    }

    public static class MyViewResolver implements ViewResolver {

        @Override
        public View resolveViewName(String s, Locale locale) throws Exception {
            return null;
        }
    }
}
```





## 7.整合JDBC

对于数据访问层，无论是SQL还是NOSql，springboot底层都是采用Spring Data的方式进行统一处理

xxxxtemplate:springboot已经配置好的模板bean，拿来即用  CRUD

> jdbcTemplate

 1、有了数据源(com.zaxxer.hikari.HikariDataSource)，然后可以拿到数据库连接(java.sql.Connection)，有了连接，就可以使用原生的 JDBC 语句来操作数据库；

 2、即使不使用第三方第数据库操作框架，如 MyBatis等，Spring 本身也对原生的JDBC 做了轻量级的封装，即JdbcTemplate。

 3、数据库操作的所有 CRUD 方法都在 JdbcTemplate 中。

 4、Spring Boot 不仅提供了默认的数据源，同时默认已经配置好了 JdbcTemplate 放在了容器中，程序员只需自己注入即可使用

 5、JdbcTemplate 的自动配置是依赖 org.springframework.boot.autoconfigure.jdbc 包下的 JdbcTemplateConfiguration 类

**JdbcTemplate主要提供以下几类方法：**

- execute方法：可以用于执行任何SQL语句，一般用于执行DDL语句；
- update方法及batchUpdate方法：update方法用于执行新增、修改、删除等语句；batchUpdate方法用于执行批处理相关语句；
- query方法及queryForXXX方法：用于执行查询相关语句；
- call方法：用于执行存储过程、函数相关语句。



```java
@RestController
public class JdbcController {
    @Autowired
    JdbcTemplate jdbcTemplate;

    @GetMapping("/userlist")
    //查询数据库的所有信息
    public List<Map<String,Object>> userList(){
        String sql = "select * from mybatis.user";
        List<Map<String, Object>> list = jdbcTemplate.queryForList(sql);
        return list;
    }
    @GetMapping("/add")
    //增加一个用户
    public void addUser(){
        String sql = "insert into mybatis.user values (7,'小王','456184151212')";
        jdbcTemplate.update(sql);
    }

}
```



## 8.整合Druid

```java
@Configuration
public class DruidConfig {

    @ConfigurationProperties(prefix = "spring.datasource")
    @Bean
    public DataSource druidDataSource(){
        return new DruidDataSource();
    }
```

```java
 /**
     * 配置druid的监控页
     * springboot内置了servlet容器，所以没有web.xml，替代方法：ServletRegistrationBean
     * @return
     */
    @Bean
    public ServletRegistrationBean statViewServlet(){
        ServletRegistrationBean<StatViewServlet> bean = new ServletRegistrationBean<>(new StatViewServlet(), "/druid/*");
        //后台需要有人登录，账号密码配置
        HashMap<String,String> initParameters = new HashMap<>();
        //增加配置
        initParameters.put("loginUsername","admin");
        initParameters.put("loginPassword","123456");

        bean.setInitParameters(initParameters); //设置初始化参数
        return bean;
    }
}
```



## 9.整合Mybatis

整合包

mybatis-spring-boot-starter

1. 导入包

2. 配置文件

   ![image-20210914171756871](F:\笔记图片\img\image-20210914171756871.png)

   ```properties
   spring.datasource.username=root
   spring.datasource.password=123456
   spring.datasource.url=jdbc:mysql://localhost:3306/mybatis?
   spring.datasource.driver-class-name=com.mysql.jdbc.Driver
   ```

   

3. mybatis配置

   ```properties
   #整合mybatis
   mybatis.type-aliases-package=com.xiong.pojo
   mybatis.mapper-locations=classpath:mybatis/mapper/*.xml
   ```

4. 创建实体类

   ```java
   @Data
   @AllArgsConstructor
   @NoArgsConstructor
   public class User {
       private int id;
       private String name;
       private String pwd;
   }
   ```

5. 创建mapper接口

   ```java
   @Mapper  //表示了这是一个mybatis的mapper类
   @Repository
   public interface UserMapper {
       List<User> queryUserList();
   
       User queryUserById(int id);
   
       int updateUser(User user);
   }
   ```

6. 创建对应mapper的映射文件

   ```xml
   <?xml version="1.0" encoding="UTF-8" ?>
   <!DOCTYPE mapper
           PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
           "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
   <mapper namespace="com.xiong.mapper.UserMapper">
       <select id="queryUserList" resultType="User">
           select * from mybatis.user;
       </select>
   </mapper>
   ```

7. 业务层调用dao层

8. controller层调用service层

   ```java
   @RestController
   public class UserController {
       @Autowired
       private UserMapper userMapper;
       @GetMapping("/getUser")
       public List<User> queryUserList(){
   
           List<User> userList = userMapper.queryUserList();
           for (User user : userList) {
               System.out.println(user);
           }
           return userList;
       }
   }
   ```

   

