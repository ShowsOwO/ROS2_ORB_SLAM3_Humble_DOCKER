# ROS 2 Humble ORB-SLAM3 Wrapper (Docker Ready)

这是一个适用于 **ROS 2 Humble** 的 [ORB-SLAM3](https://github.com/UZ-SLAMLab/ORB_SLAM3) 封装节点。
仅用于个人学习。
本项目已在 Ubuntu 22.04 (ROS 2 Humble) 环境下，配合 TurtleBot3 仿真通过测试。

![ROS2](https://img.shields.io/badge/ROS2-Humble-blue)
![License](https://img.shields.io/badge/License-GPLv3-green)

## ✨ 主要功能
- 支持单目 (Monocular) 模式（已测试）。
- 解决了 ROS 2 Image 消息到 OpenCV 格式的转换问题。
- 修复了原始 ORB-SLAM3 在单目模式下强制要求 `Camera1` 参数命名的问题。
- 提供了针对 Gazebo 仿真环境的配置文件。

## 🛠️ 依赖项 (Prerequisites)
在编译此包之前，请确保你已经安装并编译了原始的 ORB-SLAM3 库。

- **ROS 2 Humble**
- **OpenCV 4.x**
- **Pangolin**
- **ORB_SLAM3** (核心库)

## 📦 安装编译

1. **克隆代码到工作空间**
   ```bash
   cd ~/ros2_orb_ws/src
   git clone [https://github.com/ShowsOwO/ROS2_ORB_SLAM3_Humble_DOCKER.git](https://github.com/ShowsOwO/ROS2_ORB_SLAM3_Humble_DOCKER.git)
   ```
   
2. **编译**
   ```bash
   cd ~/ros2_orb_ws
   colcon build --symlink-install --packages-select orbslam3
   source install/setup.bash
   ```
   
## 🚀 运行指南 (Usage)
1. 启动仿真环境

⚠️ 注意：为了更容易初始化 SLAM，建议使用纹理丰富的环境（如 turtlebot3_house），避免使用默认的空旷环境。

  ```bash

  export TURTLEBOT3_MODEL=waffle
  ros2 launch turtlebot3_gazebo turtlebot3_house.launch.py
  ```

2. 启动 ORB-SLAM3 节点
需要提供 词袋文件 (Vocabulary) 和 参数文件 (.yaml) 的绝对路径。

```bash
# 请根据你的实际路径修改参数
ros2 run orbslam3 mono \
    /path/to/ORBvoc.txt \
    ~/ros2_orb_ws/src/ROS2_ORB_SLAM3_Humble_DOCKER/config/my_robot_cam.yaml \
    --ros-args -p use_sim_time:=true
```

3. 控制机器人进行初始化
打开一个新的终端启动键盘控制：

```Bash
ros2 run turtlebot3_teleop teleop_keyboard
```

初始化技巧：

确保 DEBUG_VIEW 窗口能看到明显的纹理（如书架、墙角），不要对着纯色的墙或圆柱体。

控制机器人左右旋转或轻微前后移动。

当看到绿色的特征点出现，且状态从 WAITING FOR IMAGES 变为建图状态，即表示初始化成功。

## ⚙️ 配置文件说明 (Important)
在 config/my_robot_cam.yaml 中，有一个关键的注意事项： 即使使用单目 (Monocular) 模式，ORB-SLAM3 的底层逻辑依然要求内参参数以 Camera1 开头，否则会报错。

正确示例：

```YAML

Camera.type: "PinHole"
Camera1.fx: 554.25469  # 必须叫 Camera1
Camera1.fy: 554.25469
```
## 📝 常见问题 (Troubleshooting)
Q: 画面显示 "WAITING FOR IMAGES" 且不初始化？

A:检查 YAML 文件中的分辨率是否与相机发布的分辨率一致。

检查 Gazebo 环境是否纹理太少（由于 ORB 特征点提取困难）,尝试将机器人移动到纹理复杂的区域。

检查是否开启了 use_sim_time:=true。

## 👏 致谢
本项目基于 [ORB_SLAM3](https://github.com/UZ-SLAMLab/ORB_SLAM3) 原作修改适配。
