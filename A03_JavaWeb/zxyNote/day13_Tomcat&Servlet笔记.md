# 1.web相关概念(了解)

```text
1. 软件架构
	1. C/S：客户端/服务器端
	2. B/S：浏览器/服务器端

2. 资源分类
	1. 静态资源：所有用户访问后，得到的结果都是一样的，称为静态资源.静态资源可以直接被浏览器解析
		* 如： html,css,JavaScript
	2. 动态资源:每个用户访问相同资源后，得到的结果可能不一样。称为动态资源。动态资源被访问后，需要先转换为静态资源，在返回给浏览器
		* 如：servlet/jsp,php,asp....
3. 网络通信三要素
	1. IP：电子设备(计算机)在网络中的唯一标识。
	2. 端口：应用程序在计算机中的唯一标识。 0~65536
	3. 传输协议：规定了数据传输的规则
		1. 基础协议：
			1. tcp:安全协议，三次握手。 速度稍慢
			2. udp：不安全协议。 速度快
```

# 2.Tomcat的启动问题

```text
下载：http://tomcat.apache.org/
安装：解压即可使用，不需要安装
启动：双击bin/startup.bat就可以启动，启动的黑窗口不能关闭
	访问自己：http://localhost:8080
	访问别人：http://别人ip地址:8080
卸载：直接删除解压的文件夹即可
正常关闭tomcat：
	1.双击bin/shutdown.bat
	2.ctrl+c也是正常关闭
```

- **tomcat的启动问题：启动黑窗口一闪而过**

```text
问题1：JAVA_HOME环境变量没有配置
解决：正确配置JAVA_HOME环境变量

问题2：tomcat端口号冲突，一般是启动了多个tomcat服务器
在logs目录中查看日志信息：Caused by: java.net.BindException: Address already in use: bind表示端口号冲突。
解决：
	1.在cmd窗口查看所有进程的端口号：netstat -ano,找到8080端口号对应进程的pid；
	2.在任务管理器中根据pid找到该进程，结束掉之前启动的tomcat进程
```

# 3.项目的3种发布方式

- 3种发布方式

```java
1. 直接将项目放到webapps目录下即可。（掌握这个就可以了）
	* /hello：项目的访问路径-->虚拟目录
	* 简化部署：将项目打成一个war包，再将war包放置到webapps目录下。
		* war包会自动解压缩
2. 配置conf/server.xml文件的<Host>标签体中配置
	<Context docBase="D:\hello" path="/hehe" />
	* docBase:项目存放的路径
	* path：虚拟目录
3. 在conf\Catalina\localhost创建任意名称的xml文件。在文件中编写：
	<Context docBase="D:\hello" />
	* 虚拟目录：xml文件的名称,不带后缀名。
	说明：idea编辑器采用的是该方式
```

- 访问路径解析

![访问路径解析.bmp](img/访问路径解析.bmp)

**Idea中集成tomcat服务器并发布项目**

```text
1、将tomcat集成到idea中
2、创建动态web项目
3、将动态web项目发布到tomcat服务器，并成功访问到
```

# 4.Servlet概述以及实现思路(重点1)

- Servlet的概念和作用

  servlet 是运行在 Web 服务器中的小型 Java 程序，本质是一个接口。**servlet 通常通过 HTTP（超文本传输协议）接收和响应来自 Web 客户端的请求。** 

![Servlet的作用.png](img/Servlet的作用.png)

- 实现一个Servlet的步骤

```java
1.定义一个类实现Servlet接口，重写抽象方法；
public class ServletDemo1 implements Servlet{
	
}
2.配置这个Servlet
	2.1 在web.xml中配置Servlet（了解）
		<!--配置servlet的类信息-->
    <servlet>
        <!--Servlet的名称-->
        <servlet-name>ServletDemo1</servlet-name>
        <!--servlet的全类名-->
        <servlet-class>com.itheima.web.servlet.ServletDemo1</servlet-class>
    </servlet>
    <!--配置servlet的映射信息，也就是访问路径和servlet类的对应关系-->
    <servlet-mapping>
        <!--Servlet的名称，和上面保持一致-->
        <servlet-name>ServletDemo1</servlet-name>
        <!--Servlet的访问路径-->
        <url-pattern>/hello</url-pattern>
    </servlet-mapping>
    2.2 使用注解配置Servlet（吐血推荐）
    //当注解的参数有且仅有一个，而且参数名叫value时，value可以省略不写
    @WebServlet("/demo1")
		public class ServletDemo1 implements Servlet{}
```

