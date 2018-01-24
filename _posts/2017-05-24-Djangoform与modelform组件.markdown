---
layout: post
title:  "Django--form组件与ModelForm"
description:  form组件与ModelForm
date:   2017-05-24 11:55:52 +0200
categories: jekyll update
---


# Django--form组件与ModelForm
-----

<br>

> **Django的Form主要功能：**
> 
> 生成HTML标签;
> 
> 验证用户数据（显示错误信息）;
> 
> HTML Form提交保留上次提交数据;
> 
> 初始化页面显示内容;
> 
> 方便地限制字段条件;

<br>

## 1 基本使用

<br>

**(1) 创建Form类**
	
	from django.forms import Form
	from django.forms import widgets
	from django.forms import fields
	 
	class MyForm(Form):
	    user = fields.CharField(
	        widget=widgets.TextInput(attrs={'id': 'i1', 'class': 'c1'})
	    )
	 
	    gender = fields.ChoiceField(
	        choices=((1, '男'), (2, '女'),),
	        initial=2,
	        widget=widgets.RadioSelect
	    )
	 
	    city = fields.CharField(
	        initial=2,
	        widget=widgets.Select(choices=((1,'上海'),(2,'北京'),))
	    )
	 
	    pwd = fields.CharField(
	        widget=widgets.PasswordInput(attrs={'class': 'c1'}, render_value=True)
	    )

<br>

**(2) View函数处理**
	
	from django.shortcuts import render, redirect
	from .forms import MyForm
	 
	 
	def index(request):
	    if request.method == "GET":
	        obj = MyForm()
	        return render(request, 'index.html', {'form': obj})
	    elif request.method == "POST":
	        obj = MyForm(request.POST, request.FILES)
	        if obj.is_valid():
	            values = obj.clean()
	            print(values)
	        else:
	            errors = obj.errors
	            print(errors)
	        return render(request, 'index.html', {'form': obj})
	    else:
	        return redirect('http://www.google.com')

<br>

**（3）生成HTML**

	<form action="/" method="POST" enctype="multipart/form-data">
	    <p>{{ form.user }} {{ form.user.errors }}</p>
	    <p>{{ form.gender }} {{ form.gender.errors }}</p>
	    <p>{{ form.city }} {{ form.city.errors }}</p>
	    <p>{{ form.pwd }} {{ form.pwd.errors }}</p>
	    <input type="submit"/>
	</form>

<br>

	创建 form 类
	
	    class LoginForm(forms.Form):
	        username=forms.CharField(min_length=5,max_length=12)           # 类型
	        password=forms.CharField()
	
	实例化form对象---传给模板渲染
	
	    myform=LoginForm()
	    return render(request,'login.html',locals())
	
	    <p>姓名{{ myform.username }}</p>
	    <p>密码{{ myform.password }}</p>
	
	服务端 form 数据 校验
	
	    if request.method == "POST":
	        checkform=LoginForm(request.POST) # 将数据传给对应的字段
	
	        if checkform.is_valid():  # 检验 --成功的字段>self.cleaned_data={"user":""}
	                                         # 失败的字段 self.errors={"user":""}
	            print(checkform.cleaned_data)
	        else:
	            print(checkform.errors)
	
	
	

<br>

## 2 Form 类详解

<br>


> 创建Form类时，主要涉及到 【字段】 和 【插件】，还有绑定的和未绑定的表单实例，字段用于对用户请求数据的验证，插件用于自动生成HTML。

<br>

**(1) 绑定的和未绑定的表单实例**

> 绑定的和未绑定的表单的区别：

未绑定的表单没有关联的数据。当渲染给用户时，它将为空或包含默认的值。
 
绑定的表单具有提交的数据，因此可以用来检验数据是否合法。如果渲染一个不合法的绑定的表单，它将包含内联的错误信息，告诉用户如何纠正数据。


