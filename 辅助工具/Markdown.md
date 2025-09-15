所有功能符号均使用英语标点

# 标题设置

- command + n(1到6)
- `#+空格`, 有几个#就是几级标题
- hi标签,  i是数字1到6  `<h1>一级标题</h1>`

# 文字格式

- 加粗: command + b或两对*号包含
- 斜体: command+ i或者一对*号包含
- 预编译: 显示符号本体, 一对反引号[tab键上方]  ``
- 引用: `> 空格`, 可嵌套
- 分割线`---`: 建议前后添加空行以提高兼容性
- 高亮: `==文本==`
- 删除线: `～～文本～～`
- 下划线: `<u>文本</u>`
- 上标: `文本^上标^`
- 下标: `文本～下标～`
- 小号字体: `<small>文本</small>`
- 大号字体: `<big>文本</big>`
- 代码块：````编程语言 + 回车`
  - 纯文本的块用````plaintext/text`
- 选项列表：
  - 空白：`- [ ] 选项`
  - 已选：`- [X] 选项`
- 表情符号：`:<表情符号>:`，一对冒号包含；[表情符号网站](https://gist.github.com/rxaviers/7360908)
  -  :bulb:`:bulb:`
  - :white_check_mark:`:white_check_mark:`
  - :exclamation:`:exclamation:`
  - :o:`:o:`
  - :question:`:question:`
  - :warning:`:warning:`
  - :red_circle:`:red_circle:`
  - :pushpin:`:pushpin:`
  - :vs:`:vs:`
  - :paperclip:`:paperclip:`
  - :books:`:books:`
  - :warning:`:warning:`
  - :wrench:`wrench:`
  - :lock:`:lock:`
  - :rocket:`:rocket:`
  - :computer:`:computer:`
  - :triangular_flag_on_post:`:triangular_flag_on_post:`
  - :star:`:star:`
  - :zap:`:zap:`
  - :fire:`:fire:`
  - :sunny:`:sunny:`
  - :label:`:label:`​


# 表格

- command +option+ t或|--|--|有N个|代表列数是N-1列, 可直接在|中间输入col的名字如|运算符|描述|…… |
- 直接对表格右键, 就可以编辑表格
- command + 回车  新增一行
- shift + 回车  单元格内换行/设置一个换行符` <br>`

# 分级序号

- 有顺序的, 格式就是 "1.空格", 回车会自动增加项目符号
- 符号是统一符号, 如圆圈或者方块等, 格式是 -空格 / +空格
- tab键是代表缩进形成子条目

# 插入图片

- 格式1: `![提示文本](图片路径)`
- 格式2:  使用img标签 ` <img src= '图片路径' >`

# 超链接

- 格式:`[文字](链接)`
- 跳转地址是完整的, 包含https或者http在内
- 按住command键点击链接,  进行跳转
- URL/email: 一对尖括号包含`<url/email>`
- 超链接可以带格式
- 页内链接：`[跳转提示文本](#标题)`
    - 锚点/标题无论级别统一用单个`#`
    - 标题中空格变为` - `
    - 英文需转为小写
    - 跨文件链接：`[跳转提示文本](文件名.md#标题)`


# LaTeX

- 行间:`$公式$`
- 行内: `$$`回车 / option+command+b
- ````math`

# Jupyter转换与同步

```bash
# 将md文件转为ipynb文件
jupytext --to notebook name.md

# 将ipynb文件转为md文件
jupytext --to md name.ipynb

# 将ipynb文件与md文件配对，修改时自动同步(双向同步)
jupytext --set-formats ipynb,md name.ipynb	# 自动生成配对md文件
# 或
jupytext --set-formats ipynb,md name.md		# 自动生成配对ipynb文件
```

# Jupyter Book

支持MyST Markdown

```bash
# 安装
pip install jupyter-book

# 创建一个书籍目录结构
jupyter-book create mybook

# 向mybook文件夹中存放.md和.ipynb文件，不能放文件夹

# 构建书籍
jupyter-book build mybook	# 输出一个完整的网站，可导出PDF
```

通过`_toc.yml`进行目录管理，可在其中写出任意层级的树形结构，不推荐超过三层

格式：

```yaml
format: jb-book
root: intro		# 设置书籍的首页
chapters:		# 列出章节，每章节对应一个文件
  - file: chapter1
  - file: chapter2
    sections:	# 子章节
      - file: chapter2/section1
      - file: chapter2/section2

```

