## 1.jsp的九大内置对象(了解)

- 记住5个内置对象（4个域对象+response）

```java
pageContext,request,session,application,response
```

- pageContext的作用

```java
1.作为域对象存值、取值、移除值
	作用范围：当前jsp页面，离开这个页面就获取不到了。
2.可以获取另外8个内置对象
	pageContext.getRequest(),pageContext.getSession(),......
```

## 2.MVC开发模式

- MVC开发模式（知道3层代表的含义就行了）

```java
概念：mvc要求我们将代码按照功能进行分层；
M：model层，也叫模型层，主要负责操作数据库以及封装结果；一般是普通的java类
V：view层，也叫视图层，主要负责展示结果，一般是jsp或者html页面
C：controller层，也叫控制层，主要负责接收客户端的请求，调用model层获取结果，将结果响应给view层展示；
```

- MVC开发模式图

![MVC开发模式](img\MVC开发模式.bmp)

## 3.EL表达式

### 3.1 EL 表达式的语法和作用

```java
语法：${表达式}
作用：简化jsp页面java代码的书写，代替jsp页面java代码的输出部分；代替<%=表达式%>
```

### 3.2 执行运算(了解)

```java
1. 算数运算符： + - * /(div) %(mod)
2. 比较运算符： > < >= <= == !=
3. 逻辑运算符： &&(and) ||(or) !(not)
4. 空运算符： empty
	* 功能：用于判断字符串、集合、数组对象是否为null或者长度是否为0
	* ${empty list}:判断字符串、集合、数组对象是否为null或者长度为0
	* ${not empty str}:表示判断字符串、集合、数组对象是否不为null 并且 长度>0
```

### 3.3 EL从域中获取数据(重点1)

```java
语法
    1. ${域范围名称.键名}：从指定域中获取指定键的值
        域范围名称：
            1. pageScope		--> pageContext
            2. requestScope 	--> request
            3. sessionScope 	--> session
            4. applicationScope --> application（ServletContext）
        举例：在request域中存储了name=张三
        获取：${requestScope.name}
    2. ${键名}：表示依次从最小的域中查找是否有该键对应的值，直到找到为止。
    3.EL表达式获取对象值
    	${域范围名称.键名.属性名}，域范围名称可以省略表示从最小域范围获取，本质是调用对象的getter方法，
    4.EL表达式获取list和map集合中的数据
    	list:
    		${域范围名称.键名[索引]}，域范围名称可以省略表示从最小域范围获取
		map：
			${域范围名称.键名.map集合的key名称}，域范围名称可以省略表示从最小域范围获取
			${域范围名称.键名["map集合的key名称"]},如果map集合中的key有特殊字符，那么就需要使用这种方法，比如map集合中的key有个.或者-；
			例如：map.put("s.name","李四")
                   request.setAttribute("smap",map)
                   必须：${smap["s.name"]}
```

- EL内置对象以及empty运算符

```java
empty运算符：判断对象为空或者长度为0，如果是判断非空并且长度>使用 not empty
	${empty list} //list集合为null或者长度为0就返回true
	${not empty list} //list集合不为null并且长度大于0就返回true
三元运算符：${a>b?a:b}
	例如：${empty errorMsg?"请速速上车!":errorMsg}
内置对象：pageContext，获取另外8个内置对象，常见用法：动态获取虚拟路径
	${pageContext.request.contextPath}
```

## 4.JSTL的使用步骤

- 使用步骤

```java
1.导入开发jar包:jstl-1.2.jar
2.在jsp页面使用taglib指令引入标签库
	<%@taglib prefix="c" uri="http://java.sun.com/jsp/jstl/core" %>
3.使用标签库中的标签
```

- 标签库中常见的标签

  - if标签：表示判断，相当于java中的if判断

  ```java
  <c:if test="${判断条件}">
  		条件成立的情况...
      </c:if>
  ```

  - choose标签：表示判断，相当于java中的if...else if...else...

  ```java
  <%
    request.setAttribute("score",158);
  %>
  <c:choose>
     <c:when test="${score>0 && score<60}">
        不及格...
     </c:when>
     <c:when test="${score>=60 && score<=100}">
        及格了...
     </c:when>
     <c:otherwise>
        输入不合法
     </c:otherwise>
  </c:choose>
  ```

  - foreach标签：遍历list集合

  ```java
  <%
    //list表示的是list集合，创建list集合代码省略了
    request.setAttribute("list",list);  
  %>
  // var="s"表示的是遍历出来的集合中的每一个元素
  //varStatus="status"表示遍历的状态,
  //${status.index}获取遍历的索引
  //${status.count}获取遍历的个数，一般用在遍历容器上
  <c:forEach items="${list}" var="s" varStatus="status">
      ${s}    |   ${status.index}   |   ${status.count} <br>
  </c:forEach>
  ```

## 5.javaEE三层架构

![三层架构.bmp](img\三层架构.bmp)

## 6.案例-用户列表展示

### 6.1 案例分析

![列表查询分析.bmp](img\列表查询分析.bmp)

### 6.2 代码实现

- 入手点：在index.jsp页面点击“查询所有用户信息”超链接

```html
<a
         href="${pageContext.request.contextPath}/userListServlet" style="text-decoration:none;font-size:33px">查询所有用户信息
</a>
```

- UserListServlet代码实现

```java
@WebServlet("/userListServlet")
public class UserListServlet extends HttpServlet {
    protected void doPost(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
        //1.调用UserService完成查询
        UserService service = new UserServiceImpl();
        List<User> users = service.findAll();
        //2.将list存入request域
        request.setAttribute("users",users);
        //3.转发到list.jsp
        request.getRequestDispatcher("/list.jsp").forward(request,response);
    }
    protected void doGet(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
        this.doPost(request, response);
    }
}
```

- UserServiceImpl代码实现

```java
public class UserServiceImpl implements UserService {
    private UserDao dao = new UserDaoImpl();
    @Override
    public List<User> findAll() {
        //调用Dao完成查询
        return dao.findAll();
    }
}
```

- UserDaoImpl代码实现

```java
public class UserDaoImpl implements UserDao {
    private JdbcTemplate template = new JdbcTemplate(JDBCUtils.getDataSource());
    @Override
    public List<User> findAll() {
        //使用JDBC操作数据库...
        //1.定义sql
        String sql = "select * from user";
        //2.调用query方法
        List<User> users = template.query(sql, new BeanPropertyRowMapper<User>(User.class));
        return users;
    }
}
```

- list.jsp页面展示所有用户信息

```jsp
<c:forEach items="${users}" var="user" varStatus="s">
    <tr>
        <td><input type="checkbox" name="uid" value="${user.id}"></td>
        <td>${s.count}</td>
        <td>${user.name}</td>
        <td>${user.gender}</td>
        <td>${user.age}</td>
        <td>${user.address}</td>
        <td>${user.qq}</td>
        <td>${user.email}</td>
        <td><a class="btn btn-default btn-sm" href="">修改</a>&nbsp;
            <a class="btn btn-default btn-sm" href="">删除</a></td>
    </tr>
</c:forEach>
```