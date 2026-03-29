---
title: Overview
layout: home
nav_order: 1
---

# EGR-530: Digital Twin & Warehouse Simulation

Complete autonomous navigation system for the **ROSMASTER X3** robot with AWS warehouse simulation environment.

---

## Project Summary

This project implements two new ROS packages for the EGR-530 course:

| Package | Description |
|---------|-------------|
| `aws-robomaker-small-warehouse-world` | AWS RoboMaker warehouse simulation environment |
| `digital_twin_description` | ROSMASTER X3 robot description with full navigation stack |

> **Note:** This repository should be cloned into `~/yahboomcar_ws/` alongside existing `yahboomcar_*` packages.

---

## Key Features

- **Digital Twin** — URDF-based ROSMASTER X3 model with LiDAR and RGB camera sensors
- **AWS Warehouse Simulation** — Pre-built warehouse world with shelves, obstacles, and layout
- **Autonomous Navigation** — Goal-based navigation using AMCL, move_base, and custom A* planner
- **SLAM Mapping** — Create new maps using gmapping or Google Cartographer
- **Pre-mapped Navigation** — Navigate a pre-built warehouse map out of the box
- **Mecanum Wheel Support** — Full lateral movement capability

---

## System Scope

- Digital twin of ROSMASTER X3 in Gazebo
- AWS warehouse simulation environment
- Full ROS navigation stack (AMCL, move_base, costmaps)
- Autonomous goal-based navigation via RViz
- SLAM and mapping capability (gmapping & cartographer)
- Custom global A* path planner

---

## System Architecture

```
┌─────────────────────────────────────────────────────────────┐
│                    GAZEBO SIMULATION                        │
│  ┌─────────────────────────────────────────────────────┐    │
│  │         AWS Warehouse World                         │    │
│  │  - Static environment (shelves, walls, obstacles)   │    │
│  │  - ROSMASTER X3 Robot spawned at origin             │    │
│  │  - Dynamic obstacles (optional)                     │    │
│  └─────────────────────────────────────────────────────┘    │
└────────────────────────┬────────────────────────────────────┘
                         │
        ┌────────────────┼────────────────┐
        │                │                │
        ▼                ▼                ▼
   /scan (LaserScan) /cmd_vel (Twist) /odom (Odometry)
        │                │                │
        ▼                ▼                ▼
┌──────────────────────────────────────────────────────────────┐
│              ROS NAVIGATION STACK                            │
├──────────────────────────────────────────────────────────────┤
│                                                              │
│  ┌──────────────────┐         ┌─────────────────┐            │
│  │   Map Server     │         │   Odometry      │            │
│  │  Publishes /map  │         │  (from Gazebo)  │            │
│  └────────┬─────────┘         └────────┬────────┘            │
│           └──────────────┬─────────────┘                     │
│                          ▼                                   │
│              ┌────────────────────┐                          │
│              │      AMCL          │ ← Particle Filter        │
│              │  Position estimate │                          │
│              └─────────┬──────────┘                          │
│                        ▼                                     │
│              ┌────────────────────┐                          │
│              │    move_base       │                          │
│              │  Navigation Core   │                          │
│          ┌───┴────────────────────┴────┐                     │
│          │                             │                     │
│     ┌────▼────────┐           ┌────────▼─────┐               │
│     │Global Planner│          │ Local Planner│               │
│     │ (Custom A*) │           │    (DWA)     │               │
│     │ - Full Map  │           │ - Real-time  │               │
│     │ - Path Find │           │ - Avoidance  │               │
│     └────┬────────┘           └────────┬─────┘               │
│          └──────────────┬──────────────┘                     │
│                         ▼                                    │
│              ┌────────────────────┐                          │
│              │   Costmaps         │                          │
│              │ Static + Dynamic   │                          │
│              │ Obstacle Detection │                          │
│              │ Inflation Layer    │                          │
│              └────────┬───────────┘                          │
│                       ▼                                      │
│              ┌────────────────────┐                          │
│              │  Velocity Smoother │                          │
│              │  Command Output    │                          │
│              └────────┬───────────┘                          │
└───────────────────────┼──────────────────────────────────────┘
                        │ /cmd_vel (Twist)
                        ▼
        ┌───────────────────────────────┐
        │   ROSMASTER X3 Control        │
        │   • Mecanum wheel commands    │
        │   • Motor controllers         │
        │   • Wheel encoders            │
        └───────────────────────────────┘
```

For a detailed breakdown of each component, see the [Architecture](architecture) page.

---

## Quick Links

- [Installation](installation) — Prerequisites and setup steps
- [Usage](usage) — Launch navigation or SLAM
- [Architecture](architecture) — System design and ROS topics
- [Configuration](configuration) — Robot specs and navigation parameters
- [Troubleshooting](troubleshooting) — Common issues and fixes

---

*Developed for ASU EGR-530 — ROS Melodic / Noetic on Ubuntu 18.04 / 20.04*
