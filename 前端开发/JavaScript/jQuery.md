# jQuery

对JavaScript的封装, 负责网页行为操作, 增加与用户的交互, 免费、开源的js函数库, 简化了js编程

用CSS选择器查找元素, 用jQuery方法操作元素

Write Less Do More, 用更少的代码来完成更多的工作

# 引入

```html
<script src="js/jquery-1.12.4.min.js"></script>

<!-- 从CDN加载jQuery -->
<script src="https://cdn.bootcss.com/jquery/3.3.1/jquery.min.js"></script>

<script>
    window.jQuery || 
        document.write('<script src="js/jquery-3.3.1.min.js"></script>')
</script>
```

## 避免和其他库的冲突

- 先引入其他库再引入jQuery的情况: 

  ```html
  <script src="other.js"></script>
  <script src="jquery.js"></script>
  <script>
  	jQuery.noConflict();
      jQuery(function() {
          jQuery('div').hide();
      });
  </script>
  ```
- 先引入jQuery再引入其他库的情况: 

  ```html
  <script src="jquery.js"></script>
  <script src="other.js"></script>
  <script>
      jQuery(function() {
          jQuery('div').hide();
      });
  </script>
  ```

# 查找元素

## jQuery选择器

- 种类:

  - 标签选择器: `$("li")`​, 选择所有li标签
  - 类选择器: `$(".myClass")`​, 选择class为myClass的标签
  - id选择器: `$("#myId")`​, 选择id为myId的标签
  - 层级选择器: `$("#ul1 li span")`​, 选择id为ul1标签下的所有li标签下的span标签
  - 属性选择器: `$("input[name=first]")`​, 选择name属性等于first的input标签
  - - \* / element / #id / .class / selector1, selector2
    - ancestor descendant / parent\>child / previous+next / previous\~siblings
- 选择集过滤: 在选择标签的集合中过滤自己需要的标签

  - ​`has(选择器名)`​: 选取包含指定选择器的标签
  - ​`eq(索引)`​: 选取指定索引的标签
  - - 基本筛选器: :not(selector) / :first / :last / :even / :odd / :eq(index) / :gt(index) / :lt(index) / :animated / :focus
    - 内容筛选器: :contains('…') / :empty / :parent / :has(selector)
    - 可见性筛选器: :hidden / :visible
    - 子节点筛选器: :nth-child(expr) / :first-child / :last-child / :only-child
    - 属性筛选器: [attribute] / [attribute\='value'] / [attribute!\='value'] / [attribute\^\='value'] / [attribute\$\='value'] / [attribute|\='value'] / [attribute\~\='value']
  - 表单: :input / :text / :password / :radio / :checkbox / :submit / :image / :reset / :button / :file / :selected / :enabled / :disabled / :checked

  ```javascript
  <script src="./js/jQuery/jquery-1.12.4.min.js"></script>

  <script>
      $(function () {
          // has方法的使用
          var $div = $("div").has("#mytext");
          // 设置样式
          $div.css({"background":"red"});

          // eq方法的使用
          var $div = $("div").eq(1);
          // 设置样式
          $dic.css({"background":"yellow"});  
      })
  </script>
  ```
- 选择集转移: 以选择的标签为参照, 获取转移后的标签

  - ​`$('#box').prev();`​: 选择id为box的元素的上一个同级元素
  - ​`$('#box').prevAll();`​: 选择id为box的元素的上面所有同级元素
  - ​`$('#box').next();`​: 选择id为box的元素的下一个同级元素
  - ​`$('#box').nextAll();`​: 选择id为box的元素的下面所有同级元素
  - ​`$('#box').parent();`​: 选择id为box的元素的父元素
  - ​`$('#box').children();`​: 选择id为box的元素的其他同级元素
  - ​`$('#box').find('.myClass');`​: 选择id为box, class为myClass的元素

# 执行操作

- 内容获取: 

  - 获取/修改内容: `html()`​ / `text()`​ / `replaceWith()`​ / `remove()`​
  - 获取/设置元素: `before()`​ / `after()`​ / `prepend()`​ / `append()`​ / `remove()`​ / `clone()`​ / `unwrap()`​ / `detach()`​ / `empty()`​ / `add()`​
  - 获取/修改属性: `attr()`​ / `removeAttr()`​ / `addClass()`​ / `removeClass()`​ / `css()`​
  - 获取/设置表单值: `val()`​

- 查找操作: 

  - 查找方法: `find()`​ / `parent()`​ / `children()`​ / `siblings()`​ / `next()`​ / `nextAll()`​ / `prev()`​ / `prevAll()`​
  - 筛选器: `filter()`​ / `not()`​ / `has()`​ / `is()`​ / `contains()`​
  - 索引编号: `eq()`​
