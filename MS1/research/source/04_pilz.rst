Pilz
=====
Not used in a message style but implemented in C++ code. Repo:
https://github.com/PilzDE/pilz_industrial_motion

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
  string group_name # MoveIt! planning group name
  string link_name
  CartesianTrajectoryPoint[] points
    Duration time_from_start
    Pose pose
    Twist velocity
    Twist acceleration
