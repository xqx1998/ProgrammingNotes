## 昨天内容回顾

```sql
1 单表查询
	条件查询-模糊查询
		关键字：where 列名 like '带占位符的字符串'
	排序查询
		关键字：order by 列名 asc/desc,列名 asc/desc,...
	聚合函数
		常见的聚合函数：sum(列名)、max(列名)、min(列名)、count(列名)、avg(列名)
	分组查询
		关键字：group by 列名 [having 条件]
		字眼：每类、每个、每种、按照...进行统计
	分页查询
		关键字：limit 每页起始索引,每页条数;
	总结：
		select * from 表名 [where 条件] [group by 列名] [having 条件] [order by 列名 asc/desc,列名 asc/desc,...] [limit 每页起始索引,每页条数]
2 约束：
	单表约束
		主键约束：
		非空约束：
		唯一约束：
	多表约束：
		外键约束：
	掌握在创建表时添加单表约束：
			create table 表名(
         id int  primary key auto_increment,
         name varchar(20) not null,
         phone varchar(11) unique,
         dept_id int not null
         -- primary key(id,name)
         -- 了解：foreign key(dept_id) references 主键表(id)
      );
3 多表关系以及键表原则
	一对一：在任意一方添加一个唯一外键(在外键列再添加一个unique约束)指向另一方的主键。
	一对多：在多的一方添加外键指向一的一方的主键
	多对多：创建一张中间表，在中间表中至少有两个字段分别指向多对多双方的主键。
```

## 1.多表查询(重点)

### 1.1 多表查询的分类

```sql
1 连接查询
	交叉连接：将一个表中的每一条数据和另一张表的每一条数据无条件连接。（了解）
		语法1：select * from 表1 cross join 表2 [cross join 表3]...
		语法2：select * from 表1,表2,...;
	  结果集：笛卡尔积：如果表1有3条数据，表2有5条数据，那么交叉连接的结果有15条数据。
	内连接：（重点）
	外连接：（重点）
2 子查询：（重点）
```

### 1.2 内连接(重点1)

```sql
1.语法：
	显示内连接：
		select * from 表1 inner join 表2 on 关联条件 [inner join 表3 on 关联条件] [where 其他条件]   -- inner可以省略
		关联条件：一般是主外键关联。
	隐式内连接：(重点)
		select * from 表1,表2,表3,...where 关联条件和其他条件;
2.案例
-- 查询所有员工信息以及所在部门信息
SELECT e.*,d.`NAME` FROM emp e INNER JOIN dept d ON e.`dept_id`=d.`id`;
SELECT e.*,d.`NAME` FROM emp e JOIN dept d ON e.`dept_id`=d.`id`;

SELECT e.*,d.`NAME` FROM emp e,dept d WHERE e.`dept_id`=d.`id`;

-- 查询1号部门和2部门的所有员工信息以及所在部门信息
SELECT e.*,d.`NAME` FROM emp e INNER JOIN dept d ON e.`dept_id`=d.`id` WHERE d.`id` IN(1,2);
SELECT e.*,d.`NAME` FROM emp e JOIN dept d ON e.`dept_id`=d.`id` WHERE d.`id` IN(1,2);

SELECT e.*,d.`NAME` FROM emp e,dept d WHERE e.`dept_id`=d.`id` AND d.`id` IN(1,2);

3.内连接结果集特点：
	只能获取到两个表的交集部分，也就是能够使用主外键连接的数据。
```

### 1.3 外连接(重点2)

```sql
1.语法：
		左外连接：select * from 表1 left outer join 表2 on 关联条件 [where 其他条件];
		右外连接：select * from 表1 right outer join 表2 on 关联条件 [where 其他条件];
2.案例：
-- 外连接
-- 查询没有员工的部门信息
SELECT d.* FROM dept d LEFT OUTER JOIN emp e ON d.`id`=e.`dept_id` WHERE e.`id` IS NULL;
SELECT d.* FROM emp e RIGHT OUTER JOIN dept d ON d.`id`=e.`dept_id` WHERE e.`id` IS NULL;

-- 统计每个部门的员工数量，没有员工的部门显示0；
SELECT 
	d.`NAME`,COUNT(e.`dept_id`) 员工数量 
FROM 
	dept d LEFT OUTER JOIN emp e ON d.`id`=e.`dept_id` 
GROUP BY d.`NAME`;

3.外连接结果集特点：
	左外连接：可以获取左表的全部数据以及和右表的交集部分。
	左外连接：可以获取右表的全部数据以及和左表的交集部分。
```

