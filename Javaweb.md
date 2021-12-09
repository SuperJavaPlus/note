## Javaweb

### 1.xml

基本概念：可扩展标记语言

> 可扩展：标签都是自定义的

- 功能：存储数据：

​					1.配置文件

​					2.在网络中传输

- xml与html的区别：xml的标签都是自定义的，html的标签都是预定义的

​							  xml的语法很严格

​							  xml存储数据，html展示数据

> 基本语法

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<users>
    <user id = '1'>
        <name>张三</name>
        <age>18</age>
        <gender>male</gender>
    </user>
    <user>
        <name>李四</name>
        <age>26</age>
        <gender>female</gender>
    </user>
</users>
```

第一行必须为文档声明

文档中有且仅有一个根标签

属性值必须使用引号



> xml组成部分

1.文档声明

- <?xml 属性列表 ?>

- 属性列表：version：版本号

  ​				  encoding：编码方式

  ​			 	 standalone:是否独立

2.指令

3.标签：自定义的

- 规则：数字或标点符号不能开头

  ​		   名称不能以字母xml（或XML,Xml）等开始

  ​		   名称不能包含空格

4.标签属性

5.文本：CDATA区：在该区域的数据会被原样展示

​			格式：<![CDATA[

​								]]>

#### 1.1约束

规定xml的书写规则

作为框架的使用者，能够在xml文件中引入约束文档，能够简单的读懂约束文档

分类：

**DTD约束：**一种简单的约束

​		   Schema约束：一种复杂的约束技术

> 引入dtd文档到xml文档中

内部dtd:将约束规则定义在xml文档中

外部dtd:将约束的规则定义在外部的dtd文件中

​	 本地：<!DOCTYPE 根标签名 SYSTEM  "dtd文件的位置">

​	 网络：<!DOCTYPE 根标签名 PUBLIC "dtd文件的名字" "dtd文件的位置URL">

**Schema约束**： 

引入：

1. 填写xml文档的根元素
2. 引入xsi前缀.  xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
3. 引入xsd文件命名空间.  xsi:schemaLocation="http://www.itcast.cn/xml  student.xsd"
4. 为每一个xsd约束声明一个前缀,作为标识  xmlns="http://www.itcast.cn/xml" 

#### 1.2解析

操作xml文档，将文档中的数据读取到内存中

 解析xml的方式：

1. DOM:将标记语言文档一次性加载进内存，在内存中形成一颗dom树

   优点：操作方便，可以对文档进行CRUD的所有操作

   缺点：比较消耗内存

2. SAX:逐行读取，基于事件驱动的 

   优点：不占内存

   缺点：只能读取，不能增删改

> 常见的解析器

1. JAXP
2. DOM4J
3. Jsoup:jsoup 是一款Java 的HTML解析器，可直接解析某个URL地址、HTML文本内容。它提供了一套非常省力的API，可通过DOM，CSS以及类似于jQuery的操作方法来取出和操作数据。



> Jsoup

快速入门：

步骤：

1. 导入jar包
2. 获取Document对象
3. 获取对应的标签的Element对象
4. 获取数据

```java
import org.jsoup.Jsoup;
import org.jsoup.nodes.Document;
import org.jsoup.nodes.Element;
import org.jsoup.select.Elements;

import java.io.File;
import java.io.IOException;

public class JsoupDemo1 {

