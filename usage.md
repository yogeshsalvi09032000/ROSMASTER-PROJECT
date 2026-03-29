---
title: Usage
layout: default
nav_order: 3
---

# Usage
{: .no_toc }

<details open markdown="block">
  <summary>Table of contents</summary>
  {: .text-delta }
1. TOC
{:toc}
</details>

---

## Option A: Autonomous Navigation (Pre-mapped Warehouse)

Use this option when you want to navigate the warehouse using the pre-built map — no mapping required.

### Launch

Open a terminal, source your workspace, and launch:

```bash
source ~/yahboomcar_ws/devel/setup.bash
roslaunch digital_twin_description navigation_aws_warehouse.launch
```

RViz will open automatically with the warehouse map loaded.

### Setting Navigation Goals in RViz

1. Wait for Gazebo and RViz to fully load
2. In RViz, click the **"2D Nav Goal"** button in the toolbar (pink arrow icon)
3. Click any location on the map as the destination
4. Drag the arrow to set the desired robot orientation
5. The robot will autonomously plan and navigate to the goal

### Topics to Monitor

| Topic | Description |
|-------|-------------|
| `/map` | Warehouse occupancy grid map |
| `/amcl_pose` | Robot's estimated position (AMCL) |
| `/move_base/goal` | Active navigation goal |
| `/cmd_vel` | Velocity commands sent to the robot |

---

## Option B: Create a New Map with SLAM

Use this option to map a new environment or update an existing map.

### Terminal 1 — Launch SLAM

**Using gmapping:**
```bash
source ~/yahboomcar_ws/devel/setup.bash
roslaunch digital_twin_description slam_aws_warehouse.launch
```

**Using Cartographer (recommended):**
```bash
source ~/yahboomcar_ws/devel/setup.bash
roslaunch digital_twin_description cartographer_aws_warehouse.launch
```

### Terminal 2 — Teleoperate the Robot

```bash
source ~/yahboomcar_ws/devel/setup.bash
rosrun teleop_twist_keyboard teleop_twist_keyboard.py
```

**Keyboard controls:**

| Key | Action |
|-----|--------|
| `i` | Move forward |
| `,` | Move backward |
| `j` | Turn left |
| `l` | Turn right |
| `k` | Stop |
| `u`, `o` | Rotate while moving |
| `m`, `.` | Speed adjustment |

Drive the robot around the warehouse until you have full coverage of the map.

### Terminal 3 — Save the Map

Once coverage is complete:

```bash
source ~/yahboomcar_ws/devel/setup.bash

# If using Cartographer: finish the trajectory first
rosservice call /finish_trajectory 0

# Save the map
cd ~/yahboomcar_ws/digital_twin_description/maps
rosrun map_server map_saver -f my_custom_map
```

This creates `my_custom_map.pgm` and `my_custom_map.yaml` in the maps directory.
