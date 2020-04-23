
FZI
===
Our internal Cartesian trajectory definition.

This is bit different to the other approaches as it doesn't require the user to
specify detailed timing and / or velocity information in each waypoint.
Instead, a maximum velocity and acceleration can be specified in both,
translation and rotation domain.


Feature list
------------
 * Global max velocities and accelerations instead of timing information
 * Contains interpolation distance
 * Contains offsets to shift the trajectory in Cartesian space

Features required from hardware / driver
----------------------------------------
 * Control interface
 * Trajectory interpolation
 * Calculate timings and velocities / accelerations in waypoints

Message definition
------------------

.. code-block:: yaml

  [CartesianPoseTrajectoryGoal]
  fzi_manipulation_msgs/CartesianExecutionConfig params
    string reference_frame
    string endeffector_frame
    float64 acceleration_lin
    float64 acceleration_rot
    float64 velocity_lin
    float64 velocity_rot
    float64 interpolation_lin
    float64 interpolation_rot
    geometry_msgs/Pose trajectory_offset
    bool has_trajectory_offset
    geometry_msgs/Pose tcp_offset
    bool has_tcp_offset
  geometry_msgs/Pose[] points

Field details
-------------

Interpolation
~~~~~~~~~~~~~

The used interpolation distances are currently used to create setpoints between
the ones specified to get closer to the desired Cartesian linear motion when
this is being executed using a joint-based backend, e.g. by using an inverse
kinematics solver for each setpoint and creating a joint-based trajectory out
of that.

Trajectory Offset
~~~~~~~~~~~~~~~~~

The trajectory offset offsets the trajectory relative to the specified
**reference_frame**. This can be useful in situations where a trajectory shall
be executed in contact with a surface but it should be executed a couple of
centimeters above the surface for testing purposes. This is equivalent to
applying the specified transformation on the **reference_frame** before executing
the trajectory.

TCP Offset
~~~~~~~~~~

The tcp offset shifts the trajectory relative to the specified
**endeffector_frame**. This is useful for example if a trajectory is specified
directly ontop of an object's surface, but the endeffector should keep a
distance to the surface at all times, e.g. to make room for a gluing layer
being applied. This is equivalent to applying the specified transformation on
the **endeffector_frame** before executing the trajectory.
