---
layout: post
title:  "scrapy框架"
description: scrapy scrapy_redis
date:   2017-06-26 14:55:52 +0200
categories: jekyll update
---

# 一 scrapy 框架的使用

--------------


<br>

<img src="https://scrapy.org/img/scrapylogo.png">


## 1 基本介绍

<br>


> **Scrapy是一个基于Python,为了爬取网站数据，提取结构性数据而编写的应用框架。 其可以应用在数据挖掘，信息处理或存储历史数据等一系列的程序中。**
> 
> 其最初是为了页面抓取 (更确切来说, 网络抓取 )所设计的， 也可以应用在获取API所返回的数据(例如 Amazon Associates Web Services ) 或者通用的网络爬虫。
> 
> Scrapy用途广泛，可以用于数据挖掘、监测和自动化测试。
> 
> **Scrapy 使用了 Twisted异步网络库来处理网络通讯。整体架构大致如下:**



<img src="https://images2015.cnblogs.com/blog/425762/201605/425762-20160507220247421-1722096301.png" alt="">


<br>


- **scrapy组件的构成:**

Scrapy主要包括了以下组件：



<br>

> **引擎(Scrapy)**

用来处理整个系统的数据流处理, 触发事务(框架核心)


> **调度器(Scheduler)**

用来接受引擎发过来的请求, 压入队列中, 并在引擎再次请求的时候返回. 可以想像成一个URL（抓取网页的网址或者说是链接）的优先队列, 由它来决定下一个要抓取的网址是什么, 同时去除重复的网址

> **下载器(Downloader)**

用于下载网页内容, 并将网页内容返回给蜘蛛(Scrapy下载器是建立在twisted这个高效的异步模型上的)

> **爬虫(Spiders)**

爬虫是主要干活的, 用于从特定的网页中提取自己需要的信息, 即所谓的实体(Item)。用户也可以从中提取出链接,让Scrapy继续抓取下一个页面

> **项目管道(Pipeline)**

负责处理爬虫从网页中抽取的实体，主要的功能是持久化实体、验证实体的有效性、清除不需要的信息。当页面被爬虫解析后，将被发送到项目管道，并经过几个特定的次序处理数据。

> **下载器中间件(Downloader Middlewares)**

位于Scrapy引擎和下载器之间的框架，主要是处理Scrapy引擎与下载器之间的请求及响应。

> **爬虫中间件(Spider Middlewares)**

介于Scrapy引擎和爬虫之间的框架，主要工作是处理蜘蛛的响应输入和请求输出。


> **调度中间件(Scheduler Middewares)**

介于Scrapy引擎和调度之间的中间件，从Scrapy引擎发送到调度的请求和响应。



<br>

- **Scrapy运行流程**

> **引擎从调度器中取出一个链接(URL)用于接下来的抓取**
> 
> **引擎把URL封装成一个请求(Request)传给下载器**
> 
> **下载器把资源下载下来，并封装成应答包(Response)**
> 
> **爬虫解析Response**
> 
> **解析出实体（Item）,则交给实体管道进行进一步的处理**
> 
> **解析出的是链接（URL）,则把URL交给调度器等待抓取**


	1 起始url
	
	    start_reqeusts ---  返回Request 对象 给 调度器
	
	2 去重
	
	    类
	
	        request_seen  True
	
	    默认去重规则：
	
	        DUPEFILTER_CLASS = "scrapy.dupefilter.REPDupeFiter"
	
	    自定义：
	
	        DUPEFILTER_CLASS = "项目名.文件名.类名"
	
	
	
	
	3 调度器  把请求发给下载器
	
	4 下载器  响应 Response
	
	5 回调函数   处理
	
	        返回 Request
	           或者 item 数据
	
	
	6 pipeline
	
	    类：
	
	        process_item
	
	                return item
	
	                raise DropItem
	


<br>

## 2 下载安装配置

<br>

> **安装**

	Linux
	      pip3 install scrapy
	 
	 
	Windows
	      a. pip3 install wheel
	      b. 下载twisted http://www.lfd.uci.edu/~gohlke/pythonlibs/#twisted
	      c. 进入下载目录，执行 pip3 install Twisted‑17.1.0‑cp35‑cp35m‑win_amd64.whl
	      d. pip3 install scrapy
	      e. 下载并安装pywin32：https://sourceforge.net/projects/pywin32/files/

	scrapy 的依赖库
	
	            wheel
	
	            lxml
	
	            pyopenssl
	
	            twisted  异步---基础
	
	            pywin32  python 与 win32 兼容的库
      





<br>

## 3 基本使用

<br>



**基本命令**



> 1. scrapy startproject 项目名称
>    在当前目录中创建中创建一个项目文件（类似于Django）
>  
> 2. scrapy genspider [-t template] <name<domain>
>    创建爬虫应用
>    如：
>       scrapy gensipider -t basic baidu baidu.com
> 
>  
> 3. scrapy list
>    展示爬虫应用列表
>  
> 4. scrapy crawl 爬虫应用名称
>    运行单独爬虫应用

<br>

