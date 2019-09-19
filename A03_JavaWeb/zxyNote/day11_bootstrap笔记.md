# 昨天内容回顾

```javascript
常见的事件绑定方式：onclick、onblur、onload、onsubmit、onchange
元素对象.onclick/onblur/onchange=function(){
  
}
页面加载完成的事件：
window.onload=function(){}
表单提交事件：
	方式1:
		<form action="" method="" onsubmit="return checkForm()"></form>
		funcrion checkForm(){
      	 return true/false；//true表示允许提交，false表示阻止提交，不写默认提交。
    }
	方式2:
		form元素对象.onsubmit=function(){
      	return true/false；//true表示允许提交，false表示阻止提交，不写默认提交。
    }
超链接的单击事件：
	<a href="javascript:void(0)" onclick="fun()"></a>
	<a href="javascript:fun()"></a>
```

# 1.准备模板

响应式布局：一套页面能够兼容不同分辨率和尺寸的设备(pc电脑，平板电脑，手机)

> 文档位置：起步---->右侧“基本模板”

```html
1、复制资源：css目录、font目录、js目录+jquery-3.2.1.min.js复制到js目录中
2、在开发文档中找到基础模板，将基础模版复制到我们的html文件中
3、修改模版中引入的css，js。
<!DOCTYPE html>
<html lang="zh-CN">
  <head>
    <meta charset="utf-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1">
    <!-- 上述3个meta标签*必须*放在最前面，任何其他内容都*必须*跟随其后！ -->
    <title>基础模版</title>

    <!-- Bootstrap -->
    <link href="css/bootstrap.min.css" rel="stylesheet">
    <!-- jQuery (necessary for Bootstrap's JavaScript plugins) -->
    <script src="js/jquery-3.2.1.min.js"></script>
    <!-- Include all compiled plugins (below), or include individual files as needed -->
    <script src="js/bootstrap.min.js"></script>
  </head>
  <body>
    <h1>你好，世界！</h1>

  </body>
</html>
```

# 2.栅格系统的使用和注意事项

> 文档位置：全局 CSS 样式---->栅格系统

- 使用的基本思路

```html
1、定义容器：container或者container-fluid
2、定义行，一行默认有12列也叫12个格子。 row
3、定义列，规定元素占几个格子 ：col-xs/sm/md/lg-占用格子的个数
<!--定义容器-->
<div class="container-fluid">
  <!--定义行-->
  <div class="row">
    <!--定义列-->
    <div class="col-sm-2">格子1</div>
    <div class="col-sm-2">格子2</div>
    <div class="col-sm-2">格子3</div>
    <div class="col-sm-2">格子4</div>
    <div class="col-sm-2">格子5</div>
    <div class="col-sm-2">格子6</div>
    <div class="col-sm-2">格子7</div><!--开始在下一行展示-->
    <div class="col-sm-2">格子8</div>
    <div class="col-sm-2">格子9</div>
    <div class="col-sm-2">格子10</div>
    <div class="col-sm-2">格子11</div>
    <div class="col-sm-2">格子12</div>
  </div>
</div>
```

- 注意事项

```html
1、如果一行中定义的元素占的格子数超过12个，超出的元素会自动换行
2、如果给元素定义了尺寸属性，那么会向上兼容，不向下兼容（在比设置小的屏幕上独占一行）
```

# 3.全局css样式、组件、插件

```html
1. 全局CSS样式：
	* 按钮：class="btn btn-default"
	* 图片：
		 class="img-responsive"：图片在任意尺寸都占100%
		图片形状
				<img src="..." alt="..." class="img-rounded">：方形
				<img src="..." alt="..." class="img-circle"> ： 圆形
				<img src="..." alt="..." class="img-thumbnail"> ：相框
	* 表格
			table
			table-bordered
			table-hover
```

# 4.补充：javascript中自定义对象

- 方式1：构造函数(了解)

```javascript
/*
            java中的构造方法
                public class Person{
                   ...
                   public Person(String name,int age){
                        this.name=name;
                        this.age=age;
                   }
                }
        */
//1 定义构造函数，类似于Java中的构造方法
function Person(name,age) {
  this.name=name;
  this.age=age;
  //只能使用匿名函数的方式给person定义函数
  this.show=function () {
    alert("show is running...")
  }
}
//2 使用new关键字创建对象
var p=new Person("杨锌怒",20);
console.info(p);
console.info(p.name+","+p.age);
//对象创建完成之后还可以继续添加属性
p.address="武汉";
console.info(p.address);
p.show();
```

