---
layout: post
title:  "python爬虫相关基础知识"
description: python爬虫相关基础知识
date:   2017-06-21 14:55:52 +0200
categories: jekyll update
---




# python爬虫相关基础知识
-----






<br>


<br>
<br>


## 1 爬虫应用场合： ##

<br>

**1 小规模的，数据量小，爬取速度不敏感**

	爬网页   ======  requests 库

**2 网站资源 中规模，数据规模较大**

	旅游网站 =======  scrapy 库

**3 全 internet 规模** 

    搜索引擎  ======== 定制开发

<br>

## 2 爬虫引发的问题： ##

<br>

**1 对网络服务器性能的 骚扰**

**2 网络内容的版权 法律问题**

**3 个人数据 隐私泄露**


<br>

## 3 网络爬虫的限制（反爬） 或者规则 ：  ##

<br>

**1 来源审查：判断User-Agent 进行限制**

  	检查来访的 HTTP协议头的User-Agent域，
	只响应浏览器或者友好爬虫的访问

  --对审查技术人员要求较高--

**2 发布公告：Robots协议**

     告知所有爬虫网站的爬取 策略，要求爬虫规则


<br>

## 4 Robots 协议： ##

<br>

**Robots Exclusion Standard  网络爬虫排除标准**

	User-agent:*
	Disallow:/     ----- 基本形式



> **目的：**告知网络爬虫规则，那些可以爬取，哪些不行


> **形式：**在网站的根目录下的 robos.txt 文件中


> **约束性：**Robots协议是建议性的而非约束性，可以不遵守，但是存在一定的法律风险
       
**类人类行为 可以不参考 Robots协议**

**如果没有 robots协议 ----- 默认为 允许爬虫爬取数据**

	https://www.jd.com/robots.txt

	例 京东的robots协议

	User-agent: * 
	Disallow: /?* 
	Disallow: /pop/*.html 
	Disallow: /pinpai/*.html?* 
	User-agent: EtaoSpider 
	Disallow: / 
	User-agent: HuihuiSpider 
	Disallow: / 
	User-agent: GwdangSpider 
	Disallow: / 
	User-agent: WochachaSpider 
	Disallow: /


<br>

## 5 互联网相关知识： ##

<br>

**1 互联网：**

网络设备(网线，路由器，交换机，防火墙) 和 一台台计算机组成

**2 浏览器与爬虫的区别:** 

浏览器把全部的页面解析，渲染，爬虫只提取有用的数据

**3 浏览器是 socket客户端软件(应用层)：**

(按 http协议封装)远程 建立双向连接(传输层)


<br>

## 6 http 协议： ##

<br>

**1 请求**
 
	 请求方式：

	        get    数据放在 url(没有请求体);     数据有最大限制;
	
	        post   数据在请求体里面;  数据没有最大限制;   formdata 表单数据 |  登录


   	 url编码 --- 中文编码  urlencode


   	 请求浏览器信息：

	        user-agent
	        cookie
	        referer

<br>


**2 响应：**


	   状态码：
	
	        200
	        300 重定向
	        400 客户端有问题
	        500 服务端有问题
	
	   响应头：
	
	        location -- 响应后, 客户端浏览器 重定向
	
	        set_cookie
	
	   响应体：
	
	        preview 网页源代码



<br>

## 7 爬虫的定义及基本流程： ##

<br>


**1  爬虫的定义：**   

请求网站 (http协议的请求) 并 提取数据 的 自动化程序；

<br>
	              
	
**2 基本流程：**
	


- **发送请求：**

		通过 http请求库 向目标站点发起请求 即 发送一个Request
        请求可以包括额外的headers等信息，等待服务器的响应


- **获取响应内容：**

        如果服务器能够正常响应 会得到一个response ， response的内容可以是html json字符串
        二进制数据(图片视频等)


- **解析内容：**

        1 得到的内容可能是html(字符串);   可以用正则 网页解析库  beautifulsoup pyquery
        2 可能是 json(ajax数据);    json模块处理
        3 可能是 二进制;   b形式写入


- **保存内容：**

        文本 -数据库 --


<br>

	

- **3 request vs response**
	
	      request:
	
	            请求方式： get post (head,put,delete,options)
	            请求URL:  统一资源定位符
	            请求头：   包含一些浏览器配置信息
	            请求体:  post 请求中的 form data
	
	      response:
	
	            响应状态： 200 代表成功 301 跳转  404 找不到页面 502服务器错误
	            响应头：  内容类型 长度 等信息
	            响应体： 最主要的 部分  包含请求的资源内容
	

<br>


- **4 可以抓取的数据内容 ：**
	
          网页文本： html json 数据
          二进制流： 图片 视频
          其他能请求到的资源
	
<br>

- **5  解析方式：**
	
	    直接处理
	    json
	    正则
	    beautifulsoup
	    xpath
	    pyquery
	

<br>

- **6 http库请求 与 真正浏览器请求 的区别：**


		用 http库 请求的 网页源代码 ---  没有js加载
    
		用浏览器 请求是完整的网页代码 --- 有js加载
	

<br>


- **7  怎么解决 js渲染问题：**
	
        1  分析 ajax请求 json数据
        2  selenium webdriver

        splash
        pyv8 ghost.py
	

<br>

- **8  保存数据：**
	
	    -- 纯文本 html json
	    -- 关系型数据库：结构化表结构形式 Mysql  oracle
	    -- 非关系型数据库：key-vaule 形式 redis mongodb
	    -- 二进制文件：