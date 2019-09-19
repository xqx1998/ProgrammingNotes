## 1.会话技术-Cookie（客户端技术）

- **一次会话： 从打开浏览器第一次访问服务器开始建立会话，到浏览器关闭就是一次会话；**
- 会话技术的作用：共享数据
- 快速入门实现思路

```java
第一大步：在CookieDemo1的Servlet中想客户端发送Cookie
	1.创建Cookie对象：Cookie cookie=new Cookie(String name,String value);
	2.将Cookie通过response对象发送给客户端：response.addCookie(cookie);
第二大步：在CookieDemo2的Servlet中获取客户端发送过来的数据
	1.通过request对象获取所有Cookie： Cookie[] cookies=request.getCookies();
	2.如果Cookie数组不为null，那么就遍历打印Cookie中的name和value值
```

- **Cookie的实现原理(重点1)**

![Cookie原理.bm](img\Cookie原理.bmp)

```java
	Cookie属于客户端技术，当客户端浏览器第一次访问服务器项目时，服务器端创建Cookie对象通过response响应头发送给客户端保存。当客户端浏览器再次访问服务器该项目时，会自动将Cookie以request请求头的形式发送过去，我们在服务器端就可以获取到之前的Cookie，获取到保存的值，从而实现数据共享；
```

- Cookie的使用细节

```java
1.一次发送多个Cookie：允许；
2.Cookie的存储时间（掌握）
	1. 默认情况下，当浏览器关闭后，Cookie数据被销毁
	2. 持久化存储：
		cookie.setMaxAge(int seconds) //单位是秒
		1. 正数：将Cookie数据写到硬盘的文件中。持久化存储。并指定cookie存活时间，时间到后，cookie文件自动失效
		2. 负数：默认值
		3. 零：删除cookie信息
3.Cookie存储中文：可以存储
	在tomcat 8 之前 cookie中不能直接存储中文数据。
		需要将中文数据转码---一般采用URL编码(%E3)，URLEncoder
	在tomcat 8 之后，cookie支持中文数据。特殊字符还是不支持，建议使用URL编码存储，URL解码解析
	注意：我们使用的tomcat8.5.31版本的tomcat发送Cookie不允许存储空格。
4.Cookie的有效路径（了解）
	通过cookie.setPath("/虚拟目录/其他路径")方法设置有效路径
	注意：如果没有调用setPath方法，默认的有效路径是当前Servlet所在的路径，如果当前Servlet是在项目下，那么浏览器访问该项目的任何资源都会将Cookie方法过来
	cookie.setPath("/day16");   	//当浏览器访问day16项目下所有资源都会携带Cookie
	cookie.setPath("/itcast");		//当浏览器访问itcast项目下所有资源都会携带Cookie
	cookie.setPath("/itcast/aaa");	//当浏览器访问itcast项目中aaa以及aaa路径下所有资源都会携带Cookie
```

- Cookie的使用场景以及缺点

```java
cookie的应用场景
	记住用户名、网站无登录状态保存设置信息、记住上一次访问时间。
缺点：
	cookie只能存储字符串类型的简单不重要的数据，浏览器保存cookie的数量和大小都有限制；
```

- 记住上一次访问时间-案例分析

![Cookie案例_记住上次访问时间-详细.bmp](img\Cookie案例_记住上次访问时间-详细.bmp)

- CookieUtils工具类

```java
/**
 * 根据指定名称从cookie数组中获取cookie
 */
public class CookieUtils {
    public static Cookie findCookie(Cookie[] cookies,String name){
        if(cookies!=null){
            for (Cookie cookie : cookies) {
                //找到我们要的cookie
                if(cookie.getName().equals(name)){
                    //找到了
                    return cookie;
                }
            }
        }
        //如果Cookie数组为null，第一次访问;如果遍历完了之后还没有进if，那也是第一次访问
        return null;
    }
}
```

- 第一次访问时间代码实现

```java
 实现思路：
    1.获取浏览器发送过来的所有Cookie
    2.判断是否是第一次访问,根据cookie的名称找到我们要的Cookie
    3.如果是第一次访问：
        3.1 响应“欢迎第一次访问”
    4.如果不是第一次访问：
        4.1 从cookie中获取上一次访问时间
        4.2 将“上一次访问时间”响应在浏览器上展示
    5.记录本次的访问时间到Cookie中，发送给客户端保存，那么这个时间就是下一次访问要展示的时间。
```

