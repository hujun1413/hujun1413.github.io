---
title: 重要VSLAM系统
date: 2018-11-09 18:35:26 # 文章生成时间，一般不改
categories:   # 文章分类目录，参数可省略
    - VSLAM
tags:   # 文章标签，参数可省略
---
### [SLAMCN](http://www.slamcn.org/index.php/%E9%A6%96%E9%A1%B5)

### [SLAM是什么？它的未来在哪里？](http://blog.sina.com.cn/s/blog_61b61d9d0102wnf2.html)

SLAM是技术，手机端的话涉及VIO

RGBD,Lidar,imu,odom,gps。基于这些传感器做一个组合SLAM。涉及：前端的tracking和后端的优化
多传感器融合是趋势，视觉结合imu等传感器。
<!--more-->

### 总结归纳
直接法的缺点：
怕模糊（需要全局曝光相机）
怕大运动（图像非凸性）
怕光照变化（灰度不变假设）

直接法的优点：
速度快
可稠密建图

### 视觉SLAM系统
单目SLAM:     
1. PTAM 2007 很牛逼的工作，论文中提出的关键帧以及多线程处理思想成为之后视觉slam标配；甚至能够实时运行在手机上。不少AR是基于ptam的；     
2. DTAM 2011 direct  slam方法的鼻祖；    
3. LSD-SLAM 2014年，将直接法应用到了半稠密的单目SLAM中。
4. ORB-SLAM 2015年，代表着主流的特征点SLAM的一个高峰，属于稀疏建图。

RGBD:     
1. KinectFusion 2011     
2. RGBD-SLAM2 2014     
3. ElasticFusion  2015
4. RTAB-MAP  RGB-D在CPU上实现实时稠密地图

以单目slam为例，SLAM可分为前端和后端；前端主要负责tracking相机的姿态位置，并筛选关键帧；而后端则主要负责姿态图的优化。而优化的方法多采用G2O或者SBA。

### [一起做RGB-D SLAM](http://www.cnblogs.com/gaoxiang12/p/4719156.html)
机器人会议：IJRR/TRO/RAM/JFD/ICRA/IROS/ROBIO

### 主流的VSLAM
视觉稀疏法：ORB-SLAM 、PTAM、MonoSLAM

半稠密法：LSD-SLAM、DSO、SVO

稠密法：DTAM、Elastic Fusion、Kintinous、DVO、RGBD-SLAM-V2、RTAB-MAP、MLM等

激光：Hector SLAM、Gmapping、tinySLAM

后端优化：g2o、ceres、GTSAM

数据：RGB-D SLAM Dataset and Benchmark、KITTI Vision Benchmark Suite、The EuRoC MAV Dataset等。

直接法：SVO、LSD-SLAM

#### MonoSLAM(2003-2007)
2007年，A.J.Davison教授提出的第一个实时的单目视觉SLAM系统。扩展卡尔曼滤波为后端，追踪前段非常稀疏的特征点。

缺点：
* 应用场景很窄
* 路标数量有限
* 特征点稀疏非常容易丢失
* 开发已经停止

#### PTAM(2007-2008)
2007年，Klein等人提出。基于FAST角点

优点：
* 跟踪和建图的并行化
* 第一个使用非线性优化。
* 同时是一个增强现实的软件

缺点：
* 缺乏回环检测和重定位
* 场景小，跟踪容易丢失

#### ORB-SLAM(2014-2015)
2015年，代表着主流的特征点SLAM的一个高峰，属于稀疏建图。

优点：
* 支持单目，双目，RGB-D。
* 围绕ORB特征进行计算，包括视觉里程计和回环检测的ORB字典。
* 回环检测是亮点，但要加载很大的ORB字典。
* 创新式地使用了三个线程：实时跟踪的Tracking，局部BA(小图)，全局Pose Graph的回环检测和优化(大图)。
* 围绕特征点进行了不少的优化。保证了特征点的均匀分布，在优化位姿时使用了一种循环优化4遍以得到更多正确匹配的方法，比PTAM更为宽松的关键帧选取策略。

缺点：
* 每副图像计算一遍ORB特征，非常耗时。
* 三线程结构也给CPU带来了较重的负担，移植到嵌入式设备上有一定的困难。
* 建图为稀疏特征点，没有开放存储和读取地图后进行重新定位的功能。
* 稀疏特征点地图只能满足定位要求，无法提供导航，避障，交互等功能，因此仅用ORB-SLAM来处理定位问题太重量级。

#### LSD-SLAM(2013-2014)
2014年，J.Engle等人提出。标志着单目直接法的成功应用，将直接法应用到了半稠密的单目SLAM中。

优点：
* 直接法是针对像素进行的。有创见地提出了像素梯度与直接法的关系，以及像素梯度与极线方向在稠密重建中的角度关系。
* 在CPU上实现了实时半稠密场景的重建。
* 不是利用单个像素，而是在极线上等距离取5个点，度量其SSD。用了很多精妙的手段来保证追踪的实时性和稳定性。
* 直接法的优点：对特征缺失区域不敏感

缺点：
* 对相机内参和曝光非常敏感，在相机快速运动时容易丢失
* 没有基于直接法的回环检测。因此必须依赖于特征点方法来进行回环检测，尚未摆脱特征点的计算。

#### SVO(Semi-direct Visual Odoemtry，半直接视觉里程计)(2014)
2014年，Forster等人提出。基于稀疏直接法的视觉里程计。特征点和直接法混合：跟踪一些关键点（角点，没有描述子），然后像直接法那样将关键点周围4×4的小块进行块匹配，来估计相机自身的运动。

SVO 2.0 2016年：
C. Forster, Z. Zhang, M. Gassner, M. Werlberger, and D. Scaramuzza.
SVO 2.0: Semi-direct visual odometry for monocular and multicamera
systems. TRO, 2016.

优点：
* 速度极快。在PC平台上能达到每秒100多帧，后续的SVO 2.0能达到每秒400帧。
* 非常适用于无人机，手持AR/VR设备这些计算平台受限场合的定位
* 创新之处：提出了深度滤波器的概念，并推导了基于均匀－高斯混合分布的深度滤波器。
* 代码清晰易读

缺点：
* 目标应用平台是无人机的俯视相机，所以在平视相机中表现不佳
* 为了速度和轻量化，舍弃了后端优化和回环检测，基本没有建图功能。所以位姿估计存在累计误差，丢失后不太容易进行重定位，所以SVO只是个VO。

#### DSO(直接稀疏VO，2016)
DSO [13]是LSD-SLAM的作者Jakob Engel放出的另一个大杀器，从其展示的实验结果看，无论是robustness，或是accuracy，或是计算速度，都完爆LSD-SLAM和ORB-SLAM，上个月又放出了code。

#### RTAB-MAP
RGB-D在CPU上实现实时稠密地图。

支持常见的RGB-D和双目传感器，提供实时的建图和定位功能

集成度较高，进行二次开发比较困难，更适合作为SLAM应用而非研究使用

#### DVO-SLAM（RGBD）
TUM的基于RGBD camera的VO方法 

#### RGBD-SLAM-V2

#### Kinect Fusion

#### Google Cartographer

#### DTAM(RGBD)

#### Elastic Fusion(RGBD 2015)

#### Kintinous(RGBD)

#### FAB-MAP
