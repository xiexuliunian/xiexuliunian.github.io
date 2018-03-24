---
title: Argparse 笔记
date: 2017-11-19 11:54:58
tags:
---
## <center>1.Argparse学习笔记</center>
### 在学习python的时候，经常遇到下面的语句。
```python
import argparse

if __name__== "__main__":
    parser=argparse.ArgumentParser()
    parser.add_argument('--number1',help='第一个数字')
    parser.add_argument('--number2',help='第二个数字')
    parser.add_argument('--operation',help='操作符')

    args=parser.parse_args()
```
<font color='red' size=3px>这些是什么呢？</font>  我们来看下定义，argparse是python用于解析命令行参数和选项的标准模块，用于代替已经过时的optparse模块。argparse模块的作用是用于解析命令行参数，例如python parseTest.py input.txt output.txt --user=name --port=8080。


## <font color="#4590a3" size=4px>第一步设置一个解析器</font>
* 使用argparse的第一步就是创建一个解析器对象，并告诉它将会有些什么参数。那么当你的程序运行时，该解析器就可以用于处理命令行参数。解析器类是 ArgumentParser 。构造方法接收几个参数来设置用于程序帮助文本的描述信息以及其他全局的行为或设置。

* `parser=argparse.ArgumentParser()` 这句代码用来设置解析器
## <font color="#4590a3" size=4px>第二步 定义参数</font>
``` python
parser.add_argument('--number1',help='第一个数字')
parser.add_argument('--number2',help='第二个数字')
parser.add_argument('--operation',help='操作符')
```
这里添加参数，我们来看下运行结果
``` python
import argparse

if __name__== "__main__":
    parser=argparse.ArgumentParser()
    parser.add_argument('number1',help='第一个数字')
    parser.add_argument('number2',help='第二个数字')
    parser.add_argument('operation',help='操作符')

    args=parser.parse_args()

    print(args.number1)
    print(args.number2)
    print(args.operation)

    n1=int(args.number1)
    n2=int(args.number2)
    result=None
    if args.operation == "+":
        print('结果是：%d'%(n1+n2))
```
输入命令`python demo.py -h`
``` shell
usage: fmt output.py [-h] number1 number2 operation

positional arguments:
  number1     第一个数字
  number2     第二个数字
  operation   操作符
```
输入命令 `pthon demo 2 5 +`,结果为：
```
2
5
+
结果是：7
```
将上述源码做一下改动
```python
parser.add_argument('--number1',help='第一个数字')
parser.add_argument('--number2',help='第二个数字')
parser.add_argument('--operation',help='操作符')
```
输入命令`python demo.py -h`
```
usage: fmt output.py [-h] [--number1 NUMBER1] [--number2 NUMBER2]
                     [--operation OPERATION]

optional arguments:
  -h, --help            show this help message and exit
  --number1 NUMBER1     第一个数字
  --number2 NUMBER2     第二个数字
  --operation OPERATION   操作符
```
* 我们可以看到不一致，此时的调用可以采用`python demo --number 2 --number 5 --operation +`来输入

## <center>2.这是第二次写Argparse这个模块，Anyway，那就写的深入点吧</center>
* 今天我们这里主要来讲`add_argument() `这个方法，这也是Argparse这个模块里面使用最多的方法。

### 2.1 Name或者flags参数
我们使用`add_argument() `来创建新的参数的时候，必须知道新的参数是位置参数还是可选参数，以及参数是否使用缩写。当我们创建位置参数时，可以创建如下。
```python
import argparse

parser=argparse.ArgumentParser()
parser.add_argument('inter')
args=parser.parse_args()

print(args.inter)
```
然后输入命令
```shell
python argp.py 12
```
传进去的参数12，就被赋给了`inter`,随后被打印出来。
```
PS E:\codeme\python learn\VS code\python basic> python .\argp.py -i 12
12
```

接下来我们看，如何创建一个可选参数
```python
import argparse

parser=argparse.ArgumentParser()
parser.add_argument('--inter')
args=parser.parse_args()

print(args.inter)
```
这时的命令该怎么输呢？
```
python argp.py --inter 12
```
这样就可以了，之所以称其为可选参数，是因为如果你不传递参数进去，程序也不会报错，而之前的位置参数，当不传递参数进去时，就会报错了。`--inter`显然有些麻烦，那如何简写呢？
```python
import argparse

parser=argparse.ArgumentParser()
parser.add_argument('-i','--inter')
args=parser.parse_args()

print(args.inter)
```
这样输入
``` shell
python argp.py -i 12
```
就仍然达到传递参数12进去的效果了。这就说明可选参数以`'-'`来表明，而其他的参数被认为是位置参数了。

### 2.2 action参数
`action` 关键字指出应该如何处理命令行参数

`'store'`只是保存参数，是默认的值
```python
import argparse

parser=argparse.ArgumentParser()
parser.add_argument('--inter')
args=parser.parse_args(['--inter','12'])

print(args.inter)
```
输出为 `12`

