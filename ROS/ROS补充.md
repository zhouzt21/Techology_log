---
title: ROS
date: 2023-04-02
tags: 知识
---



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