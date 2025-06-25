---
layout: page
title: BetterFaster SLAM
description: Tracking Semi-Static Landmarks with Joint Persistence Filtering
img: assets/img/betterFaster_thumbnail.jpg
importance: 2
category: Thesis Work
permalink: /projects/betterfaster/
---

## Project Motivation

Robots tasked with long-term monitoring in dynamic environments—such as agricultural fields or construction sites—face a critical challenge: how to maintain consistent localization and mapping when key features in the environment change or disappear over time. These features, which we refer to as *semi-static landmarks*, defy the conventional “static map” assumption in SLAM.

BetterFaster SLAM is a system developed to address this challenge by building on previous work, Better Together, and extending it into a full SLAM framework using FastSLAM 2.0. Our key innovation is tracking both the *location* and *state* (e.g., appearance or growth stage) of individual landmarks over time, enabling more robust data association across seasonal or contextual changes.

## How It Works

BetterFaster employs a particle-filter-based SLAM architecture augmented by joint persistence filtering. Key components include:

- **Clique Management**: A *clique* is a group of features associated to the same landmark. We jointly estimate their persistence using a Bayesian survival model. Clique assignment is an approach to _anchoring_ in the computer vision world.

{% include figure.liquid loading="eager" path="assets/img/clique_extraction.png" title="Clique detection in CARLA" caption="Example of clique detection in CARLA (an Unreal Engine based self-driving car simulator)"
class="img-fluid rounded z-depth-1 mb-4" class="img-fluid rounded z-depth-1 mb-4" %}

- **Clique State Tracking**: Each landmark has a <em>clique state</em>, representing its latent growth or appearance condition (e.g., seedling, mature, dead). Here is an example: we know a dead plant will not suddenly be replaced by a mature plant, and similarly, we know that a seedling cannot become mature without passing through some intermediate phase. Here, the clique state is the maturity of the plant which probabilistically follows a cycle of growth, stagnation or death. We can leverage our knowledge of these phases and their timing with the seasons to initialize the transition function.

- **Transition Function**: 
A probabilistic model estimates how clique states evolve over time (e.g., a dead plant does not spontaneously become mature again).  

<div style="max-width: 50%; margin: 0 auto;">
  {% include figure.liquid 
     loading="eager" 
     path="assets/img/transition_function_ex.png" 
     title="Clique Growth States" 
     caption="The transition function captures how likely a landmark is to exist in a given state at a given time. In unstructured environments, this is necessarily modeled as a probability distribution." 
     class="img-fluid rounded z-depth-1" 
  %}
</div>

For example, suppose we observe these plants in Spring. 

{% include figure.liquid 
    loading="eager" 
    path="assets/img/gstate_estimation1.png" 
    title="Clique Growth States" 
    caption="We take this collection of features and using those descriptiors, in addition to the time, we can create a hypothesis of what growth state these individuals belong to. We initialize the posteriors to 1, and use the projected feature locations to come up with a location estimate with a covariance." 
    class="img-fluid rounded z-depth-1" 
%}

Then suppose we return in the Summer. 

{% include figure.liquid 
    loading="eager" 
    path="assets/img/gstate_estimation2.png" 
    title="Clique Growth States" 
    caption="We observe some features consistent with the 2nd growth state for plant 0, but we don't observe any features within the measured covariance for plant 1. So we can see the posterior estimate, our confidence that plant 1 continues to persist, drops for plant 1. Since we observe features consistent with the estimated growth state within the measured covariance for both plant 0 and plant 2, we can associate those features to our localization estimate." 
    class="img-fluid rounded z-depth-1" 
%}

Finally, suppose we return to the same plot again the Fall.

{% include figure.liquid 
    loading="eager" 
    path="assets/img/gstate_estimation3.png" 
    title="Clique Growth States" 
    caption="We continue to not observe any features for plant 1, so we remove plant 1 from our map, because its posterior has fallen below the confidence threshold (red dotted line). We can associate these features to a given timestamp, refining our understanding of what different growth states look like over time." 
    class="img-fluid rounded z-depth-1" 
%}

- **Observation Filtering**: BetterFaster avoids false negatives by incorporating knowledge of expected changes in appearance and state.

Unlike conventional SLAM approaches that may discard dynamic features as outliers, BetterFaster maintains and updates landmark estimates with resilience to seasonal or contextual changes.

## Simulated and Physical Experiments

To evaluate BetterFaster, we ran experiments in a 2D simulation with a noisy sensor measurements/localization.  

{% include figure.liquid 
   loading="eager" 
   path="assets/img/gif1.gif" 
   title="Robot driving in circles" 
   caption="Here the robot (red ellipse with red pointer indicating heading) is driving in circles. The best estimated trajectory is the black dotted line, and the ground-truth is in cyan." 
   class="img-fluid rounded z-depth-1 mb-4" 
%} 