# 5.Servlet生命周期(了解)

- 描述Servlet生命周期

```java
思路：三个方法分别是什么时候被调用的？调用几次？作用分别是什么？
	当我们第一次访问Servlet时，服务器会创建Servlet对象，调用init()方法初始化，该方法只调用一次，可以用来加载配置文件；每当我们访问Servelt时，service()方法都会被调用，访问一次就调用一次，用来接收客户端请求以及对客户端作出响应；服务器正常关闭的时候销毁Servlet对象，destory()方法会被调用，调用一次，一般在该方法中释放资源；
```

- 配置启动时加载

```xml
在web.xml中的<Servlet>标签中配置<load-on-startup>2</load-on-startup>，值越小优先级越高；
```

# 6.导入项目并手动发布项目(重要2)

- 第一步：导入别人的项目  File--->new --->module from exiting Sources  然后一直下一步
- 第二步：添加tomcat环境，解决Servlet/HttpServlet报错问题

![第二步.bmp](img\手动发布项目\第二步.bmp)

第三步.让idea识别web目录，出现蓝色的小点点

![第三步.bmp](img\手动发布项目\第三步.bmp)

第四步.将web目录中的内容进行发布，将项目源代码编译成符合tomcat要求的目录结果

![第四步.bmp](img\手动发布项目\第四步.bmp)

第五步.将编译好的项目添加到tomcat中

![第五步.bmp](img\手动发布项目\第五步.bmp)

# 7、总结

```java
上午内容：
	1、web项目的三种发布方式
		掌握：直接将项目或war包放到tomcat的webapps目录中，启动服务器就可以发布了。
	2、将tomcat集成到idea中，并创建动态web项目和发布。（重点*****）自动发布项目。
		第一步：Edit Configurations-->template-->tomcat server-->local---configure...,选择本地的tomcat即可。
		第二步：new module---》Java Enterprise--->web applications(打勾)-->next..
  	第三步：点击绿色启动按钮
下午内容：Servlet
	1、概念：Servlet是一个运行在服务器端的Java小程序，接收和响应客户的发送的请求。
	2、Servlet的实现步骤：（重点）
		第一步：创建一个java类实现Servlet接口，重写抽象方法
		第二步：使用web.xml或者注解配置Servlet，推荐使用注解：@WebServlet("/demo03") 
  			<servlet>
  				<servlet-name>任意的名称</servlet-name>
  				<servlet-class>全类名</servlet-class>
  			</servlet>
				<servlet-mapping>
  				<servlet-name>任意的名称</servlet-name>
  				<url-pattern>访问路径</url-pattern>
  			</servlet-mapping>
  3、Servlet生命周期：init() service() destroy()（了解）
  	init方法默认是在第一次访问Servlet的时候创建Servlet对象并调用，只调用一次，用于初始化数据。service方法会在每一次访问Servlet的时候调用，可以多次，用于接收和响应客户的请求。destroy方法会在服务器正常关闭的时候调用，只调用一次，用于释放资源。可以在<servlet>标签中配置<load-on-startup>2</load-on-startup>在服务器启动的时候初始化servlet对象。
  
晚自习任务：
	1、能够自动和手动发布web项目，滚瓜烂熟（必须搞定）
	2、创建Servlet，继承HttpServlet方法，并能够成功访问到。必须搞定）
	3、记一下Servlet的生命周期
	4、复习：使用JdbcTemplate对象+连接池执行增删该查操作（连接池封装到JDBCUtils工具类中）必须搞定）
```

