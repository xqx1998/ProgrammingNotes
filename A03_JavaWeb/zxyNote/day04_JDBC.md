## 1.JDBC概念和入门程序

### 1.1 JDBC概念和本质

JDBC是sun公司提供的一套操作关系型数据库的规范(接口)，这些接口由各个数据库生产商实现。数据库生产商将接口的实现类会打成一个开发包，这个开发包就是jar包。我们要使用哪个数据库将哪个数据库厂商提供的jar导入程序就行了。

![JDBC本质.bmp](/Users/zhouxiangyang/Desktop/JavaEE89期总结/day04_JDBC/resource/JDBC本质.bmp)

### 1.2 JDBC入门程序

```java
public class JdbcDemo1 {
  public static void main(String[] args) throws Exception {
    //1. 导入驱动jar包
    //2.注册驱动
    Class.forName("com.mysql.jdbc.Driver");
    //3.获取数据库连接对象  假设获取连接的时候要Driver对象
    Connection conn = DriverManager.getConnection("jdbc:mysql://localhost:3306/db3", "root", "root");
    //4.定义sql语句
    String sql = "update account set balance = 500 where id=1";
    //5.获取执行sql的对象 Statement
    Statement stmt = conn.createStatement();
    //6.执行sql
    int count = stmt.executeUpdate(sql);
    //7.处理结果
    System.out.println(count);
    //8.释放资源
    stmt.close();
    conn.close();
  }
}
```

## 2.JDBC中API讲解

### 2.1 com.sql.DriverManager作用

```java
1、注册驱动:DriverManager.registerDriver(new Driver()),但是我们不使用，在开发中使用Class.forName("com.mysql.jdbc.Driver"),因为使用DriverManager的registerDriver注册驱动会注册两次。（了解）

2、获取连接：
	static Connection getConnection(String url, String user, String password);
	String url:连接数据库的路径
		语法：jdbc:mysql://localhost:3306/数据库名称
				如果是本机的数据库，可以简写：jdbc:mysql:///数据库名称
	String user:用户名
	String password：密码
```

### 2.2 com.sql.Connetion作用

```java
1、获取执行sql语句的对象
	Statement createStatement();
	PreparedStatement preparedStatement(String sql);
2、事务管理（下面专题6讲解）
```

### 2.3 com.sql.Statement作用

```java
作用：执行sql语句
1. boolean execute(String sql) ：可以执行任意的sql 了解，如果执行的是查询语句，就返回true，如果执行的是DML或者DDL语句，那么就返回false。 （了解）
2. int executeUpdate(String sql) ：执行DML（insert、update、delete）语句、DDL(create，alter、drop)语句
		* 返回值：影响的行数，可以通过这个影响的行数判断DML语句是否执行成功 返回值>0的则执行成功，反之，则失败。
3. ResultSet executeQuery(String sql)  ：执行DQL（select)语句
```

### 2.4 com.sql.ResultSet作用

![ResultSet结果集遍历获取值.png](resource/ResultSet结果集遍历获取值.png)

```java
1、判断是否有下一条数据
2、获取当前行数据
while (rs.next()){
   //获取id
   int id = rs.getInt("id");
   //获取name
   String name = rs.getString("name");
   //获取balance
   double balance = rs.getDouble("balance");
   //打印结果
   System.out.println(id+","+name+","+balance);
}
```

**注意：如果该列的数据类型是int类型，也可以使用getString("列名")来获取，但是反过来就不行。getInt(1)表示获取第一列的值，而getInt("1")表示获取列名为1的这一列的值,二者有区别。**

## 3.使用Statement对象执行增、删、改、查操作(了解)

### 3.1 执行增、删、改的步骤

```java
1、注册驱动：Class.forName("com.mysql.jdbc.Driver");
2、获取链接：DriverManager.getConnection("jdbc:mysql://localhost:3306/数据库名","root","root");
3、获取执行sql的Statement对象：stmt = conn.createStatement();
4、执行sql语句，获取结果
5、处理结果
6、释放资源
```

### 3.2 执行查询的步骤

