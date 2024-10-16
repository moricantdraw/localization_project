**Overview**  

 

This ROS package solves a robot’s localization problem by using a particle filter to estimate a robot’s pose within a known map based on odometry and laser scans.  

 

**Structure of the Particle Filter Solution ** 

 

Our solution particle filter solution estimates the robot’s location on the map using a set of weighted particles that each represent a hypothesis of the robot’s pose. The probability of the robot being located at each particle hypothesis is determined by comparing the particles relationship with the map, to the robot’s relationship with it’s true location, from a laser scan observation.  This probability is then used to weigh each particle, and the robot repeats this process while moving through the map and collecting observations about its location and updating particles weights. Over time the higher weighted particles should become centralized in the most probable location of the robot on the map.  For the sake of understanding our program this process can be broken up into 5 general steps.  

  

    **Initialize:  **

Our program starts by generating a set of random particles with equal weights to represent possible robot poses   

  **  Motion Update: **

The previously generated particles are updated as the robot moves such that they move in the same way and provide a consistent and logical guess for its location. The robot's movements can be generally tracked by its odometry readings, however the odometry cannot account for error like friction or poor wheel calibration so we add noise to simulate the uncertainty in the movement  

   ** Observation Update:  **
   
As the robot moves it makes observations using LIDAR data which is used to update the probability of each particle hypothesis. For each particle, the laser scan data is simulated at that particle’s pose by casting rays from the particle’s location and comparing them with the actual laser scan data. A higher similarity results in a higher weight being assigned to that particle which represents a “better” guess. Conversely lower similarity results in a lower weights being assigned to those particles which represents a “worse” guess. 

   ** Guess:  **

The robot then computes the weighted average of the 15 best particles. The result is the program’s best guess of where the robot is located. 

   ** Iterate: **

A new set of particles is then generated by resampling based on the current particles' weights. Particles with higher weights have a higher probability of being selected, which results in particles that are better representations of the robot's pose surviving, while less likely particles are culled. The particle distribution should then become centralized around the most probable areas. 

 

The code repeats the motion update, observation update, guess and iterate step to continue to centralize the particles as the robot moves around its environment and collects more observations.   

 

**Design Decisions  **

	 

While designing the particle filter, we deliberated over how to compute the robot’s estimated pose from the particle cloud. We thought of two possible approaches: computing the average position (x, y, θ) of all the particles, or selecting the particle with the highest weight as the estimated pose. The first method will have more resistance to outliers in the data set but is computationally costly. In the interest of leveraging this advantage without costing us as much computational energy we used a combination. We used a subset of the 15 highest weighted particles and then calculated the mean from this subset.  

 

**--TO DO -- **

Challenges  

Improvements  

  
