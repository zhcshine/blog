---
title: Python爬虫Scrapy框架入门基础
categories:
- 后端
date: 2018-01-10 15:44:12
tags:
- Python
- Scrapy
---

> Scrapy是一个为了爬取网站数据，提取结构性数据而编写的应用框架。 可以应用在包括数据挖掘，信息处理或存储历史数据等一系列的程序中。 其最初是为了页面抓取 (更确切来说, 网络抓取 )所设计的， 也可以应用在获取API所返回的数据(例如 Amazon Associates Web Services ) 或者通用的网络爬虫。Scrapy用途广泛，可以用于数据挖掘、监测和自动化测试

## 组件：
1.  引擎(Scrapy): 用来处理整个系统的数据流处理, 触发事务(框架核心)
2.  调度器(Scheduler): 用来接受引擎发过来的请求, 压入队列中, 并在引擎再次请求的时候返回. 可以想像成一个URL（抓取网页的网址或者说是链接）的优先队列, 由它来决定下一个要抓取的网址是什么, 同时去除重复的网址
3.  下载器(Downloader): 用于下载网页内容, 并将网页内容返回给蜘蛛(Scrapy下载器是建立在twisted这个高效的异步模型上的)
4.  爬虫(Spiders): 爬虫是主要干活的, 用于从特定的网页中提取自己需要的信息, 即所谓的实体(Item)。用户也可以从中提取出链接,让Scrapy继续抓取下一个页面
5.  项目管道(Pipeline): 负责处理爬虫从网页中抽取的实体，主要的功能是持久化实体、验证实体的有效性、清除不需要的信息。当页面被爬虫解析后，将被发送到项目管道，并经过几个特定的次序处理数据。
6.  下载器中间件(Downloader Middlewares): 位于Scrapy引擎和下载器之间的框架，主要是处理Scrapy引擎与下载器之间的请求及响应。
7.  爬虫中间件(Spider Middlewares): 介于Scrapy引擎和爬虫之间的框架，主要工作是处理蜘蛛的响应输入和请求输出。
8.  调度中间件(Scheduler Middewares): 介于Scrapy引擎和调度之间的中间件，从Scrapy引擎发送到调度的请求和响应。

## 运行流程：
首先，引擎从调度器中取出一个链接(URL)用于接下来的抓取 引擎把URL封装成一个请求(Request)传给下载器，下载器把资源下载下来，并封装成应答包(Response) 然后，爬虫解析Response 若是解析出实体（Item）,则交给实体管道进行进一步的处理。 若是解析出的是链接（URL）,则把URL交给Scheduler等待抓取

## 备忘

### xpath语法和基本函数
```text
/
.
..
ELEMENT
//ELEMENT
*
text
@ALTER
@*
position()
last()
string()
```

### css选择器
```text
*
E
E1>E2
E1+E2
.CLASS
#ID
[ATTR]
[ATTR=VALUE]
E:nth-child(n)
E:first-child
```

### LinkExtractor提取链接
```text
LinkExtractor参数
allow 允许链接
deny 拒绝链接
allow_domains 允许域名
deny_domains 拒绝域名
restrict_xpaths 允许xpath链接
restrict_css 允许css选择器链接
```

### Exporter导出数据
默认包含以下
```text
JSON, JSON LINES, CSV, XML, PICKLE, MARSHAL
```

### 模拟登录
使用FromRequest ORC识别验证码:pillow, pytesseract 付费网络平台识别 人工识别:爬取登录页面的验证码图片,调用Image.show方法,肉眼识别并输入到内置的input函数中 Cookie登录: 获取浏览器的cookie值(browsercookie包), 利用BrowserCookiesMiddleware将获取的cookie值添加到CookieJar中

### 动态页面抓取
Splash是Scrapy官方推荐的javascript渲染引擎, Webkit无界面浏览器
```shell
# 安装
$ sudo apt-get install docker
$ sudo docker pull scrapinghub/splash
$ sudo docker run -p 8050:8050 -p 8051:8051 scrapinghub/splash
```
splash常见服务端点 http://localhost:8050/render.html GET/POST 返回html 参数: url, timeout, proxy, wait, images, js_source http://localhost:8050/excute 传递模拟的动作给splash,例如等待页面渲染，执行代码，等等

```shell
# 安装
$ pip install scrapy-splash
```
```python
setting.py
# splash服务器地址
SPLASH_URL = 'http://localhost:8050'
# 开启splash的两个下载中间件并调整HttpCompressionMiddleware的次序
DOWNLOADER_MIDDLEWARES = {
    'scrapy_splash.SplashCookiesMiddleware': 723,
        'scrapy_splash.SplashMiddleware': 725,
        'scrapy.downloadermiddlewares.httpcompression.HttpCompressionMiddleware': 810,
}
# 过滤去重函数
DUPEFILTER_CLASS = 'scrapy_splash.SplashAwareDupeFilter',
# 用户支持cahce_args
SPIDER_MIDDLEWARES = {
  'scrapy_splash.SplashDeduplicateArgsMiddleware': 100,
}
```

## 分布式爬取

使用scrapy-redis
```shell
# 安装
$ pip install scrapy
$ pip install scrapy-redis
```
```python
# 在配置文件setting.py中添加scrapy-redis相关信息
# redis服务器
REDIS_URL = 'redis://127.0.0.1:6379'
# 使用scrapy_redis调度器替代原生调度器
SCHEDULER = 'scrapy_redis.scheduler.Scheduler'
# 使用scrapy_redis的RFPDupeFilter作为去重过滤器
DUPEFILTER_CLASS = 'scrapy_redis.dupefilter.RFPDupeFilter'
# 将爬取的数据存储到redis中
ITEM_PIPELINES = {
    'scrapy_redis.pipelines.RedisPipeline': 300,
}
# 爬虫停止后,保留/清除Reids中的请求队列以及去重集合
SCHEDULER_PERSIST = False
```
```python
# 修改爬虫spider.py
from scrapy_redis.spiders import RedisSpider

class BookSpider(RedisSpider):

    # 注释start_urls
    # start_urls = []
```
```shell
# 手动添加start_urls
$ redis-cli -h 127.0.0.1
$ lpush books:start_urls 'http://www.books.com/'
```