{% include figure.liquid 
   loading="eager" 
   path="assets/img/gif2.gif" 
   title="Robot driving in circles making observations." 
   caption="Next, the robot makes observations (red dotted line) of landmarks in its environment." 
   class="img-fluid rounded z-depth-1 mb-4" 
%} 

{% include figure.liquid 
   loading="eager" 
   path="assets/img/gif3.gif" 
   title="Robot driving in circles making observations." 
   caption="We display the covariance and the best estimate of each landmark as it's observed and localized in blue. Our best estimate of the landmark location is the particle with the highest weight." 
   class="img-fluid rounded z-depth-1 mb-4" 
%} 

{% include figure.liquid 
   loading="eager" 
   path="assets/img/gif4.gif" 
   title="Complete SLAM animation" 
   caption="Complete 2D Simulation for testing BetterFaster algorithm." 
   class="img-fluid rounded z-depth-1 mb-4" 
%} 

Now we incorporate semi-static landmarks. The landmarks change their state over time. As they are removed from the map, we want to see if our map stays up to date. If the landmark no longer persists AND is removed from the map (a true negative), it's filled with black. If the landmark no longer persists but it's still in the map (a false positive), it's displayed in red. If the landmark persists but it has been removed from the map (a false negative), it's displayed in blue. Otherwise, if the landmark persists and still exists in the map, it's shown in white with black outline.

Finally, we can compare BetterFaster with the multiple-map hypothesis method.

<div class="row">
  <div class="col-md-6">
    {% include figure.liquid 
       loading="eager" 
       path="assets/img/gif4.gif" 
       title="Complete SLAM animation" 
       class="img-fluid rounded z-depth-1 mb-4" 
    %}
  </div>
  <div class="col-md-6">
    {% include figure.liquid 
       loading="eager" 
       path="assets/img/multiMap.gif" 
       title="MultiMap Animation" 
    %}
  </div>
</div>

{% include figure.liquid 
   loading="eager" 
   path="assets/img/betterFaster-comparison_fig.png" 
   title="Robot driving in circles" 
   caption="In this given sim, after 500 timesteps, we see that BetterFaster outperforms the multi-map hypothesis method, particularly in false negative rejection." 
   class="img-fluid rounded z-depth-1 mb-4" 
%} 

But our goal here was not merely localizing the landmarks, we are also interested in tracking their state. 

{% include figure.liquid 
   loading="eager" 
   path="assets/img/cliqueState_tracking.png" 
   title="Robot driving in circles" 
   caption="Correctly labelled growth states over time across different rates of correct data association." 
   class="img-fluid rounded z-depth-1 mb-4" 
%} 

Here the x axis is time, and the y axis is the ratio of correctly labelled growth states meaning that we correctly have estimated the growth state of the observed clique at that timestep. 

So if one of these two methods correctly estimated all of the landmarks at a given timestep, the value of that curve at that time would be 1. Hopefully, it makes sense then, that what you want to see is the maximum area over the curve. 

Multimap doesn’t have a way to track clique state, so in order to make a comparison, we made it such that if multimap correctly estimated persistence at a given timestep, we would suppose that it correctly estimated the growth state at that timestep as well with some given probability.

This is how we replicate the loss of data association caused by seasonal differences, we tested rates of correct data association; 0.3, 0.6, and 0.8. 

That is, we assume that using semantic labelling or some other technique which could associate the same landmark with different appearances to the same landmark in the map with a given rate of data association despite changes in the landmark’s appearance.  

For BetterFaster, we maintain good data association for all landmarks with the clique posterior above our acceptance threshold and the correct growth state estimate. That is, if BetterFaster estimates that the given landmark would be in a given growth state at a given instance, and it is correct in its assumption of persistence, the data association is correct. 

Ultimately, we see that BetterFaster is more robust to false data associations.

Taking these results together you can conclude that tracking clique state is correlated with improved persistence estimation. Intuitively, this makes sense as we are infusing knowledge of how landmarks should be updated in the map - intuitivly, if we are endowing it with more information, we should see an improvement in performance. 

## Comparisons and Results

We benchmarked BetterFaster against:
- **FastSLAM 2.0 with variable data association rates**
- **MultiMap**, a leading alternative that stores multiple map hypotheses
- **BetterTogether without state tracking**

BetterFaster achieved the highest accuracy in estimating both landmark persistence and growth state. It does not require an initialization phase, is resistant to missed detections, and does not propagate errors across unrelated map regions. Moreover, the modular design allows integration with object detection models for real-world deployment.

## Applications and Future Work

BetterFaster is especially suited for ecological monitoring, agricultural robotics, and any scenario where long-term tracking of objects is required. For example, in rangeland restoration efforts, it can help track plant growth and survival over seasons, even when visual appearances change dramatically.

Future directions include:
- Deployment in AV environments using CARLA (self-driving car simulator in Unreal Engine)
- Integrating vision transformers for better clique state transition modeling
- Explore implications using RestoreBot data, available now: [here](https://arpg.github.io/canyonlands_dataset/)