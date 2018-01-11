---
layout: post
title:  "python爬虫用到的http请求库"
description: python爬虫 urllib + requests + selenium
date:   2017-06-22 14:55:52 +0200
categories: jekyll update
---



# python爬虫用到的http请求库

----------
**urllib + requests + selenium**

<img src="https://timgsa.baidu.com/timg?image&quality=80&size=b9999_10000&sec=1515674559688&di=19b48fdd25c86cc4b728c68185e01d60&imgtype=0&src=http%3A%2F%2Fwww.th7.cn%2Fd%2Ffile%2Fp%2F2015%2F03%2F22%2Fd49bf98874c6221fa020f8f940c892f3.jpg">

<br>
<br>

## 1 urllib库


> **加代理 cookie的时候 api 很繁琐 --- 各种handler**
> 
> **响应得到的是字节流 bytes**
> 
> **py2 urllib2.urlopen 等同于 py3 urllib.request.urlopen**


<br>

**urllib 是 python3 内置的 http 请求库**


**urllib 的 常用模块:**

        urllib,request  请求模块
        urllib.error   异常处理模块
        urllib.parse   url 解析 处理方法
        urllib.robotparser  robots.txt解析模块





 
<br>

**1. urllib.request.urlopen**   



    urllib.request.urlopen(url,data=None,timeout=1)

<br>

**请求**




> 直接请求：get类型

    import urllib.request
    response=urllib.request.urlopen("http://www.baidu.com")
    response.read().decode("utf-8")



> 直接请求 post 类型

    >>> import urllib.request
    >>> import urllib.parse
    >>> data=urllib.parse.urlencode({"hello":"world"}).encode("utf-8")
    >>> response=urllib.request.urlopen("http://httpbin.org/post",data=data)
    >>> response.read().decode("utf-8")






**响应**


> 响应类型：


    >>> import urllib.request
    >>> response=urllib.request.urlopen("http://www.baidu.com")
    >>> response
    <http.client.HTTPResponse object at 0x0000000002E35E48>

> 状态码

    >>> response.status
    200

