# JavaScript

# 概述

运行在浏览器端的脚本语言, 由浏览器解释执行, 让网页和用户可交互

- 前段开发三大块:

  - HTML: 负责网页结构
  - CSS: 负责网页样式
  - JavaScript: 负责网页行为
- JS三种使用方式:

  - 行内式: 主要用于事件

    ```python
    <input type="button" name="" onclick="alert('OK!');">
    ```
  - 内嵌式

    ```python
    <script type="text/javascript">
    	alert('OK!');
    </script>
    ```
  - 外链式

    ```python
    <script type="text/javascript" src="js/index.js"></script>
    ```
- 特性: 弱类型语言, 语句以`;`​结尾

# 语法

## 注释

- 单行: `//`​
- 多行: `/* 多行 */`​

## 变量

- 定义变量:​`var x = 1, y = 'str', z = '12';`​, 变量类型由值决定, 不需要指定变量的类型
- 变量命令规范: 

  - 区分大小写
  - 第一个字符必须为字母、下划线、$, 不能以数字开头
  - 可为字母、下划线、$、数字
- 匈牙利命令风格:

  - 对象以o开头, 如oDiv
  - 字符串以s开头, 如sUserName
  - 整数以i开头, 如iItemCount
  - 布尔值以b开头, 如bIsComplete
  - 浮点数以f开头, 如fPrice
  - 函数以fn开头, 如fnHandler
- 作用域: 局部+全局

## 数据类型

- 5种基本数据类型: 

  - number数字类型, `n++`​自增运算
  - string字符串类型: 

    - 字符串转整型: `parseInt()`​
    - 字符串拼接数字, 自动转化为字符串类型, 用`+`​: 11.1 + abc' = '11.1abc'
  - boolean布尔类型, true或false
  - undefined类型, 若变量声明时未初始化则值为undefined
  - null类型, 空对象, 若在页面上获取不到对象则返回的值为null
- 1种复合类型: `object`​, JS对象, JS中一切皆对象, 允许自定义对象, 对象拥有属性和方法

  - 创建对象:

    - 通过顶级object类来实例化一个对象

      ```javascript
      // 通过object类
      var person = new Object();

      // 添加属性
      person.name = "Tom";
      // 添加方法
      person.sayMyName = function() {
      	alert(this.name);
      }

      // 调用属性和方法
      alert(person.age);
      person.sayName();
      ```
    - 通过对象字面量创建对象

      ```javascript
      // 通过对象字面量
      var person2 = {
      	name: "Rose",
      	age: 18, 
      	sayName: function() {
      		alert('My name is' + this.name);
      	}
      }

      // 调用属性和方法
      alert(person2.age);
      person2.sayName();
      ```
  - json转化为js对象: `var oPerson = JSON.parse(sJson);`​
- 获取变量类型: `typeof()`​

## 数组

类似python列表, 元素可为不同类型, 不支持负数下标, 可有多维数组

- 定义数组:

  ```javascript
  var aList = new Array(1, 2, 3)
  var aList2 = [1, 2, 3, 'asd'] 	// 推荐使用
  ```
- 数组操作:

  ```javascript
  // 获取数组长度
  aList.length
  // 下标取值
  aList[3]

  // 从最后添加和删除数据
  aList.push(5);
  aList.pop();

  // 根据下标添加/删除元素: 
  数组.splice(start, num, elem1, elem2... elemN)
  // start: 必须, 开始删除的索引
  // num: 可选, 删除元素的个数
  // elemN: 可选, 在start位置要插入的新元素
  ```

## 条件语句

​`if`​、`if ... else`​、`if ... else if ... else`​、`switch...case...default...`​

## 循环语句

​`for`​、`while`​、`do-while`​

- for: 

  ```javascript
  for (var index=0; index < array.length; index++) {
  	dosomething;
  }
  ```
- while:

  ```javascript
  while (index < array.length) {
  	dosomething;
  	index++;
  }
  ```
- do-while:

  ```javascript
  do {
  	dosomething;
  	index++;
  } while (index < array.length);
  ```

## 运算符

- ​`==`​: 等于, 值相同
- ​`===`​: 全等, 值和类型都相同
- ​`&&`​: and
- ​`||`​: or
- ​`!`​: not

# 定时器

在指定时间后执行某代码

- 创建:

  ```javascript
  // 以指定的时间间隔(单位: 毫秒)调用一次函数
  var tId = setTimeout(func[, delay, param1, param2,...])

  // 以指定的时间间隔(单位: 毫秒)重复调用一个函数
  var tId = setInterval(func[, delay, param1, param2,...])
  ```

  - func: 定时器要执行的函数名
  - delay: 时间间隔, 单位毫秒, 默认0
  - param1: 定时器执行函数的第一个参数