```java
1、注册驱动：Class.forName("com.mysql.jdbc.Driver");
2、获取链接：DriverManager.getConnection("jdbc:mysql://localhost:3306/数据库名","root","root");
3、获取执行sql的Statement对象：stmt = conn.createStatement();
4、执行sql语句，获取结果集
5、遍历结果集获取数据
6、释放资源
```

## 4.JDBCUtils工具类（重点1）

- 目标和分析

```java
/*
    将jdbc操作重复的代码封装到此工具类中
    封装如下操作：
        加载配置文件 只需要加载一次就行了，放到静态代码块中
        注册驱动：Class.forName("com.mysql.jdbc.Driver");  只需要注册一次就行了，放到静态代码块中
        获取连接：conn = DriverManager.getConnection("jdbc:mysql://localhost:3306/db3","root","root");
        释放资源
    目的:简化jdbc代码的数据
 */
```

- **在JDBCUtils工具类的静态代码块中读取配置文件和注册驱动(核心代码)**

```java
static {
  try {
    //加载配置文件
    //1 创建Properties 对象
    Properties properties=new Properties();
    //2 调用load方法加载配置文件
    //使用类加载器获取src路径中文件的输入流对象
    //ClassLoader loader = JDBCUtils.class.getClassLoader();
    //获取流
    InputStream is = JDBCUtils.class.getClassLoader().getResourceAsStream("jdbc.properties");
    properties.load(is);
    //3 调用getProperty方法根据key获取value值
    String driver = properties.getProperty("driver");
    url=properties.getProperty("url");
    user=properties.getProperty("user");
    password=properties.getProperty("password");

    // 注册驱动
    Class.forName(driver);
  } catch (ClassNotFoundException e) {
    e.printStackTrace();
  } catch (IOException e) {
    e.printStackTrace();
  }
}
```

- 在JDBCUtils工具类中对外提供获取连接和释放资源的方法

```java
//对外提供一个获取连接的方法
public static Connection getConnection() throws SQLException {
  return DriverManager.getConnection(url,user,password);
}
//对外提供一个释放资源的方法
public static void close(Statement stmt,Connection conn){
  close(null,stmt,conn);
}
//重载
public static void close(ResultSet rs, Statement stmt,Connection conn){
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
```

## 5.使用PreparedStatement对象执行增、删、改、查操作(掌握)

### 5.1 sql注入漏洞的原因和解决办法(重要)

```java
原因：在使用Statement对象执行sql语句时，如果sql语句中拼接的变量包含sql关键字，那么就可能会出现sql注入漏洞。
解决：不使用Statement对象，使用PreparedStatement对象执行sql。
```

- **补充：PreparedStatement相比Statement的优点**

```java
1、不会产生sql注入漏洞问题
2、效率高
```

### 5.2 PreparedStatement对象执行增、删、改（重点2）

```java
1、通过工具类获取连接
2、获取PreparedStatement对象，预编译sql语句
3、如果sql语句中有？占位符，那么就设置参数
4、执行增删改，获取影响的行数，int count=pstmt.executeUpdate();
5、处理结果
6、释放资源
```

- 代码示例

```java
public class InsertTest {
  public static void main(String[] args) {
    Connection conn = null;
    PreparedStatement pstmt = null;
    try {
      //1 通过工具类获取连接
      conn = JdbcUtils.getConnection();
      //2 获取PreparedStatement对象，预编译sql语句
      pstmt = conn.prepareStatement("insert into account values(null,?,?)");
      //3 如果sql语句中有？，那么就设置参数
      pstmt.setString(1,"小迷妹");
      pstmt.setDouble(2,0.5);
      //4 执行sql，获取结果
      int count = pstmt.executeUpdate();
      //5 处理结果
      System.out.println(count>0?"添加成功":"添加失败");
    } catch (SQLException e) {
      e.printStackTrace();
    } finally {
      //6 释放资源
      JdbcUtils.close(pstmt,conn);
    }
  }
}
```

### 5.3 PreparedStatement对象执行查询（重点3）

```java
1、通过工具类获取连接
2、获取PreparedStatement对象，预编译sql语句
3、如果sql语句中有？占位符，那么就设置参数
4、执行查询操作，获取结果集对象；ResultSet rs = pstmt.executeQuery();
5、遍历结果集，获取数据
6、释放资源
```

- 代码示例

