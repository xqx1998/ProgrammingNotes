## 1.jquery概念和快速入门

- jquery概念：是一个js框架，封装了js代码，本质是一个js文件，简化我们js代码的书写。
- 使用步骤

```javascript
1.将jquery文件复制到项目中，在需要的html引入js文件
	<script src="js/jquery-3.3.1.min.js"></script>
2.使用jquery提供的函数；
```

## 2.jQuery核心函数(重点1)

### 2.1 jquery核心函数：\$()：参数值不一样作用就不一样

- **\$(匿名函数)：页面加载完成事件**

```javascript
$(function(){
    //当页面加载完成之后，匿名函数会执行
});
```

- **\$(html/js对象)**

```javascript
如果传递的是html代码：创建html代码对应的标签元素对象（可以包含子元素）
	例如:$("<div><p>Hello</p></div>").appendTo("body");//动态创建一个 div 元素（以及其中的所有内容），并将它追加到 body 元素中
如果传递的是js对象：将js对象转换成jquery对象
	 js -- > jq : $(js对象)
```

- **\$("selector")：传递css选择器，用于根据css选择器获取询元素对象**

```javascript
$("p"):获取所有的p元素；
$("#pid"):获取id="pid"的元素
```

### 2.2. jquery对象和js对象相互转换(重点)

```javascript
js对象：使用document.getElementByxxx获取的对象都是js对象；
jquery对象：我们自己使用$()函数获取对象都是jquery对象，jquery对象也是js对象的一个包装对象；

js对象和jquery对象之间的关系类型于java中String和StringBuilder的关系？
	js对象相当于String对象，jquery对象相当于StringBuilder；String和StringBuilder可以相互转换，但是方法不能混着调用；同理js对象和jquery对象也可以相互转换，但是各自的方法不能相互调用；
    jq -- > js : jq对象[索引] 或者 jq对象.get(索引)，如果jquery对象不是数组，那么索引值就是0；
    js -- > jq : $(js对象)
```

## 3.jQuery事件绑定以及样式控制(重点2)

### 3.1 常见的事件(重点)

```javascript
0.页面加载完成事件
    $(function(){
		//当页面加载完成之后，匿名函数会执行
    });
1.单击事件
    jquery对象.click(function(){
		//当元素被单击之后，匿名函数就会执行
    })
	使用代码触发单击事件：jquery对象.click(); //触发之后上述的匿名函数就执行
2.失去焦点事件
	jquery对象.blur(function(){
		//当元素失去焦点之后，匿名函数就会执行
    })
3.表单提交事件
    $("form").submit( function () {
      return false; //return true表示运行提交，return false表示阻止提交，但是不写默认提交
    });
```

### 3.2 样式控制

```javascript
css()函数：既可以或者样式值，还可以设置样式值；
	获取样式值：var value＝jquery元素对象.css("属性名");
    设置样式值：
    	jquery对象.css("属性名","属性值"); //设置一组样式值；
		$("p").css({"属性名": "属性值", "属性名": "属性值" });//设置多组样式值；
	说明：{"属性名": "属性值", "属性名": "属性值"}在js中称为对象；
```

## 4.jQuery选择器

```javascript
1. 基本选择器(掌握)
	1. 标签选择器（元素选择器）
		* 语法： $("html标签名") 获得所有匹配标签名称的元素
	2. id选择器 
		* 语法： $("#id的属性值") 获得与指定id属性值匹配的元素
	3. 类选择器
		* 语法： $(".class的属性值") 获得与指定的class属性值匹配的元素
	4. 并集选择器：
		* 语法： $("选择器1,选择器2....") 获取多个选择器选中的所有元素
2. 层级选择器（掌握）
	1. 后代选择器
		* 语法： $("A B ") 选择A元素内部的所有B元素		
	2. 子选择器
		* 语法： $("A > B") 选择A元素内部的所有B子元素
3. 属性选择器（了解）
	1. 属性名称选择器 
		* 语法： $("A[属性名]") 包含指定属性的选择器
	2. 属性选择器
		* 语法： $("A[属性名='值']") 包含指定属性等于指定值的选择器
	3. 复合属性选择器
		* 语法： $("A[属性名='值'][]...") 包含多个属性条件的选择器
4. 过滤选择器（了解）
	1. 首元素选择器 
		* 语法： :first 获得选择的元素中的第一个元素
	2. 尾元素选择器 
		* 语法： :last 获得选择的元素中的最后一个元素
	3. 非元素选择器
		* 语法： :not(selector) 不包括指定内容的元素
	4. 偶数选择器
		* 语法： :even 偶数，从 0 开始计数
	5. 奇数选择器
		* 语法： :odd 奇数，从 0 开始计数
	6. 等于索引选择器
		* 语法： :eq(index) 指定索引元素
	7. 大于索引选择器 
		* 语法： :gt(index) 大于指定索引元素
	8. 小于索引选择器 
		* 语法： :lt(index) 小于指定索引元素
	9. 标题选择器
		* 语法： :header 获得标题（h1~h6）元素，固定写法
5. 表单过滤选择器（了解）
	1. 可用元素选择器 
		* 语法： :enabled 获得可用元素
	2. 不可用元素选择器 
		* 语法： :disabled 获得不可用元素
	3. 选中选择器 
		* 语法： :checked 获得单选/复选框选中的元素
	4. 选中选择器 
		* 语法： :selected 获得下拉框选中的元素
```

