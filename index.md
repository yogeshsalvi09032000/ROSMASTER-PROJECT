---
title: Overview
layout: default
---

# ROSMASTER Perception Pipeline

This project implements a ROS-based perception pipeline using an Astra RGB camera and OpenCV for real-time feature tracking.

## System Overview

- RGB input from Astra camera
- Feature detection using OpenCV (goodFeaturesToTrack)
- Visualization using RQT

## Pipeline Execution

```bash
roslaunch astra_camera astrapro.launch
roslaunch opencv_apps goodfeature_track.launch image:=/camera/rgb/image_raw
rqt_image_view
