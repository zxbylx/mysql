MySQL必知必会

### 1.关键术语

数据库是一种以某种有组织的方式存储的数据集合。

要注意区分数据库和数据库软件，数据库(database)保存有组织的数据的容器。数据库软件应称为DBMS(数据库管理软件)。数据库是通过DBMS创建和操纵的容器。

表(table)是某种特定类型数据的结构化清单。

关键点：存储在表中的数据是一种类型的数据或一个清单。

数据库中的每个表都有一个名字，用来标识自己。唯一的。

表名的唯一性取决于多个因素，如数据库名和表名等的结合，这表示同一个数据库内不能出现相同的表名，但是不同数据库中可以使用相同的表名。

模式(schema):关于数据库和表的布局及特性的信息。

列(column)：表中的一个字段，所有表都是由一个或多个列组成的。每个列都有相应的数据类型。

数据类型(datatype)：所容许的数据的类型。它限制该列存储的数据。数据类型帮助正确的排列数据，并在优化磁盘使用方面起重要作用。

行(row)：表中的数据是按行存储的，每个记录存储在自己的行内。

##### 主键

一列（或一组列），其值能够唯一区分表中的每一行。唯一标识表中每一行的这个列(或这组列)称为主键。

应该总是设计主键，虽然并不是必须的。但是这方便于以后的数据操纵和管理。

满足主键的条件：

- 任意两行都不具有相同的主键值；
- 每个行都必须有一个主键值（主键值不能为null）

主键的好习惯：

- 不更新主键中的值；
- 不重用主键列的值；
- 不在主键列中使用可能会更改的值。

##### SQL

结构化查询语言(Structured Query Language)的缩写。专门用来与数据库通信的语言。

优点：

- SQL不是某个特定数据库供应商的专有语言。几乎所有重要的DBMS都支持SQL。
- SQL简单易学。
- SQL尽管看上去很简单，但实际上是一种很强大的语言，灵活使用语言元素，可以进行非常复杂和高级的数据库操作。

### 2.MySQL

DBMS分为两类：一类为基于共享文件系统的DBMS，另一类是基于客户机-服务器的DBMS。前者（包括诸如Access和FileMaker）用于桌面用途，通常不用于高端或更关键的应用。

MySQL、Oracle以及Microsoft SQL Server等数据库是基于客户机服务器的数据库。客户机-服务器应用分为两个不同的部分。服务器部分是负责所有数据访问和处理的一个软件。这个软件运行在称为数据库服务器的计算机上。

与数据文件打交道的只有服务器软件。客户机是与用户打交道的软件。

### 3.使用MySQL

##### 命令行登录数据库

```shell
mysql -u username -p
然后输入密码

mysql -u username -ppassword  #mysql -u root -p123456

远程连接
mysql -h主机名 -P端口号 -u用户名 -p密码
mysql -hlocalhost -P3306 -uroot -p123456 #前三个中间加不加空格都行
```

##### 选择数据库

```
USE databasename
例如：
USE blog
```

输出：Database changed

##### 查看当前所在数据库

```mysql
mysql> select database();
+------------+
| database() |
+------------+
| mydb       |
+------------+
1 row in set (0.00 sec)
```

##### 查看当前数据库版本

```mysql
mysql> select version();
+-----------+
| version() |
+-----------+
| 8.0.20    |
+-----------+
1 row in set (0.00 sec)
```

##### 修改数据库默认数据保存路径

1.查看当前数据库的data目录

连接到数据库

输入查询目录命令

```mysql
mysql> show variables like '%datadir%';
+---------------+---------------------------------------------+
| Variable_name | Value                                       |
+---------------+---------------------------------------------+
| datadir       | C:\ProgramData\MySQL\MySQL Server 8.0\Data\ |
+---------------+---------------------------------------------+
1 row in set, 1 warning (0.01 sec)
```

2.修改目录

停止mysql服务

方法1：打开开始右侧的搜索输入框，输入“服务”，进入服务页面，找到mysql服务，右击停止。

方法2：在命令行输入

```shell
net stop MYSQL80   #MYSQL80是服务页面mysql的名称，可根据自己电脑进行替换。
```

把上面查询到的Data目录复制到想要存储的新位置

同时在刚才所在目录找到my.ini文件，修改里面的datadir=自定义目录，保存

```
# Path to the database root
#datadir=C:/ProgramData/MySQL/MySQL Server 8.0/Data
datadir=D:/mysql/data
```

3.确认目录修改

启动mysql服务

```shell
net start MYSQL80
```

连接mysql数据库，并输入以下命令确认是否Data目录已修改

```mysql
mysql> show variables like '%datadir%';
+---------------+----------------+
| Variable_name | Value          |
+---------------+----------------+
| datadir       | D:\mysql\data\ |
+---------------+----------------+
1 row in set, 1 warning (0.01 sec)
```



##### 显示数据库

```
mysql> SHOW DATABASES;
+--------------------+
| Database           |
+--------------------+
| blog               |
| django             |
| information_schema |
| mydb               |
| mysql              |
| performance_schema |
| sakila             |
| sys                |
| world              |
+--------------------+
9 rows in set (0.00 sec)

mysql> show databases;
+--------------------+
| Database           |
+--------------------+
| blog               |
| django             |
| information_schema |
| mydb               |
| mysql              |
| performance_schema |
| sakila             |
| sys                |
| world              |
+--------------------+
9 rows in set (0.00 sec)
```

大小写都行，注意后面的分号。

在workbench软件中只有6个，而在命令行显示了九个。多了information_schema、mysql、performance_schema。

发现这三个可以单独拿出来记录下。

##### 显示当前数据库内可用表列表

```
mysql> SHOW TABLES;
```

##### 显示表内的列结构

```
mysql> SHOW COLUMNS FROM sp_role;
+-----------+-------------------+------+-----+---------+----------------+
| Field     | Type              | Null | Key | Default | Extra          |
+-----------+-------------------+------+-----+---------+----------------+
| role_id   | smallint unsigned | NO   | PRI | NULL    | auto_increment |
| role_name | varchar(20)       | NO   |     | NULL    |                |
| ps_ids    | varchar(512)      | NO   |     |         |                |
| ps_ca     | text              | YES  |     | NULL    |                |
| role_desc | text              | YES  |     | NULL    |                |
+-----------+-------------------+------+-----+---------+----------------+
5 rows in set (0.00 sec)

```

SHOW COLUMNS要求给出一个表名。它对每个字段返回一行，行中包含字段名、数据类型、是否允许NULL、键信息、默认值以及其他信息（如字段role_id的auto_increment）。

自增量：某些表写需要唯一值，如上面的role_id。MySQL可以自动地为每个行分配下一个可用编号，不用在添加一行时手动分配唯一值。这个功能就是所谓的自增量。

DESCRIBE语句：MySQL支持DESCRIBE作为SHOW COLUMNS FROM的一种快捷方式，DESCRIBE customers;是SHOW COLUMNS FROM customers;的一种快捷方式。

##### 导入SQL文件

```mysql
-- 登录数据库
mysql -uroot -p123456
-- 创建空数据库
create database employees;
-- 切换数据库
use employees;
-- 导入sql文件
source D:\mysql\练习\test_db-master\employees.sql
```

注意，source在workbench中无法使用。

##### 查看数据库中数据库和表的大小

这些信息都存储在information_schema中所以

```mysql
use information_schema;
-- 查询数据库大小
select concat(round(sum(data_length/1024/1024),2),'MB') as data from tables where table_schema='employees';
-- 查询数据库中表的大小
select concat(round(sum(data_length/1024/1024),2),'MB') as data from tables where table_schema='employees' and table_name='mytest';
```

##### 登录远程数据库

```mysql
mysql -h192.168.0.101 -udavid -p123456
```



##### 其他语句

```
SHOW STATUS;  #用于显示广泛的服务器状态信息
SHOW CREATE DATABASE和SHOW CREATE TABLE，分别用来显示创建特定数据库或表的MySQL语句；
SHOW GRANTS,用来显示授权用户（所有用户或特定用户）的安全权限
mysql> show grants for root@localhost;
SHOW ERRORS和SHOW WARNINGS,用来显示服务器错误或警告信息
HELP SHOW; #显示允许的所有SHOW语句。
```

### 4.检索数据

SELECT查询后面可以跟表中的字段、常量值、表达式和函数。查询的结果是一个虚拟的表格。

##### 检索单列

SELECT用途是从一个或多个表总检索信息。必须至少给出两个信息，想选择什么和从什么地方选择。

```mysql
SELECT prod_name FROM products;
```

返回的是未排序的数据。多条SQL语句必须以(;)隔开。

SQL语句不区分大小写。但是很多开发人员喜欢对所有SQL关键字使用大写，对列和表名使用小写。便于阅读和调试。

使用空格：在处理SQL语句时，其中所有的空格都被忽略。SQL语句可以在一行上给出，也可以分成多行。多数SQL开发人员认为将SQL语句分成多行更容易阅读和测试。

##### 检索多列

```mysql
SELECT prod_id, prod_name, `prod_price`
From products;
```

SQL一般返回原始的、无格式的数据。数据的格式化是一个表示问题，而不是一个检索问题。

**prod_price带有着重号，着重号是数字1键左侧的键，作用是告诉数据库这是一个字段。用于有的字段比如是NAME避免数据库认为这是关键字而导致查询出错，告诉数据库`NAME`是查询字段而不是默认关键字。**

##### 格式化SQL语句

在workbench中使用Ctrl+B会格式化SQL语句，上面的检索语句格式化后变为：

```mysql
SELECT
	prod_id, prod_name, `prod_price`
FROM
	products;
```

对应菜单是Edit>Format>Beautify Query.名称后面显示了快捷键名称。

自定义快捷键可以通过修改MySQLWorkbench安装目录\data\main_menu.xml文件。

不同的软件格式化快捷键不同，可根据不同软件自行搜索。

##### 检索所有列

```mysql
SELECT * FROM products;
```

一般，除非你确实需要表中每一列，否则别使用*通配符。使用通配符优点是可以检索出名字未知的列。

##### 检索不同的行（去重）

```mysql
SELECT DISTINCT vend_id FROM products;
```

DISTINCT关键字应用于所有列，而不仅仅是前置它的列。如果给出SELECT DISTINCT vend_id, prod_price，除非指定的两个列都不同，否则所有行都将被检索出来。

##### 限制结果

比如限制前几行结果而不是所有结果

```mysql
SELECT prod_name FROM products LIMIT 5;
```

为了得到下一个5行，可以指定要检索的开始后和行数

```mysql
SELECT prod_name FROM products LIMIT 5, 5;
```

LIMIT 5, 5指示MySQL从第五回开始的5行。第一个数为开始位置（不包括），第二个数为要检索的行数。

上面的语句替代写法

```mysql
SELECT prod_name FROM products LIMIT 6 OFFSET 5;
```

##### 使用完全限定的表名

```mysql
SELECT products.prod_name FROM products;
SELECT products.prod_name FROM crashcourse.products;
```

避免存在相同的列名和表名不知道具体哪个。

##### 检索常量值

```mysql
mysql> select 100;
+-----+
| 100 |
+-----+
| 100 |
+-----+
1 row in set (0.00 sec)
```

检索常量值不需要指定表，字段名被名义为查询的常量值，结果就是查询的常量。暂时还不知道如何使用。

##### 检索表达式

```mysql
mysql> select 100*98;
+--------+
| 100*98 |
+--------+
|   9800 |
+--------+
1 row in set (0.00 sec)
```

和查询常量值差不多，不需要指定表，字段名被定义为表达式，结果是表达式的结果。暂时不知道如何使用。

##### 检索函数

```mysql
mysql> select VERSION();
+-----------+
| VERSION() |
+-----------+
| 8.0.20    |
+-----------+
1 row in set (0.00 sec)
```

##### 为字段取别名

以查询表达式为例

```mysql
mysql> select 100*98 as 结果;
+------+
| 结果 |
+------+
| 9800 |
+------+
1 row in set (0.00 sec)

mysql> select 100*98 as '结果';
+------+
| 结果 |
+------+
| 9800 |
+------+
1 row in set (0.00 sec)
```

**平时加不加引号都行，如果别名里有特殊符号，比如#或空格等，必须加引号，否则有问题。**

好处

- 便于理解
- 如果要查询的字段有重名的情况，使用别名可以区分开来。

as可以去掉，效果一样

```mysql
mysql> select 100*98 结果;
+------+
| 结果 |
+------+
| 9800 |
+------+
1 row in set (0.00 sec)
```



### 5.排序检索数据

##### 排序数据

子句：SQL语句由子句构成，有些子句是必须的，有些是可选的。排序用ORDER BY子句，取一个或多个列的名字，据此对输出进行排序。

```mysql
SELECT prod_name From products ORDER BY prod_name;
```

order by后面的列通常是前面所选择的列，但是并不是非得这样的，可以用非检索的列。

##### 按多个排序

```mysql
SELECT prod_id, prod_price, prod_name FROM products ORDER BY prod_price, prod_name;
```

只有在多行具有相同的prod_price时才会对prod_name排序，如果对prod_price都不同，则只对prod_price排序。

##### 指定排序方向

数据排序默认升序，也可以降序，指定关键字DESC

```mysql
SELECT prod_id, prod_price, prod_name FROM products ORDER BY prod_price, prod_name DESC;
```

DESC关键字只作用于其前面的列名，上面的这个例子，prod_name是降序排序，但是prod_price还是升序，如果想这个也降序，在其后面将DESC.

使用ORDER BY和LIMIT组合，能够找出一个列中最高或最低的值。

```mysql
SELECT prod_price FROM products ORDER BY prod_price DESC LIMIT 1;
```

ORDER BY子句的位置必须保证它在FROM子句之后，如果使用LIMIT，它必须位于ORDER BY之后。使用子句的次序不对将产生错误消息。

### 6.过滤数据

##### 使用where子句

```mysql
select id, rp1_area, rp1_date from sp_report_1 where rp1_area='其他';
+----+----------+------------+
| id | rp1_area | rp1_date   |
+----+----------+------------+
|  5 | 其他     | 2017-12-27 |
| 11 | 其他     | 2017-12-28 |
| 16 | 其他     | 2017-12-29 |
| 21 | 其他     | 2017-12-30 |
| 26 | 其他     | 2017-12-31 |
| 31 | 其他     | 2018-01-01 |
+----+----------+------------+
6 rows in set (0.01 sec)
```

同时使用ORDER BY和WHERE时，ORDER BY要在WHERE之后，否则报错。

##### where子句操作符

###### 检查单个值

```mysql
select prod_name, prod_price from products
where prod_name = 'fuses';
```

MySQL在执行匹配时默认不区分大小写，所以fuses与Fuses匹配。

```mysql
select id, rp1_area from sp_report_1 where id < 10;
```

###### 不匹配检查

```mysql
select id, rp1_area from sp_report_1 where rp1_area<>'其他';
select id, rp1_area from sp_report_1 where rp1_area!='其他';
```

将值与字符串进行比较时需要单引号，与数字比较则不需要。

###### 范围检查

