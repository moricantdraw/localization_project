# Robot Localization

## Overview  

 

This ROS package solves a robot’s localization problem by using a particle filter to estimate a robot’s pose within a known map based on odometry and laser scans.  

 

### Structure of the Particle Filter Solution  

 

Our solution particle filter solution estimates the robot’s location on the map using a set of weighted `particles` that each represent a hypothesis of the robot’s pose. The probability of the robot being located at each particle hypothesis is determined by comparing the `particle`'s relationship with the map, to the robot’s relationship with it’s true location, from a laser scan observation.  This probability is then used to weigh each `particle`, and the robot repeats this process while moving through the map and collecting observations about its location and updating the `particle`'s weights. Over time the higher weighted `particles` should become centralized in the most probable location of the robot on the map.  For the sake of understanding our program this process can be broken up into 5 general steps.  

#### *Initialize*

Our program starts by generating a set of random `particles` of normal distribution around our initial guess of the neato robot’s poses to represent the possible poses of the neato. 

#### *Motion Update*

The previously generated `particles` are updated as the robot moves such that they move in the same way and provide a consistent and logical guess for its location. The robot's movements can be generally tracked by its odometry readings; however, the odometry cannot account for error like friction or poor wheel calibration so we add noise to simulate the uncertainty in the movement  

#### *Observation Update*

After the neato moves to its new position, it makes observations using LIDAR data which is used to update the probability of each particle hypothesis. For each `particle`, the laser scan data is simulated at that `particle`'s pose by casting the laser scans onto the `particle`’s location and orientation and comparing them with the actual map data by using a helper function get_closest_obstacle which returns the distance of the closest obstacle from the theoretical obstacle we found by casting the laser scans. The distance is then converted into assigning the weights of the `particles` because we know that if the `particle` is exactly where the neato is, the distance between the laser scanned obstacles should be `0`. Thus the distance between the theoretical obstacle we obtained by the casting the laser scans and the real obstacles can be used to determine the weights of the `particle` in the `particle clouds`, as we know that they have an inverse relationship. This results in a higher weight being assigned to that `particle` which represents a “better” guess. Conversely lower similarity results in a lower weights being assigned to those `particles` which represents a “worse” guess. 

#### *Guess*

The robot then computes the weighted average of the 15 best `particles`. The result is the program’s best guess of where the robot is located. 

#### *Iterate*

A new set of `particles` is then generated by resampling based on the current `particles`' weights. `Particles` with higher weights have a higher probability of being selected, which results in `particles` that are better representations of the robot's pose surviving, while less likely `particles` are culled. The `particle` distribution should then become centralized around the most probable areas. 

 

The code repeats the motion update, observation update, guess and iterate step to continue to centralize the `particles` as the robot moves around its environment and collects more observations.   

 

### Design Decisions  

While designing the particle filter, we deliberated over how to compute the robot’s estimated pose from the `particle cloud`. We thought of two possible approaches: computing the average position of the `particles` or selecting the `particle` with the highest weight as the estimated pose. The first method will have more resistance to outliers in the data set but is computationally costly. In the interest of leveraging this advantage without costing us as much computational energy we used a combination. We used a subset of the 15 highest weighted `particles` and then calculated the mean from this subset.  

 

### Challenges  

During testing we had a fairly problematic issue with Gazebo. We were able to initialize our neato in Gazebo and our particle cloud in RVIZ2 but could not move our neato in gazebo. Without any movements we couldn’t update our observation and therefore couldn’t test the rest of our particle filter.  

 

### Improvements  

Given more time we would have loved to test our code on a physical neato using one of the maps provided such as the MAC hallway. We understand that the physical world has a lot of variables we can’t account for in simulation, such as motor errors, wheel friction, and bad scans. We tried to write our code such that it would provide a margin of error for these variables (by adding noise to our `particle`'s movements) but we would have wanted to have the opportunity to test how effective those countermeasures were.  

  