- 清除定时器:

  ```javascript
  // 清除只执行一次的定时器, 即setTimeout函数, 其返回值为timeoutID
  clearTimeout(timeoutID) 

  // 清除反复执行的定时器, 即setInterval函数, 其返回值为timeoutID
  clearInterval(timeoutID)
  ```

```javascript
// 示例代码
<head>
    <script>
        function fnShow(name) {
            alert(name);
        }
  
        function fnStop() {
            clearInterval(tId);
        }
  
        var tId = setInterval(fnShow, 2000, "itcast");
    </script>
</head>
<body>
	<input type="button" value="停止" onclick="fnStop()">
</body>
```

# 函数

关键字function

```javascript
<script type="text/javascript">
	// 函数定义
	function fnAlert() {
		alert(hello!'')
	}

	// 带返回值的函数
	function fnAdd(num1, num2) {
		var iRs = num1 + num2;
		return iRs;
	}

	// 函数调用
	fnAlert();
	var iCount = fnAdd(3, 4);
</script>
```

- ​`alert()`​: 在浏览器中弹出对话框, 提示用户, 会中断代码的执行, 直到用户关闭弹窗
- ​`console.log()`​: 将信息、变量、对象等输出到浏览器的开发者工具控制台(Console), 常用于调试, 不中断代码的执行, 允许页面继续运行
- 获取标签元素: 内置对象document的`getElementById()`​方法, 获取页面上设置了id属性的标签元素, 返回html对象

  ```javascript
  <script>
  	var oBtn = document.getElementById("btn");
  	alert(oBtn);
  	// 修改标签属性
  	oBtn.value = "username";
  	// 修改样式属性
  	oBtn.style.background = "red";
  	// innerHTML: 读取/设置标签包裹内容
  	oBtn.innerHTML = '修改内容';
  </script>

  <input type="button" value="按钮" id="btn">
  ```
- 匿名函数: 

  ```javascript
  <script>
      window.onload = function {
          dosomething;
      }
  </script>
  ```
- 立即调用函数表达式: IIFE, Immediately Invoked Function Expression, 定义一个函数并立即执行, 用于创建一个独立的作用域, 以避免污染全局作用域, 最后的`()`​立即调用函数

  - 函数表达式包裹法:

    ```js
    (function() {
      console.log("This is an IIFE!");
    })();
    ```
  - 箭头函数包裹法(ES6):

    ```js
    (() => {
      console.log("This is an IIFE using arrow function!");
    })();
    ```

  作用:

  - 创建独立的作用域: 用于模块化编程, 避免与其他代码冲突, 尤其是避免污染全局作用域

    ```js
    var name = "Global";

    (function() {
      var name = "Local";
      console.log(name); // "Local"
    })();

    console.log(name); // "Global"
    ```
  - 避免变量污染全局作用域: IIFE可封装变量, 只在函数内部有效, 防止它们泄露到全局作用域中

    ```js
    (function() {
      var temp = "Temporary variable"; // temp 仅在此作用域内有效
      console.log(temp); // "Temporary variable"
    })();

    console.log(temp); // ReferenceError: temp is not defined
    ```
  - 模块化的实现: 在没有模块化机制之前, IIFE是实现模块化的一种常用方法, 可通过传递参数将外部变量传递给IIFE, 同时将计算结果暴露给外部

    ```js
    var counter = (function() {
      var count = 0; // 私有变量
      return {
        increment: function() {
          count++;
          return count;
        },
        decrement: function() {
          count--;
          return count;
        },
        getCount: function() {
          return count;
        }
      };
    })();

    console.log(counter.increment()); // 1
    console.log(counter.increment()); // 2
    console.log(counter.getCount()); // 2
    ```
- 触发事件: 

  - onload事件: 被动, 在页面加载完毕后触发(函数调用必须在函数定义之后), 在所有资源(图片、样式、外部文件)加载完成后才执行(区别于jQuery的ready函数)

    ```javascript
    <script>
        function fnLoad(){
            pass
        }
      
        // onload事件
        window.onload = fnLoad();
    </script>
    ```
  - onclick事件: 点击触发

    ```javascript
    <input type="button" value="按钮" id="btn" onclick="fnLoad()">
    ```

# 面向对象

- 对象的概念
- 创建对象的字面量语法
- 访问成员运算符
- 创建对象的构造函数语法: `this`​关键字
- 添加和删除属性: `delete`​关键字
- 标准对象

  - ​`Number`​ / `String`​ / `Boolean`​ / `Symbol`​ / `Array`​ / `Function`​
  - ​`Date`​ / `Error`​ / `Math`​ / `RegExp`​ / `Object`​ / `Map`​ / `Set`​
  - ​`JSON`​ / `Promise`​ / `Generator`​ / `Reflect`​ / `Proxy`​

# BOM

- ​`window`​对象的属性和方法
- ​`history`​对象: `forward()`​ / `back()`​ / `go()`​
- ​`location`​对象
- ​`navigator`​对象
- ​`screen`​对象