为了检查某个范围值，可以使用BETWEEN操作符。

```mysql
select id, rp1_area from sp_report_1 where id between 1 and 20;
```

between后的两个值都是包含的。

###### 空值检查

NULL：无值（no value），它与字段包含0，空字符串或仅仅包含空格不同。

select语句有一个特殊的where子句，可用来检查具有null值的列。

```mysql
select rp1_area from sp_report_1 where rp1_date is null;
```

NULL与不匹配：因为null未知具有特殊的含义，数据库不知道它们是否匹配，所以在匹配过滤或不匹配过滤时不返回它们。

### 7.数据过滤

##### 组合where子句

###### and操作符

```mysql
select ENAME, MGR, SAL from emp where MGR=7839 and SAL<=2500;
```

检索出的是同时满足MGR=7839和SAL<=2500的数据。还可以多加条件，每个条件间用and隔开。

###### or操作符

```mysql
select ENAME, MGR, SAL from emp where MGR = 7839 or MGR=7698;
```

###### 计算次序

and的优先级要高于or的优先级，如果想查询MGR=7839或7698，且SAL小于等于2500的数据时，直接按顺序写是有问题的

```mysql
mysql> select ENAME, MGR, SAL from emp where MGR = 7839 or MGR=7698 and SAL<=2500;
+--------+------+------+
| ENAME  | MGR  | SAL  |
+--------+------+------+
| ALLEN  | 7698 | 1600 |
| WARD   | 7698 | 1250 |
| JONES  | 7839 | 2975 |
| MARTIN | 7698 | 1250 |
| BLAKE  | 7839 | 2850 |
| CLARK  | 7839 | 2450 |
| TURNER | 7698 | 1500 |
| JAMES  | 7698 |  950 |
+--------+------+------+
8 rows in set (0.00 sec)
```

由于and优先级要高于or优先级，所以先计算MGR=7698 and SAL<=2500,然后在用or并上前面的MGR=7839.如果想实现原来的先or后and，那用小括号括起来or就可以了。

```mysql
mysql> select ENAME, MGR, SAL from emp where (MGR = 7839 or MGR=7698) and SAL<=2500;
+--------+------+------+
| ENAME  | MGR  | SAL  |
+--------+------+------+
| ALLEN  | 7698 | 1600 |
| WARD   | 7698 | 1250 |
| MARTIN | 7698 | 1250 |
| CLARK  | 7839 | 2450 |
| TURNER | 7698 | 1500 |
| JAMES  | 7698 |  950 |
+--------+------+------+
6 rows in set (0.00 sec)
```

在where子句中，同时具有and和or操作符时，都应该使用小括号明确的分组操作符，不要过分依赖默认计算次序。添加括号没坏处，还能消除歧义。

##### IN操作符

IN操作符用来指定条件范围，范围中每个条件都可以进行匹配。

```mysql
select ENAME, MGR, SAL from emp where MGR in (7839, 7698) order by SAL;
```

IN操作符和or功能相同。

IN操作符优点：

- 在使用长的合法清单时，IN操作符的语法更加清楚且直观
- 在使用IN时，计算的次序更容易管理（因为使用的操作符少）
- IN操作符一般比or操作符清单执行更快
- 最大优点是可以包含其他select语句，使得能够更动态地创建where子句。

##### NOT操作符

只有一个功能，就是否定后面的任何条件

```mysql
select ENAME, MGR, SAL from emp where MGR not in (7839, 7698) order by SAL;
```

mysql中支持使用NOT对IN、BETWEEN和EXISTS子句取反。

### 8.用通配符进行过滤

通配符（wildcard）用来匹配值的一部分的特殊字符。

搜索模式（search pattern）由字面值、通配符或两者组合构成的搜索条件。

##### LIKE操作符

为在搜索子句中使用通配符，必须使用LIKE操作符。LIKE只是MySQL，后跟的搜索模式利用通配符匹配而不是直接相等匹配进行比较。

谓词：操作符何时不是操作符？答案是在它作为谓词（predicate）时，从技术上说，LIKE是谓词而不是操作符。

##### 百分比（%）通配符

%表示任何字符出现任意次数。例如找出所有以S开头名字的员工和工作。%告诉MySQL接收S之后的任意字符，不管它有多少字符。

```mysql
mysql> select EMPNO, ENAME, JOB from emp where ENAME like 'S%';
+-------+-------+---------+
| EMPNO | ENAME | JOB     |
+-------+-------+---------+
|  7369 | SMITH | CLERK   |
|  7788 | SCOTT | ANALYST |
+-------+-------+---------+
2 rows in set (0.00 sec)
```

**根据MySQL的配置方式，搜索时区分大小写的。'S%'与smeb是不匹配的。**

通配符可以在搜索模式中任意位置使用，并且可以使用多个。找出名字中带A的员工和其工作。

```mysql
mysql> select EMPNO, ENAME, JOB from emp where ENAME like '%A%';
+-------+--------+----------+
| EMPNO | ENAME  | JOB      |
+-------+--------+----------+
|  7499 | ALLEN  | SALESMAN |
|  7521 | WARD   | SALESMAN |
|  7654 | MARTIN | SALESMAN |
|  7698 | BLAKE  | MANAGER  |
|  7782 | CLARK  | MANAGER  |
|  7876 | ADAMS  | CLERK    |
|  7900 | JAMES  | CLERK    |
+-------+--------+----------+
7 rows in set (0.00 sec)
```

通配符可以放中间，比如'S%A'，虽然不太有用。%还能匹配0个字符。注释是0个或多个。

注意尾空格：尾部空格可能会干扰通配符匹配。例如在保存词anvil时，如果它后面有一个或多个空格，则子句where prod_name LIKE '%anvil'将匹配不到它们。因为最后的l后有多余的字符。解决这个问题的一个简单办法是在搜索模式最后加一个%。一个更好的办法是使用函数去掉尾部空格。

%通配符不能匹配到NULL。

##### 下划线（_）通配符

下划线与%用途一样，但是只能匹配单个字符，%是0个或1个或多个。

```mysql
mysql> select EMPNO, ENAME, JOB from emp where ENAME like '_LLEN';
+-------+-------+----------+
| EMPNO | ENAME | JOB      |
+-------+-------+----------+
|  7499 | ALLEN | SALESMAN |
+-------+-------+----------+
1 row in set (0.00 sec)
```

##### 使用通配符的技巧

通配符很有用，但是代价是，通配符搜索的处理一般要比前面讨论的其他搜索花的时间都要长。

技巧：

不要过度使用通配符，如果有其他操作符能达到同样目的，应该使用其他操作符。

在确实需要使用通配符时，除非绝对有必要，否则不要把它们用在搜索模式的开始处。因为最慢。

仔细注意通配符的位置，如果放错地方，可能不会反回想要的数据。

### 9.用正则表达式进行搜索

##### 使用mysql正则表达式（REGEXP）

###### 基本字符匹配

下面语句检索ENAME中含文本LLE的所有行

```mysql
mysql> select EMPNO, ENAME, JOB from emp where ENAME regexp 'LLE' order by JOB;
+-------+--------+----------+
| EMPNO | ENAME  | JOB      |
+-------+--------+----------+
|  7934 | MILLER | CLERK    |
|  7499 | ALLEN  | SALESMAN |
+-------+--------+----------+
2 rows in set (0.00 sec)
```

正则表达式.000，'.'是正则表达式中一个特殊的字符，它匹配任意一个字符。因此1000和2000都匹配。

```mysql
SELECT prod_name FROM products WHERE prod_name LIKE '1000' ORDER BY prod_name;
SELECT prod_name FROM products WHERE prod_name REGEXP '1000' ORDER BY prod_name;
```

如果执行这两句，第一个不返回数据，第二个返回一行。是因为LIKE匹配整个列。如果匹配文本在列值中出现，LIKE不会找到它，相应的行也不会返回（除非使用通配符）。而REGEXP在列值内进行匹配，如果被匹配的文本在列值中出现，则会返回。这是非常重要的差别。

REGEXP匹配整个列值的方法：使用^和$定位符(anchor)即可。

**mysql正则表达式匹配不区分大小写。为区分大小写，可以使用BINARY关键字。如WHERE prod_name REGEXP BINARY 'JetPack .000'。通配符匹配时区分大小写的，这也是二者区别。**

###### 进行or匹配，用'|'

```mysql
mysql> select EMPNO, ENAME, JOB from emp where ENAME regexp 'AL|AD';
+-------+-------+----------+
| EMPNO | ENAME | JOB      |
+-------+-------+----------+
|  7499 | ALLEN | SALESMAN |
|  7876 | ADAMS | CLERK    |
+-------+-------+----------+
2 rows in set (0.00 sec)
```

使用|从功能上类似于select语句中的or语句。多个or条件可并入单个正则表达式。

###### 匹配几个字符之一

```mysql
mysql> select ENAME, JOB, DEPTNO from emp where DEPTNO regexp '[12]0';
+--------+-----------+--------+
| ENAME  | JOB       | DEPTNO |
+--------+-----------+--------+
| SMITH  | CLERK     |     20 |
| JONES  | MANAGER   |     20 |
| CLARK  | MANAGER   |     10 |
| SCOTT  | ANALYST   |     20 |
| KING   | PRESIDENT |     10 |
| ADAMS  | CLERK     |     20 |
| FORD   | ANALYST   |     20 |
| MILLER | CLERK     |     10 |
+--------+-----------+--------+
8 rows in set (0.00 sec)
```

这里使用了[12]0，[12]定义了一组字符，意思是匹配1或2，因此10和20都被返回了。

正如所见，[]是另一种形式的OR语句。事实上，正则表达式[12]0为[1|2]0的缩写。也可以使用后者，[]很重要，如果上面的没加括号1|20，会被认为匹配的是1或20，应用于真个字符串，而不是0前面的那一位了。

字符集合可以被否定，在集合开始的位置放一个^即可。

```mysql
mysql> select ENAME, JOB, DEPTNO from emp where DEPTNO regexp '[^12]0';
+--------+----------+--------+
| ENAME  | JOB      | DEPTNO |
+--------+----------+--------+
| ALLEN  | SALESMAN |     30 |
| WARD   | SALESMAN |     30 |
| MARTIN | SALESMAN |     30 |
| BLAKE  | MANAGER  |     30 |
| TURNER | SALESMAN |     30 |
| JAMES  | CLERK    |     30 |
+--------+----------+--------+
6 rows in set (0.00 sec)
```

##### 匹配范围

集合可以用来定义要匹配的一个或多个字符。下面集合匹配0到9.[0123456789],可以用-代替[0-9]。

常见的有[a-z],[a-zA-Z]

还是用上面的例子

```mysql
mysql> select ENAME, JOB, DEPTNO from emp where DEPTNO regexp '[1-3]0';
```

##### 匹配特殊字符

比如想要匹配 . ,[], |, -等，为了匹配特殊字符必须以\\\前导，\\\\-表示查找-。

```mysql
select vend_name from wendors where wend_name regexp '\\.' order by vend_name;
```

\\\也可以用来引用元字符

```
\\f     #换页
\\n     #换行
\\r     #回车
\\t     #制表
\\v     #纵向制表
```

多数正则表达式使用单个反斜杠转义特殊字符，但mysql要求两个，mysql解释一个，正则表达式解释另一个。

##### 匹配字符类

预定义的字符集，称为字符类

```mysql
select * from emp where SAL regexp '[:digit:]';
```

