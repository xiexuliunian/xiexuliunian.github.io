---
title: pytorch和gluon的不同
date: 2019-01-06 09:58:44
categories: 深度学习
---
# <center>pytorch和gluon的不同</center>
&emsp;&emsp;由于经常使用pytorch和gluon发现他们有很多相同之处，同时也在混用他们，但是他们的不同之处经常搞得自己很混乱，因此在本篇博客里面，对他们的不同进行区分，以帮助自己理清思路。

## 1. 数据操作
&emsp;&emsp;提到数据操作，在python里面最常用的是numpy数值计算库，但是该库不能支持gpu下运行，因此在大规模的张量情况下，两个深度学习计算库都有自己的核心数据操作库。在gluon里面是`NDArray(nd是其缩写形式)`,在pytorch里面是`tensor`。

&emsp;&emsp;`gluon`
```python
from mxnet import nd
import numpy as np
a=np.arange(12)
x=nd.arange(12)
print(x)
print(x.shape,x.size)

#[ 0.  1.  2.  3.  4.  5.  6.  7.  8.  9. 10. 11.]
#<NDArray 12 @cpu(0)>
#(12,) 12
```
&emsp;&emsp;`pytorch`
```python
import torch
import numpy as np
a=np.arange(12)
b=torch.from_numpy(a)    #从numpy建立tensor
x=torch.arange(12)
print(x)
print(x.shape,x.size())
print(b)

#tensor([ 0,  1,  2,  3,  4,  5,  6,  7,  8,  9, 10, 11])
#torch.Size([12]) torch.Size([12])
#tensor([ 0,  1,  2,  3,  4,  5,  6,  7,  8,  9, 10, 11], dtype=torch.int32)
```
## 2.自动求导
&emsp;&emsp;在gluon中对一个变量求导要先调用`attach_grad`函数来申请存储梯度所需要的内存。为了减少计算和内存开销，默认条件下 MXNet 不会记录用于求梯度的计算。我们需要调用record函数来要求 MXNet 记录与求梯度有关的计算。

&emsp;&emsp;`gluon`
```python
from mxnet import autograd,nd
x=nd.arange(1,13).reshape((3,4))
print(x)
x.attach_grad()
with autograd.record():
    y=x**2+4*x
    z=2*y+3
print(z)
z.backward()

# z是一个标量。接下来我们可以通过调用backward函数自动求梯度。需要注意的是，如果z不是一个标量，MXNet将默认先对z中元素求和得到新的变量，再求该变量有关x的梯度。
print(x.grad)
# [[ 1.  2.  3.  4.]
#  [ 5.  6.  7.  8.]
#  [ 9. 10. 11. 12.]]
# <NDArray 3x4 @cpu(0)>

# [[ 13.  27.  45.  67.]
#  [ 93. 123. 157. 195.]
#  [237. 283. 333. 387.]]
# <NDArray 3x4 @cpu(0)>

# [[12. 16. 20. 24.]
#  [28. 32. 36. 40.]
#  [44. 48. 52. 56.]]
# <NDArray 3x4 @cpu(0)>
```
&emsp;&emsp;在pytorch中对于tensor要求导，要为其附加一个`requires_grad`的属性。

&emsp;&emsp;`pytorch`
```python
import torch
import numpy as np
x=torch.tensor(np.arange(1,13).reshape(3,4),dtype=torch.float32,requires_grad=True)
print(x)
y=x**2+4*x
z=2*y+3
print(z)
z.sum().backward() #先计算出标量和
print(x.grad)

# tensor([[ 1.,  2.,  3.,  4.],
#         [ 5.,  6.,  7.,  8.],
#         [ 9., 10., 11., 12.]], requires_grad=True)
# tensor([[ 13.,  27.,  45.,  67.],
#         [ 93., 123., 157., 195.],
#         [237., 283., 333., 387.]], grad_fn=<AddBackward0>)
# tensor([[12., 16., 20., 24.],
#         [28., 32., 36., 40.],
#         [44., 48., 52., 56.]])
```
## 3.计算设备
&emsp;&emsp;实际操作中我们的很多操作都要在GPU中进行，那gluon和pytorch又是如何操作的呢？

