
Navigation Wiki: seabee_2dnav

There is already a lot of existing documentation regarding this package online at http://wiki.ros.org/navigation/Tutorials/RobotSetup

In particular, this module inherits from the move_base module. As of now, no actual code was written (in this package) for seabee. This package is solely responsible for path planning (also, only in 2d environments) and takes in data with a specific format that gives it all the information it needs to create paths between the robots current location and goals (which are sent to it externally by some other package(s)).