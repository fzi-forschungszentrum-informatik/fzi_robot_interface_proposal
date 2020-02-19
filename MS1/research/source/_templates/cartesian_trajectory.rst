Title
=====
Textual description? (Source, date, etc.)

Feature list
------------
 * Respects configuration
 * Contains tolerances
 * ...

Features required from hardware / driver
----------------------------------------
 * Impedance control
 * ...

Message definition
------------------

.. code-block:: yaml
  [CartesianTrajectoryGoal]
  Header header  # A stamp of 0 means "execute now"
  string[] effector_names
  CartesianTrajectory[] trajectory
    Pose tool  # The frame (offset ?) which is being controlled or it can be treated as separate effector ?
    CartesianTrajectoryPoint[] points
      duration time_from_start
      Pose pose
      Twist twist
  CartesianImpedance[] impedance
      Pose center_of_compliance # or it can be treated as separate effector ?
      TBD stiffness % cartesian stiffness
      TBD damping % damping ratio
  CartesianTolerance[] path_tolerance  # Tolerance for aborting the path
    float64 position
    float64 orientation  # Permitted angular error
    float64 velocity
    float64 angular_velocity
  CartesianTolerance[] goal_tolerance  # Tolerance for when reaching the goal is considered successful
  string[] joint_names
  JointTrajectoryPoint[] posture  # For determining the redundancy
  JointImpedance[] nullspace_impedance
    float64[] stiffness
    float64[] damping
