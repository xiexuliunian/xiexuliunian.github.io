---
title: Markdown 语法说明
date: 2017-12-14 13:21:12
categories: Markdown
---
<center>Markdownd的语法说明</center>

# 标题1 
## 标题2 
### 标题3
#### 标题4

分段的情况空一行即可

# 粗体和斜体

`*你好*`表示效果： *你好* ，是斜体的

`_你好_`也是表示斜体的：_你好_

`**你好**`显示的是粗体效果：**你好**

`~~你好~~`显示划线文本：~~你好~~

I end with two spaces (highlight me to see them).

There's a <br /> above me!

块引用很容易，用`>`完成，如下
>这是一个块

>这也是一个块

>> 这是什么呢？两个块

# 无序列表和有序列表
* 开头一个星号，然后空格
+ 或者一个加号
- 或者一个减号
## 下面是有序列表
1. 你好
2. hello
3. 哈哈
```
1. 你好
2. hello
3. 哈哈
```



## 子列表
1. 啦啦啦
2. 哈哈哈
3. 哇哇哇
    * 一二
    * 三四
4. 厉害了

```
1. 啦啦啦
2. 哈哈哈
3. 哇哇哇
    * 一二
    * 三四
4. 厉害了
```



# 插入连接和代码
本文的[Github](https://github.com/xiexuliunian "这是我的个人github主页哟")
```
本文的[Github](https://github.com/xiexuliunian "这是我的个人github主页哟")
```
对图片的引用格式为：
```
![图片名字](网址)
```
![陌上花开](https://timgsa.baidu.com/timg?image&quality=80&size=b9999_10000&sec=1513240842596&di=3f709ec01342b92906b56d3e4fcad471&imgtype=0&src=http%3A%2F%2Fimg5.duitang.com%2Fuploads%2Fitem%2F201407%2F30%2F20140730143346_MUG4w.thumb.700_0.jpeg)


分割线可以为`***`或者`---`

***

---
Boxes below without the 'x' are unchecked HTML checkboxes.
- [ ] First task to complete.
- [ ] Second task that needs done
This checkbox below will be a checked HTML checkbox.
- [x] This task has been completed

选框代码如下

```shell
Boxes below without the 'x' are unchecked HTML checkboxes.
- [ ] First task to complete.
- [ ] Second task that needs done
This checkbox below will be a checked HTML checkbox.
- [x] This task has been completed

```

# 代码块
可以使用四个空格，或一个制表符表示

    啦啦啦

```ruby
def foobar
    puts "Hello world!"
end
```
## 链接
[Click me!](http://www.baidu.com/ "这是一个百度链接")

`[Click me!](http://www.baidu.com/ "这是一个百度链接")`

## 图片
<https://www.baidu.com>  
[百度](www.baidu.com)

## 自动电子邮件
[邮箱](zzuzxd@126.com)

## 表示键盘按键
Your computer crashed? Try sending a
<kbd>Ctrl</kbd>+<kbd>Alt</kbd>+<kbd>Del</kbd>

`Your computer crashed? Try sending a
<kbd>Ctrl</kbd>+<kbd>Alt</kbd>+<kbd>Del</kbd>`

## 表格
|你好|啦啦|呼呼|
|:-|:-:|-:|
|左|中|右|
|一|二|三|




