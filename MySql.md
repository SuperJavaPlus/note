- ###  数据库的优点

  实现数据持久化

  使用完整的管理系统统一管理，易于查询

- ### 数据库相关的概念

  #### DB

  - 数据库（database）:存储数据的仓库，它保存了一系列有组织的数据

  #### DBMS

  - 数据库管理系统。数据库是通过DBMS创建和操作的容器

  #### SQL

  - 结构化查询语言，专门用来与数据库通信的语言

- ### 数据库的特点

  - 将数据放入表中，表再放入库中
  - 一个数据库中可以有多个表，每个表都有一个名字，用来标识自己。表明具有唯一性
  - 表由列组成，我们也称为字段。所有的表都是由一个或多个列组成的
  - 表中的数据是按行存储的

- ### 数据库的登录

  方式一：通过MySQL自带的客户端，只限于Root用户

  方式二：通过Windows自带的客户端   MySQL -h 主机名 -P 端口号 -u用户名 -p密码

- ### MySQL常见命令

  - 查看当前所有的数据库：**show databases**

  - 打开指定的库：**use 库名**

  - 查看当前所有表： **show tables**

  - 查看其它库的所有表：  **show tables from 库名**

  - 创建一个表：**create table  表名（**

    ​                                    **列名      列类型，**

    ​                                    **列名     列类型，**

    ​                                    .......       ..........);

  - 查看表结构：**desc  表名；**

  - 查看服务器版本：**登录到MySQL服务器，select version();**

- ### MySQL语法规范

  1. 不区分大小写，建议关键字大写，表明列名小写

  2. 每条命令建议用 ; 结尾

  3. 每条命令根据需要可以进行缩进或换行

  4. 注释：#注释文字（单行注释）

     ​            -- 注释文字（单行注释）

     ​            /* 注释文字 */（多行注释）

