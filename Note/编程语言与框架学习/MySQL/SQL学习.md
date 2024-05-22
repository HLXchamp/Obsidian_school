## 1.1 SQL语言概述

一、什么是SQL?
SQL（**结构化查询语言** ）是一种对关系数据库进行访问的数据操作语言。

二、SQL语言特点
① 一体化
② 使用方式灵活
③ 非过程化
④ 语言语句简单

三、SQL主要操作功能：
数据库对象创建、修改、删除
数据库表的数据插入、修改、删除、查询、统计
存储过程、触发器、函数等程序执行
数据库权限、角色、用户等管理 

![在这里插入图片描述|289](https://img-blog.csdnimg.cn/20210319083658681.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L0hMWGNoYW1w,size_16,color_FFFFFF,t_70)

四、SQL语言语句类型

1. 数据定义语言（**DDL**）是SQL语言中用于创建、修改或删除**数据库对象**的语句。
2. 数据操纵语言（**DML**）是SQL语言中用于增添、修改、删除**数据**的语句。
3. 数据查询语言（**DQL**）是SQL语言中用于对数据库进行**数据查询**的语句。
4. 数据控制语言（**DCL**）是用于对数据库对象**访问权**进行控制的SQL语句。
5. 事务处理语言（**TPL**）是SQL语言中用于数据库**内部事务处理**的语句。
6. 游标控制语言（**CCL**）是SQL语言中用于数据库**游标操作**的语句。

五、SQL语言的数据类型

- **字符**：CHAR、VARCHAR、TEXT
- **整数**：SMALLINT、INTEGER
- **浮点数**：NUMBER(n,d)、FLOAT(n,d)
- **日期**：DATE、DATETIME
- **货币**：MONEY

![[Pasted image 20240503000517.png|1175]]
![[Pasted image 20240503000640.png|525]]

注：
char(10)：最多只能存10个字符，不足10个字符，占用10个字符空间；性能高；浪费空间
varchar(10)：最多只能存10个字符，不足10个字符，按照实际长度存储；性能低；节省空间


![[Pasted image 20240503001011.png|875]]
## 1.2 数据定义DDL语句

**< >代表必选项；[ ]代表可选项；**
**所有字符串用单引号表示；
每个语句后用逗号分隔，最后一句没有逗号。**
### 1.2.1 数据库与数据库表的创建、修改、删除

**一、数据库创建SQL语句**
```sql
CREATE DATABASE <数据库名>;
```
例：执行SQL语句创建一个选课管理数据库CourseDB。

```sql
CREATE DATABASE CourseDB;
```
**二、数据库修改SQL语句**

```sql
ALTER  DATABASE  <数据库名> <修改内容>;
```

例：将选课管理数据库CourseDB名称修改为 CourseManageDB

```sql
ALTER  DATABASE  CourseDB  RENAME TO  CourseManageDB;
```
**三、数据库删除SQL语句**

```sql
DROP  DATABASE  <数据库名>;
```

例：删除前面创建的选课管理数据库CourseManageDB

```sql
DROP DATABASE CourseManageDB;
```
**四、数据库表创建SQL语句**

1、语句基本格式

```sql
CREATE TABLE  <表名>
 （ <列名1>  <数据类型>  [列完整性约束] [comment 字段1注释]，
	<列名2>  <数据类型>   [列完整性约束]，	
    <列名3>  <数据类型>  [列完整性约束]，
    …
   )[comment 表注释];
```
**2、列完整性约束**

列完整性约束关键词：
***PRIMARY KEY——主键
NOT NULL——非空值
NULL——空值
UNIQUE——值唯一
CHECK——有效性检查
DEFAULT——缺省值***

注：PRIMARY KEY定义表的主键列只能定义**单列**主键。

3、列完整性约束举例：
创建学生表Student的SQL语句

```sql
CREATE  TABLE  Student
（ StudentID  		char(13)  		PRIMARY  KEY，
   StudentName  	varchar(10)  	NOT  NULL，
   StudentGender  	char(2)  		NULL          comment '性别'，
   BirthDay  		date  		    NULL，
   Major  			varchar(30)  	NULL，
   StudentPhone  	char(11)   		NULL
 ）;
```
创建课程信息表Course的SQL语句

```sql
CREATE  TABLE  Course
( CourseID  	char(4)      PRIMARY  Key       auto_increment,
  CourseName  	varchar(20)  NOT  NULL  UNIQUE,
  CourseType  	varchar(10)  NULL CHECK(CourseType IN('基础课','专业课','选修课')),
  CourseCredit  smallint   	 NULL,
  CoursePeriod  smallint     NULL,
  TestMethod  	char(4)      NOT  NULL  DEFAULT  '闭卷考试'
);
```

加上auto_increment，可以自动增加！

**4、表约束定义主键**
若要定义由多个列构成的复合主键，则需要使用表约束方式来定义。

```sql
CREATE TABLE  <表名>
 （ <列名1>  <数据类型>  [列完整性约束]，
	<列名2>  <数据类型>   [列完整性约束]，	
    <列名3>  <数据类型>  [列完整性约束]，
    …
    CONSTRAINT  <约束名>  PRIMARY Key(主键列)
   )；
```
例：
```sql
CONSTRAINT	CoursePlan_PK	PRIMARY Key(CourseID,TeacherID)
```
使用表约束定义主键的优点：
① 便于定义复合主键
② 可命名主键约束
③ 便于定义代理键

在idea上可以使用可视化界面：

![[Pasted image 20240503152811.png|500]]

**5、表约束定义代理键**
在一些关系表中，为了方便数据处理，可以使用代理键去替代复合主键。在SQL语句中，关系表的代理键采用表约束方式来定义。

```sql
CREATE TABLE  <表名>
 （ <代理键列名>  <Serial数据类型>  NOT NULL，
	<列名2>  <数据类型>  [列完整性约束]，	
    <列名3>  <数据类型>  [列完整性约束]，
    …
    CONSTRAINT  <约束名>  PRIMARY Key(代理键列名)
   )；
```
*primary key 也可以直接写在上面*
例：

```sql
CREATE  TABLE  Plan
( CoursePlanID	serial		NOT  NULL,
  CourseID  	char(4)  		NOT  NULL,
  TeacherID  	char(4)  		NOT  NULL,
  CONSTRAINT	CoursePlan_PK	PRIMARY Key(CoursePlanID)
);
```

6、表约束定义外键
在数据库中，一些关系表之间存在关联。在一个表中作为主键的列，在另外的关联表中则作为**外键**。

```sql
CREATE TABLE  <表名>
 （ <列名1>  <数据类型>  [列完整性约束]，
	<列名2>  <数据类型>  [列完整性约束]，	
    <列名3>  <数据类型>  [列完整性约束]，
    …
    CONSTRAINT  <约束名>  FOREIGN Key(外键列)
   )；
```
例：

```sql
CREATE  TABLE  Register
( CourseRegID  	serial	NOT  NULL,
  CoursePlanID  Int  	NOT  NULL,
  StudentID  	char(13),
  Note  		varchar(30),
  CONSTRAINT	CourseRegID_PK	PRIMARY Key(CourseRegID),
  CONSTRAINT	CoursePlanID_FK	FOREIGN Key(CoursePlanID)
	REFERENCES  Plan(CoursePlanID)
    ON DELETE CASCADE,
  CONSTRAINT	StudentID_FK	FOREIGN KEY(StudentID)
	REFERENCES  Student(StudentID)
    ON DELETE CASCADE
);
```
`REFERENCES`：与另一个表的主键相关联
`plan`：主键是CoursePlanID的表
`DELETE CASCADE`：集联删除

![[Pasted image 20240503165404.png]]

**五、修改表结构SQL语句**

1.语句基本格式

```sql
ALTER TABLE <表名> <修改方式>;
```

2.主要语句类型
1）**ADD**修改方式，用于增加新列或列完整性约束

```sql
ALTER TABLE <表名> ADD <新列名称><数据类型>|[完整性约束]
```
2）**DROP**修改方式，用于删除指定列或列的完整性约束条件

```sql
ALTER TABLE<表名> DROP  COLUMN <列名>；
ALTER TABLE<表名> DROP  CONSTRAINT<完整性约束名>；
```

3）**RENAME**修改方式，用于修改表名称、列名称

```sql
ALTER TABLE <表名> RENAME TO <新表名>；
ALTER TABLE <表名> RENAME <原列名> TO <新列名>；
```

4）**ALTER**修改方式，用于修改列的数据类型

```sql
ALTER TABLE <表名> ALTER  COLUMN <列名> TYPE<新的数据类型>；
```
六、删除表结构SQL语句

```sql
DROP TABLE <表名>;
```

注意: 该语句将删除该表的所有数据及其结构。

例：删除注册表Register表及其数据

```sql
DROP TABLE Register;
```

### 1.2.2 <font color="#d83931">数据库索引</font>创建、修改、删除

一、什么是索引

索引是一种按照关系表中指定列的取值顺序组织元组数据存储的**数据结构**，使用它可以**加快表中数据的查询访问**。

![[Pasted image 20240503201217.png]]
二、索引作用及特点

索引作用：支持对数据库表中数据快速查找，其机理类似图书目录可以快速定位章节内容。

索引**优点**：
- 提高数据检索速度
- 可快速连接关联表
- 减少分组和排序时间

索引**缺点/开销**：
- 创建和维护索引都需要较大开销
- 索引会占用额外存储空间
- 数据操纵因维护索引带来系统性能开销
- 索引降低了增删改的效率

三、索引创建SQL语句

```sql
CREATE INDEX  <索引名>  ON <表名><(列名)>；
```

例：在学生信息表Student中，为出生日期Birthday列创建索引，以便支持按出生日期快速查询学生信息。

```sql
CREATE INDEX  Birthday_Idx  ON  STUDENT (Birthday)；
```
四、索引修改SQL语句
```sql
ALTER INDEX  <索引名> <修改项>；
```
索引名称修改语句格式如下：

```sql
ALTER INDEX  <索引名> RENAME TO <新索引名>；
```

例：在学生信息表Student中，将原索引Birthday_Idx更名为Bday_Idx，其索引修改SQL语句如下：

```sql
ALTER INDEX Birthday_Idx RENAME TO Bday_Idx；
```
五、索引删除SQL语句

```sql
DROP INDEX  <索引名> ；
```

例：在学生信息表Student中，删除bday_idx索引，其索引删除SQL语句如下：

```sql
DROP INDEX bday_idx；
```
六、MySQL中索引结构

![[Pasted image 20240503201611.png]]

![[Pasted image 20240503201837.png]]
## 1.3 数据操纵DML语句

一、数据插入SQL语句

1、语句基本格式
```sql
INSERT INTO <表名|视图名>[<列名表>] VALUES (列值表);
```
2、数据插入实例
例：在学生信息表Student中，插入一个新的学生数据，如“2017220101105”，“柳因”，“女”，“1999-04-23”，“软件工程”，“liuyin@163.com”。

```sql
INSERT INTO Student VALUES('2017220101105','柳因','女','1999-04-23','软件工程', 'liuyin@163.com');
```
3、多条数据插入
在数据库表插入操作中，还可以执行一组SQL数据插入语句，实现在表中多行数据插入。
例：在学生信息表STUDENT中，一次插入多个学生数据，其插入数据SQL语句如下： 

```sql
INSERT INTO Student VALUES('2017220101106','张亮','男','1999-11-21','软件工程','zhangl@163.com');
INSERT INTO Student VALUES('2017220101107','谢云','男','1999-08-12','软件工程','xiey@163.com');
INSERT INTO Student VALUES('2017220101108','刘亚','女','1999-06-20','软件工程',NULL);
```

也可以批量插入：
```sql
INSERT INTO Student VALUES('2017220101106','张亮','男','1999-11-21','软件工程','zhangl@163.com'), ('2017220101107','谢云','男','1999-08-12','软件工程','xiey@163.com');
```
二、数据更新SQL语句
1、语句基本格式

```sql
UPDATE  <表名|视图名>
SET  <列名1>=<表达式1> [，<列名2>=<表达式2>...]
[WHERE   <条件表达式>]；
```

2、数据更新实例
例 在学生信息表Student中，学生“刘亚”的原有Email数据为空，现需要修改为“zhaodong@163.com”。

```sql
UPDATE  Student
SET  Email='liuya@163.com', id=1
WHERE   StudentName='刘亚';
```
三、数据删除SQL语句
1、语句基本格式

```sql
DELETE 
FROM  <表名|视图名>
[WHERE   <条件表达式>]；
```

2、数据删除实例
例：在学生信息表STUDENT中，删除姓名为 “张亮”的学生数据，其数据删除的SQL语句如下：

```sql
DELETE 
FROM  STUDENT
WHERE   StudentName='张亮';
```

DELETE语句不能删除某一个字段的值（如果要操作，可以使用UPDATE，将该字段的值置为NULL）。

## 1.4 数据查询DQL语句
### 1.4.1 单表数据查询
**一、 数据查询SQL语句格式**

```sql
SELECT  [ALL|DISTINCT]  <目标列>[，<目标列>…]
[ INTO <新表> ]
FROM  <表名|视图名>[，<表名|视图名>…]
[ WHERE  <条件表达式> ]
[ GROUP BY  <列名> [HAVING <条件表达式> ]]
[ ORDER BY  <列名> [ ASC | DESC ] ];
```
**二、从单个表读取指定列**
在关系数据库中，最简单的数据查询操作就是从单个关系表中读取指定列的数据，即关系的投影操作。
1、语句基本格式

```sql
SELECT  <目标列>[，<目标列>…]
FROM  <关系表>；
```
例：从Student表中读取学生的学号、姓名、专业列数据输出。

```sql
SELECT StudentID, StudentName, Major
FROM Student;
```
设置别名（as可以省略）：
```sql
SELECT StudentID as '学生ID'
FROM Student;
```
查询**所有列**数据（建议直接列出全部属性搜索，性能更高）：

```sql
SELECT  *
FROM Student;
```
为了在结果集中过滤重复数据,可以在查询语句的输出列前加入**DISTINCT**关键字：

```sql
SELECT  DISTINCT Major
FROM Student;
```
**三、从单个表读取指定行**

SQL查询语句也可以从一个关系表中读取满足条件的指定行数据，即完成关系数据的元组选择操作。

语句基本格式：

```sql
SELECT  *
FROM  <关系表>
WHERE  <条件表达式>;
```
例：从Student表中查询男生数据。

```sql
SELECT  *
FROM  STUDENT
WHERE  StudentGender='男';
```
**四、从单个表读取指定行和列**
语句基本格式

```sql
SELECT   <目标列>[，<目标列>…]
FROM  <关系表>
WHERE  <条件表达式>;
```
例：从Student表中查询性别为“男”的学生学号、学生姓名、性别、专业数据。

```sql
SELECT   StudentID, StudentName, StudentGender, Major
FROM  STUDENT
WHERE  StudentGender='男';
```
**五、Where条件子句**

在WHERE子句中可以使用如下方式，指定范围数据。
1）使用**BETWEEN..AND**关键词（**闭区间**）来限定列值范围，还可以使用关键词**LIKE**与通配符来限定查询条件。
2）使用通配符来限定字符串数据范围。下划线（**_**）通配符用于代表**一个**未指定的字符。百分号（**%**）通配符用于代表**一个或多个**未指定的字符。

![[Pasted image 20240503155712.png|450]]
![[Pasted image 20240503155755.png|450]]

例1：若要从STUDENT表中查询出生日期在“2000-01-01”到“2000-12-30”的学生数据。其数据查询SQL语句如下：

```sql
SELECT * 
FROM STUDENT
WHERE BirthDay BETWEEN '2000-01-01' AND '2000-12-30';
```
例2：若要从STUDENT表中查询邮箱域名为“@163.com”的学生数据。其数据查询SQL语句如下：

```sql
SELECT  *
FROM  STUDENT
WHERE  Email  LIKE  '%@163.com';
```
在SQL查询Where子句中，还可以使用多个条件表达式，并通过逻辑运算符（**AND、OR、NOT**）连接操作，以及使用**IN或NOT IN**关键词，进一步限定结果集的数据范围。

例3：从STUDENT表中查询性别为“男”，并且专业为“软件工程”的学生数据，其数据查询SQL语句如下。

```sql
SELECT  StudentID, StudentName, StudentGender, Major
FROM  STUDENT
WHERE  Major='软件工程'  AND  StudentGender='男';
```
例4：在STUDENT表查询时，使用IN关键字限定范围”计算机应用”专业的学生。其SQL语句如下所示。

```sql
SELECT  StudentID, StudentName, StudentGender, Major
FROM  STUDENT
WHERE  Major IN  ('计算机应用');
```

**六、对结果集进行排序**

在SELECT查询语句返回的结果集中,行的顺序是任意的。如果需要结果集排序，可以在SELECT语句中加入**ORDER BY**关键字。

例：若要从STUDENT表中按学生出生日期降序输出学生数据，其数据查询SQL语句如下。

```sql
SELECT  *
FROM  STUDENT
ORDER  BY  Birthday DESC;
```

在默认情况下，是按指定列值的**升序**排列。可以使用关键词**ASC和DESC**选定排序是**升序或降序**。

如果结果集需要按多个列排序，可以分别加入关键字ASC或DESC改变。如果是多字段排序，当第一个字段值相同时，才会根据第二个字段进行排序。

例：先按出生日期降序排列，然后按姓名升序排列：

```sql
SELECT  *
FROM  STUDENT
ORDER  BY  Birthday DESC ,  StudentName  ASC;
```

### 1.4.2 内置函数、分组统计

一、SQL内置函数类型

SQL语言提供了大量内置函数，支持对SELECT查询结果数据进行处理。
典型SQL内置函数类型如下：
•聚合函数
•算术函数
•字符串函数
•日期时间函数
•数据类型转换函数

**二、SQL聚合函数**

**聚合函数**是一些对关系表中数值属性列进行计算并返回一个结果数值的函数，null不参与运算。

![在这里插入图片描述|525](https://img-blog.csdnimg.cn/20210324110109447.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L0hMWGNoYW1w,size_16,color_FFFFFF,t_70)

例1：若要统计Student表中的学生人数，在SELECT语句中可以使用**COUNT**（）函数来计算，其查询SQL语句如下：

```sql
SELECT  COUNT（*） AS  学生人数
FROM  Student;
```
**（*AS：代表重命名列名或表名
只要表用了AS，则所有出现表的地方都要用代替的名字替换，AS可省略*）**

例2：找出STUDENT表中年龄最大和年龄最小的学生出生日期，其查询SQL语句如下：

```sql
SELECT  Min（Birthday） AS 最大年龄，Max（Birthday） AS 最小年龄
```

三、**SQL内置函数与分组统计**

在SQL语言中，可使用内置函数对查询结果集进行分组数据统计。这是通过在SELECT语句中加入**Group By**子语句来实现。

分组统计SQL语句基本格式：

```sql
SELECT    统计函数（目标列）
FROM  <表名>
[WHERE  条件]
GROUP  BY  <目标列>
[Having  条件];
[ORDER  BY 条件]
```

例1：若要分专业统计Student表中的学生人数。在SELECT语句中可以使用GROUP BY分组子句完成统计，其查询SQL语句如下：

```sql
SELECT  Major  AS 专业,  COUNT（StudentID） AS 学生人数
FROM  Student
GROUP  BY  Major;
```

例2：若要分专业统计STUDENT表中男生人数，但限定只显示人数大于2的人数，其查询SQL语句如下：

```sql
SELECT  Major  AS 专业,  COUNT（StudentID） AS 学生人数
FROM  Student
WHERE  StudentGender=’男’
GROUP  BY  Major
HAVING  COUNT(*)>2;
```

四：注意的点

group by 有一个原则，就是 ***select 后面的所有列中，没有使用聚合函数的列，必须出现在 group by 后面***。
where 子句的作用是在对查询结果进行**分组前**将不符合where条件的行去掉，条件中**不能包含聚合函数**。
having 子句的作用是即在**分组后**过滤数据，条件中经常**包含聚合函数**。

注意：除了**使用GROUP BY语句**外，列的名称是不允许和内置函数一起混合使用。以下语句不规范。

```sql
SELECT  MaxHours, SUM(MaxHours)
FROM    PROJECT
WHERE   ProjectID <= 1200;
```

DBMS产品在使用内置函数的方式也不一样。一般来说，内置函数是不能用于WHERE子句中的。
以下语句不规范:

```sql
SELECT    ProjectID, MaxHours
FROM    PROJECT
WHERE    MaxHours < AVG(MaxHours);
```

注：
- 分组之后，查询的字段一般为**聚合函数**和**分组字段**，查询其他字段无任何意义；
- 执行顺序: where >聚合函数>having。
- 
#### <font color="#d83931">五、分页查询</font>

针对MySQL：

```SQL
select 字段列表 from 表名 limit 起始索引,查询记录数 ;
```

注意事项
1．起始索引从0开始，起始索引=（查询页码-1）* 每页显示记录数；
2、分页查询是数据库的方言，**不同的数据库有不同的实现**，MySQL中是LIMIT；
3．如果查询的是第一页数据，起始索引可以省略，直接简写为limit 10。

### 1.4.3 多表关联查询

一、**子查询与多表关联**

子查询SQL语句基本格式：

```sql
SELECT <目标列>[，<目标列>…]
FROM   <表名>
WHERE  <条件中嵌套另一关系表的SELECT 查询结果集>
```

在实际应用中，通常需要关联多表才能获得所需的信息。在SELECT查询语句中，可使用**子查询**方式实现多表关联查询。

例：在选课管理系统数据库中，希望能检索出“计算机学院”的教师名单。
该操作需要关联教师信息表Teacher和学院信息表College，才能获得这些数据。这里可采用子查询方法实现两表关联查询，其查询SQL语句如下：

```sql
SELECT  TeacherID,  TeacherName,  TeacherTitle
FROM  Teacher
WHERE  CollegeID  IN
       (SELECT  CollegeID  
    FROM  College
    WHERE  CollegeName='计算机学院');
```

二、**使用连接关联多表查询**

在使用多个表查询时，子查询只有在结果数据均来自一个表的情况下才有用。但如果需要从两个或多个表中获取结果数据，就不能使用子查询，而需要采用连接关联多表查询。
连接关联多表查询SQL语句基本格式：

```sql
SELECT <目标列>[，<目标列>…]
FROM   <表名1>，<表名2>，…, <表名n>，
WHERE  <关系表之间的连接关联条件>
```

例:   在选课管理系统数据库中，希望获得各个学院的教师信息列表，包括学院名称、教师编号、教师姓名、教师性别、职称等信息。要求按学院名称、教师编号分别排序输出，其查询SQL语句如下：

```sql
SELECT  B.CollegeName AS 学院名称, A.TeacherID  AS 编号, A.TeacherName  AS 姓名,  A.TeacherGender  AS 性别,  A. TeacherTitle  AS 职称
FROM  Teacher  AS  A，College  AS  B
WHERE  A.CollegeID=B.CollegeID
ORDER  BY  B.CollegeName, A.TeacherID;
```

三、**JOIN …ON连接查询语句**

在SQL语言中，实现多表连接关联查询还可以使用**JOIN…ON**关键词的语句格式。其中两表连接关联查询的JOIN…ON语句格式如下：

```sql
SELECT  <目标列>[，<目标列>…]
FROM  <表名1>  JOIN  <表名2>  ON <连接条件>;
```

例:    在选课管理系统数据库中，希望获得各个学院的教师信息，包括学院名称、教师编号、教师姓名、教师性别、职称等信息。要求按学院名称、教师编号分别排序输出，其查询SQL语句如下：

```sql
SELECT  B.CollegeName AS 学院名称,  A.TeacherID  AS 编号, A.TeacherName  AS 姓名,  A.TeacherGender  AS 性别,  A. TeacherTitle  AS 职称
FROM  TEACHER  AS  A  JOIN  COLLEGE  AS  B
ON  A.CollegeID=B.CollegeID
ORDER  BY  B.CollegeName, A.TeacherID;
```

补：
如果**查询条件**中含有**聚合运算**的结果值，**必须使用子查询**。
 
换句话说，where子句用以对行进行选择，如果我们要在where子句中使用聚合运算的结果值，由于where子句会先于分组执行，单个查询是无法做到的。必须使用子查询。

四、外部连接

前节介绍的多表连接方式在SELECT查询语句称为**内部连接**。 在一些特殊情况下，如关联表中一些行的主键与外键不匹配，查询结果集就会**丢失部分数据**。

例：在选课管理数据库中，希望查询所有开设课程的学生选课情况，包括课程名称、任课教师、选课学生人数。这需要关联课程信息表COURSE、教师信息表TEACHER、开课计划表PLAN、选课注册信息表REGISTER。其连接查询的SQL语句如下：

```sql
SELECT C.CourseName AS 课程名称, T.TeacherName AS 教师, 
  COUNT (R.CoursePlanID)  AS 选课人数
FROM  COURSE  AS  C  JOIN  PLAN  AS  P  ON  C.CourseID=P.CourseID 
  JOIN  TEACHER  AS  T  ON  P.TeacherID=T.TeacherID
  JOIN  REGISTER  AS  R  ON  P.CoursePlanID=R.CoursePlanID
GROUP  BY C.CourseName, T.TeacherName;
```
***（多个JOIN...ON之间没有符号间隔）***
在这个内连接查询中，只能找出有学生注册的课程名称和选课人数，但不能找出没有学生注册的课程名称。

在SQL 应用中，有时候也希望输出那些不满足连接条件的元组数据。这时，可使用JOIN…ON**外连接**方式实现。具体如下：
**LEFT JOIN**: **左**外连接，即使没有与右表关联列值匹配，也从左表返回所有的行。
**RIGHT JOIN**: **右**外连接，即使没有与左表关联列值匹配，也从右表返回所有的行。
**FULL JOIN**: **全**外连接，同时进行左连接和右连接，就返回所有行。

***（若前面语句写了LEFT JOIN，则后面的语句也要写，不然就不能完成操作）***

例：在选课管理系统数据库中，希望能查询所有开设课程的学生选课情况，包括课程名称、任课教师、选课学生人数。这需要关联课程信息表COURSE、开课计划表CPLAN、教师信息表TEACHER、选课注册信息表REGISTER。若使用左外连接查询，该JOIN…ON连接查询的SQL语句如下：

```sql
SELECT C.CourseName AS 课程名称, T.TeacherName AS 教师, 
COUNT  (R.CoursePlanID)  AS 选课人数
FROM  COURSE  AS  C  JOIN  PLAN  AS  P  
ON  C.CourseID=P.CourseID 
JOIN  TEACHER  AS  T  ON  P.TeacherID=T.TeacherID
LEFT  JOIN  REGISTER  AS  R  ON  P.CoursePlanID=R.CoursePlanID
GROUP  BY C.CourseName, T.TeacherName;
```

## 3.5 数据控制DCL语句

一、什么是数据控制SQL语句
数据控制SQL语句（DCL）是一种可对用户数据**访问权**进行控制的操作语句，它可以控制特定用户或角色对数据表、视图、存储过程、触发器等数据库对象的**访问权限**。

二、**GRANT权限授予语句**
**GRANT**语句是一种由**数据库对象创建者**或**管理员执行**的**权限授予**语句，它可以把访问数据库对象权限授予给其他用户或角色。
```sql
GRANT  <权限列表>  ON  <数据库对象>  TO  <用户或角色> [ WITH GRANT OPTION ]；
```
例：在选课管理系统数据库中，将课程注册表REGISTER的数据插入、数据修改、数据删除、数据查询访问权限赋予学生角色RoleS。
```sql
GRANT  SELECT, INSERT, UPDATE, DELETE  ON  REGISTER  TO  RoleS;
```
三、**REVOKE权限收回语句**
**REVOKE**语句是一种由数据库对象创建者或管理员将赋予其它用户或角色的**权限进行收回**语句，它可以收回原授予给其他用户或角色的权限。
```sql
REVOKE  <权限列表>  ON  <数据库对象>  FROM  <用户或角色> ;
```
例：在选课管理系统数据库中，收回学生角色RoleS在课程注册表REGISTER的数据删除访问权限。
```sql
REVOKE  DELETE  ON  REGISTER  FROM  RoleS;
```
四、**DENY权限拒绝语句**
**DENY**语句用于拒绝给当前数据库内的用户或者角色授予权限，并防止用户或角色通过其组或角色成员继承权限。
```sql
DENY  <权限列表>  ON  <数据库对象>  TO  <用户或角色> ;
```
例：在选课管理系统数据库中，若拒绝教师角色RoleT对教师表TEACHER的数据删除访问权限。
```sql
DENY  DELETE  ON  TEACHER  TO  RoleT;
```

## 3.6 视图SQL语句

一、什么是视图

**视图**——是一种通过基础表或其它视图构建的**虚拟表**。它本身**没有自己的数据**，而是使用了存储在基础表中的数据。 

![在这里插入图片描述](https://img-blog.csdnimg.cn/20210325170500154.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L0hMWGNoYW1w,size_16,color_FFFFFF,t_70)

二、**视图创建**SQL语句

1、语句基本格式
```sql
CREATE  VIEW  <视图名>[(列名1)，(列名2)，…]  AS  <SELECT查询>；
```
2、视图创建实例
例：在选课管理系统数据库中，若需要建立一个查看基础课数据的视图BasicCourseView，其创建SQL语句如下。
```sql
CREATE  VIEW  BasicCourseView  AS
SELECT  CourseName,  CourseCredit,  CoursePeriod,  TestMethod
FROM    COURSE
WHERE  CourseType='基础课';
```
*当视图在数据库中创建后，用户可以像访问关系表一样去操作访问视图。*

例：使用SELECT语句查询该视图数据，并按课程名称排序输出，其SQL语句如下：
```sql
SELECT   *
FROM  BasicCourseView
ORDER  BY  CourseName;
```
三、**视图删除**

当数据库不再需要某视图时，可以在数据库中删除该视图。
1、语句基本格式
```sql
DROP  VIEW  <视图名>；
```
其中**DROP VIEW** 为删除视图语句的关键词。
2、视图删除实例
例 在数据库中，若需要删除名称为BasicCourseView的视图对象，其删除该视图的SQL语句如下：
```sql
DROP  VIEW  BasicCourseView;
```
四、**SQL视图应用**

1.使用视图**简化**复杂SQL查询操作
***（可以画连接图来找关系）***
数据库开发人员可以将复杂的SQL查询语句封装在视图内，**外部程序**只需要使用简单的视图访问方式，便可获取所需要的数据。

例：在选课管理系统数据库中，查询选修“数据库系统原理与开发”课程的学生名单。这需要关联课程信息表COURSE、开课计划表PLAN、选课注册信息表REGISTER、学生信息表STUDENT，其查询SQL语句如下：

```sql
SELECT C.CourseName AS 课程名称, S.StudentID AS 学号, S.StudentName AS 姓名
FROM  COURSE AS C, PLAN AS  P,  REGISTER  AS  R, STUDENT  AS  S  
WHERE  C.CourseID=P.CourseID  AND  C.CourseName='数据库原理及应用' AND  P.CoursePlanID=R.CoursePlanID  AND  R.StudentID=S.StudentID;
```

上面这个SQL语句是较复杂和冗长，为了让外部程序简单地实现该信息查询，可以先定义一个名称为DatabaseCourseView视图，其创建SQL语句如下：

```sql
CREATE  VIEW  DatabaseCourseView  AS 
SELECT C.CourseName AS 课程名称, S.StudentID AS 学号, S.StudentName AS 姓名
FROM  COURSE AS C，PLAN AS  P,  REGISTER  AS  R, STUDENT  AS  S  
WHERE  C.CourseID=P.CourseID  AND  C. CourseName='数据库原理及应用'   AND  P.CoursePlanID=R.CoursePlanID  AND  R.StudentID=S. StudentID;
```

当DatabaseCourseView视图被创建完成后，外部程序就可以通过一个简单的SELECT语句查询视图数据，其操作语句如下：

```sql
SELECT  *  FROM  DatabaseCourseView;
```
2.使用视图提高数据访问**安全性**
通过视图可以将数据表中敏感数据隐藏起来，外部用户无法得知数据表的完整数据，降低数据库被攻击的风险。此外，还可以保护用户隐私数据。
*补：视图也可以写，只是只能对视图中的元素操作*

3. 提供一定程度的数据**逻辑独立性**
通过视图，可提供一定程度的数据逻辑独立性。当数据表结构发生改变，只要视图结构不变，应用程序可以不作修改。

4. 集中展示用户所感兴趣的特定数据
通过视图，可以将部分用户不关心的数据进行过滤，仅仅提供他们所感兴趣的数据。

## 习题：
~~主键必须是单一属性。~~ 
在数据库中，使用更多索引~~可以加快~~ 数据库处理速度。
在create table数据库表创建时，可以同时创建该表的外键与参照完整性约束。
~~在SQL语言的查询语句中使用SORT BY关键词实现结果集按指定列排序输出。~~ 
在多表关联查询中，如果最终结果数据都来自于**单个表**，则可以使用**子查询**进行处理。
SQL控制语句既可以对用户实现权限控制，也可以对角色实现权限控制。
数据库对象拥有者可以收回赋予其他用户的访问权限。
可以基于一个视图进行另一个视图对象创建。
**char(n)**数据类型适合“学号“属性列。

下面哪项不是SQL语言的特性？（B）
A：对数据库进行操作
B：**实现控制逻辑编程**
C：数据库游标操作
D：数据库事务操作

下列那个操作可能会影响数据一致性？（D）
A：UPDATE操作
B：ALTER操作
C：DELETE操作
**D：以上三种**


