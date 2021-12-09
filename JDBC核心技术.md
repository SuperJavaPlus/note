# JDBC

### 1.数据库驱动

![无标题](E:\img\无标题.png)

我们的程序会通过 数据库驱动，和数据库打交道

### 2. JDBC

​		SUN公司为了简化开发人员的（对数据库的统一）操作，提供了一个（java操作数据库的）规范，俗称JDBC

![image-20210727130712771](C:\Users\dell\AppData\Roaming\Typora\typora-user-images\image-20210727130712771.png)

java.sql

javax.sql

还需要导入一个数据库驱动包

### 3.JDBC程序

> 创建测试数据库

```mysql
CREATE DATABASE jdbcStudy CHARACTER SET utf8 COLLATE utf8_general_ci;

USE jdbcStudy;

CREATE TABLE `users`(
	id INT PRIMARY KEY,
	NAME VARCHAR(40),
	PASSWORD VARCHAR(40),
	email VARCHAR(60),
	birthday DATE
);

INSERT INTO users()
VALUES(1,'zhansan','123456','zs@sina.com','1980-12-04'),
(2,'lisi','123456','lisi@sina.com','1981-12-04'),
(3,'wangwu','123456','wangwu@sina.com','1979-12-04');
```

1.创建一个普通项目

2.导入数据库驱动

3.编写测试代码

```java
public class JdbcFirstDemo {
    public static void main(String[] args) throws Exception{
        //1.加载驱动
        Class.forName("com.mysql.jdbc.Driver"); //固定写法
        //2.用户信息和url
        String url = "jdbc:mysql://localhost:3306/jdbcstudy?useUnicode=true&characterEncoding=utf8&useSSL=true";
        String username = "root";
        String password = "123456";

        //3.连接成功，数据库对象    connection代表数据库
        Connection connection = DriverManager.getConnection(url, username, password);

        //4.执行sql的对象 statement  执行sql的对象
        Statement statement = connection.createStatement();

        //5.执行sql的对象
        String sql = "SELECT * FROM users";
        ResultSet resultSet = statement.executeQuery(sql);//结果集封装了全部查询出来的结果
        while (resultSet.next()){
            System.out.println("id=" + resultSet.getString("id"));
            System.out.println("name=" + resultSet.getString("NAME"));
            System.out.println("email=" + resultSet.getString("email"));
            System.out.println("birth=" + resultSet.getString("birthday"));
        }

        //6.断开连接
        resultSet.close();
        statement.close();
        connection.close();
    }
}
```

步骤总结：

1.加载驱动

2.建立连接DriverManager

3.获得执行的sql对象 Statement

4.获得返回结果集

5.释放连接



> DriverManager

```java
Class.forName("com.mysql.jdbc.Driver");

Connection connection = DriverManager.getConnection(url, username, password);

//connection代表数据库
//数据设置自动提交
//事务提交
//事务回滚

```



> URL

```JAVA
 String url = "jdbc:mysql://localhost:3306/jdbcstudy?
 //jdbc:协议://主机地址:端口号/数据库名?参数1参数2&参数3.....
```



>Statement  执行sql的对象

```java
String sql = "SELECT * FROM users";  //编写sql
statement.executeQuery();  //查询操作返回RsultSet
statement.execute();       //执行任何sql
statement.executeUpdate();//更新，插入，删除都是用这个，返回一个受影响的行数

```



> ResultSet 查询的结果集：封装了所有的查询结果

获得指定的数据类型

```java
resultSet.getObject(); //在不知道列的属性的情况下使用
resultSet.getString();
resultSet.getInt();
resultSet.getFloat();
........
```

遍历，指针

```java
resulSet.beforeFirst(); //移动到最前面
resulSet.afterList();//移动到最后面
resulSet.next();//移动到下一个数据
resulSet.previous();//移动到前一行
resulSet.absolute();//移动到指定行

```



> 释放资源

```java
resultSet.close();
statement.close();
connection.close();
```

### 4.statement对象

jdbc中的statement对象用于向数据库发送sql语句，想完成对数据库的增删改查，只需要通过这个对象像数据库发送增删改查语句即可。

