## 1.登录功能

### 1.1 验证码实现换一换

```html
<script type="text/javascript">
    //切换验证码
    function refreshCode(){
        //1.获取验证码图片对象
        var vcode = document.getElementById("vcode");
        //2.设置其src属性，加时间戳
        vcode.src = "${pageContext.request.contextPath}/checkCodeServlet?time="+new Date().getTime();
    }
</script>
```

### 1.2 登录后台代码实现

- LoginServlet代码实现

```java
protected void doPost(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
    //1.设置编码
    request.setCharacterEncoding("utf-8");
    //2.获取数据
    //2.1获取用户填写验证码
    String verifycode = request.getParameter("verifycode");
    //3.验证码校验
    HttpSession session = request.getSession();
    String checkcode_server = (String) session.getAttribute("CHECKCODE_SERVER");
    session.removeAttribute("CHECKCODE_SERVER");//确保验证码一次性
    if(checkcode_server!=null && !checkcode_server.equalsIgnoreCase(verifycode)){
        //验证码不正确
        //提示信息
        request.setAttribute("login_msg","验证码错误！");
        //跳转登录页面
        request.getRequestDispatcher("/login.jsp").forward(request,response);
        return;
    }
    //4.封装User对象
    User user = new User();
    try {
        BeanUtils.populate(user,request.getParameterMap());
    } catch (IllegalAccessException e) {
        e.printStackTrace();
    } catch (InvocationTargetException e) {
        e.printStackTrace();
    }
    //5.调用Service查询
    UserService service = new UserServiceImpl();
    User loginUser = service.login(user);
    //6.判断是否登录成功
    if(loginUser != null){
        //登录成功
        //将用户存入session
        session.setAttribute("user",loginUser);
        //跳转页面
        response.sendRedirect(request.getContextPath()+"/index.jsp");
    }else{
        //登录失败
        //提示信息
        request.setAttribute("login_msg","用户名或密码错误！");
        //跳转登录页面
        request.getRequestDispatcher("/login.jsp").forward(request,response);
    }
}
```

- UserServiceImpl中的login方法

```java
@Override
public User login(User user) {
    return dao.findUserByUsernameAndPassword(user.getUsername(),user.getPassword());
}
```

- UserDaoImpl中的findUserByUsernameAndPassword方法

```java
@Override
public User findUserByUsernameAndPassword(String username, String password) {
    try {
        String sql = "select * from user where username = ? and password = ?";
        User user = template.queryForObject(sql, new BeanPropertyRowMapper<User>(User.class), username, password);
        return user;
    } catch (Exception e) {
        e.printStackTrace();
        return null;
    }
}
```

## 2.添加功能

### 2.1 案例分析

![3.添加功能](img\3.添加功能.bmp)

### 2.2 代码实现

- AddUserServlet代码实现

```java
protected void doPost(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
    //1.设置编码
    request.setCharacterEncoding("utf-8");
    //2.获取参数
    Map<String, String[]> map = request.getParameterMap();
    //3.封装对象
    User user = new User();
    try {
        BeanUtils.populate(user,map);
    } catch (IllegalAccessException e) {
        e.printStackTrace();
    } catch (InvocationTargetException e) {
        e.printStackTrace();
    }
    //4.调用Service保存
    UserService service = new UserServiceImpl();
    service.addUser(user);
    //5.跳转到userListServlet
    response.sendRedirect(request.getContextPath()+"/userListServlet");
}
```

- UserServiceImpl的addUser方法实现

```java
@Override
public void addUser(User user) {
    dao.add(user);
}
```

- UserDaoImpl的add方法实现

```java
@Override
public void add(User user) {
    //1.定义sql
    String sql = "insert into user values(null,?,?,?,?,?,?,null,null)";
    //2.执行sql
    template.update(sql, user.getName(), user.getGender(), user.getAge(), user.getAddress(), user.getQq(), user.getEmail());
}
```

## 3.删除功能

### 3.1 案例分析

![4.删除功](img\4.删除功能.bmp)

### 3.2 代码实现

- 入手点-点击删除后弹出确认框

