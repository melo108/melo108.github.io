---
layout: post
title:  "python爬虫性能相关知识"
description: python爬虫如何提高性能
date:   2017-06-24 14:55:52 +0200
categories: jekyll update
---




# python 爬虫性能相关
------


<br>

<br>

<br>

 **计算密集 与 IO密集 的 CPU效率：**

 1 纯计算 --- 串行 --- 效率不低，反而 切换cpu 会花费更多的时间   



> **计算密集型：** 可以利用 多核优势--多进程同时运行

 2 IO 多的 ---并行 -- 效率高 
 
> (cpython的 gil锁 同一时间一个进程只能有一个线程运行)
>  
>  **io 密集型：** 多核 --IO密集 可以利用多线程


<br>

 **爬虫的本质:**

socket客户端与服务端的通信过程，如果我们有多个url待爬取，采用**串行的方式执行**，只能等待爬取一个结束后才能继续下一个，**效率会非常低**。

<br>

 **爬虫的流程:**

> 1 建立网络连接 --- IO阻塞 ---tcp 协议
> 
> 2 组装数据 url  方式  请求体 请求头 ---按 http 协议
> 
> 3 发送 request 发送http协议数据  ---- IO阻塞
> 
> 4 接收 response 数据   ---- IO阻塞


<br>



> **之所以爬虫程序的串行低效，很明显 ,爬虫的过程是一个 IO密集型的过程**




<br>

 **同步、异步、回调函数机制：**


<br>

> **1、同步调用：串行**

即提交一个任务后就在原地等待任务结束，等到拿到任务的结果后再继续下一行代码，效率低下

	import requests
	
	def get_page(url):
	    response=requests.get(url)
	    if response.status_code == 200:
	        return response.text
	
	
	urls=['https://www.baidu.com/','http://www.sina.com.cn/','https://www.python.org']
	for url in urls:
	    res=get_page(url) #调用一个任务，就在原地等待任务结束拿到结果后才继续往后执行
	    print(len(res))

<br>

> **2、多线程或多进程:**

多线程（或多进程）的目的是让每个连接都拥有独立的线程（或进程），这样任何一个连接的阻塞都不会影响其他的连接;


	from multiprocessing import Process
	from threading import Thread
	import requests
	
	def get_page(url):
	    response=requests.get(url)
	    if response.status_code == 200:
	        return response.text
	
	
	if __name__ == '__main__':
	    urls=['https://www.baidu.com/','http://www.sina.com.cn/','https://www.python.org']
	    for url in urls:
	        p=Process(target=get_page,args=(url,))
	        p.start()
	        # t=Thread(target=get_page,args=(url,))
	        # t.start()


> **缺点：**
> 
> 多线程还是多进程都会严重占据系统资源，降低系统对外界响应效率，而且线程与进程本身也更容易进入假死状态


<br>

> **3、线程池或进程池+异步调用：**


“线程池”旨在减少创建和销毁线程的频率，其维持一定合理数量的线程，并让空闲的线程重新承担新的执行任务

提交一个任务后并不会等待任务结束，而是继续下一行代码

	from concurrent.futures import ThreadPoolExecutor,ProcessPoolExecutor
	import requests
	import os
	
	def get_page(url):
	    print('%s GET : %s' %(os.getpid(),url))
	    response=requests.get(url)
	    if response.status_code == 200:
	        return response.text
	
	def parse_page(res):
	    res=res.result()
	    print('%s parsing' %os.getpid())
	
	if __name__ == '__main__':
	    p=ProcessPoolExecutor()
	    # p=ThreadPoolExecutor()
	
	    urls=['https://www.baidu.com/','http://www.sina.com.cn/','https://www.python.org']
	    for url in urls:
	        p.submit(get_page,url).add_done_callback(parse_page)
	    p.shutdown(wait=True)

> **缺点：**
> 
> 多线程模型可以方便高效的解决小规模的服务请求，但面对大规模的服务请求，多线程模型也会遇到瓶颈
> 
> “池”始终有其上限，当请求大大超过上限时，“池”构成的系统对外界的响应并不比没有池的时候效果好多少



<br>

 **高性能--单线程监测控制IO：**


从应用 **程序级别** 检测IO阻塞然后切换到我们自己程序的其他任务执行，这样**把程序的IO降到最低**，我们的程序处于就绪态就会增多，以此来迷惑操作系统，操作系统便以为我们的程序是IO比较少的程序，从而会尽可能多的分配CPU给我们，这样也就达到了提升程序执行效率的目的

<br>

