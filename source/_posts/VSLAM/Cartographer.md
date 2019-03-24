---
title:  Cartographer
date: 2018-04-09 18:35:26 # 文章生成时间，一般不改
categories:   # 文章分类目录，参数可省略
    - VSLAM
tags:   # 文章标签，参数可省略
---
### [Cartographer学习](https://www.cnblogs.com/yebo92/p/6266717.html)

[谷歌 slam 地图库-cartographer-源码分析](https://zhuanlan.zhihu.com/p/27631583)

[cartographer和karto-slam](https://blog.csdn.net/zyh821351004/article/details/52421005)

<!--more-->

[cartographer算法理解](http://www.luyixian.cn/news_show_3265.aspx)

[算法整体结构](https://blog.csdn.net/u013721521/article/details/81477005)

[Cartographer 代码阅读分析](https://blog.csdn.net/roadseek_zw/article/details/66968762)

首先使用本地slam算法估计初始位姿，然后使用扫描匹配检测闭环，终于通过稀疏姿态图优化（sparse pose graph optimization，原理参见论文）算法优化整个位姿序列。

如果走丢了（定位失败），算法里似乎没有恢复的方法，不能全局定位，它写了个PerformGlobalLocalization，但是没看见使用；

#### 前端
catographer利用IMU构建预测模型，ScanMatcher与Odom（可选）构建观测模型，采取UKF进行运动预测。 

通过一次角速度和加速度的prediction得到当前时刻的pose的估计。
使用和loop closure相同的匹配方法得到一个pose。
imu预测的pose和loop closure匹配方法得到的pose融合后得到一个coarse的pose。

#### 后端
Local SLAM部分获得了很多Submap.

ScanMatcher部分：cartographer采取的双搜索方式进行，先用一次real-time correlative scan matcher（三维窗口遍历寻优），再构建优化等式，利用Ceres优化求解。

本地优化里调用了一个重要的函数LocalTrajectoryBuilder::ScanMatch（），这个函数首先对激光数据进行了投射处理、栅格化滤波，然后实时在线优化real_time_correlative_scan_matcher_.Match，这个优化是可选的，优化方式比较简单，就是在之前预测位姿附近开一个三维窗口，然后在这个三维窗口内，均匀播撒候选粒子，对每个粒子计算匹配得分，选取最高得分的粒子作为优化的位姿。该优化的位姿还要传入一个ceres_scan_matcher_.Match(）在子地图中完成局部优化，最终得到本地优化后的位姿。ceres_scan_matcher_.Match()是将局部地图的scan matching作为一个二次型优化问题，由ceres slover解决

#### 全局图优化
closing loop， 采用了 SPA（Sparse Pose Adjustment）进行后端loop closure。这个过程中有一个很重要的过程是的scan和submap的匹配，这里采用了BBS（Branch-and-bound scan matching）， 利用分支定界进行全局的闭环检测完成优化，它可大幅提高精度和速度。

### [官网安装教程-Cartographer ROS Integration](https://google-cartographer-ros.readthedocs.io/en/latest/)
```bash
# Install wstool and rosdep.
sudo apt-get update
sudo apt-get install -y python-wstool python-rosdep ninja-build

# Create a new workspace in 'catkin_ws'.
mkdir catkin_ws
cd catkin_ws
wstool init src

# Merge the cartographer_ros.rosinstall file and fetch code for dependencies.
#wstool merge -t src https://raw.githubusercontent.com/googlecartographer/cartographer_ros/master/cartographer_ros.rosinstall
#wstool update -t src

由于ceres-solver的地址为https://ceres-solver.googlesource.com/ceres-solver，不翻墙下载不到，所以创建一个名字为a.rosinstall的文件，内容为：
- git: {local-name: cartographer, uri: 'https://github.com/googlecartographer/cartographer.git'}
- git: {local-name: cartographer_ros, uri: 'https://github.com/googlecartographer/cartographer_ros.git'}
- git: {local-name: ceres-solver, uri: 'https://github.com/ceres-solver/ceres-solver.git', version: '1.13.0'}

所以执行：
wstool merge -t src a.rosinstall
wstool update -t src

# Install proto3.
src/cartographer/scripts/install_proto3.sh

# Install deb dependencies.
# The command 'sudo rosdep init' will print an error if you have already
# executed it since installing ROS. This error can be ignored.
sudo rosdep init
rosdep update
rosdep install --from-paths src --ignore-src --rosdistro=${ROS_DISTRO} -y

# Build and install.
catkin_make_isolated --install --use-ninja
source install_isolated/setup.bash
```

安装cartographer遇到Unrecognized syntax identifier "proto3". This parser only recognizes "proto2"问题
需要将protobuf从2.x换成3.x版本
```
protoc --version，如果输出是libprotoc 2.6.0
which protoc，如果输出是/usr/local/protoc，则执行下面的：
sudo mv /usr/bin/protoc  /usr/bin/protoc.bk 

重新编译protobuf，然后protoc --version输出得到libprotoc 3.4.0即可
然后执行which protoc
输出：/usr/local/bin/protoc
最后执行
$ sudo ln -s /usr/local/bin/protoc /usr/bin/protoc
```


[catkin_make_isolated --install --use-ninja遇到undefined reference to google::protobuf::internal::ArenaImpl::AddCleanup(void*, void ()(void))'问题](https://github.com/googlecartographer/cartographer/issues/1395)
```
删除build，devel，install文件夹，重新编译
catkin_make_isolated --install --use-ninja

source install_isolated/setup.bash
```


将
```
将CMakeLists.txt中的find_package(Eigen3 REQUIRED)
替换成：
include_directories( "/usr/include/eigen3" )
#find_package(Eigen3 REQUIRED)

因为Eigen3全是头文件，所以可以直接在CMakeLists.txt中添加eigen3的路径
```

### apt-get安装
```
sudo apt install ros-indigo-cartographer-ros
```

### [cartographer_turtlebot](https://github.com/googlecartographer/cartographer_turtlebot)
为什么要使用cartographer_turtlebot，而不是直接用cartographer_ROS

除了多出几个turtlebot的配置文件省去你调参的麻烦以外，cartographer_turtlebot还多了flat_world_imu_node这个函数，官方的解释是'imu_data_raw' topic传递的Kobuki的IMU信息因为驱动的问题会导致乱顺序，这个函数去掉了乱序的信息。

有个开关叫TRAJECTORY_BUILDER_2D.use_imu_data，如果不用imu的话，构图的时候一定要移动的很慢、很慢，使用了imu就可以让小车飞快的跑。在cartographer_turtlebot中能找到使用方法