## 5.jQuery的DOM操作

### 5.1 内容操作（重点）

```javascript
1. html(): 获取/设置元素的标签体内容   <a><font>内容</font></a>  --> <font>内容</font>
	不传参：获取内容体html代码；
    传参：设置内容体html代码
2. text(): 获取/设置元素的标签体纯文本内容   <a><font>内容</font></a> --> 内容
3. val()： 获取/设置元素的value属性值
```

## 5.2 属性设置

```javascript
1. attr(): 获取/设置元素的属性
	获取属性值：	var value=jquery对象.attr("属性名")
	设置属性：
    	jquery对象.attr("属性名","属性值");//设置一组属性
		jquery对象.attr({ "属性名": "属性值","属性名": "属性值",... });
2. removeAttr("属性名"):删除属性
3. prop():获取/设置元素的属性，使用方式同attr()方法一样
4. removeProp("属性名"):删除属性
```

**注意：如果操作的是checked、selected、disable等状态属性，使用prop()方法，操作其他属性使用attr()方法**

### 5.3 class属性设置

```javascript
1. addClass():添加class属性值
2. removeClass():删除class属性值
3. toggleClass():切换class属性
    * toggleClass("one"): 
    * 判断如果元素对象上存在class="one"，则将属性值one删除掉。  如果元素对象上不存在class="one"，则添加
```

### 5.4 CRUD操作

```javascript
1.追加子元素也叫添加子元素
	父元素.append(子元素/html代码)；//在父元素内容后面追加子元素
    子元素.appendTo(父元素)；//子元素添加到父元素内部后面
2.删除元素
	被删除元素.remove();
3.清空所有子元素
	父元素.empty();
```

## 6.jQuery案例

### 6.1 隔行换色

```html
<script>
    //需求：将数据行的奇数行背景色设置为 pink，偶数行背景色设置为 yellow
    $(function () {
        //1. 获取数据行的奇数行的tr，设置背景色为pink
        $("tr:gt(1):odd").css("backgroundColor","pink");
        //2. 获取数据行的偶数行的tr,设置背景色为yellow
        $("tr:gt(1):even").css("backgroundColor","yellow");
    });
</script>
```

### 6.2 全选和全不选

```html
<script>
    //分析：需要保证下边的选中状态和第一个复选框的选中状态一致即可
    function selectAll(obj){
        //获取下边的复选框
        $(".itemSelect").prop("checked",obj.checked);
    }
</script>
```

### 6.3 QQ表情选择

```html
<script>
    //需求：点击qq表情，将其追加到发言框中
    $(function () {
        //1.给img图片添加onclick事件
        $("ul img").click(function () {
            //2.追加到p标签中即可。
            $(".word").append($(this).clone());
        });
    });
</script>
```

### 6.4 下拉列表中条目左右移动

```html
<script>
    //需求：实现下拉列表选中条目左右选择功能
    $(function () {
        //toRight
        $("#toRight").click(function () {
            //获取右边的下拉列表对象，append(左边下拉列表选中的option)
            $("#rightName").append($("#leftName>option:selected"));
        });
        //toLeft
        $("#toLeft").click(function () {
            //appendTo   获取右边选中的option，将其移动到左边下拉列表中
            $("#rightName > option:selected").appendTo($("#leftName"));
        });
    });
</script>
```