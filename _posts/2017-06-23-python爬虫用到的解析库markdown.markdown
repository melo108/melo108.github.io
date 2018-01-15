---
layout: post
title:  "python爬虫的解析库"
description: python爬虫 re + BeautifulSoup + pyquery
date:   2017-06-23 14:55:52 +0200
categories: jekyll update
---



# python爬虫的解析库
-----

<br>
<br>

## 1 re 模块，与正则表达式


<br>
**用处: 处理字符串，对字符串的 过滤 ，清洗，筛选**



<br>

> **1  正则表达式：  用特定的字符 表示 字符串的 逻辑公式**


> **2  python的 re库 -- 提供正则表达式的实现**


<br>

**re 相关操作**


<br>

> **re.match (pattern,string,flags=0)  最开头匹配**

        result = re.match(pattern,string,flags=0)

        result.group()  ---
        result.span()   --- 匹配结果范围

        group(n) === 返回的是 匹配中第n个的分组的内容

        泛匹配  .* (分组)--group()
        贪婪匹配
        非贪婪匹配 .*?
        转义
        re.S -- 忽略 换行

<br>


	import re
	match = re.match(r'[0-9]\d{5}', 'BIT 100081')
	if match:
	    print(match.group())  # None

<br>

> **re.search(pattern,string,flags=0)**

	import re
	match=re.search(r'[0-9]\d{5}','BIT 100081')
	if match:
	    print(match.group())   # 100081


<br>

> **re.findall(pattern,string,flags=0) 返回所有 结果列表**

	import re
	ls = re.findall(r'[0-9]\d{5}', 'BIT 100081')
	print(ls)  # ['100081']


<br>

> **re.sub(pattern,new_string,content)**


	import re
	
	res=re.sub(r'[0-9]\d{5}',':zipcode','BIT100081 TSU100053')
	print(res)   # BIT:zipcode TSU:zipcode


<br>

> **re.split(pattern,string,flags=0,maxsplit=1) 返回 split后的  结果列表**

  maxsplit=1 指定分割最大数

	import re
	ls = re.split(r'[0-9]\d{5}', 'BIT100081 Tsu231321D')
	print(ls)  # ['BIT', ' Tsu', 'D']
	re.compile()  复用


<br>

> **re.compile(pattern,flags=0)**

将正则表达式的字符串形式 ,编译成,正则表达式对象



<br>
<br>

## 2 BeautifulSoup模块

<br>

> **1  BeautifulSoup 库  install beautifulsoup4 网页 解析库 ---- 支持多种解析器**



> **2  解析库种类 :**

>      python 标准库
>         BeautifulSoup(markup,"html.parser")    内置
> 
>      lxml HTML 解析库
>         BeautifulSoup(markup,"lxml")
> 
>      lxml XML 解析库
>              BeautifulSoup(markup,"xml")**

> **3  实例 ：**
> 
    from bs4 import BeautifulSoup
    soup = BeautifulSoup(html,'lxml')
    print(soup.prettify())   # 格式化 -- 不全标准模式
    print(soup.title.string)


<br>



- **1 打开文档方式**

		 BeautifulSoup(html,"")    直接传入 html 字符串
		
		 BeautifulSoup(open("a.html"),encoding="") 打开文档文件




<br>


- **2 标签查找：**


<br>


> -  **遍历文档树：**


**标签选择器：速度快,   如果有多个 会只返回 第一个**

   
    soup.title  -- tag类型
    soup.head
    soup.p   

    --------------
     tag标签属性
    ---------------

    获取名称：
        tag.name    --- 标签的名称

    获取属性：
        tag.attrs["name"]
        tag["name"]
    获取内容：
            tag.string                 # 只找唯一的 标签内容，不唯一 ，返回None
            tag.strings                # 得到生成器,所有的 内容
            tag.stripped_strings       #去掉空格 返回所有内容

            tag.text
    嵌套选择：
        tag1.title.string

    子节点 子孙节点：

        tag.contents  [所有子代内容的列表]

        tag.childern  子节点对象的 迭代器

        tag.descendants 所有 子节点 孙代节点 的对象 迭代器

        tag.parent  父节点

        tag.parents  所有父节点 父节点的父节点 的迭代器

    兄弟节点：
        tag.next_siblings
        tag.next_sibling
        tag.previous_siblings
        tag.previous_sibling


    -----------------
      tag标签方法
    -----------------

    tag.get_text()  获取内容：所有的 文本

    tag.has_attr("")  判断是否有属性




<br>


- **搜索文档树  (find  find_all )**


find_all (name,attrs,recursive,text,**kwargs)  --- 列表

find(name,attrs,recursive,text,**kwargs) --- 标签对象


