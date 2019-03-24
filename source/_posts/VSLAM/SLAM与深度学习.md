---
title:  SLAM与深度学习
date: 2018-11-09 18:35:26 # 文章生成时间，一般不改
categories:   # 文章分类目录，参数可省略
    - VSLAM
tags:   # 文章标签，参数可省略
---
### [Tombone's Computer Vision Blog](http://www.computervisionblog.com/2016/01/why-slam-matters-future-of-real-time.html)

翻译如下：
[ICCV研讨会：实时SLAM的未来以及深度学习与SLAM的比较](http://blog.csdn.net/qq_18661939/article/details/51919329)

[当前深度学习和slam结合有哪些比较好的论文，有没有一些开源的代码?](https://www.zhihu.com/question/66006923)
<!--more-->
### slam与深度学习结合
[使用深度学习方法替换SLAM中的模块](https://blog.csdn.net/u010821666/article/details/78749356)

用深度学习方法替换传统slam中的一个/几个模块： 
目前还不能达到超越传统方法的效果，相较传统SLAM并没有很明显的优势（标注的数据集少且不全，使用视频做训练数据的非常少。SLAM中很多问题都是数学问题，深度学习并不擅长等等原因）。

[语义SLAM & 端到端](https://blog.csdn.net/u010821666/article/details/78793225)

在传统SLAM之上加入语义信息 
语义SLAM算是在扩展了传统SLAM问题的研究内容，现在出现了一些将语义信息集成到SLAM的研究，比如说用SLAM系统中得到的图像之间的几何一致性促进图像语义分割，也可以用语义分割/建图的结果促进SLAM的定位/闭环等，前者已经有了一些研究，不过还是集中于室内场景，后者貌似还没有什么相关研究。如果SLAM和语义分割能够相互促进相辅相成，应该能达到好的效果。

另：使用SLAM帮助构建大规模的图像之间有对应关系的数据集，可以降低深度学习数据集的标注难度吧，应该也是一个SLAM助力深度学习的思路。

1、利用学习的方法辅助几何方法。比如有学习的方法估计出深度信息，学习特征等等，特别是在几何法有时不行的时候。可以增强系统的鲁棒性。TUM实验室的CNN-Slam就是这个范畴，如果没记错的话。2、直接端到端学的，比如PoseNet，DeepVO，UnDeepVO3、语义Slam。不太了解，感觉机器学习做语义分割的很多，结合一下做语义Slam应该可以关联很多。

提高特征点稳定性（减少outlier）和自动提取不同层级的特征点（点、线、面、物体），
快速生成密集的地图（而非稀疏的三维点云）
结合语义信息和图像分割
生成动态地图（可以实时更新、表达动态物体）
降低SLAM调参的难度

将几何与深度学习结合。
[Have We Forgotten about Geometry in Computer Vision?](https://alexgkendall.com/computer_vision/have_we_forgotten_about_geometry_in_computer_vision/)
求解深度，求解两帧间变换，特征点描述和提取，重定位

[单目深度估计-深度学习](https://zhuanlan.zhihu.com/p/29864012)

[CNN-SLAM实时稠密单目SLAM](http://campar.in.tum.de/Chair/ProjectCNNSLAM)

### 论文
#### DeepVO: Towards End-to-End Visual Odometry with Deep Recurrent Convolutional Neural Networks
CNN和RNN结合，实现了一个端到端的VO
听说复现的效果不好，需考虑

我的改进idea：将Deep RNN换成更好的注意力模型

[注意力机制介绍](http://www.dataguru.cn/article-12415-1.html)

