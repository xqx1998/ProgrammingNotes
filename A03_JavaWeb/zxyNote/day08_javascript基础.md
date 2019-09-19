# 1.javascript概念、作用和引入方式

- 概念：JavaScript是一门浏览器端的脚本语言，不需要编译，可以直接别浏览器解析执行。

- 作用：JavaScript可以制作网页的动画效果，表单验证，可以和服务器交互。

  > 发展史文档位置：**主页**--->导航栏中的**javascript**--->左侧**javascript**--->正文最下方红色的**"马上开始学习 JavaScript 高级教程吧**"--->左侧“**JavaScript 历史** ”

- javascript的组成部分

![javascript的组成部分.png](resource/javascript的组成部分.png)

- 引入方式

```html
1、内部js：将JavaScript代码写着一组<script>标签中。
    <script>
       alert("hello 传智");
    </script>
2、外部js：在<script>标签中通过src属性引入外部的js文件。
    <script src="js/a.js"></script>
```

**注意1：\<script>标签可以放在html文件的任意位置，影响的只是先后加载顺序，一般在开发中放在body标签的结尾处。**

**注意2：\<script>标签一旦使用了src属性引入了外部js文件，那么这个script标签中就不能再写js代码，写了也不会被执行。如果还有js代码要写，可以重写使用一个script标签。**

# 2.javascript变量和数据类型(重点1)

- **数据类型**

```javascript
1、原始数据类型：
    number：数字类型，包含整数/小数/NaN
    string：字符串类型，可以使用单引号或者双引号。
    boolean：布尔类型，只有true或者false两种值
    null：空对象的占位符
    undefined：变量没有被初始化的默认值。
2、引用数据类型：
		var date=new Date();
    document.write(date.toLocaleString());
```

> **注释：**您也许会问，为什么 typeof 运算符对于 null 值会返回 "Object"。这实际上是 JavaScript 最初实现中的一个错误，然后被 ECMAScript 沿用了。现在，null 被认为是对象的占位符，从而解释了这一矛盾，但从技术上来说，它仍然是原始值。

- **定义变量，使用var关键字**

```javascript
//定义变量a并赋值
var a=100;
document.write(a+"--->"+typeof a+"<br>");//100  number

//定义变量b
var b=100;
b="hello";
document.write(b+"--->"+typeof b+"<br>");//hello  string

//定义变量c d e f g
var c=100,d="hello js",e=true,f=null,g;
document.write(c+"--->"+typeof c+"<br>"); //100 number
document.write(d+"--->"+typeof d+"<br>"); //hello js  string
document.write(e+"--->"+typeof e+"<br>"); //true  boolean
document.write(f+"--->"+typeof f+"<br>"); //null
document.write(g+"--->"+typeof(g)+"<br>"); //undefined  undefined
```

**变量的特点：JavaScript是弱类型语言，变量的类型由值决定，值是什么类型变量就是什么类型。变量的类型可以随着不同的值改变。**

# 3.javascript运算符和流程控制

- **比较运算符：==和===的区别?**

```javascript
== ：只要值一样结果就是true。（常用）
=== ：类型和值都一样结果才是true。
```

**注意：在js中判断两个字符串是否相等，没有equals方法，只能使用==或者===**

- **逻辑运算符：在进行if判断时，非boolean类型的数据转换成boolean**

```javascript
说明：在js中只有&&、||、!为逻辑运算符。
转换原则：0或者NaN数字，""字符串，null和undefined转换成false，其他都转换成true
if(100){} //true
if(-1){} //true
if("hello"){} //true
if("false"){} //true
if("0"){} //true

if(0){} //false
if(""){}//false
if(null){}//false
if(undefined){}//false
```

- 流程控制语句

```javascript
if(条件){}
if(条件){}else{}
if(条件){}else if(条件){}...else{}

while(循环条件){}
do{}while(循环条件);

for(var i=1;i<=100;i++){}
```

**注意：for循环快捷键: itar回车正着遍历  或者  ritar回车就是倒着遍历**

