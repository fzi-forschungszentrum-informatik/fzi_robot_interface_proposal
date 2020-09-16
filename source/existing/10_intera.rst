Rethink Robotics Intera SDK
===========================

.. _wiki: https://sdk.rethinkrobotics.com/intera/Motion_Interface_Tutorial
.. _ros_github: https://github.com/RethinkRobotics
.. _ros_manual: https://sdk.rethinkrobotics.com/intera/

Cartesian trajectories for Rethink robots

.. table:: Vendor specifics

  =================================   =======================================
  Teach pendant                       Integrated into the robot
  Programming / simulation software   Intera 5
  Software                            Intera SDK
  Programming language                Python
  Relevant hardware                   Robots Sawyer and Baxxter
  =================================   =======================================

Rethink robots support a native ROS interface. The ROS master runs on the robot.

**Further reading**
  * `ros_github`_
  * `ros_manual`_
  * `wiki`_

Feature list
------------
 * Contains accelerations
 * For Cartesian segments, the joint positions are used as nullspace biases
 * Joint interpolation or cartesian interpolation
 * Hybrid Trajectories not possible
 * All trajectory points take an optional WaypointOptions
 * Contains path and goal tolerances in joint space          
 * Contains for each waypoint:
   * Max joint speed ratio
   * Max joint acceleration
   * Max linear speed
   * Max linear acceleration
   * Max rotational speed
   * Max rotational acceleration
   * Corner distance
 * Allows to specify active endpoint

Features required from hardware / driver
----------------------------------------


Message definition
------------------
Trajectories have some global options and a list of waypoint. Each waypoint itself can also have some local options.

.. code-block:: yaml

  [Waypoint]
  # Representation of a waypoint used by the motion controller

  # Desired joint positions
  # For Cartesian segments, the joint positions are used as nullspace biases
  float64[] joint_positions

  # Name of the endpoint that is currently active
  string active_endpoint

  # Cartesian pose
  # This is not used in trajectories using joint interpolation
  geometry_msgs/PoseStamped pose

  # Waypoint specific options
  # Default values will be used if not set
  # All waypoint options are applied to the segment moving to that waypoint
  WaypointOptions options

.. code-block:: yaml

  [WaypointOptions]
  # Optional waypoint label
  string label

  # Ratio of max allowed joint speed : max planned joint speed (from 0.0 to 1.0)
  float64 max_joint_speed_ratio

  # Slowdown heuristic is triggered if tracking error exceeds tolerances - radians
  float64[] joint_tolerances

  # Maximum accelerations for each joint (only for joint paths) - rad/s^2.
  float64[] max_joint_accel

  ###########################################################
  # The remaining parameters only apply to Cartesian paths

  # Maximum linear speed of endpoint - m/s
  float64 max_linear_speed

  # Maximum linear acceleration of endpoint - m/s^2
  float64 max_linear_accel

  # Maximum rotational speed of endpoint - rad/s
  float64 max_rotational_speed

  # Maximum rotational acceleration of endpoint - rad/s^2
  float64 max_rotational_accel

  # Used for smoothing corners for continuous motion - m
  # The distance from the waypoint to where the curve starts while blending from
  # one straight line segment to the next.
  # Larger distance:  trajectory passes farther from the waypoint at a higher speed
  # Smaller distance:  trajectory passes closer to the waypoint at a lower speed
  # Zero distance:  trajectory passes through the waypoint at zero speed
  float64 corner_distance

.. code-block:: yaml

  [Trajectory]
  # Representation of a trajectory used by the engine and motion controller.

  # optional label
  string label

  # Array of joint names that correspond to the waypoint joint_positions
  string[] joint_names

  # Array of waypoints that comprise the trajectory
  Waypoint[] waypoints

  # Trajectory level options
  TrajectoryOptions trajectory_options
  
.. code-block:: yaml

  [TrajectoryOptions]
  # Trajectory interpolation type
  string CARTESIAN=CARTESIAN
  string JOINT=JOINT
  string interpolation_type

  # True if the trajectory uses interaction control, false for position control.
  bool interaction_control

  # Interaction control parameters
  intera_core_msgs/InteractionControlCommand interaction_params

  # Allow small joint adjustments at the beginning of Cartesian trajectories.
  # Set to false for 'small' motions.
  bool nso_start_offset_allowed

  # Check the offset at the end of a Cartesian trajectory from the final waypoint nullspace goal.
  bool nso_check_end_offset

  # Options for the tracking controller:
  TrackingOptions tracking_options

  # Desired trajectory end time, ROS timestamp
  time end_time

  # The rate in seconds that the path is interpolated and returned back to the user
  # No interpolation will happen if set to zero
  float64 path_interpolation_step

.. code-block:: yaml

  [TrackingOptions]
  # Minimum trajectory tracking time rate:  (default = less than one)
  bool     use_min_time_rate
  float64  min_time_rate
  
  # Maximum trajectory tracking time rate:  (1.0 = real-time = default)
  bool     use_max_time_rate
  float64  max_time_rate
  
  # Angular error tolerance at final point on trajectory (rad)
  float64[] goal_joint_tolerance
  
  # Time for the controller to settle within joint tolerances at the goal (sec)
  bool     use_settling_time_at_goal
  float64  settling_time_at_goal
