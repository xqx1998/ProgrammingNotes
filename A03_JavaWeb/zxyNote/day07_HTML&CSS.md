# 1.表单标签(重点1)

- form标签：定于数据采集的范围，只有在form标签内部的表单项才会被提交给服务器；

```html
<form action="首页热门商品显示.html" method="get">
  <label for="username">姓名：</label>
 <input type="text" name="username" placeholder="请输入姓名" id="username"><br>
  
  	密码：<input type="password" name="password" placeholder="请输入密码"><br>
  	性别：<input type="radio" name="gender" value="man" checked>男
  				<input type="radio" name="gender" value="woman">女<br>

    爱好：<input type="checkbox" name="hobby" value="smoking"> 抽烟
          <input type="checkbox" name="hobby" value="drink" checked> 喝酒
          <input type="checkbox" name="hobby" value="tangtou"> 烫头<br>
    <input type="submit" value="登录">
</form>
form标签的属性
  action属性：将表单中的数据提交给服务器的url路径。
  method属性：提交方式，共有7种，但是常用的只有get/post两种。
    get请求和post请求的区别？
				1、get请求参数会拼接在地址栏的url后面，post请求在请求体中。
						get请求提交参数格式：提交的url路径?username=zhangsan&password=123
				2、get请求参数有大小限制，post请求参数没有大小限制
				3、get请求参数相对不安全，post请求参数相对安全。
input表单项属性：
	type属性：input表单项的类型，常见的取值有：text(默认值)、password、radio、checkbox、submit、file、hidden、button、date、email、color...
```

- **表单项name属性和value属性的作用**

```html
name属性的作用：
	1、表单项数据要想提交给服务器，必须要有name属性。
	2、如果是radio或者checkbox表单项，name必须一样，表示为一组。
value属性的作用：
	1、作为表单项要显示的初始值，如果是输入框，提示信息不使用value使用placeholder="提示信息"
	2、如果表单项只能选不能输入，那么必须要有value属性，用于区分提交给服务器的值，否则都是on。例如radio和checkbox表单项必须有value属性。
```

**注意:所有的表单项都需要name属性，只能选不能输入的表单项必须使用value属性**

- 案例

```html
<form action="#" method="post">
  <table border="1" align="center" cellspacing="0">
    <tr>
      <td><label for="username">用户名</label></td>
      <td><input type="text" name="username" id="username" placeholder="请输入账号"></td>
    </tr>
    <tr>
      <td><label for="username">密码</label></td>
      <td><input type="password" name="password" id="password"></td>
    </tr>
    <tr>
      <td><label for="email">Email</label></td>
      <td><input type="email" name="email" id="email"></td>
    </tr>
    <tr>
      <td><label for="name">姓名</label></td>
      <td><input type="text" name="name" id="name"></td>
    </tr>
    <tr>
      <td><label for="phone">手机号</label></td>
      <td><input type="text" name="phone" id="phone"></td>
    </tr>
    <tr>
      <td><label >性别</label></td>
      <td>
        <input type="radio" name="sex" value="man" checked="checked">男
        <input type="radio" name="sex" value="woman">女
      </td>
    </tr>
    <tr>
      <td><label for="birthday">出生日期</label></td>
      <td><input type="date" name="birthday" id="birthday"></td>
    </tr>
    <tr>
      <td><label>地址</label></td>
      <td>
        <select name="province">
          <option value="hb">湖北省</option>
          <option value="hn">湖南省</option>
          <option value="fj">福建省</option>
        </select>
      </td>
    </tr>
    <tr>
      <td><label for="checkCode">验证码</label></td>
      <td>
        <input type="text" name="checkCode" id="checkCode" size="10">
        <img src="img/verify_code.jpg">
      </td>
    </tr>
    <tr>
      <td colspan="2" align="center"><input type="submit" value="注册"></td>
    </tr>
  </table>
</form>
```

# 2.CSS引入方式以及语法

- CSS概念：层叠样式表，多个样式可以共同作用在一个标签元素上；CSS主要用于美化页面；
- CSS引入方式

