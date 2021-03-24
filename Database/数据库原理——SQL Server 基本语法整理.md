---
title: 数据库原理——SQL Server 基本语法整理
date: 2020-06-19 22:25:10
tags: 
- Database
- SQL
categories:
- Database
toc: true
reward: true
---


`SQL` 可以分为**数据定义、数据查询、数据更新、数据操纵**四大部分。有时将数据更新称为数据操纵，或把数据查询和数据更新合称为数据操纵。

## 创建、备份数据库

### 创建数据库 `CREATE DATABASE`

- 数据库文件：一个数据库可以有多个数据库文件
  - 主数据库文件（**有且只有一个**），后缀名 `.mdf`
  - 次数据库文件（可以没有，也可以有多个），后缀名 `.ndf`
  - 事务日志文件 `LOG ON`（**每个数据库至少有一个**），后缀名 `.ldf`，**不属于任何文件组**
- 文件组：**一个文件只能存在于一个文件组中，一个文件组也只能被一个数据库使用**
  - 主文件组 `ON PRIMARY`（默认文件组，有且仅有一个）
  - 用户定义文件组 `FILEGROUP`（可以没有，也可以有多个）

```sql
CREATE DATABASE DBS
-- 主文件组
ON PRIMARY
( NAME = DBS1_Dat,
  -- 一个数据库只有一个主数据库文件 后缀名为 .mdf 
  FILENAME = 'F:\SQL_DBS\DBS1_Dat.mdf',
  SIZE = 10MB, MAXSIZE = 50MB, FILEGROWTH = 15% ),
( NAME = DBS2_Dat,
  -- 一个数据库可以没有，也可以有多个次数据库文件 后缀名为 .ndf
  FILENAME = 'F:\SQL_DBS\DBS2_Dat.ndf',
  SIZE = 10MB, MAXSIZE = 50MB, FILEGROWTH = 15% ),

-- 用户自定义文件组
FILEGROUP group1
( NAME = DBS11_Dat,
  FILENAME = 'F:\SQL_DBS\DBS11_Dat.ndf',
  SIZE = 10MB, MAXSIZE = 50MB, FILEGROWTH = 5% ),
( NAME = DBS12_Dat,
  FILENAME = 'F:\SQL_DBS\DBS12_Dat.ndf',
  SIZE = 10MB, MAXSIZE = 50MB, FILEGROWTH = 5% ),

FILEGROUP group2
( NAME = DBS21_Dat,
  FILENAME = 'F:\SQL_DBS\DBS21_Dat.ndf',
  SIZE = 10MB, MAXSIZE = 50MB, FILEGROWTH = 5% ),
( NAME = DBS22_Dat,
  FILENAME = 'F:\SQL_DBS\DBS22_Dat.ndf',
  SIZE = 10MB, MAXSIZE = 50MB, FILEGROWTH = 5% )

-- 事务日志文件 不属于任何文件组
LOG ON
( NAME = DBS_Log,
  -- 后缀名为 .ldf
  FILENAME = 'F:\SQL_DBS\DBS_Log.ldf',
  SIZE = 5MB, MAXSIZE = 25MB, FILEGROWTH = 5MB )
GO
```

---

### 备份数据库 `BACKUP`

`SQL Server` 中数据库备份有四种：

- 完全备份
- 差异备份
- 事务日志文件备份
- 文件及文件组备份

有两种标识备份设备的形式：

- 物理备份设备，是**操作系统**用来标识备份的名称，如 `F:\SQL_DBS\DBS.bak`
- 逻辑备份设备，使用来标识物理备份设备的**别名**或**公用名称**

**创建备份设备 `SP_ADDUMPDEVICE`**

```sql
USE MASTER
GO
-- 使用系统存储过程 SP_ADDUMPDEVICE <devType>, <logicalName>, <physicalName>
EXEC SP_ADDUMPDEVICE 'DISK', 'DBS_BAK', 'F:\SQL_DBS\DBS_BAK'
```

**查看备份设备 `SP_HELPDEVICE`**

```sql
USE MASTER
GO
EXEC SP_HELPDEVICE
```

