---
title: Overview
layout: default
---

# ROSMASTER Autonomous Warehouse System

This project implements a complete autonomous navigation system for the ROSMASTER X3 robot using a digital twin and AWS RoboMaker warehouse simulation.

## System Scope

- Digital twin of ROSMASTER X3 in Gazebo
- AWS warehouse simulation environment
- Full ROS navigation stack (AMCL, move_base, costmaps)
- Autonomous goal-based navigation
- SLAM and mapping capability

## Core Components

### 1. Simulation Environment
- AWS RoboMaker small warehouse world
- Pre-built models: shelves, obstacles, layouts

### 2. Digital Twin
- URDF-based robot model
- Sensors: LiDAR + RGB camera
- Mecanum wheel motion model

### 3. Navigation Stack
- Localization using AMCL
- Global planning using custom A*
- Local planning using DWA
- Costmaps for obstacle avoidance

## System Architecture
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
│  ┌──────────────────┐              ┌─────────────────┐       │
│  │   Map Server     │              │   Odometry      │       │
│  │  Publishes /map  │              │  (from Gazebo)  │       │
│  └────────┬─────────┘              └────────┬────────┘       │
│           │                                 │                │
│           └─────────────┬───────────────────┘                │
│                         │                                    │
│                         ▼                                    │
│              ┌────────────────────┐                          │
│              │      AMCL          │                          │
│              │  Localization      │ (Particle Filter)        │
│              │  Position estimate │                          │
│              └─────────┬──────────┘                          │
│                        │                                     │
│              ┌─────────▼──────────┐                          │
│              │    move_base       │                          │
│              │  Navigation Core   │                          │
│          ┌───┴────────────────────┴────┐                     │
│          │                             │                     │
│     ┌────▼────────┐            ┌───────▼──────┐              │
│     │Global Planner│           │Local Planner │              │
│     │(Custom A*)   │           │   (DWA)      │              │
│     │- Full Map    │           │- Real-time   │              │
│     │- Finds Path  │           │- Avoidance   │              │
│     └────┬────────┘            └───────┬──────┘              │
│          │                             │                     │
│          └─────────────┬───────────────┘                     │
│                        │                                     │
│                        ▼                                     │
│              ┌────────────────────┐                          │
│              │   Costmaps         │                          │
│              │ Static + Dynamic   │                          │
│              │ Obstacle Detection │                          │
│              │ Inflation Layer    │                          │
│              └────────┬───────────┘                          │
│                       │                                      │
│                       ▼                                      │
│              ┌────────────────────┐                          │
│              │  Velocity Smoother │                          │
│              │  Command Output    │                          │
│              └────────┬───────────┘                          │
│                       │                                      │
└───────────────────────┼──────────────────────────────────────┘
                        │
                /cmd_vel (Twist)
                        │
                        ▼
        ┌───────────────────────────────┐
        │   ROSMASTER X3 Control        │
        │   • Mecanum wheel commands    │
        │   • Motor controllers         │
        │   • Wheel encoders            │
        └───────────────────────────────┘


## Key Features

- Autonomous navigation to user-defined goals
- Pre-mapped warehouse navigation
- Real-time obstacle avoidance
- SLAM-based map generation
- Custom global planner implementation

     

