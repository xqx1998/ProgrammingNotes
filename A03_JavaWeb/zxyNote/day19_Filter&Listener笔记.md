## 1.过滤器的概念和入门案例

- 入门案例

```java
@WebFilter("/*")
public class FilterDemo1 implements Filter {
    public void destroy() {}
    public void doFilter(ServletRequest req, ServletResponse resp, FilterChain chain) throws ServletException, IOException {
        System.out.println("拦截到了请求...");
        //放行
        chain.doFilter(req, resp);
    }
    public void init(FilterConfig config) throws ServletException {}
}
```

- 修改Filter模板

![5.修改Filter模板.bmp](img\5.修改Filter模板.bmp)

## 2.Filter过滤器的相关配置

### 2.1.web.xml配置Filter过滤器(了解)

```xml
<filter>
    <filter-name>demo1</filter-name>
    <filter-class>cn.itcast.web.filter.FilterDemo1</filter-class>
</filter>
<filter-mapping>
    <filter-name>demo1</filter-name>
    <!-- 拦截路径 -->
    <url-pattern>/*</url-pattern>
</filter-mapping>
```

### 2.2.过滤器的执行流程和生命周期(会描述)

- 执行流程

```java
	当过滤器拦截到请求之后，先执行放行(chain.doFilter(req, resp))上面的代码，放行之后请求到达目标资源，执行目标资源中的代码，目标资源做出响应之后，响应会被过滤器再次拦截，最后执行放行下面的代码。
```

- 生命周期

```java
	当服务器启动的时候，会创建Filter对象，执行init方法，只执行一次，一般用于加载资源文件；每次拦截到请求之后执行doFilter方法；doFilter方法中经过判断之后决定是否放行；最后当服务器正常关闭时执行destroy方法，只执行一次，一般用于释放资源。
```

### 2.3.过滤器的url-pattern配置:拦截路径（重点1）

```java
1.完全路径匹配：以/开头，不包含通配符*  例如：/servletDemo1  只有当访问servletDemo1时过滤器才会拦截
2.目录匹配：以/开头，以*结尾；在浏览器访问时*表示任意字符  例如：/abc/*  /*  (常用)
3.扩展名匹配：以*开头，以固定的后缀名结尾  例如  *.jsp  *.abc   但是/abc/*.do错误 
```

### 2.4.过滤器拦截方式配置（重点2）

```java
@WebFilter(urlPatterns = "/*",dispatcherTypes ={DispatcherType.REQUEST,DispatcherType.FORWARD} )
DispatcherType.REQUEST:默认值，表示拦截浏览器直接访问的请求
DispatcherType.FORWARD：表示拦截转发的请求
```

## 3.过滤器案例

### 3.1.案例1：登录验证

- 案例分析

![6.案例1_登录验证.bmp](img\6.案例1_登录验证.bmp)

- 代码实现

```java
@WebFilter(value = "/jsp/*",dispatcherTypes = {DispatcherType.REQUEST,DispatcherType.FORWARD})
public class LoginFilter implements Filter {
    public void destroy() {
    }
    public void doFilter(ServletRequest req, ServletResponse resp, FilterChain chain) throws ServletException, IOException {
        HttpServletRequest request= (HttpServletRequest) req;
        //1.从session中获取用户信息
        Object user = request.getSession().getAttribute("user");
        //2.判断user是否为空
        if(user==null){
            //没有登录，保存错误信息，跳转到登录页面
            request.setAttribute("login_msg","您还没登录，无权访问!");
            request.getRequestDispatcher("/login.jsp").forward(request,resp);
        }else{
            //已经登录了，放行
            chain.doFilter(req, resp);
        }
    }
    public void init(FilterConfig config) throws ServletException {}
}
```

### 3.2.案例2：统一解决请求和响应中文乱码问题

- 代码实现

```java
public void doFilter(ServletRequest req, ServletResponse resp, FilterChain chain) throws ServletException, IOException {
    //1.解决请求和响应中文乱码问题
    req.setCharacterEncoding("utf-8");
    resp.setContentType("text/html;charset=utf-8");
    //放行
    chain.doFilter(req, resp);
}
```

