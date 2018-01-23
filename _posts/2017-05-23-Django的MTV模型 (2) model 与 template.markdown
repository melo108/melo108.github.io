---
layout: post
title:  "Django的MTV模型详解 (2) template 与 model"
description:  template 与 model
date:   2017-05-23 16:55:52 +0200
categories: jekyll update
---




# Django的MTV模型详解 （2）
------

<img src="https://timgsa.baidu.com/timg?image&quality=80&size=b9999_10000&sec=1516714973018&di=13b6bfc37ce9f8804c542127c7122dfd&imgtype=0&src=http%3A%2F%2Fassets.tianmaying.com%2Fmd-image%2F0a7650b34227a541ffdf4c20e5c1c52a.png">

<br>


## 2 模板层 views

<br>

将页面的设计和Python的代码分离开会更干净简洁更容易维护。 我们可以使用 Django的 模板系统 (Template System)来实现这种模式。

HTML代码＋模板语法

模版包括在使用时**会被值替换掉的变量**，和**控制模版逻辑的标签**


<br>

**（1）模板语法之变量**

在 Django 模板中遍历复杂数据结构的关键是句点字符

> **句点符**

	
	<h4>{{s}}</h4>
	<h4>列表:{{ l.0 }}</h4>
	<h4>列表:{{ l.2 }}</h4>
	<h4>字典:{{ dic.name }}</h4>
	<h4>日期:{{ date.year }}</h4>
	<h4>类对象列表:{{ person_list.0.name }}</h4>

也可以用来引用对象的方法(无参数方法)

	<h4>字典:{{ dic.name.upper }}</h4>

<br>

**(2) 模板之过滤器 {{obj | filtername : param}}**


> **default**

如果一个变量是false或者为空，使用给定的默认值。否则，使用变量的值。例如：

	{{ value|default:"nothing" }}


> **length**

返回值的长度。它对字符串和列表都起作用。例如：


	{{ value|length }}

如果 value 是 ['a', 'b', 'c', 'd']，那么输出是 4。


> **filesizeformat**

将值格式化为一个 “人类可读的” 文件尺寸 （例如 '13 KB', '4.1 MB', '102 bytes', 等等）。例如：

	{{ value|filesizeformat }}

如果 value 是 123456789，输出将会是 117.7 MB。　　


> **date**

如果 value=datetime.datetime.now()

	{{ value|date:"Y-m-d" }}　　后边加显示格式


> **slice**

如果 value="hello world"


	{{ value|slice:"2:-1" }}


> **truncatechars**

如果字符串字符多于指定的字符数量，那么会被截断。截断的字符串将以可翻译的省略号序列（“...”）结尾。

参数：要截断的字符数

例如：

	{{ value|truncatechars:9 }}

如果value是“Joel 是 a >，输出将为“Joel i ...”。


> **safe**

Django的模板中会对HTML标签和JS等语法标签进行自动转义，原因显而易见，这样是为了安全。

但是有的时候我们可能不希望这些HTML元素被转义，比如我们做一个内容管理系统，后台添加的文章中是经过修饰的，这些修饰可能是通过一个类似于FCKeditor编辑加注了HTML修饰符的文本，如果自动转义的话显示的就是保护HTML标签的源文件。

为了在Django中关闭HTML的自动转义有两种方式，如果是一个单独的变量我们可以通过过滤器“|safe”的方式告诉Django这段代码是安全的不必转义。比如：


	value="<a href="">点击</a>"
	
	{{ value|safe}}


<br>

**（3）模板之标签  {% tag %}**

标签比变量更加复杂：一些在输出中创建文本，一些通过循环或逻辑来控制流程，一些加载其后的变量将使用到的额外信息到模版中。

一些标签需要开始和结束标签 （例如{% tag %} ...标签 内容 ... {% endtag %}）

<br>

> **for标签**



	遍历每一个元素：

	{% for person in person_list %}
	    <p>{{ person.name }}</p>
	{% endfor %}

**{% for obj in list reversed %}反向完成循环**。

	遍历一个字典：
	
	{% for key,val in dic.items %}
	    <p>{{ key }}:{{ val }}</p>
	{% endfor %}