- ### DQL（数据查询语言）

  1. #### 基础查询

     语法：select 查询列表（表中的字段，常量，表达式，函数）

     ​            from 表名;

     查询的结构是一个虚拟的表格

     **起别名**：select    查询列表  AS(可省略)  （更改之后的名字）

     **去重**：在字段名之前加distinct

     **+的作用**：两个操作数类型都为数值型，则做加法运算

     ​                  其中一方为字符型，试图将字符型数值转化为数值型：如果转化成功，继续做加法运  算，如果转化失败，则将字符型数值转换成0.

     ​                  只要其中一方为NULL，则结果肯定是null

  2. #### 条件查询

     语法：select 查询列表  

     ​            from 表名

     ​            where 筛选条件

  3. #### 模糊查询

     like:一般和通配符搭配使用

     ​        通配符：% 任意多个字符，包含0个字符

     ​                       _  任意单个字符

     between and:

     in:判断某字段的值是否属于in列表中的某一项

     is null:     = ,!= 不能判断null值    is null  和 is not null 可以判断null值

     <=>:既可以判断null值，又可以判断普通数据类型

  4. #### 排序查询

     语法：select 查询列表  

     ​            from 表名

     ​            where 筛选条件

     ​			order by 排序列表  + （asc|desc）

  5. #### 常见函数

     调用：select 函数名(实参列表) 【from 表】

     ###### 分类：单行函数

     ​			

     | 函数名               | 作用                                        |
     | -------------------- | ------------------------------------------- |
     | length()             | 获取参数值的字节个数                        |
     | concat()             | 拼接字符串                                  |
     | upper()              | 变为大写                                    |
     | lower()              | 变为小写                                    |
     | substr()/substring() | 截取字符串                                  |
     | instr()              | 返回子串第一次出现的索引，如果找不到就返回0 |
     | trim()               | 去掉指定的字符串                            |
     | lpad()               | 用指定的字符实现左填充指定长度              |
     | rpad()               | 用指定的字符实现右填充指定长度              |
     | replace()            | 替换                                        |

     | 函数名        | 作用                                 |
     | ------------- | ------------------------------------ |
     | round()       | 四舍五入                             |
     | ceil()        | 向上取整，返回>=该参数的最小整数     |
     | floor()       | 向下取整返回<=该参数的最大整数       |
     | truncate()    | 截断                                 |
     | now()         | 返回当前系统日期                     |
     | curdate()     | 返回系统当前日期，不包含时间         |
     | curtime()     | 返回系统当前时间,不包含日期          |
     | str_to_date() | 将日期格式的字符转化为指定格式的日期 |
     | date_format() | 将日期转换成字符                     |

     ```mysql
     SELECT STR_TO_DATE('1999-7-7','%Y-%c-%d') AS out_put;
     ```

      **if语句**

     ```mysql
     SELECT last_name,commission_pct,IF(commission_pct IS NULL,'没奖金','有奖金')
     FROM employees;
     ```

     **case语句**

     case 要判断的字段或表达式

     when 常量1 then 要显示的值1或语句1

     when 常量2 then 要显示的值1或语句2

     when 常量3 then 要显示的值1或语句3

     ............

     end

     ```mysql
     SELECT salary 原始工资,department_id,
     CASE department_id
     WHEN 30 THEN salary*1.1
     WHEN 40 THEN salary*1.2
     WHEN 50 THEN salary*1.3
     ELSE salary
     END AS 新工资
     FROM employees;
     ```

     ###### 分组函数（统计函数）

     sum,avg一般用于处理数值型（忽略null值）

     max,min,count 可以处理任何数值类型（忽略null值）
     
     **GROUP BY 查询：**
     
     ​         select 分组函数 ，列（要求出现在group by的后面）
     
     ​         from 表
     
     ​        【where 查询条件】
     
     ​         group by 分组的列表
     
     ​        【order by】
     
     ```mysql
     SELECT MAX(salary),job_id
     FROM employees
     GROUP BY job_id;
     ```
     
     ```mysql
     #查询每个部门的员工个数>2
     SELECT COUNT(*),department_id
     FROM employees
     GROUP BY department_id;
     HAVING COUNT(*) > 2
     ```
     
  6. #### 连接查询

     连接查询又称多表查询，当查询字段来自多个表时，就会用到连接查询

     笛卡尔乘积：表1有m行，表2有n行，结果=m*n行

     **sql99语法**

     ​		select 查询列表

     ​		from 表1 别名 【连接类型】（内连接:inner 左外:left outer 右外:right outer 全外:full outer）

     ​		join 表2 别名

     ​		on 连接条件

     ​		where 筛选条件

     **三表连接**

     ```mysql
     SELECT last_name,department_name,job_title
     FROM departments d 
     INNER JOIN employees e ON d.department_id = e.department_id
     INNER JOIN jobs j ON e.job_id = j.job_id
     ORDER BY department_name DESC;
     ```

     

     ##### 内连接

     1. ###### 等值连接

        结果为多表的交集部分

        n表连接至少需要n-1连接条件

        多表连接没有顺序要求

        

     2. ###### 非等值连接

        ```mysql
        SELECT salary,grade_level
        FROM employees e,job_grades j
        WHERE salary BETWEEN j.lowest_sal AND j.highest_sal;
        
        
        #------方式二：SQL99语法---------------------------------------------------
        SELECT salary,grade_level
        FROM employees e
        INNER JOIN job_grades j
        ON e.salary BETWEEN j.lowest_sal AND j.highest_sal;
        ```

     3. ###### 自连接

        ```mysql
        SELECT e.last_name,e.employee_id,m.last_name,m.employee_id
        FROM employees e,employees m
        WHERE e.manager_id = m.employee_id;
        
        
        
        #-----------------SQL99语法--------------------------------
        SELECT e.last_name,m.last_name
        FROM employees e
        INNER JOIN employees m
        ON e.manager_id = m.employee_id;
        ```

        

     ##### 外连接

     用于查询一个表中有，一个表中没有的记录

     特点：外连接查询为主表中所有记录

     ​           左外连接左边的表为主表

     ​           右外连接右边的表为主表

     1. ###### 左外连接

        ```mysql
        SELECT e.employee_id,d.*
        FROM departments d
        LEFT JOIN employees e
        ON d.department_id = e.department_id
        WHERE e.employee_id IS NULL;
        ```

        

     2. ###### 右外连接

        ```mysql
        SELECT e.employee_id,d.*
        FROM employees e
        RIGHT JOIN departments d
        ON d.department_id = e.department_id
        WHERE e.employee_id IS NULL;
        ```

        

     3. ###### 全外连接

     ##### 交叉连接

      笛卡尔乘积

  7. #### 子查询

     出现在其他语句中的select语句

     子查询放在小括号内

     子查询一般放在条件的右侧

     ##### 按子查询出现的位置：

     ​	select后面：

     ​			仅仅支持标量子查询

     ```mysql
     SELECT d.*,(
     	SELECT COUNT(*)
     	FROM employees e
     	WHERE e.department_id = d.department_id
     ) 个数
     FROM departments d;
     ```

     ​	from后面：

     ​			支持表子查询

     ```mysql
     SELECT  ag_dep.*,g.`grade_level`
     FROM (
     	SELECT AVG(salary) ag,department_id
     	FROM employees
     	GROUP BY department_id
     ) ag_dep
     INNER JOIN job_grades g
     ON ag_dep.ag BETWEEN lowest_sal AND highest_sal;
     ```

     ★where或having后面：

     **标量子查询**：一般搭配单行操作符使用 (> < >= <= <>)

     ```mysql
     SELECT last_name,job_id,salary
     FROM employees
     WHERE salary = (
     SELECT MIN(salary) 
     FROM employees
     );
     ```

     ```mysql
     SELECT MIN(salary),department_id
     FROM employees
     GROUP BY department_id
     HAVING MIN(salary) >(
     	SELECT MIN(salary)
     	FROM employees
     	WHERE department_id = 50
     );
     ```

     ​	

     **列子查询**:一般搭配多行操作符使用（IN,ANY/SOME,ALL）

     ```mysql
     SELECT last_name
     FROM employees
     WHERE department_id IN(
     	SELECT DISTINCT department_id
     	FROM departments
     	WHERE location_id IN(1400,1700)
     
     );
     ```

     ​			行子查询

     ​	exists后面（相关子查询）：

     ​			exists(完整的查询语句)

     ​			结果:1或0

     ​			**表子查询**

     

     ##### 按结果集的行列数不同：

     ​		标量子查询（结果集只有一行一列）

     ​		列子查询（结果集一列多行）

     ​		行子查询（结果子一行多列）

     ​		表子查询（结果集一般为多行多列）

  8. #### 分页查询

     语法：
     	***select 查询列表***
     	***from 表***
      ***【join type join 表2***
     	***on 连接条件***
     	***where 筛选条件***
     	***group by 分组字段***
     	***having 分组后的筛选***
     	***order by 排序的字段】***
     	***limit 【offset,】size;***

     ​			offset要显示条目的起始索引（起始索引从0开始）
     ​			size 要显示的条目个数

     ​	***select 查询列表***
     ***​	from 表***
     ***​	limit (page-1)*size,size;***

  9. #### 分组查询语句中涉及到的关键字及执行顺序

     select  查询列表				 ⑦

     from 表1							 ①

     连接类型  join 表2			  ③

     on 连接条件						②

     where 筛选条件				  ④

     group by 分组列表   		   ⑤

     having 分组后的筛选 		  ⑥

     order by 排序列表			   ⑧

     limit 偏移，条目数			   ⑨
     
  10. #### 联合查询（要查询的结果来自多个表）

      **语法**：查询语句1

      ​			union

      ​			查询语句2

      ​			.。。。。。

      **特点**：要求多条查询语句列数一致

      ​			多条查询语句的查询的每一列的类型和顺序最好一致

      ​			使用union关键字默认是去重的