&emsp;&emsp;`gluon`
```python
import mxnet as mx
from mxnet import nd
import numpy as np
a=nd.arange(1,13).reshape((3,4))    #reshape(3,4)=reshape((3,4))
b=a.as_in_context(mx.gpu())
c=nd.array(np.arange(1,13).reshape(3,4),ctx=mx.gpu())
d=nd.arange(1,13,ctx=mx.gpu()).reshape((3,4))
print(a,b,c,d,sep='\n')

# [[ 1.  2.  3.  4.]
#  [ 5.  6.  7.  8.]
#  [ 9. 10. 11. 12.]]
# <NDArray 3x4 @cpu(0)>

# [[ 1.  2.  3.  4.]
#  [ 5.  6.  7.  8.]
#  [ 9. 10. 11. 12.]]
# <NDArray 3x4 @gpu(0)>

# [[ 1.  2.  3.  4.]
#  [ 5.  6.  7.  8.]
#  [ 9. 10. 11. 12.]]
# <NDArray 3x4 @gpu(0)>

# [[ 1.  2.  3.  4.]
#  [ 5.  6.  7.  8.]
#  [ 9. 10. 11. 12.]]
# <NDArray 3x4 @gpu(0)>
```
&emsp;&emsp;`pytorch`
```python
import torch
import numpy as np
a=torch.tensor(np.arange(1,13).reshape(3,4),device=torch.device('cuda'))
b=torch.tensor(np.arange(1,13).reshape(3,4)).cuda()
c=torch.arange(1,13,device=torch.device('cuda')).reshape(3,4)
d=torch.arange(1,13).cuda().reshape(3,4)
e=torch.arange(1,13).reshape(3,4).cuda()
print(a,b,c,d,e,sep='\n')

# tensor([[ 1,  2,  3,  4],
#         [ 5,  6,  7,  8],
#         [ 9, 10, 11, 12]], device='cuda:0', dtype=torch.int32)
# tensor([[ 1,  2,  3,  4],
#         [ 5,  6,  7,  8],
#         [ 9, 10, 11, 12]], device='cuda:0', dtype=torch.int32)
# tensor([[ 1,  2,  3,  4],
#         [ 5,  6,  7,  8],
#         [ 9, 10, 11, 12]], device='cuda:0')
# tensor([[ 1,  2,  3,  4],
#         [ 5,  6,  7,  8],
#         [ 9, 10, 11, 12]], device='cuda:0')
# tensor([[ 1,  2,  3,  4],
#         [ 5,  6,  7,  8],
#         [ 9, 10, 11, 12]], device='cuda:0')
```

## 4.模型构造
&emsp;&emsp;以下都以`Alexnet`网络的构造为例，来说明两个框架的不同,在gluon中如下。

