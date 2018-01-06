---
layout: post
title:  "python 流程控制与条件分支"
description: python 流程控制与条件分支
date:   2017-02-28 14:55:52 +0200
categories: jekyll update
---




# python 流程控制与条件分支 #

----------
<br>

## 1 条件分支 ##

<br>

<img src="http://www.runoob.com/wp-content/uploads/2013/11/if-condition.jpg">


**(1)通过一条或多条语句的执行结果（True或者False）来决定执行的代码语句；
Python程序语言指定任何非0和非空（null）值为true，0 或者 null为false。
Python 编程中 if 语句用于控制程序的执行，基本形式为：**

    
    if <条件1>：
    <语句1>
    elif <条件2>:
    <语句2>
    else:
    <语句3>

**(2)由于 python 并不支持 switch 语句，所以多个条件判断，只能用 elif 来实现，如果判断需要多个条件需同时判断时，可以使用 or （或），表示两个条件有一个成立时判断条件成功；使用 and （与）时，表示只有两个条件同时成立的情况下，判断条件才成功。**

**(3)当if有多个条件时可使用括号来区分判断的先后顺序，括号中的判断优先执行，此外 and 和 or 的优先级低于>（大于）、<（小于）等判断符号，即大于和小于在没有括号的情况下会比与或要优先判断。**


<br>


----------

<br>

## 2 循环语句 ##

<img src="http://www.runoob.com/wp-content/uploads/2013/11/loop_architecture.jpg" alt="loop_architecture" width="304" height="348">

**循环语句允许我们执行一个语句或语句组多次**

<br>

- **python的循环 类型(没有 do..while)**

<table border="1">
<tbody><tr><th style="width:30%">循环类型</th><th>描述</th></tr>
<tr><td><a href="/python/python-while-loop.html" title="Python WHILE 循环">while 循环</a></td><td>在给定的判断条件为 true 时执行循环体，否则退出循环体。</td></tr>
<tr><td><a href="/python/python-for-loop.html" title=" Python FOR 循环">for 循环</a></td><td>重复执行语句</td></tr>
<tr><td><a href="/python/python-nested-loops.html" title="Python 循环全套">嵌套循环</a></td><td>你可以在while循环体中嵌套for循环</td></tr>
</tbody></table>

<br>


- **循环控制语句**


<table border="1">
<tbody><tr><th style="width:30%">控制语句</th><th>描述</th></tr>
<tr><td><a href="/python/python-break-statement.html" title="Python break 语句">break 语句</a></td><td>在语句块执行过程中终止循环，并且跳出整个循环</td></tr>
<tr><td><a href="/python/python-continue-statement.html" title="Python  语句">continue 语句</a></td><td>在语句块执行过程中终止当前循环，跳出该次循环，执行下一次循环。</td></tr>
<tr><td><a href="/python/python-pass-statement.html" title="Python pass 语句">pass 语句</a></td><td>pass是空语句，是为了保持程序结构的完整性。</td></tr>
</tbody></table>

----------

<br>

**1. While 循环**

<br>


**- 基本形式**

在某条件下 True，循环执行某段程序:
    
    while 判断条件：
    执行语句……

**- 跳过循环**

>  continue 跳过该次循环


    continue 语句用来告诉Python跳过当前循环的剩余语句
	continue 语句跳出本次循环，而break跳出整个循环

<p>

> break 则是用于退出循环

	break 用来终止循环语句



**- while True 无限循环（死循环）**


**- while... else**

在循环条件为 false 时执行 else 语句

 

    count = 0
    while count < 5:
       print (count, " is  less than 5"）
       count = count + 1
    else:
       print (count, " is not less than 5"）


**- 简单语句组**

可以将该语句与while写在同一行中


    while (flag): print 'Given flag is really true!'



<br>

**2. for 循环**

<br>

**- 遍历可迭代数据**

for循环可以遍历任何序列的项目，如一个列表或者一个字符串

    for item in "python":
    	print(item)

**- 通过序列索引循环遍历**

    text_list=[1,2,3]
    for item in range(len(text_list)):
    	print(item,text_list[item])
    
**- for .. else**

else 中的语句会在循环正常执行完（即 for 不是通过 break 跳出而中断的）的情况下执行，while … else 也是一样。

    for num in range(10,20):  # 迭代 10 到 20 之间的数字
       for i in range(2,num): # 根据因子迭代
     	 if num%i == 0:  # 确定第一个因子
    	 j=num/i  # 计算第二个因子
    	 print '%d 等于 %d * %d' % (num,i,j)
     	 break# 跳出当前循环

       else:  # 循环的 else 部分
      	 print num, '是一个质数'
<br>

**3 循环嵌套**

<br>

 - for 循环嵌套 -

<p>

    冒泡排序# 定义列表 list
    arays = [1,8,2,6,3,9,4]
    for i in range(len(arays)):
    	for j in range(i+1):
   	    if arays[i] < arays[j]:
    实现连个变量的互换
   			 arays[i],arays[j] = arays[j],arays[i]


- while 循环嵌套

<p>
    
    while expression:
       while expression:
      statement(s)
       statement(s)
<br>

**4 占位语句 pass**

Python pass是空语句，是为了保持程序结构的完整性。
pass 不做任何事情，一般用做占位语句

    for number in range(0,10):
    	if number == 5:
    		pass
    		print("pass",5)
    	else:
    		print("number",number)