```html
<a class="btn btn-default btn-sm" href="javascript:deleteUser(${user.id});">删除</a>
<!--声明deleteUser方法-->
function deleteUser(id){
    //用户安全提示
    if(confirm("您确定要删除吗？")){
        //访问路径
        location.href="${pageContext.request.contextPath}/delUserServlet?id="+id;
    }
}
```

- DelUserServlet代码实现

```java
protected void doPost(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
    //1.获取id
    String id = request.getParameter("id");
    //2.调用service删除
    UserService service = new UserServiceImpl();
    service.deleteUser(id);
    //3.跳转到查询所有Servlet
    response.sendRedirect(request.getContextPath()+"/userListServlet");
}
```

- UserServiceImpl中deleteUser代码实现

```java
@Override
public void deleteUser(String id) {
    dao.delete(Integer.parseInt(id));
}
```

- UserDaoImpl中delete方法实现

```java
@Override
public void delete(int id) {
    //1.定义sql
    String sql = "delete from user where id = ?";
    //2.执行sql
    template.update(sql, id);
}
```

## 4.修改功能

### 4.1 案例分析

![5.修改功能](img\5.修改功能.bmp)

### 4.2 根据用户id查询用户信息展示到update.jsp页面

- 入手点-点击修改按钮，将id发送给服务器

```html
<a class="btn btn-default btn-sm" href="${pageContext.request.contextPath}/findUserServlet?id=${user.id}">修改</a>
```

- FindUserServlet代码实现

```java
protected void doPost(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
    //1.获取id
    String id = request.getParameter("id");
    //2.调用Service查询
    UserService service = new UserServiceImpl();
    User user = service.findUserById(id);
    //3.将user存入request
    request.setAttribute("user",user);
    //4.转发到update.jsp
    request.getRequestDispatcher("/update.jsp").forward(request,response);
}
```

- UserServiceImpl中的findUserById方法实现

```java
@Override
public User findUserById(String id) {
    return dao.findById(Integer.parseInt(id));
}
```

- UserDaoImpl中findById方法实现

```java
@Override
public User findById(int id) {
    String sql = "select * from user where id = ?";
    return template.queryForObject(sql, new BeanPropertyRowMapper<User>(User.class), id);
}
```

- update.jsp页面展示用户信息

```jsp
<!--  隐藏域 提交id-->
<input type="hidden" name="id" value="${user.id}">
<!--回显性别-->
<div class="form-group">
    <label>性别：</label>
    <input type="radio" name="gender" value="男" ${user.gender == '男'?"checked":""}/>男
    <input type="radio" name="gender" value="女" ${user.gender == '女'?"checked":""}/>女
</div>
<!--回显年龄-->
<div class="form-group">
    <label for="age">年龄：</label>
    <input type="text" class="form-control" value="${user.age}" id="age"  name="age" placeholder="请输入年龄" />
</div>
<!--回显籍贯-->
<select name="address" id="address" class="form-control" >
    <option value="陕西" ${user.address == '陕西'?"selected":""}>陕西</option>
    <option value="北京" ${user.address == '北京'?"selected":""}>北京</option>
    <option value="上海" ${user.address == '上海'?"selected":""}>上海</option>
</select>
```

### 4.3 点击提交按钮正式修改用户信息

- UpdateUserServlet代码实现

```java
protected void doPost(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
    //1.设置编码
    request.setCharacterEncoding("utf-8");
    //3.封装对象
    User user = new User();
    try {
        BeanUtils.populate(user,request.getParameterMap());
    } catch (IllegalAccessException e) {
        e.printStackTrace();
    } catch (InvocationTargetException e) {
        e.printStackTrace();
    }
    //4.调用Service修改
    UserService service = new UserServiceImpl();
    service.updateUser(user);
    //5.跳转到查询所有Servlet
    response.sendRedirect(request.getContextPath()+"/userListServlet");
}
```

- UserServiceImpl中updateUser方法实现

```java
@Override
public void updateUser(User user) {
    dao.update(user);
}
```

- UserDaoImpl实现

```java
@Override
public void update(User user) {
    String sql = "update user set name = ?,gender = ? ,age = ? , address = ? , qq = ?, email = ? where id = ?";
    template.update(sql, user.getName(), user.getGender(), user.getAge(), user.getAddress(), user.getQq(), user.getEmail(), user.getId());
}
```

## 5.删除选中功能

### 5.1 案例分析

