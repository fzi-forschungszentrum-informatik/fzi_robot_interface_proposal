Descartes Trajectory
====================

http://wiki.ros.org/descartes_trajectory
https://github.com/ros-industrial-consortium/descartes/tree/melodic-devel/descartes_trajectory/include/descartes_trajectory


Feature list
------------
 * Contains reference implementations for path sepcification and interface for more customized methods
 * Offers three implementations for Trajectory Points

   * Joint point [JointTrajectoryPt]: Represents a robot joint pose. It can accept tolerances for each joint
   * Cartesian point [CartTrajectoryPt]: Defines the position and orientation of the tool relative to a world coordinate frame. It can also apply tolerances for the relevant variables that determine the tool pose.
   * AxialSymmetric point (5DOF) [AxialSymmetricPt]: Extends the CartTrajectoryPt by specifying a free axis of rotation for the tool. Useful whenever the orientation about the tool’s approach vector doesn’t have to be defined.
 * Hybrid Trajectories possible
 * All trajectory points take an optional TimingConstraint


Features required from hardware / driver
----------------------------------------



Message definition
------------------

.. code-block:: yaml

  [JointTrajectoryPt]
  joint position
  TolerancedJointValue
  TimingConstraint


  [CartTrajectoryPt]
  TolerancedJointValue
  step size


  [AxialSymmetricPt]