- 尺寸和位置: 

  - 尺寸相关: `height()`​ / `width()`​ / `innerHeight()`​ / `innerWidth()`​ / `outerWidth()`​ / `outerHeight()`​
  - 位置相关: `offset()`​ / `position()`​ / `scrollLeft()`​ / `scrollTop()`​
- 特效和动画: 

  - 基本动画: `show()`​ / `hide()`​ / `toggle()`​
  - 消失出现: `fadeIn()`​ / `fadeOut()`​ / `fadeTo()`​ / `fadeToggle()`​
  - 滑动效果: `slideDown()`​ / `slideUp()`​ / `slideToggle()`​
  - 自定义: `delay()`​ / `stop()`​ / `animate()`​
- 事件: 

  - 文档加载: `ready()`​ / `load()`​
  - 用户交互: `on()`​ / `off()`​
- 链式操作

‍

- ​`html()`​: 获取/设置标签包裹的内容, `var $result = $(div).html();`​
- ​`append()`​: 标签内容追加新的内容
- ​`css()`​: 给标签设置样式属性, `var $px = $myp.css("font-size");`​
- ​`prop()`​: 设置标签的其他属性, `$mybtn.prop({"value":"heima"});`​
- ​`val()`​: 设置value属性, `$mybtn.val("itcast");`​

# 检测页面是否可用

```html
<script>
    $(document).ready(function() {
    
    });
</script>
```

```html
<script>
    $(function() {
    
    });
</script>
```

# jQuery插件

- jQuery Validation
- jQuery Treeview
- jQuery Autocomplete
- jQuery UI

# jQuery事件

- ​`click()`​: 鼠标单击

  ```javascript
  var $mybtn = $("#btn1");
  // 点击时触发function()
  $mythn.click(function()) {
      alert("itcast")
  } 
  ```
- ​`blur()`​: 元素失去焦点

  ```javascript
  $mytext.blur(function(){
  	$(this).css({"background":"white"});
  });
  ```
- ​`focus()`​: 元素获得焦点

  ```javascript
  $mytext.focus(function(){
  	// 原生js写法
  	this.style.background = "red";

  	// jQuery写法
  	$(this).css({"background":"red"});
  });
  ```
- ​`mouseover()`​: 鼠标进入(进入子元素也触发)

  ```javascript
  $mydiv.mouseover(function(){
  	$(this).css({"background":"red"});
  });
  ```
- ​`mouseout()`​: 鼠标离开(离开子元素也触发)

  ```javascript
  $mydiv.mouseout(function(){
  	$(this).css({"background":"white"});
  });
  ```
- ​`ready()`​: 入口函数, DOM加载完成, <u>获取标签元素需要在入口函数完成</u>, 确保文档的DOM完全加载和解析后再执行括号中js代码(等待HTML完全加载解析完毕, 但不等CSS、图像或其他资源加载, 故比onload快), 防止脚本无法访问到DOM元素

  ```javascript
  // 完整写法
  $(document).ready(function() {		// $(document): 选择document对象
  	var $mydiv = $("#mydiv");
  	dosomething;
  });

  // 简化写法
  $(function() {
  	var $mydiv = $("#mydiv");
  	dosomething;
  });
  ```
- 事件代理: `delegate(childSelector, event, function)`​, 利用事件冒泡的原理, 把事件加到父级上, 通过判断事件来源, 执行响应的子元素的操作, 可极大减少事件绑定次数, 提高性能, 让新加入的子元素也可拥有相同的操作

  - ​`childSelector`​子元素的选择器, `event`​事件名, `function`​执行的函数

  ```javascript
  $(function() {
  	$list = $('#list');
  	$list.delegate('li', 'click', function() {		// 父元素ul来代理子元素li的点击事件
  		$(this).css({"background":"red"});          // $(this)指当前点击的子元素
  	});
  })

  <ul id="list">
  	<li>1</li>
  	<li>1</li>
  	<li>1</li>
  </ul>
  ```

# AJAX

Asynchronous JavaScript and XML, 创建动态网页的技术, 与后端分离, 允许在不刷新整个页面的情况下(局部刷新)通过JS在网页与服务器之间实现异步通信, 更新数据, 提高用户体验

工作原理: 核心是使用JS的`XMLHttpRequest`​对象, 可与服务器进行交互, 获取数据并动态更新页面

## **基本流程**