![6.删除选中功能](img\6.删除选中功能.bmp)

### 5.2 代码实现

- 在用户列表的table外面包裹form表单来提交选中状态

```html
<form id="form" action="${pageContext.request.contextPath}/delSelectedServlet" method="post">
 table...
</form>
```

- 给“删除选中”按钮添加单击事件提交form表单

```javascript
window.onload = function(){
    //给删除选中按钮添加单击事件
    document.getElementById("delSelected").onclick = function(){
         //表单提交
        document.getElementById("form").submit();
    }
}
```

- DelSelectedServlet代码实现

```java
protected void doPost(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
    //1.获取所有id
    String[] ids = request.getParameterValues("uid");
    //2.调用service删除
    UserService service = new UserServiceImpl();
    service.delSelectedUser(ids);
    //3.跳转查询所有Servlet
    response.sendRedirect(request.getContextPath()+"/userListServlet");
}
```

- UserServiceImpl中delSelectedUser方法实现

```java
@Override
public void delSelectedUser(String[] ids) {
    if(ids != null && ids.length > 0){
        //1.遍历数组
        for (String id : ids) {
            //2.调用dao删除
            dao.delete(Integer.parseInt(id));
        }
    }
}
```

- 前台实现全选和全不选以及弹出警告框（可选）

```javascript
window.onload = function(){
    //给删除选中按钮添加单击事件
    document.getElementById("delSelected").onclick = function(){
        if(confirm("您确定要删除选中条目吗？")){
            var flag = false;
            //判断是否有选中条目
            var cbs = document.getElementsByName("uid");
            for (var i = 0; i < cbs.length; i++) {
                if(cbs[i].checked){
                    //有一个条目选中了
                    flag = true;
                    break;
                }
            }
            if(flag){//有条目被选中
                //表单提交
                document.getElementById("form").submit();
            }
        }
    }
    //1.获取第一个cb
    document.getElementById("firstCb").onclick = function(){
        //2.获取下边列表中所有的cb
        var cbs = document.getElementsByName("uid");
        //3.遍历
        for (var i = 0; i < cbs.length; i++) {
            //4.设置这些cbs[i]的checked状态 = firstCb.checked
            cbs[i].checked = this.checked;
        }
    }
}
```

## 6.分页查询功能

### 6.1 案例分析

- 前后台数据传递

![7.分页查询功能](img\7.分页查询功能.bmp)

- 实现思路

![7_2.分页查询功能2](img\7_2.分页查询功能2.bmp)

### 6.2 代码实现

- PageBean类封装结果

```java
public class PageBean<T> {
    private int totalCount; // 总记录数
    private int totalPage ; // 总页码
    private List<T> list ; // 每页的数据
    private int currentPage ; //当前页码
    private int rows;//每页显示的记录数
   // getter、setter、toString方法都省略
}
```

- findUserByPageServlet代码实现

```java
protected void doPost(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
    request.setCharacterEncoding("utf-8");
    //1.获取参数
    String currentPage = request.getParameter("currentPage");//当前页码
    String rows = request.getParameter("rows");//每页显示条数
    //给当前页数和每页显示条数赋默认值
    if(currentPage==null||"".equals(currentPage)){
        currentPage="1";
    }
    if(rows==null||"".equals(rows)){
        rows="5";
    }
    //2.调用service查询
    UserService service = new UserServiceImpl();
    PageBean<User> pb = service.findUserByPage(currentPage,rows);

    System.out.println(pb);
    //3.将PageBean存入request
    request.setAttribute("pb",pb);
    //4.转发到list.jsp
    request.getRequestDispatcher("/list.jsp").forward(request,response);
}
```

- UserServiceImpl中findUserByPage方法实现

```java
@Override
public PageBean<User> findUserByPage(String _currentPage, String _rows) {

    int currentPage = Integer.parseInt(_currentPage);
    int rows = Integer.parseInt(_rows);
    //1.创建空的PageBean对象
    PageBean<User> pb = new PageBean<User>();
    //2.设置参数
    pb.setCurrentPage(currentPage);
    pb.setRows(rows);
    //3.调用dao查询总记录数
    int totalCount = dao.findTotalCount();
    pb.setTotalCount(totalCount);
    //4.调用dao查询List集合
    //计算开始的记录索引
    int start = (currentPage - 1) * rows;
    List<User> list = dao.findByPage(start,rows);
    pb.setList(list);

    //5.计算总页码
    // int totalPage = (totalCount % rows)  == 0 ? totalCount/rows : (totalCount/rows) + 1;
    int totalPage= (int) Math.ceil(totalCount*1.0/rows);
    pb.setTotalPage(totalPage);
    return pb;
}
```