- ### DML  (数据操纵语言)

  1. #### 插入

     **语法**：insert into 表名（列名，...........） values(值1,....................)，.......,（值n..........）

     ​        或insert into 表名 

     ​            set 列名=值，列名=值............

  2. #### 修改语句

     **语法**：修改单表的记录：

     ​			***update 表名***

     ​			***set 列 = 新值，set 列 = 新值，set 列 = 新值***

     ​			***where 筛选条件；***

       

     ​        	修改多表的记录：

     ​			***update 表名***

     ​			***连接类型 join  表2  别名***

     ​			***on 连接条件***

     ​			***set 列 = 值，列=值.....***

     ​			***where  筛选条件***

  3. #### 删除

     **语法**：（单表的删除）***delete from 表名 where 筛选条件***

     ​			*（多表的删除）**delete 表1的别名，表2的别名***

     ​										***from 表1   别名***

     ​										***连接类型   join   表2 别名***

     ​										***on  连接条件***

     ​										***where 筛选条件***

     

     

     ​			truncate table 表名；

     **delete与truncate的区别**：

     ​		delete可以加where条件，truncate不可以加

     ​		truncate删除效率高

     ​		如果要删除的表有自增长列，用delete删除后，再添加数据，自增数列从断点开始

     用truncate删除，在添加数据，自增数列从1开始

     ​		truncate删除没有返回值，delete删除有返回值

     ​		truncate删除不能回滚，delete删除可以回滚

