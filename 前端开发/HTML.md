# HTML

# 概述

Hyper Text Markup Language, 超文本标记语言, <span data-type="text" style="font-family:">所有内容使用标签组成</span>

<span data-type="text" style="font-family:">超文本</span>: <span data-type="text" style="font-family:">不仅有纯文本, 还有超链接、图片、表格、音视频等</span>

标签不区分大小写, 推荐用小写, 起始标签可附带特性

快捷键: 先写标签名称或标签名称\*n, tab自动补齐

## 标签分类

- 单标签: 标签自带结束标记` /`​(前有空格, 可省), 无标签内容

  ```html
  <img 属性名=属性值 属性名=属性值 />
  ```
- 双标签: 由开始标签和结束标签组成, 允许嵌套和承载内容

  ```html
  <a 属性名=属性值 属性名=属性值\> 标签的内容 </a>
  ```

## 标签关系

可互相嵌套

- 父子关系: 直接包含和被包含
- 兄弟: 被同一个标签包含
- 先辈后辈: 间接包含和被包含

# 标签

- 文档声明: `<! DOCTYPE html>`​, 指定页面使用的HTML的版本

  ```html
  <!DOCTYPE HTML>
  
  <!DOCTYPE HTML PUBLIC "-//W3C//DTD HTML 4.01//EN" "http://www.w3.org/TR/html4/strict.dtd">
  
  <!DOCTYPE HTML PUBLIC "-//W3C//DTD HTML 4.01 Transitional//EN" "http://www.w3.org/TR/html4/loose.dtd">
  ```
- 代码声明: `<html lang=""></html>`​, 中间均为html代码

  - `<head></head>`: 页头, 设置页面title、meta等

    ```html
    <head>  
    	<!-- 指定编码方式 -->
        <meta charset="UTF-8">

        <!-- 当移动设备浏览网页时, 不进行缩放 -->
        <meta name='viewport' content='width=device-width, initial-scale=1.0'>

        <!-- 当使用ie浏览器时, 默认使用最高版本浏览 -->
        <meta http-equiv='x-ua-compatible' content="ie=edge">

        <!-- 网页标题, 显示在浏览器顶端选项卡中而不在浏览器主窗口中 -->
        <title>Document</title>
    </head>
    ```

  - `<body></body>`: 页身, 中间内容显示在浏览器窗口中
- `<!-- 注释 -->`: 不能嵌套, command+`/`​可注释或取消注释
- 文本: 

  - 标题`<hi></hi>`: i为1-6的数字, 不能互相嵌套
  - 段落`<p></p>`: 一个就是一段, 文本放中间, p标签中不能写块元素
  - 加粗`<b></b>`
  - 斜体`<i></i>`​
  - 上标`<sup></sup>`​、下标`<sub></sub>`​
  - 白色空间折叠: 浏览器遇到多空格/换行时视为一个空格显示
  - 换行`<br />`​、水平线(用于分割主题)`<hr />`​
  - 语义化标签: 不影响网页结构, 添加额外信息

    - 强调: 

      - `<strong></strong>`​, 显示时为粗体, 阅读时加重语气
      - `<em></em>`​, 显示为斜体
    - 引用: 

      - `<blockquote cite="URL"></blockquote>`​, 用于标记大段引用, 显示为缩进
      - `<q cite="URL"></q>`​, 用于短引用, 显示为双引号
    - 缩写词: 

      - `<abbr title=""></abbr>`​, title为全称

        ```html
        <p><abbr title="Professor">Prof</abbr> Stephen Hawking is...</p>
        ```
      - `<acronym title=""></acronym>`​, 旧
    - 引文: `<cite></cite>`​, 用于引用的书籍、电影、报告等, 不可用在人名上, 一般显示为斜体

      ```html
      <p>
      	<cite>A Brief History of Time</cite> by Stephen Hawking has sold over million copies worldwide.	 
      </p>
      ```
    - 定义: `<dfn></dfn>`​, 用于行业术语, 有的显示为斜体

      ```html
      <p>A <dfn>black hole</dfn> is a region of space from which ...</p>
      ```
    - 页面设计者联系信息: `<address></address>`​, 可包含电话、邮箱、地址等
    - 内容的修改: 

      - `<ins></ins>`: 下划线
      - `<del></del>`: 删除线
      - `<s></s>`: 不相关不准确但不应删除的内容, 显示为删除线
      - `<u></u>`: 旧, 已淘汰, 下划线
- 列表: 浏览器默认对列表缩进

  - `<li></li>`: 列表项目
  - 有序列表`<ol></ol>`​
  - 无序列表`<ul></ul>`​
  - 定义列表`<dl><dl>`​

    - `<dt></dt>`: 包含被定义的术语
    - `<dd></dd>`: 包含术语的定义, 显示为缩进