<br>


> **5种过滤器**


    #  字符过滤器:  内容完全匹配
    soup.find_all(name="ul")  #  标签名 完全匹配
    soup.find_all(id="id3")   #  标签名 完全匹配

    #  正则 匹配
    soup.find_all(name=re.compile("^ul"))  #  标签名 完全匹配
    soup.find_all(id=re.compile("^i"))

    soup.find_all(class_=re.compile("^i"))  # class_ python关键字 需要加 class_

    # 列表过滤器
    soup.find_all(id=["id1","id3"])  id等于列表中的任意一个


    # True
    soup.find_all(id="True")  # 所有有 id 的 标签


    # 方法选择器
    soup.find_all(has_class_not_id)  # 自定义方法选择

    def has_class_not_id(tag):
        return tag.has_attr("class") and not tag.has_attr("id")


<br>

> **find_all参数**

    参数 name=tag_name
        attrs = 属性 (一些特殊的属性) attrs={"":""}
        class_="cl1 css"  关键字
        text = "" 只找text的父级标签
        recursive = False 只找 直接子代标签 不找孙代
                    (默认为True)

<br>

> **find()**

**与find_all()一样，返回值是找到的第一个标签**

<br>


> **其他方法**


    find_parents()
    find_parent()

    find_next_siblings()
    find_next_sibling()

    find_previous_siblings()
    find_previous_sibling()


    find_all_next()
    find_next()

    find_all_previous()
    find_previous()




<br>

- **CSS 选择器：select(css选择器)**


		soup.select("ul li")
		soup.select(".class1 .c1 #d1")






<br>
<br>

## 3 pyquery 模块



> **jQuery --- 语法 api 一样**
> 
> **简洁  比 beautifulsoup库的语法更简洁**


<br>
<br>



**1 安装 PyQuery ： pip install pyquery**


<br>


**2  pyquery  初始化**



> **1 from pyquery import Pyqeury as pq**

> **2 doc = pq( 网页源代码 或 url 或html 文件 )**
> 
> **这里的doc(),可以理解为jQuery中的$()**

    网页代码字符串初始化

        from pyquery import PyQuery as pq
        html=""
        doc=pq(html)
        print(doc("li"))

    url初始化

        url=""
        doc=pq(url)
        print(doc("head"))

    文件初始化：

        f='demo.html'
        doc=pq(f)
        print(doc("body"))

<br>

**3  CSS 选择器**


> **doc("#id1 .list1 li"), 查找结果都是 pyquery 对象**

    查找子元素 :                                      
        find("li")          直接子元素
        children(".class1")  子代子元素


    查看父元素：
        parent("li")    父节点
        parents(".wrap")   所有祖先节点


    查看同级元素 兄弟元素：
        siblings("#d1")

<br>


**4  遍历：items() 转换成生成器**

    查找结果有多个元素：

        .items() 方法 变成 生成器 再 遍历


    单个元素：

        直接使用


<br>

**5  获取信息：**

    获取属性：

	    doc=pq(html)
	    a=doc(" .item-0 a")
	    a.attr("href")
	    a.attr.href

	        from pyquery import PyQuery as pq
	
	        url="http://httpbin.org"
	        doc=pq(url)
	
	        a=doc("a[href='http://github.com/kennethreitz/httpbin']")
	        href=a.attr.href
	
	        print(b)
	        http://github.com/kennethreitz/httpbin

    获取文本：

        a.text()

    获取HTML:

        a.html()


<br>

**6  DOM 操作：**

    li.addClass("")
    li.removeClass("")

    attr 自定义属性 attr("name","aaa")

    css 渲染样式   css("color":"red")

    remove   doc=pq(html)
             wrap=doc(".wrap")
             wrap.find("p").remove()     # 去掉 里面的p标签
             wrap.text()                # 只提取 hell world

    <div class="wrap">
        hello world
        <p> 哈哈哈 </p>
    </div>


<br>


**7  伪类选择器：**

    doc("li:first-child")
    doc("li:last-child")

    doc("li:nth-child(2n)")
    doc("li:gt(2)")

    doc("li:contains("hello")")


<br>


<br>

## 4 lxml库的 etree 模块

> **html---显示数据**

> **xml ---传输数据**

<br>

lxml 库  , path helper 工具辅助解析

**from lxml import etree**      版本问题 有时候 不能 import lxml 后 不能使用lxml.etree

**xml=etree.HTML(html)**           转换为HTML DOM

**xml.xpath("xpath语句")**

	link_list=xml.xpath("//div[@class="threadlist_lz clearfix"]//a[@class="j_th_tit "]/@href") # 匹配结果列表




