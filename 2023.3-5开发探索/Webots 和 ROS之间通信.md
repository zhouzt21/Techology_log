---
title: Webots和ROS通信
date: 2023-04-01
tags: 知识
---


# Webots 和 ROS之间通信

控制器：

方案一：官方自带的默认标准ros控制器：

- 可以用于任何机器人，将所有webots功能作为service或者topic提供给其他ROS节点
- 路径$WEBOTS_HOME/projects/default/controllers/ros中找到Webots的ros控制器。

方案二：自己编写webots控制器：（不用）

- 用webots和ROS库的ROS节点



可以直接用已有的webots-ros（相当于是一个功能包，下载之后还是要放在工作空间，来cakin编译）

如果webots版本较新，直接下载安装webots-ros包

```
sudo apt-get install ros-kinetic-webots-ros
```

如果webots版本不行，另一种配置（在github上拉取）

```
Step1: 在github上下载webot代码 ：https://github.com/cyberbotics/webots (这个文件大约在4GB左右，如果在github上下载失败可以在百度网盘中下载) 
Step2: 在目录 projects/languages/ros 找到 webots_ros 目录，将整个 webots_ros 目录拷贝到你自己的ros工作空间中。 
Step3: 将目录 projects/default/controllers/ros/include 目录下的 srv 和 msg 文件夹拷贝到 catkin_ws/src/webots_ros/ 中 
```

### 在ROS里读取webots传感器数据

#### Camera

THMOS的porto里内容

```
Camera {
		                translation 0.02000 0.03984 -0.02500
		                rotation 0 -1 0 1.57080
		                children [
		                  DEF DCameraShape Shape {
		                    appearance DEF DCameraAppearance PBRAppearance {
		                      metalness 0
		                      roughness 0.3
		                    }
		                  }
		                ]
		                name "Camera"
		                fieldOfView IS fieldOfView
		                width IS cameraWidth
		                height IS cameraHeight
		                zoom Zoom {
		                }
		              }
```







在ros节点里使能（下面C++代码仅供参考）

```c++
// enable camera
  ros::ServiceClient set_camera_client;
  webots_ros::set_int camera_srv;
  ros::Subscriber sub_camera;
  set_camera_client = n->serviceClient<webots_ros::set_int>("ros_key_test/camera/enable");
  camera_srv.request.value = 64;
  set_camera_client.call(camera_srv);
```



#### IMU

webots的定义

```python
Gyro {
  MFVec3f lookupTable [ ]    # lookup table
  SFBool  xAxis       TRUE   # {TRUE, FALSE}
  SFBool  yAxis       TRUE   # {TRUE, FALSE}
  SFBool  zAxis       TRUE   # {TRUE, FALSE}
  SFFloat resolution  -1     # {-1, [0, inf)}
}

from controller import Gyro

class Gyro (Device):
    #turns on the angular velocity measurements
    #sampling period of the sensor (milliseconds) 
    def enable(self, samplingPeriod):
        
    def disable(self):   
    def getSamplingPeriod(self):
        
    #current measurement of the Gyro device. 
    # as a 3D-vector ()  ,Each element represents the angular velocity about one of the axes of the Gyro node[rad/s]
    def getValues(self):
        
    def getLookupTable(self):
```





THMOS的proto的定义：

```
Gyro {
        translation 0 0 0
        rotation 0 0 0 0
        name "Gyro"
        resolution 0.00106526458
}
Accelerometer {
        translation 0 0 0
        rotation 0 0 0 0
        name "Accelerometer"
        resolution 0.01 
}
```



	device[
		RotationalMotor{
			name "R_leg_2"
	              minPosition -3.14
	              maxPosition 3.14
	              maxVelocity %{= mx64_max_velocity }% 
	              maxTorque %{= mx64_max_torque }%
	              }
		PositionSensor{
			name "R_leg_2_sensor"
	        resolution %{= mx64_resolution }%
		}
	]
	
	endPoint Solid[
		translation
		rotation
		children[
				Shape{
					appearance
				}
				geometry
		]
	]
	
	name "R_leg_2"
	boundingObject Transform{
		translation
		rotation
	}
	physics Physics{
		density
		mass
		centerOfMass
		inertiaMatrix
	}







遗留问题，webots中测得的数据是什么类型？在webots-ros这个功能包里，用什么service来传递给其他ROS节点？如果没有这种服务和话题，是不是要自己写一个？

另外在获取webots传感器的数据的时候，区分不同机器人的域名可以获取吗？



安装ROS

sudo sh -c 'echo "deb http://packages.ros.org/ros/ubuntu $(lsb_release -sc) main" > /etc/apt/sources.list.d/ros-latest.list' 
sudo apt-key adv --keyserver 'hkp://keyserver.ubuntu.com:80' --recv-key C1CF6E31E6BADE8868B172B4F42ED6FBAB17C654 
sudo apt-get update
sudo apt-get install ros-kinetic-desktop-full
sudo rosdep init
rosdep update