> **1 asyncio模块，检测IO（只能是网络IO），实现应用程序级别的切换（python3.3之后）**

	import asyncio
	
	@asyncio.coroutine
	def task(task_id,senconds):
	    print('%s is start' %task_id)
	    yield from asyncio.sleep(senconds) #只能检测网络IO,检测到IO后切换到其他任务执行
	    print('%s is end' %task_id)
	
	tasks=[task(task_id=1,senconds=3),task(task_id=2,senconds=4)]
	
	loop=asyncio.get_event_loop()
	loop.run_until_complete(asyncio.gather(*tasks))
	loop.close()

<br>

> **2 aiohttp模块，封装http报头，asyncio检测IO实现切换**

	import aiohttp
	import asyncio
	
	@asyncio.coroutine
	def get_page(url):
	    print('GET:%s' %url)
	    response=yield from aiohttp.request('GET',url)
	
	    data=yield from response.read()
	
	    print(url,data)
	    response.close()
	    return 1
	
	tasks=[
	    get_page('https://www.python.org/doc'),
	    get_page('https://www.cnblogs.com/linhaifeng'),
	    get_page('https://www.openstack.org')
	]
	
	loop=asyncio.get_event_loop()
	results=loop.run_until_complete(asyncio.gather(*tasks))
	loop.close()
	
	print('=====>',results) #[1, 1, 1]

<br>

> **3 协程, gevent模块**


	from gevent import monkey;monkey.patch_all()
	import gevent
	import requests
	
	def get_page(url):
	    print('GET:%s' %url)
	    response=requests.get(url)
	    print(url,len(response.text))
	    return 1
	
	# g1=gevent.spawn(get_page,'https://www.python.org/doc')
	# g2=gevent.spawn(get_page,'https://www.cnblogs.com/linhaifeng')
	# g3=gevent.spawn(get_page,'https://www.openstack.org')
	# gevent.joinall([g1,g2,g3,])
	# print(g1.value,g2.value,g3.value) #拿到返回值

<br>


> **4 twisted：(一个网络框架，其中一个功能是发送异步请求，检测IO并自动切换)**
	
	下载安装遇到的问题
	
		'''
		#问题一：error: Microsoft Visual C++ 14.0 is required. Get it with "Microsoft Visual C++ Build Tools": http://landinghub.visualstudio.com/visual-cpp-build-tools
		https://www.lfd.uci.edu/~gohlke/pythonlibs/#twisted
		pip3 install C:\Users\Administrator\Downloads\Twisted-17.9.0-cp36-cp36m-win_amd64.whl
		pip3 install twisted
		
		#问题二：ModuleNotFoundError: No module named 'win32api'
		https://sourceforge.net/projects/pywin32/files/pywin32/
		
		#问题三：openssl
		pip3 install pyopenssl
		'''
	
	twisted基本用法

		from twisted.web.client import getPage,defer
		from twisted.internet import reactor
		
		def all_done(arg):
		    # print(arg)
		    reactor.stop()
		
		def callback(res):
		    print(res)
		    return 1
		
		defer_list=[]
		urls=[
		    'http://www.baidu.com',
		    'http://www.bing.com',
		    'https://www.python.org',
		]
		for url in urls:
		    obj=getPage(url.encode('utf=-8'),)
		    obj.addCallback(callback)
		    defer_list.append(obj)
		
		defer.DeferredList(defer_list).addBoth(all_done)
		
		reactor.run()
	
	
	
	
	twisted的getPage的详细用法

		from twisted.internet import reactor
		from twisted.web.client import getPage
		import urllib.parse
		
		
		def one_done(arg):
		    print(arg)
		    reactor.stop()
		
		post_data = urllib.parse.urlencode({'check_data': 'adf'})
		post_data = bytes(post_data, encoding='utf8')
		headers = {b'Content-Type': b'application/x-www-form-urlencoded'}
		response = getPage(bytes('http://dig.chouti.com/login', encoding='utf8'),
		                   method=bytes('POST', encoding='utf8'),
		                   postdata=post_data,
		                   cookies={},
		                   headers=headers)
		response.addBoth(one_done)
		
		reactor.run()


<br>


> **tornado框架 中的 异步调用使用**


		from tornado.httpclient import AsyncHTTPClient
		from tornado.httpclient import HTTPRequest
		from tornado import ioloop
		
		
		def handle_response(response):
		    """
		    处理返回值内容（需要维护计数器，来停止IO循环），调用 ioloop.IOLoop.current().stop()
		    :param response: 
		    :return: 
		    """
		    if response.error:
		        print("Error:", response.error)
		    else:
		        print(response.body)
		
		
		def func():
		    url_list = [
		        'http://www.baidu.com',
		        'http://www.bing.com',
		    ]
		    for url in url_list:
		        print(url)
		        http_client = AsyncHTTPClient()
		        http_client.fetch(HTTPRequest(url), handle_response)
		
		
		ioloop.IOLoop.current().add_callback(func)
		ioloop.IOLoop.current().start()