# DOM

文档对象模型, Document Object Model, 一个平台和语言无关的接口, 将网页的所有元素(网页结构、样式和内容)视为**树形结构**的对象, 允许程序和脚本动态地访问和修改这些对象

DOM元素: 网页中的每一个HTML或XML标签元素, 可通过js操作, 可响应用户的交互(点击、鼠标悬停、输入等)

DOM元素属性: `id`​、`class`​、`src`​(对于`<img>`​)、`href`​(对于`<a>`​)、`value`​(对于`<input>`​)

## 访问DOM元素

- 通过ID获取元素: `getElementById()`​, 返回一个DOM元素对象, `var element = document.getElementById("container");`​
- 通过类名获取元素: `getElementsByClassName()`​, 返回一个指定类名的所有元素集合, `var elements = document.getElementsByClassName("myClass");`​

- 通过标签名获取元素: `getElementsByTagName()`​, 返回所有指定标签名的DOM元素集合, `var elements = document.getElementsByTagName("p");`​

- 通过CSS选择器获取元素:

  - ​`querySelector()`​, 使用CSS选择器返回匹配的第一个元素, `var element = document.querySelector(".myClass");`​
  - ​`querySelectorAll()`​, 返回所有匹配的元素集合, `var elements = document.querySelectorAll("p.myClass");`​
- ​`parentNode`​ / `previousSibling`​ / `nextSibling`​ / `children`​ / `firstChild`​ / `lastChild`​

## **操作DOM元素**

​`nodeValue`​

​`innerHTML`​ / `textContent`​ / `createElement()`​ / `createTextNode()`​ / `appendChild()`​ / `insertBefore()`​ / `removeChild()`​

​`className`​ / `id`​ / `hasAttribute()`​ / `getAttribute()`​ / `setAttribute()`​ / `removeAttribute()`​

- 修改内容: `.innerHTML`​, 读取/设置标签包裹内容

  ```javascript
  var element = document.getElementById("container");
  element.innerHTML = "New content";  // 修改元素的HTML内容
  ```
- 修改样式: 

  ```javascript
  var element = document.getElementById("container");
  element.style.backgroundColor = "blue";  // 修改元素的背景颜色
  ```
- 添加/删除类: 

  ```javascript
  var element = document.getElementById("container");
  element.classList.add("newClass");  // 添加类
  element.classList.remove("oldClass");  // 删除类
  ```
- 添加事件监听器: 

  ```javascript
  var button = document.querySelector("button");
  button.addEventListener("click", function() {
      alert("Button clicked!");
  });
  ```

## 事件处理

- 事件类型

  - UI事件: `load`​ / `unload`​ / `error`​ / `resize`​ / `scroll`​
  - 键盘事件: `keydown`​ / `keyup`​ / `keypress`​
  - 鼠标事件: `click`​ / `dbclick`​ / `mousedown`​ / `mouseup`​ / `mousemove`​ / `mouseover`​ / `mouseout`​
  - 焦点事件: `focus`​ / `blur`​
  - 表单事件: `input`​ / `change`​ / `submit`​ / `reset`​ / `cut`​ / `copy`​ / `paste`​ / `select`​
- 事件绑定

  - HTML事件处理程序(不推荐使用, 因为要做到标签与代码分离)
  - 传统的DOM事件处理程序(只能附加一个回调函数)
  - 事件监听器(旧的浏览器中不被支持)
- 事件流: 事件捕获 / 事件冒泡
- 事件对象(低版本IE中的window.event)

  - ​`target`​(有些浏览器使用srcElement)
  - ​`type`​
  - ​`cancelable`​
  - ​`preventDefault()`​
  - ​`stopPropagation()`​(低版本IE中的cancelBubble)
- 鼠标事件 - 事件发生的位置

  - 屏幕位置: `screenX`​和`screenY`​
  - 页面位置: `pageX`​和`pageY`​
  - 客户端位置: `clientX`​和`clientY`​
- 键盘事件 - 哪个键被按下了

  - ​`keyCode`​属性(有些浏览器使用`which`​)
  - ​`String.fromCharCode(event.keyCode)`​
- HTML5事件

  - ​`DOMContentLoaded`​
  - ​`hashchange`​
  - ​`beforeunload`​

# JavaScript API

- 客户端存储: `localStorage`​和`sessionStorage`​

  ```js
  localStorage.colorSetting = '#a4509b';
  localStorage['colorSetting'] = '#a4509b';
  localStorage.setItem('colorSetting', '#a4509b');
  ```
- 获取位置信息: `geolocation`​

  ```js
  navigator.geolocation.getCurrentPosition(function(pos) { 		  
      console.log(pos.coords.latitude)
      console.log(pos.coords.longitude)
  })
  ```
- 从服务器获取数据: Fetch API
- 绘制图形: `<canvas>`​的API
- 音视频: `<audio>`​和`<video>`​的API