### 1.4 子查询(重点3)

#### 1.4.1 子查询概念和分类

```sql
概念：一个查询中嵌套另一个查询，这种查询就叫做子查询。
子查询分类：
	简单的标准的查询语句：select * from 表名 where 条件;
	where子查询：有两种情况，将一个查询结果作为另一个查询的条件
	from子查询：有一种情况，将一个查询结果作为表和其他表连接查询。
	select子查询：（补充）将一个查询结果作为另一个查询的结果
```

#### 1.4.2 where子查询

```sql
1.什么情况下使用where子查询
	1 如果子查询结果是单行单列，那么可以考虑where子查询。因为比较运算符后面只能有一个值。
	2 如果子查询结果是多行单列，那么可以考虑where子查询。因为in()中可以使用多个值。
	总结：子查询结果一定是单列的情况。如果是多列的情况，那么只能使用from子查询。
2.子查询的结果是单行单列，因为比较运算符后面只能是一个值
-- 查询工资大于平均工资的员工信息
	-- 查询员工的平均工资
   SELECT AVG(salary) FROM emp;
   -- 查询工资大于6000的员工信息
   SELECT * FROM emp WHERE salary > 5860;
   -- 综合一下
   SELECT * FROM emp WHERE salary > (SELECT AVG(salary) FROM emp);
3.多行单列，也叫做带in的子查询
-- 查询"开发部"和"市场部"的员工信息
  -- 连接查询
  SELECT * FROM emp e,dept d WHERE e.`dept_id`=d.`id` AND d.`NAME` IN('开发部','市场部');
  -- 子查询
  -- 查询"开发部"和"市场部"的部门id
  SELECT id FROM dept WHERE NAME IN('开发部','市场部');
  -- 查询1部门和2号部门的员工信息
  SELECT * FROM emp WHERE dept_id IN(1,2);
  -- 综合一下
  SELECT * FROM emp WHERE dept_id IN(SELECT id FROM dept WHERE NAME IN('开发部','市场部'));
```

#### 1.4.3 from子查询

```sql
1.什么情况下使用from子查询
	如果子查询是多列的情况，那么只能使用from子查询
-- 查询"开发部"和"市场部"的员工信息和部门信息
  -- 连接查询
  SELECT * FROM emp e,dept d WHERE e.`dept_id`=d.`id` AND d.`NAME` IN('开发部','市场部');
  -- 子查询：
  -- 查询查询"开发部"和"市场部"的部门信息
  SELECT * FROM dept WHERE NAME IN('开发部','市场部');
  -- 将上面的查询结果和emp表连接查询
  SELECT
   * 
  FROM 
     emp e,(SELECT * FROM dept WHERE NAME IN('开发部','市场部')) d 
  WHERE e.`dept_id`=d.id;
  
  -- 查询最高工资和最低工资的员工信息
  -- 查询最高工资和最低工资
  SELECT MAX(salary),MIN(salary) FROM emp;
  -- 查询工作等于9000或者3600的员工信息
  SELECT * FROM emp WHERE salary IN(9000,3600);
  -- 综合
  SELECT 
	   e.* 
  FROM emp e,(SELECT MAX(salary) max_sal,MIN(salary) min_sal FROM emp) sal 
  WHERE salary IN(sal.max_sal,sal.min_sal)

```

#### 1.4.4 select子查询(了解)

```sql
使用条件：子查询结果只能是单行单列的
-- select 子查询
-- 连接查询
SELECT e.*,d.name FROM emp e,dept d WHERE e.`dept_id`=d.id;
-- 子查询 将查询到的每条员工信息的部门id从部门表中查询部门名称
SELECT *,(SELECT NAME FROM dept WHERE id=dept_id) 部门名称 FROM emp;
```

### 1.5 多表查询总结

​	 对于一个需求，首先分析需要查询的结果来自于哪些表，如果这些表都是原始表，那么就可以考虑连接查询。如果需要查询一个表的全部数据，那么就使用外连接，否则可以考虑使用内连接。如果查询条件不是直接给出的，而是需要通过查询得到，那么就使用子查询。

## 2.事务以及事务隔离级别

### 2.1 事务的概念(掌握)

如果一组sql操作要么同时成功要么同时失败，那么就需要统一管理，这就是事务管理。

### 2.2 如何使用事务(开启事务，提交事务,回滚事务)

