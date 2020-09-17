.. _Gijs van der Hoorn:

Gijs van der Hoorn
==================

https://github.com/gavanderhoorn/common_msgs/pull/1/files


Feature list
------------
 * Contains accelerations
 * Contains wrenches
 * Contains posture information to handle redundancies

Features required from hardware / driver
----------------------------------------
 * Control interface

Message definition
------------------

.. code-block:: yaml

  [LinearTrajectory]
  std_msgs/Header header
  string child_frame_id
  LinearTrajectoryPoint[] points
    Duration time_from_start
    Pose pose
    Twist velocity
    Accel acceleration
    Wrench wrench
    sensor_msgs/JointState configuration # optional

Field details
-------------
The following list is not complete. Please see the linked PR for more information.

header.frame_id
~~~~~~~~~~~~~~~
The trajectory describes the motion of this frame relative to `header.frame_id`.

child_frame_id
~~~~~~~~~~~~~~
Each point in the trajectory specifies at least a valid pose and a time at
which that pose must be reached by `child_frame_id`.

time_from_start
~~~~~~~~~~~~~~~
Time (in seconds) at which the system state encoded in this trajectory point
is to be attained, relative to the start of trajectory execution.

Required field.

It is an error to not initialise this field.

configuration
~~~~~~~~~~~~~
Preferred joint space configuration to achieve `pose`.

Optional field.

This field is encoded as a `sensor_msgs/JointState` to allow for the greatest
flexibility when describing joint configurations (compared to bitmasks or
lists of booleans/integers).

Only the `name` and `position` fields of the JointState message are used.
Values in other fields are ignored.

Joints present in the system, but for which no values are provided will be
considered unrestricted wrt possible IK solutions (ie: so called 'free'
joints).

Leaving this field empty in case of kinematic systems for which multiple IK
solutions exist for a given pose, in general or because of their by-design
underconstraint nature (ie: in case of (hyper-)redundancy), could lead to
motion discontinuities (ie: mid-motion configuration changes) as IK solvers
may not provide the closest or preferred solutions automatically for those
types of systems.

Example of a 6D kinematic configuration with all joints given values:

.. code-block:: yaml

  configuration.name = ['joint_1', 'joint_2', ..., 'joint_6']
  configuration.position = [0.1092, 0.4012, ..., 1.6323]

Drivers moving robots to this trajectory point are required to use an IK
solution either identical to or as close as possible to this joint
configuration.

Encoding a "configuration bitmask" (ie: shoulder, elbow, wrist) typically
used in industrial robotics into a 'configuration' field may be done as
follows:

.. code-block:: yaml

  configuration.name = ['joint_2', 'joint_3', 'joint_4']
  configuration.position = [0.0, 0.0, 3.1415]

Note: if only 'joint_4' would have been specified, the shoulder and elbow
joints would not have been constrained, leading to potentially different
solutions being used.

Also note: as `configuration` encodes joint angles instead of binary states,
turn numbers and configuration flags can be expressed as a single joint angle.
