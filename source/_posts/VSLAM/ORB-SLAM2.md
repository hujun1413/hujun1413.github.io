---
title:  ORB-SLAM2
date: 2018-11-09 18:35:26 # 文章生成时间，一般不改
categories:   # 文章分类目录，参数可省略
    - VSLAM
tags:   # 文章标签，参数可省略
---
### ORB-SLAM2

ROS版本：
```
sudo chmod 777 ORBvoc.txt
sudo chmod 777 TUM1.yaml
```
<!--more-->

话题名字为：/camera/rgb/image_color和/camera/depth/image，修改ROS/ORB_SLAM2/src/ros_rgbd.cc文件中的监听话题名，再./build_ros.sh。（编译可能出错）

（需要用opencv2.4.9来进行编译），即在CMakeList.txt中写成
find_package(OpenCV 2.4.9 EXACT REQUIRED)


#### ROS版本：
```
RGBD：
roscore
cd ~/hujun_file/ORB_SLAM2/Examples/ROS/ORB_SLAM2
rosrun ORB_SLAM2 RGBD ORBvoc.txt TUM1.yaml
rosbag play --clock hujun_file/bag/create_map/rgbd_dataset_freiburg1_xyz.bag

Stereo:
cd ~/hujun_file/ORB_SLAM2/Examples/ROS/ORB_SLAM2
rosrun ORB_SLAM2 Stereo Vocabulary/ORBvoc.txt Examples/Stereo/EuRoC.yaml true
rosbag play --pause ~/hujun_file/bag/MH_01_easy.bag cam0/image_raw:=/camera/left/image_raw /cam1/image_raw:=/camera/right/image_raw

Monocular:
rosrun ORB_SLAM2 Mono Vocabulary/ORBvoc.txt Examples/Monocular/EuRoC.yaml /cam0/image_raw #注意这里需要手动传入监听的图像话题

AR_Demo:
运行出错
rosrun ORB_SLAM2 MonoAR Vocabulary/ORBvoc.txt Examples/Monocular/EuRoC.yaml
rosbag play --pause ~/hujun_file/bag/MH_01_easy.bag cam0/image_raw:=/camera/image_raw

非ROS版本：
RGB-D:
./Examples/RGB-D/rgbd_tum Vocabulary/ORBvoc.txt Examples/RGB-D/TUM1.yaml ../rgbd_dataset_freiburg1_xyz ../rgbd_dataset_freiburg1_xyz/associations.txt

Stereo:
./Examples/Stereo/stereo_euroc Vocabulary/ORBvoc.txt Examples/Stereo/EuRoC.yaml ~/hujun_file/bag/mav0/cam0/data ~/hujun_file/bag/mav0/cam1/data Examples/Monocular/EuRoC_TimeStamps/MH01.txt

Monocular:
./Examples/Monocular/mono_euroc Vocabulary/ORBvoc.txt Examples/Monocular/EuRoC.yaml ~/hujun_file/bag/mav0/cam0/data Examples/Monocular/EuRoC_TimeStamps/MH01.txt
```

ORB-SLAM吴博注释版
ORBSlam2中的闭环检测和后端优化LoopClosing

#### 源码阅读记录
ORB-SLAM2详解（一）简介
ORB-SLAM2 程序解读
ORB-SLAM2详解（二）代码逻辑
RGB-D： 泡泡机器人ORB-SLAM2源码详解

##### 自动地图初始化
系统的第一步是初始化，ORB_SLAM使用的是一种自动初始化方法。这里同时计算两个模型：用于平面场景的单应性矩阵H和用于非平面场景的基础矩阵F，然后通过一个评分规则来选择合适的模型，恢复相机的旋转矩阵R和平移向量t。
##### 找到初始对应点
Tracking.cc 600行SearchForInitialization
###### 同时计算两个模型
调用Initializer.cc中的Initializer::Initialized函数进行初始化工作。同时计算H和F的线程，当场景是一个平面、或近似为一个平面、或者视差较小的时候，可以使用单应性矩阵H，而使用基础矩阵F恢复运动，需要场景是一个非平面、视差大的场景。 ORB_SLAM2关键算法分析1——基础矩阵F和单应矩阵H初始化位姿
##### 跟踪点在一个范围内做金字塔
每层金字塔的特征点数是有要求的，保证在特征点稠密的地方特征点不会太多，在特征点稀疏的地方特征点不会太少。

ORB-SLAM2之地图保存、加载和重定位

#### 将ORB_SLAM2用于导航
2D的栅格地图 或者 3D点云生成的octomap 适合用于机器人导航

首先运行ORB_SLAM2得到机器人的定位和一些识别出的路标。然后利用所选取的关键帧进行拼接得到点云地图，将点云转换成octomap即可用于导航
ORB_SLAM实时显示octomap
原始的orb-slam2只能显示稀疏点云，所以加了一个pcl_viewer和点云拼接的线程，来实现实时显示拼接关键帧的点云图像。

ORB_SLAM实时发布点云--高博的ORBSLAM2_with_pointcloud_map
编译安装高翔的ORBSLAM2_with_pointcloud_map，获取点云地图
ORBSLAM2_with_pointcloud_map-master通过RGBD跑TUM数据集
Octomap 在ROS环境下实时显示
#### Kinect2跑ORB_SLAM2
编译Kinect v2，跑通kinect2
标定：没标定过的kinect2，深度图和彩色图之间是不保证一一对应的。标定后得到kinect2彩色头、深度头、红外头的内参和外参。
编译orb-slam2，跑通orb-slam2
在Kinect2上运行orb-slam2
写一下kinect参数到yaml
直接跑orb的ros demo/修改kinect_viewer的代码
#### [利用ORB_SLAM2得到的路标点来得到可用于路径轨迹的地图](https://www.zhihu.com/question/68430075/answer/265121749)