**注：循环序号可以通过｛｛forloop｝｝显示**

	forloop.counter         The current iteration of the loop (1-indexed)
	forloop.counter0    The current iteration of the loop (0-indexed)
	forloop.revcounter  The number of iterations from the end of the loop (1-indexed)
	forloop.revcounter0 The number of iterations from the end of the loop (0-indexed)
	forloop.first           True if this is the first time through the loop
	forloop.last            True if this is the last time through the loop

<br>

> **for ... empty**

for 标签带有一个可选的{% empty %} 从句，以便在给出的组是空的或者没有被找到时，可以有所操作。

	{% for person in person_list %}
	    <p>{{ person.name }}</p>
	
	{% empty %}
	    <p>sorry,no person here</p>
	{% endfor %}


<br>


> **if 标签**

{% if %}会对一个变量求值，如果它的值是“True”（存在、不为空、且不是boolean类型的false值），对应的内容块会输出。

	{% if num > 100 or num < 0 %}
	    <p>Error</p>
	{% elif num > 80 and num < 100 %}
	    <p>优秀</p>
	{% else %}
	    <p>好</p>
	{% endif %}


<br>


> **with 标签**

使用一个简单地名字缓存一个复杂的变量，当你需要使用一个“昂贵的”方法（比如访问数据库）很多次的时候是非常有用的

	例如：
	
	{% with total=business.employees.count %}
	    {{ total }} employee{{ total|pluralize }}
	{% endwith %}

<br>

> **csrf_token**

这个标签用于跨站请求伪造保护


<br>

**(4) 自定义标签和过滤器**


1.在settings中的INSTALLED_APPS配置当前app，先注册app，不然django无法找到自定义的simple_tag.

2.在app中创建templatetags模块(模块名只能是templatetags)

3.创建任意 .py 文件，如：my_tags.py

	from django import template
	from django.utils.safestring import mark_safe
	 
	register = template.Library()   #register的名字是固定的,不可改变
	 
	 
	@register.filter
	def filter_multi(v1,v2):  # 简单的运算
	    return  v1 * v2

	simple_tag  返回什么页面就是什么

        @ register.simple_tag
        def func(a,b):
            return a+b

      页面 ---      {% load ry %}
                   {% func 1 4 % }

    inclusion_tag  返回的结果和模板 html 共同结合

	    a. @register.inclusion_tag('base.html')
	
	        def func2(request):
	            return {"menu_dict":menu_dict}
	
	          {% load rbac %}
	    b.    {% menu request %}

4.在使用自定义simple_tag和filter的html文件中导入之前创建的 my_tags.py

	{% load my_tags %}

5.使用simple_tag和filter
	-------------------------------.html
	{% load xxx %}  
	      
	# num=12
	{{ num|filter_multi:2 }} #24
	 
	{{ num|filter_multi:"[22,333,4444]" }}
	 
	{% simple_tag_multi 2 5 %}  参数不限,但不能放在if for语句中
	{% simple_tag_multi num 5 %}


<br>

**(5) 模板继承 (extend)**

Django模版引擎中最强大也是最复杂的部分就是模版继承了。模版继承可以让您创建一个基本的“骨架”模版，它包含您站点中的全部元素，并且可以定义能够被子模版覆盖的 blocks 。

extends 标签是这里的关键。它告诉模版引擎，这个模版“继承”了另一个模版。当模版系统处理这个模版时，首先，它将定位父模版.

如果你在模版中使用 {% extends %} 标签，它必须是模版中的第一个标签。其他的任何情况下，模版继承都将无法工作。

在base模版中设置越多的 {% block %} 标签越好。请记住，子模版不必定义全部父模版中的blocks，所以，你可以在大多数blocks中填充合理的默认内容，然后，只定义你需要的那一个。多一点钩子总比少一点好。


	<!DOCTYPE html>
	<html lang="en">
	<head>
	    <link rel="stylesheet" href="style.css" />
	    <title>{% block title %}My amazing site{%/span> endblock %}</title>
	</head>
	
	<body>
	    <div id="sidebar">
	        {% block sidebar %}
	        <ul>
	            <li><a href="/">Home</a></li>
	            <li><a href="/blog/">Blog</a></li>
	        </ul>
	        {% endblock %}
	    </div>
	
	    <div id="content">
	        {% block content %}{% endblock %}
	    </div>
	</body>
	</html>

	-----------------------华丽的分割线-------------------


	{% extends "base.html" %}
	 
	{% block title %}My amazing blog{% endblock %}
	 
	{% block content %}
	{% for entry in blog_entries %}
	    <h2>{{ entry.title }}</h2>
	    <p>{{ entry.body }}</p>
	{% endfor %}
	{% endblock %}

