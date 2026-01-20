# CSS

层叠样式表, 用来美化页面、控制页面

```html
选择器 {
	属性名1: 属性值1; 
	属性名2: 属性值2; 
	属性名3: 属性值3;
}
```

# 引用方式

- 行内式: 直接在标签的styl属性中加CSS样式, 直观, 但缺乏复用性

  ```html
  <h1 style="color: red">header</h1>
  ```
- 内嵌式: 在<head>中加入style标签, 在<style>中编写CSS代码

  - 在多个页面之间的复用性不高
  - ```html
    <head>  
    	<style type="text/css">
            h3{
                color:red;
            }
        </style>
    </head>
    ```
- 外链式: 将css放入一单独的.css文件中, 在<head>中使用<link>引入到页面中

  - css样式与html分离, 页面便于维护, 复用性高
  - css样式与html分离, 页面便于维护, 复用性高

  ```css
  /* ./css/main.css */
  h3{
  	color:blue
  }
  ```

  ```html
  <link rel="stylesheet" type="text/css" href="./css/main.css">
  ```

# CSS选择器

用来选择标签以为其加样式

- 标签选择器: 以标签开头, 一般用作通用设置

  ```html
  <style type="text/css">
      p{
          color: red;
      }
  </style>
  
  <p>hello</p>
  ```
- 类选择器: 以`.`​开头, 多个类选择器用空格分割, 应用最多

  ```html
  <style type="text/css">
      .blue{color:blue}
      .big{font-size:20px}
      .box{width:100px;height:100px;background:gold}
  </style>
  
  <div class="blue">hello</div>
  <h3 class="blue big box">header</h3>
  <p class="blue box">段落</p>
  ```
- 层级选择器/后代选择器: 父在前, 子在后

  ```html
  <style>
      div p{
          color:red;
      }
      .con{width:300px;height:80px;background:green}
      .con span{color:red}
      .con .pink{color:pink}
      .con .gold{color:gold}
  </style>
  
  <div>
      <p>hello</p>
  </div>
  ```
- id选择器: 以`#`​开头, 元素id唯一, 不能复用

  ```html
  <style>
      #box{color:red}
  </style>
  
  <p id="box">段落</p>
  ```
- 组选择器: 以`,`​分割, 若有公共样式, 可用组选择器

  ```html
  <style>
      .box1, .box2, .box3{width:100px;height:100px}
      .box1{background:red}
      .box2{background:pink}
      .box3{background:gold}
  </style>
  
  <div class="box1">first</div>
  <div class="box2">second</div>
  <div class="box3">third</div>
  ```
- 伪类选择器: 以`:`​分割, 用于添加特殊效果, 用户和网站交互时改变显示效果

  - `:hover`: 即光标悬浮时改变样式

  ```html
  <style>
      .box1{width:100px;height:100px;background:gold;}
      .box1:hover{width:300px;}
  </style>
  
  <div class="box1">first</div>
  ```

# css属性

- 文本: `text`​/`font`​

  - color: 设置文字的颜色, 如: color:red;
  - `font-size`: 文字大小, `font-size:12px;`​
  - `font-family`: 字体, 为避免中文不兼容, 一般写成`font-family: "Microsoft Yahei";`​
  - `font-weight`: `bold`​加粗, `normal`​不加粗
  - `font-style`: 样式
  - `font-stretch`: 拉伸
  - `line-height: 24px;`: 行高
  - `letter-spacing`: 字母间距
  - `word-spacing`: 单词间距
  - `text-decoration`: 下划线, `none`​无下划线
  - `text-align`: 水平对齐方式, `center`​水平居中
  - `text-indent: 24px;`: 首行缩进24px
  - 链接样式: `:link`​/`:visited`​/`:active`​/`:hover`​
  - CSS3新属性:

    - `text-shadow`: 阴影效果
    - 首字母和首行文本: `:first-letter`​/`:first-line`​
    - 响应用户
- 盒子: `box model`​

  - 盒子大小: `width`​ / `height`​
  - 盒子的边框: `border`​
  - 盒子外边距: `margin`​
  - 盒子内边距: `padding`​
  - 盒子的显示和隐藏: `display`​/`visibility`​
  - CSS3新属性: 

    - 边框图像: `border-image`​
    - 投影: `border-shadow`​
    - 圆角: `border-radius`​
- 列表、表格、表单: 

  - 列表的项目符号: `list-style`​
  - 表格的边框和背景: `border-collapse`​
  - 表单控件的外观
  - 表单控件的对齐
  - 浏览器的开发者工具
- 图像: 

  - 控制图像的大小: `display: inline-block`​
  - 对齐图像
  - 背景图像: `background`​ / `background-image`​ / `background-repeat`​ / `background-position`​
- 布局: 

  - 控制元素的位置: `position`​ / `z-index`​

    - 普通流
    - 相对定位
    - 绝对定位
    - 固定定位
    - 浮动元素: `float`​ / `clear`​
  - 网站布局:

    HTML5布局:

    ![截屏2025-01-21 20.22.42](../assets/截屏2025-01-21%2020.22.42-20250121202245-pgdaaoj.png)​
  - 适配屏幕尺寸:

    - 固定宽度布局
    - 流体布局
    - 布局网格
- 布局常用样式属性: 

  - solid: 实线
  - width: 设置元素(标签)的宽度, 如: width:100px;
  - height: 设置元素(标签)的高度, 如: height:200px;
  - background: 设置元素背景色或者背景图片

    - background: gold设置元素的背景色
    - background: url(images/logo.png)设置元素的背景图片
  - border: 设置元素四周的边框

    - border:1px solid black设置元素四周边框是1像素宽的黑色实线
  - 可分别设置四个边: 

    - border-top 设置顶边边框, 如: border-top:10px solid red;
    - border-left 设置左边边框, 如: border-left:10px solid blue;
    - border-right 设置右边边框, 如: border-right:10px solid green;
    - border-bottom 设置底边边框, 如: border-bottom:10px solid pink;
