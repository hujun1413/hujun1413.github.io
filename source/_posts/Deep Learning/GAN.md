---
title: GAN
date: 2018-05-25 16:25:26 # 文章生成时间，一般不改
categories:   # 文章分类目录，参数可省略
    - Deep Learning
tags:   # 文章标签，参数可省略
    - GAN
---
### [简单理解与实验生成对抗网络GAN](https://blog.csdn.net/on2way/article/details/72773771)
[最简单易懂的GAN（生成对抗网络）教程：从理论到实践（附代码](https://www.leiphone.com/news/201706/ty7H504cn7l6EVLd.html)

生成网络
判别网络

单独交替迭代训练
<!--more-->
判别网络不仅要做出判断，还要告诉生成网络怎么改进会更好（即：判别网络将学习到的经验反向传递给生成网络）

DCGAN:将卷积神经网络和对抗网络结合起来

[WGAN](https://zhuanlan.zhihu.com/p/25071913):训练过程中终于有一个像交叉熵、准确率这样的数值来指示训练的进程，这个数值越小代表GAN训练得越好，代表生成器产生的图像质量越高

WGAN-GP:[Improved Training of Wasserstein GANs](https://arxiv.org/pdf/1704.00028.pdf)使用正则化（gradient penalty）而非 weight clipping的改进版WGAN
[pytorch implementation of WGAN-GP](https://github.com/caogang/wgan-gp)

LSGAN:最小二乘GAN

条件GAN：把generator换成一个image to image的网络，比如encoder-decoder和U-Net。
G网络从一个Image生成另一个image，比如：Loss是GAN Loss(尽量让D判成真)+L1 Loss(生成的和真实的一致性)；D网络从一个image判断是否为真实，Loss是将真判为真，生成的判为假

U-Net是将第i层拼接到第n-i层，这样做是因为第i层和第n-i层的图像大小是一致的，可以认为他们承载着类似的信息。

[pix2pixHD，比pix2pix更好](https://github.com/NVIDIA/pix2pixHD)

### 深入浅出理解GAN
[深入浅出 GAN](http://www.sohu.com/a/143961544_741733)

### 如何指导GAN的训练
[ganhacks](https://github.com/soumith/ganhacks)

### GAN用于分类


### 知乎
[生成式对抗网络GAN有哪些最新的发展，可以实际应用到哪些场景中？](https://www.zhihu.com/question/52602529)

[GAN 研究的应用方向应该如何选择？](https://www.zhihu.com/question/268725048)

[深度对抗学习在图像分割和超分辨率中的应用](https://zhuanlan.zhihu.com/p/25201511)

图像分割
视频后续帧预测
pix2pix风格变换
低分辨率到高分辨率图片
噪声生成图片
GAN生成深度图（单帧图像的深度估计）：先做分割，再在此基础上做深度估计，因为一个平面上的深度基本相同

给一张图和路线，就知道应该怎么导航。是向前还是后，左，右


