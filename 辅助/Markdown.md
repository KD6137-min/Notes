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

- 格式1: `![](图片路径)`
- 格式2:  使用img标签 ` <img src= '图片路径' >`
- 注意: 如果图片路径使用的是本地路径  切换机器的话图片就不显示了[原因是切换的机器上没有这个相关路径]

# 超链接

- 格式:`[文字](链接)`
- 跳转地址是完整的, 包含https或者http在内
- 按住command键点击链接,  进行跳转
- URL/email: 一对尖括号包含`<url/email>`
- 超链接可以带格式

# LaTeX

- 行间:`$公式$`
- 行内: `$$`回车 / option+command+b

