# 昨天内容回顾

```javascript
函数的两种定义方式：
方式1:普通函数
	function 方法名(变量1,变量2,...){
    
  }
	调用方法：方法名(值1,值2,...);
方式2:匿名函数
  var 变量名=function (变量1,变量2,...){
    
  					}
	调用方法:变量名(值1,值2,...);
特点1:在JavaScript中，函数的是一个对象。
特点2:在JavaScript中，函数只有覆盖没有重载，方法的调用之和方法名有关，和参数列表无关。
特点3:在函数中，所有的参数值都会被存储到一个叫arguments数组中，可以通过索引获取参数值。
特点4:在函数中，如果有return语句，那么就返回return后面的值。如果没有return语句，默认返回undefined。
```

# 1.javascript常用对象

> 文档位置：**主页**--->导航栏中的**javascript**--->左侧**javascript**--->正文中间方红色的**"完整的 JavaScript  对象参考手册（包含实例）"**

## 1.1 定义数组的4种方式

```javascript
var arr=[值1,值2,...];  //定义数组并初始化值。  （掌握）
var arr=new Array(); //定义长度为0数组；  （掌握）
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

## 1.2 正则对象

```javascript
//1、定义正则对象
var reg=new RegExp("^\\w{6,12}$"); //不推荐使用
var reg2=/^\w{6,12}$/;  //(推荐使用)不能使用引号
//2、调用test方法校验
var f = reg.test("zhangsan");
alert(f);  //true

var f2 = reg2.test("yangxin怒");
alert(f2); //false
说明：^表示以xxx开头，$表示以xxx结尾。
```

## 1.3 Global对象(全局对象)

**注意：这个对象的方法不需要使用对象调用，直接写方法就行**

```javascript
1、编码和解码的方法
	encodeURI()	编码，不会对:、/、&等特殊字符进行编码，但是会对中文编码。
	decodeURI()	解码。
  
  encodeURIComponent()	编码，会对:、/、&等特殊字符进行编码，也会对中文编码。
  decodeURIComponent()	解码。。
2、将字符串类型的数字转换成number类型
	parseFloat("字符串类型的小数")	解析一个字符串并返回一个浮点数。
	parseInt("字符串类型的整数")	解析一个字符串并返回一个整数。 
```

# 2.DOM和事件的简单学习

## 2.1 DOM操作的基本思路(重点1)

DOM操作：操作html标签就叫做DOM操作。

```javascript
1、获取标签元素对象
	var 元素对象=document.getElementById("id值");
2、操作标签的属性或者内容体/标签体
	操作属性：
  	获取属性值：var 变量=元素对象.属性名;  例如：alert(lightEl.src);
    设置属性值：元素对象.属性名="属性值"; 例如：lightEl.src="img/on.gif";
  操作内容体：
  	获取内容体：var 变量=元素对象.innerHTML;  例如：alert(titleEl.innerText);
    设置内容体：元素对象.innerHTML="内容体";  例如：titleEl.innerHTML="黑马程序员";
    追加内容体：元素对象.innerHTML+="内容体"  例如：titleEl.innerHTML+="黑马程序员";
	innerHTML和innerText的区别？
  	innerHTML设置的内容会被浏览器当作html代码解析，innerText设置的内容会在浏览器原样展示，不会被浏览器解析。
```

**注意：一定要确保标签先加载，然后通过document.getElementById("id值")获取。**

## 2.2 事件的两种绑定方式(重点2)

事件：在浏览器上操作某些控件之后发生的一系列事情就是事件，要发生的事情是我们通过代码实现的。

```javascript
方式1:在html标签上添加事件属性
	第一步：在标签上添加事件属性，属性值就是要执行的代码。
		<input type="button" value="按钮" onclick="fun()">
  第二步：定义要执行的方法
		function fun(){}
方式2:给标签对象的事件属性赋一个函数对象
	第一步：获取标签的元素对象
  	var 元素对象=document.getElementById("id值");
	第二步：给对象的事件属性一个函数对象
  	元素对象.onclick=函数对象；
		例如：
  		元素对象.onclick=fun;
			元素对象.onclick=function(){}
```

**注意：获取标签元素对象之前一定要确保标签先被加载**

## 2.3 案例-灯泡开关

```html
<body>
<img id="light" src="img/off.gif" alt="">
<script>
    //第一步：获取标签的元素对象
    var light = document.getElementById("light");

    var flag = false;//代表关闭
    //第二步：给对象的事件属性一个函数对象
    light.onclick=function (){
        //修改的图片的src属性
        light.src=flag?"img/off.gif":"img/on.gif";
        //修改flag的值
        flag=!flag;
    }
</script>
</body>
```

# 3.BOM操作

- BOM：浏览器对象模型，将浏览器的各个部分封装成不同的对象；

## 3.1 window对象

```java
1、弹框相关的方法：
    //弹出警告框
    alert("不允许操作！");
    //弹出确认框（常用）
    var f = confirm("你确认要删除吗?");
    document.write(f);// //点击"确认"返回true，点击"取消"返回false
    //弹出输入框
    var name = prompt("请输入姓名:");
    document.write(name);