![aUA0mt.png](https://s1.ax1x.com/2020/08/03/aUA0mt.png)

##### 匹配多个实例

目前为止所有的正则表达式都是匹配单次出现字符，正则表达式可以匹配多次出现字符。

```
*                    #0个或多个匹配
+                    #1个或多个匹配(等价于{1,})
?                    #0个或1个匹配(等于{0, 1})
{n}                  #指定数目的匹配
{n,}                 #不少于指定数目的匹配
{n, m}               #匹配数目的范围(m不超过255)
```

```mysql
select prod_name from products where prod_name regexp '\\([0-9] sticks?\\)';
```

返回

```
prod_name
TNT (1 stick)
TNT (5 sticks)
```

sticks?匹配stick和sticks（s后的？使s可选，因为？匹配它前面的任何字符的0次或1次出现）。

下面是匹配工资四位数的员工

```mysql
mysql> select * from emp where SAL regexp '[:digit:]{4}';
+-------+--------+-----------+------+------------+------+------+--------+
| EMPNO | ENAME  | JOB       | MGR  | HIREDATE   | SAL  | COMM | DEPTNO |
+-------+--------+-----------+------+------------+------+------+--------+
|  7499 | ALLEN  | SALESMAN  | 7698 | 1981-02-20 | 1600 |  300 |     30 |
|  7521 | WARD   | SALESMAN  | 7698 | 1981-02-22 | 1250 |  500 |     30 |
|  7566 | JONES  | MANAGER   | 7839 | 1981-04-02 | 2975 | NULL |     20 |
|  7654 | MARTIN | SALESMAN  | 7698 | 1981-09-28 | 1250 | 1400 |     30 |
|  7698 | BLAKE  | MANAGER   | 7839 | 1981-05-01 | 2850 | NULL |     30 |
|  7782 | CLARK  | MANAGER   | 7839 | 1981-06-09 | 2450 | NULL |     10 |
|  7788 | SCOTT  | ANALYST   | 7566 | 1987-04-19 | 3000 | NULL |     20 |
|  7839 | KING   | PRESIDENT | NULL | 1981-11-17 | 5000 | NULL |     10 |
|  7844 | TURNER | SALESMAN  | 7698 | 1981-09-08 | 1500 |    0 |     30 |
|  7876 | ADAMS  | CLERK     | 7788 | 1987-05-23 | 1100 | NULL |     20 |
|  7902 | FORD   | ANALYST   | 7566 | 1981-12-03 | 3000 | NULL |     20 |
|  7934 | MILLER | CLERK     | 7782 | 1982-01-23 | 1300 | NULL |     10 |
+-------+--------+-----------+------+------------+------+------+--------+
12 rows in set (0.00 sec)
```

**但是相同的例子只是改了一个数，想匹配工资为3位数的员工，却没得出想要的结果，不知道为什么。**

```mysql
mysql> select * from emp where SAL regexp '[:digit:]{3}';
+-------+--------+-----------+------+------------+------+------+--------+
| EMPNO | ENAME  | JOB       | MGR  | HIREDATE   | SAL  | COMM | DEPTNO |
+-------+--------+-----------+------+------------+------+------+--------+
|  7369 | SMITH  | CLERK     | 7902 | 1980-12-17 |  800 | NULL |     20 |
|  7499 | ALLEN  | SALESMAN  | 7698 | 1981-02-20 | 1600 |  300 |     30 |
|  7521 | WARD   | SALESMAN  | 7698 | 1981-02-22 | 1250 |  500 |     30 |
|  7566 | JONES  | MANAGER   | 7839 | 1981-04-02 | 2975 | NULL |     20 |
|  7654 | MARTIN | SALESMAN  | 7698 | 1981-09-28 | 1250 | 1400 |     30 |
|  7698 | BLAKE  | MANAGER   | 7839 | 1981-05-01 | 2850 | NULL |     30 |
|  7782 | CLARK  | MANAGER   | 7839 | 1981-06-09 | 2450 | NULL |     10 |
|  7788 | SCOTT  | ANALYST   | 7566 | 1987-04-19 | 3000 | NULL |     20 |
|  7839 | KING   | PRESIDENT | NULL | 1981-11-17 | 5000 | NULL |     10 |
|  7844 | TURNER | SALESMAN  | 7698 | 1981-09-08 | 1500 |    0 |     30 |
|  7876 | ADAMS  | CLERK     | 7788 | 1987-05-23 | 1100 | NULL |     20 |
|  7900 | JAMES  | CLERK     | 7698 | 1981-12-03 |  950 | NULL |     30 |
|  7902 | FORD   | ANALYST   | 7566 | 1981-12-03 | 3000 | NULL |     20 |
|  7934 | MILLER | CLERK     | 7782 | 1982-01-23 | 1300 | NULL |     10 |
+-------+--------+-----------+------+------------+------+------+--------+
14 rows in set (0.00 sec)
```

##### 定位符

目前为止的所有例子都是匹配一个字符串中任意位置的文本，为了匹配特定位置的文本，需要定位符。

```
^                 #文本的开始
$                 #文本的结尾
[[:<:]]           #词的开始
[[:>:]]           #词的结尾
```

以1开头工资的员工信息

```mysql
mysql> select * from emp where SAL regexp '^[1].';
+-------+--------+----------+------+------------+------+------+--------+
| EMPNO | ENAME  | JOB      | MGR  | HIREDATE   | SAL  | COMM | DEPTNO |
+-------+--------+----------+------+------------+------+------+--------+
|  7499 | ALLEN  | SALESMAN | 7698 | 1981-02-20 | 1600 |  300 |     30 |
|  7521 | WARD   | SALESMAN | 7698 | 1981-02-22 | 1250 |  500 |     30 |
|  7654 | MARTIN | SALESMAN | 7698 | 1981-09-28 | 1250 | 1400 |     30 |
|  7844 | TURNER | SALESMAN | 7698 | 1981-09-08 | 1500 |    0 |     30 |
|  7876 | ADAMS  | CLERK    | 7788 | 1987-05-23 | 1100 | NULL |     20 |
|  7934 | MILLER | CLERK    | 7782 | 1982-01-23 | 1300 | NULL |     10 |
+-------+--------+----------+------+------------+------+------+--------+
6 rows in set (0.00 sec)
```

**^双重用途，在集合中([])，用它来否定集合，否则用来指定串的开始处。**

LIKE和REGEXP的不同在于，LIKE匹配整个串，而后者匹配子串，利用定位符，通过^开始每个表达式，用$结束每个表达式，可以使REGEXP的作用域LIKE一样。

正则表达式测试：可以在不使用数据库表的情况下用select来测试正则表达式，REGEXP检查总是返回0（没有匹配）或1（匹配）。

```mysql
mysql> select 'hello' regexp '[o]';
+----------------------+
| 'hello' regexp '[o]' |
+----------------------+
|                    1 |
+----------------------+
1 row in set (0.00 sec)
```

### 10.创建计算字段

场景：数据库中的数据不是应用程序所需要的，需要直接从数据库汇总检索出转换、计算或格式化的数据；而不是检索出数据，然后再在客户机应用程序或报告程序中重新格式化。

字段（field）基本上与列（column）的意思相同，经常互换使用，不过数据库列一般称为列，而术语字段通常用子啊计算字段的连接上。

##### 拼接字段

可以使用Concat()函数来拼接多个列然后以一列呈现

**mysql的不同之处，多数DBMS使用+或||来实现拼接，mysql则使用concat()函数实现。这点要注意**

```mysql
mysql> select concat(ENAME, '(', JOB, ')') from emp order by JOB;
+------------------------------+
| concat(ENAME, '(', JOB, ')') |
+------------------------------+
| SCOTT(ANALYST)               |
| FORD(ANALYST)                |
| SMITH(CLERK)                 |
| ADAMS(CLERK)                 |
| JAMES(CLERK)                 |
| MILLER(CLERK)                |
| JONES(MANAGER)               |
| BLAKE(MANAGER)               |
| CLARK(MANAGER)               |
| KING(PRESIDENT)              |
| ALLEN(SALESMAN)              |
| WARD(SALESMAN)               |
| MARTIN(SALESMAN)             |
| TURNER(SALESMAN)             |
+------------------------------+
14 rows in set (0.00 sec)
```

使用RTrim()函数可以去除字符串右侧空格，还支持LTrim()去掉左边空格和Trim()去掉两边的空格。

```mysql
select concat(rtrim(vend_name), '(', rtrim(vend_contry), ')') from vendors;
```

##### 使用别名

```mysql
mysql> select concat(ENAME, '(', JOB, ')') as `ENAME(JOB)` from emp order by JOB;
+------------------+
| ENAME(JOB)       |
+------------------+
| SCOTT(ANALYST)   |
| FORD(ANALYST)    |
| SMITH(CLERK)     |
| ADAMS(CLERK)     |
| JAMES(CLERK)     |
| MILLER(CLERK)    |
| JONES(MANAGER)   |
| BLAKE(MANAGER)   |
| CLARK(MANAGER)   |
| KING(PRESIDENT)  |
| ALLEN(SALESMAN)  |
| WARD(SALESMAN)   |
| MARTIN(SALESMAN) |
| TURNER(SALESMAN) |
+------------------+
14 rows in set (0.00 sec)
```

##### 执行算数计算

查询员工的名字，工作和工资总数(工资+奖金)

```mysql
mysql> select ENAME, JOB, if(COMM, SAL+COMM, SAL) as salary from emp order by salary;
+--------+-----------+--------+
| ENAME  | JOB       | salary |
+--------+-----------+--------+
| SMITH  | CLERK     |    800 |
| JAMES  | CLERK     |    950 |
| ADAMS  | CLERK     |   1100 |
| MILLER | CLERK     |   1300 |
| TURNER | SALESMAN  |   1500 |
| WARD   | SALESMAN  |   1750 |
| ALLEN  | SALESMAN  |   1900 |
| CLARK  | MANAGER   |   2450 |
| MARTIN | SALESMAN  |   2650 |
| BLAKE  | MANAGER   |   2850 |
| JONES  | MANAGER   |   2975 |
| SCOTT  | ANALYST   |   3000 |
| FORD   | ANALYST   |   3000 |
| KING   | PRESIDENT |   5000 |
+--------+-----------+--------+
14 rows in set (0.00 sec)
```

由于有些员工没有奖金，所以需要添加一个if语句判断if(exp1, exp2, exp3),如果exp1是真的，则返回exp2，如果是假的则发挥exp3.

```mysql
mysql> select ENAME, JOB, SAL, COMM, if(COMM, SAL+COMM, SAL) as salary from emp order by salary;
+--------+-----------+------+------+--------+
| ENAME  | JOB       | SAL  | COMM | salary |
+--------+-----------+------+------+--------+
| SMITH  | CLERK     |  800 | NULL |    800 |
| JAMES  | CLERK     |  950 | NULL |    950 |
| ADAMS  | CLERK     | 1100 | NULL |   1100 |
| MILLER | CLERK     | 1300 | NULL |   1300 |
| TURNER | SALESMAN  | 1500 |    0 |   1500 |
| WARD   | SALESMAN  | 1250 |  500 |   1750 |
| ALLEN  | SALESMAN  | 1600 |  300 |   1900 |
| CLARK  | MANAGER   | 2450 | NULL |   2450 |
| MARTIN | SALESMAN  | 1250 | 1400 |   2650 |
| BLAKE  | MANAGER   | 2850 | NULL |   2850 |
| JONES  | MANAGER   | 2975 | NULL |   2975 |
| SCOTT  | ANALYST   | 3000 | NULL |   3000 |
| FORD   | ANALYST   | 3000 | NULL |   3000 |
| KING   | PRESIDENT | 5000 | NULL |   5000 |
+--------+-----------+------+------+--------+
14 rows in set (0.00 sec)
```

测试小办法：select可以省略from子句以简单的访问和处理表达式。例如select 3*2；返回6.

```mysql
mysql> select now();
+---------------------+
| now()               |
+---------------------+
| 2020-08-03 12:12:28 |
+---------------------+
1 row in set (0.00 sec)
```

### 11.使用数据处理函数

函数没有SQL的可移植性强 。几乎每种主要的DBMS的实现都支持其他实现不支持的函数，而且有时候差异还很大。许多SQL程序员不赞成使用特殊实现的功能。虽然这样做有很多好处，但不总是利于应用程序的性能。

如果你决定使用函数，应该保证做好代码注释，以便以后你（或其他人）能确切的知道所编写的SQL代码的含义。

##### 使用函数

大多数SQL实现支持下列类型的函数：

- 用于处理文本串（如删除或填充值，转换值为大写或小写）的文本函数；
- 用于在数值数据上进行算数操作（如返回绝对值，进行代数运算）的数值函数；
- 用于处理日期和时间值并从这些值中提取特定成分（例如，返回两个日期之差，检查日期有效性等）的日期和时间函数。
- 返回DBMS正使用的特殊信息（如返回用户登录信息，检查版本细节）的系统函数。

###### 文本处理函数

```mysql
mysql> select ENAME, lower(ENAME) as lower_ename from emp order by ENAME limit 5;
+-------+-------------+
| ENAME | lower_ename |
+-------+-------------+
| ADAMS | adams       |
| ALLEN | allen       |
| BLAKE | blake       |
| CLARK | clark       |
| FORD  | ford        |
+-------+-------------+
5 rows in set (0.01 sec)
```

常用的文本处理函数

```
Left()               #返回串左边的字符
Length()             #返回串的长度
Locate()             #找出串的一个子串
Lower()              #将串转换为小写
LTrim()              #去掉串左边的空格
Right()              #返回串右边的字符
RTrim()              #去掉串右边的空格
Soundex()            #返回串的SOUNDEX值
SubString()          #返回子串的字符
Upper()              #将串转换为大写
```

SOUNDEX是一个将任何文本串转换为描述其语音表示的字母数字模式的算法。SOUNDEX考虑了类似的发音字符和音节，使得能对串进行发音比较而不是字母比较。

下面给出一个使用Soundex()函数的例子。customers表中有一个顾客Coyote Inc.，其联系名为Y.Lee。但如果这是输入错误，此联系名实际应该是Y.Lie，怎么办，显然按正确的联系名搜索不会返回数据，如下所示

```mysql
mysql> select cust_name, cust_contact from customers where cust_contact = 'Y. Lie';
Empty set (0.00 sec)
```

现在试一下使用Soundex()函数进行搜索，它匹配所有发音类似于Y.Lie的联系名：

```mysql
mysql> select cust_name, cust_contact from customers where soundex(cust_contact) = soundex('Y Lie');
+-------------+--------------+
| cust_name   | cust_contact |
+-------------+--------------+
| Coyote Inc. | Y Lee        |
+-------------+--------------+
1 row in set (0.00 sec)
```

在这个例子中，where子句使用soundex()函数来转换cust_contact列值和搜索串为它们的SOUNDEX值。因为Y.Lee和Y.Lie发音相似，所以它们的SOUNDEX值匹配，因此where子句正确地过滤出了所需的数据。

###### 日期和时间处理函数

常用的日期和时间处理函数

```
AddDate()                  #增加一个日期（天、周等）
AddTime()                  #增加一个时间（时、分等）
CurDate()                  #返回当前日期
CurTime()                  #返回当前时间
Date()                     #返回日期时间的日期部分
DateDiff()                 #计算两个日期之差
Date_Add()                 #高度灵活的日期运算函数
Date_Format()              #返回一个格式化的日期或时间串
Day()                      #返回一个日期的天数部分
DayOfWeek()                #对于一个日期，返回对应的星期几
Hour()                     #返回一个时间的小时部分
Minute()                   #返回一个时间的分钟部分
Mouth()                    #返回一个日期的月份部分
Now()                      #返回当前日期和时间
Second()                   #返回一个时间的秒部分
Time()                     #返回一个日期事件的时间部分
Year()                     #返回一个日期的年份部分
```

无论什么时候指定一个日期，不管是插入或更新表值还是用where子句进行过滤，日期必须为yyyy-mm-dd格式。虽然其他的日期格式也行，但这是首选格式，因为它排除了多义性（如04/05/06是2006年5月4日或2006年4月5日或2004年5月6日或....)

应该总是使用4位数字的年份。

```mysql
mysql> select cust_id, order_num from orders where order_date = '2005-09-01';
+---------+-----------+
| cust_id | order_num |
+---------+-----------+
|   10001 |     20005 |
+---------+-----------+
1 row in set (0.00 sec)
```

如果日期后的时间不是00:00:00，上面的检索就没有结果，所以如果想匹配日期，就用日期函数Date()只匹配日期。

```mysql
select cust_id, order_num from orders where date(order_date) = '2005-09-01';
```

如果你想检索出2005年9月的所有订单，怎么办？简单的相当测试不行，有几种解决办法，其一：

```mysql
mysql> select cust_id, order_num from orders where date(order_date) between '2005-09-01' and '2005-09-30';
+---------+-----------+
| cust_id | order_num |
+---------+-----------+
|   10001 |     20005 |
|   10003 |     20006 |
|   10004 |     20007 |
+---------+-----------+
3 rows in set (0.00 sec)
```

还有另一种办法（一种不需要记住每个月有多少天或不需要操心闰年2月的办法）：

```mysql
mysql> select cust_id, order_num from orders where year(order_date) = 2005 and month(order_date) = 9;
+---------+-----------+
| cust_id | order_num |
+---------+-----------+
|   10001 |     20005 |
|   10003 |     20006 |
|   10004 |     20007 |
+---------+-----------+
3 rows in set (0.00 sec)
```

###### 数值处理函数

数值处理函数仅处理数值数据。这些函数一般主要用于代数、三角或几何运算，因此没有串货日期时间函数的使用那么频繁。

具有讽刺意味的是，在主要的DBMS函数汇总，数值函数是最一致最统一的函数。

常用数值处理函数

```
Abs()                  #返回一个数的绝对值
Cos()                  #返回一个角度的余弦
Exp()                  #返回一个数的指数值
Mod()                  #返回除操作的余数
Pi()                   #返回圆周率
Rand()                 #返回一个随机数
Sin()                  #返回一个角度的正弦
Sqrt()                 #返回一个数的平方根
Tan()                  #返回一个角度的正切
```

### 12. 汇总数据

##### 聚集函数

有时候不需要实际表数据，只是想要汇总信息，返回表数据就是一种对时间和处理资源的浪费，这时候就需要聚集函数了。

聚集函数（aggregate function）运行在行组上，计算和返回单个值的函数。

```
AVG()                #返回某列的平均值
COUNT()              #返回某列的行数
MAX()                #返回某列的最大值
MIN()                #返回某列的最小值
SUM()                #返回某列之和
```

###### AVG()函数

