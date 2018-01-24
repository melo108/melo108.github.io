---
layout: post
title:  "cookie-session 与 Django的认证系统"
description:  cookie-session 与 Django的认证系统
date:   2017-05-26 14:35:52 +0200
categories: jekyll update
---


# cookie-session 与 Django的认证系统

--------------------

<br>

## 1 COOKIE 与 SESSION 

<br>

> **cookie的基本概念：**

cookie不属于http协议范围，由于http协议无法保持状态，但实际情况，我们却又需要“保持状态”，因此cookie就是在这样一个场景下诞生。

**cookie的工作原理**：由服务器产生内容，浏览器收到请求后保存在本地；当浏览器再次访问时，浏览器会自动带上cookie，这样服务器就能通过cookie的内容来判断。

<br>

> **session的基本概念**

cookie虽然在一定程度上解决了“保持状态”的需求，但是由于**cookie本身最大支持4096字节，以及cookie本身保存在客户端**，可能被拦截或窃取，因此就需要有一种新的东西，**session能支持更多的字节，并且他保存在服务器，有较高的安全性**。



基于http协议的无状态特征，服务器根本就不知道访问者身份。cookie起到桥接的作用。

我们可以给每个客户端的cookie分配一个唯一的id，这样用户在访问时，通过cookie的id，在服务器上保存一段时间的私密资料，如“账号密码”等等。

<br>

> **总结**：

> cookie弥补了http无状态的不足，让服务器知道来的身份；但是cookie以文本的形式保存在本地，自身安全性较差；
> 
> 所以我们就通过cookie识别不同的用户，对应的在session里保存私密的信息以及超过4096字节的文本。


> 当我们使用一款浏览器访问一个登陆页面的时候，一旦我们通过了认证。服务器端就会发送一组随机唯一的字符串（假设是123abc）到浏览器端，这个被存储在浏览端的东西就叫cookie。因为每个cookie都是唯一的，所以我们在电脑上换个浏览器再登陆同一个网站也需要再次验证。




<br>

## 2 Django实现的COOKIE

<br>


**设置Cookie**

	rep = HttpResponse(...) 或 rep ＝ render(request, ...) 或 rep ＝ redirect()
	 
	rep.set_cookie(key,value,...)

	rep.set_signed_cookie(key,value,salt='加密盐',...)　



**获取Cookie**

	request.COOKIES['key']
	
	request.get_signed_cookie(key, default=RAISE_ERROR, salt='', max_age=None)
	    #参数：
	        default: 默认值
	           salt: 加密盐
	        max_age: 后台控制过期时间


**删除cookie**

	response.delete_cookie("cookie_key",path="/",domain=name)



**参数介绍**
	
	def set_cookie(self, key,                 键
	　　　　　　　　　value='',            值
	　　　　　　　　　max_age=None,        超长时间
	　　　　　　　　　expires=None,        超长时间
	　　　　　　　　　path='/',           Cookie生效的路径，
	                                         浏览器只会把cookie回传给带有该路径的页面，这样可以避免将
	                                         cookie传给站点中的其他的应用。
	                                         / 表示根路径，特殊的：根路径的cookie可以被任何url的页面访问
	　　　　　　　　　　　　 
	                domain=None,         Cookie生效的域名
	                                        
	                                          你可用这个参数来构造一个跨站cookie。
	                                          如， domain=".example.com"
	                                          所构造的cookie对下面这些站点都是可读的：
	                                          www.example.com 、 www2.example.com 
	　　　　　　　　　　　　　　　　　　　　　　　　　和an.other.sub.domain.example.com 。
	                                          如果该参数设置为 None ，cookie只能由设置它的站点读取。
	
	　　　　　　　　　secure=False,        如果设置为 True ，浏览器将通过HTTPS来回传cookie。
	　　　　　　　　　httponly=False       只能http协议传输，无法被JavaScript获取
	                                         （不是绝对，底层抓包可以获取到也可以被覆盖）
	　　　　　　　　　　): pass


**由于cookie保存在客户端的电脑上，所以，JavaScript和jquery也可以操作cookie。**

	<script src='/static/js/jquery.cookie.js'>
	 
	</script> $.cookie("key", value,{ path: '/' });


**cookie的优缺点**

       优点：
           数据存在在客户端，减轻服务器端的压力，提高网站的性能。
       缺点：
           安全性不高：在客户端机很容易被查看或破解用户会话信息


<br>

## 3 Django实现的SESSION

<br>


> **session基本操作**

	1、设置Sessions值
	          request.session['session_name'] ="admin"
	2、获取Sessions值
	          session_name = request.session["session_name"]
	3、删除Sessions值
	          del request.session["session_name"]
	4、检测是否操作session值
	          if "session_name" is request.session :
	
	5、get(key, default=None)
	 
	fav_color = request.session.get('fav_color', 'red')
	 
	6、pop(key)
	 
	fav_color = request.session.pop('fav_color')
	 
	7、keys()
	 
	8、items()
	 
	9、setdefault()
	 
	10、flush() 删除当前的会话数据并删除会话的Cookie。
	            这用于确保前面的会话数据不可以再次被用户的浏览器访问
	            例如，django.contrib.auth.logout() 函数中就会调用它。
	 
	 
	11 用户session的随机字符串
	        request.session.session_key
	  
	        # 将所有Session失效日期小于当前日期的数据删除
	        request.session.clear_expired()
	  
	        # 检查 用户session的随机字符串 在数据库中是否
	        request.session.exists("session_key")
	  
	        # 删除当前用户的所有Session数据
	        request.session.delete("session_key")
	  
	        request.session.set_expiry(value)
	            * 如果value是个整数，session会在些秒数后失效。
	            * 如果value是个datatime或timedelta，session就会在这个时间后失效。
	            * 如果value是0,用户关闭浏览器session就会失效。
	            * 如果value是None,session会依赖全局session失效策略。