并不能在一个模版中定义多个相同名字的 block 标签，
如果在一个模版中有两个名字一样的 block 标签，模版的父模版将不知道使用哪个block的内容。


<br>

## 3 模型层 model

<br>


**model 基础**

<br>



- **1  ORM与数据库的映射关系**
	
	
		映射关系：
		
		　　　  表名  <－－－－－－－> 类名
		
		       字段  <－－－－－－－> 属性
		
		　　　　表记录 <－－－－－－－>类实例对象



<br>


- **2 创建表(建立模型)**

		
		class Author(models.Model):
		    nid = models.AutoField(primary_key=True)
		    name=models.CharField( max_length=32)
		    age=models.IntegerField()
		 
		    # 与AuthorDetail建立一对一的关系
		    authorDetail=models.OneToOneField(to="AuthorDetail")
		 
		class AuthorDetail(models.Model):
		 
		    nid = models.AutoField(primary_key=True)
		    birthday=models.DateField()
		    telephone=models.BigIntegerField()
		    addr=models.CharField( max_length=64)
		    
		class Publish(models.Model):
		    nid = models.AutoField(primary_key=True)
		    name=models.CharField( max_length=32)
		    city=models.CharField( max_length=32)
		    email=models.EmailField()
		 
		 
		class Book(models.Model):
		 
		    nid = models.AutoField(primary_key=True)
		    title = models.CharField( max_length=32)
		    publishDate=models.DateField()
		    price=models.DecimalField(max_digits=5,decimal_places=2)
		    keepNum=models.IntegerField()<br>    commentNum=models.IntegerField()
		 
		    # 与Publish建立一对多的关系,外键字段建立在多的一方
		    publish=models.ForeignKey(to="Publish",to_field="nid")
		 
		    # 与Author表建立多对多的关系,ManyToManyField可以建在两个模型中的任意一个，自动创建第三张表
		    authors=models.ManyToManyField(to='Author')　



> **通过logging可以查看翻译成的sql语句**

		LOGGING = {
		    'version': 1,
		    'disable_existing_loggers': False,
		    'handlers': {
		        'console':{
		            'level':'DEBUG',
		            'class':'logging.StreamHandler',
		        },
		    },
		    'loggers': {
		        'django.db.backends': {
		            'handlers': ['console'],
		            'propagate': True,
		            'level':'DEBUG',
		        },
		    }
		}　　　




> **注意事项：**

1、 表的名称myapp_modelName，是根据 模型中的元数据自动生成的，也可以覆写为别的名称　　

2、id 字段是自动添加的

3、对于外键字段，Django 会在字段名上添加"_id" 来创建数据库中的列名

4、这个例子中的CREATE TABLE SQL 语句使用PostgreSQL 语法格式，要注意的是Django 会根据settings 中指定的数据库类型来使用相应的SQL 语句。

5、定义好模型之后，你需要告诉Django _使用_这些模型。你要做的就是修改配置文件中的INSTALL_APPSZ中设置，在其中添加models.py所在应用的名称。

6、外键字段 ForeignKey 有一个 null=True 的设置(它允许外键接受空值 NULL)，你可以赋给它空值 None 。






> **字段选项**