- 链接: `<a href="" target=""></a>`​, 默认显示为蓝色带下划线

  - 特性href, 指向其他网站时必须为绝对URL, 指向同网站其他页面可用相对URL
  - 特性target, `"_blank"`​表示在新窗口中打开链接
  - email链接: `<a href="mailto:name@example.org">Email Me</a>`​, 单击会启动email程序并自动添加指定email地址为收件人
  - 锚链接: 

    - 链接到当前页面特定位置: `<a href="#top">Top</a>`​, 如在底部设置单击回到顶部的链接
    - 链接到其他页面特定位置: `<a href="URL/#id"></a>`​, url绝对或相对均可
- 图像: `<img src="" />`​, 单标签, 用像素衡量

  - `src="url"`: 设置图片地址
  - `alt=""`: 加载失败时显示的替代文字
  - `title=""`: 图像附加信息, 光标在图像悬停时显示title
  - `width=""`​、`height=""`: 指定图像大小

    - 图像加载慢, 可先指定图像大小, 使浏览器为图像留出合适空间, 同时继续显示其他元素
  - `align=""`: 旧, 已淘汰, 表示页面其他元素如何环绕图像

    - left或right: 

      ![截屏2025-02-05 14.03.04](../assets/截屏2025-02-05%2014.03.04-20250205140308-zj4rpjp.png)
    - top/middle/bottom:

      ![截屏2025-02-05 14.04.05](../assets/截屏2025-02-05%2014.04.05-20250205140410-hn0r637.png)
  - 图像格式: 

    - jpg或jpeg: 包含多种不同的颜色
    - png: 包含少量颜色/大面积同色
    - gif: 能显示几帧序列图像, 适用于简单插图
    - svg: 可伸缩矢量图形
  - 图像说明: 

    - `<figure></figure>`: 包含图像及其说明且使二者相关联, 说明相同时可添加多个图像
    - `<figcaption><figcaption>`: 包含图像的说明文字

    ```html
    <figure>
    	<img src="images/otters.jpg" alt="Photograph of two sea otters floating in water">
    	<br />
    	<figcaption>
    		Sea otters hold hands when they sleep so they don't drift away from each other.
    	</figcaption>
    </figure>
    ```
- 表格: 

  - `<table></table>`​, 内容逐行编写
  - `<tr></tr>`​, table row, 表示一行
  - `<td><td>`​, table data, 表示一个单元格
  - `<th></th>`​, 表格标题
  - 表格标题: caption
  - 长表格: thead / tbody / tfoot
  - 快捷键: `tr*2>td*3`​+`tab`​, 创建2行3列表格
  - 设置表格属性:

    - width: 设置表格的宽
    - height: 设置表格的高
    - border: 设置单元格的边框大小
    - cellspacing: 单元格间隙大小
  - 合并单元格:

    - 行跨列合并: 一个单元格占n列: colspan\="n"
    - 列跨行合并: 一个单元格占n行: rowspan\="n"
  - 边框:

    - 设置边框和颜色: border: 1px solid #xxxx
    - 设置边框合并: border-collapse: collapse

  ```html
  <table style="border: 1px solid #00b8d4; border-collapse: collapse">
      <tr>
          <th style="border: 1px solid #00b8d4">姓名</th>
          <th style="border: 1px solid #00b8d4">年龄</th>
      </tr>
      <tr>
          <td style="border: 1px solid #00b8d4">张三</td>
          <td style="border: 1px solid #00b8d4">22</td>
      </tr>
  </table>
  ```
- 普通文本标签`<span></span>`: 嵌套在其它文本标签中, 用`style`​给文字单独设计样式: `<span style="font-size:25px;"></span>`​, `""`​包含, `=`​赋值, `;`​分隔

  - 单独设置, 在需要处调用:

    ```html
    <style>
    	span{
    		color: red;
    	}
    </style>
    ```
  - width宽度: 像素值或百分比(占父标签的)
  - height高度: 像素值
  - color文字颜色: 设置rgb, 每个颜色值范围[0-255], 16进制
  - font-size字号大小: 18px(18像素)
  - font-weight字体加粗: normal正常, bolder加粗
  - text-align设置对齐模式: left居左, center居中, right居右​
  - `<em></em>`​斜体​
  - `<strong></strong>`​粗体
- 块标签`<div></div>`: div一般作容器标签, 含其他标签, 结合CSS设计页面

  - 块级元素, 独占一行, 什么都能写
  - 行内元素, 不独占, 不能写块级元素
- 输入标签`<input>`: 单标签​

  - `<input disabled>`: 有输入框但禁止输入
