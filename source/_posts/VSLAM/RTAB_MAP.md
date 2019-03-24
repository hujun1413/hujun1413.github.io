---
title:  RTAB_Map
date: 2018-06-09 18:35:26 # 文章生成时间，一般不改
categories:   # 文章分类目录，参数可省略
    - VSLAM
tags:   # 文章标签，参数可省略
---
### RTAB是什么 
[RtabMap中文解析](http://remyspot.blog.51cto.com/8218746/1784914/)
[RTABMAP-ROS RGB-D的建图原理](http://www.cnblogs.com/severnvergil/p/6671879.html)
<!--more-->
RGB-D,可以换输入

通过sift关键点进行匹配，然后估计相机运动，最后通过闭环检测减少误差。

### [rtabMap](https://github.com/introlab/rtabmap)源码
RTABMap

问题可能主要在于 g2o and gtsam没有使用c++11的标准，而作者的工程使用的是c++11标准，所以出现了这个错误。 
而作者直接给出的解决办法是编译时不使用g2o and gtsam.但是整个工程中是怎么用这两个库的，可能还的看看源码。

cmake -DWITH_G2O=OFF -DWITH_GTSAM=OFF ..

app/src/main.cpp中new了一个MainWindow类
MainWindow.cpp：
4592行：_preferencesDialog这个dialog中获取注入colorOnly之类的参数

### ros上[rtabmap_ros](http://wiki.ros.org/rtabmap_ros?distro=lunar)包
安装rtabmap_ros包。

[rtabmap_ros的安装和使用](http://www.rosclub.cn/post-118.html)

[github上rtab_map的安装](https://github.com/introlab/rtabmap_ros#rtabmap_ros)

用debian方式安装
sudo apt-get install ros-indigo-rtabmap-ros 

debian卸载
sudo apt-get remove ros-indigo-rtabmap

安装rtabmap0.11.8版本，配套rtabmap_ros0.11.8-indigo版本。
编译rtabmap0.11.8遇到的问题：
[flann/util/serialization.h class std::unordered_map<unsigned int, std::vector<unsigned int> >' has no member named 'serialize'](https://stackoverflow.com/questions/42504592/flann-util-serialization-h-class-stdunordered-mapunsigned-int-stdvectorun)
解决方法：将include flann.hpp的地方都放在include opencv头文件的前面。

### 用奥比中光跑rtabmap_ros
[ros上跑奥比中光](https://github.com/tfoote/ros_astra_camera)
[奥比中光3D传感摄像头Ubuntu系统安装ROS说明](http://blog.csdn.net/suijideren/article/details/60876382)
[奥比中光Orbbec Astra Pro RGBD 3D视觉传感器在ROS（indigo和kinetic）使用说明 rgb depth同时显示](http://blog.csdn.net/zhangrelay/article/details/53515859)

```
roslaunch astra_launch astra.launch #启动奥比中光摄像头

roslaunch rtabmap_ros rtabmap.launch rtabmap_args:="--delete_db_on_start" rviz:=true rtabmapviz:=false　#启动rtabmap_ros，如果窗口一闪而过，先执行rtabmap，关闭rtabmap后重新执行这句即可。

rviz　#可以查看奥比中光摄像头产生的rgb图，深度图和三维点云

rtabmap-databaseViewer ~/.ros/rtabmap.db　#查看地图数据库

```

[ERROR] (2017-10-21 10:34:13.134) RegistrationVis.cpp:1156::computeTransformationImpl() Calibrated camera required (multi-cameras not supported). Id=221 Models=1 StereoModel=0 weight=0

### 用速感摄像头跑rtabmap_ros

### rtabmap启动的node
When launching rtabmap_ros's nodes, if you have the error error while loading shared libraries..., add the next line at the end of your ~/.bashrc to fix it:
`$ export LD_LIBRARY_PATH=$LD_LIBRARY_PATH:/opt/ros/kinetic/lib/x86_64-linux-gnu`
```
\<!-- RGB-D Odometry -->
rgbd_odometry src/RGBDOdometryNode.cpp

<!-- Visual SLAM (robot side) -->
rtabmap src/CoreNode.cpp

<!-- Visualisation RTAB-Map -->
rtabmapviz src/GuiNode.cpp src/GuiWrapper.cpp src/PreferencesDialogROS.cpp
或者选择rviz用于可视化

```

### rtabmap_ros-入门教程
[ROS与VSLAM入门教程-rtabmap_ros-深度相机手持建图](http://www.ncnynl.com/archives/201709/1992.html)

[使用投影地图,使用Handsfree进行三维建图导航](http://www.rosclub.cn/thread-25.html)
### rtab_map的优缺点
1. 在Tango上的表现体现出对动态场景不鲁棒。