- 案例-九九乘法表

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>九九乘法表</title>
    <style>
        td{
            border: 1px solid gray;
            padding: 5px;
        }
    </style>
    <script>
        //输出table开始标签
        document.write("<table cellspacing='0' align='center'>");

        for (var i = 1; i < 10; i++) {
            //输出tr开始标签
            document.write("<tr>");
            for (var j = 1; j <=i; j++) {
               document.write("<td>");
               document.write(i+"*"+j+"="+(i*j));
               document.write("</td>")
            }
            //document.write("<br>")
            //输出tr结束标签
            document.write("</tr>")
        }
        //输出table的结束标签
        document.write("</table>")
    </script>
</head>
<body>
</body>
</html>
```

# 4.javascript函数(重点2)

- **函数定义的2种方式**

```javascript
方式1：普通函数
    声明格式：
      function 方法名(变量名,变量名,...){
        //方法体
      }
    调用格式：方法名(参数值);
方式2：匿名函数
		声明格式：
      var 变量名=function (变量名,变量名,...){
                  //方法体
                }
      说明：变量名将来就是方法名
    调用格式：变量名(参数值);
```

特点1：在JavaScript中，函数是一个对象，方法名就是对象名。

特点2：在JavaScript中，函数只有覆盖没有重载，函数的调用只和方法名有关，和参数列表无关。

特点3：在函数中，所有的参数都会被封装到一个叫arguments数组中，可以使用索引获取参数值。

特点4：在函数中，如果有return语句，那么就返回return后面的值；如果没有return语句，那么默认返回undefined。

# 总结

```javascript
1、数据类型和定义变量（重点1）
	数据类型：
  	原始数据类型：number、string、boolean、null、undefined。
    引用数据类型：var date=new Date();
	定义变量：使用var关键字，var不是数据类型。
  	var a=100,b="hello";
	注意：JavaScript属于弱类型语言，变量的类型和值相关，值是什么类型变量就是什么类型，动态可变。如果要打印变量的类型，使用typeof关键字。例如：document.write(typeof a);
2、运算符和流程控制语句
	1、==和===的区别？
			== ：值一样结果就是true。
			=== ：类型和值都一样结果才是true。
		注意：在JavaScript中比较字符串使用==或者===。没有equals方法。
	2、在参与if判断时，非boolea类型的数据转换成boolean类型？
  	0/NaN数字，""，null，undefined转换成false，其他的都转换成true
  3、for循环:快捷键：itar+回车正着遍历，ritar+回车倒着遍历。
  	for(var i=0;i<100;i++){}
3、定义函数以及函数的特点（重点2）
	方式1：普通函数
  	声明格式：
    	function 方法名(变量名,变量名,...){
        //方法体
      }
		调用方法格式：方法名(值,...);
  方式2：匿名函数
    声明格式：
    	var 变量名=function (变量名,变量名,...){
                  //方法体
                }
		调用方法格式：变量名(值,...);
  特点1:在JavaScript中，函数是一个对象，方法名就是对象名。
  特点2:在JavaScript中，函数只有覆盖没有重载，函数的调用只和方法名有关，和参数列表无关。
  特点3:在函数中，所有的参数都会被存储到一个叫arguments数组中，可以使用索引获取参数值。
  特点4:在函数中，如果有return语句，那么就返回return后面的值。如果没有return语句，那么默认返回undefined。

预习内容：
    02_DOM的简单学习。
        掌握：dom的操作步骤
    03_事件的简单学习。
        掌握：事件的两种绑定方式。
```

# 5.javascript常用对象

> 文档位置：**主页**--->导航栏中的**javascript**--->左侧**javascript**--->正文中间方红色的**"完整的 JavaScript  对象参考手册（包含实例）"**

- 定义数组的4种方式

```javascript
var arr=[值1,值2,...];  //定义数组并初始化值。
var arr=new Array(); //定义长度为0数组；
var arr=new Array(size); //定义一个长度为size的数组
var arr=new Array(值1,值2,...); //定义数组并初始化值为值1,值2,...
//例如
var arr=[100,200,"hello",true,false];
//遍历
for (var i = 0; i < arr.length; i++) {
  alert(arr[i]);
}
```

**数组的特点：数组的长度是可变的，可以存储任意类型的数据；**

- 正则对象

```javascript

```

- Global对象(全局对象)：这个对象的方法不需要使用对象调用，直接写方法就行

```javascript

```

# 6.补充

- javascript中的中的输出方式

```java

```