- UserDaoImpl实现

```java
@Override
public int findTotalCount() {  //查询总记录数
    //1.定义sql
    String sql="select count(*) from user";
    //2.调用queryForObject方法
    return template.queryForObject(sql,Integer.class);
}
@Override
public List<User> findByPage(int start, int rows) { //查询当前页用户信息
    //1.定义sql
    String sql="select * from user limit ?,?";
    //2.调用query方法
    return template.query(sql,new BeanPropertyRowMapper<User>(User.class),start,rows);
}
```

- 前台展示用户信息和分页条信息

```jsp
<form id="form" action="${pageContext.request.contextPath}/delSelectedServlet" method="post">
    <table border="1" class="table table-bordered table-hover">
        <tr class="success">
            <th><input type="checkbox" id="firstCb"></th>
            <th>编号</th>
            <th>姓名</th>
            <th>性别</th>
            <th>年龄</th>
            <th>籍贯</th>
            <th>QQ</th>
            <th>邮箱</th>
            <th>操作</th>
        </tr>
        
        <c:forEach items="${pb.list}" var="user" varStatus="s">
            <tr>
                <td><input type="checkbox" name="uid" value="${user.id}"></td>
                <td>${s.count}</td>
                <td>${user.name}</td>
                <td>${user.gender}</td>
                <td>${user.age}</td>
                <td>${user.address}</td>
                <td>${user.qq}</td>
                <td>${user.email}</td>
                <td><a class="btn btn-default btn-sm" href="${pageContext.request.contextPath}/findUserServlet?id=${user.id}">修改</a>&nbsp;
                    <a class="btn btn-default btn-sm" href="javascript:deleteUser(${user.id});">删除</a></td>
            </tr>
        </c:forEach>
    </table>
</form>
<!--分页条信息-->
<div>
    <nav>
        <ul class="pagination">
            <%--上一页--%>
            <c:if test="${pb.currentPage==1}">
                <li class="disabled">
                    <a href="javascript:void(0)" aria-label="Previous">
                        <span aria-hidden="true">&laquo;</span>
                    </a>
                </li>
            </c:if>
            <c:if test="${pb.currentPage!=1}">
                <li>
                    <a href="${pageContext.request.contextPath}/findUserByPageServlet?currentPage=${pb.currentPage-1}&rows=5" aria-label="Previous">
                        <span aria-hidden="true">&laquo;</span>
                    </a>
                </li>
            </c:if>
            <%--页码--%>
            <c:forEach begin="1" end="${pb.totalPage}" var="i" step="1">
                <li ${i==pb.currentPage?"class='active'":""}><a href="${pageContext.request.contextPath}/findUserByPageServlet?currentPage=${i}&rows=5">${i}</a></li>
            </c:forEach>
            <%--下一页--%>
            <c:if test="${pb.currentPage==pb.totalPage}">
                <li class="disabled">
                    <a href="javascript:void(0)" aria-label="Next">
                        <span aria-hidden="true">&raquo;</span>
                    </a>
                </li>
            </c:if>
            <c:if test="${pb.currentPage!=pb.totalPage}">
                <li>
                    <a href="${pageContext.request.contextPath}/findUserByPageServlet?currentPage=${pb.currentPage+1}&rows=5" aria-label="Next">
                        <span aria-hidden="true">&raquo;</span>
                    </a>
                </li>
            </c:if>
            <span style="font-size: 25px;margin-left: 5px;">
                共${pb.totalCount}条记录，共${pb.totalPage}页
            </span>
        </ul>
    </nav>
</div>
```

## 7.复杂条件查询功能

### 7.1 案例分析

![8.复杂条件分页查询.bmp](img\8.复杂条件分页查询.bmp)

### 7.2 代码实现

- findUserByPageServlet获取查询条件并传递给service