    public static void main(String[] args) throws IOException {

        //2.获取Document对象，根据xml文档获取
        //2.1 获取student.xml的path
        String path = JsoupDemo1.class.getClassLoader().getResource("student.xml").getPath();
        //2.2 解析文档对象，加载文档对象到内存，获取dom树—————>Document对象
        Document document = Jsoup.parse(new File(path), "utf-8");
        //3.获取元素对象
        Elements elements = document.getElementsByTag("name");
        //3.1获取第一个name的element对象
        Element element = elements.get(0);
        //3.2获取数据
        String text = element.text();
        System.out.println(text);

    }
} 
```



> 对象的使用

Jsoup:工具类，可以解析html或xml文档，返回Document

- parse:解析html文档或xml文档，返回Document  
- parse(File in,String path):解析xml或html文件
- parse(URL url,int timeoutMillis):通过网络路径获取指定的html或xml文档对象

Document:文档对象，代表内存中的dom树 

- 获取Element对象
  - getElementsByTag(String tagName):根据标签名称获取元素对象的集合
  - getElementsByAttribute(String key):根据属性获取元素对象集合
  - getElementsBy



Elements:元素Element对象的集合。可以当作ArrayList<Element>来使用 

Element：元素对象

1. 获取子元素对象
2. 获取属性值
   - String attr(String key):根据属性名称获取属性值 
3. 获取文本
   - String text():获取文本
   - String html():获取标签体的所有内容

Node:节点对象

> 快捷查询方式

1.selector选择器

使用的方法：Elements select(String cssQuery)

```java
public class JsoupDemo5 {
    public static void main(String[] args) throws IOException {
        String path = JsoupDemo5.class.getClassLoader().getResource("student.xml").getPath();

        Document document = Jsoup.parse(new File(path), "utf-8");

        Elements name = document.select("name");
        System.out.println(name);
        System.out.println("---------------------------------------------------");

        //
        Elements select = document.select("#itcast");
        System.out.println(select);

        System.out.println("---------------------------------------------------");
        Elements select1 = document.select("student[number='heima_0001']");
        System.out.println(select1);

        System.out.println("--------------------------------------");
        Elements select2 = document.select("student[number='heima_0001'] > age");
        System.out.println(select2);
```

2.XPath: **XPath**即为[XML](https://baike.baidu.com/item/XML)路径语言（XML Path Language），它是一种用来确定XML文档中某部分位置的语言。

使用Jsoup的XPath需要额外导入jar包

###  2.web相关概念

C/S：客户端/服务器端

B/S：浏览器/服务器端



资源分类

1. 静态资源：所有用户访问后，得到的结果都是一样的，称为静态资源。静态资源可以直接被浏览器解析

   如 html.css.JavaScript

2. 动态资源：每个用户访问相同资源后，得到的结果可能不一样。。动态资源在被访问之后，需要先转换为静态资源，再返回给浏览器

   如 servlet / jsp php,asp...

3. 网络通信三要素：

   IP：电子设备在网络中的唯一标识

   端口号：应用程序在计算机中的唯一表示

   传输协议：规定了数据通信的规则

### 3.web服务器软件

服务器：安装了服务器软件的计算机

服务器软件：接受用户的请求，处理请求，做出响应

web服务器软件（web容器）：在web服务器软件中，可以部署web项目，让用户通过浏览器来访问这些项目

- 常见的java相关的web服务器

  weblogic

  webSphere

  JBOSS

  Tomcat

访问TomCat:浏览器输入：http://locast:8080

​										 http://别人的ip地址:8080

TomCat部署项目方式：

1. 直接将项目放到webapps目录下即可

   /项目名称       虚拟路径

   http://localhost:8080/hello/hello.html

2. 配置conf/server.xml文件

   在<host>标签中配置

   - ```xml
     <Context docBase = "E:\hello" path = "/hehe"/>
     
     <!-- docBase: 项目存放的路径 -->
     <!-- path: 虚拟目录 -->
     ```

3. 在conf\Catalina\localhost下创建任意名称的xml文件，在文件中编写<Context docBase = "E:\hello" />

   虚拟目录：xml文件的名称



### 4.Servlet

运行在服务器端的小程序

Servlet就是一个接口，定义了java类被浏览器访问到（Tomcat识别）的规则

将来我们自定义一个类，实现Servlet接口，复写方法

#### 4.1快速入门：

1. 创建javaEE项目

2. 定义一个类，实现Servlet接口

3. 实现接口中的抽象方法

4. 配置Servlet(在web.xml中配置)

   ```xml
   <!-- 配置Servlet -->
   <servlet>
       <servlet-name>demo1</servlet-name>
       <servlet-class>cn.itcast.web.servlet.ServletDemo1</servlet-class>
   </servlet>
   
   <servlet-mapping>
       <servlet-name>demo1</servlet-name>
       <url-pattern>/demo1</url-pattern>
   </servlet-mapping>
   ```

#### 4.2执行原理

1. 当服务器接收到客户端浏览器的请求后，会解析请求的URL路径，获取访问的Servlet的资源路径
2. 查找web.xml文件，是否有对应的<url-pattern>标签体内容
3. 如果有，则找到对应的<servlet-class>全类名
4. tomcat会将字节码文件加载到内存，并且创建其对象
5. 调用其方法

#### 4.3Servlet生命周期

1. 被创建：执行init方法，只执行一次

   Servlet什么时候被创建

   - 默认情况下，Servlet第一次被访问时创建
   - 可以配置执行Servlet的创建时机
     - 在<servlet>下配置
       1. 第一次被访问时创建 <load-on-startup>的值为负数
       2. 服务器启动时被创建 <load-on-startup>的值为0或正数

   Servlet的init方法，只执行一次，说明一个Servlet在内存中只存在一个对象，Servlet时单例的

   - 多个用户同时访问时可能存在线程安全
   - 尽量不要再Servlet中定义成员变量

2. 提供服务：执行service方法，执行多次

3. 被销毁：执行destroy方法，只执行一次

> Servlet3.0

支持注解配置，可以不需要web.xml

步骤：

1. 创建JavaEE项目，选择Servlet版本3.0以上，可以不用创建web.xml

2. 定义一个类，实现Servlet接口

3. 复写方法

4. 在类上使用@@WebServlet注解，进行配置

   @WebServlet("/资源 路径")

WEB-INF下的目录不能被浏览器直接访问

> Servlet体系结构

Servlet         -----        接口

​	|

GenericServlet -----    抽象实现类

​	|

HttpServlet 	-----	抽象类 

GenericServlet：将Servlet接口中其他方法做了默认空实现，只将Service（）方法作为抽象

HttpServlet： 对http协议的一种封装，简化操作

1. 定义类继承HttpServlet
2. 复写doGet()/doPost()方法

> Servlet相关配置

1. urlparttern:Servlet访问路径
   1. 一个Servlet可以定义多个访问路径
   2. 路径定义规则
      1. /xxx
      2. /xxx/xxx
      3. *.后缀名

### 5.Http

概念 Hyper Text Transfer Protocol  超文本传输协议

> 传输协议：定义了客户端和服务器端通信时，发送数据的格式

特点：

1. 基于TCP/IP的高级协议
2. 默认端口号：80
3. 基于请求响应/模型的：一次请求一次响应
4. 无状态的：每次请求之间相互独立，不能交互数据

> 请求消息数据格式:客户端发送给服务端的数据

1. 请求行

   请求方式  请求url  请求协议/版本

   - 请求方式
     - http有7种请求方式，常用的有2种
       - GET：请求方式在请求行中；请求的url长度时有限制的；不太安全 
       - POST：请求参数在请求体中，请求的url长度是没有限制的

2. 请求头：客户端浏览器告诉服务器一些信息

   请求头名称：请求头值

   常见的请求头：

   1. User-Agent:浏览器告诉服务器，访问服务器使用的浏览器版本信息

   2. Referer:告诉服务器，当前请求从哪里来

      作用：防盗链

      ​		   统计工作 

3. 请求空行

   空行，就是用于分割POST请求的请求头和请求体的

4. 请求体（正文）

   封装POST请求消息的请求参数的

### 6.Request

request对象和response对象的原理

1. request对象和response对象是服务器创建的，我们来使用它们
2. request对象是来获取请求消息的，response对象是来设置响应消息

> request对象体系结构：

ServletRequest		---		接口

​			|继承

HttpSeverletRequest	-		接口

​			|实现

RequestFacade(Tomcat创建的类)



> request功能

1. 获取请求消息数据

   1. 获取请求行数据

      *GET/servlet2/demo1?name = zhangsan HTTP/1.1

      - 获取请求方式

        String getMethod()

      - *获取虚拟目录：    /servlet2

        String getContextPath()

      - 获取Servlet路径 ： /demo1

        String getServletPath()

      - 获取get方式请求参数 ：name = zhangsan

        String getQueryString()  

      - *获取请求URI： /servlet2/demo1

        String getRequestURI()

        URL:统一资源定位符	http://localhost/servlet2/RequestDemo1

        URI：统一资源标识符	/servlet2/RequestDemo1

      - 获取协议及版本

        String getProtocol()

      - 获取客户机的IP地址

        String getRemoteAddr()

      

   2. 获取请求头数据

      String getHeader(Sring name):通过请求头的名称获取请求头的值

      Enumeration<String> getHeaderNames():获取所有的请求头名称

      ```java
      @WebServlet("/RequestDemo2")
      public class RequestDemo2 extends HttpServlet {
          protected void doGet(HttpServletRequest request, HttpServletResponse response)throws ServletException, IOException {
              //获取请求头数据
      
              //获取所有的请求头名称
              Enumeration<String> headerNames = request.getHeaderNames();
              while (headerNames.hasMoreElements()){
                  String name = headerNames.nextElement();
                  //根据名称获取请求头信息
                  String header = request.getHeader(name);
                  System.out.println(header + "-----" + name);
              }
      
          }
          protected void doPost(HttpServletRequest request,HttpServletResponse response)throws ServletException, IOException{
      
          }
      }
      ```

      

   3. 获取请求体数据
   
      只有POST请求方式才有请求体，在请求体中封装了POST请求的请求参数
   
       步骤：
   
      1. 获取流对象
   
         BufferedReader getReader():获取字符输入流，只操作字符数据
   
         ServletInputStream getInputStream():获取字节输入流，可以操作所有类型数据
   
      2. 再从流对象中拿数据
   
2. 其他功能

   1. 获取请求参数通用方式

      String getParameter(String name):根据参数名称获取参数值

      String[] getParameterValues(String name):根据参数名称获取参数值的数组

      Enumeration<String> getParameterNames():获取所有请求的参数名称

      Map<String,String[]> getParameterMap():获取所有参数的map集合

      ```java
      @WebServlet("/RequestDemo6")
      public class RequestDemo6 extends HttpServlet {
          protected void doPost(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
      
              String username = request.getParameter("username");
              System.out.println("post");
              System.out.println(username);
      //-----------------------------------------------------------------
              String[] hobbies = request.getParameterValues("hobby");
              for (String hobby: hobbies){
                  System.out.println(hobby);
              }
      //--------------------------------------------------------------------
              Map<String, String[]> parameterMap = request.getParameterMap();
              Set<String> keySet = parameterMap.keySet();
              //遍历
              for (String  name : keySet){
                  String[] values = parameterMap.get(name);
                  System.out.println(name);
                  for (String value : values){
                      System.out.println(value);
                  }
                  System.out.println("---------------------------------");
              }
          }
      
          protected void doGet(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
             this.doPost(request,response);
          }
      }
      ```

      get方式：tomcat 8 已经将get方式乱码问题解决

      post方式：在获取参数前，设置request的编码request.setCharacterEncoding();

   2. 请求转发:一种在服务器内部的资源跳转方式

      步骤

      1. 通过request对象获取请求转发器对象

         RequestDispatcher getRequestDispatcher(String path)

      2. 使用RequestDispatcher对象来进行转发:

         forward(ServletRequest request,ServletResponse response)

      特点：

      1. 浏览器地址栏没有发生变化
      2. 只能转发到当前服务器的内部资源中
      3. 转发是一次请求

   3. 共享数据

      域对象：一个有作用范围的对象，可以在范围内共享数据

      request域：代表一次请求的范围，一般用于请求转发多个资源中共享数据

      方法：

      1. setAttribute(String name,Object obj) :存储数据
      2. Object getAttribute(String name):通过键获取值
      3. removeAttribute(String name):通过键移除键值对

   4. 获取Servletcontext

      Servletontext getServletcontext() 

### 7.Response

> 响应消息：服务器端发送给客户端的数据

响应行

1. 组成：协议/版本 响应状态码  状态码

2. 响应状态码：服务器告诉客户端浏览器本次请求和响应的一个状态

   分类：

   1. 1xx:服务器接收客户消息，但没有接受完成，等待一段时间后，发送1xx状态码
   2. 2xx:成功。代表 200
   3. 3xx：重定向。代表：302（重定向），304（访问缓存）
   4. 4xx：客户端错误       404（请求路径没有对应的资源） 405（请求方式没有对应的doxxx方法）
   5. 5xx：服务器错误       500（服务器内部出现异常）

响应头

1. 格式：头名称：值

2. 常见的响应头：

   Content-Type:服务器告诉客户端本次响应体数据格式以及编码格式

   Content-disposition:服务器告诉客户端以什么格式打开响应体数据

   - 值：in-line:默认值，在当前页面内打开
   - attachment;filename=xxx:以附件形式打开响应体，文件下载

   

响应空行

响应体：真实传输的数据



Response对象

功能：设置响应消息

1. 设置响应行

   设置状态码：setStatus(int sc)

2. 设置响应头:setHeader(String name,String value)

3. 设置响应体

   使用步骤：

   1. 获取输出流

      字符输出流：printWriter getWriter()

      字节输出流：ServletOutputStream getOutputStream()

   2. 使用输出流，将数据输出到客户端浏览器

> 重定向

代码实现：

```java
protected void doPost(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
		//设置状态响应码
         response.setStatus(302);
         //设置响应头
         response.setHeader("location","/servlet2/responseDemo2");

         //简单的重定向
        response.sendRedirect("/servlet2/responseDemo2");
```

> 重定向的特点

1. 地址栏发生变化
2. 重定向可以访问其他站点的资源
3. 重定向是两次请求，不能使用request对象来共享数据

> 转发的特点

1. 转发地址栏路径不变
2. 转发只能访问当前服务器下的资源
3. 转发是一次请求，可以使用request对象来共享数据



> 路径写法

路径的分类

1. 相对路径：通过相对路径不可以确定唯一资源

   不以/开头，以.开头 

   规则：找到当前资源和目标资源的相对位置关系

   ​			./ 当前目录

   ​			../后退上一级目录

2. 绝对路径：通过绝对路径可以确定唯一资源

   以/开头的路径

   判断定义的路径是给谁用的？判断请求将来从哪儿发出

   1. 给客户端浏览器使用：需要添加虚拟目录（项目的访问路径）

      重定向路径

      建议虚拟目录动态获取：request.getContextPath():动态的获取虚拟目录

   2. 给服务器使用：不需要添加虚拟目录

      转发路径

> 服务器输出字符数据到浏览器

步骤

1. 获取字符输出流
2. 输出数据

注意：

- 乱码问题 	PrintWriter writer = response.getWriter();获取的流的默认编码是ISO
- 设置该流的编码，告诉浏览器响应体使用的编码 response.setContentType("text/html;charset = utf-8");

```java
public class ResponseDemo3 extends HttpServlet {
    protected void doPost(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {

        //告诉浏览器，服务器发送的消息体数据的编码
        response.setContentType("text/html;charset = utf-8");

        PrintWriter writer = response.getWriter();
        writer.write("你好，response");
    }

    protected void doGet(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
        this.doPost(request,response);
    }
}
```



> 服务器输出字节流数据

步骤：

1. 获取字节流文件
2. 输出数据

```java
@WebServlet("/responseDemo4")
public class ResponseDemo4 extends HttpServlet {
    protected void doPost(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
        response.setContentType("text/html;charset = utf-8");
        ServletOutputStream sos = response.getOutputStream();
        sos.write("你好".getBytes("utf-8"));
    }

    protected void doGet(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
        this.doPost(request,response);
    }
}
```

> 验证码

本质：图片

目的：防止恶意表单注册

### 8.ServletContext

概念：代表整个web应用，可以和程序的容器（服务器）来通信

获取

1. 通过request对象获取

   request.getServletContext()

2. 通过HttpServlet获取

   getServletContext()

功能：

1. 获取MIME路径

   MIME类型：在互联网通信过程中定义的一种文件数据类型

   格式：大类型/小类型   text/html   image/jpeg

   获取：getMimeType(String file)

2. 域对象：共享数据

   setAttribute(String name,Object obj)

   getAttribute(String name)

   removeAttribute(String name)

   ServletContext对象范围：所有用户请求的数据

3. 获取文件的真实（服务器）路径

   方法：getRealPath("")

> 文件下载

步骤：

1. 定义页面，编辑超链接href属性，指向Servlet,传递资源名称filename
2. 定义Servlet
   1. 获取文件名称
   2. 使用字节流加载文件进内存
   3. 指定response的响应头：content-disposition:attachment;filename = xx
   4. 将数据写出到response输出流

```java
@WebServlet("/downloadServlet")
public class DownloadServlet extends HttpServlet {
    @Override
    protected void doPost(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
    //1. 获取文件名称
        String filename = req.getParameter("filename");
    //2. 使用字节流加载文件进内存
        //2.1 找到服务器路径
        ServletContext servletContext = this.getServletContext();
        String realPath = servletContext.getRealPath("/img/" + filename);
        //2.2 通过字节流关联
        FileInputStream fileInputStream = new FileInputStream(realPath);

        //3. 指定response的响应头：content-disposition:attachment;filename = xx
        String mimeType = servletContext.getMimeType(filename);
        resp.setHeader("content-type",mimeType);
        resp.setHeader("content-disposition","attachment;filename = " + filename);

        //4. 将数据写出到response输出流
        ServletOutputStream outputStream = resp.getOutputStream();
        byte[] buff = new byte[1024 * 8];
        int len = 0;
        while ((len =fileInputStream.read(buff)) != (-1)){
            outputStream.write(buff,0,len);

        }
        fileInputStream.close();
    }

    @Override
    protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        this.doPost(req,resp);
    }
}
```





### 9.会话技术

会话：一次会话中包含多次请求和响应

- 一次会话：浏览器第一次给服务器资源发送请求，会话建立，直到有一方断开为止

功能：在一次会话的范围内的多次请求间，共享数据

方式

1. 客户端会话技术：Cookie
2. 服务器端技术：Session

#### 9.1 Cookie

概念：客户端会话技术，将数据保存到客户端

使用步骤

1. 创建Cookie对象，绑定数据

   Cookie cookie = new Cookie(String name,String value)

2. 发送Cookie

   response.addCookie(Cookie cookie)

3. 获取Cookie，拿到数据

   request.getCookies()

4. ![image-20210815203417534](C:\Users\dell\AppData\Roaming\Typora\typora-user-images\image-20210815203417534.png)

#### 9.2 Cookie的细节

一次可以发送多个Cookie

- 可以创建多个Cookie对象，使用response调用多次addCookie方法发送cookie即可

Cookie在浏览器中保存多长时间

1. 默认情况下，当浏览器关闭后，Cookie数据被销毁

2. 持久化存储

   setMaxAge(int seconds)

   1. 正数：将Cookie数据写到硬盘的文件中。持久化存储。Cookie存活时间
   2. 负数：默认值
   3. 零：删除Cookie信息

Cookie的共享问题：

1. 假设在一个tomcat服务器中，部署了多个web项目，那么在这些web项目中cookie能不能共享

   - 默认情况下cookie不能共享

   - setPath(String path):设置cookie的获取范围，默认情况下，设置当前的虚拟目录

     如果要共享，则可以将path设置为"/"

2. 不同tomcat服务器间cookie共享问题

   - setDomain(String path):如果设置一级域名相同，那么多个服务器间cookie可以共享
     - setDomain(".baidu.com")，那么tieba.baidu.com和news.baidu.com中cookie共享



#### 9.3Cookie的特点

特点：

1. Cookie存储数据在客户端浏览器
2. 浏览器对于单个Cookie的大小有限制（4kb）以及对同一个域名下的总Cookie数量也有限制

作用：

1. cookie一般用于存储少量的不太敏感的数据
2. 在不登陆的情况下，完成服务器对客户端的身份识别



#### 9.4 Session会话技术 

概念：服务器端会话技术，在一次会话的多次请求间共享数据，将数据保存在服务器端的对象中，HttpSession

快速入门：

1. 获取Session对象

   HttpSession session = request.getSession();

2. 使用HttpSession对象；

   Object getAttribute(String name);

   void setAttribute(String name,Object value);

   void removeAttribute(String name);

```java
@WebServlet("/sessionDemo1")
public class SessionDemo1 extends HttpServlet {
    protected void doPost(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
        HttpSession session = request.getSession();

        session.setAttribute("msg","hello,session");
    }

    protected void doGet(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
        this.doPost(request, response);
    }
}


@WebServlet("/sessionDemo2")
public class SessionDemo2 extends HttpServlet {
    protected void doPost(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
        HttpSession session = request.getSession();

        Object msg = session.getAttribute("msg");
        System.out.println(msg);
    }

    protected void doGet(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
        this.doPost(request, response);
    }
}
```

Session的实现是依赖于Cookie的



#### 9.5 Session细节

当客户端关闭后，服务器不关闭，再次获取session是否为同一个：

- 默认情况下不是的

- 如果需要相同，则可以创建Cookie，键为JSESSIONID，设置最大存活时间，让cookie持久化

  Cookie cookie = new Cookie("JESSIONID","session.getId()");

  cookie.setMaxAge(60 * 60);

  response.addCookie(cookie);

客户端不关闭，服务器关闭后，两次获取的session是同一个么

- 不是同一个，但是要确保数据不丢失

  - session的钝化

    在服务器正常关闭之前，将session对象系列化到硬盘上

  - session的活化

    在服务器启动后，将session文件转化为内存中的session对象即可

session什么时候销毁

- 服务器关闭
- session对象调用invalidate()
- session默认失效时间30分钟



#### 9.6 session特点

session用于存储一次会话的多次请求的数据，存在于服务器端

session可以存储任意类型，任意大小的数据



session与cookie的区别

1. session存数据于服务器端，Cookie存数据于客户端
2. session没有数据大小的限制，Cookie有
3. session数据安全，Cookie相对于不安全

### 10.JSP

概念： Java Server Page:java服务器端页面

可以理解为：一个特殊的页面，其中既可以指定定义html标签，又可以定义java代码

原理：JSP本质就是Servlet

#### 10.1 JSP脚本

JSP定义代码的方式

1. <%  代码  %>:定义的java代码在service方法中。service方法中可以定义什么，该脚本中就可以定义什么
2. <%!  代码  %>：定义的java代码在jsp转换后的java类的成员位置
3. <%=  代码  %>：定义的java代码会输出到页面上，输出语句中可以定义什么，该脚本就可以定义什么



#### 10.2 JSP内置对象

在jsp页面中不需要获取和创建，可以直接使用的对象

request

response

out：可以将数据输出到页面上

1. response.getWriter()和out.write()区别：
   - 在tomcat服务器中真正给客户端做出响应前，会先找response缓冲区数据，再找out缓冲区数据
   - response.getWriter()数据输出永远在out.writer()之前



#### 10.3 指令

作用：用于配置JSP页面，导入资源文件

格式： <%@ 指令名称 属性名1 = 属性值1 属性名2 = 属性值2 ....... %>

> 分类：

1. page:配置JSP页面

   - contentType：等同于response.setContentType()
     1. 设置响应体的mime类型以及字符集
     2. 设置当前jsp页面的编码（只有高级的IDE才能生效，如果使用低级工具，则需要设置pageEncoding属性设置当前页面的字符集）
   - import:导包
   - errorPage:当前页面发生异常后，会自动跳转到指定的错误页面
   - isErrorPage:标识当前页面是否是错误页面
     - true : 是，可以使用内置对象exception
     - false:否，默认值。不可以使用内置对象exception

2. include：页面包含的，导入页面的资源文件

3. taglib：导入资源

   ``` jsp
   <%@ taglib prefix="c" uri="http://java.sun.com/jsp/jstl/core" %>
   
   <%--prefix:前缀，自定义的 --%>
   ```

> 注释： 

1. html注释 

   <!--   -->:只能注释html代码片段

2. JSP注释

   <%-- 	--%>：能够注释所有



#### 10.4 内置对象

在JSP页面中不需要创建，直接使用对象

一共有九个

| 变量名      | 真实类型            | 作用                                         |
| ----------- | ------------------- | -------------------------------------------- |
| pageContext | PageContext         | 当前页面共享数据，还可以获取其他八个内置对象 |
| request     | HttpServletRequest  | 一次请求访问的多个资源（转发）共享数据       |
| session     | HttpSession         | 一次会话的多个请求间共享数据                 |
| application | ServletContext      | 所有用户间共享数据                           |
| response    | HttpServletResponse | 响应对象                                     |
| page        | Object              | 当前页面（Servlet）的对象                    |
| out         | JspWriter           | 输出对象，数据输出到页面上                   |
| config      | ServletConfig       | Servlet配置对象                              |
| exception   | Throwable           | 异常对象                                     |

 

#### 10.5 MVC设计模式

 ![image-20210816142323894](C:\Users\dell\AppData\Roaming\Typora\typora-user-images\image-20210816142323894.png)

> 优点

1. 耦合性低，方便维护，可以利于分工协作
2. 重用性高

> 缺点

- 是的项目架构变得复杂，对开发人员要求较高



#### 10.6 EL表达式

> 概念

表达式信息



> 作用

替换和简化jsp页面中java代码的编写



> 语法

${表达式}



注意：JSP默认支持el表达式的。如果要忽略jsp页面中的所有el表达式

1. 设置jsp中page指令中：isELIgnored = "true",忽略当前jsp页面中所有的el表达式
2. \ ${表达式}：忽略当前这个el表达式



> 使用

1. 运算

   运算符：

   1. 算术运算符

   2. 比较运算符

   3. 逻辑运算符

   4. 空运算符:empty

      功能：用于判断字符串，集合，数组对象是否为null并且长度是否为0

      ${empty list}:判断字符串，集合，数组对象是否为null或者长度为0

      ${not empty list}:判断字符串，集合，数组对象是否为null并且长度>0

2. 获取值

   1. el表达式只能从域对象中获取值

   2. 语法：

      1. ${域名称，键名}：从指定域中获取指定键的值

         域名称：

         1. pageScope				-->pageContext
         2. requestScope            -->request
         3. sessionScope            -->session
         4. applicatonScope        -->application(ServletContext)

      2. ${键名}：表示依次从最小的域中查找是否有该键对应的值，直到找到为止 

      3. 获取对象，List集合，Map集合的值

         1. 对象： ${域名称.键名.属性名}

            本质上会去调用对象的getter方法

         2. List集合：${域名称.键名[索引]}

         3. Map集合：

            - ${域名称.键名.key名称}
            - ${域名称.键名[key名称]}

   3. 隐式对象（不用创建，直接拿来用）

      el表达式中有 11个隐式对象

      pageContext:获取其他八个内置对象

      - ${pageContext.request.contextPath}:动态获取虚拟目录



#### 10.7JSTL

概念：JavaServer Pages Tag Library       JSP标准标签库

作用：用于简化和替换jsp页面上的java代码

使用步骤：

1. 导入jstl相关的jar包
2. 引入标签库：taglib指令  <%@ taglib %>
3. 使用标签

> 常用的JSTL标签

if:相当于java代码的if语句

1. 属性

   - test为必须属性，接受Boolean表达式
   - 一般情况下，test属性值会结合el表达式一起使用

   ```jsp
   <%@ page contentType="text/html;charset=UTF-8" language="java" %>
   <%@taglib prefix="c" uri="http://java.sun.com/jsp/jstl/core" %>
   <html>
   <head>
       <title>if标签</title>
   </head>
   <body>
   
   <%
       List list = new ArrayList();
       list.add(1);
       request.setAttribute("list",list);
   %>
   <c:if test="${not empty list}">
    我是真。。。。
   </c:if>
   </body>
   </html>
   ```

choose:相当于java代码中的switch语句

1. 域中存储数字
2. 使用choose标签取出数字             相当于switch
3. 使用when标签对数字判断             相当于catch
4. otherwise标签做其他情况的声明   相当于default

```jsp
<%@ page contentType="text/html;charset=UTF-8" language="java" %>
<%@taglib prefix="c" uri="http://java.sun.com/jsp/jstl/core" %>
<html>
<head>
    <title>Title</title>
</head>
<body>
<%
request.setAttribute("number","3");
%>

<c:choose>
    <c:when test="${number == 1}">星期一</c:when>
    <c:when test="${number == 2}">星期二</c:when>
    <c:when test="${number == 3}">星期三</c:when>
    <c:when test="${number == 4}">星期四</c:when>
    <c:when test="${number == 5}">星期五</c:when>
    <c:when test="${number == 6}">星期六</c:when>
    <c:when test="${number == 7}">星期日</c:when>

    <c:otherwise>数字输入有误</c:otherwise>

</c:choose>
</body>
</html>
```



foreach标签：相当于java的for循环

> 完成重复的操作

属性：

1. begin：开始值
2. end：结束值
3. var：临时变量
4. step：步长
5. varstatus:循环状态对象
   1. inedx 容器中元素的索引
   2. count：循环次数

> 遍历容器

 属性：

1. item：容器对象
2. var：容器中元素的临时变量
3. varstatus:循环状态对象
   1. inedx 容器中元素的索引
   2. count：循环次数

```jsp
<%@ page contentType="text/html;charset=UTF-8" language="java" %>
<%@taglib prefix="c" uri="http://java.sun.com/jsp/jstl/core" %>
<html>
<head>
    <title>Title</title>
</head>
<body>

<%
    List list = new ArrayList();
    list.add(new User("张三",18,new Date()));
    list.add(new User("李四",22,new Date()));
    list.add(new User("王五",28,new Date()));

    request.setAttribute("list",list);
%>

<table border="1.0" width="500" >
    <tr>
        <th>编号</th>
        <th>姓名</th>
        <th>年龄</th>
        <th>生日</th>
    </tr>

<c:forEach var="user" varStatus="s" items="${list}">
    <c:if test="${s.count % 2 == 0}" >
        <tr>
            <td>${s.count}</td>
            <td>${user.name}</td>
            <td>${user.age}</td>
            <td>${user.birth}</td>
        </tr>
    </c:if>
    <c:if test="${s.count % 2 != 0}">
        <tr bgcolor="#8a2be2">
            <td>${s.count}</td>
            <td>${user.name}</td>
            <td>${user.age}</td>
            <td>${user.birth}</td>
        </tr>
    </c:if>
</c:forEach>
</table>
</body>
</html>
```



#### 10.8 三层架构

![image-20210817093614396](C:\Users\dell\AppData\Roaming\Typora\typora-user-images\image-20210817093614396.png)





### 11.过滤器

web中的过滤器：当访问服务器时，过滤器可以将请求拦截下来，完成一些特殊的功能

过滤器的作用：

- 一般用于完成通用的操作。如：登陆验证，统一编码处理，敏感字符过滤.......



#### 11.1 快速入门

步骤：

1. 定义一个类，实现接口Filter

2. 重写方法

3. 配置拦截路径

   1. web.xml

      ```xml
      <filter>
          <filter-name>demo</filter-name>
          <filter-class>cn.itcast.web.filter.FilterDemo1</filter-class>
      </filter>
      
      <filter-mapping>
          <filter-name>demo</filter-name>
          <url-pattern>/*</url-pattern>
      </filter-mapping>
      ```

   2. 注解

```java
@WebFilter("/*")  //访问所有资源之前，都会执行此该过滤器
public class FilterDemo1 implements Filter {

    @Override
    public void init(FilterConfig filterConfig) throws ServletException {

    }

    @Override
    public void doFilter(ServletRequest servletRequest, ServletResponse servletResponse, FilterChain filterChain) throws IOException, ServletException {
        System.out.println("filter被执行了");

        filterChain.doFilter(servletRequest, servletResponse);
    }

    @Override
    public void destroy() {

    }
}
```

####   11.2 过滤器生命周期方法

init:在服务器启动之后，会创建Filter对象，然后调用init方法，只执行一次，用于加载资源

doFilter:每一次请求被拦截资源时，会执行，执行多次

destroy：在服务关闭后，Filter对象被销毁。如果服务器是正常关闭，则会执行destroy方法，只执行一次，用于释放资源



#### 11.3 过滤器配置

> 拦截路径配置

- 具体资源路径： /index.jsp				只有访问index.jsp资源时，过滤器才会执行
- 拦截目录：/user/*                              访问user下所有的资源时，过滤器会被执行
- 后缀名拦截   *.jsp                               访问所有后缀名为jsp的资源时，过滤器都会被执行
- 拦截所有资源   /*                                 访问所有资源时，过滤器都会被执行 





> 拦截方式配置：资源被访问的方式

注解配置

- 设置dispatcherTypes属性
  1. REQUEST:默认值，浏览器直接请求资源
  2. FORWORD:转发访问资源
  3. INCLUDE:包含访问资源
  4. ERROR:错误跳转资源
  5. ASYNC:异步访问资源
- web.xml配置
  - 设置<dispatche></dispatche>标签即可



> 过滤器链（配置多个过滤器）

执行顺序：如果有两个过滤器：过滤器1和过滤器2

1. 过滤器1
2. 过滤器2
3. 资源执行
4. 过滤器2
5. 过滤器1

过滤器先后顺序问题：

1. 注解配置：按照类名的字符串进行规则比较，值小的先执行
2. web.xml配置：谁定义在上边谁先执行



### 12.监听器

web的三大组件之一 

事件监听机制

- 事件：一件事情
- 事件源：事件发生的地方
- 监听器：一个对象
- 注册监听：将事件，事件源，监听器绑定在一起。当事件源上发生某个事件后，执行监听器代码



> ServletContextListener:监听ServletContext对象的创建和销毁

步骤

1. 定义一个类，实现 ServletContextListener接口

2. 复写方法

3. 配置

   1. web.xml

      ```xml
      <listener>
          <listener-class>cn.itcast.web.listener.ListenerDemo1</listener-class>
      </listener>
      ```

      指定初始化参数

      ```xml
      <context-param>
          <param-name>namespace</param-name>
          <param-value>/WEB-INF/classes/application.xml</param-value>
      </context-param>
      ```

   2. 注解

      @WebListener

```java
public class ListenerDemo1 implements ServletContextListener{
    /**
     *监听ServletContext对象创建的，服务器启动后会自动创建
     *
     * 在服务器启动后自动调用
     * @param sce
     */
    @Override
    public void contextInitialized(ServletContextEvent sce) {
        //获取ServletContext对象
        ServletContext servletContext = sce.getServletContext();

        //加载资源文件
        String namespace = servletContext.getInitParameter("namespace");
        System.out.println("ServletContext被创建了");

        //获取真实路径
        String realPath = servletContext.getRealPath(namespace);

        //加载进内存
        try {
            FileInputStream in = new FileInputStream(realPath);
            System.out.println(in);
        } catch (FileNotFoundException e) {
            e.printStackTrace();
        }
    }

    /**
     * 服务器关闭后，ServletContext对象被销毁，当服务器正常关闭后该方法被调用
     * @param sce
     */
    @Override
    public void contextDestroyed(ServletContextEvent sce) {
        System.out.println("ServletContext被销毁了");
    }
}
```





### 13.JQuery

概念：jQuery是一个快速、简洁的[JavaScript](https://baike.baidu.com/item/JavaScript/321142)框架，是继[Prototype](https://baike.baidu.com/item/Prototype/14335188)之后又一个优秀的JavaScript代码库（框架）于2006年1月由[John Resig](https://baike.baidu.com/item/John Resig/6336344)发布。jQuery设计的宗旨是“write Less，Do More”，即倡导写更少的代码，做更多的事情。它封装JavaScript常用的功能代码，提供一种简便的JavaScript[设计模式](https://baike.baidu.com/item/设计模式/1212549)，优化[HTML](https://baike.baidu.com/item/HTML/97049)文档操作、事件处理、动画设计和[Ajax](https://baike.baidu.com/item/Ajax/8425)交互。



#### 13.1 JQuery快速入门

步骤：

1. 下载JQuery
   - jquery-xxx.js:开发版本。给程序员看的，有良好的缩进和注释。体积大一些
   - jquery-xxx.min.js:生产版本。程序中使用，没有缩进。体积小一些，程序加载快
2. 导入JQuery的js文件：导入min.js文件
3. 使用

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>JQuery快速入门</title>
    <script src="js/jquery-3.3.1.min.js"></script>
</head>
<body>
<div id="div1">div1.....</div>
<div id="div2">div2.....</div>

<script>
    var div1 = $(`#div1`);
    alert(div1.html());
</script>
</body>
</html>
```

```html
<script>
    //js
    var divs = document.getElementsByTagName("div");
    alert(divs.length); //可以将其当作数组来使用
    for (var i = 0; i < divs.length;i++){
        divs[i].innerHTML = "aaa";
    }


    //jq方式
    var $divs = $("div");
    $divs.html("bbb");
    alert($divs.length);//可以当作数组来使用

</script>
```

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
    <script src="js/jquery-3.3.1.min.js"></script>
</head>
<body>
<div id="div1">div1.....</div>
<div id="div2">div2.....</div>

<script>
    //js
    var divs = document.getElementsByTagName("div");
    alert(divs.length); //可以将其当作数组来使用
    for (var i = 0; i < divs.length;i++){
        divs[i].innerHTML = "aaa";
       
    }

    

    //jq方式
    var $divs = $("div");
    $divs.html("bbb");
    alert($divs.length);//可以当作数组来使用

</script>
</body>
</html>
```

jq------->js: jq对象（索引） 或者 jq对象.get(索引)

```js
$divs[0].innerHTML("ddd");
$divs.get(1).innerHTML("eee");    //jq----->js
```

 js------->jq: ${js对象}

```js
$([divs[i]]).html("aaa");   // js ---->jq
```



#### 13.2 选择器

筛选具有相似特征的元素（标签）

基本的语法：

1. 事件绑定

   ```html
   <body>
       <input type="button" value="点我" id="d1">
   <script>
       //给d1按钮添加单机事件
   
       //获取b1按钮
       $("#d1").click(function () {
           alert("abc")
       })
   </script>
   </body>
   ```

2. 入口函数

   ```html
   <html lang="en">
   <head>
       <meta charset="UTF-8">
       <title>事件绑定</title>
       <script src="js/jquery-3.3.1.min.js"></script>
   
       <script>
           //jquery入口函数（dom文档加载完之后执行改方法）
           $(function () {
               $("#d1").click(function () {
                   alert("abc")
               })
           })
       </script>
   </head>
   <body>
       <input type="button" value="点我" id="d1">
   </body>
   </html>
   ```

3. 样式控制

   ```html
   <!DOCTYPE html>
   <html lang="en">
   <head>
       <meta charset="UTF-8">
       <title>事件绑定</title>
       <script src="js/jquery-3.3.1.min.js"></script>
   
       <script>
         
           $(function () {
               $("#div1").css("backgroundColor","red")
               $("#div2").css("background-color","blue")
           })
       </script>
   </head>
   <body>
       <div id="div1">div1....</div>
       <div id="div2">div2....</div>
       <input type="button" value="点我" id="d1">
   
   </body>
   </html>
   ```




> 分类

1. 基本选择器

   1. 标签选择器（元素选择器）：

      语法：$("html标签名")   获得所有匹配标签名称的元素

   2. id选择器

      语法：$("#id的属性值")  获得与指定id属性值匹配的元素

   3. 类选择器

      语法：$(".class的属性值") 获得与指定的class属性值匹配的元素

   4. 并集选择器

      语法：$("选择器1,选择器2,选择器3.........")

> 代码实现

```html
<script type="text/javascript">
		$(function () {
			// <input type="button" value="改变 id 为 one 的元素的背景色为 红色"  id="b1"/>
			$("#b1").click(function () {
				$("#one").css("backgroundColor","red");
			})
			// <input type="button" value=" 改变元素名为 <div> 的所有元素的背景色为 红色"  id="b2"/>
			$("#b2").click(function () {
				$("div").css("backgroundColor","pink")
			})
			// <input type="button" value=" 改变 class 为 mini 的所有元素的背景色为 红色"  id="b3"/>
			$("#b3").click(function () {
				$(".mini").css("backgroundColor","blue");
			})
			// <input type="button" value=" 改变所有的<span>元素和 id 为 two 的元素的背景色为红色"  id="b4"/>
			$("#b4").click(function () {
				$("span,#two").css("backgroundColor","blue");
			})

		})

	</script>
```

2.层级选择器

1. 后代选择器

   语法：$("A B") 选择A元素内部的所有B元素

2. 子选择器

   语法：$(A > B) 选择A元素内部的所有B子元素

> 代码实现

```html
  <script type="text/javascript">
	$(function () {
	// <input type="button" value=" 改变 <body> 内所有 <div> 的背景色为红色"  id="b1"/>
		$("#b1").click(function () {
			$("body div").css("backgroundColor","pink");
		})
	// <input type="button" value=" 改变 <body> 内子 <div> 的背景色为 红色"  id="b2"/>
		$("#b2").click(function () {
			$("body > div").css("backgroundColor","pink")
		})
	})
		
		
	</script>
```

3.属性选择器

1. 属性名称选择器

   语法：$("A[属性名]")    包含指定属性的选择器

2. 属性选择器

   语法：$("A[属性名 = '值']")  包含指定属性等于指定值的选择器

3. 复合属性选择器

   语法：$("A[属性名 = '值'] [] []......") 包含多个属性条件的选择器

> 代码实现

```html
	 <script type="text/javascript">
	$(function () {
		// <input type="button" value=" 含有属性title 的div元素背景色为红色"  id="b1"/>
		$("#b1").click(function () {
			$("div[title]").css("backgroundColor","pink")
		})
		// <input type="button" value=" 属性title值等于test的div元素背景色为红色"  id="b2"/>
		$("#b2").click(function () {
			$("div[title = 'test']").css("backgroundColor","pink");
		})
		// <input type="button" value=" 属性title值不等于test的div元素(没有属性title的也将被选中)背景色为红色"  id="b3"/>
		$("#b3").click(function () {
			$("div[title != 'test']").css("backgroundColor","pink")
		})
		// <input type="button" value=" 属性title值 以te开始 的div元素背景色为红色"  id="b4"/>
			$("#b4").click(function () {
				$("div[title ^= 'te']").css("backgroundColor","pink")
			})
		// <input type="button" value=" 属性title值 以est结束 的div元素背景色为红色"  id="b5"/>
			$("#b5").click(function () {
				$("div[title $= 'est']").css("backgroundColor","pink")
			})
		// <input type="button" value="属性title值 含有es的div元素背景色为红色"  id="b6"/>
			$("#b6").click(function () {
				$("div[title *= 'es']").css("backgroundColor","pink")
			})
		// <input type="button" value="选取有属性id的div元素，然后在结果中选取属性title值含有“es”的 div 元素背景色为红色"  id="b7"/>
			$("#b7").click(function () {
				$("div[title *= 'es'],[id]").css("backgroundColor","pink")
			})
	})
		
		
	</script>
```



4.过滤选择器

1. 首元素选择器

   语法： :first  获得选择的元素中的第一个元素

2. 尾元素选择器

   语法： :last  获得选择的元素中的最后一个元素

3. 非元素选择器

   语法： :not(selector) 不包括指定内容的元素

4. 偶数选择器

   语法： :even 偶数，从0开始

5. 奇数选择器

   语法： :odd 奇数，从0开始

6. 等于索引选择器

   语法：  :eq(index)  指定索引元素

7. 大于索引选择器

   语法：   :gt(index) 大于指定索引元素

8. 小于索引选择器

   语法：   :lt(index) 小于指定索引元素

9. 标题选择器

   语法：	:header 获得标题元素，固定写法

> 代码实现

```html
<script type="text/javascript">
		$(function () {
		// <input type="button" value=" 改变第一个 div 元素的背景色为 红色"  id="b1"/>
			$("#b1").click(function () {
				$("div:first").css("backgroundColor","pink");
			})
		// <input type="button" value=" 改变最后一个 div 元素的背景色为 红色"  id="b2"/>
			$("#b2").click(function () {
				$("div:last").css("backgroundColor","pink");
			})
		// <input type="button" value=" 改变class不为 one 的所有 div 元素的背景色为 红色"  id="b3"/>
			$("#b3").click(function () {
				$("div:not(.one)").css("backgroundColor","pink");
			})
		// <input type="button" value=" 改变索引值为偶数的 div 元素的背景色为 红色"  id="b4"/>
			$("#b4").click(function () {
				$("div:even").css("backgroundColor","pink");
			})
		// <input type="button" value=" 改变索引值为奇数的 div 元素的背景色为 红色"  id="b5"/>
			$("#b5").click(function () {
				$("div:odd").css("backgroundColor","pink");
			})
		// <input type="button" value=" 改变索引值为大于 3 的 div 元素的背景色为 红色"  id="b6"/>
			$("#b6").click(function () {
				$("div:gt(3)").css("backgroundColor","pink");
			})
		// <input type="button" value=" 改变索引值为等于 3 的 div 元素的背景色为 红色"  id="b7"/>
			$("#b7").click(function () {
				$("div:eq(3)").css("backgroundColor","pink");
			})
		// <input type="button" value=" 改变索引值为小于 3 的 div 元素的背景色为 红色"  id="b8"/>
			$("#b8").click(function () {
				$("div:lt(3)").css("backgroundColor","pink");
			})
		// <input type="button" value=" 改变所有的标题元素的背景色为 红色"  id="b9"/>
			$("#b9").click(function () {
				$(":header").css("backgroundColor","pink");
			})
		})
		
		 
	</script>
```



5.表单过滤选择器

1. 可用元素选择器

   语法：enabled  获取可用元素

2. 不可用元素选择器

   语法：disabled 获取不可用元素

3. 选中选择器

   语法：checked 获得单选/复选框中的元素

4. 选中选择器

   语法：selected获得下拉框中的元素

> 代码实现

```html
 <script type="text/javascript">
	$(function () {
		// <input type="button" value=" 利用 jQuery 对象的 val() 方法改变表单内可用 <input> 元素的值"  id="b1"/>
		$("#b1").click(function () {
			$("input[type = 'text']:enabled").val("aaa")
		})
		// <input type="button" value=" 利用 jQuery 对象的 val() 方法改变表单内不可用 <input> 元素的值"  id="b2"/>
		$("#b2").click(function () {
			$("input[type = 'text']:disabled").val("bbb")
		})
		// <input type="button" value=" 利用 jQuery 对象的 length 属性获取复选框选中的个数"  id="b3"/>
		$("#b3").click(function () {
			alert($("input[type = 'checkbox']:checked").length);
		})
		// <input type="button" value=" 利用 jQuery 对象的 length 属性获取下拉框选中的个数"  id="b4"/>
		$("b4").click(function () {
			alert($("#job>option:selected").length);
		})
	})
		
	
	
	</script>
```



#### 13.3 DOM操作

1.内容操作

1. html():获取元素/设置元素的标签体内容
2. text():获取元素/设置元素的标签体纯文本内容
3. val():获取元素/设置元素的属性值



> 代码实现

```html
<script  src="../js/jquery-3.3.1.min.js"></script>
<script>
   $(function () {

   // 获取myinput 的value值
   var val = $("#myinput").val();
   $("#myinput").val("李四");
   // 获取mydiv的标签体内容
   var html = $("#mydiv").html();
   alert(html)
      $("#mydiv").html("aaa");
   // 获取mydiv文本内容
      var text = $("#mydiv").text();
      alert(text)
      $("#mydiv").text("bbb")
   })

</script>
```



2. 属性操作
    1. 通用属性操作

       1. attr(): 获取/设置元素的属性

       2. removeAttr():删除属性
       3. prop():获取/设置元素的属性
       4. removeProp():删除属性

   * attr和prop区别？
   	1. 如果操作的是元素的固有属性，则建议使用prop
   	2. 如果操作的是元素自定义的属性，则建议使用attr

> 代码实现

```javascript
//获取北京节点的name属性值
			var name = $("#bj").attr("name")
			alert(name)
			//设置北京节点的name属性的值为dabeijing
			$("#bj").attr("name","dabeijing")
			//新增北京节点的discription属性 属性值是didu
			$("#bj").attr("discription","didu")
			//删除北京节点的name属性并检验name属性是否存在
			$("#bj").removeAttr("name")
			//获得hobby的的选中状态
			$("#hobby").prop("checked")
```

​		2.对class属性操作

​			1.addClass():添加class属性值

​			2.removeClass():删除class属性值

​			3.toggleClass():切换class属性

​					toggleClass("one"): 

​						判断如果元素对象上存在class="one"，则将属性值one删除掉。  如果元素对象上不						存在class="one"，则添加css():

```js
//<input type="button" value="采用属性增加样式(改变id=one的样式)"  id="b1"/>
$("#b1").click(function () {

   $("#one").prop("class", "second")
})
//<input type="button" value=" addClass"  id="b2"/>
$("#b2").click(function () {

   $("#one").addClass("second")
})
//<input type="button" value="removeClass"  id="b3"/>
$("#b3").click(function () {

   $("#one").removeClass("second")
})

//<input type="button" value=" 切换样式"  id="b4"/>
$("#b4").click(function () {

   $("#one").toggleClass("second")
})

//<input type="button" value=" 通过css()获得id为one背景颜色"  id="b5"/>
$("#b5").click(function () {

   var backGroundColor = $("#one").css("backgroundColor")
   alert(backGroundColor)
})
//<input type="button" value=" 通过css()设置id为one背景颜色为绿色"  id="b6"/>
$("#b6").click(function () {

    $("#one").css("backgroundColor","green")

})
```

​		3.CRUD操作:

​			1.append():父元素将子元素追加到末尾

​				对象1.append(对象2): 将对象2添加到对象1元素内部，并且在末尾

​			2.prepend():父元素将子元素追加到开头

​				对象1.prepend(对象2):将对象2添加到对象1元素内部，并且在开头

​			3.appendTo():

​				对象1.appendTo(对象2):将对象1添加到对象2内部，并且在末尾

​			4.prependTo()：

​				对象1.prependTo(对象2):将对象1添加到对象2内部，并且在开头

​			5.after():添加元素到元素后边

​				对象1.after(对象2)： 将对象2添加到对象1后边。对象1和对象2是兄弟关系

​			6.before():添加元素到元素前边

​				对象1.before(对象2)： 将对象2添加到对象1前边。对象1和对象2是兄弟关系

​			7.insertAfter()

​				对象1.insertAfter(对象2)：将对象2添加到对象1后边。对象1和对象2是兄弟关系

​			8.insertBefore()	

​				对象1.insertBefore(对象2)： 将对象2添加到对象1前边。对象1和对象2是兄弟关系

​			9.remove():移除元素

​				对象.remove():将对象删除掉

​			10.empty():清空元素的所有后代元素。

​				对象.empty():将对象的后代元素全部清空，但是保留当前对象以及其属性节点

> 代码实现

```js
 // <input type="button" value="将反恐放置到city的后面"  id="b1"/>
		$("#b1").click(function () {
			// $("#city").append($("#fk"))
			$("#fk").appendTo($("#city"))
		})
		 // <input type="button" value="将反恐放置到city的最前面"  id="b2"/>

			$("#b2").click(function () {
				// $("#city").prepend($("#fk"))
				$("#fk").prependTo($("#city"))
			})


		 // <input type="button" value="将反恐插入到天津后面"  id="b3"/>
			 $("#b3").click(function () {
				 // $("#tj").after($("#fk"))
				 $("#fk").insertAfter($("#tj"))
			 })
		 // <input type="button" value="将反恐插入到天津前面"  id="b4"/>
			 $("#b4").click(function () {
				 // $("#tj").before($("#fk"))
				 $("#fk").insertBefore($("#tj"))
			 })

			// <input type="button" value="删除<li id='bj' name='beijing'>北京</li>"  id="b1"/>
				$("#b1").click(function () {
					$("#bj").remove();
				})
			// <input type="button" value="删除city所有的li节点   清空元素中的所有后代节点(不包含属性节点)"  id="b2"/>
				$("#b2").click(function () {
					$("#city").empty();
				})
		 })

```



### 14.AJAX

概念：

同步和异步：客户端和服务器端相互通信的基础上

​	同步：客户端必须等待服务器端的响应。在等待的期间客户端不能做其他操作

​	异步：客户端不需要等待服务器端的响应。在服务器处理请求的过程中，客户端可以进行其他的				操作

- Ajax 是一种在无需重新加载整个网页的情况下，能够更新部分网页的技术。
  		通过在后台与服务器进行少量数据交换，Ajax 可以使网页实现异步更新。这意味着可以在		不重新加载整个网页的情况下，对网页的某部分进行更新。
    		传统的网页（不使用 Ajax）如果需要更新内容，必须重载整个网页页面。



#### 14.1 实现方式

JQuery实现方式

1. $.ajax()

   - 语法：$.ajax({键值对})

   ```html
   <!DOCTYPE html>
   <html lang="en">
   <head>
       <meta charset="UTF-8">
       <title>Title</title>
       <script src="js/jquery-3.3.1.min.js"></script>
       <script>
           function f() {
               $.ajax({
                   url:"ajaxServlet",  //请求路径
                   type:"POST",
                   // data:"username=jack&age=22",
                   data:{"username":"jack","age":23},
                   success:function (data) {
                       alert(data);
                   } //响应成功后的回调函数
               })
           }
       </script>
   </head>
   <body>
       <input type="button" value="发送异步请求" onclick="f()">
   </body>
   </html>
   ```

   ```java
   @WebServlet("/ajaxServlet")
   public class AjaxServlet extends HttpServlet {
       @Override
       protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
           String username = req.getParameter("username");
   
           System.out.println(username);
   
           resp.getWriter().write("hello" + username);
       }
   
       @Override
       protected void doPost(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
           this.doGet(req, resp);
       }
   }
   ```

2. $.get()

   语法：$.get(url,[data],[callback],[type])

   - 参数：

     url:请求路径

     data:请求参数

     callback：回调函数

     type:响应结果的类型

3. $.post()

   语法：$.post(url,[data],[callback],[type])

   - 参数：

     url:请求路径

     data:请求参数

     callback：回调函数

     type:响应结果的类型

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
    <script src="js/jquery-3.3.1.min.js"></script>
    <script>
        function f() {
          $.get("ajaxServlet",{username:"jack",age: 22},function (data) {
                alert(data)
          },"text");
        }
    </script>
</head>
<body>
    <input type="button" value="发送异步请求" onclick="f()">
</body>
</html>
```



### 15.JSON

概念：JavaScript Object Notation

现在多用于存储和交换文本信息的语法

进行数据的传输



#### 15.1语法

1. 基本规则：

   数据在名称/值对中：json数据是由键值对构成的

   数据由逗号分离：多个键值对由逗号分隔

   - 值的类型：

     数字

     字符串

     逻辑值

     数组（在方括号中）

     对象（在花括号中）

   花括号保存对象：使用{}定义JSON格式

   方括号保存数组

   ```js
     //定义基本格式
   var person = {"name":"张三",age:22,'gender':true}
   
     //2.嵌套格式
           var persons = {persons: [
               {"name":"jack","age":22,"gender":"男"},
               {"name":"李四","age":23,"gender":"男"},
               {"name":"王五","age":24,"gender":"男"}
               ]}
              
           var per = [
               {"name":"jack","age":22,"gender":"男"},
               {"name":"李四","age":23,"gender":"男"},
               {"name":"王五","age":24,"gender":"男"}
               ]
   ```

2. 获取数据：

   json对象.键名

   json对象["键名"]

   ```js
   var persons = {persons: [
       {"name":"jack","age":22,"gender":"男"},
       {"name":"李四","age":23,"gender":"男"},
       {"name":"王五","age":24,"gender":"男"}
       ]}
       alert(persons.persons[2].gender)
   var per = [
               {"name":"jack","age":22,"gender":"男"},
               {"name":"李四","age":23,"gender":"男"},
               {"name":"王五","age":24,"gender":"男"}
               ]
           alert(per[1].name)
    var person = {"name":"张三",age:22,'gender':true}
           alert(person["name"])
   ```

3. 数据的遍历

   ```js
   
       var per = {"name":"张三","age":21,"gender":"男"}
       for (var key in per){
           alert(key + per[key])
       }
       var persons = [
           {"name":"张三","age":21,"gender":"男"},
           {"name":"李四","age":22,"gender":"男"},
           {"name":"王五","age":23,"gender":"女"}
       ]
   
      for (var i = 0; i < persons.length;i++){
          var p = persons[i];
          for (var key in p){
              alert(key + p[key])
          }
      }
   ```





#### 15.2 JSON数据和java对象的相互转换

- JSON解析器：

  常见的解析器：JsonLib,Gson,fastjson,jackson

1. JSON转换为java对象

   1. 使用步骤
      1. 导入jackjson的相关jar包
      2. 创建Jackson核心对象ObjectMapper
      3. 调用ObjectMapper的相关方法进行转换
         1. readValue(json字符串数据，Class)

2. Java对象转换为JSON

   1. 使用步骤

      1. 导入jackjson的相关jar包
      2. 创建Jackson核心对象ObjectMapper
      3. 调用ObjectMapper的相关方法进行转换

      > 转换方法

       *      writerValue(参数一,obj)
       *      参数一：
       *          File:将Obj对象转换为json字符串，并保存到指定文件夹中
       *          Writer:将Obj对象转换为json字符串，并将json数据填充到字符输出流中
       *          OutputStream:将Obj对象转换为json字符串，并将json数据填充到字节输出流中
       *      writerValueAsString(obj):将Obj对象转换为json字符串
      

   ```java
       public void test(){
           Person person = new Person("张三",22,"男");
   
   
           /*
            *转换方法
            *      writerValue(参数一,obj)
            *      参数一：
            *          File:将Obj对象转换为json字符串，并保存到指定文件夹中
            *          Writer:将Obj对象转换为json字符串，并将json数据填充到字符输出流中
            *          OutputStream:将Obj对象转换为json字符串，并将json数据填充到字节输出流中
            *      writerValueAsString(obj):将Obj对象转换为json字符串
            */
           ObjectMapper objectMapper = new ObjectMapper();
           try {
               String s = objectMapper.writeValueAsString(person);
               System.out.println(s);
           } catch (JsonProcessingException e) {
               e.printStackTrace();
           }
   
           try {
               objectMapper.writeValue(new File("d://a.txt"),person);
           } catch (IOException e) {
               e.printStackTrace();
           }
       }
   ```

   2.注解：

   ​	@JsonIgnore:排除属性

   ​	@JsonFormat:属性值的到格式化

   3.复杂的java对象转换

   list集合：

   ```java
   public void test3() throws JsonProcessingException {
       Person p = new Person();
       p.setName("李四");
       p.setAge(22);
       p.setGender("男");
       p.setBirthDay(new Date());
   
       Person p1 = new Person();
       p1.setName("李四");
       p1.setAge(22);
       p1.setGender("男");
       p1.setBirthDay(new Date());
   
       Person p2 = new Person();
       p2.setName("李四");
       p2.setAge(22);
       p2.setGender("男");
       p2.setBirthDay(new Date());
   
       List<Person> list = new ArrayList<>();
       list.add(p);
       list.add(p1);
       list.add(p2);
   
       ObjectMapper objectMapper = new ObjectMapper();
       String s = objectMapper.writeValueAsString(list);
       System.out.println(s);
   }
   ```

   map集合：

   ```java
   public void test3() throws JsonProcessingException {
       Map<String,Object> map = new HashMap<>();
       map.put("name","李四");
       map.put("age",22);
       map.put("gender","男");
   
       ObjectMapper objectMapper = new ObjectMapper();
       System.out.println(objectMapper.writeValueAsString(map));
   
   }
   ```

   







### 案例

1. web层：

   1. Servlet：前端控制器
   2. html:视图
   3. Filter:过滤器
   4. BeanUtils:数据封装
   5. Jackson:json序列化工具

2. Service层：

   ​	a)    Javamail：java发送邮件工具

   ​	b)    Redis：nosql内存数据库

   ​	c)    Jedis：java的redis客户端

3. DAO层

   ​	a)    Mysql：数据库

   ​	b)    Druid：数据库连接池

   ​	c)    JdbcTemplate：jdbc的工具

#### 创建数据库

CREATE DATABASE travel

USE travel

创建表



#### 注册功能

##### 1.功能分析

![](F:/start/img/1.注册功能分析.bmp)

##### 2.代码实现

1. 前台效果
   1. 前台校验
   
   2. 异步提交表单
   
      在此时用异步提交表单是为了获取服务器响应的数据。因为我们前台使用的是html作为视图层，不能直接从servlet相关的域对象获取值，只能通过Ajax获取响应数据

2. 
