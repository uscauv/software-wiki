
Navigation Wiki: seabee_tf

This package directly inherits from the tf package provided by ROS, the documentation for that can be found here http://wiki.ros.org/tf

This package keeps a "transform tree" of the robot and its environment. In other words, it keeps track of the relative positions and orientations of a variety of different parts of our robot and its course, so that we can know where we are in relation to everything else. Most obviously, there is a transform between the "world" frame, which represents the entire pool (ideally the corner of the pool [like the origin of a coordinate system], but more realistically, it will probably be where we drop the robot in the water), and the center of our robot (called "base_link"). It takes in information from the IMU driver (see seabee_imu_driver) and updates the position of "base_link" with respect to "world" as the robot moves around. 

In addition to "base_link" and "world", there are currently two other transforms logged, "camera1_link" and "camera2_link" for camera 1 and camera 2 respectively. These two are logged in relation to "base_link" because it makes the transform very easy to keep track of (it never changes). If we want the relationship between the camera and the world, tf would first compute the translation from camera1 to base and then base to world.

As of now, neither of the camera translations are recorded (they simply say 0), because the orientation and position of the cameras on the robot are unknown.