```java
//获取查询条件
Map<String, String[]> condition = request.getParameterMap();

//2.调用service查询
UserService service = new UserServiceImpl();
PageBean<User> pb = service.findUserByPage(currentPage,rows,condition);
```

- UserServiceImpl中将查询条件传递给dao的两个方法

```java
//3.调用dao查询总记录数
int totalCount = dao.findTotalCount(condition);
pb.setTotalCount(totalCount);
//4.调用dao查询List集合
//计算开始的记录索引
int start = (currentPage - 1) * rows;
List<User> list = dao.findByPage(start,rows,condition);
pb.setList(list);
```

- 核心--UserDaoImpl中带条件查询总记录数据

```java
@Override
public int findTotalCount(Map<String, String[]> condition) {
    //1.定义模板初始化sql
    String sql = "select count(*) from user where 1 = 1 ";
    StringBuilder sb = new StringBuilder(sql);
    //2.遍历map
    Set<String> keySet = condition.keySet();
    //定义参数的集合
    List<Object> params = new ArrayList<Object>();
    for (String key : keySet) {
        //排除分页条件参数
        if("currentPage".equals(key) || "rows".equals(key)){
            continue;
        }
        //获取value
        String value = condition.get(key)[0];
        //判断value是否有值
        if(value != null && !"".equals(value)){
            //有值
            sb.append(" and "+key+" like ? ");
            params.add("%"+value+"%");//？条件的值
        }
    }
    return template.queryForObject(sb.toString(),Integer.class,params.toArray());
}
```

- 核心--UserDaoImpl中带条件查询当前页数据

```java
@Override
public List<User> findByPage(int start, int rows, Map<String, String[]> condition) {
    String sql = "select * from user  where 1 = 1 ";
    StringBuilder sb = new StringBuilder(sql);
    //2.遍历map
    Set<String> keySet = condition.keySet();
    //定义参数的集合
    List<Object> params = new ArrayList<Object>();
    for (String key : keySet) {
        //排除分页条件参数
        if("currentPage".equals(key) || "rows".equals(key)){
            continue;
        }
        //获取value
        String value = condition.get(key)[0];
        //判断value是否有值
        if(value != null && !"".equals(value)){
            //有值
            sb.append(" and "+key+" like ? ");
            params.add("%"+value+"%");//？条件的值
        }
    }
    //添加分页查询
    sb.append(" limit ?,? ");
    //添加分页查询参数值
    params.add(start);
    params.add(rows);
    //打印结果
    System.out.println(sb.toString());
    System.out.println(params);
    return template.query(sb.toString(),new BeanPropertyRowMapper<User>(User.class),params.toArray());
}
```

- 查询条件回显

```jsp
1. 在findUserByPageServlet中将查询条件存储到request域中
	//3.将PageBean存入request
    request.setAttribute("pb",pb);
    request.setAttribute("condition",condition);
    //4.转发到list.jsp
    request.getRequestDispatcher("/list.jsp").forward(request,response);
2.在list.jsp页面表单中回显查询条件，核心是value="${condition.name[0]}"
<form class="form-inline" action="${pageContext.request.contextPath}/findUserByPageServlet" method="post">
    <div class="form-group">
        <label for="exampleInputName2">姓名</label>
        <input type="text" name="name" value="${condition.name[0]}" class="form-control" id="exampleInputName2" >
    </div>
    <div class="form-group">
        <label for="exampleInputName3">籍贯</label>
        <input type="text" name="address" value="${condition.address[0]}" class="form-control" id="exampleInputName3" >
    </div>

    <div class="form-group">
        <label for="exampleInputEmail2">邮箱</label>
        <input type="text" name="email" value="${condition.email[0]}" class="form-control" id="exampleInputEmail2"  >
    </div>
    <button type="submit" class="btn btn-default">查询</button>
</form>
```

- 点击分页条带条件查询

```jsp
1.以页码为例，上一页和下一页同理：在rows=5后面拼接查询条件
<li ${i==pb.currentPage?"class='active'":""}><a href="${pageContext.request.contextPath}/findUserByPageServlet?currentPage=${i}&rows=5&name=${condition.name[0]}&address=${condition.address[0]}&email=${condition.email[0]}">${i}</a></li>
```