2、定时器相关方法
	一次性定时器：
		var id=setTimeout(code/function,毫秒值);
				参数1:定时器要执行的js代码或者函数对象，代码或者函数只执行一次
				参数2:毫秒数。
				返回值：返回定时器的唯一编号id，用于取消定时器
		clearTimeout(id);
	循环定时器：
		var id=setInterval(code/function,毫秒值);
				参数1:定时器要执行的js代码或者函数对象,代码或者函数循环执行
				参数2:毫秒数。
				返回值：返回定时器的唯一编号id，用于取消定时器
		clearInterval(id);

	辨别？
				setInterval("fun()",2000); //正确
        setInterval("fun",2000); //错误
        setInterval(fun(),2000); //错误，将方法的返回值传递个定时器
        setInterval(fun,2000); //正确
```

- 案例-轮播图

```html
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="UTF-8">
    <title>轮播图</title>
    <script>
      //1 定义一个循环定时器
      setInterval("fun()",2000)

      var number=1; //默认第一张图片
      //定义定时器要执行的函数
      function fun() {
        //切换到下一张,如果已经是第三张，那么就切换到第一张图片。
        number=++number>3?1:number;
        //2 在要执行的函数中，获取img元素对象
        var imgEl = document.getElementById("banner");
        //3 修改元素对象的src属性
        imgEl.src="img/banner_"+number+".jpg";
      }
    </script>
  </head>
  <body>
    <img id="banner"  src="img/banner_1.jpg" width="100%">
  </body>
</html>
```

## 3.2 location对象：地址栏对象(重点3)

```javascript
location对象：表示地址栏对象

1、获取地址栏地址和设置地址
	var url=location.href
  location.href="3_案例1-电灯开关.html"; //设置完成之后页面会跳转。
2、获取请求参数，?后面的参数
	location.search,但是结果会包含?，可以使用字符串截取去掉?，例如：	location.search.substring(1)
3、页面刷新
	location.reload()
```

- 案例-定时跳转

```html
<body>
  <p align="center">
    <font color="red"><span id="time">5</span></font>秒之后,自动跳转<a href="3_案例1-电灯开关.html">首页</a>
  </p>

  <script>
    //1、循环定时器
    var id=setInterval(fun,1000);
    var time=5;
    //获取元素对象
    var spanEl = document.getElementById("time");
    //2 在定时器的函数中，使用location.href页面跳转
    function fun() {
      time--;//减1秒
      //设置显示的秒数
      spanEl.innerHTML=time;
      if(time==0){
        //0秒之后页面跳转
        location.href="3_案例1-电灯开关.html";
        //消除定时器
        clearInterval(id);
      }
    }
  </script>
</body>
```

# 4.总结

```javascript
1、JavaScript常用对象
	1、Array数组对象，数组的四种创建方式。
  2、Date日期时间对象：var date=new Date();
  3、Math数学对象:Math.floor(数字) 向下取整。Math.ceil(数字) 向上取整
  4、RegExp正则对象
  	创建正则对象：var regExp=/正则表达式/;
		正则校验：var flag=regExp.test(字符串);
  5、Global对象：方法不需要对象调用，直接写方法名调用即可。
  	编码和解码的方法（2组）；
    var num=parseInt(字符串类型的数字); var num=parseFloat(字符串类型的数字);
2、DOM和事件的简单学习（重点*******）
	2.1 DOM操作的基本思路（重点1）
  	1、获取元素对象：var 元素对象=document.getElementById("id值");
		2、操作属性或者内容体/标签体
			操作属性：
      	获取属性：var 变量=元素对象.属性名; 例如： alert(imgEl.src);
        设置属性：元素对象.属性名="属性值"; 例如：imgEl.src="img/on.gif"
      操作内容体：
      	获取内容体：var 变量=元素对象.innerHTML; 例如：alert(titleEL.innerHTML)
        设置内容体：元素对象.innerHTML="新内容"; 例如：titleEL.innerHTML="黑马"
        追加内容体：元素对象.innerHTML+="新内容"; 例如：titleEL.innerHTML+="黑马"
	2.2 事件的两种绑定方式（重点2）
  	1、给html标签添加事件属性
        第一步：给html标签添加事件属性，属性值为要执行的js代码。
          <input type="button" value="按钮" onclick="fun()">
        第二步：定义要执行的函数：
           function fun(){}
    2、给元素对象的事件属性赋一个函数对象。
    		第一步：获取元素对象：var 元素对象=document.getElementById("id值");
        第二步：给元素对象的事件属性赋一个函数对象
        	元素对象.onclick=funtion(){}  
					或者：
          元素对象.onclick=fun; //前提：要先定义一个名字叫fun的函数
3、BOM对象，大部分都是了解
	window对象：（了解）
  	1、弹框的方法：
      alert("警告信息");
      var f=confirm("你确定要删除吗?");//点击“确定"返回true，点击“取消"返回false
      var name=prompt("请输入姓名："); //输入框
		2、定时器
    	一次性定时器：
      		var id=setTimeout(code/function,毫秒值);
					取消定时器：
          	clearTimeout(id);
      循环定时器：
      		var id=setInterval(code/function,毫秒值);
					取消定时器：
          	clearInterval(id);
			辨别？
      	setInterval("fun()",2000); //正确
				setInterval("fun",2000);//错误
        setInterval(fun(),2000);//错误，将fun()方法的返回值传递给定时器
        setInterval(fun,2000);//正确
  location对象：地址栏对象（重点3）
  	页面跳转：location.href="要跳转的路径";
		获取地址栏参数：location.search.subString(1);//不包含?
		刷新当前页面:location.reload();
```

