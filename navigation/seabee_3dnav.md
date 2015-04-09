Navigation Wiki: seabee_3dnav

This package acts as the (currently) highest level of navigation control for seabee. It provides two services via actionlib - SeabeeGoToLocation and SeabeeGoToOrientation. 

navigation_node.py:

    This node subsribes to /odom and /imu/data for location/vel and orientation information respectively. It publishes desired velocity info to /cmd_vel

    SeabeeGoToOrientationServer:

        Takes in a desired delta_theta (in radians!) just for rotation around the z-axis. If the value is positive, it contrusts a message with positive angular velocity and sends it over /cmd_vel and the submarine will subsequently rotate right. Otherwise it will do the opposite, create a negative angular velocity, send it, and rotate left. It sends this message until the difference between the measured angle and desired final angle is within a window. At that point, it sends a message with all values 0 to stop the motors.

    SeabeeGoToLocationServer:

        This takes in a desired location (with regards to seabee's current position) and takes the submarine there. First, it takes the inverse tan of the x and y coord's and calls the Orientation server to rotate the sub. Then, once that is done, it constructs a message for forward movement and does so until the /odom indicates we have traveled the desired amount of distance, at which point it sends a message with all values 0 to stop the submarine. 