```sql
开启事务：start trunsation;
提交事务：commit;
回滚事务：rollback;
```

说明：对于mysql数据库，每一条DML语句都是在一个单独的事务中执行，**如果没有使用START TRANSACTION开启事务，那么事务默认是提交**，如果使用了TART TRANSACTION开启事务，那么就必须手动提交或者回滚。对于oracle数据库，每一条DML语句都是在一个单独的事务中执行，但是都必须手动提交或者回滚。

### 2.3 事务的4大特征(了解)

```sql
原子性：一组sql操作要么同时成功要么同时失败。
持久性：事务一旦提交或者回滚，那么就将数据永久性的保存到数据库中。
隔离性：事务与事务之间本不应该相互影响。
一致性：事务前后，数据的总量不会发生变化。

其他错误说法：完整性、有效性...
```

### 2.4 事务的隔离级别(了解)

```sql
1. read uncommitted：读未提交
	产生的问题：脏读、不可重复读、幻读
2. read committed：读已提交 （Oracle）
	产生的问题：不可重复读、幻读
3. repeatable read：可重复读 （MySQL默认）
	产生的问题：幻读
4. serializable：串行化
	可以解决所有的问题
数据库查询隔离级别：
	select @@tx_isolation;
数据库设置隔离级别：
	set global transaction isolation level 级别字符串;
```

**注意：隔离级别从小到大安全性越来越高，但是效率越来越低**

## 3.DCL-用户管理及授权

### 3.1 修改root用户密码（了解）

```sql
1. 需要管理员运行cmd -- > net stop mysql 停止mysql服务
2. 使用无验证方式启动mysql服务： mysqld --skip-grant-tables
3. 打开新的cmd窗口,直接输入mysql命令，敲回车。就可以登录成功
4. use mysql;
5. update user set password = password('你的新密码') where user = 'root';
6. 关闭两个窗口
7. 打开任务管理器，手动结束mysqld.exe 的进程
8. 启动mysql服务
9. 使用新密码登录
```

### 3.2 用户管理以及授权（了解）

```sql
1.查询用户
    USE myql;
    select * from user;
2.创建用户
    语法：CREATE USER '用户名'@'主机名' IDENTIFIED BY '密码';
    例如：create user 'zhangsan'@'%' IDENTIFIED BY '123456';
3.删除用户
    语法：DROP USER '用户名'@'主机名';
    例如：drop user 'zhangsan'@'%';
4.查看权限
    语法：SHOW GRANTS FOR '用户名'@'主机名';
    例如：SHOW GRANTS FOR 'zhangsan'@'%';
5.授予权限
    语法：grant 权限列表 on 数据库名.表名 to '用户名'@'主机名';
    例如：GRANT ALL ON *.* TO 'zhangsan'@'%';
6.撤销权限
    语法：revoke 权限列表 on 数据库名.表名 from '用户名'@'主机名';
    例如：REVOKE UPDATE ON db3.`account` FROM 'zhangsan'@'%';
```

## 4 总结

```sql
1 多表查询（重点）
	连接查询：
		交叉连接：将两个表中的数据无条件连接，得到的结果是笛卡尔积。（了解）
			语法1:select * from 表1 cross join 表2；
			语法2:select * from 表1,表2;
			
		内连接：得到的结果是两个表的交集部分（能够使用主外键连接的数据）。（重点）
			显示内连接：select * from 表1 [inner] join 表2 on 关联条件 [where 其他条件];
			隐式内连接：select * from 表1,表2 where 条件; -- 常用
		外连接：得到的结果是一个表的全部数据以及和另一张表的交集部分。
			左外连接：select * from 表1 left [outer] join 表2 on 关联条件 [where 其他条件];
			右外连接：select * from 表1 right [outer] join 表2 on 关联条件 [where 其他条件];
	子查询：
		where子查询：将一个查询结果作为另一个查询条件。
				两种情况：子查询结果是单行单列或者多行单列。总之是单列的。如果是多行单列，那么条件使用in()
		from子查询：将一个查询结果作为表和其他表连接查询。
			  一种情况：子查询结果一般是多列
		select子查询：将一个查询结果作为另一个查询的结果，
				一种情况：子查询结果是单行单列。
2 事务管理
	概念：一组sql操作要么同时成功要么同时失败，那么就需要统一管理，这就是事务管理。
	四大特征：原子性、持久性、隔离性、一致性。
```

