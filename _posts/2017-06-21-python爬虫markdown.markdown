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


<div class="postBody">
			<div id="cnblogs_post_body" class="blogpost-body"><p><span style="font-size: 18px; color: #0000ff;"><strong>1 爬虫应用场合：</strong></span></p>
<div class="cnblogs_code">
<pre>1 小规模的，数据量小，爬取速度不敏感 -----<span style="color: #0000ff;"><strong>爬去网页</strong></span>   ======  <span style="color: #000000;"><span style="font-size: 14px; color: #0000ff;"><strong>requests 库</strong></span><br>
</span>2 <span style="color: #0000ff;"><strong>网站资源</strong></span> 中规模，数据规模较大，旅游网站           =======<span style="color: #000000;"><span style="color: #0000ff;"><strong><span style="font-size: 14px;">scrapy 库</span></strong></span><br>
</span>3 全 internet 规模<span style="color: #0000ff;"> <strong>搜索引擎</strong> </span>                   =========  <span style="font-size: 13px;"><strong><span style="color: #0000ff;">定制开发</span></strong></span></pre>
</div>
<p><span style="color: #0000ff; font-size: 18px;"><strong>2 爬虫引发的问题：</strong></span></p>
<div class="cnblogs_code">
<pre>1<span style="color: #000000;"> 对<strong>网络服务器性能</strong>的 <strong>骚扰

</strong></span>2<span style="color: #000000;"><strong> 网络内容</strong>的<strong>版权</strong> 法律问题

</span>3 <strong>个人数据 隐私泄露</strong></pre>
</div>
<p><span style="font-size: 18px; color: #0000ff;"><strong>3 网络爬虫的限制&nbsp;<strong><strong>或者</strong></strong><strong>规则 </strong>：</strong>&nbsp;</span></p>
<div class="cnblogs_code">
<pre>1 <span style="font-size: 13px;"><strong>来源审查</strong></span>：判断User-<span style="color: #000000;">Agent 进行限制
                  检查来访的 <strong>HTTP协议头的User</strong></span><strong>-</strong><span style="color: #000000;"><strong>Agent域</strong>，只响应浏览器或者友好爬虫的访问
                  </span>--对审查技术人员要求较高--
2<span style="color: #000000;"><strong><span style="font-size: 13px;"> 发布公告</span></strong>：<strong>Robots协议</strong>
                 告知所有爬虫网站的爬取 策略，<strong>要求爬虫规则</strong></span></pre>
</div>
<p><span style="color: #0000ff; font-size: 18px;"><strong>4 Robots 协议：</strong></span></p>
<div class="cnblogs_code"><div class="cnblogs_code_toolbar"><span class="cnblogs_code_copy"></span></div>
<pre><span style="color: #000000;"><strong>Robots Exclusion Standard  网络爬虫排除标准<br><br></strong>User-agent:*<br>Disallow:/     ----- 基本形式

<span style="font-size: 13px;"><strong>目的</strong></span>：告知<strong>网络爬虫规则</strong>，<strong>那些可以</strong>爬取，<strong>哪些不行</strong>
<span style="font-size: 13px;"><strong>形式</strong></span>：在<strong>网站的根目录</strong>下的 <span style="font-family: 黑体;"><strong>robos.txt</strong></span> 文件中<br><span style="font-size: 13px;"><strong>约束性</strong></span>：<strong>Robots协议是建议性</strong>的而非约束性，<strong>可以不遵守</strong>，<strong>但是存在一定的法律风险<br>        ----<span style="color: #800080;">类人类行为 可以不参考 Robots协议</span><br></strong><br><span style="color: #0000ff;"><strong>如果没有 robots协议 ----- 默认为 允许爬虫爬取数据<br></strong></span><br><br><span style="font-size: 12px;"><strong>例子 京东的robots：<br></strong></span><br><img src="https://images2017.cnblogs.com/blog/1220788/201709/1220788-20170924181354978-2146618399.jpg" alt="" width="178" height="168"></span></pre>
<p>&nbsp;</p>




<p><span style="color: #0000ff; font-size: 18px;"><strong>5 互联网相关知识：</strong></span></p>
<div class="cnblogs_code">
<pre>1<span style="color: #000000;"> <strong>互联网：</strong>网络设备(网线，路由器，交换机，防火墙) 和 一台台计算机组成

</strong></span>2<span style="color: #000000;"><strong> 浏览器与爬虫的区别:</strong> 浏览器把全部的页面解析，渲染，爬虫只提取有用的数据

</span>3 <strong>浏览器 socket客户端软件(应用层)：</strong>(按 http协议封装)远程 建立双向连接(传输层)</pre>
</div>

<br>

<p><span style="color: #0000ff; font-size: 18px;"><strong>6 http 协议：</strong></span></p>
<div class="cnblogs_code">
<pre>1<span style="color: #000000;"> <strong>请求</strong>
	1 请求方式：

	        get    数据放在 url(没有请求体);     数据有最大限制;
	
	        post   数据在请求体里面;  数据没有最大限制;   formdata 表单数据 |  登录


   	 2 url编码 --- 中文编码  urlencode


   	 3 请求浏览器信息：

	        user-agent
	        cookie
	        referer

</strong></span>2<span style="color: #000000;"><strong> 响应：</strong>

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

</pre>
</div>


<br>

<p><span style="color: #0000ff; font-size: 18px;"><strong>7 爬虫的定义及基本流程：</strong></span></p>
<div class="cnblogs_code">
<pre>
	1  <strong>爬虫的定义：    请求网站 (http协议的请求) 并 提取数据 的 自动化程序；
	              
	
	2 基本流程：
	
	
            发送请求：通过 http请求库 向目标站点发起请求 即 发送一个Request
	            请求可以包括额外的headers等信息，等待服务器的响应

	    获取响应内容：
	            如果服务器能够正常响应 会得到一个response ， response的内容可以是html json字符串
	            二进制数据(图片视频等)

	    解析内容：
	            1 得到的内容可能是html(字符串);   可以用正则 网页解析库  beautifulsoup pyquery
	            2 可能是 json(ajax数据);    json模块处理
	            3 可能是 二进制;   b形式写入

	    保存内容：
	            文本 -- 数据库 --
	
	3 request vs response
	
	      request:
	
	            请求方式： get post (head,put,delete,options)
	            请求URL:  统一资源定位符
	            请求头：   包含一些浏览器配置信息
	            请求体:  post 请求中的 form data
	
	      response:
	
	            响应状态： 200 代表成功 301 跳转  404 找不到页面 502服务器错误
	            响应头：  内容类型 长度 等信息
	            响应体： 最主要的 部分  包含请求的资源内容
	
	
	4 可以抓取的数据内容 ：
	
	          网页文本： html json 数据
	          二进制流： 图片 视频
	          其他能请求到的资源
	
	5  解析方式：
	
	    直接处理
	    json
	    正则
	    beautifulsoup
	    xpath
	    pyquery
	
	
	6   用 http库 请求的 网页源代码 ---  没有js加载
	    用浏览器 请求是完整的网页代码 --- 有js加载
	
	7  怎么解决 js渲染问题：
	
	        分析 ajax请求 json数据
	        selenium webdriver
	        splash
	        pyv8 ghost.py
	
	8  保存数据：
	
	    纯文本 html json
	    关系型数据库：结构化表结构形式 Mysql  oracle
	    非关系型数据库：key-vaule 形式 redis mongodb
	    二进制文件：
</pre>
</div>


