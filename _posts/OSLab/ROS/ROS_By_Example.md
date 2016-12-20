---
title:  ROS_By_Example笔记
date: 2016-10-13 20:35:26 # 文章生成时间，一般不改
categories:   # 文章分类目录，参数可省略

    - OSLab
    - ROS
tags:   # 文章标签，参数可省略
---

### 网址记录

* [Beginner](http://wiki.ros.org/ROS/Tutorials)

* [tf](http://wiki.ros.org/tf/Tutorials)

* [actionlib](http://wiki.ros.org/actionlib/Tutorials)

* [RViz User's Guide](http://docs.ros.org/indigo/api/rviz/html/user_guide/)

* [Browsing packages for indigo](http://www.ros.org/browse/list.php?package_type=package&distro=indigo)

* [catkin/CMakeLists.txt](http://wiki.ros.org/catkin/CMakeLists.txt)

* [ ROS中nodelet的使用](http://blog.csdn.net/zyh821351004/article/details/52143309)

* [launch文件介绍](http://blog.csdn.net/zqxf123456789/article/details/52497833)



<!--more-->

### 源码下载

```bash

cd ~/catkin_ws/src

git clone https://github.com/pirobot/rbx1.git

cd rbx1

git checkout indigo-devel

cd ~/catkin_ws

catkin_make

source ~/catkin_ws/devel/setup.bash

rospack profile

```



### TurtleBot机器人底盘的模拟

```bash

roscore

roslaunch rbx1_bringup fake_turtlebot.launch

rosrun rviz rviz -d `rospack find rbx1_nav`/sim.rviz



rostopic pub -r 10 /cmd_vel geometry_msgs/Twist '{linear: {x: 0.2, y: 0, z: 0}, angular: {x: 0, y: 0, z: 0.5}}'



rostopic pub -1 /cmd_vel geometry_msgs/Twist '{linear: {x: 0.2, y: 0, z: 0}, angular: {x: 0, y: 0, z: 0}}'; rostopic pub -r 10 /cmd_vel geometry_msgs/Twist '{linear: {x: 0.2, y: 0, z: 0}, angular: {x: 0, y: 0, z: 0.5}}'



rostopic pub -1 /cmd_vel geometry_msgs/Twist '{}'



rosrun rbx1_nav timed_out_and_back.py



rosrun rbx1_nav odom_out_and_back.py



rosrun rbx1_nav nav_square.py



#键盘或者手柄控制

sudo apt-get install ros-indigo-joystick-drivers \

ros-indigo-turtlebot-teleop



roslaunch rbx1_nav keyboard_teleop.launch



#虚拟摇杆

arbotix_gui



#Interactive Markers

sudo apt-get install ros-indigo-turtlebot-interactive-markers



roslaunch rbx1_bringup fake_turtlebot.launch



rosrun rviz rviz -d `rospack find rbx1_nav`/interactive_markers.rviz



roslaunch rbx1_nav interactive_markers.launch



```



### [SLAM Map Building with TurtleBot](http://wiki.ros.org/turtlebot_navigation/Tutorials/Build%20a%20map%20with%20SLAM)



### Testing move_base in the ArbotiX Simulator（路径规划）

```bash

roslaunch rbx1_bringup fake_turtlebot.launch

roslaunch rbx1_nav fake_move_base_blank_map.launch

rosrun rviz rviz -d `rospack find rbx1_nav`/nav.rviz



rostopic pub /move_base_simple/goal geometry_msgs/PoseStamped \

'{ header: { frame_id: "map" }, pose: { position: { x: 1.0, y: 0, z: 0 }, orientation: { x: 0, y: 0, z: 0, w: 1 } } }'



rostopic pub /move_base_simple/goal geometry_msgs/PoseStamped \

'{ header: { frame_id: "map" }, pose: { position: { x: 0, y: 0, z: 0 }, orientation: { x: 0, y: 0, z: 0, w: 1 } } }'



#动态修改参数

rosrun rqt_reconfigure rqt_reconfigure

```



### Navigating a Square using move_base（走正方形）

```bash

roslaunch rbx1_bringup fake_turtlebot.launch

roslaunch rbx1_nav fake_move_base_blank_map.launch

rosrun rviz rviz -d `rospack find rbx1_nav`/nav.rviz



rosrun rbx1_nav move_base_square.py

```



### Avoiding Simulated Obstacles（避障）

```bash

roslaunch rbx1_bringup fake_turtlebot.launch

rosparam delete /move_base



roslaunch rbx1_nav fake_move_base_map_with_obstacles.launch



rosrun rviz rviz -d `rospack find rbx1_nav`/nav_obstacles.rviz



rosrun rbx1_nav move_base_square.py

```



### Navigation and Localization using a Map and amcl（导航与定位）

```bash

roslaunch rbx1_bringup fake_turtlebot.launch



roslaunch rbx1_nav fake_amcl.launch map:=test_map.yaml



rosrun rviz rviz -d `rospack find rbx1_nav`/amcl.rviz

```



### Fully Autonomous Navigation（随机位置自动漫游）

```bash

roscore



rqt_console &



roslaunch rbx1_nav fake_nav_test.launch



rosrun rviz rviz -d `rospack find rbx1_nav`/amcl.rviz





所用节点：

/arbotix

/fake_localization

/map_server

/move_base

/nav_test

/robot_state_publisher

/rosout

/rviz_1476674751821829778



```