**删除备份设备 `SP_DROPDEVICE`**

```sql
USE MASTER
GO
-- SP_DROPDEVICE <logicalName>
EXEC SP_DROPDEVICE 'DBS_BAK'
```

---

> 在 `SQL Server` 中，备份使用 `BACKUP` 语句

**完整备份 `WITH INIT`**

```sql
BACKUP DATABASE DBS TO DBS_BAK
WITH INIT,
NAME = 'DBS 完整备份',
DESCRIPTION = 'This is the full backup of DBS on disk.'
```

**差异备份 `WITH DIFFERENTIAL`**

```sql
BACKUP DATABASE DBS TO DBS_BAK
WITH DIFFERENTIAL,
NAME = 'DBS 差异备份',
DESCRIPTION = 'This is the differential backup of DBS on disk.'
```

**事务日志备份 `WITH NOINIT`**

```sql
BACKUP DATABASE DBS TO DBS_BAK
WITH NOINIT,
NAME = 'DBS 事务日志备份'
DESCRIPTION = 'This is the transaction backup of DBS on disk.'
```

## 数据表的基本操作

### 🔺定义基本表 `CREATE TABLE`

- 完整性约束：
  - **列级完整性**约束通常包括：**是否为空、缺省值**
  - **表级完整性**约束通常包括：**主键、外键、唯一性、检查**
- 数据类型：
  - 字符串型
    - `VARCHAR(n)` 长度最多为 `n` 的**变长**字符串
    - `CHAR(n)` 长度为 `n` 的**定长**字符串
  - 数值型
    - `INT, INTEGER` 长整型 4 字节
    - `SMALLINT` 短整型 2 字节
    - `BIGINT` 大整数 8 字节

```sql
USE DBS
GO
-- Student 表
CREATE TABLE Student (
	Sno CHAR(10),
	-- 非空约束
	Sname CHAR(20) NOT NULL, 
	Sage SMALLINT,
	Ssex CHAR(4),
	Sdept CHAR(20),
	-- 主键约束
	CONSTRAINT SPK PRIMARY KEY(Sno),
	-- 检查约束
	CONSTRAINT SCK CHECK (Ssex IN ('男', '女')),
	-- 唯一值约束
	CONSTRAINT SUQ UNIQUE(Sname)
)

-- Course 表
USE DBS
GO
CREATE TABLE Course (
	Cno CHAR(10),
	Cname CHAR(20),
	Cpno CHAR(10),
	Ccredit SMALLINT,
	-- 主键约束
	CONSTRAINT CPK PRIMARY KEY(Cno),
	-- 外键约束
	CONSTRAINT CpnoFK FOREIGN KEY(Cpno) REFERENCES Course(Cno)
)

-- SC 表
USE DBS
GO
CREATE TABLE SC (
	Sno CHAR(10),
	Cno CHAR(10),
	-- 默认值约束，-1 代表还没有成绩
	Grade SMALLINT DEFAULT -1,
	-- 主键约束
	CONSTRAINT SCPK PRIMARY KEY(Sno, Cno),
	-- 外键约束
	CONSTRAINT SCFKS FOREIGN KEY(Sno) REFERENCES Student(Sno),
	CONSTRAINT SCFKC FOREIGN KEY(Cno) REFERENCES Course(Cno)
)
```

### 修改基本表 `ALTER TABLE`

**增加新列名 `ADD`** 

```sql
ALTER TABLE Student
-- ADD <列名> <数据类型> <表级完整性约束>
ADD Test INT NOT NULL
```

**修改列的数据类型 `ALTER COLUMN`**

```sql
ALTER TABLE Student
-- ALTER COLUMN <列名> <新数据类型>
ALTER COLUMN Test CHAR(20)
```

**修改列名 `SP_RENAME`**

```sql
-- SP_RENAME <表名.列名>, <新列名>, 'COLUMN'
EXEC SP_RENAME 'Student.Test', 'NewTest', 'COLUMN'
```

**删除列名 `DROP COLUMN`**

```sql
ALTER TABLE Student
-- DROP COLUMN <列名>
DROP COLUMN Test
```

