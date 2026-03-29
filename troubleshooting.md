---
title: Troubleshooting
layout: default
nav_order: 6
---

# Troubleshooting
{: .no_toc }

<details open markdown="block">
  <summary>Table of contents</summary>
  {: .text-delta }
1. TOC
{:toc}
</details>

---

## Robot Doesn't Spawn in Gazebo

Check that both packages are visible to ROS and rebuild the workspace:

```bash
rospack find digital_twin_description
rospack find aws_robomaker_small_warehouse_world

cd ~/yahboomcar_ws
catkin_make clean
catkin_make
source devel/setup.bash
```

---

## Map Not Loading

Verify the map files exist in the expected location:

```bash
ls ~/yahboomcar_ws/digital_twin_description/maps/
```

You should see:
```
aws_warehouse_cartographer_map.pgm
aws_warehouse_cartographer_map.yaml
```

---

## Robot Keeps Colliding With Obstacles

**Option 1 — Increase inflation radius:**
Edit `digital_twin_description/config/navigation/costmap_common_params.yaml` and increase `inflation_radius` from `0.25` to `0.35`.

**Option 2 — Reduce robot velocity:**
Edit `digital_twin_description/config/navigation/dwa_local_planner_params.yaml` and reduce `max_vel_x`, `max_vel_y`, and `max_rot_vel`.

---

## RViz Not Showing Map

1. In RViz, verify **Fixed Frame** is set to `map`
2. Check the **Map** display is subscribed to the `/map` topic
3. Confirm `map_server` is running: `rosnode list | grep map`

---

## Gazebo Crashes or Slow Performance

- Close other resource-heavy applications
- Reduce graphics quality: **View → Camera Angle** in Gazebo
- Check system resources: `top` or `htop`

---

## Connection Refused Errors

Verify that a ROS master is running:

```bash
echo $ROS_MASTER_URI
rosnode list
```

If issues persist:

```bash
killall rosmaster
roscore &
```

---

## Navigation Not Responding to Goals

1. Confirm AMCL has converged — the `/amcl_pose` topic should be publishing
2. Verify the goal location is reachable (not inside an obstacle)
3. Check move_base status:
   ```bash
   rostopic echo /move_base/status
   ```

---

## Additional Resources

- **Detailed package docs:** `digital_twin_description/digital_twin_README.md`
- [ROS Navigation Stack Wiki](http://wiki.ros.org/navigation)
- [move_base Documentation](http://wiki.ros.org/move_base)
- [Google Cartographer ROS](https://google-cartographer-ros.readthedocs.io)
- [AMCL Documentation](http://wiki.ros.org/amcl)
- [Gazebo Tutorials](https://gazebosim.org/tutorials)
- [ROS TF Documentation](http://wiki.ros.org/tf)