**（2）Django内置字段**

	
	Field
	    required=True,               是否允许为空
	    widget=None,                 HTML插件
	    label=None,                  用于生成Label标签或显示内容
	    initial=None,                初始值
	    help_text='',                帮助信息(在标签旁边显示)
	    error_messages=None,         错误信息 {'required': '不能为空', 'invalid': '格式错误'}
	    show_hidden_initial=False,   是否在当前插件后面再加一个隐藏的且具有默认值的插件（可用于检验两次输入是否一直）
	    validators=[],               自定义验证规则
	    localize=False,              是否支持本地化
	    disabled=False,              是否可以编辑
	    label_suffix=None            Label内容后缀
	 
	 
	CharField(Field)
	    max_length=None,             最大长度
	    min_length=None,             最小长度
	    strip=True                   是否移除用户输入空白
	 
	IntegerField(Field)
	    max_value=None,              最大值
	    min_value=None,              最小值
	 
	FloatField(IntegerField)
	    ...
	 
	DecimalField(IntegerField)
	    max_value=None,              最大值
	    min_value=None,              最小值
	    max_digits=None,             总长度
	    decimal_places=None,         小数位长度
	 
	BaseTemporalField(Field)
	    input_formats=None          时间格式化   
	 
	DateField(BaseTemporalField)    格式：2015-09-01
	TimeField(BaseTemporalField)    格式：11:12
	DateTimeField(BaseTemporalField)格式：2015-09-01 11:12
	 
	DurationField(Field)            时间间隔：%d %H:%M:%S.%f
	    ...
	 
	RegexField(CharField)
	    regex,                      自定制正则表达式
	    max_length=None,            最大长度
	    min_length=None,            最小长度
	    error_message=None,         忽略，错误信息使用 error_messages={'invalid': '...'}
	 
	EmailField(CharField)      
	    ...
	 
	FileField(Field)
	    allow_empty_file=False     是否允许空文件
	 
	ImageField(FileField)      
	    ...
	    注：需要PIL模块，pip3 install Pillow
	    以上两个字典使用时，需要注意两点：
	        - form表单中 enctype="multipart/form-data"
	        - view函数中 obj = MyForm(request.POST, request.FILES)
	 
	URLField(Field)
	    ...
	 
	 
	BooleanField(Field)  
	    ...
	 
	NullBooleanField(BooleanField)
	    ...
	 
	ChoiceField(Field)
	    ...
	    choices=(),                选项，如：choices = ((0,'上海'),(1,'北京'),)
	    required=True,             是否必填
	    widget=None,               插件，默认select插件
	    label=None,                Label内容
	    initial=None,              初始值
	    help_text='',              帮助提示
	 
	 
	ModelChoiceField(ChoiceField)
	    ...                        django.forms.models.ModelChoiceField
	    queryset,                  # 查询数据库中的数据
	    empty_label="---------",   # 默认空显示内容
	    to_field_name=None,        # HTML中value的值对应的字段
	    limit_choices_to=None      # ModelForm中对queryset二次筛选
	     
	ModelMultipleChoiceField(ModelChoiceField)
	    ...                        django.forms.models.ModelMultipleChoiceField
	 
	 
	     
	TypedChoiceField(ChoiceField)
	    coerce = lambda val: val   对选中的值进行一次转换
	    empty_value= ''            空值的默认值
	 
	MultipleChoiceField(ChoiceField)
	    ...
	 
	TypedMultipleChoiceField(MultipleChoiceField)
	    coerce = lambda val: val   对选中的每一个值进行一次转换
	    empty_value= ''            空值的默认值
	 
	ComboField(Field)
	    fields=()                  使用多个验证，如下：即验证最大长度20，又验证邮箱格式
	                               fields.ComboField(fields=[fields.CharField(max_length=20), fields.EmailField(),])
	 
	MultiValueField(Field)
	    PS: 抽象类，子类中可以实现聚合多个字典去匹配一个值，要配合MultiWidget使用
	 
	SplitDateTimeField(MultiValueField)
	    input_date_formats=None,   格式列表：['%Y--%m--%d', '%m%d/%Y', '%m/%d/%y']
	    input_time_formats=None    格式列表：['%H:%M:%S', '%H:%M:%S.%f', '%H:%M']
	 
	FilePathField(ChoiceField)     文件选项，目录下文件显示在页面中
	    path,                      文件夹路径
	    match=None,                正则匹配
	    recursive=False,           递归下面的文件夹
	    allow_files=True,          允许文件
	    allow_folders=False,       允许文件夹
	    required=True,
	    widget=None,
	    label=None,
	    initial=None,
	    help_text=''
	 
	GenericIPAddressField
	    protocol='both',           both,ipv4,ipv6支持的IP格式
	    unpack_ipv4=False          解析ipv4地址，如果是::ffff:192.0.2.1时候，可解析为192.0.2.1， PS：protocol必须为both才能启用
	 
	SlugField(CharField)           数字，字母，下划线，减号（连字符）
	    ...
	 
	UUIDField(CharField)           uuid类型

    UUID是根据MAC以及当前时间等创建的不重复的随机字符串