**增加表级完整性约束 `ADD CONSTRAINT`**

```sql
ALTER TABLE Student
-- ADD CONSTRAINT <表级完整性约束名> <约束类型和作用列>
ADD CONSTRAINT TestCst UNIQUE(Sage)
```

**删除完整性约束 `DROP CONSTRAINT`**

```sql
ALTER TABLE Student
-- DROP CONSTRAINT <完整性约束名>
DROP CONSTRAINT TestCst
```

### 删除基本表 `DROP TABLE`

```sql
DROP TABLE Test
```

### 索引 `INDEX`

**建立索引 `CREATE INDEX`**

- `ASC` 升序（默认）
- `DESC` 降序
- `UNIQUE` 唯一索引
- **`CLUSTER` 聚簇索引**

```sql
-- CREATE [UNIQUE] INDEX <索引名> ON 表名(列名 [ASC/DESC])
CREATE UNIQUE INDEX SCno ON SC(Sno ASC, Cno DESC)
```

**修改索引 `SP_RENAME`**

```sql
-- SP_RENAME '表名.索引名', '新索引名', 'INDEX'
EXEC SP_RENAME 'SC.SCno', 'NewSCno', 'INDEX'
```

> 标准 `SQL` 语言中修改索引：`ALTER INDEX <索引名> RENAME TO <新索引名>`

**删除索引 `DROP INDEX`**

```sql
-- DROP INDEX 表名.索引名
DROP INDEX SC.NewSCno
```

### 对基本表的增删改

**增加元组 `INSERT`**

```sql
INSERT Student VALUES('2020120', '张三', 19, '男', 'IS')
```

**删除元组 `DELETE FROM`**

删除单个元组

```sql
DELETE FROM Student
WHERE Sno = '2020120'
```

删除所有元组

```sql
DELETE FROM Student
```

**更新元组 `UPDATE...SET`**

```sql
UPDATE Student
SET Sname = '赵梦'
WHERE Sno = '2020121'
```

### 🔺数据查询 `SELECT`

#### 单表查询

- 重复数据
  - `ALL` ：不去重，默认
  - `DISTINCE` ：去重
- 比较大小 `=, >, <, >=, <=, != 或 <>, !>, !<`
- 确定范围 `[NOT] BETWEEN...AND...`
- 集合 `[NOT] IN`
- 字符匹配 `LIKE`
  - `%` **任意长度**，长度可以为 `0`
  - `_` 表示**单个字符**，数据库字符集为 `ASCII` 时汉字占两个字符，字符集为 `GBK` 时汉字占一个字符
  - `ESCAPE` 表示转义，例：`Sname LIKE '\%李_' ESCAPE '\'`，则 `%` 变为原本含义
- 对于 `NULL`，**只能使用 `IS` 或 `IS NOT` 进行判断**

```sql
-- 查找名字中倒数第二个字为"李"，并且属于 IS 或 CS 或 MA 系的年龄大于等于 20 岁的学生的学号、姓名、出生年份
SELECT DbiaoISTINCT Sno, Sname, 2020-Sage Birth
FROM Student
WHERE Sdept IN ('IS', 'CS', 'MA')
-- 只要"李"字为倒数第二个字符就会被选出
AND Sname LIKE '%李_'
AND Sage >= 20
-- NULL 只能用 IS 或 IS NOT
AND Ssex IS NOT NULL
```

#### `ORDER BY` 子句

- 结果按 `ASC` 或 `DESC` 排序，默认为 `ASC` 升序

```sql
-- 查找所有学生的学号，并且按照降序排列
SELECT Sno
FROM Student
ORDER BY Sage DESC
```

#### 聚集函数

- `COUNT()`，`COUNT(*)` 可以统计元组个数
- `SUM()`
- `AVG()`
- `MAX()`
- `MIN()`
- 聚集函数中可以使用 `DISTINCT` 或 `ALL`
- 聚集函数遇到 `NULL` 值时，除了 `COUNT(*)` 以外，都会跳过而不会处理
- **聚集函数不能用在 `WHERE` 子句，只能使用在 `SELECT` 子句和 `GROUP BY` 中的 `HAVING` 子句中**

