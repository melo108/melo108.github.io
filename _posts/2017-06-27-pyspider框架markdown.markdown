---
layout: post
title:  "pyspider框架"
description: pyspider
date:   2017-06-27 14:55:52 +0200
categories: jekyll update
---

# pyspider 框架的简单介绍与使用

--------------


<br>


## 1 简介

pyspider是一个国人编写的强大的网络爬虫系统并带有强大的WebUI。采用Python语言编写，分布式架构，支持多种数据库后端，强大的WebUI支持脚本编辑器，任务监视器，项目管理器以及结果查看器。

> ----<a href="http://www.pyspider.cn">pyspider中文网 </a>
<br>


**pyspider的设计基础是：以python脚本驱动的抓取环模型爬虫**

<br>

**(1) pyspider的特性**


> <ul style="font-size:13px;color:#444444;font-family:'Helvetica Neue', Helvetica, Arial, sans-serif;text-align:justify;"><li>python脚本控制，可以用任何你喜欢的html解析包（内置 pyquery）</li>
> <li>WEB 界面编写调试脚本，起停脚本，监控执行状态，查看活动历史，获取结果产出</li>
> <li>数据存储支持MySQL, MongoDB, Redis, SQLite, Elasticsearch; PostgreSQL 及 SQLAlchemy</li>
> <li>队列服务支持RabbitMQ, Beanstalk, Redis 和 Kombu</li>
> <li>支持抓取 JavaScript 的页面</li>
> <li>组件可替换，支持单机/分布式部署，支持 Docker 部署</li>
> <li>强大的调度控制，支持超时重爬及优先级设置</li>
> <li>支持python2&amp;3</li>
> </ul>

<br>

**(2) pyspider安装 **


如果使用 ubuntu请先安装以下依赖包：apt-get install python python-dev python-distribute python-pip libcurl4-openssl-dev libxml2-dev libxslt1-dev python-lxml如果需要调用js解析框架，请先照着phantomjs官网说明安装phantomjs应用，并把可执行程序添加到系统环境变量PATH中以方便调用。

> **注意:本程序在WINDOWNS系统中运行问题比较多,作者也没有对win系统做兼容性测试.如果没有特别强的解决问题的能力不建议用WINDOWS类系统.**

	window需要32位的python版本
	
	PySpider 安装 ：
	
	    pip install PySpider
	
	    解决办法: 利用wheel安装对应的pycurl
	
	    1 安装wheel
	    pip install wheel
	
	     https://www.lfd.uci.edu/~gohlke/pythonlibs/
	     
	    2 下载pycurl编译安装包
	    pip install d:\pycurl‑7.43.0‑cp36‑cp36m‑win32.whl
	
	    这样顺利安装好pycurl,从此在windows上面安装各种包行不通的,都可以尝试通过以上方法安装.

	
	遇到的问题
	
	    1 windows 64位的python会 挂掉
	        只支持 32位的windows 需要 32位的python 
 
	    2  HTTP 599: SSL certificate problem:
	        请求 https 开头的网址，SSL 验证错误，证书有误。
	        self.crawl(url, callback=method_name, validate_cert=False)

以上安装完成后，**执行以下命令安装pyspider**

    pip install pyspider


命令**运行pyspider程序**

	pyspider



> **注意：pyspider命令默认会以all模式运行所有的组件，方便调试。作者建议在线上模式分开部署各各组件，详情请查看部署章节**


运行成功后用**浏览器打开http://localhost:5000/访问控制台**




<br>

**(3) pyspider的基本使用**


<br>

- **编写脚本**

**在web控制台点create按钮新建任务，项目名自定义**。

<img src="http://box.kancloud.cn/document_2015-10-08_5615f3a52bec8.png" alt="document/2015-10-08/5615f3a52bec8">

**保存后打开代码编辑器**（代码编辑器默认有简单的实例代码）

<img src="http://box.kancloud.cn/document_2015-10-08_5615f42ae7d11.png" alt="document/2015-10-08/5615f42ae7d11">

**右侧就是代码编辑器，以后可以直接在这添加修改代码。**本实例是自带的代码。代码如下
	
	#!/usr/bin/env python
	# -*- encoding: utf-8 -*-
	# Created on 2015-10-08 12:45:44
	# Project: test
	
	from pyspider.libs.base_handler import *
	
	
	class Handler(BaseHandler):
	    crawl_config = {
	    }
	
	    @every(minutes=24 * 60)
	    def on_start(self):
	        self.crawl('http://scrapy.org/', callback=self.index_page)
	
	    @config(age=10 * 24 * 60 * 60)
	    def index_page(self, response):
	        for each in response.doc('a[href^="http"]').items():
	            self.crawl(each.attr.href, callback=self.detail_page)
	
	    @config(priority=2)
	    def detail_page(self, response):
	        return {
	            "url": response.url,
	            "title": response.doc('title').text(),}