```html
1、内联样式：也叫行内样式，<div style="color: red;font-size: 20px">hello css</div>
2、内部样式：将样式定义在一组style标签中
    p{
      color: blue;
      font-size: 30px;
    }
3、外部样式：
		1、定义一个以.css结尾的文件，在文件中定义样式。
		2、在html页面使用link标签引入样式
		<!--引入外部样式-->
    <link rel="stylesheet" href="css/style.css">
```

**注意：link标签必须要有rel="stylesheet"属性，否则引入的样式无效；快捷键：写link然后ctrl+alt+空格提示**

- CSS基本语法

```html
选择器{
	属性名:属性值;
	属性名:属性值;
	...
}
选择器:表示样式作用于哪些标签元素。换一句话说就是"选择器用于选择哪些标签元素被样式修饰"。
```

# 3.CSS常用选择器

- **基础选择器（重点2）**

```css
1. id选择器：选择具体的id属性值的元素.建议在一个html页面中id值唯一
	语法：#id属性值{}
2. 元素选择器：选择具有相同标签名称的元素
	语法： 标签名称{}
3. 类选择器：选择具有相同的class属性值的元素。
	语法：.class属性值{}
```

**注意优先级关系：id选择器>类选择器>元素选择器**

- 扩展选择器（了解）

```css
1.通配符选择器:所有标签元素都会被选择
	语法：*{样式代码}  例如：*{ color: blueviolet;}
2.组合选择器/并集选择器：将多个选择器使用逗号并列到一起作用；
	语法：选择器1,选择器2,...{样式代码}
	例如：
				h2,.cDiv{
            color: blue;
            font-size: 50px;
        }
3.属性选择器：筛选具有相同属性的元素
	语法：选择器[属性名]{样式代码}     选择器[属性名="属性值"]{样式代码}
	例如：
			/*input[type]{
           color: red;
        }*/
       input[type="text"]{
           color: red;
       }
4.后代选择器：筛选某些元素的直接或者间接子元素（筛选出直接或者间接的孩子）
	语法：选择器1 选择器2{样式代码}
	例如：
		 div p{
            color: green;
            font-size: 50px;
        }
5.子元素选择器：筛选某些元素的直接子元素（筛选出直接的孩子）
	语法：选择器1>选择器2{样式代码}
	例如：
			div>p{
           color: green;
           font-size: 50px;
       }
6.伪类选择器：表示标签的不同状态，常用与超链接的不同状态；
	  a:link {color: #FF0000}		/* 未访问的链接 */
    a:visited {color: #00FF00}	/* 已访问的链接 */
    a:hover {color: #FF00FF}	/* 鼠标移动到链接上 */
    a:active {color: #0000FF}	/* 选定的链接 */
	可能需要清除缓存：ctrl+shift+del  不要将密码打钩
7.过滤选择器：过滤第几个子元素
	语法：选择器1>选择器2:first-child{样式代码}  表示找到第一个孩子
	语法：选择器1>选择器2:last-child{样式代码}  表示找到最后一个孩子
```

# 4.CSS常用属性

```css
1.字体、文本
    font-size：字体大小
    color：文本颜色
    text-align：水平对其方式
    line-height：行高,可以用来使文本垂直居中
    去掉下划线：text-decoration: none;
		p{
        border: 1px solid red;
        width: 500px;
        height: 100px;
        text-align: center;
        line-height: 100px;
    }
2. 背景
	background: url("img/logo.jpg") no-repeat center;
3. 边框
    border：设置边框，复合属性 常见用法：border:1px solid red; border-left:1px solid red;
    border-radius: 5px; 设置圆角
4. 尺寸
    width：宽度
    height：高度
5. 盒子模型：控制布局
    margin：外边距,可以设置4个值，分别代表上、右、下、左；如果设置1个值，那么代表4个外边距；margin可以设置负值
    padding：内边距，可以设置4个值，分别代表上、右、下、左；如果设置1个值，那么代表4个内边距；padding不能设置负值
    默认情况下内边距会影响整个盒子的大小
    box-sizing: border-box; 设置盒子的属性，让width和height就是最终盒子的大小
    水平居中：margin:auto;
    垂直居中：vertical-align: middle;
6.浮动:作用：让块级元素共占一行；几个元素共占一行就给这几个元素设置浮动
    float：浮动：块级元素默认是独占一行的，如果想让块级元素共占一行，那么就使用浮动属性
    float：left
    float：right
```