# Title 
Proposed by Konrad Banachowicz

Part of http://wiki.ros.org/trajectory_msgs/Reviews/Cartesian%20Trajectories_API_Review_2013_06_05

## Feature list
 * Multiple possible effector names
 * Multiple trajectories in one goal (One for each effector?)
 * Trajectory points consist of pose and twist
 * Contains impedance for each effector with
   * Center of compliance
   * stiffness
   * damping
 * Contains path and goal tolerances in
   * absolute position distance
   * absolute rotation angular error
   * absolute linear velocity
   * absolute angular velocity
 * Contains posture information to handle redundancies (for each effector???)
 * Contains a nullspace impedance

## Features required from hardware / driver
 * Control interface
 * Impedance control

## Message definition

```yaml
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
```