### 3.3.案例3：过滤敏感词汇

- 案例需求

![3.过滤敏感词汇.bmp](img\3.过滤敏感词汇.bmp)

- 动态代理模式图

![4.代理.bmp](img\4.代理.bmp)

- 动态代理代码示例

```java
//这个测试类就相当于调用者，在这里面调用代理对象的方法，由代理对象调用目标对象的方法
public class ProxyTest {
    public static void main(String[] args) {
        //1.创建目标对象
        Lenovo lenovo=new Lenovo();
        //2.创建代理对象
        /**
         * ClassLoader loader,类加载器：用于生成代理对象的Class对象；
           Class<?>[] interfaces,接口的Class对象：规定代理对象要实现和目标对象相同的接口，保证功能一直；
           InvocationHandler h,接口：只要调用代理对象的方法，InvocationHandler的invoke方法就执行，
         我们可以在这个方法中对目标对象的方法进行增强
            返回值：就是代理对象
         */
        ClassLoader loader = lenovo.getClass().getClassLoader();
        Class<?>[] interfaces = lenovo.getClass().getInterfaces();
        SaleComputer proxyObj = (SaleComputer) Proxy.newProxyInstance(loader, interfaces, new InvocationHandler() {
            /**
             * @param proxy 表示代理对象
             * @param method 表示代理对象调用的方法对象
             * @param args  表示代理对象调用方法传的参数
             * @return 将结果进一步返回给代理对象调用的方法
             * @throws Throwable
             */
          @Override
          public Object invoke(Object proxy, Method method, Object[] args) throws Throwable { 
                //找目标对象
                // String computer = lenovo.sale(5000);
                String computer= (String) method.invoke(lenovo,args);
                //替换敏感词汇“联想”，用"华为"代替
                if(computer.contains("联想")){
                    computer = computer.replace("联想", "华为");
                }
                return computer+",送鼠标垫，送垃圾键盘";
            }
        });
        //3.调用代理对象的方法
        String computer = proxyObj.sale(8000);
        System.out.println("computer = " + computer);
    }
}
```

- 案例代码实现

```java
@WebFilter("/*")
public class SensitiveWordsFilter implements Filter {
    public void doFilter(ServletRequest req, ServletResponse resp, FilterChain chain) throws ServletException, IOException {
        //1.创建代理对象，增强getParameter方法
        ServletRequest proxy_req = (ServletRequest) Proxy.newProxyInstance(req.getClass().getClassLoader(), req.getClass().getInterfaces(), new InvocationHandler() {
        @Override
        public Object invoke(Object proxy, Method method, Object[] args) throws Throwable {
                //增强getParameter方法
                //判断是否是getParameter方法
                if(method.getName().equals("getParameter")){
                    //增强返回值
                    //获取返回值
                    String value = (String) method.invoke(req,args);//你是笨蛋
                    if(value != null){
                        for (String str : list) {
                            if(value.contains(str)){
                                //包含敏感词汇，那么就替换成***
                                value = value.replaceAll(str,"***");
                            }
                        }
                    }
                    return  value;
                }
                return method.invoke(req,args);
            }
        });
        //2.放行
        chain.doFilter(proxy_req, resp);
    }
    private String[] list = new String(){"笨蛋","坏蛋"};//敏感词汇集合
    public void init(FilterConfig config) throws ServletException {}
    public void destroy() {}
}
```

## 4.监听器

- 监听器的实现步骤

```java
1.创建一个类实现监听器接口，实现抽象方法
public class MyListener implements ServletContextListener {
    /**
     * 当ServletContext被创建的时候调用
     * @param sce
     */
    public void contextInitialized(ServletContextEvent sce) {}
    /**
     * 当ServletContext被销毁的时候调用
     * @param sce
     */
    public void contextDestroyed(ServletContextEvent sce) {}
}
2.配置
	2.1 在类上面添加@WebListener注解
	2.2 在web.xml中配置（了解）
	<listener>
     <listener-class>cn.itcast.web.listener.MyListener</listener-class>
  </listener>
```