Banachowicz2
============
Proposed by Konrad Banachowicz, 2nd version

Part of http://wiki.ros.org/trajectory_msgs/Reviews/Cartesian%20Trajectories_API_Review_2013_06_05

Feature list
------------
 * Trajectory points consist of pose and twist
 * Contains impedance for each trajectory point
   * target_frame_id
   * stiffness (6D)
   * damping (6D)
 * Contains path tolerance in
   * translation
   * orientation
   * twist
   * wrench
 * Contains posture information to handle redundancies (for each effector???)
 * Contains a nullspace impedance

Features required from hardware / driver
----------------------------------------
 * Control interface
 * Impedance control

Message definition
------------------

.. code-block:: yaml

  [CartesianTrajectory]
  Express trajectory of frame target_frame_id in relation to frame header.frame_id .
  Header header # stamp - trajectory start time, frame_id - trajectory reference frame 
  string target_frame_id # target controlled frame
  CartesianTrajectoryPoint[] points
    duration time_from_start
    Pose pose
    Twist twist

.. code-block:: yaml

  [CartesianImpedance]
  # Parameters of spring-damper located between frame header.frame_id and frame target_frame_id.
  Header header # stamp - trajectory start time, frame_id - spring-damper base frame 
  string target_frame_id # spring-damper end frame
  CartesianImpedancePoint[] points
    duration time_from_start
    CartesianStiffness stiffness
      Vector3 translational
      Vector3 rotational
    CartesianDamping damping
      Vector3 translational
      Vector3 rotational

.. code-block:: yaml

  [CartesianConstraints]
  # Constraint the relation between header.frame_id and target_frame_id.
  Header header # stamp - time of constraint activation
  string target_frame_id
  duration time_from_start # duration of constraint activation
  TranslationConstraint translation
  OrientationConstraint orientation
  Twist twist
  Wrench wrench

.. code-block:: yaml

  [CartesianTrajectoryGoal]
  CartesianTrajectory[] trajectory
  CartesianImpedance[] impedance
  CartesianConstraints[] path_constraints  # Tolerance for aborting the path
  JointTrajectory[] posture  # For determining the redundancy
  JointImpedance[] nullspace_impedance
