Navigation Overview

The navigation stack is a collection of ros packages and nodes used to collect and process inertial/visual data and turn that into useful information in terms of the robots location and direction relative to its surroundings. 

**These tutorials assume the reader is familiar with the ros stack: Most importantly, rosnodes, rostopics, rosservices, and the actionlib library.**

There are several main components to the stack:

1. seabee_imu_driver
2. seabee_3dnav
3. seabee_tf


1. seabee_imu_driver

    This is a package that has two main nodes - imu_node.py and seabee_imu_filters.py. All other modules in this package are currently helper modules to this one (i.e. integrators, etc). 

    The seabee_imu_filters reads data coming in from the imu on the /imu/data topic and filters the data to be more useful and less noisy. It publishes the filtered data on /nav_filtered_signals/filter_stack. See the doc on this node for more info.

    The imu_node reads data coming from seabee_imu_filters on /nav_filtered_signals/filter_stack and calculates the estimated velocity and position of the robot based on this data. This data is then published on the /odom topic. 

2. seabee_3dnav

    This node provides high level control for moving seabee. It provides two actions - SeabeeGoToLocation and SeabeeGoToOrientation, both of which are pretty self explanatory. It subscribes to /odom and makes calulations based on data published there, and publishes to /cmd_vel which is the interpreted by base_controller (which communicates with the motors directly). Stabilization is currently handled in base_controller.

3. seabee_tf

    **As of right now, this isn't really used, but will be important for future tasks.**

    This is a tranform tree (see ROS tf) representing the robot. Currently, there are 4 coordinate systems: "world", "base_link", "camera1_link", "camera2_link".

    The "world" frame represents the coordinate system in which we operate. Ideally, the origin of this frame would be in the corner of the pool, but more than likely the origin will be set where we start in the obstacle course.

    "base_link" represents the center of the robot. It reads data coming from the /odom topic (published to by seabee_imu_driver) and updates its relative position in comparison to "world". This is a dynamic relationship and is constantly changing. It also heavily depends on accurate data coming from the IMU. 

    The two camera frames represent the two cameras and their orientations, and are fixed distances from the "base_link",and as such never change. As base_link changes, however, their relationship to "world" changes accordingly.