---
layout: page
title: Asynchronous Semantic SLAM
description: 
img: assets/projects/async_sem_slam/hunter.png
importance: 2
category: research projects
---

My most recent research was funded by a grant from Lockheed Martin. The goal was to build off a former student's work of enabling robots to collaborate during navigation missions in communication-constrained, GPS-denied indoor environments. While there have been many works posed at improving the task of multi-robot communication in some capacity, this student's work had an interesting approach to reduce the dimentionality of mapped data: 

1) While mapping indoors, transform each 3D pointcloud into 2D laserscan, if not already in 2D.
   
2) Classify major known areas (cooridoors) from 2D laserscan into semantic categories using machine learning.

3) As the 2D laserscan-based map is being built, replace sections of classified points with their corresponding shape that encompasses them. 

Hopefully this is not a gross oversimplification of the work's pipeline, but it is definitely the easiest for people to read. Three steps is simple, right? 

Anyways, the end result of this approach allowed robots to communicate their individual maps using much more memory-efficient shape primitives that what point clouds can offer. This enabled robots to share indoor maps they made not only faster, but also in bandwidth-constrained environments (think subterranean). Further, the maps generally reduced to a shape resembling the laserscan-based map, but with even more uniqueness, since the sequence of shapes provided a key. The general geometric outline of these efficient indoor map representations, along with the uniqueness of the semantic shape sequence that it's shape comprised of, meant that map-merging was not only achievable, but also very fast.

### My task was to take the work into outdoor settings.

One of the major challenges here was that the original work used a support vector machine (SVM) to classify indoor areas (cooridoors). This worked well for the indoor scenes, which were mostly static and fully-observed. However, due to the dynamic and unstructured nature of outdoor scenes, it was decided that this approach would be best benefitted by utilizing deep-learning methods to semantically understand the environment. 

<div class="col-sm mt-3 mt-md-0">
    {% include figure.liquid path="assets/projects/async_sem_slam/map_fast.gif" title="SLAM demonstration on CU campus" class="img-fluid rounded z-depth-1" %}
</div>

<video width="800" height="300" autoplay loop>
    <source src="/assets/projects/async_sem_slam/sem_map_seg.mp4" type="video/mp4">
</video>