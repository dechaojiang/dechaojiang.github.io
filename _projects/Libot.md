---
layout: page
title: Libot
description: Autonomous Mobile Manipulation Robot for Libraries
img: assets/img/Libot/Libot_outline_demo.jpg
importance: 1
category: Robotics
related_publications: false
tabs: trues
images:
  slider: true
---

I would like to express my gratitude to Professor Qingsong Xu for his invaluable guidance. Special thanks to my partner, [Mr. Zhang Tong](https://www.linkedin.com/in/tong-zhang-399891227/), for his dedication and countless hours spent discussing and debugging code with me in the FST AI room. 

Libot is a mobile robot designed to automate tasks in libraries.

Visit my gitrepo for codewise ideas: [24EME_FYP](https://github.com/dechaojiang/24EME_FYP)

<!-- put a demo video here for demostrating the key features-->

## Key Features:

<div class="row mt-3">
    <div class="col-sm mt-3 mt-md-0">
        {% include video.liquid path="assets/video/Libot480.mp4" class="img-fluid rounded z-depth-1" controls=true autoplay=true %}
    </div>
</div>
<div class="caption">
    Libot in Gazebo simulation
</div>

- **Book Detection System:** Utilizes the YOLO v8 deep learning model to recognize book spine labels from RGB images, facilitating efficient book cataloging.
- **Deep-learning-based Object grasping:** Integrated with the [MoveIt](https://moveit.ros.org/) platform through move group API to optimize book handling through precise pick-and-place operations. Deep-learning algorithm [GPD](https://github.com/atenpas/gpd?tab=readme-ov-file) by [Andreas](https://www.khoury.northeastern.edu/home/atp/) is used for grasp generation.
- **SLAM Mapping and Navigation:** Enhanced with Simultaneous Localization and Mapping (SLAM) to adapt to dynamic library layouts, improving navigation and operational efficiency. Mapping through hectoring mapping by [TeamHector](https://www.teamhector.de/) at TU Darmstadt.

***
## Hardware
Libot's hardware consists of three main modules: vision sensor, manipulator, and navigator. These modules are connected to a central control computer.

<div class="row justify-content-sm-center">
    <div class="col-sm-7 mt-3 mt-md-0">
        {% include figure.liquid path="assets/img/Libot/Libot_hw_diag.png" title="example image" class="img-fluid rounded z-depth-1" %}
    </div>
    <div class="col-sm-4 mt-3 mt-md-0">
        {% include figure.liquid path="assets/img/Libot/Libot_outline_demo.jpg" title="example image" class="img-fluid rounded z-depth-1" %}
    </div>
</div>
<div class="caption">
    You can also have artistically styled 2/3 + 1/3 images, like these.
</div>


- Navigator Module: Utilizes the **MiR250** mobile robot, providing mobility and a customized book sink. The book sink serves as both a storage unit for books and a mounting platform for the manipulator.
- Manipulator Module: Features a **UR5** robotic arm mounted on the book sink. Attached to the UR5 are:
  - **Robotiq 2F-140** Gripper: Enables Libot to grasp and manipulate objects.
  - **Intel RealSense D435i** Depth Camera: Part of the vision sensor module, mounted on the UR5’s third wrist through a dedicated holder, providing real-time depth perception.
  

***
## Control Methodology & Simulation Environment

<div class="row">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="eager" path="assets/img/Libot/fsm.png" title="example image" class="img-fluid rounded z-depth-1" %}
    </div>
</div>
<div class="caption">
    Libot's working logic
</div>

Above shows the conceptual program design of Libot. There are four states

{% tabs fsm %}

{% tab fsm IDLE %}
- `IDLE` means Libot not being used or active. No task is execute during Libot’s IDLE state
{% endtab %}

{% tab fsm Book_Indication %}
- Detection of book placed in booksink can trigger the transition from `IDLE` state to `Book_Indication` state. This transition will output tag information recognized by our vision model 3.16 of the newly placed book and stored in a shared memory. Ultimately, the tag info will be compared with a library database then return the location information of the book, `Indication task` completes by this point.
{% endtab %}

{% tab fsm P2P_Navigation %}
- This state execute the task of the navigation to a given point. It has three entries corresponding to three other states. From `Book_Indication` to this state requires the condition of the booksink is full, the first location point which obtained from indication task store in a queue (shared memory) will be assign to a variable name `target_point` which is the point will be given to navigation task. Direct transition from `IDLE` to this state is possible, this design is to make Libot work at a certain frequency. For example, the `timeout` value can set to be 4 hours, with this entry to navigation, Libot will be in return book operation every 4 hours even the booksink is not full. Onceit enters `P2P_Navigation` from `IDLE`, a re-entry will be trigger with the condition of the booksink is not full, it will assign `target_point` in the way mentioned earlier.
{% endtab %}

{% tab fsm Pick_Place %}
- After the complement of the navigation task, Libot will be in this state to perform `Pick and Place task`, upon the completion of this task, it will returns to `P2P_Navigation` state. Please scroll down to see detailed description of Pick and Place task.
{% endtab %}

{% endtabs %}

Once the Libot return all the books after the transition between `P2P_Navigation` and `Pick_Place` state, the emptiness of booksink will trigger the re-entry of assigning the `target_point` to HOME position. Libot will move to it’s HOME position first and turn to `IDLE` again.

***

### Collaborating Gazebo and ROS control
Libot was developed and tested in the simulation platform [Gazebo](https://gazebosim.org/home). Physical parameters like mass, inertia, were defined and fine-tuned through our Universal Robotic Description Format (URDF) files for Libot, and pass on to Gazebo to mimic the real-world situation. Gazebo can read and write the `hardware_interface::RobotHWSim` provided by `ros_control` package which enables the reflection of control by ROS in Gazebo simulation. 

<div class="row">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="eager" path="assets/img/Libot/Data_flow_of_ros_control_Gazebo_reality.png" title="example image" class="img-fluid rounded z-depth-1" %}
    </div>
</div>
<div class="caption">
    Dataflow of ros_control, Gazebo and reality in Libot test
</div>
***

### Mapping & Point2Point Navigation
MiR is a commercial product with its own mapping and navigation technology. However, Libot uses the open-source system ROS and its packages for navigation. The key component is the move_base node from the `ros_navigation` package, enhanced by `hector mapping` for SLAM. This approach utilizes a LiDAR and depth cameras on Libot’s MiR base.

<div class="row">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="eager" path="assets/img/Libot/nav_overview.png" title="example image" class="img-fluid rounded z-depth-1" %}
    </div>
</div>
<div class="caption">
    ROS structure of Libot's navigation
</div>


{% tabs nav %}

{% tab nav input %}

`Sensory input:`
- Odometry Source: Provides data about Libot’s movement over time, such as distance and speed, based on wheel encoders or other motion sensors.
  
- Sensor Sources: Inputs from the robot’s perception hardware, like LIDAR or depth cameras, providing `sensor_msgs/LaserScan` and `sensor_msgs/PointCloud` data.


{% endtab %}

{% tab nav TF %}
`Sensor Transforms`
- The data from various sensors are transformed using `tf/tfMessage`, which maintains the relationship between coordinate frames. This step aligns all sensory data to a common reference frame, helping Libot understand its environment relative to its position and orientation.
{% endtab %}

{% tab nav costmap %}
`Costmap generation`
- Both the `global_costmap` and `local_costmap` are updated with the transformed sensor data. The `global_costmap` reflects the overall environment based on a pre-existing map, while the `local_costmap` is dynamic, reflecting immediate obstacles and changes around Libot.
{% endtab %}

{% tab nav planner %}
`Global planner`
- Using the `global_costmap`, the global_planner computes an initial path to the destination. This path is laid out as a series of waypoints and passed down as `nav_msgs/Path` to the `local_planner`.

`Local planner`
- The `local_planner` refines the path provided by the `global_planner`, considering real-time data from the `local_costmap`. It generates a short-term path for Libot to follow safely, adjusting for unexpected obstacles and ensuring the robot’s movements are kinematically feasible.
{% endtab %}

{% tab nav Execution %}
`Execution of movement`
- Movement commands, encapsulated in `cmd_vel` messages of type `geometry_msgs/Twist`, are sent from the `local_planner` to the MiR controller. This component controls Libot’s actuators, translating the velocity commands into physical motion.
{% endtab %}

{% tab nav Mapping %}
`Hector Mapping`
- In parallel, the `hector_mapping` node performs SLAM to update the map of the environment and locate Libot within it. This information updates the `global_costmap` and aids in continuous navigation.
{% endtab %}

{% tab nav Recovery %}
`Recovery behaviour`
- If Libot encounters a problem, like an impassable obstacle or a localization error, the `recovery_behaviors` are triggered. These behaviors aim to resolve the issue by re-planning a path or clearing the costmaps to restart the mapping process.
{% endtab %}

{% tab nav HI %}
`Human interaction`
- Throughout this process, a human operator can intervene by providing new commands or interrupting the current operation.
{% endtab %}

{% endtabs %}
***

### Computer Vision Book Detection

<swiper-container keyboard="true" navigation="true" pagination="true" pagination-clickable="true" pagination-dynamic-bullets="true" rewind="true">
  <swiper-slide>{% include figure.liquid loading="eager" path="assets/img/Libot/yolo_chart.png" class="img-fluid rounded z-depth-1" %}</swiper-slide>
  <swiper-slide>{% include figure.liquid loading="eager" path="assets/img/Libot/yolo_table" class="img-fluid rounded z-depth-1" %}</swiper-slide>
</swiper-container>

Dectection results trained by `YOLOv8l` model are illustrated in Table 4.2. The model showcases its capability to effectively detect objects with various degrees of overlapping bounding boxes, as evidenced by an overall class mAP50 of 92.3% and a noteworthy mAP50-95 of 82.8% among all string classes.

Many Thanks to my partner **Zhang Tong** who resolve all ML dependencies and trained the model.


***
### Grasp Pose Detection (GPD)

<div class="row">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="eager" path="assets/img/Libot/gpd.png" title="example image" class="img-fluid rounded z-depth-1" %}
    </div>
</div>
<div class="caption">
    Task diagram of GPD pick&place
</div>

Within sequence showing above, there are three core subprocesses outlined:

{% tabs gpd %}

{% tab gpd GPD %}
- `GPD Algorithm:` This module processes the `PointCloud2` data, received as a ROS topic, to compute potential grasps. These grasps are identified and then published as a `clustered_grasps` topic, which aggregates the grasping points identified by the GPD algorithm.
{% endtab %}

{% tab gpd TF %}
- `TF Pose Transform:` Subsequent to grasp identification, the `clustered_grasps` must undergo a transformation to align with the arm of Libot’s operational frame of reference. This step involves converting the grasps from the `camera_depth_optical_frame` to the `booksink_link`, which serves as the reference frame for the MoveIt motion planning framework. This transformation is pivotal as it ensures the grasps are contextualized within the robot’s spatial understanding and MoveIt’s planning pipeline.
{% endtab %}

{% tab gpd P&P %}
- `Simple Pick Place Task:` The final module, depicted as a rounded rectangle, utilizes the transformed grasp data to inform the motion planning for the UR5 robotic arm. This `simple_pick_place.py` script orchestrates the physical actions required for the robotic arm to move to the desired positions and execute the pick and place task, finally returns a new state to `Libot State` in a rectangle which represents the shared memory that can be pass to and edit in other state.
{% endtab %}

{% endtabs %}

***
### Manipulation planning with MoveIt!
Learn more about MoveIt: [MoveIt motion planning network](https://moveit.ros.org/)

**Work done by this project through MoveIt:**
- Two move groups set up using moveit setup assistant, generated a ROS package powering Libot's manipulation task MoveIt planning pipeline.
- Integration with Deep Learning based Grasp Pose Detection

**Libot’s control through MoveIt can be done in several ways:**
- MoveIt Commander: This includes the MoveIt Python API and the MoveIt Command Line Tool for programmatic control of Libot’s movements.
- ROS Visualizer (RViz): An interactive tool that lets users visually plan and execute trajectories within a simulated environment.