每个字段有一些特有的参数，例如，CharField需要max_length参数来指定VARCHAR数据库字段的大小。还有一些适用于所有字段的通用参数。 这些参数在文档中有详细定义，这里我们只简单介绍一些最常用的：

	(1)null
	
		如果为True，Django 将用NULL 来在数据库中存储空值。 默认值是 False.
	
	(1)blank
	
		如果为True，该字段允许不填。默认为False。
		要注意，这与 null 不同。null纯粹是数据库范畴的，而 blank 是数据验证范畴的。
		如果一个字段的blank=True，表单的验证将允许该字段是空值。如果字段的blank=False，该字段就是必填的。
	
	(2)default
	
		字段的默认值。可以是一个值或者可调用对象。如果可调用 ，每有新对象被创建它都会被调用。
	
	(3)primary_key
	
		如果为True，那么这个字段就是模型的主键。如果你没有指定任何一个字段的primary_key=True，
		Django 就会自动添加一个IntegerField字段做为主键，所以除非你想覆盖默认的主键行为，
		否则没必要设置任何一个字段的primary_key=True。
	
	(4)unique
	
		如果该值设置为 True, 这个数据字段的值在整张表中必须是唯一的
	
	(5)choices
		由二元组组成的一个可迭代对象（例如，列表或元组），用来给字段提供选择项。 如果设置了choices ，默认的表单将是一个选择框而不是标准的文本框，而且这个选择框的选项就是choices 中的选项。
		
		这是一个关于 choices 列表的例子：
		
		YEAR_IN_SCHOOL_CHOICES = (
		    ('FR', 'Freshman'),
		    ('SO', 'Sophomore'),
		    ('JR', 'Junior'),
		    ('SR', 'Senior'),
		    ('GR', 'Graduate'),
		)
		每个元组中的第一个元素，是存储在数据库中的值；第二个元素是在管理界面或 ModelChoiceField 中用作显示的内容。 在一个给定的 model 类的实例中，想得到某个 choices 字段的显示值，就调用 get_FOO_display 方法(这里的 FOO 就是 choices 字段的名称 )。例如：
		
		from django.db import models
		
		class Person(models.Model):
		    SHIRT_SIZES = (
		        ('S', 'Small'),
		        ('M', 'Medium'),
		        ('L', 'Large'),
		    )
		    name = models.CharField(max_length=60)
		    shirt_size = models.CharField(max_length=1, choices=SHIRT_SIZES)
		
	
	>>> p = Person(name="Fred Flintstone", shirt_size="L")
	>>> p.save()
	>>> p.shirt_size
	'L'
	>>> p.get_shirt_size_display()
	'Large' 


一旦你建立好数据模型之后，django会自动生成一套数据库抽象的API，可以让你执行关于表记录的增删改查的操作。  


<br>


**3 添加表记录**

<br>


> **普通字段**

	方式1
		publish_obj=Publish(name="人民出版社",city="北京",email="renMin@163.com")
		publish_obj.save() # 将数据保存到数据库
	
	方式2 
		publish_obj是添加的记录对象
		publish_obj=Publish.objects.create(name="人民出版社",city="北京",email="renMin@163.com")<br><br>方式3<br>表.objects.create(**request.POST.dict())



> **外键字段**

	方式1:
	    publish_obj=Publish.objects.get(nid=1)
	    Book.objects.create(title="金瓶眉",publishDate="2012-12-12",price=665,pageNum=334,publish=publish_obj)
	
	 方式2:
	    Book.objects.create(title="金瓶眉",publishDate="2012-12-12",price=665,pageNum=334,publish_id=1)　　
	



> **多对多字段**

	book_obj=Book.objects.create(title="追风筝的人",publishDate="2012-11-12",price=69,pageNum=314,publish_id=1)
	
	author_yuan=Author.objects.create(name="yuan",age=23,authorDetail_id=1)
	author_egon=Author.objects.create(name="egon",age=32,authorDetail_id=2)
	
	book_obj.authors.add(author_egon,author_yuan)    #  将某个特定的 model 对象添加到被关联对象集合中。   =======    book_obj.authors.add(*[])
	
	book_obj.authors.create()      #创建并保存一个新对象，然后将这个对象加被关联对象的集合中，然后返回这个新对象。
	关键点：book_obj.authors是什么？　　
	
	解除关系：
	
		book_obj.authors.remove()     # 将某个特定的对象从被关联对象集合中去除。    ======   book_obj.authors.remove(*[])
		book_obj.authors.clear()       #清空被关联对象集合。

<br>

**增删改查操作**

<br>

