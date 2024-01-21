# ROS重映射问题

## 第一次尝试解决
命名空间：

此时，节点所在的空间发生了变化，我们把这个节点从全局名空间放进了一个/my的名空间下。于是节点内的所有参数，服务，话题也都放在了/my的命名空间下。你会发现，它所发布的和订阅的话题，服务都在这个名空间，这意味着它现在只会订阅和发布这个名空间下的话题。

这个时候运行rosrun turtlesim turtle_teleop_key ，是不会控制小乌龟的，因为它只会发布/turtel1/cmd_vel话题，不会发布/my/turtel1/cmd_vel。

我们通过重映射和参数服务器，很容易让不同命名空间的节点都能通信。好吧，先简单的通过重映射使他们通信

重映射不仅可以把命名空间改变，还可以将发布的话题给改变（还可改变参数的空间），这样/my空间下的乌龟就可以移动了。当然你也可以直接将这个节点的名空间改为/my，这样他们在同一个命名空间，当然可以直接通信了。


### 1.rosrun设置命名空间（节点）

#### 1.1设置命名空间演示
```
 //语法: rosrun 包名 节点名 __ns:=新名称
  rosrun turtlesim turtlesim_node __ns:=/xxx
  rosrun turtlesim turtlesim_node __ns:=/yyy
```


两个节点都可以正常运行

