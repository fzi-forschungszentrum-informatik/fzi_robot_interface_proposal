Vendor interfaces for Cartesian motions
=======================================

In section :ref:`Existing` we had a closer look at existing Cartesian interface definitions and
suggestions. While most of them showed a great similarity to existing joint-based interfaces,
there's one that sticks out. :ref:`RepI0003` suggests an interface that is closer to how most robot
manufacturers define their own interfaces. To pickup this thought, we had a closer look at different
vendor interfaces to check for similarities.

While existing interfaces such as the
`control_msgs/FollowJointTrajectory.action
<http://docs.ros.org/api/control_msgs/html/action/FollowJointTrajectory.html>`_ are widely accepted
inside the ROS community it requires quite some effort to be filled correctly. Users have to
provide positions, velocities and timings for each waypoint significantly affecting the
path the robot is following between waypoints. To handle this, users usually leverage advanced tools
such as `MoveIt! <https://moveit.ros.org/>`_ to fill in all these constraints while giving away full
control of the actual path the robot's joints and end effector will take.

Most robot manifacturers offer programming interfaces that focus more on point-to-point motions and
how interpolation between those waypoints is done. Some of them already expose their full
motion command set to ROS, e.g. :ref:`Doosan`. Having a closer look at how different manufacturers
handle motion commands and how they are interfaced, obviously makes sense in order to create a
standardized ROS interface for defining trajectories in such a way.

As a result of this we would like to extend :ref:`RepI0003` to not only offering such an interface
at planning level but also as a direct control interface for industrial robots.

.. toctree::
   :maxdepth: 2

   KUKA.rst
   UR.rst
   Fanuc.rst
   Doosan.rst
   Franka.rst
   ABB.rst
   Yaskawa.rst
   summary.rst
