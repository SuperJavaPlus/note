## Maven

在javaweb开发中需要使用大量的jar包，我们手动去导入

如何能够让一个东西自动帮我们导入和配置jar包

由此Maven诞生了



### Maven项目架构管理工具

我们目前用它来方便导入jar包的

Maven核心思想：约定大于配置

- 有约束不要去违反

Maven会规定好你如何去编写我们的java代码，必须按照这个规范来



### 阿里云镜像

- 镜像：mirrors
  - 作用：加速我们的下载
- 国内建议使用阿里云的镜像

```xml
 <mirror>
         <id>nexus-aliyun</id>
         <mirrorOf>*,!jeecg,!jeecg-snapshots</mirrorOf>
         <name>Nexus aliyun</name>
         <url>http://maven.aliyun.com/nexus/content/groups/public</url>
      </mirror>
```



### 本地仓库

在本地仓库

**建立一个仓库**：localRepository

```xml
<localRepository>E:\Maven\apache-maven-3.8.2\maven-repo</localRepository>
```



### 在IDEA中使用Maven

1. 启动IDEA

2. 创建项目

3. IDEA项目创建成功后看一眼Maven的配置

   ![image-20210819110458487](F:\笔记图片\img\image-20210819110458487.png)



### 创建一个普通的Maven项目

1. ![image-20210819110927789](F:\笔记图片\img\image-20210819110927789.png)
2. ![image-20210819111322385](F:\笔记图片\img\image-20210819111322385.png)



### IDEA标记文件夹目录

![image-20210819112008504](F:\笔记图片\img\image-20210819112008504.png)



### IDEA中maven的侧边栏

![image-20210819113134267](F:\笔记图片\img\image-20210819113134267.png)



### pom文件

pom.xml是maven的核心配置文件

```xml
<?xml version="1.0" encoding="UTF-8"?>

<!--maven版本和头文件-->
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
  xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
  <modelVersion>4.0.0</modelVersion>

  <!--这里就是我们刚配置的GAV-->
  <groupId>org.example</groupId>
  <artifactId>javaweb-01-maven</artifactId>
  <version>1.0-SNAPSHOT</version>
  <packaging>war</packaging>
<!--package 项目的打包方式
 jar:java应用
 war:javaweb应用
 -->
  <name>javaweb-01-maven Maven Webapp</name>
  <!-- FIXME change it to the project's website -->
  <url>http://www.example.com</url>

<!--  配置-->
  <properties>
<!--    项目的默认构建编码-->
    <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
<!--    编码版本-->
    <maven.compiler.source>1.7</maven.compiler.source>
    <maven.compiler.target>1.7</maven.compiler.target>
  </properties>

  <dependencies>
    <dependency>
      <groupId>junit</groupId>
      <artifactId>junit</artifactId>
      <version>4.11</version>
      <scope>test</scope>
    </dependency>
  </dependencies>

  <build>
    <finalName>javaweb-01-maven</finalName>
    <pluginManagement><!-- lock down plugins versions to avoid using Maven defaults (may be moved to parent pom) -->
      <plugins>
        <plugin>
          <artifactId>maven-clean-plugin</artifactId>
          <version>3.1.0</version>
        </plugin>
        <!-- see http://maven.apache.org/ref/current/maven-core/default-bindings.html#Plugin_bindings_for_war_packaging -->
        <plugin>
          <artifactId>maven-resources-plugin</artifactId>
          <version>3.0.2</version>
        </plugin>
        <plugin>
          <artifactId>maven-compiler-plugin</artifactId>
          <version>3.8.0</version>
        </plugin>
        <plugin>
          <artifactId>maven-surefire-plugin</artifactId>
          <version>2.22.1</version>
        </plugin>
        <plugin>
          <artifactId>maven-war-plugin</artifactId>
          <version>3.2.2</version>
        </plugin>
        <plugin>
          <artifactId>maven-install-plugin</artifactId>
          <version>2.5.2</version>
        </plugin>
        <plugin>
          <artifactId>maven-deploy-plugin</artifactId>
          <version>2.8.2</version>
        </plugin>
      </plugins>
    </pluginManagement>
  </build>
</project>

```



maven由于他的约定大于配置，我们之后可能遇到我们写的配置文件，无法导出或者生效的问题，解决方案：

```xml
<build>
    <resources>
        <resource>
            <directory>src/main/java</directory>
            <includes>
                <include>**/*.properties</include>
                <include>**/*.xml</include>
            </includes>
            <filtering>false</filtering>
        </resource>
        <resource>
            <directory>src/main/resources</directory>
            <includes>
                <include>**/*.properties</include>
                <include>**/*.xml</include>
            </includes>
            <filtering>false</filtering>
        </resource>
    </resources>
</build>
```