- 文本滑动`<marquee></marquee>`​

  - `loop='1'`: 循环次数
  - `bgcolor='green'`: 设置背景颜色
- 音视频: audio / video

  - 视频格式和播放器
  - 视频托管服务
  - 添加视频的准备工作
  - video标签和属性: autoplay / controls / loop / muted / preload / src
  - audio标签和属性: autoplay / controls / loop / muted / preload / src / width / height / poster
- 窗口: frame

  - frameset / frame: 框架集: 过时, 不建议使用
  - iframe: 内嵌窗口
- 属性: 

  - id: 唯一标识
  - class: 元素所属的类, 用于区分不同的元素
  - title: 元素的额外信息(鼠标悬浮时会显示工具提示文本)
  - tabindex: Tab键切换顺序
  - contenteditable: 元素是否可编辑
  - draggable: 元素是否可拖拽
- 字符实体(实体替换符): `&实体名;`​/`&#数字;`​, 用于表示无法直接书写的特殊字符、符号、字符

  |字符实体|数字实体|描述|对应字符|
  | ----------| ----------| ------------------| ----------|
  |​`&amp;`​|​`&#38;`​|与符号(`&`​)|​`&`​|
  |​`&lt;`​|​`&#60;`​|小于符号(`<`​)|​`<`​|
  |​`&gt;`​|​`&#62;`​|大于符号(`>`​)|​`>`​|
  |​`&quot;`​|​`&#34;`​|双引号(`"`​)|​`"`​|
  |​`&apos;`​|​`&#39;`​<br />|单引号(`'`​)|​`'`​|
  |​`&nbsp;`​|​`&#160;`​|不换行空格(` `​)|空格|
  |​`&copy;`​|​`&#169;`​|版权符号(`©`​)|​`©`​|
  |​`&reg;`​|​`&#174;`​|注册商标符号(`®`​)|​`®`​|
  |​`&euro;`​|​`&#8364;`​|欧元符号(`€`​)|​`€`​|
  |​`&pound;`​|​`&#163;`​<br />|英镑符号(`£`​)|​`£`​|
  |​`&yen;`​|​`&#165;`​|日元符号(`¥`​)|​`¥`​|
- 不常用标签: <cite> <dfn> <del> <sub> <sup> <ins> <code> <samp> <kbd> <abbr> <bdo> <var> <small> <b> <i> <u> <q> <blockquote> <address>

# 表单

用于搜集不同类型的用户输入的数据, 把用户数据提交web服务器

`<form></form>`: 表单标签, 定义整体的**表单区域**, 内容由表单元素标签承载

|表单控件|功能<br />||
| ----------------------------------| -------------------------------------------------------------------------------------| ----------------------------------|
|​`<label>`​|文字标签<br />||
|​`<textarea>`​|多行文本输入框<br />||
|​`<select>`​|下拉列表, `<option>`​定义选项<br />||
|`fieldset`​/`legend`​|组合表单元素||
|`<input>`​<br />|表单元素的用户输入标签, 定义不同类型的用户输入数据方式<br />||
||`type="password"`​: 密码输入框<br />||
||`type="radio"`​: 单选框<br />||
||`type="checkbox"`​: 复选框<br />||
||`type="file"`​: 文件上传框<br />||
||`type="submit"`​: 提交按钮<br />||
||`type="reset"`​: 重置按钮<br />||
||`type="button"`​: 普通按钮<br />||
||`type="text"`​: 单行文本输入框<br />||
||​`type="number"`​: 数字框||

```html
<form>
    <label>姓名: </label>
    <input type="text">
    <br>  	<!-- 记得换行 -->

    <label>密码: </label>
    <input type="password">
    <br>

    <label>性别: </label>
    <input type="radio">男
    <input type="radio">女
    <br>

    <label>爱好: </label>
    <input type="radio">吃饭
    <input type="radio">睡觉
    <input type="radio">打豆豆
    <br>

    <label>照片: </label>
    <input type="file">
    <br>

    <label>个人描述: </label>
    <label>
        <textarea></textarea>
    </label>
    <br>

    <label>地址: </label>
    <select>
        <option>北京</option>
        <option>上海</option>
        <option>广州</option>
        <option>深圳</option>
    </select>
    <br>

    <input type="submit" value="提交">
    <input type="result" value="重置">
    <input type="button" value="按钮">
</form>
```

表单数据提交: 把用户数据提交到web服务器

- action属性: 设置数据提交地址
- method属性: 设置表单提交的方式, 有get、post, 不区分大小写

```html
<form action="https://www.baidu.com" method="GET"> 
```

表单元素属性设置: 

- name属性: 设置表单元素名称, 即提交数据时的参数名
- value属性: 设置表单元素的值, 即提交数据时参数名对应的值

```html
<input type="text" name="username">
```
