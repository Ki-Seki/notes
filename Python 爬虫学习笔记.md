title:      Python 爬虫学习笔记  
summary:    在慕课网学习 Scrapy 时所作的笔记，后来不断发展，内容更丰富，不仅限于 Scrapy 的知识  
author:     小 K  
datetime:   2021-08-17 16:44  
            2022-02-27 17:11  
tags:       Python  
            Scrapy  
            笔记  
            web crawling  
            selenium  
            CSS selector  
            XPath  

[TOC]

# 1. Python 爬虫学习笔记

## 1.1. 爬虫相关基础知识

### 1.1.1. 区分 crawling，scraping 和 parsing

* Crawling（网页抓取） is essentially following links, both internal and external.
* Scraping（数据下载） is the act of extraction, for instance from crawling.
* Parsing（数据提取） is basically breaking it down into pieces, constituent parts, or segments.

爬虫作为动词，可以用 crawl 来表示；作为名词，可以用 spider 或 crawler 来表示

### 1.1.2. HTTP状态码分类

|分类|描述|
|---|---|
|1xx|信息，服务器收到请求，需要请求者继续执行操作|
|2xx|成功，操作被成功接收并处理|
|3xx|重定向，需要进一步的操作以完成请求|
|4xx|客户端错误，请求包含语法错误或无法完成请求|
|5xx|服务器错误，服务器在处理请求的过程中发生了错误|

### 1.1.3. 当遇到一个爬虫项目时，该怎么规划？

1. 确定抓取对象。根据需求、浏览网页识别要抓取的对象。
2. 选择抓取的技术栈：
    * requests + re + beautiful soup：针对简单静态资源网站
    * 网络请求分析：找出请求 JSON 资源的 URL
    * scrapy：适应于大规模、异步、分布式的任务
    * 动态网页获取技术
       * selenium [+ pyvirtualdisplay]
       * PhantomJS：无界面浏览器
       * scrapy-splash
       * selenium grid
       * splinter
3. 确定保存方法：
    * CSV
    * Excel
    * MySQL、SQLite

## 1.2. Parsing 技术

### 1.2.1. CSS 选择器

|选择器|解释|
|:---|:---|
|`.classname`               |选择类名为 `classname`|
|`.class::attr(attrname)`   |选择类名为 `classname` 的标签中 `attrname` 的属性值|
|`.class::text`             |选择类名为 `classname` 的标签间的内容|
|`#idname`                  |选择 id 为 `idname` 的标签|

### 1.2.2. XPath

#### 1.2.2.1. 常用的

|选择器|解释|
|:---|:---|
|`//book`                   |选择文档中所有 `book` 节点|
|`/book`                    |选择当前节点下的 `book` 节点|
|`*`                        |选择任意节点|
|`//em/text()`              |所有 `em` 标签间的文本|

#### 1.2.2.2. 包含谓语的

|选择器|解释|
|:---|:---|
|`//title[@lang='eng']`             |选择所有 `title` 节点，且拥有 `lang` 属性，且属性值为 `eng`|

#### 1.2.2.3. 包含运算符的

|运算符|选择器|解释|
|:---|:---|:---|
|竖线|`//book/title 竖线 //book/price`             |选择 `book` 元素的所有 `title` 和 `price` 元素（竖线因为无法转义，不能打出来）|

### 1.2.3. RegEx

### 1.2.4. 网络请求分析

如果明确了要爬取的内容完全是动态加载出来的，就可以：

1. 打开网页
2. 进入开发者工具模式
3. 打开 Network（网络）
4. 直接搜索在页面中看到的内容：比如要爬取的是商品的评论，那就复制看到的评论内容并搜索
5. 确定请求动态内容的 API：如 `https://club.jd.com/comment/productPageComments.action?callback=fetchJSON_comment98&productId=100014415029&score=0&sortType=5&page=0&pageSize=10&isShadowSku=0&fold=1`
6. 分析请求的结构：如上面的 URL 中，
    * callback：回调函数
    * productId：商品的 ID
    * sortType：排序方式，经测试当值为 6 时，为按时间从最新到最晚排序
    * page：页码
    * pageSize：每页评论数量

## 1.3. Scrapy

### 1.3.1. Scrapy 架构

