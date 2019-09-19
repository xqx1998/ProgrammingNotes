# 昨天内容回顾

```java
1、JDBCUtils工具类核心代码
	static{
		//加载properties配置文件
	  //1 创建Properties对象
	  	Properties properties=new Properties();
	  //2 调用load方法加载
	  InputStream is=JDBCUtils.class.getClassLoader().getResourceAsStream("jdbc.properties");
	  properties.load(is)
	  //3 调用getProperty方法根据key获取value
	  String driver=properties.getProperty("driver");
	  ...
	  //注册驱动
	  Class.forName(driver);
	}
2、使用PreparedStatement对象完成增删改查的思路
	增删改：
		1、使用工具类获取连接
		2、获取PreparedStatement对象，预编译sql语句
		3、如果sql语句中有？占位符，就设置参数。
		4、调用executeUpdate()获取影响的行数。
		5、处理结果
		6、释放资源
	查询：
		1、使用工具类获取连接
		2、获取PreparedStatement对象，预编译sql语句
		3、如果sql语句中有？占位符，就设置参数。
		4、调用executeQuery()获取结果集ResultSet对象。
		5、遍历结果集，获取结果
		6、释放资源
3、事务管理
	1、获取连接之后开启事务：conn.setAutoCommit(false);
	2、一组sql操作完成之后提交事务：conn.commit();
  3、出现任何异常，在catch中回滚事务：conn.rollback();
	注意：事务管理要捕获最大的异常，也就是Exception异常。
```

# 1.JDBC连接池

## 1.1 概念和实现

- 概念

  就是 一个装有Connection对象的容器，需要连接对象的时候就从容器中获取，使用完毕之后自动归还。

- 实现

```java
1、所有的连接池对象都必须实现DataSource接口。
2、所有连接池都有获取连接的方法：getConnection()；而且调用从连接池获取连接的close()方法不是关闭连接，而是归还连接。
```

## 1.2 c3p0连接池(了解)

- 使用步骤

```java
1、导入开发jar包：c3p0-0.9.5.2.jar和mchange-commons-java-0.2.12.jar，不要忘了mysql驱动jar包。
2、创建src目录下配置文件：c3p0.properties或者c3p0-config.xml(复制资料中的即可)
3、创建连接池对象
4、获取连接
```

- **注意事项**

```java
1、配置文件必须放在src目录中(类路径下)
2、配置文件的名称必须是：c3p0.properties或者c3p0-config.xml
```

- 补充：使用属性文件代替xml文件(了解)

```properties
# 驱动全类名
c3p0.driverClass=com.mysql.jdbc.Driver
# 连接的url
c3p0.jdbcUrl=jdbc:mysql://localhost:3306/db3
# 用户名
c3p0.user=root
# 密码
c3p0.password=root

# 初始化连接个数
c3p0.initialPoolSize=5
# 最大连接数
c3p0.maxPoolSize=15
# 超时时间
c3p0.checkoutTimeout=3000
```


## 1.3 druid连接池(重点)

- 使用步骤

```java
1、导入开发jar包：druid-1.0.9.jar，不要忘了mysql驱动jar包。
2、在src目录下创建配置文件（将资料中的配置文件复制进去即可）druid.properties
3、加载properties属性文件
4、获取连接池对象：DruidDataSourceFactory.createDataSource(Properties pro);
5、获取连接
```

- 代码示例

```java
public static void main(String[] args) throws Exception {
  //加载配置文件
  Properties properties=new Properties();
  InputStream is = DruidDataSourceTest.class.getClassLoader().getResourceAsStream("druid.properties");
  properties.load(is);
  //3、获取连接池对象：DruidDataSourceFactory.createDataSource(Properties pro);
  DataSource dataSource = DruidDataSourceFactory.createDataSource(properties);
  //4、获取连接
  Connection conn = dataSource.getConnection();
  System.out.println("conn = " + conn);
}
```

- **修改JDBCUtils工具类(重点1)**

