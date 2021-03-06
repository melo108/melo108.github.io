---
layout: post
title:  "python 函数基础"
description: python 函数基础
date:   2017-03-11 14:55:52 +0200
categories: jekyll update
---




# python 函数基础

----------

<br>

## 1 函数的基本认识

<br>


**- 函数相当于一个 有功能的代码块 的容器**

**- 为什么要用函数？**


>  1  解决代码冗余
>  
>  2  提高代码的可读性
>  
>  3  提高代码的重用性
>  
>  4  增强代码的可扩展性
>  
>  5  解耦    --->>>把大的功能 拆解 ，把一个功能尽量细化成多个小功能，      
>     并且功能之间的影响尽量减到最小，面向过程编程功能与功能之间耦合很紧
>     密


**- 函数的分类**
> 
> 1 内置函数，比如print()
> 
> 2 自定义函数



<br>

## 2 函数的定义

<br>

**函数的定义**：

　　　　**def：**表示函数的关键字

　　　　**函数名：**以后可以根据函数名调用函数，test可以作函数名

　　　　**函数体：**中间可以做一系列的运算

　　　　**参数：**为函数提供数据

　　　　**返回值（return）：**当函数执行完毕后，可以给调用者返回数据。 多个返回元组形式，单个为原形式

    
		    def 函数名(参数1，参数2):  #  函数的定义
		    
			    '''
			    这是一个什么函数
			    参数1               # 函数的注释
			    参数2
			    return
			    '''
		
		       函数体               # 函数的内容
		       
		    
		    函数名('alex',83)      # 函数的调用   



<br>

## 3 函数的调用

<br>


**函数的基本结构完成定义以后，你可以通过另一个函数调用执行 func()**

		def F():         #  第一步 整个函数先存储在内存       1
			print(123)   # 默认 return None
		a =F()           # 执行函数 ----打印  接收None     2
		print(a)         #  打印函数的返回值 a



<br>

## 4 函数的返回值

<br>

**return执行后，结束函数**

**返回值的三种情况  None， 多个值 ，一个值**


> 1 return None   #=====>返回None，什么都不写
> 
> 2 return 1,2,3   #  ==== >返回一个元组(形式是返回多个值)
> 
> 3 return [1,2,3] # ==== 返回一个列表



    def H():
    	return (1,2,3)#  返回一个元组   # 1,2,3
    	return 1,[1,2,3,4]  #  返回的是一个元祖 (1,[1,2,3,4])


<br>

## 5 函数的参数

<br>



**- 形参与实参**

形参不占用内存空间，调用时才会占用内存，在调用结束后，才会被释放。实参是给实参进行赋值

> 
> 形式参数：接受参数
> 
> 实际参数：实际传进的参数

    
     1 def test(x):   # x代表形参
     2 		'''
     3 		2*x+1
     4 		:param x:整形数字
     5 		:return:返回计算结果
     6 		'''
     7 		y = 2*x+1
     8 		return y

     9 p = test(3) # test()表示运行名为test函数，3代表实参，给x进行赋值
    10 print(p)


<br>

**-  函数定义 的角度： 位置参数，默认参数，动态参数**


 
> 位置参数

 def func(a,b):  

> 默认参数 

 def  welcome (name,sex='male' )


不传参数时为默认，传参数了就是实际参数，可以不传参数，默认参数再定义的阶段就已经确定了

	注意 默认参数陷阱(可变的数据类型 (列表) )

	def func(l=[]):
	      l.append(123)
	      print(l)
	 func()
	 func([])
	 func()
	                                      # [123]
	                                      # [123]
	                                      # [123, 123]




> 动态参数 ---- 传入多个参数 


* *args   只适用于 按位置传参

* **kwargs  按关键字  传参数



站在函数定义的角度  *  聚合作用，将一个一个的参数组合成一个元祖

站在函数调用的角度  *  打散  将一个列表 或者元祖 打散成多个参数

    
     def demo4(**kwargs):           #   聚合
     	print(kwargs)				 # 字典

     demo4(a=1,b=2,c=3)
     demo4(**{'a': 1, 'b': 2, 'c': 3}) # 打散

<br>

    def demo5(*args,**kwargs):  # 先位置 后关键字
   		 print(args,kwargs) #(1, 2, 3) {'a': 10}
    
    demo5(1,2,3,a=10)  # 先位置 后关键字
    
    def demo(*args,**kwargs):
    	print(args,kwargs)
    
    t=[1,2,3]
    d={'a':1,'b':2}
    demo(*t,**d)

<br>

**-  函数调用 (传参)的角度： 按位置传参，按关键字传参，混用**




> 按照位置传参

func (1,2)   


> 按关键字传参


 func (b=1,a=1)  


> 混用  


func(1,b=2)

混用的时候  先按位置传参，再按关键字传参 



<br>

**- 函数的 正确 参数使用**

     def func ( 位置1,位置2, *args, 默认=10, **kwargs ):  >>>  正确的顺序
    
	     print(位置1,位置2)
	     print(默认)
	     print(args)
	     print(kwargs)

     func(1,2,3,4,a=1,b=2)


<br>

## 6 函数的嵌套调用与定义

<br>

    def func():
   		 print(123)
    def func2():
    	 func()
   		 print(456)
    func2()
    print(789)   # 函数的  嵌套调用

<br>

    
    def func(*args,**kwargs):
    	def func2(a,b,c):
    		print(a,b,c)
    	func2(*args,**kwargs)
    func(1,2,c=3)
    

<br>

## 7 命名空间--作用域, 全局变量和局部变量

<br>

在Python中，对象是独立的，不同作用域中的不同名字都可以被绑定在同一个对象上，当然对这个对象的修改会影响所有的引用。赋值操作就是名字和对象的绑定或重绑定。

一个程序的所有的变量并不是在哪个位置都可以访问的。访问权限决定于这个变量是在哪里赋值的。**变量的作用域,命名空间**

定义在函数内部的变量拥有一个**局部作用域**，定义在函数外的拥有**全局作用域**。

局部变量只能在其被声明的函数内部访问，而全局变量可以在整个程序范围内访问。调用函数时，所有在函数内声明的变量名称都将被加入到作用域中

<br>

> **全局变量(常用大写)**

<br>

    
    一个Python程序的几个作用域：
    
	    最里面的局部作用域
	    外层函数的局部作用域
	    模块的全局作用域
	    包含Python内置对象的最外层作用域

<br>

**- 命名空间的 加载顺序** 



python启动 ===>> 内置的命名空间 ====>> 加载全局命名空间(从上到下) ===>>> 加载局部的命名空间(从上到下)


> 对于变量的使用  内部优先用自己的

<img src="https://images2017.cnblogs.com/blog/1220788/201708/1220788-20170830110835124-285884752.png" alt="">


<br>

**- nonlocal 与 global**


global语句用来声明一系列变量，这些变量会引用到当前模块的全局命名空间的变量
，nonlocal语句用来声明一系列的变量，这个声明会从声明处从里到外的namespace去搜寻这个变量，直到模块的全局域（不包括全局域），找到了则引用这个命名空间的这个名字和对象，若作赋值操作，则直接改变外层域中的这个名字的绑定。


>  globals()        打印全局，
>  
>  locals()        #局部的变量


