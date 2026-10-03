<h1 align="center">ROS 2 Autonomous Mecanum Mobile Robot</h1>

<p align="center">
  Simulation and navigation stack for an omnidirectional autonomous mobile robot (AMR):<br>
  Mecanum drive · LiDAR SLAM · Nav2 · Vision-based line following
</p>

<p align="center">
  <img src="https://img.shields.io/badge/ROS%202-Humble-22314E?logo=ros" alt="ROS 2 Humble">
  <img src="https://img.shields.io/badge/Ubuntu-22.04-E95420?logo=ubuntu&logoColor=white" alt="Ubuntu 22.04">
  <img src="https://img.shields.io/badge/Simulation-Gazebo-FF6F00" alt="Gazebo">
  <img src="https://img.shields.io/badge/Navigation-Nav2-0A7BBB" alt="Nav2">
  
</p>

---

## Table of Contents

1. [Overview](#overview)
2. [Key Features](#key-features)
3. [Demo](#demo)
4. [Package Layout](#package-layout)
5. [System Requirements](#system-requirements)
6. [Installation](#installation)
7. [Usage](#usage)
8. [Green Line Following](#green-line-following)
9. [SLAM Mapping](#slam-mapping)
10. [System Architecture](#system-architecture)
11. [ROS 2 Topics](#ros-2-topics)
12. [Project Structure](#project-structure)
13. [Roadmap](#roadmap)
14. [Credits and License](#credits-and-license)
15. [Author](#author)

---

## Overview

This project implements a simulated **Mecanum-wheeled Autonomous Mobile Robot (AMR)** on **ROS 2**. It combines robot modeling, simulation, perception, localization, mapping, navigation and high-level task execution in a modular architecture.

The Mecanum drive provides **omnidirectional mobility**: the robot can move forward, backward and sideways, and rotate in place, which is well suited to constrained environments such as warehouses.

**Objectives**

- Build a realistic ROS 2 simulation of a Mecanum AMR
- Implement omnidirectional motion control
- Integrate LiDAR-based perception
- Perform autonomous mapping with SLAM
- Enable autonomous navigation with Nav2
- Implement vision-based green line following
- Provide a modular base for future robotics developments

---

## Key Features

| Domain | Capabilities |
| ------ | ------------ |
| **Robot** | Four-wheel Mecanum drive, omnidirectional motion, URDF/Xacro model, simulated sensors, ROS 2 control interface |
| **Simulation** | Gazebo environments (warehouse, minimal, empty), RViz 2 visualization, TF and robot state management |
| **Perception** | LiDAR environment sensing, camera-based perception, green line detection |
| **Navigation** | SLAM mapping, localization, Nav2 autonomous navigation, velocity command generation, goal execution |

---

## Demo

<table>
  <tr>
    <td align="center" width="50%">
      <img src="https://github.com/user-attachments/assets/4cd3e89a-0edc-4156-91e0-c0ddeaf1143e" alt="SLAM mapping in the warehouse" width="426"><br>
      <sub><b>SLAM mapping</b> in the warehouse world</sub>
    </td>
    <td align="center" width="50%">
      <img src="https://github.com/user-attachments/assets/c32f9565-642e-4c64-b3fd-41212d267bd6" alt="Generated occupancy grid map" width="421"><br>
      <sub><b>Occupancy grid</b> generated from LiDAR data</sub>
    </td>
  </tr>
</table>

---

## Package Layout

The workspace is split into modular packages, each responsible for one subsystem, so that they can be developed, tested and extended independently.

| Package | Responsibility |
| ------- | -------------- |
| `mecanum_description` | Robot model, URDF/Xacro, meshes and sensor configuration |
| `mecanum_bringup` | Main launch files and system initialization |
| `mecanum_simulation` | Gazebo worlds, models and simulation components |
| `mecanum_control` | Motion control, velocity commands and line following |
| `mecanum_navigation` | SLAM, Nav2 configuration and maps |
| `mecanum_task_manager` | High-level task and mission management |

---

## System Requirements

| Component | Version |
| --------- | ------- |
| Operating system | Ubuntu 22.04 LTS |
| ROS 2 | Humble |
| Simulator | Gazebo, RViz 2 |

**Main dependencies:** Nav2, SLAM Toolbox, `ros2_control`, `tf2`, Python 3, OpenCV, `cv_bridge`, `colcon`, `rosdep`.

---

## Installation

**1. Clone the repository**

```bash
git clone https://github.com/Adim-Jebali/Autonomous-mobile-robot-.git ~/ros2_ws
cd ~/ros2_ws
```

**2. Install dependencies**

```bash
rosdep update
rosdep install --from-paths src --ignore-src -r -y
```

**3. Build the workspace**

```bash
colcon build --symlink-install
```

**4. Source the workspace**

```bash
source ~/ros2_ws/install/setup.bash
```

To source it automatically in every new terminal:

```bash
echo "source ~/ros2_ws/install/setup.bash" >> ~/.bashrc
```

---

## Usage

| Goal | Command |
| ---- | ------- |
| Full simulation (default warehouse world) | `ros2 launch mecanum_bringup sim_bringup.launch.py` |
| Minimal environment | `ros2 launch mecanum_bringup sim_bringup.launch.py world:=minimal` |
| Empty environment | `ros2 launch mecanum_bringup sim_bringup.launch.py world:=empty` |
| Simulation with navigation | `ros2 launch mecanum_bringup sim_bringup.launch.py launch_navigation:=true` |

---

## Green Line Following

The robot follows a green line on the ground using its onboard camera. The perception pipeline segments the green path, estimates the tracking error and generates velocity commands for the Mecanum drive.

https://github.com/user-attachments/assets/35271f2d-414e-43e3-ad6a-444b0617a3a7

**Processing pipeline**

```text
Camera → Image acquisition → Color segmentation → Green line detection
       → Position / error estimation → Control algorithm
       → Velocity command (/cmd_vel) → Mecanum drive
```

**Run**

```bash
# Start the simulation first
ros2 launch mecanum_bringup sim_bringup.launch.py

# Detection view only (no motion)
ros2 run mecanum_control green_lane_detector.py

# Follow the line autonomously
ros2 run mecanum_control green_lane_detector.py --follow
```

---

## SLAM Mapping

The robot uses LiDAR data to build a map of the environment with **SLAM (Simultaneous Localization and Mapping)**.

**1. Launch the simulation with SLAM**

```bash
ros2 launch mecanum_navigation test_mapping_slam.launch.py world:=warehouse
```

**2. Drive the robot manually** (second terminal) so the LiDAR can progressively build the map:

```bash
ros2 run teleop_twist_keyboard teleop_twist_keyboard
```

**3. Save the map** once the environment is fully covered:

```bash
ros2 run nav2_map_server map_saver_cli -f ~/ros2_ws/src/mecanum_navigation/maps/my_map
```

The saved map can then be loaded by Nav2 for autonomous navigation.

---

## System Architecture

Simulation, perception, control, navigation and task management are separated into independent ROS 2 components that communicate through topics.

<p align="center">
  <img src="https://github.com/user-attachments/assets/d495ee6f-db8a-4dd7-b5f0-43dcd8a8f15a" alt="ROS 2 system architecture" width="900">
</p>

---

## ROS 2 Topics

| Topic | Message type | Purpose |
| ----- | ------------ | ------- |
| `/cmd_vel` | `geometry_msgs/msg/Twist` | Robot velocity commands |
| `/odom` | `nav_msgs/msg/Odometry` | Robot odometry |
| `/scan` | `sensor_msgs/msg/LaserScan` | LiDAR measurements |
| `/map` | `nav_msgs/msg/OccupancyGrid` | Generated occupancy map |
| `/camera/image_raw` | `sensor_msgs/msg/Image` | Camera images |
| `/tf`, `/tf_static` | `tf2_msgs/msg/TFMessage` | Coordinate transformations |

**Useful inspection commands**

```bash
ros2 topic list            # list active topics
ros2 topic echo /cmd_vel   # print messages on a topic
ros2 topic info /scan      # type and publisher/subscriber count
```

---

## Project Structure

```text
Autonomous-mobile-robot-/
├── README.md
├── .gitignore
└── src/
    ├── mecanum_description/     # urdf, meshes, rviz, launch
    ├── mecanum_bringup/         # launch, config
    ├── mecanum_simulation/      # worlds, models, launch
    ├── mecanum_control/         # src, scripts, config
    ├── mecanum_navigation/      # launch, maps, config
    └── mecanum_task_manager/    # src, scripts, launch
```

---

## Roadmap

- [ ] Object detection with YOLO
- [ ] RGB-D perception
- [ ] Autonomous obstacle avoidance
- [ ] Behavior Tree mission orchestration
- [ ] Advanced task planning
- [ ] Mobile manipulation
- [ ] Multi-robot coordination
- [ ] Reinforcement learning
- [ ] Deployment on a physical AMR platform

---

## Credits and License

This project is based on the open-source work
[Trkkhrmn/ros2_amr_mecanumbot](https://github.com/Trkkhrmn/ros2_amr_mecanumbot)
(MIT License), which provided the original Mecanum AMR stack. Adaptations, documentation and further development are by the author below.

Released under the [MIT License](LICENSE).

---

## Author

**Adim Jebali**
Mechatronics Engineering Student: Robotics & Autonomous Systems

`ROS 2` · `Mobile Robotics` · `Autonomous Navigation` · `Computer Vision` · `AI for Robotics` · `Robotic Manipulation`
