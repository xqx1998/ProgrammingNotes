## 1.\<url-pattern>访问路径问题

```java
1.完全路径匹配：以/开头，不包含通配符*  例如：/servletDemo1  /demo1 /abc /aaa/bbb  /abc.do  (常用)
2.目录匹配：以/开头，以*结尾；在浏览器访问时*表示任意字符  例如：/abc/*  /*
3.扩展名匹配：以*开头，以固定的后缀名结尾  例如  *.jsp  *.abc   但是/abc/*.do错误  
```

## 2.HTTP协议

- 概念：http协议规定的是客户端和服务器之间通信的规则；
- 特点：http协议是基于请求和响应；一定是先有请求后有响应，一次请求对应一次响应；
- 请求部分：分4个部分

```text
请求行：
	请求方式  url路径  协议/版本
	POST /day14/servletDemo5 HTTP/1.1
	
请求头：一般是一个key对应一个value，也有一个key对应多个value；
    User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/69.0.3497.100 Safari/537.36
	告诉服务器浏览器的版本信息，一般用来解决浏览器兼容性问题；
    Referer: http://localhost:8080/day14/login.html
    告诉浏览器当前请求的来源，用于防盗链以及统计工作；
    
请求空行：用于分隔请求头和请求体

请求体：只有post请求才有请求体，get请求没有，post请求参数就在请求体中
	username=zhaoying
```

## 3.Requst对象获取请求行/头方法(了解)

### 3.1 Request原理

**request和response是服务器创建的两个对象给我们使用，request封装了浏览器发送过来的所有数据，如果是获取浏览器发送过来的信息那么就找request对象，如果想告诉浏览器展示信息那么就找response对象；**

![request&response对象原理.bmp](img\request&response对象原理.bmp)

### 3.2 Requst对象获取请求行方法

```java
1. 获取请求方式 ：GET
	String getMethod()  
2. (*)获取虚拟目录：/day14
	String getContextPath()
3. 获取Servlet路径: /demo1
	String getServletPath()
4. 获取get方式请求参数：name=zhangsan
	String getQueryString()
5. (*)获取请求URI：/day14/demo1
	String getRequestURI():		/day14/demo1	不全的路径
	StringBuffer getRequestURL()  :http://localhost/day14/demo1   全路径
	URL:统一资源定位符 ： http://localhost/day14/demo1	中华人民共和国
	URI：统一资源标识符 : /day14/demo1				共和国

6. 获取协议及版本：HTTP/1.1
	String getProtocol()
7. 获取客户机的IP地址：
	String getRemoteAddr()
```

**重点记一下：获取虚拟目录的getServletPath()方法，getRequestURI()方法作为一个次掌握的方法**

### 3.3 Requst对象获取请求头方法（了解）

```java
1. String getHeader(String name) ;根据指定的请求头获取请求头的值；
2. Enumeration<E> getHeaderNames() ；获取所有请求头
```

## 4.Request对象获取请求参数方法(重点)

### 4.1 获取请求参数的三个方法（重点1）

```java
String getParameter(String name)  :根据一个name获取一个value值		很常用
String[] getParameterValues(String name)  :根据一个name获取多个value值
Map<String,String[]> getParameterMap()  :获取所有请求参数	很常用
```

### 4.2 请求参数中文乱码问题（重点2）

**说明：从tomcat8.0之后，get请求参数不再乱码，但是post请求参数依然乱码**

```java
原因：request缓冲区默认字符集是iso-8859-1不支持中文
解决办法：在获取请求参数之前设置request缓冲区字符集为utf-8
request.setCharacterEncoding("utf-8")；
```

## 5.Request对象请求转发和作为域对象(重点)

### 5.1 请求转发实现页面跳转(重点3)

![请求转发例子.bmp](img\请求转发例子.bmp)

```java
1.请求转发api：
	request.getRequestDispatcher("/转发的路径，不带虚拟路径").forward(request,response);
2.转发的特点：
	2.1 请求转发是一次请求一次响应；
	2.2 请求转发浏览器地址栏不会发生变化
	2.3 请求转发api中路径不需要携带虚拟路径，默认是访问项目内部资源；
```

