# 昨天内容回顾

```javascript
1、DOM操作的基本思路
	第一步：获取元素对象：var 元素对象=document.getElementById("id值");
  第二步：操作属性或者内容体
  	操作属性：
  		获取属性：var 变量=元素对象.属性名;
			设置属性：元素对象.属性名="属性值";
		操作内容体：
    	获取内容体：var 变量=元素对象.innerHTML;
			设置内容体：元素对象.innerHTML="新内容体";
			追加内容体：元素对象.innerHTML+="新内容体";
2、事件的两种绑定方式
	方式1:在html标签上添加事件属性。
  		第一步：在html标签上添加事件属性
      		<input type="button" value="按钮" onclick="fun()"/>
      第二步：定义事件要执行的函数
					function fun(){}
  方式2:给元素对象的事件属性赋一个函数对象。
  		第一步：获取元素对象：var 元素对象=document.getElementById("id值");
			第二步：给给元素对象的事件属性赋一个函数对象
      		元素对象.onclick=fun;  //需要定义一个fun函数，上面有。
					元素对象.onclick=function(){}
3、location对象
	页面跳转：location.href="要跳转的路径";
  获取请求参数：var 变量=location.search.substring(1);
  刷新页面：location.reload();
```

# 1.DOM操作 

## 1.1 DOM操作概念以及DOM树

DOM操作：操作html页面中的标签。

DOM树：html文档被浏览器加载进内存之后，形成一个document对象，这个对象也叫文档对象，代表整个html文档。

> 文档位置：**主页**--->导航栏"**javascript**"--->左侧"**HTML DOM**"

![](resource/理解DOM树.bmp)

## 1.2 document对象:获取Element对象(重要)

```javascript
document.getElementById("id属性值")：根据id属性值获取元素对象，一个对象。（常用）
document.getElementsByTagName("标签名"):根据标签名获取元素对象，数组。
document.getElementsByClassName("class属性值"):根据class属性值获取元素对象，数组。
document.getElementsByName("name属性值"):根据name属性值获取元素对象，数组。
```

- 其他方法(了解)

```javascript
创建元素对象：document.createElement("标签名")；
创建文本对象：document.createTextNode("文本");
```

## 1.3 Element对象（了解）

```javascript
设置属性：setAttribute(name,value);
移除属性：removeAttribute(name);
```

## 1.4 Node对象(了解)

```javascript
方法：
	添加子元素：元素对象.appendChild(子元素对象);
	移除子元素：元素对象.removeChild(子元素对象);
属性：
	获取父节点：元素对象.parentNode;
```

## 1.5 补充：超链接的点击事件(掌握)

**说明：超链接自带点击效果，一点之后页面跳转。href="javascript:void(0)"表示点击超链接之后不再是页面跳转，而是执行冒号后面的JavaScript代码,执行void()方法，什么事都不干，也不返回任何结果，也就将页面跳转禁用掉了。**

```javascript
超链接点击事件方式1:禁用页面跳转，添加onclick事件属性
    <a href="javascript:void(0)" onclick="fun()">点击超链接</a>
    function fun() {
      alert("你好，杨锌怒");
    }
超链接点击事件方式2:将页面跳转效果转换成单击事件
    <a href="javascript:fun()">点击超链接</a>
    function fun() {
      alert("你好，杨锌怒");
    }
```

## 1.6 innerHTML属性(重要)

```javascript
获取内容体：var 变量=元素对象.innerHTML;
设置内容体：元素对象.innerHTML="新内容体";
追加内容体：元素对象.innerHTML+="新内容体"
innerText和innerHTML的区别？
	innerHTML设置的内容体会被浏览器当作html代码解析，获取的也是html代码。
  innerText设置的内容不会被浏览器解析，会原模原样的展示在浏览器上，获取的也只是文本内容，不是html代码。
```

## 1.6 操作样式

```javascript
方式1:直接给设置样式值,适用于设置少量的样式。
	元素对象.style.属性名="属性值";
方式2:先写样式，在通过添加class属性的方式添加样式，适用于一次性设置多组样式。
	第一步：在style标签中使用类选择器定义样式。
  第二步：设置元素对象的class属性值为选择器名称。
```

# 2.事件

## 2.1 单击事件 onclick

```javascript
元素对象.onclick=function(){
   //当元素被单击之后触发匿名函数。this表示当前元素对象
}
```

## 2.2 失去焦点事件 onblur

```javascript
元素对象.onblur=function(){
   //当元素失去焦点之后触发匿名函数。this表示当前元素对象
}
```

## 2.3 页面加载完成事件 onload

```javascript
window.onload=function(){
   //当页面加载完成之后触发匿名函数。我们就可以获取元素对象。this表示当前元素对象
}
```

## 2.4 表单提交事件 onsubmit(重要)

```javascript
方式1:在html标签上添加事件属性。
	第一步：在html标签上添加事件属性
  		<form action="" method="" onsubmit="return checkForm()">...</form>
	第二步：定义事件要执行的函数
      function checkForm(){        
        //当submit按钮被点击之后触发表单提交事件
        return true/false;//返回true表示提交表单，返回false表示阻止提交。默认是提交
      }
方式2:给元素对象的事件属性赋一个函数对象。
	第一步：获取元素对象：var 元素对象=document.getElementById("formId");
	第二步：给给元素对象的事件属性赋一个函数对象
    元素对象.onsubmit=function(){//this表示当前元素对象
      //当submit按钮被点击之后触发表单提交事件
      return true/false;//返回true表示提交表单，返回false表示阻止提交。默认是提交
    }
```

## 2.5 内容改变事件 onchange

```javascript
元素对象.onchange=function(){
   //内容被改变之后触发匿名函数。this表示当前元素对象
}
说明：一般用在下拉列表中内容改变了。例如选择了一个省份，那么后面的输入框中的市就会发生变化。
```