相对名称：开头没有“/” ROS会为相对名称提供一个默认的命名空间(默认为“/”)，但你可以为每个节点设置默认命名空间。
（1）rosrun turtlesim turtlesim_node __ns:=/my
（2）在launch文件中设置命名空间用到的属性是ns (ns设置的是默认命名空间)
这两个设置的是node_namespace相对于全局“/”还是自己设置的
[https://blog.csdn.net/ahelloyou/article/details/105596163](https://blog.csdn.net/ahelloyou/article/details/105596163)

#### 1.2运行结果

`rosnode list`查看节点信息,显示结果:
  /xxx/turtlesim
  /yyy/turtlesim

在Launch文件中写命名空间：

### 2.rosrun名称重映射

#### 2.1为节点起别名
```
  //语法: rosrun 包名 节点名 __name:=新名称

  rosrun turtlesim  turtlesim_node __name:=t1 |  rosrun turtlesim   turtlesim_node /turtlesim:=t1(不适用于python)

  rosrun turtlesim  turtlesim_node __name:=t2 |  rosrun turtlesim   turtlesim_node /turtlesim:=t2(不适用于python)
```


两个节点都可以运行

#### 2.2运行结果

`rosnode list`查看节点信息,显示结果:

  /t1

  /t2

### 3.rosrun命名空间与名称重映射　叠加

#### 3.1设置命名空间同时名称重映射
```
  //语法: rosrun 包名 节点名 __ns:=新名称 __name:=新名称

  rosrun turtlesim turtlesim_node **__ns:=/xxx** **__name:=tn**
```

#### 3.2运行结果

`rosnode list`查看节点信息,显示结果:

  /xxx/tn

使用环境变量也可以设置命名空间,启动节点前在终端键入如下命令:

export ROS_NAMESPACE=xxxx

## 第二次尝试使用Launch文件解决

### 1 . launch基本成分

最简单的启动文件是由一个包含了几个节点元素的根元素(root element)组成。

根元素：launch文件是XML文档，每一个XML文档必须有且只有一个跟元素(root element)。对于ROS launch文件，根元素(root element)是由一对 launch 标签定义的


#### 1-2  `<node>`

**启动的节点**：任何一个**launch文件**的重点都是：节点(**node**)元素的集合。启动的每一个节点(**node**)都要有自己独一无二的名字(**name**)。

在节点(**node**)标签的末尾加上’ `/` ‘斜杠是重要的，并且也容易被忘记。’ `/` ’ 表明：这里不会再有”`</node>`“出现，并且**node**元素定义完成。

```YAML
<node
pkg="package-name"
type="executable-name"
name="node-name"
/>
```

一个**node**(节点)元素**必需的三个属性**：`pkg` 、 `type` 和 `name` 属性：

`pkg` 和 `type` 属性确定了：启动此节点，**ROS**应该运行哪个程序。它们与 `rosrun` 的两个命令行参数一样，它们分别是：**程序包名字** 和 **可执行文件的名字**。



**Q：** 如何将标准输出信息显示在终端(**console**)上？

  **A：** 在 `node` 元素中使用 `output` 属性：

  output="screen"



启动完所有请求启动的节点之后，`roslaunch` 监测每一个节点，让它们保持正常的运行状态。对于每一个节点(`node`)，当它终止( **terminates**)时，我们可以要求 `roslaunch` 重新启动它，这就是 `respawn` 属性做的事情：

  respawn="true"



使用 roslaunch 命令 的一个潜在的缺点：相比我们原来对每个节点在单独的终端使用 rosrun 命令启动的做法，roslaunch 则是让所有的节点共享同一个终端。 那些只需要生产简单的日志消息文件而不需要终端(console)输入的节点是容易管理的，而那些依赖终端输入的节点，比如 turtle_teleop_key 节点，它可能要优先的保留在独立的终端上。

庆幸的是，roslaunch 提供了一个简单的属性去实现这一点，在 node 元素里使用 launch-prefix 属性：

```YAML
launch-prefix="command-prefix"
```

在例子launch文件中，我们给 teleoperation 节点使用了这个属性：

```YAML
launch-prefix="xterm -e"
```

因为这个属性，启动这个 node 元素的 rosrun 命令大致相当于：

```YAML
xterm -e rosrun turtlesim turtle_teleop_key
```

正如我们所知道的，xterm 命令会开一个新的终端窗口。 -e 参数告诉 xterm ：执行其命令行剩余部分(rosrun turtlesim turtle_teleop_key)。


### 2. 重映射名字

在启动一个节点的时候，有两种方法创建重映射：

在**终端命令行**中启动一个节点时，要重新给这个节点命名：给出一个节点原来的名字和新的名字，中间用:=分开。

original-name:=new-name

例如，在运行turtlesim实例时，我们现在想把发布姿态数据的话题/turtle1/pose名称改为：/tim，那么命令就是这样的：

rosrun turtlesim turtlesim_node turtle1/pose:=tim

在**launch文件中重新命名：使用 remap 元素：**

<remap from="original-name" to="new-name" />

如果这个 remap 是 launch 元素的一个child（子类），**与 node 元素同一层级**， 并在 launch 元素内的最顶层。那么这个 remapping 将会作用于后续所有的节点。

这个 remap 元素也可以作为 node 元素的一个child（子类）出现。下面这个就是使用模板：



```YAML
    <node node-attributes >
        <remap from="original-name" to="new-name" />
        . . .
    </node>
```

上面命令行命令如果在launch文件中，就是下面这个样子的：

```YAML
<node pkg="turtlesim" type="turtlesim_node"
name="turtlesim" >
<remap from="turtle1/pose" to="tim" />
</node>

```


### 3. file

包含(including)其他文件：

```YAML
<include file="path-to-launch-file" />
```

这个 file 属性期望我们添加想要包含的文件的完整路径。但是大多数时候，include 元素使用一个 find 命令来搜索一个程序包，代替一个明确的完整路径：

```YAML
<include file="$(find package-name)/launch-file-name" />
```

include 元素必须要指定文件的特定路径，你可以使用 find 来找到这个程序包，但是却不能在这个程序包目录里面自动的找到某个子目录里有launch文件。举例：

这样做是正确的：<include file = "find learning_tutrols"/launch/start_demo.launch" / >

这样做是错误的：<include file = "find learning_tutrols"/start_demo.launch" />



ns ：`include` 元素也支持 `ns` 属性，可以让这个文件里的内容推送到一个**命名空间**里面：
```YAML
  <include file=". . . " ns="namespace" />
```

一般我们都会给 `include` 元素设置一个 `ns` 属性。



### 4.启动参数(Launch arguments)

为了帮助launch文件配置，roslaunch 支持 launch arguments（启动参数），也叫做 arguments 或者 args。它的功能有点像一个可执行程序的局部变量。它的优点是，你可以通过编写launch文件来避免编写重复代码。为少数的信息使用arguements，可以改变程序的运行。

为了说明这个道理，我们的例子launch文件中使用了一个argument，叫做 use_sim3，目的是为了确定是否启动了3个turtlesim副本或只有2个。
————————————————

```YAML
Z：尽管术语argument和parameter在许多计算机环境中稍微可以互换使用，它们的含义在ROS中有很大的不同。Parameters（参数）在一个运行的ROS系统中是变量(values)，它被存储在parameter服务器中，活动（或者叫：运行）的节点通过ros::param::get()函数访问它，并且用户可以通过 rosparam 命令行工具使用它。

相比之下，arguments只有在launch文件里合法，它们的值不是直接提供给节点。

```

