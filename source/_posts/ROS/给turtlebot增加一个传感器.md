---
title:  给turtlebot增加一个传感器
date: 2018-06-13 20:35:26 # 文章生成时间，一般不改
categories:   # 文章分类目录，参数可省略
    - ROS
tags:   # 文章标签，参数可省略
---
### 参考
[ros中利用gazebo进行gmapping仿真:kobuki+kinect -> kobuki+rplidar](https://blog.csdn.net/zyh821351004/article/details/48846179)

[ROS中给turtlebot加两个传感器：kinect+hokuyo，进行gazebo仿真](https://blog.csdn.net/qq_36355662/article/details/61413860)

<!--more-->
### 让turtlebot带雷达和asua_xtion_pro
roslaunch turtlebot_bringup minimal.launch是用于启动机器人robot.launch.xml和底盘mobile_base.launch.xml

minimal.launch中的    
```
<arg name="base" value="$(arg base)" />
<arg name="stacks" value="$(arg stacks)" />
<arg name="3d_sensor" value="$(arg 3d_sensor)" /> #rplidar,asua_xtion_pro,kinect2等
<arg name="serialport" value="$(arg serialport)" /> #底盘的设备端口，如/dev/ttyUSB0
```
可以通过在~/.bashrc中设置TURTLEBOT_BASE，TURTLEBOT_STACKS，TURTLEBOT_3D_SENSOR，TURTLEBOT_SERIAL_PORT来赋值
```
export TURTLEBOT_BASE=kobuki
export TURTLEBOT_STACKS=hexagons
export TURTLEBOT_3D_SENSOR=asus_xtion_pro
export TURTLEBOT_SERIAL_PORT=/dev/ttyUSB0
```

robot.launch中调用description.launch.xml来建立机器人的各关节和传感器的坐标系描述，调用robot_state_publisher来读取robot_description文件并发布tf tree。

description.launch.xml调用$(find turtlebot_description)/robots/$(arg base)_$(arg stacks)_$(arg 3d_sensor).urdf.xacro'文件来定义机器人的关节和传感器，可以在turtlebot_description)/robots下定义新的.urdf.xacro文件来代表拥有不同关节和传感器的机器人


例如：
在kobuki_hexagons_asus_xtion_pro.urdf.xacro文件中，
```
<xacro:include filename="$(find turtlebot_description)/urdf/turtlebot_library.urdf.xacro" />

<kobuki/>
<stack_hexagons                 parent="base_link"/>
<sensor_asus_xtion_pro          parent="base_link"/>
<sensor_rplidar           parent="base_link"/>
```
代表拥有kobuki底盘，hexagons 平台，asua_xtion_pro深度摄像头,rplidar雷达的turtlebot机器人。

urdf/turtlebot_library.urdf.xacro文件的内容为：
```
...
<xacro:include filename="$(find turtlebot_description)/urdf/sensors/asus_xtion_pro_offset.urdf.xacro"/>  
<xacro:include filename="$(find turtlebot_description)/urdf/sensors/rplidar.urdf.xacro"/>  
...
```

调用了turtlebot_description/urdf/stack/hexagons.urdf.xacro和turtlebot_description/urdf/sensor/asus_xtion_pro.urdf.xacro和turtlebot_description/urdf/sensor/rplidar.urdf.xacro文件。
这几个文件都是平台和传感器的描述和坐标变换。

如果以后需要增加一个新的传感器，只需要在turtlebot_description/urdf/sensor文件夹下增加一个[sensor_name].urdf.xacro文件，里面写好该传感器的描述和坐标系转换，然后在urdf/turtlebot_library.urdf.xacro文件中include新增加的传感器描述文件，然后在选择的像kobuki_hexagons_asus_xtion_pro.urdf.xacro这样的文件中，加入一行
`<sensor_[sensor_name]            parent="base_link"/>`

这样robot_state_publisher就会自动读取机器人的各关节和传感器，并发送tf tree。

注意：asus_xtion_pro.urdf.xacro文件中会用到turtlebot_properies.urdf.xacro中的cam_px等变量，代表相机相对base_link的位置。