statement对象的executeUpadte方法，用于向数据库发送增删改的sql语句，executeUpadte执行完后，将会返回一个整数（即增删改语句导致数据库几行数据发生了变化）



> CRUD操作-create

```java
Statement st = conneciton.createStatement();
String sql = "insert into user(.....) values(......)"
int num = statement.excuteUpdate(sql);
if(num > 0){
    System.out.println("插入成功");
}
```

> CRUD操作-delete

```java
Statement st = conneciton.createStatement();
String sql = "delete from user where id = 1";
int num = statement.excuteUpdate(sql);
if(num > 0){
    System.out.println("删除成功");
}
```



> 代码实现

1.提取工具类

```java
package com.jdbc.lesson2.utils;

import java.io.IOException;
import java.io.InputStream;
import java.sql.*;
import java.util.Properties;

public class JdbcUtils {
    private static String driver = null;
    private static String url = null;
    private static String username = null;
    private static String password = null;
    static {
        try{
            InputStream in = JdbcUtils.class.getClassLoader().getResourceAsStream("db.properties");
            Properties properties = new Properties();
            properties.load(in);
            driver = properties.getProperty("driver");
            url = properties.getProperty("url");
            username = properties.getProperty("username");
            password = properties.getProperty("password");

            //1.驱动只用加载一次
            try {
                Class.forName(driver);
            } catch (ClassNotFoundException e) {
                e.printStackTrace();
            }
        } catch (IOException e) {
            e.printStackTrace();
        }
    }
    //获取连接
    public static Connection getConnection() throws SQLException {
        return DriverManager.getConnection(url,username,password);
    }

    //释放资源
    public static void release(Connection connection, Statement statement, ResultSet resultSet){
        if (connection != null){
            try {
                connection.close();
            } catch (SQLException throwables) {
                throwables.printStackTrace();
            }
        }
        if (statement != null){
            try {
                statement.close();
            } catch (SQLException throwables) {
                throwables.printStackTrace();
            }
        }
        if (resultSet != null){
            try {
                resultSet.close();
            } catch (SQLException throwables) {
                throwables.printStackTrace();
            }
        }
    }
}

```



2.编写增删改的方法，excuteUpdate

```java
package com.jdbc.lesson2;

import com.jdbc.lesson2.utils.JdbcUtils;

import java.sql.Connection;
import java.sql.ResultSet;
import java.sql.SQLException;
import java.sql.Statement;

public class InsertTest {
    public static void main(String[] args) {
        Connection connection = null;
        Statement statement = null;
        ResultSet resultSet = null;
        try {
            connection = JdbcUtils.getConnection();
            statement = connection.createStatement();
            String sql = "INSERT INTO users()" +
                    "VALUES(4,'jeery','123456','508486509@qq.com','1999-07-07')";
            int i = statement.executeUpdate(sql);
            if (i > 0){
                System.out.println("插入成功");
            }
        } catch (SQLException e) {
            e.printStackTrace();
        }finally {
            JdbcUtils.release(connection,statement,resultSet);
        }
    }
}

```

```java
package com.jdbc.lesson2;

import com.jdbc.lesson2.utils.JdbcUtils;

import java.sql.*;

public class DeleteTest {

    public static void main(String[] args) {
        Connection connection = null;
        Statement statement = null;
        ResultSet resultSet = null;
        try {
             connection = JdbcUtils.getConnection();
             statement = connection.createStatement();
             String sql = "DELETE FROM users WHERE id = 4";
            int i = statement.executeUpdate(sql);
            if (i > 0){
                System.out.println("删除成功");
            }
        } catch (SQLException e) {
            e.printStackTrace();
        }finally {
            JdbcUtils.release(connection,statement,resultSet);
        }
    }
}

```