1. 用户在网页中触发事件(例如点击按钮、滚动页面等)
2. JavaScript创建一个`XMLHttpRequest`​对象
3. 使用`XMLHttpRequest`​向服务器发送请求(GET或POST)
4. 服务器处理请求并返回数据
5. JavaScript接收服务器响应, 并使用DOM动态更新页面中的内容

## 特点

- 优点:

  - 异步通信: 页面无需刷新即可更新内容
  - 独立传输数据: 传输的数据格式可以是JSON、XML、HTML或纯文本
  - 用户体验更好: 减少等待时间, 使网页更快、更流畅
- 缺点: 

  - 搜索引擎优化(SEO)问题: 早期AJAX的动态内容不容易被搜索引擎索引
  - 复杂性: 前端逻辑更复杂, 可能增加调试难度
  - 依赖JavaScript: 如果用户禁用JavaScript, 则AJAX功能无法正常运行
  - 跨域限制: 浏览器有同源策略限制, 必须通过CORS或其他方式解决跨域问题

## **核心组件**

- ​`XMLHttpRequest`​对象

  - 属性: 

    - ​`onreadystatechange`​: 监听请求状态变化
    - ​`responseText`​: 服务器返回的文本数据
  - 方法: 

    - ​`open(method, url, async)`​: 初始化请求

      - ​`method`​: 请求方法(GET 或 POST)
      - ​`url`​: 请求地址
      - ​`async`​: 是否异步(true 表示异步)
    - ​`send(data)`​: 发送请求
- JavaScript和DOM: 用于处理服务器返回的数据, 并更新页面
- 数据格式: 服务器和客户端之间交换的数据格式

  - JSON: 最流行的格式, 轻量且易解析
  - XML: 最初的 AJAX 数据格式, 现代开发中较少使用
  - HTML: 直接返回 HTML 片段
  - 纯文本: 返回简单的字符串

## JS实现AJAX

```javascript
// 创建 XMLHttpRequest 对象
var xhr = new XMLHttpRequest();

// 设置请求类型、URL 和是否异步
xhr.open("GET", "https://api.example.com/data", true);

// 监听服务器响应
xhr.onreadystatechange = function () {
    if (xhr.readyState === 4 && xhr.status === 200) {
        // 处理响应数据
        var data = JSON.parse(xhr.responseText);
        console.log(data); // 打印返回的数据
    }
};

// 发送请求
xhr.send();
```

​`readyState ===`​:

- ​`0`​(UNSENT): 请求未初始化
- ​`1`​(OPENED): 服务器连接已建立
- ​`2`​(HEADERS_RECEIVED): 请求已接收
- ​`3`​(LOADING): 请求处理中
- ​`4`​(DONE): 请求已完成, 且响应已就绪

## **jQuery实现AJAX**

加载内容, 提交表单

方法:

- ​`$.ajax()`​: 详细形式
- ​`$.get()`​:

  ```javascript
  $.get("https://api.example.com/data", function (data) {
      console.log(data); // 处理返回的数据
  });
  ```
- ​`$.post()`​: 

  ```javascript
  $.post("https://api.example.com/data", { key: "value" }, function (response) {
      console.log(response); // 处理返回的数据
  });
  ```

```javascript
// 完整形式
$.ajax({
	// 1.url请求地址
	url:'http://t.weather.sojson.com/api/weather/city/15442423',
	// 2. type请求方式, 默认get
	type:'GET',
	// 3. dataType设置返回的数据格式
	dataType:'JSON',
	// 4. data设置发送给服务器的数据, 无参数不需要设置
	// 5. success设置请求成功后的回调函数
	success:function(response) {
		console.log(response);		// 成功处理数据
	},
	// 6. error设置请求失败后的回调函数
	error:function(xhr, status, error) {
		console.error("请求失败: ", error); 	// 错误处理
	},
	// 7. async设置是否异步, 默认为true异步, 一般省略
	async:true
});

// 简写形式
$.get("http://t.weather.sojson.com/api/weather/city/15442423", function(dat, status) {
	console.log(dat);    	// dat设置发送给服务器的数据
	console.log(status);
	alert(dat);
}).error(function(){
	alert("网络异常");
});
```

## **AJAX的现代替代技术**

1. Fetch API(替代XMLHttpRequest)

    ```javascript
    fetch("https://api.example.com/data")
        .then(response => response.json())
        .then(data => console.log(data))
        .catch(error => console.error("Error:", error));
    ```
2. Axios(流行的HTTP请求库)

    ```javascript
    axios.get("https://api.example.com/data")
        .then(response => console.log(response.data))
        .catch(error => console.error(error));
    ```
