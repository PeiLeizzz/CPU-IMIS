---
title: 数据库原理——触发器与过程化 SQL
date: 2020-06-29 16:56:10
tags: 
- Database
- SQL
categories:
- Database
toc: true
reward: true

---

## 标准 `SQL` 语言

### 触发器

> 关于触发器的概念请看[这里](http://peilei722.gitee.io/lazyswifts/CPU/Database/%E6%95%B0%E6%8D%AE%E5%BA%93%E5%8E%9F%E7%90%86%E2%80%94%E2%80%94%E6%95%B0%E6%8D%AE%E5%BA%93%E5%AE%8C%E6%95%B4%E6%80%A7.html)

**创建触发器 `CREATE TRIGGER`：**

```sql
CREATE TRIGGER <trigger_name>
{BEFORE|AFTER} <事件名(SELECT/UPDATE...)>
ON <table_name>
REFERENCING NEW|OLD ROW AS <变量名>
FOR EACH {ROW|STATEMENT}
[WHEN<condition>] <condition>
/* 触发器中也可以使用 SQL 过程语句 */
```

:::tip
例子：定义一个 `BEFORE` 行级触发器，为教师表 `Teacher` 定义完整性规则“教授的工资不得低于 `4000` 元，如果低于 `4000` 元，则自动改为 `4000` 元
```sql
CREATE TRIGGER Insert_Or_Update_Sal
BEFORE INSERT OR UPDATE
ON TEACHER
FOR EACH ROW
BEGIN
	IF (newtuple.Job = '教授') AND (newtuple.Sal < 4000) THEN
    	newtuple.Sal := 4000;
    END IF;
END;
```
:::

**删除触发器 `DROP TRIGGER`：**

```sql
DROP TRIGGER <trigger_name> ON <table_name>
```

### 变量和常量的定义

- **变量定义**

  `变量名 数据类型 [[NOT NULL] := 初值表达式]` 或

  `变量名 数据类型 [[NOT NULL] 初值表达式`

- **常量定义 `CONSTANT`**

  `常量名 数据类型 CONSTANT := 常量表达式`

  常量**必须要给一个值**，并且该值**存在期间或常量的作用域内不能修改**

- **赋值语句 `:=`**

  `变量名称 := 表达式`

### 流程控制

**条件控制语句：**

- **`IF-THEN` 语句**

  ```sql
  IF condition THEN
  	Sequence_of_statements;
  END IF;
  ```

- **`IF-THEN-ELSE` 语句**

  ```sql
  IF condition THEN
  	Sequence_of_statements1;
  ELSE
  	Sequence_of_statements2;
  END IF;
  ```

- `THEN` 和 `ELSE` 子句中还可以嵌套 `IF` 语句

**循环控制语句：**

- **`LOOP` 语句（死循环）**

  ```sql
  LOOP
  	Sequenece_of_statements;
  END LOOP;
  ```

- **`WHILE-LOOP` 语句**

  ```sql
  WHILE condition LOOP
  	Sequence_of_statements;
  END LOOP;
  ```

- **`FOR-LOOP` 语句**

  ```sql
  FOR count IN [REVERSE] bound1 .. bound2 LOOP
  	Sequence_of_statements;
  END LOOP;
  ```

### 存储过程

:::tip
持久性存储模块（`PSM`），可以被反复调用，被编译后保存在数据库中，运行速度较快，**过程和函数**是命名块。
:::

**存储过程的优点**：

- 运行效率高
- 降低了客户机和服务器之间的通信量
- 方便实施企业规划

**创建存储过程：`CREATE OR REPLACE PROCEDURE`**

```sql
CREATE OR REPLACE PROCEDURE procedure_name([param1, param2, ...])
AS <过程化 SQL 块>
```

:::tip
**参数列表**：用名字来标识调用时给出的参数值，**必须指定值的数据类型**。可以定义输入参数、输出参数或输入/输出参数，默认为输入参数，也可以无参数
:::

:::tip
例子：从账户 1 转指定数额的款项到账户 2 中，假设账户关系表为 `Account(Accountnum, Total)`

```sql
/* 定义存储过程 TRANSFER 其参数为转入账户、转出账户、转账额度 */
CREATE OR REPLACE PROCEDURE TRANSFER(inAccount INT, outAccount INT, amount, FLOAT)
/* 定义变量 */
AS DECLARE  
	totalDepositOut FLOAT;
	totalDepositIn FLOAT;
	inAccountnum INT;
BEGIN  
	/* 检查转出账户的余额 */
	SELECT Total 
	INTO totalDepositOut
	FROM Account
	WHERE Accountnum = outAccount;
	/* 如果转出账户不存在或者账户中没有存款 */
	IF totalDepositOut IS NULL THEN
		/* 回滚事务 */
		ROLLBACK;  
		RETURN;
	END IF;
	/* 如果转出账户中余额不足 */
	IF totalDepositOut < amount THEN
		ROLLBACK;
		RETURN;
	END IF;
	/* 检查转入账户是否存在 */
	SELECT Accountnum
	INTO inAccountnum
	FROM Account
	WHERE Accountnum = inAccount;
	IF inAccountnum IS NULL THEN
		ROLLBACK;
		RETURN;
	END IF;
	/* 此时可以正常转账，更新双方余额 */
	UPDATE Account
	SET Total = Total - amount
	WHERE Accountnum = outAccount;
	
	UPDATE Account
	SET Total = Total + amount
	WHERE Accountnum = inAccount;
	/* 提交事务 */
	COMMIT;  
END;
```
:::

**执行存储过程：`CALL/PERFORM PROCEDURE`**

```sql
CALL/PERFORM PROCEDURE procedure_name([param1, param2, ...]);
```

:::tip
例子：从账户 123 转 100 元到账户 456 中
```sql
CALL PROCEDURE TRANSFER(456, 123, 100);
```
:::

**修改存储过程： `ALTER PROCEDURE`**

- 重命名

  ```sql
  ALTER PROCEDURE procedure_name1 RENAME TO procedure_name2;
  ```

- 重新编译

  ```sql
  ALTER PROCEDURE procedure_name COMPILE;
  ```

**删除存储过程：`DROP PROCEDURE`**

```sql
DROP PROCEDURE procedure_name();
```

### 函数

:::tip
函数和存储过程的异同：
- 同：都是持久性存储模块
- 异：**函数必须指定返回值的类型**
:::

**定义函数： `CREATE OR REPLACE FUNCTION`**

```sql
CREATE OR REPLACE FUNCTION function_name([parma1, param2, ...])
RETURNS <param_type>
AS <过程化 SQL 块>
```

**执行函数：`CALL/SELECT function_name([param1, param2, ...])`**

**修改函数：`ALTER FUNCTION`**

- 重命名

  ```sql
  ALTER FUNCTION function_name1 RANEME TO function_name2;
  ```

- 重新编译

  ```sql
  ALTER FUNCTION function_name COMPILE;
  ```

## `SQL Server` 

:::tip
**查看存储过程的文本信息**：`EXEC SP_HELPTEXT <procedure_name>`
:::

:::tip
三种自定义函数：

- 标量值函数返回的是一个数据类型值
- 内联表值函数返回的是一个 `TABLE`
- 多语句表值函数返回的是一个 `TABLE` 的变量（类似前面两个的结合）

语法的结构：
- 标量值函数和多语句表值函数都是要有 `BEGIN...END`，内联表值函数就没有      

调用：
- 标量函数要写成在 `dbo.function_name`
:::

例一（**存储过程**）：创建存储过程 `cjjicx`，该存储过程的作用是：当任意输入一个学生的姓名时，将从三个表中返回该学生的学号、选修的课程名称和成绩

```sql
USE DBS
GO
CREATE PROCEDURE Cjjicx(
	@name CHAR(20)
)
AS
BEGIN
	SELECT Student.Sno, Course.Cname, SC.Grade
	FROM Student
	LEFT JOIN SC
	ON Student.Sno = SC.Sno
	LEFT JOIN Course
	ON SC.Cno = Course.Cno
	WHERE Sname = @name
END
RETURN
GO
```

执行：`EXEC Cjjicx @name='张三'`

上例的**函数**实现：

```sql
USE DBS
GO
CREATE FUNCTION Cjjicx2(
	@name CHAR(20)
)
RETURNS TABLE
AS
/* 内联表值函数只有一句完整的 RETURN */
RETURN (
	SELECT Student.Sno, Course.Cname, SC.Grade
	FROM Student
	LEFT JOIN SC
	ON Student.Sno = SC.Sno
	LEFT JOIN Course
	ON SC.Cno = Course.Cno
	WHERE Student.Sname = @name
)
```

**内联表格值函数**执行：`SELECT * FROM Cjjicx('张三')`

---

例二（**存储过程**）：

创建一个加密的存储过程 `Jmxs`，当执行该存储过程时，返回 `IS` 系学生的所有信息

```sql
USE DBS
GO
CREATE PROCEDURE Jmxs
WITH ENCRYPTION
AS
BEGIN
	SELECT *
	FROM Student
	WHERE Sdept = 'IS'
END
RETURN
GO
```

:::tip
`SQL Server` 中**存储过程加密的关键字为 `WITH ENCRYPTION`**
:::

删除该存储过程：`DROP PROCEDURE Jmxs`

---

例三（**存储过程**）：

创建一个名为 `Find_Grade` 的存储过程，该存储过程的作用是：如果 `Sname` 和 `Cname`，返回对应的 `Grade`，假设 `Sname` 和 `Cname` 都唯一

```sql
USE DBS
GO
CREATE PROCEDURE find_Grade(
	@StuName CHAR(10),
	@ClaName CHAR(10)
)
AS
BEGIN
	DECLARE @StuNo CHAR(10),
		    @ClaNo CHAR(10)

	SELECT @StuNo = Sno
	FROM Student
	WHERE Sname = @StuName
	
	SELECT @ClaNo = Cno
	FROM Course 
	WHERE Cname = @ClaName

	SELECT Grade
	FROM SC
	WHERE Sno = @StuNo
	AND Cno = @ClaNo
END
RETURN
GO
```

上例**函数**实现：

```sql
USE DBS
GO
CREATE FUNCTION dbo.Test2(
	@StuName CHAR(10),
	@ClaName CHAR(10)
)
RETURNS SMALLINT
AS
BEGIN
	DECLARE @StuNo CHAR(10),
		    @ClaNo CHAR(10),
			@res SMALLINT

	SELECT @StuNo = Sno
	FROM Student
	WHERE Sname = @StuName
	
	SELECT @ClaNo = Cno
	FROM Course 
	WHERE Cname = @ClaName

	SELECT @res = Grade
	FROM SC
	WHERE Sno = @StuNo
	AND Cno = @ClaNo

	RETURN @res
END
GO
```

**标量函数的执行**：`SELECT dbo.Test2('张三', '高等数学') grade`

---

例四（**触发器**）：

建立一个名为 `Insert_xh` 的 `INSERT` 触发器，存储在 `SC` 表中，该触发器的作用是：当用户向 `SC` 表中插入记录时，如果插入了在 `Student` 表中没有的学生学号 `Sno`，则题时用户不能插入记录，否则提示记录插入成功

```sql
USE DBS
GO
CREATE TRIGGER Insert_xh
ON SC
FOR INSERT
AS
BEGIN
	IF ((SELECT INS.Sno FROM INSERTED INS) NOT IN (SELECT Sno FROM Student))
		BEGIN
		PRINT '不能插入记录'
		ROLLBACK
		END
	ELSE
		PRINT '插入记录成功'
END
GO
```

:::danger
`SQL Server` 的 `IF-ELSE` 语句块中若语句不止一句，**需要用 `BEGIN-END` 语句块包裹**
:::

---

例五（**触发器**）：

为 `Student` 表创建一个名为 `Dele_stu` 的 `DELETE` 触发器，该触发器的作用是：禁止删除 `Student` 表中的记录

```sql
USE DBS
GO
CREATE TRIGGER Dele_stu
ON Student
FOR DELETE
AS
BEGIN
	PRINT '禁止删除'
	ROLLBACK
END
GO
```

---

例六（触发器）：

为 `SC` 表创建一个名为 `Update_grade` 的 `UPDATE` 触发器，该触发器的作用是禁止更新 `SC` 表中的 `Grade` 字段的内容

```sql
USE DBS
GO
CREATE TRIGGER Update_grade
ON SC
FOR UPDATE
AS
BEGIN
	IF (COLUMNS_UPDATED() & 04) > 0
		BEGIN
		PRINT '禁止更新'
		ROLLBACK
		END
END
GO
```

:::tip
`SQL Server` 中 `COLUMNS_UPDATED()` 函数的用法，以上面的题为例，在 `SC` 表中，有三个属性，从左到右分别为 `Sno, Cno, Grade`。
`&` 符号是**按位与运算**的意思，`04` 是一个**十六进制数**，转换为二进制就是 `0000 0100`，`0` 代表该位置的属性没有被修改，`1` 代表被修改了。这里的左右顺序与表中属性的**左右顺序相反**，对应关系如下：

```
0 0 0 0 0   1    0   0 
↑ ↑ ↑ ↑ ↑   ↑    ↑   ↑
- - - - - Grade Cno Sno
```
也即，如果要限制 `Cno` 列属性不能被 `UPDATE`，只需要将语句改为：`IF(COLUMNS_UPDATED() & 02) > 0`，`02` 就是 `0000 0010` 的十六进制
:::

**禁用**该触发器：`DISABLE TRIGGER update_grade ON SC`

删除该触发器：`DROP TRIGGER update_grade`