- **方式2：直接量方式，对象直接创建并初始化好了（重点）**

```javascript
//不需要定义构造，直接创建对象，语法是 var 对象名={属性名:属性值,属性名:属性值,...}
var p={
  name:"杨锌怒",
  age:20,
  show:function () {
    alert("show method is running...")
  }
};
//打印结果
console.info(p);
console.info(p.name+","+p.age);
//对象创建完成之后还可以继续添加属性
p.address="武汉";
console.info(p.address);
p.show();
```

# 5.补充：ECMAScript6新特性

## 1.let和const关键字(了解)

```html
let和const关键字(了解)
const关键字：用于声明常量的，在ECMAScript6之前没有常量的概念只有变量，但是在es6之后就分常量和变量。
let关键字：用于声明变量，和var类似，有点区别。
1、var是es6之前定义变量使用的关键字，let是es6之后定义变量使用的关键字，当然es6之后也可以使用var。
2、var声明变量作用范围只有全局范围，函数范围。let声明的变量有全局范围，代码块范围，函数范围。
3、var声明的变量用于在作用范围的最顶端，let声明的变量必须要先声明后使用。
```

## 2.模板字符串(重点)

```java
模版字符串：比普通字符串更高级的字符串，普通字符串能调用的属性和方法，模版字符串也都能调用。
区别：
   1、普通字符串使用""或者''，模版字符串使用反引号``。
   2、普通字符串中如果需要使用变量，那么就需要使用字符串拼接。模版字符串不需要使用字符串拼接，使用EL表达式。
EL表达式：expression language 。语法：${表达式}，{}中的表达式可以是变量、常量、有运算符，可以执行运算，可以执行方法。
document.getElementById("tab").innerHTML+=`<tr>
          <td>${id}</td>
          <td>${name}</td>
          <td>${gender}</td>
          <td><a href="javascript:void(0)" onclick="del(this)">删除</a></td>
        </tr>`;
```

## 3.es6函数(了解)

- 扩展运算符

```javascript
/*
   1、扩展运算符，类似于Java中可变参数符号...
*/
//计算任意个数之和
function fun3(...a) {
  let sum=0;
  for (let i = 0; i < a.length; i++) {
    sum+=a[i];
  }
  console.info(`sum=${sum}`)
}
fun3(100,200,300);
```

- 箭头函数:类似于java中的lambda表达式

```javascript
/*
   箭头函数：类似Java的lambda表达式
   Lambda表达式格式
        (参数列表)->{}
   箭头函数格式：
        (参数列表)=>{}
*/
setInterval(function () {
  console.info("hello")
},1000);
setInterval(()=>{
  console.info("hello")
},1000);
//简写格式和Lambda表达式一样
setInterval(a=>console.info(`world-${a}`),1000)
```

## 4.for...of遍历(重要):增强for循环

```javascript
/*
   语法格式：
      for(let/var 变量 of 数组){
          //循环体
      }
*/
let arr=[100,200,true,"hello",false];
//遍历
for(let a of arr){
  console.info(a);
}
//快捷键 iter+回车
for (let b of arr) {
  console.info(b)
}
```

## 5.set和map集合(了解)

```javascript
/*
            set集合
            1、创建对象
            2、调用方法存值
            3、遍历取值
         */
//1、创建对象
let set = new Set();
//2、调用方法存值
set.add(100);
set.add("hello");
set.add(true);
set.add(100);
//3、遍历取值
for (let e of set) {

  console.info(e);
}

/*
            map集合
            1、创建对象
            2、调用方法存值
            3、遍历取值
         */
//1、创建对象
let map = new Map();
//2、调用方法存值
map.set("name","杨锌怒");
map.set("age",20);
map.set("address","武汉");
console.info(map.get("name"))
//3、遍历
//方式1：键找值
let keys = map.keys();
for (let key of keys) {
  console.info(`${key}----${map.get(key)}`)
}
//方式2：entry对象
let entries = map.entries();
for (let entry of entries) {
  console.info(`${entry[0]}=======${entry[1]}`)
}
```

