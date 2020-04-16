Conclusion / Proposed Interface
===============================

This section wraps up section :ref:`Existing` and contains out proposed interface derived from the information collected in the previous
sections.

Conclusion
----------

CartesianTrajectoryPoint
~~~~~~~~~~~~~~~~~~~~~~~~

One common thing in all existing proposal is to have a Cartesian trajectory point definition. This
would be fairly similar to the `trajectory_msgs/JointTrajectoryPoint
<http://docs.ros.org/melodic/api/trajectory_msgs/html/msg/JointTrajectoryPoint.html>`_ message. As
the joint version contains joint efforts, we propose to also include Cartesian wrenches to the
Cartesian point definition, althogh this is only proposed in one of the existing interfaces.


.. code-block:: yaml
   :caption: CartesianTrajectoryPoint.msg

   duration time_from_start
   geometry_mgs/Pose pose
   geometry_mgs/Twist twist
   geometry_msgs/Accel acceleration
   geometry_msgs/Wrench wrench


The definition above doesn't contain any `frame_id` or `timestamp` information raising the need to
contain this into a parent message containing this.

.. code-block:: yaml
   :caption: CartesianPosture.msg

   float64[] joint_values

Postures - to solve joint configuration ambiguities - are defined in a separate message type to
decouple it from the geometric waypoint definition. When assembling those Cartesian points to a
trajectory additional posture information should be given.
The user has to make sure that the number of entries given in the ``posture`` array match the number
of joints similar to the `trajectory_msgs/JointTrajectoryPoint
<http://docs.ros.org/melodic/api/trajectory_msgs/html/msg/JointTrajectoryPoint.html>`_ message.
:ref:`Gijs Vanderhoorn` proposed to use a
`sensor_msgs/JointState <http://docs.ros.org/melodic/api/sensor_msgs/html/msg/JointState.html>`_
message for posture information. However, in contrast to that we propose to use a plain
:code:`float64[]` field instead of a full joint state in order to prevent redundant information /
contain a lot of unused fields. This on the other hand raises the requirement to specify the joint
names on a higher level as mentioned above.

It is still open how posture information should be treated. Here, multiple options seem valid at
first glance
* Make the field optional. If left empty, there is no preferre posture information.
* Allow partial postures. It might be beneficial to allow partial posture definitions such as a
shoulder lift joint or elbow joint value only. However, allowing this will require to have the
joint names available at this stage, as well.

CartesianTrajectory
~~~~~~~~~~~~~~~~~~~

To get a trajectory from multiple ``CartesianTrajectoryPoint`` objects the next container is a
trajectory object consisting of multiple trajectory points.

.. code-block:: yaml
   :caption: CartesianTrajectory.msg

   # header.frame_id is the frame in which all data from CartesianTrajectoryPoint[] is given
   Header header
   CartesianTrajectoryPoint[] points
   string tcp_frame
   string[] posture_joint_names
   CartesianPosture[] postures

At this stage we introduce stamping information for all the trajectory points and a ``frame_id``
corresponding the the link that we want to follow the poses defined inside the trajectory. Some of
the existing propsals use a ``geometry_msgs/Pose`` field instead but we think that reusing existing
frame names makes this interface more versatile so users won't have to lookup the transform to their
TCP by hand when executing such a trajectory.

CartesianError / CartesianTolerance
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

In the investigated interfaces tolerances are often proposed as scalar values for each of [position,
orientation, velocity, angular velocity]. We proppose to allow path constraints in each positional
dimension as well as each angular dimension (rotation about each of the end effector's axes):

.. code-block:: yaml
   :caption: CartesianError.msg

   geometry_msgs/Vector3 position
   geometry_msgs/Vector3 orientation
   geometry_mgs/Twist velocity
   geometry_mgs/Accel acceleration

CartesianTrajectoryAction
~~~~~~~~~~~~~~~~~~~~~~~~~

Similar to the `control_msgs/FollowJointTrajectory
<http://docs.ros.org/melodic/api/control_msgs/html/action/FollowJointTrajectory.html>`_ action we
propose an action interface for executing Cartesian trajectories.

.. code-block:: yaml
   :caption: FollowCartesianTrajectory.action

   CartesianTrajectory trajectory
   CartesianError path_tolerance
   CartesianError goal_tolerance
   duration goal_time_tolerance

   ---

   int32 error_code
   int32 SUCCESSFUL = 0
   int32 INVALID_GOAL = -1 # e.g. illegal quaternions in poses
   int32 INVALID_JOINTS = -2
   int32 OLD_HEADER_TIMESTAMP = -3
   int32 PATH_TOLERANCE_VIOLATED = -4
   int32 GOAL_TOLERANCE_VIOLATED = -5
   int32 INVALID_POSTURE = -6

   string error_string

   ---

   Header header
   string tcp_frame
   CartesianTrajectoryPoint desired
   CartesianTrajectoryPoint actual
   CartesianError error

For the result and feedback we again are following the methods from joint-based trajectory
execution. The errors get extended by a posture-related error flag.


TLDR; Proposed interface
------------------------

As elaborated in the previous section we propose the following action interface

.. code-block:: yaml
   :caption: FollowCartesianTrajectory.action

   CartesianTrajectory trajectory
     # header.frame_id is the frame in which all data from CartesianTrajectoryPoint[] is given
     Header header
     CartesianTrajectoryPoint[] points
       duration time_from_start
       geometry_mgs/Pose pose
       geometry_mgs/Twist twist
       geometry_msgs/Accel acceleration
       geometry_msgs/Wrench wrench
     string tcp_frame
     string[] posture_joint_names
     CartesianPosture[] postures
       float64[] joint_values
   CartesianError path_tolerance
     geometry_msgs/Vector3 position
     geometry_msgs/Vector3 orientation
     geometry_mgs/Twist velocity
     geometry_mgs/Accel acceleration
   CartesianError goal_tolerance
     geometry_msgs/Vector3 position
     geometry_msgs/Vector3 orientation
     geometry_mgs/Twist velocity
     geometry_mgs/Accel acceleration
   duration goal_time_tolerance

   ---

   int32 error_code
   int32 SUCCESSFUL = 0
   int32 INVALID_GOAL = -1 # e.g. illegal quaternions in poses
   int32 INVALID_JOINTS = -2
   int32 OLD_HEADER_TIMESTAMP = -3
   int32 PATH_TOLERANCE_VIOLATED = -4
   int32 GOAL_TOLERANCE_VIOLATED = -5
   int32 INVALID_POSTURE = -6

   string error_string

   ---

   Header header
   string tcp_frame
   CartesianTrajectoryPoint desired
       duration time_from_start
       geometry_mgs/Pose pose
       geometry_mgs/Twist twist
       geometry_msgs/Accel acceleration
       geometry_msgs/Wrench wrench
   CartesianTrajectoryPoint actual
       duration time_from_start
       geometry_mgs/Pose pose
       geometry_mgs/Twist twist
       geometry_msgs/Accel acceleration
       geometry_msgs/Wrench wrench
   CartesianError error
     geometry_msgs/Vector3 position
     geometry_msgs/Vector3 orientation
     geometry_mgs/Twist velocity
     geometry_mgs/Accel acceleration

.. note::
   For readability reasons we left the commonly-used ROS messages collapsed.
