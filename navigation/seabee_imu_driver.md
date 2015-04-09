Navigation Wiki: seabee_imu_driver

This package serves as a wrapper for the package provided by ROS called xsens_driver. The documentation for that can be found here http://wiki.ros.org/xsens_driver

This package reads in data sent by the xsens_driver node called mtnode.py on the rostopic "/imu/data" and sends it out on the "/odom" rostopic. The message format for "/imu/data" can be found in package sensor_msgs.msg.Imu and the message format for "/odom" can be found in nav_msgs.msg.Odometry. The main distinction between the two is that Odometry provides position and velocity data, while the Imu message only provides acceleration data (with some exceptions). As such, one of the functions of the seabee_imu_driver is to integrate the acceleration data to provide accurate velocital and positional data. 

There are currently two nodes to this package - imu_node.py and seabee_imu_filters.py.

imu_node.py : 
    
    This node is pretty straight-forward. It has an integrator (objects which we also wrote, residing in the seabee_imu_integrator.py file) and simply forwards data received from seabee_imu_filters.py to the integrator, which makes its calculations, and then publishes the updated data on /odom. It also provides a reset service ("reset_odom"), which zeros all velocity and position values.

    seabee_imu_integrator:

        These are pretty straight forward. Theres a lot of rendundancy initializing/updating the whole list of values in every message, but nothing very complex goes on here. The most complex part is the drift_filters that attempt to prevent/account for drift, but those aren't done at the time of writing this.

seabee_imu_filters.py:

    The seabee_imu_filters node is the life-blood of the imu stack right now. It has three main components - the Logger, the FilterStack, and the filters.

    **discuss the effect of references and deep copies 

    Logger: 

        The logger listens to /imu/data and collects messages, and then stores them in a list which acts as a queue. There are two main parameters, initial_message_pack_size and message_pack_update_size. initial_message_pack_size is the size of the packages of messages that are stored in the queue. message_pack_update_size is the # of new messages in each message pack. As such, consecutive message packs have some reduncancy of messages. The FilterStack pulls packages as it needs them from the Logger.

    FilterStack: 

        The FilterStack is an object that initializes and applies the different filters. It provides the benefit of having one location that manages all of the different types of filters, allowing for easy insertion/deletion/manipulation of one or more filters. Currently, it only uses a moving_filter (surprisingly effective!). This polls Logger and then sends the message_pack through the filters, and then publishes the finished data to /nav_filtered_signals/filter_stack.

        **talk about enviro vars

    Filters: 

        These inheret from the BaseFilter class (which ensures compatibility within the FilterStack). There are two currently written, only one is used (moving_filter)

        ButterworthFilter:

            Utilizes scipy.signal library for signal processing to implement a low pass filter. Pretty straight forward, the documentation can be fuond online if there are any questions.

            This certainly is a huge step up from the raw data, but the behavior wasn't ideal, so it's not used.

        MovingAverageFilter:

            Again, pretty simple in nature and surprisingly effective. For every point in the given data set (in this case, its usually the middle 100 values or so out of the entire message pack) it takes the average of a group of points around it (usually ~50) and sets the value of the point to that result. It smoothes out the curve significantly and provides relatively accurate integrals for position and velocity.