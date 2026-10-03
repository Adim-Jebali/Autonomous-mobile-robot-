# 🤖 ROS 2 Autonomous Mecanum Mobile Robot

A ROS 2-based autonomous mobile robot platform featuring **Mecanum omnidirectional drive**, **Gazebo simulation**, **LiDAR perception**, **SLAM**, **autonomous navigation**, and **vision-based green line following**.

The project is designed as a modular robotics platform for developing and testing autonomous navigation and perception algorithms in simulation.

---

## 📋 Table of Contents

* [Overview](#-overview)
* [Key Features](#-key-features)
* [Package Layout](#-package-layout)
* [System Requirements](#-system-requirements)
* [Installation](#-installation)
* [Usage](#-usage)
* [Green Line Following](#-green-line-following)
* [SLAM Mapping](#-slam-mapping)
* [Architecture](#-architecture)
* [ROS 2 Topics](#-ros-2-topics)
* [Project Structure](#-project-structure)
* [Future Development](#-future-development)
* [Credits](#-credits)

---

## 🔎 Overview

This project implements a simulated **Mecanum-wheeled Autonomous Mobile Robot (AMR)** using **ROS 2**.

The platform combines robot modeling, simulation, perception, localization, mapping, navigation, and high-level task execution within a modular ROS 2 architecture.

The Mecanum drive configuration provides **omnidirectional mobility**, allowing the robot to move forward, backward, laterally, and rotate within constrained environments.

### Main objectives

* Develop a realistic ROS 2 simulation of a Mecanum AMR
* Implement omnidirectional robot control
* Integrate LiDAR-based perception
* Perform autonomous mapping using SLAM
* Enable autonomous navigation with Nav2
* Implement vision-based green line following
* Provide a modular architecture for future autonomous robotics applications

---

## 🚀 Key Features

### Mobile Robot

* Four-wheel Mecanum drive
* Omnidirectional motion
* URDF/Xacro robot description
* Simulated sensors
* ROS 2 control interface

### Simulation

* Gazebo simulation environment
* Custom simulation worlds
* RViz 2 visualization
* Robot state and TF management

### Perception

* LiDAR-based environment perception
* Camera-based visual perception
* Green line detection

### Autonomous Navigation

* SLAM-based mapping
* Robot localization
* Nav2 autonomous navigation
* Velocity command generation
* Navigation goal execution

---

# 📦 Package Layout

The ROS 2 workspace is organized into modular packages, each responsible for a specific part of the robotic system.

| Package                | Responsibility                                           |
| ---------------------- | -------------------------------------------------------- |
| `mecanum_description`  | Robot model, URDF/Xacro, meshes and sensor configuration |
| `mecanum_bringup`      | Main launch files and system initialization              |
| `mecanum_simulation`   | Gazebo worlds, models and simulation components          |
| `mecanum_control`      | Robot motion control and velocity commands               |
| `mecanum_navigation`   | SLAM, navigation, maps and Nav2 configuration            |
| `mecanum_task_manager` | High-level autonomous task and mission management        |

This modular organization makes it possible to independently develop, test, and extend each subsystem.

---

# 🧰 System Requirements

### Operating System

```text
Ubuntu 22.04 LTS
```

### ROS

```text
ROS 2 Humble
```

### Main Dependencies

* Gazebo
* RViz 2
* Nav2
* SLAM Toolbox
* `ros2_control`
* `tf2`
* Python 3
* OpenCV
* `cv_bridge`
* `colcon`
* `rosdep`

---

# ⚙️ Installation

## 1. Create a ROS 2 workspace

```bash
mkdir -p ~/ros2_ws/src
cd ~/ros2_ws/src
```

## 2. Clone the repository

```bash
git clone https://github.com/YOUR_USERNAME/YOUR_REPOSITORY.git
```

Move into the workspace:

```bash
cd ~/ros2_ws
```

## 3. Install dependencies

```bash
rosdep update
rosdep install --from-paths src --ignore-src -r -y
```

## 4. Build the workspace

```bash
colcon build --symlink-install
```

## 5. Source the workspace

```bash
source ~/ros2_ws/install/setup.bash
```

For convenience, the workspace can be added to `.bashrc`:

```bash
echo "source ~/ros2_ws/install/setup.bash" >> ~/.bashrc
```

---

# ▶️ Usage

## Launch the complete simulation

```bash
ros2 launch mecanum_bringup sim_bringup.launch.py
```

This launches the Mecanum robot in the Gazebo simulation environment.

---

## Launch a minimal environment

```bash
ros2 launch mecanum_bringup sim_bringup.launch.py world:=minimal
```

---

## Launch an empty environment

```bash
ros2 launch mecanum_bringup sim_bringup.launch.py world:=empty
```

---

## Launch simulation with navigation

```bash
ros2 launch mecanum_bringup sim_bringup.launch.py launch_navigation:=true
```

---

# 🟢 Green Line Following

The robot can perform **vision-based green line following** using its onboard camera.

The perception pipeline detects the green path on the ground and generates motion commands allowing the Mecanum robot to follow the detected trajectory.


https://github.com/user-attachments/assets/35271f2d-414e-43e3-ad6a-444b0617a3a7


### Processing pipeline

```text
Camera
   │
   ▼
Image Acquisition
   │
   ▼
Color Segmentation
   │
   ▼
Green Line Detection
   │
   ▼
Line Position / Error Estimation
   │
   ▼
Control Algorithm
   │
   ▼
Velocity Command
   │
   ▼
Mecanum Drive
```

### Main stages

1. Capture camera frames
2. Convert the image to an appropriate color space
3. Extract the green region
4. Estimate the position of the line
5. Calculate the tracking error
6. Generate the appropriate velocity command
7. Send the command to the robot

> **Note:** The exact launch and execution commands depend on the implementation present in `mecanum_control`.

---

# 🗺️ SLAM Mapping

The robot uses LiDAR data to construct a representation of the simulated environment using **SLAM (Simultaneous Localization and Mapping)**.

### SLAM pipeline

```text
LiDAR
  │
  ▼
LaserScan
  │
  ▼
SLAM
  │
  ├── Robot Pose
  │
  └── Occupancy Grid Map
             │
             ▼
          Map Server
             │
             ▼
            Nav2
```

### Launch the simulation

```bash
ros2 launch mecanum_bringup sim_bringup.launch.py
```

### Start SLAM

Use the SLAM launch configuration provided by the navigation package.

Then control the robot manually:

```bash
ros2 run teleop_twist_keyboard teleop_twist_keyboard
```

Drive the robot throughout the environment so that the LiDAR can progressively construct the map.

### Save the map

Once mapping is complete:

```bash
ros2 run nav2_map_server map_saver_cli -f ~/ros2_ws/src/mecanum_navigation/maps/my_map
```

The resulting map can then be used for autonomous navigation.

---

# 🏗️ Architecture

The system follows a modular ROS 2 architecture in which simulation, perception, control, navigation, and task management are separated into independent components.
<img width="2536" height="1494" alt="ros2_architecture" src="https://github.com/user-attachments/assets/d495ee6f-db8a-4dd7-b5f0-43dcd8a8f15a" />



# 📡 ROS 2 Topics

The main communication interfaces can be inspected using the ROS 2 CLI.

### Common Topics

| Topic               | Message Type                 | Purpose                    |
| ------------------- | ---------------------------- | -------------------------- |
| `/cmd_vel`          | `geometry_msgs/msg/Twist`    | Robot velocity commands    |
| `/odom`             | `nav_msgs/msg/Odometry`      | Robot odometry             |
| `/scan`             | `sensor_msgs/msg/LaserScan`  | LiDAR measurements         |
| `/map`              | `nav_msgs/msg/OccupancyGrid` | Generated occupancy map    |
| `/tf`               | `tf2_msgs/msg/TFMessage`     | Coordinate transformations |
| `/tf_static`        | `tf2_msgs/msg/TFMessage`     | Static transformations     |
| `/camera/image_raw` | `sensor_msgs/msg/Image`      | Camera images              |

### List all active topics

```bash
ros2 topic list
```

### Inspect a topic

```bash
ros2 topic echo /cmd_vel
```

### Check topic type

```bash
ros2 topic type /scan
```

### Display topic information

```bash
ros2 topic info /scan
```

---

# 📁 Project Structure

```text
ros2-autonomous-mecanum-robot/
│
├── README.md
├── .gitignore
│
└── src/
    │
    ├── mecanum_description/
    │   ├── urdf/
    │   ├── meshes/
    │   ├── rviz/
    │   └── launch/
    │
    ├── mecanum_bringup/
    │   ├── launch/
    │   └── config/
    │
    ├── mecanum_simulation/
    │   ├── worlds/
    │   ├── models/
    │   └── launch/
    │
    ├── mecanum_control/
    │   ├── src/
    │   ├── scripts/
    │   └── config/
    │
    ├── mecanum_navigation/
    │   ├── launch/
    │   ├── maps/
    │   └── config/
    │
    └── mecanum_task_manager/
        ├── src/
        ├── scripts/
        └── launch/
```

---

# 🔭 Future Development

The architecture is designed to support future extensions such as:

* Object detection with YOLO
* RGB-D perception
* Autonomous obstacle avoidance
* Behavior Tree-based mission orchestration
* Advanced task planning
* Mobile manipulation
* Multi-robot coordination
* Reinforcement Learning
* Deployment on a physical AMR platform

---

# 👤 Author

**Adim Jebali**

Mechatronics Engineering Student
**Robotics & Autonomous Systems**

Areas of interest:

`ROS 2` · `Mobile Robotics` · `Autonomous Navigation` · `Computer Vision` · `AI for Robotics` · `Robotic Manipulation`
