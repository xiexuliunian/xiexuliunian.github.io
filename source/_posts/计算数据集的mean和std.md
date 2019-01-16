---
title: 计算数据集的mean和std
date: 2019-01-16 20:38:33
tags: 深度学习
---
# <center>计算数据集的mean和std</center>
&emsp;&emsp;经常我们要对数据集进行`标准化操作`，需要减去均值`mean`，除以标准差`std`,那就需要先计算出整个数据集的均值和标准差。下列代码给出两种方法。
```python
import torch
import numpy as np
import torchvision.datasets as dsets
import torchvision.transforms as transforms

device = torch.device("cuda:0" if torch.cuda.is_available() else "cpu")
print(device)

# CIFAR-10数据集
train_dataset = dsets.CIFAR10(
    root="./data/", train=True, transform=transforms.ToTensor(), download=True)


def get_mean_and_std(dataset):
    '''Compute the mean and std value of dataset.'''
    dataloader = torch.utils.data.DataLoader(dataset, batch_size=1, shuffle=True, num_workers=2)
    mean = torch.zeros(3)
    std = torch.zeros(3)
    print('==> Computing mean and std..')
    for inputs, targets in dataloader:
        # 3通道
        for i in range(3):
            mean[i] += inputs[:, i, :, :].mean()
            std[i] += inputs[:, i, :, :].std()
    mean.div_(len(dataset))
    std.div_(len(dataset))
    return mean, std


if __name__ == "__main__":
    #第一种方法
    mean, std = get_mean_and_std(train_dataset);
    print(mean, std, sep='\n')
    print(train_dataset.train_data.shape)
    #第二种方法
    print("data mean: %s" % (np.mean(train_dataset.train_data, axis=(0, 1, 2)) / 255))
    print("data std: %s" % (np.std(train_dataset.train_data, axis=(0, 1, 2)) / 255))
```