- ### DDL（数据定义语言）

  1. #### 库的管理

     **库的创建**：create database 库名；

     **库的修改**：alter database 库名 character set 新的字符集  （修改字符集）

     **库的删除**：drop database 库名

  2. #### 表的管理

     **表的创建**：create table 表名（

     ​						 列名	列的类型【（长度），约束】， 

     ​					列名	列的类型【（长度），约束】，

     ​					列名	列的类型【（长度），约束】，

     ​					列名	列的类型【（长度），约束】，

     ​									..............................

     ​					）

     **表的修改**：

     |              |                                                          |
     | ------------ | -------------------------------------------------------- |
     | 修改列名     | alter table 表名 change column 旧列名   新列名  字段类型 |
     | 修改列的类型 | alter table 表名 modify column 列名  新的字段类型        |
     | 添加新列     | alter table 表名 add column 新列名 字段类型              |
     | 删除列       | alter table 表名 drop column 列名                        |
     | 修改表名     | alter table 旧表名 rename to 新表名                      |

     **表的删除**：***drop table  表名***

     **表的复制**：仅仅复制表的结构

     ​							***create table copy like 表名***

     ​					复制表的结构+数据

     ​							***create table 表名***

     ​							***select 查询语句***

     ​							***from 被复制的表名***

     ​							***where 筛选条件***

  3. #### 常见的数据类型

     ##### 数值型

     ​			整型

     ​					*分类：**tinyint,smallint,mediumint,int/integer,bigint***

     ​					                 ***1  		2				3					4			8***

     ​					*特点：**如果不设置有符号还是无符号，默认是有符号***

     ​								***如果需要设置无符号，使用unsigned关键字***

     ​								***如果插入的数值超出范围，会报异常，并且插入临界值***

     ​			小数

     ​					浮点型

     ​								float(M,D)

     ​								double(M,D)

     

     ​					定点型

     ​								dec(M,D)

     ​								decimal(M,D)

     ​				M和D： ***M代表整数部位+小数部位的长度***

     ​								***D代表小数部位的长度***

     ​								***如果超过范围，则插入临界值***

     ​							***M和D都可以省略，但如果是decimal的话，则M默认类型为10，D默认为0***

     ##### 字符型

     ​			*较短的文本：**char（）：    char(M)      固定长的的字符	效率高***

     ​								 ***varchar（）： char(M)	  可变长度的字符	效率低***

     ​					***M代表最大的字符数,char中可以省略，默认为1；varchar中不可省略***

     

     ​			较长的文本：text blob

     ##### 日期型

     ​			date只保存日期

     ​			time只保存时间

     ​			year只保存年

     ​			datatime保存 日期+时间

     ​			timestamp保存 日期+时间

     |           | 字节 | 范围      | 时区等影响 |
     | --------- | ---- | --------- | ---------- |
     | datetime  | 8    | 1000-9999 | 不受       |
     | timestamp | 4    | 1970-2038 | 受         |

  4. #### 常见的约束

     含义：一种限制，用于限制表中的数据，为了保证表中数据的准确和可靠性

     | 约束名                | 作用                                                         |
     | --------------------- | ------------------------------------------------------------ |
     | NOT NULL(非空约束)    | 用于保证该字段的值不能为空                                   |
     | DEFAULT(默认约束)     | 用于保证该字段的值有默认值                                   |
     | PRIMARY KEY(主键约束) | 用于保证该字段的值具有唯一性，并且非空                       |
     | UNIQUE(唯一约束)      | 用于保证该字段的值具有唯一性，可以为空                       |
     | CHECK(检查约束)       | 【MySQL中不支持】                                            |
     | FOREIGN KEY(外键约束) | 用于限制两个表的关系，用于保证该字段的值必须来自于主表的关联列的值 |

  ​           约束的分类：列级约束:

  ​											***六大约束语法上都支持，但外键约束没有效果***

  ​								  表级约束:

  ​											***除了非空，默认，其他都支持***

  ```mysql
  create table 表名（
  		列名	字段类型	列级约束
  		列名	字段类型,
          表级约束
  
  ）
  ```

  1. #### 创建表示添加约束

     1. 添加列级约束

        ***直接在字段名和类型后面追加约束类型即可***

        ***只支持默认 非空  主键  唯一***

     2. 添加表级约束

        ***【constraint 约束名】 约束类型(字段名)*** 

        ```mysql
        CREATE TABLE stuinfo(
        	id INT,
        	stuname VARCHAR(20),
        	gender CHAR(1),
        	seat INT,
        	age INT,
        	majorid INT,
        	
        	CONSTRAINT pk PRIMARY KEY(id),#主键
        	CONSTRAINT uq UNIQUE(seat),#唯一键
        	CONSTRAINT fk_stuinfo_major FOREIGN KEY(majorid) REFERENCES major(id)#外键
        	
        );
        ```

     通用的写法：		

     ```mysql
     
     CREATE TABLE IF NOT EXISTS stuinfo(
     	id INT PRIMARY KEY,
     	stuname VARCHAR(20),
     	sex CHAR(1),
     	age INT DEFAULT 18,
     	seat INT UNIQUE,
     	majorid INT,
     	CONSTRAINT fk_stuinfo_major FOREIGN KEY(majorid) REFERENCES major(id)
     
     );
     ```

     |        | 保证唯一性 | 是否允许为空 | 一个表中可以有多个 | 是否允许组合 |
     | ------ | ---------- | ------------ | ------------------ | ------------ |
     | 主键   | √          | ×            | ×                  | √            |
     | 唯一键 | √          | √            | √                  | √            |

     **外键**：1.要求在从表设置外键关系

     ​			2.从表的外键列的类型和主表的关联列的类型一致或兼容

     ​			3.主表中的关联列必须是一个键（一般是主键或唯一键）

     ​			4.插入数据时，先插入主表，再插入从表

     ​				删除数据时，先删除从表数据，再删除主表

  2. #### 修改表时添加约束

     |              |                                                              |
     | ------------ | ------------------------------------------------------------ |
     | 添加列级约束 | alter table 表名 modify column 字段名 字段类型  新约束       |
     | 添加表级约束 | alter table 表名 add【constraint 约束名】约束类型（字段名）【外键的引用】 |

  3. #### 修改表时删除约束

     |              |                                                              |
     | ------------ | ------------------------------------------------------------ |
     | 删除非空约束 | alter table 表名  modify column  字段名 字段类型 null        |
     | 删除默认约束 | alter table 表名  modify column  字段名 字段类型 (直接去掉默认约束) |
     | 删除主键     | alter table 表名 drop PRIMARY KEY                            |
     | 删除唯一     | alter table 表名 drop index 唯一约束的字段名                 |
     | 删除外键     | alter table 表名 drop foreign key 外键名                     |

  4. #### 标识列

     又称为自增长列：可以不用手动的插入值，系统提供默认的序列值 

     特点：标识列必须和一个key搭配

     ​			一个表至多一个标识列

     ​			标识列的类型只能是数值型

     ​			表示列可以通过 set_increament_increament = 设置步长

     ​			可以通过手动插入设置起始值

     **修改表时设置标识列**

     ​			alter table 表名 modify column 字段名 字段类型 约束名 auto_increament

