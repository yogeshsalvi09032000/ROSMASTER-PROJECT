---
title: Configuration
layout: default
nav_order: 5
---

# Configuration Parameters
{: .no_toc }

<details open markdown="block">
  <summary>Table of contents</summary>
  {: .text-delta }
1. TOC
{:toc}
</details>

---

## Robot Specifications

| Parameter | Value |
|-----------|-------|
| Footprint | 24 cm × 24 cm |
| Max Linear Speed | 0.3 m/s |
| Max Lateral Speed | 0.2 m/s (mecanum wheels) |
| Max Angular Speed | 1.0 rad/s |
| Weight Capacity | 10 kg |

---

## LiDAR Sensor

| Parameter | Value |
|-----------|-------|
| Type | 2D planar scanner |
| Minimum Range | 0.35 m |
| Maximum Range | 12 m |
| Field of View | 360° |
| Update Rate | 10 Hz |

---

## Camera

| Parameter | Value |
|-----------|-------|
| Resolution | 640 × 480 |
| Frame Rate | 30 FPS |
| Field of View | 62° |

---

## Navigation Stack Parameters

| Parameter | Value |
|-----------|-------|
| Safety Inflation Radius | 25 cm |
| Obstacle Detection Range | 3.5 m |
| Goal Position Tolerance | 15 cm |
| Goal Angle Tolerance | 15° |
| Global Costmap Update Rate | 1 Hz |
| Local Costmap Update Rate | 5 Hz |
| Local Costmap Size | 6 m × 6 m |

---

## Tuning Tips

### Increase Inflation Radius (robot colliding with obstacles)
Edit `digital_twin_description/config/navigation/costmap_common_params.yaml` and increase `inflation_radius` from `0.25` to `0.35`.

### Reduce Velocity (robot moving too fast)
Edit `digital_twin_description/config/navigation/dwa_local_planner_params.yaml` and lower `max_vel_x`, `max_vel_y`, and `max_rot_vel`.
