# MySQL高级

## 1.MySQL基本介绍

### 1.1 MySQL配置文件

- 二进制日志log-bin:用于主从复制
- 错误日志log-error：默认是关闭的，记录严重的警告和错误信息，每次启动和关闭的详细信息等
- 查询日志log:默认关闭，记录查询的SQL语句，如果开启会降低MySQL的整体性能
- 数据文件
  - frm文件：存放表结构
  - myd文件：存放表数据
  - myi文件：存放表缩影



### 1.2 MySQL逻辑架构介绍

![img](https://img-blog.csdn.net/20180831173911997?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3pfcnlhbg==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

-  最上层`连接层`是一些客户端和连接服务，包含本地的sock通信和大多数基于客户端/服务端工具实现的类似于tcp/ip的通信，主要完成一些类似于连接处理、授权认证及相关的安全方案，在该层上引用了线程池的概念，为通过认证安全接入的客户端提供线程。同样在该层上可以实现基于ssl的安全链接。服务器也会为安全接入的每个客户端验证它所具有的操作权限。

- 第二层`服务层`架构主要完成大多数的核心服务功能。如sql接口，并完成缓存的查询。sql的分析和优化 以及部分内置函数的执行。所有跨存储引擎的功能也在这一层实现，如过程，函数等。在该层，服务器会解析查询并创建相应的内部解析树，并对其完成相应的优化如确定查询表的顺序，是否利用索引等。最后生成相应的执行操作。如select语句，服务器还会查询内部的缓存。如果缓存空间足够大，这样就解决大量读操作的环境中能够很好的提升系统的性能。

- 存储引擎层：

  ​    存储引擎真正的负责MySQL中数据的存储和提取，服务器通过API与存储引擎进行通信，不同的存储引擎具有的功能不同，这样我们可以根据自己的实际需进行选取。

- 数据存储层：

  ​    主要是将数据存储在运行于裸设备的文件系统之上，并完成于存储引擎的交互。

和其他数据库相比，MySQL有点与众不同，它的架构可以在多种不同场景中应用并发挥良好的作用。主要体现在存储引擎架构上，**插件式的存储引擎架构将查询处理和其他的系统任务以及数据的存储提取相分离。**这种架构可以根据业务的需求和实际上的需要选择合适的存储引擎



### 1.3 MySQL存储引擎介绍

> 查看MySQL当前默认的存储引擎

```BASH
show variables like '%storage_engine%';
```

![image-20211008104940321](F:\笔记图片\img\image-20211008104940321.png)

| 对比项   | MyISAM                                                 | InnoDB                                                       |
| -------- | ------------------------------------------------------ | ------------------------------------------------------------ |
| 主外键   | 不支持                                                 | 支持                                                         |
| 事务     | 不支持                                                 | 支持                                                         |
| 行表锁   | 表锁，即使操作一条记录也会锁住整个表，不适合高并发操作 | 行锁，操作失只锁一行，不对其他行有影响，适合高并发操作       |
| 缓存     | 只缓存索引，不缓存真实数据                             | 不仅缓存索引还缓存真实数据，对内存要求极高，而且内存大小对性能有决定性影响 |
| 表空间   | 小                                                     | 大                                                           |
| 关注点   | 性能                                                   | 事务                                                         |
| 默认安装 | Y                                                      | Y                                                            |

## 2.MySQL索引优化

### 2.1 SQL性能下降的原因

> 执行时间长

- 查询语句写的烂
- 索引失效
- 关联查询太多join
- 服务器调优及各个参数

```MYSQL
SELECT DISTINCE
	<select_list>
FROM
	<left_table> 
	<join_type> JOIN <right_table>
ON
	<join_condition>
WHERE
	<where_condition>
GROUP BY
	<group_by_list>
HAVING
	<having_condition>
ORDER BY
	<order_by_condition>
LIMIT <limit number>
```



### 2.2 七种JOIN

```mysql
CREATE TABLE `tbl_emp` (
`id` int(11) NOT NULL AUTO_INCREMENT,
`name` varchar(20) DEFAULT NULL,
`deptId` int(11) DEFAULT NULL,
PRIMARY KEY (`id`) ,
KEY `fk_dept_id`(`deptId`)
)ENGINE = InnoDB AUTO_INCREMENT = 1 CHARACTER SET = utf8;

CREATE TABLE `tbl_dept` (
`id` int(11) NOT NULL AUTO_INCREMENT,
`deptName` varchar(30) DEFAULT NULL,
`locAdd` varchar(40) DEFAULT NULL,
PRIMARY KEY (`id`)
) ENGINE = InnoDB AUTO_INCREMENT = 1 CHARACTER SET = utf8;

insert into tbl_dept(deptName,locAdd) values('RD',11);
insert into tbl_dept(deptName,locAdd) values('HR',12);
insert into tbl_dept(deptName,locAdd) values('MK',13);
insert into tbl_dept(deptName,locAdd) values('MIS',14);
insert into tbl_dept(deptName,locAdd) values('FD',15);

insert into tbl_emp(NAME,deptId) values('z3',1);
insert into tbl_emp(NAME,deptId) values('z4',1);
insert into tbl_emp(NAME,deptId) values('z5',1);
insert into tbl_emp(NAME,deptId) values('w5',2);
insert into tbl_emp(NAME,deptId) values('w6',2);
insert into tbl_emp(NAME,deptId) values('s7',3);
insert into tbl_emp(NAME,deptId) values('s8',4);
insert into tbl_emp(NAME,deptId) values('s9',51);

```

![image-20211008143733367](F:\笔记图片\img\image-20211008143733367.png)

![image-20211009085215461](F:\笔记图片\img\image-20211009085215461.png)

**内连接**

![image-20211008143758085](F:\笔记图片\img\image-20211008143758085.png)

**左外连接**

![image-20211008143831671](F:\笔记图片\img\image-20211008143831671.png)

**右外连接**

![image-20211008143848976](F:\笔记图片\img\image-20211008143848976.png)

**左表独有**

![image-20211008143918873](F:\笔记图片\img\image-20211008143918873.png)

**右表独有**

![image-20211008143934889](F:\笔记图片\img\image-20211008143934889.png)

**查询两个表的全部数据并去重**

![image-20211008144019179](F:\笔记图片\img\image-20211008144019179.png)

**查询两个表各个独有的部分**

![image-20211008144156394](F:\笔记图片\img\image-20211008144156394.png)



### 2.3 索引

> 什么是索引

- MySQL官方对索引的定义为：索引是帮助MySQL高效获取数据的数据结构。

  可以得到索引的本质:索引时数据结构

  可以理解为**排好序的快速查找数据结构**

  在数据之外，数据库系统还维护着满足特定查找算法的数据结构，这些数据结构以某种方式引用（指向）数据，这样就可以在这些数据结构的基础上实现高级查找算法，这种数据结构就是索引



**索引本身也很大，不可能全部存储在内存中，因此索引往往以索引文件的形式存储在磁盘上**



> 索引的优势

- 提高了数据的检索效率，降低了数据库的IO效率
- 通过索引列对数据进行排序，降低数据排序的成本，降低了CPU的消耗



> 索引的劣势

- 实际上索引也是一张表，该表保存了主键与索引字段，并指向实体表记录，所以索引也是要占用空间的
- 虽然索引大大提高了查询速度，同时却降低了更新表的速度，因为更新表时，MySQL不仅要保存数据，还要保存一下索引文件每次更新添加了索引列的字段，都会调整因为更新所带来的键值变化后的索引信息

- 索引只是提高效率的一个因素，如果MySQL有数据量的表，就需要花费时间研究建立最优秀的索引



### 2.4 索引的分类

- 单值索引

  即一个索引只包含单个列，一个表可以有多个单列索引

- 唯一索引

  索引列的值必须唯一，但允许有空值

- 复合索引

  即一个索引包含多个列



> 语法

```mysql
CREATE [UNIQUE] INDEX indexName ON mytable(columnname(length));
ALTER mytable ADD [UNIQUE] INDEX [indexName] ON (columnname(length));

DROP INDEX[indexName] ON mytable;

SHOW INDEX FROM table_name\G


#有四种方式来添加数据表的索引
ALTER TABLE tbl_name ADD PRIMARY KEY(column_list):该语句添加一个主键，这意味着索引值必须是唯一的，且不能为null
ALTER TABLE tbl_name ADD UNIQUE index_name(column_list):这条语句创建索引的值必须是唯一的（除了NULL外，NULL可能会出现多次）
ALTER TABLE tbl_name ADD INDEX index_name(column_list):添加普通索引，索引值可出现多次
ALTER TABLE tbl_name ADD FULLTEXT index_name(column_list):该语句指定了索引为FULLTEXT，用于全文索引
```



### 2.5 索引结构与检索原理

**B-Tree索引**

 B-Tree 索引是 MySQL 数据库中使用最为频繁的索引类型，除了 Archive 存储引擎之外的其他所有的存储引擎都支持 B-Tree 索引。Archive 引擎直到 MySQL 5.1 才支持索引，而且只支持索引单个 AUTO_INCREMENT 列。

不仅仅在 MySQL 中是如此，实际上在其他的很多数据库管理系统中B-Tree 索引也同样是作为最主要的索引类型，这主要是因为 B-Tree 索引的存储结构在数据库的数据检索中有非常优异的表现。

 一般来说， MySQL 中的 B-Tree 索引的物理文件大多都是以 Balance Tree 的结构来存储的，也就是所有实际需要的数据都存放于 Tree 的 Leaf Node(叶子节点) ，而且到任何一个 Leaf Node 的最短路径的长度都是完全相同的，所以我们大家都称之为 B-Tree 索引。当然，可能各种数据库（或 MySQL 的各种存储引擎）在存放自己的 B-Tree 索引的时候会对存储结构稍作改造。**如 Innodb 存储引擎的 B-Tree 索引实际使用的存储结构实际上是 B+Tree**，也就是在 B-Tree 数据结构的基础上做了很小的改造，在每一个Leaf Node 上面出了存放索引键的相关信息之外，还存储了指向与该 Leaf Node 相邻的后一个 LeafNode 的指针信息（增加了顺序访问指针），这主要是为了加快检索多个相邻 Leaf Node 的效率考虑。



![b树索引原理](https://qqe2.com/java/data/upload/ueditor/20200712/5f0af2df2e642.png)

> 初始化介绍：

一颗b+树，浅蓝色的块我们称之为一个磁盘块，可以看到每个磁盘块包含几个数据项（深蓝色所示）和指针（黄色所示）

p1表示小于17的磁盘块，p2表示在17和35之间的磁盘块，p3表示大于35的磁盘块

**真实的数据存在于叶子节点**,即3，5，9，10..........

非叶子节点不存储真实数据，只存储指引搜索方向的数据项，如17，35并不真实存在与数据表中



> 查找过程

如果要查找数据项29，那么首先会把磁盘块1有磁盘加载进内存，此事发生一次IO，在内存中用二分查找确定29在17和35之间，锁定磁盘块的p2指针，内存时间因为非常短（相比于磁盘的IO）可以忽略不计，通过磁盘1的p2指针的磁盘地址把磁盘3有磁盘加载进内存，发生第二次IO，29在26和30之间，锁定磁盘3的p2指针，通过指针加载磁盘块8到内存，发生第三次IO，同时内存中做二分查找法找到29，结束查询，总共三次IO

**三层的b+树可以表示上百万的数据，如果上百万的数据查找只需要三次IO，那么性能的提高是巨大的**



> 索引使用的注意点

- 主键自动建立唯一索引
- 频繁作为查询条件的字段应该创建索引
- 查询中与其他表关联的字段，外键关系建立索引
- 频繁更新的字段不适合创建索引，因为每次更新不单是更新数据，还要更新索引树
- where条件里用不到的字段不创建索引
- 在高并发下倾向创建组合索引
- 查询中排序的字段，排序字段若通过索引去访问讲大大提高排序速度
- 查询中统计或者分组的字段要建立索引

> 不需要建索引

- 表记录太少
- 经常增删改的表
- 数据重复且分布平均的字段



### 2.6 性能分析

MySQL常见瓶颈：

- CPU：CPU的饱和一般发生在数据装入内存或者从磁盘上读数据的时候
- IO：磁盘IO瓶颈发生在装入数据远大于内存容量的时候
- 服务器硬件的性能瓶颈



#### 2.6.1 explain

> 是什么

- 使用explain关键字可以模拟优化器执行SQL的查询语句，从而知道MySQL是如何处理你的SQL语句的。分析你的查询语句或是表结构的性能瓶颈

> 怎么用

- ```SQL
  explain  + sql语句
  ```

  ![image-20211008162555054](F:\笔记图片\img\image-20211008162555054.png)

- 执行计划包含的信息

  - id:select查询的序列号，包含了一组数字，表示查询中执行select字句或操作表的顺序
    - id相同，执行顺序由上到下
    - id不同，如果是子查询，id的序列号会递增，id值越大优先级越高，越先被执行
    - id有相同，也有不相同，同时存在：id相同可以被认为是同一组，从上往下顺序执行，在所有组中，id越大，优先级越高，越先执行
    
  - select_type：查询类型，主要是用于区别普通查询，联合查询，子查询和复杂查询

    - SIMPLE：简单的select查询，查询中不包含子查询和UNION
    - PRIMARY：查询中包含任何的子部分，最外层查询标记为PRIMARY
    - SUBQUERY：在select或WHERE列表中包含了子查询
    - DERIUED：在FROM列表中包含的子查询被标记为DERVIED(衍生)，MySQL会递归执行这些子查询，把结果放在临时表里
    - UNION：在第二个select出现在UNION之后，则被标记为UNION；若UNION包含在FROM子句的子查询中，外层select将标记为DERIVED
    - UNION RESULT：从UNION表获取结果的select

  - table：显式这一行数据是关于哪张表的

  - type：显示了查询使用了何种类型，从最好到最差依次是：**system>const>eq_ref>ref>range>index>ALL**,一般来说，得至少查询达到range级别，最好能达到ref

    - system：表只有一行记录（等于系统表），这是const类型的特例，平时不会出现
    - const：表示通过索引一次就找到了，const用于比较primary key或者unique索引。因为只匹配一行数据，所以很快。如果将主键置于where列表中，MySQL就能将该查询转换为一个常量
    - eq_ref：唯一性索引扫描，对于每个索引键，表中只有一条记录与之匹配。常用于主键或唯一索引扫描
    - ref：非唯一性索引扫描，返回匹配某个单独值的所有行。本质上也是一种索引访问，它返回所有匹配某个单独值的行，然而，他可能会找多个符合条件的行，所以它应该属于查找和扫描的混合体
    - 只检索给定范围的行，使用一个索引来选择行。key列显示使用了哪个索引。一般就是你在where语句中出现了between，<, >，in等的查询，这种范围扫描索引比全表扫描要好，因为它只需要开始于索引的某一点，而结束于另一点，不用扫描全部索引
    - index：Full Index Scan,index与ALL区别为index类型只遍历索引树，这通常比ALL快，因为索引文件通常比数据文件小。（也就是说虽然all和index都是读全表，但index是从索引中读取的，而all是从硬盘中读取的）
    - all:Full table scan.将遍历全表找到匹配的行

  - possible_keys:显示可能会应用到这张表的索引，一个或者多个。查询涉及到的字段若存在索引，则该索引将被列出，但**不一定会被查询实际使用**

  - keys：查询中实际用到的索引，如果为NULL，则没有使用索引

    ​			**若查询中使用了覆盖索引，则该索引仅出现在key列表中**

    - `覆盖索引`：如果一个索引包含（覆盖）所有需要查询的字段的值，我们就称之为“覆盖索引”。select的数据列只从索引中就获得了，不必读取数据行，MySQL可以利用索引返回select列表中的字段，而不必根据索引再次读取数据文件，换句话说**查询列要被所建的索引覆盖**

  - key_len：表示索引中使用的字节数，可通过该列计算查询中使用的索引的长度。在不损失精确性的情况下，长度越短越好

    key_len显示的值为索引字段的最大可能长度，并非实际长度，即key_len是根据表定义计算而得，不是通过表内检索的

  - ref：显示索引的那一列被使用了，如果可能的话，是一个常数。哪些列或者常量被用于查找索引列上的值

  - rows：根据表信息及索引选用情况，大致估算出找到所需的记录所需要读取的行数

  - extra：包含不适合在其他列中显示但十分重要的信息

    - Using filesort：说明MySQL会对数据使用一个外部的索引排序，而不是按照表内的索引顺序进行读取。MySQL中无法利用索引完成的排序操作称为“文件排序”
    - Using temporary：使用了临时表保存中间结果，MySQL在对查询结果排序时使用临时表。常见排序order by和分组查询group by
    - USING index：表示相应的select操作中使用了覆盖索引，避免访问了表的数据行，效率不错，如果同时出现using where,表明索引被用来执行索引键值的查找；如果没有同时出现using where,表名索引用来读取数据而非执行查找动作



> 能干吗

- 表的读取顺序
- 数据读取操作的操作类型
- 哪些索引可以使用
- 哪些索引被实际使用
- 表之间的引用
- 每张表有多少行被优化器查询



### 2.7 索引分析

#### 2.7.1 单表分析

```sql
CREATE TABLE IF NOT EXISTS `article`(
`id` INT(10) UNSIGNED NOT NULL PRIMARY KEY AUTO_INCREMENT,
`author_id` INT (10) UNSIGNED NOT NULL,
`category_id` INT(10) UNSIGNED NOT NULL , 
`views` INT(10) UNSIGNED NOT NULL , 
`comments` INT(10) UNSIGNED NOT NULL,
`title` VARBINARY(255) NOT NULL,
`content` TEXT NOT NULL
);

insert into `article`(author_id,category_id,views,comments,title,content) values
(1,1,1,1,'1','1'),
(2,2,2,2,'2','2'),
(1,1,3,3,'3','3');

```

![image-20211009143540154](F:\笔记图片\img\image-20211009143540154.png)

我们可以看出，在使用了范围之后，文件内部又发生了重新排序

我们已经建立了索引，但是为啥没用呢？

- 这是因为按照BTREE索引的工作原理，先排序category_id
- 如果遇到相同的category_id，再排序comments，如果遇到相同的comments，则再排序views
- 当comments字段在联合索引里处于中间位置时，因为comments>1时是一个范围值，MySQL无法利用索引再对后面的views部分进行检索，即range类型查询字段后面的索引无效

**开始优化**

- 创建不使用范围的索引

  ```sql
  CREATE index idx_article_cv on article(category_id,views);
  show index from article;
  explain select id,author_id from article where category_id = 1 and comments > 1 order by views desc limit 1;
  ```

  ![image-20211009144944964](F:\笔记图片\img\image-20211009144944964.png)



#### 2.7.2 双表分析

```Java
CREATE TABLE IF NOT EXISTS `class`(
`id` INT(10) UNSIGNED NOT NULL PRIMARY KEY AUTO_INCREMENT,
`card` INT (10) UNSIGNED NOT NULL
);
CREATE TABLE IF NOT EXISTS `book`(
`bookid` INT(10) UNSIGNED NOT NULL PRIMARY KEY AUTO_INCREMENT,
`card` INT (10) UNSIGNED NOT NULL
);

insert into class(card) values(floor(1+(rand()*20)));
insert into class(card) values(floor(1+(rand()*20)));
insert into class(card) values(floor(1+(rand()*20)));
insert into class(card) values(floor(1+(rand()*20)));
insert into class(card) values(floor(1+(rand()*20)));
insert into class(card) values(floor(1+(rand()*20)));
insert into class(card) values(floor(1+(rand()*20)));
insert into class(card) values(floor(1+(rand()*20)));
insert into class(card) values(floor(1+(rand()*20)));
insert into class(card) values(floor(1+(rand()*20)));
insert into class(card) values(floor(1+(rand()*20)));
insert into class(card) values(floor(1+(rand()*20)));
insert into class(card) values(floor(1+(rand()*20)));
insert into class(card) values(floor(1+(rand()*20)));
insert into class(card) values(floor(1+(rand()*20)));
insert into class(card) values(floor(1+(rand()*20)));
insert into class(card) values(floor(1+(rand()*20)));
insert into class(card) values(floor(1+(rand()*20)));
insert into class(card) values(floor(1+(rand()*20)));
insert into class(card) values(floor(1+(rand()*20)));

insert into book(card) values(floor(1+(rand()*20)));
insert into book(card) values(floor(1+(rand()*20)));
insert into book(card) values(floor(1+(rand()*20)));
insert into book(card) values(floor(1+(rand()*20)));
insert into book(card) values(floor(1+(rand()*20)));
insert into book(card) values(floor(1+(rand()*20)));
insert into book(card) values(floor(1+(rand()*20)));
insert into book(card) values(floor(1+(rand()*20)));
insert into book(card) values(floor(1+(rand()*20)));
insert into book(card) values(floor(1+(rand()*20)));
insert into book(card) values(floor(1+(rand()*20)));
insert into book(card) values(floor(1+(rand()*20)));
insert into book(card) values(floor(1+(rand()*20)));
insert into book(card) values(floor(1+(rand()*20)));
insert into book(card) values(floor(1+(rand()*20)));
insert into book(card) values(floor(1+(rand()*20)));
insert into book(card) values(floor(1+(rand()*20)));
insert into book(card) values(floor(1+(rand()*20)));
insert into book(card) values(floor(1+(rand()*20)));
insert into book(card) values(floor(1+(rand()*20)));

```

![image-20211009150534510](F:\笔记图片\img\image-20211009150534510.png)

我们可以看到type出现了最差的ALL



**索引优化**

给右表添加索引后：

![image-20211009150720007](F:\笔记图片\img\image-20211009150720007.png)

给左表添加索引后

![image-20211009150922265](F:\笔记图片\img\image-20211009150922265.png)

这是由左连接的特性决定的，LEFT JOIN条件用于确定如何从右表搜索行，左边一定都有；**所以右边是我们的关注点，一定要建立索引**，RIGTH JOIN则反之。



#### 2.7.3 三表分析

```sql
CREATE TABLE IF NOT EXISTS `phone`(
`phoneid` INT(10) UNSIGNED NOT NULL PRIMARY KEY AUTO_INCREMENT,
`card` INT (10) UNSIGNED NOT NULL
)ENGINE = INNODB;

insert into phone(card) values(floor(1+(rand()*20)));
insert into phone(card) values(floor(1+(rand()*20)));
insert into phone(card) values(floor(1+(rand()*20)));
insert into phone(card) values(floor(1+(rand()*20)));
insert into phone(card) values(floor(1+(rand()*20)));
insert into phone(card) values(floor(1+(rand()*20)));
insert into phone(card) values(floor(1+(rand()*20)));
insert into phone(card) values(floor(1+(rand()*20)));
insert into phone(card) values(floor(1+(rand()*20)));
insert into phone(card) values(floor(1+(rand()*20)));
insert into phone(card) values(floor(1+(rand()*20)));
insert into phone(card) values(floor(1+(rand()*20)));
insert into phone(card) values(floor(1+(rand()*20)));
insert into phone(card) values(floor(1+(rand()*20)));
insert into phone(card) values(floor(1+(rand()*20)));
insert into phone(card) values(floor(1+(rand()*20)));
insert into phone(card) values(floor(1+(rand()*20)));
insert into phone(card) values(floor(1+(rand()*20)));
insert into phone(card) values(floor(1+(rand()*20)));
insert into phone(card) values(floor(1+(rand()*20)));

```

![image-20211009152350007](F:\笔记图片\img\image-20211009152350007.png)

type全是最差的ALL

**索引优化**

![image-20211009153950630](F:\笔记图片\img\image-20211009153950630.png)



由此我们可以看出：**索引最好设置在经常查询的字段中**

**尽可能减少join语句中的循环总次数，永远用小的结果集驱动大的结果集**

**优先优化嵌套循环里面的内层循环**

**保证join语句中 被驱动表上join条件字段已经被索引**





### 2.8 索引优化

```sql
CREATE TABLE staffs(
id INT PRIMARY KEY AUTO_INCREMENT,
`name` VARCHAR(24)NOT NULL DEFAULT'' COMMENT'姓名',
`age` INT NOT NULL DEFAULT 0 COMMENT'年龄',
`pos` VARCHAR(20) NOT NULL DEFAULT'' COMMENT'职位',
`add_time` TIMESTAMP NOT NULL DEFAULT CURRENT_TIMESTAMP COMMENT'入职时间'
)CHARSET utf8 COMMENT'员工记录表';

insert into staffs(NAME,age,pos,add_time) values('z3',22,'manager',NOW());
insert into staffs(NAME,age,pos,add_time) values('July',23,'dev',NOW());
insert into staffs(NAME,age,pos,add_time) values('2000',23,'dev',NOW());

select * from staffs;

ALTER table staffs add index inx_staff_nameAgePos(name,age,pos);
```



1. > 全值匹配

   ![image-20211009160858380](F:\笔记图片\img\image-20211009160858380.png)

   ![image-20211009160919594](F:\笔记图片\img\image-20211009160919594.png)

   ![image-20211009160938249](F:\笔记图片\img\image-20211009160938249.png)

   `我们改变一下`

   ![image-20211009161211032](F:\笔记图片\img\image-20211009161211032.png)

   ![image-20211009161418433](F:\笔记图片\img\image-20211009161418433.png)

   ![image-20211009161918354](F:\笔记图片\img\image-20211009161918354.png)

   这违背了最佳左前缀法则

2. > 最佳左前缀法则

   如果索引了多列，要遵守最佳左前缀法则。指的是**查询从索引的最左前列开始并且不跳过索引中的列**----->`带头大哥不能无，中间兄弟不能断`

3. > 不在索引列上做任何操作(计算，函数，（自动or手动）类型转换)，否则会导致索引失效而转向全表扫描

   ![image-20211009162512535](F:\笔记图片\img\image-20211009162512535.png)

4. > 存储引擎不能使用索引中范围条件右边的列

   ![image-20211009162837863](F:\笔记图片\img\image-20211009162837863.png)

   **注意**

   ​	此时age的索引也用到了，但是age后边的索引失效了

5. > 尽量使用覆盖索引，只访问索引的查询（索引列和查询列一致），减少select*

   ![image-20211009163333722](F:\笔记图片\img\image-20211009163333722.png)

6. > MySQL在使用不等于（!= 或 <>）的时候无法使用索引会导致全表扫描

   ![image-20211009164048171](F:\笔记图片\img\image-20211009164048171.png)

   **注意**

   ​	***如果此处使用了覆盖索引，则会直接使用索引***

   ![image-20211009164503285](F:\笔记图片\img\image-20211009164503285.png)

7. > is null,ni not null也无法使用索引

   ![image-20211009164737242](F:\笔记图片\img\image-20211009164737242.png)

8. > like以通配符开头（'%abc...'）MySQL索引失效会变成全表扫描的操作

   ![image-20211009165304636](F:\笔记图片\img\image-20211009165304636.png)

   like右边加%

   ![image-20211009170227613](F:\笔记图片\img\image-20211009170227613.png)

   ```java
   CREATE TABLE tbl_user(
   `id` INT(11) NOT NULL AUTO_INCREMENT,
   `name` VARCHAR(20) DEFAULT NULL,
   `age`INT(11) DEFAULT NULL,
   `email` VARCHAR(20) DEFAULT NULL,
   PRIMARY KEY(`id`)
   )ENGINE=INNODB AUTO_INCREMENT=1 DEFAULT CHARSET=utf8;
   
   insert into tbl_user(NAME,age,email) values('1aa1',21,'b@163.com');
   insert into tbl_user(NAME,age,email) values('2aa2',222,'a@163.com');
   insert into tbl_user(NAME,age,email) values('3aa3',265,'c@163.com');
   insert into tbl_user(NAME,age,email) values('4aa4',21,'d@163.com');
   
   ```

   ![image-20211009170819207](F:\笔记图片\img\image-20211009170819207.png)

   **解决%like%，我们推荐使用覆盖索引**

   （id为主键索引）

   ![image-20211009171344653](F:\笔记图片\img\image-20211009171344653.png)

9. > 字符串不加单引号会导致索引失效

   数据类型自动转换（int --->varchar）,索引失效

   ![image-20211009171944097](F:\笔记图片\img\image-20211009171944097.png)

   ![image-20211009172156064](F:\笔记图片\img\image-20211009172156064.png)

10. > 少用or,用它来连接时会索引失效

    ![image-20211009172336826](F:\笔记图片\img\image-20211009172336826.png)



### 2.9 索引面试题

```MySQL
create table test03(
id int primary key not null auto_increment,
c1 char(10),
c2 char(10),
c3 char(10),
c4 char(10),
c5 char(10)
);

insert into test03(c1,c2,c3,c4,c5) values('a1','a2','a3','a4','a5');
insert into test03(c1,c2,c3,c4,c5) values('b1','b2','b3','b4','b5');
insert into test03(c1,c2,c3,c4,c5) values('c1','c2','c3','c4','c5');
insert into test03(c1,c2,c3,c4,c5) values('d1','d2','d3','d4','d5');
insert into test03(c1,c2,c3,c4,c5) values('e1','e2','e3','e4','e5');

CREATE index inx_test03_c1234 on test03(c1,c2,c3,c4);
```

![image-20211010160127647](F:\笔记图片\img\image-20211010160127647.png)

此处索引列的顺序与我们查询列的顺序并不相同，但是索引仍旧没有失效，这是为什么呢？

**MySQL查询优化器会把命令信息自动调整优化以达到最佳效果**



![image-20211010160642671](F:\笔记图片\img\image-20211010160642671.png)

**此处也要考虑MySQL调优**



![image-20211010160839168](F:\笔记图片\img\image-20211010160839168.png)

**此处的c3实际上是用到了，但是它没有由于查找，而是用于排序**



![image-20211010161045117](F:\笔记图片\img\image-20211010161045117.png)



![image-20211010161858507](F:\笔记图片\img\image-20211010161858507.png)

**出现了filesort**



![image-20211010162154453](F:\笔记图片\img\image-20211010162154453.png)

**只用到了c1一个字段检索，但c2 c3用于排序，无filesort**



![image-20211010162338947](F:\笔记图片\img\image-20211010162338947.png)

**出现了filesort，我们的索引顺序为1234，order by 后面没有按顺序来**



![image-20211010162555693](F:\笔记图片\img\image-20211010162555693.png)

**用到了c1 c2两个检索，同时c2 c3用于排序**



![image-20211010162711748](F:\笔记图片\img\image-20211010162711748.png)



![image-20211010162827279](F:\笔记图片\img\image-20211010162827279.png)

**居然没有出现filesort！！！这是因为 此时c2在orderby后面已经为常量，所以orderby 后面就不存在乱序了，自然不会出现filesort**



![image-20211010163909598](F:\笔记图片\img\image-20211010163909598.png)



![image-20211010164028098](F:\笔记图片\img\image-20211010164028098.png)



> 结论

- 要分清楚是定值，范围还是排序，一般order by是给个范围
- group by基本上都需要进行排序，会有临时表的产生



**一般性建议：**

- 对于单键索引，尽量选择当前查询过滤性更好的索引
- 在选择组合索引的时候，当前查询中过滤性最好的字段在索引字段顺序中，位置越靠前越好
- 在选择组合索引的时候，尽量选择可以能够包含当前查询中的where子句中更多字段的索引
- 尽可能通过分析统计信息和调整查询的写法来达到选择合适索引的目的

 

> 优化总结口诀

全值匹配我最爱，最左前缀要遵守

带头大哥不能无，中间兄弟不能断

索引列上少计算，范围之后全失效

LIKE百分写最右，覆盖索引不写*

不等空值还有or，索引失效要少用



## 3.查询截取分析

### 3.1 查询优化

> 小表驱动大表

```mysql
select .... from table where exists (subquery)
#该语法可以理解为：将主查询的数据，放到子查询中做条件验证，根据验证结果（true或false）来决定主查询的数据结果是否得以保留
#先主查询然后在子查询
#如果主查询的数据量小于子查询的数据量，用exists的查询效率会高


#exists查询只返回true和false，因此子查询中的 select* 也可以替换为 select 1或其他，官方说法是实际执行时会忽略select清单，因此没有区别
#exists子查询的实际执行过程可能经过了优化而不是我们理解上的优化，如果担忧效率问题，可进行实际检验以确定是否有效率问题
#exists子查询往往也可以用条件表达式，其他子查询或者join来替代
```

```mysql
select ... from table where .. in (select .. from table2);

#如果主查询的数据量大于子查询的量，用in查询效率会更高
```



#### 3.1.1 ORDER BY优化

- order by 字句：尽量使用Index方式排序，避免使用FileSort排序

```mysql
create table tblA(
age int,
birth timestamp not null
);

insert into tblA(age,birth) values(22,now());
insert into tblA(age,birth) values(23,now());
insert into tblA(age,birth) values(24,now());

create index inx_tblA_ageBirth on tblA(age,birth);
```

![image-20211012101030603](F:\笔记图片\img\image-20211012101030603.png)



- ```mysql
  explain select * from tblA where age > 20 order by birth;
  ```

  ![image-20211012101255121](F:\笔记图片\img\image-20211012101255121.png)

- ```mysql
   explain select * from tblA where age > 20 order by birth,age;
   
  ```

  ![image-20211012101400614](F:\笔记图片\img\image-20211012101400614.png)

- ```MySQL
  explain select * from tblA order by age ASC,birth DESC;
  ```

  ![image-20211012104440242](F:\笔记图片\img\image-20211012104440242.png)



>优化

- MySQL支持两种方式的排序，filesort和index。index效率会更高，它指MySQL扫描索引本身完成排序。Filesort方式效率较低
- order by满足两情况会使用索引排序：
  - orderby语句使用索引最左前列
  - 使用where字句与orderby字句条件列组合满足索引最左前列



如果不在索引列上，filesort有两种算法MySQL就要启动双路排序和单路排序

- 双路排序：MySQL4.1之前是使用双路排序的，字面意思就是两次扫描磁盘，最终得到数据读取行指针和orderby列，对他们进行排序，然后扫描已经排序好的列表，按照列表中的值重新从列表中读取对应的数据输出。**要对磁盘进行两次扫描，众所周知，I\O是很耗时的**
- 单路排序：从磁盘读取查询需要的所有列，按照orderby列在buffer对他们进行排序，然后扫描排序后的列表进行输出，它的效率会更快一些，避免了第二次读取数据，并且把随机IO变成了顺序IO，但是它会使用更多的空间，因为它把每一行都保存在内存中了。
  - 但是单路也是有问题的，在sort_buffer中，方法B比方法A要占用很大的空间，因为方法B是把所有字段取出，**所以有可能取出的数据总大小超过了sort_buffer的容量，导致每次只能读取sort_buffer容量大小的数据，进行排序**（**创建临时文件**），排完再取sort_buffer容量大小，再排....从而多次IO

**优化策略：**

- 增大sort_buffer_size的参数设置
- 增大max_length_for_sort_data参数的设置

> 提高order by的速度

1. 不适用select *，这点非常重要，在这里影响的是：

   1. 当查询的字段大小总和小于max_length_for_sort_data而且排序字段不是大对象集时，会用改进后的算法单路排序，否则就会用多路排序
   2. 两种算法的数据都有可能超出sort_buffer的容量，超出之后，会创建临时文件进行合并排序，导致多次IO，但是用单路排序算法风险更大一些，所以要提高sort_buffer_size

2. 尝试提高sort_buffer_size

3. 尝试提高max_length_for_sort_data

   提高这个参数，会增加使用改进算法的概率。但是如果设置的太高，数据总量超出sort_buffer_size的概率就增大，明显症状是高的磁盘IO活动和低的处理器使用率



> 为排序使用索引

- MySQL两种排序方式：文件排序（filesort）和索引排序（index）
- MySQL能为排序和查询使用相同的索引



#### 3.1.2 GROUP BY优化

- group by实质上是先排序，后进行分组，遵照索引最左前缀原则
- 当无法使用索引列时，增大max_length_for_sort_data参数的设置+增大sort_buffer_size
- where高于having，能在在where的限定条件就不要去having限定了



### 3.2 慢查询日志

- MySQL慢查询日志是MySQL提供的一种日志记录，它用来记录在MySQL中响应时间超过阙值的语句，具体指运行时间超过long_query_time值的SQL，则会被记录到慢查询中
- 具体值运行时间超过long_query_time值的SQL，则会被记录到慢查询日志中。long_query_time的默认值为10，意思是运行10秒以上的语句
- **默认情况下，MySQL数据库没有开启慢查询日志，需要我们来手动设置这个参数，如果不是调优需要的话，一般不建议启动该参数**

![image-20211012144343856](F:\笔记图片\img\image-20211012144343856.png)



> 使用

**开启**

```mysql
set global slow_query_log = 1
#使用该语句开启慢日志查询只对当前数据库生效，如果MySQL重启则会失效
```

**设置**

![image-20211012145034460](F:\笔记图片\img\image-20211012145034460.png)

![image-20211012145043169](F:\笔记图片\img\image-20211012145043169.png)

**案例**

先让它睡4秒，此时设置的超过三秒就会被记录到慢查询日志

![image-20211012145608989](F:\笔记图片\img\image-20211012145608989.png)

![image-20211012145719918](F:\笔记图片\img\image-20211012145719918.png)



### 3.3 批量查询数据脚本

```mysql
create table dept(
id int unsigned primary key auto_increment,
deptno mediumint unsigned not null default 0,
dname varchar(20) not null default "",
loc varchar(13) not null default ""
)engine=innodb default charset=GBK;

CREATE TABLE emp(
id int unsigned primary key auto_increment,
empno mediumint unsigned not null default 0,
ename varchar(20) not null default "",
job varchar(9) not null default "",
mgr mediumint unsigned not null default 0,
hiredate date not null,
sal decimal(7,2) not null,
comm decimal(7,2) not null,
deptno mediumint unsigned not null default 0
)ENGINE=INNODB DEFAULT CHARSET=GBK;

#创建函数假如报错，This function has none of DETERMINISTIC.....
#由于开启过慢查询日志，因为我们开启了bin-log,我们就必须为我们的function指定一个参数
show variables like 'log_bin_trust_function_creators';
set global log_bin_trust_function_creators = 1;

//函数
delimiter $$
create function ran_string(n int) returns varchar(255)
begin
declare chars_str varchar(100) default 'abcdefghijklmnopqrstuvwxyzABCDEFGHIJKLMNOPQRSTUVWXYZ';
declare return_str varchar(255) default '';
declare i int default 0;
while i < n do
set return_str = concat(return_str,substring(chars_str,floor(1+rand()*52),1));
set i=i+1;
end while;
return return_str;
end $$
//函数
delimiter $$
create function rand_num() returns int(5)
begin
declare i int default 0;
set i=floor(100+rand()*10);
return i;
end $$
//存储过程
delimiter $$ 
create procedure insert_emp(in start int(10),in max_num int(10))
begin
declare i int default 0;
set autocommit = 0;
repeat
set i = i+1;
insert into emp(empno,ename,job,mgr,hiredate,sal,comm,deptno) values((start+i),ran_string(6),'salesman',0001,curdate(),2000,400,rand_num());
until i=max_num
end repeat;
commit;
end $$
//存储过程
delimiter $$ 
create procedure insert_dept(in start int(10),in max_num int(10))
begin
declare i int default 0;
set autocommit = 0;
repeat
set i = i+1;
insert into dept(deptno,dname,loc) values((start+i),ran_string(10),ran_string(8));
until i=max_num
end repeat;
commit;
end $$

```

### 3.4 show profile进行SQL分析

- show profile是MySQL提供用来可以分析当前会话中语句执行的资源消耗情况。可以用于SQL的调优的测量
- 默认情况下，参数处于关闭状态，并保存最近15次的运行结果



> 使用

**查看当前版本是否支持**

![image-20211012160237548](F:\笔记图片\img\image-20211012160237548.png)

**开启**

![image-20211012160328178](F:\笔记图片\img\image-20211012160328178.png)



**诊断SQL**

![image-20211012161844667](F:\笔记图片\img\image-20211012161844667.png)

```mysql
#诊断SQL，
#当然不止能查cpu和io
show profile cpu,block io for query + 上一步前面的问题SQL数字号码;
```

![image-20211012162302178](F:\笔记图片\img\image-20211012162302178.png)

**如果出现了以下这些，就出现了大问题**

- converting HEAP to MyISAM 查询结果太大，内存都不够用了，开始往磁盘上搬了
- creating tem table 拷贝数据到临时表，用完再删除
  - 创建临时表，拷贝数据到临时表，数据发送后再删除临时表
- copying to tmp table on disk 把内存中临时表复制到磁盘   **危险！！！！**
- locked



## 4.MySQL锁机制

从对数据操作的类型分类：

- 读锁
- 写锁

从对数据操作的粒度分类：

- 表锁
- 行锁



### 4.1 表锁（偏读）

偏向MyISAM引擎，开销小，加锁快；无死锁；锁定粒度大，发生锁冲突的概率最高，并发度最低

```MySQL
create table mylock (
id int not null primary key auto_increment,
name varchar(20) default ''
) engine myisam;

insert into mylock(name) values('a');
insert into mylock(name) values('b');
insert into mylock(name) values('c');
insert into mylock(name) values('d');
insert into mylock(name) values('e');

```

> show open tables:显示表缓存中正在打开的非临时表

![image-20211012195131578](F:\笔记图片\img\image-20211012195131578.png)

> 手动增加表锁

```
lock table 表名字 read(write),表名字2 read(write),其他
```

![image-20211012194751576](F:\笔记图片\img\image-20211012194751576.png)

> 释放锁

```mysql
unlock tables;
```



> 读锁案例

![image-20211012195952992](F:\笔记图片\img\image-20211012195952992.png)

加了读锁后，我们可以看到只能**读取被锁的表，不能更新表，也不能读取其他的表**

如果此时有另一个线程进来修改该表的数据，则该线程会被阻塞，如下图所示：

![image-20211012200445899](F:\笔记图片\img\image-20211012200445899.png)

然后释放锁

![image-20211012200610937](F:\笔记图片\img\image-20211012200610937.png)

另一个线程就不再阻塞，就会修改成功

![image-20211012200733256](F:\笔记图片\img\image-20211012200733256.png)

![image-20211012200649810](F:\笔记图片\img\image-20211012200649810.png)



> 写锁案例

![image-20211012201601759](F:\笔记图片\img\image-20211012201601759.png)

当加了写锁之后，可以读和修改被加锁的表，但不能读取其他的表

此时另一个线程进来读这个表

![image-20211012201723267](F:\笔记图片\img\image-20211012201723267.png)

我们可以看到会被阻塞，然后释放锁，另一个线程才会读取到内容

![image-20211012201935639](F:\笔记图片\img\image-20211012201935639.png)



> 结论

MyAISM在执行查询（select）语句前，会自动给涉及的所有表加读锁；

​			  在执行增删改之前，会自动给涉及的表加写锁

MySQL表级锁有两种模式

- 表共享读锁
- 表独占写锁

| 锁类型 | 是否兼容 | 读锁 | 写锁 |
| ------ | -------- | ---- | ---- |
| 读锁   | 是       | 是   | 否   |
| 写锁   | 是       | 否   | 否   |

结合上表，所有对MyISAM表进行操作，会有以下情况

- 对MyISAM表的读操作（加读锁），不会阻塞其他进程对同一表的读请求，但会阻塞对同一表的写请求。只有读锁释放后，才会执行其他进程的写操作
- 对MyISAM表的写操作（加表锁），会阻塞其他进程对同一表的读和写的请求，只有当写锁释放后，才会执行其他进程的读写操作

**简而言之，就是读锁会阻塞写，但是不会阻塞读。而写锁则会把读和写都阻塞**



> 表锁分析

- 看哪些表被加锁了

  ```
  show open tables
  ```

- 分析表锁定，可以通过检查table_locks_waiteds和table_locks_immediate状态变量来分析系统上的表锁定

  ```mysql 
  show status like 'table%';
  
  #Table_locks_immediate :产生表级锁定的次数，表示可以立获取锁的查询次数，每立即获取锁值加1
  
  #Table_locks_waited：出现表级锁争用而发生等待的次数（不能立即获取锁的次数，每等待一次值加1），此值高说明存在着比较严重的表级锁征用情况  
  ```



**MyISAM的读写锁调度是写优先，这也是MyISAM不适合作为主表的原因。因为写锁后，其他线程不能进行任何操作，大量更新会使查询很难得到锁，从而造成永久阻塞**



### 4.2 行锁（偏写）

- 偏向于InnoDB存储引擎，开销大，加锁慢，会出现死锁；锁定粒度最小，发生锁冲突的概率最低，并发度也最高

**MySQL默认的隔离级别是可重复读，所以一般不会出现脏读和不可重复读，但是可能会出现幻读**

```MySQL
# 建表语句
CREATE TABLE `test_innodb_lock`(
`a` INT,
`b` VARCHAR(16)
)ENGINE=INNODB DEFAULT CHARSET=utf8 COMMENT='测试行锁'; 

# 插入数据
INSERT INTO `test_innodb_lock`(`a`, `b`) VALUES(1, 'b2');
INSERT INTO `test_innodb_lock`(`a`, `b`) VALUES(2, '3');
INSERT INTO `test_innodb_lock`(`a`, `b`) VALUES(3, '4000');
INSERT INTO `test_innodb_lock`(`a`, `b`) VALUES(4, '5000');
INSERT INTO `test_innodb_lock`(`a`, `b`) VALUES(5, '6000');
INSERT INTO `test_innodb_lock`(`a`, `b`) VALUES(6, '7000');
INSERT INTO `test_innodb_lock`(`a`, `b`) VALUES(7, '8000');
INSERT INTO `test_innodb_lock`(`a`, `b`) VALUES(8, '9000');

# 创建索引
CREATE INDEX idx_test_a ON `test_innodb_lock`(a);
CREATE INDEX idx_test_b ON `test_innodb_lock`(b);
```

> 关闭自动提交事务

```MySQL
# 开启MySQL数据库的手动提交
mysql> SET autocommit=0;
```



> 只有一个session进行修改时

session1对数据进行修改，但并未提交事务

![image-20211012210207646](F:\笔记图片\img\image-20211012210207646.png)

session2读取数据：出现了脏读

![image-20211012210249098](F:\笔记图片\img\image-20211012210249098.png)



> 两个session同时修改同一行时

session1对值就行修改

![image-20211012210821144](F:\笔记图片\img\image-20211012210821144.png)

此时session2对该值也进行修改，就会发生阻塞

![image-20211012210957103](F:\笔记图片\img\image-20211012210957103.png)

![image-20211012211353637](F:\笔记图片\img\image-20211012211353637.png)

哦哟，行锁有设置的过期时间



只有解除了阻塞，后面的更新才能够正常



> 两个session同时修改不同行时

如果两个session同时修改不同行时，不会造成阻塞



**索引失效会使行锁变为表锁**

比如varchar 类型不加引号，引起自动类型转换，索引失效



### 4.3 间隙锁的危害

> 什么是间隙锁

当我们用范围条件而不是相等条件检索数据，并请求共享或排他锁时，InnoDB会给符合条件的已有数据记录的索引项加锁；对于键值在条件范围内但并不存在的记录，叫做“间隙”；

InnoDB也会对这个间隙加锁，这种锁机制就是所谓的间隙锁

> 间隙锁的危害

因为`Query`执行过程中通过范围查找的话，他会锁定整个范围内所有的索引键值，即使这个键值不存在。

间隙锁有一个比较致命的缺点，就是**当锁定一个范围的键值后，即使某些不存在的键值也会被无辜的锁定，而造成在锁定的时候无法插入锁定键值范围内的任何数据。**在某些场景下这可能会対性能造成很大的危害。

### 4.4  如何锁定一行

![](F:\笔记图片\img\68747470733a2f2f696d672d626c6f672e6373646e696d672e636e2f323032303038303631363035303335352e706e673f782d6f73732d70726f636573733d696d6167652f77617465726d61726b2c747970655f5a6d46755a33706f5a57356e6147567064476b2c736861646f775f31302c74.png)

`SELECT .....FOR UPDATE`在锁定某一行后，其他写操作会被阻塞，直到锁定的行被`COMMIT`。

mysql InnoDB引擎默认的修改数据语句，update,delete,insert都会自动给涉及到的数据加上排他锁，select语句默认不会加任何锁类型，如果加排他锁可以使用select ...for update语句，加共享锁可以使用select ... lock in share mode语句。所以加过排他锁的数据行在其他事务中是不能修改数据的，也不能通过for update和lock in share mode锁的方式查询数据，但可以直接通过select ...from...查询数据，因为普通查询没有任何锁机制。



### 4.5 行锁总结

InnoDB存储引擎由于实现了行锁，虽然在锁定机制的实现方面所带来的性能损耗比表锁会高一些，但是在整体并发处理能力方面要远远优于MyISAM表级锁定的。当系统的并发量比较高的时候，InnoDB的整体性能和MyISAM相比就会有比较明显的优势

但是,InnoDB的行锁也有其脆弱的一面，当我们使用不当的时候，可能会让InnoDB的整体性能比MyISAM还差





```MySQL
mysql> SHOW STATUS LIKE 'innodb_row_lock%';
+-------------------------------+--------+
| Variable_name                 | Value  |
+-------------------------------+--------+
| Innodb_row_lock_current_waits | 0      |
| Innodb_row_lock_time          | 124150 |
| Innodb_row_lock_time_avg      | 31037  |
| Innodb_row_lock_time_max      | 51004  |
| Innodb_row_lock_waits         | 4      |
+-------------------------------+--------+
5 rows in set (0.00 sec)
```

対各个状态量的说明如下：

- `Innodb_row_lock_current_waits`：当前正在等待锁定的数量。
- `Innodb_row_lock_time`：从系统启动到现在锁定总时间长度（重要）。
- `Innodb_row_lock_time_avg`：每次等待所花的平均时间（重要）。
- `Innodb_row_lock_time_max`：从系统启动到现在等待最长的一次所花的时间。
- `Innodb_row_lock_waits`：系统启动后到现在总共等待的次数（重要）。

尤其是当等待次数很高，而且每次等待时长也不小的时候，我们就需要分析系统中为什么会有如此多的等待，然后根据分析结果着手制定优化策略。



> 优化建议

- 尽可能让所有的检索数据都通过索引来完成，避免无锁升级为表锁
- 合理设计索引，尽量缩小锁的范围
- 尽可能减少检索条件，避免间隙锁
- 尽量控制事无大小，减少锁定资源量和时间长度
- 尽可能低级别事务隔离

## 5.主从复制

![image-20211013103856881](F:\笔记图片\img\image-20211013103856881.png)

MySQL复制过程分为三步：

1. master将改变记录到二进制日志，这些记录过程叫做二进制日志事件
2. slave将master的二进制日志事件拷贝到它的中继日志（relay log）
3. slave重做中继日志中的事件，将改变应用到自己的数据库中。MySQL复制是异步的且串行化的