- ### TCL (事务控制语言)

  事务：一个或一组sql语句组成一个执行单员，这个执行单员要么全部执行，要么全部不执行

  事物的属性：ACID

  ​						***原子性：不可被分割***

  ​						***一致性：事务必须使数据库从一个一致性状态变换到另一个一致性状态***

  ​						***隔离性：一个事务的执行不被其他的事务干扰***

  ​						***持久性：一个事务一旦被提交，他对数据库的改变就是永久性的***

  1. #### 事物的创建

     ##### 隐式事务：

     ​					事务没有明显的开启和结束的标记

     ​					比如insert、update、delete语句

     ##### 显示事务

     ​				事务具有明显的开启和结束的标记
     ​				前提：必须先设置自动提交功能为禁用

     ```mysql
     步骤1：开启事务
     set autocommit=0;
     start transaction;可选的
     步骤2：编写事务中的sql语句(select insert update delete)
     语句1;
     语句2;
     ...
     
     步骤3：结束事务
     commit;提交事务
     rollback;回滚事务
     ```

     脏读：***对于两个事务T1,T2，T1读取了已经被T2更新但是没有被提交的字段之后，若T2回滚，T1读取的内容就是临时且无效的***

     不可重复读：***对于两个事务T1,T2，T1读取了一个字段，然后T2更新了该字段之后，T1再次读取同一字段，值就不同了***

     幻读：***对于两个事务T1,T2，T1从一个表中读取了一个字段，然后T2在该表中插入了一些新的行之后，如果T1再次读取同一个表，就会多出几行***

     查看隔离级别：**select @@tx_isolation;**

     设置当前MySQL连接的隔离级别：**set session transaction isolation level   隔离级别**

     设置数据库系统的全局隔离级别：**set global transaction isolation level   隔离级别**

     |                  | 脏读 | 不可重复读 | 幻读 |
     | ---------------- | ---- | ---------- | ---- |
     | read uncommitted | √    | √          | √    |
     | read committer   | ×    | √          | √    |
     | repeatable read  | ×    | ×          | √    |
     | serializable     | ×    | ×          | ×    |

     savepoint:设置保存点

  2. #### 视图

     虚拟表

     1. ##### 视图的创建 

        create view  视图名

        as

        查询语句；

     2. ##### 视图的修改

        方式一：  create or replace 视图名

        ​					as

        ​					查询语句

        方式二： alter view 视图名

        ​				as

        ​				查询语句

     3. ##### 删除视图

        drop view 视图名，视图名，......

     4. ##### 查看视图

        desc view 视图名

        show create view 视图名

     5. ##### 视图的更新

        插入：insert into  视图名  values()

        修改：update 视图名  set 列名 = 新值 where 筛选条件

        #具备以下特点的视图不允许更新

        ```mysql
        - 1. 1. #①包含以下关键字的sql语句：分组函数、distinct、group  by、having、union或者union all
        
                CREATE OR REPLACE VIEW myv1
                AS
                SELECT MAX(salary) m,department_id
                FROM employees
                GROUP BY department_id;
        
                SELECT * FROM myv1;
        
                #更新
                UPDATE myv1 SET m=9000 WHERE department_id=10;
        
                #②常量视图
                CREATE OR REPLACE VIEW myv2
                AS
        
                SELECT 'john' NAME;
        
                SELECT * FROM myv2;
        
                #更新
                UPDATE myv2 SET NAME='lucy';
        
                
        
             
                #③Select中包含子查询
        
                CREATE OR REPLACE VIEW myv3
                AS
        
                SELECT department_id,(SELECT MAX(salary) FROM employees) 最高工资
                FROM departments;
        
                #更新
                SELECT * FROM myv3;
                UPDATE myv3 SET 最高工资=100000;
        
        
                #④join
                CREATE OR REPLACE VIEW myv4
                AS
        
                SELECT last_name,department_name
                FROM employees e
                JOIN departments d
                ON e.department_id  = d.department_id;
        
                #更新
        
                SELECT * FROM myv4;
                UPDATE myv4 SET last_name  = '张飞' WHERE last_name='Whalen';
                INSERT INTO myv4 VALUES('陈真','xxxx');
        
                
        
                #⑤from一个不能更新的视图
                CREATE OR REPLACE VIEW myv5
                AS
        
                SELECT * FROM myv3;
        
                #更新
        
                SELECT * FROM myv5;
        
                UPDATE myv5 SET 最高工资=10000 WHERE department_id=60;
        
                
        
                #⑥where子句的子查询引用了from子句中的表
        
                CREATE OR REPLACE VIEW myv6
                AS
        
                SELECT last_name,email,salary
                FROM employees
                WHERE employee_id IN(
                	SELECT  manager_id
                	FROM employees
                	WHERE manager_id IS NOT NULL
                );
        
                #更新
                SELECT * FROM myv6;
                UPDATE myv6 SET salary=10000 WHERE last_name = 'k_ing';
        
        
        ```