**其他常见命令**
		
		--nolog  没有 日志信息
		
		-a keyword="aaaaa" 传递 关键字给 spider

		scrapy shell  + 网址 用于测试
		
		scrapy check 检查 代码
		
		scrapy list 返回所有的项目spider名称
		
		scrapy  fetch   返回请求信息
		
		        fetch --nolog  没有 日志信息
		        fetch --nolog --headers
		        fetch --nolog --no-redirect 无重定向
		
		scrapy view xxx.com  网页保存成文件---打开(可以很明显的看出 是不是ajax加载)
		
		scrapy settings 设置命令
		
		scrapy version -v ---依赖库的版本
		
		scrapy bench  --- 性能测试

<br>

**项目结构说明**


	project_name/
	   scrapy.cfg
	   project_name/
	       __init__.py
	       items.py
	       pipelines.py
	       settings.py
	       spiders/
	           __init__.py
	           爬虫1.py
	           爬虫2.py
	           爬虫3.py

	scrapy.cfg  项目的主配置信息。（真正爬虫相关的配置信息在settings.py文件中）
	items.py    设置数据存储模板，用于结构化数据，如：Django的Model
	pipelines    数据处理行为，如：一般结构化的数据持久化
	settings.py 配置文件，如：递归的层数、并发数，延迟下载等
	spiders      爬虫目录，如：创建文件，编写爬虫规则


> **注意：一般创建爬虫文件时，以网站域名命名**



<br>

## 4 选择器

<br>


