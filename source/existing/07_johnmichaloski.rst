johnmichaloski
==============

Upstream URL:
https://github.com/johnmichaloski/ROS/blob/master/nistfanuc_ws/src/cartesian_trajectory_msg/msg/CartesianTrajectoryGoal.msg

This is very similar to :ref:`WillowGarage`.

Feature list
------------
 * Trajectory points defined by pose, twist and posture (joint configuration of joints defined in
   `posture_joint_names`, It is undecided where the `posture` should go. Into the trajectory points
   or as a separate field.)
 * Contains path and goal tolerances in
   * absolute position distance
   * absolute rotation angular error
   * absolute linear velocity
   * absolute angular velocity
 * `queue` field which is not really clear how it should be used

Features required from hardware / driver
----------------------------------------
 * Control interface

Message definition
------------------

.. code-block:: yaml

  [CartesianTrajectoryGoal]
  std_msgs/Header header  # A stamp of 0 means "execute now"
  geometry_msgs/PoseStamped tool  # The frame which is being controlled
  std_msgs/String[] posture_joint_names
  CartesianTrajectoryPoint[] points
    std_msgs/Duration time_from_start
    geometry_msgs/Pose pose
    geometry_msgs/Twist twist
    std_msgs/Float64[] posture
    std_msgs/Float64 velocity
    std_msgs/Float64 acceleration
    std_msgs/Float64 jerk
  std_msgs/Duration time_from_start
  geometry_msgs/Pose pose
  geometry_msgs/Twist twist
  std_msgs/Float64[] posture
  #trajectory_msgs/JointTrajectory posture  # For determining the redundancy

  CartesianTolerance path_tolerance  # Tolerance for aborting the path
    float64 position
    float64 orientation  # Permitted angular error
    float64 velocity
    float64 angular_velocity
  CartesianTolerance goal_tolerance  # Tolerance for when reaching the goal is considered successful
    float64 position
    float64 orientation  # Permitted angular error
    float64 velocity
    float64 angular_velocity
  std_msgs/Bool queue

.. code-block:: yaml

   [CartesianTrajectoryResult]
   std_msgs/Int32 error_code  # 0 if successful
   CartesianTrajectoryPoint cartesian_state
   trajectory_msgs/JointTrajectoryPoint joint_state
   geometry_msgs/Twist pose_error
   geometry_msgs/Twist twist_error
   CartesianTolerance path_tolerance  # Current tolerance used for the path

.. code-block:: yaml

   [CartesianTrajectoryError]
   int32 SUCCESSFUL = 0
   int32 ROOT_TRANSFORM_FAILED
   int32 TOOL_TRANSFORM_FAILED
   int32 PATH_TOLERANCE_VIOLATED
   int32 INVALID_POSTURE