**（3）Django内置插件**

	
	TextInput(Input)
	NumberInput(TextInput)
	EmailInput(TextInput)
	URLInput(TextInput)
	PasswordInput(TextInput)
	HiddenInput(TextInput)
	Textarea(Widget)
	DateInput(DateTimeBaseInput)
	DateTimeInput(DateTimeBaseInput)
	TimeInput(DateTimeBaseInput)
	CheckboxInput
	Select
	NullBooleanSelect
	SelectMultiple
	RadioSelect
	CheckboxSelectMultiple
	FileInput
	ClearableFileInput
	MultipleHiddenInput
	SplitDateTimeWidget
	SplitHiddenDateTimeWidget
	SelectDateWidget


**（4）常用的选择插件**

	
	单radio，值为字符串
	user = fields.CharField(
	    initial=2,
	    widget=widgets.RadioSelect(choices=((1,'上海'),(2,'北京'),))
	)
	
	单radio，值为字符串
	user = fields.ChoiceField(
	    choices=((1, '上海'), (2, '北京'),),
	    initial=2,
	    widget=widgets.RadioSelect
	)
	
	单select，值为字符串
	user = fields.CharField(
	    initial=2,
	    widget=widgets.Select(choices=((1,'上海'),(2,'北京'),))
	)
	
	单select，值为字符串
	user = fields.ChoiceField(
	    choices=((1, '上海'), (2, '北京'),),
	    initial=2,
	    widget=widgets.Select
	)
	
	多选select，值为列表
	user = fields.MultipleChoiceField(
	    choices=((1,'上海'),(2,'北京'),),
	    initial=[1,],
	    widget=widgets.SelectMultiple
	)
	
	
	单checkbox
	user = fields.CharField(
	    widget=widgets.CheckboxInput()
	)
	
	
	多选checkbox,值为列表
	user = fields.MultipleChoiceField(
	    initial=[2, ],
	    choices=((1, '上海'), (2, '北京'),),
	    widget=widgets.CheckboxSelectMultiple
	)


**(5) Widgets**

每个表单字段都有一个对应的Widget 类，它对应一个HTML，
在大部分情况下，字段都具有一个合理的默认Widget。例如，默认情况下，CharField 具有一个TextInput Widget，它在HTML 中生成一个

		<input type="text">。


**(6) Form.cleaned_data**



不管表单提交的是什么数据，一旦通过调用is_valid() 成功验证（is_valid() 返回True），验证后的表单数据将位于form.cleaned_data 字典中。这些数据已经为你转换好为Python 的类型。
	
	IntegerField 和FloatField 字段分别将值转换为Python 的int 和float。

**(7) Form.errors**

验证失败的数据会存在 form.errors 字典中



**(8) form组件的钩子：自定义验证规则**

> 局部钩子


    def clean_user(self):
        var=self.cleaned_data.get("user")
        if  re.match(r"\w+$",var):
            if not User.objects.filter(username=var):
                return self.cleaned_data.get("user")
            else:
                raise ValidationError("用户已经注册")
        else:
            raise ValidationError("用户含有非法字符")

> 全局钩子


    def clean(self):
        if self.cleaned_data.get("pwd")==self.cleaned_data.get("repwd"):
            return self.cleaned_data
        else:
            raise ValidationError("两次密码不一致")


按子段顺序一一校验，即校验到username时，你无法使用self.cleaned_data.get("password")。	

而局部钩子使用完，到全局时，已经可以使用所有的self.cleaned_data


**（9）其他自定义验证规则的方法**


> **重新定义validator**