> **xapth选择器：**    文本/text()  属性 /@href


	response.selector.xpath("//title/text()").extract()
	
	response.xpath("//title[@id="d1"]").extract()
	
	*** 模糊查询  response.xpath('//div[contains(@id,"im")]').extract()
  	response.xpath(//title[@id="d1"]).re_first("").extract()




> **css 选择器：**   文本 ::text  属性 ::attr()

	response.selector.css("title::text").extract()
	response.css("title::attr(href)").extract()
	
	*** 模糊查询  response.css("a[href*=image] img::attr(src)")
	


	***** 正则 查询 response.css("a::text").re("").extract()


两者同时使用：

	    response.xpath("//div[@id='images']").css("a::attr(href)").extract()

		extract_first(defalut="")
	
	    response.xpath("asadasd").extract_first(default='')


<br>

## 5 item格式化 pipeline持久化处理

<br>

利用Scrapy的items将数据格式化，然后统一交由pipelines来处理




> **item 格式化**

	import scrapy
	
	class InfoItem(scrapy.Item):
	    name = scrapy.Field()
	    school = scrapy.Field()
	    url = scrapy.Field()


> **自定义pipeline处理数据**


	
	    class MongoPipeline(object):

	      2 def __init__(self,mongo_url,mongo_db,mongo_table):
	            self.mongo_url=mongo_url
	            self.mongo_db=mongo_db
	            self.mongo_db=mongo_table
	
	        @classmethod
	      1 def from_crawler(cls,crawler):
	            return cls(
	                mongo_url=crawler.settings.get("MONGO_URL"),
	                mongo_db=crawler.settings.get("MONGO_DB"),
	                mongo_table=crawler.settings.get("MONGO_TABLE"),
	            )
	
	     3  def open_spider(self,spider):
				# 爬虫开始执行时，调用
	            self.client=pymongo.MongoClient(self.mongo_url)
	            self.db=self.client[self.mongo_db]
	            self.table = self.db[self.mongo_table]
	
	     4   def process_item(self,item,spider):
	            self.table.insert(dict(item))
	            return item
	
	     5  def close_mongo(self,spider):
				# 爬虫关闭时，被调用
	            self.client.close()



<br>

## 6 中间件

<br>

**爬虫中间件**
	
	class SpiderMiddleware(object):
	
	    def process_spider_input(self,response, spider):
	        """
	        下载完成，执行，然后交给parse处理
	        :param response: 
	        :param spider: 
	        :return: 
	        """
	        pass
	
	    def process_spider_output(self,response, result, spider):
	        """
	        spider处理完成，返回时调用
	        :param response:
	        :param result:
	        :param spider:
	        :return: 必须返回包含 Request 或 Item 对象的可迭代对象(iterable)
	        """
	        return result
	
	    def process_spider_exception(self,response, exception, spider):
	        """
	        异常调用
	        :param response:
	        :param exception:
	        :param spider:
	        :return: None,继续交给后续中间件处理异常；含 Response 或 Item 的可迭代对象(iterable)，交给调度器或pipeline
	        """
	        return None
	
	
	    def process_start_requests(self,start_requests, spider):
	        """
	        爬虫启动时调用
	        :param start_requests:
	        :param spider:
	        :return: 包含 Request 对象的可迭代对象
	        """
	        return start_requests

**下载器中间件**

	class DownMiddleware1(object):
	    def process_request(self, request, spider):
	        """
	        请求需要被下载时，经过所有下载器中间件的process_request调用
	        :param request: 
	        :param spider: 
	        :return:  
	            None,继续后续中间件去下载；
	            Response对象，停止process_request的执行，开始执行process_response
	            Request对象，停止中间件的执行，将Request重新调度器
	            raise IgnoreRequest异常，停止process_request的执行，开始执行process_exception
	        """
	        pass
	
	
	
	    def process_response(self, request, response, spider):
	        """
	        spider处理完成，返回时调用
	        :param response:
	        :param result:
	        :param spider:
	        :return: 
	            Response 对象：转交给其他中间件process_response
	            Request 对象：停止中间件，request会被重新调度下载
	            raise IgnoreRequest 异常：调用Request.errback
	        """
	        print('response1')
	        return response
	
	    def process_exception(self, request, exception, spider):
	        """
	        当下载处理器(download handler)或 process_request() (下载中间件)抛出异常
	        :param response:
	        :param exception:
	        :param spider:
	        :return: 
	            None：继续交给后续中间件处理异常；
	            Response对象：停止后续process_exception方法
	            Request对象：停止中间件，request将会被重新调用下载
	        """
	        return None


<br>

## 7 自定制命令

<br>


**在spiders同级创建任意目录，如：commands，**

**在其中创建 crawlall.py 文件 （此处文件名就是自定义的命令）**

	    from scrapy.commands import ScrapyCommand
	    from scrapy.utils.project import get_project_settings
	
	
	    class Command(ScrapyCommand):
	
	        requires_project = True
	
	        def syntax(self):
	            return '[options]'
	
	        def short_desc(self):
	            return 'Runs all of the spiders'
	
	        def run(self, args, opts):
	            spider_list = self.crawler_process.spiders.list()
	            for name in spider_list:
	                self.crawler_process.crawl(name, **opts.__dict__)
	            self.crawler_process.start()


**在settings.py 中添加配置 COMMANDS_MODULE = '项目名称.目录名称'**

**在项目目录执行命令：scrapy crawlall**



<br>

## 8 扩展与信号

<br>

自定义扩展时，利用信号在指定位置注册制定操作

	from scrapy import signals

	class MyExtension(object):
	    def __init__(self, value):
	        self.value = value
	
	    @classmethod
	    def from_crawler(cls, crawler):
	        val = crawler.settings.getint('MMMM')
	        ext = cls(val)
	
	        crawler.signals.connect(ext.spider_opened, signal=signals.spider_opened)
	        crawler.signals.connect(ext.spider_closed, signal=signals.spider_closed)
	
	        return ext
	
	    def spider_opened(self, spider):
	        print('open')
	
	    def spider_closed(self, spider):
	        print('close')



<br>

## 9 避免重复访问

<br>

	
	scrapy默认使用 scrapy.dupefilter.RFPDupeFilter 进行去重，相关配置有：
	
	DUPEFILTER_CLASS = 'scrapy.dupefilter.RFPDupeFilter'
	DUPEFILTER_DEBUG = False
	
	JOBDIR = "保存范文记录的日志路径，如：/root/"  # 最终路径为 /root/reques

**自定义URL去重操作**

	class RepeatUrl:

	    def __init__(self):
	        self.visited_url = set()
	
	    @classmethod
	    def from_settings(cls, settings):
	        """
	        初始化时，调用
	        :param settings: 
	        :return: 
	        """
	        return cls()
	
	    def request_seen(self, request):
	        """
	        检测当前请求是否已经被访问过
	        :param request: 
	        :return: True表示已经访问过；False表示未访问过
	        """
	        if request.url in self.visited_url:
	            return True
	        self.visited_url.add(request.url)
	        return False
	
	    def open(self):
	        """
	        开始爬去请求时，调用
	        :return: 
	        """
	        print('open replication')
	
	    def close(self, reason):
	        """
	        结束爬虫爬取时，调用
	        :param reason: 
	        :return: 
	        """
	        print('close replication')
	
	    def log(self, request, spider):
	        """
	        记录日志
	        :param request: 
	        :param spider: 
	        :return: 
	        """
	        print('repeat', request.url)



<br>

## 10 配置文件 参数

<br>

	
	# -*- coding: utf-8 -*-
	
	# Scrapy settings for step8_king project
	#
	# For simplicity, this file contains only settings considered important or
	# commonly used. You can find more settings consulting the documentation:
	#
	#     http://doc.scrapy.org/en/latest/topics/settings.html
	#     http://scrapy.readthedocs.org/en/latest/topics/downloader-middleware.html
	#     http://scrapy.readthedocs.org/en/latest/topics/spider-middleware.html
	
	# 1. 爬虫名称
	BOT_NAME = 'step8_king'
	
	# 2. 爬虫应用路径
	SPIDER_MODULES = ['step8_king.spiders']
	NEWSPIDER_MODULE = 'step8_king.spiders'
	
	# Crawl responsibly by identifying yourself (and your website) on the user-agent
	# 3. 客户端 user-agent请求头
	# USER_AGENT = 'step8_king (+http://www.yourdomain.com)'
	
	# Obey robots.txt rules
	# 4. 禁止爬虫配置
	# ROBOTSTXT_OBEY = False
	
	# Configure maximum concurrent requests performed by Scrapy (default: 16)
	# 5. 并发请求数
	# CONCURRENT_REQUESTS = 4
	
	# Configure a delay for requests for the same website (default: 0)
	# See http://scrapy.readthedocs.org/en/latest/topics/settings.html#download-delay
	# See also autothrottle settings and docs
	# 6. 延迟下载秒数
	# DOWNLOAD_DELAY = 2
	
	
	# The download delay setting will honor only one of:
	# 7. 单域名访问并发数，并且延迟下次秒数也应用在每个域名
	# CONCURRENT_REQUESTS_PER_DOMAIN = 2
	# 单IP访问并发数，如果有值则忽略：CONCURRENT_REQUESTS_PER_DOMAIN，并且延迟下次秒数也应用在每个IP
	# CONCURRENT_REQUESTS_PER_IP = 3
	
	# Disable cookies (enabled by default)
	# 8. 是否支持cookie，cookiejar进行操作cookie
	# COOKIES_ENABLED = True
	# COOKIES_DEBUG = True
	
	# Disable Telnet Console (enabled by default)
	# 9. Telnet用于查看当前爬虫的信息，操作爬虫等...
	#    使用telnet ip port ，然后通过命令操作
	# TELNETCONSOLE_ENABLED = True
	# TELNETCONSOLE_HOST = '127.0.0.1'
	# TELNETCONSOLE_PORT = [6023,]
	
	
	# 10. 默认请求头
	# Override the default request headers:
	# DEFAULT_REQUEST_HEADERS = {
	#     'Accept': 'text/html,application/xhtml+xml,application/xml;q=0.9,*/*;q=0.8',
	#     'Accept-Language': 'en',
	# }
	
	
	# Configure item pipelines
	# See http://scrapy.readthedocs.org/en/latest/topics/item-pipeline.html
	# 11. 定义pipeline处理请求
	# ITEM_PIPELINES = {
	#    'step8_king.pipelines.JsonPipeline': 700,
	#    'step8_king.pipelines.FilePipeline': 500,
	# }
	
	
	
	# 12. 自定义扩展，基于信号进行调用
	# Enable or disable extensions
	# See http://scrapy.readthedocs.org/en/latest/topics/extensions.html
	# EXTENSIONS = {
	#     # 'step8_king.extensions.MyExtension': 500,
	# }
	
	
	# 13. 爬虫允许的最大深度，可以通过meta查看当前深度；0表示无深度
	# DEPTH_LIMIT = 3
	
	# 14. 爬取时，0表示深度优先Lifo(默认)；1表示广度优先FiFo
	
	# 后进先出，深度优先
	# DEPTH_PRIORITY = 0
	# SCHEDULER_DISK_QUEUE = 'scrapy.squeue.PickleLifoDiskQueue'
	# SCHEDULER_MEMORY_QUEUE = 'scrapy.squeue.LifoMemoryQueue'
	# 先进先出，广度优先
	
	# DEPTH_PRIORITY = 1
	# SCHEDULER_DISK_QUEUE = 'scrapy.squeue.PickleFifoDiskQueue'
	# SCHEDULER_MEMORY_QUEUE = 'scrapy.squeue.FifoMemoryQueue'
	
	# 15. 调度器队列
	# SCHEDULER = 'scrapy.core.scheduler.Scheduler'
	# from scrapy.core.scheduler import Scheduler
	
	
	# 16. 访问URL去重
	# DUPEFILTER_CLASS = 'step8_king.duplication.RepeatUrl'
	
	
	# Enable and configure the AutoThrottle extension (disabled by default)
	# See http://doc.scrapy.org/en/latest/topics/autothrottle.html
	
	"""
	17. 自动限速算法
	    from scrapy.contrib.throttle import AutoThrottle
	    自动限速设置
	    1. 获取最小延迟 DOWNLOAD_DELAY
	    2. 获取最大延迟 AUTOTHROTTLE_MAX_DELAY
	    3. 设置初始下载延迟 AUTOTHROTTLE_START_DELAY
	    4. 当请求下载完成后，获取其"连接"时间 latency，即：请求连接到接受到响应头之间的时间
	    5. 用于计算的... AUTOTHROTTLE_TARGET_CONCURRENCY
	    target_delay = latency / self.target_concurrency
	    new_delay = (slot.delay + target_delay) / 2.0 # 表示上一次的延迟时间
	    new_delay = max(target_delay, new_delay)
	    new_delay = min(max(self.mindelay, new_delay), self.maxdelay)
	    slot.delay = new_delay
	"""
	
	# 开始自动限速
	# AUTOTHROTTLE_ENABLED = True
	# The initial download delay
	# 初始下载延迟
	# AUTOTHROTTLE_START_DELAY = 5
	# The maximum download delay to be set in case of high latencies
	# 最大下载延迟
	# AUTOTHROTTLE_MAX_DELAY = 10
	# The average number of requests Scrapy should be sending in parallel to each remote server
	# 平均每秒并发数
	# AUTOTHROTTLE_TARGET_CONCURRENCY = 1.0
	
	# Enable showing throttling stats for every response received:
	# 是否显示
	# AUTOTHROTTLE_DEBUG = True
	
	# Enable and configure HTTP caching (disabled by default)
	# See http://scrapy.readthedocs.org/en/latest/topics/downloader-middleware.html#httpcache-middleware-settings
	
	
	"""
	18. 启用缓存
	    目的用于将已经发送的请求或相应缓存下来，以便以后使用
	    
	    from scrapy.downloadermiddlewares.httpcache import HttpCacheMiddleware
	    from scrapy.extensions.httpcache import DummyPolicy
	    from scrapy.extensions.httpcache import FilesystemCacheStorage
	"""
	# 是否启用缓存策略
	# HTTPCACHE_ENABLED = True
	
	# 缓存策略：所有请求均缓存，下次在请求直接访问原来的缓存即可
	# HTTPCACHE_POLICY = "scrapy.extensions.httpcache.DummyPolicy"
	# 缓存策略：根据Http响应头：Cache-Control、Last-Modified 等进行缓存的策略
	# HTTPCACHE_POLICY = "scrapy.extensions.httpcache.RFC2616Policy"
	
	# 缓存超时时间
	# HTTPCACHE_EXPIRATION_SECS = 0
	
	# 缓存保存路径
	# HTTPCACHE_DIR = 'httpcache'
	
	# 缓存忽略的Http状态码
	# HTTPCACHE_IGNORE_HTTP_CODES = []
	
	# 缓存存储的插件
	# HTTPCACHE_STORAGE = 'scrapy.extensions.httpcache.FilesystemCacheStorage'
	
	
	"""
	19. 代理，需要在环境变量中设置
	    from scrapy.contrib.downloadermiddleware.httpproxy import HttpProxyMiddleware
	    
	    方式一：使用默认
	        os.environ
	        {
	            http_proxy:http://root:woshiniba@192.168.11.11:9999/
	            https_proxy:http://192.168.11.11:9999/
	        }
	    方式二：使用自定义下载中间件
	    
	    def to_bytes(text, encoding=None, errors='strict'):
	        if isinstance(text, bytes):
	            return text
	        if not isinstance(text, six.string_types):
	            raise TypeError('to_bytes must receive a unicode, str or bytes '
	                            'object, got %s' % type(text).__name__)
	        if encoding is None:
	            encoding = 'utf-8'
	        return text.encode(encoding, errors)
	        
	    class ProxyMiddleware(object):
	        def process_request(self, request, spider):
	            PROXIES = [
	                {'ip_port': '111.11.228.75:80', 'user_pass': ''},
	                {'ip_port': '120.198.243.22:80', 'user_pass': ''},
	                {'ip_port': '111.8.60.9:8123', 'user_pass': ''},
	                {'ip_port': '101.71.27.120:80', 'user_pass': ''},
	                {'ip_port': '122.96.59.104:80', 'user_pass': ''},
	                {'ip_port': '122.224.249.122:8088', 'user_pass': ''},
	            ]
	            proxy = random.choice(PROXIES)
	            if proxy['user_pass'] is not None:
	                request.meta['proxy'] = to_bytes（"http://%s" % proxy['ip_port']）
	                encoded_user_pass = base64.encodestring(to_bytes(proxy['user_pass']))
	                request.headers['Proxy-Authorization'] = to_bytes('Basic ' + encoded_user_pass)
	                print "**************ProxyMiddleware have pass************" + proxy['ip_port']
	            else:
	                print "**************ProxyMiddleware no pass************" + proxy['ip_port']
	                request.meta['proxy'] = to_bytes("http://%s" % proxy['ip_port'])
	    
	    DOWNLOADER_MIDDLEWARES = {
	       'step8_king.middlewares.ProxyMiddleware': 500,
	    }
	    
	"""
	
	"""
	20. Https访问
	    Https访问时有两种情况：
	    1. 要爬取网站使用的可信任证书(默认支持)
	        DOWNLOADER_HTTPCLIENTFACTORY = "scrapy.core.downloader.webclient.ScrapyHTTPClientFactory"
	        DOWNLOADER_CLIENTCONTEXTFACTORY = "scrapy.core.downloader.contextfactory.ScrapyClientContextFactory"
	        
	    2. 要爬取网站使用的自定义证书
	        DOWNLOADER_HTTPCLIENTFACTORY = "scrapy.core.downloader.webclient.ScrapyHTTPClientFactory"
	        DOWNLOADER_CLIENTCONTEXTFACTORY = "step8_king.https.MySSLFactory"
	        
	        # https.py
	        from scrapy.core.downloader.contextfactory import ScrapyClientContextFactory
	        from twisted.internet.ssl import (optionsForClientTLS, CertificateOptions, PrivateCertificate)
	        
	        class MySSLFactory(ScrapyClientContextFactory):
	            def getCertificateOptions(self):
	                from OpenSSL import crypto
	                v1 = crypto.load_privatekey(crypto.FILETYPE_PEM, open('/Users/wupeiqi/client.key.unsecure', mode='r').read())
	                v2 = crypto.load_certificate(crypto.FILETYPE_PEM, open('/Users/wupeiqi/client.pem', mode='r').read())
	                return CertificateOptions(
	                    privateKey=v1,  # pKey对象
	                    certificate=v2,  # X509对象
	                    verify=False,
	                    method=getattr(self, 'method', getattr(self, '_ssl_method', None))
	                )
	    其他：
	        相关类
	            scrapy.core.downloader.handlers.http.HttpDownloadHandler
	            scrapy.core.downloader.webclient.ScrapyHTTPClientFactory
	            scrapy.core.downloader.contextfactory.ScrapyClientContextFactory
	        相关配置
	            DOWNLOADER_HTTPCLIENTFACTORY
	            DOWNLOADER_CLIENTCONTEXTFACTORY
	
	"""
	
	
	
	"""
	21. 爬虫中间件
	    class SpiderMiddleware(object):
	
	        def process_spider_input(self,response, spider):
	            '''
	            下载完成，执行，然后交给parse处理
	            :param response: 
	            :param spider: 
	            :return: 
	            '''
	            pass
	    
	        def process_spider_output(self,response, result, spider):
	            '''
	            spider处理完成，返回时调用
	            :param response:
	            :param result:
	            :param spider:
	            :return: 必须返回包含 Request 或 Item 对象的可迭代对象(iterable)
	            '''
	            return result
	    
	        def process_spider_exception(self,response, exception, spider):
	            '''
	            异常调用
	            :param response:
	            :param exception:
	            :param spider:
	            :return: None,继续交给后续中间件处理异常；含 Response 或 Item 的可迭代对象(iterable)，交给调度器或pipeline
	            '''
	            return None
	    
	    
	        def process_start_requests(self,start_requests, spider):
	            '''
	            爬虫启动时调用
	            :param start_requests:
	            :param spider:
	            :return: 包含 Request 对象的可迭代对象
	            '''
	            return start_requests
	    
	    内置爬虫中间件：
	        'scrapy.contrib.spidermiddleware.httperror.HttpErrorMiddleware': 50,
	        'scrapy.contrib.spidermiddleware.offsite.OffsiteMiddleware': 500,
	        'scrapy.contrib.spidermiddleware.referer.RefererMiddleware': 700,
	        'scrapy.contrib.spidermiddleware.urllength.UrlLengthMiddleware': 800,
	        'scrapy.contrib.spidermiddleware.depth.DepthMiddleware': 900,
	
	"""
	# from scrapy.contrib.spidermiddleware.referer import RefererMiddleware
	# Enable or disable spider middlewares
	# See http://scrapy.readthedocs.org/en/latest/topics/spider-middleware.html
	SPIDER_MIDDLEWARES = {
	   # 'step8_king.middlewares.SpiderMiddleware': 543,
	}
	
	
	"""
	22. 下载中间件
	    class DownMiddleware1(object):
	        def process_request(self, request, spider):
	            '''
	            请求需要被下载时，经过所有下载器中间件的process_request调用
	            :param request:
	            :param spider:
	            :return:
	                None,继续后续中间件去下载；
	                Response对象，停止process_request的执行，开始执行process_response
	                Request对象，停止中间件的执行，将Request重新调度器
	                raise IgnoreRequest异常，停止process_request的执行，开始执行process_exception
	            '''
	            pass
	    
	    
	    
	        def process_response(self, request, response, spider):
	            '''
	            spider处理完成，返回时调用
	            :param response:
	            :param result:
	            :param spider:
	            :return:
	                Response 对象：转交给其他中间件process_response
	                Request 对象：停止中间件，request会被重新调度下载
	                raise IgnoreRequest 异常：调用Request.errback
	            '''
	            print('response1')
	            return response
	    
	        def process_exception(self, request, exception, spider):
	            '''
	            当下载处理器(download handler)或 process_request() (下载中间件)抛出异常
	            :param response:
	            :param exception:
	            :param spider:
	            :return:
	                None：继续交给后续中间件处理异常；
	                Response对象：停止后续process_exception方法
	                Request对象：停止中间件，request将会被重新调用下载
	            '''
	            return None
	
	    
	    默认下载中间件
	    {
	        'scrapy.contrib.downloadermiddleware.robotstxt.RobotsTxtMiddleware': 100,
	        'scrapy.contrib.downloadermiddleware.httpauth.HttpAuthMiddleware': 300,
	        'scrapy.contrib.downloadermiddleware.downloadtimeout.DownloadTimeoutMiddleware': 350,
	        'scrapy.contrib.downloadermiddleware.useragent.UserAgentMiddleware': 400,
	        'scrapy.contrib.downloadermiddleware.retry.RetryMiddleware': 500,
	        'scrapy.contrib.downloadermiddleware.defaultheaders.DefaultHeadersMiddleware': 550,
	        'scrapy.contrib.downloadermiddleware.redirect.MetaRefreshMiddleware': 580,
	        'scrapy.contrib.downloadermiddleware.httpcompression.HttpCompressionMiddleware': 590,
	        'scrapy.contrib.downloadermiddleware.redirect.RedirectMiddleware': 600,
	        'scrapy.contrib.downloadermiddleware.cookies.CookiesMiddleware': 700,
	        'scrapy.contrib.downloadermiddleware.httpproxy.HttpProxyMiddleware': 750,
	        'scrapy.contrib.downloadermiddleware.chunked.ChunkedTransferMiddleware': 830,
	        'scrapy.contrib.downloadermiddleware.stats.DownloaderStats': 850,
	        'scrapy.contrib.downloadermiddleware.httpcache.HttpCacheMiddleware': 900,
	    }
	
	"""
	# from scrapy.contrib.downloadermiddleware.httpauth import HttpAuthMiddleware
	# Enable or disable downloader middlewares
	# See http://scrapy.readthedocs.org/en/latest/topics/downloader-middleware.html
	# DOWNLOADER_MIDDLEWARES = {
	#    'step8_king.middlewares.DownMiddleware1': 100,
	#    'step8_king.middlewares.DownMiddleware2': 500,
	# }


<br>

# 二 scrapy_reids介绍
------

**scrapy-redis是一个基于redis的scrapy组件，通过它可以快速实现简单分布式爬虫程序，该组件本质上提供了三大功能：**

> **scheduler - 调度器**
> 
> **dupefilter - URL去重规则（被调度器使用）**
> 
> **pipeline   - 数据持久化**


<br>
	
	1 单机架构 在本机维护一个爬取队列 Schedule 进行调度
	
	
	
	2  request 队列 共享 分布式  ---- 多台主机的互相协作
	
	
	    主机 请求队列--维护队列 (redis -- 高效)
	
	    从机 负责数据的处理，存储
	
	
	3  redis 队列
	
	    redis 非关系型数据库(限制少) key value 形式  结构灵活()
	    内存的 数据结构 处理速度快
	    提供队列 集合 多种储存结构
	
	
	4  按成请求队列去重： redis - 集合  识别指纹
	
	    reids 提供 集合数据结构
	
	    在redis 集合 中存储 request的指纹
	
	    再加入队列的时候 首先验证是否已经存在

<br>



**1 URL去重**


> **定义去重规则（被调度器调用并应用）**
 
    a. 内部会使用以下配置进行连接Redis
 
        # REDIS_HOST = 'localhost'                            # 主机名
        # REDIS_PORT = 6379                                   # 端口
        # REDIS_URL = 'redis://user:pass@hostname:9001'       # 连接URL（优先于以上配置）
        # REDIS_PARAMS  = {}                                  # Redis连接参数             默认：REDIS_PARAMS = {'socket_timeout': 30,'socket_connect_timeout': 30,'retry_on_timeout': True,'encoding': REDIS_ENCODING,}）
        # REDIS_PARAMS['redis_cls'] = 'myproject.RedisClient' # 指定连接Redis的Python模块  默认：redis.StrictRedis
        # REDIS_ENCODING = "utf-8"                            # redis编码类型             默认：'utf-8'
     
    b. 去重规则通过redis的集合完成，集合的Key为：
     
        key = defaults.DUPEFILTER_KEY % {'timestamp': int(time.time())}
        默认配置：
            DUPEFILTER_KEY = 'dupefilter:%(timestamp)s'
              
    c. 去重规则中将url转换成唯一标示，然后在redis中检查是否已经在集合中存在
     
        from scrapy.utils import request
        from scrapy.http import Request
         
        req = Request(url='http://www.cnblogs.com/wupeiqi.html')
        result = request.request_fingerprint(req)
        print(result) # 8ea4fd67887449313ccc12e5b6b92510cc53675c
         
         
        PS:
            - URL参数位置不同时，计算结果一致；
            - 默认请求头不在计算范围，include_headers可以设置指定请求头
            示例：
                from scrapy.utils import request
                from scrapy.http import Request
                 
                req = Request(url='http://www.baidu.com?name=8&id=1',callback=lambda x:print(x),cookies={'k1':'vvvvv'})
                result = request.request_fingerprint(req,include_headers=['cookies',])
                 
                print(result)
                 
                req = Request(url='http://www.baidu.com?id=1&name=8',callback=lambda x:print(x),cookies={'k1':666})
                 
                result = request.request_fingerprint(req,include_headers=['cookies',])
                 
                print(result)
         

		# Ensure all spiders share same duplicates filter through redis.
		# DUPEFILTER_CLASS = "scrapy_redis.dupefilter.RFPDupeFilter"




**2 调度器**


> **调度器，调度器使用PriorityQueue（有序集合）、FifoQueue（列表）、LifoQueue（列表）进行保存请求，并且使用RFPDupeFilter对URL去重**
     
	    a. 调度器
	        SCHEDULER_QUEUE_CLASS = 'scrapy_redis.queue.PriorityQueue'          # 默认使用优先级队列（默认），其他：PriorityQueue（有序集合），FifoQueue（列表）、LifoQueue（列表）
	        SCHEDULER_QUEUE_KEY = '%(spider)s:requests'                         # 调度器中请求存放在redis中的key
	        SCHEDULER_SERIALIZER = "scrapy_redis.picklecompat"                  # 对保存到redis中的数据进行序列化，默认使用pickle
	        SCHEDULER_PERSIST = True                                            # 是否在关闭时候保留原来的调度器和去重记录，True=保留，False=清空
	        SCHEDULER_FLUSH_ON_START = True                                     # 是否在开始之前清空 调度器和去重记录，True=清空，False=不清空
	        SCHEDULER_IDLE_BEFORE_CLOSE = 10                                    # 去调度器中获取数据时，如果为空，最多等待时间（最后没数据，未获取到）。
	        SCHEDULER_DUPEFILTER_KEY = '%(spider)s:dupefilter'                  # 去重规则，在redis中保存时对应的key
	        SCHEDULER_DUPEFILTER_CLASS = 'scrapy_redis.dupefilter.RFPDupeFilter'# 去重规则对应处理的类
	 
	 
	"""
	# Enables scheduling storing requests queue in redis.
	SCHEDULER = "scrapy_redis.scheduler.Scheduler"
	 
	# Default requests serializer is pickle, but it can be changed to any module
	# with loads and dumps functions. Note that pickle is not compatible between
	# python versions.
	# Caveat: In python 3.x, the serializer must return strings keys and support
	# bytes as values. Because of this reason the json or msgpack module will not
	# work by default. In python 2.x there is no such issue and you can use
	# 'json' or 'msgpack' as serializers.
	# SCHEDULER_SERIALIZER = "scrapy_redis.picklecompat"
	 
	# Don't cleanup redis queues, allows to pause/resume crawls.
	# SCHEDULER_PERSIST = True
	 
	# Schedule requests using a priority queue. (default)
	# SCHEDULER_QUEUE_CLASS = 'scrapy_redis.queue.PriorityQueue'
	 
	# Alternative queues.
	# SCHEDULER_QUEUE_CLASS = 'scrapy_redis.queue.FifoQueue'
	# SCHEDULER_QUEUE_CLASS = 'scrapy_redis.queue.LifoQueue'
	 
	# Max idle time to prevent the spider from being closed when distributed crawling.
	# This only works if queue class is SpiderQueue or SpiderStack,
	# and may also block the same time when your spider start at the first time (because the queue is empty).
	# SCHEDULER_IDLE_BEFORE_CLOSE = 10　　



**3 数据持久化**



> **定义持久化，爬虫yield Item对象时执行RedisPipeline**
	     
	    a. 将item持久化到redis时，指定key和序列化函数
	     
	        REDIS_ITEMS_KEY = '%(spider)s:items'
	        REDIS_ITEMS_SERIALIZER = 'json.dumps'
	     
	    b. 使用列表保存item数据


**4 起始URL相关**

	起始URL相关
	 
	    a. 获取起始URL时，去集合中获取还是去列表中获取？True，集合；False，列表
	        REDIS_START_URLS_AS_SET = False    # 获取起始URL时，如果为True，则使用self.server.spop；如果为False，则使用self.server.lpop
	    b. 编写爬虫时，起始URL从redis的Key中获取
	        REDIS_START_URLS_KEY = '%(name)s:start_urls'
	         
	"""
	# If True, it uses redis' ``spop`` operation. This could be useful if you
	# want to avoid duplicates in your start urls list. In this cases, urls must
	# be added via ``sadd`` command or you will get a type error from redis.
	# REDIS_START_URLS_AS_SET = False
	 
	# Default start urls key for RedisSpider and RedisCrawlSpider.
	# REDIS_START_URLS_KEY = '%(name)s:start_urls'



**5 配置信息**


	
	# DUPEFILTER_CLASS = "scrapy_redis.dupefilter.RFPDupeFilter"
	#
	#
	# from scrapy_redis.scheduler import Scheduler
	# from scrapy_redis.queue import PriorityQueue
	# SCHEDULER = "scrapy_redis.scheduler.Scheduler"
	# SCHEDULER_QUEUE_CLASS = 'scrapy_redis.queue.PriorityQueue'          # 默认使用优先级队列（默认），其他：PriorityQueue（有序集合），FifoQueue（列表）、LifoQueue（列表）
	# SCHEDULER_QUEUE_KEY = '%(spider)s:requests'                         # 调度器中请求存放在redis中的key
	# SCHEDULER_SERIALIZER = "scrapy_redis.picklecompat"                  # 对保存到redis中的数据进行序列化，默认使用pickle
	# SCHEDULER_PERSIST = True                                            # 是否在关闭时候保留原来的调度器和去重记录，True=保留，False=清空
	# SCHEDULER_FLUSH_ON_START = False                                    # 是否在开始之前清空 调度器和去重记录，True=清空，False=不清空
	# SCHEDULER_IDLE_BEFORE_CLOSE = 10                                    # 去调度器中获取数据时，如果为空，最多等待时间（最后没数据，未获取到）。
	# SCHEDULER_DUPEFILTER_KEY = '%(spider)s:dupefilter'                  # 去重规则，在redis中保存时对应的key
	# SCHEDULER_DUPEFILTER_CLASS = 'scrapy_redis.dupefilter.RFPDupeFilter'# 去重规则对应处理的类
	#
	#
	#
	# REDIS_HOST = '10.211.55.13'                           # 主机名
	# REDIS_PORT = 6379                                     # 端口
	# # REDIS_URL = 'redis://user:pass@hostname:9001'       # 连接URL（优先于以上配置）
	# # REDIS_PARAMS  = {}                                  # Redis连接参数             默认：REDIS_PARAMS = {'socket_timeout': 30,'socket_connect_timeout': 30,'retry_on_timeout': True,'encoding': REDIS_ENCODING,}）
	# # REDIS_PARAMS['redis_cls'] = 'myproject.RedisClient' # 指定连接Redis的Python模块  默认：redis.StrictRedis
	# REDIS_ENCODING = "utf-8"                              # redis编码类型             默认：'utf-8'