> **add(obj1[, obj2, ...])**把指定的模型对象添加到关联对象集中。
	

	例如：
	
	>>> b = Blog.objects.get(id=1)
	>>> e = Entry.objects.get(id=234)
	>>> b.entry_set.add(e) # Associates Entry e with Blog b.
	在上面的例子中，对于ForeignKey关系，e.save()由关联管理器调用，执行更新操作。然而，在多对多关系中使用add()并不会调用任何 save()方法，而是由QuerySet.bulk_create()创建关系。
	
	延伸：
	
	# 1 *[]的使用
	>>> book_obj = Book.objects.get(id=1)
	>>> author_list = Author.objects.filter(id__gt=2)
	>>> book_obj.authors.add(*author_list)
	
	
	# 2 直接绑定主键
	book_obj.authors.add(*[1,3])  # 将id=1和id=3的作者对象添加到这本书的作者集合中
 	# 应用: 添加或者编辑时,提交作者信息时可以用到. 


> **create(******kwargs**)

	创建一个新的对象，保存对象，并将它添加到关联对象集之中。返回新创建的对象：
	
	>>> b = Blog.objects.get(id=1)
	>>> e = b.entry_set.create(
	...     headline='Hello',
	...     body_text='Hi',
	...     pub_date=datetime.date(2005, 1, 1)
	... )
	
	# No need to call e.save() at this point -- it's already been saved.
	这完全等价于（不过更加简洁于）：
	
	>>> b = Blog.objects.get(id=1)
	>>> e = Entry(
	...     blog=b,
	...     headline='Hello',
	...     body_text='Hi',
	...     pub_date=datetime.date(2005, 1, 1)
	... )
	>>> e.save(force_insert=True)
	要注意我们并不需要指定模型中用于定义关系的关键词参数。在上面的例子中，我们并没有传入blog参数给create()。Django会明白新的 Entry对象blog 应该添加到b中。


> **remove(obj1[, obj2, ...])**

从关联对象集中移除执行的模型对象：

	>>> b = Blog.objects.get(id=1)
	>>> e = Entry.objects.get(id=234)
	>>> b.entry_set.remove(e) # Disassociates Entry e from Blog b.
	对于ForeignKey对象，这个方法仅在null=True时存在。


> **clear()**

	从关联对象集中移除一切对象。
	
	>>> b = Blog.objects.get(id=1)
	>>> b.entry_set.clear()
	注意这样不会删除对象 —— 只会删除他们之间的关联。
	
	就像 remove() 方法一样，clear()只能在 null=True的ForeignKey上被调用。



> **set()方法**

先清空，在设置，编辑书籍时即可用到



> **注意：**
> 
> **对于所有类型的关联字段，add()、create()、remove()和clear(),set()都会马上更新数据库。换句话说，在关联的任何一端，都不需要再调用save()方法**

<br>

> **直接赋值：**

通过赋值一个新的可迭代的对象，关联对象集可以被整体替换掉。

	>>> new_list = [obj1, obj2, obj3]
	>>> e.related_set = new_list
	如果外键关系满足null=True，关联管理器会在添加new_list中的内容之前，首先调用clear()方法来解除关联集中一切已存在对象的关联。否则， new_list中的对象会在已存在的关联的基础上被添加。



<br>


**4 查询表记录**

<br>




- **查询相关API**

		<1> all():                 查询所有结果
		
		 <2> filter(**kwargs):      它包含了与所给筛选条件相匹配的对象
		
		 <3> get(**kwargs):         返回与所给筛选条件相匹配的对象，返回结果有且只有一个，
		                            如果符合筛选条件的对象超过一个或者没有都会抛出错误。
		
		 <5> exclude(**kwargs):     它包含了与所给筛选条件不匹配的对象
		
		 <4> values(*field):        返回一个ValueQuerySet——一个特殊的QuerySet，运行后得到的并不是一系列
		                            model的实例化对象，而是一个可迭代的字典序列
		
		 <9> values_list(*field):   它与values()非常相似，它返回的是一个元组序列，values返回的是一个字典序列
		
		 <6> order_by(*field):      对查询结果排序
		
		 <7> reverse():             对查询结果反向排序
		
		 <8> distinct():            从返回结果中剔除重复纪录
		
		 <10> count():              返回数据库中匹配查询(QuerySet)的对象数量。
		
		 <11> first():              返回第一条记录
		
		 <12> last():               返回最后一条记录
		
		 <13> exists():             如果QuerySet包含数据，就返回True，否则返回False
	
	
	
	**注意：一定区分object与querySet的区别**
	
	
