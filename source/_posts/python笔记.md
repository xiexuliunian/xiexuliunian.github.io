---
title: python 学习笔记
date: 2017-11-18 13:46:18
categories: python基础知识
---
# <center>学习笔记</center>
### 1. <font color="#4590a3" size = "4px">元组(tuple)</font>里面的元素是不可以更改的，但是元组里面的列表中的元素是可以更改的。如下所示。
<center>{% asset_img 1.png 说明 %}</center>

### 2. <font color="#4590a3" size = "4px">if </font>语句执行有个特点，它是从上往下判断，如果在某个判断上是True，把该判断对应的语句执行后，就忽略掉剩下的elif和else，所以，请测试并解释为什么下面的程序打印的是teenager：
``` python
age = 20
if age >= 6:
    print('teenager')
elif age >= 18:
    print('adult')
else:
    print('kid')
```
### 3.<font color="#4590a3" size = "4px">循环 </font>我们可以轻易的实现一个100的求和。
``` python
sum=0
for i in range(101):
    sum +=i
print(sum)
```
### 4.<font color="#4590a3" size = "4px">字典 </font>dict的支持，dict全称dictionary，在其他语言中也称为map，使用键-值（key-value）存储，具有极快的查找速度。

``` python
>>> d = {'Michael': 95, 'Bob': 75, 'Tracy': 85}
>>> d['Michael']
95
```
和list比较，dict有以下几个特点：

查找和插入的速度极快，不会随着key的增加而变慢；

需要占用大量的内存，内存浪费多。

而list相反：

查找和插入的时间随着元素的增加而增加；
占用空间小，浪费内存很少。

<font color="#4590a3" size = "4px">Set(集合) </font> 也是一组key的集合，但不存储value。由于key不能重复，所以，在set中，没有重复的key。而且集合中的数据是无序的。
``` python
>>> s = set([1, 1, 2, 2, 3, 3])
>>> s
{1, 2, 3}
```
<font color="#4590a3" size = "4px">5 .Python中的Map函数 </font>  

`map()`是 Python 内置的高阶函数，它接收一个函数 f 和一个 list，并通过把函数 f 依次作用在 list 的每个元素上，得到一个新的 list 并返回。
``` python
def f(x):
    return x*x

print map(f, [1, 2, 3, 4, 5, 6, 7, 8, 9])
```
输出结果为：
`[1, 4, 9, 10, 25, 36, 49, 64, 81]`

<font color="red" size = "6px">任务 </font>    
&ensp;&ensp;&ensp;&ensp;假设用户输入的英文名字不规范，没有按照首字母大写，后续字母小写的规则，请利用map()函数，把一个list（包含若干不规范的英文名字）变成一个包含规范英文名字的list：  输入：['adam', 'LISA', 'barT']  
输出：['Adam', 'Lisa', 'Bart']

``` python
def format_name(s):
    # s=s[0].upper()+s[1:].lower()
    # s=s.capitalize()
    s=s.title()
    return s
a=map(format_name,['adam','LISA','barT'])
print(list(a))
```
输出为：`['Adam', 'Lisa', 'Bart']`


