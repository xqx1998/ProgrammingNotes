# 1 数据库的概念以及sql通用语法

- 开启mysql服务器：win+R打开运行窗口，输入“services.msc”即可，推荐都自动开启。
- 登录和退出mysql

```sql
登录本机的数据库：mysql -uroot -p管理员密码
登录别人的数据库: mysql -h别人的ip地址 -uroot -p管理员密码
退出数据：exit或者quit
```

- **登录的两个小异常（重要）**

  - 数据库密码不正确

  ![1.密码不正确.png](resource/1.密码不正确.png)

  - mysql服务没有打开

![2.mysql服务没有开启.png](resource/2.mysql服务没有开启.png)

- mysql数据目录

```sql
1 数据库：本质是文件夹，在开发中一个项目对应一个数据库。
		数据库管理系统和数据库的区别？数据库管理系统指的是我们安装mysql软件。数据库指的是存放数据文件的文件夹
2 表：本质是一个文件
3 数据
```

- SQL以及通用语法

```sql
sql:操作关系型数据库通用的语言。
通用的语法：
	1 sql语句是以分号结尾，不区分大小写。
	2 sql的注释：
			单行注释： -- 空格
			多行注释： /*注释内容*/
```

- SQL分类

```sql
1) Data Definition Language (DDL 数据定义语言) 对数据库和表进行增删改查
2) Data Manipulation Language(DML 数据操纵语言)，对表中的数据进行增删改
3) Data Query Language(DQL 数据查询语言)，对表中的数据进行查询操作
4) Data Control Language(DCL 数据控制语言)，对用户权限的设置
```

# 2 对数据库的增删改查(DDL)

## 2.1 创建数据库(重要)

- **语法：create database [if not exists] 数据库名称 [charater set utf8];**  — []中的内容表示可选。

```sql
-- 创建一个db1数据库，使用默认的字符集
create database db1;
-- 创建一个db2数据库，使用gbk字符集
create database db22 character set gbk;
create database if not exists db2 character set gbk;
```

## 2.2 删除数据库(重要)

- **语法：drop database [if exists] 数据库名称;**       — []中的内容表示可选。

```sql
-- 删除db1数据库
drop database db1;
-- 删除db2数据库，如果存在就删除
drop database if exists db2;
```

2.3 其他操作(了解)

```sql
查看所有数据库：show databases;
使用数据库：use 数据库名称;
```

# 3 对数据库中表的增删改查(DDL)

## 3.1 创建表(重点)

```sql
语法：
create table [if not exists] 表名(  -- []中的内容表示可选。
	列1 数据类型,
  列2 数据类型,
  ...
  列n 数据类型
);
注意：最后一列不需要逗号，写了反而出错。
数据类型:
	整型：int
	浮点型：float(总位数,小数位数) 、	double(总位数,小数位数)
	字符串类型：
		char(长度)：固定长度字符串，假设长度写的是8，不管存几个字符，那么在底层都是站8个字符的位置
		varchar(长度):可变长度字符串，假设长度写的是8，字符串的实际长度多长，存进去就占几个字符。
	时间类型：
		date：年月日时间类型
		datetime：年月日时分秒类型，如果不存值或者存null，那么值都是null。
		timestampt：年月日时分秒类型，如果不存值或者存null，那么值是系统当前时间。
例如：
	create table if not EXISTS student(
   id int,
   name VARCHAR(20),
   age int,
   score DOUBLE(5,2),
   birthday date,
   insert_time TIMESTAMP
  );
```

## 3.2 删除表(重要)

- 语法：drop table [if exists] 表名;

```SQL
-- 删除表
drop table if EXISTS student;
```

## 3.3 修改表(了解)

```sql
规律：alter table 表名 add/drop/change/modify/rename to
其中；
	修改列名：alter table 表名 change 旧列名 新列名 新数据类型(长度);
	修改数据类型：alter table 表名 modify 列名 新数据类型(长度);
```

## 3.4 数据库和表的总结

```sql
0 登陆数据库的两个异常：密码错误和mysql服务没有开启
1 创建数据库：create database ...
2 删除数据库：drop database ...
3 创建表：create table ...
4 删除表: drop table ...
```