```mysql
mysql> select avg(prod_price) as avg_price from products;
+-----------+
| avg_price |
+-----------+
| 16.133571 |
+-----------+
1 row in set (0.00 sec)
```

AVG()可以用来确定特定列或行的平均值

```mysql
mysql> select avg(prod_price) as avg_price from products where vend_id = 1003;
+-----------+
| avg_price |
+-----------+
| 13.212857 |
+-----------+
1 row in set (0.00 sec)
```

AVG()只可作用域单列，且列名必须以函数参数给出。为了获得多个列的平均值需要使用多个avg()函数。

AVG()函数忽略列值为NULL的行。

###### COUNT()函数

两种使用方式：

- 使用COUNT(*)对表中的行的数目进行计数，不管表列中包含的是空值（NULL）还是非空值。
- 使用COUNT(column)对特定列中具有值的行进行计数，忽略NULL值。

```mysql
mysql> select count(*) as num_cust from customers;
+----------+
| num_cust |
+----------+
|        5 |
+----------+
1 row in set (0.00 sec)
```

```mysql
mysql> select count(cust_email) as num_cust from customers;
+----------+
| num_cust |
+----------+
|        3 |
+----------+
1 row in set (0.00 sec)
```

###### MAX()函数

```mysql
mysql> select max(prod_price) as max_price from products;
+-----------+
| max_price |
+-----------+
|     55.00 |
+-----------+
1 row in set (0.00 sec)
```

对非数值数据使用max():MySQL允许将它用来返回任意列中的最大值，包括文本列中的最大值，在用于文本数据时，如果数据按相应的列排序，则max()返回最后一行。

max()函数忽略列值为NULL的行。

###### MIN()函数

```mysql
mysql> select min(prod_price) as min_price from products;
+-----------+
| min_price |
+-----------+
|      2.50 |
+-----------+
1 row in set (0.00 sec)
```

min()和max()作用相反，但都可以作用域任何列，都忽略NULL。

###### SUM()函数

```mysql
mysql> select sum(quantity) as items_ordered from orderitems where order_num = 20005;
+---------------+
| items_ordered |
+---------------+
|            19 |
+---------------+
1 row in set (0.00 sec)
```

sum()也可以用来合计计算值

```mysql
mysql> select sum(item_price*quantity) as total_price from orderitems where order_num = 20005;
+-------------+
| total_price |
+-------------+
|      149.87 |
+-------------+
1 row in set (0.00 sec)
```

sum()忽略NULL。

##### 聚集不同值

以上5个聚集函数都可以如下使用：

- 对所有的行执行计算，指定ALL参数或不给参数（因为ALL是默认的）
- 值包含不同的值，指定DISTINCT参数

```mysql
mysql> select avg(distinct prod_price) as avg_price from products where vend_id = 1003;
+-----------+
| avg_price |
+-----------+
| 15.998000 |
+-----------+
1 row in set (0.00 sec)
```

**如果指定列名，则DISTINCT只能用于COUNT()，DISTINCT不能用于COUNT(*)。DISTINCT必须使用列名，不能用于计算或表达式。**

##### 组合聚集函数

```mysql
SELECT 
    COUNT(*) AS num_items,
    MIN(prod_price) AS price_min,
    MAX(prod_price) AS price_max,
    AVG(prod_price) AS price_avg
FROM
    products;
```

取别名：在指定别名以包含某个聚集函数的结果时，不应该使用表中实际的列名。便于理解。

### 13.分组数据

##### 数据分组

分组数据允许把数据分为多个逻辑组，以便能对每个组进行聚集计算。

##### 创建分组

使用group by分组。

```mysql
mysql> select vend_id, count(*) as num_prods from products group by vend_id;
+---------+-----------+
| vend_id | num_prods |
+---------+-----------+
|    1001 |         3 |
|    1002 |         2 |
|    1003 |         7 |
|    1005 |         2 |
+---------+-----------+
4 rows in set (0.00 sec)
```

group by以vend_id排序并分组数据。这导致对每个vend_id计算了一次产品数目。结果就是统计了每个供应商提供的产品数目。

重要规定：

- group by子句可以包含任意数目的列。这使得能对分组进行嵌套，为数据分组提供更细致的控制。
- 如果在group by子句中嵌套了分组，数据将在最后规定的分组上进行汇总。换句话说，在建立分组时，指定的所有列都一起计算（所以不能从个别的列取回数据）。
- group by子句中列出的每个列都不显示建所列或有效的表达式（不能是聚集函数），如果在select中使用表达式，则必须在group by子句中指定相同的表达式，不能使用别名。
- 除了聚集计算语句外，select语句中的每个列都必须在group by子句中给出。
- 如果分组列中具有NULL值，则NULL将作为一个分组返回。如果列中有多行NULL值，他们将分为一组。
- group by子句必须出现在where子句之后，order by子句之前。



使用rollup关键字，可以得到每个分组及每个分组汇总级别（针对每个分组）的值。

```mysql
mysql> select vend_id, count(*) as num_prods from products group by vend_id with rollup;
+---------+-----------+
| vend_id | num_prods |
+---------+-----------+
|    1001 |         3 |
|    1002 |         2 |
|    1003 |         7 |
|    1005 |         2 |
|    NULL |        14 |
+---------+-----------+
5 rows in set (0.00 sec)
```

##### 过滤分组

HAVING子句，非常类似于where。事实上，目前为止所学过的所有类型的where子句都可以用having替代。唯一的差别是where过滤行，having过滤分组。

```mysql
mysql> select cust_id, count(*) as orders from orders group by cust_id having count(*) >= 2;
+---------+--------+
| cust_id | orders |
+---------+--------+
|   10001 |      2 |
+---------+--------+
1 row in set (0.00 sec)
```

having和where的差别：where在分组前进行过滤，having在分组后进行过滤。where排除的行不包括在分组中，这可能会改变计算值，从而影响having子句中基于这些值过滤掉的分组。

同时具有where和having的例子

```mysql
mysql> select vend_id, count(*) as num_prods from products where prod_price >= 10 group by vend_id having count(*) >=2;
+---------+-----------+
| vend_id | num_prods |
+---------+-----------+
|    1003 |         4 |
|    1005 |         2 |
+---------+-----------+
2 rows in set (0.00 sec)
```

##### 分组和排序

