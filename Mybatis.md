## 1.Mybatis

### 1.1 什么是Mybatis

- MyBatis 是一款优秀的**持久层**框架

- 它支持自定义 SQL、存储过程以及高级映射。
- MyBatis 免除了几乎所有的 JDBC 代码以及设置参数和获取结果集的工作。
- MyBatis 可以通过简单的 XML 或注解来配置和映射原始类型、接口和 Java POJO（Plain Old Java Objects，普通老式 Java 对象）为数据库中的记录。



如何获得Mybaits

1. githup获取

2. maven仓库

   ```xml
   <!-- https://mvnrepository.com/artifact/org.mybatis/mybatis -->
   <dependency>
       <groupId>org.mybatis</groupId>
       <artifactId>mybatis</artifactId>
       <version>3.5.2</version>
   </dependency>
   ```



### 1.2 持久化

数据持久化

- 持久化就是将程序的数据在持久状态下和瞬时状态转化的过程
- 内存：断电即失
- 数据库（jdbc）,io文件持久化



### 1.3持久层

DAO层，Service层，Controller层

- 完成持久化工作的代码块
- 层界限十分明显



### 1.4 为什么需要Mybatis

- 帮助程序员将数据存入到数据库中
- 方便
- 传统的JDBC代码太复杂了。简化，框架，自动化
- 优点
  - 简单易学
  - 灵活
  - sql和代码的分离，提高了可维护性
  - 提供了映射标签，支持对象与数据库的orm字段关系映射
  - 提供对象关系映射标签，支持对象关系组建维护
  - 提供xml标签，支持编写动态sql



## 2.第一个Mybatis程序

思路：搭建环境-->导入mybatis-->编写代码-->测试

### 2.1 搭建环境

 搭建数据库

```mysql
CREATE DATABASE mybatis;
USE mybatis;
CREATE TABLE `user`(
			`id` INT(20) NOT NULL PRIMARY KEY,
			`name` VARCHAR(20) DEFAULT NULL
			`pwd` VARCHAR(20) DEFAULT NULL
	)ENGINE=INNODB DEFAULT CHARSET=utf8; 
INSERT INTO `user`(`id`,`name`,`pwd`) VALUES
(1,'张三','123456'),
(2,'李四','456789'),
(3,'王五','789123')
```

新建项目

1. 新建一个普通的maven项目

2. 删除src

3. 导入maven依赖

   ```xml
   <?xml version="1.0" encoding="UTF-8"?>
   <project xmlns="http://maven.apache.org/POM/4.0.0"
            xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
            xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
       <modelVersion>4.0.0</modelVersion>
       <!--父工程-->
       <groupId>org.example</groupId>
       <artifactId>Mybaits-Strdy</artifactId>
       <version>1.0-SNAPSHOT</version>
   
       <!--导入依赖-->
       <dependencies>
           <!--mysql驱动-->
           <dependency>
               <groupId>mysql</groupId>
               <artifactId>mysql-connector-java</artifactId>
               <version>5.1.47</version>
           </dependency>
           <!--mybatis-->
           <dependency>
               <groupId>org.mybatis</groupId>
               <artifactId>mybatis</artifactId>
               <version>3.5.2</version>
           </dependency>
           <!--junit-->
           <dependency>
               <groupId>junit</groupId>
               <artifactId>junit</artifactId>
               <version>4.11</version>
           </dependency>
       </dependencies>
   </project>
   ```



### 2.2 创建一个模块

- 编写mybaits核心配置文件

  ```xml
  <?xml version="1.0" encoding="UTF-8" ?>
  <!DOCTYPE configuration
          PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
          "http://mybatis.org/dtd/mybatis-3-config.dtd">
  
  <!--核心配置文件-->
  <configuration>
      <environments default="development">
          <environment id="development">
              <transactionManager type="JDBC"/>
              <dataSource type="POOLED">
                  <property name="driver" value="com.mysql.jdbc.Driver"/>
                  <property name="url" value="jdbc:mysql://localhost:3306/mybatis?useSSL=false&amp;useUnicode=true&amp;characterEncoding=UTF-8"/>
                  <property name="username" value="root"/>
                  <property name="password" value="123456"/>
              </dataSource>
          </environment>
      </environments>
  <!--    每一个mapper.xml都会在mybatis核心配置文件中注册-->
      <mappers>
          <mapper resource="UserMapper.xml"/>
      </mappers>
  </configuration>
  ```

- 编写mybaits工具类

  ```java
  import java.io.IOException;
  import java.io.InputStream;
  
  /**
   * @description:sqlSessionFactory-->sqlSession
   */
  
  public class MyUtils {
      private static SqlSessionFactory sqlSessionFactory;
      static {
          try {
              //使用Mybatis第一步：获取sqlSessionFactory对象
              String resource = "mybatis-config.xml";
              InputStream inputStream = Resources.getResourceAsStream(resource);
              sqlSessionFactory = new SqlSessionFactoryBuilder().build(inputStream);
          } catch (IOException e) {
              e.printStackTrace();
          }
      }
  
      //    既然有了 SqlSessionFactory，顾名思义，我们可以从中获得 SqlSession 的实例。
      //    SqlSession 提供了在数据库执行 SQL 命令所需的所有方法。你可以通过 SqlSession 实例来直接执行已映射的 SQL 语句。
      public static SqlSession getSqlSession(){
              return sqlSessionFactory.openSession();
      }
  
  }
  ```



