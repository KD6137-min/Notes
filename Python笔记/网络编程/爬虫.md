# Python网络爬虫

爬虫/网络数据采集/Spider: 模拟浏览器上网, 批量获取即有数据

# 概述

![截屏2025-01-27 22.45.33](../../assets//截屏2025-01-27%2022.45.33-20250127224536-jtx6ccv.png)

- 分类: 

  - 聚焦爬虫: 完成某一项特定数据的采集(占百分之九十)
  - 通用爬虫: 什么内容都采集, 都存下来(搜索引擎)
  - 增量爬虫: 可聚焦可通用, 当内容发生变化的时候, 它可以增量的获取内容
  - 暗网爬虫: 很少见, 违法, 可聚焦可通用可增量
- 合法性: 

  - 机器人协议 `robots.txt`​

    - 规定了哪些内容可以获取, 哪些内容不能获取
    - `link+robots.txt`​可以看协议, 通常协议中会标明哪些不让爬
    - 国内比较顶级的购物网站都没有写机器人协议
  - 君子协议: 未标注是否可以爬取
  - 注意事项: 

    - 不能威胁网站运行: 爬虫开启后, 使用其他设备去访问爬虫网站做测试
    - 不能攻击服务器
    - 不要获取敏感数据: 包含个人信息的
    - 不要公开收费
    - 不要爬国家公共资源网站
- 反反爬策略: 

  - 伪装浏览器
  - 休眠, 代理ip轮流使用
  - 登录, cookie
- 网页数据类型:

  - 静态数据: 在页面可见, 发送请求可获取, 准备好url和请求头即可
  - 动态数据: 在页面可见, 检查页面, 发送请求不可获取, 获取需要分析:

    - 在检查页面找不到
    - 在elements中一个一个找, 拿到id
    - comment\_url \= ''
    - `response_comment = requests.get(comment_url, headers=headers)`​
- Browser的选择: Chrome打开开发者工具

  - 提供了被代码控制的方式
  - 提供了请求的分析工具: 网络交互、数据存储、源码
  - 提供了检查工具: 右击选择`检查`​-`更多工具`​-`开发者工具`​

    - ![截屏2024-12-26 10.50.23](../../assets//截屏2024-12-26%2010.50.23-20241226105026-lwrr2jf.png): 右键选中时, 显示相应的代码
    - `Elements`​元素: 即当前显示的页面对应的HTML标签
    - `Console`​控制台: 终端, 日志输入的位置, 执行js代码
    - `Source`​源代码: 从服务器中获取的原始数据, 支持断点调试JS代码, 查看静态资源文件

      - 与elements的区别: elements是source经浏览器渲染转换之后的
    - `network`​网络: 可抓包, 可看到request和response
    - `Application`: 网站的一些资源, 主要是存储信息, cookie、缓存……

# requests

- 导包: `import requests`​
- 指定URL: `url = '……'`​

  - `from urllib.parse import quote, unquote`​, 可以把搜索内容编/解码为链接格式
- 发送请求: `response = requests.get(url, headers=head, params=param)`​

  - 构建请求头: `head = {'User-Agent': '……', 'cookie': '……'}`​

    - 必须为字典格式, 键值均为字符串
    - 在'网络', 类型为 document 的文件中找
    - 在请求报文中找 User-Agent和 cookie
  - 构造请求参数: `param = {'query': word}`​

    - url 结尾？后面即为请求参数

  - `.text`: 文本数据
  - `.content`: 二进制数据
- 解析静态数据

  - 正则解析: 最好去相应数据中粘贴, 返回列表
  - css选择器解析

    - `from bs4 import BeautifulSoup`​
    - 获取页面源码: `data = response.text`​, 字符串格式
    - 实例化对象: `BeautifulSoup(data, 解析器)`​

      ```python
      html_data = BeautifulSoup(data, 'html.parser')  # 标签
      ```
    - 进行标签定位

      - `html_data.select(css选择的语法)`: 找所有, 返回列表, 元素为标签
      - `html_data.select_one(css选择的语法)`: 找一个,返回标签

        - 获取标签之间的文本: `标签.text`​
        - 获取标签中属性值: `标签.attrs`: 返回字典

          - 标签.attrs.get(属性名)
          - 标签.attrs['属性名']
      - 可在 element 中中找, 但有时会不一样, 要在 source 中找
    - css 选择器

      - id选择器: 标签具有id属性, id值唯一, 使用: `#`​+`值`​
      - class选择器: `.`​+`值`​, 搭配 command+f 使用

        - 只能有一个class值, 若有有多个class值, 使用属性选择器: div[clsss\='a b'], 多个类使用空格分隔
      - 包含选择器

        - 父子使用`>`: `父选择器 > 子选择器`​
        - 先辈后辈使用空格` `: `父选择器 后辈选择器`​
      - 标签选择器: 直接使用标签名定位, div、p……
      - 兄弟选择器

        - `～`: A～B, 选择 A 标签后的所有 B 标签
        - `+`: A+B, 选择 A 标签后紧邻的那一个兄弟 B
      - 属性选择器

        - id: 使用`#`​
        - class: 使用`.`​
        - `选择器[属性名]`: div[content]找具有content属性的div标
        - `选择器[属性名=值]`: div[content\="haha"]找具有content属性并且对应的值为haha的div标签
        - `选择器[属性名^=指定内容]`: div[content\^\="h"]找具有content属性并且对应的值以h开头为h的div标签
        - `选择器[属性名$=指定内容]`: div[content\$\="h"]找具有content属性并且对应的值以h结尾为h的div标签
        - `选择器[属性名*=指定内容]`: div[content\*\="h"]找具有content属性并且对应的值包含h的div标签
      - 结构选择器: 找某个选择器下的第n和或者倒数第n个

        - 第一个: `父选择器 > 子选择器: first-child`​
        - 第n个: `父选择器 > 子选择器: nth-child(n)`​
        - 倒数第一个: `父选择器 > 子选择器: last-child`​
        - 倒数第n个: `父选择器 > 子选择器: nth-last-child(n)`​

          冒号不能省, 执行顺序: 先找父选择器中的第n个, 验证是否满足子选择器
      - 全局选择器

        - `*`​任意: \*[content]所有具有content属性的标签
  - xpath解析/路径解析法

    解析xml, 兼容html, xml全部由双标签组成, 支持自定义

    - 节点+谓语

      - 路径查询

        - `/`: 从根节点开始查询, 可直接右键复制
        - `//`: 全局查找
        - `.`: 当前节点
        - `..`: 当前节点的父节点
      - 属性: `@属性名`​

        - `路径[@属性名]`: 具有某个属性的
        - `路径[@属性名=值]`: 属性为指定内容
        - `路径[starts-with(@属性名, 指定内容)]`: 属性以指定内容开头
        - `路径[ends-with(@属性名, 指定内容)]`: 属性以指定内容结尾[2.0版本下使用]
        - `路径[contains(@属性名, 指定内容)]`: 属性包含指定内容
        - `//*[@class='……']`: 所有class为……的标签

        - `路径/@属性名`: 获取某个属性对应的值
      - 谓语

        - `路径[n]`: 第n个, 先筛选标签, 再排序定位, 不同于css
        - `路径[last()]`: 最后一个
        - `路径[last()-(n-1)]`: 倒数第n个
        - `路径[position()<=3]`: 获取前3个
    - 函数

      - `contains`​
      - `text()`: 获取标签之间的文本数据

        - `路径/text()`: 该路径文本
        - `路径//text()`: 该路径下所有路径的文本
    - 使用

      - 导包: `from lxml import etree`​
      - 构建xpath对象:

        - `etree.HTML(response.text)`: 将网络获取的数据加载
        - `etree.parse()`: 将文件中内容加载
      - 使用.xpath()提取: `elements = tree.xpath('路径')`​

        - xpath只能提取全部对象, 返回列表, 常用[0]来提取元素
- 解析动态数据: 在网络中手动找 或 json数据类型解析
- 保存数据

  ```python
  with open(' ', 'w', encoding='utf-8') as fp: 
  	fp.write(data)
  
  # 或
  open('xxx', 'wb').write(response.content)
  ```

  - 注意数据格式: 二进制、html、txt、excel、csv、数据库

    - rb和wb的问题

      - 读: 从文件中读取数据, `r`:read, `rb`:read-binary
      - 写: 向文件中写入数据, `w`:wirte, `wb`:write-binary
  - 图片的下载有第三方模块: wget, `wget.download(url, '文件名')`​

# selenium

不需要考虑数据是否是静动态

- 准备工作

  - 导包: `from selenium import webdriver`​
  - 创建对象: `driver = webdriver.Chrome()`​
  - 明确URL: `url = '……'`​
- 基本操作

  - 打开页面: `driver.get(url)`​
  - 回到上一个页面: `driver.back()`​
  - 前进到下一个页面: `driver.forward()`​
  - 关闭页面: `driver.close()`​

  - 窗口最大化: `.maximize_window()`​
  - 刷新页面: `driver.refresh()`​, 当页面有重大变化时必须刷新

    - 注意休眠和刷新时机: 点击、请求、翻页、大变化
  - 获取浏览器打开的所有窗口: `browser.window_handles`​, 返回列表, 新窗口在后
  - 窗口切换

    - `.window_handles[n]`: 列表中第n个窗口
    - 切换到第n个窗口: `browser.switch_to.window(browser2.window_handles[n])`​
    - 回到最初的窗口: `browser.switch_to.default_content()`​
    - 内嵌窗口的切换: 内嵌窗口iframe 标签, 直接定位会找不到

      - 先定位 iframe 所在标签, 再 `switch_to.frame()`​, 记得切换回来
- 操作

  - 获取页面中的内容: `content = driver.page_source`​
  - 向输入框中发送值:

    - `element = driver.find_element('id', '')`​, 找到输入框元素
    - `element.send_keys('')`​
    - `element.clear()`​, 清空输入框
    - 点击搜索键: `element2 = driver.find_element()`​, 找到搜索键的元素
    - 点击: `element2.click()`​
    - 键盘按键

      - `from selenium.webdriver.common.keys import Keys`​
      - `.send_keys(Keys.RETURN)`​
      - 可输入组合键: `.send_keys(Keys.CONTROL, 'a')`​
  - 解析数据

    - `find_element()`: 获取满足条件的第一个, 返回一个标签
    - `find_elements()`: 获取满足条件的所有, 返回列表
    - 参数`by=By……`​, `value=……`​

      - 需导包: `from selenium.webdriver.common.by import By`​
      - ID: 属性名, value直接使用属性值, 不用加#
      - XPATH: value使用xpath表达式
      - TAG\_NAME: 标签名称, value直接使用标签名称, 如a、div、span ……
      - CLASS\_NAME: class属性, value不用加.
      - CSS\_SELECTOR: css选择器, value使用css表达式
    - 获取标签之间文本: `.text`​
    - 获取标签属性: `.get_atribute('属性名')`​
  - 图片识别: `ddddocr模块`​

    - 配合 screenshot: `img = code.screenshot_as_png`​
    - 创建对象: `ocr1 = ddddocr.DdddOcr(show_ad=False)`​
    - 识别 img: `code1 = ocr1.classification(img)`​
  - 动作链: `Actionchains模块`​

    - 导包: `from selenium.webdriver import ActionChains`​
    - 每个后面都加`.perform()`​
    - 创建动作链对象, 绑定浏览器对象: `action = ActionChains(browser)`​
    - 移动鼠标: `action.move_to_element(标签对象).perform()`​
    - 点击: `action.click(对象).perform()`​
    - 滚动: `action.scroll_by_amount(delta_x, delta_y).perform()`​
  - js操作

    ```python
    js_code = '''
    let divs=document.querySelectorAll('.skill-show>div')  # let: 关键字声明变量
    for(let i=0;i<divs.length;i++){ 
    	let div=divs[i] 
    	div.style.display='block' 
    }
    '''
    ```
    - 实现滚动: `document.documentElement.scrollTop=8000`​
    - 执行js代码: `浏览器对象.execute_script(js_code)`​
  - 超级鹰

    - 创建超级鹰对象: `cjy = Chaojiying_Client(超级鹰账号, 超级鹰密码, 软件id)`​

      - 账号: kd6137 密码: zdwiker0517 软件id: 955285
    - 获取图片数据

      - `with open(' ', 'rb'): data = ……read()`​
      - `data = img_tag.screenshot_as_png`​
    - `res = cjy.PostPic(data, 验证码类型)`​

      - 验证码类型:

        - 1902, 常用, 4-6位英文数字
        - 9004, 1-4位坐标
    - 图片验证码:

      ```python
      res = cjy.PostPic(data, 1902) 
      code = res.get('pic_str')
      ```
    - 点字识别

      ```python
      res = cyj.PostPic(img_data, 9004) 
      code = res.get('pic_str')  # '33,172|99,197'  
      all_code = [eval(i) for i in code.split('|')]
      ```
      - 返回的为每个字相对于左上角的坐标, 字符串格式, 每个字用`｜`​隔开, 横纵坐标用逗号隔开

      - 移动鼠标

        ```python
        for x, y in all_code: 
        	action.move_to_element_with_offset( 
        		img,  			# 图片x轴右为正, y轴下为正
        		x - (1 / 2) * img.rect.get('width'),  	# .rect可以获取图像几何信息, 字典格式
        		y - (1 / 2) * img.rect.get('height') 	# 鼠标移动从图片中心开始, 故要减一半边长
        	).click().perform()
        ```
- cookie操作: 

  - 保存cookie: 

    - 导包: `import json`​
    - 第一次打开页面之后, 手动进行登录
    - 提取cookie: `cookies = driver.get_cookies()`​
    - cookie存到变量: `cookies = json.dumps(cookies)`​
    - cookie存到文件:

      ```python
      with open('cookies.json', 'w', encoding='utf-8') as file: 
      	json.dump(cookies, file)
      ```
  - 注入cookie:

    ```python
    for cookie in cookies: 
    	driver.add_cookie(cookie)
    ```
