## 1.http协议响应内容

```java
1.响应行：协议版本	状态码	 状态描述
	常见的状态码：（记住）
		200	：响应成功
		302	：重定向，也是页面跳转的一种方式。
		304	：访问缓存
		404	：访问路径不正确(记住)
		405	：get请求没有对应的doGet()方法，post请求没有对应的doPost()方法(记住)
		500	：服务器端java代码出现异常了(记住)
2. 响应头：一般是一个key对应一个value，也有一个key对应多个value
	content-type：告诉浏览器响应的内容类型以及采用的字符集
	content-disposition：告诉浏览器以文件下载的形式打开；
	location：告诉浏览器重定向的路径
3.响应空行：分隔响应头和响应体
4.响应体：服务器告诉浏览器展示的内容
```

## 2.response对象重定向实现页面跳转(重点1)

![请求转发和重定向的区别.bmp](img\请求转发和重定向的区别.bmp)

```java
1.重定向api
	response.sendRedirect("/带虚拟路径的访问路径");
2.请求转发和重定向的区别?
    1.请求转发是一次请求一次响应，重定向是两个请求和两次响应；
    2.请求转发浏览器地址栏不会发生改变，重定向浏览器地址栏会发生改变
    3.请求转发路径不带虚拟目录，重定向路径要带虚拟目录；
    4.请求转发只能跳转到项目内部资源，重定向可以跳转到任意服务器中的任意项目资源。
```

**注意：如果跳转前后使用request数据传递，那么只能使用请求转发，否则使用重定向；**

## 3.web中页面访问路径问题(重点2)

![客户端路径和服务端路径.bmp](img\客户端路径和服务端路径.bmp)

```java
相对路径：以./或者../开头  ./表示当前路径可以省略不写，../表示上一级目录
	规则：要找到当前页面和要跳转的页面之间的相对位置关系,比较当前页面访问路径和要跳转的页面的访问路径
		例如：
			http://localhost:8080/day15/responseServletDemo1
			http://localhost:8080/day15/responseServletDemo2
			从responseServletDemo1跳转到responseServletDemo2相对路径：./responseServletDemo2;
	暂时不推荐使用相对路径
绝对路径：吐血推荐  以/开头
	客户端路径：客户端浏览器访问服务器的路径就叫客户端路径，例如：form表单的action、a标签、img的src、重定向，需要使用虚拟目录
	服务端路径：服务器内部跳转的路径，请求转发 不需要使用虚拟目录
```

**总结：除了请求转发不需要使用虚拟目录，其他路径都要使用虚拟目录。**

## 4.response响应内容以及中文乱码问题(重点3)

```java
1.响应数据:
	response.getWriter().write("数据");
	response.getWriter().print("数据");

	区别例如：
		    response.getWriter().write(97); //a
        response.getWriter().write("<br>");
        response.getWriter().print(97); //97
2.响应中文乱码问题
	原因：1.response缓冲区字符集是iso-8859-1不支持中文；缓冲区字符集和浏览器采用的字符集不一致；
	解决办法：在获取到流对象之前response.setContentType("text/html;charset=utf-8");
		1.设置了response缓冲区字符集为utf-8；2.告诉浏览器采用utf-8字符集
```

- 验证码换一换功能

```javascript
//声明refreshCode()
function refreshCode() {
    //向验证码的Servlet发送请求
    document.getElementById("vcode").src="/day15/checkCodeServlet?time="+new Date().getTime();
}
```

## 5.ServletContext作为域对象存值、取值、移除值

```java
作用范围：整个web项目
创建和销毁时间：在服务器启动的时候会为每个web项目创建一个ServletContext对象，代表这个web项目；服务器关闭了。
存值、取值、移除值api：
	存值:request.setAttribute(String name,Object value)；
	取值:Object value=request.getAttribute(String name);
	移除值：request.removeAttribute(String name);
```

## 6.ServletContext对象读取文件IO流路径

```java
1.获取文件在服务器中的真实IO流路径：
	String path1=servletContext.getRealPath("/a.txt"); //a.txt在web目录中
	String path2=servletContext.getRealPath("/WEB-INF/b.txt");//b.txt在web/WEB-INF目录里面
	String path3=servletContext.getRealPath("/WEB-INF/classes/c.txt");  //c.txt在src目录中

2.一步到位获取文件输入流
	InputStream is1 = servletContext.getResourceAsStream("/a.txt");
	InputStream is2 = servletContext.getResourceAsStream("/WEB-INF/b.txt");
	InputStream is3 = servletContext.getResourceAsStream("/WEB-INF/classes/c.txt");

	特殊:src中的文件还可以使用类加载器获取
		InputStream is=xxxServlet.getClass().getClassLoader().getResourceAsStream("文件名");
```

## 7.response对象实现文件下载

- 思路分析

![文件下载思路.bmp](img\文件下载思路.bmp)