![awIRPK.png](https://s1.ax1x.com/2020/08/04/awIRPK.png)

我们工厂发现用group by分组的数据确实以分组顺序输出的。但情况并不总是这样，它并不是SQL规范所要求的。此外，用户也可能会要求以不同于分组的顺序排序。

一般在使用group by子句时，应该也给出order by子句，这是保证数据正确排序的唯一方法，千万不要仅依赖group by排序数据。

检索总订单价格大于等于50的订单的订单号和总计订单价格。

```mysql
mysql> select order_num, sum(quantity*item_price) as ordertotal from orderitems group by order_num having sum(quantity*item_price) >=50;
+-----------+------------+
| order_num | ordertotal |
+-----------+------------+
|     20005 |     149.87 |
|     20006 |      55.00 |
|     20007 |    1000.00 |
|     20008 |     125.00 |
+-----------+------------+
4 rows in set (0.00 sec)
```

为按总计订单价格排序输出，需要添加order by子句。

```mysql
mysql> select order_num, sum(quantity*item_price) as ordertotal from orderitems group by order_num having sum(quantity*item_price) >=50 order by ordertotal;
+-----------+------------+
| order_num | ordertotal |
+-----------+------------+
|     20006 |      55.00 |
|     20008 |     125.00 |
|     20005 |     149.87 |
|     20007 |    1000.00 |
+-----------+------------+
4 rows in set (0.00 sec)
```

##### SELECT子句顺序

[![aw7igU.png](https://s1.ax1x.com/2020/08/04/aw7igU.png)](https://imgchr.com/i/aw7igU)
[![aw7P3T.png](https://s1.ax1x.com/2020/08/04/aw7P3T.png)](https://imgchr.com/i/aw7P3T)



### 14.使用子查询

子查询就是多个查询语句的嵌套，里面的结果作为外面语句的筛选条件。

前提是两个搜索语句使用的表有共同的列。

假设需要列出订购物品TNT2的所有客户：

1.检索包含物品TNT2的所有订单的编号

2.检索具有前一步列出的订单编号的所有客户的ID

3.检索前一步骤返回的所有客户ID的客户信息

```mysql
mysql> select order_num from orderitems where prod_id = 'TNT2';
+-----------+
| order_num |
+-----------+
|     20005 |
|     20007 |
+-----------+
2 rows in set (0.00 sec)
```

```mysql
mysql> select cust_id from orders where order_num in (20005, 20007);
+---------+
| cust_id |
+---------+
|   10001 |
|   10004 |
+---------+
2 rows in set (0.00 sec)
```

```mysql
mysql> select cust_name, cust_contact from customers where cust_id in (10001, 10004);
+----------------+--------------+
| cust_name      | cust_contact |
+----------------+--------------+
| Coyote Inc.    | Y Lee        |
| Yosemite Place | Y Sam        |
+----------------+--------------+
2 rows in set (0.00 sec)
```

上面三个可以用子查询来替代

```mysql
SELECT 
    cust_name, cust_contact
FROM
    customers
WHERE
    cust_id IN (SELECT 
            cust_id
        FROM
            orders
        WHERE
            order_num IN (SELECT 
                    order_num
                FROM
                    orderitems
                WHERE
                    prod_id = 'TNT2'));
```

在where子句中使用子查询，应该保证select语句具有与where子句中相同数目的列。通常子查询将返回单个列并且与单个列匹配，但如果需要也可以使用多个列。

虽然子查询一般与in操作符结合使用，但也可以用于测试等于(=)、不等于(<>)等。

##### 作为计算字段使用子查询

显示customers表中每个客户的订单总数：

(1) 从customers中检索出客户列表

(2) 对于检索出的每个客户，统计其在orders表中的订单数目。

拿到大问题进行简化，先对客户10001的订单进行计数

```mysql
mysql> select count(*) as orders from orders where cust_id = 10001;
+--------+
| orders |
+--------+
|      2 |
+--------+
1 row in set (0.00 sec)
```

为了对每个客户执行count(*)，应该将count(\*)作为一个子查询

```mysql
SELECT 
    cust_name,
    cust_state,
    (SELECT 
            COUNT(*)
        FROM
            orders
        WHERE
            orders.cust_id = customers.cust_id) AS orders
FROM
    customers
ORDER BY cust_name; 

+----------------+------------+--------+
| cust_name      | cust_state | orders |
+----------------+------------+--------+
| Coyote Inc.    | MI         |      2 |
| E Fudd         | IL         |      1 |
| Mouse House    | OH         |      0 |
| Wascals        | IN         |      1 |
| Yosemite Place | AZ         |      1 |
+----------------+------------+--------+
5 rows in set (0.00 sec)
```

这里的where子句使用了完全限定列名(orders.cust_id=customers.cust_id)，这种类型的子查询称为相关子查询，任何时候只要列名可能有多义性，就必须使用这种语法。

使用子查询需要逐渐增加子查询来建立查询：也就是说先测试内部查询，确定没问题后再进行嵌套，嵌套一层没问题，再进行第二次嵌套测试。这样可保证子查询没有问题。

### 15.联结表

##### 联结

###### 关系表

关系表的设计就是要保证把信息分解成多个表，一类数据一个表。各表通过某些常用的值（即关系设计中的关系）互相关联。

外键：外键为某个表中的一列，它包含了另一个表的主键值，定义了两个表之间的关系。

好处：

- 信息不重复，从而不浪费时间和空间；

- 如果主键所在表信息变动，只更新表中的单个记录，外键所在表中的数据不用改动；

- 由于数据无重复，显然数据是一致的，这使得处理数据更简单。

关系数据可以有效的存储和方便地处理。因此，关系数据库的可伸缩性远比非关系数据库要好。

可伸缩性（scale）能够适应不断增加的工作量而不失败。设计良好的数据库或应用程序称之为可伸缩性好。

##### 创建联结

```mysql
mysql> select vend_name, prod_name, prod_price from vendors, products where vendors.vend_id = products.vend_id order by vend_name, prod_name;
+-------------+----------------+------------+
| vend_name   | prod_name      | prod_price |
+-------------+----------------+------------+
| ACME        | Bird seed      |      10.00 |
| ACME        | Carrots        |       2.50 |
| ACME        | Detonator      |      13.00 |
| ACME        | Safe           |      50.00 |
| ACME        | Sling          |       4.49 |
| ACME        | TNT (1 stick)  |       2.50 |
| ACME        | TNT (5 sticks) |      10.00 |
| Anvils R Us | .5 ton anvil   |       5.99 |
| Anvils R Us | 1 ton anvil    |       9.99 |
| Anvils R Us | 2 ton anvil    |      14.99 |
| Jet Set     | JetPack 1000   |      35.00 |
| Jet Set     | JetPack 2000   |      55.00 |
| LT Supplies | Fuses          |       3.42 |
| LT Supplies | Oil can        |       8.99 |
+-------------+----------------+------------+
14 rows in set (0.00 sec)
```

注意：在引用的列可能出现歧义时，必须使用完全限定列名（用一个点分隔的表名和列名）。

笛卡尔积：由没有联结条件的表关系返回的结果为笛卡尔积。检索出的行的数目将是第一个表中的行数乘以第二个表中的行数。

```mysql
select vend_name, prod_name, prod_price from vendors, products order by vend_name, prod_name;
```

**应该保证所有的联结都有where子句，否则mysql将返回比想要的数据多得多的数据。**

###### 内部联结

目前为止所用的联结称为等值联结（equijoin），它基于两个表之间的相等测试。这种联结也称为内部联结。

```mysql
select vend_name, prod_name, prod_price from vendors inner join products on vendors.vend_id = products.vend_id;
```

与第一次的内部联结相同。不过where子句换为on子句。好处是明确联结语法能够确保不会忘记联结条件，有时候这样做也能影响性能。

###### 联结多个表

```mysql
SELECT 
    prod_name, vend_name, prod_price quantity
FROM
    orderitems,
    products,
    vendors
WHERE
    products.vend_id = vendors.vend_id
        AND orderitems.prod_id = products.prod_id
        AND order_num = 20005;
        
+----------------+-------------+------------+----------+
| prod_name      | vend_name   | prod_price | quantity |
+----------------+-------------+------------+----------+
| .5 ton anvil   | Anvils R Us |       5.99 |       10 |
| 1 ton anvil    | Anvils R Us |       9.99 |        3 |
| TNT (5 sticks) | ACME        |      10.00 |        5 |
| Bird seed      | ACME        |      10.00 |        1 |
+----------------+-------------+------------+----------+
4 rows in set (0.00 sec)
```

mysql运行关联指定的每个表以处理联结。这种处理可能非常耗费资源，不要联结不必要的表。

用联结表替换14章子查询的例子

```mysql
SELECT 
    cust_name, cust_contact
FROM
    customers,
    orders,
    orderitems
WHERE
    customers.cust_id = orders.cust_id
        AND orderitems.order_num = orders.order_num
        AND prod_id = 'TNT2';
+----------------+--------------+
| cust_name      | cust_contact |
+----------------+--------------+
| Coyote Inc.    | Y Lee        |
| Yosemite Place | Y Sam        |
+----------------+--------------+
2 rows in set (0.00 sec)
```

### 16.创建高级联结

##### 使用表别名

理由：

- 缩短SQL语句；

- 允许在单挑select语句中多次使用相同的表；

```mysql
SELECT 
    cust_name, cust_contact
FROM
    customers AS c,
    orders AS o,
    orderitems AS oi
WHERE
    c.cust_id = o.cust_id
        AND oi.order_num = o.order_num
        AND prod_id = 'TNT2';
```

表别名只在查询执行中使用。与列别名不一样，表别名不返回到客户机。

##### 使用不同的类联结

###### 自联结

假如你发现某物品（其ID为DTNTR）存在问题，因此想知道生产该物品的供应商生产的其他物品是否也存在这些问题。因此查询要求首先找到生产ID为DTNTR的物品的供应商，然后找出这个供应商生产的其他物品。

方法一：子查询

```mysql
SELECT 
    prod_id, prod_name
FROM
    products
WHERE
    vend_id = (SELECT 
            vend_id
        FROM
            products
        WHERE
            prod_id = 'DTNTR');
```

方法二：自联结

```mysql
SELECT 
    p1.prod_id, p1.prod_name
FROM
    products AS p1,
    products AS p2
WHERE
    p1.vend_id = p2.vend_id
        AND p2.prod_id = 'DTNTR';
```

这个例子中，表products引用了两次，虽然完全合法，但对products的引用具有二义性，因此MySQL不知道引用的是products表中的哪个实例。为解决此问题，使用了表别名。

上面两种方法结果相同，但是有时候处理联结比处理子查询快很多。应该试一下两种方法，以确定哪一种的性能更好。

###### 自然联结

无论何时对表进行联结，应该至少有一个列出现在不止一个表中（被联结的列）。标准的联结（前面说的内部联结）返回所有数据，甚至相同的列多次出现，自然联结排除多次出现，使每个列只返回一次。

系统不处理重复列，只能手动处理。

```mysql
SELECT 
    c.*,
    o.order_num,
    o.order_date,
    oi.prod_id,
    oi.quantity,
    oi.item_price
FROM
    customers AS c,
    orders AS o,
    orderitems AS oi
WHERE
    c.cust_id = o.cust_id
        AND oi.order_num = o.order_num
        AND prod_id = 'FB';
```

通配符只对第一个表使用，所有其他列明确列出，所以没有重复的列被检索出来。

###### 外部联结

联结包含了那些在相关表中没有关联行的行，这种类型的联结称为外部联结。

举例，对每个客户下路多少订单进行计数，包括那些至今尚未下订单的客户；

先用内部联结找出所有下单客户的订单数：

```mysql
mysql> select customers.cust_id, orders.order_num from customers inner join orders on customers.cust_id = orders.cust_id;
+---------+-----------+
| cust_id | order_num |
+---------+-----------+
|   10001 |     20005 |
|   10001 |     20009 |
|   10003 |     20006 |
|   10004 |     20007 |
|   10005 |     20008 |
+---------+-----------+
5 rows in set (0.00 sec)
```

然后用左外部联结对比：

```mysql
mysql> select customers.cust_id, orders.order_num from customers left outer join orders on customers.cust_id = orders.cust_id;
+---------+-----------+
| cust_id | order_num |
+---------+-----------+
|   10001 |     20005 |
|   10001 |     20009 |
|   10002 |      NULL |
|   10003 |     20006 |
|   10004 |     20007 |
|   10005 |     20008 |
+---------+-----------+
6 rows in set (0.00 sec)
```

可以发现二者语句上差不多，唯一的区别是两个表之间的内容。内部联结是inner join，左外部联结是left outer join。得到的结果是10002客户没有下单，在外部联结时被筛选出来了。

左外部联结时选择左边表的所有行，然后在右边表中找出所有匹配的行。右外部联结时选择右边表的所有行，找左边表的匹配行。左右外部联结可以通过换表的顺序进行替换。

```mysql
-- 右外部联结
select customers.cust_id, orders.order_num from orders right outer join customers on customers.cust_id = orders.cust_id;
```

这个右外部联结和上面的左外部联结结果一样。

注意：mysql不支持简化字符`*=`或`=*`的使用。

##### 使用带聚集函数的联结

检索所有客户及每个客户所下的订单数

使用count函数

```mysql
SELECT 
    customers.cust_name,
    customers.cust_id,
    COUNT(orders.order_num) AS num_ord
FROM
    customers
        INNER JOIN
    orders ON customers.cust_id = orders.cust_id
GROUP BY customers.cust_id;

+----------------+---------+---------+
| cust_name      | cust_id | num_ord |
+----------------+---------+---------+
| Coyote Inc.    |   10001 |       2 |
| Wascals        |   10003 |       1 |
| Yosemite Place |   10004 |       1 |
| E Fudd         |   10005 |       1 |
+----------------+---------+---------+
4 rows in set (0.00 sec)
```

聚集函数和外部联结一起使用

```mysql
SELECT 
    customers.cust_name,
    customers.cust_id,
    COUNT(orders.order_num) AS num_ord
FROM
    customers
        LEFT OUTER JOIN
    orders ON customers.cust_id = orders.cust_id
GROUP BY customers.cust_id;

+----------------+---------+---------+
| cust_name      | cust_id | num_ord |
+----------------+---------+---------+
| Coyote Inc.    |   10001 |       2 |
| Mouse House    |   10002 |       0 |
| Wascals        |   10003 |       1 |
| Yosemite Place |   10004 |       1 |
| E Fudd         |   10005 |       1 |
+----------------+---------+---------+
5 rows in set (0.00 sec)
```

这里显示了没有下单的客户。

##### 使用联结和联结条件

- 注意所使用的联结类型。一般使用内部联结，但使用外部联结也有效；

- 保证使用正确的联结条件，否则结果不对；

- 应该总是提供联结条件，否则会得出笛卡尔积

- 在一个联结中可以包含多个表，甚至对每个联结可以采用不同的联结类型。虽然这样做是合法的，一般也很有用，但应该在一起此时它们前，分别测试每个联结。这样使得故障排除更为简单。

### 17.组合查询

##### 组合查询

mysql允许执行多个查询（多条select语句），并将结果作为单个查询结果集返回。这些组合查询称为并（union）或复合查询。

有两种基本情况需要使用组合查询：

- 在单个查询中从不同的表返回类似结构的数据。
- 对哪个表执行多个查询，按单个查询返回数据。

组合查询和多个where条件：多数情况下二者完成的工作相同。也就是说，任何具有多个where子句的select语句都可以作为一个组合查询给出。两种技术在不同查询中性能不同，实际中试一下两种技术，以确定对特定的查询哪一种性能更好。

##### 创建组合查询

###### 使用union

假如需要价格小于等于5的所有物品的列表，而且还想包括供应商1001,1002生产的所有物品，不考虑价格。

多个where语句

```mysql
select vend_id, prod_id, prod_price from products where prod_price <= 5 or vend_id in (1001, 1002);
```

组合查询

```mysql
SELECT 
    vend_id, prod_id, prod_price
FROM
    products
WHERE
    prod_price <= 5 
UNION SELECT 
    vend_id, prod_id, prod_price
FROM
    products
WHERE
    vend_id IN (1001 , 1002);
```

这两个结果一样，从这个简单例子中使用union比使用where子句更为复杂。但对于更复杂的过滤条件，或从多个表中检索数据的情形，使用union可能会处理更简单。

###### union规则

union必须是由两条或两条以上的select语句组成，语句之间用关键字union分割（因此，如果组合4条select语句，将要使用3个union关键字）

union中的每个查询必须包含相同的列，表达式或聚集函数（不过各个列不需要以相同的次序列出）

列数据类型必须兼容：类型不必完全相同，但必须是DBMS可以隐含转换的类型（例如，不同数值类型或不同的日期类型）

###### 包含或取消重复的行

union默认取消重复的行，如果不想取消可以使用union all.

```mysql
SELECT 
    vend_id, prod_id, prod_price
FROM
    products
WHERE
    prod_price <= 5 
UNION ALL SELECT 
    vend_id, prod_id, prod_price
FROM
    products
WHERE
    vend_id IN (1001 , 1002);
```

union all为union的一种形式，它完成where子句完成不了的工作。如果确实需要每个条件的匹配行全部出现（包括重复行），则必须使用union all，而不是where。

###### 对组合查询结果排序

只能使用一条order by，必须出现在最后一条select语句之后。

```mysql
SELECT 
    vend_id, prod_id, prod_price
FROM
    products
WHERE
    prod_price <= 5 
UNION SELECT 
    vend_id, prod_id, prod_price
FROM
    products
WHERE
    vend_id IN (1001 , 1002)
ORDER BY vend_id, prod_price desc;
+---------+---------+------------+
| vend_id | prod_id | prod_price |
+---------+---------+------------+
|    1001 | ANV03   |      14.99 |
|    1001 | ANV02   |       9.99 |
|    1001 | ANV01   |       5.99 |
|    1002 | OL1     |       8.99 |
|    1002 | FU1     |       3.42 |
|    1003 | SLING   |       4.49 |
|    1003 | FC      |       2.50 |
|    1003 | TNT1    |       2.50 |
+---------+---------+------------+
8 rows in set (0.00 sec)
```

### 18.全文本搜索

##### 理解全文本搜索

并非所有引擎都支持全文本搜索，mysql支持几种基本的数据库引擎。并非所有的引擎都支持全文本搜索。两个最常使用的引擎为MyISAM和InooDB，前者支持全文本搜索，后者不支持。

前面介绍了LIKE和正则表达式，虽然这些搜索机制非常有用，但存在几个重要的限制。

性能——二者要求mysql尝试匹配所有行（而且这些搜索极少使用表索引）。因此，由于搜索行数不断增加，这些搜索可能非常耗时。

明确控制——使用通配符和正则表达式匹配，很难（而且并不总是能）明确地控制匹配什么和不匹配什么。

智能化的结果——都不能提供一种智能化的选择结果的方法。



在使用全文本搜索时，mysql不需要分别查看每一行，不需要分别分析和处理每个词。mysql创建指定列中各词的一个索引，搜索可以针对这些词进行。这样mysql可以快速有效地决定哪些词匹配（哪些行包含它们），哪些词不匹配，它们匹配的频率，等等。



##### 使用全文本搜索

为了进行全文本搜索，必须索引被搜索的列，而且要随着数据的改变不断地重新索引。在对表列进行适当设计后，MySQL会自动进行所有的索引和重新索引。

在索引之后，select可与match()和against()一起使用以实际执行搜索。

不需要在导入数据时使用FULLTEXT，应该先导入所有数据，然后再修改表，定义FULLTEXT，这样比导入每行时分别进行索引时间短。

###### 进行全文本搜索

在索引之后，使用两个函数match和against来执行全文本搜索，其中match指定被搜索的列，against指定要使用的搜索表达式。

```mysql
mysql> select note_text from productnotes where match(note_text) against('rabbit');
+-----------------------------------------------------------------------------------------------------------------------+
| note_text                                                                                                             |
+-----------------------------------------------------------------------------------------------------------------------+
| Customer complaint: rabbit has been able to detect trap, food apparently less effective now.                          |
| Quantity varies, sold by the sack load.
All guaranteed to be bright and orange, and suitable for use as rabbit bait. |
+-----------------------------------------------------------------------------------------------------------------------+
2 rows in set (0.01 sec)
```

使用完整的match说明，传递给match的值必须与FULLTEXT定义中的相同。如果指定多个列，则补习列出它们（且次序正确）

搜索不区分大小写，除非使用BINARY方式。

上面的可以用like代替，但是返回的顺序不一样（没有固定的规律）

```mysql
select note_text from productnotes where note_text like '%rabbit%';
```

二者都没有包含order by子句，使用LIKE以不特别有用的顺序返回数据。使用全文本搜索返回以文本匹配的良好程度排序的数据。两个行中都包含rabbit，但包含词rabbit作为第三个词的行的等级比作为第20个词的行高。这很重要。全文本搜索的一个重要的部分就是对结果排序。具有较高等级的行先返回。

```mysql
select note_text, match(note_text) against('rabbit') as `rank` from productnotes;
```

这句可查看不同行的排序等级值。

###### 使用查询扩展

在使用查询扩展时，mysql对数据和索引进行两遍扫描来完成搜索：

首先，基本的全文本搜索

其次，mysql检查这些匹配的行并选择所有有用的词

再其次，mysql再次进行全文本搜索，这次不仅使用原来的条件，而且还使用所有有用的词。

利用查询扩展，能找出可能相关的结果，即使他们并不精确地包含所查找的词。

```mysql
-- 没有查询扩展
select note_text from productnotes where match(note_text) against('anvils');
-- 使用了查询扩展
select note_text from productnotes where match(note_text) against('anvils' with query expansion);
```

没有使用查询扩展时，只能搜索出包含anvils的行，一行，使用了查询扩展则查出7行。后面的包含了第一行中其他的词。

行越多越好，查询扩展返回的结果越好。

###### 布尔文本搜索

即使没有FULLTEXT索引也可以使用。但是这是一种非常缓慢的操作。

```mysql
-- 为了匹配包含heavy但不包含rope开始的词的行
select note_text from productnotes where match(note_text) against('heavy -rope*' in boolean mode);
```

![aXC6Vf.png](https://s1.ax1x.com/2020/08/11/aXC6Vf.png)

```mysql
-- 全文本操作符：包含rabbit和bait的行
select note_text from productnotes where match(note_text) against('+rabbit +bait' in boolean mode);
```

```mysql
-- 包含rabbit和bait中至少一个词
select note_text from productnotes where match(note_text) against('rabbit bait' in boolean mode);
```

```mysql
-- 包含rabbit bait短语而不是匹配两个词
select note_text from productnotes where match(note_text) against('"rabbit bait"' in boolean mode);
```

```mysql
-- 匹配rabbit和carrot，增加前者等级，降低后者等级
select note_text from productnotes where match(note_text) against('>rabbit <carrot' in boolean mode);
```

```mysql
-- 匹配safe和combination,降低后者等级
select note_text from productnotes where match(note_text) against('+safe +(<combination)' in boolean mode);
```

在布尔方式中不按等级值降序排序返回行。

###### 全文本搜索使用说明

3（包含）个一下的字符的词会被忽略且从索引中排除（数目可修改）

mysql有一个内建的非用词（stopword）列表，这些词在索引全文本时总是被忽略。如果需要可以覆盖这个列表。

mysql规定了一条50%原则，一个词出现在50%以上的行中，则将它作为一个非用词忽略。此规则不用于in boolean mode.

行数少于3行，则全文本搜索不返回结果（因为每个词要么不出现，要么出现在50%的行中）

忽略词中的单引号，例如don't索引为dont。

不具有词分隔符（包括中文和日语）的语言不能恰当的返回全文本搜索结果。

仅在MyISAM数据库引擎中支持全文本搜索。

没有临近操作符。

### 19.插入数据

可针对每个表或每个用户，利用MySQL安全机制禁止使用insert语句。

##### 插入完整的行

```mysql
insert into customers values(null, 'Pep E. LaPew', '100 Main Street', 'Los Angeles', 'CA', '90046', 'USA', null, null);
```

上面的语句高度依赖表中列定义次序，并且还依赖其次序容易获得的信息。不能保证下一次表结构变动后各个列保持完全相同的次序，因此尽量避免使用。

更安全的语句如下

```mysql
insert into customers(cust_name, cust_address, cust_city, cust_state, cust_zip, cust_country, cust_contact, cust_email) values('Pep E. LaPew', '100 Main Street', 'Los Angeles', 'CA', '90046', 'USA', null, null);
```

插入语句在workbench中无法使用Ctrl+B格式化。

省略列，可以在insert语句中省略某些列，但需要具备以下某个条件：

该列定义允许为null值（无值或空值）

在表定义中给出默认值。



insert语句操作可能很耗时，而且它可能降低等待处理的select语句的性能。如果数据检索是最重要的，则你可以通过在insert和into之间添加关键字LOW_PRIORITY,知识mysql降低insert语句的优先级。这个也适用于update和delete。

##### 插入多行

可以使用多个insert语句分号隔开，或者只要每条insert语句中列名（和次序）相同，可以组合各句：

```mysql
insert into customers(cust_name,
	cust_address,
    cust_city,
    cust_state,
    cust_zip,
    cust_country)
values(
		'Pep E. LaPew',
        '100 Main Street',
        'Los Angeles',
        'CA',
        '90046',
        'USA'
	),
	(
		'M. Martian',
        '42 Galaxy Way',
        'New York',
        'NY',
        '11213',
        'USA'
	);
```

这种比使用多条insert语句快，可以提高insert性能。

##### 插入检索出的数据

假如你想从另一个表中合并客户列表到你的customers表，不需要每次读取一行，然后将它用insert插入，可以如下进行：

```mysql
insert into customers(cust_id,
	cust_contact,
    cust_email,
    cust_name,
    cust_address,
    cust_city,
    cust_state,
    cust_zip,
    cust_country)
select cust_id,
	cust_contact,
    cust_email,
    cust_name,
    cust_address,
    cust_city,
    cust_state,
    cust_zip,
    cust_country
from custnew;
```

这个例子导入了cust_id（需求确保两个表中cust_id不重复），否则报错。可以简单的省略这个列，让自增加。

insert select中的列名：不一定要求二者列名匹配。事实上，mysql甚至不关心select返回的列名。它使用的是列的位置。

insert select语句可包含where子句以过滤插入的数据。

### 20.更新和删除数据

不要省略where子句，在使用update时一定要注意，因为没有就会更新表中所有的行。

update语句由3部分组成：要更新的表；列名和他们的新值；确定要更新行的过滤条件。

更新客户10005的电子邮件地址：

```mysql
update customers set cust_email = 'elmer@fudd.com' where cust_id = 10005;
```

更新多个列

```mysql
update customers set cust_name = 'The fudds', cust_email = 'elemer@fudd.com' where cust_id = '10005';
```

在update语句中可以使用子查询，使得能用select语句检索出数据更新列数据。

IGNORE关键字：update要么全成功，要么全失败，为了出现失败还能继续更新，可食用ignore关键字：update ignore customers...

为了删除某个列的值，可设置它为null（表得允许为null）。

##### 删除数据

同样是不要省略where子句，避免删除所有行

```mysql
delete from customers where cust_id = 10006;
```

delete不需要列名或通配符。delete是删除整行而不是删除列。为了删除指定的列，请使用update语句。

删除表的内容，不是删除表本身。

如果想从表中删除所有行，不要使用delete。可食用TRUNCATE TABLE语句。速度更快。（实际上是删除原来的表并重新创建了一个表，而不是逐行删除表中的数据）。

##### 更新和删除的指导原则

在对update和delete使用where子句之前，先用select测试，保证过滤的是正确的记录。

使用强制实施引用完整性的数据库，这样mysql将不允许删除具有其他表相关联的数据的行。

### 21.创建和操纵表

##### 创建表

```mysql
create table custnew
(
	cust_id int not null auto_increment,
    cust_name char(50) not null,
    cust_address char(50) null,
    cust_city char(50) null,
    cust_state char(5) null,
    cust_zip char(10) null,
    cust_country char(50) null,
    cust_contact char(50) null,
    cust_email char(255) null,
    primary key (cust_id)
) engine=InnoDB;
```

在创建新表时，指定的表名必须不存在，否则将出错。如果要防止意外覆盖已有的表，SQL要求首先手工删除该表，然后再重建它，而不是简单地用创建表语句覆盖它。

如果想在一个表不存在时创建它，可以在表名后加IF NOT EXISTS。

###### 主键再介绍

主键必须是唯一的。表中的每一行必须具有唯一的主键值。如果是单列，则它的值必须是唯一的，如果使用多个列作为主键，则这些列的组合值必须是唯一的。

为创建多个列的主键，应该以逗号分隔列表给出的各列名。primary key (cust_id, cust_name)。

主键中只能使用不允许null的列。

###### 使用AUTO_INCREMENT

AUTO_INCREMENT告诉mysql，本列每当增加一行时自动增量。每个表只允许一个AUTO_INCREMENT列。而且它必须被索引（如，通过使用它作为主键）。

可以在使用insert语句中覆盖AUTO_INCREMENT值，下一个自增量则从你插入的值开始。

让mysql生成（通过自增量）主键的一个缺点是你不知道这些值都是谁。

使用select last_insert_id()获取最新的自增量值。此语句返回最后一个AUTO_INCREMENT值，然后可以将它用于后续的mysql语句。

###### 指定默认值

```mysql 
create table custnew
(
	cust_id int not null auto_increment,
    cust_name char(50) not null,
    cust_address char(50) null,
    cust_city char(50) null,
    cust_state char(5) null,
    cust_zip char(10) null,
    cust_country char(50) null default 'USA',
    cust_contact char(50) null,
    cust_email char(255) null,
    primary key (cust_id)
) engine=InnoDB;
```

与大多数DBMS不一样，mysql不允许使用函数作为默认值。

###### 引擎类型

mysql和其他DBMS不一样，它具有多种引擎。它打包多个引擎隐藏着mysql服务器内，全都能执行create table和select等命令。

多种引擎是因为他们具有各自不同的功能和特性，胃不痛的任务选择正确的引擎能获得良好的功能和灵活性。

如果省略ENGINE=语句，则使用默认引擎（很可能是MyISAM）。多数SQL语句都默认使用它。

- InnoDB是一个可靠的事务处理引擎，它不支持全文本搜索。

- MEMORY在功能等同于MyISAM，但由于数据存储在内存（而不是磁盘），速度快（特别适合临时表）。

- MyISAM是一个性能极高的引擎，它支持全文本搜索，但不支持事务处理。

**外键不能跨引擎。**

##### 更新表

为了更新表定义，可食用alter table语句，但是，在理想状态下，当表中存储数据后，该表就不应该再被更新。

```mysql
-- 添加列
alter table vendors add vend_phone char(20);
-- 删除列
alter table vendors drop column vend_phone;
```

alter table的一种常见用途是定义外键。

```mysql
alter table orderitems add constraint fk_orderitems_orders foreign key (order_num) references orders (order_num);
```

**references后面的是主表，前面的是从表。**

**constraint是外键约束。让orderitems中外键值只能是orders的order_num中的值。**

##### 删除表

```mysql
drop table custnew;
```

##### 重命名表

```mysql
rename table customers2 to customers;
```

对多个表重命名

```mysql
rename table customers2 to customers,
			 backup_vendors to vendors,
			 backup_products to products;
```

上面的这些语句应该谨慎使用，并且应在做了备份后使用。

### 22.使用视图

##### 视图

视图是查询语句查出来的虚拟表。

视图常见应用：

- 重用SQL语句；
- 简化复杂的SQL操作。在编写查询后，可以方便地重用它而不必知道它的基本查询细节；
- 使用表的组成部分而不是整个表；
- 保护数据，可以给用户授予表的特定部分的访问权限，而不是整个表的访问权限。
- 更改数据格式和表示。视图可返回与底层表的标识和格式不同的数据。

###### 视图的规则和限制

- 与表一样，视图必须是唯一命名（不能和别的视图和表重名）
- 创建视图数目没有限制；
- 为了创建视图，必须有足够的访问权限。
- 视图可以嵌套，即可以利用从其他视图中检索数据的查询来构造一个视图。
- order by可以用在视图中，但如果从该视图检索数据select中也含有order by，那么该视图中的order by将被覆盖。
- 视图不能索引，也不能有关联的触发器或默认值；
- 视图可以和表一起使用。例如编写一条联结表和视图的select语句。

##### 使用视图

- 视图用create view来创建
- 使用show create view viewname;来查看创建视图的语句；
- 用drop删除视图，其语法为drop view viewname;
- 更新视图时，可以先用drop再用create，也可以直接用create or replace view。如果更新的视图不存在，则第二条更新语句会创建一个视图；如果要更新的视图存在，则第二条语句会替换原有的视图。

###### 利用视图简化复杂的联结

视图最常见的应用之一是隐藏复杂的SQL，这通常都会涉及联结。

```mysql
CREATE VIEW productcustomers AS
    SELECT 
        cust_name, cust_contact, prod_id
    FROM
        customers,
        orders,
        orderitems
    WHERE
        customers.cust_id = orders.cust_id
            AND orderitems.order_num = orders.order_num;
```

```mysql
-- 检索订购了产品TNT2的客户
select cust_name, cust_contact from productcustomers where prod_id = 'TNT2';
```

视图简化了复杂的SQL语句的使用，利用视图，可一次性编写基础的SQL，然后根据需要多次使用。

###### 用视图重新格式化检索出的数据

在前面使用过拼接字段得出如下数据

```mysql
mysql> select concat(rtrim(vend_name), '(', rtrim(vend_country), ')') as vend_title from vendors order by vend_name;
+------------------------+
| vend_title             |
+------------------------+
| ACME(USA)              |
| Anvils R Us(USA)       |
| Furball Inc.(USA)      |
| Jet Set(England)       |
| Jouets Et Ours(France) |
| LT Supplies(USA)       |
+------------------------+
6 rows in set (0.00 sec)
```

经常需要这总格式的结果，就可以创建视图

```mysql
CREATE VIEW vendorlocations AS
    SELECT 
        CONCAT(RTRIM(vend_name),
                '(',
                RTRIM(vend_country),
                ')') AS vend_title
    FROM
        vendors
    ORDER BY vend_name;
select * from vendorlocations;
```

###### 用视图过滤不想要的数据

```mysql
-- 用视图过滤数据：排除没有电子邮件的用户
CREATE VIEW customeremaillist AS
    SELECT 
        cust_id, cust_name, cust_email
    FROM
        customers
    WHERE
        cust_email IS NOT NULL;
select * from customeremaillist;
```

如果从视图检索时使用了where子句，则两组子句（一组视图中，另一组传递给视图的）将自动组合。

###### 使用视图与计算字段

```mysql
mysql> select prod_id, quantity, item_price, quantity*item_price as expanded_price from orderitems where order_num = 20005;
+---------+----------+------------+----------------+
| prod_id | quantity | item_price | expanded_price |
+---------+----------+------------+----------------+
| ANV01   |       10 |       5.99 |          59.90 |
| ANV02   |        3 |       9.99 |          29.97 |
| TNT2    |        5 |      10.00 |          50.00 |
| FB      |        1 |      10.00 |          10.00 |
+---------+----------+------------+----------------+
4 rows in set (0.00 sec)
```

通过视图创建一个泛化的查询

刚开始创建的视图是这样的

```mysql
CREATE VIEW orderitemsexpanded AS
    SELECT 
        prod_id,
        quantity,
        item_price,
        quantity * item_price AS expanded_price
    FROM
        orderitems;
```

然后使用查询

```mysql
select * from orderitemsexpanded where order_num = 20005;
```

发现报错了，Error Code: 1054. Unknown column 'order_num' in 'where clause',仔细一看发现少了order_num列，这个以后需要记一下，泛化视图需要包含要筛选的列，不能直接复制上面的查询语句到创建视图语句后面。

所以需要做视图更新

```mysql
CREATE OR REPLACE VIEW orderitemsexpanded AS
    SELECT 
        order_num,
        prod_id,
        quantity,
        item_price,
        quantity * item_price AS expanded_price
    FROM
        orderitems;
```

这样添加了order_num再查询就没有问题了。

**总结一下，视图就类似与函数，可以泛化一些具体的操作，并通过添加参数的形式具体到某个操作。也类似于语法糖，就是把一些长的语句简化为一个词。**

###### 更新视图

通常视图是可以更新的（即，可以对它使用insert、update和delete）。更新一个视图将更新其基表（视图本身没有数据）。如果对视图增加或删除行，实际上是对其基表增加或删除行。

并非所有视图都是可以更新的。如果mysql不能正确的确定被更新的基数据，则不允许更新（包括插入和删除），如果视图定义中有以下操作，则不能进行视图的更新：

- 分组（使用group by和having）
- 联结
- 子查询
- 并
- 聚集函数（min()、count()、sum()等）
- DISTINCT；
- 导出（计算）列

### 23.使用存储过程

##### 存储过程

到目前都是针对一个或多个表的单条语句，但实际上并不是都这么简单，经常会有一个完整的操作需要多条语句才能完成。

为了处理订单，需要核对以保证库存中有相应的物品。

如果库存有物品，这些物品需要预定以便不将它们再卖给别的人，并且要减少可用的物品数量以反映正确的库存量。

库存中没有的物品需要订购，这需要与供应商进行某种交互。

关于哪些物品入库（并且可以立即发货）和哪些物品退订，需要通知相应客户。

执行上面例子需要针对多个表处理多条mysql语句。

可以创建存储过程，简单来说就是为以后使用而保存的一条或多条mysql语句的集合。可将其视为批文件，虽然他们的作用不仅限于批处理。

##### 为什么要使用存储过程

- 通过把处理封装在容易使用的单元中，简化复杂的操作（正如前面的例子所描述）
- 由于不要求反复建立一系列处理步骤，这保证了数据的完整性。如果所有开发人员和应用程序都使用同一（试验和测试）存储过程，则所使用的代码都是相同的。这一点的延伸就是防止错误。需要执行的步骤越多，出错的可能性就越大。防止错误保证了数据的一致性。
- 简化对变动的管理。如果表名、列名或业务逻辑（或别的内容）有变化，只需要更改存储过程的代码。使用它的人员甚至不需要知道这些变化。这一点的延伸就是安全性。通过存储过程限制对基础数据的访问减少了数据讹误的机会。
- 提高性能，因为使用存储过程比使用单独的SQL语句要快。
- 存在一些职能用在单个请求中的mysql元素和特性，存储过程可以使用它们来编写功能更强更灵活的代码。

简单来说就是简单、安全、高性能。

缺陷：

存储过程的编写比基本的SQL语句复杂，编写存储过程需要更高的技能，更丰富的经验。

你可能没有创建存储过程的安全访问权限。

mysql将编写存储过程的安全和访问与执行存储过程的安全和访问区分开来，即使你不能编写自己的存储过程，也仍然可以使用执行别的存储过程。

##### 使用存储过程

###### 执行存储过程

```mysql
call productpricing(@pricelow, @pricehigh, @priceaverage);
```

productpricing是存储过程名称，里面的是参数。

###### 创建存储过程

刚开始语句时这样的，然后报错了

```mysql
create procedure productpricing() 
BEGIN
	select avg(prod_price) as priceaverage from products;
END
```

最后发现是因为编译器遇到第一次分号就结束了，所以需要定界符delimiter

```mysql
delimiter $     -- 告诉下面$是结束符
create procedure productpricing() 
BEGIN
	select avg(prod_price) as priceaverage from products;
END$
delimiter ;
```

有了定界符才能知道下面的是一整句，而不是好几句。

delimiter默认是分号。

mysql命令行客户机的分隔符：

默认的mysql语句分隔符为';'。mysql命令行使用程序也使用';'作为语句分隔符。如果命令行实用程序要解释存储过程自身内的';'字符，则它们最终不会成为存错过程的成分，这会使得存储过程中的SQL出现句法错误。

解决办法是临时更改命令行实用程序的语句分隔符

```mysql
delimiter //     
create procedure productpricing() 
BEGIN
	select avg(prod_price) as priceaverage from products;
END//
delimiter ;  --需要改回来，否则分隔符就变成了//
```

其中delimiter //高速命令行实用程序//作为新的语句结束分隔符，最后回复原来的语句分隔符。

```mysql
mysql> call productpricing();
+--------------+
| priceaverage |
+--------------+
|    16.133571 |
+--------------+
1 row in set (0.00 sec)

Query OK, 0 rows affected (0.00 sec)
```

###### 删除存储过程

```mysql
drop procedure productpricing;  -- 删除存储过程后面没有括号
```

###### 使用参数

变量：内存中一个特定的位置，用来临时存储数据。

以下是productpricing的修改版本：

```mysql
delimiter //
create procedure productpricing(
	OUT pl decimal(8, 2),   -- decimal(8, 2)表示十进制小数，8是指整数加小数部分总长度，多截少补
    out ph decimal(8, 2),
    out pa decimal(8, 2)
)
begin
	select min(prod_price)
    into pl
    from products;
    select max(prod_price)
    into ph
    from products;
    select avg(prod_price)
    into pa
    from products;
end //
delimiter ;
```

接收了3个参数，每个参数必须具有指定的类型，这里使用十进制值。

关键字out指出相应的参数用来从存储过程传出一个值（返回给调用者）。mysql支持in(传递给存储过程)、out（从存储过程传出）和inout（对存储过程传入和传出）类型的参数。

**注意，记录集不是允许的类型，因此，不能通过一个参数返回多个行和列。**

调用存储过程

```mysql
call productpricing(@pricelow, @pricehigh, @priceaverage);
```

有一个警告：1 row(s) affected, 1 warning(s): 1265 Data truncated for column 'pa' at row 1。表名pa数据被截断了。小数多余2位的部分被截掉了。

调用时，不显示任何数据。

**我刚开始好奇，为什么不是@pl, @ph和@pa。感觉就是上面的存储过程中是形参，下面传入的是实参，瞎编的，名称覆盖，用@pl和@ph也可。反正就是一个名称代替。**

为了显示检索出的额产品平均价格，可如下进行

```mysql
select @priceaverage;
```

为了获得三个值，可使用以下语句：

```mysql
select @pricehigh, @pricelow, @priceaverage;
```

创建一个带in和out的存储过程

```mysql
delimiter //
create procedure ordertotal(
	in onumber int,
    out ototal decimal(8, 2)
)
begin
	select sum(item_price*quantity)
    from orderitems
    where order_num = onumber
    into ototal;
end//
delimiter ;
```

onumber定义为in，因此订单号被传入存储过程。调用这个存储过程

```mysql
call ordertotal(20005, @total);
```

必须给ordertotal传递两个参数，第一个为订单号，第二个为包含计算出来的变量名

```mysql
select @total;
```

显示此结果。

###### 建立智能存储过程

上面的例子只会使得事情更复杂，只有在存储过程内包含业务规则和智能处理时，它们的威力才真正显现出来。

场景：你需要获得与以前一样的订单合计，但需要对合计增加营业税，不过只针对某些顾客，那么，需要做下面几件事情：

- 获得合计
- 把益康也睡有条件的添加到合计；
- 返回合计（带或不带营业税）

```mysql
-- name:ordertotal
-- parameters: onumber = order number
--             taxable = 0 if not taxable, 1 if taxable
--             ototal = order total variable
delimiter //
create procedure ordertotal(
	in onumber int,
    in taxable boolean,
    out ototal decimal(8, 2)
) comment 'Obtain order total, optionally adding tax'
begin
	-- declare variable for total
    declare total decimal(8, 2);
    -- declare tax percentage
    declare taxrate int default 6;
    
    -- get the order total
    select sum(item_price*quantity)
    from orderitems
    where order_num = onumber
    into total;
    
    -- is this taxable?
    if taxable then
		-- yes, so add taxrate to the total
        select total+(total/100*taxrate) into total;
	end if;
    
    -- and finally, save to out variable
    select total into ototal;
end//
delimiter ;
```

declare语句定义了两个局部变量，要求指出变量名和数据类型。它也支持可选的默认值。

select语句已经改变，因此其结果存储到total（局部变量）而不是ototal。

if语句检查taxable是否为真，为真则用另一个select语句增加营业税到局部变量total。if语句还支持elseif和else子句，前者还使用then子句，后者不使用。

最后用另一个select语句将total保存到ototal。

COMMIT关键字不是必须的，但如果给出，将在show procedure status结果中显示。

调用和显示

```mysql
call ordertotal(20005, 0, @total);
select @total;  --149.87
call ordertotal(20005, 1, @total);
select @total;  --158.86
```

###### 检查存储过程

为显示用来穿件一个存储过程的create子句，使用

```mysql
show create procedure ordertotal;
```

为获得包括何时、由谁创建等详细信息的存储过程列表，使用

```mysql
show procedure status;
```

为了限制过程状态结果，可以使用like指定一个过滤模式，例如

```mysql
show procedure status like 'ordertotal';
```

### 24.使用游标

##### 游标

游标（cursor）是一个存储在mysql服务器上的数据库查询，它不是一条select语句，而是被该语句检索出来的结果集。在存储了游标之后，应用程序可以根据需要滚动或浏览其中的数据。

不像多数DBMS，mysql游标只能用于存储过程（和函数）。

##### 使用游标

在使用前，先声明它。这个过程实际上没有检索数据，它只是定义要使用的select语句。

一旦声明后，必须打开游标以供使用。这个过程用前面定义的select语句把数据实际检索出来。

对于填有数据的游标，根据需要取出（检索）各行。

在结束游标使用时，必须关闭游标。

###### 创建游标

```mysql
delimiter //
create procedure processorders()
begin
	declare ordernumbers cursor for
    select order_num from orders;
end//
delimiter ;
```

declare语句创建游标。命名了ordernumbers游标，并定义了相应的select语句。

存储过程处理完成后，游标消失（因为它局限于存储过程）。

###### 打开和关闭游标

在上面声明完后再使用

```mysql
open ordernumbers;
close ordernumbers;
```

上面两个都报错，下面是修改版本，写到声明里面没报错，暂时还没弄清楚

```mysql
delimiter //
create procedure processorders()
begin
	-- declare the cursor
    declare ordernumbers cursor
    for
    select order_num from orders;
    -- open the cursor
    open ordernumbers;
    -- close the cursor
    close ordernumbers;
end //
delimiter ;
```

###### 使用游标数据

```mysql
delimiter //
create procedure processorders()
begin
	-- declare local variables
    declare o int;

	-- declare the cursor
    declare ordernumbers cursor
    for
    select order_num from orders;
    -- open the cursor
    open ordernumbers;
    -- get order numbers
    fetch ordernumbers into o;
    -- close the cursor
    close ordernumbers;
end //
delimiter ;
```

其中fetch用来检索当前的order_num列，将自动从第一行开始，到一个名为o的局部声明变量中。对检索出的数据不做任何处理。

循环检索数据，从第一行到最后一行

```mysql
delimiter //
create procedure processorders()
begin
	-- declare local variables
    declare done boolean default 0;
    declare o int;

	-- declare the cursor
    declare ordernumbers cursor
    for
    select order_num from orders;
    -- decalre continue handler
    declare continue handler for sqlstate '02000' set done = 1;
    -- open the cursor
    open ordernumbers;
    -- loop throught all rows
    repeat
		-- get order numbers
		fetch ordernumbers into o;
	-- end of loop
    until done end repeat;
    -- close the cursor
    close ordernumbers;
end //
delimiter ;
```

declare continue handler for sqlstate '02000' set done = 1;这条语句定义了continue handler，它是在条件出现时被执行的代码，这里，它指出当SQLstate '02000'出现时，set done=1.这个SQLstate '02000'是一个未找到的条件，当repeat由于没有更多的行供循环时，出现这个条件。

**declare语句的次序：declare语句的发布存在特定的次序。用declare语句定义的局部变量必须在定义任意游标或句柄之前定义，而句柄（handle）必须在游标之后定义。否则报错。**



结合上一张的存储过程ordertotal使用，对取出的数据进行实际处理

```mysql
delimiter //
drop procedure if exists processorders//
create procedure processorders()
begin
	-- declare local variables
    declare done boolean default 0;
    declare o int;
    declare t decimal(8, 2);

	-- declare the cursor
    declare ordernumbers cursor
    for
    select order_num from orders;
    -- decalre continue handler
    declare continue handler for sqlstate '02000' set done = 1;
    
    -- create a table to store the results
    create table if not exists ordertotals (order_num int, total decimal(8, 2));
    
    -- open the cursor
    open ordernumbers;
    -- loop throught all rows
    repeat
		-- get order numbers
		fetch ordernumbers into o;
        -- get the total for this order
        call orderotal(o, 1, t);
        -- insert order and total into ordertotals
        insert into ordertotals(order_num, total) values(o, t);
	-- end of loop
    until done end repeat;
    -- close the cursor
    close ordernumbers;
end //
delimiter ;
```

这次增加了另一个名为t的变量（存储每个订单的合计）。此存储过程还在运行中创建了一个新表（如果它不存在的话），这个表保存存储过程生成的结果。fetch像前面一样取每个order_num，然后用call执行另一个存储过程来计算每个订单的带水的合计（结果存储到t）。最后用insert保存每个订单的订单号和合计。

然后会发现表没有被创建，执行查询语句会报错，说表不存在。原因是这个只是创建了存储过程，还没有执行存储过程。所以在执行查询语句前，需要先执行存储过程。

```mysql
call processorders();  --注意不要忘记执行存储过程
select * from ordertotals;
+-----------+---------+
| order_num | total   |
+-----------+---------+
|     20005 |  158.86 |
|     20009 |   40.78 |
|     20006 |   58.30 |
|     20007 | 1060.00 |
|     20008 |  132.50 |
|     20008 |  132.50 |
+-----------+---------+
6 rows in set (0.01 sec)
```

### 25.使用触发器

如果你想要某条语句（或某些语句）在事件发生时自动执行，例如

每当增加一个顾客到某个数据库表时，都检查其电话号码格式是否正确，州的缩写是否为大写；

每当订购一个产品时，都从库存数量中减去订购的数量；

无论何时删除一行，都在某个存档表中保留一个副本。

共同之处：都需要在某个表发生更改时自动处理。这确切的说就是触发器。

触发器是mysql响应以下任意语句而自动执行的一条mysql语句（或位于begin和end语句之间的一组语句）

delete、insert、update。

其他mysql语句不支持触发器。

##### 创建触发器

触发器包含：

唯一的触发器名（表中唯一，但是同一个数据库中可以有多个同名触发器，其他DBMS中是数据库中唯一，以后mysql会更严格，所以按数据库唯一来使用就可以了）

触发器关联的表；

触发器应该响应的活动（delete、insert或update）；

触发器何时执行（处理之前或之后）。

```mysql
create trigger newproduct after insert on products for each row select 'Product added';
```

create trigger创建触发器名为newproduct，after是表示在insert执行后出发。这个触发器还指定了for each row,因此对每个插入行执行，在这个例子中，文本Product added将对每个插入行显示一次。

运行上面句子发现报错了Error Code: 1415. Not allowed to return a result set from a trigger，原因是mysql5早期版本是支持的，新版本已经不支持这种写法了。触发器不允许出现select * 的形式。因为这会返回一个结果集。这是不允许的。

正确的写法是使用select into的形式进行查询，将结果放入一个变量，然后查询该变量。

```mysql
CREATE TRIGGER newproduct AFTER INSERT  ON products
FOR EACH ROW SELECT 'Product added' into @arg;
```

然后插入一条数据做测试

```mysql
insert into products(prod_id, vend_id, prod_name, prod_price, prod_desc) values('comp', 1001, 'computer', 9999, 'A nice computer');
select @arg;
```

可正常显示Product added.

注意点：

只有表才支持触发器，视图不支持（临时表也不支持）。

触发器按每个表每个事件每次地定义，每个表每个事件每次只允许一个触发器。因此每个表最多支持留个触发器（每条insert、update、delete之前和之后）。单一触发器不能与多个时间或多个表关联。

如果before触发器失败，则mysql将不执行请求操作。此外如果before触发器或语句本身失败，mysql将不执行after触发器。

##### 删除触发器

```mysql
drop trigger newproduct;
```

触发器不能更新或覆盖，为了修改一个触发器，必须先删除它，然后重建它。

##### 使用触发器

###### insert触发器

- 在INSERT触发器代码内，可引用一个名为NEW的虚拟表，访问被插入的行
- 在BEFORE INSERT触发器中，NEW的值也可以被更新(允许更改被插入的值)
- 对于AUTO_INCREMENT列，NEW在INSERT执行前包含0，在INSERT被执行后包含新的自动生成值。

```mysql
create trigger neworder after insert on orders for each row select NEW.order_num into @order_num;
insert into orders(order_date, cust_id) values(Now(), 10001);
select @order_num;
```

在插入一个新订单到orders表时，mysql生成一个新的订单号并保存到order_num中。触发器从NEW.order_num取得这个值并返回它。此触发器必须按照after insert执行。因为在before insert语句执行之前，新的order_num还没有生成。

before还是after? 通常将before用于数据验证和净化（目的是保证插入表中的数据确实是需要的数据）。这个也适用于update触发器。

###### delete触发器

- 在DELETE触发器代码内，可以引用一个名为OLD的虚拟表，访问被删除的行
- OLD中的值全部都是只能读的，不能更新

```mysql
delimiter //
create trigger deleteorder before delete on orders for each row
begin
	create table if not exists archive_order(
		`order_num` int NOT NULL AUTO_INCREMENT,
		`order_date` datetime(0) NOT NULL,
		`cust_id` int NOT NULL,
        primary key(order_num)
	);
	insert into archive_order(order_num, order_date, cust_id)
    values(OLD.order_num, OLD.order_date, OLD.cust_id);
end//
delimiter ;
```

刚开始想着删除后把数据放到另一个表中，那就直接把创建表的过程也写到这个触发器里好了，结果报错了

Error Code: 1422. Explicit or implicit commit is not allowed in stored function or trigger.翻译过来的意思是存储函数或触发器中不允许显式或隐式提交，大致上理解就是触发器中不能创建表吧，后面再确认下。

然后把创建表的过程移除去。

```mysql
-- 创建表
create table if not exists archive_order(
		`order_num` int NOT NULL AUTO_INCREMENT,
		`order_date` datetime(0) NOT NULL,
		`cust_id` int NOT NULL,
        primary key(order_num)
);
-- 触发器
delimiter //
create trigger deleteorder before delete on orders for each row
begin
	insert into archive_order(order_num, order_date, cust_id)
    values(OLD.order_num, OLD.order_date, OLD.cust_id);
end//
delimiter ;
-- 删除试试
delete from orders where order_num = 20011;
```

发现在archive_order中出现了被删除的数据。成功。

在任意订单被删除前将执行此触发器。

- 它使用一条INSERT语句将OLD中的值（要被删除的订单）保存到一个名为archive_orders的存档表中（为实际使用这个例子，你需要用与orders相同的列创建一个名为archive_orders的表）。

使用BEFORE DELETE触发器的优点（相对于AFTER DELETE触发器
 来说）为，如果由于某种原因，订单不能存档，DELETE本身将被放弃。

使用BEGIN END块的好处是触发器能容纳多条SQL语句（在BEGIN END块中一条挨着一条）。

###### update触发器

UPDATE触发器在UPDATE语句执行之前或之后执行

- 在UPDATE触发器代码中，你可以引用一个名为OLD的虚拟表访问
   以前（UPDATE语句前）的值，引用一个名为NEW的虚拟表访问新
   更新的值；
- 在BEFORE UPDATE触发器中，NEW中的值可能也被更新（允许更改
   将要用于UPDATE语句中的值）
- OLD中的值全都是只读的，不能更新。

```mysql
create trigger updatevendor before update on vendors for each row set new.vend_state = upper(new.vend_state);
```

### 26.管理事务处理

并非所有的引擎都支持事务处理。MyISAM和InnoDB是两种最常用的引擎。前者不支持，后者支持。

事务处理（transaction processing）可以用来维护数据库的完整性，它保证成批的mysql操作要么完全执行，要么完全不执行。

事务处理是一种机制，用来管理必须成批执行的mysql操作，以保证数据库不包含不完整的操作结果。

在使用事务和事务处理是，有几个需要知道的术语：

- 事务（transaction）指一组SQL语句；
- 回退（rollback）指撤销指定SQL语句的过程；
- 提交（commit）指将未存储的SQL语句结果写入数据库表
- 保留点（savepoint）指事务处理中设置的临时占位符（placeholder），你可以对它发布回退（与回退整个事务处理不同）。

##### 控制事务处理

管理事务处理的关键在于将SQL语句分解为逻辑块，并明确规定数据何时应该回退，何时不应该回退。

```mysql
start transaction;
```

使用上面语句标识事务的开始

###### 使用rollback

```mysql
select * from archive_order;   -- 表不为空
start transaction;   -- 开始一个事务处理
delete from archive_order;  -- 删除所有行
select * from archive_order; -- 查看表已经为空
rollback;  -- 回退start transaction之后的所有语句
select * from archive_order; -- 表不为空
```

这里有个问题，在删除数据时报错：

```mysql
Error Code: 1175. You are using safe update mode and you tried to update a table without a WHERE that uses a KEY column.  To disable safe mode, toggle the option in Preferences -> SQL Editor and reconnect.
```

原因是：mysql在执行删除更新语句时报这种错误，是因为在mysql在safe-updates模式中，如果你where后跟的条件不是主键id，那么就会出现这种错误。

解决方式有两种

   1、SET SQL_SAFE_UPDATES = 0;执行该命令更改mysql数据库模式。

   2、在where判断条件中跟上主键id  例如：delete from firstmysqldatabase.user where UserName='zhangsan' and ID>=0;

这个因为没有涉及主键问题，所以采用方法一，解决了这个问题。

事务处理用来管理insert、update、delete语句，不能回退select语句。不能回退create或drop操作。事务处理中可以使用这两条语句，但执行回退，它们不会取消。

###### 使用commit

一般的mysql语句都是直接针对数据库表执行和编写的。这就是所谓的隐含提交（implicit commit），即提交（写或保存）操作是自动进行的。

事务处理块中，提交不会隐含进行。为进行明确的提交，使用commit

```mysql
start transaction;
delete from orderitems where order_num = 20006;
delete from orders where order_num = 20006;
commit;
```

因为涉及更新两个数据表，所以使用事务处理来保证订单不被部分删除。最后的commit语句仅在不出错是写出更改。如果第一条起作用，但第二条delete失败，则delete不会提交（实际上它会被自动撤销）。

隐含事务关闭：当commit或rollback语句执行后，事务会自动关闭（将来的更改会隐含提交）。



###### 使用保留点

为了支持回退部分事务处理，必须能在事务处理模块中何时的位置放占位符，这样，如果回退，可以回退到某个占位符。

这个占位符称为保留点。

```mysql
savepoint delete1;
rollback to delete1;
```

保留点越多越好。这就像git中的版本，分支，让你可以随时切换到过去的版本。

保留点在事务完成（rollback或commit）后自动释放。也可以用release savepoint明确的释放保留点。

###### 更改默认提交行为

默认mysql行为都是自动提交所有更改的。为指示mysql不自动提交更改，需要下面语句：

```mysql
set autocommit = 0;
```

autocommit是针对每个连接，而不是服务器的。

### 27.全球化和本地化

字符集为字母和符号的集合；

编码为某个字符集成员的内部表示；

校对为规定字符如何比较的指令。

##### 使用字符集合校对顺序

```mysql
show character set;
```

这条语句显示所有可用的字符集以及每个字符集的描述和默认校对。

```mysql
-- 查看所支持的胶墩的完整列表
show collation;
```

可以看到有的字符集具有不止一种校对。例如latin1对不同的欧洲语言有几种校对，而且许多校对出现两次，一次区分大小写（由_CS表示），一次不区分大小写（由\_Ci表示）。

为了确定所用的字符集合校对，使用下面语句

```mysql
show variables like 'character%';
show variables like 'collation%';
```

实际上，字符集很少是服务器范围（甚至数据库范围）的设置，不同的表甚至不同的列都可能需要不同的字符集。而且两者都可以在创建表时指定。

给表指定字符集和校对

```mysql
create table mytable
(
	column1 int,
    column2 varchar(10)
)default character set hebrew
collate hebrew_general_ci;
```

允许对每个列设置它们

```mysql
create table mytable
(
	column1 int,
    column2 varchar(10),
    column3 varchar(10) character set latin1 collate latin1_general_ci
)default character set hebrew
```

校对在对用order by子句检索出来的数据排序时起重要的作用。如果你需要用与创建表时不同的校对顺序排序特定的select语句，可以在select语句自身中进行：

```mysql
select * from customers order by cust_name collate latin1_general_cs;
```

上面的select语句演示了在通常不区分大小写的表上进行大小写搜索的一种技术。反过来也可以。

collate还可以用于group by、having、聚集函数，别名等。

注意，如果绝对需要，串可以在字符集之间进行转换，谓词使用cast()或convert()函数。

### 28.安全管理

##### 访问控制

安全基础：用户应该对他们需要的数据具有适当的访问权，既不能多也不能少。

你需要给用户提供他们所需要的的访问权，且仅提供他们需要的访问权。这就是所谓的访问控制。管理访问控制需要创建和管理用户账号。

不要使用root：应该严肃对待root登录的使用。仅在绝对需要使用时使用它（或许你在不能登录其他管理账号时使用）。不应该再日常的mysql操作中使用root。

##### 管理用户

mysql用户账号和信息存储在名为mysql的MySQL数据库中。

```mysql
use mysql;
select user from user;
```

###### 创建用户账号

```mysql
create user ben identified by '123456';
```

ben是用户名，123456是密码。

使用grant和insert：grant语句也可以创建用户账号，但一般来说create user是最清楚和最简单的句子。此外也可以通过直接插入行到user表来增加用户。不过为了安全不建议这么做。

```mysql
-- 重命名用户
rename user ben to bforta;
```

###### 删除用户账号

```mysql
drop user bforta;
```

###### 设置访问权限

查看用户账号的权限

```mysql
mysql> show grants for zxbylx;
+------------------------------------+
| Grants for zxbylx@%                |
+------------------------------------+
| GRANT USAGE ON *.* TO `zxbylx`@`%` |
+------------------------------------+
1 row in set (0.00 sec)
```

USAGE表示根本没有权限。

mysql的权限用用户名和主机名结合定义。如果不指定主机名，则使用默认的主机名%（授予用户访问权限而不管主机名）。

grant要素：

- 要授予的权限；
- 被授予访问权限的数据库或表；
- 用户名

```mysql
grant select on crash_course.* to zxbylx;
```

crash_course.*表示crash_course数据库的所有表。这一句表示可以在上面使用select。

```mysql
mysql> show grants for zxbylx;
+--------------------------------------------------+
| Grants for zxbylx@%                              |
+--------------------------------------------------+
| GRANT USAGE ON *.* TO `zxbylx`@`%`               |
| GRANT SELECT ON `crash_course`.* TO `zxbylx`@`%` |
+--------------------------------------------------+
2 rows in set (0.00 sec)
```

每个grant添加或更新用户的一个权限。mysql读取所有授权，并根据他们确定权限。

```mysql
-- 取消授权
revoke select on crash_course.* from zxbylx;
```

grant和revoke可在几个层次上控制访问权限：

整个服务器，使用grant all和revoke all；

整个数据库，使用on database.*;

特定的表，使用on database.table;

特定的列；

特定的存储过程。

[![axY4W8.png](https://s1.ax1x.com/2020/08/12/axY4W8.png)](https://imgchr.com/i/axY4W8)
[![axYISS.png](https://s1.ax1x.com/2020/08/12/axYISS.png)](https://imgchr.com/i/axYISS)
[![axYoQg.png](https://s1.ax1x.com/2020/08/12/axYoQg.png)](https://imgchr.com/i/axYoQg)

在使用grant和revoke时，用户账号必须存在，但对所涉及的对象没有这个要求。这允许管理员在创建数据库和表之前设计和实现安全措施。

副作用，当某个数据库或表删除时，相关的访问权限仍然存在。而且如果将来重建该数据库或表，这些权限仍然可用。

简化操作，逗号隔开多个权限

```mysql
grant select, insert on crash_coursh.* to zxbylx;
```

###### 修改账号密码

```mysql
set password for zxbylx = '654321';
```

书上和网上都是在等于后面调用password('654321')，这种形式，发现会报错。语法问题，说明新版本的mysql修改密码的语法改了。我用的mysql8.

set password还可以用来设置本账号的口令

```mysql
set password = '654321';
```

### 29.数据库维护

##### 备份数据

使用命令行程序mysqldump转存所有数据库内容到某个外部文件。

可用命令行实用程序mysqlhotcopy从一个数据库赋值所有数据（并非所有数据库引擎都支持这个实用程序）。

可以使用mysql的backup table和select into outfile转存所有数据到某个外部文件。这两条语句都接受将要创建的系统文件名。此文件必须不存在，否则出错。可用restore table来复原。

刷新未写数据：为了保证所有数据被写到磁盘（包括索引数据），可能需要在背负前使用flush tables语句。

##### 进行数据库维护

analyze table，用来检查表键是否正确。

```mysql
mysql> analyze table orders;
+---------------------+---------+----------+----------+
| Table               | Op      | Msg_type | Msg_text |
+---------------------+---------+----------+----------+
| crash_course.orders | analyze | status   | OK       |
+---------------------+---------+----------+----------+
1 row in set (0.02 sec)
```

check table用来针对许多问题对表进行检查。在MyISAM表上海对索引进行检查。check table支持一系列的用于MyISAM表的方式。

changed检查最后一次检查以来改动过的表。

extended执行最彻底的检查，

fast只检查未正常关闭的表，

medium检查所有被删除的链接并进行键检查，

quick只进行快速扫描。



check table发现 和修复问题

```mysql
mysql> check table orders, orderitems;
+-------------------------+-------+----------+----------+
| Table                   | Op    | Msg_type | Msg_text |
+-------------------------+-------+----------+----------+
| crash_course.orders     | check | status   | OK       |
| crash_course.orderitems | check | status   | OK       |
+-------------------------+-------+----------+----------+
2 rows in set (0.01 sec)
```

如果MyISAM表访问产生不正确和不一致的结果，可能需要用repair table来修复相应的表。不应该经常使用，否则可能会有更大的问题需要解决。

如果从一个表中删除大量数据，应该使用optimize table来收回所用的空间，从而优化表的性能。

##### 诊断启动问题

服务器启动问题通常在对mysql配置或服务器本身进行更改时出现。

在排除熊启动问题时，首先应该尽量手动启动服务器。

mysql服务器自身通过在命令行执行mysqld启动，下面是几个重要的mysqld命令行选项：

--help显示帮助，一个选项列表；

--safe-mode装载减去某些最佳配置的服务器；

--verbose显示全文本消息（为获得更详细的帮助消息与--help联合使用）

--version显示版本信息然后退出。

##### 查看日志文件

错误日志：包含启动和关闭问题以及任意关键错误的细节。此日志通常名为hostname.err,位于data目录中。此日志名可用--log-error命令行选项更改。

查询日志：它记录所有mysql活动，在诊断问题时非常有用。此日志文件可能会很快地变得非常大，因此你不应该长期使用。此日志名通常为hostname.log,位于data目录汇总。此名字可用--log命令行选项更改。

二进制日志：它记录更新过数据（或可能更新过数据）的所有语句。此日志名通常为hostname-bin,位于data目录中。此名字可以用 --log-bin命令行选项更改。

缓存查询日志：此日志记录执行缓慢的任何查询。这个日志在确定数据库何处需要优化很有用。此日志通常名为hostname-slow.log,位于data目录中，此名字可以用--log-slow-queries命令行选项更改。

在使用日志时，可用flush logs语句来刷新和重新开始所有日志文件。

### 30.改善性能

```mysql
-- 查看当前设置
show variables;
show status;
```

mysql是一个多用户多线程的DBMS，换言之，它经常同时执行多个任务。如果这些任务中的某一个执行缓慢，则所有请求都会执行缓慢。

```mysql
-- 显示所有活动进程（以及他们的线程ID和执行时间）
show processlist;
```

可以用kill命令中介某个特定的进程。

总有不止一种方法编写同一条select语句。应该试验联结、并、子查询等，找出最佳方法。

使用expain让mysql解释它将如何执行一条select语句。

一般来说，存储过程比一条一条执行其中的各条mysql语句块。