- ### 变量

  1. #### 系统变量

     变量是由系统定义的，不是用户定义，属于服务器层面

     语法：查看所有的系统变量  ***show global|【session】variables***

     ​			查看满足条件的部分系统变量    ***show global|【session】variables like  '%.....%'***

     ​			查看指定的某个系统变量的值    ***select @@global|【session】系统变量名***

     ​			*为某个系统变量赋值		**set  global|【session】 系统变量名 = 值；***

     ​														***set @@global|【session】.系统变量名 =值***

     1. ##### 全局变量

        针对所有的会话有效，但是不能跨重启

     2. ##### 会话变量

        作用域：仅仅针对于当前的会话（连接）有效

  2. #### 自定义变量

     

     1. ##### 用户变量

        作用域：针对于当前会话（连接）

        步骤：① 声明并初始化			set @用户变量名 = 值

        ​													set @用户变量名:= 值

        ​													select @用户变量名 := 值

        ​			②赋值							set @用户变量名 = 值

        ​													set @用户变量名:= 值

        ​													select @用户变量名 := 值

        ​													**方式二**

        ​													SELECT 字段 INTO @变量名
        ​													FROM 表;

        ​			

        ​			③查看							SELECT @变量名;

     2. ##### 局部变量

        作用域：仅仅在定义它的begin end 中有效

        ​				应用在begin end 中的第一句话

        步骤：	①声明					
        											DECLARE 变量名 类型;
        											DECLARE 变量名 类型 【DEFAULT 值】;

        ​				②赋值

        ​											SELECT 字段 INTO @局部变量名
        ​											FROM 表;

        ​											**方式二**

        ​											set 局部变量名 = 值

        ​											set 局部变量名:= 值

        ​											select 局部变量名 := 值

        ​				③使用

        ​											select 局部变量名

