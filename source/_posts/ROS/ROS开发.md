---
title:  ROS开发
date: 2017-07-18 20:35:26 # 文章生成时间，一般不改
categories:   # 文章分类目录，参数可省略
    - ROS
tags:   # 文章标签，参数可省略
---
## 环境配置
### Linux系统
参考[ROS与C++入门教程-搭建开发环境(QT+ros_qtc_plugin)](http://www.ncnynl.com/archives/201701/1277.html)

<!--more-->

## 将ROS代码和业务逻辑代码分离开 
包含自己写的roshelper.hpp库即可，里面包含创建发布器，订阅器和定时器。
自己写一个继承RosHelper类的子类，然后在初始化函数中调用RosHelper的函数来创建发布器，订阅器和定时器；然后将发布消息进行封装，以后的发布消息都调用封装后的函数即可。

## [tf变换的使用](http://www.guyuehome.com/355)
[TF,base_link, map,odom和odom 主题的关系](http://blog.csdn.net/crazyquhezheng/article/details/43346907)
[ROS的坐标系TF](http://blog.csdn.net/EAIBOT/article/details/51542721)
[关于turtlebot 中tf tree的理解](http://blog.csdn.net/light_jiang2016/article/details/55517129)

odom->base_link的坐标关系是由里程计节点计算并发布的，相当于底盘做了一个粗略的定位，得到了机器人在odom坐标系中的位置。 /odom消息是由里程计发出来的，代表底盘的粗略定位，消息中的pos是指底盘算出来的机器人在odom坐标系中的位置。


map -> base_link的坐标关系是由定位节点计算出来，但并不发布，而是利用接收odom->base_link的坐标关系，计算出map->odom的坐标关系（这就相当于定位节点修正了底盘定位的误差，得到修正后机器人在map坐标系下的误差），然后发布。

有了底盘的odom->base_link和定位建图节点的map->odom，就可以知道map->base_link的坐标变换，也就是得到机器人在map坐标系中的位置，一般认为map坐标系是真实世界的坐标系，所以就得到了机器人在真实世界中的定位。
同时定位建图节点也建出了一张map坐标系下的地图，用于可视化和在真实世界中进行导航。

建图定位节点接收/odom的数据，然后用SLAM方法来进行定位修正和建图，用以消除底盘粗略定位的累计误差。建图定位节点可以得到map->base_link的坐标变换，可以得到机器人在map坐标系中的位置。

```
tf tf_echo /base_link /map
输出：
At time 1527845513.918
- Translation: [-1.782, -0.758, -0.017]
- Rotation: in Quaternion [0.000, 0.000, 0.548, 0.837]
            in RPY (radian) [0.000, -0.000, 1.159]
            in RPY (degree) [0.000, -0.000, 66.392]

说明：/map所在的坐标系在地面上(origin_x, origin_y, 0)位置
```

首先，编写一个用来发布tf tree的广播节点，然后编写订阅tf广播的节点，即可在订阅端利用tf进行坐标变换
### tf广播
```
tf::TransformBroadcaster broadcaster;
 broadcaster.sendTransform(
      tf::StampedTransform(
        tf::Transform(tf::Quaternion(0, 0, 0, 1), tf::Vector3(0.1, 0.0, 0.2)),
        ros::Time::now(),"base_link", "base_laser"));
```

### 订阅tf的节点
```
tf::TransformListener listener;
sleep(1); //wait for receive message
geometry_msgs::PointStamped base_point;
    listener.transformPoint("base_link", laser_point, base_point);
```

## 格式化输出
DEBUG和INFO 信息是输出到标准输出cout（或stdout）；而WARN、ERROR和 FATAL会输出到错误信息输出cerr（或stderr）。

DEBUG为绿色， INFO为白色， WARN为黄色， ERROR为红色，以及FATAL为紫色。

* ROS_DEBUG_STREAM
* ROS_WARN_STREAM
```
ROS_WARN_STREAM(
    "My INFO stream message with argument: " << val
);
```
* ROS_INFO_STREAM
```
ROS_INFO_STREAM("parameter:" << std::endl
<< "        base_name: " FG_CYAN << base_name << NO_COLOR << std::endl
<< "     base_name_tf: " FG_CYAN << baseNameTF << NO_COLOR << std::endl
<< "   worker_threads: " FG_CYAN << worker_threads << NO_COLOR);
```
* OUT_ERROR_STREAM
```
OUT_ERROR_STREAM("could not close device!");
```

## 打断机制的实现
在消息的回调函数中，判断为何种指令，然后调用另一个类中的记录函数，记录又来了一个新的消息。在这个记录函数中可以开启一个专门的线程来做消息所代表的任务，保证在消息的回调函数执行时间足够短。在类中可以用一个消息队列来缓冲消息（不打断当前任务，当前执行完后再执行队列中的任务），也可以直接打断当前的指令（打断当前任务，即在当前任务中要知道是否有新的任务来）。

以上专门开一个线程来处理消息的好处：如果主线程用来监听所有的topic，当来了一个需要处理很久的消息时，在处理消息过程中又来了很多频率很快的消息，这时缓冲消息的队列一旦满了就会丢失之后的消息。因此，最好将回调函数中的代码效率进行优化，实在优化不了就开一个线程处理接收的消息。

## ROS gtest
[ROS UnitTesting](http://wiki.ros.org/UnitTesting)

[rostest/Writing](http://wiki.ros.org/rostest/Writing)

[ROS gtest](http://wiki.ros.org/gtest)

[ ROS rostest tool使用](http://blog.csdn.net/x_r_su/article/details/53184871)



/tools/rosconsole/test
/test/test_roscpp/test/test_poll_set.cpp

直接在自己的包里建一个test子目录，里面写一个gtest的测试用例shm_transport_test就可以了

```
if(CATKIN_ENABLE_TESTING)
  find_package(rostest REQUIRED)
  catkin_add_gtest(shm_test shm_test/shm_test.cpp)
  if(TARGET shm_test)
    target_link_libraries(shm_test ${catkin_LIBRARIES})
  endif()
endif()
```

编译所有test:
`catkin_make tests`

编译运行所有test:
`catkin_make run_tests`

编译指定的某个test
`catkin_make tests <package name>`

编译运行指定的某个test
`catkin_make run_tests <package name>`

```
make run_test shm_test
= make tests shm_test + rosrun shm_transport shm_test 
= make tests + ./devel/lib/shm_transport/shm_test
```

## [安装ros_comm后roscore出错](http://blog.exbot.net/archives/941)
roscd rosout，发现进入到刚编译好的~/catkin_ws_hujun/src/ros_comm里，而不是/opt/ros/indigo

方法一（已测试，可行）：
1. 删除~/.bashrc中的source ~/catkin_ws_hujun/devel/setup.bash
2. 重新打开一个终端（此时.bashrc让catkin_ws_hujun不在环境变量中），进入~/catkin_ws_hujun/src
3. 删除ros_comm，同时删除所在workspace的build和devel文件夹，重新执行catkin_make，编译通过
4. 在~/.bashrc中的重新加入source ~/catkin_ws_hujun/devel/setup.bash，执行roscore即可成功

方法二（未测试）：
1. source /opt/ros/indigo/setup.bash
2. 进入~/catkin_ws_hujun/src，删除ros_comm，同时删除所在workspace的build和devel文件夹，重新执行catkin_make，编译通过
3. 打开另一终端，执行roscore即可成功

## 对象和constptr或ptr直接互相转换
### 对象转constptr
```
nav_msgs::OccupancyGrid a;
a.info.origin.position.x = 1.2;

nav_msgs::OccupancyGridConstPtr b = nav_msgs::OccupancyGridConstPtr(&a);
//b的地址就是a的地址，如果a被销毁，b的内容也将不复存在

double c= b->info.origin.position.x;
printf("%lf\n", c);
```

### constptr转对象
```
nav_msgs::OccupancyGridConstPtr map = tool->getGlobalMap();

nav_msgs::OccupancyGrid b = *map;

double c= b.info.origin.position.x;
printf("%lf\n", c);
```

### ptr转constptr
```
直接赋值即可。

cv_bridge::CvImagePtr cv_ptr;
sensor_msgs::imagePtr img_ptr = cv_ptr->toImageMsg();

sensor_msgs::ImageConstPtr img_constptr = img_ptr;
```

### constptr转ptr
[const_cast](https://www.jb51.net/article/55885.htm)用来将类型的const、volatile和__unaligned属性移除
```
const CA *pA = new CA;
CA *pB = const_cast<CA *>(pA);
pB->m_iA = 100;
// Now the pA and the pB points to the same object
```

## 仿真启动turtlebot
```
<include file="$(find turtlebot_bringup)/launch/minimal.launch">
<arg name="simulation" value="true" />
</include>
```

## [ros time](https://www.ncnynl.com/archives/201611/1074.html)
```
t = rospy.Time.from_sec(time.time())
seconds = t.to_sec() #floating point
nanoseconds = t.to_nsec()

```
## python 欧拉角和四元数相互转换
```
tf.transformations.euler_from_quaternion(quaternion, axes='sxyz')
tf.transformations.quaternion_from_euler(ai, aj, ak, axes='sxyz')
```

## [ros tf官方api文档](http://docs.ros.org/indigo/api/tf/html/)