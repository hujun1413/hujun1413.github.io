---
title: Pytorch教程
date: 2018-05-25 16:25:26 # 文章生成时间，一般不改
categories:   # 文章分类目录，参数可省略
    - Deep Learning
tags:   # 文章标签，参数可省略
    - Pytorch
---
### [PyTorch-Tutorial](https://github.com/MorvanZhou/PyTorch-Tutorial)
与之配套的说明教程：
[pytorch动态神经网络](https://morvanzhou.github.io/tutorials/machine-learning/torch/)
<!--more-->
### [《深度学习框架PyTorch：入门与实践》](https://github.com/chenyuntc/pytorch-book)
softmax分类器
fast rcnn

### [pytorch中文文档](http://pytorch-cn.readthedocs.io/zh/latest/package_references/torch-autograd/)


### 理解
torch.FloatTensor 相比于numpy，可以使用GPU
Variable 相比于Tensor，可以定义计算图，用于自动计算梯度

The biggest difference between the two is that TensorFlow’s computational graphs are static and PyTorch uses dynamic computational graphs.

TensorFlow是静态的计算图，而pytorch用动态计算图

### [Dogs vs. Cats Kaggle](https://www.kaggle.com/c/dogs-vs-cats/data)
[PyTorch实战指南6-知乎](https://zhuanlan.zhihu.com/p/29024978)
```
CUDA_VISIBLE_DEVICES=1 python my_script.py

CUDA_VISIBLE_DEVICES=0 python main.py train --train-data-root=./data/train/train --use-gpu=True --env=classifier

CUDA_VISIBLE_DEVICES=0 python main.py test --load-model-path=./checkpoints/resnet34_0426_17\:40\:25.pth

```

### 使用Jupyter notebook或IPython等交互式调试

### 二分类随机瞎蒙的loss在0.69左右
0.9这个loss很高   二分类随机瞎蒙的loss应该在0.69左右。

而且学习率太大了

建议用resnet 34  学习率 0.001  adam优化器