```sql
-- 查找所有课程平均分大于等于 90 分的学生的学号和平均分
SELECT Sno, AVG(ALL Grade) Gavg
FROM SC
GROUP BY Sno
-- 不能使用 WHERE
HAVING AVG(Grade) >= 90
```

**聚集函数与 `ANY/ALL` 谓词的对应关系**

> 一般来说聚集函数的查询效率高于 `ANY/ALL` 谓词

 |=|<> 或 !=|<|<=|>|>=
:-:|:-:|:-:|:-:|:-:|:-:|:-:
ANY|IN|--|< MAX|<= MAX|\> MIN|\>= MIN
ALL|--|NOT IN|< MIN|<= MIN|\> MAX|\>= MAX

#### 连接查询

**普通连接**

```sql
-- 查找有课程分数大于等于 90 分的学生的学号、姓名、课程名
SELECT DISTINCT Sno, Sname, Cname
FROM Student, SC, Course
WHERE Student.Sno = SC.Sno
AND Course.Cno = SC.Cno
AND SC.Grade >= 90
```

**自身连接**

自身连接需要**取别名**

```sql
-- 查找一门课程的间接先修课（先修课的先修课）
SELECT C1.Cno, C2.Cpno
FROM Course C1, Course C2
WHERE C1.Cpno = C2.Cno
```

**左外连接 `LEFT JOIN...ON`**

保留左边的**悬浮元组**，在对应的右边表的属性上取 **`NULL` 值**

```sql
-- 查找有课程大于等于 90 分的学生的学号和姓名
SELECT DISTINCT Sno, Sname
FROM Student
LEFT JOIN SC
ON Student.Sno = SC.Sno
WHERE SC.Grade >= 90
```

**右外连接 `RIGHT JOIN...ON`**

保留右边的**悬浮元组**，在对应的右边表的属性上取 **`NULL` 值**

```sql
-- 查找有课程大于等于 90 分的学生的学号和姓名
SELECT DISTINCT Sno, Sname
FROM SC
RIGHT JOIN Student
ON SC.Sno = Student.Sno
WHERE SC.Grade >= 90
```

#### 嵌套查询

##### 不相关子查询

- 子查询中不能使用 **`ORDER BY`** 子句，`ORDER BY` 子句只能**对最终结果进行排序**
- 有些嵌套查询可以用**连接查询**替代，但不是全部都可以

带有 `IN` 谓词的子查询：

```sql
-- 查找选修了课程号为'2'的学生的学号
SELECT Sno
FROM Student
WHERE Sno IN (
	SELECT Sno
	FROM SC
	WHERE SC.Cno = '2'
)
```

##### 相关子查询

- 相关子查询：子查询的查询条件依赖于父查询

带有比较运算符的子查询

```sql
-- 找出每个学生超过他所有课程平均分的课程号
SELECT Sno, Cno
FROM SC x
WHERE x.Grade > (
	SELECT AVG(Grade)
	FROM SC y
	WHERE x.Sno = y.Sno
)
```

带有 `ANY(SOME)` 或 `ALL` 谓词的子查询

```sql
-- 查询课程成绩比学号为'2020120'的同学的课程最高分还高的学生的学号和课程号
SELECT Sno, Cno
FROM SC
WHERE Grade > ALL(
	SELECT Grade
	FROM SC
	WHERE Sno = '2020120'
)
```
::: danger
**🔺带有 `EXISTS` 谓词的子查询**
:::

- `EXISTS` 代表**存在量词 $\exists$，带有 `EXISTS` 谓词的子查询不返回任何数据，只产生逻辑真值 `true` 或逻辑假值 `false`**
- 对于 `EXISTS` 量词，若**内层查询结果非空，则外层的 `WHERE` 子句返回真值，否则返回假值**
- 对于 `NOT EXISTS` 量词，若**内层查询结果非空，则外层的 `WHERE` 子句返回假值，否则返回真值**
- 由于带 `EXISTS` 的子查询只返回真值或假值，列名无意义，所以目标列表达式通常都用 `*`
- 可以利用 `EXISTS` 来判断 $x\in S, S\subseteq R, S=R, S\cap R\not= \emptyset$ 等是否成立