### 2.3编写代码

- 实体类

  ```java
  package com.xiong.pojo;
  
  /**
   * @author zgx
   * @date 2021/8/24 14:13
   */
  
  //实体类
  public class User {
      private int id;
      private String name;
      private String pwd;
  
      public User() {
      }
  
      public User(int id, String name, String pwd) {
          this.id = id;
          this.name = name;
          this.pwd = pwd;
      }
  
      public int getId() {
          return id;
      }
  
      public void setId(int id) {
          this.id = id;
      }
  
      public String getName() {
          return name;
      }
  
      public void setName(String name) {
          this.name = name;
      }
  
      public String getPwd() {
          return pwd;
      }
  
      public void setPwd(String pwd) {
          this.pwd = pwd;
      }
  
      @Override
      public String toString() {
          return "User{" +
                  "id=" + id +
                  ", name='" + name + '\'' +
                  ", pwd='" + pwd + '\'' +
                  '}';
      }
  }
  ```

- Dao接口

  ```java
  public interface UserDao {
      List<User> getUserList();
  }
  ```

- 接口实现类由原来的UserDaoImpl转换为一个mapper配置文件

  ```xml
  <?xml version="1.0" encoding="UTF-8" ?>
  <!DOCTYPE mapper
          PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
          "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
  <!--namespace = 绑定一个对应的Dao/Mapper接口-->
  <mapper namespace="com.xiong.dao.UserDao">
      <select id="getUserList" resultType="com.xiong.pojo.User">
          select * from mybatis.user;
      </select>
  </mapper>
  ```



### 2.4 测试

注意点：

org.apache.ibatis.binding.BindingException: Type interface com.xiong.dao.UserDao is not known to the MapperRegistry.

MapperRegistry是什么：

核心配置文件中注册mappers

```xml
<!--    每一个mapper.xml都会在mybatis核心配置文件中注册-->
    <mappers>
        <mapper resource="com/xiong/dao/UserMapper.xml"/>
    </mappers>
```



MapperRegistry

- jnuit测试

```java
public class UserDaoTest {
    @Test
    public void test(){

        //第一步：获得SqlSession对象
        SqlSession sqlSession = MybatisUtils.getSqlSession();


        //方式一：getMapper()
        UserDao mapper = sqlSession.getMapper(UserDao.class);
        List<User> userList = mapper.getUserList();

        for (User user : userList) {
            System.out.println(user);
        }
        sqlSession.close();
    }

}
```



## 3.CRUD

### 1.namespace

namespace的包名要和Dao/mapper接口的包名一致

### 2.select

选择，查询语句

- id：就是对应的namespace中的方法名
- resultType：SQL语句执行的返回值
- parameterType：参数类型

1.编写接口

```java
  //根据id查询用户
    User getUserById(int id);
```

2.编写对应的Mapper.xml中的sql语句

```xml
<select id="getUserById" parameterType="int" resultType="com.xiong.pojo.User">
        select * from mybatis.user where id = #{id}
    </select>
```

3.测试

```java
 public void test2(){
        SqlSession sqlSession = MybatisUtils.getSqlSession();

        UserMapper mapper = sqlSession.getMapper(UserMapper.class);

        User user = mapper.getUserById(1);

        System.out.println(user);

        sqlSession.close();
    }
```



### 3.insert

1.编写接口

```java
 //增加一个用户
    int addUser(User user);
```

2.编写对应的Mapper.xml中的sql语句

```xml
    <insert id="addUser" parameterType="com.xiong.pojo.User" >
        insert into mybatis.user(id, name, pwd) VALUES (#{id},#{name},#{pwd});
    </insert>
```

3.测试

```java
  //增删改需要提交事务
    @Test
    public void addTest(){
        SqlSession sqlSession = MybatisUtils.getSqlSession();

        UserMapper mapper = sqlSession.getMapper(UserMapper.class);

        int number = mapper.addUser(new User(4, "李六", "123789"));

        if (number > 0){
            System.out.println("插入成功");
        }

        //提交事务
        sqlSession.commit();

        sqlSession.close();
    }
```



### 4.update

1.编写接口

```java
 //修改用户
    int updateUser(User user);
```

2.编写对应的Mapper.xml中的sql语句

```xml
 <update id="updateUser" parameterType="com.xiong.pojo.User">
        update mybatis.user set name = #{name},pwd =#{pwd} where id = #{id};
    </update>
```

3.测试

```java
  @Test
    public void updateTest(){
        SqlSession sqlSession = MybatisUtils.getSqlSession();

        UserMapper mapper = sqlSession.getMapper(UserMapper.class);

        int i = mapper.updateUser(new User(1, "小明", "123123"));

        if (i > 0){
            System.out.println("修改成功");
        }
        sqlSession.commit();
        sqlSession.close();
    }

```



