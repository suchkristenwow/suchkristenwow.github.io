---
layout: page
title: Restorebot
description: An Autonomous Robotic System for Degraded Rangeland Restoration
img: assets/img/husky.jpg
importance: 1
category: Thesis Work
permalink: /projects/restorebot/
---

<div class="row">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="eager" path="assets/img/degradedRangeland.jpg" title="Our Field Site" class="img-fluid rounded z-depth-1" %}
    </div>
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="eager" path="assets/img/kristen_in_field_w_husky.jpg" title="example image" class="img-fluid rounded z-depth-1" %}
    </div>
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="eager" path="assets/img/conmod1.jpg" title="example image" class="img-fluid rounded z-depth-1" %}
    </div>
</div>

## Project Motivation

Dry-land ecosystems make up 40 percent of the global land surface and support nearly one-sixth of the world’s population. Restorebot aims to develop robotics systems capable of restoring degraded rangelands. We aim to have a robot independently identify sites of interest for restoration with shared autonomy capable of ingesting human knowledge to assist in site selection. In addition to learning more about restoration in these environments, we are also targeting seeding native grass species and placing Connectivity Modifiers or ConMods to help increase germination. Read more about ConMods [**here**](https://www.nps.gov/articles/cany-conmods.htm)

## Our Platform

<div style="text-align: center; margin: 2em 0;">
  <video controls style="width: 80%; max-width: 960px; border-radius: 12px;">
    <source src="restorebot_fixed.mp4" type="video/mp4">
    Your browser does not support the video tag.
  </video>
  <p style="max-width: 800px; margin: 0.5em auto; font-style: italic;">
    Thanks to my labmate Drew Beathard for sharing this hype video showing off Restorebot and in particular, his contributions to the sand-proof case and other hardware features.
  </p>
</div>

We utilize a Clearpath Husky A200 (Restorebot) with the following sensors; Ouster OS1-64 LiDAR (Light Detection and Ranging), a LORD Microstrain 3DM-GX5-15 VRU, a Trimble BX922 GPS with a Trimble AG25 antenna with 5cm RTK/RTX corrections, Intel RealSense D435 cameras, and Gig-E cameras. In May 2022 we only used one forward-facing D435 and one downward-facing Intel RealSense D435 camera. In November 2022 we modified the camera setup to include three outward-facing Gig-E cameras in combination with two downward-facing RealSense D435 cameras. All onboard processing is handled by a 32-core AMD Ryzen 3990x and a GTX 1650. The initial result of data collection and immediate post-processing yields cm-level accurate GPS localization, lidar-inertial localization results (as well as the point clouds and inertial measurements themselves), a 15cm-voxel Octomap of the plots, point clouds, and stereo pairs of images both of the robot’s immediate vicinity (roughly 225 degrees for the November 2022 data, and roughly 90 degrees for the May 2022 data) as well as the area in between the robot’s drive train.

We had two deployments using “RestoreCart”, the previous iteration of Restorebot. For these deployments, we only had a wheeled cart outfitted with a Nvidia Jetson Xavier, the Ouster OS1-64 LiDAR, PhidgetSpatial Precision 3/3/3 IMU, a Ublox NEO-M8P RTK GPS rover/base station pair, and one forward-facing and one downward-facing Intel RealSense D435 camera. We employed Trimble SPS855 GPS Base Station paired with a Trimble Zephyr Model 3 antenna to get a good estimate of the initial GPS coordinate, as well as the corners of each of the plots which were marked with rebar. In order to localize this data in the GPS frame, we mounted 32 cm diameter Radar Reflectors onto the rebar on the corners of each of the experimental plots. Obviously, the GPS estimates are not as precise as they are with our robot-mounted Trimble system, however it is still possible to localize the robot in the GPS coordinate frame with a larger margin of error.

## Experiments and Results

So far, Restorebot has been deployed three times (November 2021, May 2022, and November 2022) to the Restoration Field site at the Canyonlands Research Center, hosted by the Redd family’s Dugout Ranch. Our ongoing strategy has been to deploy once in late spring to observe grass seed germination and again in the fall to observe grass seed success and maturation. This gives us an idea as to what factors are not only relevant to plant germination, but also their maturation and ultimate survival.

See our plots [**here**](https://www.google.com/maps/d/u/0/edit?hl=en&mid=10_fBHdJzKWCaG4_qAbnWutCOpgOCnXjF&ll=38.10959366427508%2C-109.6018758118886&z=19)

Restorebot has evolved over the course of our deployments as we have experimented with sensor configurations and improved our platform. In November 2021, we have sparse RTK GPS data, IMU data, point clouds, forward-facing and downward facing images. For May and November 2022 we have extremely high fidelity GPS data, IMU data, point clouds, forward-facing and downward facing images. These data help us to localize the robot as well as objects in its environment in the GPS coordinate frame, in some cases down to the centimeter level, allowing us to better associate plant outcomes over multiple seasons. By localizing the robot, we can attribute features in the environment as well as individual plants to a very precise GPS location, allowing us to make conclusions about plant outcomes.

## Dataset 
As of May 2025, we've made our data availble to the public! Check out the data and some accompanying tools [**here**](https://arpg.github.io/canyonlands_dataset/)

Publication pending.