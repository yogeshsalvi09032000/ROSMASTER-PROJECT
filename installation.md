---
title: Installation
layout: default
nav_order: 2
---

# Installation
{: .no_toc }

<details open markdown="block">
  <summary>Table of contents</summary>
  {: .text-delta }
1. TOC
{:toc}
</details>

---

## Prerequisites

Before starting, ensure your system has the following:

- Ubuntu **18.04** or **20.04**
- ROS **Melodic** or **Noetic**
- Gazebo **9+**
- A catkin workspace already set up
- All `yahboomcar_*` packages already installed

---

## Step 1 — Clone the Repository

Clone this repository into your catkin workspace source folder:

```bash
cd ~/yahboomcar_ws/src
git clone git@github.com:ASU-EGR530-PROJECT/EGR-530-ROSMASTER-PROJECT.git temp_file
```

Then copy the package folders from `temp_file/` into `src/` and remove the temporary folder:

```bash
cp -r temp_file/aws-robomaker-small-warehouse-world .
cp -r temp_file/digital_twin_description .
rm -rf temp_file
```

---

## Step 2 — Install ROS Dependencies

```bash
sudo apt update
sudo apt install -y \
    ros-melodic-navigation \
    ros-melodic-gmapping \
    ros-melodic-cartographer \
    ros-melodic-cartographer-ros \
    ros-melodic-teleop-twist-keyboard \
    ros-melodic-map-server \
    ros-melodic-amcl \
    ros-melodic-move-base \
    ros-melodic-dynamicReconfigure
```

> **Note:** Replace `melodic` with `noetic` if using ROS Noetic.

---

## Step 3 — Configure Gazebo Model Path

Add the warehouse models to your Gazebo model path so Gazebo can find them at launch:

```bash
echo 'export GAZEBO_MODEL_PATH=$GAZEBO_MODEL_PATH:~/yahboomcar_ws/aws-robomaker-small-warehouse-world/models' >> ~/.bashrc
source ~/.bashrc
```

---

## Step 4 — Build the Workspace

```bash
cd ~/yahboomcar_ws
catkin_make
source devel/setup.bash
```

---

## Verification Checklist

After installation, run through this checklist to confirm everything is set up correctly:

- [ ] Both packages present in `~/yahboomcar_ws/src/`
- [ ] Dependencies installed: `rosdep check --from-paths .`
- [ ] Workspace builds cleanly: `catkin_make` succeeds with no errors
- [ ] Packages found by ROS: `rospack find digital_twin_description`
- [ ] GAZEBO_MODEL_PATH is set: `echo $GAZEBO_MODEL_PATH`
- [ ] Basic world launches: `roslaunch aws_robomaker_small_warehouse_world small_warehouse.launch`
- [ ] Robot spawns correctly: `roslaunch digital_twin_description spawn_robot_aws.launch`
- [ ] RViz opens with the navigation configuration

---

## Repository Structure

```
EGR-530-ROSMASTER-PROJECT/
├── README.md
├── aws-robomaker-small-warehouse-world/
│   ├── CMakeLists.txt
│   ├── package.xml
│   ├── launch/
│   │   ├── small_warehouse.launch
│   │   ├── no_roof_small_warehouse.launch
│   │   └── view_small_warehouse.launch
│   ├── models/           # 20+ warehouse object models
│   ├── worlds/
│   │   └── small_warehouse.world
│   ├── maps/
│   └── rviz/
│
└── digital_twin_description/
    ├── CMakeLists.txt
    ├── package.xml
    ├── urdf/
    │   └── rosmaster_x3_gazebo.urdf
    ├── launch/
    │   ├── spawn_robot_aws.launch
    │   ├── navigation_aws_warehouse.launch
    │   ├── cartographer_aws_warehouse.launch
    │   └── slam_aws_warehouse.launch
    ├── config/
    │   ├── navigation/
    │   │   ├── costmap_common_params.yaml
    │   │   ├── global_costmap_params_map.yaml
    │   │   ├── local_costmap_params_map.yaml
    │   │   ├── dwa_local_planner_params.yaml
    │   │   └── custom_global_planner_params.yaml
    │   └── cartographer/
    │       └── cartographer.lua
    ├── maps/
    │   ├── aws_warehouse_cartographer_map.pgm
    │   └── aws_warehouse_cartographer_map.yaml
    ├── src/
    │   └── custom_astar_global_planner.cpp
    └── include/
        └── digital_twin_description/
            └── custom_astar_global_planner.h
```