### 5.delete

1.编写接口

```java
  //删除用户
    int deleteUser(int id);
```

2.编写对应的Mapper.xml中的sql语句

```xml
 <delete id="deleteUser" parameterType="int">
        delete from mybatis.user where id = #{id};
    </delete>
```

3.测试

```java
 @Test
    public void deleteTest(){
        SqlSession sqlSession = MybatisUtils.getSqlSession();

        UserMapper mapper = sqlSession.getMapper(UserMapper.class);
        mapper.deleteUser(4);
        sqlSession.commit();
        sqlSession.close();
    }
```



**注意点**：增删改查需要提交事务



### 6.map

假设我们的实体类，或者数据库中的表，字段或者参数过多，我们应当考虑使用Map

```java
int addUser1(Map<String,Object> map);
```

```xml
<insert id="addUser1" parameterType="map">
    insert into mybatis.user (id, name, pwd) VALUES (#{userid},#{userName},#{password})
</insert>
```

```java
public void addTest2(){
    SqlSession sqlSession = MybatisUtils.getSqlSession();

    UserMapper mapper = sqlSession.getMapper(UserMapper.class);
    Map<String,Object> map = new HashMap<String, Object>();

    map.put("userid",4);
    map.put("userName","张三");
    map.put("password","123456789");

    mapper.addUser1(map);

    sqlSession.commit();

    sqlSession.close();

}
```



Map传递参数，直接在sql中取出key即可

对象传递参数，直接在sql中取对象的属性即可



模糊查询怎么写

1. java代码执行的时候，传递通配符% %

   ```java
   List<User> users = mapper.getUserLike("%李%");
   ```

2. 在sql拼接中使用通配符

   ```xml
   <select id="getUserLike" resultType="com.xiong.pojo.User">
       select * from mybatis.user where name like "%"#{value}"%"
   </select>
   ```



## 4.配置解析

### 1.核心配置文件

- mybatis-config.xml
- MyBatis的配置文件包含了会深深影响MyBatis行为的设置和属性信息

```
configuration（配置）
properties（属性）
settings（设置）
typeAliases（类型别名）
typeHandlers（类型处理器）
objectFactory（对象工厂）
plugins（插件）
environments（环境配置）
environment（环境变量）
transactionManager（事务管理器）
dataSource（数据源）
databaseIdProvider（数据库厂商标识）
mappers（映射器）
```

### 2.环境配置

MyBatis 可以配置成适应多种环境

**不过要记住：尽管可以配置多个环境，但每个 SqlSessionFactory 实例只能选择一种环境。**

学会配置多套运行环境

mybatis默认的事务管理器是jdbc,默认的连接池是POOLED



### 3.属性（properties）

我们可以通过properties属性实现引用配置文件

这些属性可以在外部进行配置，并可以进行动态替换。你既可以在典型的 Java 属性文件中配置这些属性，也可以在 properties 元素的子元素中设置



编写一个配置文件

dp.properties

```properties
driver=com.mysql.jdbc.Driver
url=jdbc:mysql://localhost:3306/mybatis?useSSL=false&useUnicode=true&characterEncoding=UTF-8"
username=root
password=123456
```

在核心配置文件中引入

```xml
<!-- 引入外部配置文件-->
<properties resource="dp.properties">
    <property name="username" value="root"/>
    <property name="password" value="123456"/>
</properties>
```

- 可以直接引入外部文件
- 可以在其中增加一些属性配置
- 如果两个文件有同一个字段，优先使用外部文件配置



### 4.类型别名（typeAliases）

类型别名可为 Java 类型设置一个缩写名字。 

它仅用于 XML 配置，意在降低冗余的全限定类名书写。

```xml
<!-- 可以给实体类起别名-->
<typeAliases>
    <typeAlias type="com.xiong.pojo.User" alias="User"/>
</typeAliases>
```

也可以指定一个包名，MyBatis 会在包名下面搜索需要的 Java Bean

扫面实体类的包，他的默认别名就为这个类的类名，首字母小写

```xml
<!-- 可以通过包起别名-->
<typeAliases>
    <package name="com.xiong.pojo"/>
</typeAliases>
```

在实体类比较少的时候使用第一种

在实体类比较多的时候建议使用第二种

也可以在实体类上加注解

```java
@Alias("user")
```



### 5.设置

| cacheEnabled       | 全局性地开启或关闭所有映射器配置文件中已配置的任何缓存。     |
| ------------------ | ------------------------------------------------------------ |
| lazyLoadingEnabled | 延迟加载的全局开关。当开启时，所有关联对象都会延迟加载。 特定关联关系中可通过设置 `fetchType` 属性来覆盖该项的开关状态。 |



### 6.其他配置