<br>
	
	
- **双下划线之单表查询**
	

		models.Tb1.objects.filter(id__lt=10, id__gt=1)   # 获取id大于1 且 小于10的值
		
		models.Tb1.objects.filter(id__in=[11, 22, 33])   # 获取id等于11、22、33的数据
		models.Tb1.objects.exclude(id__in=[11, 22, 33])  # not in
		
		models.Tb1.objects.filter(name__contains="ven")
		models.Tb1.objects.filter(name__icontains="ven") # icontains大小写不敏感
		
		models.Tb1.objects.filter(id__range=[1, 2])      # 范围bettwen and
		
		startswith，istartswith, endswith, iendswith　
		

	<br>
	
	
- **基于对象的跨表查询**


<br>

**一对多查询（Publish 与 Book）**

<br>

> **正向查询(按字段：publish)：**

	# 查询nid=1的书籍的出版社所在的城市
	
	book_obj=Book.objects.get(nid=1)
	print(book_obj.publish.city) # book_obj.publish 是nid=1的书籍对象关联的出版社对象　　

> **反向查询(按表名：book_set)：**

	# 查询 人民出版社出版过的所有书籍	
	publish=Publish.objects.get(name="人民出版社")	

	book_list=publish.book_set.all()  	# 与人民出版社关联的所有书籍对象集合
	
	for book_obj in book_list:
		print(book_obj.title)

<br>

**一对一查询(Author 与 AuthorDetail)**


> **正向查询(按字段：authorDetail)：**

	# 查询egon作者的手机号
	
	    author_egon=Author.objects.get(name="egon")
	    print(author_egon.authorDetail.telephone)

> **反向查询(按表名：author)：**

	# 查询所有住址在北京的作者的姓名
	
	    authorDetail_list=AuthorDetail.objects.filter(addr="beijing")
	
	    for obj in authorDetail_list:
	        print(obj.author.name)

<br>

**多对多查询 (Author 与 Book)**


> **正向查询(按字段：authors)：**

	# 金瓶眉所有作者的名字以及手机号
	
	    book_obj=Book.objects.filter(title="金瓶眉").first()
	
	    authors=book_obj.authors.all()
	
	    for author_obj in authors:
	
	        print(author_obj.name,author_obj.authorDetail.telephone)


> **反向查询(按表名：book_set)：**

	# 查询egon出过的所有书籍的名字
	
	    author_obj=Author.objects.get(name="egon")
	    book_list=author_obj.book_set.all() #与egon作者相关的所有书籍
	
	    for book_obj in book_list:
	        print(book_obj.title)


<br>

> **注意：related_name可以重写反向查询的关键字**

你可以通过在 ForeignKey() 和 ManyToManyField的定义中设置 **related_name** 的值来覆写 FOO_set 的名称。例如，如果 Article model 中做一下更改： **publish = ForeignKey(Blog, related_name='bookList')**，那么接下来就会如我们看到这般：
	

	 # 查询 人民出版社出版过的所有书籍
	
	    publish=Publish.objects.get(name="人民出版社")
	
	    book_list=publish.bookList.all()  # 与人民出版社关联的所有书籍对象集合

<br>

- **基于双下划线的 跨表查询**

Django 还提供了一种直观而高效的方式在查询(lookups)中表示关联关系，它能自动确认 SQL JOIN 联系。要做跨关系查询，就使用两个下划线来链接模型(model)间关联字段的名称，直到最终链接到你想要的 model 为止。


