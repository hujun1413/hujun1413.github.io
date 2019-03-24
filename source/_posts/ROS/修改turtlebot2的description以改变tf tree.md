---
title:  修改turtlebot2的description以改变tf tree
date: 2018-06-13 20:35:26 # 文章生成时间，一般不改
categories:   # 文章分类目录，参数可省略
    - ROS
tags:   # 文章标签，参数可省略
---
### 调用什么文件来显示机器人及其身上的坐标系转换
在turtlebot_description包中，urdf/turtlebot_library.urdf.xacro文件中引用了基本所有的机器人组件。

一般的，minimal.launch是用于启动机器人robot.launch.xml和底盘mobile_base.launch.xml
<!--more-->
robot.launch中调用description.launch.xml来建立机器人的各关节和传感器的坐标系描述，调用robot_state_publisher来读取robot_description文件并发布tf tree。

description.launch.xml调用$(find turtlebot_description)/robots/$(arg base)_$(arg stacks)_$(arg 3d_sensor).urdf.xacro'文件来定义机器人的关节和传感器

因此，通过调用(find turtlebot_description)/robots/$(arg base)_$(arg stacks)_$(arg 3d_sensor).urdf.xacro'文件来显示机器人及其坐标系。可以修改这个文件所包含的文件，来改变机器人的形态和坐标系变换

例如：
在kobuki_hexagons_asus_xtion_pro.urdf.xacro文件中，
```
<xacro:include filename="$(find turtlebot_description)/urdf/turtlebot_library.urdf.xacro" />

<kobuki/>
<stack_hexagons                 parent="base_link"/>
<sensor_asus_xtion_pro          parent="base_link"/>
<sensor_rplidar  				  parent="base_link"/>
```

### turtlebot的hexagon平台
以base_link为母坐标系，base_link在turtlebot中代表的是机器人中心，若使用kobuki则在实际世界坐标系中高度为0.0102m，即若以机器人正中心在地面的坐标为origin（0,0,0），则base_link相对于origin的坐标为（0,0,0.0102）。发布base_link与base_footprint之间的tf变换为`xyz="0 0 0.0102" rpy="0 0 0"`

详情参考urdf/stacks/hexagons.urdf.xacro文件。

以下坐标都是相对于base_link坐标系而言，即真实的高度都要加上0.0102m，且坐标都是指该物体的中心点坐标。若是柱子则是柱子的中心点，若是平台则是平台的中心点。

层与层之间支撑的柱子，如：
```
<stack_top_pole parent="${parent}" number="0" x_loc= "0.0381" y_loc= "0.1505" z_loc="0.2920"/>
```
代表最高层和中间层之间的柱子，柱子中间点的高度为0.2920m，而urdf/stacks/hexagons.urdf.xacro文件的87行的`<cylinder length="0.2032" radius="0.006"/>`，说明柱子长度为0.2032m，因此柱子的底部为0.1904m，顶部为0.3936。


urdf/stacks/hexagons.urdf.xacro文件的157行的
`<cylinder length="0.006" radius="0.170"/>`，说明平台的宽度为0.006m。
最高层平台中心的高度为：0.3966m，平台上的物体的高度为0.3996m（平台上物体的真实高度为0.4096m）
中间层平台中心的高度为：0.1874m
最低层平台中心的高度为：0.1306m

### turtlebot的asus xtion pro
urdf/sensors/asus_xtion_pro.urdf.xacro文件中，摄像头的中心camera_rgb_frame相对于base_link的转换为：
`<origin xyz="${cam_px} ${asus_xtion_pro_cam_py} ${cam_pz}"
              rpy="${cam_or} ${cam_op} ${cam_oy}"/>`
              
asus_xtion_pro_cam_py在asus_xtion_pro.urdf.xacro文件中设定。
cam_px,cam_pz,cam_or,cam_op,cam_oy在urdf/turtlebot_properties.urdf.xacro文件中设定

/camera_rgb_frame相对于/camera_rgb_optical_frame为`<origin xyz="0 0 0" rpy="${-M_PI/2} 0 ${-M_PI/2}" />`

/camera_link相对于/camera_rgb_frame为`<origin xyz="0 ${asus_xtion_pro_cam_rel_rgb_py} 0" rpy="0 0 0"/>`

/camera_depth_frame相对于/camera_rgb_frame为`<origin xyz="0 ${asus_xtion_pro_depth_rel_rgb_py} 0" rpy="0 0 0" />`

带入数据，
depth_frame rgb_optical_frame base_link camera_link
0.027m 0.0205m 0.0225-0.0205=0.002m

世界坐标系的相对关系为右手坐标系，机器人前方为x轴正方向，机器人左方为y轴正方向，机器人上方为z轴正方向。

相机坐标系：机器人前方为z轴正方向，机器人右方为x轴正方向，机器人下方为y轴正方向

### 相机坐标系与世界坐标系的变换
```
cutcat@cutcat:~$ rosrun tf tf_echo /camera_rgb_optical_frame /map
At time 1528257902.539
- Translation: [0.034, 0.685, 0.055]
- Rotation: in Quaternion [0.502, -0.498, 0.498, 0.502]
            in RPY (radian) [1.571, -1.563, 0.000]
            in RPY (degree) [90.000, -89.528, 0.000]

tf_listener.transformPoint(kMapTF, msg->start, start);
msg->start在/camera_rgb_optical_frame坐标系下，start在/map坐标系下
msg->start_x=2     msg->satrt_y=-1     msg->satrt_z=1
start_x=0.961461     start_y=-1.95825     start_z=1.6848

(2-0.034)=1.966 (-1-0.685)=-1.685 1-0.055=0.945 #源坐标系下坐标-translation，若正方向相反，则添负号。（相机的x轴正方向是机器人右方，对应于直接坐标系的y轴负方向，将1.966添负号后放在世界坐标系的y坐标上。相机的y轴正方向是机器人的下方，将-1.685添负号后放在世界坐标系的z坐标上。相机的z轴正方向是机器人前方，不添负号直接放在世界坐标系的x坐标上）
0.945 -1.966 1.685
0.961461 -1.95825 1.6848


cutcat@cutcat:~$ rosrun tf tf_echo /base_link /map
At time 1528257977.424
- Translation: [-0.043, -0.013, -0.010]
- Rotation: in Quaternion [0.000, 0.000, -0.004, 1.000]
            in RPY (radian) [0.000, 0.000, -0.008]
            in RPY (degree) [0.000, 0.000, -0.472]

```

### turtlebot2的description参数
#### turtlebot_description/urdf/sensors/rplidar.urdf.xacro
```
<joint name="laser" type="fixed">  
  <!--<origin xyz="0.15 0.0 0.15" rpy="0 0.0 0.0" />  -->
  <origin xyz="0.10 -0.03 0.4716" rpy="0 0.0 0.0" /> 
```

#### turtlebot_description/urdf/sensors/asus_xtion_pro.urdf.xacro
```
<joint name="mount_asus_xtion_pro_joint" type="fixed">
  <origin xyz="-0.1024 0.0 0.6596" rpy="0 0 0"/>
  <!--<origin xyz="-0.1024 0.0 0.272" rpy="0 0 0"/>-->

```

#### turtlebot_description/urdf/turtlebot_properties.urdf.xacro
```
<!--<property name="cam_px" value="-0.087" />-->
<property name="cam_px" value="-0.098" />
<!--	<property name="cam_py" value="-0.0125" /> Y varies for different configurations. get sets in sensors_urdf -->
<!--<property name="cam_pz" value="0.2870" />-->
<property name="cam_pz" value="0.6746" />
```