# 4 对表中的数据增删改查(DML+DQL)(绝对的重点)

## 4.1 添加数据（重点）

```sql
语法：
给指定列添加一条数据：insert into 表名(列1,列2,...) values (值1,值2,...);
给所有列添加一条数据：insert into 表名 values (值1,值2,...);
一次性添加多条数据：insert into 表名 values (值1,值2,...),(值1,值2,...),(值1,值2,...),...;

例如：
-- 给指定列添加一条数据：添加 1号,张无忌,20岁
INSERT INTO stu(id,name,age) VALUES(1, '张无忌',20);
-- 给所有列添加一条数据：添加赵敏
INSERT INTO stu VALUES(2, '赵敏',18,80.5,'2001-2-21',null);
-- 一次性添加多条数据：添加张三崩，殷天正
INSERT INTO stu VALUES(3, '张三崩',88,100.0,'1921-2-21',null),(4, '殷天正',68,90.5,'1951-2-21',null);
```

**注意：**

**1 添加的值要和列一一对应(顺序和类型一致，且不能超出指定长度)。**

**2 除了数字类型，其他类型的数据都需要使用引号引起来。推荐单引号。**

## 4.2 删除数据（重点）

```sql
1、带条件删除
		delete from 表名 where 条件;
2、不带条件删除（删除所有数据）
		delete from 表名;  --DML语句，将表中的数据一条一条的删除
		删除所有数据还有另一种做法：TRUNCATE table 表名; -- DDL语句，将表删除，然后再创建一张一样的新表
```

## 4.3 修改数据（重点）

```sql
语法：
		不带条件修改：update 表名 set 列名=值,列=值,...;
		带条件修改：（大部分情况）update 表名 set 列名=值,列=值,... where 条件;
例如：
-- 将所有人的分数设置为100分
update stu set score=100;
-- 将所有人的分数在原来的基础上-10分
update stu set score=score-10;
-- 将赵敏的分数设置为92分,年龄设置为20
update stu set score=92,age=20 where name='赵敏';
```

## 4.4 查询数据（重点）

### 4.4.1 基础查询

```sql
-- 查询指定列数据
			select 列1,列2,... from 表名;
		-- 查询所有列数据
			select * from 表名; -- *表示所有列，是一个通配符
		-- 去重：distinct关键字
			-- 查询所有的学生来自哪些城市。
			select distinct address from student;
			注意：只有两行数据完全一样才可以去重。
		-- 计算
			注意：任何数据和null做运算，结果都是null，使用ifnull函数将为null的列的值转换为0进行计算。
			ifnull(列名,0)：如果该列的值为null，那么将他的值用0替代参与运算。
			-- 查询所有学生的总分
			select name,math,english,math+IFNULL(english,0) from student;
		-- 取别名： AS关键字可以省略
select name as 姓名,math as 数学,english as 英语,math+IFNULL(english,0) as 总分 from student;
select name 姓名,math 数学,english 英语,math+IFNULL(english,0) 总分 from student;
```

### 4.4.2 条件查询--基础的条件

```sql
语法：select * from 表名 where 条件;
比较运算符：>	>=	<	<= = !=/<>
逻辑运算符：&&/and	 ||/or	  推荐些英文单词
between 值1 and 值2 ：在值1和值2之间，包含值1和值2
in(值1,值2,...):表示等于值1或者等于值2或者...
is null / is not null
-- 查询年龄大于20岁
select * from student where age>20;
-- 查询年龄等于20岁
select * from student where age=20;
-- 查询年龄不等于20岁
select * from student where age!=20;
select * from student where age <> 20;
-- 查询年龄小于等于20或者大于等于30
select * from student where age <=20 or age>=30;-- 推荐
select * from student where age <=20 || age>=30;
-- 查询年龄大于等于20并且小于等于30
select * from student where age >=20 and age <=30;-- 推荐
select * from student where age >=20 && age <=30;
-- 使用between ... and... 
select * from student where age BETWEEN 20 and 30;

-- 查询年龄为22岁，18岁，25岁的信息
select * from student where age=22 or age=18 or age=25;
-- 使用in语句
select * from student where age in(22,18,25);
-- 查询年龄不是22岁，18岁，25岁的信息
select * from student where age not in(22,18,25);
-- 查询英语成绩为null
select * from student where english is null;
-- 查询英语成绩不为null
select * from student where english is not null;

```

