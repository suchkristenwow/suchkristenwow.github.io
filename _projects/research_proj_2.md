---
layout: page
title: Household Fire Detecting, Tracking, and Extinguishing Robot
description:
img: assets/projects//household_fire_robot/robot.gif
importance: 2
category: research projects
---

Anyone who has an undergraduate background in how engineering works in academia is familiar with an engineering capstone project. These are the projects that eventually are displayed at each university's Engineer Days (E-Days) celebration. At CSU, a new program was established that focused on preparing undergraduate students to apply for and pursue a PhD. This program had a similar senior design captone project, named the engineering research capstone project. 

Following the work I did during my NSF REU under the advice of Steven Simske, I decided to extend the work into a more realistic system: A household fire detection, tracking, and elimination robot. This was due to the fact that a wall-mounted system would not only be more practical than a UGV for indoor settings, but it would scale better, and most importantly, people already have smart fire detectors, so why not the ability to eliminate a detected fire?

This was definitely a project that my advisor, my professor, and I were all intimitated by. It was a large project to complete in the course of a year, but it seemed too good to just let go of. 

During the development of this system, I learned a lot. For one, I learned about proposal writing and gained a lot of experience in presenting research. From a robotics/mechatronics/systems perspective, there was a lot of lessons in the project management and design of the robot as whole - I needed it to be cost-effective while also requiring that it be able to do the desired task of accurately detecting a fire before eliminating it. 

On the techincal side, I gained a lot more experience in mechanical design, additive manufacturing (3D printing), electrical design and integration (for controls cabinet). Up until this point I only used Arduinos to control the robots I made. Using C as a first language allowed me to become comfortable with variable types and statically-typed programming, but high-level tasks like machine learning were out of scope. Arduino is an amazing microcontroller board that taught me a lot about circuits, GPIO, signals, and communication protocols, but in order to complete this project I needed to work with something that had more available compute. I decided to use a RaspberryPi and Python. Using Python as the primary language allowed me to gain experience in using SMTP protocol, image conpression, machine learning, Tensorflow/TensorflowLite, implementing PID cntrol, and how to design a behavior tree.

There was continuous integration testing that I needed to do during the development of the system. Each time there were major mechanical, electrical, or software changes, I made sure to test the components of the system to ensure functionality was not altered. The effort to do this was worth it. I had some hard lessons during previous project development, where I had to hunt down a root failure without knowing which change could have led to it. 

<br>


## Final Product

<div style="display: flex; justify-content: center;">
<iframe width="315" height="560" src="https://www.youtube.com/embed/2oVTqzw8dh8" title="FirePutOut" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" allowfullscreen></iframe>
</div>

<br>


## Controller

The robot functions autonomously using a [behavior tree](https://en.wikipedia.org/wiki/Behavior_tree_(artificial_intelligence,_robotics_and_control)). A behavior tree was used to approach the robot's autonomy due to it's relativly simple and organized design. The tasks were as follows:

1) Pan left to right and tilt down at each pass until hitting a threshold. After hitting threshold, pan left to right and tilt up at each pass until hitting a maximum tilt threshold. Repeat.

2) If a infrared mass is observed during survellence, use PID control to center Flir camera onto it. The mass will be bounded by rectangular blob provided by OpenMV.

3) Send warning email to homeowner using SMTP. This email contains image of observed infrared mass.

4) Use pretrained network to make final decision if mass is or is not a fire. If the inference predicts that infrared mass is in fact a fire, adjust nozzle and fire jet of water.

5) Jet nozzle will be adjusted using an estimated trajectory. This estimation is calculated keeping in mind that the spray nozzle is vertically offset from the Flir imager. Details can be found below.

6) Water is then actuated from a pump and tube system into the nozzle head. The water is jet out for 5 seconds.

7) If IR mass is still active, infer if it is likely still an active fire. If a fire, spray for 5 seconds. Repeat until inference determines IR mass is non-active fire.

8) If the mass grows by a threshold or increses in IR over time, spray again. If IR mass shrinks or decreases in IR over time, continue observing without spraying. Observe IR mass until it reaches ambient heat. 

9) Email homeowner with image of where IR mass was that detected fire has been eliminated by system. 

10) Return to pan-tilt mode.

<br>


## Pan-tilt Mechanism Design

<video width="800" height="300" autoplay loop>
    <source src="/assets/projects/household_fire_robot/Assem1.mp4" type="video/mp4">
</video>

<video width="800" height="300" autoplay loop>
    <source src="/assets/projects/household_fire_robot/System_Assembly.mp4" type="video/mp4">
</video>

Below is the final assembly of the mechanism (STL). 

<div style="display: flex; justify-content: center;">
    {% include stlviewer.html src="household_fire_robot_assem.stl" width=300 height=300 extrastyle="" %}
</div>