> **关键点：正向查询按字段，反向查询按表名。**


	# 练习1:  查询人民出版社出版过的所有书籍的名字与价格(一对多)
	
	    # 正向查询 按字段:publish
	
	    queryResult=Book.objects
	　　　　　　　　　　　　.filter(publish__name="人民出版社")
	　　　　　　　　　　　　.values_list("title","price")
	
	    # 反向查询 按表名:book
	
	    queryResult=Publish.objects
	　　　　　　　　　　　　　　.filter(name="人民出版社")
	　　　　　　　　　　　　　　.values_list("book__title","book__price")
	
	
	
	# 练习2: 查询egon出过的所有书籍的名字(多对多)
	
	    # 正向查询 按字段:authors:
	    queryResult=Book.objects
	　　　　　　　　　　　　.filter(authors__name="yuan")
	　　　　　　　　　　　　.values_list("title")
	
	    # 反向查询 按表名:book
	    queryResult=Author.objects
	　　　　　　　　　　　　　　.filter(name="yuan")
	　　　　　　　　　　　　　　.values_list("book__title","book__price")
	
	
	# 练习3: 查询人民出版社出版过的所有书籍的名字以及作者的姓名
	
	
	    # 正向查询
	    queryResult=Book.objects
	　　　　　　　　　　　　.filter(publish__name="人民出版社")
	　　　　　　　　　　　　.values_list("title","authors__name")
	    # 反向查询
	    queryResult=Publish.objects
	　　　　　　　　　　　　　　.filter(name="人民出版社")
	　　　　　　　　　　　　　　.values_list("book__title","book__authors__age","book__authors__name")
	
	
	# 练习4: 手机号以151开头的作者出版过的所有书籍名称以及出版社名称
	
	    queryResult=Book.objects
	　　　　　　　　　　　　.filter(authors__authorDetail__telephone__regex="151")
	　　　　　　　　　　　　.values_list("title","publish__name")



> **注意：related_name**

反向查询时，如果定义了**related_name ，则用related_name替换表名**，例如： publish = ForeignKey(Blog, related_name='bookList')：


	# 练习1:  查询人民出版社出版过的所有书籍的名字与价格(一对多)
	
	    # 反向查询 不再按表名:book,而是related_name:bookList
	
	    queryResult=Publish.objects
	　　　　　　　　　　　　　　.filter(name="人民出版社")
	　　　　　　　　　　　　　　.values_list("bookList__title","bookList__price")


<br>

- **聚合查询与分组查询**


<br>

> **聚合：aggregate()**

aggregate()是QuerySet 的一个终止子句，意思是说，它返回一个包含一些键值对的字典。键的名称是聚合值的标识符，值是计算出来的聚合值。键的名称是按照字段和聚合函数的名称自动生成出来的。如果你想要为聚合值指定一个名称，可以向聚合子句提供它。


	>>> Book.objects.aggregate(average_price=Avg('price'))
	{'average_price': 34.35}

	>>> from django.db.models import Avg, Max, Min
	>>> Book.objects.aggregate(Avg('price'), Max('price'), Min('price'))
	{'price__avg': 34.35, 'price__max': Decimal('81.20'), 'price__min': Decimal('12.99')}


<br>


> **分组：annotate()**　



	(1) 练习：统计每一本书的作者个数
	
	    bookList=Book.objects.annotate(authorsNum=Count('authors'))
	    for book_obj in bookList:
	        print(book_obj.title,book_obj.authorsNum)


	(2) 练习：统计每一个出版社的最便宜的书
	
	    publishList=Publish.objects.annotate(MinPrice=Min("book__price"))
	
	    for publish_obj in publishList:
	        print(publish_obj.name,publish_obj.MinPrice)

	(3) 统计每一本以py开头的书籍的作者个数：
	
	 queryResult=Book.objects
	　　　　　　　　　　 .filter(title__startswith="Py")
	　　　　　　　　　 　.annotate(num_authors=Count('authors'))
	
	
	(4) 统计不止一个作者的图书：
	
	queryResult=Book.objects
	　　　　　　　　　　.annotate(num_authors=Count('authors'))
	　　　　　　　　　　.filter(num_authors__gt=1)

	(5) 根据一本图书作者数量的多少对查询集 QuerySet进行排序:
	
	Book.objects.annotate(num_authors=Count('authors')).order_by('num_authors')


	(6) 查询各个作者出的书的总价格:


		# 按author表的所有字段 group by
		    queryResult=Author.objects
		　　　　　　　　　　　　　　.annotate(SumPrice=Sum("book__price"))
		　　　　　　　　　　　　　　.values_list("name","SumPrice")
		    print(queryResult)
		    
		# 按authors__name group by
		    queryResult2=Book.objects.values("authors__name")
		　　　　　　　　　　　　　　.annotate(SumPrice=Sum("price"))
		　　　　　　　　　　　　　　.values_list("authors__name","SumPrice")
		    print(queryResult2)