```java
//定义一个登录的方法
public static boolean login(String name,String password){
  Connection conn = null;
  PreparedStatement pstmt = null;
  ResultSet rs = null;
  try {
    //1 通过工具类获取连接
    conn = JdbcUtils.getConnection();
    //2 获取PreparedStatement对象，预编译sql语句
    pstmt = conn.prepareStatement("select * from user where name=? and password=?");
    //3 如果有？占位符就设置参数
    pstmt.setString(1,name);
    pstmt.setString(2,password);
    //4 执行sql 获取结果
    rs = pstmt.executeQuery();
    //5 处理结果，返回结果
    return rs.next();
  } catch (SQLException e) {
    e.printStackTrace();
  } finally {
    //6 释放资源，
    JdbcUtils.close(rs,pstmt,conn);
  }
  return false;
}
```

## 6.jdbc事务管理

### 6.1 事务管理的概念

一组sql操作要么同时成功要么同时失败，那么就需要统一管理，这就是事务管理。

### 6.2 事务管理API

```java
获取到连接对象之后开启事务：conn.setAutoCommit(false);
一组sql执行完成之后提交事务：conn.commit();
在catch中回滚事务：conn.rollback();
```

**注意：在执行事务管理的时候，一般是捕获一个最大的Exception异常，出现任何异常都要回滚。**

### 6.3 代码示例

```java
public class TransactionTest {
  public static void main(String[] args) {
    Connection conn = null;
    PreparedStatement pstmt = null;
    try {
      //1 通过工具类获取连接
      conn = JdbcUtils.getConnection();
      //开启事务-------------------------------
      conn.setAutoCommit(false);
      //2 获取PreparedStatement对象，预编译sql语句
      pstmt = conn.prepareStatement("update account set balance=balance+? where name=?");
      //1、张三的账户-500
      //3 如果有？，就设置参数
      pstmt.setDouble(1,-500);
      pstmt.setString(2,"张三");
      //4 执行sql
      pstmt.executeUpdate();

      //制造异常
      //int i=10/0;

      //2、杨锌怒的账户+500
      //3 如果有？，就设置参数
      pstmt.setDouble(1,500);
      pstmt.setString(2,"杨锌怒");
      //4 执行sql
      pstmt.executeUpdate();

      //提交事务-------------------------------
      conn.commit();

    } catch (Exception e) {
      //            e.printStackTrace();
      System.out.println("事务回滚了");
      //回滚事务---------------------------------
      if (conn!=null) {
        try {
          conn.rollback();
        } catch (SQLException e1) {
          e1.printStackTrace();
        }
      }
    } finally {
      //6 释放资源
      JdbcUtils.close(pstmt,conn);
    }
  }
}
```

# 7 总结

```java
1、JDBCUtils工具类封装 注册驱动、获取连接、释放资源
	  private static String url;
    private static String user;
    private static String password;
    static {
        //1 加载配置文件  只需要加载一次
        //1.1 创建Properties对象
        Properties properties=new Properties();
        //1.2 调用load方法加载配置文件
        InputStream is = JdbcUtils.class.getClassLoader().getResourceAsStream("jdbc.properties");
        try {
            properties.load(is);
            //1.3 调用getProperty方法，根据key获取value值
            String driver = properties.getProperty("driver");
            url = properties.getProperty("url");
            user = properties.getProperty("user");
            password = properties.getProperty("password");
        //2 注册驱动 只需要注册一次  可以放到静态代码块中
        Class.forName(driver);

        } catch (Exception e) {
            e.printStackTrace();
        }
    }
2、使用PreparedStatement对象执行增删改查的思路
	增删改的思路：
		1、通过工具类获取连接
		2、获取PreparedStatement对象，预编译sql语句
		3、如果sql语句中有？占位符，那么就设置参数
		4、执行增删改，获取影响的行数，int count=pstmt.executeUpdate();
		5、处理结果
		6、释放资源
	查询的思路：
		1、通过工具类获取连接
		2、获取PreparedStatement对象，预编译sql语句
		3、如果sql语句中有？占位符，那么就设置参数
		4、执行查询操作，获取结果集对象；ResultSet rs = pstmt.executeQuery();
		5、遍历结果集，获取数据
		6、释放资源
3、使用JDBC完成事务管理
```