&emsp;&emsp;`gluon`
```python
from mxnet.gluon import nn
from mxnet import init
from mxnet import nd
import mxnet as mx


def try_gpu():
    """If GPU is available, return mx.gpu(0); else return mx.cpu()"""
    try:
        ctx = mx.gpu()
        _ = nd.zeros((1,), ctx=ctx)
    except:
        ctx = mx.cpu()
    return ctx


ctx = try_gpu()


class AlexNet(nn.Block):
    def __init__(self, classes=1000, verbose=False, **kwargs):
        super(AlexNet, self).__init__(**kwargs)
        self.verbose = verbose
        with self.name_scope():
            self.features = nn.Sequential(prefix='')
            with self.features.name_scope():
                self.features.add(nn.Conv2D(64, kernel_size=11, strides=4,
                                            padding=2, activation='relu'))
                self.features.add(nn.MaxPool2D(pool_size=3, strides=2))
                self.features.add(nn.Conv2D(192, kernel_size=5, padding=2,
                                            activation='relu'))
                self.features.add(nn.MaxPool2D(pool_size=3, strides=2))
                self.features.add(nn.Conv2D(384, kernel_size=3, padding=1,
                                            activation='relu'))
                self.features.add(nn.Conv2D(256, kernel_size=3, padding=1,
                                            activation='relu'))
                self.features.add(nn.Conv2D(256, kernel_size=3, padding=1,
                                            activation='relu'))
                self.features.add(nn.MaxPool2D(pool_size=3, strides=2))
                # Flatten层负责将(N,C,H,W)维度变为(N,C*H*W)维度
                self.features.add(nn.Flatten())
                self.features.add(nn.Dense(4096, activation='relu'))
                self.features.add(nn.Dropout(0.5))
                self.features.add(nn.Dense(4096, activation='relu'))
                self.features.add(nn.Dropout(0.5))
            self.output = nn.Dense(classes)

    def forward(self, x):
        for i, b in enumerate(self.features):
            x = b(x)
            if self.verbose:
                print("features[%d] 输出维度: %s" % (i + 1, x.shape))
        x = self.output(x)
        if self.verbose:
            print("output 输出维度: %s" % (x.shape,))
        return x


def get_net(ctx, verbose=False):
    classes = 1000
    net = AlexNet(classes, verbose=verbose)
    # 定义的网络需要初始化设备和参数
    # 相比于pytorch，gluon不需要指定输入的通道数，就是由于先对网络进行了初始化，
    # 可以推断出来输入的通道数量
    net.initialize(ctx=ctx, init=init.Xavier())
    # net.initialize()
    # 默认的初始化方法是把所有权重初始化成在[-0.07, 0.07]之间均匀分布的随机数
    return net


if __name__ == '__main__':
    x = nd.random.uniform(shape=(32, 3, 227, 227), ctx=ctx)
    net = get_net(ctx, verbose=True)
    print(net)
    y = net(x)
    print("输出y的维度: %s" % (y.shape,))

# AlexNet(
#   (features): Sequential(
#     (0): Conv2D(None -> 64, kernel_size=(11, 11), stride=(4, 4), padding=(2, 2))
#     (1): MaxPool2D(size=(3, 3), stride=(2, 2), padding=(0, 0), ceil_mode=False)
#     (2): Conv2D(None -> 192, kernel_size=(5, 5), stride=(1, 1), padding=(2, 2))
#     (3): MaxPool2D(size=(3, 3), stride=(2, 2), padding=(0, 0), ceil_mode=False)
#     (4): Conv2D(None -> 384, kernel_size=(3, 3), stride=(1, 1), padding=(1, 1))
#     (5): Conv2D(None -> 256, kernel_size=(3, 3), stride=(1, 1), padding=(1, 1))
#     (6): Conv2D(None -> 256, kernel_size=(3, 3), stride=(1, 1), padding=(1, 1))
#     (7): MaxPool2D(size=(3, 3), stride=(2, 2), padding=(0, 0), ceil_mode=False)
#     (8): Flatten
#     (9): Dense(None -> 4096, Activation(relu))
#     (10): Dropout(p = 0.5, axes=())
#     (11): Dense(None -> 4096, Activation(relu))
#     (12): Dropout(p = 0.5, axes=())
#   )
#   (output): Dense(None -> 1000, linear)
# )
# features[1] 输出维度: (32, 64, 56, 56)
# features[2] 输出维度: (32, 64, 27, 27)
# features[3] 输出维度: (32, 192, 27, 27)
# features[4] 输出维度: (32, 192, 13, 13)
# features[5] 输出维度: (32, 384, 13, 13)
# features[6] 输出维度: (32, 256, 13, 13)
# features[7] 输出维度: (32, 256, 13, 13)
# features[8] 输出维度: (32, 256, 6, 6)
# features[9] 输出维度: (32, 9216)
# features[10] 输出维度: (32, 4096)
# features[11] 输出维度: (32, 4096)
# features[12] 输出维度: (32, 4096)
# features[13] 输出维度: (32, 4096)
# output 输出维度: (32, 1000)
# 输出y的维度: (32, 1000)

```

