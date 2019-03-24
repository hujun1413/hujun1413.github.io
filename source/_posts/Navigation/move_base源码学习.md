---
title: move_base源码学习
date: 2017-11-25 16:25:26 # 文章生成时间，一般不改
categories:   # 文章分类目录，参数可省略
    - Navigation
tags:   # 文章标签，参数可省略
    - move_base
---
### [move_base代码学习一](http://www.cnblogs.com/shhu1993/p/6323699.html)

[move_base导航的源码心得](http://blog.csdn.net/david_han008/article/details/72032898)


### 源码架构分析

#### move_base
move_base 提供的是框架，在move_base 中是通过nav_core 中规定的planner 与 recovery_behavior 的基类的接口进行调用。与具体的实现方法隔离开来。

planThread是全局路径规划的线程，在有新的目标到达时等需要进行全局规划时被唤醒，用于规划出一条全局路径。

executeCycle是局部路径规划的工作函数，其中会调用computeVelocityCommands来规划出当前机器人的速度
<!--more-->
```C++
MoveBase::MoveBase(tf::TransformListener& tf)　
//MoveBase类的构造函数，读取配置参数，加载所选择的插件，开启global　planner的线程

void MoveBase::reconfigureCB(move_base::MoveBaseConfig &config, uint32_t level)
//动态更改节点参数的回调函数，用于修改参数

void MoveBase::goalCB(const geometry_msgs::PoseStamped::ConstPtr& goal)
//In ROS goal callback, wrapping the PoseStamped in the action message and re-sending to the server.将接收到的目标重新发送给action_goal server

void MoveBase::clearCostmapWindows(double size_x, double size_y)
//clear the planner's costmap and the controller's costmap

bool MoveBase::clearCostmapsService(std_srvs::Empty::Request &req, std_srvs::Empty::Response &resp)
//a service for clearing the costmaps

bool MoveBase::planService(nav_msgs::GetPlan::Request &req, nav_msgs::GetPlan::Response &resp)
//a service for getting a plan

MoveBase::~MoveBase()
//MoveBase类的析构函数，清除并关闭线程，退出

bool MoveBase::makePlan(const geometry_msgs::PoseStamped& goal, std::vector<geometry_msgs::PoseStamped>& plan)
//global planner function, 调用planner_->makePlan，这里即调用bool NavfnROS::makePlan(const geometry_msgs::PoseStamped& start, const geometry_msgs::PoseStamped& goal, std::vector<geometry_msgs::PoseStamped>& plan)

void MoveBase::planThread()
//global planner thread，会被executeCb,executeCycle唤醒或挂起

void MoveBase::executeCb(const move_base_msgs::MoveBaseGoalConstPtr& move_base_goal)
//MoveBaseActionServer，每次goal到来都被调用,如果有新目标到来而被抢占则唤醒planThread线程处理，否则为取消目标并挂起处理线程。

bool MoveBase::executeCycle(geometry_msgs::PoseStamped& goal, std::vector<geometry_msgs::PoseStamped>& global_plan)
//local planner 核心工作函数

bool MoveBase::loadRecoveryBehaviors(ros::NodeHandle node)
//加载指定的void MoveBase::loadDefaultRecoveryBehaviors()

void MoveBase::loadDefaultRecoveryBehaviors()
//加载默认的void MoveBase::loadDefaultRecoveryBehaviors()

void MoveBase::resetState()
//状态重置，速度归零

```

### navfn_ros
为navigation的全局规划器，接受costmap生成的 global costmap 规划出从起始点到目标点的路径，为local_planner 作出参考。

所有的global planner都必须实现nav_core::BaseGlobalPlanner定义的接口(即纯虚函数)

makePlan用于规划出全局路径，调用calcNavFnDijkstra和getPlanFromPotential。calcNavFnDijkstra会调用propNavFnDijkstra的Dijkstra算法来计算全局路径。
```C++
void NavfnROS::initialize(std::string name, costmap_2d::Costmap2DROS* costmap_ros)
//初始化

bool NavfnROS::makePlan(const geometry_msgs::PoseStamped& start, const geometry_msgs::PoseStamped& goal, double tolerance, std::vector<geometry_msgs::PoseStamped>& plan)
//global planner function


bool NavFn::calcNavFnDijkstra(bool atStart)
//调用propNavFnDijkstra，找到全局规划路径

bool NavFn::propNavFnDijkstra(int cycles, bool atStart)	
//main propagation function，Dijkstra method, breadth-first

int NavFn::calcPath(int n, int *st)
//Path construction,Find gradient at array points, interpolate path, Use step size of pathStep, usually 0.5 pixel


bool NavfnROS::getPlanFromPotential(const geometry_msgs::PoseStamped& goal, std::vector<geometry_msgs::PoseStamped>& plan)
//从potential中选出最佳的下一个pose，调用了calcPath

```

### base_local_planner
所有的local planner都必须实现nav_core::BaseLocalPlanner定义的接口(纯虚函数)

为navigation 的局部规划器，接受costmap 生成的local costmap 规划出速度。

setPlan()用于设置之前全局规划得到的全局路径，然后转换成相对于机器人位置的路径之后，调用findBestPath来得到当前的速度。
```C++
//Given the current position, orientation, and velocity of the robot, compute velocity commands to send to the base
virtual bool computeVelocityCommands (geometry_msgs::Twist &cmd_vel)=0
 
virtual void initialize (std::string name, tf::TransformListener *tf, costmap_2d::Costmap2DROS *costmap_ros)=0
 
virtual bool isGoalReached ()=0
 
virtual bool setPlan (const std::vector< geometry_msgs::PoseStamped > &plan)=0
```

```C++
bool TrajectoryPlannerROS::computeVelocityCommands(geometry_msgs::Twist& cmd_vel)
//计算得到当前的速度

Trajectory TrajectoryPlanner::findBestPath(tf::Stamped<tf::Pose> global_pose, tf::Stamped<tf::Pose> global_vel, tf::Stamped<tf::Pose>& drive_velocities)
//given the current state of the robot, find a good trajectory

```

### clear_costmap_recovery
所有的recovery behaviors都必须实现nav_core::RecoveryBehavior定义的接口(纯虚函数)

规定move_base 行为集合中处理异常情况的行为，可以定义多种恢复行为的插件。
```C++
virtual void initialize (std::string name, tf::TransformListener *tf, costmap_2d::Costmap2DROS *global_costmap, costmap_2d::Costmap2DROS *local_costmap)=0
 
virtual void runBehavior ()=0
//executeCycle中的CLEARING模式，调用runBehavior，是把local,global costmap在reset_distance半径之外的free，occupied都清除，变成NO_INFORMATION。不对static layer操作
```

### rotate_recovery
他的作用只是在原地旋转，这样costmap就会自己更新，mark或者clear一点的值

### [costmap_2d](http://blog.csdn.net/sonictl/article/details/51518492)
使用分层的机制，每层的信息都加到costmap_2d::LayeredCostmap进行管理。
可看作为navigation的输入处理器。不同的传感器输入的数据差异很大（激光雷达 & RGBD-camera）通过costmap_2d ， 不同的数据被处理成统一的格式：栅格地图，权值用经过概率方法处理过的，表示空间中障碍物，未知与安全区域。生成出来的costmap则是planner的输入。

[costmap_2d中类的解析](http://blog.csdn.net/mllearnertj/article/details/74838981)

StaticLayer：订阅map消息
ObstacleLayer：监听Laser回调
inflation_layer：Given a distance, compute a cost.

```C++
void Costmap2DROS::reconfigureCB(costmap_2d::Costmap2DConfig &config, uint32_t level)
//动态更改节点参数的回调函数，用于修改参数

void Costmap2DROS::mapUpdateLoop(double frequency)
//根据频率更新costmap

void Costmap2DROS::updateMap()

void LayeredCostmap::updateMap(double robot_x, double robot_y, double robot_yaw)

(*plugin)->updateCosts(costmap_, x0, y0, xn, yn);
//cost 值的更新，每个plugin都实现这个函数,是一份costmap在不同的plugin下的更新，costmap是一样的，cost值是叠加上去的

void StaticLayer::updateCosts(costmap_2d::Costmap2D& master_grid, int min_i, int min_j, int max_i, int max_j)或者obstacle_layer,inflation_layer等插件

```

### [dwa_planner](http://wiki.ros.org/dwa_local_planner)
[ROS局部规划模块分析](http://www.bubuko.com/infodetail-720132.html)

[ROS Navigation-----dwa_local_planner（DWA）简介](http://blog.csdn.net/x_r_su/article/details/53393872)

[agv 路径规划move_base](http://blog.csdn.net/david_han008/article/details/72171602)

[机器人局部避障的动态窗口法(dynamic window approach)](http://blog.csdn.net/heyijia0327/article/details/44983551)
```
SimpleTrajectoryGenerator::generateTrajectory
//生成轨迹

DWAPlanner::findBestPath
//given the current state of the robot, find a good trajectory

SimpleTrajectoryGenerator::initialise
//初始化路径生成器

SimpleScoredSamplingPlanner::findBestTrajectory
//

SimpleTrajectoryGenerator::nextTrajectory
//

SimpleTrajectoryGenerator::generateTrajectory
//

SimpleScoredSamplingPlanner::scoreTrajectory
//

base_local_planner::LocalPlannerLimits* limits的min_vel和max_vel之间平均采样vsamples次，作为sample_params_数组的值

```


### 问题记录
1. 导航中是如何使用costmap_2d的
2. 所需设置的参数及含义
2. 几个线程及功能：
    * move_base.cpp的line 86，planner_thread_：做全局规划的线程
    * 主线程，ros::spin()
    * move_base，MoveBase::executeCb，MoveBaseActionServer
    * MoveBase::reconfigureCB，dynamic_reconfigure::Server
    * 
    * make_plan，MoveBase::planService，private_nh.advertiseService
    * clear_costmaps，MoveBase::clearCostmapsService，private_nh.advertiseService
    * 提供两种方式send goal目标位置的信息
    SimpleActionServer(可以追踪移动到目标过程的状态信息，到了没有，是不是没有plan，取消了etc)
    Subscribe topic(move_base_simple/goal),直接发布这个topic的信息，不会有目标执行过程中的反馈信息
    提供的service
    ~make_plan(只会提供plan该怎么走的位置信息，不会使机器人移动)
    ~clear_unknown_space(告诉move_base清楚机器人周围的unknown space)
    ~clear_costmaps(告诉move_base清楚costmap中的障碍物信息，可能导致撞到障碍物)
3. global local　整合
4. 有了plan怎么得速度













