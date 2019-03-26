## 连接查询

### 概念

把两张表连起来查。如果在一张表中没有全部客户需要的查询结果，这时候我们可以把两个表或者多个表连起来查询。在互联网开发中一般不使用join，因为会降低性能。

### 种类

连接查询分为内连接（inner join）和外连接（outer join）。内连接，必须两个表都有记录，结果才会显示出来。外连接则是可以用`left join`或者`right join`定义一个主表，主表有的都会显示出来。

### 使用

如果我们直接把两张表直接连起来查，会得到一个笛卡尔积，即每条记录都会连接起来，这时候我们需要用`ON`来剔除我们不需要的数据。

```sql
SELECT province.pid,province.pname,COUNT(city.cid) AS '城市数量'
FROM province LEFT JOIN city 
ON city.pid=province.pid
GROUP BY pid
ORDER BY COUNT(city.cid) DESC;
```

## 子查询

### 定义

子查询也叫嵌套查询，即在`select` 中使用`select`。它通常在两个地方使用，一个是`from`之后，一个是`where`之后。

### 使用

*在where之后使用*：

```sql
SELECT
	*
FROM
	student_test
WHERE
	cid = 1
# 当分数大于查询出来的分数时
AND score > (
	SELECT
		score
	FROM
		student_test
	WHERE
		sid = 1
	AND cid = 1
)
GROUP BY
	cid;
```

*在from之后使用*

```sql
SELECT
	student.sid,
	student.sname,
	(
		CASE
		WHEN ywcj.score IS NULL THEN
			0
		ELSE
			ywcj.score
		END
	) AS '语文',
	(
		CASE
		WHEN sxcj.score IS NULL THEN
			0
		ELSE
			sxcj.score
		END
	) AS '数学',
	(
		CASE
		WHEN yycj.score IS NULL THEN
			0
		ELSE
			yycj.score
		END
	) AS '英语'
FROM
	student
LEFT JOIN (
	SELECT
		sid,
		score
	FROM
		student_test
	WHERE
		cid = 1
) AS ywcj ON student.sid = ywcj.sid
LEFT JOIN (
	SELECT
		sid,
		score
	FROM
		student_test
	WHERE
		cid = 2
) AS sxcj ON student.sid = sxcj.sid
LEFT JOIN (
	SELECT
		sid,
		score
	FROM
		student_test
	WHERE
		cid = 3
) AS yycj ON student.sid = yycj.sid
```

### 行转列

行转列是一种子查询，它实现方法是先分别查出你需要的部分的数据，再拼起来。

```sql
SELECT
	product.pid,
	product.pname,
	a.num AS '一月'
FROM
	product
LEFT JOIN (
	SELECT
		pid,
		SUM(num) AS num
	FROM
		orders
	WHERE
		odate BETWEEN '2019-01-01 00:00:00'
	AND '2019-01-31 23:59:59'
	GROUP BY
		pid
) AS a ON product.pid = a.pid
```

## DCL

DCL（Data Control Language）,数据控制语言，它用于对用户进行操作。在实际开发中，一般都会给项目组分配单独数据库用户，一般这个用户权限是自己的数据库。

#### 创建用户

```sql
# create user '用户名'@'主机名' identified by '密码'
CREATE USER 'xlk'@'localhost' IDENTIFIED BY '123'
```

#### 给用户赋予权限

```sql

GRANT ALL ON bank.* TO 'xlk'@'%'  -- *代表所有文件
```

#### 撤销用户权限

```sql
REVOKE ALL ON bank.* FROM 'xlk'@'%' -- all代表所有权限
```

#### 查看权限

```sql
SHOW GRANTS FOR 'xlk'@'%'
```



#### 删除用户

```sql
DEOP USER 'xlk'@'%' --%代表哪里都可以连接
```

### statement和preparedstatement的区别

#### 相同点

都能操作数据库

#### 不同点

- 前者直接拼接sql语句，后者sql语句有占位符
- 后者的可读性强
- 后者的执行性能强，它执行过的sql语句，会被缓存起来
- 后者安全，可防止sql注入攻击