> 响应头

    >>> response.getheaders()
	    [('Date', 'Tue, 14 Nov 2017 14:30:39 GMT'), ('Content-Type', 'text/html; charset=ut
	    f-8'), ('Transfer-Encoding', 'chunked'), ('Connection', 'Close'), ('Vary', 'Accept-
	    Encoding'), ('Set-Cookie', 'BAIDUID=EC3F27C9ED7F4B3FDF533DA943D30596:FG=1; expires=
	    Thu, 31-Dec-37 23:55:55 GMT; max-age=2147483647; path=/; domain=.baidu.com'), ('Set
	    -Cookie', 'BIDUPSID=EC3F27C9ED7F4B3FDF533DA943D30596; expires=Thu, 31-Dec-37 23:55:
	    55 GMT; max-age=2147483647; path=/; domain=.baidu.com'), ('Set-Cookie', 'PSTM=15106
	    69839; expires=Thu, 31-Dec-37 23:55:55 GMT; max-age=2147483647; path=/; domain=.bai
	    du.com'), ('Set-Cookie', 'BDSVRTM=0; path=/'), ('Set-Cookie', 'BD_HOME=0; path=/'),
	     ('Set-Cookie', 'H_PS_PSSID=1463_24565_21096_24880_20928; path=/; domain=.baidu.com
	    '), ('P3P', 'CP=" OTI DSP COR IVA OUR IND COM "'), ('Cache-Control', 'private'), ('
	    Cxy_all', 'baidu+a5ac9332a10b24f979657fc4d2d6e207'), ('Expires', 'Tue, 14 Nov 2017
	    14:30:29 GMT'), ('X-Powered-By', 'HPHP'), ('Server', 'BWS/1.1'), ('X-UA-Compatible'
	    , 'IE=Edge,chrome=1'), ('BDPAGETYPE', '1'), ('BDQID', '0xdba809710000d6b2'), ('BDUS
	    ERID', '0')]

    >>> response.getheader("Server")
    'BWS/1.1'




> 获取响应体内容：(字节流内容需要 解码)

    >>> response.read().decode("utf-8")



<br>

**2 urllib.request.Request**

<br>

**request 对象**

> request 对象


可以添加 url data headers 的信息


	req=urllib.request.Request(url,data=data,headers=headers,METHOD="post")

	response=urllib.request.urlopen(req)




> request.add_header

    req=urllib.request.Request(url,data,method)

    req.add_header("User-Agent","...")


<br>

**3 handler**

<br>

**代理**

	proxy_handler = urllib.request.ProxyHandler({
	"http":"http://127.0.0.1:9743"
	"https":"https://127.0.0.1:9743"
	})
	
	opener = urllib.request.build_opener(proxy_handler)
	
	response = opener.open("http://www.baidu.com")

**cookie**


> 生成cookie
	
	import cookiejar
	import urllib.request
	
	cookie = http.cookiejar.Cookiejar()
	handler = urllib.request.HTTPCookieProcessor(cookie)
	
	opener=urllib.request.build_opener(handler)
	
	response=opener.open("http://www.baidu.com")
	
> 保存为本地cookie文档
	
	import http.cookiejar, urllib.request
	
	filename="cookie.txt"
	
	cookie=http.cookiejar.MozillaCookieJar(filename)  # |火狐浏览器| CookieJar()的子类 可以保存cookie文件
	#cookie=http.cookiejar.LWPCookieJar(filename)      # |LWP cookie 格式| CookieJar()的子类 可以保存cookie文件
	handler=urllib.request.HTTPCookieProcessor(cookie)
	
	opner=urllib.request.build_opener(handler)
	response=opener.open(url)
	
	cookie.save(ignore_discard=True,ignore_expire-True)
	
	
> 导入cookie：
	
	import http.cookiejar, urllib.request
	cookie=http.cookiejar.LWPCookieJar()
	cookie.load("cookie.txt",ignore_discard=True,ignore_expire=True)
	handler=urllib.request.HTTPCookieProcessor(cookie)
	opener=urllib.request.bulid_opener(handler)
	response=opener.open(url)
	response.read()

<br>

**4 异常处理 ， urllib.error**

<br>

> URLError ---- reason

    import urllib.error, urllib.request
    try:
        response=urllib.request.urlopen(url)
    except urllib.eror.URLError as e:
        print(e.reason)

> HTTPError ---  reason code headers

    from urllib import error, request

    try:
        response=request.urlopen(url)
    except error.HTTPError as e:
        print(e.reason,e.code,e.headers)
    except error.URLError as e:
        print(e.reason)


<br>

**5 URL 解析,urllib.parse**

<br>

> url 解析 urlparse

        from urllib.parse import urlparse
        result=urlpasrse("http://www.baidu.com/index.html")

        ParseResult(scheme='http', netloc='www.baidu.com', path='/index.html', params='', query='', fragment='')



> url 拼接 urlunparse 

	["http","www.baidu.com","index.html"]


> urljoin 拼接 

	urljoin(url1,url2) 以后变得为基准





> urlencode 编码 字典 -- >请求参数

        from urllib.parse import urlencode
        data_dict={}
        url= base_url + urlencode(data_dict)

<br>

**注意 URL编码**

> **urllib.parse.urlencode(dic)** **编辑字典格式**
> 
> **urllib.request.quote(string)** **编辑字符串格式**



	import urllib.request
	import urllib.parse
	
	url='https://www.baidu.com/s?'
	
	keyword_dic={'kd':'哈哈哈 '}#'kd=嘿嘿嘿'
	
	print(urllib.parse.urlencode(keyword))



<br>
<br>

## 2 requests库




> **更强大的 http 请求库**


> **得到的是 string 字符串 区别于 urllib**



> **Requests : 基于python  基于urllib3  采用 Apache2 Licensed 开源协议 的 http 请求库**


> **各种请求方式 ：get post put delete head  option**



<br>

<br>

**1  基本使用介绍**

<br>



> **（1）GET 请求：**


**普通get请求:**

        >>> import requests
        >>> response= requests.get("http://httpbin.org/get")
        >>> response.text
            '{\n  "args": {}, \n  "headers": {\n    "Accept": "*/*", \n    "Accept-Encoding": "gzip, deflate", \n    "
            Connection": "close", \n    "Host": "httpbin.org", \n    "User-Agent": "python-requests/2.18.2"\n  }, \n
            "origin": "223.72.93.151", \n  "url": "http://httpbin.org/get"\n}\n'

**带参数的get请求：(params 接收 一个字典)**

        >>> params={"name":"grammy","age":21}
        >>> response=requests.get("http://httpbin.org/get",params=params)
        >>> response.text
            '{\n  "args": {\n    "age": "21", \n    "name": "grammy"\n  }, \n  "headers": {\n    "Accept": "*/*", \n
              "Accept-Encoding": "gzip, deflate", \n    "Connection": "close", \n    "Host": "httpbin.org", \n    "Use
            r-Agent": "python-requests/2.18.2"\n  }, \n  "origin": "223.72.93.151", \n  "url": "http://httpbin.org/get
            ?name=grammy&age=21"\n}\n'


	添加 headers 参数

                headers={
                    useragent:
                    referer:
                    cookies:
                }



    添加 cookies

            cookies={"user_session":"Lo5zIk2SyfEdeTSVIQJV2xUIjJ9XP6mass5eMtr86N2b1sTY"}

**默认跳转**

	allow_redirects=True ==== 默认跳转


<br>

> **（2）POST 请求**


**有请求体,formdata**


          data={}
          headers={}

          response=requests.post(url,data=data,headers=headers)

**同样可以添加参数**





<br>

> **（3）response 属性**



	response.status_code
	
	response.headers
	
	response.cookies
	response.cookies.get_dict()
	response.cookies.items() 元组形式
	
	response.encoding = response.apprent_encoding
	
	response.raise_for_status()--状态码不是200
	会触发异常
	
	response.url
	
	response.history(跳转之前的页面历史)
	
	response.content 二进制数据
	
	
	response.iter_content()--写大文件的时候使用
	迭代器形式：
	with open("a.jpg","wb") as f:
	    for line in response.iter_content():
	        f.write(line)
	
	response.json()
	
   	相当于 json.loads(response.text)





<br>

> **4 高级操作**




<br>

**文件上传：**

post -- files {"file":文件句柄}

    import requests
    files={"file":open("1.jpg","rb")}
    response =requests.post("http:/xxx/post",files=files)


<br>

**获取cookie**

维持用户登录


> 使用response获取 

		response.cookies.get_dict()



> session对象 

         session=requests.session()  使用session 对象请求

         session.get("http://xxx/set/number/123324")  #   会自动设置 set cookie




<br>

**证书验证：**

> 证书机构 --- 服务端 + 客户端 双向的 证书验证身份
> 
> CA中心 分发证书 --


<br>

> verify=False


	response =requests.get(url,verify=False)

    不带证书也可以访问



> 强制限制 | 

    response =requests.get(url,cert=(本地证书元祖形式(crt文件,key)))  |

<br>

**代理设置：**


> socks代理可以代理全部协议的代理

http代理

        proxies={
        "http":"http://xxx:port"
        "https":"https://xxx:port"
        }
        response= requests.get(url,proxies=proxies)

有用户名和密码的代理

		http://user:password@xxx:port
            proxiex={
            "http":"http://user:password@xxx:port"
            "https":"https://xxx:port"
            }

socks代理

        pip install requests[socks]
        proxiex={
        "http":"socks5://xxx:port"
        "https":"socks5://xxx:port"
        }
        response= requests.get(url,proxies=proxies)


<br>

**超时设置：**

     response= requests.get(url,timeout=1)  限定 响应时间



<br>

**认证设置：**

      response= requests.get(url,auth=("user","123"))  访问前 需要登录验证的网站



<br>

**异常处理：**

          import requests
          from requests.exceptions import ReadTimeout, ConnectionError, RequestException
          try:
              response=requests.get(url)
              print(response.status_code)
          except ReadTimeout:
              print("Timeout")
          except  ConnetionError:
              print("HTTP Error")
          except  RequestException:
              print("error")

<br>


<br>
<br>

## 3 selenium




> **自动化测试工具,  支持多种浏览器**


> **在爬虫中主要用来 解决javascript渲染问题**


> **selenium  发送指令 驱动  浏览器动作**


> **安装  pip install selenium，chromedriver，PhantomJS等一些浏览器驱动 -- 要注意版本 兼容**

> **phantomjs  无界面的浏览器 (配置的时候 先配置全局变量 然后 把phantomjs.exe 放入 python的scripts中)**


<br>

**1 使用前需要导入的依赖工具：**

> from selenium import webdriver   **# 实例浏览器对象**
> 
> from selenium.webdriver.common.by import By          **# 按什么方式查找**
> 
> from selenium.webdriver.common.keys import Keys      **# 键盘操作**
> 
> from selenium.webdriver.support import expected_conditions as EC   **# 预期的状态**
> 
> from selenium.webdriver.support.wait import WebDriverWait  ** # 等待操作**
> 
> from selenium.webdriver import ActionChains      #**交互动作： 驱动浏览器 连续动作链**
> 

**小例子**

    >>> browser=webdriver.Chrome()
    >>> browser.get("http://www.baidu.com")

    >>> input=browser.find_element_by_id("kw")
    >>> input_send_keys("Python")
    >>> input.send_keys(Keys.ENTER)

    >>> wait=WebDriverWait(browser,10)
    >>> wait.until(EC.presence_of_element_located((By.ID,"content_left")))
        >>> print(browser.current_url)
        >>> print(browser.get_cookies())
        >>> print(browser.page_source)

    >>> browser.close()


<br>

**2 selenium 基本使用：**

<br>

**声明浏览器对象：**


        首先引入 webdriver

        from selenium import webdriver

         browser = webdriver.Chrome()
         browser = webdriver.Firefox()
         browser = webdriver.Edge()
         browser = webdriver.PhantomJS()
         browser = webdriver.Safari()
<br>

**访问页面：**

        browser.get(url)
        browser.page_source 源代码
        browser.close()

<br>

**查找单个元素：**

	    find_element_by_id
	    find_element_by_name
	    find_element_by_link_text         通过文本查找
	    find_element_by_partial_link_text  部分文本内容
	    find_element_by_class_name
        find_element_by_css_selector("#id1")
        find_element_by_xpath("//*[@id='id1']")
        

<br>
           
**查找多个元素：**

        find_elements_by_class_name("c1")

        find_elements(By.CSS_SELECTOR,".c1")

<br>

**也可以导入 By 直接写在参数里**

        browser.find_element(By.ID,'q')

<br>

**获取标签的信息：**

        tag.get_attribute("class")
        tag.text
        tag.id
        tag.location  x,y 像素位置
        tag.tag_name
        tag.size      width,height


<br>

**元素操作：  选择特定的元素 然后控制动作**


input框输入

        input.send_keys("iphone")

        time.sleep(3)
清除内容

        input.clear()
        input.send_keys("ipad")

点击

        button=browser.find_element_by....("")
        button.click()



<br>

**交互动作： 驱动浏览器 连续动作链**


        实例一  基于一条链  一下从头移动到尾

            driver.switch_to.frame("idframe1")
                # driver.switch_to.parent_frame("iframe2")

            source = driver.find_element_by_id("..")
            target = driver.find_element_by_id("..")

            from selenium.webdriver import ActionChains as ac

            actions = ac(driver)
            actions.drag_and_drop(source,target)
            actions.perform()



        实例二 基于不同的actionchains,可以控制 移动的位移

            source = driver.find_element_by_id("..")
            target = driver.find_element_by_id("..")

            distance = target.location["x"] - target.location["x"]

            actions = ac(driver)

            actions.click_and_hold(source).perform()
            actions.move_by_offset(xoffset=distance,yoffset=0).perform()
            actions.realse().perform()

            res=0
            while res < distance:
                actions.move_by_offset(xoffset=offset,yoffset=0).perform()
            actions.realese().perform()


<br>

**执行js： 进度条的下拉**

         browser.execute_script("window.scrollTop(0,document.body.scrollHeight)")


<br>

**frame 切换** 

里面的不能查到外面的 frame

      browser.switch_to.frame("另一个frame的id")
      browser.switch_to.parent_frame("另一个frame的id")


<br>

**等待：**

隐式等待：

        browser.implicity_wait(10)   规定时间内加载

显示等待：

        等待时间 -- 等待条件  直到最长的等待时间

        from selenium.webdriver.support import expected_conditions as EC

        wait=WebDriverWait(browser,10)

        input = wait.until(EC.presence_of_element_located((By.ID,"q"))) # 直到 出现

        button = wait.until(EC.element_to_be_clickable((By.CSS_SELECTOR,".btn-search"))) # 直到 出现

<br>

**浏览器前进后退：**

    browser.get(url1)
    browser.get(url2)
    browser.get(url3)

    browser.back()
    browser.forward()



<br>
**cookies**

      browser.get_cookies()
      browser.add_cookie({})
      browser.delete_all_cookie({})


<br>

**窗口 选项卡: (快捷键 新建选项卡 -- 不常用)**

        browser.execute_script("window.open()")

        browser.window_handles  --- 所有的window引用

        browser.switch_to_window(browser.window_handles[0])
        browser.get(url1)
        time.sleep(3)
        browser.switch_to_window(browser.window_handles[1])
        browser.get(url2)


<br>

**异常处理：**

         from selenium.common.exceptions import TimeoutException,NoSuchElementException

         try:
         except   TimeoutException:
         except  NoSuchElementException:
         finally: browser.close()




<br>
<br>


**例子--reqeusts---校花网的小视频**


	import requests
	from pyquery import PyQuery as pq
	import re
	import fake_useragent
	import hashlib,os,time
	from concurrent.futures import ThreadPoolExecutor
	
	ua=fake_useragent.UserAgent()
	useragent = ua.chrome
	
	urlList=["http://www.xiaohuar.com/list-3-%s.html" % page for page in range(6)]
	pool = ThreadPoolExecutor(20)
	
	def get_indexContent(url):
	
	    response=requests.get(url)
	    if response.status_code == 200:
	        return response.text
	
	
	def get_detailUrl(indexContent):
	    indexContent=indexContent.result()
	    detailUrlList=re.findall(r'class="items".*?href="(http.*?)"',indexContent,re.S)
	    for detailUrl in detailUrlList:
	        print("detailUrl",detailUrl)
	        pool.submit(get_indexContent,detailUrl).add_done_callback(get_sourceUrl)
	
	
	def get_sourceUrl(indexcontent):
	    indexcontent=indexcontent.result()
	    doc=pq(indexcontent)
	    sourceUrlList=doc("video source").attr("src")
	    for sourceUrl in sourceUrlList:
	        print("sourceUrl",sourceUrl)
	        pool.submit(handle_source,sourceUrl)
	
	def handle_source(sourceUrl):
	    response=requests.get(sourceUrl)
	    filecontent=response.content
	    dlpath=r"C:\Users\Administrator\Desktop\111"
	    md=hashlib.md5()
	    md.update(str(time.time()).encode("utf-8"))
	    md.update(sourceUrl.encode("utf-8"))
	    filepath=os.path.join(dlpath,"%s.mp4"%md.hexdigest())
	    with open(filepath,"wb") as f:
	        f.write(filecontent)
	
	def main():
	    for url in urlList:
	        print("url",url)
	        pool.submit(get_indexContent,url).add_done_callback(get_detailUrl)

	if __name__ == '__main__':
	    main()

<br>

**例子2 requests---51job的工作信息**


	import requests,re
	from bs4 import BeautifulSoup
	import json
	
	from urllib.parse import urlencode
	
	from concurrent.futures import ThreadPoolExecutor
	
	pool=ThreadPoolExecutor(50)
	
	params = {
	    "lang": "python",
	    "stype": "1",
	    "postchannel": "0000",
	    "workyear": "99",
	    "cotype": "99",
	    "degreefrom": "04",
	    "jobterm": "99",
	    "companysize": "99",
	    "lonlat": "0,0",
	    "radius": "-1",
	    "ord_field": "0",
	    "confirmdate": "9",
	    "fromType": "7",
	    "dibiaoid": "0",
	    "specialarea": "00",
	}
	keyword = "python"
	session = requests.session()
	
	
	def get_urlList():
	
	    base_url = "http://search.51job.com/list/010000,000000,0000,00,9,07,%s,2,1.html?"%keyword
	    response = session.get(base_url,
	                headers={
	        "User-Agent":"Mozilla/5.0 (Windows NT 6.1; WOW64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/58.0.3029.110 Safari/537.36"
	                },
	                params=params
	                )
	    response.encoding = response.apparent_encoding
	
	    content = response.text
	
	
	    all_page_num = int(re.findall(r"共(\d+)页",content)[0])
	
	
	    request_urlList = ["http://search.51job.com/list/010000,000000,0000,00,9,07,%s,2,%s.html?"%(keyword,page) for page in range(1,all_page_num+1) ]
	
	    return request_urlList
	
	
	def get_jobInfo(url):
	    response = session.get(url,headers={"User-Agent":"Mozilla/5.0 (Windows NT 6.1; WOW64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/58.0.3029.110 Safari/537.36"
	                        }, params=params
	                        )
	
	    response.encoding = response.apparent_encoding
	    content = response.text
	    soup = BeautifulSoup(content,"html.parser")
	    tag=soup.find("div",attrs={"class":"el title"})
	    if tag:
	        tag.extract()
	    # soup.extact(tag)
	
	
	    titleList=[{"工作名":item.attrs["title"],"详细页网址":item.attrs["href"]} for item in soup.select(".el .t1 a")]
	    namelist=[{"公司名":item.attrs["title"]} for item in soup.select(".el .t2 a")]
	    locationlist=[{"地址":item.text} for item in soup.select(".el .t3")]
	    salarylist=[{"工资":item.text} for item in soup.select(".el .t4")]
	
	    infoList = list(zip(titleList,namelist,locationlist,salarylist))
	
	    return infoList
	
	
	def save_file(infoList):
	    infoList=infoList.result()
	    for info in infoList:
	        pool.submit(write, info)
	
	
	def write(info):
	    data = json.dumps(info, ensure_ascii=False)
	    with open("51job.txt", 'a', encoding="utf-8") as f:
	        f.write(data + "\r\n")
	
	
	def main():
	    urlList = get_urlList()
	
	    for url in urlList:
	        print(url)
	        pool.submit(get_jobInfo, url).add_done_callback(save_file)
	
	if __name__ == '__main__':
	    main()



<br>

**例子3 selenium--淘宝iphonex信息**

	from selenium import webdriver
	from selenium.webdriver.common.by import By
	from selenium.webdriver.common.keys import Keys
	from selenium.webdriver.support import expected_conditions as EC
	from selenium.webdriver.support.wait import WebDriverWait
	from selenium.webdriver import ActionChains
	
	from pyquery import PyQuery as pq
	import re,json
	
	driver = webdriver.Chrome()
	
	driver.get("https://www.taobao.com/")
	
	wait=WebDriverWait(driver,10)
	
	input_tag = wait.until(EC.presence_of_element_located((By.CSS_SELECTOR, "#q")))  # 直到 出现
	
	button = wait.until(EC.element_to_be_clickable((By.XPATH, '//div[@class="search-button"]/button')))  # 直到 出现
	
	# input_tag = driver.find_element_by_css_selector("#q")
	
	input_tag.send_keys("iphone x")
	button.click()
	
	# button = driver.find_element_by_xpath('//div[@class="search-button"]/button').click()
	
	driver.execute_script("window.scrollTo(0,document.body.scrollHeight)")
	
	wait.until(EC.element_to_be_clickable((By.XPATH, "//li[@class='item next']/a[@class='J_Ajax num icon-tag']")))
	
	
	def get_all_pageNum():
	
	    page_source = driver.page_source
	
	    doc = pq(page_source)
	    all_page_num_text= doc(".inner .total").text()
	    all_page_num = int(re.search(r"(\d+)",all_page_num_text).group())
	
	    return all_page_num
	
	
	def turnPage(all_page_num,start_pageNum = 1):
	
	    try:
	        while start_pageNum <= all_page_num:
	            page_input=wait.until(EC.presence_of_all_elements_located((By.XPATH,"//div[@class='inner clearfix']/div[@class='form']/input[@class='input J_Input']")))[0]
	
	            page_input.clear()
	            page_input.send_keys(start_pageNum)
	            page_input.send_keys(Keys.ENTER)
	
	            page_source = driver.page_source
	
	            get_info(page_source)
	
	            start_pageNum +=1
	            print(start_pageNum)
	    except:
	        turnPage(all_page_num,start_pageNum = start_pageNum)
	
	
	
	
	
	def get_info(page_source):
	
	    doc = pq(page_source)
	
	    itemList=doc(".item").items()
	
	    for item in itemList:
	        title=item.find(".J_U2IStat").text()
	        price = item.find(".price-row").text()
	        location = item.find(".location").text()
	        comment = item.find(".comment").text()
	
	        data={"title":title,"price":price,"location":location,"comment":comment}
	
	        if data["title"]:
	            data=json.dumps(data,ensure_ascii=False)
	            with open('tbiphone.txt',"a",encoding="utf-8") as f:
	                f.write(data+'\r\n')
	
	all_page_num = get_all_pageNum()
	
	page_source = turnPage(all_page_num)



