---
layout: post
title:  "python 异常处理"
description: python 异常处理
date:   2017-03-18 14:55:52 +0200
categories: jekyll update
---




# python 异常处理

-----------
**python程序在运行中出现的异常和错误的处理**


> **异常处理**
> 
> **断言**


<br>

## 1 异常处理

**在Python无法正常处理程序时就会发生一个异常，Python脚本发生异常时我们需要捕获处理它，否则程序会终止执行**

> **异常处理的特点：** 一旦发生异常，程序就不再执行
> 处理异常的时候 报错的在最下边

<br>

- **python 常见的异常：**

<table border="1">
<tbody><tr>
<th>
异常名称</th><th> 描述</th>
</tr><tr>
</tr><tr><td>
BaseException</td><td> 所有异常的基类</td></tr><tr><td>
SystemExit</td><td>解释器请求退出</td></tr><tr><td>
KeyboardInterrupt</td><td> 用户中断执行(通常是输入^C)</td></tr><tr><td>
Exception</td><td>常规错误的基类</td></tr><tr><td>
StopIteration </td><td>迭代器没有更多的值</td></tr><tr><td>
GeneratorExit </td><td>生成器(generator)发生异常来通知退出</td></tr><tr><td>
StandardError</td><td> 所有的内建标准异常的基类</td></tr><tr><td>
ArithmeticError </td><td>所有数值计算错误的基类</td></tr><tr><td>
FloatingPointError </td><td>浮点计算错误</td></tr><tr><td>
OverflowError</td><td> 数值运算超出最大限制</td></tr><tr><td>
ZeroDivisionError</td><td> 除(或取模)零 (所有数据类型)</td></tr><tr><td>
AssertionError</td><td> 断言语句失败</td></tr><tr><td>
AttributeError</td><td> 对象没有这个属性</td></tr><tr><td>
EOFError </td><td>没有内建输入,到达EOF 标记</td></tr><tr><td>
EnvironmentError </td><td>操作系统错误的基类</td></tr><tr><td>
IOError </td><td>输入/输出操作失败</td></tr><tr><td>
OSError </td><td>操作系统错误</td></tr><tr><td>
WindowsError</td><td> 系统调用失败</td></tr><tr><td>
ImportError </td><td>导入模块/对象失败</td></tr><tr><td>
LookupError </td><td>无效数据查询的基类</td></tr><tr><td>
IndexError</td><td> 序列中没有此索引(index)</td></tr><tr><td>
KeyError</td><td> 映射中没有这个键</td></tr><tr><td>
MemoryError</td><td> 内存溢出错误(对于Python 解释器不是致命的)</td></tr><tr><td>
NameError</td><td> 未声明/初始化对象 (没有属性)</td></tr><tr><td>
UnboundLocalError </td><td>访问未初始化的本地变量</td></tr><tr><td>
ReferenceError</td><td> 弱引用(Weak reference)试图访问已经垃圾回收了的对象</td></tr><tr><td>
RuntimeError </td><td>一般的运行时错误</td></tr><tr><td>
NotImplementedError</td><td> 尚未实现的方法</td></tr><tr><td>
SyntaxError</td><td>Python 语法错误</td></tr><tr><td>
IndentationError</td><td> 缩进错误</td></tr><tr><td>
TabError</td><td> Tab 和空格混用</td></tr><tr><td>
SystemError </td><td>一般的解释器系统错误</td></tr><tr><td>
TypeError</td><td> 对类型无效的操作</td></tr><tr><td>
ValueError </td><td>传入无效的参数</td></tr><tr><td>
UnicodeError </td><td>Unicode 相关的错误</td></tr><tr><td>
UnicodeDecodeError </td><td>Unicode 解码时的错误</td></tr><tr><td>
UnicodeEncodeError </td><td>Unicode 编码时错误</td></tr><tr><td>

UnicodeTranslateError</td><td> Unicode 转换时错误</td></tr><tr><td>
Warning </td><td>警告的基类</td></tr><tr><td>
DeprecationWarning</td><td> 关于被弃用的特征的警告</td></tr><tr><td>
FutureWarning </td><td>关于构造将来语义会有改变的警告</td></tr><tr><td>
OverflowWarning</td><td> 旧的关于自动提升为长整型(long)的警告</td></tr><tr><td>
PendingDeprecationWarning</td><td> 关于特性将会被废弃的警告</td></tr><tr><td>
RuntimeWarning </td><td>可疑的运行时行为(runtime behavior)的警告</td></tr><tr><td>
SyntaxWarning</td><td> 可疑的语法的警告</td></tr><tr><td>
UserWarning</td><td> 用户代码生成的警告</td></tr></tbody></table>


<br>

- **异常的大致归类**

> 语法异常 (在程序运行之前就存在)
> 
> 逻辑异常  自己代码问题


<br>

- **try... except... 捕获异常语句**

> **try的工作原理:** 当开始一个try语句后，python就在当前程序的上下文中作标记，这样当异常出现时就可以回到这里，try子句先执行，接下来会发生什么依赖于执行时是否出现异常。
> 
- 如果当try后的语句执行时发生异常，python就跳回到try并执行第一个匹配该异常的except子句，异常处理完毕，控制流就通过整个try语句（除非在处理异常时又引发新的异常）。
- 如果在try后的语句里发生了异常，却没有匹配的except子句，异常将被递交到上层的try，或者到程序的最上层（这样将结束程序，并打印缺省的出错信息）。
- 如果在try子句执行时没有发生异常，python将执行else语句后的语句（如果有else的话），然后控制流通过整个try语句。

		try:
		    f=open('file')
		    int(f.read())
		except ValueError:
		    print('读文件失败')
		except Exception as e:
		    print(e)
		else:                 # try 里面的代码没触发异常的时候
		    print('成功')
		finally:
		    f.close()


<br>

> **except ValueError 带有确定的异常参数**

**明确知道发生的异常时   ---用实际的异常**

对于已经可以预料的 但是不能完全规避的，要处理的异常 应该要指定具体的处理方式


<br>

> **万能异常 except Exception as e: | except Exception: |except:不带参数**

**万能异常 放在最后**

出现第一个异常(异常下的程序不会执行)的时候 一直往找处理 直到找到处理方式


<br>


> **try... else...  没有异常的时候**

**当try语句没有异常的时候执行**

	try:
	   a
	except NameError as e:
	     print(e)
	else:
	     print('没有错的时候 执行！！')   
当try语句没有异常的时候执行----与  for else，while else相似


<br>



> **try... finally...退出try时总会执行**

 
**语句无论是否发生异常都将执行最后的代码**


<br>
<br>

- **自定义异常 raise**

**使用raise语句自己触发异常**

**定义的异常类型必须是python提供的**


	mystr=input("输入字符")
	if mystr == "hello":
	    raise NameError("输入敏感字符错误")
	
	# raise NameError("输入敏感字符错误")
	# NameError: 输入敏感字符错误

<br>

	def func1( level ):
	    if level < 1:
	        raise Exception("万能错误")
	try:
	    func1(0) #  触发异常
	except Exception as e:
	    print(1,e)
	else:
	    print(2)





<br>

## 2 断言 assert

**在程序没完善之前，不知道程序在哪里会出错，与其让它在运行中崩溃，不如在出现错误条件时就崩溃，这时候就需要assert断言。**

*Assert statements are a convenient way to insert debugging assertions into a program*--*Python 官方文档解释*

<br>

> **assert  condition(条件) ,  "错误信息"**

    assert 1==2,"数值错误"

如果condition为False，那么raise一个AssertionError出来