### 4.4.3 条件查询--模糊查询

# 总结

```sql
1 mysql的概念以及sql语法
	1、数据库管理系统和数据库的区别？
		数据库管理系统值的是mysql软件，数据库的本质是一个个文件夹，一个项目对应一个数据库。
	2、登录数据库的两个异常？（重点）
		密码不正确		mysql服务没有开启
  3、SQL：操作关系型数据库的语言。
  		SQL语句以分号结尾，不区分大小写。
  		SQL中的注释：
  			单行注释：-- 空格
  			多行注释：/*注释内容*/
2 DDL-对数据库进行CRUD操作
	1、创建数据库（重点）
		create database 数据库名称;
		create database 数据库名称 character set utf8;
		create database if not exists 数据库名称 character set utf8;
	2、删除数据库（重点）
		drop database 数据库名称;
		drop database if exists 数据库名称;
	3、其他操作
		查看所有数据库:show databases;
		使用数据库:use 数据库名称;
		
3 DDL-对数据库中表进行CRUD操作
	1、创建表（重点）
		create table [if not exists] 表名(
    	列1 数据类型,
      列2 数据类型,
      ...
      列n 数据类型
    );
    注意：最后一列不能写逗号，否则执行出错。
    常见的数据类型：
    	int	、float(总位数,小数位数)/double(总位数,小数位数)、 char(长度)/varchar(长度)、 date/datetime/timestamp
	2、删除表（重点）
		drop table [if exists] 表名;
	3、修改表(了解)
		规律：alter table 表名 add/drop/change/modify/rename to ...
		修改列名：alter table 表名 change 旧列名 新列名 新数据类型(长度);
		修改数据类型：alter table 表名 modify 列名 新数据类型(长度);
4 DML+DQL-对表中的数据进行CRUD操作（重点）
	1、添加数据
		给指定列添加一条数据：
			insert into 表名(列1,列2,...) values (值1,值2,...);
		给所有列添加一条数据：
			insert into 表名 values (值1,值2,...);
		给所有列添加多条数据：
			insert into 表名 values (值1,值2,...),(值1,值2,...),...;
		注意：
			1、值和列要一一对应(顺序和类型要一样)
			2、除了数字类型，其他类型的数据都需要使用引号引起来。推荐单引号。
	2、删除数据(一般都是带条件)
		带条件：delete from 表名 where 条件;（重要）
		不带条件：删除所有数据
			delete from 表名;-- 一条一条的删除，属于DML语句
			truncate table 表名;-- 先删除表，后创建一张一样的表，属于DDL语句
	3、修改数据
		带条件：update 表名 set 列1=值1,列2=值2,... where 条件; (重要)
		不带条件：修改该列的所有数据。
			update 表名 set 列1=值1,列2=值2,...;
	4、基础查询
		-- 查询指定列数据
			select 列1,列2,... from 表名;
		-- 查询所有列数据
			select * from 表名; -- *表示所有列，是一个通配符
		-- 去重：distinct关键字
			-- 查询所有的学生来自哪些城市。
			select distinct address from student;
			注意：只有两行数据完全一样才可以去重。
		-- 计算
			注意：任何数据和null做运算，结果都是null，使用ifnull函数将为null的列的值转换为0进行计算。
			ifnull(列名,0)：如果该列的值为null，那么将他的值用0替代参与运算。
			-- 查询所有学生的总分
			select name,math,english,math+IFNULL(english,0) from student;
		-- 取别名： AS关键字可以省略
select name as 姓名,math as 数学,english as 英语,math+IFNULL(english,0) as 总分 from student;
select name 姓名,math 数学,english 英语,math+IFNULL(english,0) 总分 from student;
	5、基础条件查询
```

