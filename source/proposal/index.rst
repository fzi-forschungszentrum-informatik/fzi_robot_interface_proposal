Conclusion / Proposed Interface
===============================
Cartesian trajectory definitions have long been a complicated topic in ROS.
The investigated definitions showed that quite some differences exist about
generality vs expressiveness vs ease of use, not to mention the somewhat
`orthogonal` industrial way of doing things.  In fact, most people will agree
that it's probably impossible to cover every detail, to meet all requirements
of all possible users of such an interface.
Nevertheless, with this document having a lot of information in one place, we
believe that there are sufficient similarities to start a new trial.

If you don't want to read the reasoning, you can jump to the :ref:`proposed interface <Proposed interface>` directly.

Components
----------
Here's our proposal for Cartesian Trajectory Definitions.
We present the new message types step by step and explain our design decisions,
basing them on conclusions from the previous section :ref:`Existing`.

CartesianTrajectoryPoint
~~~~~~~~~~~~~~~~~~~~~~~~

One common thing in all existing proposals is a Cartesian trajectory point definition. This
would be fairly similar to the `trajectory_msgs/JointTrajectoryPoint
<http://docs.ros.org/melodic/api/trajectory_msgs/html/msg/JointTrajectoryPoint.html>`_ message. As
the joint version contains joint efforts, we propose to also include Cartesian wrenches to the
Cartesian point definition, although this is only introduced in one of the existing interfaces.


.. code-block:: yaml
   :caption: CartesianTrajectoryPoint.msg

   duration time_from_start
   geometry_msgs/Pose pose
   geometry_msgs/Twist twist
   geometry_msgs/Accel acceleration
   geometry_msgs/Wrench wrench


The definition above doesn't contain any `frame_id` or `timestamp` information raising the need to
contain this into a parent message. The ``CartesianTrajectoryPoint`` is meant to be a pure geometric
representation of a setpoint independent of any robot configuration.

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
containing a lot of unused fields. This on the other hand raises the requirement to specify the joint
names on a higher level as mentioned above.

It is still open how posture information should be treated. Here, multiple options seem valid at
first glance:

* Make the field optional. If left empty, there is no preferred posture information.
* Allow partial postures. It might be beneficial to allow partial posture
  definitions such as a shoulder lift joint or elbow joint value only. However,
  allowing this will require to have the joint names available at this stage,
  as well.

As at the current point we would like to leave the discussion about posture definition quite open,
we propose to use a separate message for this, although it currently only contains one element.

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

At this stage we include a time stamp through the ``header`` message.
Note that ``header`` also includes a ``frame_id``, which is the assumed reference frame for the data given in ``points``.
The link that shall follow the trajectory is specified with ``tcp_frame``.
Some of
the existing proposals use a ``geometry_msgs/Pose`` field to express the points' reference frame. However, we think that using names as identifiers makes this interface more versatile, because it delegates possible lookups to where this information is easier available.

CartesianTolerance
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

In the investigated interfaces tolerances are often proposed as scalar values for each of [position,
orientation, velocity, angular velocity]. In contrast we propose specifying constraints for each
axis individually by using 3-dimensional datatypes:

.. code-block:: yaml
   :caption: CartesianTolerance.msg

   geometry_msgs/Vector3 position_error
   geometry_msgs/Vector3 orientation_error
   geometry_msgs/Twist twist_error
   geometry_msgs/Accel acceleration_error
   geometry_msgs/Wrench wrench_error

With this definition users can define tolerances per axis, where rotational constraints are meant to
be angle differences in the local coordinate system. Therefore we use ``geometry_msgs/Vector3``
instead of ``geometry_msgs/Pose`` for ``position_error`` and ``orientation_error``.


CartesianTrajectoryAction
~~~~~~~~~~~~~~~~~~~~~~~~~

Similar to the `control_msgs/FollowJointTrajectory
<http://docs.ros.org/melodic/api/control_msgs/html/action/FollowJointTrajectory.html>`_ action we
propose an action interface for executing Cartesian trajectories.

.. code-block:: yaml
   :caption: FollowCartesianTrajectory.action

   CartesianTrajectory trajectory
   CartesianTolerance path_tolerance
   CartesianTolerance goal_tolerance
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
   CartesianTolerance error

For the result and feedback we again are following the methods from joint-based trajectory
execution. The errors get extended by a posture-related error flag.


.. _Proposed interface:

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
       geometry_msgs/Pose pose
       geometry_msgs/Twist twist
       geometry_msgs/Accel acceleration
       geometry_msgs/Wrench wrench
     string tcp_frame
     string[] posture_joint_names
     CartesianPosture[] postures
       float64[] joint_values
   CartesianTolerance path_tolerance
     geometry_msgs/Vector3 position_error
     geometry_msgs/Vector3 orientation_error
     geometry_msgs/Twist twist_error
     geometry_msgs/Accel acceleration_error
     geometry_msgs/Wrench wrench_error
   CartesianTolerance goal_tolerance
     geometry_msgs/Vector3 position_error
     geometry_msgs/Vector3 orientation_error
     geometry_msgs/Twist twist_error
     geometry_msgs/Accel acceleration_error
     geometry_msgs/Wrench wrench_error
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
       geometry_msgs/Pose pose
       geometry_msgs/Twist twist
       geometry_msgs/Accel acceleration
       geometry_msgs/Wrench wrench
   CartesianTrajectoryPoint actual
       duration time_from_start
       geometry_msgs/Pose pose
       geometry_msgs/Twist twist
       geometry_msgs/Accel acceleration
       geometry_msgs/Wrench wrench
   CartesianTrajectoryPoint error
       duration time_from_start
       geometry_msgs/Pose pose
       geometry_msgs/Twist twist
       geometry_msgs/Accel acceleration
       geometry_msgs/Wrench wrench

.. note::
   For readability reasons we left the commonly-used ROS messages collapsed.
