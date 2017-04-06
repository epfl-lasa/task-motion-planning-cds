## Task and Motion Planning with CDS Learned from Demonstrations
This repository includes the packages and instructions to run the LASA Motion planning architecture developed initially for a pizza dough rolling task within the Robohow project, but can be used for any task and any type of controller that outputs the desired command in task space (i.e. desired cartesian pose/ft/stiffness)

### Video of the architecture in action (click on image):
<div style="text-align:center">
![Task and Motion Planning using CDS Architecture used to Roll Pizza Dough](http://img.youtube.com/vi/br5PM9r91Fg/0.jpg)](http://www.youtube.com/watch?v=br5PM9r91Fg)

This research was conducted in the Learning Algorithms and Systems Laboratory (LASA) at the Swiss Federal Institute of Technology in Lausanne (EPFL) under the supervision of Prof. Aude Billard.  ---- http://lasa.epfl.ch/

It was funded by the EU Project ROBOHOW.COG. ----https://robohow.eu/

### Modular Architecture Description:
![alt tag](https://github.com/epfl-lasa/task-motion-planning-cds/blob/bare/img/lasa-architecture.png)

Following a brief run-through of the architecture:

  - **Action  planner:** The  action  planner  tells  theMotion  planner which  learned  action  to execute and the corresponding attractor obtained from the vision module (or fixed values insimulation).
  - **Vision Module (dough/attractor detectors):** This is the vision module, which detects the attractors for each corresponding action in the sequence or single action involved in the desired task.  (This module can be substituted by fixed values to facilitate simulation and fast integration of new components.)
  - **Motion planner:** Executes the requested action from the Action planner.This involves commanding the next desired state of the end-effector (pose, force/torque) using the currentstate of the end-effector and the parameters learned for the specifc action (i.e.  parameters of Coupled-Dynamical-System for pose control, parameters of probability distribution functionfor force/torque, stiffness profile) until the attractor/force is reached.
  - **EPFL  Task  Models:** These  are  text  files  that  contain  the  parameters  for  the  learnedaction/motion for each task.
  - **Cartesian to Joint State Transformer:** This module takes the desired end-effector com-mand (pose, force/torque) and converts it to joint velocities and stiffness. --- (Using IKSolver implemented in [robot-toolkit](https://github.com/epfl-lasa/robot-toolkit)) ---
  - **Joint to Cartesian State Estimator:** This module estimates the end-effector pose and force/torque from the joint angle/torques provided by the low-level controller. --- (Using FwdKinematics implemented in [robot-toolkit](https://github.com/epfl-lasa/robot-toolkit))) ---
  
### Usage in your own projects:
Using this **modular architecture**, one can easily simulate and test their own controllers and experiments. As the modules are not tied to each other, one can implement their own motion planner/policy controller in task space and execute it on a simulation or on a real robot with only using the corresponding topics, the state transformers/simulator or kuka_fri_bridge will take care of the rest. An example of these module being used to simulate and control actions can be found in this package:  [kuka_planning_interface ](https://github.com/nbfigueroa/kuka_planning_interface)

Also, if one chooses to use their own inverse kinematics/dynamics solvers on can send topics directly to the kuka_fri_bridge which works as a bridge to the KUKA control box. 

---
### Installation:

#### System Requirements:

OS: Ubuntu 14.04

ROS compatibility: Indigo

#### Instructions:

For **each package/repo** listed below, the user needs to do the following:

*Download:*
```
$ cd /catkin_ws/src
$ git clone <remote branch>
```
*Build:*
```
$ cd /catkin_ws/
$ catkin_make
```
#### Package list:
  1. [kuka-rviz-simulation](https://github.com/epfl-lasa/kuka-rviz-simulation):
  ```
  $ git clone https://github.com/epfl-lasa/kuka-rviz-simulation.git
  ```
  and don't forget to install all [dependencies](https://github.com/epfl-lasa/kuka-rviz-simulation) for this package.
  
  2. If not already installed, [kuka_interface_packages](https://github.com/nbfigueroa/kuka_interface_packages):
  ```
  $ git clone https://github.com/nbfigueroa/kuka_interface_packages.git
  ```
  and don't forget to install all [dependencies](https://github.com/nbfigueroa/kuka_interface_packages) for this package.

  3. Install [coupled-dynamical-systems](https://github.com/epfl-lasa/coupled-dynamical-systems) package:
  ```
  $ git clone https://github.com/epfl-lasa/coupled-dynamical-systems.git
  ```
  
  4. Install [state-transfomers](https://github.com/epfl-lasa/state-transformers) package:
  ```
  $ git clone https://github.com/epfl-lasa/state-transformers
  ```
  
---  

### Simulation of a Pouring task in Rviz:

##### Robot Simulator
```
$ roslaunch kuka_lwr_bringup lwr_simulation.launch
```


##### Control/Motion Planning
Cartesian-to-Joint/Joint-to-Cart Estimation
```
$ roslaunch state_transformers pouring_ctrls_sim.launch
```

Cartesian Trajectory Generator
  - For complete trajectory generation (open-loop):
  ```
  $ roslaunch motion_planner lasa_sim_fixed_pouring.launch
  ```
  
  Expected simulation: http://bit.ly/1HA0Fj4
  
  - For online trajectory generation (closed-loop with "simulated" robot controllers):
  ```
  $ roslaunch motion_planner lasa_fixed_pouring.launch
  ```
  
  Expected simulation: http://bit.ly/1CM6BTt
  
##### Action Planning  
```
$ rosrun lasa_action_planners pouring_demo_fixed_lasa.py
```
then follow the instructions on the terminal of this node.

---  

### Real-Time Control of a Pouring task on the KUKA LWR @ LASA:


##### Robot State Communication
Bringup ```kuka_fri_bridge``` (a custom KUKA control bridge using FRI library) check instructions to run [here](https://github.com/nbfigueroa/kuka_interface_packages.git).
```
$ rosrun kuka_fri_bridge run_lwr.sh
```
##### Real-time Robot Visualization
```
$ roslaunch kuka_lwr_bringup lwr_realtime_viz.launch
```

##### Control/Motion Planning
Cartesian-to-Joint/Joint-to-Cart Estimation
```
$ roslaunch state_transformers pouring_ctrls_real.launch
```

Cartesian Trajectory Generator
```
$ roslaunch motion_planner lasa_sim_fixed_pouring_tool.launch
```

##### Action Planning  
```
$ rosrun lasa_action_planners pouring_tool_demo_fixed_lasa.py
```
then follow the instructions on the terminal of this node.  

The robot will then follow the learned pouring trajectories: [Pouring Trajectories](https://www.dropbox.com/s/fgxrk9lj5avlw0j/pour_demo.mp4?dl=0)


--- 
### References:

[1] N. Figueroa and A. Billard, “Discovering hierarchical structure in heterogenous and sequential task demonstrations,” In preparation.

[2] A. L. Pais, K. Umezawa, Y. Nakamura, and A. Billard, “Task parametrization using continuous constraints extracted from human demonstrations,” Accepted, IEEE TRO, 2015.

[3] A. Shukla and A. Billard, “Coupled dynamical system based arm-hand grasping model for learning fast adaptation strategies,” Robotics and Autonomous Systems, vol. 60, no. 3, pp. 424 – 440, 2012.