> **流程解析图**

<img src="https://images2017.cnblogs.com/blog/877318/201709/877318-20170929173142981-2106190717.png">


> **session存储的相关配置:**

（1）数据库配置（默认）：

	Django默认支持Session，并且默认是将Session数据存储在数据库中，即：django_session 表中。
	  
	   配置 settings.py
	  
	    SESSION_ENGINE = 'django.contrib.sessions.backends.db'   # 引擎（默认）
	      
	    SESSION_COOKIE_NAME ＝ "sessionid"                       # Session的cookie保存在浏览器上时的key，即：sessionid＝随机字符串（默认）
	    SESSION_COOKIE_PATH ＝ "/"                               # Session的cookie保存的路径（默认）
	    SESSION_COOKIE_DOMAIN = None                             # Session的cookie保存的域名（默认）
	    SESSION_COOKIE_SECURE = False                            # 是否Https传输cookie（默认）
	    SESSION_COOKIE_HTTPONLY = True                           # 是否Session的cookie只支持http传输（默认）
	    SESSION_COOKIE_AGE = 1209600                             # Session的cookie失效日期（2周）（默认）
	    SESSION_EXPIRE_AT_BROWSER_CLOSE = False                  # 是否关闭浏览器使得Session过期（默认）
	    SESSION_SAVE_EVERY_REQUEST = False                       # 是否每次请求都保存Session，默认修改之后才保存（默认）
	


（2）缓存配置　

	    配置 settings.py
	  
	    SESSION_ENGINE = 'django.contrib.sessions.backends.cache'  # 引擎
	    SESSION_CACHE_ALIAS = 'default'                            # 使用的缓存别名（默认内存缓存，也可以是memcache），此处别名依赖缓存的设置
	  
	  
	    SESSION_COOKIE_NAME ＝ "sessionid"                        # Session的cookie保存在浏览器上时的key，即：sessionid＝随机字符串
	    SESSION_COOKIE_PATH ＝ "/"                                # Session的cookie保存的路径
	    SESSION_COOKIE_DOMAIN = None                              # Session的cookie保存的域名
	    SESSION_COOKIE_SECURE = False                             # 是否Https传输cookie
	    SESSION_COOKIE_HTTPONLY = True                            # 是否Session的cookie只支持http传输
	    SESSION_COOKIE_AGE = 1209600                              # Session的cookie失效日期（2周）
	    SESSION_EXPIRE_AT_BROWSER_CLOSE = False                   # 是否关闭浏览器使得Session过期
	    SESSION_SAVE_EVERY_REQUEST = False                        # 是否每次请求都保存Session，默认修改之后才保存


（3）文件配置

	 配置 settings.py
	  
	    SESSION_ENGINE = 'django.contrib.sessions.backends.file'    # 引擎
	    SESSION_FILE_PATH = None                                    # 缓存文件路径，如果为None，则使用tempfile模块获取一个临时地址tempfile.gettempdir()        
	    SESSION_COOKIE_NAME ＝ "sessionid"                          # Session的cookie保存在浏览器上时的key，即：sessionid＝随机字符串
	    SESSION_COOKIE_PATH ＝ "/"                                  # Session的cookie保存的路径
	    SESSION_COOKIE_DOMAIN = None                                # Session的cookie保存的域名
	    SESSION_COOKIE_SECURE = False                               # 是否Https传输cookie
	    SESSION_COOKIE_HTTPONLY = True                              # 是否Session的cookie只支持http传输
	    SESSION_COOKIE_AGE = 1209600                                # Session的cookie失效日期（2周）
	    SESSION_EXPIRE_AT_BROWSER_CLOSE = False                     # 是否关闭浏览器使得Session过期
	    SESSION_SAVE_EVERY_REQUEST = False                          # 是否每次请求都保存Session，默认修改之后才保存





<br>

## 4 用户认证

<br>



**导入auth模块**

	from django.contrib import auth

<br>

**auth模块中的方法：**


> **authenticate()**  

提供了用户认证，即验证用户名以及密码是否正确,一般需要username  password两个关键字参数，
如果认证信息有效，会返回一个 User对象。

	user = authenticate(username='someone',password='somepassword')


> **login(HttpRequest, user)**

该函数接受一个HttpRequest对象，以及一个认证了的User对象

此函数使用django的session框架给某个已认证的用户附加上session id等信息。


	user = authenticate(username=username, password=password)
    if user is not None:
	    login(request, user)



> **logout(request) 注销用户**

　
该函数接受一个HttpRequest对象，无返回值。当调用该函数时，当前请求的session信息会全部清除。该用户即使没有登录，使用该函数也不会报错。

	from django.contrib.auth import logout
	   
	def logout_view(request):
	  logout(request)



> **user对象的 is_authenticated()**
	
	def my_view(request):
	  if not request.user.is_authenticated():
	    return redirect('%s?next=%s' % (settings.LOGIN_URL, request.path))



> **修改密码 set_password(password='')**

使用 set_password() 来修改密码


	ser = User.objects.get(username='')
	user.set_password(password='')
	user.save　



