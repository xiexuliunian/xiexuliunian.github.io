---
title: NMS 非极大值抑制
date: 2019-01-18 19:21:37
categories: 深度学习
---
# <center>NMS 非极大值抑制</center>
&emsp;&emsp;`非极大值抑制(NMS)`是目标检测、人脸检测中比较常用的一种去除重复目标框的算法，下面以人脸检测为例子，详细的对NMS算法进行分析。

&emsp;&emsp;首先来看一副没有经过极大值抑制的人脸检测的图片。

<div style="width: 60%; margin: auto">
<center>{% asset_img 1.png [人脸检测图片] %}</center>
</div>