```sql
-- 查询所有选修了课程号为'1'的学生的姓名
SELECT Sname
FROM Student
WHERE EXISTS (
	SELECT *
	FROM SC
	WHERE SC.Sno = Student.Sno
	AND SC.Cno = '1'
)
```

- 一些带 `EXISTS` 或 `NOT EXISTS` 谓词的子查询**不能**被其他形式的子查询等价替换，而**所有**带 `IN` 谓词、比较运算符、`ANY/ALL` 谓词的子查询**都能**用带 `EXISTS` 谓词的子查询等价替换

- `SQL` 中没有**全称量词**，但是可以把带有全称量词的谓词转换为等价的带有**存在量词**的谓词：

  $$(\forall x)P\equiv (\exists x(\neg P))$$

```sql
-- 查询至少选修了学号为'2020120'的学生选修的所有课程的学生学号
SELECT Sno
FROM Student x
WHERE NOT EXISTS (
	SELECT *
	FROM SC y
	WHERE Sno = '2020120'
	AND NOT EXISTS (
    	SELECT *
    	FROM SC z
    	WHERE z.Sno = x.Sno
    	AND z.Cno = y.Cno
    )
)
```

> 此查询可以用**逻辑蕴含**来表达：查询学生 x，对所有课程 y，若学号为'2020120'的学生选修了 y，则 x 也选修了 y
>
> 用$p$表示谓词“学号为'2020120'的学生选修了课程 y”
>
> 用$q$表示谓词“学生 x 也选修了 y”
>
> 则上述查询为$(\forall y)p\rightarrow q$
>
> `SQL` 语言中没有蕴含逻辑运算，但是可以将其转换为：
>
> $$p\rightarrow q\equiv \neg p\lor q（p 若\\满\\足\\，\\则 q 一\\定\\满\\足\\ \equiv \\要\\么\\是 p 不\\满\\足\\要\\么\\是 q 满\\足\\）$$
>
> 所以该查询可以转换为：
>
> $$(\forall y)p\rightarrow q\\\equiv \neg(\exists y(\neg (p\rightarrow q)))\\\equiv \neg(\exists y(\neg (\neg p\lor q)))\\\equiv \neg (\exists y(p \vee \neg q))$$
>
> 即语义变为：**不存在这样的课程 y，学生'2020120'选修了 y，但是学生 x 没有选修 y**

#### 集合查询

- 集合操作主要包括**并操作 `UNION`、交操作 `INTERSECT`、差操作 `EXCEPT`**
- 参加集合操作的各查询结果的**列数必须相同，对应项的数据类型也必须相同**
- 使用 `UNION` 将多个查询结果合并在一起时，系统会**默认去掉重复元组**，若要保留，则需使用 **`UNION ALL`** 操作符

```sql
-- 查询计算机系中年龄不大于 19 岁的学生
SELECT Sno, Sname
FROM Student
WHERE Sdept = 'CS'
INTERSECT
SELECT Sno, Sname
FROM Student
WHERE Sage <= 19
```

```sql
-- 查询计算机系中年龄小于 19 岁的学生
SELECT Sno, Sname
FROM Student
WHERE Sdept = 'CS'
EXCEPT
SELECT Sno, Sname
FROM Student
WHERE Sage >= 19
```

#### 基于派生表的查询

- 派生表的查询：子查询出现在 `FROM` 子句中，此时生成临时派生表

```sql
-- 查询每个学生超过他自己选修课程的平均成绩的课程号
SELECT Sno, Cno
FROM SC, (SELECT Sno, AVG(Grade) FROM SC GROUP BY Sno) AS Avg_sc(avg_sno, avg_grade)
WHERE SC.Sno = Avg_sc.avg_sno
AND SC.Grade > Avg_sc.avg_grade
```

如果子查询没有使用聚集函数，则派生表可以不指定属性列，子查询 `SELECT` 子句后面的列名为其默认属性列名

