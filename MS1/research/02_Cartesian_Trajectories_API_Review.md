# Willow Garage (2010)
Part of http://wiki.ros.org/trajectory_msgs/Reviews/Cartesian%20Trajectories_API_Review_2013_06_05

Specified inside http://wiki.ros.org/robot_mechanism_controllers/Reviews/Cartesian%20Trajectory%20Proposal%20API%20Review

## Feature list
 * Trajectory points defined by pose, twist and posture (joint configuration of joints defined in
   `posture_joint_names`, It is undecided where the `posture` should go. Into the trajectory points
   or as a separate field.)
 * Contains path and goal tolerances
 * `queue` field which is not really clear how it should be used

## Features required from hardware / driver
 * Control interface

## Message definition

```
[CartesianTrajectoryGoal]
CartesianTrajectory trajectory
  Header header  # A stamp of 0 means "execute now"
  PoseStamped tool  # The frame which is being controlled
  string[] posture_joint_names
  CartesianTrajectoryPoint[] points
    duration time_from_start
    Pose pose
    Twist twist
    float64[] posture
  JointTrajectory posture  # For determining the redundancy
    std_msgs/Header header
    string[] joint_names
    trajectory_msgs/JointTrajectoryPoint[] points
      float64[] positions
      float64[] velocities
      float64[] accelerations
      float64[] effort
      duration time_from_start
CartesianTolerance path_tolerance  # Tolerance for aborting the path
  float64 position
  float64 orientation  # Permitted angular error
  float64 velocity
  float64 angular_velocity
CartesianTolerance goal_tolerance  # Tolerance for when reaching the goal is considered successful
bool queue
```

```
[CartesianTrajectoryResult]
int32 error_code  # 0 if successful
CartesianTrajectoryPoint cartesian_state
JointTrajectoryPoint joint_state
Twist pose_error
Twist twist_error
CartesianTolerance path_tolerance  # Current tolerance used for the path
```

```
[CartesianTrajectoryError]
int32 SUCCESSFUL = 0
int32 ROOT_TRANSFORM_FAILED
int32 TOOL_TRANSFORM_FAILED
int32 PATH_TOLERANCE_VIOLATED
int32 INVALID_POSTURE
```

## Field details
### Control (Tool) Frame
The `tool` field describes the control frame for this trajectory. The poses and twists of the trajectory will be applied in this frame, and the tolerances will be measured in this frame. The tool frame should be rigidly attached to the "tip" frame given in the controller configuration; the transform between the two will only be computed once.

### Redundancy Resolution
Each cartesian trajectory point contains a posture, which is an array of joint positions for the joints listed in posture_joint_names. The controller attempts to track the posture in the nullspace of the cartesian movement. The posture value for each point is either the given value, or the previous posture value if the array is empty. The posture is linearly interpolated between trajectory points. If the posture array is empty in every point, then the posture is uncontrolled.

### Tolerances
Tolerances are specified for the entire trajectory (path_tolerance) and for the success conditions (goal_tolerance). In both, a tolerance of 0 is interpreted as "unspecified", and a default tolerance (such as a parameter to the controller) is used. A tolerace of -1 means "no tolerance" and the corresponding field is ignored when tolerances are checked.

There are two possible ways to handle the path tolerance:

 1. Abort if the path tolerance is violated
 2. Stall the desired and allow the controller to catch up if the path tolerance is violated.

Option 1 is the most straightforward to implement, but more difficult to use. I'm pretty sure I can implement option 2 by stalling the time used for computing the desired point. I'm considering making this choice a parameter of the controller so the user can choose either behavior.
