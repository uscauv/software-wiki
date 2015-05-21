Implement navigation

Evaluate using ROS navigation stack to generate paths
since we are working in 3d and know the locations of every possible obstacle, the built-in ROS navigation might not be the best fit.
Also investigate building a plugin to work in the 3d space underwater if using ROS navigation planner stack

At the lowest level, you have to create a base controller (the platform which direclty talks to the robot) 
	http://wiki.ros.org/pr2_controllers/Tutorials/Using%20the%20robot%20base%20controllers%20to%20drive%20the%20robot

Eventually the base controller will be attached to the ROS Navigation Stack which gathers IMU data and directly translates it to the base controller
	http://wiki.ros.org/navigation/Tutorials/RobotSetup
	
It is also useful to make a launch file (http://wiki.ros.org/roslaunch/XML) so you do not have to start all the ROS nodes. You can also include new param nodes.

Here is how to run the program. First run roscore and then rosrun the specific nodes that are publishing/subscribing