* 命令式编程：可以拿到所有中间变量值。(NDArray方式)
* 符号式编程：更加高效而且更容易移植。(Symbol方式)

&emsp;&emsp;通过使用 HybridBlock 或者 HybridSequential 来构建神经网络。默认他们跟 Block 和 Sequential 一样使用命令式执行。当我们调用`.hybridize()`后，系统会转换成符号式来执行。事实上，所有 Gluon 里定义的层全是 HybridBlock，这个意味着大部分的神经网络都可以享受符号式执行的优势。而如果使用gluon中的混合模式，要做相应的修改。
```python
from mxnet.gluon import nn
from mxnet import init
from mxnet import nd
import mxnet as mx


def try_gpu():
    """If GPU is available, return mx.gpu(0); else return mx.cpu()"""
    try:
        ctx = mx.gpu()
        _ = nd.zeros((1,), ctx=ctx)
    except:
        ctx = mx.cpu()
    return ctx


ctx = try_gpu()

# 使用HybridBlock
class AlexNet(nn.HybridBlock):
    def __init__(self, classes=1000, verbose=False, **kwargs):
        super(AlexNet, self).__init__(**kwargs)
        self.verbose = verbose
        with self.name_scope():
            # 使用HybridSequential
            self.features = nn.HybridSequential(prefix='')
            with self.features.name_scope():
                self.features.add(nn.Conv2D(64, kernel_size=11, strides=4,
                                            padding=2, activation='relu'))
                self.features.add(nn.MaxPool2D(pool_size=3, strides=2))
                self.features.add(nn.Conv2D(192, kernel_size=5, padding=2,
                                            activation='relu'))
                self.features.add(nn.MaxPool2D(pool_size=3, strides=2))
                self.features.add(nn.Conv2D(384, kernel_size=3, padding=1,
                                            activation='relu'))
                self.features.add(nn.Conv2D(256, kernel_size=3, padding=1,
                                            activation='relu'))
                self.features.add(nn.Conv2D(256, kernel_size=3, padding=1,
                                            activation='relu'))
                self.features.add(nn.MaxPool2D(pool_size=3, strides=2))
                # Flatten层负责将(N,C,H,W)维度变为(N,C*H*W)维度
                self.features.add(nn.Flatten())
                self.features.add(nn.Dense(4096, activation='relu'))
                self.features.add(nn.Dropout(0.5))
                self.features.add(nn.Dense(4096, activation='relu'))
                self.features.add(nn.Dropout(0.5))
            self.output = nn.Dense(classes)
    # 相比之前加了个参数F
    def hybrid_forward(self, F, x):
        for i, b in enumerate(self.features):
            x = b(x)
            if self.verbose:
                print("features[%d] 输出维度: %s" % (i + 1, x.shape))
        x = self.output(x)
        if self.verbose:
            print("output 输出维度: %s" % (x.shape,))
        return x


def get_net(ctx, verbose=False):
    classes = 1000
    net = AlexNet(classes, verbose=verbose)
    # 定义的网络需要初始化设备和参数
    # 相比于pytorch，gluon不需要指定输入的通道数，就是由于先对网络进行了初始化，
    # 可以推断出来输入的通道数量
    net.initialize(ctx=ctx, init=init.Xavier())
    # net.hybridize()  享受训练网络加速，但是采用symbol模式后，中间过程的shape就拿不到了
    # net.initialize()
    # 默认的初始化方法是把所有权重初始化成在[-0.07, 0.07]之间均匀分布的随机数
    return net


if __name__ == '__main__':
    x = nd.random.uniform(shape=(32, 3, 227, 227), ctx=ctx)
    net = get_net(ctx, verbose=True)
    print(net)
    y = net(x)
    print("输出y的维度: %s" % (y.shape,))

# AlexNet(
#   (features): HybridSequential(
#     (0): Conv2D(None -> 64, kernel_size=(11, 11), stride=(4, 4), padding=(2, 2))
#     (1): MaxPool2D(size=(3, 3), stride=(2, 2), padding=(0, 0), ceil_mode=False)
#     (2): Conv2D(None -> 192, kernel_size=(5, 5), stride=(1, 1), padding=(2, 2))
#     (3): MaxPool2D(size=(3, 3), stride=(2, 2), padding=(0, 0), ceil_mode=False)
#     (4): Conv2D(None -> 384, kernel_size=(3, 3), stride=(1, 1), padding=(1, 1))
#     (5): Conv2D(None -> 256, kernel_size=(3, 3), stride=(1, 1), padding=(1, 1))
#     (6): Conv2D(None -> 256, kernel_size=(3, 3), stride=(1, 1), padding=(1, 1))
#     (7): MaxPool2D(size=(3, 3), stride=(2, 2), padding=(0, 0), ceil_mode=False)
#     (8): Flatten
#     (9): Dense(None -> 4096, Activation(relu))
#     (10): Dropout(p = 0.5, axes=())
#     (11): Dense(None -> 4096, Activation(relu))
#     (12): Dropout(p = 0.5, axes=())
#   )
#   (output): Dense(None -> 1000, linear)
# )
# features[1] 输出维度: (32, 64, 56, 56)
# features[2] 输出维度: (32, 64, 27, 27)
# features[3] 输出维度: (32, 192, 27, 27)
# features[4] 输出维度: (32, 192, 13, 13)
# features[5] 输出维度: (32, 384, 13, 13)
# features[6] 输出维度: (32, 256, 13, 13)
# features[7] 输出维度: (32, 256, 13, 13)
# features[8] 输出维度: (32, 256, 6, 6)
# features[9] 输出维度: (32, 9216)
# features[10] 输出维度: (32, 4096)
# features[11] 输出维度: (32, 4096)
# features[12] 输出维度: (32, 4096)
# features[13] 输出维度: (32, 4096)
# output 输出维度: (32, 1000)
# 输出y的维度: (32, 1000)
```
&emsp;&emsp;`pytorch`
```python
import torch.nn as nn
import torch

device = torch.device("cuda:0" if torch.cuda.is_available() else "cpu")
class AlexNet(nn.Module):
    def __init__(self, num_classes=1000,verbose=False):
        super(AlexNet, self).__init__()
        self.verbose=verbose
        self.features = nn.Sequential(
            nn.Conv2d(3, 64, kernel_size=11, stride=4, padding=2),
            nn.ReLU(inplace=True),
            nn.MaxPool2d(kernel_size=3, stride=2),
            nn.Conv2d(64, 192, kernel_size=5, padding=2),
            nn.ReLU(inplace=True),
            nn.MaxPool2d(kernel_size=3, stride=2),
            nn.Conv2d(192, 384, kernel_size=3, padding=1),
            nn.ReLU(inplace=True),
            nn.Conv2d(384, 256, kernel_size=3, padding=1),
            nn.ReLU(inplace=True),
            nn.Conv2d(256, 256, kernel_size=3, padding=1),
            nn.ReLU(inplace=True),
            nn.MaxPool2d(kernel_size=3, stride=2),
        )
        self.classifier = nn.Sequential(
            nn.Dropout(),
            nn.Linear(256 * 6 * 6, 4096),
            nn.ReLU(inplace=True),
            nn.Dropout(),
            nn.Linear(4096, 4096),
            nn.ReLU(inplace=True),
            nn.Linear(4096, num_classes),
        )

    def forward(self, x):
        for i, b in enumerate(self.features):
            x = b(x)
            if self.verbose:
                print("features[%d] 输出维度: %s" % (i + 1, x.shape))
        # x = x.view(x.size(0), 256 * 6 * 6)
        # x = x.view(x.size(0), -1)
        x = x.reshape(x.size(0), -1)
        for i,b in enumerate(self.classifier):
            x=b(x)
            if self.verbose:
                print("classifier[%d] 输出维度: %s" % (i + 1, x.shape))
        return x

def get_net(device,verbose=False):
    classes=1000
    net=AlexNet(classes,verbose=True).to(device)
    # net = AlexNet(classes, verbose=True,device=device)

    return net

if __name__=='__main__':
    x=torch.nn.init.uniform_(torch.Tensor(32,3,227,227)).to(device)
    net=get_net(device,verbose=True)
    print(net)
    y=net(x)
    print("输出y的维度: %s" % (y.shape,))

# AlexNet(
#   (features): Sequential(
#     (0): Conv2d(3, 64, kernel_size=(11, 11), stride=(4, 4), padding=(2, 2))
#     (1): ReLU(inplace)
#     (2): MaxPool2d(kernel_size=3, stride=2, padding=0, dilation=1, ceil_mode=False)
#     (3): Conv2d(64, 192, kernel_size=(5, 5), stride=(1, 1), padding=(2, 2))
#     (4): ReLU(inplace)
#     (5): MaxPool2d(kernel_size=3, stride=2, padding=0, dilation=1, ceil_mode=False)
#     (6): Conv2d(192, 384, kernel_size=(3, 3), stride=(1, 1), padding=(1, 1))
#     (7): ReLU(inplace)
#     (8): Conv2d(384, 256, kernel_size=(3, 3), stride=(1, 1), padding=(1, 1))
#     (9): ReLU(inplace)
#     (10): Conv2d(256, 256, kernel_size=(3, 3), stride=(1, 1), padding=(1, 1))
#     (11): ReLU(inplace)
#     (12): MaxPool2d(kernel_size=3, stride=2, padding=0, dilation=1, ceil_mode=False)
#   )
#   (classifier): Sequential(
#     (0): Dropout(p=0.5)
#     (1): Linear(in_features=9216, out_features=4096, bias=True)
#     (2): ReLU(inplace)
#     (3): Dropout(p=0.5)
#     (4): Linear(in_features=4096, out_features=4096, bias=True)
#     (5): ReLU(inplace)
#     (6): Linear(in_features=4096, out_features=1000, bias=True)
#   )
# )
# features[1] 输出维度: torch.Size([32, 64, 56, 56])
# features[2] 输出维度: torch.Size([32, 64, 56, 56])
# features[3] 输出维度: torch.Size([32, 64, 27, 27])
# features[4] 输出维度: torch.Size([32, 192, 27, 27])
# features[5] 输出维度: torch.Size([32, 192, 27, 27])
# features[6] 输出维度: torch.Size([32, 192, 13, 13])
# features[7] 输出维度: torch.Size([32, 384, 13, 13])
# features[8] 输出维度: torch.Size([32, 384, 13, 13])
# features[9] 输出维度: torch.Size([32, 256, 13, 13])
# features[10] 输出维度: torch.Size([32, 256, 13, 13])
# features[11] 输出维度: torch.Size([32, 256, 13, 13])
# features[12] 输出维度: torch.Size([32, 256, 13, 13])
# features[13] 输出维度: torch.Size([32, 256, 6, 6])
# classifier[1] 输出维度: torch.Size([32, 9216])
# classifier[2] 输出维度: torch.Size([32, 4096])
# classifier[3] 输出维度: torch.Size([32, 4096])
# classifier[4] 输出维度: torch.Size([32, 4096])
# classifier[5] 输出维度: torch.Size([32, 4096])
# classifier[6] 输出维度: torch.Size([32, 4096])
# classifier[7] 输出维度: torch.Size([32, 1000])
# 输出y的维度: torch.Size([32, 1000])

```
&emsp;&emsp;从中可以看出gluon里面的激活函数是内嵌在每一层里面的，作为一个参数，而pytorch是作为一个层来看的。

## 5.优化器
&emsp;&emsp;两种框架的优化器写法不一样。同样都是SGD优化器

&emsp;&emsp;`gluon`

```python
trainer = gluon.Trainer(
        net.collect_params(), 'sgd', {'learning_rate': lr, 'momentum': 0.9, 'wd': wd})

```
&emsp;&emsp;`pytorch`

```python
optimizer=torch.optim.SGD(net.parameters(),lr=lr,momentum=0.9,weight_decay=wd)
```