![Scrapy 架构](https://www.osgeo.cn/scrapy/_images/scrapy_architecture_02.png "Scrapy 架构")

> 1. "引擎"从"爬虫"获取种子url。
> 2. "引擎"将种子url推入"调度器"，并向“调度器”请求一个url。
> 3. “调度器”返回“引擎”一个url。
> 4. ”引擎“将url送入”下载器“，同时中间经过”下载器中间件“。
> 5. ”下载器“把下载的页面又通过”下载器中间件“，发送回”引擎“。
> 6. ”引擎“将下载的页面送入”爬虫“，同时中间经过”爬虫中间件“。
> 7. ”爬虫“解析下载的页面（这部分就是我们最经常写的解析方法），把结果和新链接发给”引擎“，同时经过”爬虫中间件“。
> 8. ”引擎“将结果发送给pipeline，把新链接发给”调度器“，并向“调度器”请求一个url。
> 9. 重复上述过程，直到”调度器“中没有剩余链接。
> 
> ————————————————  
> 版权声明：本文为CSDN博主「joker1993」的原创文章，遵循CC 4.0 BY-SA版权协议，转载请附上原文出处链接及本声明。  
> 原文链接：https://blog.csdn.net/u011423145/article/details/102549491  

### 1.3.2. Scrapy 爬虫编程一般过程

1. 创建项目。这个项目可用包含爬取不同网站站点的多个爬虫。
2. 访问网站，确定需要爬取的字段。以 `website.com` 为例。
3. 创建特定的爬虫。如 `./spiders/website.py`。有不同的爬虫模板，具体可以参考文档，这里以默认的为例。
4. 修改 `items.py`。按照需要爬取的字段，定义相应的 `Item` 和 `ItemLoader`。
    * `Item` 是用来定义字段的。
    * `ItemLoader` 是用来将提取的字段作一定的数据清洗的。
5. 修改 `website.py`。主要是 `WebsiteSpider` 类里的一些函数，如：
    * `def start_requests(self)`：用于发起网络请求前的一些准备工作，比如模拟登录、处理验证码、提取 cookie 等
    * `def parse(self, response, **kwargs)`：用于解析网络请求。返回新的网络请求或返回 *item*。如果是返回 *item*，则要修改 `ItemLoader`
6. 修改 `pipelines.py`。对 *item* 的处理管道定义在这里。管道就是用来做数据保存的。
7. 修改 `settings.py`。或者修改 `custom_settings`。建议到官方文档查阅（常用如下）
    * `USER_AGENT`
    * `ROBOTSTXT_OBEY`：是否遵守 robots.txt
    * `DOWNLOAD_DELAY`：下载延迟
    * `COOKIES_ENABLED`
    * `COOKIES_DEBUG`
    * `ITEM_PIPELINES`：管道的开关
    * 其他的一些常量
8. 修改 `middlewares.py`。增强客制化程度。
9. 重写 Scrapy 的其他部分，增加客制化程度。如
    * middleware（中间件）：比如将 selenium 集成到某个 middleware 进去等。
    * stats collection（数据收集机制）：收集额外数据
    * signal（信号机制）：使程序更加灵活
    * extension（扩展）：本质上，middleware，pipeline 都是由 signal 实现的 extension。重写 extension 是最为底层的修改之一。

### 1.3.3. Scrapy 与命令行

* `scrapy shell [url|file]`：交互式爬虫界面
* `scrapy startproject <project_name> [project_dir]`：创建目录，保存项目
* `scrapy genspider [options] <name> <domain>`：创建项目后使用此命令，创建指定域名下的爬虫
* `scrapy crawl somespider -s JOBDIR=crawls/somespider-1`：启动可以暂停的爬虫（通过 ctrl+c 暂停）
* `telnet localhost 6023`：用于远程连接一个爬虫，远程查看爬取信息。（其默认链接地址和端口号可在 log 信息中找到）

### 1.3.4. Scrapy 的 selector 和 http

#### 1.3.4.1. scrapy.selector

```python
>>> from scrapy.selector import Selector
>>> body = '<html><body><span>good</span></body></html>'
>>> Selector(text=body).xpath('//span/text()').get()
'good'
```

#### 1.3.4.2. scrapy.http

```python
>>> from scrapy.selector import Selector
>>> from scrapy.http import HtmlResponse
>>> response = HtmlResponse(url='http://example.com', body=body)
>>> Selector(response=response).xpath('//span/text()').get()
'good'
```

## 1.4. Selenium with Python

### 1.4.1. 基本使用

安装：

* `pip install selenium`
* `pip install msedge-selenium-tools`：提供针对微软 Edge 浏览器的更多支持，如添加 option 以禁止加载图片

浏览器驱动程序下载：

* Chrome: https://sites.google.com/chromium.org/driver/
* Edge: https://developer.microsoft.com/en-us/microsoft-edge/tools/webdriver/
* Firefox: https://github.com/mozilla/geckodriver/releases
* Safari: https://webkit.org/blog/6900/webdriver-support-in-safari-10/

示例：

```python
from selenium import webdriver
from selenium.webdriver.common.keys import Keys

driver = webdriver.Firefox()
driver.get("http://www.python.org")
assert "Python" in driver.title
elem = driver.find_element_by_name("q")
elem.clear()
elem.send_keys("pycon")
elem.send_keys(Keys.RETURN)
assert "No results found." not in driver.page_source
driver.close()
```

### 1.4.2. 使用 By 类以增加灵活性

```python
from selenium.webdriver.common.by import 
driver.find_elements(By.CSS_SELECTOR, '.tab-con .comment-item .comment-column.J-comment-column')
```

### 1.4.3. 服务器识别出 selenium？

1. 在使用 selenium 前要用以下 cmd 启动 chrome
    1. `cd "C:\Program Files\Google\Chrome\Application"`
    2. `chrome.exe --remote-debugging-port=92`
2. 不能使用下面的 python 代码的原因是：这个命令是要求返回值的，除非使用多线程
3. `os.system('"C:\\Program Files\\Google\\Chrome\\Application\\chrome.exe" --remote-debugging-port=9222')`

### 1.4.4. selenium 禁止加载图片

在 chromedriver 中的示例如下：

```python
from selenium import webdriver
chrome_opt = webdriver.ChromeOptions()
prefs = {"profile.managed_default_content_settings.images":2}
chrome_opt.add_experimental_option("prefs", prefs)
browser = webdriver.Chrome(executable_path="chrome driver's address", chrome_options=chrome_opt)
browser.get("the url u want to crawl")
```

在 msedgedriver 中的示例如下：

```python
from msedge.selenium_tools import Edge, EdgeOptions
options = EdgeOptions()
options.use_chromium = True
prefs = {"profile.managed_default_content_settings.images": 2}
options.add_experimental_option("prefs", prefs)
driver = Edge(options=options)
driver.get(link)
```

### 1.4.5. 在 Selenium 中进行 User Agent 变换

以下代码适用于 msedge-selenium-tools：

```python
driver.execute_cdp_cmd('Network.setUserAgentOverride', {
         "userAgent": "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/98.0.4758.102 Safari/537.36 Edg/98.0.1108.56",
         "platform": "Windows"})
driver.get(link)
```

### 1.4.6. 将 Selenium 集成到 Scrapy 中

也就是通过重写 downloader middleware，spider 等方式将其集成。

### 1.4.7. 一些常见的问题

#### 1.4.7.1. `ElementNotInteractableException`

可能有些元素还未加载完毕，建议:

* 网页使用 Ajax 技术时，Selenium 不会自己判断是否加载完毕了。可以将 `driver.find_element_by_id('c_title')` 改为 `WebDriverWait(driver, 10).until(EC.presence_of_element_located((By.ID, 'c_title')))`。其中的 10 是等待时间上限，10 秒钟
* 在程序中多添加些 `time.sleep()` 语句

#### 1.4.7.2. `ElementNotFoundException`

可能存在 iframe

####  1.4.7.3. Element is not clickable at point (xxx, xxx)

可能是因为浏览器窗口没有全屏打开。

在实例化 driver 后，加上 `driver.maximize_window()` 即可。

## 1.5. 分布式爬虫

request 队列，url 去重放入某台主机的内存数据库 redis 中。

## 1.6. 突破反爬的方法

### 1.6.1. 休眠一会儿

在发起网络请求之前或之后统一加上类似下面的代码：

```python
time.sleep(random.random() * 2 + 2)  # Sleep for [2, 4] seconds.
```

### 1.6.2. User-Agent 变换

如果要将 User Agent 变换集成到 Scrapy 当中去，可以有以下三种方法（按代码优良度从高到低排序）：

1. UA 表 + 随机 UA　函数
   1. 在 `settings.py` 中定义一个 `ua_list`；
   2. 在需要进行　Request　的地方定义 `random_ua()`，随用随调
2. UA 表 + 重写 `UserAgentMiddleware`
   1. 在 `settings.py` 中定义一个 `ua_list`；同时在 `settings.py` 中修改 `SPIDER_MIDDLEWARES` 字段值
   2. 修改 `middlewares.py`。增加 `class MyUserAgentMiddleware`，重写函数（需查阅文档），如：
      * `__init__`
      * `from_crawler`
      * `process_request`
3. [fake-useragent](https://github.com/hellysmile/fake-useragent) + 重写 `UserAgentMiddleware`
   1. 调用 `fake_useragent` 包
   2. 同上重写 `UserAgentMiddleware`

### 1.6.3. IP 代理池

实现一个 IP 代理池的功能，其实和 User-Agent 的变换是异曲同工的，主要有两种方法。

1. 自己实现一个 IP 自动变换的功能
2. 使用现有的工具，如：
    * [scrapy-proxies](https://github.com/aivarsk/scrapy-proxies): 随机变换 IP 的中间件
    * [scrapy-zyte-smartproxy](https://github.com/scrapy-plugins/scrapy-zyte-smartproxy): 更加智能的随即变换工具，自带 IP 资源，但收费
    * [tor](https://www.torproject.org/): 这本质上是个将请求多次进行转发的浏览器，但可以通过配置作为 IP 变换工具

IP 资源：可以到网上找到免费付费资源。

### 1.6.4. cookie 池

待添加

## 1.7. 其他爬虫相关的 Python 包或工具

### 1.7.1. HTML 解析：w3lib

`w3lib` 是处理 html 文档的包，也是 `Scrapy` 的依赖包之一。eg. `w3lib.html.remove_tags` 可以很方便的去除 html 中的标签。

### 1.7.2. mysql-connector-python

MySQL 官方推出的 Python API。

### 1.7.3. PhantomJS

> PhantomJS是一个基于webkit的JavaScript API。它使用 QtWebKit 作为它核心浏览器的功能，使用webkit来编译解释执行JavaScript代码。任何你可以在基于webkit浏览器做的事情，它都能做到。它不仅是个隐形的浏览器，提供了诸如CSS选择器、支持Web标准、DOM操作、JSON、HTML5、Canvas、SVG等，同时也提供了处理文件I/O的操作，从而使你可以向操作系统读写文件等。PhantomJS的用处可谓非常广泛，诸如网络监测、网页截屏、无需浏览器的 Web 测试、页面访问自动化等。
> 
> 作者：HelloJames  
> 链接：https://www.jianshu.com/p/8210a17bcdb8  
> 来源：简书  
> 著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。  

### 1.7.4. pyvirtualdisplay

python 包，可以虚拟显示图形化的界面。

使用场景：在 Linux 服务器环境下使用 selenium 进行爬虫，而 selenium 需要图形化界面，这时候就可以用 pyvirtualdisplay 来虚拟图形化界面。例如：

```python
from pyvirtualdisplay import Display
display = Display(visible=0, size=(800, 600))
display.start()

"""
这里是正式代码
"""

display.stop()
```

### 1.7.5. redis

REmote DIctionary Server(Redis) 是一个由 Salvatore Sanfilippo 写的 key-value 存储系统，是跨平台的非关系型数据库。

Redis 是一个开源的使用 ANSI C 语言编写、遵守 BSD 协议、支持网络、可基于内存、分布式、可选持久性的键值对(Key-Value)存储数据库，并提供多种语言的 API。

Redis 通常被称为数据结构服务器，因为值（value）可以是字符串(String)、哈希(Hash)、列表(list)、集合(sets)和有序集合(sorted sets)等类型。

### 1.7.6. Python 虚拟环境

* venv：Python 自带的虚拟环境生成器
* virtualenv：最流行的，强大的虚拟环境生成器
* virtualenvwrapper：为了方便 virtualenv 使用，而进行的封装，但只能在 Linux 环境下使用
* virtualenvwrapper-win：virtualenvwrapper 的 Windows 版本。使用方法：
  1. 使用前定义环境变量 `WORKON_HOME`，用来集中保存各个虚拟环境。
  2. 在命令行中键入 `virtualenvwrapper` 即可查看为数不多的常用命令。