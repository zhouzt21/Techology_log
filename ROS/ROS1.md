---
title: ROS
date: 2023-04-02
tags: 知识
---

# ROS 简介

1. 一套编译辅助框架（catkin）：ROS编译的时候需要Cmake，ROS要依赖C如果不加ROS.CPP不会创造环境（cpp框架），很麻烦；
2. 一组命令行工具
3. 一系列可调用的API（C++、Python、lisp）

很多ROS应用都是在树莓派上做的



#### 与单片机控制系统的区别

1. ROS不是实时的、要人为校验 ；单片机是实时的
2. 并行的、面向任务的程序：创建的若干个节点是单独的（open mpi并行编程）
3. 通信方式新（不完全是网络协议）：支持多主机（类似于分布式系统）
4. 为大型复杂功能而生：如语音识别、视觉

#TTS

# ROS1常用命令
按指令：

1. rosservice

```Python
rosservice list         print information about active services
rosservice call  [service] [args]        call the service with the provided args
rosservice type         print service type
rosservice find         find services by service type
rosservice uri          print service ROSRPC uri
```

    
2. rostopic

```Python
rostopic info       //   /cmd_vel速度状态话题
rostopic list
rostopic pub [topic][msg_type]
rostopic hz 

```
3. rosmsg 

```Python
rosmsg show      //   查看消息结构

```
4. rosnode
5. roslaunch & rosrun







按目的：

1. 启动仿真环境/运行节点

```Python
roslaunch bitbots_dynamic_kick viz_thmos.launch
```

```Python
运行节点
方法一：rosrun
roscore——运行master节点
rosrun package名称 node名称
说明：rosrun方法每次只能运行一个节点。

方法二：roslaunch
roslaunch package名称 launch文件名称
说明：自动打开master节点，并且自动运行多个节点。

//正常编译的node，都会在对应的工程目录的devel/lib/对应package/下生成node文件
```

2. 创建功能包
   
```Python
    catkin_create_pkg 包名 ros库1 ros库2…
```
3. 编译运行: 只“编译”一个功能包
```Python
    catkin_make -DCATKIN_WHITELIST_PACKAGES=" 包名"
    catkin_make —pkg my_package
    catkin build
     package_name
```
4. 可视化
```Python
    rqt_image_view
    roslaunch rqt_tf_tree rqt_tf_tree关节坐标转化信息
```
5. 发布消息

    可以使用rqt发布消息
    也可以直接命令行运行topic： rostopic ...(包) ...（话题）
    也可以写一个脚本

```Python
from  import
//导入消息类型；需要先定义消息类型(也可以找教程自定义消息类型）
//创建消息的实例
vel_pub = rospy.Publisher('/cmd_vel', Twist, queue_size=1) 
//创建发布者
vel_pub.publish(vel_msg)
//发布消息 
//多个发布与接收，”高级“教程
http://zhaoxuhui.top/blog/2019/10/20/ros-note-7.html
```
6. cv
```Python
    cv2.imread（）的默认格式为bgr
    cv2.imread()的默认通道格式H_W_C
```


ROS in C++:

编译

[https://blog.csdn.net/mandadinda/article/details/112262111?ops_request_misc=%7B%22request%5Fid%22%3A%22166891336416800213083472%22%2C%22scm%22%3A%2220140713.130102334.pc%5Fall.%22%7D&request_id=166891336416800213083472&biz_id=0&utm_medium=distribute.pc_search_result.none-task-blog-2~all~first_rank_ecpm_v1~rank_v31_ecpm-3-112262111-null-null.142^v65^control,201^v3^add_ask,213^v2^t3_control1&utm_term=ros如何只编译一个功能包&spm=1018.2226.3001.4187](https://blog.csdn.net/mandadinda/article/details/112262111?ops_request_misc=%7B%22request%5Fid%22%3A%22166891336416800213083472%22%2C%22scm%22%3A%2220140713.130102334.pc%5Fall.%22%7D&request_id=166891336416800213083472&biz_id=0&utm_medium=distribute.pc_search_result.none-task-blog-2~all~first_rank_ecpm_v1~rank_v31_ecpm-3-112262111-null-null.142^v65^control,201^v3^add_ask,213^v2^t3_control1&utm_term=ros如何只编译一个功能包&spm=1018.2226.3001.4187)



# ROS补充

service


关于topic 命名空间问题：/群组名(   /节点名/句柄名   )/话题名

CPP补充：

1. 在一个`roscpp`程序中，节点句柄可以启动或关闭一个ROS内部节点；
2. 节点句柄可以增加一层命名空间，可以为编写组件提供便利。

节点句柄的另一个构造函数形式可以让我们自定义命名空间：

```
ros::NodeHandle nh("my_ns");  // <node_namespace>/my_ns
```

若存在节点命名空间，如在launch文件中启动该节点时加入ns="node_ns"，则命名空间为ode_ns/my_ns；若不存在节点命名空间，则为my_ns。

另外也可以创建多个节点句柄，然后为某个句柄设置父节点句柄：

```
ros::NodeHandle nh1("ns1");  // <node_namespace>/ns1
ros::NodeHandle nh2(nh1, "ns2");  // <node_namespace>/ns1/ns2
```


原文链接：https://blog.csdn.net/Azahaxia/article/details/113950533