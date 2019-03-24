---
title:  SFM Revisited--Colmap
date: 2019-03-09 18:35:26 # 文章生成时间，一般不改
categories:   # 文章分类目录，参数可省略
    - VSLAM
tags:   # 文章标签，参数可省略
---
### [泡泡机器人-重新认识SfM (CVPR-16)](https://www.sohu.com/a/199305001_715754)

是著名SFM开源系统COLMAP的稀疏三维重构部分的论文。

增量式SfM是从无序图像集合中进行三维重建的常用方法。 虽然增量式SfM在所有方面都取得极大的进展，但在鲁棒性，准确性，完整性和可扩展性方面离真正的实用还有一定的差距。 

<!--more-->

#### correspondence search
包含特征提取->特征匹配->几何验证，得到的是verified pairs C and a graph of image projections for each point

#### Incremental reconstruction
输入是scene graph，输出是位姿P和重建场景X

包含初始化->图像注册->三角化->BA

主要挑战：
* scene graph不完整
* image registration失败

### 主要贡献
主要有五点贡献：

1. 引入几何验证策略来标记场景图中的边的类型，以此提高初始化和三角化的鲁棒性，从而增强了scene graph

2. Next best view selection：评价标准为三角化出来的点多并且均匀分布

3. 一种计算成本更低，且鲁棒性更优的三角化方法：sampling-based triangulation，采用RANSAC且满足重投影小于阈值

4. 迭代BA，再三角化和异常值滤波策略能够减小累计误差，从而显着提高重建完整性和准确性。每次进行局部BA,当模型变大时才进行全局BA，全局BA之后再进行重三角化

5. 从无序图像集合挖掘相似视图，使之成组，从而减小BA计算量，提高BA准确性

#### 1.增强场景图
一般的方法不对图像类型进行区分，初始化和三角化的鲁棒性不高

选择合适的矩阵，用GRIC这样的方法。

根据NE,NH,NF的内点数，判断是一般的精确标定图，还是适合单应矩阵的图。

根据三角化的角度判断是全景图还是平面图。

根据在边缘处的内点数判断是WTF图（水印，时间戳，frames图）

初始化只选非全景图和精确标定的图。
三角化时不选全景图？为什么？不能用H矩阵吗

#### 2. 选取下一帧最好的图像
一般的方法只取三角化出来的可视点的个数，这样选出来的图还是很多，因为很多图都能看到相同的结构。所以还要考虑可视点的分布。

uncertainty-driven的方法

三角化出来的点多且分布均匀

用了一个栅格的数据结构，统计图片的得分

#### sampling-based 三角化
bundler采样所有的track elements的组合，再执行双视三角化，当三角化角度够大，再执行多视三角化，当所有的观测点都通过cheirality检查时接受三角化点。

分解R和t有4种情况，其中通过cheirality检查的作为内点，。

因此bundle无法将孤立的点恢复到一个track中，而且穷举的计算量太大

基于RANSAC的采样方法，满足三角化的角度足够大，三角化点在a,b两个相机的深度为正，且重投影误差小于阈值t。

RANSAC的优化：LO-RANSAC

#### 迭代BA
在图像注册和三角化之后执行局部BA，当模型增长到一定比例时进行一次全局BA。

参数：局部BA用Cauthy函数作为鲁棒的loss函数。当几百个相机时用稀疏直接求解器（sparse direct solver），当相机规模更大时，用PCG。对于网络图像，还给定一个径向畸变参数作为自标定的估计。

迭代BA，再三角化和异常值滤波策略能够减小累计误差，从而显着提高重建完整性和准确性。

每次进行局部BA,当模型变大时才进行全局BA

全局BA之前和之后都进行重三角化，为了提高重建的完整度。保留了低于观测误差阈值的点，而不是增大三角化的阈值。

迭代局部BA。一般2次就够了，BA后过滤，再次迭代

#### 从无序图像集合挖掘相似视图
使之成组，从而减小BA计算量，提高BA准确性

### [COLMAP documentation](https://colmap.github.io/)

#### 图像匹配的策略
[feature matching and geometric verification](https://colmap.github.io/tutorial.html):Exhaustive Matching, Sequential Matching, Vocabulary Tree Matching等

穷举匹配（Exhaustive Matching）：当你的数据集比较小的时候（小于100张），选择这种模式。这种模式匹配速度足够快并且是最好的效果。每张图片都与其他的所有图片进行匹配。最后生成的块大小取决于你载入内存中的图片的数量。

顺序匹配（Sequential Matching）：当你拍照的时候是以连续的方式（比如视频）采集图像的话，这种方式是有用的。连续帧具有视觉重叠。每一帧的图像不必和其他所有图像都进行匹配。只有连续帧匹配就OK了。该模式也有内置的循环检测，第N个图像与其视觉上最为相似的图片匹配。但需要事先训练字典树。https://demuc.de/colmap/ 在这里可以下载。此种方法非常适合在视频序列中大量采用的情况，相比于全局匹配模式大大降低了matching时间。

字典树匹配（Vocabulary Tree Matching）：在这种匹配模式下，每张图片都与与他视觉上最相近的经过按空间顺序重新排序的邻居匹配。也需要实现训练好字典树，网页地址同上。

空间匹配（Spatial Matching）：这种模式下每个图像均与其空间最近邻居进行匹配。可以在数据库中手动设置空间位置。默认情况下colmap还会从EXIF中获取GPS的相关信息并用来寻找空间上最相近的邻居。如果有准确的先验空间位置信息的话，这种是推荐的匹配模式。

传递匹配（Transitive Matching）：这种匹配模式使用已经存在的特征匹配的传递关系来完成更完整的匹配图。例如如果图像A匹配B而B也匹配C，那么colmap将尝试直接匹配A到C。

自定义匹配（Custom Matching）：这种模式下允许指定的单个图片去匹配其他图片或者导入指定的已匹配的信息。要指定图像对的话，必须提供一个文本文件如下（每行一对一）：


### [COLMAP github](https://colmap.github.io/)