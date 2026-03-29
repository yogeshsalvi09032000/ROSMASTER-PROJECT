---
title: Architecture
layout: default
nav_order: 4
---

# System Architecture
{: .no_toc }

<details open markdown="block">
  <summary>Table of contents</summary>
  {: .text-delta }
1. TOC
{:toc}
</details>

---

## Overview

The system connects a Gazebo simulation of the AWS warehouse with the full ROS navigation stack, driving the ROSMASTER X3 robot to autonomously plan and execute paths to user-defined goals.

---

## Architecture Diagram

```
┌─────────────────────────────────────────────────────────────┐
│                    GAZEBO SIMULATION                         │
│  ┌─────────────────────────────────────────────────────┐   │
│  │         AWS Warehouse World                          │   │
│  │  - Static environment (shelves, walls, obstacles)   │   │
│  │  - ROSMASTER X3 Robot spawned at origin             │   │
│  │  - Dynamic obstacles (optional)                      │   │
│  └─────────────────────────────────────────────────────┘   │
└────────────────────────┬────────────────────────────────────┘
                         │
        ┌────────────────┼────────────────┐
        │                │                │
        ▼                ▼                ▼
   /scan (LaserScan) /cmd_vel (Twist) /odom (Odometry)
        │                │                │
        ▼                ▼                ▼
┌──────────────────────────────────────────────────────────────┐
│              ROS NAVIGATION STACK                             │
├──────────────────────────────────────────────────────────────┤
│                                                                │
│  ┌──────────────────┐              ┌─────────────────┐      │
│  │   Map Server     │              │   Odometry      │      │
│  │  Publishes /map  │              │  (from Gazebo)  │      │
│  └────────┬─────────┘              └────────┬────────┘      │
│           │                                 │                │
│           └─────────────┬───────────────────┘                │
│                         │                                     │
│                         ▼                                     │
│              ┌────────────────────┐                           │
│              │      AMCL          │                           │
│              │  Localization      │ ← Particle Filter        │
│              │  Position estimate │                           │
│              └─────────┬──────────┘                           │
│                        │                                      │
│              ┌─────────▼──────────┐                           │
│              │    move_base       │                           │
│              │  Navigation Core   │                           │
│          ┌───┴────────────────────┴────┐                     │
│          │                             │                     │
│     ┌────▼────────┐            ┌───────▼──────┐             │
│     │Global Planner│           │Local Planner │             │
│     │ (Custom A*) │           │    (DWA)     │             │
│     │ - Full Map  │           │ - Real-time  │             │
│     │ - Path Find │           │ - Avoidance  │             │
│     └────┬────────┘            └───────┬──────┘             │
│          │                             │                     │
│          └─────────────┬───────────────┘                      │
│                        │                                      │
│                        ▼                                      │
│              ┌────────────────────┐                           │
│              │   Costmaps         │                           │
│              │ Static + Dynamic   │                           │
│              │ Obstacle Detection │                           │
│              │ Inflation Layer    │                           │
│              └────────┬───────────┘                           │
│                       │                                       │
│                       ▼                                       │
│              ┌────────────────────┐                           │
│              │  Velocity Smoother │                           │
│              │  Command Output    │                           │
│              └────────┬───────────┘                           │
│                       │                                       │
└───────────────────────┼──────────────────────────────────────┘
                        │
                /cmd_vel (Twist)
                        │
                        ▼
        ┌───────────────────────────────┐
        │   ROSMASTER X3 Control       │
        │   • Mecanum wheel commands   │
        │   • Motor controllers        │
        │   • Wheel encoders           │
        └───────────────────────────────┘
```

---

## Component Descriptions

### Gazebo Simulation
The AWS RoboMaker small warehouse world provides the physical simulation environment. The ROSMASTER X3 robot is spawned at the origin. Gazebo publishes sensor data (LiDAR scans, odometry) and receives velocity commands.

### AMCL — Localization
Adaptive Monte Carlo Localization uses a particle filter to estimate the robot's pose within the pre-loaded map. It fuses `/scan` (LiDAR) and `/odom` (wheel odometry) to produce a probabilistic position estimate published to `/amcl_pose`.

### move_base — Navigation Core
The central hub of the navigation stack. It accepts goals from RViz, coordinates the global and local planners, and outputs `/cmd_vel` velocity commands.

### Global Planner — Custom A*
A custom A* implementation replaces the default navfn planner. It computes an optimal path from the robot's current position to the goal across the full static map.

### Local Planner — DWA
The Dynamic Window Approach planner operates in real time to follow the global path while avoiding dynamic obstacles detected by the LiDAR.

### Costmaps
Two costmaps are maintained:
- **Global costmap** — full warehouse map with obstacle inflation, updated at 1 Hz
- **Local costmap** — 6×6 m window around the robot, updated at 5 Hz

---

## Key ROS Topics

| Topic | Type | Publisher | Description |
|-------|------|-----------|-------------|
| `/scan` | `sensor_msgs/LaserScan` | Gazebo | Raw 360° LiDAR data |
| `/cmd_vel` | `geometry_msgs/Twist` | move_base | Velocity commands to robot |
| `/odom` | `nav_msgs/Odometry` | Gazebo | Wheel odometry data |
| `/map` | `nav_msgs/OccupancyGrid` | map_server | Pre-loaded warehouse map |
| `/amcl_pose` | `geometry_msgs/PoseWithCovarianceStamped` | AMCL | Localized robot position |
| `/move_base/goal` | `move_base_msgs/MoveBaseActionGoal` | RViz/User | Navigation goal |
| `/move_base/feedback` | `move_base_msgs/MoveBaseFeedback` | move_base | Navigation progress |
| `/move_base/result` | `move_base_msgs/MoveBaseActionResult` | move_base | Navigation completion status |
| `/camera/rgb/image_raw` | `sensor_msgs/Image` | Gazebo | Camera RGB feed |
| `/tf` | `tf2_msgs/TFMessage` | Various | Coordinate frame transforms |

---

## Package Descriptions

### aws-robomaker-small-warehouse-world

AWS RoboMaker warehouse environment with pre-built models and world files.

**Key files:**
- `worlds/small_warehouse.world` — Main simulation world
- `models/` — 3D models for 20+ warehouse objects (shelves, buckets, desks, lamps)
- `launch/` — World launching configurations
- `rviz/` — RViz configuration files

### digital_twin_description

ROSMASTER X3 robot description with complete navigation stack configuration.

**Key files:**
- `urdf/rosmaster_x3_gazebo.urdf` — Robot model definition
- `launch/navigation_aws_warehouse.launch` — Autonomous navigation launcher
- `launch/cartographer_aws_warehouse.launch` — SLAM launcher
- `config/` — Navigation parameters and costmap configs
- `maps/` — Pre-mapped warehouse files
- `src/custom_astar_global_planner.cpp` — Custom path planning algorithm
