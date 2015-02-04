
Navigation Wiki: seabee_imu_driver

This package serves as a wrapper for the package provided by ROS called xsens_driver. The documentation for that can be found here http://wiki.ros.org/xsens_driver

This package reads in data sent by the xsens_driver node called mtnode.py on the rostopic "/imu/data" and sends it out on the "/odom" rostopic. The message format for "/imu/data" can be found in package sensor_msgs.msg.Imu and the message format for "/odom" can be found in nav_msgs.msg.Odometry. The main distinction between the two is that Odometry provides position and velocity data, while the Imu message only provides acceleration data (with some exceptions). As such, one of the functions of the seabee_imu_driver is to integrate the acceleration data to provide accurate velocital and positional data. 

It currently works (terribly). The current plan is to save every major type of integrator that's created (which will be found in the seabee_imu_integrator module), so that we can easily switch between them. The current integrator just simply multiplies acceleration by delta t and adds it to a running total.