from django.core.validators import RegexValidator
	
	from django.forms import Form
	from django.forms import widgets
	from django.forms import fields
	from django.core.validators import RegexValidator
	 
	class MyForm(Form):
	    user = fields.CharField(
	        validators=[RegexValidator(r'^[0-9]+$', '请输入数字'), RegexValidator(r'^159[0-9]+$', '数字必须以159开头')],
	    )


> **自定义validator**
	
	import re
	from django.forms import Form
	from django.forms import widgets
	from django.forms import fields
	from django.core.exceptions import ValidationError
	 
	 
	# 自定义验证规则
	def mobile_validate(value):
	    mobile_re = re.compile(r'^(13[0-9]|15[012356789]|17[678]|18[0-9]|14[57])[0-9]{8}$')
	    if not mobile_re.match(value):
	        raise ValidationError('手机号码格式错误')
	 
	 
	class PublishForm(Form):
	 
	 
	    # 使用自定义验证规则
	    phone = fields.CharField(validators=[mobile_validate, ],
	                            error_messages={'required': '手机不能为空'},
	                            widget=widgets.TextInput(attrs={'class': "form-control",
	                                                          'placeholder': u'手机号码'}))



<br>

## 3 模板中 form 的渲染

<br>


> **按格式渲染**

	
	{{ form.as_table }} 以表格的形式将它们渲染在<tr> 标签中
	{{ form.as_p }} 将它们渲染在<p> 标签中
	{{ form.as_ul }} 将它们渲染在<li> 标签中
	注意，你必须自己提供<table> 或<ul> 元素。
	

> **按form的字段渲染**

	<div class="fieldWrapper">
	    {{ form.Username.errors }}
	    {{ form.Username.label_tag }}
	    {{ form.Username }}
	</div>


> **表单错误信息的渲染**
	
	registerForm=RegisterForm(request.POST)
	print(type(registerForm.errors))                      #<class 'django.forms.utils.ErrorDict'>
	print(type(registerForm.errors["username"]))          #<class 'django.forms.utils.ErrorList'>


<br>


**例子**

<br>

**myform** 

	from django import forms
	from django.forms import widgets
	from django.core.exceptions import ValidationError
	import re
	from django.contrib.auth.models import User
	class MyForm(forms.Form):
	    user=forms.CharField(max_length=12,
	                         min_length=5,
	                         error_messages={
	                             "required":"内容不能为空",
	                             "min_length":"不能少于5个字符",
	                             "max_length":"不能超过12个字符",
	                         },
	                         widget=widgets.TextInput(attrs={"class":"form-control"})
	                         )
	    email=forms.EmailField(error_messages={
	        "required":"内容不能为空",
	        "invalid":"格式必须是邮箱",
	    },
	        widget=widgets.TextInput(attrs={"class": "form-control"})
	    )
	    pwd=forms.CharField(
	        max_length=8,
	        min_length=8,
	        error_messages={
	            "required":"内容不能为空",
	            "min_length":"请输入8位密码",
	            "max_length":"请输入8位密码",
	        },
	        widget=widgets.PasswordInput(attrs={"class": "form-control"})
	    )
	    repwd=forms.CharField(
	        max_length=8,
	        min_length=8,
	        error_messages={
	            "required": "内容不能为空",
	            "min_length": "请输入8位密码",
	            "max_length": "请输入8位密码",
	        },
	        widget=widgets.PasswordInput(attrs={"class": "form-control"})
	    )
	
	    gender=forms.CharField(
	        widget=widgets.Select(choices=((1,"男"),(0,"女")),attrs={"class": "form-control"})
	    )
	
	    def clean_pwd(self):
	        var=self.cleaned_data.get("pwd")
	        if not var.isdigit() and not var.isalpha():
	            return self.cleaned_data.get("pwd")
	        else:
	            raise ValidationError("密码不能是纯字母或者是数字")
	
	    def clean_repwd(self):
	        var=self.cleaned_data.get("repwd")
	        if not var.isdigit() and not var.isalpha():
	            return self.cleaned_data.get("repwd")
	        else:
	            raise ValidationError("密码不能是纯字母或者是数字")
	
	    def clean_user(self):
	        var=self.cleaned_data.get("user")
	        if  re.match(r"\w+$",var):
	            if not User.objects.filter(username=var):
	                return self.cleaned_data.get("user")
	            else:
	                raise ValidationError("用户已经注册")
	        else:
	            raise ValidationError("用户含有非法字符")
	
	
	    def clean(self):
	        if self.cleaned_data.get("pwd")==self.cleaned_data.get("repwd"):
	            return self.cleaned_data
	        else:
	            raise ValidationError("两次密码不一致")