<br>

> **代码流程分析：**

**def on_start(self) 方法是入口代码**。当在web控制台点击run按钮时会执行此方法。

**self.crawl(url, callback=self.index_page)这个方法是调用API生成一个新的爬取任务，这个任务被添加到待抓取队列。**

**def index_page(self, response) 这个方法获取一个Response对象。** response.doc是pyquery对象的一个扩展方法。pyquery是一个类似于jQuery的对象选择器。

**def detail_page(self, response)返回一个结果集对象。**这个结果默认会被添加到resultdb数据库（如果启动时没有指定数据库默认调用sqlite数据库）。你也可以重写on_result(self,result)方法来指定保存位置。

**更多知识**：

**@every(minutes=24*60, seconds=0)** 这个设置是告诉scheduler（调度器）on_start方法每天执行一次。

**@config(age=10 * 24 * 60 * 60)** 这个设置告诉scheduler（调度器）这个request（请求）过期时间是10天，10天内再遇到这个请求直接忽略。这个参数也可以在self.crawl(url, age=10*24*60*60) 和 crawl_config中设置。

**@config(priority=2)** 这个是优先级设置。数字越大越先执行。


<br>

- **执行任务**


完成脚本编写，调试无误后，请先保存脚本,然后返回到控制台首页。

直接点击项目状态status那栏，把状态由TODO改成debug或running。

最后点击项目最右边那个RUN按钮启动项目。

当progress那栏有数据显示说明启动成功。到现在就可以点最右侧的results查看结果了。

<img src="http://box.kancloud.cn/document_2015-10-08_5615f9fd6410a.png" alt="document/2015-10-08/5615f9fd6410a">



> **<a href="http://www.pyspider.cn/book/pyspider/pyspider-Quickstart-1.html">更多api参考：	
> pyspider中文文档</a>**

**<a href="http://www.pyspider.cn/book/pyspider/self.crawl-16.html">self.crawl</a>**

**<a href="http://www.pyspider.cn/book/pyspider/Response-17.html">Response</a>**

**<a href="http://www.pyspider.cn/book/pyspider/self.send-message-18.html">self.send_message</a>**

**<a href="http://www.pyspider.cn/book/pyspider/catch-status-code-error-19.html">@catch_ status_ code_error</a>**

**<a href="http://www.pyspider.cn/book/pyspider/every-20.html">@every(minutes=0, seconds=0)</a>**


<br>

**小实例**

	 ===== python 32位 ======
	#!/usr/bin/env python
	# -*- encoding: utf-8 -*-
	# Created on 2017-11-25 15:13:08
	# --author--: jimmy
	# Project: tripadvisor
	
	from pyspider.libs.base_handler import *
	
	import pymongo
	
	
	class Handler(BaseHandler):
	    crawl_config = {
	    }
	
	    client=pymongo.MongoClient("localhost")
	
	    db=client["trip"]
	    t=db["london"]
	
	    @every(minutes=24 * 60)
	
	    def on_start(self):
	        self.crawl('https://www.tripadvisor.cn/Attractions-g186338-Activities-London_England.html#ATTRACTION_SORT_WRAPPER', callback=self.index_page,validate_cert=False)
	
	    @config(age=10 * 24 * 60 * 60)
	
	    def index_page(self, response):
	        for each in response.doc('.attraction_element .listing_title > a').items():
	            self.crawl(each.attr.href, callback=self.detail_page,validate_cert=False)
	        next=response.doc(".pagination > .taLnk").attr("href")
	        self.crawl(next,callback=self.index_page,validate_cert=False)
	
	    @config(priority=2)
	
	    def detail_page(self, response):
	        name=response.doc('.heading_title').text()
	        rank=response.doc("b > span").text()
	        score=response.doc(".overallRating").text()
	        tele=response.doc(".headerBL .phone > span").text()
	        addr=response.doc(".colCnt3").text()
	        time=response.doc(" div.detail_section.duration").text()
	        return {
	            "url": response.url,
	            "name":name,
	            "排名":rank,
	            "评分":score,
	            "电话":tele,
	            "地址":addr,
	            "游玩时间":time,
	
	        }
	
	    def on_result(self,result):
	        if result:
	            self.save_mongo(result)
	
	    def save_mongo(self,result):
	        if self.t.insert(result):
	            print("%s saved to mongodb" %result["name"])



> **更多请查阅 <a href="https://cuiqingcai.com/2652.html">崔庆才：Python爬虫进阶四之PySpider的用法</a>**







