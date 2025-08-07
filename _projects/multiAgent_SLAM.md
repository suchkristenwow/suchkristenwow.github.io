---
layout: page
title: Heterogeneous SLAM
description: Multi-Robot Mapping and Tracking Using Joint Persistence Across Robot Types
img: assets/img/heteroSLAM_thumbnail.png
importance: 3
category: Thesis Work
permalink: /projects/heterogeneous-slam/
---

## Project Motivation

In multi-robot systems, collaborating agents are often heterogeneous—differing in sensors, mobility, or onboard computation. However, standard SLAM frameworks typically assume homogeneous agents and fixed environments. This project explores how multiple different robots can jointly perform SLAM while also tracking dynamic and semi-static features in a shared map.

We introduce a framework that fuses detections and beliefs across robots with different sensing modalities and priors, enabling robust performance in dynamic environments and over extended periods.

## Key Concepts

- **Heterogeneous Sensor Fusion**: Each robot contributes observations from its own sensor suite, such as stereo cameras, lidar, or GPS. Our method fuses these disparate sources into a unified, probabilistic map.

- **Joint Persistence Filtering**: The system models how features appear, change, or disappear over time. It performs joint filtering over multiple detections to improve confidence and handle occlusions.

- **Belief Propagation Across Agents**: By maintaining local beliefs and sharing relevant cliques between agents, the system avoids duplication and improves landmark persistence estimation without requiring identical observations.

- **Dynamic Feature Management**: Unlike traditional SLAM, which discards dynamic elements as outliers, our system identifies and models dynamics explicitly—including time-varying features such as vehicles.

## Experiments and Evaluation

We validated our method first using simulations in Gazebo, including a mixed-reality forest scenario with mobile ground robots and aerial agents. Key results include:

- Successful long-term data association across agents with different fields of view and motion constraints.
- Improved robustness in semi-structured environments with occlusion and clutter.
- Demonstrated resilience to data loss and poor overlap between robot trajectories.

My contribution was implementing the experiments using 2 of our Husky platforms - recycled from the DARPA SUBT challenge we configured one to use LIOSAM and another one to use Kimera (stereo-inertial). We then had the robots "track" a target - a plate with a bunch of reflectors attached. In practice, we republished the MoCap measurements of the target with some noise, but this could also be done using April Tags, or some other method of uniquely identifiying and localizing a landmark.

## Applications

- **Search and Rescue**: Ground and aerial robots can jointly track missing persons or hazards in changing environments.
- **Precision Agriculture**: Different robots monitor crop growth and health, even as seasons change or field conditions vary. It's natural to imagine a heterogenous team consisting of a aerial robot for high-level monitoring paired with ground agents who perform interventions, for example.
- **Autonomous Construction**: Mixed fleets of inspection and manipulation robots maintain consistent environmental models over time.

## Publication

**Joint Persistence Filtering for Heterogeneous Multi-Robot SLAM in Semi-Static Environments**  
Ofer Dagan, Tycho L. Cinquini, Luke Morrissey, Kristen Such, Nisar R. Ahmed, Christoffer Heckman
_ICRA 2023 workshop on "Distributed Graph Algorithms for Robotics"_  
[Read on arXiv](https://arxiv.org/abs/2306.04570)