**views**
	
	from django.shortcuts import render,HttpResponse,redirect
	from appform.myform import MyForm
	from django.contrib.auth.models import User
	def reg(request):
	    if request.method == "POST":
	        myform=MyForm(request.POST)
	        if myform.is_valid():
	            print(myform.cleaned_data)
	            user=request.POST.get("user")
	            pwd=request.POST.get("pwd")
	            email=request.POST.get("eamil")
	            User.objects.create_user(username=user,password=pwd,email=email)
	            return redirect('/reg/')
	        else:
	            errors=myform.errors
	            all_error=myform.errors.get("__all__")
	            return render(request, 'reg.html', {"myform": myform, "errors":errors, "all_error":all_error})
	    myform=MyForm()
	    return render(request, 'reg.html', {"myform":myform})

**html**
	
	<form action="" method="post" class="col-md-8">
	    {% csrf_token %}
	    <p ><strong>姓名</strong>{{ myform.user }} <div class="has-error"><span class="help-block ">{{ errors.user.0 }}</span> </div></p>
	    <p><strong>邮箱</strong>{{ myform.email }}  <div class="has-error"><span class="help-block ">{{ errors.email.0 }}</span></div></p>
	    <p><strong>密码</strong>{{ myform.pwd }} <div class="has-error"> <span class="help-block ">{{ errors.pwd.0 }}</span></div> </p>
	    <p><strong>确认密码</strong>{{ myform.repwd }} <div class="has-error"><span class="help-block ">{{ errors.repwd.0 }}</span> </div></p>
	    <p class="has-error"><span class="help-block ">{{ all_error.0 }}</span></p>
	    <p><strong>性别</strong>{{ myform.gender }}</p>
	    <p><input type="submit" class="form-control btn btn-primary" value="提交"></p>
	</form>


<br>

## 4 ModelForm

<br>
modelForm将models与forms结合使用，modelForm组件本质还是一个form组件，继承了BaseForm类，作为model和form的结合体，自然具有以下功能：

> form --- 验证
> 
> model --- 数据库操作

缺点：form与model的耦合性太高

<br>

**ModelForm 基本操作**


> **forms.py**

	class BookModelForm(forms.ModelForm):
	
	    class Meta:
	        model = models.Book    # 与models建立关系
	        fields = "__all__"     # 显示所有字段




**自定制字段名**

方法一： 定义model中的verbose_name='书名'

	class Book(models.Model):
	    title = models.CharField(max_length=32, verbose_name='书名')


方法二： labels={'title':'书名'}

	class BookModelForm(forms.ModelForm):
	
	    class Meta:
	        model = models.UserInfo
	        fields = "__all__"
	        labels = {
	            'title':'书名',
	        }

**展示指定的列**

fields = "__ all __"上面展示所有的字段
	
fields = ['title','author']   # 显示指定列

exclude = ['publisher']       # 排除指定列



**其他参数介绍**   



	class BookModelForm(forms.ModelForm):
	
	    class Meta:
	        model = models.Book    # 与models建立关系
	        fields = "__all__"     # 显示所有字段
			exclude=['title',]     # 排除title字段
			labels={"title":"书名","author":"作者"} #显示提示信息
	  		widgets={"title":wgts.TextInput(attrs={"class":"form-control"})},  # 自定义插件
            error_messages=None,   #错误信息 

> 当然 modelform 也支持 钩子


**modelform的数据库操作**

> **保存**

	bookform = BookModelForm(request.POST)
	
	if bookform.is_valid():

		bookform.save()


> **修改**

	instance = Book.objects.filter(...).first()
	
	bookform = BookModelForm(request.POST,instance=instance)
	
	if bookform.is_valid():
	
			bookform.save()


**template渲染的循环遍历方法**

	 {% for foo in book_form %}
        <label for="">{{ foo.label }}</label>
            <p>{{ foo }}</p>
     {% endfor %}
