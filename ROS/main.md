ROS 1 中文补全

[TOC]



## 基本概念

### 环境变量

[官方wiki-环境变量](http://wiki.ros.org/cn/ROS/EnvironmentVariables#ROS_ROOT)

- 主要
    | 环境变量                   | 含义                      | 典型值                                             |
    | -------------------------- | ------------------------- | -------------------------------------------------- |
    | `ROS_ROOT`                 | 核心软件包的安装位置      | `/opt/ros/melodic/share/ros`                       |
    | `ROS_MASTER_URI`           | 主服务器运行时uri         | `http://localhost:11311`                           |
    | `PYTHONPATH`               | Python包路径列表          | 包含`/opt/ros/melodic/lib/python2.7/dist-packages` |
    | `ROS_PACKAGE_PATH`（可选） | ROS包路径列表，递归搜索包 | `/home/dell/catkin_ws/src:/opt/ros/melodic/share`  |

- 系统数据存放地址
    | 环境变量               | 含义         | 默认                     |
    | ---------------------- | ------------ | ------------------------ |
    | `ROS_HOME`             | 数据地址     | `~/.ros`                 |
    | `ROS_LOG_DIR`          | 日志地址     | `$ROS_HOME/log`          |
    | `ROS_TEST_RESULTS_DIR` | 测试结果地址 | `$ROS_HOME/test_results` |

- 构建：
  - `export ROS_PARALLEL_JOBS='-j8 -l8'`：视系统负载而选择并行数量，限制最多8个并行（**强烈建议**）
  - `export ROS_PARALLEL_JOBS=-j8`：8个并行，无视当前系统负载

### 创建工作空间

```bash
mkdir -p ~/catkin_ws/src
cd ~/catkin_ws/
catkin_make
source devel/setup.zsh  # 视情况选择bash或zsh或其他
# 验证
echo $ROS_PACKAGE_PATH
```

### 常用命令

- rospack 软件包管理。

  根据 `ROS_ROOT` 和 `ROS_PACKAGE_PATH`，读取并解析每个软件包的`manifest.xml`，并为所有软件包组装完整的依赖关系树。

  - `rospack find <包名>`：打印包的绝对路径
  - `rospack depends <包名>`：打印包的所有依赖
  - `rospack depends1 <包名>`：打印包的直接依赖
  - `rospack depends-on <包名>`：打印谁依赖这个包

- `roscd <包名>[/子路径]`
- `rosls  <包名>[/子路径]`

- `rosrun <包名> <可执行文件> [<参数名>:=<参数值>]`
- `rosnode`
  - `list`：列举所有node
  - `info <node 名>` ：节点详细信息
- `rostopic`：话题
  - `echo <topic 名>`：显示在某个话题上发布的数据
  - `list`
  - `type <topic 名>`：消息类型
  - `pub 参数` ：把数据发布到当前某个正在广播的话题上
  - `hz <topic 名>`：数据发布的速率
- `rosmsg show <消息类型>`：查看某消息类型的详细信息
- `rosservice`：服务
- `rosparam`：参数服务器

### 创建包

软件包必须形式：

```sh
my_package/
  CMakeLists.txt  # catkin
  package.xml  # 软件包的元信息
```

#### 创建

```bash
cd ~/catkin_ws/src
catkin_create_pkg my_package std_msgs rospy roscpp
```

#### 构建

```bash
cd ~/catkin_ws
catkin_make
# or
catkin_make --source my_src  # 构建catkin_ws/my_src下的包
```

#### `package.xml`

```xml
<?xml version="1.0"?>
<package format="2">
  <!-- 包名 -->
  <name>my_package</name>
  <version>0.1.0</version>
  <description>The beginner_tutorials package</description>

  <maintainer email="you@yourdomain.tld">Your Name</maintainer>
  <license>BSD</license>
  <url type="website">http://wiki.ros.org/beginner_tutorials</url>
  <author email="you@yourdomain.tld">Jane Doe</author>

  <buildtool_depend>catkin</buildtool_depend>

  <build_depend>roscpp</build_depend>
  <build_depend>rospy</build_depend>
  <build_depend>std_msgs</build_depend>

  <exec_depend>roscpp</exec_depend>
  <exec_depend>rospy</exec_depend>
  <exec_depend>std_msgs</exec_depend>
</package>
```

## 名称

包名(pkg)：在`package.xml`中指定

可执行文件名(type)：python文件名 或在`CMakeLists`中的可执行文件名

节点名(name)：

- `.lanuch`里的`name`字段

- `ros::init(argc, argv, "node_name")`或`rospy.init_node('node_name', anonymous=False)`

常用：

- `ns/node_name` = `~`

测试代码：

```c++
#include <ros/ros.h>
#include <std_msgs/String.h>

int main(int argc, char **argv) {
    // ns = "ns"
    // name = "$(anon launch_name)"将取代下行
    ros::init(argc, argv, "node_name", ros::init_options::AnonymousName);  // /node_name_xxxxx
    ros::NodeHandle nh;  // /ns
    ros::NodeHandle nh1("nh");  // /ns/nh
    ros::NodeHandle nh2("~nh");  // ns/node_name_xxxxx/nh
    ros::NodeHandle nh3("/nh");  // /nh
    ros::NodeHandle nh4(nh2, "nh");  // ns/node_name_xxxxx/nh/nh

    ros::Publisher chatter_pub = nh.advertise<std_msgs::String>("topic", 1000);
    ros::Publisher chatter_pub1 = nh1.advertise<std_msgs::String>("topic1", 1000);
    ros::Publisher chatter_pub2 = nh2.advertise<std_msgs::String>("topic2", 1000);
    ros::Publisher chatter_pub3 = nh3.advertise<std_msgs::String>("topic3", 1000);
    ros::Publisher chatter_pub4 = nh4.advertise<std_msgs::String>("topic4", 1000);

    ros::spin();
    return 0;
}
```

输出：

```bash
$ rostopic list                  
/nh/topic3
/ns/launch_name_dell_7050_380_2087875102403258839/nh/nh/topic4
/ns/launch_name_dell_7050_380_2087875102403258839/nh/topic2
/ns/nh/topic1
/ns/topic
/rosout
/rosout_agg
```

## 常用包

`joint_state_publisher`：发布`JointState`

`robot_state_publisher`：发布`TF`