`'store_const'`保存const关键字指出的值
```python
import argparse

parser=argparse.ArgumentParser()
parser.add_argument('--inter',action='store_const',const=23)
args=parser.parse_args(['--inter'])

print(args.inter)
```
输出为 `23`

`'store_true'`和`'store_false'` - 它们是`'store_const'`的特殊情形，分别用于保存值True和False
```python
import argparse

parser=argparse.ArgumentParser()
parser.add_argument('--inter',action='store_true')
parser.add_argument('--float',action='store_false')
parser.add_argument('--double',action='store_false')


args=parser.parse_args(['--inter','--float'])

print(args.inter,args.float,args.double)
```
输出 `True False True`

`'append'` - 保存一个列表，并将每个参数值附加在列表的后面。这对于允许指定多次的选项很有帮助。
```python
import argparse

parser=argparse.ArgumentParser()
parser.add_argument('--inter',action='append')
args=parser.parse_args(['--inter','12','--inter','45'])

print(args.inter)
```
这里由于`--inter`可以调用多次，将后面的数字加到其列表里，所以输出为`['12', '45']`

`'append_const'` - 保存一个列表，并将const关键字参数指出的值附加在列表的后面。（注意const关键字参数默认是None。）`'append_const'` 动作在多个参数需要保存常量到相同的列表时特别有用。
```python
import argparse

parser=argparse.ArgumentParser()
parser.add_argument('--inter',dest='types',action='append_const',const=int)
parser.add_argument('--float',dest='types',action='append_const',const=float)

args=parser.parse_args(['--inter','--float'])

print(args.types)
```
输出 `[<class 'int'>, <class 'float'>]`

`'count'` - 计算关键字参数出现的次数
```python
import argparse

parser=argparse.ArgumentParser()
parser.add_argument('--inter','-i',action='count')


args=parser.parse_args(['-iii'])

print(args.inter)
args=parser.parse_args(['-iiiiii'])
print(args.inter)
```
输出为 `3 6`
### 2.3 nargs参数
`nargs`关键字参数将一个动作(`action`)与不同数目的命令行参数关联在一起。

> 它的值首先可以是一个整数 **`N`**

* 我们先来看一个默认的情况, 没有nargs参数：
```python
import argparse

parser=argparse.ArgumentParser()
parser.add_argument('--foo')
args=parser.parse_args()

print(args.foo)
```
运行命令
```
python args.py --foo 2
```
结果为 **`2`**

* 再来看一个具有nargs参数的情况  
```python
import argparse

parser=argparse.ArgumentParser()
parser.add_argument('--foo',nargs=2)
args=parser.parse_args()

print(args.foo)
```
这里 `nargs=2` ,规定可以传递两个参数
```
python args.py --foo 2 3
```
来看一下输出
`['2', '3']`,是一个列表，而之前的是元素本身。

> `nargs`的值可以是 **`？`**,如果有的话就从命令行读取一个参数并生成一个元素。如果没有对应的命令行参数，则产生一个来自default的值。注意，对于可选参数，有另外一种情况 - 有选项字符串但是后面没有跟随命令行参数。在这种情况下，将生成一个来自const的值

* 来看一个有参数的情况
```python
import argparse

parser=argparse.ArgumentParser()
parser.add_argument('--foo',nargs="?")
args=parser.parse_args()

print(args.foo)
```
运行命令
```
python args.py --foo 2
```
结果为 **`2`**

* 没有参数时
```python
import argparse

parser=argparse.ArgumentParser()
parser.add_argument('--foo',nargs="?",default='2',const='3')
parser.add_argument('int',nargs='?',default='z')
args=parser.parse_args()

print(args.foo,args.int)
```
运行命令
```
python args.py
```
输出 **`2 z`**

运行命令
```
python args.py y --foo
```
输出 **`3 y`**

运行命令
```
python args.py y --foo 5
```
输出 **`5 y`**

> `nargs`的值可以是 `*`和 `+`, 出现的所有命令行参数都被收集到一个列表中。注意，一般情况下具有多个带有 `nargs='*'`的位置参数是不合理的，但是多个带有 `nargs='*' `的可选参数是可能的。`+`的情况下，如果没有至少出现一个命令行参数将会产生一个错误信息，`*`不会产生这个问题。
```python
import argparse

parser=argparse.ArgumentParser()
parser.add_argument('--foo', nargs='*')
parser.add_argument('--bar', nargs='*')
parser.add_argument('baz', nargs='*')
args=parser.parse_args()
print('args.foo=%6s'%args.foo,'args.bar=%6s'%args.bar,'args.baz=%6s'%args.baz,sep='\n')
```
运行命令
```
python args.py a b --foo 2 3 --bar z s
```
输出

`args.foo=['2', '3']`

`args.bar=['z', 's']`

`args.baz=['a', 'b']`

> 如果没有提供 `nargs`关键字参数，读取的参数个数取决于`action`。通常这意味着将读取一个命令行参数并产生一个元素（不是一个列表）。