```java
package com.jdbc.lesson2.utils;

import java.sql.Connection;
import java.sql.ResultSet;
import java.sql.SQLException;
import java.sql.Statement;

public class UpdateTest {
    public static void main(String[] args) {
        Connection conn = null;
        Statement statement = null;
        ResultSet resultSet = null;
        try {
            conn = JdbcUtils.getConnection();
            statement = conn.createStatement();
            String sql = "UPDATE users SET NAME = 'zheng',email = '508486509@qq.com' WHERE id = 1";
            int i = statement.executeUpdate(sql);
            if (i > 0){
                System.out.println("修改成功！！！");
            }
        } catch (SQLException e) {
            e.printStackTrace();
        }finally {
            JdbcUtils.release(conn,statement,resultSet);
        }
    }
}

```

3.查询

```java
package com.jdbc.lesson2;

import com.jdbc.lesson2.utils.JdbcUtils;

import java.sql.Connection;
import java.sql.ResultSet;
import java.sql.SQLException;
import java.sql.Statement;

public class SelectTest {
    public static void main(String[] args) {
        Connection connection = null;
        Statement statement = null;
        ResultSet resultSet = null;
        try {
           connection =  JdbcUtils.getConnection();
           statement = connection.createStatement();
           String sql = "SELECT * FROM users";
            ResultSet resultSet1 = statement.executeQuery(sql);
            while (resultSet1.next()){
                System.out.println("id = " + resultSet1.getString("id"));
                System.out.println("name = " + resultSet1.getString("NAME"));
                System.out.println("pss = " + resultSet1.getString("PASSWORD"));
                System.out.println("email = " + resultSet1.getString("email"));
                System.out.println("birthday = " + resultSet1.getString("birthday"));
            }
        } catch (SQLException e) {
            e.printStackTrace();
        }finally {
            JdbcUtils.release(connection,statement,resultSet);
        }
    }
}

```



> sql注入的问题

sql存在漏洞，会被攻击，导致数据泄露





### 5.PreparedStatement对象

可以防止sql注入，并且效率更高

1.新增

```java
package com.jdbc.lesson3;

import com.jdbc.lesson2.utils.JdbcUtils;
import netscape.security.UserTarget;

import java.util.Date;
import java.sql.*;

public class InsertTest {
    public static void main(String[] args) {
        Connection connection = null;
        PreparedStatement pstm = null;
        ResultSet resultSet = null;
        try {
           connection = JdbcUtils.getConnection();

           //使用?占位符代替参数
           String sql = "insert into users(id,`NAME`,`PASSWORD`,`email`,`birthday`) values(?,?,?,?,?)";
           pstm = connection.prepareStatement(sql);//预编译sql,先写sql，不执行

           //手动给参数赋值
            pstm.setInt(1,4);
            pstm.setString(2,"zgx");
            pstm.setString(3,"123456");
            pstm.setString(4,"15698712@qq.com");
            pstm.setDate(5,new java.sql.Date(new Date().getTime()));

            //执行
            int i = pstm.executeUpdate();
            if (i > 0){
                System.out.println("插入成功");
            }
        } catch (SQLException e) {
            e.printStackTrace();
        }finally {
            JdbcUtils.release(connection,pstm, resultSet);
        }
    }
}

```



2.删除

```java
package com.jdbc.lesson3;

import com.jdbc.lesson2.utils.JdbcUtils;

import java.sql.Connection;
import java.sql.PreparedStatement;
import java.sql.SQLException;

public class DeleteTest {
    public static void main(String[] args) {
        Connection conn = null;
        PreparedStatement st = null;
        try {
           conn = JdbcUtils.getConnection();
           String sql = "delete from users where id = ?";
           st = conn.prepareStatement(sql);
           st.setInt(1,4);
            int i = st.executeUpdate();
            if (i > 0){
                System.out.println("删除成功");
            }
        } catch (SQLException e) {
            e.printStackTrace();
        }finally {
            JdbcUtils.release(conn,st,null);
        }
    }
}

```



3.查询

```JAVA
package com.jdbc.lesson3;

import com.jdbc.lesson2.utils.JdbcUtils;

import java.sql.Connection;
import java.sql.PreparedStatement;
import java.sql.ResultSet;
import java.sql.SQLException;

public class SelectTest {
    public static void main(String[] args) {
        Connection connection = null;
        PreparedStatement st = null;
        ResultSet resultSet = null;
        try {
            connection = JdbcUtils.getConnection();
            String sql = "select * from users where id = ?";
            st = connection.prepareStatement(sql);
            st.setInt(1, 3);
            resultSet = st.executeQuery();
            while (resultSet.next()) {
                System.out.println(resultSet.getString("NAME"));
            }
        } catch (SQLException e) {
            e.printStackTrace();
        } finally {
            JdbcUtils.release(connection, st, resultSet);
        }
    }
}

```

