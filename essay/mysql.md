# mysql基础

## 什么是数据（data），数据库（database），数据库管理系统（dbms）

数据是描述一个东西的特征，比如一个物品的长宽高就是数据，它可以存放于表中。数据库是表的集合。数据库管理系统则是用于操作管理数据库的一套系统。

常见的数据库管理系统有： oracle、mysql、db2、sql server。



## sql语言

sql语言用于对数据库进行操作。它分为：DDL、DML、DCL、DQL。在不同的数据库管理系统中，相同的sql语言可占到90%。

### DDL

DDL(Date Definition Language)，即数据定义语言，它用来定义数据库对象：如数据库、表、列。

#### 定义数据库

*创建数据库*

```sql
#创建一个名字为test001的数据库
create DATABASE test001;
```

*使用数据库*

```sql
USE test001;
```

*删除数据库*

```sql
# 如果存在test001的数据库就删除
DROP DATABASE
IF EXISTS test001;
```

#### 定义表

##### 字段的常见类型

- int：整型
- bigint：长整型（java中的long） 
- double：浮点型，在sql中，通常用decimal代替double
- char：固定长度字符串
- varchar：可变长度字符串
- text：文本，字符流
- blob：字节流，文件、图片等（一般不使用）
- data：日期，格式 yy-MM-dd
- time：时间，格式 hh:mm:ss
- datatime：日期时间，格式 yy-MM-dd hh:mm:ss
- bit：用来表示bool型，通常性别用这个表示

##### 常用约束条件

- 主键约束：primary key
- 唯一约束：unique
- 非空约束：not null
- 默认约束：default
- 外键约束：foreign key
- 自动增长：auto_increment

*创建表*

```sql
CREATE TABLE test001 (
	id INT PRIMARY KEY auto_increment,
	sname VARCHAR (100) NOT NULL,
	phone BIGINT,
	tno INT UNIQUE
)
```

*删除表*

```sql
DROP TABLE
IF EXISTS test001;
```

#### 定义列

*添加列*

```sql
ALTER TABLE test001 ADD age INT  -- 添加一列字段为age，数据类型为int
```

*删除列*

```sql
ALTER TABLE test001 DROP age
```

*修改列*

```sql
ALTER TABLE test001 MODIFY tno VARCHAR (11);
```

### DML

DML(Data Maniplation Language)，即数据操作语言。对数据进行增删改。

#### 增

```sql
INSERT INTO test001 (sname,phone,tno)
VALUES('xxx',123456789,'123');
```

#### 删

```sql
DELETE FROM test001 WHERE id = 1; -- 把id为1的这条数据删除

DELETE FROM test001；	-- 把整张表一行一行地删除，可以被恢复

TRUNCATE TABLE test001;		-- 直接删除整张表，再新建一张名字一样的表，不可恢复
```

#### 改

```sql
UPDATE test001 set sname = 'xxx' WHERE id = 1
```

### DQL

DQL(Data Query Language)，即数据查询语言，它用于进行数据库的查询。

```sql
SELECT
	phone	-- 要查询的字段
FROM
	users	-- 表名
WHERE
	uid = 3;	-- 查询的条件
```

其中，要查询的字段可以包括内置函数，通常是统计函数，常用的统计函数入下：

- `count`：有几个
- `sum`
- `avg`
- `max`
- `min`
- `case`

可以用 as 为这些内置函数命名。

常见的查询条件如下：

- `=    !=    <=   >=`
- `is null     is not null`
- `like '表达式'`：%代表无限多个，_代表一个
- `between  and  `
- `and  or`

统计函数经常与分组（`group by`）一起使用。

```sql
SELECT
	(count(uid)) AS '数量',
	company,
	(SUM(weight)) AS '总体重',
	(AVG(weight)) AS '平均体重',
	(MAX(weight)) AS '最重',
	(MIN(weight)) AS '最轻'
FROM
	users
GROUP BY
	company
HAVING
	count(uid) = 2;  -- 分组之后的条件判断
```

`order by 字段` ：排序关键字，默认升序（asc），降序是 desc。 

`limit 从第几行开始,显示几行`：(页码-1)*显示几行 = 从第几行开始，用于分页。

### 常见的关系

#### 一对一

用外键约束

#### 一对多

用外键约束

#### 多对多

用第三张表过渡