---
title:  Lidar Visual SLAM
date: 2019-03-09 18:35:26 # 文章生成时间，一般不改
categories:   # 文章分类目录，参数可省略
    - VSLAM
tags:   # 文章标签，参数可省略
---
### V-LOAM
VLOAM ：Visual-lidar Odometry and Mapping: Low-drift, Robust, and Fast

别人已开源，https://github.com/daobilige-su/loam_velodyne
视觉里程计用DEMO，论文见https://ri.cmu.edu/pub_files/2014/9/IROS_2014.pdf，核心思想是用深度信息来辅助视觉里程计，有深度时可以不需要三角化。用iSAM做因子图优化
雷达里程计用LOAM，高频运动估计，低频L-M非线性优化和建图。

<!--more-->
The result is that the visual odometry handles rapid motion, and the lidar odometry warrants low-drift and robustness in undesirable lighting conditions.

V-LOAM uses images to compute motion prior for scan matching, while LOAM only processes laser data.
视觉可以为雷达获得更好的点云深度，视觉也可以为雷达提供扫描匹配的运动先验

深度图指的是利用雷达的点云和vo得到的运动估计不断注册，得到一个合并后在相机坐标系下的大点云，可以用这个点云来帮助获取特征点的深度。
特征点的距离是取深度图中离该点最近的3个点构成的平面，求光心到平面的距离作为该特征点的距离。如果在深度图中没有该特征点的深度，则可以根据特征匹配的结果三角化出来3D点

V-LOAM 源码解析（一）
节点名称：featureTracking 
订阅topic：<sensor_msgs::Image>（"/camera/image_raw"）
发布topic：1、<sensor_msgs::PointCloud2> ("/image_points_last"）
          2、<sensor_msgs::Image>("/image/show")

节点：visualOdometry
功能：订阅"/image_points_last"和"/depth_cloud"消息，将图像特征与三维点云匹配，获得图像特征点深度值，然后采用非线性优化的方法迭代收敛得到两帧图像之间的变换矩阵，即融合了三维点云信息的视觉里程计。

节点：processDepthmap
功能：根据视觉里程计topic("/cam_to_init")，和点云数据（"/sync_scan_cloud_filtered"），将新点云和已有点云变换到当前相机坐标系下，实现图像与点云数据的对齐

节点：stackDepthPoint
功能：将视觉里程计中用于定位的特征点三维坐标分批存储，用于后面的局部BA优化

节点：bundleAdjust
功能：对视觉里程计获得的odom信息进行局部BA优化，获得精度更高的odom信息

节点：transformMaintenance
功能：视觉里程计信息帧率较高，BA优化帧率较低，将二者结合，利用BA优化的结果修正视觉里程计，可以得到帧率和精度都较高的里程计信息

节点：registerPointCloud
功能：根据最终的里程计信息将三维激光的点云加入到odom坐标系中，获得点云地图

### VI-LOAM
imu和视觉可以进行pose约束（预积分的两帧间位姿可以和视觉得到的r,t做约束），视觉可以进行camera的重投影约束（雷达可以给特征点提供已有的深度，如果雷达没深度可以三角化出深度，没有深度的特征也有一种约束方式）

The motion estimation is to solve an optimization problem combining three sets of constraints: (a) From features with known depth as (6) and (7); (b) from features with unknown depth as (8); and (c) from the IMU prediction.

### LIMO
已开源，github见https://github.com/johannes-graeter/limo
论文见https://arxiv.org/pdf/1807.07524.pdf
在本研究中，我们提出了一种针对摄像机特征轨迹的激光雷达深度提取算法，并利用基于鲁棒关键帧的BA算法来估计运动。语义标记用于植被地标的离群剔除和加权，该传感器组合的能力在具有竞争力的KITTI数据集上得到了验证，排名在前15位。

核心思想：用语义去剔除在动态物体上的特征点。在特征点在一个平面上时，比如在道路上时，可以根据平面检测的结果，得到特征点更好的深度信息。然后优化方程为：3d-3d和3d-2d的重投影误差

没有和vloam一样使用icp，是为了体现视觉里程计和深度信息的融合。
LIMO is thereforethe second best LIDAR-Camera method published and the best performing method that does not use ICP based LIDAR- SLAM as refinement.
相对于纯vslam，结合雷达信息就是可以把雷达的点云准换到相机坐标系下，因此可以为vslam提供点云。视觉也可以为雷达获得更好的点云深度。

特征提取和预处理：
feature tracking methodology, implemented in the viso2 library [7]
reject landmarks lying on moving objects，在语义图像中把属于动态物体的feature剔除

尺度估计：
In order to estimate scale (Block S), depth corresponding to detected feature points is extracted from the LIDAR，从雷达的电云中提取出特征点的深度
直接从一张图中进行深度的估计

先选择一个ROI记作f，然后把投影在图像的区域f上的雷达点云作为F点云。
把F点云进行分割前景，得到Fseg点云：只选择一部分点云来降低优化问题的规模
在Fseg中得到平面p，如果f在地面上，就应用一个特殊的匹配算法
根据f的投影线和平面p的交点求出f的深度：To accurately estimate the depth of f on the road, we rather segment points corresponding to the ground plane than to the foreground.因为特征点的深度可能不平，所以在特征点附近求出一个地面的方程，然后只取高度离平面一定范围内的点的深度。
f投影线和平面p的法线的角度要小于某个阈值


### Elastic Lidar Fusion论文
泡泡机器人翻译