### 5.2 request作为域对象数据共享(存值、取值、移除值)(重点4)

```java
作用范围：一次请求范围，浏览器发送请求到服务器做成响应结束，请求转发在一次请求范围内；
创建和销毁时间：浏览器发送请求之后服务器创建request对象，服务器做成相应之后销毁；
存值、取值、移除值api：
	存值:request.setAttribute(String name,Object value)；
	取值:Object value=request.getAttribute(String name);
	移除值：request.removeAttribute(String name);
```

## 6.登录案例以及BeanUtils(重点)

### 6.1 案例分析

![登录案例分析.bmp](img\登录案例分析.bmp)

### 6.2 BeanUtils的使用（重点5）

```java
//3.创建User对象
User loginUser = new User();
//3.2使用BeanUtils封装
try {
    BeanUtils.populate(loginUser,req.getParameterMap());
} catch (IllegalAccessException e) {
    e.printStackTrace();
} catch (InvocationTargetException e) {
    e.printStackTrace();
}
```

**注意：form表单的name属性要和javabean的属性一一对应；**

### 6.3 javabean规范

```java
就是一个标准的java类。
javabean的成员变量和属性的区别：
	成员变量：成员变量都是private修饰的；
	属性：去掉setter和getter方法的set和get部分，将首字母变小写之后就是属性
		setUsername()----->Username----->username;
注意：往往javabean的属性和成员变量是一样，因为getter和setter方法都是idea工具根据成员变量自动生成的。
```

# 7、重点内容总结

```java
1、http协议-请求部分（4部分）（能够表述）
	请求行：
	  请求方式 url路径 协议/版本
		POST /day14/servletDemo2 HTTP/1.1
	请求头：都是一个key对应一个或者多个value值，多个value值用逗号隔开
		user-agent:获取当前浏览器的类型和版本信息，下一次课文件下载会用到。
		referer：当前请求的来源，用于防盗链和统计工作。
	请求空行：就是一个换行
	请求体：只有post请求才有请求体，get请求没有请求体。
		存放post请求的请求参数: username=zhouxiangyang&password=123456
  继承体系：tomcat中的RequestFacade--->实现了HttpServletRequest接口--->继承ServletRequest
2、request对象获取请求行的方法：
	获取虚拟目录/路径：String contextPath=request.getContextPath();（重要）
	获取URI：String uri=request.getRequestURI()（了解）
  
3、获取请求参数的通用方法（重要***************）
String getParameter(String name):根据一个name获取一个value值。
String[] getParameterValues(String name):根据一个name获取多个value值。
Map<String,String[]> getParameterMap():获取所有的请求参数
4、请求中文乱码问题的原因和解决办法（重要***************）
		get请求：在tomcat8之后中文就不再乱码。
		post请求：中文会乱码
			原因：request缓冲区字符集默认是iso-8859-1不支持中文
			解决办法：在第一次获取请求参数之前设置缓冲区字符集
				request.setCharacterEncoding("utf-8");

5、请求转发实现页面跳转：（重要***************）
	api：request.getRequestDispatcher("不带虚拟目录的路径").forward(request,response);
	特点：
		1、本质：请求转发是一次请求一次响应。
		2、请求转发地址栏不会发生变化
		3、请求转发的路径不带虚拟目录
		4、请求转发只能跳转到项目内部资源（了解）
6、request作为域对象共享数据（重要***************）
	域对象：有一定作用范围的对象。
	作用范围：一次请求，从浏览器发送亲请求到接收响应结束。请求转发在一次请求范围内。
	存值、取值、移除值的API：
		存值：void setAttribute(String name,Object value);
		取值：Object getAttribute(String name);
		移除值：void removeAttribute(String name);

7、登录案例
	1、案例的环境搭建
			准备数据库
			创建项目，创建包结构、拷贝资源代码、相关jar包、相关工具类和配置文件
			相关jar包:数据库驱动jar、druid连接池、jdbcTemplate工具类,lib目录必须在WEB-INF	中，名字也必须叫lib。
	2、登录逻辑分析
	3、Servlet代码实现
	4、dao操作数据库代码实现
```

