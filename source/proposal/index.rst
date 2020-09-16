.. _Conclusion and Proposed Interface:

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

A jerk is added to the trajectory point definition, as well, so controllers executing a Cartesian
trajectory can provide a smoother trajectory execution. As there is currently no message available
to encode this, a custom message will be provided initially. Essentially, it would be a copy of
``geometry_msgs/Accel`` but reusing this would be semantically incorrect. There is also an `open
discussion <https://github.com/ros/common_msgs/issues/137>`_ on adding Jerks to ``geometry_msgs``
which would be the preferable solution.

.. code-block:: yaml
   :caption: CartesianTrajectoryPoint.msg

   duration time_from_start
   geometry_msgs/Pose pose
   geometry_msgs/Twist twist
   geometry_msgs/Accel acceleration
   <to_be_determined_msgs>/Jerk jerk
   geometry_msgs/Wrench wrench


The definition above doesn't contain any `frame_id` or `timestamp` information raising the need to
contain this into a parent message.

Postures
""""""""

When assembling Cartesian points to a trajectory additional posture information could be
given to specify the desired joint configuration in case of multiple possible solutions coming from
an IK solver or planner.

In a first naive attempt we define posture information inside a separate message to decouple it from
the geometric waypoint definition. A given joint configuration defines the configuration being close
to the desired IK solution. Multiple solutions can be checked for similarity to the given
configuration.

.. code-block:: yaml
   :caption: NaiveCartesianPosture.msg

   float64[] joint_values

The user has to make sure that the number of entries given in the ``posture`` array match
the number of joints similar to the `trajectory_msgs/JointTrajectoryPoint
<http://docs.ros.org/melodic/api/trajectory_msgs/html/msg/JointTrajectoryPoint.html>`_ message.
:ref:`Gijs van der Hoorn` proposed to use a `sensor_msgs/JointState
<http://docs.ros.org/melodic/api/sensor_msgs/html/msg/JointState.html>`_ message for posture
information. However, in contrast to that we propose to use a plain :code:`float64[]` field instead
of a full joint state in order to prevent redundant information / containing a lot of unused fields.
This on the other hand raises the requirement to specify the joint names on a higher level as
mentioned above.

The posture definition proposed above is raises a couple of questions / concerns as discussed inside
`#5 <https://github.com/fzi-forschungszentrum-informatik/fzi_robot_interface_proposal/issues/5>`_.

Should the field be made optional?
''''''''''''''''''''''''''''''''''

* There might be kinematic structures that only result in one single IK solution for a given Cartesian
  pose.
* Posture constraints are not necessarily known or relevant when defining / executing a trajectory.
  The user might not have a preference for a specific configuration in which case a mandatory
  posture definition will force the user to pick one instead.

In order to give users the choice instead of enforcing posture definitions, we propose to have
posture definitions optional.


Should partial posture specifications be supported?
'''''''''''''''''''''''''''''''''''''''''''''''''''

* It might be beneficial to allow partial posture definitions such as a shoulder lift joint or elbow
  joint value only. However, allowing this will require to have the joint names available at this
  stage, as well.
* This would effectively allow both, a "flip bits" approach as used by many vendors,
  as well as a "qnear" approach where the user defines a (partial) joint configuration which is
  close to the desired configuration.
* By allowing partial posture definitions, users can choose to specify selected joints only, while
  leaving the other joints for optimization e.g. by an IK solver or planner. Especially when
  combined with a ``CartesianTolerance`` this would be a rather powerful feature.

As a result, we propose to allow partial posture definitions.


