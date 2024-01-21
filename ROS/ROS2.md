ros2配置：

domain ID

[https://docs.ros.org/en/humble/Concepts/Intermediate/About-Domain-ID.html](https://docs.ros.org/en/humble/Concepts/Intermediate/About-Domain-ID.html)


# ROS2

- 成功编译了功能包，但是无法找到可执行文件

换源？？

emporarily switch the source list of apt to 20.04:

- replace the file `/etc/sources.list` with [20.04 source list](https://gist.github.com/ishad0w/788555191c7037e249a439542c53e170)
- run `sudo apt update` and then install `libsoundio1`
- switch back your source list to 22.04


python:

创建话题

```Python
       self.point_cloud_topic = self.create_subscription(
            PointCloud2,
            self.get_parameter('point_cloud_topic').get_parameter_value().string_value,
            self.point_callback,
            10
        )
```