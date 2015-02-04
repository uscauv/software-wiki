Navigation Overview

The navigation stack is a collection of ros packages and nodes used to collect and process inertial/visual data and turn that into useful information in terms of the robots location and direction relative to its surroundings. Currently, the navigation stack is heavily based on the ros navigation stack.

There are several main components to the stack:

1. seabee_imu_driver
2. seabee_tf
3. seabee_2dnav


3. seabee_2dnav

    This is a package provided completely by ROS navigation (move_base). It relies on several assumptions:
        a. There is a transform tree (tf) operating (seabee_tf)
        b. There is odometry data being published on 'odom' in the nav_msgs/Odometry format

    In the ros docs, it says that a laser or sonar mount is required. However, this is only for obstacle/object detection,
    and not including these will still allow seabee_2dnav to operate. The only difference is that the obstacle map that 2dnav uses to create paths will simply be empty (i.e. every path between two points will probably be straight, because it doesn't have to go around anything).

1. seabee_imu_driver

    This is a package that has one main node - imu_node.py. All other modules in this package are currently helper modules to this one (i.e. integrators). 

    The imu_node reads data coming from the IMU and publishes it in a form that the seabee_2dnav stack can understand on the rostopic "odom" in the message format nav_msgs/Odometry. 

2. seabee_tf

    This is a tranform tree (see ROS tf) representing the robot. Currently, there are 4 coordinate systems: "world", "base_link", "camera1_link", "camera2_link".

    The "world" frame represents the coordinate system in which we operate. Ideally, the origin of this frame would be in the corner of the pool, but more than likely the origin will be set where we start in the obstacle course.

    "base_link" represents the center of the robot. It reads data coming from the /odom topic (published to by seabee_imu_driver) and updates its relative position in comparison to "world". This is a dynamic relationship and is constantly changing. It also heavily depends on accurate data coming from the IMU. 

    The two camera frames represent the two cameras and their orientations, and are fixed distances from the "base_link",and as such never change. As base_link changes, however, their relationship to "world" changes accordingly.