### 5.事务

要么都成功，要么都失败

> ACID原则

原子性：要么全部完成，要么都不完成

一致性：总数不变

隔离性：多个进程互不干扰

持久性：一旦提交不可逆，持久化到数据库了



> 代码实现

1.开启事务  connection.setAutoCommit(false);

2.一组业务成功，提交事务

3.可以在catch语句中显示的定义回滚语句，但默认失败就是回滚





### 6.数据库连接池

数据库连接--执行完毕--释放    **连接---释放十分浪费资源**

**池化技术**：准备一些预先的资源，过来就连接预先准备好

 



编写连接池，实现一个接口 DateSourse



> 开源数据源实现

DBCP

C3P0

Druid

使用了这些数据库连接池之后，我们在项目开发中就不需要编写连接数据库的代码了

> DBCP

需要用到的jar包

commons-dbcp-1.4  commons-pool-1.6

> 代码实现

```java
package com.jdbc.lesson5.utils;

import org.apache.commons.dbcp.BasicDataSource;
import org.apache.commons.dbcp.BasicDataSourceFactory;

import javax.sql.DataSource;
import java.io.IOException;
import java.io.InputStream;
import java.sql.*;
import java.util.Properties;

public class JdbcUtils_DBCP {
    private static DataSource dataSource = null;
    static {
        try{
            InputStream in = JdbcUtils_DBCP.class.getClassLoader().getResourceAsStream("dbcpconfig.properties");
            Properties properties = new Properties();
            properties.load(in);


            //创建数据源
            dataSource = BasicDataSourceFactory.createDataSource(properties);


        } catch (Exception e) {
                e.printStackTrace();
            }

    }
    //获取连接
    public static Connection getConnection() throws SQLException {
        return dataSource.getConnection(); //从数据源获取连接
    }

    //释放资源
    public static void release(Connection connection, Statement statement, ResultSet resultSet){
        if (connection != null){
            try {
                connection.close();
            } catch (SQLException throwables) {
                throwables.printStackTrace();
            }
        }
        if (statement != null){
            try {
                statement.close();
            } catch (SQLException throwables) {
                throwables.printStackTrace();
            }
        }
        if (resultSet != null){
            try {
                resultSet.close();
            } catch (SQLException throwables) {
                throwables.printStackTrace();
            }
        }
    }
}

```

```java
package com.jdbc.lesson5;

import com.jdbc.lesson2.utils.JdbcUtils;
import com.jdbc.lesson5.utils.JdbcUtils_DBCP;

import java.sql.Connection;
import java.sql.PreparedStatement;
import java.sql.ResultSet;
import java.sql.SQLException;
import java.util.Date;

public class TestJDCP {
    public static void main(String[] args) {
        Connection connection = null;
        PreparedStatement pstm = null;
        ResultSet resultSet = null;
        try {
            connection = JdbcUtils_DBCP.getConnection();

            //使用?占位符代替参数
            String sql = "insert into users(id,`NAME`,`PASSWORD`,`email`,`birthday`) values(?,?,?,?,?)";
            pstm = connection.prepareStatement(sql);//预编译sql,先写sql，不执行

            //手动给参数赋值
            pstm.setInt(1,4);
            pstm.setString(2,"zgx");
            pstm.setString(3,"123456");
            pstm.setString(4,"15698712@qq.com");
            pstm.setDate(5,new java.sql.Date(new Date().getTime()));

            //执行
            int i = pstm.executeUpdate();
            if (i > 0){
                System.out.println("插入成功");
            }
        } catch (SQLException e) {
            e.printStackTrace();
        }finally {
            JdbcUtils_DBCP.release(connection,pstm, resultSet);
        }
    }
}
```





> C3P0

需要导入的jar包

c3p0-0.9.1.2.jar