```java
/*
    目的：封装连接池对象，一个应用程序有一个连接池就够了
    做哪些事：
        1、加载properties配置文件
        2、初始化连接池对象
        3、对外提供一个方法获取连接
        4、对外提供一个方法释放资源
        5、对外提供一个获取连接池对象的方法。
 */
public class JDBCUtils {
    private static DataSource ds;  //ctrl+alt+f
    static {
        //1、加载properties配置文件
        //1.1 创建Properties对象
        Properties properties=new Properties();
        //1.2 调用load方法
        InputStream is = JDBCUtils.class.getClassLoader()
                .getResourceAsStream("druid.properties");
        try {
            properties.load(is);
            // 2、初始化连接池对象
            ds = DruidDataSourceFactory.createDataSource(properties);
        } catch (Exception e) {
            e.printStackTrace();
        }
    }
    //3、对外提供一个方法获取连接
    public static Connection getConnection() throws SQLException {
        return ds.getConnection();
    }
    //5、对外提供一个获取连接池对象的方法。
    public static DataSource getDataSource(){
        return ds;
    }
    //4、对外提供一个方法释放资源
    public static void close(Statement stmt, Connection conn){
        close(null,stmt,conn);
    }
    public static void close(ResultSet rs, Statement stmt, Connection conn){
        if(rs!=null){
            try {
                rs.close();
            } catch (SQLException e) {
                e.printStackTrace();
            }
        }
        if(stmt!=null){
            try {
                stmt.close();
            } catch (SQLException e) {
                e.printStackTrace();
            }
        }
        if (conn!=null) {
            try {
                conn.close();
            } catch (SQLException e) {
                e.printStackTrace();
            }
        }
    }
}
```

# 2.JdbcTemplate工具类(重点2)

## 2.1 JdbcTemplate执行增、删、改操作(重点)

- JdbcTemplate介绍

  JdbcTemplate是Spring框架给我们提供的一个简化jdbc操作的工具类，封装了增删改查的操作。

- 实现步骤

```java
前提：导入相关的开发jar包（5个）+mysql驱动jar包(有就不需要了)+连接池jar包(有就不需要了)
1、创建JdbcTemplate对象
2、调用update(String sql,Object... 代替?的参数值 )方法。
```

- 代码示例

```java
public class JdbcTemplateDMLTest {
  //1. 修改1号数据的 salary 为 10000
  @Test
  public void testUpdate(){
    //1、创建JdbcTemplate对象
    JdbcTemplate template=new JdbcTemplate(JDBCUtils.getDataSource());
    //2、调用update(String sql,Object... 代替?的参数值 )方法。
    int count = template.update("update emp set salary=10000 where id=?", 1001);
    System.out.println("count = " + count);
  }
  //2. 添加一条记录
  @Test
  public void testAdd(){
    //1、创建JdbcTemplate对象
    JdbcTemplate template=new JdbcTemplate(JDBCUtils.getDataSource());
    //2、调用update(String sql,Object... 代替?的参数值 )方法。
    String sql="insert into emp values(null,?,?,?,?,?,?,?)";
    int count = template.update(sql, "杨锌怒", 3, 1006, "2000-12-17", 20000, null, 30);
    System.out.println("count = " + count);
  }

  //3. 删除刚才添加的记录
  @Test
  public void testDelete(){
    //1、创建JdbcTemplate对象
    JdbcTemplate template=new JdbcTemplate(JDBCUtils.getDataSource());
    // 2、调用update(String sql,Object... 代替?的参数值 )方法。
    int count = template.update("delete from emp where id=?", 1015);
    System.out.println("count = " + count);
  }
}
```

## 2.2 JdbcTemplate执行查询操作(重点)

- 实现步骤

```java
前提：导入相关的开发jar包（5个）+mysql驱动jar包(有就不需要了)+连接池jar包(有就不需要了)
1、创建JdbcTemplate对象
2、调用query()或者queryForObject()方法
```