<br>


- **F查询与Q查询**

<br>

> **F查询**

对两个字段的值做比较

Django 提供 F() 来做这样的比较。F() 的实例可以在查询中引用字段，来比较同一个 model 实例中两个不同字段的值。


	Django 支持 F() 对象之间以及 F() 对象和常数之间的加减乘除和取模的操作。
	
	# 查询评论数大于收藏数2倍的书籍
	Book.objects.filter(commnetNum__lt=F('keepNum')*2)


	修改操作也可以使用F函数,比如将每一本书的价格提高30元：
	
	Book.objects.all().update(price=F("price")+30)　

<br>


> **Q查询**

执行更复杂的查询（例如 or语句），你可以使用Q对象

	from django.db.models import Q

	Q 对象可以使用& 和| 操作符组合起来。当一个操作符在两个Q 对象上使用时，它产生一个新的Q 对象。
	
	bookList=Book.objects.filter(Q(authors__name="yuan")|Q(authors__name="egon"))



<br>

- **extra**
	

有些情况下，Django的查询语法难以简单的表达复杂的 WHERE 子句，对于这种情况, Django 提供了 extra() QuerySet修改机制 — 它能在 QuerySet生成的SQL从句中注入新子句

		extra(select=None, where=None, params=None, 
		      tables=None, order_by=None, select_params=None)

extra可以指定一个或多个 参数,例如 select, where or tables. 这些参数都不是必须的，但是你至少要使用一个!要注意这些额外的方式对不同的数据库引擎可能存在移植性问题.(因为你在显式的书写SQL语句),除非万不得已,尽量避免这样做


<br>

> **参数之select**

The select 参数可以让你在 SELECT 从句中添加其他字段信息，它应该是一个字典，存放着属性名到 SQL 从句的映射。

	queryResult=models.Article
	　　　　　　　　　　　.objects.extra(select={'is_recent': "create_time > '2017-09-05'"})

结果集中每个 Entry 对象都有一个额外的属性is_recent, 它是一个布尔值，表示 Article对象的create_time 是否晚于2017-09-05.

	
	# in sqlite:
	    article_obj=models.Article.objects
	　　　　　　　　　　　　　　.filter(nid=1)
	　　　　　　　　　　　　　　.extra(select={"standard_time":"strftime('%%Y-%%m-%%d',create_time)"})
	　　　　　　　　　　　　　　.values("standard_time","nid","title")
	    print(article_obj)
	    # <QuerySet [{'title': 'MongoDb 入门教程', 'standard_time': '2017-09-03', 'nid': 1}]>


> **参数之where / tables**

您可以使用where定义显式SQL WHERE子句 - 也许执行非显式连接。您可以使用tables手动将表添加到SQL FROM子句。

where和tables都接受字符串列表。所有where参数均为“与”任何其他搜索条件。


	
	queryResult=models.Article
	　　　　　　　　　　　.objects.extra(where=['nid in (1,3) OR title like "py%" ','nid>2'])

	 





**5 修改表记录**

	
	a. 方法一

		author = Author.objects.get(id=5)
		author.name = 'amy'
		author.save()

	b. 方法二
	
		author.objects.filter(id=5).update(name='amy') # 返回的是一个布尔值


update是QuerySet对象的方法，get返回的是一个model对象，它没有update方法，而filter返回的是一个QuerySet对象(filter里面的条件可能有多个条件符合，比如name＝'alvin',可能有两个name＝'alvin'的行数据)。


<br>

**6 删除表记录**

删除方法就是 delete()。它运行时立即删除对象而不返回任何值。

你也可以一次性删除多个对象。每个 QuerySet 都有一个 delete() 方法，它一次性删除 QuerySet 中所有的对象。

例如，下面的代码将删除 pub_date 是2005年的 Entry 对象：

	Entry.objects.filter(pub_date__year=2005).delete()


**在 Django 删除对象时，会模仿 SQL 约束 ON DELETE CASCADE 的行为，换句话说，删除一个对象时也会删除与它相关联的外键对象**。例如：


	如果不想级联删除，可以设置为：
	
	pubHouse = models.ForeignKey(to='Publisher', on_delete=models.SET_NULL, blank=True, null=True)



