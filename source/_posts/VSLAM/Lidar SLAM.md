---
title:  Lidar SLAM
date: 2019-03-09 18:35:26 # 文章生成时间，一般不改
categories:   # 文章分类目录，参数可省略
    - VSLAM
tags:   # 文章标签，参数可省略
---
### LOAM_velodyne系列学习
loam中文代码注解

主要思路分为：1. 高频的运动估计； 2. 低频的环境建图。

<!--more-->

Lidar接收数据，首先进行Point Cloud Registration，Lidar Odometry以10Hz的频率进行运动估计和坐标转换，Lidar Mapping以1Hz的频率构建三维地图，Transform Integration完成位姿的优化。这样并行的结构保证了系统的实时性。

整个算法分为四个模块，相对于其它直接匹配两个点云的算法，LOAM是通过提取特征点进行匹配之后计算坐标变换。具体流程为：ScanRegistration 提取特征点并排除瑕点；LaserOdometry从特征点中估计运动，然后整合数据发送给LaserMapping；LaserMapping输出的laser_cloud_surround为地图；TransformMaintenance订阅LaserOdometry与LaserMapping发布的Odometry消息，对位姿进行融合优化。后面将详细进行说明。

ScanRegistration：特征点提取
一次扫描的点通过曲率值来分类，特征点曲率大于阈值的为边缘点；特征点曲率小于阈值的为平面点。为了使特征点均匀的分布在环境中，将一次扫描划分为4个独立的子区域。每个子区域最多提供2个边缘点和4个平面点。此外，将不稳定的特征点（瑕点）排除。

LaserOdometry：进行点云数据配准，完成运动估计
知乎讲解得很详细
利用ScanRegistration中提取到的特征点，建立相邻时间点云数据之间的关联，由此推断lidar的运动。
找到特征线和特征面，然后用L-M方法做优化。整个L-M运动估计的迭代次数为25次，以保证运算效率。迭代部分又可分为：对特征边/面上的点进行处理，构建Jaccobian矩阵，L-M运动估计求解。

LaserMapping：优化Lidar的位姿，在此基础上完成低频的环境建图
拿当前扫描的点云和地图中所有点云去配准，利用已构建地图对位姿估计结果进行修正，优化Lidar里程计的位姿估计结果。

TransformMaintenance：对位姿进行融合优化
每次接收到laser_odom_to_init消息并调用回调函数laserOdometryHandler时，就发布一次integrated_to_init消息。
有优化结果了就拿这一时刻的优化结果作为轨迹，没有优化结果只有里程计结果了，就直接拿里程计结果作为这一时刻的轨迹。

### LeGO-LOAM
它是一种轻量级和地面优化的激光雷达里程计和建图方法。LeGO-LOAM经过地面优化，因为它在分割和优化步骤中利用了地面的约束。我们首先应用点云分割来滤除噪声，并进行特征提取，以获得独特的平面和边缘特征。然后，采用两步Levenberg-Marquardt优化方法，使用平面和边缘特征来解决连续扫描中六个自由度变换的不同分量。

将位姿图SLAM集成到LeGO-LOAM中。 每个特征点集的传感器位姿可以被建模为位姿图中的节点。
LeGO-LOAM初探：原理，安装和测试
LeGO-LOAM学习

### Cartographer学习
Cartograhper的设计目的是为了解决在有限的计算资源下，能够实时地获取相对较高精度的2D地图，考虑到基于模拟策略的粒子滤波方法在较大环境时对内存和计算资源的高需求，Cartographer采用基于图网络的优化方法。精度5厘米左右

首先使用本地slam算法估计初始位姿，然后使用扫描匹配检测闭环，终于通过稀疏姿态图优化（sparse pose graph optimization，原理参见论文）算法优化整个位姿序列。

用Grid（2D/3D）的形式建地图；本地优化的局部匹配直接建模成一个非线性优化问题，利用IMU提供一个比较靠谱的初值；全局优化的后端用Graph来优化，用分支定界算法来加速；2D和3D的问题统一在一个框架下解决。

### gmapping 
粒子+扫描匹配。在里程计的基础上，在机器人周围位置尝试算匹配度。
[Rao-Blackwellized Particle Filters 论文参考](https://people.eecs.berkeley.edu/~pabbeel/cs287-fa12/optreadings/GrisettiStachnissBurgard_gMapping_T-RO2006.pdf)

[参考](https://blog.csdn.net/roadseek_zw/article/details/53316177)

[百度文库代码解析](https://wenku.baidu.com/view/3a67461550e2524de4187e4d.html)

    ROS 提供的 gmaping 包是用来生成地图的，它是对著名的开源 OpenSlam 包在 ROS 框架下的一个实现。
    这个包提供了对激光设备的 Slam,根据激光设备的输入和姿态数据从而建立一个基于网格的的2D地图。
    它需要从 ROS 系统监听许多 Topic，并输出一个 Topic——map(nav_msgs/OccupancyGrid)，
    这也是 RViz 的输入 Topic。

#### 已知精确位姿（坐标和朝向）的地图创建:
    机器人位置已知，通过激光雷达扫描到环境特征，即障碍物距离。
    可通过机器人坐标和朝向以及障碍物距离计算出障碍物的坐标，采用bresenham直线段扫面算法，
    障碍物所处的栅格标注为occupy，机器人所处的栅格与障碍物所处的栅格之间画直线，
    直线所到之处都为free。当然每个栅格并不是简单的非0即1，栅格的占据可用概率表示，
    若某一个栅格在激光束a扫描下标识为occupy，在激光束b扫描下也标识为occupy，
    那该栅格的占据概率就变大，反之，则变小。
    这样，机器人所处的环境就可以通过二维栅格地图来表征。

#### 如何在已知地图的情况下采用粒子滤波算法进行精确定位，
    一般包括以下几个步骤：
    （1）给定初始位姿，初始化粒子群，采用高斯分布进行随机采样；
    （2）根据运动模型模拟粒子运动；
    （3）计算粒子评分
            每个粒子的位姿即为假设的机器人位姿，采用bresenham直线段扫面算法，
            可计算出粒子当前位置与障碍物之间的栅格占据集合，
            计算出的栅格占据集合与给定的地图进行匹配计算，
            从而对每个粒子进行评分，选择得分高的粒子作为该时间点的机器人位姿。
    （4）粒子群重采样
            将评分低的粒子舍弃，将评分高且很接近的粒子都保留下来，并对评分高的粒子进行复制，保持粒子数量不变。

### fast slam 
粒子+卡尔曼

### hector slam 
扫描匹配。完全不用里程计，算匹配概率