- ### 存储过程和函数

  1. #### 存储过程

     含义：一组预先编译好的SQL语句的集合，理解成批处理语句

     1、提高代码的重用性
     2、简化操作
     3、减少了编译次数并且减少了和数据库服务器的连接次数，提高了效率

     ​          

     ##### 一、创建语法

     ```mysql
     CREATE PROCEDURE 存储过程名(参数列表)
     BEGIN
     
     存储过程体（一组合法的SQL语句）
     
     END
     #注意：
     /*
     1、参数列表包含三部分
     参数模式  参数名  参数类型
     举例：
     in stuname varchar(20)
     
     参数模式：
     in：该参数可以作为输入，也就是该参数需要调用方传入值
     out：该参数可以作为输出，也就是该参数可以作为返回值
     inout：该参数既可以作为输入又可以作为输出，也就是该参数既需要传入值，又可以返回值
     
     2、如果存储过程体仅仅只有一句话，begin end可以省略
     存储过程体中的每条sql语句的结尾要求必须加分号。
     存储过程的结尾可以使用 delimiter 重新设置
     语法：
     delimiter 结束标记
     案例：
     delimiter $
     */
     ```

     ##### 二、调用语法

     CALL 存储过程名(实参列表);

        

     ```mysql
     #案例2 ：创建存储过程实现，用户是否登录成功
     
     CREATE PROCEDURE myp4(IN username VARCHAR(20),IN PASSWORD VARCHAR(20))
     BEGIN
     	DECLARE result INT DEFAULT 0;#声明并初始化
     	
     	SELECT COUNT(*) INTO result#赋值
     	FROM admin
     	WHERE admin.username = username
     	AND admin.password = PASSWORD;
     	
     	SELECT IF(result>0,'成功','失败');#使用
     END $
     
     #调用
     CALL myp3('张飞','8888')$
     ```

  2. #### 存储过程的删除

     语法：drop procedure 存储过程名

  3. #### 查看存储过程

     SHOW CREATE PROCEDURE 存储过程名



## 索引

MySQL官方对索引的定义为：索引是帮助MySQL高效获取数据的数据结构



### 索引的分类

- 主键索引
  - 唯一表示，主键不可重复，只能有一个
- 唯一索引
  - 避免重读的列出现，索引可以重复
- 常规索引（key/index）
  - 默认的
- 全文索引（FullText）
  - 快速定位数据

**使用ALTER TABLE语句创建索引。**

语法如下：

1.PRIMARY KEY（主键索引）
    mysql>ALTER TABLE `table_name` ADD PRIMARY KEY ( `column` ) 
2.UNIQUE(唯一索引)
     mysql>ALTER TABLE `table_name` ADD UNIQUE (`column` ) 
3.INDEX(普通索引)
    mysql>ALTER TABLE `table_name` ADD INDEX index_name ( `column` )
4.FULLTEXT(全文索引)
    mysql>ALTER TABLE `table_name` ADD FULLTEXT ( `column` )
5.多列索引
    mysql>ALTER TABLE `table_name` ADD INDEX index_name ( `column1`, `column2`, `column3` )

**使用CREATE INDEX语句对表增加索引。**

能够增加普通索引和UNIQUE索引两种。其格式如下：
create index index_name on table_name (column_list) ;
create unique index index_name on table_name (column_list) ;

 