```java
@WebServlet("/lastVisitServlet")
public class LastVisitServlet extends HttpServlet {
    protected void doPost(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
        //解决响应中文乱码问题
        response.setContentType("text/html;charset=utf-8");
        //1.获取浏览器发送过来的所有Cookie
        Cookie[] cookies = request.getCookies();
        //2.判断是否是第一次访问,根据cookie的名称找到我们要的Cookie
        Cookie cookie = CookieUtils.findCookie(cookies, "lastVisit");
        if (cookie==null) {
            //3.如果是第一次访问：
            //3.1 响应“欢迎第一次访问”
            response.getWriter().write("欢迎老板第一次光临...");
        }else{
            //4.如果不是第一次访问：
            //4.1 从cookie中获取上一次访问时间
            String value = cookie.getValue();//%4E%56%AC
            //解码回去
            value=URLDecoder.decode(value,"utf-8");
            //4.2 将“上一次访问时间”响应在浏览器上展示
            response.getWriter().write("你上一次访问的时间是: "+value);
        }
        //5.记录本次的访问时间到Cookie中，发送给客户端保存，那么这个时间就是下一次访问要展示的时间。
        //5.1 获取系统当前时间并格式化
        SimpleDateFormat sdf=new SimpleDateFormat("yyyy年MM月dd日 HH:mm:ss");
        String dateStr = sdf.format(new Date());
        //在存储之前对dateStr进行url编码
        dateStr=URLEncoder.encode(dateStr,"utf-8"); //%4E%56%AC
        //5.2 创建Cookie对象，保存当前时间
        Cookie c=new Cookie("lastVisit",dateStr);
        //持久化存储
        c.setMaxAge(60*60*24*7);
        //5.3将Cookie响应个客户端保存
        response.addCookie(c);
    }

    protected void doGet(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
        doPost(request, response);
    }
}
```

## 2.jsp本质以及脚本元素

- **jsp的本质：本质是一个Servlet。**
- jsp脚本元素

```java
1. <%  代码 %>：代码片段脚本，比较常用，此处可以定义局部变量，for循环、if判断...
	定义的java代码，在service方法中。service方法中可以定义什么，该脚本中就可以定义什么。
2. <%! 代码 %>：成员声明脚本，很少用，定义成员变量和成员方法
	定义的java代码，在jsp转换后的java类的成员位置。
3. <%= 代码 %>：输出脚本，比较常用，输出的结果在浏览器页面上
	定义的java代码，会输出到页面上。输出语句中可以定义什么，该脚本中就可以定义什么。
```

- 例如

```jsp
<%--使用for循环在页面上打印10赵洋贼帅....  脚本元素可以和html混着写--%>
<%
	for (int i = 0; i < 10; i++) {
%>
	<h1>杨锌怒贼鸡儿帅<%=i%>......</h1>
<%
	}
%>
```

## 3.会话技术-Session(服务端技术,重点)

- **session的实现原理(重点2)**

![Session原理.bmp](img\Session原理.bmp)

```java
	session属于服务端技术，session是依赖于cookie实现。当我们第一次在服务器端调用request.getSession()方法时，服务器会创建session对象，同时生产一个唯一的sessionid，将sessionid保存在cookie中发送给浏览器保存；当浏览器再次访问服务器时会自动将sessionid携带过来，那么服务器端再次调用getSession()方法时就不是创建session对象，而是根据sessionid获取之前的session对象使用。
```

- session的使用细节

```java
1. 当客户端关闭后，服务器不关闭，两次获取session是否为同一个？
	* 默认情况下。不是。
	* 如果需要相同，则可以创建Cookie,键为JSESSIONID，设置最大存活时间，让cookie持久化保存。
		Cookie c = new Cookie("JSESSIONID",session.getId());
         c.setMaxAge(60*60);
         response.addCookie(c);

2. 客户端不关闭，服务器关闭后，两次获取的session是同一个吗？
	* 不是同一个，但是要确保数据不丢失。tomcat自动完成以下工作
		* session的钝化：
			* 在服务器正常关闭之前，将session对象系列化到硬盘上
		* session的活化：
			* 在服务器启动后，将session文件转化为内存中的session对象即可。
		
3. session什么时候被销毁？
	1. 服务器关闭
	2. session对象调用invalidate() 。
	3. session默认失效时间 30分钟
		选择性配置修改	
		<session-config>
	        <session-timeout>30</session-timeout>
	    </session-config>
```

- **session作为域对象存值、取值、移除值（重点3）**

```java
作用范围：默认是一次会话范围。
创建和销毁时间：
	创建：在服务器端第一次调用request.getSession()方法时创建
	销毁：服务器关闭；手动调用invalidate()方法；默认的30分钟到期了。
api：
	存值:request.setAttribute(String name,Object value)；
	取值:Object value=request.getAttribute(String name);
	移除值：request.removeAttribute(String name);
```