- [typeHandlers（类型处理器）](https://mybatis.org/mybatis-3/zh/configuration.html#typeHandlers)
- [objectFactory（对象工厂）](https://mybatis.org/mybatis-3/zh/configuration.html#objectFactory)
- [plugins（插件）](https://mybatis.org/mybatis-3/zh/configuration.html#plugins)



### 7.映射器

MapperRegistry:注册绑定我们的Mapper文件

方式一：【推荐使用】

```xml
<!-- 使用相对于类路径的资源引用 -->
 <mappers>
        <mapper resource="com/xiong/dao/UserMapper.xml"/>
    </mappers>
```



方式二：使用class文件绑定注册

```xml
<!-- 使用映射器接口实现类的完全限定类名 -->
<mappers>
         <mapper class="com.xiong.dao.UserMapper"/>
    </mappers>
```

注意点：

- 接口和他的Mapper配置文件必须同名
- 接口和他的Mapper配置文件必须在同一个包下



方式三：使用扫描包进行注入绑定

```xml
<!-- 将包内的映射器接口实现全部注册为映射器 -->
<mappers>
        <package name="com.xiong.dao"/>
    </mappers>
```

注意点：

- 接口和他的Mapper配置文件必须同名
- 接口和他的Mapper配置文件必须在同一个包下



### 8.生命周期和作用域

![image-20210827161108728](F:\笔记图片\img\image-20210827161108728.png)

不同作用域和生命周期类别是至关重要的，因为错误的使用会导致非常严重的**并发问题**。

**SqlSessionFactoryBuilder**

- 一旦创建SqlSessionFactory ，就不再需要它了
- 局部变量

**SqlSessionFactory**

- 说白了就是可以想象为数据库连接池
- SqlSessionFactory 一旦被创建就应该在应用的运行期间一直存在，**没有任何理由丢弃它或重新创建另一个实例**
- 因此 SqlSessionFactory 的最佳作用域是应用作用域
- 最简单的就是使用单例模式或者静态单例模式

**SqlSession**

- 连接到连接池的一个请求
- 关闭请求
- SqlSession 的实例不是线程安全的，因此是不能被共享的，所以它的最佳的作用域是请求或方法作用域
- 用完之后赶紧关闭，否则资源将被占用
- ![image-20210827161124344](F:\笔记图片\img\image-20210827161124344.png)



### 9.解决属性名字和字段名不一致的问题

**resultMap**:结果集映射

```xml
    <!--    结果集映射-->
<resultMap id="UserMap" type="User">
    <!-- column数据库中的字段   property 实体类中的属性-->
    <result column="pwd" property="password"/>
</resultMap>
```

- resultMap元素是Mybatis中最重要最强大的元素
- ResultMap 的设计思想是，对简单的语句做到零配置，对于复杂一点的语句，只需要描述语句之间的关系就行了。



## 5.日志

### 1.日志工厂

如果一个数据库操作，出现了异常，我们需要排错，日志就是最好的助手

![image-20210827141734705](F:\笔记图片\img\image-20210827141734705.png)

- SLF4J
- LOG4J
- LOG4J2
- JDK_LOGGING
- COMMONS_LOGGING
- STDOUT_LOGGING
- NO_LOGGING



在mybatis中具体使用哪一个日志实现，在设置中设定！

### 2.STDOUT_LOGGING标准日志输出

![image-20210827142451232](F:\笔记图片\img\image-20210827142451232.png)

### 3.LOG4J

- Log4j是[Apache](https://baike.baidu.com/item/Apache/8512995)的一个开源项目，通过使用Log4j，我们可以控制日志信息输送的目的地是[控制台](https://baike.baidu.com/item/控制台/2438626)、文件、[GUI](https://baike.baidu.com/item/GUI)组件，甚至是套接口服务器、[NT](https://baike.baidu.com/item/NT/3443842)的事件记录器、[UNIX](https://baike.baidu.com/item/UNIX) [Syslog](https://baike.baidu.com/item/Syslog)[守护进程](https://baike.baidu.com/item/守护进程/966835)等
- 我们也可以控制每一条日志的输出格式
- 通过定义每一条日志信息的级别，我们能够更加细致地控制日志的生成过程。
- 最令人感兴趣的就是，这些可以通过一个[配置文件](https://baike.baidu.com/item/配置文件/286550)来灵活地进行配置，而不需要修改应用的代码。



1. 先导入log4j的包

   ```xml
   <dependency>
       <groupId>log4j</groupId>
       <artifactId>log4j</artifactId>
       <version>1.2.17</version>
   </dependency>
   ```

2. 配置log4j

   ```properties
   log4j.rootLogger=DEBUG,console,file
   
   #控制台输出的相关设置
   log4j.appender.console = org.apache.log4j.ConsoleAppender
   log4j.appender.console.Target = System.out
   log4j.appender.console.Threshold=DEBUG
   log4j.appender.console.layout = org.apache.log4j.PatternLayout
   log4j.appender.console.layout.ConversionPattern=[%c]-%m%n
   
   #文件输出的相关设置
   log4j.appender.file = org.apache.log4j.RollingFileAppender
   log4j.appender.file.File=./log/kuang.log
   log4j.appender.file.MaxFileSize=10mb
   log4j.appender.file.Threshold=DEBUG
   log4j.appender.file.layout=org.apache.log4j.PatternLayout
   log4j.appender.file.layout.ConversionPattern=[%p][%d{yy-MM-dd}][%c]%m%n
   
   #日志输出级别
   log4j.logger.org.mybatis=DEBUG
   log4j.logger.java.sql=DEBUG
   log4j.logger.java.sql.Statement=DEBUG
   log4j.logger.java.sql.ResultSet=DEBUG
   log4j.logger.java.sql.PreparedStatement=DEBUG
   ```

3. 配置log4j为日志实现

   ```xml
   <settings>
           <setting name="logImpl" value="LOG4J"/>
       </settings>
   ```



**简单实用**

1. 在要使用Log4j的类中，导入包import org.apache.log4j.Logger;

2. 日志对象，加载参数为当前类的class 

   ```java
   static Logger logger = Logger.getLogger(UserMapperTest.class);
   ```

3. 日志级别

   ```java
   logger.info("info:进入了testlog4j");
   logger.debug("debug:进入了testlog4j");
   logger.error("error:进入了testlog4j");
   ```



## 6.分页

**为社么要分页？**

- 减少数据的处理量



**使用Limit分页**

```mysql
select * from user limit startIndex,pageSize
```



### 1.使用mybatis实现分页

1. 接口

   ```java
   //分页
   List<User> getUserByLimit(Map<String,Object> map);
   ```

2. UserMapper.xml

   ```xml
       <!-- 分页实现查询-->
   <select id="getUserByLimit" parameterType="map" resultType="user" resultMap="UserMap">
       select * from mybatis.user limit #{startIndex},#{pageSize}
   </select>
   ```

3. 测试

   ```java
   public void getUserByLimit(){
       SqlSession sqlSession = MybatisUtils.getSqlSession();
       UserMapper mapper = sqlSession.getMapper(UserMapper.class);
   
       Map<String,Object> map = new HashMap<String, Object>();
       map.put("startIndex",0);
       map.put("pageSize",2);
       List<User> user = mapper.getUserByLimit(map);
   
       for (User u:user){
           System.out.println(u);
       }
   }
   ```





## 7.注解开发

### 1.面向接口编程

**根本原因：解耦，可拓展，提高复用。分层开发中，上层不用管具体的实现，大家都遵守共同的标准，使得开发变得容易，规范性更好**



### 2.使用注解开发

1. 注解在接口上实现

   ```java
   public interface UserMapper {
       @Select("select * from user")
       List<User> getUsers();
   }
   ```

2. 需要在核心配置文件中绑定接口

   ```xml
   <mappers>
       <mapper class="com.xiong.dao.UserMapper"/>
   </mappers>
   ```

3. 测试



本质：反射机制实现

底层：动态代理

![image-20210827164430106](F:\笔记图片\img\image-20210827164430106.png)



### 2.CRUD

我们可以在工具类创建的时候实现自动提交事务

```java
public static SqlSession getSqlSession(){
        return sqlSessionFactory.openSession(true);
    }
```



编写接口，增加注解

```JAVA
 //方法存在多个参数，所有的参数前面必须加上@Param注解
    @Select("select * from user where id = #{id}")
    User getUserById(@Param("id") int id);

    @Insert("insert into user(id,name,pwd) values(#{id},#{name},#{password})")
    int addUser(User user);

    //修改
    @Update("update user set name = #{name},pwd = #{password} where id = #{id}")
    int upDateUser(User user);

    //删除
    @Delete("delete from user where id = #{id}")
    int deleteUser(@Param("id") int id);
```



测试类

**注意我们必须将接口注册绑定到我们的核心配置文件中**



### 3.关于@Parm()注解

- 基本类型的参数或者String类型需要加上
- 引用类型不需要加
- 如果只有一个基本类型的话，可以忽略，但是建议都加上！
- 我们在sql中引用的的就是我们在@Parm()中设定的属性



## 8.Lombok

使用步骤

1. 在IDEA中安装Lombok插件

2. 在项目中导入Lombok的jar包

3. 在实体类上加注解

   @Data:无参构造，getset，hashcode,toString,equals

   @AllArgsConstructor:有参构造

   @NoArgsConstructor.......



## 9.多对一的处理

多对一

- 多个学生，对应一个老师
- 对于学生来说，**关联**，多个学生**关联**一个老师【多对一】
- 对于老师而言，**集合**，一个老师有很多个学生【一对多】



```sql
CREATE TABLE `teacher` (
  `id` INT(10) NOT NULL,
  `name` VARCHAR(30) DEFAULT NULL,
  PRIMARY KEY (`id`)
) ENGINE=INNODB DEFAULT CHARSET=utf8
INSERT INTO teacher(`id`, `name`) VALUES (1, '秦老师'); 


CREATE TABLE `student` (
  `id` INT(10) NOT NULL,
  `name` VARCHAR(30) DEFAULT NULL,
  `tid` INT(10) DEFAULT NULL,
  PRIMARY KEY (`id`),
  KEY `fktid` (`tid`),
  CONSTRAINT `fktid` FOREIGN KEY (`tid`) REFERENCES `teacher` (`id`)
) ENGINE=INNODB DEFAULT CHARSET=utf8 
INSERT INTO `student` (`id`, `name`, `tid`) VALUES (1, '小明', 1);
INSERT INTO `student` (`id`, `name`, `tid`) VALUES (2, '小红', 1);
INSERT INTO `student` (`id`, `name`, `tid`) VALUES (3, '小张', 1);
INSERT INTO `student` (`id`, `name`, `tid`) VALUES (4, '小李', 1);
INSERT INTO `student` (`id`, `name`, `tid`) VALUES (5, '小王', 1);
```



### 测试环境搭建

1. 新建实体类Student Teacher
2. 建立Mapper接口
3. 建立Mapper.xml
4. 在核心配置文件中绑定我们的接口或者Mapper文件
5. 测试查询是否能够成功



### 按照查询进行嵌套处理

```xml
 <!--
        1.查询出来所有学生的信息
        2.根据查询出来所有的学生的tid找老师对应的id
    -->

    <select id="getStudent" resultMap="StudentTeacher">
        select * from mybatis.student
    </select>

    <resultMap id="StudentTeacher" type="Student">
        <result property="id" column="id"/>
        <result property="name" column="name"/>

        <!--
            复杂的属性我们要单独处理
            对象：association
            集合：collection
        -->
        <association property="teacher" column="tid" javaType="Teacher" select="getTeacher"/>
    </resultMap>

    <select id="getTeacher" resultType="Teacher">
        select * from mybatis.teacher where id = #{id}
    </select>
```



### 按照结果嵌套处理

```xml
<!-- 按照结果嵌套查询-->
<select id="getStudent2" resultMap="StudentTeacher2">
    select s.id sid,s.name sname,t.name tname
    from mybatis.student s,mybatis.teacher t
    where s.tid = t.id
</select>

<resultMap id="StudentTeacher2" type="Student">
    <association property="id" column="sid"/>
    <association property="name" column="sname"/>
    <association property="teacher" javaType="Teacher">
        <result property="name" column="tname"/>
    </association>
</resultMap>
```



回顾mysql多对一查询方式

- 子查询
- 联表查询



## 10.一对多的处理

1. 编写实体类

   ```java
   public class Student {
       private int id;
       private String name;
       //学生需要关联一个老师
       private int tid;
   
       public Student() {
       }
   
       public Student(int id, String name, int tid) {
           this.id = id;
           this.name = name;
           this.tid = tid;
       }
   
       public int getId() {
           return id;
       }
   
       public void setId(int id) {
           this.id = id;
       }
   
       public String getName() {
           return name;
       }
   
       public void setName(String name) {
           this.name = name;
       }
   
       public int getTid() {
           return tid;
       }
   
       public void setTid(int tid) {
           this.tid = tid;
       }
   
       @Override
       public String toString() {
           return "Student{" +
                   "id=" + id +
                   ", name='" + name + '\'' +
                   ", tid=" + tid +
                   '}';
       }
   }
   ```

   ```java
   public class Teacher {
       private int id;
       private String name;
       private List<Student> students;
   
       public Teacher(int id, String name, List<Student> students) {
           this.id = id;
           this.name = name;
           this.students = students;
       }
   
       public Teacher() {
       }
   
       public int getId() {
           return id;
       }
   
       public void setId(int id) {
           this.id = id;
       }
   
       public String getName() {
           return name;
       }
   
       public void setName(String name) {
           this.name = name;
       }
   
       public List<Student> getStudents() {
           return students;
       }
   
       public void setStudents(List<Student> students) {
           this.students = students;
       }
   
       @Override
       public String toString() {
           return "Teacher{" +
                   "id=" + id +
                   ", name='" + name + '\'' +
                   ", students=" + students +
                   '}';
       }
   }
   ```

2. 按照结果嵌套处理

   ```xml
   <select id="getTeacher" resultMap="TeacherStudent">
       select s.id sid,s.name sname,t.name tname,t.id tid
       from mybatis.student s,mybatis.teacher t
       where s.tid = t.id and t.id = #{tid};
   </select>
   <resultMap id="TeacherStudent" type="Teacher">
       <result property="id" column="tid"/>
       <result property="name" column="tname"/>
   
       <!--集合中的泛型我们使用ofType获取-->
       <collection property="students" ofType="Student">
           <result property="id" column="sid"/>
           <result property="name" column="sname"/>
           <result property="tid" column="tid"/>
       </collection>
   </resultMap>
   ```

   按照查询进行嵌套处理

   ```xml
   <select id="getTeacher2" resultMap="TeacherStudent2">
       select *
       from mybatis.teacher
       where id = #{tid};
   </select>
   <resultMap id="TeacherStudent2" type="Teacher">
       <collection property="students" javaType="ArrList" ofType="Student" select="getStudentByTeacherId" column="id"/>
   </resultMap>
   <select id="getStudentByTeacherId" resultType="Student">
       select * from mybatis.student where tid = #{tid}
   </select>
   ```



**小结**

关联：association 多对一

集合：collection   一对多

- javaType & ofType
  - javaType用来指定实体类中属性的类型
  - ofType用来指定映射到List或者集合中的pojo类型，泛型中的约束类型

注意点：

- 保证SQL的可读性，尽量保证通俗易懂
- 注意一对多和多对一中属性名和字段的问题





## 11.动态sql

**什么是动态SQL：动态sql就是根据不同的条件生成不同的SQL语句**



### 1.环境搭建

```sql
CREATE TABLE `blog`(
`id` VARCHAR(50) NOT NULL COMMENT '博客id',
`title` VARCHAR(100) NOT NULL COMMENT '博客标题',
`author` VARCHAR(30) NOT NULL COMMENT '博客作者',
`create_time` DATETIME NOT NULL COMMENT '创建时间',
`views` INT(30) NOT NULL COMMENT '浏览量'
)ENGINE=INNODB DEFAULT CHARSET=utf8
```



### 2.创建一个基础工程

1. 导包

2. 编写配置文件

3. 编写实体类

   ```java
   @Data
   public class Blog {
       private int id;
       private String title;
       private String author;
       private Date date;
       private int views;
   }
   ```

4. 编写实体类对应的接口及Mapper.xml

   ```java
   public interface BlogMapper {
       //插入数据
       int addBlog(Blog blog);
   }
   ```

   ```xml
   <insert id="addBlog" parameterType="blog">
       insert into mybatis.blog(id, title, author, create_time, views)
       values (#{id},#{title},#{author},#{createTime},#{views})
   </insert>
   ```

5. 测试

   ```java
   public class MyTest {
       @Test
       public void addBlogTest(){
           SqlSession sqlSession = MybatisUtils.getSqlSession();
           BlogMapper mapper = sqlSession.getMapper(BlogMapper.class);
           Blog blog = new Blog();
           blog.setId(IDUtils.getId());
           blog.setTitle("Mybatis从入门到放弃");
           blog.setAuthor("不知名人士");
           blog.setCreateTime(new Date());
           blog.setViews(9999);
   
           mapper.addBlog(blog);
   
           blog.setId(IDUtils.getId());
           blog.setTitle("Java从入门到入土");
           mapper.addBlog(blog);
   
           blog.setId(IDUtils.getId());
           blog.setTitle("微服务之如此困难");
           mapper.addBlog(blog);
   
   
           sqlSession.close();
       }
   ```



### 3.if

1. 实体类

2. 对应的接口及Mapper,xml

   ```java
   < //查询博客
       List<Blog> queryBlogIf(Map map);
   ```

   ```xml
   <select id="queryBlogIf" parameterType="map" resultType="blog">
       select * from mybatis.blog where 1=1
       <if test="title != null">
           and title = #{title}
       </if>
       <if test="author != null">
           and author = #{author}
       </if>
   </select>
   ```

3. 测试

   ```java
   @Test
   public void queryBlogTest(){
       SqlSession sqlSession = MybatisUtils.getSqlSession();
       BlogMapper mapper = sqlSession.getMapper(BlogMapper.class);
   
       Map map = new HashMap();
       map.put("title","Mybatis从入门到放弃");
       map.put("author","不知名人士");
       List<Blog> blogs = mapper.queryBlogIf(map);
       for (Blog blog : blogs) {
           System.out.println(blog);
       }
   
   }
   ```



### 4.choose(when,otherwise)

```xml
 
<select id="queryBlogChoose" parameterType="map" resultType="blog">
        select * from mybatis.blog
        <where>
            <choose>
                <when test="title != null">
                    title = #{title}
                </when>
                <when test="author != null">
                    and author = #{author}
                </when>
                <otherwise>
                    and views = #{views}
                </otherwise>
            </choose>
        </where>
    </select>

```

*where* 元素只会在子元素返回任何内容的情况下才插入 “WHERE” 子句。而且，若子句的开头为 “AND” 或 “OR”，*where* 元素也会将它们去除。

### 5.trim(set where)

```xml
<update id="UpdateBlog" parameterType="map">
    update mybatis.blog
    <set>
        <if test="title != null">
            title = #{title},
        </if>
        <if test="author != null">
            author = #{author}
        </if>
    </set>
    where id = #{id}
</update>
```

**所谓的动态SQL,本质还是sql语句，只是我们可以在sql层面，去执行一个逻辑代码**



### 6.SQL片段

有的时候我们可能会将一些公共的部分抽取出来，方便复用

步骤：

1. 使用sql标签抽取公共的部分

   ```xml
   <sql id="if-title-author">
       <if test="title != null">
           title = #{title}
       </if>
       <if test="author != null">
           and author = #{author}
       </if>
   </sql>
   ```

2. 在需要使用的地方使用include标签引用即可

   ```xml
   <select id="queryBlogIf" parameterType="map" resultType="blog">
       select * from mybatis.blog
       <where>
          <include refid="if-title-author"></include>
       </where>
   </select>
   ```



注意事项：

- 最好基于单表来定义SQL片段
- 不要存在where标签



### 7.foreach

***foreach* 元素的功能非常强大，它允许你指定一个集合，声明可以在元素体内使用的集合项（item）和索引（index）变量。它也允许你指定开头与结尾的字符串以及集合项迭代之间的分隔符。这个元素也不会错误地添加多余的分隔符，看它多智能！**

```xml
<select id="queryBlogForEach" parameterType="map" resultType="blog">
    select *
    from mybatis.blog
    <where>
        <foreach collection="ids" item="id" open="and (" close=")" separator="or">
            id =#{id}
        </foreach>
    </where>
</select>
```

测试

```java
@Test
public void queryBlogForEachTest(){
    SqlSession sqlSession = MybatisUtils.getSqlSession();
    BlogMapper mapper = sqlSession.getMapper(BlogMapper.class);
    Map map = new HashMap();

    List<Integer> ids = new ArrayList<Integer>();
    ids.add(1);
    ids.add(2);
    map.put("ids",ids);
    List<Blog> blogs = mapper.queryBlogForEach(map);
    for (Blog blog : blogs) {
        System.out.println(blog);
    }
}
```



**动态SQL就是拼接SQL语句，我们只要保证sql语句的正确性，按照sql的样式，去排列组合就可以了**



## 12.Mybatis缓存

### 1.简介

什么是缓存【cache】？

- 存在内存中的临时数据
- 将用户经常查询的数据放在缓存中，用户查询数据就不用从磁盘上（关系型数据库数据文件）查询，从缓存中查询，从而提高查询效率，解决了高并发系统的性能问题

为什么使用缓存：

- 减少和数据库交互次数，减少系统开销，提高系统效率

什么样的数据能使用缓存？

- 经常查询且不经常改变的数据





### 2.Mybatis缓存

- mybatis包含一个非常强大的查询缓存特性，它可以非常方便的定制和配置缓存。缓存可以极大地提升查询效率
- Mybatis系统中默认定义了两级缓存:一级缓存和二级缓存
  - 默认情况下，只有一级缓存开启。（SqlSession级别的缓存，也称为本地缓存）
  - 二级缓存需要手动开启和配置，它是基于namesspace级别的缓存
  - 为了提高扩展性，Mybatis定义了缓存接口CaChe。我们可以通过实现Cache接口老自定义二级缓存



### 3.一级缓存

测试步骤：

1. 开启日志
2. 测试一个Sesion中查询两次相同记录
3. 查看日志输出



缓存失效的情况：

1. 增删改操作，可能会改变原来的数据，所以必定会刷新缓存

   ![image-20210829205654249](F:\笔记图片\img\image-20210829205654249.png)

   ![image-20210829205728383](F:\笔记图片\img\image-20210829205728383.png)

2. 查询不同的东西

3. 查询不同的Mapper

4. 手动清理缓存

   clearCache();

小结：一级缓存是默认开启的，只在一次SqlSession中有效，也就是拿到连接到关闭连接这个区间段



### 4.二级缓存

- 二级缓存也叫全局缓存，一级缓存作用域太低了，所以诞生了二级缓存
- 基于namespace级别的缓存，一个名称空间，对应一个二级缓存
- 工作机制
  - 一个会话查询一条数据，这个数据就会被放在当前会话的一级缓存中
  - 如果当前会话关闭了，这个会话对应的一级缓存就没了，但是我们想要的是会话关闭了，一级缓存中的数据会被保存到二级缓存中
  - 新的会话查询信息，就可以从二级缓存中获取内容
  - 不同的mapper查出的数据就会放在自己对应的缓存中



步骤：

1. 开启全局缓存

   ```xml
   <!--显式的开启二级缓存-->
   <setting name="cacheEnabled" value="true"/>
   ```

2. 在要使用二级缓存的mapper中开启二级缓存

   ```xml
   !--    在当前mapper.xml中开启二级缓存-->
       <cache/>
   ```

3. 测试

   我们需要将实体类序列化！否则就会报错



小结：

- 只要开启了二级缓存，在同一个Mapper下就有效
- 所有的数据都会先放到一级缓存中，
- 只有当会话提交，或者关闭的时候，才会提交到二级缓存中



### 5.缓存原理

![](F:\笔记图片\img\image-20210830091838671.png)

**缓存顺序**

1. 先看二级缓存中有没有
2. 再看一级缓存中有没有
3. 查询数据库



### 6.自定义缓存-ehchche

**Ehcache是一种广泛使用的开源Java分布式缓存。主要面向通用缓存**

要在程序中使用，先要导包

在mapper中指定使用我们的ehcache缓存实现

```xml
<cache type="org.mybatis.caches.ehcache.EhcacheCache"/>
```



**ehcache.xml**

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<ehcache xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
        xsi:noNamespaceSchemaLocation="http://ehcache.org/ehcache.xsd"
        updateCheck="false">

    <diskStore path="./tmpdir/Tmp_EhCache"/>

    <defaultCache
        eternal="false"
        maxElementsInMemory="10000"
        overflowToDisk="false"
        diskPersistent="false"
        timeToIdleSeconds="1800"
        timeToLiveSeconds="259200"
        memoryStoreEvictionPolicy="LRU"/>


    <cache
        name="cloud_user"
        eternal="false"
        maxElementsInMemory="5000"
        overflowToDisk="false"
        diskPersistent="false"
        timeToIdleSeconds="1800"
        timeToLiveSeconds="1800"
        memoryStoreEvictionPolicy="LRU"/>
</ehcache>
```

