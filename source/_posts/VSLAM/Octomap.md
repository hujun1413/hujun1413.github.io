---
title:  OctoMap
date: 2018-10-09 18:35:26 # 文章生成时间，一般不改
categories:   # 文章分类目录，参数可省略
    - VSLAM
tags:   # 文章标签，参数可省略
---
### [octomap Reference](http://octomap.github.io/octomap/doc/)

### [SLAM拾萃(1)：octomap](https://www.cnblogs.com/gaoxiang12/p/5041142.html)
[基于高博的修改](https://github.com/hujun1413/octomap_tutor)
<!--more-->
octomap的网页见：https://octomap.github.io

它的github源码在：https://github.com/OctoMap/octomap

它还有ROS下的安装方式：http://wiki.ros.org/octomap

### 编译octovis提示链接出错
```
CMakeFiles/octovis.dir/src/ViewerGui.cpp.o: In function `octomap::OcTreeBaseImpl<octomap::ColorOcTreeNode, octomap::AbstractOccupancyOcTree>::writeData(std::ostream&) const':
ViewerGui.cpp:(.text._ZNK7octomap14OcTreeBaseImplINS_15ColorOcTreeNodeENS_23AbstractOccupancyOcTreeEE9writeDataERSo[_ZNK7octomap14OcTreeBaseImplINS_15ColorOcTreeNodeENS_23AbstractOccupancyOcTreeEE9writeDataERSo]+0xe): undefined reference to `octomap::ColorOcTreeNode::writeValue(std::ostream&) const'
```
[编译octovis提示链接出错的解决措施](https://blog.csdn.net/qq_36420239/article/details/79488181)
```
cd octomap
git checkout v1.6.9   //校验为/opt/ros/indigo/share/octpmap/octomap-config-version文件中的自己的版本
```
重新编译即可

#### 正确安装octomap
```
git clone https://github.com/OctoMap/octomap
cd octomap
git checkout v1.6.9
mkdir build
cd build
cmake ..
make 
sudo make install
```

### 点云转换为不带颜色的octomap
将点云中的点一个一个地插入到OcTree中
```
octomap::OcTree tree( 0.05 );

for (auto p:cloud.points)
{
    // 将点云里的点插入到octomap中
    tree.updateNode( octomap::point3d(p.x, p.y, p.z), true );
}

// 更新octomap
tree.updateInnerOccupancy();
// 存储octomap
tree.writeBinary( output_file );
cout<<"done."<<endl;
```

### 点云转换成带颜色的octomap
将点云经过一个变换后，插入到ColorOcTree中
```
octomap::ColorOcTree tree( 0.05 ); //全局map

octomap::Pointcloud cloud_octo;
for (auto p:temp->points)
    cloud_octo.push_back( p.x, p.y, p.z );

tree.insertPointCloud( cloud_octo, 
        octomap::point3d( pose(0,3), pose(1,3), pose(2,3) ) );

for (auto p:temp->points)
    tree.integrateNodeColor( p.x, p.y, p.z, p.r, p.g, p.b );
tree.updateInnerOccupancy();
tree.write( "./data/map.ot" );
//带颜色的octomap，调用integrateNodeColor来混合颜色。后缀名改成了.ot文件。
cout<<"done."<<endl;
```