- **查询多条数据--多行多列，封装成自定义java对象(非常重要\*\*\*\*\*)**

```java
//6. 查询所有记录，将其封装为Emp对象的List集合 重点
@Test
public void test4(){
  //1、创建JdbcTemplate对象
  JdbcTemplate template=new JdbcTemplate(JDBCUtils.getDataSource());
  //2 调用query方法
  List<Emp> list = template.query("select * from emp", new BeanPropertyRowMapper<Emp>(Emp.class));
  for (Emp emp : list) {
    System.out.println(emp);
  }
}
```

- **查询一条数据--单行多列，封装成一个自定义的java对象,异常要try...catch(非常重要\*\*\*\*\*)**

```java
//8. 查询id为1001的记录，将其封装为Emp集合  重点
@Test
public void test6(){
  //1、创建JdbcTemplate对象
  JdbcTemplate template=new JdbcTemplate(JDBCUtils.getDataSource());
  //2、调用queryForObject
  Emp emp = null;
  try {
    emp = template.queryForObject("select * from emp where id=?",new BeanPropertyRowMapper<Emp>(Emp.class), 1001);
  } catch (DataAccessException e) {
    e.printStackTrace();
  }
  System.out.println(emp);
}
```

- 查询多条数据--多行单列，指定要存入的集合的泛型类型(了解)

```java
//9 查询每一个用户的姓名，多行单例 结果肯定是list集合
@Test
public void test7(){
  //1、创建JdbcTemplate对象
  JdbcTemplate template=new JdbcTemplate(JDBCUtils.getDataSource());
  //2 调用
  List<String> list = template.queryForList("select ename from emp", String.class);
  for (String s : list) {
    System.out.println(s);
  }
}
```

- 查询一条数据--单行单列，这个值是什么类型就是用什么类型的Class来封装（了解）

```java
//7. 查询总记录数 select count(*) from emp;  单行单列 14
@Test
public void test5(){
  //1、创建JdbcTemplate对象
  JdbcTemplate template=new JdbcTemplate(JDBCUtils.getDataSource());
  //2、调用queryForObject()
  Integer count = template.queryForObject("select count(*) from emp", Integer.class);
  System.out.println(count);
}
```

# 总结

```java
1、连接池
	共讲解了c3p0和druid两个连接池，这两个连接池的使用都需要配置文件。
	核心：将druid连接池封装到JDBCUtils工具类中。
	public class JDBCUtils{  //重点
		private static DataSource ds;
    static{
      //加载配置文件
      //1 创建Properties对象
      Properties properties=new Properties();
      //2 调用load方法加载配置文件
      InputStream is
  =JDBCUtils.class.getClassLoader().getResourcesAsStream("druid.properties");
      properties.load(is)
      //初始化druid连接池对象
      ds=DruidDataSourceFactory.createDataSource(properties);
    }
    //5、对外提供一个获取连接池对象的方法。
    public static DataSource getDataSource(){
      return ds;
    }
	}
2、JdbcTemplate工具类(重点)
	增删改思路：
		1 创建JdbcTemplate对象，需要传递连接池对象。
			JdbcTemplate template=new JdbcTemplate(JDBCUtils.getDataSource());
		2 调用update(String sql,Object... args)方法
			int count=template.update("delete from account where id=?",1);	
	查询：
		1 创建JdbcTemplate对象，需要传递连接池对象。
			JdbcTemplate template=new JdbcTemplate(JDBCUtils.getDataSource());
		2 调用query(String sql,RowMapper<T> rowMapper,Object... args)或者queryForObject(String sql,RowMapper<T> rowMapper,Object... args)方法。
		重点是多列需要使用自定义Java对象封装。例如：
		List<Emp> list = template.query("select * from emp", new BeanPropertyRowMapper<Emp>(Emp.class));
		emp = template.queryForObject("select * from emp where id=?",new BeanPropertyRowMapper<Emp>(Emp.class), 1001);
```