Should postures be made a member of ``CartesianTrajectoryPoint``?
'''''''''''''''''''''''''''''''''''''''''''''''''''''''''''''''''

* One argument against that is that ``CartesianTrajectoryPoint`` should be a pure geometric
  representation of a setpoint independent of any robot configuration. This way, a sequence of
  ``CartesianTrajectoryPoint`` objects could be reused and applied to different robot kinematics and / or
  use-cases. Posture definition should be a part of the trajectory execution but not the trajectory
  definition. However, as raised in `#5 (comment)
  <https://github.com/fzi-forschungszentrum-informatik/fzi_robot_interface_proposal/issues/5#issuecomment-666235226>`_,
  we should not mix up tool paths and robot trajectories. As this proposal is about robot
  trajectories, ``CartesianTrajectoryPoint`` instances should be treated as trajectory setpoints,
  not tool paths and therefor it makes sense to incorporate the posture definition into the
  setpoint.
* If posture is not included into the ``CartesianTrajectoryPoint`` structure, there has to be a way
  of matching posture definitions to trajectory setpoints. For this, either a unique identifier for
  each waypoint would be needed or users would have to provide a posture definition for each
  waypoint to get a 1-to-1 mapping. This would however conflict with the posture definition being
  optional for each waypoint. Additionally, there would have to be additional code required checking
  that each waypoint has a corresponding posture definition.
* If the posture configuration is defined for each ``CartesianTrajectoryPoint`` it can be left empty
  for each waypoint by simply not defining it. Thus, if a user chooses not to define any posture,
  no additional action would be required. If postures would be stored in a parallel datastructure on
  trajectory level, users would have to define an empty posture for each waypoint individually.

For the sake of usability we propose to include the posture definition into the
``CartesianTrajectoryPoint``. This comes with the cost of a CartesianTrajectoryPoint being coupled
to a specific kinematic setup, though.


Should ``posture_joint_names`` be a member of ``CartesianPosture``?
'''''''''''''''''''''''''''''''''''''''''''''''''''''''''''''''''''

* Defining joint names in each ``CartesianPosture`` would effectively increase the amount of
  redundant information in case of a fully defined posture specification for each waypoint, which
  motivated us to exclude it from our naive posture definition above.
* Integrating joint names into the posture definition adds the possibility to define partial posture
  constraints, e.g. only requiring shoulder and elbow configuration.

As reasoned above partial posture definitions are a desired feature which is why including the joint
names into the posture definition is required.

Posture definition
''''''''''''''''''

With the reasons above, we propose the following ``CartesianPosture`` to be included into
``CartesianTrajecoryPoint``:


.. code-block:: yaml
   :caption: CartesianPosture.msg

   # Posture joint names may reflect a subset of all available joints (empty posture definitions are
   # also possible). The length of posture_joint_names and posture_joint_values have to be equal.

   string[] posture_joint_names
   float64[] posture_joint_values


CartesianTrajectory
~~~~~~~~~~~~~~~~~~~

To get a trajectory from multiple ``CartesianTrajectoryPoint`` objects the next container is a
trajectory object consisting of multiple trajectory points.

.. code-block:: yaml
   :caption: CartesianTrajectory.msg

   # header.frame_id is the frame in which all data from CartesianTrajectoryPoint[] is given
   Header header
   CartesianTrajectoryPoint[] points
   string controlled_frame

At this stage we include a time stamp through the ``header`` message.
Note that ``header`` also includes a ``frame_id``, which is the assumed reference frame for the data given in ``points``.
The link that shall follow the trajectory is specified with ``controlled_frame``.
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
   string controlled_frame
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
       <to_be_determined_msgs>/Jerk jerk
       geometry_msgs/Wrench wrench
       CartesianPosture posture
         string [] posture_joint_names
         float64[] posture_joint_values
     string controlled_frame
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
   string controlled_frame
   CartesianTrajectoryPoint desired
       duration time_from_start
       geometry_msgs/Pose pose
       geometry_msgs/Twist twist
       geometry_msgs/Accel acceleration
       <to_be_determined_msgs>/Jerk jerk
       geometry_msgs/Wrench wrench
   CartesianTrajectoryPoint actual
       duration time_from_start
       geometry_msgs/Pose pose
       geometry_msgs/Twist twist
       geometry_msgs/Accel acceleration
       <to_be_determined_msgs>/Jerk jerk
       geometry_msgs/Wrench wrench
   CartesianTrajectoryPoint error
       duration time_from_start
       geometry_msgs/Pose pose
       geometry_msgs/Twist twist
       geometry_msgs/Accel acceleration
       <to_be_determined_msgs>/Jerk jerk
       geometry_msgs/Wrench wrench

.. note::
   For readability reasons we left the commonly-used ROS messages collapsed.
