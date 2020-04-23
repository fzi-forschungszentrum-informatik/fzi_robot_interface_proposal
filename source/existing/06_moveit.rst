MoveIt!
=======

Implemented in moveit_msgs:
https://github.com/ros-planning/moveit_msgs/blob/master/msg/CartesianTrajectory.msg

Feature list
------------
 * Contains accelerations

Features required from hardware / driver
----------------------------------------
 * Control interface

Message definition
------------------

.. code-block:: yaml

  [CartesianTrajectory]
  Header header

  # The name of the Cartesian frame being tracked with respect to the base frame provided in header.frame_id
  string tracked_frame

  CartesianTrajectoryPoint[] points
    CartesianPoint point
      geometry_msgs/Pose pose
      geometry_msgs/Twist velocity
      geometry_msgs/Accel acceleration
    duration time_from_start