```sql
-- 查询所有选修了 1 号课程的学生姓名
SELECT Sname
FROM Student, (SELECT Sno FROM SC WHERE Cno = '1') AS SC1
WHERE Student.Sno = SC1.Sno
```

通过 `FROM` 子句生成派生表时，`AS` 关键字可以省略，但是**必须为派生表指定一个别名**

### 空值的处理

- 空值的产生有以下几种情况：
  - 该属性应该取一个值，但目前不知道它的具体值
  - 该属性不应该有值
  - 由于某种原因不便于填写
- 空值的判断：
  - 需要使用 `IS NULL` 或 `IS NOT NULL` 来判断
- 空值的约束条件：
  - 属性定义（或者域定义）中有 `NOT NULL` 约束条件的不能取空值
  - 加了 `UNIQUE` 约束的属性不能取控制
  - 码属性不能取空值
- **空值与另一个值（包括空值）的算术运算结果为空值，与另一个值（包括空值）的比较运算结果为 `UNKNOWN`**
- **逻辑运算中，`U AND T = U, U OR F = U`**
- 查询语句中，只有使 `WHERE` 和 `HAVING` 子句为 `TRUE` 的元组才会被选出 

x    y|x AND y| x OR y|NOT x
:-:|:-:|:-:|:-:
T    T|T|T|F
T    U|U|T|F
T    F|F|T|F
U    T|U|T|U
U    U|U|U|U
U    F|F|U|U
F    T|F|T|T
F    U|F|U|T
F    F|F|F|T

### 🔺视图 `VIEW`

#### 定义视图 `CREATE VIEW`

- `WITH CHECK OPTION` ：表示对视图进行 `INSERT、UPDATE、DELETE` 操作时要满足视图中**子查询的条件表达式**
- 视图的列名：必须**全部省略**或者**全部指定**。如果省略，则由 `SELECT` 子句目标列中的诸字段组成，必须指定列名的情况：
  - 某个目标列不是单纯的属性名，而是**聚集函数**或**表达式（虚拟列）**
  - 多表连接时选出了几个**同名列**作为视图的字段
  - 需要在视图中为某个列启用新的更合适的名字
- 若一个视图是由**单个基本表**导出，并且**只是去掉了基本的某些行和某些列**，但**保留了主码**，则称这类视图为 ***行列子集视图***
- 带有聚集函数和 `GROUP BY` 子句的查询定义的视图，也称为**分组视图**
- 视图可以定义在其他视图之上

```sql
CREATE VIEW IS_Student
AS
-- 子查询
SELECT Sno, Sname, Sage
FROM Student
WHERE Sdept = 'IS'
WITH CHECK OPTION
```

**带有聚集函数的情况：必须指定列名**

```sql
-- 建立一个视图，可以查询 20 届学生的平均分
CREATE VIEW S_G(Sno, Gavg)
AS
SELECT Sno, AVG(Grade)
FROM SC
GROUP BY Sno
HAVING Sno LIKE '2020%'
```

#### 视图查询 `SELECT`

对视图的查询实际上被转换为**对基本表的查询**。步骤为：先检查基本表、视图是否存在，若存在，先从**数据字典**中取出视图的定义，把定义中的子查询和用户的查询结合起来，转换成等价的对基本表的查询，然后再执行修正了的查询。这一转换过程称为**视图消解**。

多数关系数据库系统对**行列子集视图**均能进行正确转换，但对非行列子集视图则不一定。

#### 更新视图 `UPDATE`

由于视图是不存储数据的虚表，因此对视图的更新最终也转换为**对基本表的更新**。

一般而言，**行列子集视图**都是可以更新的，其他规定依数据库管理系统的不同而不同。

#### 删除视图 `DROP VIEW`

视图删除后视图的定义从**数据字典**中删除。如果该视图上还导出了其他视图，则使用 `CASCADE` 级联删除语句可以把改视图及由它导出的视图一起删除。

#### 视图的作用

- 简化用户操作
- 使用户能以多种角度看待同一数据
- 对重构数据库提供了一定程度的逻辑独立性
- 能够对机密数据提供安全保护
- 适当利用视图可以更清晰地表达查询